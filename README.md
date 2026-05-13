[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on003190-blue)](https://doi.org/10.82901/nemar.on003190)

Dataset description:

The database consists of a total of 382 electroencephalographic files from 19 participants. All recordings were collected on channels Fz, Cz, P3, Pz,P4, PO7, PO8 and Oz, according to the 10-20 EEG electrode placement standard, grounded to AFz channel and referenced to right mastoid (M2).
•Each participant (S1-S19) performed 3 experimental sessions (Session01-Session03) and in each session there are 7 data files.
•The filenames for these data files are ’Training 4’, ’Training 5 - SF’, ’Training 5 - CF’, ’Training 6’, ’Training 7’, ’Training 8’, and ’Training 9’.
•The number accompanying the filename indicates the number of stimuli, whereas letters SF and CF for data files with 5 stimuli indicate the type of flash, SF for Standard-Flash of the stimulus and CF for superimposing a yellow smiling Cartoon Face.
•Note that filenames for data-files with 4, 6, 7, 8, and 9 stimuli do not have a letter and were recorded with the type of flash that provided the greater classification accuracy when using 5 stimuli.
•Each data file contains the data stream in a 2D matrix where rows correspond to channels and columns correspond to time samples with sampling frequency of 256Hz.
•There are 10 rows, 1 to 8 for each EEG electrode (in descending order Fz, Cz, P3, Pz, P4, PO7, PO8 and Oz), 9 for time stamps, and 10 for a marker that encode information about the execution of theexperiment.

The marker encodes this information as follows:
•(i)marker numbers 101, 200, 201, 202 and 203, indicate the beginning and end of the five phases in a block
•(ii)marker numbers 1, 2, 3, 4, 5, 6, 7, 8 and 9, indicate the symbol that is activated on the screen
•(iii)each phase of the experiment block is identified with a marker
•(iv)the phases of one block of the experiment are: Fixation, Target Presentation, Preparation, Stimulation and Rest
•(iv)in particular the Stimulation phase has a start marker and an end marker