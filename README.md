# Tapping-analysis
Analysis of tapping task in speech and music entrainment study.
Still working on it.

## About the tapping task
For tapping task we collected data with Cubase 12. 
MIDI files are collected for each participant for 7 different tasks.
The tapping test has 6 + 1 tasks:
-	tapping to music at 80, 120 and 150 bpm (3 tasks)
-	tapping to metronome at 80, 120 and 150 bpm (3 tasks)
-	tapping without stimulus (1 task)

The first two are called sensorimotor synchronization (SMS) tasks. They use a synchronization-continuation design which means that after the participant tap together with the stimulus (metronome or music) they have to continue for ca. 30 s in silence.  We have two measures here: asynchrony (mean absolute difference from where the taps should have occured) and consistency (the spread of the taps compared to themselves). In the continuation phase there is no asynchrony (because there is no reference) but there is still consistency. In the +1 task that has no stimulus (also called spontaneous motor tempo) we calculate mean difference between taps (tempo) and also consistency. The above measures are sometimes calculated differently, but I’ll get to that later.

## Input file
After the participants have finished, we export the tapping via MIDI files for each trial and participant individually. Time codes are extracted from the MIDI files using Midianal.exe resulting in csv files. Example of csv files in Input folder.

All files should be named with this pattern: participant_code + task + .csv (this is done automatically). Examples: ab3b80.csv, ab3b80m.csv, ab3b120.csv, ab3b120m.csv, ab3b150.csv, ab3b150m.csv, ab3bSMT.csv.

All this files (7 in total), which we get from .wav to .csv files should be collected in folders with name of participant, for example: ab3b, bg3a, bi3a ...

All folders with participant's files should be in same directory, which is *the main and only input* for analysing code.

## Analysis
- Be careful to install all packages needed, especially R to Python, because part of the code is proceed in R.
- Be careful to change to the right path in R code - add directory in which you are working (name of the file is already set).

**IOI limits**
For each trial, all of the timecodes are gathered in an excel sheet and IOIs (inter-onset-intervals) are calculated as the difference between neighbouring taps. An lower and a upper limit is calculated:
-	Q1-3*IQR (first quartile – inter quartile range *3)
-	Q3+3*IQR
Theese will be used later in the analysis.
 
**Outlier filtering** 
-	taps occuring before 2,625 for 80 bpm trials, 1,75 for 120 bpm trials and 1,4 for 150 bpm trials are deleted because these occur during the count in („one-two-three-four”)

The second applies an artefact filter and removes outliers. If the difference between a timecode and the previous one is smaller than 0,1 it gets deleted because that’s probably an artefact from the equipment.

**REF**
The REF column is the reference, where taps should optimally occur. Taps in the synchronization phase are processed differently than in the continuation phase. For each tempo there is time limit where the first phase ends and the second starts. For 80, 120 and 150 bpm trials (music and metronome) this is 21, 28 and 20,8s. The synch phase is processed on the left side of the table while the continuation phase is on the right side, below. 
After filtering, the first 10 valid taps are deleted from the beginning of each trial)
 
**Synchronization phase**
Every tap from col. B is paired to the nearest reference in col. C and their difference is calculated (DIF). There is an extra col. (N, absDIF) for the absolute values of (DIF).

Differences are converted into angles (ANGLE) by dividing the value by the task tempo in milliseconds (for 80 bpm trials this is 0,75 for 120 bpm 0,5 and for 150 bpm 0,4 and multiplying by 360. Angles are later used in circular analysis.

**Linear measures for the synch phase**
-	DIF – mean of col. D / the tempo of the trial in ms (0,75 ; 05; 0,4)
-	absDIF – mean of col. N / the tempo of the trial in ms (0,75 ; 05; 0,4)

**Circular measures for the sync phase**
ANGLE-s  are compiled in excel sheets for each trial and opened in R R studio  (see R project) where four measures are calculated using the „circular” package:
-	The statistic for the Rayleigh test (RAY) which is the circular equivalent of a normality test
-	The p-value for the Rayleigh test (RAYsig) – if this value is larger than 0,05 the next two measures should be marked or deleted
-	Circular mean (MEAN) – this is the asynchrony measure
-	Rho – this is the consistency measure

 
**Continuation phase**
This starts with col. F in the main excel sheet. Taps that belong to the synch phase (smaller than 21 ; 28 and 20,8) are deleted (contMTC)
Outliers smaller than the value IOI lower limit from earlier or larger than IOI upper limit are deleted (preIOI). 
Outliers smaller than Q1-3*IQR or larger than Q3+3*IQR are deleted (contIOI).

**Linear measures for the continuation phase**
-	mIOI(cont) – mean of col. H
-	CV(cont) – spread of col. H / mIO(Icont)

For the continuation phase ther is no circular analysis because it requires a reference.

**Spontaneous motor tempo task (SMT)**
For this task there is no reference so slightly different analysis is required. Raw time codes are in the first col. The preIOI col. uses these filters:
-	Timecodes above 30 are deleted, only taps in the first 30s are considered valid. 
-	The first 10 valid taps are deleted. 
-	if the difference between a timecode and the previous one is smaller than 0,1 it gets deleted 
In the preIOI2 col. the lower and upper IOI limits from earlier are applied. 
Finally, in the IOI col. two filters are applied:
the Q1-3*IQR and Q3+3*IQR limits from earlier, but now calculated from the preIOI2 col.
The IOI col. should have a maximum of 39 cells with data (this is 40 taps), if there are more, those should be deleted. 

**Linear measures in the SMT task:**
-	mSMT – the mean of the IOI col.
-	CV – SD of IOI col. / mSMT


## Output 
Output can be viewed in *output folder*. Output are excels files (named: participant_code + all_anal) for each participants, where sheets are named after every measurement or input file (80, 80m, 120, 120, 150, 150m, SMT).

Addition to this are files: IOI_filter_80,IOI_filter_80m, IOI_filter_120, IOI_filter_120m, IOI_filter_150, IOI_filter_150m, IOI_filter_SMT, which is IOI limits files, later used in all seperate files. 

## Interpretation of results
