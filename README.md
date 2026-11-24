# Aadhaar Image Check

An application that runs a set of **heuristic image checks** on an
uploaded Aadhaar-card image. It is an educational screening demo, not an
identity-verification system or a trained deepfake detector.

> **Important:** A score is not a probability, an accuracy measure, or proof
> that a card is genuine or fake. Do not use this tool to make identity,
> eligibility, law-enforcement, or other consequential decisions. Verify a
> Secure QR code with UIDAI's official app or service.

## What it checks

The app reports the checks it could run, each with a status and explanation:

- Known AI-generator and image-editor strings in a small portion of the file
  data.
- Image dimensions and, for JPEGs, whether EXIF metadata is present.
- Error-level and local-noise heuristics that can flag unusual image patterns.
- Whether OpenCV can decode a QR code and whether its text resembles an
  Aadhaar-format payload. **It does not verify the QR signature.**
- If entered, whether a 12-digit number has a valid Verhoeff checksum.
  **A passing checksum does not establish that a number was issued or who it
  belongs to.**

The displayed risk score is a weighted summary of these rules. Their
thresholds have not been validated against a labelled dataset, so false
positives and false negatives are expected. A genuine image may be flagged
after editing or recompression, and a manipulated image may pass.

## Requirements

- Python 3.10 or later
- Packages listed in [`requirements.txt`](requirements.txt)

## Run locally

From the project directory, create and activate a virtual environment, then
install the dependencies:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

Start the web app:

```powershell
python app.py
```

Open <http://127.0.0.1:5000> in a browser. The development server accepts
uploads up to 15 MB.

For command-line analysis, provide an image path and optionally a number:

```powershell
python app.py .\sample.jpg
python app.py .\sample.jpg 123456789012
```

The result is printed as JSON. Use only synthetic or otherwise non-sensitive
sample images for demonstrations.

## HTTP endpoint

`POST /api/check` accepts `multipart/form-data` with:

| Field | Required | Description |
| --- | --- | --- |
| `image` | Yes | Image file to analyze |
| `number` | No | Optional number for a checksum-format check |

Successful responses contain the overall score and verdict, a list of checks,
and a reliability warning. Errors return a JSON `error` message.

## Deploy to Vercel

Import this project into Vercel from its Git repository, or run `vercel` from
the project directory and follow the CLI prompts. Vercel detects the Flask
application exposed as `app` in [`app.py`](app.py) and installs dependencies
from [`requirements.txt`](requirements.txt). The deployed function accepts
uploads up to 4 MB; local runs allow up to 15 MB.

The deployment is publicly reachable unless you enable Vercel Deployment
Protection. Do not deploy a public instance for real Aadhaar cards or numbers:
this demo is not an identity-verification system, and hosting providers may
process request data. Use synthetic sample images only.

## Privacy and deployment

The app processes the uploaded image in memory and does not intentionally save
it or call an external analysis service. The optional number is used only for
the checksum check. This describes the current app code; hosting providers,
proxies, or modified deployments may handle requests differently.

Run it only on a trusted local machine for demonstrations. The included Flask
development server is not production deployment infrastructure. Before any
network-facing deployment, add appropriate authentication, HTTPS, request
limits, logging and monitoring controls, and a documented data-retention
policy. Never upload real Aadhaar cards or numbers to a public demo or issue
tracker.

## Project files

```text
app.py
requirements.txt
README.md
```
