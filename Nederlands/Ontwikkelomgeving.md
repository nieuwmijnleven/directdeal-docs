## 5. Ontwikkelomgeving

### 5.1. Ontwikkeltools
Voor de ontwikkeling van het DirectDeal-project zijn de volgende tools gebruikt:

- **IDE**: IntelliJ IDEA als primaire ontwikkelomgeving.  
- **Versiebeheer**: Git voor broncodebeheer en GitHub als centrale opslagplaats voor samenwerking en versiecontrole.  
- **Containerisatie en orkestratie**: Docker voor het bouwen van containers en Kubernetes (Minikube) voor het lokaal uitrollen en beheren van microservices.  
- **Build-tool**: Gradle (v7.6.4) voor projectconfiguratie, afhankelijkheidsbeheer en build-automatisering.  

---

### 5.2. Ontwikkelbibliotheken
Voor de implementatie en ondersteuning van het platform is gebruikgemaakt van een reeks frameworks, databases en ondersteunende bibliotheken:

- **Programmeertaal en runtime**:  
  - Java 17 als basistaal voor alle backendservices.  

- **Frameworks en bibliotheken**:  
  - Spring Boot (v3.2.12) als kernframework voor het ontwikkelen van microservices.  
  - Spring Web (v3.2.12) voor REST API’s en webfunctionaliteit.  
  - Spring Validation (v3.2.12) voor invoervalidatie.  
  - Spring Data JPA (v3.2.12) in combinatie met Hibernate voor relationele data-opslag.  
  - Spring Actuator (v3.2.12) voor monitoring en health checks.  
  - Spring Data MongoDB (v3.2.12) voor NoSQL-integratie.  
  - Spring Data Redis (v2.5.0) voor caching en key-value opslag.  
  - Spring Cloud Kubernetes Client Config (v3.2.0) voor Kubernetes-integratie.  
  - Axon Framework (v4.9.4) voor Event Sourcing en CQRS.  
  - Axon Micrometer (v4.9.4) voor metrics en monitoring.  
  - Axon Kafka Extension (v4.9.0) voor integratie met Kafka.  
  - Axon Mongo Extension (v4.4) voor event storage in MongoDB.  
  - Jackson (v2.15.4) voor JSON serialisatie en deserialisatie.  
  - Lombok (v1.18.36) voor boilerplate-reductie.  

- **Databases en messaging**:  
  - MySQL Connector (v8.0.25) voor toegang tot relationele databases.  
  - MongoDB (v4.0.25) als NoSQL-database.  
  - Apache Kafka Clients (v3.6.2) voor berichtgebaseerde communicatie tussen microservices.  
  - Redis (v6.2.4) voor caching en snelle toegang tot tijdelijke data.  

- **Testbibliotheken**:  
  - Spring Boot Starter Test (v3.2.12) voor integratie- en eenheidstesten.  
  - Mockito Inline (v3.9.0) voor mocking in testscenario’s.  
  - Spring Security Test (v5.5.0) voor authenticatie- en autorisatietesten.  
  - RestAssured (compatibel met Spring Boot 3.x) voor het testen van REST API’s.