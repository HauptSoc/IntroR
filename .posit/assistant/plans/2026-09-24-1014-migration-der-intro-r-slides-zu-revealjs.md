# Migration der intro_R xaringan-Folien zu Quarto revealjs

## Ausgangslage

**Quelle:** `C:\GIT\Teaching\intro_R\static\` — 13 xaringan-Decks (`.Rmd`, je ca. 400–800 Zeilen):
`1-Hello_R`, `2-Vectors`, `3-Data-Frames`, `4-Visualization-I`, `5-Piping`,
`6-Visualization-II`, `7-Pivoting-Joining`, `8-PCA`, `9-OLS-I`, `10-OLS-II`,
`11-GLM`, `12-Mixed`, `13-RMarkdown`.

Jedes Deck nutzt: `xaringan::moon_reader` YAML, einen `setup`-Chunk mit
`knitr::opts_chunk$set(...)`, `RefManageR` (`ReadBib("../../intRo.bib")`,
`Citet(myBib, "key")`, abschließende `PrintBibliography(myBib)`-Folie),
`xaringanExtra::use_xaringan_extra(c("tile_view","tachyons"))` +
`use_panelset()` (in den bisher gelesenen Decks ungenutzt), remark.js
`layout: true` / `layout: false` Template-Vererbung (kein Quarto-Äquivalent),
`.left-column[]`/`.right-column[]`/`.pull-left[]`/`.pull-right[]`/`.center[]`
Div-Syntax, sowie `knitr::include_graphics()` für lokale und entfernte Bilder.

**Referenzformat:** `C:\GIT\Teaching\Grundlagen EmpSoz\slides\` — ein `.qmd` pro
Thema, `slide-level: 2`, Bilder via `![](images/x.png){width="60%"}` aus
flachem `images/`-Ordner, `::: columns` / `::: {.column width="..."}`
Divs, eigenes `style.scss`/`global.css`, plus dünne `NN-slides.qmd`
Wrapper-Seiten, die das gerenderte Deck per `{{< revealjs file="NN-Topic.html" ... >}}`
in die Website einbetten.

**Ziel:** `C:\GIT\Teaching\IntroR\` — aktuell kein `slides/`-Ordner, nur 6 leere
"Sitzungen"-Platzhalter (bleiben unangetastet, separates System).

## Entscheidungen (mit dir abgestimmt)

1. **Umfang:** Alle 13 Themen 1:1 als eigenständige revealjs-Decks übernehmen,
   unabhängig von den 6 Sitzungen-Platzhaltern.
2. **Zitate:** Umstellung auf Quarto-natives Zitieren (`[@key]`/`@key`) statt
   RefManageR/`Citet()`. Die `.bib`-Datei bleibt Grundlage, damit später eine
   Zotero-Anbindung (Better BibTeX) direkt darauf aufsetzen kann — das ist ein
   Editor-seitiges Setup (Positron/RStudio Citation-Picker), keine Datei-Änderung.
3. **Bilder:** Nur manuell platzierte Bilder (Screenshots, Diagramme) werden in
   einen flachen `slides/images/`-Ordner kopiert. Automatisch generierte
   Chunk-Outputs (`*_files/figure-html/*.png`) werden **nicht** kopiert, da sie
   beim Rendern neu entstehen.
4. **Navigation:** Für jedes Deck eine `NN-slides.qmd`-Wrapper-Seite, eingebunden
   in die `_quarto.yml`-Sidebar (neue Sektion, getrennt von "Sitzungen").
5. **Theme:** `style.scss`/`global.css` von "Grundlagen EmpSoz" als Basis
   kopieren und anpassen.
6. **Branding/Namen:** Uni-Logos (KU/UCPH) werden **nicht** übernommen. Neue
   Dateien: `01-Hello-R.qmd` … `13-RMarkdown.qmd`.

## Wichtige Klarstellung zum Scope

Dies ist eine **Format-Migration**, keine inhaltliche Überarbeitung. Text,
Übungen, Beispiele und fachlicher Inhalt bleiben substanziell erhalten; ich
ändere nur, was für die technische Umstellung von xaringan/remark.js auf
Quarto revealjs nötig ist (Syntax, Zitate, Bildpfade, Slide-Struktur). Bekannte
inhaltliche Eigenheiten wie `13-RMarkdown` (behandelt R Markdown, nicht Quarto)
werden **nicht** automatisch modernisiert — das wäre ein separater Auftrag.

## Bekannte Migrations-Herausforderungen

- **`layout: true`/`layout: false`** (remark.js Folien-Vererbung): Kein
  Quarto-Äquivalent. Lösung: den vererbten Inhalt (z. B. wiederkehrende
  Kopfzeile/linke Spalte) manuell in jede betroffene Folge-Folie kopieren, bis
  `layout: false` im Original erscheint, dann die Layout-Direktiven entfernen.
  Das ist der arbeitsintensivste Schritt und muss pro Deck von Hand geprüft
  werden.
- **Bild-Dateinamenkollisionen** zwischen Themen-Ordnern (nur diese 3 bestätigt):
  - `Lego2.png` (4-Visualization-I / 6-Visualization-II) — identische Dateigröße
    → einmal kopieren, von beiden Decks referenziert.
  - `xeno_quest.png` (4-Visualization-I / 8-PCA) — identische Dateigröße →
    einmal kopieren.
  - `dscrgrp.png` (3-Data-Frames / 5-Piping) — **unterschiedliche Dateigröße**
    (54.7K vs. 72.0K) → beide behalten, umbenennen zu
    `dscrgrp-dataframes.png` / `dscrgrp-piping.png` und Referenzen anpassen.
- **Nicht-Bild-Assets** werden ausgeschlossen (keine separate Rückfrage nötig,
  wird hier transparent gemacht): `my_movie.mp4` (Rayshader-Animation in
  8-PCA), `manifesto_apikey.txt`, `MyOLSModels.doc/.html/.tex`
  (9-OLS-I/10-OLS-II Word-Exporte). Falls du eines davon doch brauchst, sag
  Bescheid.
- **Zitat-Stil:** Quarto nutzt ohne `csl:`-Angabe einen Standard-Autor-Jahr-Stil
  (Chicago-ähnlich). Ich setze zunächst keinen CSL, das lässt sich später leicht
  ergänzen (z. B. sobald die Zotero-Bibliothek einen bevorzugten Stil vorgibt).

## Umsetzungsschritte

### Phase 0 — Grundgerüst
1. `slides/` Ordner in `IntroR` anlegen.
2. `style.scss` und `global.css` aus `Grundlagen EmpSoz/slides/` kopieren nach
   `IntroR/slides/`; kursspezifische Klassen, die nicht gebraucht werden,
   drinlassen (schadet nicht), offensichtlich EmpSoz-spezifische Inhalte
   (z. B. Mermaid-Only-Fixes) sind neutral und werden übernommen.
3. `IntRo.bib` nach `IntroR/slides/IntRo.bib` kopieren (unverändert, Zitierschlüssel
   bleiben identisch).
4. `slides/images/` anlegen; alle 1:1-Bilder aus den 13 `img/`/`media/`-Unterordnern
   dorthin kopieren, Kollisionen wie oben behandeln.

### Phase 1 — Konvertierung der 13 Decks
Für jedes der 13 Themen wird die `.Rmd` in eine neue `.qmd`-Datei
(`slides/01-Hello-R.qmd` … `slides/13-RMarkdown.qmd`) überführt:

- YAML-Header ersetzen durch revealjs-Format nach EmpSoz-Vorbild (`format:
  revealjs:`, `theme: style.scss`, `css: global.css`, `slideNumber: true`,
  `execute: {echo: false, message: false, warning: false}`, plus
  `bibliography: IntRo.bib`).
- `setup`-Chunk vereinfachen: nur noch benötigte `library()`-Aufrufe, kein
  `RefManageR`/`xaringanExtra`/`opts_chunk$set`-Boilerplate mehr (Ausführungsoptionen
  wandern in YAML `execute:`).
- Zitate `` `r Citet(myBib, "key")` `` → `[@key]` bzw. `@key` im Fließtext.
- Abschließende Bibliografie-Folie → `## Literatur` mit `::: {#refs}\n:::`.
- `knitr::include_graphics(...)` → Markdown-Bildsyntax `![](images/x.png){fig-align="center" width="70%"}`
  (Remote-URLs bleiben als externe Links erhalten).
- `layout: true`/`false`-Blöcke auflösen (siehe oben).
- `.left-column[]`/`.right-column[]`/`.pull-left[]`/`.pull-right[]` →
  `::: columns` / `::: {.column width="50%"}`.
- `background-image: url(...)`-Folien → Quarto-Slide-Attribut
  `## {background-image="..."}`.
- `.center[]`/`.backgrnote[]` → einfache Markdown- bzw. `::: {.center}`-Divs;
  `.backgrnote`-Optik (kleine, gedämpfte Fußnote) wird als CSS-Klasse in
  `style.scss` ergänzt, falls im Original visuell relevant.
- `xaringanExtra`-Aufrufe entfernen; falls sich beim vollständigen Lesen eines
  Decks doch aktive `.panelset[...]`-Tabs finden (bisher nur in den ersten 3
  Decks geprüft), Konvertierung zu `::: panel-tabset`.
- Uni-Logo-Referenzen entfernen.
- Nicht-Bild-Assets (s. o.) nicht referenzieren/kopieren.

Wegen des Umfangs (13 strukturell ähnliche, aber inhaltlich große Dateien)
werden die Decks über mehrere parallele Subagenten konvertiert, jeweils mit
den obigen Regeln als Vorgabe. Ich lese vorher jedes Original vollständig,
um deckspezifische Besonderheiten (z. B. PCA/OLS/GLM-Modell-Chunks, DAG-Pakete)
korrekt zu berücksichtigen.

### Phase 2 — Navigation
5. Für jedes Deck eine `slides/NN-slides.qmd`-Wrapper-Seite nach EmpSoz-Muster
   (`format: html` + `{{< revealjs file="NN-Thema.html" ... >}}`).
6. `_quarto.yml`-Sidebar um eine neue Sektion (z. B. "Folien") mit allen 13
   Wrapper-Seiten ergänzen.

### Phase 3 — Verifikation
7. Vollständiges `quarto render` des Projekts; Fehler beheben.
8. Stichprobenartige Sichtprüfung von 2–3 gerenderten Decks (Bilder, Zitate,
   Spaltenlayouts, aufgelöste `layout:true`-Folgen) via Screenshot/Browser.

## Offene Punkte, die ich während der Umsetzung transparent mache

- Falls beim vollständigen Lesen einzelner Decks weitere xaringan-spezifische
  Features auftauchen (z. B. `class: inverse`, `count: false`, Incremental-Reveal
  `--`), wende ich sinnvolle Quarto-Äquivalente an und dokumentiere Abweichungen
  in der finalen Zusammenfassung statt erneut nachzufragen — außer bei
  Entscheidungen mit spürbaren inhaltlichen Konsequenzen.
