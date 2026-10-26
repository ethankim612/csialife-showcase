# CSIALife

**The schoolwide platform students at CheongShim International Academy use for study hall requests, seminar room bookings, meals, and room reservations.**

[csialife.com](https://csialife.com) · 600+ users · 2025 – present

> The source code stays private because the production system handles real student records. This repository documents the design and shows the interface with fictional demo data.

<p align="center">
  <img src="screenshots/home.png" width="100%" alt="CSIALife home screen with today's requests, weekly study hall stats, and meals">
</p>

## The problem

Every evening, students declared what they would do during each night study period: study hall, an after-school course, an outing, or a seminar room session. Teachers collected these by hand every day and typed them into spreadsheets. CSIALife replaced that process, but as features piled up, the study hall request logic developed a structural bug that left ghost records in the data teachers relied on.

## My role

**Leader & Developer.** I started as a co-developer alongside senior students. I now lead development and maintenance.

- Rebuilt the study hall request logic from scratch after the ghost record bug.
- Unified seminar room booking into a single booking model and added daily cleanup jobs.
- Moved scheduled work (sheet sync, cleanup) onto Celery and deployed the stack on Railway with PostgreSQL.
- Added lab, music room, and meal features, plus a PWA and token API for mobile access.

## Architecture

```mermaid
flowchart LR
    S[Students<br>web / PWA] --> D[Django app<br>Railway]
    D <--> P[(PostgreSQL)]
    D --> R[(Redis)]
    R --> C[Celery worker + beat]
    C -->|17:00, 17:30 sync| G[Google Sheets<br>teachers' rosters]
    C -->|00:00, 23:30 cleanup| P
    D -->|meal lookup| N[NEIS Open API]
```

| Scheduled job | When | What it does |
|:--|:--|:--|
| Study hall sync | 17:00, 17:30 | Writes the day's requests into the teachers' Google Sheets |
| Seminar sync | 13:00 | Writes seminar room bookings to the sheet |
| Daily cleanup | 00:00, 23:30 | Clears past bookings and the day's request data so nothing lingers into the next day |

## Screens

<table>
<tr>
<td width="50%"><img src="screenshots/yaja.png" alt="Weekly study hall request grid"><p align="center"><sub>Study hall requests, Monday to Thursday</sub></p></td>
<td width="50%"><img src="screenshots/seminar.png" alt="Seminar room availability by room and period"><p align="center"><sub>Seminar room availability</sub></p></td>
</tr>
<tr>
<td width="50%"><img src="screenshots/meal.png" alt="Weekly meal menu"><p align="center"><sub>Weekly meals from the NEIS API</sub></p></td>
<td width="50%"><img src="screenshots/music-room.png" alt="Music room reservation table"><p align="center"><sub>Music room reservations</sub></p></td>
</tr>
</table>

## Stack

Django · Celery · Redis · PostgreSQL · Google Sheets API · NEIS Open API · Railway

<sub>All names and data in the screenshots are fictional.</sub>
