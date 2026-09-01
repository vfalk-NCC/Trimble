# Snygga kommentarer – Trimble Connect-tillägg

Ett litet extension till Trimble Connects 3D Viewer som ersätter de inbyggda
gula "pratbubble"-kommentarerna med ett eget, helt stylingbart kommentarkort.

## Vad det gör

- Du väljer ett objekt i 3D-vyn i Trimble Connect.
- I sidopanelen klickar du **"+ Ny kommentar på valt objekt"**.
- Appen hämtar automatiskt objektets **IFC-egenskaper** (klass, produktnamn,
  alla property sets/attribut) via Workspace API:t.
- Du skriver din kommentar, väljer ikon och färg.
- En pin (`PointMarkup`) placeras i modellen på objektets mittpunkt, och
  kommentaren visas som ett eget designat kort i panelen – med rubrik,
  kommentartext och en kompakt lista över hämtade egenskaper.
- Under **⚙ Utseende** styr du standardfärg, standardikon, om egenskaper ska
  visas i korten, och kortstorlek (kompakt/normal/stor).
- Allt sparas bara i webbläsarens minne under sessionen – som du bad om,
  ingen permanent lagring. Laddar du om sidan/tillägget försvinner
  kommentarerna (pinsen i modellen tas bort automatiskt när du klickar
  "Rensa alla", annars ligger de kvar tills du manuellt tar bort dem).

## Så här får du in det i Trimble Connect

Extensions i Trimble Connect måste laddas från en webbadress du själv hostar
(kan inte köras direkt från din dator). Enklaste gratisalternativen:

1. **GitHub Pages** (rekommenderas – gratis, HTTPS, CORS fungerar per default)
   - Skapa ett nytt repo, lägg in `index.html` och `manifest.json`.
   - Aktivera GitHub Pages för repot (Settings → Pages).
   - Din app hamnar på `https://dittanvändarnamn.github.io/reponamn/index.html`.
2. Vilken annan statisk webbhotell-lösning som helst (Netlify, Vercel, Azure
   Static Web Apps, egen server) fungerar också – kravet är bara **HTTPS**
   och att servern skickar CORS-headers (`Access-Control-Allow-Origin`) så
   att Trimble Connect kan läsa manifestfilen.

### Steg för steg

1. Ladda upp `index.html` och (valfritt) en ikon-bild till din hosting.
2. Öppna `manifest.json` och ersätt `"url"` och `"icon"` med dina riktiga
   adresser, t.ex.:
   ```json
   {
     "url": "https://dittanvändarnamn.github.io/mitt-tillagg/index.html",
     "title": "Snygga kommentarer",
     "icon": "https://dittanvändarnamn.github.io/mitt-tillagg/icon.png",
     "description": "Placera anpassningsbara kommentar-pins på modellobjekt och hämta IFC-data automatiskt.",
     "extensionType": ["3dviewer"]
   }
   ```
3. Ladda upp `manifest.json` till samma hosting.
4. I Trimble Connect: gå till ditt projekt → **Project Settings → Apps &
   Capabilities → Add Custom**, och klistra in URL:en till din
   `manifest.json`.
5. Öppna 3D-vyn i projektet – tillägget dyker upp som en panel/ikon i
   verktygsfältet.

## Tekniska detaljer (för dig eller den som underhåller koden)

- Bygger på **Trimble Connect Workspace API**
  (`trimble-connect-workspace-api`, laddas via CDN i `index.html`).
- Använder:
  - `API.viewer.getSelection()` – vilket objekt som är markerat
  - `API.viewer.getObjectBoundingBoxes()` – för att placera pinnen mitt på objektet
  - `API.viewer.getObjectProperties()` – IFC-klass, produktdata och alla property sets
  - `API.markup.addSinglePointMarkups()` – skapar själva pinnen i 3D-vyn
  - `API.markup.removeMarkups()` – tar bort pin när kommentar raderas
  - `API.viewer.setCamera()` – "Zooma till"-knappen i varje kort
- Lyssnar på `viewer.onSelectionChanged`-eventet för att automatiskt
  uppdatera vilket objekt som är valt. Om detta av någon anledning inte
  triggar i din version av Connect finns en manuell **"↻ Uppdatera
  markering"**-knapp som säkerhetsnät.
- All kommentardata hålls i en JS-array i minnet (`comments`) – inget
  skickas till någon backend. Vill du senare göra kommentarerna
  **permanenta och delade mellan användare** är nästa steg att byta ut det
  mot t.ex. Trimble Connects **Topics API** (BCF-kompatibla ärenden) eller
  en egen liten databas/API som extensionen pratar med.

## Vidareutveckling – förslag

- Fler ikonval / egen ikonuppladdning (`API.viewer.addIcon`).
- Filtrera vilka property sets som visas (just nu visas alla).
- Exportera alla kommentarer som CSV/Excel innan sessionen stängs.
- Byt till `API.extension.requestPermission("accesstoken")` +
  Trimbles Topics-API om ni vill spara kommentarerna permanent och dela dem
  mellan projektmedlemmar.
