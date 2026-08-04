# Sito del gruppo — Jekyll

Nessuna informazione è scritta due volte. Menu, persone, pubblicazioni, filoni di
ricerca e software vivono in `_data/`: modifichi un file YAML e il sito si aggiorna
ovunque serva.

## Struttura

```
_config.yml           dati globali: titolo, email, indirizzo, URL
_data/
  nav.yml             il menu — un solo file per tutte le pagine
  people.yml          le persone, divise per gruppo
  publications.yml    le pubblicazioni
  research.yml        i filoni di ricerca
  code.yml            software e dataset
_includes/            pezzi riusati: head, menu, footer
_layouts/
  default.html        impianto delle pagine interne
  home.html           impianto della home
index.md              home
research.html         \
team.html              |  poche righe ciascuna:
publications.html      |  i contenuti arrivano da _data/
code.html              |
contact.html          /
404.html
assets/
  style.css
  people/             foto quadrate, 600×600 px
```

## Pubblicare

1. Apri `_config.yml` e sistema `url` e `baseurl` (le istruzioni sono nei commenti).
2. Carica tutto nella root di un repository pubblico.
3. Settings → Pages → Source: **Deploy from a branch**, branch `main`, `/ (root)`.

GitHub compila il sito da solo a ogni push: non devi installare Ruby né Jekyll, e
non devi committare la cartella `_site/`.

## Operazioni frequenti

**Aggiungere una persona** — apri `_data/people.yml`, aggiungi:

```yaml
    - name: Nome Cognome
      role: PhD student
      photo: nome-cognome.jpg
```

e metti la foto in `assets/people/nome-cognome.jpg`, quadrata. `photo`, `role` e
`links` si possono omettere.

**Aggiungere una pubblicazione** — `_data/publications.yml`. L'ordine in cui scrivi
le voci non conta: il raggruppamento per anno è automatico. Aggiungi
`featured: true` per farla comparire anche in home.

**Aggiungere una voce di menu** — `_data/nav.yml`, una riga. Il link della pagina
corrente si evidenzia da solo. Poi crea la pagina con lo stesso `permalink`.

**Cambiare colori o font** — `assets/style.css`, blocco `:root` in cima.

**Passare alla home con video** — in `index.md` cambia `hero: ruler` in
`hero: video`, e metti `assets/hero.mp4` più `assets/hero-poster.jpg`.

## Aggiungere una pagina

Crea un file `.html` nella root con questo inizio:

```yaml
---
layout: default
title: Openings
permalink: /openings/
heading: Open positions
lede: Una riga sotto il titolo.
---
```

Poi aggiungi la voce in `_data/nav.yml`. Il menu, la testata e il footer arrivano
dal layout: nel file scrivi solo il contenuto.

## Anteprima in locale (opzionale)

Non è necessaria — puoi lavorare committando e guardando il risultato online — ma
se preferisci vedere le modifiche subito, serve Ruby:

```bash
bundle install
bundle exec jekyll serve --livereload
```

e vai su `http://localhost:4000`. Se cambi `_config.yml` devi riavviare il comando;
per tutto il resto la pagina si ricarica da sola.

## Nota sulle immagini

`assets/people/` è vuota: mettici le foto. Finché mancano, le schede mostrano il
riquadro grigio del segnaposto — non è un errore.
