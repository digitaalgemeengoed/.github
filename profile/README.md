# Digitaal GemeenGoed

Open-source bouwstenen voor gemeenten, gedeeld onder de EUPL 1.2.

## De Office Assistent

Een assistent in de tekstverwerker, het rekenblad en de presentatie. Hij helpt een medewerker om een stuk goed vast te leggen: metadata invullen, toegankelijkheid controleren en herstellen (WCAG), en persoonsgegevens anonimiseren in het document zelf. Hetzelfde paneel werkt in Microsoft Word, Excel en PowerPoint, in LibreOffice, in Collabora Online en in een demo in de browser.

> **Prototype.** Gemaakt bij de gemeente Haarlem om de architectuur te beproeven, geen product. Gebruik alleen testgegevens.

### Begin hier

| Je wilt … | Ga naar |
|---|---|
| **uitproberen** op een omgeving die al draait | [TESTEN.md](https://github.com/digitaalgemeengoed/oa-uitrol/blob/main/TESTEN.md) |
| een eigen omgeving **inrichten** (drie VPS'en) | [INRICHTING.md](https://github.com/digitaalgemeengoed/oa-uitrol/blob/main/server/INRICHTING.md), stap voor stap |
| **ontwikkelen** op je eigen computer | [oa-uitrol](https://github.com/digitaalgemeengoed/oa-uitrol#op-één-machine): één opdracht haalt alles op |
| zien **wat er veranderd is** | [CHANGELOG van oa-uitrol](https://github.com/digitaalgemeengoed/oa-uitrol/blob/main/CHANGELOG.md) |

### Hoe de repositories samenhangen

```
 werkomgeving (Word, LibreOffice, Collabora, demo)
        │  oa-adapters
        ▼
 paneel ─ oa-paneel (React, NL Design System)
        │  oa-kern (de modules)   oa-contract (de afspraken)
        ▼
 oa-integratielaag (inloggen, twee ingangen)
        ├──► opendop-mini   platform: versies, gebeurtenissen, versleuteling
        └──► oa-services    assistent-diensten ──► oa-wcag, OpenAnonymiser
 oa-beheer: beheerportaal          oa-uitrol: alles samen uitrollen
```

| Repository | Wat |
|---|---|
| [oa-uitrol](https://github.com/digitaalgemeengoed/oa-uitrol) | **Begin hier.** Uitrol op één machine of drie VPS'en, inrichting, testronde |
| [oa-contract](https://github.com/digitaalgemeengoed/oa-contract) | Het contract: inhoud, vermogens, bevindingen, gebeurtenissen |
| [oa-kern](https://github.com/digitaalgemeengoed/oa-kern) | De assistentkern met de modules, zonder framework |
| [oa-paneel](https://github.com/digitaalgemeengoed/oa-paneel) | Het paneel in React met NL Design System |
| [oa-adapters](https://github.com/digitaalgemeengoed/oa-adapters) | Adapters per werkomgeving, de LibreOffice-extensie en de demo |
| [oa-integratielaag](https://github.com/digitaalgemeengoed/oa-integratielaag) | Next.js: inloggen (Keycloak) en de twee ingangen |
| [oa-services](https://github.com/digitaalgemeengoed/oa-services) | Assistent-diensten: toegankelijkheid beoordelen en persoonsgegevens herkennen; lezen en rekenen, nooit vastleggen |
| [oa-wcag](https://github.com/digitaalgemeengoed/oa-wcag) | Toegankelijkheidscontrole van docx, xlsx en pptx |
| [opendop-mini](https://github.com/digitaalgemeengoed/opendop-mini) | Minimaal platform: Object Service op PostgreSQL en S3 |
| [oa-beheer](https://github.com/digitaalgemeengoed/oa-beheer) | Beheerportaal: accounts, groepen, audit |

### Inloggen: Keycloak

Medewerkers loggen in via **Keycloak** (OpenID Connect), in de realm `gemeenten`.

- De **integratielaag** is de enige client van Keycloak: authorization code flow met PKCE, als vertrouwelijke client. Het token blijft op de server; de browser en de werkomgeving (Word, LibreOffice, Collabora) krijgen alleen een sessiecookie (httpOnly). Platform en diensten krijgen de identiteit van de integratielaag, achter een dienstsleutel.
- **Uit het token:** de claim `gemeente` (welke gemeente; gemeenten zien elkaars stukken niet), de rollen `medewerker` en `beheerder`, en het niveau voor persoonsgegevens `ppl-1`, `ppl-2` of `ppl-3` (AVG art. 6, 9 en 10).
- **Tweede factor:** nieuwe gebruikers koppelen bij de eerste inlog een authenticator-app (TOTP); na tien foute wachtwoorden volgt een blokkade. Het beheerportaal eist de tweede factor en heeft daarnaast een noodtoegang voor als Keycloak plat ligt.
- Het Keycloak-beheer is niet van buiten bereikbaar (alleen via een SSH-tunnel).

### Techniek

| Laag | Gebruikt |
|---|---|
| Paneel | React 19, TypeScript, Vite; [NL Design System](https://nldesignsystem.nl) (Utrecht-componenten, Haarlem-thema) |
| Werkomgevingen | Office.js (Word, Excel, PowerPoint); een LibreOffice-extensie in Python (UNO) met het webpaneel in WebKitGTK, WebView2 of WKWebView; Collabora Online via WOPI |
| Integratielaag | Next.js 16 (Node.js 22), jose voor OIDC |
| Platform (opendop-mini) | Fastify, PostgreSQL 16 met row-level security per gemeente, onveranderlijke gebeurtenissen en een outbox; S3-opslag met Object Lock ([SILO](https://github.com/pgsty/silo)); optioneel OpenSearch |
| Sleutels | OpenBao (transit): een sleutel per document, persoonsgegevens per AVG-categorie versleuteld (AES-256-GCM, ChaCha20-Poly1305, RSA-OAEP) |
| Assistent-diensten | Fastify; [OpenAnonymiser](https://github.com/ConductionNL/anonymiq-light) van Conduction (Presidio en spaCy, Nederlands taalmodel); oa-wcag in Java 21 met Apache POI |
| Beheerportaal | React en Vite, Fastify, eigen PostgreSQL |
| Uitrol | Docker Compose op drie VPS'en (Ubuntu 24.04), WireGuard ertussen, Caddy voor HTTPS |

Standaarden: WCAG 2.2 AA, MDTO (metadata), de Woo-informatiecategorieën, OpenID Connect, WOPI. Alles open source. De assistent zelf gebruikt geen diensten buiten de eigen servers: geen externe AI of cloud-API's.

Gemaakt door Erik Hoekstra (Gemeente Haarlem, i-Ontwikkeling), met assistentie van Claude (Anthropic). Licentie: EUPL 1.2.
