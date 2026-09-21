# IntroR

Quarto-Website für das Seminar "Einführung in R und Quarto".

## Setup nach dem Klonen

Dieses Repo enthält einen Git-Hook, der vor jedem Commit automatisch
`quarto render` ausführt und den aktualisierten `_site`-Ordner mit ins
Commit aufnimmt. Der Hook liegt in `.githooks/` statt im ungetrackten
`.git/hooks/`-Ordner und muss deshalb nach jedem Klon einmalig aktiviert
werden:

```sh
git config core.hooksPath .githooks
```

Ohne diesen Schritt wird die Website vor einem Commit nicht automatisch
neu gerendert, und Änderungen an `.qmd`-Dateien oder am Theme
(`style/*.scss`) erscheinen dann nicht im deployten `_site`-Ordner.

## Deployment

Die Seite wird über Netlify als statischer `_site`-Ordner ausgeliefert
(kein Build-Command auf dem Server, siehe `netlify.toml`). Änderungen
müssen daher lokal gerendert und committet werden, bevor sie live gehen.
