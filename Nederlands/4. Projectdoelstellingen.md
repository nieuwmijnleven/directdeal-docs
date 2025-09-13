## 4. Projectdoelstellingen

### 4.1. Functionele doelstellingen
Het DirectDeal-platform is ontworpen om de volgende functionele doelstellingen te realiseren:

- **Gebruikersbeheer**  
  Registratie, inloggen en uitloggen van gebruikers, inclusief veilige authenticatie via JWT-tokens.  
- **Productbeheer**  
  Mogelijkheid voor gebruikers om producten aan te maken, te wijzigen, te verwijderen en te publiceren.  
- **Zoek- en catalogusfunctionaliteit**  
  Opvragen van beschikbare producten via een aparte catalogus-service, met snelle responstijden door toepassing van CQRS.  
- **Transactiegeschiedenis**  
  Weergave van afgeronde transacties, zodat gebruikers inzicht hebben in hun handelsactiviteiten.  
- **Communicatie**  
  Chatfunctionaliteit tussen kopers en verkopers ter ondersteuning van onderhandelingen en afstemming.  
- **Toegang via API Gateway**  
  Eén centraal toegangspunt voor externe clients, waarmee beveiliging en consistentie worden gewaarborgd.  

**Prestatiedoelen**  
- **Responstijd**: leesoperaties (zoals productcatalogus) moeten binnen enkele honderden milliseconden beschikbaar zijn.  
- **Verwerkingscapaciteit**: het systeem moet meerdere gelijktijdige gebruikers en transacties kunnen ondersteunen door schaalbare microservices.  
- **Beschikbaarheid**: de architectuur is ontworpen om fouttolerantie te waarborgen, met minimale downtime bij service-updates of -uitval.  

---

### 4.2. Technische doelstellingen
Naast de functionele eisen zijn de volgende technische doelstellingen geformuleerd:

- **Architectuur**  
  Implementatie van een microservices-architectuur (MSA) waarin elke service autonoom en modulair opereert.  
- **Toepassing van ontwerp­patronen**  
  - **CQRS**: scheiding van lees- en schrijfbewerkingen om lock-contentie te minimaliseren en prestaties te verbeteren.  
  - **Event Sourcing**: opslag van domeinmutaties als gebeurtenissen, waardoor consistentie, herleidbaarheid en asynchrone verwerking mogelijk zijn.  
- **Technologie-stack**  
  Gebruik van moderne enterprise-technologieën, waaronder:  
  - Backend: Java 17, Spring Boot, Spring WebFlux, JPA (Hibernate)  
  - Messaging & data: Kafka, Redis, MongoDB, MySQL  
  - Orkestratie & containerisatie: Docker, Kubernetes (minikube)  
  - Frontend: Vue.js, Vuetify  
  - Monitoring & logging: Logback, Micrometer  
- **Ontwikkelingsmethodologie**  
  - Agile, iteratieve aanpak met nadruk op continue integratie en testen.  
  - Automatisering van build- en deploymentprocessen met Gradle en Kubernetes.  
  - Validatie van services via testframeworks (JUnit, Mockito, RestAssured).  
- **Schaalbaarheid en veerkracht**  
  Het systeem moet horizontaal schaalbaar zijn en bestand tegen partiële storingen, ondersteund door messaging en load balancing.