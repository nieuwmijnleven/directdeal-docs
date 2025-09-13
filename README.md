# DirectDeal Docs

`directdeal-docs` is de **officiële documentatie- en rapportopslagplaats van het DirectDeal-project**.  
Dit document heeft als doel het volledige proces – van planning, ontwerp, implementatie, uitvoering tot toekomstige verbeteringen – systematisch vast te leggen.  

---

## Achtergrond van het project

DirectDeal is een **online direct-handelsplatform** dat gebruikers in staat stelt vrij producten te registreren en te verhandelen.  
In het bijzonder neemt dit project de uitdaging aan om een **Enterprise Java-omgeving** op te bouwen op basis van een **microservices-architectuur (MSA)**.  

Binnen het DirectDeal-project zijn verschillende moderne technologie-stacks toegepast, waaronder Spring Boot, Spring WebFlux, Kafka, MongoDB, Docker en Kubernetes.  
Hiermee zijn service-ontwerpen in gedistribueerde omgevingen, evenals geavanceerde architectuurpatronen zoals CQRS en Event Sourcing, uitgeprobeerd en gevalideerd. 

---

## Doel van dit document

Dit document gaat verder dan een eenvoudige functiebeschrijving en omvat de **visie en doelstellingen van het project**, de **ontwerpmethodologie (DDD)**, het **proces van het bouwen van microservices**, de **redenen voor technologische keuzes**, en de **uitvoerings- en implementatiestappen**.  
Zo helpt dit document de lezer om het volledige verloop en de structuur van het DirectDeal-project te begrijpen, te reproduceren of verder uit te breiden.  

---

## Structuur van het document

Dit document is als volgt opgebouwd:

1. **[Introductie](README.md)**
2. **[Inhoudspgave](2.%20Inhoudsopgave.md)**
3. **[3. Introductie van het project](<3. Introductie van het project.md>)** – onderwerp en achtergrond, ontwikkelingsdoelen, visie en kernwaarden, projectomvang  
4. **[4. Projectdoelstellingen](<4. Projectdoelstellingen.md>)** – functionele/technische doelstellingen en prestatie-indicatoren  
5. **[5. Ontwikkelomgeving](<5. Ontwikkelomgeving.md>)** – IDE, libraries, databases, frameworks, testtools  
6. **[6. Uitvoeringsmethode](<6. Uitvoeringsmethode.md>)** – lokale omgeving en GCP GKE, scripts, beheer van omgevingsvariabelen  
7. **[7. Functie-overzicht](<7. Functie-overzicht.md>)** – beschrijving van functies, scenario’s, gebruikerservaring  
8. **[8. Beschrijving van de systeemarchitectuur](<8. Beschrijving van de systeemarchitectuur.md>)** – systeemdiagrammen, MSA-ontwerp, technische keuzes  
9. **[9. Ontwerp met de DDD (Domain-Driven Design)](<9. Ontwerp met de DDD (Domain-Driven Design).md>)** – domeinmodellering, Bounded Context, Aggregates, Entities, Value Objects, UML-diagrammen  
10. **[10. Opbouw van microservices (met Spring Boot)](<10. Opbouw van microservices (met Spring Boot).md>)** – service-ontwerp en implementatie, API, service-communicatie, logging/monitoring  
11. **[11. Conclusie en toekomstige verbeteringen](<11. Conclusie en toekomstige verbeteringen.md>)** – resultaten, geleerde lessen, verbeter- en uitbreidingsmogelijkheden  
12. **[12. Bijlage](<12. Bijlage.md>)** – codelijsten, DB-schema, referentiemateriaal  

---

## Verwachte impact

Door dit document kan het DirectDeal-project zich positioneren als meer dan enkel een codebase:  
het wordt een referentie waarin **architectonische benaderingen, technologische keuzes en ontwerpfilosofieën** zijn vastgelegd.  

Daarnaast kan dit rapport dienen als basisdocument voor toekomstige projecten of uitbreidingen met een soortgelijke scope.  

---

✍️ Auteur: **Cheol Jeon**  
📅 Datum: *13-09-2025*
