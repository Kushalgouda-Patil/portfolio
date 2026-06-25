---
title: "The Art of Talking to a Server"
description: "From the humble request-response cycle to full-duplex real-time streams."
publishDate: "2026-05-13"
tags: ["http", "networking", "technology", "websocket"]
---

*From the humble request-response cycle to full-duplex real-time streams.*

## HTTP: The Foundation

Every web communication ultimately rests on **HTTP** (HyperText Transfer Protocol). Its model is deceptively simple: a client sends a *request*, the server sends back a *response*, and the connection closes. Stateless, synchronous, and clean.

HTTP's statelessness is both its strength and its limitation. Each request is independent — the server holds no memory of previous interactions. This makes HTTP wonderfully scalable, but it also means the server can *never initiate* a message to the client. The burden of asking always falls on the browser.

> The server doesn't know you exist until you knock.

- HTTP/1.1 introduced persistent connections (`Keep-Alive`), allowing multiple requests over one TCP connection.
- HTTP/2 added multiplexing, many concurrent streams over a single connection.
- HTTP/3 swapped TCP for QUIC. But the request-response paradigm itself stayed intact across all versions.

## AJAX: Async Without a Page Reload

**AJAX** (Asynchronous JavaScript and XML) isn't a protocol, it's a *technique*. Instead of submitting a form and reloading the entire page, AJAX lets JavaScript fire an HTTP request in the background and update only a piece of the DOM with the response.

The browser's `XMLHttpRequest` object (introduced circa 2000) made this possible. Today, the `fetch()` API is the modern, promise-based replacement.

AJAX transformed the web from a series of page loads into responsive applications. Gmail (2004) and Google Maps (2005) famously demonstrated what became possible.

- Search autocomplete
- Form validation
- Infinite scroll / pagination
- Submitting data without reload

## Short Polling: Ask. Wait. Repeat.

What if you need *fresh* data continuously — a stock price or order status — but all you have is HTTP? The naïve solution: poll the server on a fixed interval using AJAX.

Short polling is trivially easy to implement: a `setInterval` wrapping a `fetch` call. But it has serious costs. Most responses will return nothing new, yet every request still consumes bandwidth, opens a connection, and occupies a server thread. With 10,000 clients polling every second, the math turns ugly fast.

> Short polling is the equivalent of asking "are we there yet?" every few seconds for the entire car journey.

## Long Polling: Hold the Line

**Long polling** is a clever trick — the client sends a request, but the server *deliberately delays its response* until new data is available or until a timeout fires. Once the client gets a response (with or without data), it immediately fires another request, keeping the cycle alive.

Long polling dramatically reduces unnecessary requests — a response only comes when there's something to say. However, the server must hold open thousands of suspended connections simultaneously, which demands a non-blocking, event-driven architecture (Node.js, Go, Nginx) rather than a thread-per-request model.

Facebook Messenger and early Campfire used long polling extensively before WebSockets matured.

- Chat notifications
- Order/job status updates
- Legacy browser support
- Simple push behind firewalls

## Server-Sent Events: One-Way Stream Over HTTP

Server-Sent Events (SSE) is a W3C standard that enables a server to push a continuous stream of data to the browser over a single, long-lived HTTP connection, with no protocol upgrade required. Unlike long polling, the connection stays open indefinitely; the server just keeps writing.

The wire format is refreshingly plain — just UTF-8 text with `data:`, `event:`, and `id:` fields separated by newlines. The browser's built-in `EventSource` API handles parsing, and crucially, it *automatically reconnects* if the connection drops, something WebSocket requires manual wiring for.

SSE is strictly unidirectional: server to client only. If the client needs to send data back, it uses a separate `fetch()` call. WebSocket is fully bidirectional on a single connection. Choose SSE when your data only flows one way; choose WebSocket when both sides talk freely.

- AI / LLM response streaming
- Live sports scores
- Server log tailing
- CI/CD build progress
- Live notifications feed
- Dashboard metric updates

## WebSocket: A Permanent Open Channel

WebSocket is the genuine upgrade to HTTP for real-time communication. It begins life as an HTTP request — the famous *handshake* — then upgrades the connection to a persistent, bidirectional channel. After that, either side can send data frames at any time without new HTTP headers.

The protocol overhead per message shrinks from hundreds of bytes (HTTP headers) to as few as **2 bytes** for a WebSocket frame. This makes WebSocket ideal for high-frequency, low-latency scenarios where every millisecond counts.

- Live collaborative editing
- Multiplayer games
- Financial trading feeds
- Real-time chat (Slack, Discord)

```javascript
const ws = new WebSocket('wss://api.example.com/feed');
ws.onmessage = (e) => console.log(e.data);
ws.send(JSON.stringify({ type: 'subscribe', channel: 'prices' }));
```