# GURUDEV — Serverless P2P Group Chat

A single-file, **zero-backend** group chat that runs entirely in the browser using
**WebRTC DataChannels**. No signaling server, no database, no accounts — just two
browsers talking directly to each other.

> ⚠️ Everything lives in browser memory. Refreshing the page ends the session and
> clears all chat history — there is no server to remember anything for you.

---

## ✨ Features

- 💬 **Group text chat** — real-time messaging between everyone in the room
- 📩 **Personal / direct messages** — tap any member to open a private 1:1 thread,
  routed only to that person (never broadcast to the group), reusing the existing
  connection — no extra handshake needed
- 📎 **File sharing** — send images, videos, PDFs, Word/Excel/PowerPoint docs, zips,
  and more, straight over the DataChannel (chunked + base64 encoded)
- 🖼️ **Inline previews** — images and videos render directly in the chat; other file
  types show a tappable download card
- 👥 **Multi-user invites** — host can generate several invite codes at once and
  bring people in independently, without disturbing in-progress connections
- 🔔 **Unread badges** — get notified with a badge + toast when someone DMs you
  while you're viewing another thread
- 🎨 **Dark, terminal-inspired UI** — built with Tailwind CSS, zero build step

## 🧠 How it works (architecture)

Browsers can't open a raw socket to an arbitrary IP address — that's a sandbox
restriction. WebRTC solves this with a one-time **signaling handshake**: two SDP
blobs (an "offer" and an "answer") have to be exchanged once, by any means, before
a direct DataChannel can open.

Since this app has **no backend**, that handshake is done manually:

1. The **host** generates an Offer code (base64 text) and shares it with a friend
   (via WhatsApp, email, anything).
2. The **peer** pastes the Offer, and the app generates an Answer code back.
3. The peer sends the Answer back to the host, who pastes it in.
4. ICE negotiation completes → a **direct WebRTC DataChannel** opens. From this
   point, no server or third party is involved in the chat at all.

**Topology — star/hub:** every peer connects directly to the **host** only. The
host's browser tab relays chat messages between everyone else. This keeps manual
signaling usable for groups — a full mesh would need every pair of people to swap
codes, which explodes past 2–3 users.

Personal DMs use this same star topology: a direct message travels
`peer → host → target peer`, but the host relays it to **exactly one** recipient
instead of broadcasting it to the room.

## 🚀 Usage

1. Open `index.html` in a browser (Chrome/Edge/Firefox recommended).
2. Enter a nickname and choose **Host a Room** or **Join a Room**.
3. **Hosting:** generate an invite code, send it to a friend, paste back their
   answer code to connect them. Repeat for more people.
4. **Joining:** paste the host's offer code, generate your answer code, and send
   it back to the host.
5. Once connected, chat away — send text, files, or tap a member's name for a
   private conversation.

No installation, no build tools, no dependencies — it's a single HTML file.

## 🛠️ Tech stack

- Vanilla JavaScript (no framework)
- WebRTC (`RTCPeerConnection` + `RTCDataChannel`)
- Tailwind CSS (precompiled, no build step)
- Google's public STUN server (`stun.l.google.com:19302`) — used only so a
  browser can discover its own public address; no chat data ever passes through it

## ⚠️ Known limitations

- **No persistence** — refreshing the page loses the entire chat and connection;
  everyone has to redo the invite/join handshake
- **No delivery confirmation** — if you message someone whose connection has
  silently dropped, there's currently no explicit "not delivered" warning
- **File size cap** — 30MB per file, since chunked-over-DataChannel transfer
  isn't built for huge files
- **Manual signaling** — works best for small groups (a handful of people);
  not designed for large rooms

## 📄 License

Personal project — use, modify, and share freely.

---

Built by **Gurudev**.
