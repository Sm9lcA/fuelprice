# Deployment instructies — Brandstofprijzen PWA

## Overzicht
- **brandstof-worker.js** → Cloudflare Worker (proxy voor API's)
- **index.html + manifest.json** → GitHub Pages (de PWA die je op je iPhone zet)

---

## Stap 1 — Cloudflare Worker deployen

1. Ga naar https://dash.cloudflare.com en maak een gratis account aan (als je er nog geen hebt)
2. Ga naar **Workers & Pages** → **Create** → **Create Worker**
3. Klik op **Edit code**, verwijder de voorbeeldcode, plak de inhoud van `brandstof-worker.js`
4. Klik **Deploy**
5. Noteer de Worker-URL, bijv: `https://brandstof.jouwnaam.workers.dev`

---

## Stap 2 — GitHub Pages opzetten

1. Ga naar https://github.com en maak een nieuw repository aan (bijv. `brandstof`)
2. Upload `index.html` en `manifest.json` naar de repository
3. Ga naar **Settings** → **Pages** → Source: **Deploy from a branch** → branch: `main` → **Save**
4. Je site is beschikbaar op: `https://jouw-gebruikersnaam.github.io/brandstof/`

### App-icoon (optioneel maar netjes)
- Maak een PNG-icoon van 512×512 pixels (bijv. een ⛽ emoji op donkere achtergrond)
- Sla op als `icon-192.png` (192×192) en `icon-512.png` (512×512)
- Upload naar je GitHub repository
- Zonder icoon werkt de PWA ook gewoon, maar dan is er geen mooi icoon op je homescreen

---

## Stap 3 — Worker URL en GitHub Pages URL koppelen

**In brandstof-worker.js** (doe dit vóór deployment, of update de worker achteraf):
```
const ALLOWED_ORIGINS = [
  "https://jouw-gebruikersnaam.github.io",  // ← jouw GitHub Pages URL
  "http://localhost:5500",
];
```

**In index.html**, zoek naar:
```
const WORKER_URL = "https://jouw-worker.jouw-subdomain.workers.dev";
```
Vervang dit door jouw Worker-URL en upload opnieuw naar GitHub.

---

## Stap 4 — Op iPhone homescreen zetten

1. Open Safari op je iPhone (moet Safari zijn, geen Chrome)
2. Ga naar `https://jouw-gebruikersnaam.github.io/brandstof/`
3. Tik op het Deel-icoon (vierkantje met pijl omhoog)
4. Scroll naar beneden → **Zet op beginscherm**
5. Geef het een naam → **Voeg toe**

De app verschijnt nu als icoon op je homescreen en opent zonder adresbalk, 
net als een native app. Prijzen worden elke 15 minuten automatisch ververst.

---

## Aanpassen

### Tankvolume
In `index.html`:
```js
const TANK_LITERS = 45;  // ← jouw mediaan volume
```

### Nieuwe regio toevoegen (NL)
In `brandstof-worker.js`:
```js
const NL_REGIONS = [
  ...
  { id: "nieuw", label: "Nieuwe plek", bounds: "lat1,lon1:lat2,lon2" },
];
```

### Duitsland: grotere zoekradius
```js
// In fetchTK(), pas &rad=1 aan naar bijv. &rad=3
```

---

## Gratis limieten (ruim voldoende voor persoonlijk gebruik)

| Service | Gratis limiet |
|---|---|
| Cloudflare Workers | 100.000 requests/dag |
| GitHub Pages | Onbeperkt voor statische sites |

Bij 15-minuten auto-refresh + handmatig gebruik kom je op hooguit ~150 requests/dag.
