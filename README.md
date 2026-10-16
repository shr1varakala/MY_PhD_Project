
Steeradar
<div align="center"> <img src="./steerardar-logo.png" alt="Steeradar" width="140" height="140" />
Live community rides. Peer chat. Local mesh. Universal DB.
A vehicle-gated, modular, serverless-first community network — built in plain HTML, CSS & JavaScript.







</div>
📖 What is Steeradar
Steeradar is a community movement protocol. Any group that repeats a local journey — a bus route, a carpool, a jogging pack, a walk, a delivery run, an event shuttle — gets a live, discoverable lane that anyone nearby can join.

Built on plain web primitives — WebRTC, Geolocation, Bluetooth, and a single Google Sheet as the universal database — Steeradar runs entirely from GitHub Pages with zero backend infrastructure, zero commission, and zero login wall.

Enter your vehicle number → your neighbourhood comes alive.

✨ Features
🚗 Vehicle Gate
The app unlocks only after a valid vehicle number. It becomes your identity — your lanes, your Hive posts, your peer ID.

📷 Photo Documents
Optional onboarding step to upload your photo, vehicle photo, number plate, and UPI QR — stored securely on Google Drive.

🗺️ Pulse — Live Community Map
Bright OpenFreeMap canvas with emoji markers for every active lane. Real-time movement animations, pulsing rings, filters: Live now · All · Has seats · My lanes.

🛣️ Lanes — Real-Time Driver Feed
Editorial cards with animated occupancy rings, vehicle-type badges, route previews, fare tags, and one-tap Chat / Track / Request. Edit your own lanes inline.

📍 Live Tracking (Zomato-style)
Tap Track on any lane → OSRM routing draws the real driving path to your pickup, with live distance, ETA, and seat count in a floating HUD.

📡 Near — Local Mesh & Proximity Radar
Your GPS coordinate becomes a geohash — everyone within ~1 km auto-forms a PeerJS room without any server. Includes real Web Bluetooth scan.

🐝 Hive — Community Board
Deepstash-style editorial feed. Story circles for active drivers. Single-form composer (heading + body + tags). Edit and delete your own posts.

💬 Peer Chat with Persistence
Real-time WebRTC delivery + every message saved to the messages sheet. Reopening a chat loads the last 50 messages.

⚙️ Full Settings
Account (OTP sign-in) · Profile · Appearance (theme + map style) · Notifications · Privacy (GPS, ghost mode, plate visibility) · Universal DB · Data (clear / reset).

📋 Hidden Diagnostics
Long-press the Steeradar logo for 600ms → in-app log viewer with Copy · Clear · Refresh. Tap the version line 5× in Settings → reveals the cloud endpoint config.

🏗️ Architecture
Modular JS — 11 files, each under 300 lines
js/
├── api.js          · Network gateway (every fetch goes through here)
├── store.js        · Shared state, storage, session, utils
├── logger.js       · In-app console capture + log viewer
├── auth.js         · OTP login / register flow
├── onboarding.js   · Vehicle gate + photo step
├── photos.js       · Image compression + Drive upload
├── lanes.js        · Post/view lanes, edit, map markers
├── hive.js         · Posts + likes + edit/delete
├── chat.js         · WebRTC + persistent messages
├── presence.js     · Live tracking + geohash mesh
└── app.js          · Boot, tabs, settings, wiring
Every module imports only what it needs. Changes to chat.js never touch lanes.js.

Peer layers
PEER A — Vehicle ID
  steeradar-veh-<VEHICLE>
  Used for: lane chats (1:1 WebRTC)

PEER B — Geohash Room Slot
  steeradar-loc-<geohash>-<slot 1..20>
  Used for: local mesh (auto-forms within ~1 km)

PEER C — Bluetooth
  navigator.bluetooth.requestDevice()
  Used for: proximity scan of BLE peripherals
Cloud sync flow
Publish lane → save local + push to Apps Script → row in Google Sheet
              ↓
Every 15s:  fetch lanes + posts → merge → re-render
Every 20s:  heartbeat presence with GPS + vehicle type
Live tracking flow
Tap Track → draw pickup pin + vehicle pin
          → OSRM fetch driving route (free, no key)
          → draw teal dashed polyline
          → recompute every 8s as vehicle moves
          → HUD shows distance / ETA / seats
🛠️ Tech Stack
Layer	Technology
Markup	HTML5
Styling	Vanilla CSS (light-first, Deepstash-inspired)
Logic	Vanilla JavaScript (ES Modules, ES2020)
Maps	Leaflet + MapLibre GL
Tiles	OpenFreeMap — no API key
Routing	OSRM — free, no signup
Geocoding	Nominatim (OpenStreetMap)
Real-time chat	PeerJS (WebRTC)
Proximity mesh	Custom geohash + PeerJS slot claiming
Bluetooth	Web Bluetooth API
Universal DB	Google Apps Script + Sheets
Image storage	Google Drive (via Apps Script)
Hosting	GitHub Pages
Zero build step. Zero npm. Zero bundlers.

🚀 Quick Start
Run it live
👉 https://YOUR_GITHUB_USERNAME.github.io/Steeradar/

Run locally
git clone https://github.com/YOUR_GITHUB_USERNAME/Steeradar.git
cd Steeradar
python3 -m http.server 8000
# open http://localhost:8000
Deploy your own
Fork this repo
Settings → Pages → Source: main / root
Live in ~30 seconds at https://<your-username>.github.io/Steeradar/
🗄️ Apps Script Backend Setup
The DB lets every device see the same lanes, posts, messages, and presence. Setup takes ~5 minutes.

1. Create the project
Open script.new → rename to Steeradar DB
Delete default code → paste the contents of `Code.gs`
Ctrl + S
2. Authorize + create the Sheet
Function dropdown → setup → Run
Review permissions → Advanced → Go to project → Allow
Copy the printed Sheet URL
3. Run the self-test
Function dropdown → selfTest → Run
Expect:
users    write: PASS
sessions write: PASS
lanes    write: PASS
hive     write: PASS
messages write: PASS
presence write: PASS
trips    write: PASS
4. Deploy as Web App
Deploy → New deployment → Web app
Execute as: Me
Who has access: Anyone
Copy the /exec URL
5. Wire it up
Edit config.js:

SHEET_API_URL: 'https://script.google.com/macros/s/AKfyc.../exec',
SHEET_WEBHOOK_SECRET: 'steeradar-secret-2026',
Commit, push, hard-refresh. Verify in Settings → Universal DB → Test connection → ✓ Connected.

📊 Sheet Schema
Sheet	Purpose
users	phone, email, name, vehicle, avatar, plate, UPI QR, session refs
sessions	Auth tokens (90-day expiry)
otps	Login + register OTP audit trail
lanes	Live lanes with vehicleType + GPS + lastSeen
hive	Community posts + likes
messages	Persistent chat history
presence	Active vehicles + GPS heartbeat (2-min freshness)
trips	Start/end time, distance, fare, rating
🎯 Beyond buses — the real USP
A vehicle number is one kind of persistent public identifier. The same pattern works for:

Activity	Identifier	Lane	Fare
Community bus	Bus reg	Route + occupancy	Ticket price
Carpool	Car reg	Daily commute	Fuel share
Auto pool	Auto reg	Shared trip	Split meter
Walking group	Group name	Route + pace	Free
Morning jog	Route ID	Pace + meetup	Free
Cycling pack	Pack name	Route + speed	Free
Event shuttle	Event code	Pickup points	Bundled
Delivery coop	Vehicle ID	Delivery route	Per parcel
Farm-to-home	Producer ID	Weekly run	Per basket
School run	Parent group	School route	Free swap
Steeradar is a protocol for community movement. Fork it, run your own.

🗺️ Roadmap

Vehicle gate & validation

Live community map (OpenFreeMap)

WebRTC lane chats (PeerJS)

Geohash local mesh (serverless)

Real Web Bluetooth scan

Universal DB via Google Sheets

Modular 11-file architecture

OTP authentication (email)

Image uploads (Google Drive)

Live tracking (OSRM route lines)

In-app diagnostics log viewer

Edit / delete lanes and posts
UPI payment flow (scan QR → pay → confirm)
Post-trip ratings
Push notifications (FCM)
PWA install prompt
Offline mode (Service Worker)
Multi-language support
Self-hosted OSRM
🔐 Privacy & Security
No tracking — no analytics, no third-party scripts
Vehicle-gated — no anonymous access
Cloud-optional — everything works locally if cloud sync is disabled
Ghost mode — hide your location from the map
Masked plates — control who sees your number plate
UID-protected — every write goes through the vehicle identity
Secret-keyed — Apps Script rejects any request without the correct key
Session tokens — 90-day, hashed, revocable
🤝 Contributing
Steeradar is intentionally minimal. No build step, no framework, no lock-in.

git checkout -b feature/amazing-idea
git commit -m "Add amazing idea"
git push origin feature/amazing-idea
Open a PR. Bug reports with the in-app log (long-press logo → Copy) are gold.

👤 Project Maintainer
Shravan
Steeradar is a community movement application built with HTML, CSS, and JavaScript. This version is maintained by Shravan.

The project aims to remain:

Easy to read and understand
Deployable with a GitHub account
Focused on privacy and simplicity
📬 Connect
Maintainer: Shravan

GitHub: https://github.com/YOUR_GITHUB_USERNAME

Replace YOUR_GITHUB_USERNAME with your actual GitHub username before publishing.

📜 License
MIT License. When redistributing the project, retain the original copyright and permission notices from the repository's LICENSE file.

🙏 Built on the shoulders of
Leaflet — the friendliest map library on the web
MapLibre GL — the open vector map engine
OpenFreeMap — free, keyless vector tiles
OSRM — open-source routing machine
PeerJS — WebRTC made human
OpenStreetMap — the free world map
Nominatim — reverse geocoding for everyone
Google Apps Script — the serverless backend nobody pays for
<div align="center">
🌟 Thank you for exploring Steeradar
Steeradar is a project exploring simpler, more local community mobility.

⭐ Project on GitHub · 🚀 Live Demo

<sub>Maintained by Shravan</sub>

</div>
