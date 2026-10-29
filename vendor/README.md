# DDCS V3 / DM500 — vendor/: your controller's own files

> Nothing in this file is bench-measured.

This folder is yours, and git ignores everything in it but this file. This pack lists no vendor file for the DDCS V3 / DM500 yet: if you put your controller's own macro (.nc) or dialog (.rc) files here, the assistant reads them as the vendor's code, not as a bench test.

## How to fill it

Put each `.nc` file in `vendor/macros/` and each `.rc` file in `vendor/dialogs/`, names unchanged, then write your controller's firmware version on one line in `vendor/VERSION.txt` so the assistant can say which version it read.
