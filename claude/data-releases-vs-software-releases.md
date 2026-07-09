# Data Releases vs Software Releases

Data releases and software releases are two separate tracks. Do not conflate them.

- A software release ships application code (FE, BE, services) on the sprint release cadence: feature freeze, code freeze, stage deployment, production GA.
- A data release is the loading and publishing of study data (for example CMB v6) and follows its own schedule, independent of any software release.
- Data modeling is an activity, not a release. Do not equate "data modeling" with "releasing data."
- Team ownership: the Data team performs data modeling and indexing; the BE team performs data loading. Both are ongoing, never ending efforts that continue across software releases.
- When scoping or presenting a software release, keep data work on its own track. Do not list data modeling, indexing, or loading as software-release must-haves, and do not describe the software release as "releasing" the data.

> Note: this guidance belongs in claude/SKILL.md. It was committed as a companion file because SKILL.md is large and could not be safely edited in place through the automated tooling. Fold this section into SKILL.md when convenient.
