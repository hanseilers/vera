# VERA-Standaard Documentatie voor ERP-Platform Woningcorporaties

> **Doel**: Dit document bevat alle verzamelde informatie van de CORA/VERA-standaard die nodig is voor het bouwen van een ERP-platform voor woningcorporaties dat voldoet aan de VERA-standaard.
>
> **Bron**: https://www.coraveraonline.nl
>
> **Versie**: VERA 4.3 (januari 2026)

---

## Inhoudsopgave

1. [Wat is CORA en VERA?](#1-wat-is-cora-en-vera)
2. [De VERA-standaard](#2-de-vera-standaard)
3. [Informatiedomeinen (Datamodel)](#3-informatiedomeinen-datamodel)
4. [Bedrijfsobjecten per Domein](#4-bedrijfsobjecten-per-domein)
5. [Bedrijfsfuncties](#5-bedrijfsfuncties)
6. [Processen](#6-processen)
7. [Producten en Diensten](#7-producten-en-diensten)
8. [Referentiedata](#8-referentiedata)
9. [OpenAPI Koppelvlakken](#9-openapi-koppelvlakken)
10. [Technische Implementatie](#10-technische-implementatie)
11. [Bronnen en Links](#11-bronnen-en-links)

---

## 1. Wat is CORA en VERA?

### CORA (COrporatie Referentie Architectuur)
CORA is de referentiearchitectuur voor woningcorporaties in Nederland. Het beschrijft:
- **Hoe** een woningcorporatie werkt
- **Welke** processen er zijn
- **Welke** bedrijfsfuncties nodig zijn
- De **samenhang** tussen alle onderdelen

CORA wordt beheerd door **CorpoNet**.

### VERA (Volkshuisvestelijke En Referentie Architectuur)
VERA is de datastandaard voor woningcorporaties. Het bevat:
- Het **logisch gegevensmodel**
- **Referentiedata** (standaard waardelijsten)
- **Procesberichten** voor gegevensuitwisseling
- **OpenAPI koppelvlakspecificaties**

VERA wordt beheerd door **Aedes Datastandaarden**.

### Samenhang
```
┌─────────────────────────────────────────────────────────────┐
│                         NORA Familie                         │
│  (Nederlandse Overheid Referentie Architectuur)              │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│   ┌─────────────────┐     ┌─────────────────┐               │
│   │      CORA       │     │      VERA       │               │
│   │  (Architectuur) │◄───►│  (Datastandaard)│               │
│   │                 │     │                 │               │
│   │ - Processen     │     │ - Gegevensmodel │               │
│   │ - Functies      │     │ - Referentiedata│               │
│   │ - Diensten      │     │ - OpenAPI specs │               │
│   └─────────────────┘     └─────────────────┘               │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## 2. De VERA-standaard

### Kerncomponenten

#### 2.1 Het Logisch Gegevensmodel
- Technologie-onafhankelijk datamodel
- Gebaseerd op OO-principes (Object-Oriented)
- Georganiseerd in **12 informatiedomeinen**
- Eén authentieke bron per gegeven

#### 2.2 Het Procesberichtenmodel
- Event-driven architectuur
- Gegevensuitwisseling op basis van gebeurtenissen
- Berichten tussen werkprocessen

#### 2.3 Referentiedata
- Vaste waarden voor attributen (enumeraties)
- Drieletterige codes + naam + omschrijving
- Onafhankelijk van VERA-releases bijgewerkt
- Publiek beschikbaar (CC0-licentie)

#### 2.4 Koppelvlakken
- OpenAPI 3.0 standaard
- YAML specificaties
- Zelf te implementeren binnen eigen technologie

---

## 3. Informatiedomeinen (Datamodel)

VERA kent **12 informatiedomeinen** die de gegevensobjecten groeperen:

| # | Domein | Beschrijving |
|---|--------|-------------|
| 1 | **Algemeen** | Algemene gegevens, referentiedata, sturingslabels |
| 2 | **Dossier** | Documentbeheer, informatieobjecten |
| 3 | **Energie** | Energieprestaties, labels, verbruik |
| 4 | **Financiën** | Boekingen, facturen, betalingen |
| 5 | **Kwaliteit** | Conditiemetingen, inspecties |
| 6 | **Onderhoud** | Onderhoudsorders, werkzaamheden |
| 7 | **Organisatie** | Medewerkers, afdelingen, rollen |
| 8 | **Overeenkomsten** | Huurcontracten, koopcontracten |
| 9 | **Projectontwikkeling** | Nieuwbouw, renovatieprojecten |
| 10 | **Relaties** | Huurders, leveranciers, contactpersonen |
| 11 | **Vastgoed** | Eenheden, panden, clusters, adressen |
| 12 | **Woonruimteverdeling** | Woningzoekenden, publicaties, matching |

### Domeindiagram
```
┌──────────────────────────────────────────────────────────────────┐
│                     VERA INFORMATIEDOMEINEN                       │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌───────────┐  ┌───────────┐  ┌───────────┐  ┌───────────┐     │
│  │ Algemeen  │  │  Dossier  │  │  Energie  │  │ Financiën │     │
│  └───────────┘  └───────────┘  └───────────┘  └───────────┘     │
│                                                                   │
│  ┌───────────┐  ┌───────────┐  ┌───────────┐  ┌───────────┐     │
│  │ Kwaliteit │  │ Onderhoud │  │Organisatie│  │Overeenkom.│     │
│  └───────────┘  └───────────┘  └───────────┘  └───────────┘     │
│                                                                   │
│  ┌───────────┐  ┌───────────┐  ┌───────────┐  ┌───────────────┐ │
│  │Projectontw│  │ Relaties  │  │ Vastgoed  │  │Woonruimteverd.│ │
│  └───────────┘  └───────────┘  └───────────┘  └───────────────┘ │
│                                                                   │
└──────────────────────────────────────────────────────────────────┘
```

---

## 4. Bedrijfsobjecten per Domein

### 4.1 Domein Vastgoed (Kerndomein)

Het vastgoeddomein is het hart van een woningcorporatie-systeem.

**Definitie**: Vastgoed is de grond, de nog niet gewonnen delfstoffen, de met de grond verenigde beplantingen, evenals de gebouwen en werken die duurzaam met de grond zijn verenigd.

#### Kernentiteiten Vastgoed

| Entiteit | Beschrijving |
|----------|-------------|
| **Eenheid** | Verhuurbare/verkoopbare eenheid (woning, bedrijfsruimte, garage) |
| **Pand** | Fysiek gebouw conform BAG |
| **Gebouw** | Cluster van aaneengesloten panden |
| **Cluster** | Administratieve groepering van eenheden |
| **Adres** | Combinatie BAG-objecten (woonplaats, nummeraanduiding, openbare ruimte) |
| **BouwkundigElement** | Kleinste onderhoudbaar onderdeel |
| **Bouwdeel** | Verzameling bouwkundige elementen |
| **CollectiefObject** | Gedeelde ruimte/installatie |

#### Aanvullende Vastgoed-entiteiten

| Entiteit | Beschrijving |
|----------|-------------|
| **Woningwaardering** | WWS-punten voor maximale huurprijs |
| **WoningwaarderingGroep** | Groepering WWS-criteria |
| **WoningwaarderingCriterium** | Individueel WWS-element |
| **Marktwaarde** | Marktwaarde van eenheid |
| **Beleidswaarde** | Beleidswaarde voor verantwoording |
| **Taxatie** | Resultaat waardebepaling |
| **Garantie** | Garantievoorwaarden per onderdeel |
| **Renovatie** | Renovatiehistorie |
| **Energieprestatie** | Energielabel en prestaties |
| **EenheidToestand** | Status (leegstand, verhuurd, etc.) |
| **Prijscomponent** | Opbouw huurprijs |
| **KadasterPerceel** | Koppeling met Kadaster |
| **ZekerheidVerpanding** | Hypotheek, pandrechten |

#### Geografische entiteiten

| Entiteit | Beschrijving |
|----------|-------------|
| **Gemeente** | CBS gemeente |
| **Wijk** | CBS wijk |
| **Buurt** | CBS buurt |
| **Stadsdeel** | Bestuurlijk deelgebied |
| **Woonplaats** | BAG woonplaats |
| **Geometrie** | GIS-gegevens |
| **Punt** | GPS-coördinaten |

### 4.2 Domein Relaties

| Entiteit | Beschrijving |
|----------|-------------|
| **Relatie** | Basisklas voor personen/organisaties |
| **NatuurlijkPersoon** | Individu (huurder, bewoner) |
| **Rechtspersoon** | Organisatie (leverancier, aannemer) |
| **RelatieRol** | Verschijningsvorm (huurder, woningzoekende) |
| **Contactpersoon** | Contactgegevens |
| **Communicatiekanaal** | Telefoon, e-mail, etc. |

### 4.3 Domein Overeenkomsten

| Entiteit | Beschrijving |
|----------|-------------|
| **Overeenkomst** | Basisklasse contracten |
| **Huurovereenkomst** | Huurcontract |
| **Koopovereenkomst** | Verkoopcontract |
| **Serviceovereenkomst** | Serviceabonnement |
| **Betalingsregeling** | Afspraak schulden |
| **Huurmutatie** | Wijziging huurovereenkomst |

### 4.4 Domein Onderhoud

| Entiteit | Beschrijving |
|----------|-------------|
| **Onderhoudsorder** | Werkopdracht |
| **Onderhoudsverzoek** | Reparatieverzoek van huurder |
| **Werkorder** | Detailopdracht voor uitvoering |
| **Besteding** | Kosten (arbeid, materiaal) |
| **Inspectie** | Voorinspectie, eindinspectie |
| **Offerte** | Aanbieding leverancier |

### 4.5 Domein Financiën

| Entiteit | Beschrijving |
|----------|-------------|
| **Boeking** | Financiële transactie |
| **Factuur** | Uitgaande/inkomende factuur |
| **FactuurRegel** | Detailregel factuur |
| **Betaling** | Ontvangst/betaling |
| **Vordering** | Openstaand bedrag |
| **Grootboekrekening** | RGS-rekening |

### 4.6 Domein Woonruimteverdeling

| Entiteit | Beschrijving |
|----------|-------------|
| **Woningzoekende** | Geregistreerde kandidaat |
| **Inschrijving** | Registratie woningzoekende |
| **Publicatie** | Advertentie beschikbare woning |
| **Reactie** | Interesse woningzoekende |
| **Aanbieding** | Concreet aanbod aan kandidaat |
| **Toewijzing** | Definitieve toewijzing |

### 4.7 Domein Dossier

| Entiteit | Beschrijving |
|----------|-------------|
| **Dossier** | Verzameling documenten |
| **Informatieobject** | Document, foto, video |
| **Zaak** | Procesgang rond onderwerp |

### 4.8 Domein Energie

| Entiteit | Beschrijving |
|----------|-------------|
| **Energieprestatie** | Energielabel |
| **Energieverbruik** | Verbruiksgegevens |
| **Installatie** | CV, warmtepomp, etc. |

### 4.9 Domein Projectontwikkeling

| Entiteit | Beschrijving |
|----------|-------------|
| **Project** | Bouw/renovatieproject |
| **Projectfase** | Stadium van project |
| **Programma** | Verzameling projecten |

### 4.10 Domein Organisatie

| Entiteit | Beschrijving |
|----------|-------------|
| **Organisatie** | De woningcorporatie zelf |
| **Organisatieonderdeel** | Afdeling |
| **Medewerker** | Werknemer |
| **Functie** | Functieomschrijving |

---

## 5. Bedrijfsfuncties

Bedrijfsfuncties zijn onafhankelijk van de organisatie-inrichting. Ze zijn uniek, niet overlappend en kunnen op meerdere plaatsen voorkomen.

### 5.1 Sturende Bedrijfsfuncties

Deze functies geven richting aan de organisatie:

| Functie | Beschrijving |
|---------|-------------|
| **Besturing** | Richting geven door bepalen verandering en kaders |
| **Verantwoording** | Rapporteren naar belanghebbenden |
| **Vastgoed-Portefeuillesturing** | Bepalen gewenste vastgoedportefeuille |
| **Vastgoed-Assetmanagement** | Realiseren portefeuillebeleid |
| **Vastgoed-Onderhoudsplanning** | Plannen juiste onderhoudsmaatregelen |
| **Control** | Controleren en bijsturen uitvoering |
| **Financiële planning** | Balanceren financiële behoefte en middelen |
| **Risicomanagement** | Bepalen en bewaken risico's |
| **Bedrijfsstrategie** | Bepalen missie, visie en strategie |
| **Organisatieontwikkeling** | Implementeren veranderingen |

### 5.2 Primaire Bedrijfsfuncties

Deze functies leveren direct de producten en diensten:

| Functie | Beschrijving |
|---------|-------------|
| **Vastgoedonderhoud** | Technisch onderhoud vastgoed |
| **Coördinatie reparatieonderhoud** | Realisatie reparaties |
| **Coördinatie mutatieonderhoud** | Onderhoud bij mutatie |
| **Coördinatie planmatig onderhoud** | Gepland onderhoud |
| **Realisatie onderhoudsorder** | Uitvoeren werkzaamheden |
| **Vastgoedbemiddeling** | Vraag en aanbod vastgoed |
| **Matching** | Koppelen aanbod aan klantgroep |
| **Vastgoedverhuur** | Verhuur aan doelgroepen |
| **Contracteren** | Afsluiten overeenkomsten |
| **Vastgoedtransactie** | Aan- en verkoop |
| **Vastgoedexploitatie** | Administratie eenheden |
| **Debiteurenbeheer** | Beheer debiteurenadministratie |
| **Incasso** | Inlopen achterstanden |
| **Prolongatie** | Verlenging/indexering |
| **Sociaal beheer** | Signaleren sociale issues |
| **Omgevingsbeheer** | Kwaliteit leefomgeving |
| **VvE-beheer** | Beheer Vereniging van Eigenaren |
| **Energie coaching** | Kennisdeling energieprestaties |
| **Leefomgevingsbeheer** | Participatie met partners |

### 5.3 Ondersteunende Bedrijfsfuncties

| Functie | Beschrijving |
|---------|-------------|
| **HRM** | Personeelszaken |
| **Financiën secundair** | Financiële administratie |
| **ICT** | Informatievoorziening |
| **Facilitair** | Facilitaire diensten |
| **Juridisch** | Juridische ondersteuning |
| **Communicatie** | Interne/externe communicatie |
| **Inkoop** | Inkoopmanagement |

---

## 6. Processen

### 6.1 Proceshiërarchie

```
┌─────────────────────────────────────────────────────────────┐
│                     PROCESHIËRARCHIE                         │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│   Stuurscenario                                             │
│      │                                                       │
│      └─► Procesketen                                        │
│             │                                                │
│             └─► Bedrijfsproces                              │
│                    │                                         │
│                    └─► Werkproces                           │
│                           │                                  │
│                           └─► Processtap                    │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**Definities**:
- **Stuurscenario**: Keten van processen waar integraal op gestuurd wordt
- **Procesketen**: Samenwerking meerdere organisaties voor dienst aan klant
- **Bedrijfsproces**: Geordende reeks werkprocessen voor één dienst
- **Werkproces**: Reeks processtappen binnen één bedrijfsfunctie
- **Processtap**: Reeks handelingen door één uitvoerende rol

### 6.2 Sturende Processen

| Proces | Beschrijving |
|--------|-------------|
| Bepalen visie | Woonvisie, klantvisie, organisatievisie |
| Verantwoorden bedrijfsvoering | Jaarverslag, jaarrekening, dPi, dVi |
| Uitvoeren control | Rapportages, in-control statement |
| Opstellen portefeuilleplan | Ideaalportefeuille, transformatieopgave |
| Opstellen beleidsplannen | Facetbeleid per domein |
| Uitvoeren risicomanagement | Risk control framework |

### 6.3 Primaire Processen

#### Verhuurprocessen
| Proces | Beschrijving |
|--------|-------------|
| Verhuren eenheden | Complete verhuurketen |
| Werven kandidaten | Inschrijving woningzoekenden |
| Publiceren aanbod | Adverteren beschikbare eenheden |
| Matchen | Koppelen kandidaat aan eenheid |
| Contracteren | Afsluiten huurovereenkomst |
| Beëindigen overeenkomst | Huuropzegging afhandelen |

#### Onderhoudsprocessen
| Proces | Beschrijving |
|--------|-------------|
| Onderhouden eenheden | Complete onderhoudsketen |
| Afhandelen reparatieverzoek | Klantmelding verwerken |
| Uitvoeren mutatieonderhoud | Woning opknappen bij mutatie |
| Uitvoeren planmatig onderhoud | MJOB/JOB uitvoeren |
| Uitvoeren projectmatig onderhoud | Renovaties |

#### Financiële processen
| Proces | Beschrijving |
|--------|-------------|
| Incasseren | Innen huur en servicekosten |
| Prolongeren | Jaarlijkse huurverhoging |
| Innen achterstanden | Incassoprocedures |
| Factureren | Aanmaken facturen |

### 6.4 Ketenprocessen (voor API's)

| Code | Ketenproces | Beschrijving |
|------|-------------|-------------|
| BDO | Casemanagement | Dossierbeheer |
| BEP | Beheer Energieprestaties | Energielabels |
| BFG | Beheer Financiële gegevens | Financiën |
| BOG | Beheer Overeenkomstgegevens | Contracten |
| BRG | Beheer Relatiegegevens | Relaties |
| BVG | Beheer Vastgoedgegevens | Vastgoed |
| INC | Incasso | Incassoprocessen |
| KMT | Kwaliteitsmanagement | Kwaliteit/inspecties |
| OHD | Onderhouden Eenheden | Onderhoud |
| VHE | Verhuren Eenheden | Verhuur |
| WRV | Woonruimteverdeling | Woningtoewijzing |

---

## 7. Producten en Diensten

### 7.1 Producten voor Externe Klanten

| Product | Diensten |
|---------|----------|
| **Klantrelatie** | Contactinformatie, klanttevredenheidstoets, klachtafhandeling |
| **Beheerde VvE** | Administratief/financieel/technisch/bestuurlijk VvE beheer |
| **Primair financieel management** | Debiteurenbeheer, prolongatie, voorkomen achterstand |
| **Passende eenheid** | Inschrijving, urgentieverklaring, matching, toewijzing |
| **Gehuurde eenheid** | Huurcontract, servicekosten, mutatieonderhoud |
| **Gekochte eenheid** | Terugkoopgarantie, erfpacht |
| **Onderhouden eenheid** | Reparaties, planmatig onderhoud |
| **Leefbare woonomgeving** | Overlastmelding, buurtbeheer |

### 7.2 Interne Producten

| Product | Beschrijving |
|---------|-------------|
| Beheerde vastgoedportefeuille | Strategisch vastgoedbeheer |
| Ontwikkeld vastgoed | Nieuwbouw en renovatie |
| Getransformeerd vastgoed | Sloop, verkoop, aankoop |

---

## 8. Referentiedata

### 8.1 Wat is Referentiedata?

Referentiedata zijn vaste waarden die worden gebruikt om andere gegevens te beschrijven, classificeren of valideren. Het zijn standaard waardelijsten (enumeraties).

**Structuur per waarde**:
- **Code**: Drieletterige unieke code
- **Naam**: Unieke naam
- **Omschrijving**: Toelichting
- **Begindatum**: Introductiedatum
- **Einddatum**: Vervaldatum (optioneel)
- **Parent**: Gekoppelde referentiedata
- **Informatiedomein**: Domein waartoe het behoort

### 8.2 Belangrijkste Referentiedatasoorten

#### Vastgoed
| Soort | Beschrijving | Voorbeeldwaarden |
|-------|-------------|------------------|
| EENHEIDSOORT | Type eenheid | WOO (Woonruimte), BED (Bedrijfsruimte), PAR (Parkeergelegenheid) |
| EENHEIDSTATUS | Status eenheid | VER (Verhuurd), LEE (Leegstand), VER (Verkocht) |
| BOUWJAARKLASSE | Bouwperiode | VOO1945, 1945-1970, 1970-1990, etc. |
| WONINGTYPE | Type woning | EEN (Eengezinswoning), APP (Appartement), MAI (Maisonette) |

#### Overeenkomsten
| Soort | Beschrijving |
|-------|-------------|
| OVEREENKOMSTSOORT | Type contract |
| OVEREENKOMSTSTATUS | Status contract |
| OPZEGREDENSOORT | Reden beëindiging |
| BETALINGSWIJZE | Wijze van betaling |

#### Onderhoud
| Soort | Beschrijving |
|-------|-------------|
| ONDERHOUDSORDERSOORT | Type onderhoudsorder |
| ONDERHOUDSORDERSTATUS | Status order |
| AFREKENWIJZESOORT | Wijze afrekenen |
| PRIORITEIT | Urgentie |

#### Woonruimteverdeling
| Soort | Beschrijving |
|-------|-------------|
| AANBIEDINGSTATUS | Status aanbieding (Aangeboden, Geweigerd, Toegewezen) |
| DOELGROEP | Doelgroep woning |
| PASSENDHEIDSTOETSSOORT | Type passendheidstoets |
| URGENTIESOORT | Type urgentie |

#### Relaties
| Soort | Beschrijving |
|-------|-------------|
| RELATIESOORT | Type relatie |
| ADRESSOORT | Type adres |
| ACCOUNTSTATUS | Status account |
| AFSPRAAKSTATUS | Status afspraak |

### 8.3 Toegang tot Referentiedata

**CSV Download**:
```
https://github.com/Aedes-datastandaarden/vera-referentiedata
```

**API Endpoints**:
```
# Alle referentiedata
GET https://vera-service.azurewebsites.net/api/referentiedata

# Specifieke soort
GET https://vera-service.azurewebsites.net/api/referentiedata/{soort}

# Specifieke versie
GET https://vera-service.azurewebsites.net/api/referentiedata?version=v4.0.221125
```

---

## 9. OpenAPI Koppelvlakken

### 9.1 Twee typen API's

VERA levert twee soorten OpenAPI-specificaties:

#### A. Informatiedomein API's
- Eén API per informatiedomein
- Alle entiteiten als resources
- Voor master data management
- Cross-domein verwijzingen via URI

#### B. Ketenproces API's
- Eén API per ketenproces
- Alleen relevante entiteiten voor dat proces
- Inclusief proces-specifieke attributen
- Voor transactieverwerking

### 9.2 Beschikbare API's

#### Informatiedomein API's
| API | URL |
|-----|-----|
| Algemeen | `https://aedes-datastandaarden.github.io/vera-openapi/Informatiedomeinen/Algemeen.html` |
| Dossier | `https://aedes-datastandaarden.github.io/vera-openapi/Informatiedomeinen/Dossier.html` |
| Financien | `https://aedes-datastandaarden.github.io/vera-openapi/Informatiedomeinen/Financien.html` |
| Kwaliteit | `https://aedes-datastandaarden.github.io/vera-openapi/Informatiedomeinen/Kwaliteit.html` |
| Onderhoud | `https://aedes-datastandaarden.github.io/vera-openapi/Informatiedomeinen/Onderhoud.html` |
| Overeenkomsten | `https://aedes-datastandaarden.github.io/vera-openapi/Informatiedomeinen/Overeenkomsten.html` |
| Projectontwikkeling | `https://aedes-datastandaarden.github.io/vera-openapi/Informatiedomeinen/Projectontwikkeling.html` |
| Relaties | `https://aedes-datastandaarden.github.io/vera-openapi/Informatiedomeinen/Relaties.html` |
| Vastgoed | `https://aedes-datastandaarden.github.io/vera-openapi/Informatiedomeinen/Vastgoed.html` |
| Woonruimteverdeling | `https://aedes-datastandaarden.github.io/vera-openapi/Informatiedomeinen/Woonruimteverdeling.html` |

#### Ketenproces API's
| API | Ketenproces |
|-----|-------------|
| BDO | Casemanagement |
| BFG | Beheer Financiële gegevens |
| BOG | Beheer Overeenkomstgegevens |
| BRG | Beheer Relatiegegevens |
| BVG | Beheer Vastgoedgegevens |
| INC | Incasso |
| KMT | Kwaliteitsmanagement |
| OHD | Onderhouden Eenheden |
| VHE | Verhuren Eenheden |
| WRV | Woonruimteverdeling |

### 9.3 API Design Principes

1. **OpenAPI 3.0** voor overerving (oneOf/allOf)
2. **Afgeleide klassen** alleen via superentiteit
3. **Cross-domein verwijzingen** als URI-referentie
4. **Sleutelvelden** als aparte entiteitstypen (`-sleutels`)
5. **Metadata** in headers, niet in payload
6. **Paginatie** via cursor of pagina-parameter
7. **Semantic versioning 2.0.0**

### 9.4 Headers

| Header | Beschrijving |
|--------|-------------|
| `X-Stuurgegevens-ZenderOrganisatie` | Verzendende organisatie |
| `X-Stuurgegevens-TijdstipBericht` | Tijdstip bericht |
| `X-Stuurgegevens-Referentienummer` | Referentienummer |
| `X-Parameters-Pagina` | Paginanummer |
| `X-Parameters-AantalPerPagina` | Aantal per pagina |
| `X-Parameters-Cursor` | Cursor voor volgende pagina |

---

## 10. Technische Implementatie

### 10.1 Architectuurprincipes

1. **Eén authentieke bron** per gegeven
2. **Technologie-onafhankelijk** datamodel
3. **Event-driven** gegevensuitwisseling
4. **Domeinscheiding** via API's
5. **Referentiedata** voor standaardwaarden

### 10.2 Identificatie van Objecten

VERA kent verschillende ID-velden:

| Veld | Gebruik |
|------|---------|
| `id` | Primaire interne sleutel |
| `idExtern` | Sleutel extern systeem |
| `code` | Leesbare code |

### 10.3 Koppeling met Basisregistraties

| Basisregistratie | Entiteiten |
|------------------|-----------|
| **BAG** | Adres, Pand, Verblijfsobject, Nummeraanduiding |
| **BRK** | KadasterPerceel, Akte |
| **BRP** | NatuurlijkPersoon |
| **HR** | Rechtspersoon |
| **WOZ** | WozEenheid |
| **BRI** | Inkomen |

### 10.4 Externe Standaarden

| Standaard | Toepassing |
|-----------|-----------|
| **DICO** | Ketenintegratie met externe partijen |
| **SBR-wonen** | Financiële verantwoording (dPi, dVi) |
| **RGS** | Referentiegrootboekschema |
| **ISO** | Taal- en landcodes |

---

## 11. Bronnen en Links

### Officiële bronnen

| Bron | URL |
|------|-----|
| CORA/VERA Wiki | https://www.coraveraonline.nl |
| VERA GitHub | https://github.com/Aedes-datastandaarden |
| OpenAPI Specs | https://github.com/Aedes-datastandaarden/vera-openapi |
| Referentiedata | https://github.com/Aedes-datastandaarden/vera-referentiedata |
| Referentiedata API | https://vera-service.azurewebsites.net/swagger/index.html |

### Beheerorganisaties

| Organisatie | Verantwoordelijk voor |
|-------------|----------------------|
| **CorpoNet** | CORA (referentiearchitectuur) |
| **Aedes Datastandaarden** | VERA (datastandaard) |

### Contact

- CORA: info@corponet.nl
- VERA: vera@aedesdatastandaarden.nl

### Licenties

- CORA: Creative Commons Naamsvermelding 4.0
- VERA: Creative Commons Naamsvermelding 4.0
- Referentiedata: Creative Commons 0 (publiek domein)

---

## Appendix A: Lokale Bestanden

In deze workspace zijn de volgende VERA-bronnen beschikbaar:

```
/Volumes/Public/vera/
├── VERA-Domeinmodellen/     # ArchiMate modellen
│   └── model/
│       ├── application/     # Applicatielaag
│       ├── business/        # Businesslaag  
│       ├── diagrams/        # Diagrammen
│       └── relations/       # Relaties
│
├── vera-openapi/            # OpenAPI specificaties
│   ├── Informatiedomeinen/  # Per domein
│   │   ├── Vastgoed.yaml
│   │   ├── Relaties.yaml
│   │   ├── Onderhoud.yaml
│   │   └── ...
│   └── Ketenprocessen/      # Per proces
│       ├── OHD.yaml         # Onderhoud
│       ├── VHE.yaml         # Verhuur
│       └── ...
│
└── vera-referentiedata/     # Referentiedata
    └── Referentiedata.csv   # Alle waardelijsten
```

---

*Document gegenereerd op basis van CORA/VERA versie 4.3 - Januari 2026*
