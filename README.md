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

## NEMAR curation changes (2026-05-21)

BIDS validator: 560 errors + 15403 warnings → 0 errors + 11201 warnings. Raw `.eeg` / `.vhdr` / `.vmrk` binary payloads unchanged.

### `dataset_description.json`
- Added `DatasetType: "raw"` and a `GeneratedBy` entry naming `nemar-cli`. Why: BIDS-validator otherwise infers a derivative-dataset rules cascade when `DatasetType` is missing alongside `GeneratedBy`, producing spurious warnings.
- Bumped `BIDSVersion` from `1.2` to `1.8.0`. Why: BIDS-validator fires `UNKNOWN_BIDS_VERSION` for any pre-1.8 version string; 1.8.0 is the current stable schema that all the changes below target.

### `task-cnos_channels.json` (new, inheriting root sidecar)
- Created a root-level `task-cnos_channels.json` declaring a `Description` for the `software_filters` column. Why: the per-recording `channels.tsv` files carry a non-standard `software_filters` column that is not part of the BIDS channels-table spec, which fires `TSV_ADDITIONAL_COLUMNS_MUST_DEFINE:software_filters` once per file. One root sidecar inherits to all 280 cnos recordings and closes the 280 errors. Only the genuinely non-standard column is declared; the other columns (`name`, `type`, `units`, `sampling_frequency`, `low_cutoff`, `high_cutoff`, `notch`, `status`, `status_description`) are BIDS-canonical and inherit the built-in schema without redeclaration.

### `sub-*/ses-*/eeg/sub-*_task-cnos_run-*_channels.tsv` (280 files)
- Rewrote the `type` column for each of the 9 rows: the first 8 channels (`FZ`, `Cz`, `P3`, `PZ`, `P4`, `PO7`, `PO8`, `Oz`) had `type=unknown` and are now `type=EEG`; the trailing `Timestamp` channel had `type=unknown` and is now `type=MISC`. Why: BIDS validator fires `TSV_VALUE_INCORRECT_TYPE:type` for `unknown` (not in the BIDS channel-type enum). The 8/1 EEG/MISC split is documented in this README (see the channel list above) and is the same partition across all 280 files (verified by md5 hash; 280 cnos channels.tsv files share a single content hash).
- Rewrote the `units` column for the 8 EEG rows from `unknown` to `uV`. Why: scalp EEG is conventionally µV; the BIDS-validator accepts `uV` for the EEG units. The `Timestamp` row's `units` was set to `n/a` (sample-index column, no SI unit).

### `sub-*/ses-*/eeg/sub-*_task-cnos_run-*_eeg.json` (280 files)
- Renamed the PascalCase typo `MiscChannelCount` to the BIDS-canonical `MISCChannelCount`, and fixed the value from `9` to `1`. Why: per the README, the recording has 8 EEG channels + 1 timestamp channel; the original `MiscChannelCount: 9` lumped every channel into the misc bucket, which also caused the validator's `MISC_CHANNEL_COUNT_MISMATCH` to fire once per recording (1 MISC-typed row in channels.tsv vs the declared count of 9). The canonical spelling `MISCChannelCount` plus the correct count of `1` closes both the typo warning and the mismatch.
- Set `EEGChannelCount` from `0` to `8`. Why: per the README, 8 scalp electrodes are recorded (Fz, Cz, P3, Pz, P4, PO7, PO8, Oz). The original `0` was inconsistent with both the channels.tsv body and the README; the corrected value now matches the EEG row count after the type fix.

### `task-cnos_eeg.json` (new, inheriting root sidecar)
- Created a root-level `task-cnos_eeg.json` declaring `EEGPlacementScheme: "10-20"` (README states explicitly: "according to the 10-20 EEG electrode placement standard") and `EEGGround: "AFz"` (README states explicitly: "grounded to AFz channel"). Why: these two fields are recommended by BIDS and fire `SIDECAR_KEY_RECOMMENDED` once per recording when missing. One inheriting root sidecar broadcasts both values to all 280 cnos recordings and closes 1680 warnings.

### `task-cnos_events.json` (new, inheriting root sidecar)
- Created a root-level `task-cnos_events.json` declaring `Description` entries for the non-required events.tsv columns `sample`, `type`, and `value`. Why: the per-recording `events.tsv` files use these three columns in addition to the required `onset`/`duration`, but no sidecar declares them, which fires `TSV_ADDITIONAL_COLUMNS_UNDEFINED:{sample,type,value}` once per file. The `value` Description references this README's marker key (codes 1-9, 101, 200-203) rather than enumerating `Levels` because the on-disk values include codes 102-109 that this README does not document, and locking the column to an incomplete `Levels` enum would reject those cells.

### Remaining warnings (Tier C — left intact)
- 11201 `SIDECAR_KEY_RECOMMENDED` warnings remain across `Manufacturer`, `ManufacturersModelName`, `SoftwareVersions`, `DeviceSerialNumber`, `Instructions`, `CogAtlasID`, `CogPOID`, `CapManufacturer`, `CapManufacturersModelName`, `RecordingType`, `HeadCircumference`, `HardwareFilters`, `SubjectArtefactDescription`, `StimulusPresentation`, plus 1 `JSON_KEY_RECOMMENDED:HEDVersion`. None of these fields are documented anywhere in the dataset (README, existing sidecars, file headers), so filling them would mean inventing metadata. Per the curation rule of 100% defensibility, they are left as recommended-only warnings rather than guessed.

### Out of mechanical scope
- The 104 channels.tsv files under `task-ctos_*` (run literals `5F` and `5H`) were not touched. The BIDS validator silently excludes them from validation because the `run-` entity must be an integer; non-integer suffixes cause those filenames to fall outside the BIDS file-naming rule and thus generate no errors or warnings. Renaming runs (e.g. to `run-5_acq-F`/`run-5_acq-H`) would require structural rework outside mechanical sidecar curation, since the run numbering is documented as "Standard-Flash" vs "Cartoon-Face" in this README and any renaming has to preserve that semantic association.