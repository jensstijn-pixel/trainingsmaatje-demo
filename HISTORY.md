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
  - Gemma 2 2B: zie hieronder.
- **Niet getest:** op een echte iPhone (Safari heeft WebGPU vanaf iOS 26). Geheugen is daar
  het risico bij de grotere modellen.

## Draaien
- Lokaal: `python3 -m http.server 8765` in deze map, open http://localhost:8765
- Live: GitHub Pages (zie README/onderaan zodra live).
