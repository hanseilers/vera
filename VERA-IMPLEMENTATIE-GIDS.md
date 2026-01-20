# VERA Implementatie Gids voor ERP-Platform

> Praktische gids voor het implementeren van een VERA-compliant ERP-systeem.

---

## 1. Database Schema Aanbevelingen

### 1.1 Naamgeving Conventies

Volg de VERA naamgeving:
- **Entiteiten**: PascalCase (Eenheid, Relatie, Onderhoudsorder)
- **Attributen**: camelCase (straatnaam, huisnummer, boekdatum)
- **Referentiedata**: HOOFDLETTERS (EENHEIDSOORT, RELATIEROLSOORT)

### 1.2 Kernentiteiten per Module

#### Module: Vastgoedbeheer
```sql
-- Kernentiteiten
CREATE TABLE eenheid (...)
CREATE TABLE pand (...)
CREATE TABLE cluster (...)
CREATE TABLE adres (...)
CREATE TABLE woningwaardering (...)
CREATE TABLE prijscomponent (...)

-- Koppeltabellen
CREATE TABLE eenheid_cluster (...)
CREATE TABLE eenheid_collectiefobject (...)
```

#### Module: Relatiebeheer
```sql
CREATE TABLE relatie (...)
CREATE TABLE natuurlijk_persoon (...) -- extends relatie
CREATE TABLE rechtspersoon (...) -- extends relatie
CREATE TABLE relatie_rol (...)
CREATE TABLE communicatiekanaal (...)
```

#### Module: Contractbeheer
```sql
CREATE TABLE overeenkomst (...)
CREATE TABLE huurovereenkomst (...) -- extends overeenkomst
CREATE TABLE huurmutatie (...)
CREATE TABLE betalingsregeling (...)
```

#### Module: Onderhoud
```sql
CREATE TABLE onderhoudsverzoek (...)
CREATE TABLE onderhoudsorder (...)
CREATE TABLE besteding (...)
CREATE TABLE inspectie (...)
```

#### Module: Financiën
```sql
CREATE TABLE boeking (...)
CREATE TABLE factuur (...)
CREATE TABLE factuurregel (...)
CREATE TABLE betaling (...)
```

#### Module: Woonruimteverdeling
```sql
CREATE TABLE woningzoekende (...)
CREATE TABLE publicatie (...)
CREATE TABLE reactie (...)
CREATE TABLE aanbieding (...)
```

### 1.3 Referentiedata Tabel

```sql
CREATE TABLE referentiedata (
    id UUID PRIMARY KEY,
    soort VARCHAR(50) NOT NULL,        -- bijv. EENHEIDSOORT
    code VARCHAR(10) NOT NULL,          -- bijv. WOO
    naam VARCHAR(100) NOT NULL,         -- bijv. Woonruimte
    omschrijving TEXT,
    begindatum DATE,
    einddatum DATE,
    parent_soort VARCHAR(50),
    parent_code VARCHAR(10),
    informatiedomein VARCHAR(50),
    
    UNIQUE(soort, code),
    FOREIGN KEY (parent_soort, parent_code) 
        REFERENCES referentiedata(soort, code)
);

-- Index voor snelle lookups
CREATE INDEX idx_referentiedata_soort ON referentiedata(soort);
```

---

## 2. API Design Richtlijnen

### 2.1 Endpoint Structuur

```
/api/v4/{domein}/{entiteit}

Voorbeelden:
GET  /api/v4/vastgoed/eenheden
GET  /api/v4/vastgoed/eenheden/{id}
POST /api/v4/vastgoed/eenheden
PUT  /api/v4/vastgoed/eenheden/{id}

GET  /api/v4/relaties/relaties
GET  /api/v4/onderhoud/onderhoudsorders
GET  /api/v4/financien/boekingen
```

### 2.2 Headers (VERA Standaard)

```http
# Request headers
X-Stuurgegevens-ZenderOrganisatie: L0001
X-Stuurgegevens-ZenderAdministratie: ERP-SYSTEEM
X-Stuurgegevens-TijdstipBericht: 2026-01-20T14:30:00Z
X-Stuurgegevens-Referentienummer: REF-2026-00123
X-Parameters-Pagina: 1
X-Parameters-AantalPerPagina: 50

# Response headers
X-Parameters-Cursor: eyJwYWdlIjoxfQ==
X-Parameters-TotaalAantal: 1250
```

### 2.3 Paginatie

```json
GET /api/v4/vastgoed/eenheden?pagina=2&aantalPerPagina=50

Response:
{
  "data": [...],
  "_links": {
    "self": "/api/v4/vastgoed/eenheden?pagina=2",
    "first": "/api/v4/vastgoed/eenheden?pagina=1",
    "prev": "/api/v4/vastgoed/eenheden?pagina=1",
    "next": "/api/v4/vastgoed/eenheden?pagina=3",
    "last": "/api/v4/vastgoed/eenheden?pagina=25"
  },
  "_meta": {
    "pagina": 2,
    "aantalPerPagina": 50,
    "totaalAantal": 1250
  }
}
```

### 2.4 Referentiedata in Responses

```json
{
  "id": "uuid",
  "soort": {
    "code": "WOO",
    "naam": "Woonruimte",
    "omschrijving": "Zelfstandige woonruimte"
  },
  "status": {
    "code": "VER",
    "naam": "Verhuurd"
  }
}
```

### 2.5 Cross-domein Referenties

```json
{
  "id": "uuid-onderhoudsorder",
  "eenheid": {
    "id": "uuid-eenheid",
    "code": "WON-001234",
    "_links": {
      "self": "/api/v4/vastgoed/eenheden/uuid-eenheid"
    }
  },
  "leverancier": {
    "id": "uuid-leverancier",
    "naam": "Onderhoudsbedrijf B.V.",
    "_links": {
      "self": "/api/v4/relaties/relaties/uuid-leverancier"
    }
  }
}
```

---

## 3. Integratie met Basisregistraties

### 3.1 BAG (Basisregistratie Adressen en Gebouwen)

```
Koppelvelden:
- bagNummeraanduidingIdentificatie
- bagOpenbareruimteIdentificatie  
- bagWoonplaatsIdentificatie
- bagPandIdentificatie
- bagVerblijfsobjectIdentificatie
```

**Synchronisatie aanbeveling:**
- Periodiek synchroniseren met BAG-API
- Wijzigingen ophalen via BAG-mutaties
- Eigen adressen toevoegen voor niet-BAG objecten

### 3.2 BRK (Basisregistratie Kadaster)

```
Koppelvelden:
- kadastraleGemeente
- kadastraalPerceel
- kadastraalAppartementsrecht
```

### 3.3 BRP (Basisregistratie Personen)

```
Koppelvelden:
- bsn (Burgerservicenummer)
- brpIdentificatie
```

**Let op**: BSN alleen opslaan als noodzakelijk en met juiste beveiliging.

### 3.4 HR (Handelsregister)

```
Koppelvelden:
- kvkNummer
- rsin
- hrIdentificatie
```

---

## 4. Referentiedata Implementatie

### 4.1 Initiële Import

Download de referentiedata CSV:
```bash
curl -o referentiedata.csv \
  https://github.com/Aedes-datastandaarden/vera-referentiedata/raw/main/Referentiedata.csv
```

Import script:
```python
import csv
import psycopg2

def import_referentiedata(csv_path, conn):
    with open(csv_path, 'r', encoding='utf-8') as f:
        reader = csv.DictReader(f, delimiter=';')
        
        cursor = conn.cursor()
        for row in reader:
            cursor.execute("""
                INSERT INTO referentiedata 
                (soort, code, naam, omschrijving, begindatum, 
                 einddatum, parent_soort, parent_code, informatiedomein)
                VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s)
                ON CONFLICT (soort, code) DO UPDATE SET
                    naam = EXCLUDED.naam,
                    omschrijving = EXCLUDED.omschrijving
            """, (
                row['Soort'],
                row['Code'],
                row['Naam'],
                row['Omschrijving'],
                row['Begindatum'] or None,
                row['Einddatum'] or None,
                row['Parent'].split('.')[0] if row['Parent'] else None,
                row['Parent'].split('.')[1] if '.' in row['Parent'] else None,
                row['Informatiedomein']
            ))
        
        conn.commit()
```

### 4.2 API voor Referentiedata

```python
# Endpoint: GET /api/v4/referentiedata/{soort}

@app.get("/api/v4/referentiedata/{soort}")
def get_referentiedata(soort: str, actief: bool = True):
    query = """
        SELECT code, naam, omschrijving, begindatum, einddatum
        FROM referentiedata
        WHERE soort = %s
    """
    
    if actief:
        query += " AND (einddatum IS NULL OR einddatum > CURRENT_DATE)"
    
    query += " ORDER BY naam"
    
    return db.execute(query, [soort.upper()])
```

### 4.3 Caching Strategie

Referentiedata wijzigt weinig - implementeer caching:

```python
from functools import lru_cache
from datetime import datetime, timedelta

@lru_cache(maxsize=200)
def get_referentiedata_cached(soort: str):
    return db.query("SELECT * FROM referentiedata WHERE soort = %s", [soort])

# Cache invalideren bij update
def invalidate_referentiedata_cache():
    get_referentiedata_cached.cache_clear()
```

---

## 5. Event-Driven Architectuur

### 5.1 Gebeurtenissen (Events)

VERA hanteert event-driven architectuur. Belangrijke gebeurtenissen:

| Domein | Gebeurtenis | Trigger |
|--------|-------------|---------|
| Vastgoed | EenheidGewijzigd | Update eenheid |
| Overeenkomsten | HuurovereenkomstAangemaakt | Nieuw contract |
| Overeenkomsten | HuurovereenkomstBeeindigd | Huuropzegging |
| Onderhoud | OnderhoudsverzoekOntvangen | Nieuwe melding |
| Onderhoud | OnderhoudsorderAfgerond | Werk voltooid |
| Financien | BetalingOntvangen | Betaling verwerkt |
| Woonruimteverdeling | EenheidToegewezen | Toewijzing |

### 5.2 Event Schema

```json
{
  "eventId": "uuid",
  "eventType": "HuurovereenkomstAangemaakt",
  "timestamp": "2026-01-20T14:30:00Z",
  "source": "erp-systeem",
  "data": {
    "overeenkomstId": "uuid",
    "eenheidId": "uuid",
    "huurderIds": ["uuid1", "uuid2"],
    "ingangsdatum": "2026-02-01"
  }
}
```

### 5.3 Message Broker Integratie

```python
# Publiceren event
def publish_event(event_type: str, data: dict):
    event = {
        "eventId": str(uuid.uuid4()),
        "eventType": event_type,
        "timestamp": datetime.utcnow().isoformat(),
        "source": "erp-systeem",
        "data": data
    }
    
    message_broker.publish(
        topic=f"vera.{event_type.lower()}",
        message=json.dumps(event)
    )
```

---

## 6. Externe Integraties

### 6.1 DICO (Ketenstandaard)

Voor externe partijen (aannemers, etc.) gebruik DICO-berichten:

```
VERA (intern) <---> DICO Adapter <---> Externe Partij
```

Website: https://ketenstandaard.nl/standaard/dico/

### 6.2 SBR-wonen (dPi/dVi)

Voor verantwoording naar toezichthouder:

| Rapportage | Frequentie | Inhoud |
|------------|-----------|--------|
| dPi | Jaarlijks (prognose) | Meerjarenbegroting |
| dVi | Jaarlijks (realisatie) | Jaarrekening |

### 6.3 Woonruimteverdeelsystemen

Koppeling met regionale woonruimteverdeelsystemen:
- WoningNet
- Woonservice
- Etc.

---

## 7. Beveiligingsoverwegingen

### 7.1 Gevoelige Gegevens

| Gegeven | Classificatie | Maatregelen |
|---------|---------------|-------------|
| BSN | Zeer gevoelig | Encryptie, beperkte toegang |
| Inkomen | Gevoelig | Encryptie, audit logging |
| Adres | Normaal | Standaard beveiliging |
| Huurprijs | Normaal | Standaard beveiliging |

### 7.2 Autorisatie per Domein

```yaml
rollen:
  verhuurconsulent:
    - vastgoed: lezen
    - relaties: lezen, schrijven
    - overeenkomsten: lezen, schrijven
    - onderhoud: lezen
    
  onderhoudsconsulent:
    - vastgoed: lezen
    - relaties: lezen
    - onderhoud: lezen, schrijven
    
  financieel_medewerker:
    - relaties: lezen
    - overeenkomsten: lezen
    - financien: lezen, schrijven
```

---

## 8. Testdata

### 8.1 Voorbeelddata Eenheid

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "code": "WON-001234",
  "naam": "Lindenlaan 32",
  "soort": {"code": "WOO", "naam": "Woonruimte"},
  "detailsoort": {"code": "EGW", "naam": "Eengezinswoning"},
  "status": {"code": "VER", "naam": "Verhuurd"},
  "oppervlakte": 85.5,
  "bouwjaar": 1985,
  "aantalKamers": 4,
  "aantalSlaapkamers": 3,
  "adres": {
    "straatnaam": "Lindenlaan",
    "huisnummer": 32,
    "postcode": "1234AB",
    "woonplaatsnaam": "Utrecht"
  },
  "woningwaardering": {
    "stelsel": {"code": "WWS", "naam": "Woningwaarderingsstelsel"},
    "puntentotaal": 142,
    "maximaleHuurprijs": 879.66
  },
  "energieprestatie": {
    "label": "B",
    "energieIndex": 1.35
  },
  "prijscomponenten": [
    {"soort": {"code": "NTH"}, "bedrag": 650.00},
    {"soort": {"code": "SVC"}, "bedrag": 45.00}
  ]
}
```

### 8.2 Voorbeelddata Huurder

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440001",
  "soort": {"code": "NAT", "naam": "Natuurlijk persoon"},
  "voornamen": "Jan",
  "voorletters": "J.",
  "achternaam": "Jansen",
  "geslacht": {"code": "MAN"},
  "geboortedatum": "1975-05-15",
  "rollen": [
    {
      "rol": {"code": "HUU", "naam": "Huurder"},
      "begindatum": "2020-01-01"
    }
  ],
  "adressen": [
    {
      "soort": {"code": "EEN"},
      "straatnaam": "Lindenlaan",
      "huisnummer": 32,
      "postcode": "1234AB",
      "woonplaatsnaam": "Utrecht"
    }
  ],
  "communicatiekanalen": [
    {
      "soort": {"code": "EMA"},
      "waarde": "j.jansen@email.nl"
    },
    {
      "soort": {"code": "MOB"},
      "waarde": "06-12345678"
    }
  ]
}
```

---

## 9. Checklist voor VERA Compliance

### 9.1 Datamodel Compliance

- [ ] Entiteiten volgen VERA naamgeving
- [ ] Attributen volgen VERA definities
- [ ] Referentiedata correct geïmplementeerd
- [ ] Cross-domein relaties als referenties
- [ ] ID-velden conform VERA (id, idExtern, code)

### 9.2 API Compliance

- [ ] OpenAPI 3.0 specificaties
- [ ] VERA headers geïmplementeerd
- [ ] Paginatie conform standaard
- [ ] Referentiedata in responses als object
- [ ] Cross-domein links met href

### 9.3 Integratie Compliance

- [ ] BAG-koppeling voor adressen
- [ ] Referentiedata synchronisatie
- [ ] Event-driven communicatie
- [ ] DICO voor externe partijen

---

## 10. Nuttige Links

| Resource | URL |
|----------|-----|
| CORA/VERA Wiki | https://www.coraveraonline.nl |
| OpenAPI Specificaties | https://github.com/Aedes-datastandaarden/vera-openapi |
| Referentiedata | https://github.com/Aedes-datastandaarden/vera-referentiedata |
| Referentiedata API | https://vera-service.azurewebsites.net/api/referentiedata |
| SwaggerUI Vastgoed | https://aedes-datastandaarden.github.io/vera-openapi/Informatiedomeinen/Vastgoed.html |
| Ketenstandaard DICO | https://ketenstandaard.nl/standaard/dico/ |
| Brongegevens Excel | https://github.com/vereniging-corponet/vera-openapi/blob/main/docs/brongegevens.xlsx |

---

*Document gegenereerd voor VERA 4.3 - Januari 2026*
