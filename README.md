# VScan

VScan is an interactive vulnerability scanning console built with Python and Nmap.
It provides a framework-style CLI (`vscan >`) to configure targets, choose scan profiles, run concurrent scans, and export reports.

## Features

- Interactive console experience (similar to security frameworks): set options once, run scans repeatedly
- Multi-target scanning with concurrency controls
- Large set of built-in Nmap scan profiles
- Custom Nmap argument support via `custom:<args>`
- Service/version collection and basic CVE matching from a local lookup table
- Optional `searchsploit` lookups per discovered service/version
- Ranked and deduplicated `searchsploit` results for cleaner output
- Report export to JSON, TXT, CSV, and HTML

## Important Note

Only scan systems you own or have explicit permission to test.

## Requirements

- Linux (recommended)
- Python 3.9+
- Nmap installed and available in PATH
- Optional: `searchsploit` (Exploit-DB package) for exploit lookup integration

## Installation

1. Clone the project.
2. Install Python dependencies:

```bash
pip install -r requirements.txt
```

3. Ensure `nmap` is installed:

```bash
nmap --version
```

4. Optional: ensure `searchsploit` is installed:

```bash
searchsploit --version
```

## Running VScan

Run from the project directory:

```bash
sudo python vscan.py
```

> `sudo` is often required for privileged scan types (for example SYN scan).

## Interactive Commands

- `show options` — display current global options
- `set <OPTION> <VALUE>` — set an option
- `run` — start scan with current options
- `scan` — alias for `run`
- `exploit` — alias for `run`
- `scan_types` — list available scan types
- `help scan_types` — help table for scan types
- `help output` — output file format guidance
- `exit` — quit console

## Global Options

- `TARGETS` (default: empty)
	- Comma-separated list of targets
	- Example: `set TARGETS 192.168.1.10,scanme.nmap.org`

- `SCAN_TYPE` (default: `syn`)
	- Uses built-in profiles or custom args
	- Example: `set SCAN_TYPE comprehensive`
	- Custom example: `set SCAN_TYPE custom:-sS -Pn -sV --script vuln`

- `PORTS` (default: `1-1024`)
	- Nmap ports syntax
	- Example: `set PORTS 1-65535`

- `THREADS` (default: `10`)
	- Max number of targets scanned in parallel
	- Does not need to equal number of targets

- `OUTPUT` (default: `None`)
	- Report output file path
	- Supported extensions: `.json`, `.txt`, `.csv`, `.html`, `.htm`
	- Disable output with: `none`, `null`, or `0`

- `SEARCHSPLOIT` (default: `True`)
	- Enables/disables searchsploit enrichment for discovered services
	- Example: `set SEARCHSPLOIT false`

- `SEARCHSPLOIT_MAX` (default: `3`)
	- Maximum number of ranked searchsploit matches shown per discovered service
	- Must be an integer greater than `0`
	- Example: `set SEARCHSPLOIT_MAX 5`

## Typical Workflow

```text
vscan > set TARGETS 192.168.1.10, scanme.nmap.org
vscan > set SCAN_TYPE syn
vscan > set PORTS 1-1000
vscan > set THREADS 5
vscan > set SEARCHSPLOIT true
vscan > set SEARCHSPLOIT_MAX 3
vscan > set OUTPUT report.json
vscan > run
```

## Output and Reporting

- Console output shows discovered services and matched findings.
- If `OUTPUT` is set, a report file is generated.
- Findings may include:
	- source type (`known-cve` or `searchsploit`)
	- exploit DB ID and path (when available)
- Searchsploit entries are filtered and ranked to reduce noisy matches.
- `SEARCHSPLOIT_MAX` controls how many searchsploit entries are shown per service.

## Troubleshooting

- `Import "nmap" could not be resolved`
	- Install dependencies from `requirements.txt`
	- Verify you are using the same Python environment where `python-nmap` is installed

- `searchsploit not found`
	- Install searchsploit or disable with: `set SEARCHSPLOIT false`

- Too many / too few exploit suggestions
	- Tune with: `set SEARCHSPLOIT_MAX <number>`


## Project Structure

- `vscan.py` — interactive console and scanning engine
- `requirements.txt` — Python dependencies
- `README.md` — documentation
