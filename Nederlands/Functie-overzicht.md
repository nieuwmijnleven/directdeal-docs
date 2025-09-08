### 7.1 Gedetailleerde beschrijving van elke functie

Hieronder een overzicht van de kernfuncties van het DirectDeal-platform, met per functie de hoofddoelen en belangrijkste subfunctionaliteiten:

| Functie                                  | Beschrijving                                                        | Subfuncties / kenmerken                                                                    |
| ---------------------------------------- | ------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| **Accountbeheer / Authenticatie**        | Gebruikersregistratie, login, autorisatie                           | Registreren met e‑mail, wachtwoord reset, JWT-token uitgifte, autoriteiten (rollen) beheer |
| **Gebruikerscommunicatie (Chat)**        | Realtime chat tussen koper en verkoper                              | Chatrooms per item, berichten historie, verzendstatus, melding van nieuw bericht           |
| **Verkoopbeheer (Sale — Command-zijde)** | Beheer van verkoopitems inclusief registratie, update, statusbeheer | Event Sourcing: ItemRegistered, ItemUpdated, ItemSaleStarted, etc.                         |
| **Productcatalogus (Query-zijde)**       | Weergave van producten via leesmodellen                             | Op basis van events van Sale, zoekfunctionaliteit, optimalisatie voor leesverzoeken        |
| **Transactiegeschiedenis**               | Overzicht van voltooide aankopen/verkoop                            | Historische data voor koper & verkoper, Kafka-consumptie, opslag in leesmodel              |
| **API Gateway en Routing**               | Toegangspunt voor externe requests                                  | Verdeling van routes, doorsturen van JWT, load balancing, fallback / foutafhandeling       |

---

### 7.2 Gebruikersscenario’s en verwachte gebruikerservaring

Een paar typische gebruikersscenario’s illustreren de beoogde workflow en gebruikerservaring:

- **Scenario 1: Verkoopitem aanmaken**  
    **Actor**: verkoper  
    **Proces**: De verkoper logt in → maakt een nieuw verkoopitem aan via formulier → valideert prijsinformatie → item wordt geregistreerd (event gegenereerd) → via Catalog‐service zichtbaar voor kopers.  
    **Verwachting**: intuïtieve formuliervalidatie, snelle bevestiging, real‑time zichtbaarheid in catalogus.
    
- **Scenario 2: Product zoeken & kopen**  
    **Actor**: koper  
    **Proces**: Koper logt in → zoekt product via de catalogus → bekijkt details → plaatst bestelling → status update via events teruggekoppeld in transactie‑geschiedenis.  
    **Verwachting**: vlotte zoekervaring, actuele statusinformatie, veilige betaling/authenticatie.
    
- **Scenario 3: Transactie geschiedenis bekijken**  
    **Actor**: koper of verkoper  
    **Proces**: Na aankoop/verkoop navigeert gebruiker naar geschiedenis → overzicht van items, datum, prijs, betrokken partijen.  
    **Verwachting**: consistent historisch inzicht, duidelijkheid over wie wat wanneer deed, leesbare interface.
    

---

### 7.3 Uitleg van de implementatiemethode van kernfuncties

Hoe zijn de hierboven beschreven functionaliteiten technisch gerealiseerd binnen het DirectDeal-project:

- **Gebruik van Event Sourcing en CQRS** bij Sale-service om verkoopmutaties te modelleren: commands leiden tot events, events worden opgeslagen in MongoDB (event store), en event handlers voeren updates uit naar leesmodellen.
    
- **Conformist‑patroon** toegepast in Catalog- en Transaction‑History‑services: events worden één‑op‑één overgenomen zonder interpretatie, zodat leesmodellen volledig synchroon blijven met businesslogic van Sale‑service.
    
- **Domeinlogica geïsoleerd in aggregates / domain services**: businessregels (zoals prijsvalidatie, eigenaarcontrole) worden in domainlaag geimplementeerd, met minimale technische afhankelijkheden.
    
- **JWT‑authenticatie**:
    
    - Inkomende requests via REST worden onderschept in security filters voordat controllers aangeroepen worden.
        
    - JWT tokens worden gevalideerd met een symmetrisch geheim tijdens ontwikkeling, in productie gepland op asymmetrische (private/public key) validatie.
        
    - Validatielogica zit niet in domein‑ of applicatielaag, zodat domein zuiver blijft.
        
- **Interface‑ontwerp en UX**: gebruikersinteracties worden afgeschermd via REST controllers, met duidelijke foutmeldingen (via validatieannotaties) en feedback. API’s zijn documenteerbaar (bijv. OpenAPI / Swagger) voor ontwikkelaars en eventueel frontends.
    

---
