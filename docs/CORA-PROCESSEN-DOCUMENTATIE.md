# CORA Processen Documentatie voor ERP-Platform

> Dit document bevat de gedetailleerde procesinformatie uit de CORA-standaard (COrporatie Referentie Architectuur) die nodig is voor het bouwen van een ERP-platform voor woningcorporaties.
>
> **Bron**: https://www.coraveraonline.nl
>
> **Versie**: CORA 4.3.2 (januari 2026)

---

## Inhoudsopgave

1. [Proceshiërarchie](#1-proceshiërarchie)
2. [Stuurscenario's](#2-stuurscenarios)
3. [Procesketens](#3-procesketens)
4. [Procesketen: Verhuren Eenheid](#4-procesketen-verhuren-eenheid)
5. [Procesketen: Reparatieonderhoud](#5-procesketen-reparatieonderhoud)
6. [Procesketen: Incasso](#6-procesketen-incasso)
7. [Procesketen: Verhuurmutatie](#7-procesketen-verhuurmutatie)
8. [Bedrijfsprocessen Overzicht](#8-bedrijfsprocessen-overzicht)
9. [Workflow Implementatie](#9-workflow-implementatie)

---

## 1. Proceshiërarchie

CORA hanteert een hiërarchische processtructuur:

```
┌─────────────────────────────────────────────────────────────────┐
│                     PROCESHIËRARCHIE                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   STUURSCENARIO                                                 │
│   └── Keten van processen waar integraal op gestuurd wordt     │
│       │                                                          │
│       ▼                                                          │
│   PROCESKETEN                                                    │
│   └── Samenwerking meerdere organisatieonderdelen               │
│       voor levering samenhangende diensten                      │
│       │                                                          │
│       ▼                                                          │
│   BEDRIJFSPROCES                                                 │
│   └── Geordende reeks werkprocessen                             │
│       voor levering van één dienst                              │
│       │                                                          │
│       ▼                                                          │
│   WERKPROCES                                                     │
│   └── Reeks processtappen binnen één bedrijfsfunctie           │
│       │                                                          │
│       ▼                                                          │
│   PROCESSTAP                                                     │
│   └── Reeks handelingen door één uitvoerende rol                │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Kenmerken per niveau

| Niveau | Verantwoordelijkheid | Resultaat | Voorbeeld |
|--------|---------------------|-----------|-----------|
| **Stuurscenario** | Directie/Management | Strategische sturing | Verhuurmutatie |
| **Procesketen** | Meerdere afdelingen | Samenhangende diensten | Verhuren eenheid |
| **Bedrijfsproces** | Eén afdeling | Eén dienst | Opstellen kandidatenlijst |
| **Werkproces** | Eén bedrijfsfunctie | Deelresultaat | Publiceren eenheid |
| **Processtap** | Eén medewerker | Handeling | Verzamelen publicatiegegevens |

---

## 2. Stuurscenario's

Stuurscenario's zijn verzamelingen processen waarbij de woningcorporatie stuurt op de samenhang bij het leveren van diensten.

### Beschikbare Stuurscenario's

| Stuurscenario | Beschrijving | Status |
|---------------|-------------|--------|
| **Verhuurmutatie** | Van huuropzegging tot opnieuw verhuren inclusief matching | Referentie |
| **Planmatig onderhoud incl. (M)JOB** | Van plannen onderhoud tot afronding planmatig onderhoudsproject | Referentie |
| **Inkopen, leveren en verrekenen servicecomponenten** | Volledige keten servicecomponenten | Referentie |
| **Vastgoedsturing alle fases** | Van visie tot projectinvesteringsplan | Prototype |
| **Voeren Assetsadministratie** | In/uit exploitatie nemen vastgoed | Prototype |
| **Wederverkoop eenheden** | Terugkoop tot wederverkoop | Prototype |
| **Wederverkoop appartementsrechten** | Terugkoop tot wederverkoop appartementsrecht | Prototype |

---

## 3. Procesketens

### Overzicht Procesketens

| Procesketen | Beschrijving | Status |
|-------------|-------------|--------|
| **Verhuren eenheid** | Inschrijven, matchen en verhuren t/m sleuteloverdracht | Referentie |
| **Reparatieonderhoud** | Intake, beoordeling en afhandeling reparatieverzoek | Referentie |
| **Incasso** | Bewaking betalingsachterstanden tot ontruiming | Referentie |
| **Verhuurklaar maken huureenheid** | Eenheid verhuurklaar maken incl. mutatieonderhoud | Referentie |
| **Eerstelijns afhandeling klantvraag** | Intake, routering en beantwoording klantvraag | Referentie |
| **Leveren en verrekenen servicecomponenten** | Inkopen t/m verrekenen servicecomponenten | Referentie |
| **Bepalen en inkopen servicecomponenten** | Inkopen incl. managen inkoopcontract | Referentie |
| **(Her)ontwikkelen en opleveren vastgoed** | Realisatie vastgoed t/m overdracht beheerder | Prototype |
| **Periodieke huurverhoging** | Bepalen en effectueren huurverhoging | Prototype |
| **Verwerken verzoek huuropzegging** | Huuropzegging t/m opnieuw verhuren | Prototype |
| **Wijzigen tenaamstelling** | Aanvraag en verwerking tenaamstelling | Prototype |
| **Splitsen en verkopen vastgoed** | Splitsen t/m verkopen appartementsrechten | Prototype |
| **Verkopen verkoopbare eenheid** | Verkoop aan reagerende kandidaat | Prototype |
| **Asbestsanering** | Asbest controle en sanering | Prototype |
| **Jaarcyclus ALV VvE** | Voorbereiding, deelname en leiden ALV | Prototype |
| **Vernieuwen energielabels** | Inspectie t/m registreren energielabel | Prototype |

---

## 4. Procesketen: Verhuren Eenheid

### 4.1 Overzicht

De procesketen "Verhuren eenheid" beschrijft het complete verhuurproces van publicatie tot sleuteloverdracht.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    PROCESKETEN: VERHUREN EENHEID                         │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  TRIGGER: Eenheid beschikbaar voor publicatie                           │
│      │                                                                   │
│      ▼                                                                   │
│  ┌────────────────────────────────────────────────────────────────┐     │
│  │ BEDRIJFSPROCES: Werven en selecteren kandidaten                │     │
│  │ Bedrijfsfunctie: Vastgoedbemiddeling / Matching                │     │
│  │                                                                 │     │
│  │  ┌─────────────────────┐   ┌─────────────────────┐             │     │
│  │  │ WERKPROCES:         │   │ WERKPROCES:         │             │     │
│  │  │ Publiceren eenheid  │──►│ Opstellen           │             │     │
│  │  │                     │   │ kandidatenlijst     │             │     │
│  │  └─────────────────────┘   └─────────────────────┘             │     │
│  │           │                         │                           │     │
│  │           ▼                         ▼                           │     │
│  │  ┌─────────────────────┐   ┌─────────────────────┐             │     │
│  │  │ WERKPROCES:         │   │                     │             │     │
│  │  │ Bepalen kandidaat   │◄──┤                     │             │     │
│  │  │                     │   │                     │             │     │
│  │  └─────────────────────┘   └─────────────────────┘             │     │
│  └────────────────────────────────────────────────────────────────┘     │
│      │                                                                   │
│      ▼ Kandidaat voorlopig geaccepteerd                                 │
│  ┌────────────────────────────────────────────────────────────────┐     │
│  │ BEDRIJFSPROCES: Verhuren eenheden                              │     │
│  │ Bedrijfsfunctie: Vastgoedverhuur / Contracteren                │     │
│  │                                                                 │     │
│  │  ┌─────────────────────┐   ┌─────────────────────┐             │     │
│  │  │ WERKPROCES:         │   │ WERKPROCES:         │             │     │
│  │  │ Verhuren eenheid    │──►│ Opleveren eenheid   │             │     │
│  │  │                     │   │                     │             │     │
│  │  └─────────────────────┘   └─────────────────────┘             │     │
│  └────────────────────────────────────────────────────────────────┘     │
│      │                                                                   │
│      ▼                                                                   │
│  RESULTAAT: Eenheid opgeleverd aan nieuwe huurder                       │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

### 4.2 Werkproces: Publiceren eenheid

**Doel**: Een te verhuren eenheid publiceren zodat woningzoekenden kunnen reageren.

| # | Processtap | Input | Output |
|---|------------|-------|--------|
| 1 | **Verzamelen en controleren publicatiegegevens** | Vervolgstap voor eenheid + Vastgestelde aanbiedhuur | Gecontroleerde publicatiegegevens |
| 2 | **Publiceren / adverteren** | Gecontroleerde publicatiegegevens | Gepubliceerde woningadvertentie |
| 3 | **Aanvullen advertentiegegevens** | Eindinspectierapport, mutatieonderhoud, aanbiedhuur | Aangevulde woningadvertentie |

### 4.3 Werkproces: Opstellen kandidatenlijst

**Doel**: Voor een te verhuren eenheid een lijst met passende kandidaten opstellen.

| # | Processtap | Input | Output |
|---|------------|-------|--------|
| 1 | **Notificeren passend aanbod** | Gepubliceerde eenheid + Zoekprofielen | Genotificeerde woningzoekende |
| 2 | **Reageren** | Woningadvertentie | Reactie woningzoekende |
| 3 | **Verwerken reacties** | Reacties | Voorlopige kandidatenlijst |
| 4 | **Controleren passendheid** | Voorlopige kandidatenlijst | Gecontroleerde gegevens |
| 5 | **Afwijzen kandidaat** | Gecontroleerde gegevens | Afgewezen kandidaat |
| 6 | **Definitief maken kandidatenlijst** | Gecontroleerde gegevens | Definitieve kandidatenlijst |

### 4.4 Werkproces: Bepalen kandidaat

**Doel**: Een definitieve gegadigde bepalen die de eenheid gaat huren.

| # | Processtap | Input | Output |
|---|------------|-------|--------|
| 1 | **Aanbieden eenheid** | Definitieve kandidatenlijst | Bezichtigingsplanning |
| 2 | **Uitvoeren (groeps)bezichtiging** | Bezichtigingsplanning | Bezichtigde eenheid |
| 3 | **Verwerken acceptatie/weigering** | Reactie kandidaat | Verwerkte reactie |
| 4 | **Screenen kandidaat / Intake** | Positieve reactie | Gecontroleerde gegevens |
| 5 | **Voorlopige acceptatie** | Gescreende kandidaat | Voorlopig geaccepteerde kandidaat |

### 4.5 Werkproces: Verhuren eenheid

**Doel**: Voor een leeggekomen eenheid een nieuw verhuurcontract afsluiten.

| # | Processtap | Input | Output |
|---|------------|-------|--------|
| 1 | **Voorbereiden verhuurdossier** | Voorlopig geaccepteerde kandidaat | Verhuurdossier |
| 2 | **Aanmaken klant** | Voorlopig geaccepteerde kandidaat | Aangemaakte klant |
| 3 | **Opstellen huurovereenkomst** | Aangemaakte klant | Aangemaakte huurovereenkomst |
| 4 | **Ondertekenen huurovereenkomst** | Huurovereenkomst | Ondertekende huurovereenkomst |

### 4.6 Werkproces: Opleveren eenheid

**Doel**: Een eenheid met nieuwe huurovereenkomst opleveren aan de huurder.

| # | Processtap | Input | Output |
|---|------------|-------|--------|
| 1 | **Controleren ontvangst eerste huurbetaling** | Ondertekende huurovereenkomst | Betaling eerste verhuurnota |
| 2 | **Ondertekenen staat van aanvaarding** | Ondertekende huurovereenkomst | Ondertekende staat van aanvaarding |
| 3 | **Overhandigen sleutels** | Ondertekende huurovereenkomst | Overhandigde sleutels |
| 4 | **Nazorg nieuwe huurder** | Oplevering | Geleverde nazorg |

### 4.7 Gebeurtenissen (Events)

| Event | Trigger | Beschrijving |
|-------|---------|-------------|
| `EenheidVoorPublicatieBeschikbaarGesteld` | Start | Eenheid klaar voor publicatie |
| `EenheidGepubliceerd` | Na publiceren | Advertentie live |
| `KandidatenlijstDefinitiefGemaakt` | Na matching | Kandidatenlijst gereed |
| `KandidaatVoorlopigGeaccepteerd` | Na acceptatie | Gegadigde bekend |
| `HuurovereenkomstOndertekend` | Na ondertekening | Contract getekend |
| `EenheidOpgeleverd` | Einde | Sleutels overhandigd |

---

## 5. Procesketen: Reparatieonderhoud

### 5.1 Overzicht

De procesketen "Reparatieonderhoud" beschrijft de intake, beoordeling en afhandeling van reparatieverzoeken.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    PROCESKETEN: REPARATIEONDERHOUD                       │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  TRIGGER: Reparatieverzoek klant ontvangen                              │
│      │                                                                   │
│      ▼                                                                   │
│  ┌────────────────────────────────────────────────────────────────┐     │
│  │ BEDRIJFSPROCES: Afhandelen klantvragen                         │     │
│  │ Bedrijfsfunctie: Klantbeheer / Contactmanagement               │     │
│  │                                                                 │     │
│  │  ┌─────────────────────┐                                       │     │
│  │  │ WERKPROCES:         │                                       │     │
│  │  │ Intake klantvraag   │                                       │     │
│  │  └─────────────────────┘                                       │     │
│  └────────────────────────────────────────────────────────────────┘     │
│      │                                                                   │
│      ▼ Melding klant afgehandeld (doorgerouteerd)                       │
│  ┌────────────────────────────────────────────────────────────────┐     │
│  │ BEDRIJFSPROCES: Coördineren reparatieonderhoud                 │     │
│  │ Bedrijfsfunctie: Coördinatie reparatieonderhoud                │     │
│  │                                                                 │     │
│  │  ┌─────────────────────┐                                       │     │
│  │  │ WERKPROCES:         │                                       │     │
│  │  │ Regisseren          │                                       │     │
│  │  │ reparatieonderhoud  │                                       │     │
│  │  └─────────────────────┘                                       │     │
│  └────────────────────────────────────────────────────────────────┘     │
│      │                                                                   │
│      ▼ Opdracht onderhoudsorder verstrekt                               │
│  ┌────────────────────────────────────────────────────────────────┐     │
│  │ BEDRIJFSPROCES: Uitvoeren onderhoudsorder                      │     │
│  │ Bedrijfsfunctie: Realisatie onderhoudsorder                    │     │
│  │                                                                 │     │
│  │  ┌─────────────────────┐                                       │     │
│  │  │ WERKPROCES:         │                                       │     │
│  │  │ Realiseren          │                                       │     │
│  │  │ onderhoudsorder     │                                       │     │
│  │  └─────────────────────┘                                       │     │
│  └────────────────────────────────────────────────────────────────┘     │
│      │                                                                   │
│      ▼ Onderhoudsorder technisch gereed                                 │
│  ┌────────────────────────────────────────────────────────────────┐     │
│  │ BEDRIJFSPROCES: Voeren crediteurenbeheer                       │     │
│  │ + Managen klantwaardering                                      │     │
│  └────────────────────────────────────────────────────────────────┘     │
│      │                                                                   │
│      ▼                                                                   │
│  RESULTAAT: Reparatie afgerond + Factuur verwerkt + KTO uitgevoerd     │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

### 5.2 Werkproces: Intake klantvraag

| # | Processtap | Input | Output |
|---|------------|-------|--------|
| 1 | **Uitvoeren intake klantvraag en stellen diagnose** | Klantvraag | Gestelde diagnose |
| 2 | **Bepalen en plannen opvolging** | Gestelde diagnose | Geplande opvolging |
| 3 | **Bewaking opvolging klantvraag** | Geplande opvolging | Bewaakte opvolging |

### 5.3 Werkproces: Regisseren reparatieonderhoud

| # | Processtap | Input | Output |
|---|------------|-------|--------|
| 1 | **Bepalen uitvoerende** | Onderhoudsmelding | Vastgestelde uitvoerende |
| 2 | **Afstemmen met verzekering** | (indien van toepassing) | Afgestemde aanpak |
| 3 | **Beoordelen inspectie nodig** | Melding | Besluit inspectie |
| 4 | **Opdracht verstrekken voor inspectie** | (optioneel) | Inspectie opdracht |
| 5 | **Beoordelen inspectierapport** | Inspectierapport | Beoordeelde resultaten |
| 6 | **Opvragen offerte(s)** | Melding | Offertes |
| 7 | **Beoordelen offerte(s)** | Offertes | Beoordeelde offerte |
| 8 | **Aanmaken en verstrekken opdracht** | Diagnose/offerte | Verstrekte opdracht |
| 9 | **Bepalen planning uitvoerende** | Verstrekte opdracht | Geplande uitvoering |
| 10 | **Bepalen te verrekenen kosten** | Verstrekte opdracht | Te verrekenen kosten |

### 5.4 Werkproces: Realiseren onderhoudsorder

| # | Processtap | Input | Output |
|---|------------|-------|--------|
| 1 | **Uitsplitsen onderhoudstaken** | Opdracht | Uitgesplitste taken |
| 2 | **Controleren voorraad c.q. bestellen materialen** | Opdracht | Besteld materiaal |
| 3 | **Intern inplannen taken** | Taken | Ingeplande taken |
| 4 | **Ontvangen materialen** | Besteld materiaal | Ontvangen materiaal |
| 5 | **Realiseren taken** | Ingeplande taken | Uitgevoerde taken |
| 6 | **Registreren bestedingen** | Uitgevoerde taken | Geregistreerde bestedingen |
| 7 | **Bewaken uitvoering onderhoudsorder** | Onderhoudsorder | Bewaakte uitvoering |
| 8 | **Beoordelen meerwerk/vervolgtaken** | Uitgevoerde taken | Beoordeeld meerwerk |
| 9 | **Technisch gereedmelden onderhoudsorder** | Gerealiseerde order | Technisch gereed gemelde order |

### 5.5 Werkproces: Inspecteren element

| # | Processtap | Input | Output |
|---|------------|-------|--------|
| 1 | **Voorbereiden inspectie** | Inspectieopdracht | Voorbereide inspectie |
| 2 | **Uitvoeren inspectie element** | Voorbereide inspectie | Inspectierapport |
| 3 | **Verwerken inspectieresultaten** | Inspectierapport | Bijgewerkte melding |

### 5.6 Financiële afhandeling

**Werkproces: Opstellen en verzenden factuur** (naar huurder indien doorbelasting)

| # | Processtap | Input | Output |
|---|------------|-------|--------|
| 1 | **Boeken in rekening te brengen kosten** | Te verrekenen kosten | Boeking |
| 2 | **Opstellen factuur** | Boeking | Opgestelde factuur |
| 3 | **Versturen factuur** | Opgestelde factuur | Verstuurde factuur |

**Werkproces: Verwerken factuur** (van leverancier)

| # | Processtap | Input | Output |
|---|------------|-------|--------|
| 1 | **Inboeken factuur** | Ontvangen factuur | Ingeboekte factuur |
| 2 | **Uitsplitsen factuur** | Ingeboekte factuur | Verwerkte factuur |
| 3 | **Controleren factuur** | Ingeboekte factuur | Gecontroleerde factuur |
| 4 | **Betaalbaar stellen factuur** | Gecontroleerde factuur | Betaalbaar gestelde factuur |

---

## 6. Procesketen: Incasso

### 6.1 Overzicht

De procesketen "Incasso" beschrijft de bewaking van betalingsachterstanden tot eventuele ontruiming.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    PROCESKETEN: INCASSO                                  │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  TRIGGER: Pre-notificatiemoment bereikt / Achterstand geconstateerd    │
│      │                                                                   │
│      ▼                                                                   │
│  ┌────────────────────────────────────────────────────────────────┐     │
│  │ WERKPROCES: Pre-notificeren contractant                        │     │
│  │                                                                 │     │
│  │  • Toepassen klantsegmentatie                                  │     │
│  │  • Pre-notificeren (betaalverzoek)                             │     │
│  └────────────────────────────────────────────────────────────────┘     │
│      │                                                                   │
│      ▼ Geen betaling ontvangen                                          │
│  ┌────────────────────────────────────────────────────────────────┐     │
│  │ WERKPROCES: Incasseren vordering, minnelijke fase 1            │     │
│  │                                                                 │     │
│  │  • Herinneren/aanmanen (brief, SMS, email, etc.)               │     │
│  │  • Uitvoeren belronde                                          │     │
│  │  • Uitvoeren huisbezoek                                        │     │
│  │  • Opstellen en versturen WIK brief                            │     │
│  │  • Overdragen deurwaarder                                      │     │
│  └────────────────────────────────────────────────────────────────┘     │
│      │                                                                   │
│      ▼ Verzoek betalingsregeling of Overdracht deurwaarder              │
│  ┌────────────────────────────────────────────────────────────────┐     │
│  │ WERKPROCES: Afsluiten betalingsregeling                        │     │
│  │                                                                 │     │
│  │  • Afspraak met contractant                                    │     │
│  │  • Overeenkomen betalingsregeling                              │     │
│  │  • Registreren betalingsregeling                               │     │
│  │  • Bevestigen betalingsregeling                                │     │
│  └────────────────────────────────────────────────────────────────┘     │
│      │                                                                   │
│      ▼ Bijzondere situatie                                              │
│  ┌────────────────────────────────────────────────────────────────┐     │
│  │ WERKPROCES: Bewaken uitzonderingen incasso                     │     │
│  │                                                                 │     │
│  │  • Schuldhulpverlening                                         │     │
│  │  • WSNP (Wet schuldsanering)                                   │     │
│  │  • GKB (Gemeentelijke Kredietbank)                             │     │
│  └────────────────────────────────────────────────────────────────┘     │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

### 6.2 Fasen Incassoproces

| Fase | Beschrijving | Acties |
|------|-------------|--------|
| **Pre-minnelijk** | Voorsorteren op minnelijke fase | Klantsegmentatie, Pre-notificatie |
| **Minnelijk fase 1** | Corporatie handelt zelf af | Aanmaningen, Belrondes, Huisbezoeken, WIK-brief |
| **Minnelijk fase 2** | Deurwaarder handelt af | Dagvaarding, Executie |
| **Gerechtelijk** | Rechter komt eraan te pas | Vonnis, Ontruiming |

### 6.3 Processtappen Pre-notificeren

| # | Processtap | Input | Output |
|---|------------|-------|--------|
| 1 | **Toepassen klantsegmentatie** | Contractanten | Gesegmenteerde contractanten |
| 2 | **Pre-notificeren** | Gesegmenteerde contractant | Geprenotificeerde contractant |

### 6.4 Processtappen Minnelijke fase 1

| # | Processtap | Input | Output |
|---|------------|-------|--------|
| 1 | **Herinneren/aanmanen** | Contractant met achterstand | Verstuurde herinnering |
| 2 | **Uitvoeren belronde** | Bestand te bellen contractanten | Gevoerd telefoongesprek |
| 3 | **Uitvoeren huisbezoek** | Bestand huisbezoeken | Gevoerd huisbezoek |
| 4 | **Opstellen en versturen WIK brief** | Contractant met achterstand | Verstuurde WIK brief |
| 5 | **Overdragen deurwaarder** | Over te dragen dossier | Overgedragen dossier |

### 6.5 Communicatiekanalen Incasso

| Kanaal | Toepassing |
|--------|-----------|
| Brief | Aanmaningen, WIK-brief |
| E-mail | Pre-notificatie, herinneringen |
| SMS | Betaalverzoek |
| WhatsApp | Betaalverzoek |
| Tikkie | Directe betaallink |
| Telefoon | Belrondes |
| Huurdersportaal | Status, betaallinks |

---

## 7. Procesketen: Verhuurmutatie

### 7.1 Overzicht (Stuurscenario)

Het stuurscenario "Verhuurmutatie" combineert meerdere procesketens:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    STUURSCENARIO: VERHUURMUTATIE                         │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  TRIGGER: Huuropzegging ontvangen                                       │
│      │                                                                   │
│      ▼                                                                   │
│  ┌────────────────────────────────────────────────────────────────┐     │
│  │ PROCESKETEN: Verwerken verzoek huuropzegging                   │     │
│  │                                                                 │     │
│  │  • Registreren huuropzegging                                   │     │
│  │  • Plannen voorinspectie                                       │     │
│  │  • Uitvoeren voorinspectie                                     │     │
│  │  • Bepalen aanpak mutatieonderhoud                             │     │
│  └────────────────────────────────────────────────────────────────┘     │
│      │                                                                   │
│      ├──────────────────────────────────────────────┐                   │
│      ▼                                              ▼                   │
│  ┌─────────────────────────┐   ┌─────────────────────────────────┐     │
│  │ PROCESKETEN:            │   │ PROCESKETEN:                    │     │
│  │ Verhuurklaar maken      │   │ Verhuren eenheid                │     │
│  │ huureenheid             │   │                                  │     │
│  │                         │   │ (parallel: publiceren            │     │
│  │ • Plannen mutatieond.   │   │  tijdens mutatieonderhoud)      │     │
│  │ • Uitvoeren mutatieond. │   │                                  │     │
│  │ • Eindinspectie         │   │                                  │     │
│  └─────────────────────────┘   └─────────────────────────────────┘     │
│      │                              │                                   │
│      └──────────────┬───────────────┘                                   │
│                     ▼                                                    │
│  RESULTAAT: Eenheid opgeleverd aan nieuwe huurder                       │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

### 7.2 Kritieke doorlooptijden

| Fase | Streeftijd | KPI |
|------|-----------|-----|
| Huuropzegging → Voorinspectie | 5 werkdagen | % binnen norm |
| Voorinspectie → Eindinspectie | Afhankelijk van scope | Gemiddelde dagen |
| Sleutelinname → Sleuteluitgifte | 10-15 werkdagen | Mutatiegraad |
| Totale mutatietijd | < 20 werkdagen | Leegstandsdagen |

---

## 8. Bedrijfsprocessen Overzicht

### 8.1 Sturende bedrijfsprocessen

| Bedrijfsproces | Werkprocessen |
|----------------|---------------|
| **Bepalen visie** | Opstellen woonvisie, klantvisie, organisatievisie |
| **Verantwoorden bedrijfsvoering** | Opstellen jaarverslag, jaarrekening, dPi, dVi |
| **Uitvoeren control** | Opstellen maand/kwartaal/jaarrapportage |
| **Opstellen portefeuilleplan** | Uitvoeren marktscan, bepalen transformatieopgave |
| **Opstellen MJOB/MJIB** | Vaststellen meerjarenbegrotingen |
| **Uitvoeren risicomanagement** | Beheren risk control framework |

### 8.2 Primaire bedrijfsprocessen

| Bedrijfsproces | Werkprocessen |
|----------------|---------------|
| **Werven en selecteren kandidaten** | Publiceren, matching, bepalen kandidaat |
| **Verhuren eenheden** | Verhuren eenheid, opleveren eenheid |
| **Afhandelen klantvragen** | Intake klantvraag, bewaking opvolging |
| **Coördineren reparatieonderhoud** | Regisseren reparatieonderhoud |
| **Uitvoeren onderhoudsorder** | Realiseren onderhoudsorder |
| **Incasseren vorderingen** | Pre-minnelijk, minnelijk fase 1/2 |
| **Managen klantwaardering** | Uitvoeren KTO |
| **Voeren debiteurenbeheer** | Opstellen en verzenden factuur |
| **Voeren crediteurenbeheer** | Verwerken factuur |

---

## 9. Workflow Implementatie

### 9.1 Workflow Status Model

Voor ERP-implementatie adviseer ik het volgende statusmodel per entiteit:

**Onderhoudsorder statussen**:
```
NIEUW → TOEGEWEZEN → INGEPLAND → IN_UITVOERING → 
TECHNISCH_GEREED → FINANCIEEL_GEREED → AFGESLOTEN

Alternatieve paden:
- NIEUW → GEANNULEERD
- IN_UITVOERING → WACHT_OP_MATERIAAL → IN_UITVOERING
- IN_UITVOERING → MEERWERK_VEREIST → GOEDGEKEURD → IN_UITVOERING
```

**Huurovereenkomst statussen**:
```
CONCEPT → VERSTUURD → ONDERTEKEND → ACTIEF → OPGEZEGD → BEEINDIGD

Alternatieve paden:
- CONCEPT → GEANNULEERD
- VERSTUURD → GEWEIGERD
```

**Publicatie statussen**:
```
CONCEPT → GEPUBLICEERD → REACTIES_GESLOTEN → IN_BEHANDELING → 
TOEGEWEZEN → GEARCHIVEERD

Alternatieve paden:
- GEPUBLICEERD → INGETROKKEN
```

### 9.2 Event-Driven Triggers

| Trigger Event | Actie(s) |
|---------------|----------|
| `HuuropzeggingOntvangen` | Start proces Verhuurmutatie, Plan voorinspectie |
| `EenheidBeschikbaar` | Start publicatieproces |
| `KandidaatGeaccepteerd` | Start contracteerproces, Stuur afwijzingen |
| `HuurovereenkomstGetekend` | Plan sleuteloverdracht, Start incasso |
| `ReparatieverzoekOntvangen` | Start onderhoudsproces, Bepaal prioriteit |
| `OnderhoudsorderGereed` | Start facturering, Verstuur KTO |
| `BetalingsachterstandGeconstateerd` | Start incassoproces |
| `BetalingsregelingAfgesloten` | Pas incassostatus aan |

### 9.3 Rollen en Verantwoordelijkheden

| Rol | Processen |
|-----|-----------|
| **Verhuurconsulent** | Werven kandidaten, Contracteren, Opleveren |
| **Woonmakelaar** | Publiceren, Bezichtigingen, Matching |
| **Onderhoudsconsulent** | Intake, Regisseren reparatieonderhoud |
| **Vakman/Monteur** | Realiseren onderhoudsorder |
| **Incassomedewerker** | Minnelijke fase, Betalingsregelingen |
| **Financieel medewerker** | Facturering, Betalingsverwerking |
| **Inspecteur** | Voor/eindinspecties, Conditiemetingen |

---

## 10. Procesdiagrammen voor Implementatie

### 10.1 Verhuurproces Swimlane

```
┌────────────────┬────────────────┬────────────────┬────────────────┐
│  Woonmakelaar  │ Verhuurcons.   │   Financiën    │    Huurder     │
├────────────────┼────────────────┼────────────────┼────────────────┤
│                │                │                │                │
│ ○ Publiceren   │                │                │                │
│ ↓              │                │                │                │
│ ◇ Reacties?    │                │                │ ● Reageren     │
│ ↓              │                │                │                │
│ □ Matching     │                │                │                │
│ ↓              │                │                │                │
│ □ Bezichtiging │                │                │ ● Bezichtigen  │
│ ↓              │                │                │                │
│ □ Screening    │                │                │                │
│ ↓              │                │                │                │
│                │ □ Contract     │                │                │
│                │ ↓              │                │                │
│                │ □ Ondertekenen │                │ ● Ondertekenen │
│                │ ↓              │                │                │
│                │                │ □ 1e Verhuurn. │ ● Betalen      │
│                │ ↓              │                │                │
│                │ □ Sleutels     │                │ ● Ontvangen    │
│                │ ●              │                │                │
│                │                │                │                │
└────────────────┴────────────────┴────────────────┴────────────────┘

Legenda: ○=Start ●=Einde □=Activiteit ◇=Beslissing
```

### 10.2 Reparatieproces Swimlane

```
┌────────────────┬────────────────┬────────────────┬────────────────┐
│ Klantcontact   │ Onderhoudcons. │   Vakman       │   Financiën    │
├────────────────┼────────────────┼────────────────┼────────────────┤
│                │                │                │                │
│ ○ Intake       │                │                │                │
│ ↓              │                │                │                │
│ □ Diagnose     │                │                │                │
│ ↓              │                │                │                │
│                │ □ Beoordelen   │                │                │
│                │ ↓              │                │                │
│                │ ◇ Inspectie?   │                │                │
│                │ ↓              │                │                │
│                │ □ Opdracht     │                │                │
│                │ ↓              │                │                │
│                │                │ □ Plannen      │                │
│                │                │ ↓              │                │
│                │                │ □ Uitvoeren    │                │
│                │                │ ↓              │                │
│                │                │ □ Gereedmelden │                │
│                │ ↓              │                │                │
│                │ □ Controle     │                │                │
│                │ ↓              │                │                │
│                │                │                │ □ Factureren   │
│                │ ●              │                │                │
│                │                │                │                │
└────────────────┴────────────────┴────────────────┴────────────────┘
```

---

## Appendix A: Bronverwijzingen

| Bron | URL |
|------|-----|
| Procesketens | https://www.coraveraonline.nl/index.php/Procesketens |
| Stuurscenario's | https://www.coraveraonline.nl/index.php/Stuurscenario's |
| Bedrijfsprocessen | https://www.coraveraonline.nl/index.php/Bedrijfsprocessen |
| Werkprocessen | https://www.coraveraonline.nl/index.php/Werkprocessen |
| Processtappen | https://www.coraveraonline.nl/index.php/Processtappen |

---

*Document gegenereerd op basis van CORA 4.3.2 - Januari 2026*
