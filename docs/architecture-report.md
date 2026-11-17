# Architecture and Event Findings

## Collection flow

The Windows endpoint generates Security events. Universal Forwarder sends them to Splunk on TCP `9997`, where they are stored in the `main` index and used by the dashboard.

Kali Linux provides the controlled SMB login test over TCP `445`.

## Input configuration

The lab guide uses this stanza in the forwarder's `etc/system/local/inputs.conf`:

```ini
[WinEventLog://Security]
disabled = 0
index = main
```

The repository copy is available in [configs/inputs.conf](../configs/inputs.conf).

## Successful login record

The raw-log and expanded-detail screenshots show these fields:

| Field | Observed value |
| --- | --- |
| Event code | `4624` — successful login |
| Target account | `VictimUser` |
| Logon type | `3` — network login |
| Source network address | `10.242.185.195` |
| Record number | `92368` |

See the [raw event](../screenshots/04-raw-logs.png) and [expanded details](../screenshots/06-successful-logon-details.png).

## Findings

The terminal shows three rejected passwords followed by successful authentication. Splunk contains a successful network login for the test account. The dashboard provides an overview of failed login activity and successful logins by account.

Together, these show how centralized logs support an investigation: the dashboard highlights activity, while the raw event identifies the account and source. Confirming the exact failure-to-success sequence would require reviewing the matching raw failure records.
