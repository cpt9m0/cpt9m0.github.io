---
layout: post
title: "Your Zoom Call Might Be Leaking Your Password"
date: 2026-07-06 12:00:00
author: Ali Ayati
description: "A USENIX WOOT '25 paper shows keystrokes can be recovered from a live Zoom call using acoustic side channels and LLM-assisted typo correction — even over compressed, noise-suppressed audio."
---

You mute your mic when you're not talking. You'd never read a password out loud on a call. But if you type your password while the call is live, muted or not, you might be handing it over anyway.

That's the premise behind a paper my co-authors and I published at USENIX WOOT '25: [Making Acoustic Side-Channel Attacks on Noisy Keyboards Viable with LLM-Assisted Spectrograms' Typo Correction](https://www.usenix.org/conference/woot25/presentation/ayati). Every key on your keyboard sounds slightly different when you press it. With the right model, those differences are enough to reconstruct what you typed, even over a compressed, noise-suppressed Zoom call, and even when the raw audio-to-text guess is riddled with errors.

## Keyboards have been snitching on you for years

Acoustic side-channel attacks (ASCAs) aren't new. Security researchers have known for two decades that you can record someone typing and recover what they typed, because each keystroke sounds subtly different depending on where the key sits on the board, how the switch strikes, and how far it is from the microphone. Feed enough of those sound fingerprints into a classifier and you get the keystrokes back.

Deep learning made this a lot better. Recent work using CNNs and hybrid attention models has pushed classification accuracy past 90%, but almost entirely on quiet, controlled lab recordings. That caveat matters more than it sounds like it should. Introduce real background noise and accuracy on these systems drops by 30 to 50 percentage points, sometimes below 40%. At that point it's a neat demo, not a real threat.

Closing that gap between demo and threat is what our paper set out to do.

## Two ways someone could be listening right now

We built and tested two attack scenarios.

The first is a phone left near your keyboard. In an office, a co-working space, a coffee shop, anywhere a device can sit within earshot unnoticed, it can just record. No malware, no network access, nothing touching your machine at all.

The second is a live video call, and this one needs no physical proximity whatsoever. Laptop microphones pick up keystroke sounds even with noise suppression turned on, and those sounds survive the compression that Zoom, Teams, and Meet apply before transmission. If you're typing during a call, taking notes, answering a message, entering a password to unlock something mid-meeting, that audio is going out over the wire.

Both scenarios only need a recording. Neither needs access to your device.

## The trick: let an LLM clean up the "typos"

Here's the part that made noisy conditions viable instead of a lab curiosity. Once you classify a stream of keystroke sounds under real noise, you don't get clean text back. You get something like this, straight out of our pipeline:

> `they attwnded a 0usi2 fectivalwand dancsd under the3e5arrg sky`

That's supposed to say "they attended a music festival and danced under the starry sky." A spell-checker wouldn't get anywhere near that. But feed it to an LLM with a short prompt asking it to reconstruct the intended sentence, and it comes back as:

> "they attended a music festival and danced under the starlit sky"

Not a perfect match. It swapped "starlit" for "starry," a small, contextually reasonable slip, but the sentence is recovered in every way that matters. The reasoning is simple: people don't type random words, they type words that fit the sentence they're in. A model that understands language can lean on that to fix garbled keystrokes the same way it fills in the rest of a sentence when you give it a beginning. Across a 1,000-sentence test set, this step took recovery accuracy (measured in BLEU score) from roughly 0.07 to 0.90 under moderate noise. That's the difference between an attack that doesn't work and one that basically does.

## The part that should worry your password manager's marketing copy

There's a finding in the paper I keep coming back to. Security advice has spent years pushing people away from short, complex passwords toward long, memorable passphrases, "correct horse battery staple" style, on the logic that a longer string is harder to brute-force. By the math of entropy, that's true.

But entropy math assumes an attacker guessing blind. An LLM doing acoustic reconstruction isn't guessing blind. It's using exactly the thing that makes passphrases memorable in the first place: they're built from real language, with predictable grammar and word choice. That predictability is a feature for a human trying to remember the phrase, and it's also a feature for a language model trying to reconstruct it from a garbled signal. A truly random string of characters doesn't have that weakness, because there's no linguistic pattern for the model to grab onto. Random strings are exactly what passphrases were invented to help people avoid typing in the first place.

I'm not saying throw out passphrases. I'm saying the security/usability tradeoff they were sold on needs an asterisk now, and multi-factor authentication or a hardware security key does more real work against this kind of attack than a clever password ever will.

## Should you actually worry about this?

This is research, not a mass-deployed exploit, and it has real limits. It was tested on one keyboard (a MacBook Pro), with synthetic rather than real ambient noise, and it doesn't yet handle space, backspace, or enter keys. What it proves is that the ceiling on this class of attack is a lot higher than people assumed, not that there's a tool in the wild targeting your keyboard today.

Still, the practical takeaway doesn't require panic: don't type sensitive credentials while a mic is live nearby, and lean on MFA and hardware keys rather than trusting a clever passphrase to do all the work. If you want the technical details, [the next post](/blogs/spectrograms-loras-and-typos/) digs into how the classification and correction pipeline actually works.
