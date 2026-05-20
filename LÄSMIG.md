# Trumquiz – setup-guide

Quizet är en enda fil (`index.html`). Inga byggsteg, inga bibliotek.
Det fungerar direkt med inbyggd demo-data tills du kopplar Google Sheets.

---

## 1. Lägg upp på GitHub Pages

1. Skapa ett nytt repo, t.ex. `trumslagaren-quiz`
2. Ladda upp `index.html` (dra-och-släpp i webbläsaren går bra)
3. Skapa en mapp `images/` för fråge- och resultatbilder
4. Settings → Pages → Source: `main` / root → Save
5. Efter någon minut ligger quizet på `https://<konto>.github.io/trumslagaren-quiz/`

**Egen domän (valfritt):** lägg en CNAME-post i DNS som pekar
`quiz.trumslagaren.se` → `<konto>.github.io`, och ange domänen under Pages.

---

## 2. Koppla Google Sheets

1. Skapa ett kalkylark med tre flikar: **Config**, **Fragor**, **Nivaer**
   (kolumnstruktur nedan)
2. Dela → "Alla med länken" → **Visa**
3. Kopiera ID:t ur kalkylarkets URL:
   `docs.google.com/spreadsheets/d/`**`DETTA_ÄR_ID`**`/edit`
4. Klistra in det i `index.html` högst upp i skriptet:
   `const SHEET_ID = "DETTA_ÄR_ID";`
5. Spara och ladda upp filen igen → quizet hämtar nu frågorna automatiskt.

Lämnas `SHEET_ID` tomt används demo-datan.

---

## 3. Kalkylbladsstruktur

### Flik `Config`
| Inställning | Värde |
|---|---|
| titel_sv | Trumquiz |
| titel_en | Drum Quiz |
| sekunder_per_fraga | 30 |
| blanda_fragor | nej |
| blanda_alternativ | ja |

### Flik `Fragor` (en rad = en fråga)
Kolumner: `fraga_sv`, `fraga_en`, `alt_a_sv`, `alt_a_en`, `alt_b_sv`, `alt_b_en`,
`alt_c_sv`, `alt_c_en`, `alt_d_sv`, `alt_d_en`, `ratt`, `bild`, `forklaring_sv`, `forklaring_en`

- `ratt` = bokstaven för rätt svar: **A**, **B**, **C** eller **D**
- `alt_c` / `alt_d` tomma → bara 2–3 alternativ visas
- `bild` tom → bara text. Ifylld → laddar `images/<filnamn>`
- `forklaring` tom → ingen förklaring + auto-advance.
  Ifylld → visas + en "Nästa"-knapp (så spelaren hinner läsa)

### Flik `Nivaer` (resultatnivåer)
Kolumner: `min`, `max`, `titel_sv`, `titel_en`, `text_sv`, `text_en`, `bild`

- `min`/`max` = procentintervall (0–100)
- Lägg till fler rader = fler nivåer (helt dynamiskt)
- `bild` = nivåbild som visas på resultatsidan

---

## 4. Bilder

Alla bilder läggs i `images/`-mappen i repot. I kalkylarket anges bara filnamnet,
t.ex. `bonham.jpg`. Komprimera gärna till webb-storlek så quizet laddar snabbt på mobil.

---

## 5. Om Facebook-delningen (viktigt)

Facebooks robot läser sidan **statiskt** och kör inte JavaScript. Det betyder:

- **Förhandsbilden i flödet** kommer från `og:image`-taggen i `<head>` och är
  alltid samma branded bild — oavsett spelarens poäng. Byt ut den mot en egen
  Trumslagaren-bild (`og-default.jpg` i `images/`).
- **På mobil** används enhetens egna delningsark, och då följer den personliga
  poängtexten med ("Jag fick 8/10 …") in i Facebook-inlägget.
- **På desktop** delas sidan via Facebooks dialog med den fasta og-bilden.

Vill man ha en unik delningsbild per poängnivå krävs en serverlös bildgenerator
(t.ex. en Cloudflare Worker) — det kan byggas senare om behovet finns.

---

## 6. Lägga till nya quiz

Två vägar:
- **Samma quiz, fler frågor:** lägg bara till rader i `Fragor`-fliken.
- **Helt nytt quiz:** kopiera `index.html` till `quiz2.html` och peka det mot ett
  annat kalkylark (eller en annan flik). Motorn är densamma.
