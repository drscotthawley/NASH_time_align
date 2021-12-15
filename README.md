# Learning (to do) Time Alignment
*or "Neural Beat Detective" / "Beating Beat Detective"*

A proposal for collaboration in the [NASH Hackathon](https://signas-qmul.github.io/nash/).  This is limited-scope project for which diverse ideas, perspectives, and skillsets could really pay off! 

## Summary
The idea is to train a neural network to perform time alignment, e.g. drum performance editing, as an audio effect. In the music production industry, this is "grunt work" that is typically done "by hand": tools such as [Beat Detective](https://www.wikiaudio.org/beat-detective/) provide a good first approximation, but invariably need to be corrected by humans. 

(**Note:** we say "drum editing" but time-alignment can be applied to any recording of a live performance, e.g. guitars & vocals. Somewhat unique to the drum domain is the fixing of "flam," i.e., cases when different drums/objects are not all hit at quite the same time.)

It's been shown (by Hawley, in talks to the AES [such as this one](https://hedges.belmont.edu/AES_ML_2020/), but not published) that neural networks such as [SignalTrain](https://github.com/drscotthawley/signaltrain) can learn to do time alignment of audio signals -- i.e. resynthesizing a sequence of randomly-spaced audio samples such that they occur at a regular spacing or "grid."  And I expect that newer/better models could do as well or better, especially models based on transformers, which are "a differentiable way to do alignment" (as said by Lucas Kaiser, co-author on "Attention is All You Need" and "Reformer" papers). (SignalTrain had a lot of similarities to transformers BTW.)   Recent work by Christian Steinmetz & Josh Reiss, and others around the world, also present interesting candidates for viable models.

## Hypothesis/es
It seems likely that transformer-based models would outperform purely convnet-based models, however if one were to add a positional encoding scheme (e.g. via extra audio channels) then purely convnet-based models might do fine.  Beat Detective output could serve as a baseline; not sure how it would perform, but the hope would be for the neural network to "beat" Beat Detective somehow! 

## Background

![time alignment description images](https://raw.githubusercontent.com/drscotthawley/NASH_time_align/main/time_align_description.png)

Note how in the above image the Output (green) and Target (red) waveforms are essentially on top of each other. *(Image source: ["Profiling musical audio processing effects with deep neural networks"](https://docs.google.com/presentation/d/1j37RzWoxKXANQup10ofKtQjxLDVdKTX-1lNHMWy24Sg/edit?usp=sharing), Acoustical Society of America Meeting, Nov 6, 2018.)*

Hawley's previous work on this was a model/paper called SignalTrain ([SignalTrain Demo Page](https://signaltrain.herokuapp.com)), which never achieved great audio quality due to noise artifacts, and which in IMO has been essentially supplanted by works by Christian Steinmentz & Josh Reiss, and cf. works by Marco Martinez et al.  SignalTrain was intended to learn "general" audio effects but for our first paper we stuck with compressors because they were "hard".  The second paper (Mitchell & Hawley) tried some other effects, e.g. Leslie horns, but  was largely preoccupied with getting the noise down.  Newer models, as you know, have much less noise!

One of the general "inverse" effects that SignalTrain showed some success with, but was never fully investigated, was time alignment. SignalTrain could do it, and what was really interesting is that SignalTrain would fill in the silent gaps created when late audio is moved earlier, not via time-stretching like most humans would do, but by filling in "more" of whatever the signal is -- e.g. by continuing a decaying sine wave without lowering its frequency.

## Model Design Ideas

### Architecture(s)?
Use whatever models people think would be best...
- [micro-tcn](https://csteinmetz1.github.io/tcn-audio-effects/)
- [steerable-nafx](https://huggingface.co/spaces/akhaliq/steerable-nafx)
- SignalTrain is "old" and won't work with newest version of libraries (e.g. PyTorch), but I could perhaps update it to use for comparison; there will be probably be noise though.
- others?

...and compare results. 

Steinmetz: "Implicit neural representations could be another far out idea"

### Channels?
Multi-channel audio support strongly preferred.  Mono-only is useful, but true drum editing requires 5+ input channels. Multi-channel would also allow positional encoding and/or a "metronome track".

### Overall Strategy?
1. Re-synthesize all audio (Hawley's default idea) or
2. (thanks to convo with Steinmetz): Only predict where to make the "cuts" and then fix/resynthesize the transitions between moved samples -- what one might call a "Neural Beat Detective Cleaner-Upper."  This has the potential to allow higher-quality audio while needing fewer trainable parameters, although making sure the process is fully differentiable may require some special treatment for the cutting-and-pasting parts (e.g. an interpolation scheme such as via FFT (which PyTorch has) or a [fractional delay all-pass filter](https://colab.research.google.com/github/GuitarsAI/ADSP_Tutorials/blob/master/ADSP_09_AllPassFilters.ipynb) that we'd need to write in PyTorch, or sigmoid-tunable wet/dry mix, or ??). 

### (Simplifying) Assumptions?
- Steinmetz: "Do we also make the assumption that we know the audio file starts on the first beat of the song and that the tempo is constant?"
- Hawley: "If we tried the cut-and-paste approach: Do we assume that for a multi-channel input, that all channels would be "cut" the same place?" (Probably not). 

## Partial To-Do List
Datasets: Decide how to generate/encode datasets.
* Tunable grid interval: Add a "metronome track" and/or positional encoding?
* What kind of data?
    * Machine-generated data: Write code to grab "samples" and paste them along a grid (for targets) and "randomly messed up" (for inputs). I have some of this already [in the SignalTrain code](https://github.com/drscotthawley/signaltrain/blob/7d93cb4b63cc4ebd1a2f7a06e3192d755f56739d/signaltrain/audio.py#L585).
    * Human-edited data: I could ask for human-edited datasets from Nashville engineers (i.e., before & after editing), however note that these will all likely include time-stretching -- which is not necessarily a bad thing, just a "feature" to keep in mind.

## Team Member Roles
- dataset generation & preparation
- model training (multiple models).
- evaluation / comparison of results
- someone to write the inevitable conference paper. ;-) 
- ...more?

## Joining the Team
The NASH Hackathon has setup [this Trello board](https://trello.com/invite/b/f99P2bLG/fddb3c36457bc5901db563bc06e33a67/brainstorming-team-forming) for organizing teams.  Please find this project under "Team Members Wanted" and join! 

## Desired Skill-Sets
- Anybody actually used Beat Detective much? ;-) I haven't in probably a decade
- Usual ML skills: Python proficiency, ability to read instructions & train a model, evaluate a model 
- thinking up a cool name for the project? ;-) 
- ...more?

## Questions For You
- ^ See above questions in "Model Design Ideas" and "Partial To Do List".  What do you think the answers should be?
- How would you like to be involved?
- What model(s) do you think we should include?
- Other thoughts/comments/ideas/questions?  


## Ethics Considerations
Drum editing is often viewed as a thankless task and thus is typically relegated to interns.  A system that significantly outperforms Beat Detective may result in reduced wages and fewer entry-level employment opportunities in the music production industry. However the added "win" of time-saving may open up new opportunities for entry-level work for which we are not yet aware. 
