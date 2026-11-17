# SOC Case Study: Repeated SMB Failures Followed by Success

## Business context

Consider a small business whose Windows endpoints send Security events to Splunk. An analyst notices failures against one account from an internal workstation, followed by a successful network logon. The source could be an employee's mistyped password, a stale service configuration, or a compromised workstation attempting access to another host.

This is an illustrative production scenario grounded in the owner's lab evidence. The organization is hypothetical. The captured activity was an authorized test with disposable account `VictimUser`.

## Investigation question

**Did repeated password guesses lead to a successful network logon, and what should the SOC investigate next?**

A failure chart provides context. An investigation must link the source address, destination endpoint, target account, event outcome, and time sequence.

## Observed evidence

| Stage | Observation | Interpretation |
| --- | --- | --- |
| Collection configured | Receiving port `9997` enabled; installer specifies `10.242.185.132:9997`. | Documented collection settings are present. |
| Guesses rejected | Three `STATUS_LOGON_FAILURE` lines for `VictimUser` over SMB. | Several candidates failed against one account. |
| Credential accepted | The next terminal result succeeds for that account. | The test obtained successful authentication. |
| Event searchable | Splunk displays `4624`, record `92368`. | A Windows success event was indexed. |
| Context inspected | `VictimUser`, logon type `3`, source `10.242.185.195`. | The event records a network logon for the test account. |
| Dashboard reviewed | Failure trend and account-count charts are saved. | An analyst can review authentication patterns. |

The displayed success timestamp is `11/17/2026 09:28:26.073 PM`; the screenshot does not identify its timezone. The terminal has no per-attempt timestamps, so elapsed time between guesses and success cannot be measured from these images.

The terminal sequence and indexed success are consistent with the owner's exercise. Definitive failure-to-success correlation in Splunk would also require raw `4625` records with matching timestamps, source, destination, and account. That correlation has not been claimed as a completed test.

## Threat interpretation

Trying several passwords against one account is consistent with **Password Guessing (T1110.001)** in the Credential Access tactic. MITRE includes SMB among the services targeted by password guessing. [MITRE ATT&CK: T1110.001](https://attack.mitre.org/techniques/T1110/001/).

The accepted credential demonstrates authentication in this lab. Administrator access, file access, command execution, lateral movement, and data theft are not shown.

## Analyst workflow

### 1. Confirm scope and event meaning

Search the interval containing the test or alert:

```spl
index=main (EventCode=4624 OR EventCode=4625)
```

Check the originating computer and raw messages. Microsoft documents `4625` as a failure, `4624` as a success, and logon type `3` as network access. [Microsoft: event 4625](https://learn.microsoft.com/windows/security/threat-protection/auditing/event-4625), [Microsoft: event 4624](https://learn.microsoft.com/windows/security/threat-protection/auditing/event-4624).

The 1,889 results in the screenshot are the broad search population. Narrow to the relevant endpoint, source, account, and short time interval before attributing events to the test.

### 2. Identify the target account and source

Windows events contain subject and target-account information. Depending on extraction, `Account_Name` can include service, machine, or subject accounts. The relevant success-event field here is **New Logon / Account Name: `VictimUser`**.

[queries/authentication_timeline.spl](../queries/authentication_timeline.spl) produces a chronological table. It uses `TargetUserName` when available and extracts the target from the English text-event sections as a fallback. Source address and logon type are normalized too.

Validate extracted fields against raw success and failure records. Then narrow to `user="VictimUser"`, `src_ip="10.242.185.195"`, and the confirmed endpoint.

### 3. Correlate failures before success

[queries/failed_then_success.spl](../queries/failed_then_success.spl) proposes a candidate when:

- The same endpoint, source IP, and target account have at least **three failures in the preceding five minutes**.
- The current event is a successful network logon (`4624`, type `3`).
- A failure timestamp precedes the success timestamp.

It sorts events in ascending order and applies a five-minute `streamstats` window. Splunk documents that ascending order makes this window represent preceding events. [Splunk: streamstats](https://help.splunk.com/en/splunk-enterprise/search/spl-search-reference/10.0/search-commands/streamstats).

The analytic has not been run on the lab instance or enabled as an alert. Its threshold suits a small exercise and needs tuning. Include at least five minutes of lookback before candidate successes; truncated time ranges can omit earlier failures. Large volumes can reach the configured stream-window limit.

Before deployment, validate these cases:

| Case | Expected behavior |
| --- | --- |
| Three failures followed by success for one endpoint/source/account inside five minutes | Return the success candidate. |
| Failures without later success | No success candidate. |
| Success from a different source, account, or endpoint | Keep groups separate. |
| Failures outside five minutes, or fewer than three failures | Threshold not satisfied. |
| Missing source or target account | Exclude and investigate collection/extraction coverage. |

Several successes can reuse the same preceding failures. Scheduled use needs suppression by endpoint/source/account, a defined schedule and lookback, and testing for late-arriving events.

### 4. Enrich and assess

In the hypothetical business, the analyst would:

1. Identify the owner and purpose of source asset `10.242.185.195`.
2. Confirm the destination asset; resolve the duplicate receiver/target address in the lab captures.
3. Ask whether the account owner expected this access and identify the account's role and privileges.
4. Inspect failure reason/status fields and related authentication records.
5. Review available share-access and endpoint telemetry for actions after the logon.
6. Preserve raw records and record timezone information before deciding on response.

### 5. Respond to confirmed unauthorized access

If the organization confirms unauthorized access, its incident team could isolate the source, restrict the affected account or sessions, rotate the credential, and scope access to other assets under its incident process.

These are proposed production actions. The lab does not claim containment, session termination, or credential rotation.

## False positives and tuning

| Possible cause | Validation |
| --- | --- |
| Mistyped password followed by successful access | Confirm the device and expected user activity. |
| Mapped drive or scheduled task using an old password | Check configuration owners and recent credential changes. |
| Authorized security exercise | Match the source, account, and time to the approved test. |
| Shared or NATed source | Obtain asset/session context before attribution. |
| Subject and target accounts mixed together | Inspect raw event sections and fix normalization. |

Tune thresholds per asset and account type. A daily chart supports trend review; a short-window correlation supports a specific sequence investigation.

## Outcome

The evidence supports a completed lab exercise with successful SMB authentication, an indexed Windows network-logon event for `VictimUser`, and a saved monitoring dashboard. The SOC lesson is to connect a failure trend to a specific account, source, endpoint, and subsequent activity before declaring an incident or taking containment action.

See the [architecture report](architecture-report.md) and [evidence register](evidence-register.md) for the underlying configuration and captures.
