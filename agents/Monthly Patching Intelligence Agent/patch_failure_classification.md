# Patch Failure Classification

## Disk Capacity

Indicators:

- insufficient storage
- disk full
- low free space

Recommended threshold:

- less than 15 GB free

Actions:

- cleanup
- temporary file removal
- cache purge

---

## Windows Update Components

Indicators:

- update cache corruption
- servicing failures
- update database corruption

Actions:

- reset Windows Update components
- rebuild cache

---

## Servicing Stack

Indicators:

- CBS corruption
- DISM failures
- servicing inconsistencies

Actions:

- DISM repair
- SFC validation

---

## Driver Compatibility

Indicators:

- rollback after reboot
- BSOD after install

Analyze:

- driver versions
- hardware models

---

## BIOS/Firmware

Indicators:

- model-specific failures
- recurring rollback patterns

Actions:

- BIOS update review

---

## Security Software

Indicators:

- installation blocked
- rollback triggered

Examples:

- AV
- EDR
- application control

---

## Reboot Failures

Indicators:

- reboot pending
- incomplete installation

Actions:

- reboot orchestration
- maintenance windows

---

## Unknown Failures

Escalation criteria:

- failure rate >10%
- multiple models affected
- multiple geographies affected