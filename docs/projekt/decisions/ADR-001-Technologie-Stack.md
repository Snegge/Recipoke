# ADR-001 – Technologie-Stack

## Status und Einordnung

**Nachträgliche Dokumentation und Bewertung der bestehenden Entscheidung**, Stand 18.11.2026. Keine Neuauswahl, kein Versionswechsel und keine Migration. Diese lokale Blueprint-ID ersetzt nicht die vorhandenen Architektur-ADRs 001–017. Die unten betrachteten Alternativen sind eine **heutige vergleichende Einordnung**, keine behauptete historische Evaluation.

## Kontext

Recipoke verwaltet private Rezepte mit strukturierten Zutaten, geordneten Schritten und Bildern. Eine mobile Weboberfläche, geschützte Konten, Kochbuchrollen und sichere gemeinsame Bearbeitung sind bereits vorhanden. Import und Shopping Lists erweitern die Anwendung. Für die Hochschulaufgabe müssen Anforderungen, UI, API und Qualitätsziele nachvollziehbar zusammenpassen; weder die Vorlagen noch der Auftrag schreiben eine bestimmte Architektur oder eine native App vor.

Ein kleines Projekt profitiert von überschaubarem Betrieb und gemeinsam prüfbaren Datenverträgen. Browser und Server haben unterschiedliche Vertrauensgrenzen: lokale Komfortvalidierung ersetzt keine serverseitigen Regeln. SQLite erleichtert lokale Entwicklung; die vorhandene Cloud-Variante benötigt einen dauerhaften externen Datenspeicher. Diese Aussagen beschreiben den Repository-Entwurf, keine aktuelle Markt-/Hostingbewertung.

**Erfahrung – Annahme A-02:** Angular/TypeScript und C#/.NET sind als vorhandene Erfahrung im Auftragskontext relevant. Die Codebasis belegt praktische Verwendung dieser Technologien, aber keine Anzahl von Erfahrungsjahren und keine ursprüngliche Entscheidungsbegründung. Vertraute Werkzeuge reduzieren hier voraussichtlich Einarbeitung; die persönliche Vorerfahrung ist vom Projektverantwortlichen noch zu bestätigen.

## Entscheidung

Den eingesetzten Stack für die dokumentierte Projektplanung beibehalten und seine Grenzen ausdrücklich benennen. Quellen: [Q-08](../nachweise/QUELLEN.md#q-08--stack-und-historische-prüfberichte).

| Baustein | Nachweisbarer Stand | Begründung im bestehenden Projekt |
|---|---|---|
| Frontend | Angular Core/Common/Router/Forms 22.2.1; Angular Material/CDK 22.2.2; Build/CLI 22.2.2 in package.json | Vorhandene Routen, Reactive Forms, Guards und Material-Komponenten tragen Listen, Editor und Konfliktdialoge. Wiederverwendung ist günstiger als Neuaufbau. |
| Sprache / Reaktivität | TypeScript `~6.0.2`, Lockfile **6.0.3**; RxJS `~7.8.2`, Lockfile **7.8.2** | Typisierte DTOs und bestehende HTTP-/Formabläufe; Versionrange und aufgelöste Version nicht verwechseln. |
| Node-Anforderung | package.json: `^22.22.3 || ^24.15.0 || ^26.0.0` | Deklarierte Laufzeitvoraussetzung, keine Aussage über installierte oder produktiv verwendete Node-Version. |
| Backend | C#, ASP.NET Core, TargetFramework **net10.0** | Minimal-API-Endpunkte, DI, Identity und vorhandene Featuremodule; produktiver Runtime-Patch bzw. exakte C#-Compiler-Version aus diesem TFM allein nicht ableitbar. |
| EF/Identity/OpenAPI | EF Core Sqlite/Design, Identity.EntityFrameworkCore, AspNetCore.OpenApi und DataProtection.EntityFrameworkCore jeweils **10.0.12** | Relationale Daten, vorhandene Migrationen und typisierte Endpunkte; veröffentlichter Vertragssnapshot kann mit Frontend-Modellen abgeglichen werden. |
| Persistenz | SQLite lokal; Npgsql.EntityFrameworkCore.PostgreSQL **10.0.3** für PostgreSQL; eigener PostgresRecipeDb mit separaten Migrationen | Lokale Installation ohne DB-Server; Cloud-Daten und verschlüsselter Keyring liegen laut bestehendem Entwurf dauerhaft in PostgreSQL. Keine Serverversion aus dem Provider ableiten. |
| Bilder | Ein Rezeptbild als DB-Blob, API-Grenze **5 MiB**; lokaler Canvas-Resize im Browser | Einfache gemeinsame Sicherung/Autorisierung; wachsendes Datenbank-/Backupvolumen und zwei Save-Requests sind die Kosten. |
| Authentifizierung | ASP.NET Core Identity, Cookie `Recipoke.Session`, same-origin; kein Token in Browserstorage; vorhandener Header `X-RecipeAtlas: 1` | Bestehende eingeladene Konten und Rechte; API und Browser folgen demselben Sitzungsmodell. |
| Zusammenarbeit | Frontend `@microsoft/signalr` **10.0.11**, ASP.NET-Core-Hub; LongPolling im vorhandenen Shopping-Client | Gezielte Invalidierungen für gemeinsame Listen; zusätzliche Verbindungs-/Retry-Zustände bleiben zu prüfen. Rezeptkonflikte nutzen Versionen, kein SignalR-Zwang. |
| Import | AngleSharp **1.5.0**, YoutubeDLSharp **1.2.0**; Tool-Pins yt-dlp **2026.8.19**, yt-dlp-ejs **0.8.0**; optional Gemini-Adapter | Bestehende getrennte Website-/Social-/Textadapter. Externe Verfügbarkeit und korrekte Extraktion benötigen Review/Fallback; ein Pin belegt keine Installation oder Live-Funktion. |
| Installierbare Hülle | `@angular/service-worker` **22.2.1**, Manifest und Icons | Öffentliche Shell lässt sich cachen; private API-Inhalte bleiben außerhalb des Offline-Caches. |
| Darstellung | Material Color Utilities **0.4.0**, esbuild **0.28.2** im lokalen Frontend-Arbeitsstand | Bereits vorhandene uncommittete Darstellungserweiterung; nicht als veröffentlichte Basis ausgeben. |
| Betriebspfad | Vorhandene Docker/Caddy-Dateien und kombinierter Render-/Neon-Prototyp laut ADR 017 | Ein zusammengehöriges Frontend/Backend kann same-origin laufen. Kein neuer Infrastrukturauftrag und keine Aussage über aktuell bereitgestellte Dienste. |

**Versionswiderspruch:** Die Backend-README nennt SDK 10.0.401 mit Verweis auf `global.json`; diese Datei existiert im untersuchten Checkout nicht. Daher ist diese SDK-Version hier nur eine Dokumentationsbehauptung und kein nachgewiesener Repository-Pin. Es wird nichts ergänzt oder umgestellt.

**Architektur:** Featureorientierter Monolith mit unabhängiger Domain-Assembly. Application-Operationen verwenden EF Core unmittelbar; das ist kein striktes Clean-Architecture-Schichtenmodell. HTTP, fachliche Operationen und Domain sind erkennbar getrennt, ohne generisches Repository-/Unit-of-Work-Doppelmodell. Bestehende ADR 001, 003, 007 und 017 bleiben die detaillierten Quellen.

## Betrachtete Alternativen

Neu für diese nachträgliche Bewertung betrachtet; kein Beleg, dass sie historisch erprobt wurden:

| Alternative | Konkreter Vorteil für Recipoke | Konkreter Nachteil gegenüber dem Bestand | Bewertung |
|---|---|---|---|
| React/TypeScript statt Angular | Freiere Zusammensetzung einer kleinen mobilen UI | Bestehende Forms, Guards, Material-Komponenten und Tests müssten übertragen werden; mehr Entscheidungen für dieselben Abläufe | Für bestehendes Produkt kein belegter Nutzen eines Wechsels |
| ASP.NET Razor Pages statt separater SPA | Ein Laufzeit-/Sprachschwerpunkt, weniger replizierte DTO-Modelle | Lokale Skalierung, Bildaufbereitung, Konfliktvergleich und Live-Listen brauchen weiterhin Browserlogik; vorhandenes Angular würde ersetzt | Für eine viel kleinere Neuanwendung plausibel, hier hoher Umbau |
| Node.js/TypeScript-Backend | Gemeinsame Sprache mit Frontend | Vorhandene Domain-/Mengenregeln, EF-Migrationen, Identity-Sitzungen und Tests müssten neu abgebildet werden | Kein aktueller Änderungsgrund |
| Ausschließlich SQLite | Sehr einfache lokale Bereitstellung und Sicherung | Passt nicht zum bereits dokumentierten Cloud-Pfad mit flüchtigem Anwendungsdateisystem | Lokal beibehalten; bestehende PostgreSQL-Option nachvollziehbar |
| Ausschließlich PostgreSQL | Ein Provider und weniger doppelte Migrationspflege | Zusätzlicher Datenbankdienst auch für lokale Entwicklung; vorhandene SQLite-Bestände müssten umgezogen werden | Keine Migration durch diesen ADR; Dualität ist bewusster Aufwand |
| Bilder in Object Storage | Kleinere relationale Backups, getrennte Bildkapazität | Zusätzliche Rechte-, Konsistenz-, Lösch- und Betriebsabläufe | Erst bei gemessener Größen-/Lastgrenze erneut bewerten |

## Konsequenzen

Leichter werden die Wiederverwendung der bestehenden Screens und Operationen, typisierte DTOs, relationale Rechte-/Rezeptdaten und ein überschaubarer gemeinsamer Betrieb. Angular Material liefert vorhandene Fokus-/Formkonventionen, die weiterhin korrekt verwendet und geprüft werden müssen. Der manuelle Rezeptablauf bleibt ohne Importanbieter nutzbar.

Schwerer werden die koordinierte Pflege zweier Repositories, die doppelte Mengen-/Einheitenabbildung, Vertragsänderungen über Frontend und Backend sowie zwei Migrationspfade. Der PostgreSQL-Prototyp serialisiert fachliche Writes über einen Advisory Lock; PostgreSQL-Unterstützung allein bedeutet keine hohe Schreibskalierbarkeit. SQLite startet mit automatischen Migrationen, PostgreSQL verlangt den expliziten Migrationsablauf; sichere Bestandsanalyse darf daher die Anwendung nicht unbedacht starten.

Fotos vergrößern Backups. Text und Foto werden getrennt gespeichert, sodass Teilerfolge sichtbar bleiben müssen. Drittanbieterimporte bringen Netzwerk-, Extraktions- und Konfigurationsabhängigkeiten. PWA-Shell und Themes verbessern den Zugang, lösen aber keine private Offline-Synchronisation. Qualitätsziele für Änderbarkeit, Berechtigungen, Konflikte und mobile Bedienung stehen im [Utility Tree](https://gitlab.ai.it.hs-worms.de/inf4155/Recipoke/-/wikis/Utility-Tree); keine davon wurde durch diesen ADR implementiert oder neu vermessen.
