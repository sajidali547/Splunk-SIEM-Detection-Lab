# Splunk SIEM Detection Lab

I used Splunk to collect Windows Security logs and monitor failed and successful logins.

## Lab setup

- **Splunk Enterprise:** receives and displays logs.
- **Universal Forwarder:** sends Windows Security logs to Splunk on port `9997`.
- **Kali Linux:** tests SMB logins using the lab account `VictimUser`.

## What I did

1. Enabled receiving on port `9997`.
2. Configured the forwarder to collect Security logs.
3. Tested three wrong passwords, followed by a successful login.
4. Checked the logs in Splunk and built a dashboard.

## Real-world scenario

Someone tries several passwords for an employee account and then logs in successfully. A SOC analyst checks the account, source IP, and login records to see whether the access was expected. This lab demonstrates that pattern using a test account.

## Dashboard

**SOC - Enterprise Monitoring** shows failed logins over time and successful logins by account.

![SOC - Enterprise Monitoring dashboard](screenshots/05-siem-dashboard.png)

## Project files

| File | Purpose |
| --- | --- |
| [inputs.conf](configs/inputs.conf) | Collect Windows Security logs. |
| [Dashboard queries](queries/dashboard_queries.spl) | Two panel searches; run each separately. |
| [Lab report](docs/architecture-report.md) | Short setup and results. |
| [Screenshots](screenshots/) | Six original screenshots from the lab. |
