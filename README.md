# Trading City

3D autonomous-trading visualization. One self-contained `index.html` — no build step.
Each trader is a tower in the city; live positions, P&L, risk and price candles
render onto the towers in real time.

## Run it

Serve the folder with any static server (it must be served over http, not
`file://`, because it loads Three.js as an ES module):

```sh
npx serve .
# or
python3 -m http.server 8000
```

Internet access is required — the 3D engine loads from `cdn.jsdelivr.net`.
If the CDN is unreachable the page says so instead of showing a blank screen.

## The backend it talks to

All data comes from one API base, overridable per load:

```
index.html?api=http://host:port
```

Default: `http://localhost:3001`. The page polls every 5 seconds and shows
ONLINE / DEGRADED / OFFLINE in the HUD based on real responses — nothing is
mocked or invented.

| Endpoint | Every | Feeds |
|---|---|---|
| `GET /api/gaze/positions` | 5s | Open positions → tower height, color, P&L |
| `GET /api/gaze/closed` | 5s | Closed trades → history panel |
| `GET /api/risk/status` | 5s | Risk state → city alert level |
| `GET /api/market/ticker/{id}` | 5s | Live price + 24h change per coin (12 coins) |
| `GET /api/market/ohlcv/{id}?days=2&interval=1H` | 60s, staggered | Hourly candles → tower monitors |

`{id}` is the CoinGecko id (`bitcoin`, `ethereum`, `solana`, …). The parser is
deliberately forgiving — it accepts arrays, `{positions:[...]}`,
`{SYMBOL:{...}}` maps, and many field-name variants for symbol / side / size /
entry / stop / pnl. See `normPos` / `asArray` / `extractPrice` in `index.html`.

## Debug

Append `?debug` to see the live endpoint checklist: API base, poll count, and
per-endpoint ok/fail with latency, plus a 220-char sample of each response.

## Rebranding

The name is a variable, not the product. Brand strings live at:
- `<title>` and HUD brand block (top of file)
- Canvas fallback text (~line 1182)

Change those and it becomes any city — Media City, T2R Enterprise, whatever's
next. The trader list (`TRADERS`, 12 coins with colors/exchanges) is the other
thing you'd swap per city.

## Provenance

Built 2026-10-05, exported from neta.art (`meta.json`). Single-file export;
no secrets or credentials inside — the API base is a runtime parameter.
