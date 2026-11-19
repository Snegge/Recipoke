# Validierung der Hochschulaufgaben

Stand: 19.11.2026 (Bestandsanalyse vom 18.11.2026). Ergebnis einer **statischen Prüfung** zweier lokaler Repositories. Keine funktionale Abnahme der Anwendung. Ein vorhandener Blueprint-Ordner wurde beim Einstieg nicht gefunden; die vorbestehenden Dokumente außerhalb dieses Ordners bleiben unverändert.

## Ausgangszustand und Regeln

Im aktuellen Repository, im Frontend und in den übergeordneten Workspace-Verzeichnissen wurde keine geltende `AGENTS.md` gefunden; auch die rekursive Suche einschließlich versteckter Verzeichnisse lieferte in diesen beiden Repositories keine. Die auf andere Projekte bezogenen AGENTS-Dateien gelten hier nicht. Ponytail wurde gelesen; der Auftrag begrenzt sämtliche Änderungen auf Dokumentation.

Backend-HEAD: `bfb36c4`; Ausgangsstatus nur `?? Hochschulvorlagen/`. Frontend-HEAD: `20e3b64`; bereits zahlreiche geänderte Dateien, insbesondere Darstellung, Accounts, Templates, package.json und Lockfile sowie unversionierte Darstellungsunterlagen und Komponenten. Bei der Übernahme bestätigte der SHA256-Abgleich 437 unveränderte Ausgangsdateien außerhalb des Blueprints; die einmalige Hashdatei wurde nach Abschluss entfernt. Die normale Sandbox-Git-Abfrage scheiterte an unterschiedlichen Eigentümern; die anschließenden autorisierten Leseabfragen liefen im Eigentümerkontext. Keine safe.directory- oder andere Git-Konfiguration wurde geändert.

## Kurze Bestandsaufnahme

| Bereich | Befund und konkrete Nachweise | Einordnung |
|---|---|---|
| Produkt | README und Architektur führen **Recipoke**; RecipeAtlas bleibt Kompatibilitätsname. [Q-01](nachweise/QUELLEN.md#q-01--produkt-vorlagen-und-architektur) | im Code und in Dokumentation vorhanden |
| Persönliche Rezepte | Kochbuchgebundene CRUD-Endpunkte, Bild-Endpunkte, Stringmengen, Such-/Seitenvertrag; Angular Liste, Editor und Detail. [Q-04](nachweise/QUELLEN.md#q-04--rezeptvertrag-speicherung-und-suche), [Q-06](nachweise/QUELLEN.md#q-06--kochen-und-lokale-interaktion) | im Code vorhanden, statisch geprüft |
| Zugang | Identity-Cookies, eingeladene Konten, persönliche Kochbücher, Owner/Editor/Viewer, keine öffentliche Registrierung. [Q-02](nachweise/QUELLEN.md#q-02--routen-und-sitzungen), [Q-03](nachweise/QUELLEN.md#q-03--kochbuchkontext-und-rechte) | im Code vorhanden |
| Datenmodell | Recipe enthält CookbookId, Version, geordnete Ingredients/Steps, optionales Bild; Cookbook hat Owner und Mitgliedschaften. [Q-03](nachweise/QUELLEN.md#q-03--kochbuchkontext-und-rechte), [Q-04](nachweise/QUELLEN.md#q-04--rezeptvertrag-speicherung-und-suche) | im Code vorhanden |
| Gemeinsame Bearbeitung | expectedVersion bzw. If-Match, expliziter Konfliktvergleich, getrennte Text-/Bildspeicherung. [Q-05](nachweise/QUELLEN.md#q-05--editor-bild-und-konflikte) | im Code vorhanden |
| Erweiterungen | Website-/Social-/Textimport, gemeinsame Kochbücher und unabhängige Shopping Lists mit SignalR. [Q-07](nachweise/QUELLEN.md#q-07--erweiterungen) | im Code vorhanden; Live-Provider nicht geprüft |
| Mobil/PWA | Responsive Templates; öffentliche Shell installierbar, keine private Offline-Synchronisation; Darstellung lokal weiterentwickelt. [Q-07](nachweise/QUELLEN.md#q-07--erweiterungen) | Shell/Darstellung im Code vorhanden; Offline-Gesamtziel nur teilweise vorhanden |
| Persistenz und Stack | .NET/EF Core 10; SQLite lokal, PostgreSQL-Provider für Cloud; Angular 22.2 und Material, konkrete Versionen im ADR. [Q-08](nachweise/QUELLEN.md#q-08--stack-und-historische-prüfberichte) | im Code vorhanden; „nur SQLite“ wäre unvollständig |
| Bestehende Abgabeunterlagen | Architektur-ADRs, Betriebs- und Prüfberichte, drei PDFs; keine eigenständige Recipoke-Vision, kein konsistenter Hochschul-Storybacklog/Utility Tree/Wireframe-API-Satz gefunden. | Hochschulaufbereitung fehlte |

Die SQLite-Startsequenz in `backend/RecipeAtlas.Api/Program.cs` ruft `MigrateAsync` auf. Auch ein Frontend-Start hat laut package.json einen erzeugenden `prestart`-Schritt. Deshalb wurden weder Anwendung noch Builds, Migrationen oder Anwendungstests gestartet. Vorhandene Screenshots und Templates wurden ausgewertet. Historische Prüfberichte sind Quellen, keine aktuelle Bestätigung.

## Aufgabenabgleich und Verbesserungen

| Aufgabenanforderung | Vorhandener Nachweis | Bewertung | Lücke oder Widerspruch | Vorgenommene Dokumentationsverbesserung |
|---|---|---|---|---|
| Bestand statt Neuentwicklung | README, Architektur, aktuelle Arbeitsdateien | tragfähig | Ältere Single-owner- und reine SQLite-Aussagen nicht mehr allgemein aktuell | Statusmatrix, Quellen und nachträgliches ADR |
| Vision: 3–5 Sätze, Problem/Zielgruppe/Nutzen | Produktabsätze in README; MyFlow.Money-Beispiel S. 1 | teilweise | Keine eigenständige, nutzerorientierte Recipoke-Vision | Vier Sätze und vier stabile Kern-Use-Cases in [Vision](https://gitlab.ai.it.hs-worms.de/inf4155/Recipoke/-/wikis/Vision) |
| Core fachlich begründen | Bestehende Funktionen, kein verbindlicher Kurs-Core dokumentiert | offen als Umfangsentscheidung | Implementiert bedeutet nicht verpflichtender Core | Acht Core-Stories, sechs grobe Erweiterungen; Annahme A-01 |
| Einzelne Story-Issues mit Details | PDF-Template S. 3; Feature-/Betriebsdokumentation | Template vorhanden, projektspezifische Issue-Fassung fehlte | Titel, ID, Core/Extension, Zusatzaspekte, Wireframes, Required, Dependencies, DoD, Developer Tasks und Open Issues zusammenführen | [issues](BACKLOG.md) enthält je Core-Story alle Template-Felder plus Nutzen, Status, Akzeptanzkriterien und konkrete Testfälle |
| Erweiterung als normales Issue mit „epic“ | Beispiel trennt Produktumfang und Lehr-Core | passend | Keine GitLab-Epic-Hierarchie erforderlich | Sechs normale EPIC-Dateien mit Label `epic`, ohne Detailstories |
| Implementierung und Abnahme trennen | Historische Prüfberichte, bestehende Tests | Teilbeleg | Neue Kriterien und lokale Frontend-Änderungen nicht abgenommen | Kein Issue als geschlossen vorgeschlagen; separater Prüfstatus überall |
| Utility Tree: 3–5 Szenarien, Schwerpunkt Modifiability | Szenario-Spickzettel vollständig textuell gelesen | Vorgaben erfüllt im Entwurf | Frühere ADRs sind keine messbaren Qualitätsszenarien | Fünf Szenarien, davon zwei Modifiability, sechs Felder, H/M/L und Zielvorgaben |
| 3–5 echte Wireframes | Angular-Templates, zwei vorhandene mobile Screenshots | UI-Grundlage vorhanden | Keine vereinfachte, nachvollziehbare Core-Skizzenserie | Zeichnungen am 19.11.2026 entfernt; eigene Handzeichnungen folgen. Fünf Screen-IDs mit Daten-/API-Zuordnung bleiben als Grundlage erhalten |
| API aus Wireframes ableiten | Endpunktcode, DTOs, Frontend-Services, OpenAPI-Snapshot | weitgehend direkt verwendbar | Alte unscoped Rezeptpfade sind stillgelegt; Portionen/Abhaken brauchen kein API | [API-Entwurf](https://gitlab.ai.it.hs-worms.de/inf4155/Recipoke/-/wikis/API-Entwurf) übernimmt bestehende Pfade und Versionierung, keine Ersatz-API |
| Stack begründen | Projektdateien und ADRs 001–017 | umfangreiche technische Basis | README nennt global.json/SDK 10.0.401, global.json fehlt; Erfahrung nicht belegt | [ADR-001](decisions/ADR-001-Technologie-Stack.md) nennt belegte Paketversionen, SDK-Lücke und Erfahrungsannahme |
| Zusammenführung/GitLab | GitLab-Repository inf4155/Recipoke erreichbar | API-Anmeldung über glab außerhalb der Sandbox erfolgreich | Bestand geprüft: zuvor keine Issues oder Wiki-Seiten; Übernahme siehe Protokoll | Traceability, Linkregeln und manuelle Duplikatprüfung in [Übernahme](https://gitlab.ai.it.hs-worms.de/inf4155/Recipoke/-/blob/efffae8373ab5cbbbc90a75b8a558549bb6376c0/docs/gitlab-blueprint/GITLAB-UEBERNAHME.md) |

## Begründete Core-Abgrenzung

Der Core ist der kleinste vollständige **persönliche Rezeptkreislauf im vorhandenen Produkt**: Zugang, Kochbuchkontext, Finden, manuelles Erfassen, Lesen/Portionieren, Korrigieren, ein optionales Foto und bewusstes Löschen. Login und Kochbuchwahl sind notwendig, weil reale Rezepte über private, kochbuchgebundene Endpunkte erreichbar sind. Mengen und Reihenfolge gehören zum Kochen; Bildpflege ergänzt die ausdrücklich vorhandene persönliche Rezeptdarstellung. Rollenprüfung und Konfliktschutz bleiben auch im Core verpflichtend: Bereits geteilte Bestände dürfen nicht unsicher werden.

Das persönliche Kochbuch wird bei Einladungsannahme erstellt (`Invitations.cs`). Für den Core wird ein bestehendes aktives Konto samt zugänglichem Kochbuch vorausgesetzt. Konto-Provisionierung und zusätzliche Kochbücher/Freigaben bleiben eigenständige Erweiterungen. Das ist keine Neuentwicklung einer zweiten, vereinfachten Anmeldung.

Import spart Eingabearbeit, ist aber für einen vollständigen manuellen Ablauf nicht erforderlich und bringt externe Providerabhängigkeiten. Shopping Lists haben einen unabhängigen Lebenszyklus und eigene Rechte. Teilen, Administration, PWA und Themes erweitern den Nutzen, ohne Voraussetzung für die ausgewählten Lern-/Abnahmeabläufe zu sein. Umfangskennzeichnung und Implementierungsstatus sind unabhängig.

## Statusvokabular

- **im Code vorhanden**: zusammenhängender statischer Pfad gefunden, kein aktueller Funktionsnachweis.
- **teilweise vorhanden**: Basis vorhanden, konkret genannter Rest fehlt oder ist nur Entwurf.
- **geplant**: als zurückgestellt/geplant in vorhandenen Unterlagen benannt, nicht als umgesetzt gezählt.
- **vorgeschlagene Verbesserung**: neu in diesem Blueprint, weder implementiert noch Produktbeschluss.

Prüfstatus separat: „statisch geprüft; Abnahme offen“. Historische Nachweise werden ausdrücklich historisch genannt. Priorität bedeutet Reihenfolge der fachlichen Absicherung, nicht Behauptung noch fehlenden Codes.

## Annahmen und offene Fragen

- **A-01 – Annahme:** Der Core darf fachlich aus dem Projekt abgeleitet werden; ein verbindlicher individueller Umfang des Lehrenden ist nicht enthalten. Acht Core-Stories sind ein begründeter Vorschlag. Spätere Bestätigung kann die Zuordnung ändern, ohne IDs neu zu vergeben.
- **A-02 – Annahme:** Erfahrung mit Angular/TypeScript und C#/.NET ist vorhanden, wie im Auftrag als relevanter Kontext genannt. Dauer, Tiefe und historische Auswahlgründe sind nicht aus Code ableitbar und bleiben zu bestätigen.
- **A-03 – Annahme:** Testnutzer besitzen aktive, durch Einladung angelegte Konten und ein persönliches Kochbuch. Ein leerer/entzogener Kochbuchbestand wird erklärt; im Core wird dafür kein neues Onboarding erfunden.
- **A-04 – Annahme:** Deutsch ist die Sprache der Abgabe. Die bestehende UI ist überwiegend Englisch; die Sprache der noch zu ergänzenden Handzeichnungen ist offen, kein zugesagtes Übersetzungsprojekt.
- **A-05 – Zielvorgaben:** Zeit-/Aufwands- und Usabilitywerte des Utility Trees sind Planungsziele, keine Messwerte oder bisherige Zusagen.
- Ziel: GitLab-Projekt `inf4155/Recipoke`. Offen: vorhandene Issues und Wiki-Seiten im Browser prüfen; die verfügbare Anmeldung erlaubt Git-Lesen, aber keinen bestätigten API-Zugriff.
- Offen: SDK-Pin fehlt trotz README-Verweis. Im Blueprint als Widerspruch dokumentiert; keine Konfiguration geändert.
- Offen: Funktionsabnahme am aktuellen Arbeitsstand, mobile Geräteprüfung und Live-Importanbieter; für die reine Dokumentation bewusst nicht durchgeführt.

Alle drei Hochschulvorlagen wurden gefunden und textuell abgeglichen. Kein inhaltlich blockierender Vorlagenmangel. PDF-Layout/Fettmarkierungen wurden nicht als vollständig geprüft ausgegeben. Abschlussnachweis: [PRUEFPROTOKOLL](https://gitlab.ai.it.hs-worms.de/inf4155/Recipoke/-/blob/efffae8373ab5cbbbc90a75b8a558549bb6376c0/docs/gitlab-blueprint/PRUEFPROTOKOLL.md).
