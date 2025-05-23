# 🎧  Proxy API (Rust)

> A privacy-respecting, fast, and lightweight API proxy for forwarding Spotify Web API traffic using randomized User-Agents.
> Built with Rust + Warp + Reqwest.

---

## 🌍 Base URL

```
http://<your-server-ip>:3030/
```

---

## 📦 Supported Endpoints

| Endpoint                 | Method | Description                           |
| ------------------------ | ------ | ------------------------------------- |
| `/playlist/:id`          | GET    | Fetch metadata for a public playlist  |
| `/track/:id`             | GET    | Fetch metadata for a specific track   |
| `/search?q=...&type=...` | GET    | Search for tracks, albums, or artists |

---

## 🔐 Authentication

Clients **must** send a valid Spotify Bearer token via the `Authorization` header.

```
Authorization: Bearer <access_token>
```

* Tokens are not validated by this proxy. They're forwarded directly to Spotify.
* Use Spotify's [Authorization Guide](https://developer.spotify.com/documentation/web-api/tutorials/code-flow) to generate tokens.

---

## 📤 Request Examples

### 🎵 Get Playlist

```
GET /playlist/37i9dQZF1DXcBWIGoYBM5M
Host: your-proxy.com
Authorization: Bearer BQC...
```

### 🎧 Get Track

```
GET /track/4uLU6hMCjMI75M1A2tKUQC
Host: your-proxy.com
Authorization: Bearer BQC...
```

### 🔍 Search Tracks

```
GET /search?q=nirvana&type=track&limit=10
Host: your-proxy.com
Authorization: Bearer BQC...
```

---

## 📥 Response

* Format: `application/json`
* Forwarded **as-is** from `https://api.spotify.com`
* You get the **exact same structure** as the original Spotify Web API

---

## 🧠 How It Works (Dev Guide)

1. Clients hit your Rust proxy (e.g. `/track/abc123`)
2. Proxy forwards the request to `https://api.spotify.com/v1/track/abc123`
3. A **random User-Agent** is set for each request
4. Authorization headers are passed through unchanged
5. Responses are streamed directly back to the client

---

## 🛠️ Developing Clients for This Proxy

You can use any Spotify Web API-compatible client. Just change the **base URL** to your proxy. For example:

```
# Original:
curl -H "Authorization: Bearer TOKEN" https://api.spotify.com/v1/track/123

# Updated:
curl -H "Authorization: Bearer TOKEN" http://your-proxy.com/track/123
```

### Example with `fetch` (JavaScript)

```js
fetch('http://your-proxy.com/track/4uLU6hMCjMI75M1A2tKUQC', {
  headers: {
    'Authorization': 'Bearer ' + token
  }
}).then(res => res.json())
  .then(console.log);
```

---

## 🚧 Limitations

* Only forwards supported endpoints (`playlist`, `track`, `search`)
* Only supports **GET** methods for now
* Requires clients to handle their own token generation

---

## 🚀 Hosting

1. Clone your Rust project
2. Build it:

   ```
   cargo build --release
   ```
3. Run it:

   ```
   ./target/release/spotify_proxy
   ```

It will listen on:

```
http://0.0.0.0:3030
```

---

## 🔄 Roadmap (suggested future features)

* [ ] Token introspection and refresh support
* [ ] Caching layer (e.g. Redis) to reduce API hits
* [ ] Rate-limiting abusive clients
* [ ] HTTPS support

---
