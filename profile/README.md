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

Gemaakt door Erik Hoekstra (Gemeente Haarlem, i-Ontwikkeling), met assistentie van Claude (Anthropic). Licentie: EUPL 1.2.
