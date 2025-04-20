# companyregister-api-documentation

# Firmenbuch SOAP API Documentation (Austria)

This document provides a simplified but complete reference for using the Firmenbuch SOAP API provided by the Austrian government.

---

## Base WSDL Endpoint

```xml
https://justizonline.gv.at/jop/api/at.gv.justiz.fbw/ws/fbw.wsdl
```

This WSDL describes all available services and operations.

---

## SOAP Version

- **Default:** SOAP 1.2
- **Alternate:** SOAP 1.1

### SOAP 1.2 Content-Type
```http
Content-Type: application/soap+xml; charset=utf-8
```

### SOAP 1.1 Content-Type (if needed)
```http
Content-Type: text/xml; charset=utf-8
SOAPAction: "<operation>"
```

---

## Required Headers

```http
Content-Type: application/soap+xml; charset=utf-8
X-API-KEY: your_api_key_here
```

Replace `your_api_key_here` with your issued key.

---

## Available Operations

### 1. SUCHEFIRMAREQUEST
**Search for companies in the Austrian Firmenbuch (company register).**

#### Namespace
```xml
xmlns:suc="ns://firmenbuch.justiz.gv.at/Abfrage/SucheFirmaRequest"
```

#### Request XML
```xml
<suc:SUCHEFIRMAREQUEST xmlns:suc="ns://firmenbuch.justiz.gv.at/Abfrage/SucheFirmaRequest">
    <suc:FIRMENWORTLAUT>GmbH</suc:FIRMENWORTLAUT>
    <suc:EXAKTESUCHE>false</suc:EXAKTESUCHE>
    <suc:SUCHBEREICH>1</suc:SUCHBEREICH>
    <suc:GERICHT></suc:GERICHT>
    <suc:RECHTSFORM></suc:RECHTSFORM>
    <suc:RECHTSEIGENSCHAFT></suc:RECHTSEIGENSCHAFT>
    <suc:ORTNR></suc:ORTNR>
</suc:SUCHEFIRMAREQUEST>
```

#### Parameters
| Field             | Type    | Description                                      |
| ----------------- | ------- | ------------------------------------------------ |
| FIRMENWORTLAUT    | String  | Search term for company name                     |
| EXAKTESUCHE       | Boolean | `true` for exact match, `false` for fuzzy search |
| SUCHBEREICH       | Integer | Search domain, usually `1`                       |
| GERICHT           | String  | (Optional) Specific court code                   |
| RECHTSFORM        | String  | (Optional) Legal form filter                     |
| RECHTSEIGENSCHAFT | String  | (Optional) Legal characteristics filter          |
| ORTNR             | String  | (Optional) Municipality code                     |

---

### 2. AUSZUGREQUEST
**Retrieve a detailed company extract.**

#### Namespace
```xml
xmlns:aus="ns://firmenbuch.justiz.gv.at/Abfrage/v2/AuszugRequest"
```

#### Request XML
```xml
<aus:AUSZUGREQUEST xmlns:aus="ns://firmenbuch.justiz.gv.at/Abfrage/v2/AuszugRequest">
    <aus:FIRMA_ID>123456</aus:FIRMA_ID>
    <aus:VARIANTE>2</aus:VARIANTE>
    <aus:SPRACHE>DE</aus:SPRACHE>
</aus:AUSZUGREQUEST>
```

#### Parameters
| Field       | Type   | Description                              |
|-------------|--------|------------------------------------------|
| FIRMA_ID    | String | Firmenbuchnummer                         |
| VARIANTE    | Int    | Output version (1 = V1, 2 = V2, etc.)    |
| SPRACHE     | String | Language code, e.g., DE for German       |

---

### 3. URKUNDEREQUEST
**Request a specific document (e.g., PDF) for a company.**

#### Namespace
```xml
xmlns:urk="ns://firmenbuch.justiz.gv.at/Abfrage/UrkundeRequest"
```

#### Request XML
```xml
<urk:URKUNDEREQUEST xmlns:urk="ns://firmenbuch.justiz.gv.at/Abfrage/UrkundeRequest">
    <urk:URKUNDEN_ID>UR123456789</urk:URKUNDEN_ID>
</urk:URKUNDEREQUEST>
```

#### Parameters
| Field         | Type   | Description                      |
|---------------|--------|----------------------------------|
| URKUNDEN_ID   | String | ID of the document to retrieve   |

---

### 4. VERAENDERUNGENFIRMAREQUEST
**Get historical changes to a company.**

#### Namespace
```xml
xmlns:verf="ns://firmenbuch.justiz.gv.at/Abfrage/VeraenderungenFirmaRequest"
```

#### Request XML
```xml
<verf:VERAENDERUNGENFIRMAREQUEST xmlns:verf="ns://firmenbuch.justiz.gv.at/Abfrage/VeraenderungenFirmaRequest">
    <verf:FIRMA_ID>123456</verf:FIRMA_ID>
</verf:VERAENDERUNGENFIRMAREQUEST>
```

#### Parameters
| Field      | Type   | Description                  |
|------------|--------|------------------------------|
| FIRMA_ID   | String | Firmenbuchnummer of company  |

---

## Response Format

The response is defined in multiple schemas under `AuszugResponse`. Key elements include:

### Root Element
```xml
<AUSZUGRESPONSE>
```

### Common Elements
| Element | Description                                        |
| ------- | -------------------------------------------------- |
| FIRMA   | Main company details                               |
| FUN     | Officers (e.g., managing directors, board members) |
| PER     | Persons involved                                   |
| ZWL     | Branch offices                                     |
| VOLLZ   | Registration acts                                  |
| IDENT   | Company identifiers                                |
| KUR     | Key from company register (optional)               |

---

## Example CURL Request

```bash
curl -X POST https://justizonline.gv.at/jop/api/at.gv.justiz.fbw/ws/fbw.wsdl \
  -H "Content-Type: application/soap+xml; charset=utf-8" \
  -H "X-API-KEY: your_api_key_here" \
  -d '<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope"
               xmlns:suc="ns://firmenbuch.justiz.gv.at/Abfrage/SucheFirmaRequest">
  <soap:Header/>
  <soap:Body>
    <suc:SUCHEFIRMAREQUEST>
      <suc:FIRMENWORTLAUT>GmbH</suc:FIRMENWORTLAUT>
      <suc:EXAKTESUCHE>false</suc:EXAKTESUCHE>
      <suc:SUCHBEREICH>1</suc:SUCHBEREICH>
      <suc:GERICHT></suc:GERICHT>
      <suc:RECHTSFORM></suc:RECHTSFORM>
      <suc:RECHTSEIGENSCHAFT></suc:RECHTSEIGENSCHAFT>
      <suc:ORTNR></suc:ORTNR>
    </suc:SUCHEFIRMAREQUEST>
  </soap:Body>
</soap:Envelope>'
```

---

## Notes
- All XML must be UTF-8 encoded.
- Empty tags (e.g. `<suc:GERICHT></suc:GERICHT>`) are valid.
- SOAP responses are versioned (v1, v2, etc.) and vary by VARIANTE requested.
- Errors are returned as SOAP Faults.

---

## Next Steps
You now have access to the following operations:

- `SUCHEFIRMAREQUEST` – Search for companies
- `AUSZUGREQUEST` – Get detailed company data
- `URKUNDEREQUEST` – Retrieve official documents
- `VERAENDERUNGENFIRMAREQUEST` – Get company change history

Contact the Firmenbuch integration team for production keys, error codes, or rate limits.

