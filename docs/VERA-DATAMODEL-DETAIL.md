# VERA Datamodel - Technisch Detail

> Dit document bevat gedetailleerde informatie over het VERA datamodel voor technische implementatie.

---

## 1. Domein Vastgoed - Entiteiten in Detail

### 1.1 Eenheid (Kernentiteit)

De **Eenheid** is het centrale object in het vastgoeddomein. Het is de kleinste verhuurbare of verkoopbare eenheid.

```
Eenheid
├── id: string (UUID)
├── idExtern: string
├── code: string
├── naam: string
├── soort: Referentiedata (EENHEIDSOORT)
│   ├── WOO - Woonruimte
│   ├── BED - Bedrijfsruimte
│   ├── PAR - Parkeergelegenheid
│   ├── BER - Berging
│   └── OVE - Overig
├── detailsoort: Referentiedata (EENHEIDDETAILSOORT)
├── status: Referentiedata (EENHEIDSTATUS)
├── oppervlakte: decimal (m²)
├── bouwjaar: integer
├── aantalKamers: integer
├── aantalSlaapkamers: integer
├── woonlaag: integer
├── toegankelijkheid: Referentiedata
├── doelgroep: Referentiedata (DOELGROEP)
├── begindatum: date
├── einddatum: date
│
├── adres: Adres (relatie)
├── pand: Pand (relatie)
├── cluster: Cluster[] (relaties)
├── collectiefObject: CollectiefObject[] (relaties)
├── woningwaardering: Woningwaardering (relatie)
├── energieprestatie: Energieprestatie (relatie)
└── prijscomponenten: Prijscomponent[] (relaties)
```

### 1.2 Adres

```
Adres
├── id: string
├── straatnaam: string
├── huisnummer: integer
├── huisletter: string
├── huisnummertoevoeging: string
├── postcode: string
├── woonplaatsnaam: string
├── gemeentenaam: string
├── land: Referentiedata (LAND)
│
├── bagNummeraanduidingIdentificatie: string
├── bagOpenbareruimteIdentificatie: string
│
├── gemeente: Gemeente (relatie)
├── wijk: Wijk (relatie)
├── buurt: Buurt (relatie)
└── geometrie: Geometrie (relatie)
```

**Specialisaties:**
- `BagAdres` - Met BAG-koppelingen
- `BuitenlandsAdres` - Voor buitenlandse adressen
- `Postadres` - Voor postadressen
- `Eenheidadres` - Adres gekoppeld aan eenheid

### 1.3 Pand

```
Pand
├── id: string
├── idExtern: string
├── bouwjaar: integer
├── bouwjaarklasse: Referentiedata
├── status: Referentiedata (PANDSTATUS)
├── oppervlakte: decimal
├── aantalBouwlagen: integer
├── hoogsteBouwlaag: integer
├── laagsteBouwlaag: integer
│
├── bagPandIdentificatie: string
│
├── adressen: Adres[] (relaties)
├── eenheden: Eenheid[] (relaties)
├── gebouw: Gebouw (relatie)
└── kadasterPercelen: KadasterPerceel[] (relaties)
```

### 1.4 Cluster

```
Cluster
├── id: string
├── code: string
├── naam: string
├── soort: Referentiedata (CLUSTERSOORT)
│   ├── OND - Onderhoudscluster
│   ├── VVE - VvE-cluster
│   ├── FIN - Financieel cluster
│   ├── HUV - Huurverhogingscluster
│   └── BEH - Beheercluster
├── omschrijving: string
├── begindatum: date
├── einddatum: date
│
└── eenheden: Eenheid[] (relaties)
```

### 1.5 Woningwaardering

```
Woningwaardering
├── id: string
├── stelsel: Referentiedata (WONINGWAARDERINGSTELSEL)
├── puntentotaal: decimal
├── maximaleHuurprijs: decimal
├── peildatum: date
├── begindatum: date
├── einddatum: date
│
├── eenheid: Eenheid (relatie)
├── groepen: WoningwaarderingGroep[] (relaties)
└── resultaat: WoningwaarderingResultaat (relatie)
```

### 1.6 Prijscomponent

```
Prijscomponent
├── id: string
├── soort: Referentiedata (PRIJSCOMPONENTSOORT)
│   ├── NTH - Netto huur
│   ├── SUB - Subsidiabele huur
│   ├── SVC - Servicekosten
│   ├── STK - Stookkosten
│   └── OVE - Overige kosten
├── bedrag: decimal
├── btw: decimal
├── btwPercentage: decimal
├── begindatum: date
├── einddatum: date
│
└── eenheid: Eenheid (relatie)
```

---

## 2. Domein Relaties - Entiteiten in Detail

### 2.1 Relatie (Basisklasse)

```
Relatie
├── id: string
├── idExtern: string
├── soort: Referentiedata (RELATIESOORT)
│   ├── NAT - Natuurlijk persoon
│   ├── REC - Rechtspersoon
│   └── GRO - Groep
├── naam: string
├── status: Referentiedata (RELATIESTATUS)
├── begindatum: date
├── einddatum: date
│
├── adressen: Adres[] (relaties)
├── communicatiekanalen: Communicatiekanaal[] (relaties)
└── rollen: RelatieRol[] (relaties)
```

### 2.2 NatuurlijkPersoon (Specialisatie van Relatie)

```
NatuurlijkPersoon extends Relatie
├── bsn: string (Burgerservicenummer)
├── geslacht: Referentiedata (GESLACHT)
├── voornamen: string
├── voorletters: string
├── voorvoegsel: string
├── achternaam: string
├── geboortedatum: date
├── geboorteplaats: string
├── geboorteland: Referentiedata (LAND)
├── overlijdensdatum: date
├── nationaliteit: Referentiedata (NATIONALITEIT)
├── burgerlijkeStaat: Referentiedata (BURGERLIJKESTAAT)
│
└── brpIdentificatie: string
```

### 2.3 Rechtspersoon (Specialisatie van Relatie)

```
Rechtspersoon extends Relatie
├── kvkNummer: string
├── rsin: string
├── rechtsvorm: Referentiedata (RECHTSVORM)
├── statutaireNaam: string
├── handelsnaam: string[]
│
└── hrIdentificatie: string
```

### 2.4 RelatieRol

```
RelatieRol
├── id: string
├── rol: Referentiedata (RELATIEROLSOORT)
│   ├── HUU - Huurder
│   ├── MED - Medehuurder
│   ├── BEW - Bewoner
│   ├── WOZ - Woningzoekende
│   ├── LEV - Leverancier
│   ├── AAN - Aannemer
│   └── VVE - VvE-lid
├── begindatum: date
├── einddatum: date
│
├── relatie: Relatie (relatie)
└── adres: Adres (relatie)
```

---

## 3. Domein Overeenkomsten - Entiteiten in Detail

### 3.1 Overeenkomst (Basisklasse)

```
Overeenkomst
├── id: string
├── idExtern: string
├── contractnummer: string
├── soort: Referentiedata (OVEREENKOMSTSOORT)
├── status: Referentiedata (OVEREENKOMSTSTATUS)
│   ├── ACT - Actief
│   ├── BEE - Beëindigd
│   ├── OPG - Opgezegd
│   └── ONT - Ontbonden
├── ondertekeningsdatum: date
├── ingangsdatum: date
├── einddatum: date
├── opzegdatum: date
│
├── relaties: Relatie[] (contractanten)
├── eenheid: Eenheid (relatie)
└── prijscomponenten: Prijscomponent[] (relaties)
```

### 3.2 Huurovereenkomst (Specialisatie)

```
Huurovereenkomst extends Overeenkomst
├── huursoort: Referentiedata (HUURSOORT)
│   ├── SOC - Sociale huur
│   ├── VRI - Vrije sector
│   └── GER - Gereguleerd
├── huurregime: Referentiedata (HUURREGIME)
├── huurprijsGeliberaliseerd: boolean
├── kaleHuurprijs: decimal
├── totaleHuurprijs: decimal
├── servicekosten: decimal
├── borgsom: decimal
├── huurtoeslag: boolean
│
├── medehuurders: Relatie[] (relaties)
└── mutaties: Huurmutatie[] (relaties)
```

### 3.3 Betalingsregeling

```
Betalingsregeling
├── id: string
├── status: Referentiedata (BETALINGSREGELINGSTATUS)
├── totaalbedrag: decimal
├── termijnbedrag: decimal
├── aantalTermijnen: integer
├── begindatum: date
├── einddatum: date
│
└── relatie: Relatie (relatie)
```

---

## 4. Domein Onderhoud - Entiteiten in Detail

### 4.1 Onderhoudsverzoek

```
Onderhoudsverzoek
├── id: string
├── volgnummer: string
├── soort: Referentiedata (ONDERHOUDSVERZOEKSOORT)
├── status: Referentiedata (ONDERHOUDSVERZOEKSTATUS)
│   ├── ONT - Ontvangen
│   ├── INB - In behandeling
│   ├── AFG - Afgerond
│   └── ANN - Geannuleerd
├── melddatum: date
├── omschrijving: string
├── prioriteit: Referentiedata (PRIORITEIT)
│
├── melder: Relatie (relatie)
├── eenheid: Eenheid (relatie)
└── onderhoudsorders: Onderhoudsorder[] (relaties)
```

### 4.2 Onderhoudsorder

```
Onderhoudsorder
├── id: string
├── volgnummer: string
├── soort: Referentiedata (ONDERHOUDSORDERSOORT)
│   ├── REP - Reparatie
│   ├── MUT - Mutatieonderhoud
│   ├── PLN - Planmatig onderhoud
│   └── PRO - Projectmatig onderhoud
├── status: Referentiedata (ONDERHOUDSORDERSTATUS)
├── omschrijving: string
├── prioriteit: Referentiedata (PRIORITEIT)
├── geplandeDatum: date
├── uitvoeringsDatum: date
├── afrekenwijze: Referentiedata (AFREKENWIJZESOORT)
│   ├── VPR - Vaste prijs
│   ├── NCR - Nacalculatie regie
│   ├── NCE - Nacalculatie eenheidsprijzen
│   └── GAR - Garantie
├── offertebedrag: decimal
├── werkelijkBedrag: decimal
│
├── eenheid: Eenheid (relatie)
├── leverancier: Relatie (relatie)
├── bestedingen: Besteding[] (relaties)
└── afspraak: Afspraak (relatie)
```

### 4.3 Besteding

```
Besteding
├── id: string
├── soort: Referentiedata (BESTEDINGSOORT)
│   ├── ARB - Arbeid
│   ├── MAT - Materiaal
│   ├── REI - Reistijd
│   └── OVE - Overig
├── omschrijving: string
├── aantal: decimal
├── eenheid: string
├── tarief: decimal
├── bedrag: decimal
│
└── onderhoudsorder: Onderhoudsorder (relatie)
```

---

## 5. Domein Financiën - Entiteiten in Detail

### 5.1 Boeking

```
Boeking
├── id: string
├── boekingsnummer: string
├── soort: Referentiedata (BOEKINGSOORT)
├── status: Referentiedata (AFLETTERSTATUS)
│   ├── NIE - Niet afgeletterd
│   ├── DEE - Deels afgeletterd
│   └── VOL - Volledig afgeletterd
├── boekdatum: date
├── vervaldatum: date
├── bedrag: decimal
├── btw: decimal
├── omschrijving: string
│
├── relatie: Relatie (relatie)
├── overeenkomst: Overeenkomst (relatie)
└── grootboekrekening: Grootboekrekening (relatie)
```

### 5.2 Factuur

```
Factuur
├── id: string
├── factuurnummer: string
├── soort: Referentiedata (FACTUURSOORT)
│   ├── VER - Verkoopfactuur
│   ├── INK - Inkoopfactuur
│   └── CRE - Creditnota
├── status: Referentiedata (FACTUURSTATUS)
├── factuurdatum: date
├── vervaldatum: date
├── subtotaal: decimal
├── btw: decimal
├── totaal: decimal
│
├── relatie: Relatie (relatie)
├── factuurregels: FactuurRegel[] (relaties)
└── betalingen: Betaling[] (relaties)
```

### 5.3 Betaling

```
Betaling
├── id: string
├── soort: Referentiedata (BETALINGSOORT)
│   ├── INC - Incasso
│   ├── OVE - Overboeking
│   ├── PIN - Pinbetaling
│   └── CON - Contant
├── betaaldatum: date
├── bedrag: decimal
├── valuta: Referentiedata (VALUTA)
├── referentie: string
│
├── relatie: Relatie (relatie)
└── boekingen: Boeking[] (relaties)
```

---

## 6. Domein Woonruimteverdeling - Entiteiten in Detail

### 6.1 Woningzoekende

```
Woningzoekende
├── id: string
├── inschrijfnummer: string
├── status: Referentiedata (WONINGZOEKENDESTATUS)
├── inschrijfdatum: date
├── zoekprofiel: Zoekprofiel (embedded)
│   ├── gewensteWoonplaatsen: string[]
│   ├── minimaleOppervlakte: decimal
│   ├── maximaleHuurprijs: decimal
│   ├── aantalKamers: integer
│   └── toegankelijkheid: Referentiedata
│
├── relatie: Relatie (relatie)
├── huishouden: Huishouden (relatie)
└── reacties: Reactie[] (relaties)
```

### 6.2 Publicatie

```
Publicatie
├── id: string
├── publicatienummer: string
├── status: Referentiedata (PUBLICATIESTATUS)
│   ├── CON - Concept
│   ├── ACT - Actief
│   ├── GES - Gesloten
│   └── ING - Ingetrokken
├── publicatiedatum: date
├── sluitingsdatum: date
├── toewijzingsmethode: Referentiedata (TOEWIJZINGSMETHODE)
├── doelgroep: Referentiedata (DOELGROEP)
│
├── eenheid: Eenheid (relatie)
└── reacties: Reactie[] (relaties)
```

### 6.3 Aanbieding

```
Aanbieding
├── id: string
├── status: Referentiedata (AANBIEDINGSTATUS)
│   ├── AAN - Aangeboden
│   ├── GEW - Geweigerd
│   ├── ING - Ingetrokken
│   └── TOE - Toegewezen
├── detailstatus: Referentiedata (AANBIEDINGDETAILSTATUS)
├── aanbiedingsdatum: date
├── reactiedatum: date
│
├── woningzoekende: Woningzoekende (relatie)
├── publicatie: Publicatie (relatie)
└── eenheid: Eenheid (relatie)
```

---

## 7. Relaties tussen Domeinen

### 7.1 Kernrelaties

```
┌─────────────────────────────────────────────────────────────────┐
│                     DOMEIN RELATIES                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   Relatie ────────────── Overeenkomst ──────────── Eenheid      │
│      │                        │                        │         │
│      │                        │                        │         │
│      ▼                        ▼                        ▼         │
│   RelatieRol              Boeking              BouwkundigElement│
│      │                        │                        │         │
│      │                        │                        │         │
│      ▼                        ▼                        ▼         │
│   Communicatie-          Factuur              Onderhoudsorder   │
│   kanaal                      │                        │         │
│                               │                        │         │
│                               ▼                        ▼         │
│                           Betaling              Besteding        │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 7.2 Cross-domein referenties

Bij API-implementatie worden cross-domein verwijzingen als URI opgenomen:

```json
{
  "id": "uuid-onderhoudsorder",
  "eenheid": {
    "href": "/vastgoed/eenheden/uuid-eenheid"
  },
  "leverancier": {
    "href": "/relaties/relaties/uuid-leverancier"
  }
}
```

---

## 8. Referentiedata Categorieën

### 8.1 Vastgoed Referentiedata

| Soort | Voorbeeldwaarden |
|-------|------------------|
| EENHEIDSOORT | WOO, BED, PAR, BER |
| EENHEIDDETAILSOORT | EGW, APP, MAI, SEN |
| EENHEIDSTATUS | VER, LEE, VRK, ONG |
| CLUSTERSOORT | OND, VVE, FIN, BEH |
| PANDSTATUS | INB, GBK, SLP |
| BOUWJAARKLASSE | VOO1945, 1945-1970, 1970-1990 |
| DOELGROEP | GEZ, SEN, STU, JON |
| TOEGANKELIJKHEID | NUL, LEV, ROL |

### 8.2 Relaties Referentiedata

| Soort | Voorbeeldwaarden |
|-------|------------------|
| RELATIESOORT | NAT, REC, GRO |
| RELATIEROLSOORT | HUU, MED, BEW, WOZ, LEV |
| GESLACHT | MAN, VRO, ONB |
| COMMUNICATIEKANAALSOORT | TEL, MOB, EMA |
| ADRESSOORT | EEN, POS, BUI |

### 8.3 Overeenkomsten Referentiedata

| Soort | Voorbeeldwaarden |
|-------|------------------|
| OVEREENKOMSTSOORT | HUU, KOO, SER |
| OVEREENKOMSTSTATUS | ACT, BEE, OPG |
| HUURSOORT | SOC, VRI, GER |
| OPZEGREDENSOORT | VER, OVE, OVL |
| BETALINGSWIJZE | AUT, OVE, CON |

### 8.4 Onderhoud Referentiedata

| Soort | Voorbeeldwaarden |
|-------|------------------|
| ONDERHOUDSORDERSOORT | REP, MUT, PLN, PRO |
| ONDERHOUDSORDERSTATUS | NIE, UIT, AFG |
| PRIORITEIT | SPO, URG, NOR, LAA |
| AFREKENWIJZESOORT | VPR, NCR, NCE, GAR |
| BESTEDINGSOORT | ARB, MAT, REI |

### 8.5 Woonruimteverdeling Referentiedata

| Soort | Voorbeeldwaarden |
|-------|------------------|
| AANBIEDINGSTATUS | AAN, GEW, ING, TOE |
| AANBIEDINGDETAILSTATUS | AND, ANV, BUI, DOC |
| PUBLICATIESTATUS | CON, ACT, GES, ING |
| TOEWIJZINGSMETHODE | LOT, INS, DIR |
| URGENTIESOORT | MED, SOC, STA |

---

## 9. API Voorbeelden

### 9.1 GET Eenheid

```yaml
GET /vastgoed/eenheden/{id}

Response:
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "code": "WON-001234",
  "naam": "Lindenlaan 32",
  "soort": {
    "code": "WOO",
    "naam": "Woonruimte"
  },
  "status": {
    "code": "VER",
    "naam": "Verhuurd"
  },
  "oppervlakte": 85.5,
  "bouwjaar": 1985,
  "aantalKamers": 4,
  "adres": {
    "straatnaam": "Lindenlaan",
    "huisnummer": 32,
    "postcode": "1234AB",
    "woonplaatsnaam": "Utrecht"
  },
  "woningwaardering": {
    "puntentotaal": 142,
    "maximaleHuurprijs": 879.66
  },
  "prijscomponenten": [
    {
      "soort": {"code": "NTH", "naam": "Netto huur"},
      "bedrag": 650.00
    },
    {
      "soort": {"code": "SVC", "naam": "Servicekosten"},
      "bedrag": 45.00
    }
  ]
}
```

### 9.2 POST Onderhoudsverzoek

```yaml
POST /onderhoud/onderhoudsverzoeken

Request:
{
  "soort": {"code": "REP"},
  "omschrijving": "Lekkage onder wastafel badkamer",
  "prioriteit": {"code": "URG"},
  "melder": {
    "href": "/relaties/relaties/uuid-melder"
  },
  "eenheid": {
    "href": "/vastgoed/eenheden/uuid-eenheid"
  }
}

Response:
{
  "id": "550e8400-e29b-41d4-a716-446655440001",
  "volgnummer": "RV-2026-00123",
  "status": {"code": "ONT", "naam": "Ontvangen"},
  "melddatum": "2026-01-20"
}
```

---

*Document gegenereerd op basis van VERA 4.3 - Januari 2026*
