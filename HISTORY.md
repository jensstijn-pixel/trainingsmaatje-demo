# HISTORY — Trainingsmaatje-demo

Demo: een gratis open AI-model dat **op de telefoon zelf** draait (geen server, geen
kosten per vraag) en tijdens het trainen vragen beantwoordt ("oefening bezet, wat nu?").
Verkenning voor de geparkeerde PT-app (ideeënlijst 4.1).

## 2026-10-25
- Gebouwd: één `index.html`, geen build-stap. WebLLM `0.2.85` via jsDelivr draait het model
  in de browser met WebGPU. Modellen komen van Hugging Face (mlc-ai) en staan daarna in de
  browsercache (knop "Verwijder van toestel" ruimt op).
- Keuze uit 4 modellen: Qwen 2.5 0,5B (280 MB), Qwen 2.5 1,5B (870 MB), Qwen 3 1,7B (970 MB,
  thinking uit via `extra_body.enable_thinking=false`), Gemma 2 2B (1,5 GB; geen systeemrol,
  instructies gaan in het eerste gebruikersbericht). f16 als de GPU `shader-f16` heeft, anders f32.
- Demo-data: een oefeningenbibliotheek (32 oefeningen, 6 bewegingspatronen) en een vast
  full-body schema. De knop "Bezet" werkt **zonder AI** (zelfde patroon, ander materiaal).
- **Belangrijkste les:** een klein model met de hele bibliotheek in de prompt gaf onzin
  (bankdrukken als vervanger van squats). Opgelost door de **code het denkwerk te laten doen**:
  per oefening alleen de 3 juiste vervangers, de resterende tijd uitgerekend, een
  schrapvolgorde, en bij "X minuten" een kant-en-klare berekening. Plus 2 voorbeeldgesprekken.
  Het model kiest en verwoordt alleen nog.
- Getest in Chrome op de Mac (M-serie), zelfde 5 vragen:
  - Qwen 2.5 0,5B: onbruikbaar.
  - Qwen 2.5 1,5B: 3/5 goed (Chest press als vervanger van schouderdrukken, tijdvraag fout).
  - Qwen 3 1,7B: 4/5 goed, ~2 s tot het eerste woord, ~29 tokens/s. Tijdvraag fout → daarna
    opgelost met de tijdberekening in code.
  - Gemma 2 2B: 4/5 goed, maar zwaarder (1,5 GB) en trager (~3 s tot eerste woord, ~20 tokens/s).
  - Na de tijdberekening (in het gebruikersbericht, niet in de systeemprompt — daar negeerde
    Qwen 3 hem): "30 min" en "20 min" goed; "over 10 min weg" spreekt zichzelf nog tegen.
- **Qwen 3 1,7B is de standaard.** Model draait in een Web Worker (`worker.js`): zonder worker
  bevroor de pagina tijdens het laden.
- Live: https://jensstijn-pixel.github.io/trainingsmaatje-demo/ (publieke repo
  `jensstijn-pixel/trainingsmaatje-demo`, geen persoonsgegevens). Live getest op de Mac met
  het 0,5B-model: laden + antwoorden werkt. Testmodellen daarna weer uit Chrome verwijderd.
- Let op: GitHub Pages deelt één origin (`jensstijn-pixel.github.io`) met de Sportlog/Daily-app.
  Andere cachenamen, dus geen conflict, maar opslag telt samen.
- **Niet getest:** op een echte iPhone (Safari heeft WebGPU vanaf iOS 26). Geheugen is daar
  het risico bij de grotere modellen.

## Draaien
- Lokaal: `python3 -m http.server 8765` in deze map, open http://localhost:8765
- Live: https://jensstijn-pixel.github.io/trainingsmaatje-demo/ (push naar `main`, Pages bouwt zelf)

## Valkuilen
- **Opslag op iOS is niet blijvend.** Safari wist websiteopslag (ook het model) na 7 dagen
  zonder bezoek. Een beginscherm-app heeft die regel niet, maar heeft wél eigen opslag los van
  Safari (dus opnieuw downloaden). Bij een volle telefoon kan iOS alsnog opruimen. Een echte
  app moet het model dus altijd opnieuw kunnen ophalen.
- **Nooit "Websitegegevens wissen" voor `jensstijn-pixel.github.io`**: dat raakt ook Daily.
  Gebruik de knop "Verwijder van toestel".
- **Oude service worker op `localhost:8765`:** de `sw.js` van Bregje-games (cache `bregje-v7`)
  stond daar nog geregistreerd en zette alle modelbestanden (3,4 GB) in zijn eigen cache. Op
  25 okt afgemeld en gewist. Lokaal testen? Check eerst `navigator.serviceWorker.getRegistrations()`
  of gebruik een andere poort.
- **Modellen echt van de Mac halen:** de knop in de demo markeert ze alleen als verwijderd. Ook
  nodig: `caches.delete('webllm/...')` per origin, en de modelbestanden in Chrome's gewone
  HTTP-cache (`~/Library/Caches/Google/Chrome/Default/Cache`). Op 25 okt alles opgeruimd
  (Chrome-cache-opslag van 4,0 GB naar 0,6 GB, de rest is Gmail/Docs).
- Chrome-automatisering time-out (45 s) tijdens het laden van een model; korte polls gebruiken.

## Open punten
- Jens test op zijn iPhone: laadt Qwen 3 zonder geheugenfout? Hoe snel? Is het Nederlands goed genoeg?
- Als het een echte app wordt: meer vragen in code afvangen (tijd, rust, sets) en de AI alleen
  voor de rest; inspreken in plaats van typen.
