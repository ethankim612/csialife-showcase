# CSIALife

**The schoolwide platform students at CheongShim International Academy use for night study requests, seminar room bookings, timetables, meals, and music room reservations.**

[csialife.com](https://csialife.com) · 600+ users · 2025 – present

> The source code stays private because the production system handles real student records. This repository documents the design and shows the current version with fictional demo data.

<p align="center">
  <img src="screenshots/v27-yaja.png" width="100%" alt="Weekly night study request grid">
</p>

## The problem

Every evening, students declare what they will do in each night study period: self-study, an after-school course, an outing, or a seminar room session. Teachers used to collect these by hand every day and type them into spreadsheets. CSIALife replaced that process, but as features piled up in the first version, the request logic developed a structural bug that left ghost records in the data teachers relied on.

## My role

**Leader & Developer.** I started as a co-developer alongside senior students and now lead development and maintenance.

- Rebuilt the night study request logic after the ghost record bug.
- Started a new codebase for the 2026–27 school year, which is the version running at csialife.com today.
- Run the system on Railway as three processes (web, Celery worker, Celery beat) on PostgreSQL and Redis.
- Added daily snapshots of the teachers' sheet history so past records survive even after Google merges old versions.

## Architecture

```mermaid
flowchart LR
    S[Students] --> W[Django + Gunicorn<br>web]
    W <--> P[(PostgreSQL)]
    W --> R[(Redis)]
    R --> B[Celery beat] --> K[Celery worker]
    K -->|17:30 sync| G[Google Sheets<br>teachers' rosters]
    K -->|00:10 snapshot| P
    W -->|meal lookup| N[NEIS Open API]
```

| Scheduled job | When | What it does |
|:--|:--|:--|
| Night study sync | 17:30 | Re-syncs every active request for the day into the teachers' sheet |
| Seminar sync | 13:00 | Writes the day's seminar room bookings to the sheet |
| Sheet history snapshot | 00:10 | Saves the previous day's sheet version to the database |
| Seminar reset | 00:30 | Clears past bookings so the next day starts clean |

## Screens

<table>
<tr>
<td width="50%"><img src="screenshots/v27-seminar.png" alt="Seminar room availability and booking form"><p align="center"><sub>Seminar room booking</sub></p></td>
<td width="50%"><img src="screenshots/v27-timetable.png" alt="Weekly class timetable"><p align="center"><sub>Timetable matched to each student ID</sub></p></td>
</tr>
<tr>
<td width="50%"><img src="screenshots/v27-meal.png" alt="Weekly meal menu"><p align="center"><sub>Weekly meals from the NEIS API</sub></p></td>
<td width="50%"><img src="screenshots/v27-music-room.png" alt="Music room reservation table"><p align="center"><sub>Music room reservations</sub></p></td>
</tr>
</table>

## Stack

Django · Gunicorn · Celery · Redis · PostgreSQL · Google Sheets API · NEIS Open API · Railway

<sub>All names, timetables, and menus in the screenshots are fictional.</sub>
