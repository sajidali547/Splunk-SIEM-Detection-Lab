# Lab Report

## Setup

Windows Security logs -> Universal Forwarder -> Splunk on TCP `9997` -> `main` index -> dashboard.

The [input configuration](../configs/inputs.conf) follows the lab guide.

## Test and results

- The Kali SMB test shows three failed passwords followed by a successful login for `VictimUser`.
- Splunk shows a successful login event (`4624`) for that account from `10.242.185.195`.
- The event has logon type `3`, meaning a network login.
- The dashboard shows failed logins over time and successful logins by account.

See the [original screenshots](../screenshots/) for evidence.
