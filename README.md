# grand-race-auto
# Grand Race Auto

Grand Race Auto is a **real-time GPS racing platform** where racers compete on real streets while spectators watch the race live on an interactive map.

The experience should feel like **GTA-style racing + esports + live map tracking**.

The entire platform is **dark themed**, neon styled, and optimized for **real-time updates**.

This project focuses on:

- Real-time racing
- Live GPS tracking
- Spectator map viewing
- Crypto entry fees
- Competitive leaderboards
- Game-style race UI

---

# Core Concept

Grand Race Auto is a **real-time racing platform** where:

1. Racers join a race.
2. Racers broadcast GPS position from their phone.
3. The backend streams live location updates.
4. Spectators watch cars move in real time on a map.
5. The system calculates race position, speed, and winner.

The experience should feel like watching a **live racing game but in the real world**.

---

# UI / Design Requirements

The entire UI must be **dark themed only**.

There is **no light theme**.

Color palette:

```

background: #0a0a0a
cards: #121212
primary: #00ffff (cyan)
secondary: #00bcd4
text: #d9ffff

```

Design style:

- Cyberpunk
- GTA race HUD
- Neon UI
- Glowing borders
- High contrast

All input fields must be dark:

```

background: #0d0d0d
border: 1px solid #00ffff
color: #00ffff

```

---

# Core Features

## Racer Accounts

Users can register as racers.

Fields:

```

username
passcode
car_model
created_at

```

No email required.

---

## Race Creation

Races have:

```

race_name
start_lat
start_lng
finish_lat
finish_lng
entry_fee_usd
max_racers
status
start_time

```

Status types:

```

waiting
countdown
live
finished

```

---

## Race Lobby

Before the race starts users see a **race lobby**.

Example:

```

Race: Nairobi Midnight Run
Entry Fee: $20
Max Racers: 8

Players:
TurboKen
NeonGhost
Vortex
NightRunner

Start in: 02:30

```

---

## Live GPS Tracking

Racers broadcast location updates every **1 second**.

Example payload:

```

POST /gps

{
race_id: 24,
user_id: 8,
latitude: -1.2921,
longitude: 36.8219,
speed: 120
}

```

The backend processes GPS events and pushes updates to spectators.

Real-time streaming should use **WebSockets**.

Real-time tracking systems typically stream small GPS updates frequently and broadcast them to clients without page refresh using event-driven architectures. :contentReference[oaicite:0]{index=0}

---

## Spectator Map

Spectators watch the race on a live map.

Use:

```

Mapbox GL JS

```

Map features:

- glowing racer markers
- live position updates
- race route overlay
- motion trails behind racers
- auto zoom
- follow racer mode

Mapbox APIs allow building customizable real-time maps for web and mobile applications. :contentReference[oaicite:1]{index=1}

---

# Main Race Screen Layout

```

---

| RACE: NAIROBI MIDNIGHT RUN         LIVE 🔴     |
| ---------------------------------------------- |
|                                                |
| MAPBOX LIVE MAP                                |
|                                                |
| 🔵 NeonGhost (110km/h)                         |
| 🔴 TurboKen (125km/h)                          |
|                                                |
| ---------------------------------------------- |
| POSITIONS                                      |
| 1️⃣ TurboKen      125 km/h   0.7km to finish   |
| 2️⃣ NeonGhost     110 km/h   0.9km to finish   |
| 3️⃣ Vortex         98 km/h   1.2km to finish   |
| 4️⃣ NightRunner    96 km/h   1.5km to finish   |

---

```

---

# Racer HUD

The racer interface should feel like a **vehicle HUD**.

Display:

```

speed
race position
distance to finish
mini map
race timer

```

Example HUD:

```

SPEED
128 KM/H

POSITION
#2 / 6

DISTANCE
0.8 KM

```

---

# Race Engine Logic

Every GPS update must calculate:

```

distance_to_finish
race_position
current_speed

```

Distance calculation uses the **Haversine formula**.

Winner = first racer within **finish radius (20m)**.

---

# Live Leaderboard

Displayed next to the map.

Example:

```

1. TurboKen
   Speed: 125 km/h
   Distance: 0.7 km

2. NeonGhost
   Speed: 110 km/h
   Distance: 0.9 km

3. Vortex
   Speed: 98 km/h
   Distance: 1.2 km

```

Updates every second.

---

# Race Replay System

After race completion:

Users can replay the race.

Replay animates all racer positions over time.

Controls:

```

Play
Pause
1x speed
2x speed
4x speed

```

---

# Crypto Payments

Entry fees are paid using **Bitcoin only**.

System rules:

- Platform wallet receives BTC
- Users have internal USD balance
- Racers pay entry fees from balance
- Winners receive race prize

Users do not have personal wallets inside the platform.

---

# Database Structure

## users

```

id
username
passcode_hash
car_model
balance_usd
wins
losses
top_speed
created_at

```

## races

```

id
race_name
start_lat
start_lng
finish_lat
finish_lng
entry_fee_usd
max_racers
status
start_time

```

## race_participants

```

id
race_id
user_id
joined_at
finish_position

```

## gps_logs

```

id
race_id
user_id
latitude
longitude
speed
created_at

```

---

# Realtime Architecture

System flow:

```

Racer Phone
↓
POST GPS updates
↓
Backend API
↓
WebSocket Server
↓
Broadcast to spectators

```

Real-time tracking apps typically push location updates through WebSockets and only update map markers rather than reloading the map, enabling smooth animations and scalability. :contentReference[oaicite:2]{index=2}

---

# Recommended Tech Stack

Frontend

```

HTML
TailwindCSS
Mapbox GL JS
Socket.io

```

Backend

```

Node.js
Express
MySQL
Redis
Socket.io

```

Mobile

```

PWA
navigator.geolocation.watchPosition

```

---

# Future Features

Possible upgrades:

- race seasons
- racer ranking tiers
- team racing
- betting pools
- AI race commentary
- race heatmaps
- replay highlights
- car classes

---

# Goal

The goal is to create a **live racing platform that feels like watching a video game race but using real vehicles and real maps**.

Spectators should feel excitement watching cars move across the map in real time.

The system should prioritize:

- real-time updates
- smooth map animation
- competitive gameplay
- high-performance infrastructure
```
