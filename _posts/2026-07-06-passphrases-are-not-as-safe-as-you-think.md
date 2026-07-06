---
layout: post
title: "Passphrases Were Supposed to Save Us. LLMs Might Take That Back."
date: 2026-07-06 10:00:00
author: Ali Ayati
description: "Why long, memorable passphrases can be weaker than equal-entropy random strings against LLM-assisted acoustic side-channel attacks — and why the entropy argument misses it."
---

For years, the standard security advice has been: stop using short, clever passwords, start using long, memorable passphrases. "Four random words" beats "P@ssw0rd1" because it's harder to brute-force and easier for a human to remember. That advice is grounded in real math. It's also, according to a finding in our USENIX WOOT '25 paper, quietly assuming an attacker that no longer describes the state of the art.

## The entropy argument, and where it breaks

Take two credentials. `this is a strong password` is a passphrase, 25 characters, drawn from a 58-character set. `Lw7@NcQhZ#f8GvXsT2rY` is a random string, 20 characters, drawn from a 94-character set.

Run the standard Shannon entropy calculation on both and you get almost identical numbers: about 86.5 bits for the passphrase, 86.4 bits for the random string. By the textbook measure, these are equally secure. The passphrase is even easier to type and remember, so it looks like a clear win.

That equivalence assumes the attacker is guessing combinations blind, with no information about which combinations are more likely than others. That's a reasonable assumption against brute-force and even against most dictionary attacks. It stops being reasonable against an attacker using a language model to reconstruct text from a noisy signal, which is exactly the scenario our acoustic side-channel work operates in, and increasingly the scenario a lot of adjacent research does too.

## Why "memorable" and "predictable" are the same property

The entire reason passphrases are memorable is that they're built from real language: real words, in an order that follows normal grammar, drawing on associations a human brain latches onto. That's a useful property for the person memorizing it.

It's also, structurally, a useful property for a language model trying to reconstruct a degraded version of it. In our acoustic attack pipeline, the correction step works specifically because people don't type random words, they type words that fit a plausible sentence, and a model that handles language well can lean on that to recover text from noisy, error-riddled input. A passphrase isn't just language-like by coincidence. It's built to be as linguistically natural and predictable as possible, which is exactly the property the correction model exploits.

A genuinely random string has none of that structure. There's no grammar to lean on, no word-frequency pattern, nothing that tells the model what's likely to come next. It's the case our paper found LLM correction struggled with: impractical for humans to use, but resistant to exactly this kind of contextual reconstruction.

So the two credentials that looked equivalent under entropy math diverge sharply under this threat model. One is recoverable from a garbled signal because it's predictable. The other isn't, precisely because it isn't.

## This isn't a reason to panic, but it is a reason to layer

To be clear about scope, this doesn't mean passphrases are broken or that you should abandon them. It means the specific guarantee people assumed passphrases provided, that they're as secure as a random string of equivalent entropy, doesn't fully hold against a specific, increasingly realistic class of attacker. Acoustic side-channel attacks aren't the only place this shows up. Anywhere an attacker gets a noisy, partial, or probabilistic read on your input, whether that's side-channel timing, degraded audio, partial screen captures, or autocomplete-adjacent leaks, a language model can lean on the same predictability that makes a credential memorable.

The practical response isn't to memorize random strings. Nobody does that reliably, and credentials people can't remember get written down somewhere worse. The better response is to stop treating a strong passphrase as the whole solution. Multi-factor authentication and hardware security keys don't rely on the secret being unguessable in isolation. They require possession of something an acoustic (or any other side-channel) attacker doesn't have, no matter how well they've reconstructed what you typed. Behavioral biometrics point the same direction, verifying how you type rather than just what you typed.

Entropy tells you how hard a secret is to guess. It doesn't tell you how hard it is to reconstruct once an attacker has a noisy version of it, and for anything built from real language, those two questions have different answers.
