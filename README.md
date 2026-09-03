# Property Labels – Trimble Connect-tillägg

Markera ett eller flera objekt, välj vilka egenskaper (från objektets
riktiga property sets — inget hårdkodat) som ska visas, och skapa en label
i 3D-vyn som visar dem. Labels kan redigeras, flyttas, döljas, uppdateras
och tas bort.

## Användarflöde

1. Markera ett eller flera objekt i 3D-vyn (Trimble Connects vanliga
   markering — inget särskilt klickläge behövs).
2. Klicka **"+ Skapa label (markerat objekt)"**.
3. En panel visar objektets/objektens verkliga property sets och
   egenskaper — exakt de som finns i just den modellen, inget är
   förvalt/hårdkodat förutom att "Namn" och "Klass" (om de finns) är
   förikryssade som en rimlig startpunkt.
4. Bocka i vilka egenskaper som ska visas (t.ex. Length, Weight, Volume,
   Type, Status — förutsatt att objektet faktiskt har dem i sina
   property sets).
5. Klicka **"Skapa label(er)"** — en label per markerat objekt skapas i
   3D-vyn: en rubrikrad + en rad per vald egenskap, plus en ledartråd
   (pil) ner till objektet.
6. I listan längst ner kan varje label:
   - **✏️ Redigeras** — ändra vilka egenskaper som visas eller färgen.
   - **📍 Flyttas** — klicka på en ny plats i 3D-vyn.
   - **🙈 Döljas / 👁 Visas** — utan att förlora kopplingen eller valen.
   - **🔄 Uppdateras** — hämtar objektets egenskaper på nytt (om värden
     ändrats i modellen sedan labeln skapades) och ritar om texten.
   - **Tas bort.**
7. **"🔄 Uppdatera alla"** i verktygsfältet uppdaterar samtliga labels i
   ett svep.

Varje label behåller sin koppling till `modelId` + `objectRuntimeId`
internt, så "Uppdatera" alltid hämtar från rätt objekt — även om du bytt
vilket objekt som råkar vara markerat i vyn.

## Installation

Samma mönster som övriga tillägg: ladda upp `index.html` + `manifest.json`
till en egen hosting/repo, uppdatera `url`/`icon` i manifestet, och lägg
till raw-länken till manifestet via **Project Settings → Apps &
Capabilities → Add Custom**.

## Tekniska val & kända begränsningar

- **Inga hårdkodade property-namn.** Property sets och deras egenskaper
  läses direkt från `API.viewer.getObjectProperties()` och visas exakt som
  modellen definierar dem — vilket innebär att olika IFC-exportörer/
  -mallar visar olika listor, precis som avsett.
- **Labels ritas som staplade textrader** (en `addTextMarkup`-rad per vald
  egenskap) + en ledartråd/pil ner till objektet + ett litet kryss vid
  objektets mittpunkt — samma beprövade teknik som redan fungerar i
  kommentar- och foto-tilläggen. Ingen `addIcon` används alls i det här
  tillägget, av samma skäl som vi övergav den i foto-tillägget
  (opålitlig i praktiken).
- **Att klicka på en label i 3D-vyn öppnar inte en redigeringsdialog** —
  text-/linjemarkeringar går inte att tillförlitligt identifiera vid klick.
  Använd listan längst ner i panelen för alla ändringar.
- **"Flytta"** sätter en helt ny position (inte en offset-justering) genom
  att du klickar en ny punkt i 3D-vyn — kräver klick på synlig
  geometri/punktmoln, samma begränsning som i övriga verktyg.
- **Uppdatering är manuell**, inte automatisk/live — det finns ingen känd
  händelse för "objektets egenskaper ändrades" i Workspace API, så
  "🔄 Uppdatera" (per label eller alla) är den tillförlitliga vägen.
- Session-only, som alla andra tillägg i den här serien: labels finns kvar
  tills du tar bort dem eller laddar om sidan.
