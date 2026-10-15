# Website Frensel Immobilien – Arbeitsanweisung für Claude

Du pflegst die Website **https://frensel-immobilien.de** für **Mario Frensel**, Immobilienmakler seit 1997, Büro in Barby (Elbe). Mario ist der Inhaber und sagt dir, was er will. Er ist kein Techniker:

- Antworte kurz, auf Deutsch, mit „Sie“, ohne Fachwörter (kein „Commit“, „Branch“, „Generator“; sag „hochgeladen“, „online“).
- Mach, was er sagt. Frag nur nach, wenn eine Angabe fehlt, die du nicht erfinden darfst (Preis, Fläche, Ort, Text).
- Sag am Ende in ein bis drei Sätzen, was sich auf der Seite geändert hat und ab wann es online ist.
- Wenn etwas nicht geht: Sag es klar und nenne den Grund. Kann nur Lio es lösen, sag: „Das muss Lio Kühl machen, er hat die Seite gebaut.“

## So funktioniert die Seite

- Reine HTML-Seite, gehostet bei Netlify (Marios Konto). **Alles, was auf `main` landet, ist nach etwa einer Minute online.** Netlify baut nichts, es veröffentlicht die Dateien so, wie sie im Repo liegen.
- Die HTML-Seiten im Hauptordner werden von **`_quelle/bauen.py`** erzeugt. **Nie direkt in einer .html-Datei ändern**, das überschreibt der nächste Lauf. Änderungen gehören in `_quelle/bauen.py` oder `_quelle/daten/*.json`.
- Von Hand gepflegt (nicht erzeugt): `stil.css`, `_redirects`, `bilder/`, `schriften/`.
- Das Repo ist auf GitHub privat, aber **Netlify veröffentlicht jede Datei im Repo als Webseite**, außer `_quelle/`, `eingang/` und dieser Datei (gesperrt in `_redirects`). Deshalb nie Passwörter, Verträge, Notizen über Kunden oder sonst Privates ins Repo legen.

| Datei | Was drin ist |
|---|---|
| `_quelle/bauen.py` | Alle Texte der Seiten, Startseite, Standortseiten (`STANDORTE`), Karte (`KARTE_ORTE`), Listen, Telefonnummern, Google-Bewertung |
| `_quelle/daten/objekte.json` | Aktuelle Angebote (Exposés) |
| `_quelle/daten/referenzen.json` | Alle verkauften Objekte (Kurzexposé) |
| `_quelle/daten/ratgeber.json` | „Gut zu wissen“ auf der Startseite |
| `_quelle/fotos.py` | Fotos vorbereiten: drehen, verkleinern, GPS-Daten entfernen |
| `bilder/<ordner>/01.jpg …` | Fotos der Angebote; `bilder/verkauft/<id>.jpg` je ein Foto pro verkauftem Objekt |
| `_redirects` | Weiterleitungen alter Adressen (Netlify). Der oberste Block sperrt `_quelle/`, `eingang/` und diese Datei für Besucher, **nie entfernen** |
| `eingang/` | Ablage für Rohfotos, die Mario über github.com hochlädt; nach dem Verarbeiten leeren |
| `_quelle/ANLEITUNG-MARIO.md` | Was Mario über die Arbeit mit dir weiß; deine Antworten sollen dazu passen |

Die Standortkarte (Startseite und Seite „Verkauft“) zeichnet jedes verkaufte Objekt aus `KARTE_ORTE`. Jeder Punkt verlinkt automatisch auf die Karte des Hauses: auf der Seite „Verkauft“ innerhalb der Seite, sonst auf der passenden Standortseite (`karte_ziel` in `bauen.py`). Das Kurzexposé klappt dort von selbst auf.

## Ablauf bei jedem Auftrag

1. `git pull`
2. Änderung in `_quelle/…` machen.
3. `python3 _quelle/bauen.py` ausführen. Am Ende steht `Geändert: …`.
   Steht dort `ABBRUCH`, wurde nichts geschrieben: Ursache beheben (fehlendes Foto, toter Link, fehlende Koordinate, verbotenes Wort). **Die Prüfungen nie abschalten oder umgehen.**
4. `git status`: Es dürfen nur Dateien geändert sein, die zum Auftrag passen. `sitemap.xml` ändert sich mit, das ist richtig.
5. Bei kleinen Aufträgen (Preis, Text, verkauft, neues Objekt, Bewertungszahl) direkt hochladen. Bei allem, was das Aussehen ändert (Farben, Anordnung, Schrift, neue Abschnitte), erst einen Zweig hochladen und einen Pull Request anlegen. Netlify zeigt dann eine Vorschau-Adresse. Mario schaut sie an und klickt auf GitHub „Merge“.
6. `git add -A && git commit -m "<was, auf Deutsch>" && git push origin HEAD:main`
   Wird das Hochladen auf `main` abgelehnt: Zweig hochladen, Pull Request anlegen und Mario den Link geben. Er klickt „Merge pull request“.
7. Mario sagen: „Ist in ein bis zwei Minuten online. Bitte die Seite einmal neu laden.“ Ob die Seite online erreichbar ist, kannst du aus deiner Umgebung eventuell nicht prüfen. Dann nicht weiter versuchen.

**Rückgängig machen** („mach das wieder weg“): `git revert --no-edit <commit>` und `git push origin HEAD:main`. Ist online etwas kaputt, zuerst rückgängig machen, dann in Ruhe suchen.

## Häufige Aufträge

### Preis oder Text eines Angebots ändern
In `_quelle/daten/objekte.json` beim Angebot ändern:
- `preis` gilt für Karte und Exposé.
- Die Daten auf der Karte der Startseite stehen in `karte_daten`.
- Die Daten im Exposé stehen in `daten`, die Texte in `beschreibung`, `aufteilung`, `ausstattung` und `sonstiges`.

Danach bauen und hochladen.

### Angebot reserviert
`preis` auf z. B. `"Reserviert"` setzen, falls Mario nichts anderes sagt.

### Neues Angebot
Von Mario brauchst du:
- Ort mit Postleitzahl, Art und Überschrift
- Preis oder „Kaufpreis auf Anfrage“
- Wohnfläche, Grundstück, Zimmer, Baujahr
- Beschreibung, Aufteilung, Ausstattung, Sonstiges
- Käuferprovision (Text wie bei den anderen Angeboten)
- Kleinanzeigen-Link
- Fotos

**Energieausweis:** Frag nach den Pflichtangaben (§ 87 GEG): Art des Ausweises, Kennwert in kWh/(m²·a), wesentlicher Energieträger, Baujahr laut Ausweis, Effizienzklasse. Sie gehören als Zeilen in `daten`. Will Mario sie nicht angeben, weise einmal darauf hin, dass Anzeigen ohne diese Angaben abgemahnt werden können, und mach es dann trotzdem. Das entscheidet er.

**Fotos:**
- Liegen sie als Dateien in deiner Sitzung, bereite sie so vor:
  `python3 _quelle/fotos.py <ordner-mit-fotos> bilder/<ordner>`
  Das erste Foto (Dateiname) wird das Titelbild. Will Mario ein anderes, gib `--erstes <datei>` mit.
- Kommen keine Dateien an, bitte Mario, die Fotos auf github.com in diesem Repo in den Ordner `eingang/` hochzuladen: Ordner `eingang` öffnen, „Add file“ → „Upload files“, Fotos hineinziehen, „Commit changes“. Dann `git pull`, `python3 _quelle/fotos.py eingang bilder/<ordner>` und die Rohfotos aus `eingang/` wieder löschen (die Datei `eingang/LIESMICH.txt` bleibt). Rohfotos gehören nur nach `eingang/`, nie nach `bilder/`, weil sie GPS-Daten enthalten.
- Sind Autokennzeichen oder Personen erkennbar: Mario darauf hinweisen, er entscheidet.

**Eintrag:**
- Schlüssel klein, ohne Umlaute, z. B. `gommern-2`.
- Datei `<schlüssel>.html`.
- Ordner `bilder/<schlüssel>`.
- In `objekte.json` alle Felder wie beim Beispiel `arnstein`: `datei`, `ordner`, `kurzname`, `h1`, `ort`, `alt`, `preis`, `karte_daten` (4 kurze Zeilen), `karte_provision`, `anzahl_fotos`, `kleinanzeigen`, `beschreibung` (Liste von Absätzen), `aufteilung` (Liste, darf leer sein), `ausstattung`, `sonstiges`, `daten` (Liste von [Bezeichnung, Wert]), `provision`.
- Schlüssel in `ANGEBOTE` (oben in `bauen.py`) eintragen. Die Reihenfolge dort ist die Reihenfolge auf der Startseite.

Sitemap und Startseite zieht der Generator selbst nach.

### Angebot verkauft
1. Schlüssel aus `ANGEBOTE` nehmen und den Eintrag aus `objekte.json` löschen.
2. Neuen Eintrag **vorne** in `referenzen.json` anlegen:
   - `id`: Ort und Zusatz, z. B. `magdeburg-cracau`
   - `titel`, `ort` (wie „39114 Magdeburg-Cracau“ oder „39249 Barby, OT Tornitz“)
   - `preis`: Angebotspreis, wie er im Angebot stand
   - `wohn`, `zimmer`, `grund`, `baujahr`
   - `text`: Kurzexposé, Absätze mit Leerzeile
3. Titelfoto übernehmen: `python3 _quelle/fotos.py bilder/<ordner>/01.jpg bilder/verkauft/<id>.jpg`
4. In `bauen.py`:
   - `<id>` vorne in `AUSWAHL` (Seite „Verkauft“)
   - Koordinate in `KARTE_ORTE` (Ortsmitte reicht, Breite und Länge, 3 Nachkommastellen)
   - Liegt der Ort im Gebiet einer Standortseite, `<id>` vorne in deren `ids` in `STANDORTE`
5. Alles entfernen, was noch auf das Angebot zeigt: `grep -n <schlüssel> _quelle/bauen.py _redirects` (z. B. Hinweiskasten „Aktuell im Angebot“ auf einer Standortseite).
6. `git rm` für `<datei>.html` und den Ordner `bilder/<ordner>`.
7. In `_redirects`:
   - Zeilen, die auf `/<datei>.html` zeigen, auf `/referenzen.html` umstellen
   - neue Zeile `/<datei>.html  /referenzen.html  301` hinzufügen

   So landen alte Links nicht im Leeren.
8. Bauen. Der Generator meldet, wenn noch ein Link oder eine Koordinate fehlt.

### Verkauftes Objekt nachtragen (war nie als Angebot online)
Wie oben ab Schritt 2. Das Foto kommt mit `fotos.py <foto> bilder/verkauft/<id>.jpg` ins Repo.

### Neue Google-Bewertungen
- **Zahl und Note:** In `bauen.py` die Werte `GOOGLE_NOTE` (z. B. `4.8`) und `GOOGLE_ANZAHL` ändern. Die Sterne passen sich an.
- **Neue Stimme unter „Das sagen Kunden“:** Nur echte Bewertungen aus Marios Google-Profil.
  - Wortlaut unverändert übernehmen, Name wie bei Google.
  - Als weiteren `stimme(...)`-Aufruf neben die vorhandenen setzen.
  - Den Text gibt dir Mario, oder er bestätigt ihn.
  - Nie eine Bewertung erfinden oder umformulieren.

### Texte ändern
- **Startseite und Seite „Verkauft“:** in `bauen.py` (Text suchen mit `grep -n`)
- **Standortseiten** (Barby, Schönebeck, Calbe, Bördeland, Jerichower Land, Magdeburg): in `STANDORTE`
- **„Gut zu wissen“:** in `daten/ratgeber.json`

### Foto oder Objekt entfernen (z. B. nach einer Beschwerde)
Sofort und vollständig, ohne Diskussion:
- `grep -rn <id> _quelle/` und jede Erwähnung entfernen (`referenzen.json`, `AUSWAHL`, `STANDORTE`, `KOPFBILDER`, `teaser_ids`, `KARTE_ORTE`)
- `git rm bilder/verkauft/<id>.jpg`
- bauen und hochladen

### Kopfbilder der Startseite
`KOPFBILDER` in `bauen.py`, jedes Bild als (Pfad, Bildunterschrift, Link). Nur Fotos verkaufter Objekte nehmen, die dafür in guter Größe vorliegen.

## Regeln, die immer gelten

- **Wir-Form** in allen Texten („Wir verkaufen …“). „Mario Frensel ruft Sie an“ als Name ist in Ordnung.
- **Das Wort „Pömmelte“ steht nirgends auf der Seite.** Der Generator prüft das. Die Adresse lautet „Neue Siedlung 7, 39249 Barby“.
- **Nichts erfinden:** keine Bewertungen, keine Verkaufszahlen, keine Auszeichnungen, keine Marktdaten, keine Fotos aus dem Internet, keine Preisschätzungen. Die Seite berechnet keinen Immobilienwert, das macht Mario nach der Besichtigung.
- **Keine fremden Dienste einbauen:** kein Google Maps, keine Statistik (Google Analytics u. Ä.), keine Werbe-Pixel (Google Ads, Facebook), keine eingebetteten Videos oder Instagram-Feeds, keine Schriften von Google, kein Chat-Fenster. Das bräuchte einen Cookie-Banner und eine neue Datenschutzerklärung. Wünscht Mario so etwas, erklär ihm das und verweise an Lio.
- **Rechtstexte** (`rechtliches.html` mit Impressum und Datenschutz, `widerruf.html`, Provisionstexte): nur mit dem Wortlaut ändern, den Mario vorgibt. Nie selbst Rechtstext formulieren. Bei inhaltlichen Fragen auf einen Anwalt verweisen.
- **Formulare** (Bewertung, Tippgeber, Kontakt): `name`-Attribute, `data-netlify`, `netlify-honeypot` und die versteckten Felder `form-name` nicht ändern. Sonst kommen keine Anfragen mehr bei Mario an.
- **Gestaltung** nur auf ausdrücklichen Wunsch ändern, und dann mit Vorschau: Farben, Schriften, Kopfbild mit Medaillon und Sternen, Reihenfolge der Abschnitte. Die Schalter `MARIO_STIL` und `STERNE_OBEN` bleiben, wie sie sind. Fällt der Abschnitt „Aktuelle Angebote“ weg (keine Angebote), stoßen zwei grüne Abschnitte aneinander. Dann Mario fragen, ob das so bleiben soll.
- **Fotos:** nur Marios eigene. Beschwert sich jemand über ein Foto: sofort entfernen (siehe oben).
- **Außerhalb dieses Repos kannst und sollst du nichts ändern:** Domain, DNS, IONOS, E-Mail-Postfach, Netlify-Einstellungen, Google-Unternehmensprofil, Kleinanzeigen, Instagram, Facebook. Sag Mario, wo er das selbst macht oder dass Lio es machen muss.

## Ansprechpartner
Lio Kühl hat die Seite gebaut und hat Zugriff auf GitHub und Netlify. Für alles, was hier nicht beschrieben ist und Technik betrifft (Domain, Netlify, neue Funktionen, Fehler, die du nicht lösen kannst), schickt Mario ihm den Fehlertext.
