
## **11. Conclusie en toekomstige verbeteringen**

### **11.1. Samenvatting van projectresultaten**

Het DirectDeal-project heeft een functioneel microservicesysteem opgeleverd dat is gebouwd volgens moderne architecturale principes, zoals **Domain-Driven Design (DDD)**, **event-driven architectuur**, en de **hexagonale architectuur**. Elk bounded context is logisch gescheiden met een eigen verantwoordelijkheid, waarbij **JWT-gebaseerde authenticatie**, **CQRS**, en **event sourcing** succesvol zijn toegepast.

Belangrijke realisaties zijn onder andere:

- Een werkend microservicesysteem met **duidelijke domeinafbakening**.
    
- Implementatie van **CQRS en Event Sourcing** in het verkoopdomein (Sale Context).
    
- Gebruik van **Kafka** voor asynchrone communicatie tussen services.
    
- **JWT-authenticatie met stateless tokenvalidatie** per context.
    
- **Hexagonale architectuur** en duidelijke **package-structuur** voor betere onderhoudbaarheid.
    
- Dynamische configuratie via **Kubernetes ConfigMap**.
    

Deze architectuur zorgt voor goede schaalbaarheid, onafhankelijke ontwikkeling per service, en duidelijke domeinscheiding.

---

### **11.2. Technische/persoonlijke groei door het project**

Tijdens het project heb ik me verdiept in zowel theoretische als praktische aspecten van software-architectuur en domeinmodellering. Belangrijke leermomenten:

- **Beter begrip van DDD-principes**: zoals bounded contexts, aggregates, conformist-patterns en event handlers.
    
- **Hands-on ervaring met event sourcing** en het beheren van complexe state-transities via events.
    
- **Gebruik van Kafka** voor event-driven communicatie, inclusief consumer group beheer en topic-design.
    
- **Toepassing van hexagonale architectuur** in combinatie met Spring Boot, wat geleid heeft tot een sterk gestructureerde codebase.
    
- **Pragmatische keuzes maken**: zoals gedeeld gebruik van databases in de ontwikkelomgeving, en het plannen van toekomstige verbeteringen zonder over-engineering.
    

Ook persoonlijk heeft dit project mijn probleemoplossend vermogen, zelfdiscipline en technische diepgang aanzienlijk versterkt.

---

### **11.3. Toekomstige verbeterpunten en voorstel van extra functies**

Hoewel het systeem robuust is opgezet, zijn er verschillende punten van verbetering voor toekomstige iteraties of productie-uitrol:

#### 🔧 Technische verbeteringen

- **Migratie naar asymmetrische JWT-verificatie** (bijv. RSA) voor betere veiligheid en schaalbaarheid.
    
- **Volledige implementatie van het database-per-service patroon**, door elke service zijn eigen database toe te wijzen in productieomgevingen.
    
- **Beveiliging en rate-limiting op API-gatewayniveau**, bijvoorbeeld via Spring Cloud Gateway of Istio.
    
- **Monitoring en tracing uitbreiden**, met tools zoals Prometheus, Grafana en distributed tracing (bijv. OpenTelemetry).
    
- **Anti-Corruption Layers toevoegen** bij toekomstige integraties met externe systemen of legacy services.
    

#### ➕ Extra functies en uitbreidingen

- **Review- en ratingsysteem** voor kopers en verkopers.
    
- **Notificatiesysteem** (email, SMS, push) gekoppeld aan domeinevents.
    
- **Veilingsfunctionaliteit** of **biedingsmechanisme** bij verkoopitems.
    
- **Moderatie- of rapportagesysteem** voor ongepaste content.
    
- **Mobiele client** of progressive web app voor betere toegankelijkheid.
    

---

Deze toekomstige stappen kunnen de waarde, schaalbaarheid en veiligheid van het DirectDeal-platform verder vergroten. Het project vormt een solide basis voor professionele doorontwikkeling of samenwerking in teamverband.

---

