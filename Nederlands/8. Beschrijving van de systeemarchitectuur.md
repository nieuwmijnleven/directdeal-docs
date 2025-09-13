## 8. Beschrijving van de systeemarchitectuur

### 8.1 Overzicht van het systeem

Het systeem bestaat uit meerdere microservices die samenwerken. De meeste services communiceren via synchrone RESTful API’s, terwijl enkele specifieke services zoals `direct-deal-sale-catalog-service`, `direct-deal-sale-service` en `direct-deal-transaction-history-service` een event-driven architectuur toepassen met Event Sourcing en CQRS. Hierdoor wordt binnen deze domeinen asynchrone verwerking en een volledige audit trail van gebeurtenissen mogelijk gemaakt.

![image](https://github.com/nieuwmijnleven/directdeal/assets/56591823/8a180293-c45e-4bf8-aab4-447fa0b3a8ad)

De belangrijkste componenten zijn:

- **direct-deal-account-service**: Beheert gebruikersaccounts en authenticatie.
- **direct-deal-chatting-service**: Verzorgt chatfunctionaliteiten tussen gebruikers.
- **direct-deal-sale-catalog-service**: Beheert het verkoopaanbod en productcatalogus.
- **direct-deal-sale-service**: Verwerkt verkooptransacties en bestellingen.
- **direct-deal-transaction-history-service**: Houdt een log bij van alle transacties.
- **direct-deal-gateway**: Fungeert als API Gateway voor inkomende verzoeken.
- **direct-deal-ui**: De gebruikersinterface voor interactie met het systeem.

Deze componenten communiceren via RESTful API's en een event-driven benadering. Hierbij worden gebeurtenissen via een event bus gepubliceerd, waarbij ze zowel worden opgeslagen in een event store voor persistente opslag als verzonden naar een externe message queue (zoals Kafka) voor consumptie door andere services binnen deze domeinen.

### 8.2 Gedetailleerde uitleg van de microservices-architectuur

#### Microservices
1. **direct-deal-account-service**
	* De microservice is verantwoordelijk voor het beheer van gebruikersaccounts en het in- en uitloggen
	* Bij het inloggen genereert de service een JWT (JSON Web Token) en stuurt deze naar de gebruiker
	* Door het JWT in de authorization-header van de HTTP-aanvraag mee te sturen, krijgen gebruikers toegang tot alle microservices binnen het systeem 
  
2. **direct-deal-chatting-service**
	* Deze microservice biedt chatfunctionaliteit tussen verkopers en kopers

3. **direct-deal-gateway-service**
	* Deze microservice fungeert als API Gateway binnen de MSA-omgeving en vormt het centrale toegangspunt voor externe aanvragen richting de achterliggende microservices

4. **direct-deal-sale-service**
	* Deze microservice faciliteert functies voor het registreren, aanpassen en verwijderen van producten door gebruikers
	* Zowel het Eventsourcing- als CQRS-patroon (Command Query Responsibility Segregation) worden toegepast op deze service
	* De service is verantwoordelijk voor het Command-gedeelte van het CQRS-patroon
	* Daarnaast beheert deze service de EventStore ten behoeve van Eventsourcing

5. **direct-deal-sale-catalog-service**
	* Deze microservice voorziet gebruikers van een overzicht van beschikbare producten
	* Voor deze service gelden eveneens Eventsourcing en het CQRS-patroon
	* De service implementeert het Query-gedeelte van CQRS
	* Daarnaast leest deze service uit de EventStore als onderdeel van Eventsourcing

6. **direct-deal-transaction-history-service**
	* Deze microservice toont gebruikers een overzicht van afgeronde transacties

#### Communicatiemethoden tussen services

- **RESTful API's**: Voor synchrone communicatie tussen services, zoals gebruikersregistratie en productinformatie.
    
- **Event-driven communicatie**: Gebeurtenissen worden via een event bus gepubliceerd, waarbij ze worden opgeslagen in een event store voor persistente opslag en tevens verzonden naar een message queue, zoals Kafka voor consumptie door andere services.
    
- **API Gateway**: De direct-deal-gateway fungeert als toegangspunt voor alle backend API-verzoeken en routeert deze naar de juiste service. De daadwerkelijke ingang van het systeem wordt echter verzorgd door een nginx reverse proxy, die ook de frontend applicatie (Vue.js) bedient en zo fungeert als het eerste toegangspunt voor gebruikersverkeer.

#### Gegevensverwerkingsstrategieën

Binnen bepaalde domeinen van het systeem — met name de verkoop- en transactiegerelateerde services — is gekozen voor de toepassing van **Event Sourcing** en **CQRS**. Deze keuzes zijn gemaakt op basis van specifieke functionele en technische eisen.

- **Event Sourcing**  
  In plaats van enkel de huidige status van entiteiten op te slaan, worden alle wijzigingen als onveranderlijke gebeurtenissen vastgelegd. Deze aanpak is gekozen omdat:  
  - Er een **volledige audit trail** nodig is van alle wijzigingen (bijv. voor juridische of compliance-doeleinden).  
  - Het wenselijk is om de **historische staat** van een entiteit te kunnen reconstrueren (bijvoorbeeld: hoe is een verkoop tot stand gekomen?).  
  - Event-sourcing maakt het eenvoudiger om **event-driven communicatie** op te zetten tussen services.

- **CQRS (Command Query Responsibility Segregation)**  
  Door lees- en schrijfbewerkingen te scheiden, kunnen beide onderdelen onafhankelijk worden geoptimaliseerd. Dit is waardevol in dit systeem omdat:  
  - De leesoperaties sterk verschillen van de schrijfbewerkingen qua structuur en prestatie-eisen.  
  - CQRS maakt het mogelijk om **snelle, schaalbare leesmodellen** op te bouwen op basis van events, zonder het schrijfmodel te belasten.  
  - Het verhoogt de **modulariteit en onderhoudbaarheid** van de code.

De combinatie van deze strategieën sluit goed aan bij een domeingedreven benadering, waarin elk domein zijn eigen logica en datastructuur beheert. Het zorgt bovendien voor betere schaalbaarheid, traceerbaarheid en fouttolerantie in de domeinen waar dat het meest nodig is.

    

### 8.3 Toepassing van Event Sourcing

- **Wat is Event Sourcing?**: Een patroon waarbij alle wijzigingen in de systeemstatus worden vastgelegd als onveranderlijke gebeurtenissen. Deze gebeurtenissen vormen de enige bron van waarheid en kunnen worden afgespeeld om de huidige of historische status te reconstrueren.
    
- **Toepassing in het systeem**: In de `direct-deal-sale-service` worden verkooptransacties vastgelegd als gebeurtenissen, zoals `SaleCreated`, `SaleConfirmed`, en `SaleCancelled`. Deze gebeurtenissen worden opgeslagen in een event store en kunnen worden afgespeeld om de status van een verkoop te reconstrueren.
    
- **Voordelen**:
    
    - Volledige audit trail van alle systeemwijzigingen.
        
    - Mogelijkheid om de systeemstatus op elk gewenst moment in het verleden te reconstrueren.
        
    - Betere ondersteuning voor event-driven communicatie en integratie met andere systemen.
        
- **Uitdagingen**:
    
    - Complexiteit in het beheren van event stores en het afspelen van gebeurtenissen.
        
    - Behoefte aan strategieën voor het beheren van event schema evolutie.
        

### 8.4 Toepassing van CQRS (Command Query Responsibility Segregation)

- **Uitleg van CQRS**: CQRS is een patroon waarbij de lees- en schrijfbewerkingen worden gescheiden. Schrijfoperaties (commands) worden uitgevoerd door command handlers, terwijl leesoperaties (queries) worden uitgevoerd door query handlers die geoptimaliseerde leesmodellen gebruiken.
    
- **Implementatie in het systeem**: In de `direct-deal-sale-service` worden verkooptransacties behandeld door command handlers die gebeurtenissen genereren. Deze gebeurtenissen worden vervolgens gebruikt om leesmodellen bij te werken die door query handlers worden gebruikt om snelle leesoperaties uit te voeren.
    
- **Impact op schaalbaarheid en prestaties**:
    
    - Lees- en schrijfoperaties kunnen onafhankelijk worden geschaald.
        
    - Leesmodellen kunnen worden geoptimaliseerd voor specifieke query's, wat de prestaties verbetert.
        
    - Schrijfoperaties kunnen complexer zijn door de noodzaak om gebeurtenissen te genereren en te verwerken.
        

### 8.5 Uitleg van de redenen voor technische architectuurkeuzes

- **Microservices-architectuur**: Maakt het mogelijk om verschillende domeinen onafhankelijk te ontwikkelen, te schalen en te onderhouden.
    
- **Event-driven communicatie**: Verhoogt de flexibiliteit en loskoppeling tussen services, waardoor het systeem beter bestand is tegen fouten en gemakkelijker te integreren is met andere systemen.
    
- **Event Sourcing**: Biedt een volledige audit trail en de mogelijkheid om de systeemstatus op elk gewenst moment in het verleden te reconstrueren.
    
- **CQRS**: Maakt het mogelijk om lees- en schrijfoperaties onafhankelijk te schalen en leesmodellen te optimaliseren voor specifieke query's, wat de prestaties verbetert.
    

---
