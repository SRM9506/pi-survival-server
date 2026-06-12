# pi-survival-server

A self-contained, offline-first knowledge and survival server running on a Raspberry Pi 3B+. It serves an offline Wikipedia/medical knowledge base, a library of survival and emergency-preparedness documents, and styled offline maps — all accessible from any phone or laptop over a self-hosted WiFi hotspot, with **zero internet or cell service required**.

Pull it out, power it on, connect to its WiFi, and you have a full survival reference system in your pocket.

---

## What it does

- **Knowledge base** — Offline Wikipedia (Simple English), a medical encyclopedia (WikiMed), and a post-disaster survival wiki, all fully searchable via [Kiwix](https://www.kiwix.org/).
- **Document library** — 70+ survival, military, medical, and emergency-preparedness PDFs, browsable in any browser.
- **Offline maps** — The entire Eastern US as a single vector map file, rendered in-browser with [MapLibre](https://maplibre.org/) + [Protomaps](https://protomaps.com/). GPS-ready (plug in a USB dongle and your live position appears).
- **Self-hosted hotspot** — Broadcasts its own `SurvivalKit` WiFi network so any device can connect and access everything offline.

Everything is served through a single browser homepage. Nothing leaves the device. No internet, no cloud, no accounts.

---

## Hardware

| Component | Notes |
|---|---|
| Raspberry Pi 3B+ | The server itself (1GB RAM, armv7l) |
| 64GB+ microSD card | OS + all content |
| USB WiFi dongle (TP-Link 802.11ac, RTL88xx) | Runs the hotspot on a second interface so the built-in WiFi stays free for SSH/home network |
| USB GPS dongle *(optional)* | VK-172 or GlobalSat BU-353S4 — enables live positioning on the maps |
| Power | USB power supply, battery bank, or solar for field use |

### Dual-interface design

The Pi runs **two WiFi interfaces at once**:

- `wlan0` (built-in) — stays connected to a home network for SSH and updates
- `wlan1` (USB dongle) — permanently broadcasts the `SurvivalKit` hotspot

This means the hotspot is always live without ever dropping remote access — no mode-switching required. In the field, `wlan0` simply has nothing to connect to while `wlan1` keeps serving.

---

## Software stack

| Layer | Tool |
|---|---|
| OS | Raspberry Pi OS |
| Web server / reverse proxy | Nginx |
| Knowledge base | Kiwix (`kiwix-serve`) serving `.zim` files |
| Maps | MapLibre GL JS + PMTiles (Protomaps basemap), served statically |
| GPS *(optional)* | `gpsd` + a small Python HTTP bridge exposing position as JSON |
| Hotspot | hostapd (AP) + dnsmasq (DHCP) |

---

## Architecture

```
                 ┌─────────────────────────────┐
   Phone/Laptop  │      Raspberry Pi 3B+        │
   ────WiFi────► │  wlan1: SurvivalKit hotspot  │
                 │         (x.x.x.x)        │
                 │                              │
                 │   Nginx (port 80)            │
                 │    ├── /        homepage     │
                 │    ├── /kiwix/  Kiwix :8888  │
                 │    ├── /docs/   PDF library  │
                 │    ├── /maps/   MapLibre     │
                 │    └── /gps     GPS bridge   │
                 │                              │
                 │  wlan0: home WiFi (SSH)      │
                 └─────────────────────────────┘
```

---

## Content

### Knowledge base (Kiwix `.zim` files)
- Simple English Wikipedia (full, with images)
- WikiMed — medical encyclopedia (73,000+ articles)
- Post-disaster survival wiki

### Document library (sample)
US Army Survival Manual (FM 21-76), Where There Is No Doctor, Where There Is No Dentist, FEMA Citizen Preparedness Guide, Nuclear War Survival Skills, map-reading & land-navigation field manuals, cold-weather survival handbooks, first-aid manuals, bug-out/get-home bag checklists, and dozens more.

### Maps
- Entire Eastern US (Maine to Florida), styled vector tiles, single PMTiles file
- GPS-ready — `gpsd` + bridge staged so a USB GPS dongle is plug-and-play

---

## Access

Connect any device to the **SurvivalKit** WiFi network, then open:

```
http://x.x.x.x
```

From there: Knowledge Base, Documents, and Maps.

---

## Status

| Feature | State |
|---|---|
| Kiwix knowledge base | ✅ Working |
| Document library | ✅ Working |
| Offline maps | ✅ Working |
| Self-hosted hotspot (dual-interface) | ✅ Working |
| GPS positioning | 🟡 Software staged — awaiting dongle |
| Meshtastic / LoRa comms | ⬜ Planned |
| Battery / solar power | ⬜ Planned |

---

## Roadmap

- [ ] Add USB GPS dongle for live positioning
- [ ] Meshtastic + LoRa HAT for off-grid mesh communication
- [ ] Battery bank / solar power for true field deployment
- [ ] Pelican-style weatherproof case build
- [ ] Expand Kiwix library (Wikivoyage, additional medical/reference ZIMs)

---

## Build log

Step-by-step build notes live in [`build-log/`](build-log/).
