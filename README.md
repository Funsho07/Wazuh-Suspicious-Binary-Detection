# Wazuh-Suspicious-Binary-Detection
Wazuh SOC lab demonstrating detection of suspicious binaries using Rootcheck
# Wazuh Suspicious Binary Detection

A hands-on SOC lab demonstrating host-based anomaly detection using Wazuh Rootcheck on an Ubuntu endpoint.

## 1. Project Overview

The objective of this lab was to test whether Wazuh Rootcheck could identify a suspicious, modified system binary and generate a security alert.

The exercise followed the Wazuh proof-of-concept approach for detecting trojaned binaries.

## 2. Lab Environment

| Component | Role | IP Address |
|---|---|---|
| Ubuntu Linux | Test endpoint and Wazuh agent | `192.168.1.103` |
| Wazuh Manager | Centralized alert monitoring | `192.168.1.102` |

### Original System Binary

The lab involved `/usr/bin/w`, a system utility supplied by the `procps` package. The evidence below documents the binary during the test workflow.

![System binary evidence](screenshots/Original%20System%20binary.png)

*Figure 1: System binary evidence collected during the lab.*

## 3. Test Procedure

A test modification was made to `/usr/bin/w` using the proof-of-concept approach. The test script included commands and output intended to demonstrate that the binary had been altered.

![Modified binary test](screenshots/Original%20binary%20replaced.png)

*Figure 2: Terminal evidence of the test modification workflow.*

The Wazuh agent was restarted during the test process.

![Wazuh agent restart](screenshots/Wazuh%20agent%20restarted.png)

*Figure 3: Wazuh agent restart evidence.*

## 4. Detection Results

Wazuh Rootcheck generated an alert indicating that a trojaned version of `/bin/w` had been detected.

![Wazuh Rootcheck alert](screenshots/Wazuh%20alert.png)

*Figure 4: Wazuh alert for suspicious binary detection.*

### Alert Details

| Field | Value |
|---|---|
| Alert title | Trojaned version of file detected |
| Rule ID | `510` |
| Severity | Level `7` |
| Rule groups | `ossec`, `rootcheck` |
| Agent | `ubuntu` |
| Agent ID | `001` |
| Agent IP | `192.168.1.103` |
| Detected file | `/bin/w` |
| Detection source | Rootcheck |
| Alert timestamp | October 24, 2026, 19:28:37.045 |

The rule metadata identifies the alert ID, severity, and associated groups.

![Rootcheck rule details](screenshots/wazuh%20rule%20ID.png)

*Figure 5: Rule 510 and its associated detection metadata.*

### Alert Investigation

The event details identify the file as `/bin/w` and describe the Rootcheck finding as a host-based anomaly detection event.

![Rootcheck event analysis](screenshots/log%20analysis.png)

*Figure 6: Alert event fields reviewed during the investigation.*

## 5. Recovery and Verification

During the workflow, `/usr/bin/w.copy` was created after the test binary had already been modified. Therefore, it was **not a verified copy of the original binary** and was not relied upon for recovery.

The `procps` package was reinstalled to restore the system utility. Subsequent checks showed that `/usr/bin/w` was an ELF executable and that the utility ran normally.

![Restored binary hash](screenshots/Real%20Hash%20restored.png)

*Figure 7: SHA-256 hash recorded after restoration.*

Recorded post-restoration SHA-256:

`0eaae827a227749b3c724897d0d9e2b7272a20dbe51b983b7403df50d6528413`

**Recovery limitation:** A baseline hash of the original binary was not recorded before the test. The post-restoration hash documents the recovered file, but cannot independently prove that its contents are identical to the original pre-test binary.

## 6. Findings and Lessons Learned

- Wazuh Rootcheck generated an alert for a suspicious version of `/bin/w`.
- Rule `510` produced a Level `7` alert in the `ossec` and `rootcheck` groups.
- Reinstalling the owning `procps` package was used to restore the system utility.
- A verified baseline hash should be recorded before modifying a system binary.
- Recovery should use a trusted package source rather than an unverified backup.

## 7. Conclusion

This lab demonstrated Wazuh Rootcheck's ability to detect a suspicious system binary and generate an alert for investigation. It also highlighted the importance of preserving a trusted baseline and verifying system integrity after testing.

The evidence supports the detection and recovery workflow described above. It does not establish that the host was compromised by an external attacker.

## 8. Reference

[Wazuh Documentation — Detecting a Trojaned Binary](https://documentation.wazuh.com/current/proof-of-concept-guide/poc-detect-trojan.html)
