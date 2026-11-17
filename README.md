# Splunk SIEM Detection Lab

Repository scaffold for a Splunk SIEM detection lab. Add your lab configuration, dashboard searches, architecture notes, and screenshots to the files below.

## Repository structure

```text
Splunk-SIEM-Detection-Lab/
├── README.md
├── configs/
│   └── inputs.conf
├── queries/
│   └── dashboard_queries.spl
├── screenshots/
│   ├── 01-splunk-receiving.png
│   ├── 02-forwarder-config.png
│   ├── 03-inputs-conf.png
│   ├── 04-raw-logs.png
│   └── 05-siem-dashboard.png
└── docs/
    └── architecture-report.md
```

## Files to complete

| Path | Purpose | Current status |
| --- | --- | --- |
| [configs/inputs.conf](configs/inputs.conf) | Splunk data input configuration | Comment-only placeholder |
| [queries/dashboard_queries.spl](queries/dashboard_queries.spl) | Searches used by the SIEM dashboard | Empty placeholder |
| [docs/architecture-report.md](docs/architecture-report.md) | Lab topology, data flow, and detection documentation | Outline to complete |
| [screenshots/](screenshots/) | Evidence of receiving, forwarding, configuration, ingestion, and the dashboard | Clearly labeled placeholder images |

## Screenshot checklist

Replace each placeholder image with your own screenshot, keeping the same filename.

- [ ] `01-splunk-receiving.png` — Splunk receiving configuration.
- [ ] `02-forwarder-config.png` — Forwarder configuration.
- [ ] `03-inputs-conf.png` — Completed input configuration.
- [ ] `04-raw-logs.png` — Ingested raw events.
- [ ] `05-siem-dashboard.png` — Completed SIEM dashboard.

This scaffold does not claim that a lab has been deployed or that detections have been tested.
