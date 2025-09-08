
## **10. Opbouw van microservices (met Spring Boot)**

### **10.1 Ontwerp en implementatie per service**

De DirectDeal-applicatie is opgebouwd uit zes onafhankelijke microservices, elk geïsoleerd volgens de principes van Domain-Driven Design (DDD). Elke service is geïmplementeerd als een aparte **Spring Boot-applicatie**, met een eigen domeinmodel, datalaag en infrastructuurcomponenten.

| Microservice                    | Verantwoordelijkheid              | Bijzonderheden                                   |
| ------------------------------- | --------------------------------- | ------------------------------------------------ |
| **Account Service**             | Authenticatie & autorisatie       | JWT-generatie, stateless tokenvalidatie          |
| **Chatting Service**            | Real-time communicatie            | Gebruik van WebSockets & lokale message-opslag   |
| **Sale Service**                | Verkoopbeheer (Command)           | Event Sourcing, CQRS, Event-publicatie via Kafka |
| **Catalog Service**             | Productcatalogus (Query)          | Projecties op basis van events van Sale          |
| **Transaction History Service** | Historiek van aankopen & verkopen | Conformist-pattern, event-consumptie via Kafka   |
| **Gateway Service**             | API-routering en toegangscontrole | Forwarding van requests met JWT, geen logica     |

---

#### 💡Hexagonale Architectuur (Ports and Adapters) 

Het DirectDeal-project is opgezet volgens het principe van de **hexagonale architectuur** (ook wel bekend als het **Ports-and-Adapters-patroon**). Deze architectuur zorgt ervoor dat de domeinlogica (de kern van de applicatie) volledig **onafhankelijk** blijft van frameworks, databases, externe APIs of transportprotocollen.

In combinatie met **Domain-Driven Design (DDD)** ondersteunt deze structuur een duidelijke scheiding van verantwoordelijkheden binnen elke microservice.

---

##### Basisstructuur van de architectuur

| Laag          | Verantwoordelijkheid                                     | Componenten                                                   |
| ------------- | -------------------------------------------------------- | ------------------------------------------------------------- |
| `domain`      | Pure domeinlogica zonder afhankelijkheden van buitenaf   | Entiteiten, value objects, aggregates, domeinservices         |
| `application` | Use cases & coördinatie van domeininteracties            | Command-handlers, serviceklassen                              |
| `port`        | Abstracties van interacties (zowel inbound als outbound) | Interfaces voor REST, messaging, repositories                 |
| `adapter`     | Concrete implementaties van de poorten                   | REST-controllers, Kafka listeners, MongoDB/MySQL repositories |

---

##### Voorbeeld van de pakketstructuur

```plaintext
src/
├── domain/
│   ├── object/               ← Domeinmodellen en value objects
│   └── service/             ← Domeinservices (businesslogica)
│
├── port/
│   ├── inbound/             ← Use case interfaces (bv. RegisterUserUseCase)
│   └── outbound/            ← Infra-abstracties (bv. UserRepository)
│
├── application/
│   └── service/             ← Implementaties van use cases
│
├── adapter/
│   ├── inbound/             ← REST-controllers, Kafka-listeners
│   └── outbound/            ← Database-repositories, messaging producers
│
└── config/
    └── Bean-configuraties, dependency injection
```

---

##### Rol van elke laag

|Laag|Rol|Voorbeelden|
|---|---|---|
|`port.inbound`|Interface voor business use cases|`RegisterItemUseCase`|
|`application.service`|Implementatie van use cases|`RegisterItemService`|
|`port.outbound`|Abstractie van infrastructuur|`ItemRepository`, `EventPublisher`|
|`adapter.inbound`|Inkomende interacties|`ItemController`, `KafkaListener`|
|`adapter.outbound`|Implementatie van externe afhankelijkheden|`JpaItemRepository`, `MongoEventStore`|

---

##### Richting van afhankelijkheden

```plaintext
adapter.inbound (bv. REST-controller)
        ↓
port.inbound (bv. use case interface)
        ↓
application (bv. service met business flow)
        ↓
port.outbound (bv. repository interface)
        ↓
adapter.outbound (bv. JPA-repository, Kafka-producer)
```

➡ **De afhankelijkheden wijzen altijd van buiten naar binnen — het domein is het centrum.**

---

##### Integratie met DirectDeal

- Domeinlogica in services zoals `SaleService`, `CatalogService`, `TransactionHistoryService` zijn netjes ingekapseld in aggregates, services en event handlers, binnen de `domain`-laag.
    
- Events zoals `ItemRegisteredEvent` worden binnen `SaleContext` afgehandeld in de domeinlaag, waarbij adapters enkel zorgen voor opslag (MongoDB) of verspreiding (Kafka).
    
- JWT-authenticatie wordt afgehandeld via een adapter (inbound), maar validatie gebeurt in de applicatie- of domeinlaag van elke service.
    
- Door gebruik te maken van **Conformist-patroon** in `Catalog` en `TransactionHistory`, worden inkomende events direct vertaald naar hun eigen leesmodellen, zonder complexe transformatielogica in de domeinlaag.
    
---

#### 🗂️ Structurering en isolatie van domeinlogica per context

De domeinlogica binnen het DirectDeal-systeem is zorgvuldig gescheiden en geïmplementeerd volgens de principes van Domain-Driven Design (DDD). De kernlogica van elk bounded context bevindt zich in duidelijke structuren zoals **aggregates**, **domain services** en **event handlers**, wat zorgt voor modulariteit, testbaarheid en helderheid van verantwoordelijkheden.

- In de **Sale Context** is de verkooplogica gecentraliseerd in aggregates zoals `Item` en `Sale`, die de businessregels rond het registreren, wijzigen en sluiten van verkoopitems afdwingen. De command-zijde maakt gebruik van **Event Sourcing**, waarbij iedere statewijziging resulteert in het genereren van een domeinevent zoals `ItemRegisteredEvent` of `ItemUpdatedEvent`. Deze events worden afgehandeld door specifieke **event handlers** die de veranderingen persistenter maken in een MongoDB-gebaseerde event store.
    
- De **Catalog Context** volgt het **conformist-patroon** en bevat geen eigen domeinlogica. In plaats daarvan worden de events die door de Sale Context worden gepubliceerd, ontvangen en verwerkt om leesmodellen op te bouwen. Dit gebeurt via event handlers die updates aanbrengen in de MongoDB query-database. De catalogus weerspiegelt hiermee een projectie van het domein zonder eigen interpretatie of transformatie van businessregels.
    
- In de **Transaction History Context** is eveneens sprake van een puur leesgericht model, dat via **Kafka** domeinevents consumeert en in een eigen datastore vastlegt. Deze service bevat minimale businesslogica, en de event handlers dienen hoofdzakelijk om historische data consistent en traceerbaar te houden.
    
- **Domain services** worden gebruikt in gevallen waar logica niet op natuurlijke wijze bij één aggregate thuishoort. Dit maakt de logica herbruikbaar en contextueel gescheiden van infrastructuur of presentatielagen.
    
- De **JWT-tokenverificatie**, hoewel technisch van aard, is eveneens lokaal en zelfstandig per context geïmplementeerd, passend bij het ontwerpprincipe van **stateless services met lokale verantwoordelijkheid**.
    

>Door deze structuur is het domeinmodel in elk bounded context sterk gescheiden, wat niet alleen de **isolatie van logica** bevordert, maar ook **autonome ontwikkeling en schaalbaarheid** mogelijk maakt. Dit sluit aan bij de gekozen microservicesarchitectuur, waarin elke service zijn eigen domein beheert en ontwikkelt binnen zijn eigen context en verantwoordelijkheid.


---

#### ⚙️ Spring Boot-configuratie

- Elke microservice maakt gebruik van **eigen configuratiebestanden (`application.yml`)**, beheerd via **Kubernetes ConfigMap**.

	Elke microservice in het systeem maakt gebruik van een **eigen Spring Boot-configuratie**, die normaal gesproken wordt beheerd via `application.yml` of `application.properties`. In plaats van deze configuratiebestanden lokaal in de codebase op te slaan, wordt de configuratie centraal beheerd via **Kubernetes ConfigMaps**.

- Via `spring.cloud.kubernetes.config` wordt de configuratie bij het opstarten automatisch geladen per service.

	Bij het opstarten van een service wordt de configuratie **dynamisch ingeladen vanuit Kubernetes** via de integratie met **`spring.cloud.kubernetes`**. Deze integratie stelt Spring Boot in staat om configuratiegegevens (zoals databaseverbindingen, poorten, Kafka-gegevens, enz.) rechtstreeks uit de bijbehorende ConfigMap op te halen.
	
- Dit ondersteunt dynamische configuratie-updates en scheiding van configuratie per omgeving (dev, staging, production).


---

#### 🗄️ Database-per-Service strategie

Hoewel in productie het **Database-per-Service-patroon** zal worden toegepast (elke service zijn eigen databank), is er in de ontwikkelfase bewust gekozen voor **gedeelde databanken** omwille van beperkte systeemresources (CPU/memory).

|Context|Gebruikte DB|Functie|
|---|---|---|
|Account, Chatting|MySQL|Standaard CRUD-operaties|
|Sale Context (Command)|MongoDB|Event Store (event sourcing)|
|Catalog Context (Query)|MongoDB|Leesmodel-opslag (CQRS)|
|TransactionHistory Context|MongoDB|Leesmodel-opslag + Token Store|

> In de toekomst volstaat het om per service de connectie-instellingen aan te passen om volledig gescheiden databases te gebruiken, zonder grote codewijzigingen.

---

####  ✅ Voordelen van deze aanpak

|Voordeel|Uitleg|
|---|---|
|**Domeinbescherming**|Domeinlogica blijft vrij van infrastructuurkennis|
|**Flexibele infrastructuur**|Adapters kunnen eenvoudig vervangen worden (REST ↔ Kafka, MySQL ↔ MongoDB)|
|**Betere testbaarheid**|Inbound- en outbound-poorten maken unit-testing eenvoudig|
|**Schaalbaarheid**|Elke microservice is modulair en afzonderlijk te deployen|
|**Duidelijkheid**|Structuur is helder en geschikt voor samenwerking met meerdere teams|

---

### 10.2 Implementatie van communicatie en gegevensintegratie tussen services

Binnen het DirectDeal-systeem is de communicatie tussen microservices zorgvuldig ontworpen op basis van **Domain-Driven Design (DDD)**-principes en **event-driven architectuur**. Elke service beheert zijn eigen bounded context en interageert met andere diensten via expliciete integratiepatronen, waarbij de onderlinge afhankelijkheden geminimaliseerd zijn.

#### 🔁 Communicatiemechanismen

##### Asynchrone communicatie via Kafka

- **Kafka** wordt gebruikt als het primaire kanaal voor asynchrone communicatie tussen services.

- Domein-events zoals `ItemRegisteredEvent`, `ItemUpdatedEvent` worden gepubliceerd door de **Sale-service** na succesvolle commandoperaties.
    
- Andere services (zoals **Catalog**, **Transaction History**) abonneren zich op deze events en verwerken ze in hun eigen context, zonder directe koppeling aan de bronservice.
    
- Dit bevordert **losse koppeling** en verhoogt de schaalbaarheid.
    
##### Synchrone communicatie via REST

- Sommige services communiceren direct via **RESTful HTTP**-aanroepen, vooral in gevallen waar directe feedback nodig is (bijv. login via de **Account-service**).
    
- De **API Gateway** fungeert als centrale router en stuurt binnenkomende HTTP-verzoeken door naar de juiste microservice, inclusief JWT-authenticatieheader.
    
---

#### 🧩 Gegevensintegratiepatronen

##### Event-sourcing & CQRS

- In de **Sale-service** wordt een **Command Query Responsibility Segregation (CQRS)**-architectuur gecombineerd met **Event Sourcing**:
    
    - Commands leiden tot events die in een **MongoDB event store** worden opgeslagen.
        
    - Leesmodellen worden geüpdatet via event-processors.
        
- Deze events worden gedeeld met andere services via Kafka.
    
##### Conformist-patroon

- Zowel de **Catalog-service** als de **Transaction History-service** volgen het **Conformist-patroon**:
    
    - Zij adopteren het eventmodel van de Sale-service zonder eigen interpretatie.
        
    - Dit betekent dat hun interne gegevensmodellen (leesmodellen) rechtstreeks worden afgeleid van de originele events, wat zorgt voor **modelconsistente integratie**.
        
---

#### 🗃️ Gegevensscheiding en database-integratie

- Iedere service beheert zijn eigen **datastore**, in lijn met het **Database-per-service**-patroon. Vanwege resourcebeperkingen in de ontwikkelomgeving delen sommige services tijdelijk een MySQL-database.
    
- **MongoDB** wordt gebruikt:
    
    - In de **Sale-service** voor het event store (command-zijde).
        
    - In de **Catalog-service** voor het leesmodel (query-zijde).
        
    - In de **Transaction History-service** als opslag voor afgehandelde events.
    
	- In de **Catalog-service** en de **Transaction History-service** voor het token store.
        
- In productie zal elk van deze services volledig van elkaar gescheiden databases gebruiken, wat eenvoudig configureerbaar is via Kubernetes ConfigMaps.
    
---

#### 🔐 Beveiliging & authenticatie

- JWT-authenticatie wordt toegepast bij alle communicatie, zowel REST als event-based.
    
- De **JWT-token wordt gevalideerd in elke service**, op basis van een gedeelde (symmetrische) sleutel tijdens ontwikkeling.
    
- In de productieomgeving zal worden overgestapt op **asymmetrische sleutelvalidatie** om veiligheid te verbeteren.
    
- Validatie gebeurt in een filterlaag vóór de controller en dringt dus niet door tot domeinlogica.
    

---

