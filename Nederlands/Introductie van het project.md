## 3. Introductie van het project

### 3.1. Projectoverzicht
**Uitleg van het onderwerp en de achtergrond**  
DirectDeal is een online direct-handelsplatform waar gebruikers vrij goederen kunnen aanbieden, kopen en verkopen. Het project is opgezet in een gedistribueerde omgeving en maakt gebruik van een microservices-architectuur (MSA). Binnen dit platform worden moderne technologieën en ontwerp­patronen toegepast, waaronder Spring Boot, Spring WebFlux, Event Sourcing (via het Axon Framework), het CQRS-patroon, Kafka, NoSQL (MongoDB), Docker en Kubernetes.  
Het project heeft als doel een realistisch en schaalbaar voorbeeld te bieden van hoe een enterprise Java-omgeving kan worden ontworpen en geïmplementeerd binnen een microservices-landschap.  

**Ontwikkelingsmotieven en doelstellingen**  
De initiële motivatie voor dit project was het opdoen van praktijkervaring met gedecentraliseerde systemen. Hoewel de ontwikkelaar ruime ervaring had met traditionele monolithische toepassingen, ontbrak hands-on ervaring met MSA en bijbehorende patronen. DirectDeal is daarom opgezet als een leer- en experimenteeromgeving waarin kennis over enterprise development kan worden verdiept.  
De doelstellingen zijn:
- Het ontwerpen en implementeren van een schaalbaar, robuust en flexibel handelsplatform.
- Het toepassen en valideren van geavanceerde architectuurpatronen zoals CQRS en Event Sourcing.
- Het vergroten van de persoonlijke en professionele competenties op het gebied van enterprise Java en microservices.

---

### 3.2. Visie en kernwaarden van het project
De visie van DirectDeal is om een betrouwbaar en toekomstbestendig platform te ontwikkelen dat als referentie kan dienen voor moderne softwarearchitecturen. Hierbij staan de volgende kernwaarden centraal:
- **Leren door praktijk**: het project is een experimentele omgeving waarin nieuwe technologieën en patronen worden toegepast en gevalideerd.
- **Schaalbaarheid en veerkracht**: het platform moet bestand zijn tegen groei en piekbelasting, met behoud van prestaties.
- **Transparantie en modulariteit**: door middel van microservices wordt het systeem opgedeeld in overzichtelijke, goed te beheren componenten.
- **Innovatie**: de toepassing van geavanceerde concepten zoals Event Sourcing en CQRS draagt bij aan een innovatieve en moderne benadering van softwareontwikkeling.

---

### 3.3. Reikwijdte van het project (inclusief beperkingen)
De reikwijdte van het project omvat het ontwerpen, implementeren en valideren van een volledig functionerend microservices-platform voor directe handel. De belangrijkste onderdelen zijn:
- Implementatie van kernfunctionaliteiten: gebruikersregistratie, productbeheer, transacties en communicatie.
- Integratie van essentiële infrastructuurcomponenten zoals API-gateway, messaging, databases en monitoring.
- Validatie van MSA-specifieke patronen, met name Event Sourcing en CQRS.

**Beperkingen**:  
- Het project is opgezet als een individueel leertraject, wat inhoudt dat sommige onderdelen mogelijk simplificaties bevatten in vergelijking met productieklare systemen.  
- De focus ligt op technische architectuur en implementatie, niet op gebruikersinterface of uitgebreide commerciële features.  
- De gebruikte technologieversies en configuraties zijn afgestemd op leerdoelen en experimentele validatie, niet per se op langdurige productie-ondersteuning.