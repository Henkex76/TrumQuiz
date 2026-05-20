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
Kolumner: `min`, `max`, `titel_sv`, `titel_en`, `text_sv`, `text_en`, `bild`, `delningsid`

- `min`/`max` = procentintervall (0–100)
- Lägg till fler rader = fler nivåer (helt dynamiskt)
- `bild` = nivåbild som visas på resultatsidan inuti quizet
- `delningsid` = vilken Facebook-delningssida nivån använder (se avsnitt 5).
  Standard: `pagang`, `silver`, `master`

---

## 4. Bilder

Alla bilder läggs i `images/`-mappen i repot. I kalkylarket anges bara filnamnet,
t.ex. `bonham.jpg`. Komprimera gärna till webb-storlek så quizet laddar snabbt på mobil.

---

## 5. Facebook-delning med resultat + bild

Facebooks robot läser sidan **statiskt** (kör ingen JavaScript). En enda sida kan
därför bara visa **en** og-bild och **en** og-titel. Lösningen är en liten
**resultatsida per nivå** — varje med rätt pokalbild och titel inbakad statiskt:

| Nivå (`delningsid`) | Fil | og-bild (16:9) | Titel i flödet |
|---|---|---|---|
| master | `r_master.html` | TrumQuiz-guldpokal-16-9.jpg | Jag blev MÄSTARE i TrumQuiz! 🥇 |
| silver | `r_silver.html` | TrumQuiz-silverpokal-16-9.jpg | Jag fick SILVER i TrumQuiz! 🥈 |
| pagang | `r_pagang.html` | TrumQuizz-logga-16-9.jpg | Jag testade TrumQuiz – klarar du bättre? |

När spelaren delar skickas länken till rätt resultatsida (t.ex.
`r_master.html?s=8&t=10`). Facebook visar pokalen + nivåtiteln. Den som klickar
landar på sidan och ser den exakta poängen ("Någon fick 8/10…") via JavaScript,
plus en knapp som startar quizet.

**Viktigt om filnamn:** GitHub Pages är **skiftlägeskänsligt**. Bilderna måste
heta exakt som i tabellen ovan (observera blandningen `TrumQuiz` / `TrumQuizz` /
`Trumquizz` i dina nuvarande filer). Vill du slippa krångel: döp om alla till
ett enhetligt mönster och uppdatera referenserna.

**Lägga till en ny nivå:** frågorna är fortsatt 100 % dynamiska i Sheets, men en
ny resultatnivå behöver en egen `r_<id>.html`. Säg till så genererar jag den.

**Exakt poäng som bild i flödet** (t.ex. "8/10" inbränt) kräver en serverlös
bildgenerator (Cloudflare Worker) — separat bygge om behovet finns.

---

## 6. Lägga till nya quiz

Två vägar:
- **Samma quiz, fler frågor:** lägg bara till rader i `Fragor`-fliken.
- **Helt nytt quiz:** kopiera `index.html` till `quiz2.html` och peka det mot ett
  annat kalkylark (eller en annan flik). Motorn är densamma.
