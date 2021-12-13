# Learning (to do) Time Alignment
*or "Neural Beat Detective"*

A proposal for collaboration in the NASH Hackathon.

## Summary
Training a network to learn time alignment, e.g. drum performance editing, as an audio effect. In the music production industry, this is "grunt work" that typically done "by hand": algorithms such as Beat Detective provide a good first approximation, but invariably need to be corrected by humans. 

It's been shown (by me, in talks to the AES [such as this one](https://hedges.belmont.edu/AES_ML_2020/), but not published) that neural networks can learn to do time alignment of audio signals -- i.e. resynthesizing a sequence of randomly-spaced audio samples such that they occur at a regular spacing or "grid."  And I expect that newer/better models could do as well or better, especially models based on transformers, which are "a differentiable way to do alignment" (as said by Lucas Kaiser, co-author on "Attention is All You Need" and "Reformer" papers). (SignalTrain had a lot of similarities to transformers BTW.)   Recent work by Christian Steinmetz & Josh Reiss, and others around the world, also present interesting candidates for viable models.

## Hypothesis
It seems likely that transformer-based models would outperform purely convnet-based models, however if one were to add a positional encoding scheme (e.g. via extra audio channels) then purely convnet-models might do fine.

## Background

![time alignment description images](https://raw.githubusercontent.com/drscotthawley/NASH_time_align/main/time_align_description.png)

Note how in the above image the Output (green) and Target (red) waveforms are essentially on top of each other. *(Image source: ["Learning Tunable Audio Effects via Neural Networks with Self-Attention"](https://hedges.belmont.edu/AES_ML_2020), AES Virtual Symposium: Applications of Machine Learning in Audio, Sep 28, 2020.)*

My previous work on this was a model/paper called SignalTrain ([SignalTrain Demo Page](https://signaltrain.herokuapp.com)), which never achieved great audio quality due to noise artifacts, and which in IMO has been essentially supplanted by works by Christian Steinmentz & Josh Reiss, and cf. works by Marco Martinez et al.  SignalTrain was intended to learn "general" audio effects but for our first paper we stuck with compressors because they were "hard".  The second paper (Mitchell & Hawley) tried some other effects, e.g. Leslie horns, but  was largely preoccupied with getting the noise down.  Newer models, as you know, have much less noise!

One of the general "inverse" effects I had some success with, but never fully investigated, was time alignment. SignalTrain could do it, and what was really interesting is that SignalTrain would fill in the silent gaps created when late audio is moved earlier, not via time-stretching like most humans would do, but by filling in "more" of whatever the signal is -- e.g. by continuing a decaying sine wave without lowering its frequency.

## Model Design Ideas
Use whatever models people think would be best (e.g. Steinmetz's micro-tcn or steerable-nafx, or others).

Multi-channel audio strongly preferred.  Only mono is useful, but true drum editing requires 5+ input channels. Multi-channel would also allow positional encoding and/or a "metronome track".
1. Re-synthesize all audio (Hawley default) or
2. (thanks to convo with Steinmetz): Only predict where to make the "cuts" and then fix/resynthesize the transitions between moved samples -- what one might call a "Neural Beat Detective Cleaner-Upper."

## To-Do List
Datasets: Decide how to generate/encode datasets.
* Tunable grid interval: Add a "metronome track" and/or positional encoding?
* What kind of data?
        * Machine-generated data: Write code to grab "samples" and paste them along a grid (for targets) and "randomly messed up" (for inputs). I have some of this already [in the SignalTrain code](https://github.com/drscotthawley/signaltrain/blob/7d93cb4b63cc4ebd1a2f7a06e3192d755f56739d/signaltrain/audio.py#L585).
        * Human-edited data: I could ask for human-edited datasets from Nashville engineers (i.e., before & after editing), however note that these will all likely include time-stretching.

## Ethical Issues
Drum editing is often viewed as a thankless task thus typically relegated to interns.  A system that significantly outperforms Beat Detective may result in reduced wages and fewer entry-level employment positions in the music production industry.
