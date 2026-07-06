---
layout: post
title: "Spectrograms, LoRAs, and Typos: Inside an Acoustic Side-Channel Attack Pipeline"
date: 2026-07-06 11:00:00
author: Ali Ayati
---

This is the technical follow-up to [my post on why your Zoom call might be leaking your password](/blogs/). If you want the "should I be worried" version, read that one. This one is for people who want to know exactly how a stream of keyboard clicks turns into recovered text, including the parts that didn't work.

The full paper is [Making Acoustic Side-Channel Attacks on Noisy Keyboards Viable with LLM-Assisted Spectrograms' Typo Correction](https://www.usenix.org/system/files/woot25-ayati.pdf) (USENIX WOOT '25), and the code and fine-tuned weights are public on [GitHub](https://github.com/Botacin-s-Lab/EchoCrypt) and [HuggingFace](https://huggingface.co/seyyedaliayati/zoom_model).

## The pipeline, end to end

```
Audio → separate keystrokes → inject noise → time-shift → Mel spectrogram
      → frequency/time masking → classifier (CoAtNet / Vision Transformer)
      → LLM-based correction
```

Each keystroke recording (25 presses per key, across the 36 alphanumeric keys, giving 900 samples per dataset) gets split into individual `.wav` files using energy-peak detection on the FFT. Each one becomes a Mel spectrogram, a visual representation of frequency content over time, which turns "classify this keystroke" into an image classification problem.

## Why the accuracy numbers you've seen elsewhere are misleading

Most prior ASCA papers report accuracy on spectrograms generated from clean recordings. Ours does too, as a baseline. We re-implemented the CoAtNet architecture from the paper we were building on, since the original didn't specify its exact configuration, and got 96.45% mean accuracy on phone recordings and 96.67% on Zoom recordings, both a few points above the numbers we were comparing against.

Then we ran the identical model against noisy input, injecting Gaussian noise directly into the waveform before spectrogram generation, at three calibrated levels (Low/Medium/High, tuned so post-classification accuracy landed around 95/85/70%). The same CoAtNet model that hit 99% on clean audio dropped to 58% on noisy audio. That's the number that matters, and it's the number almost nobody reports.

## Trying five Vision Transformers, and what actually happened

Our hypothesis going in was that Vision Transformers, with self-attention that can capture long-range structure across a spectrogram, might handle noise better than a CNN-based CoAtNet. We tested five: ViT, Swin, DeiT, CLIP, and BEiT, each fed spectrograms two ways (bilinear-resized from 64×64, or generated natively at 224×224).

The honest result is mixed. BEiT edged out CoAtNet slightly (97.6% vs 96.45% on phone recordings, direct transformation), but CLIP was consistently the weakest performer and, on the Zoom dataset with direct-transformation spectrograms, collapsed to 2.7% accuracy, essentially random guessing. That's almost certainly because CLIP's vision encoder was designed to sit next to a text encoder for multimodal retrieval, and repurposing just half of that architecture for a narrow, single-modality classification task throws away exactly what it's good at.

More telling than any single number: several VT/dataset combinations showed enormous seed-to-seed variance, with standard deviations near 45-49 percentage points on some Zoom configurations. That means the same model, trained twice, can land anywhere from near-zero to near-perfect depending on random initialization. With only 900 samples per dataset that's not surprising, and we didn't tune VT hyperparameters at all, so there's real headroom left here for anyone who wants to push this further.

## The actual novelty: fixing keystroke "typos" with an LLM instead of a bigger classifier

Rather than trying to squeeze more accuracy out of the classifier alone, we treated its noisy output as a sentence full of typos and handed the correction problem to a language model. The prompt is deliberately simple: few-shot, no fine-tuning required for the base version.

```
System: You are an expert in correcting typos in sentences.

User: Here are pairs of sentences with typos; learn from them:

sentence: {noisy example 1}
corrected: {true example 1}

sentence: {noisy example 2}
corrected: {true example 2}

Now, please correct these sentences and output only the corrected
version with no additional text: {actual noisy sentence}
```

This works because the errors coming out of the classifier aren't like typical typos. They're not adjacent-key slips a spell-checker is tuned for, they're closer to random character substitutions and deletions caused by waveform noise, sometimes without clear word boundaries. Fixing that requires actually inferring what sentence was probably intended, not looking up nearby dictionary words.

We benchmarked GPT-4o against Llama-3.2-1B, -3B, and Llama-3.1-8B, all used zero/few-shot with no fine-tuning, across 1,000 test sentences from the EnglishTense dataset at each noise level. GPT-4o was the strongest and most consistent performer overall. But on the phone dataset specifically, at low and moderate noise, a fine-tuned 3B Llama model actually beat it.

## Making it fit in your pocket: QLoRA fine-tuning

GPT-4o is roughly 200B parameters. Running that as part of an attack pipeline is expensive and slow, which matters if you're trying to show this isn't just a cloud-scale research toy. So we fine-tuned Llama-3.2-3B, 67x smaller, using QLoRA: freeze the pretrained weights, train only a pair of small low-rank update matrices (`W = W₀ + AB`), quantize for efficiency.

Training ran three epochs, one each at increasing noise levels (low, then medium, then high), using the classifier's noisy predictions as input and the ground-truth sentences as target. The result is a model with about 1.5% of GPT-4o's parameter count that reaches at least 90% of its correction performance across every metric we measured (BLEU, ROUGE, METEOR), and actually surpasses GPT-4o on the phone dataset in low and moderate noise. Fine-tuning gave the biggest lift exactly where you'd expect: at high noise, BLEU score improved by 430-500% over the non-fine-tuned 3B baseline, versus a much smaller gain at low noise where there wasn't much room left to improve.

That's the part I think is most underappreciated about the result. You don't need frontier-model scale to make this correction step work. A small model, fine-tuned on the right kind of noisy-to-clean pairs, gets you most of the way there, cheaply enough to run locally.

## What we didn't solve

The limits are worth stating plainly, because they're real. The acoustic dataset only covers 25 samples per key on a single keyboard (a MacBook Pro), excludes space, backspace, and enter entirely, and uses synthetic Gaussian noise rather than authentic ambient sound like cafe chatter or street noise. Every number in this post should be read as "this is what's achievable under these specific conditions," not "this is a deployed attack against your keyboard." Closing that gap, with more devices, real noise, and a broader key set, is exactly what future work needs to do.
