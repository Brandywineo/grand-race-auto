# Grand Race Auto — System Architecture

This document defines how the **real-time racing engine** works.

The race engine is responsible for:

- ingesting GPS updates from racers
- maintaining race state
- calculating live race positions
- broadcasting updates to spectators
- detecting cheating
- determining the race winner

The system must support **real-time updates every 1 second**.

---

# Core System Components

The system is composed of five major services.

1. GPS Ingestion Service
2. Race State Engine
3. Redis Position Cache
4. WebSocket Broadcast Server
5. Race Result Processor

High-level architecture:

```

Racer Phone
↓
GPS API
↓
Race Engine
↓
Redis Cache
↓
WebSocket Server
↓
Spectators

```

---

# 1. GPS Ingestion Service

This service receives GPS updates from racers.

Endpoint:

```

POST /api/gps

```

Example payload:

```

{
race_id: 24,
user_id: 8,
latitude: -1.2921,
longitude: 36.8219,
speed: 120,
heading: 90
}

```

Update frequency:

```

1 second

```

Responsibilities:

- validate payload
- verify racer is in the race
- store update
- push update to race engine

---

# 2. Race State Machine

Every race has a defined lifecycle.

Race states:

```

waiting
countdown
live
finished
cancelled

```

Transitions:

```

waiting → countdown → live → finished

```

State rules:

### waiting
racers can join

### countdown
race locked
10 second countdown begins

### live
GPS updates processed
positions calculated

### finished
winner declared
results stored

---

# 3. Redis Position Cache

Redis stores **live race state**.

Using Redis prevents constant database queries.

Structure example:

```

race:24:positions

```

Example data:

```

user_id → {
lat
lng
speed
distance_to_finish
}

```

Redis allows:

- fast reads
- real-time updates
- leaderboard calculations

---

# 4. Distance Calculation

Distance to finish is calculated using the **Haversine formula**.

Example pseudocode:

```

distance = haversine(
current_lat,
current_lng,
finish_lat,
finish_lng
)

```

This distance determines race position.

---

# 5. Position Calculation

Race positions are determined by:

```

closest distance to finish

```

Algorithm:

1. read all racer positions from Redis
2. calculate distance to finish
3. sort by shortest distance
4. assign rank

Example:

```

1 TurboKen   0.6km
2 NeonGhost  0.8km
3 Vortex     1.2km

```

---

# 6. WebSocket Broadcast

Spectators receive real-time updates through WebSockets.

Every GPS update triggers broadcast.

Example event:

```

race_update

```

Payload:

```

{
race_id: 24,
racers: [
{user:"TurboKen", lat:-1.29, lng:36.82, speed:125},
{user:"NeonGhost", lat:-1.28, lng:36.83, speed:110}
]
}

```

Clients update map markers in real time.

---

# 7. Winner Detection

The finish line is defined as:

```

finish_lat
finish_lng
finish_radius = 20 meters

```

Winner logic:

```

if distance_to_finish <= finish_radius

```

The first racer entering the radius wins.

---

# 8. Anti-Cheat Detection

The system must detect GPS manipulation.

Checks include:

### impossible speed

```

speed > 300 km/h

```

### teleport detection

If distance jump between updates exceeds realistic movement.

Example:

```

> 500 meters in 1 second

```

### route deviation

Racers must stay near the defined race path.

Violations trigger:

```

race disqualification

```

---

# 9. GPS Storage Strategy

Not every update should be written to the database.

Strategy:

```

Redis → live updates
Database → periodic snapshots

```

Example:

Store every:

```

10 seconds

```

This prevents database overload.

---

# 10. Race Replay Data

Replay is generated using stored GPS snapshots.

Replay structure:

```

timestamp
user_id
lat
lng
speed

```

The frontend replays this timeline.

---

# 11. Scaling Strategy

To support large audiences:

### Redis

Used for:

- race state
- racer positions

### WebSocket clustering

Multiple socket servers can broadcast race events.

### CDN

Static assets delivered via CDN.

---

# 12. Recommended Tech Stack

Backend:

```

Node.js
Express
Socket.io
Redis
MySQL

```

Frontend:

```

Mapbox GL JS
TailwindCSS
Vanilla JS or React

```

Mobile:

```

PWA
navigator.geolocation.watchPosition

```

---

# 13. Race Data Flow

Full race flow:

```

1 racer joins race
2 race countdown begins
3 racers broadcast GPS
4 backend updates Redis
5 backend calculates positions
6 WebSocket broadcasts updates
7 spectators see live race
8 first racer reaches finish
9 race marked finished

```

---

# 14. Performance Goals

Target performance:

```

GPS update latency < 500ms
Map update latency < 1 second

```

Concurrent users goal:

```

1000 spectators per race

```

---

# 15. System Priority

The system must prioritize:

1. real-time updates
2. smooth map animation
3. race accuracy
4. scalability
5. cheat detection

The race experience must feel like **watching a live multiplayer racing game**.
```

