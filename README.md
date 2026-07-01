# Telemetry Format Converter

A Python solution that unifies two different telemetry message formats into a single, consistent output format.

## Overview

Devices in the field send telemetry data in two different JSON formats. This project implements conversion functions that transform both formats into one unified structure.

## Files

| File | Description |
|---|---|
| `main.py` | Contains the conversion logic and unit tests |
| `data-1.json` | Sample telemetry message in Format 1 |
| `data-2.json` | Sample telemetry message in Format 2 |
| `data-result.json` | Expected unified output format |

## Input Formats

**Format 1** — flat structure with a slash-delimited location string and epoch timestamp in milliseconds:
```json
{
    "deviceID": "dh28dslkja",
    "deviceType": "LaserCutter",
    "timestamp": 1624445837783,
    "location": "japan/tokyo/keiyō-industrial-zone/daikibo-factory-meiyo/section-1",
    "operationStatus": "healthy",
    "temp": 22
}
```

**Format 2** — nested device object, individual location fields, and an ISO 8601 timestamp:
```json
{
    "device": { "id": "dh28dslkja", "type": "LaserCutter" },
    "timestamp": "2021-06-23T10:57:17.783Z",
    "country": "japan",
    "city": "tokyo",
    "area": "keiyō-industrial-zone",
    "factory": "daikibo-factory-meiyo",
    "section": "section-1",
    "data": { "status": "healthy", "temperature": 22 }
}
```

## Unified Output Format

```json
{
    "deviceID": "dh28dslkja",
    "deviceType": "LaserCutter",
    "timestamp": 1624445837783,
    "location": {
        "country": "japan",
        "city": "tokyo",
        "area": "keiyō-industrial-zone",
        "factory": "daikibo-factory-meiyo",
        "section": "section-1"
    },
    "data": {
        "status": "healthy",
        "temperature": 22
    }
}
```

## Implementation Details

- **`convertFromFormat1(jsonObject)`** — splits the `location` string on `/` into its five components and maps `operationStatus` → `status`, `temp` → `temperature`.
- **`convertFromFormat2(jsonObject)`** — parses the ISO 8601 timestamp string and converts it to milliseconds since epoch using `datetime`; extracts `device.id`/`device.type` and copies location and data fields directly.
- **`main(jsonObject)`** — detects which format was passed based on the presence of a `device` key, then routes to the appropriate conversion function.

## Running the Tests

```bash
python main.py
```

Expected output:
