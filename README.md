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

## NEMAR curation changes (2026-05-21, revised 2026-05-27)

The BIDS validator went from 560 errors + 15403 warnings to 0 errors + 11202 warnings. None of the raw recording files (.eeg, .vhdr, .vmrk) were touched — every change is to a text sidecar.

**Dataset description (`dataset_description.json`)**
- Added `DatasetType: "raw"` so the dataset is validated as raw data rather than a derivative.
- Updated `BIDSVersion` from `1.2` to `1.11.1` (the version the current validator checks against).
- `GeneratedBy` was left absent, exactly as the source published it — nothing was added there.

**Channel tables (`channels.tsv`, all 280 task-cnos recordings)**
- The 8 scalp electrodes (Fz, Cz, P3, Pz, P4, PO7, PO8, Oz) had their channel type written as `unknown`, which is not a valid BIDS channel type; they were set to `EEG`. The trailing timestamp channel, also `unknown`, was set to `MISC` (it is a sample-index column, not a measured signal).
- The same 8 EEG rows had their units written as `unknown`; these were set to `uV`, the conventional unit for scalp EEG. The timestamp row's units were set to `n/a`, since a sample index has no physical unit.

**Channel-column documentation (`task-cnos_channels.json`, new, at the dataset root)**
- The per-recording channel tables carry a non-standard `software_filters` column that BIDS does not define. A single root-level sidecar adds a description for that column, which then applies to all 280 recordings. The other columns are standard BIDS channel-table columns and need no description.

**Recording sidecars (`_eeg.json`, all 280 task-cnos recordings)**
- The misc-channel-count field was spelled `MiscChannelCount`; BIDS uses the all-uppercase `MISCChannelCount`. It was renamed, and its value corrected from `9` to `1` — there is a single timestamp (misc) channel, not nine.
- `EEGChannelCount` was `0`, which contradicted both the channel table and the dataset description; it was set to `8` to match the 8 scalp electrodes that are actually recorded.

**Electrode layout (`task-cnos_eeg.json`, new, at the dataset root)**
- Added `EEGPlacementScheme: "10-20"` and `EEGGround: "AFz"`. Both values come straight from this dataset's own description above, not from any guess: the description states the recordings were collected "according to the 10-20 EEG electrode placement standard" and were "grounded to AFz channel." A single root sidecar applies both to all 280 recordings.

**Event-column documentation (`task-cnos_events.json`, new, at the dataset root)**
- The event tables use `sample`, `type`, and `value` columns that were previously undocumented; a root sidecar describes all three. For `value`, the numeric trigger codes are explained in the dataset description above: codes 1–9 indicate which on-screen symbol was activated, 101 marks the start of a block, and 200–203 mark the boundaries of the experiment's five phases (Fixation, Target Presentation, Preparation, Stimulation, Rest). A few additional codes (102–109) also appear in the data but are not explained in the source, so they were left as plain numeric codes rather than given invented meanings.

**Remaining warnings (11202) — left on purpose**
- These are all "recommended but missing" equipment and study fields (for example: manufacturer, cap model, hardware filters, device serial number, instructions, head circumference, cognitive-atlas IDs). None of them are documented anywhere in the dataset, so they were left blank rather than filled with guesses.

**Left untouched — outside this metadata cleanup**
- The 104 channel tables under `task-ctos` with run labels `5F` and `5H` were left as published. BIDS requires run labels to be integers, so the validator quietly skips these files (they raise no errors or warnings). Renaming the runs would mean restructuring the files while preserving the Standard-Flash versus Cartoon-Face meaning that those labels encode (described above), which is beyond a sidecar cleanup.