## 9. Ontwerp met de DDD (Domain-Driven Design) methodologie

### 9.1 Inleiding tot DDD

#### **Wat is Domain-Driven Design?**  
DDD (Domain-Driven Design) is een methodologie voor het ontwerpen van software, waarbij complexe zakelijke problemen nauwkeurig worden begrepen en opgelost door middel van domeinmodellen.

#### **Wat is Domain?**
Het domein is het gebied waar software moet problemen op te lossen.

#### **Wat is Business Domain?**
Een businessdomein is het bedrijfsgebied waar software moet problemen op te lossen.

#### **Wat is domainmodel?**
Een domeinmodel is een conceptueel model dat de concepten, regels en data van een specifiek domein abstracteert.

#### **Wat is dominobject?**
Domeinobjecten kapselen concepten en bedrijfsregels binnen het domeinmodel in en  beheren de toestand en het gedrag van het domein. De soorten domeinobjecten bestaan uit entiteiten (Entity), waarde-objecten (Value Object), aggregaten (Aggregate), domeindiensten (Domain Service) en repositories (Repository).

* **Entiteit (Entity)**: Een domeinobject met een unieke identifier en een levenscyclus.
* **Waarde-object (Value Object)**: Een object zonder unieke identifier dat onveranderlijk is.
* **Aggregaat (Aggregate)**: Een eenheid die gerelateerde entiteiten en waarde-objecten bundelt binnen een consistentiegrens.
* **Domeindienst (Domain Service)**: Een stateless domeinobject dat domeinlogica uitvoert die niet toebehoort aan entiteiten of waarde-objecten.
* **Repository (Repository)**: Een component die de persistentie van domeinobjecten beheert (opslag, opvragen, etc.).

#### **Wat is Bounded Context**
De bounded context is de grens waarbinnen een specifiek domeinmodel en terminologie consequent worden toegepast.

---

### 9.2 Motivatie voor het gebruik van Domain-Driven Design (DDD)

#### Waarom DDD essentieel is voor DirectDeal ?

Het ontwerp van DirectDeal — een platform gebaseerd op meerdere microservices — vereist een robuuste aanpak om **complexe businesslogica rond verkoop, catalogusbeheer, transacties en gebruikersinteractie** te structureren. Domain-Driven Design (DDD) biedt hiervoor een krachtig denkkader.

In plaats van het systeem op technische componenten te baseren (zoals controllers, services en repositories), plaatst DDD het **domein** — de kern van de business — centraal in het ontwerp. Dit maakt het mogelijk om logica te organiseren rond betekenisvolle domeinconcepten, in nauwe samenwerking met de echte businessprocessen.

---
#### Belangrijkste voordelen van DDD in dit project

##### 🧩 Bounded Contexts per service

Elke microservice (zoals Sale, Catalog, Transaction History of Chatting) vertegenwoordigt een **bounded context**, met een eigen domeinmodel, taalgebruik en businesslogica. Dit zorgt voor **duidelijke verantwoordelijkheidsgrenzen** en voorkomt onnodige afhankelijkheden tussen services.

##### 🔗 Lage koppeling, hoge cohesie

Door domeinen te scheiden en te encapsuleren, kunnen teams (of in dit geval, logische componenten) zich onafhankelijk ontwikkelen en deployen zonder verstoring van andere delen van het systeem. Dit sluit perfect aan bij de **microservicesarchitectuur**.

##### 📚 Ubiquitous Language en domeinmodellering

DDD moedigt het gebruik aan van een **gemeenschappelijke taal** tussen ontwikkelaars en (potentiële) domeinexperts. Ook al was dit project een soloproject, deze aanpak verhoogt de leesbaarheid, uitbreidbaarheid en consistentie van het systeem.

##### ♻️ Herbruikbaarheid en schaalbaarheid

Doordat domeinlogica expliciet is gedefinieerd in aggregates, services en events (zoals te zien in Sale Context), kunnen onderdelen eenvoudig worden uitgebreid, hergebruikt of vervangen zonder het gehele systeem aan te passen.

##### ⚙️ Klaar voor toekomstige groei

De toepassing van DDD creëert een **stabiele en uitbreidbare fundering** waarop toekomstige features, integraties of zelfs teamuitbreiding kunnen plaatsvinden. Het helpt om verandering te omarmen zonder chaos te creëren.

---

### 9.3 Proces van domeinmodellering

#### **Werkwijze voor domeinanalyse**  
DirectDeal is als persoonlijk project volledig zelfstandig door mij ontwikkeld, waardoor er geen samenwerking met domeinexperts of andere belanghebbenden heeft plaatsgevonden. In plaats daarvan heb ik zelf de belangrijkste gebruikersscenario's en bedrijfsprocessen geanalyseerd, en op basis daarvan de noodzakelijke entiteiten (zoals accountbeheer, chat, verkoop, catalogus en transacties) en hun gedrag gedefinieerd.

#### **Beperkingen door individuele ontwikkeling**  
Door het ontbreken van feedback van externe gebruikers of domeinexperts was het vaststellen van een eenduidige domeintaal (ubiquitous language) lastiger. Alle terminologie, domeinbegrenzing en roltoewijzingen zijn volledig door mijzelf bepaald, waardoor er een grotere kans op misinterpretaties of inefficiënties is ontstaan.

#### **Ontwikkeling van de ubiquitous language en domeingrenzen**  
Om verwarring te voorkomen, heb ik duidelijke en consistent gebruikte termen gekozen voor documentatie en broncode. De grenzen van bounded contexts voor elke microservice heb ik afgeleid uit mijn eigen interpretatie van de businessprocessen en deze zodanig ingericht dat ze logisch gescheiden zijn en elk hun eigen verantwoordelijkheden hebben.
#### **Lessen en beperkingen van het toepassen van DDD als soloproject**

>	- Zonder externe validatie was het lastig om de kwaliteit en consistentie van het domeinmodel te waarborgen op langere termijn.
>
>	- Bij toevoegingen of wijzigingen van functionaliteit moest ik veelvuldig refactoren en zelf bepalen of het model nog voldeed aan de eisen.    
>
>	- Ik heb veel geleerd over het analyseren van domeinen en het opstellen van models, maar met meer feedback had het model wellicht efficiënter en robuuster kunnen zijn.
    
---
### 9.4 Structuur van Bounded Contexts

#### **Overzicht van de Bounded Contexts in het systeem**

- **Accountbeheer**: User management, authenticatie (`direct-deal-account-service`)    
- **Chat**: Communicatie tussen gebruikers (`direct-deal-chatting-service`)
- **Verkoop (Command side)**: Registratie en wijziging van verkopen (`direct-deal-sale-service`)
- **Catalogus (Query side)**: Weergave van producten (`direct-deal-sale-catalog-service`)
- **Transactiegeschiedenis**: Overzicht van afgeronde transacties (`direct-deal-transaction-history-service`)
- **Gateway**: API toegang en routing (`direct-deal-gateway-service`)
    
#### **Mapping van services naar contexten**  
Elke microservice beheert een eigen bounded context met eigen domeinlogica , regels en data.

#### **Interactie tussen Bounded Contexts**  
Communicatie verloopt via events en REST API’s met eventual consistency:

- CQRS patroon splitst lezen en schrijven over verschillende contexten
- Asynchrone event-berichten via Kafka zorgen voor synchronisatie tussen contexten
- Een Anti-Corruption Layer (ACL) wordt niet structureel toegepast, omdat het project als soloproject weinig integratie met complexe externe systemen kent. Indien nodig kan ACL overwogen worden om domeinintegriteit te waarborgen bij toekomstige uitbreidingen of externe koppelingen.

---
### **9.5 Implementatievoorbeelden en domeinmodellering per microservice**

In dit onderdeel worden concrete voorbeelden uit het project gecombineerd met een nadere toelichting op de domeinmodellen per microservice. Hiermee wordt inzichtelijk hoe de DDD-principes in de praktijk zijn toegepast.

---
### **9.5.1 direct-deal-account-service**

- **Bounded Context**: Accountbeheer
- **Aggregate Root**: `Account`
- **Entities**: `Account`
- **Value Objects**: `Authority`(maar Entity qua JPA)
- **Domain Services**: ~~Authenticatie, tokenbeheer~~
- **Repositories**: `AccountRepository`

**Implementatievoorbeeld:**  
> In dit voorbeeld wordt de kernstructuur van de `direct-deal-account-service` binnen het domein van accountbeheer getoond. De `Account`-entiteit fungeert als aggregate root en bevat cruciale gebruikersgegevens zoals e-mail, versleuteld wachtwoord, naam, activatiestatus en een verzameling `Authority`-objecten die de rollen van de gebruiker vertegenwoordigen.
> 
> De `Authority`-klasse is, hoewel technisch een JPA-entity vanwege de unieke identifier `authorityName`, vanuit een DDD-perspectief te beschouwen als een value object omdat het een onveranderlijke rol of rechten-set voor een gebruiker definieert, zoals `ADMIN` of `USER`.
> 
> De `AccountRepository` verzorgt de toegang tot de persistentielaag, waarbij specifieke query’s zijn gedefinieerd om accounts inclusief hun autoriteiten efficiënt op te halen.
> 
> Deze opzet zorgt voor een heldere scheiding van verantwoordelijkheden, wat onderhoudbaarheid en uitbreidbaarheid van de service ten goede komt.

```java
// Account.java
@Entity  
@Table(name = "ACCOUNT")   
public class Account extends AbstractAuditingEntity {  
    @Id  
    @GeneratedValue(strategy = GenerationType.IDENTITY)  
    @Column(name = "ACCOUNT_ID")  
    private Long id;  
      
    @Column(name = "ACCOUNT_EMAIL", length = 64, unique = true)  
    private String email;  
      
    @Column(name = "ACCOUNT_PASSWORD_HASH", length = 60, nullable = false)  
    private String password;  
      
    @Column(name = "ACCOUNT_NAME", length = 30)  
    private String name;  

    @Column(name = "ACCOUNT_ACTIVATED", nullable = false)  
    private boolean activated;  
  
    @ManyToMany  
    @JoinTable(  
       name = "ACCOUNT_AUTHORITY",  
       joinColumns = {@JoinColumn(name = "ACCOUNT_ID", referencedColumnName = "ACCOUNT_ID")},  
       inverseJoinColumns = {@JoinColumn(name = "AUTHORITY_NAME", referencedColumnName = "AUTHORITY_NAME")})  
    private Set<Authority> authorities;  
      
    public void updateFrom(AccountDTO dto) {  
       if (Objects.nonNull(dto.getName())) this.name = dto.getName();  
    }  
      
    public void changePassword(String encodedNewPassword) {  
       this.password = encodedNewPassword;  
    }  
  
    public void active() {  
       this.activated = true;  
    }  
  
    public void inactive() {  
       this.activated = false;  
    }  
}

// Authority.java
@Entity  
@Table(name = "AUTHORITY")  
public class Authority {  
  
    public static final Authority ADMIN = new Authority(AuthorityConstants.ADMIN);   
    public static final Authority USER = new Authority(AuthorityConstants.USER);  
  
    @Id      
    @Column(name = "AUTHORITY_NAME", length = 30)  
    private String authorityName;  
}

// AccountRepository.java
@Repository  
public interface AccountRepository extends JpaRepository<Account, Long> {  
  
    public Optional<Account> findByEmail(String email);  
  
    @EntityGraph(attributePaths = "authorities")  
    public Optional<Account> findOneWithAuthoritiesByEmail(String email);  
}
```

---

### **9.5.2 direct-deal-chatting-service**

- **Bounded Context**: Chat
- **Aggregate Root**: `ChattingRoom`
- **Entities**: `ChattingRoom`
- **Value Objects**: `ChattingMessage`
- **Domain Services**: Geen
- **Repositories**: `ChattingRepository`

**Implementatievoorbeeld:**  
> In dit voorbeeld wordt het domein van chatten binnen de `direct-deal-chatting-service` weergegeven. De `ChattingRoom`-entiteit is de aggregate root en beheert de context van een gesprek tussen een verkoper en een klant over een specifiek item.  
> 
> De `ChattingRoom` bevat een lijst van `ChattingMessage`-value objects die de individuele berichten in het gesprek representeren. Elk bericht bevat informatie over wie het heeft verzonden (`talkerId`), de inhoud van het bericht (`text`), en metadata zoals tijdstip en verzendstatus.  
> 
> Het `ChattingRepository` verzorgt de toegang tot de persistentielaag, met querymethoden om chatrooms te zoeken op basis van de combinatie van item, verkoper en klant, evenals overzichten van chatrooms voor specifieke gebruikers.  
> 
> Deze structuur zorgt voor een duidelijke scheiding van verantwoordelijkheden binnen het domeinmodel, waarbij `ChattingRoom` de lifecycle en consistentie van de berichten beheert en `ChattingMessage` als waarde-object dient binnen die context. Dit draagt bij aan een overzichtelijke, onderhoudbare en uitbreidbare service.

```java
//ChattingRoom.java
@Entity  
@Table(name = "CHATTING_ROOM",   
        indexes = {  
            @Index(unique = true, columnList = "CHATTING_ROOM_ITEM_ID, CHATTING_ROOM_SELLER_ID, CHATTING_ROOM_CUMSTOMER_ID")  
        })  
public class ChattingRoom {  
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)  
    @Column(name = "CHATTING_ROOM_ID")  
    private Long id;  
  
    @Column(name = "CHATTING_ROOM_TITLE", length = 128, nullable = false)  
    private String title;  
  
    @Column(name = "CHATTING_ROOM_ITEM_ID", length = 36, nullable = false)  
    private String itemId;  
   
    @Column(name = "CHATTING_ROOM_SELLER_ID", length = 50, nullable = false)  
    private String sellerId;  
  
    @Column(name = "CHATTING_ROOM_CUMSTOMER_ID", length = 50, nullable = false)  
    private String customerId;  
  
    @OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)  
    @JoinColumn(name = "CHATTING_ROOM_ID")  
    private List<ChattingMessage> messages = new ArrayList<>();  
  
    @Column(name = "CHATTING_ROOM_CREATED_DATE", nullable = false, updatable = false)  
    private Instant createdDate;  
  
    public void addMessage(ChattingMessage message) {  
        this.messages.add(message);  
    }  
}

//ChattingMessage.java
@Entity  
@Table(name = "CHATTING_MESSAGE")  
public class ChattingMessage {  
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)  
    @Column(name = "CHATTING_MESSAGE_ID")  
    private Long id;  
  
    @Column(name = "CHATTING_ROOM_ID", nullable = false)  
    private Long chattingRoomId;  
      
    @Column(name = "CHATTING_MESSAGE_TALKER_ID", length = 50, nullable = false)  
    private String talkerId;  
   
    @Column(name = "CHATTING_MESSAGE_TEXT", length = 512, nullable = false)  
    private String text;  
  
    @Column(name = "CHATTING_MESSAGE_CREATED_DATE", nullable = false)  
    private Instant createdDate;  
  
    @Column(name = "CHATTING_MESSAGE_SENT", nullable = false)  
    private boolean sent;  
  
    public boolean isNotSent() {  
        return !sent;  
    }  
}

//ChattingRepository.java
@Repository  
public interface ChattingRepository extends JpaRepository<ChattingRoom, Long> {  
    public Optional<ChattingRoom> findByItemIdAndSellerIdAndCustomerId(String itemId, String sellerId, String customerId);  
    public List<ChattingRoom> findBySellerIdAndItemId(String sellerId, String itemId);  
    public List<ChattingRoom> findBySellerIdOrCustomerId(String sellerId, String customerId);  
    public boolean existsByItemIdAndSellerIdAndCustomerId(String itemId, String sellerId, String customerId);  
}
```

---

### **9.5.3 direct-deal-sale-service**

- **Bounded Context**: Verkoopbeheer (Command-gedeelte in EventSorcing en CQRS)
- **Aggregate Root**: `SaleItemAggregate`, `ItemCategory`, `ImageUploadStatus`
- **Entities**: `SaleItemAggregate`, `ItemCategory`, `ImageUploadStatus`
- **Value Objects**:  `FavoriteItem`
- **Domain Services**: 
- **Repositories**: `FavoriteItemRepository`,`ItemCategoryRepository`, `ImageUploadStatusRepository`,  `EventSourcing (SaleItemAggregate)`
- **Opmerkingen**: Toepassing van Event Sourcing en CQRS (Command-gedeelte)

**Implementatievoorbeeld:**  

> In dit voorbeeld zien we de kerncomponenten van de `direct-deal-sale-service`, gericht op het command-gedeelte binnen het domein van verkoopbeheer. 
>
>	- De `SaleItemAggregate` fungeert als aggregate root en gebruikt Event Sourcing en CQRS om de levenscyclus van een verkoopitem te beheren. Hierbij worden commando’s verwerkt die events genereren, welke vervolgens de status en eigenschappen van het aggregate bijwerken. Dit biedt een duidelijke scheiding tussen commandoverwerking en leesmodellen.
>
>	- `ItemCategory` is een entiteit die categorieën hiërarchisch organiseert via een ouder-kindrelatie, wat het mogelijk maakt om boomstructuren van categorieën te modelleren.
>
>	- `ImageUploadStatus` registreert de status van beelduploadprocessen en is eveneens een >entiteit die de voortgang en resultaat van uploads bijhoudt.
>
>	- `FavoriteItem` is een value object dat favorieten van gebruikers bijhoudt. Hoewel het een database-identificatie heeft voor persistentie, heeft het geen eigen levenscyclus buiten de context van de gebruiker en het item, waardoor het als een waardeobject wordt beschouwd.
>
>	- De repositories voor `FavoriteItem`, `ItemCategory` en `ImageUploadStatus` verzorgen de persistente opslag en opvraging.
>
>Belangrijke aspecten:
>
>	- Event Sourcing in `SaleItemAggregate` zorgt voor audittrail en eenvoudig herstel van de toestand via events.
>	
>	- Validaties worden doorgevoerd binnen command handlers, zoals het controleren van een positieve targetprijs en het eigenaarschap van het item.
>	
>	- Cqrs en event sourcing ondersteunen een schaalbare en robuuste architectuur voor het verkoopproces.

```java

//SaleItemAggregate.java   
@Aggregate  
public class SaleItemAggregate {  
    @AggregateIdentifier  
    private String id;  
  
    private String ownerId;  
      
    private String title;  
      
    private String category;  
      
    private long targetPrice;  
  
    private boolean discountable;  
      
    private String text;  
      
    private List<String> images;  
      
    private SaleItemStatus status;  
          
    // private String createdBy;  
    private Instant createdDate;   
      
    // private String lastModifiedBy;  
    private Instant lastModifiedDate;   
  
    @CommandHandler  
    public SaleItemAggregate(ItemRegisterCommand cmd) {  
        if (cmd.getTargetPrice() <= 0) {  
            throw new IllegalArgumentException("target price <= 0");  
        }  
  
        log.debug("id => " + cmd.getId());  
        AggregateLifecycle.apply(ItemRegisteredEvent.builder()  
                                    .id(cmd.getId())  
                                    .ownerId(cmd.getOwnerId())  
                                    .title(cmd.getTitle())  
                                    .category(cmd.getCategory())  
                                    .targetPrice(cmd.getTargetPrice())  
                                    .discountable(cmd.isDiscountable())  
                                    .text(cmd.getText())  
                                    .images(cmd.getImages())  
                                    .status(cmd.getStatus())  
                                    .createdDate(Instant.now())  
                                    .build());        
    }  
  
    @CommandHandler  
    public void handle(ItemUpdateCommand cmd) {  
        if (cmd.getTargetPrice() <= 0) {  
            throw new IllegalArgumentException("target price <= 0");  
        }  
  
        String userId = SecurityUtils.getCurrentUserLogin();  
        if (!Objects.equals(userId, this.getOwnerId()))  
            throw SaleItemException.builder()  
                        .messageKey("saleservice.exception.saleitemaggregate.itemupdatecommand.notthesame.onwerid.message")  
                        .messageArgs(new String[]{userId, this.getOwnerId()})  
                        .build();  
  
        log.debug("call {}.ItemUpdateCommand", this.getClass().getSimpleName());  
        AggregateLifecycle.apply(ItemUpdatedEvent.builder()  
                                    .id(cmd.getId())  
                                    // .ownerId(cmd.getOwnerId())  
                                    .title(cmd.getTitle())  
                                    .category(cmd.getCategory())  
                                    .targetPrice(cmd.getTargetPrice())  
                                    .discountable(cmd.isDiscountable())  
                                    .text(cmd.getText())  
                                    .images(cmd.getImages())  
                                    //.status(SaleItemStatus.SALE)  
                                    .lastModifiedDate(cmd.getLastModifiedDate())  
                                    .build());  
    }  
  
    @CommandHandler  
    public void handle(ItemDeleteCommand cmd) {  
        log.debug("call {}.ItemDeleteCommand", this.getClass().getSimpleName());  
        AggregateLifecycle.apply(ItemDeletedEvent.builder()  
                                    .id(cmd.getId())  
                                    .build());  
    }  
  
    @CommandHandler  
    public void handle(ItemSaleStartCommand cmd) {  
        AggregateLifecycle.apply(ItemSaleStartedEvent.builder()  
                                    .id(cmd.getId())  
                                    .build());  
    }  
  
    @CommandHandler  
    public void handle(ItemSaleStopCommand cmd) {  
        AggregateLifecycle.apply(ItemSaleStoppedEvent.builder()  
                                    .id(cmd.getId())  
                                    .build());  
    }  
  
    @CommandHandler  
    public void handle(ItemSaleCompleteCommand cmd) {  
        AggregateLifecycle.apply(ItemSaleCompletedEvent.builder()  
                                    .id(this.getId())  
                                    .ownerId(this.getOwnerId())  
                                    .title(this.getTitle())  
                                    .category(this.getCategory())  
                                    .targetPrice(this.getTargetPrice())  
                                    .mainImage(this.getImages().get(0))  
                                    .build());  
  
        log.debug("ItemSaleCompletedEvent: {}", this);  
    }  
  
      
    @EventSourcingHandler  
    public void on(ItemRegisteredEvent event) {  
        this.id = event.getId();  
        this.title = event.getTitle();  
        this.ownerId = event.getOwnerId();  
        this.category = event.getCategory();  
        this.targetPrice = event.getTargetPrice();  
        this.discountable = event.isDiscountable();  
        this.text = event.getText();  
        this.images = event.getImages();  
        this.status = event.getStatus();  
        this.createdDate = event.getCreatedDate();  
    }  
  
    @EventSourcingHandler  
    public void on(ItemUpdatedEvent event) {  
        this.id = event.getId();  
        this.title = event.getTitle();  
        this.category = event.getCategory();  
        this.targetPrice = event.getTargetPrice();  
        this.discountable = event.isDiscountable();  
        this.text = event.getText();  
        this.images = event.getImages();  
        this.status = event.getStatus();  
        this.lastModifiedDate= event.getLastModifiedDate();  
    }  
      
    @EventSourcingHandler  
    public void on(ItemDeletedEvent event) {  
        log.debug("call {}.ItemDeletedEvent", this.getClass().getSimpleName());  
        this.id = event.getId();  
        this.status = SaleItemStatus.DELETED;  
    }  
      
    @EventSourcingHandler  
    public void on(ItemSaleStartedEvent event) {  
        this.id = event.getId();  
        this.status = SaleItemStatus.SALE;  
    }  
  
      
    @EventSourcingHandler  
    public void on(ItemSaleStoppedEvent event) {  
        this.id = event.getId();  
        this.status = SaleItemStatus.STOPPED;  
    }  
  
    @EventSourcingHandler  
    public void on(ItemSaleCompletedEvent event) {  
        this.id = event.getId();  
        this.status = SaleItemStatus.COMPLETED;  
    }  
}

//ItemCategory.java
@Entity  
@Table(name = "ITEM_CATEGORY",   
        indexes = {  
            @Index(columnList = "PARENT_CATEGORY_ID")  
        })  
// @Table(name = "ITEM_CATEGORY")  
public class ItemCategory {  
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)  
    @Column(name = "ITEM_CATEGORY_ID")  
    private Long id;  
   
    @Column(name = "ITEM_CATEGORY_NAME", length = 128, nullable = false)  
    private String name;  
  
    @ManyToOne(fetch = FetchType.LAZY)  
    @JoinColumn(name = "PARENT_CATEGORY_ID", nullable = true)  
    private ItemCategory parent;  
  
    @OneToMany(mappedBy = "parent", cascade = CascadeType.ALL, orphanRemoval = true)  
    private List<ItemCategory> child = new ArrayList();  
  
    public void addChildItemCategory(ItemCategory itemCategory) {  
        this.child.add(itemCategory);  
        itemCategory.setParent(this);  
    }  
}

//FavoriteItem.java
@Entity  
@Table(name = "FAVORITE_ITEM",   
        indexes = {  
            @Index(unique = true, columnList = "USER_ID, ITEM_ID")  
        })   
public class FavoriteItem {  
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)  
    @Column(name = "FAVORITE_ITEM_ID")  
    private Long id;  
  
    @Column(name = "USER_ID", length = 64, unique = true)  
    private String userId;  
    
    @Column(name = "ITEM_ID", length = 36, nullable = false)  
    private String itemId;  
   
    @Column(name = "CREATED_DATE")  
    private Instant createdDate;   
}

//ImageUploadState.java
@Entity  
@Table(name = "IMAGE_UPLOAD_STATUS")   
public class ImageUploadStatus {  
    @Id    
    @Column(name = "IMAGE_UPLOAD_STATUS_ID", length = 36)  
    private String id;  
  
    @NotNull  
    @Enumerated(EnumType.STRING)  
    @Column(name = "IMAGE_UPLOAD_STATUS_RESULT", length = 10, nullable = false)  
    private Status status;  
  
    @CreatedDate  
    @Column(name = "IMAGE_UPLOAD_STATUS_CREATED_DATE", nullable = false)  
    private Instant createdDate;   
  
    public enum Status {  
        PROCESSING,  
        SUCCESS,  
        FAILURE  
    }  
}
```

---

### **9.5.4 direct-deal-sale-catalog-service**

- **Bounded Context**: Catalogus (Query-gedeelte in EventSorcing en CQRS)
- **Aggregates**: n.v.t. (leesmodel)
- **Projectie**: `Item`-events naar leesmodellen
- **Value Objects**: `SaleItem`, `SaleList`
- **Repositories**: `SaleItemRepository`, `SaleListRepository`
- **Opmerkingen**: Toepassing van CQRS (Query-gedeelte)
    

**Implementatievoorbeeld:**  
> In dit voorbeeld zien we de leeszijde van de `direct-deal-sale-catalog-service`, die deel uitmaakt van een Event Sourcing en CQRS-architectuur. De service verwerkt domein-events die afkomstig zijn van de schrijfzijde (`direct-deal-sale-service`) en projecteert deze naar leesmodellen die geoptimaliseerd zijn voor query-doeleinden. Er zijn hier dus geen Aggregates in de traditionele DDD-zin, aangezien deze service geen wijzigingen aanbrengt in de toestand van domeinobjecten.
>
> Belangrijke kenmerken van deze implementatie zijn onder andere:
> 
> - **Gebruik van leesmodellen (`SaleItem`, `SaleList`)** die geprojecteerd worden uit events zoals `ItemRegisteredEvent`, `ItemUpdatednt`, `ItemSaleCompletedEvent`, enzovoort.
> - **MongoDB** wordt gebruikt als opslag voor de geprojecteerde documenten (`@Document`), die makkelijk opvraagbaar zijn in UI of API.
> - **Scheiding van verantwoordelijkheden** tussen Command (schrijven) en Query (lezen), conform CQRS-principes.
>

```java

//SaleItem.java
@Document("SALE_ITEM")   
public class SaleItem {  
    @Id  
    private String id;  
  
    private String ownerId;  
  
    private String title;  
  
    private String category;  
  
	private long targetPrice;  
  
    private boolean discountable;  
  
    private String text;  
  
    private List<String> images;  

    private SaleItemStatus status;  
   
    private Instant createdDate;   
  
    private Instant lastModifiedDate;   
}

//SaleList.java
@Document("SALE_LIST")  
public class SaleList {  
    @Id  
    private String id;  
  
    private String title;  
  
    private String category;  
  
	private long targetPrice;  
  
    private boolean discountable;  
  
    private String mainImage;  
    
    private SaleItemStatus status;  
  
    @CreatedDate  
    private Instant createdDate;   
  
    @LastModifiedDate  
    private Instant lastModifiedDate;   
  
    public static SaleList copyOf(SaleList another) {  
        return SaleList.builder()  
                    .id(another.getId())  
                    .title(another.getTitle())  
                    .category(another.getCategory())  
                    .targetPrice(another.getTargetPrice())  
                    .mainImage(another.getMainImage())  
                    .status(another.getStatus())  
                    .createdDate(another.getCreatedDate())  
                    .build();  
    }  
}
```

---

### **9.5.5 direct-deal-transaction-history-service**

- **Bounded Context**: Transactiegeschiedenis
- **Aggregate**: `TransactionHistory`, `BuyHistory`
- **Entities**: `TransactionHistory`, `BuyHistory`
- **Value Objects**: 
- **Domain Services**: 
- **Repositories**: `TransactionHistoryRepository`, `BuyHistoryRepository`

 **Implementatievoorbeeld:**

> In dit voorbeeld zien we de implementatie van de `direct-deal-transaction-history-service`, die verantwoordelijk is voor het bijhouden van transacties binnen het domein van **transactiegeschiedenis**. Deze service maakt deel uit van een post-orderproces en biedt een historisch overzicht van verkochte en gekochte artikelen.
> 
> De service bestaat uit twee belangrijke entiteiten:
> 
> - `TransactionHistory`: Vertegenwoordigt de verkoopgeschiedenis van een verkoper. Elke transactie bevat informatie zoals het artikel-ID, titel, categorie, doelprijs, hoofdafbeelding, koper-ID en voltooiingstijdstip.
>     
> - `BuyHistory`: Vertegenwoordigt de aankoopgeschiedenis van een koper met vergelijkbare gegevens als `TransactionHistory`, maar gefocust op de koperzijde.
>     
> 
> Beide entiteiten worden afzonderlijk opgeslagen en onderhouden, zodat zowel verkopers als kopers hun eigen transactiegeschiedenis kunnen opvragen zonder wederzijdse afhankelijkheid.
> 
> Er worden repositories gebruikt voor persistente opslag en domeininteractie:
> 
> - `TransactionHistoryRepository`: Biedt methoden om alle verkooptransacties op te halen per verkoper of koper.
>     
> - `BuyHistoryRepository`: Biedt zoekfunctionaliteit op basis van koper-ID, aankoop-ID en tijdstip van voltooiing.
>     
> 
> Er wordt **geen gebruik gemaakt van aggregates of domeinservices**, omdat het hier gaat om het registreren en ophalen van eenvoudige, historiserende gegevens zonder complexe businesslogica of invarianties.
> 
> Dit ontwerp ondersteunt **CQRS**-achtige principes, waarbij deze service fungeert als een gescheiden leesmodel voor transactiegegevens, losgekoppeld van het verkoopproces zelf.


```java

//TransactionHistory.java
@Entity  
@Table(name = "TRANSACTION_HISTORY",   
        indexes = {  
            @Index(unique = false, columnList = "TRANSACTION_ITEM_ID, TRANSACTION_ITEM_SELLER_ID")  
        })  
public class TransactionHistory {  
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)  
    @Column(name = "TRANSACTION_ID")  
    private Long id;  
  
    @Column(name = "TRANSACTION_ITEM_SELLER_ID", length = 64, nullable = false)  
    private String sellerId;  
  
    @Column(name = "TRANSACTION_ITEM_ID", length = 36, nullable = false)  
    private String itemId;  
  
    @Column(name = "TRANSACTION_ITEM_TITLE", length = 128, nullable = false)  
    private String title;  
  
    @Column(name = "TRANSACTION_ITEM_CATEGORY", length = 128, nullable = false)  
    private String category;  
  
   @Column(name = "TRANSACTION_ITEM_TARGET_PRICE", nullable = false)  
    private long targetPrice;  
   
    @Column(name = "TRANSACTION_ITEM_MAIN_IMAGE", length = 41, nullable = false) 
    private String mainImage;  
  
    @Column(name = "TRANSACTION_ITEM_BUYER_ID", length = 64, nullable = true)  
    private String buyerId;  
  
    @Column(name = "TRANSACTION_ITEM_COMPLETION_TIME", nullable = false)  
    private Instant completionTime;   
}

//BuyHistory.java
@Entity  
@Table(name = "BUY_HISTORY",   
        indexes = {  
            @Index(unique = false, columnList = "BUY_ITEM_BUYER_ID, BUY_ITEM_ID")  
        })  
public class BuyHistory {  
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)  
    @Column(name = "BUY_ID")  
    private Long id;  
  
    @Column(name = "BUY_ITEM_BUYER_ID", length = 64, nullable = false)  
    private String buyerId;  
  
    @Column(name = "BUY_ITEM_ID", length = 36, nullable = false)  
    private String itemId;  
  
    @Column(name = "BUY_ITEM_TITLE", length = 128, nullable = false)  
    private String title;  
  
    @Column(name = "BUY_ITEM_CATEGORY", length = 128, nullable = false)  
    private String category;  
  
	@Column(name = "BUY_ITEM_TARGET_PRICE", nullable = false)  
    private long targetPrice;  
  
    @Column(name = "BUY_ITEM_MAIN_IMAGE", length = 41, nullable = false)  
    private String mainImage;  
  
    @Column(name = "BUY_ITEM_SELLER", length = 64, nullable = true)  
    private String sellerId;  
   
    @Column(name = "BUY_ITEM_COMPLETION_TIME", nullable = false)  
    private Instant completionTime;   
}

//TransactionHistoryRepository.java
@Repository  
public interface TransactionHistoryRepository extends JpaRepository<TransactionHistory, Long> {  
    List<TransactionHistory> findAllBySellerId(String sellerId);  
    List<TransactionHistory> findAllByBuyerId(String buyerId);  
}

//BuyHistoryRepository.java
@Repository  
public interface BuyHistoryRepository extends JpaRepository<BuyHistory, Long> {  
    Optional<BuyHistory> findByIdAndBuyerId(Long id, String buyerId);  
    List<BuyHistory> findAllByBuyerId(String buyerId);  
    boolean existsByBuyerIdAndItemIdAndCompletionTime(String buyerId, String itemId, Instant completionTime);  
}
```

---

### **9.5.6 direct-deal-gateway-service**

- **Bounded Context**: API-toegang
- Geen domeinlogica, fungeert als technische façade
- Mogelijke toekomstige uitbreiding: autorisatie en authenticatie
    
**Implementatievoorbeeld:**  
Hoewel de gateway geen domeinlogica bevat, speelt het een cruciale rol in het routeren van requests en het beveiligen van de toegang tot microservices.

---

### 9.6 Contextmaps

#### Contextmap met onderlinge relaties
	
![Context Map](directdeal-contextmap.svg)

**Account Context**
- Beheert gebruikersauthenticatie en -autorisatie    
- Verstrekt **JWT-tokens** bij succesvolle login  
- Andere services (zoals Sale, Catalog, Chatting valideren de JWT’s zelfstandig 
- Geen centrale authenticatieserver vereist dankzij stateless token-verificatie
    
**Sale Context**
- Beheert verkoopgerelateerde businesslogica (**Command-zijde**)
- Implementeert **Event Sourcing en CQRS** 
- Genereert domeinevents zoals `ItemRegisteredEvent`, `ItemUpdatedEvent`, etc.  
- Slaat events op in een **event store (MongoDB)**
- Valideert JWT-tokens direct via lokale configuratie
    
**Catalog Context**
- Levert een **productcatalogus en verkoopoverzicht** (**Query-zijde**)
- Ontvangt en verwerkt events van de Sale Context    
- Werkt leesmodellen bij op basis van events    
- Volgt het **Conformist-patroon**: neemt het eventmodel van Sale één-op-één over  
- Valideert JWT-tokens voor toegang

**Transaction History Context**
- Toont een historisch overzicht van afgeronde transacties 
- Abonneert zich op events van de Sale Context via **Kafka** 
- Werkt eigen datastore bij op basis van ontvangen events    
- Volgt eveneens het **Conformist-patroon**
- JWT-validatie voor beveiligde toegang
    
**Chatting Context**
- Biedt onafhankelijke **real-time chatfunctionaliteit** tussen gebruikers
- Geen directe afhankelijkheid van verkoopdomeinen    
- Valideert JWT-tokens lokaal voor toegang en identiteit

 **Gateway Context**
- Verwerkt inkomende externe HTTP-verzoeken
- Fungeert als centrale toegangspoort tot alle microservices 
- **Routeert verzoeken met JWT-token** naar de juiste backend-services
- Voert zelf geen authenticatie uit, maar vertrouwt op de tokenvalidatie binnen elke service
---

### 9.7 Evaluatie van DDD-toepassing

De toepassing van Domain-Driven Design (DDD) binnen het DirectDeal-project heeft waardevolle inzichten en voordelen opgeleverd, maar ook beperkingen en uitdagingen gekend, vooral omdat het een individueel soloproject betrof.

#### Positieve resultaten

- **Structuur en modulariteit**: Door bounded contexts te definiëren, is er een heldere scheiding ontstaan tussen de verschillende domeinen (zoals accountbeheer, chat, verkoop, catalogus en transactiegeschiedenis). Dit heeft geleid tot een duidelijke domeinarchitectuur en beter onderhoudbare microservices.
    
- **Toepassing van CQRS en Event Sourcing**: De splitsing van lees- en schrijflogica en het gebruik van events hebben geleid tot betere schaalbaarheid, auditability en flexibiliteit in het verkoopproces.
    
- **Consistente domeinmodellen**: Entiteiten, value objects en aggregates zijn zorgvuldig ontworpen en sluiten goed aan bij de kernprocessen van het systeem. Dit bevordert de uitbreidbaarheid en consistentie van businessregels.
    
- **Ubiquitous language**: Hoewel individueel opgesteld, is er consistent gebruikgemaakt van domeinterminologie in de code en documentatie, wat de begrijpelijkheid van het systeem vergroot.
    
#### Uitdagingen en beperkingen

- **Ontbreken van domeinexperts**: Doordat het project zonder directe interactie met domeinexperts of stakeholders is ontwikkeld, bestond het risico op eenzijdige interpretaties van processen en domeingrenzen.
    
- **Iteratief refactoren**: Bij het uitbreiden van functionaliteit moest het domeinmodel vaak door mijzelf opnieuw worden beoordeeld en aangepast, wat tot herhaaldelijke refactorings leidde.
    
- **Geen toepassing van Anti-Corruption Layer**: Integratie met externe systemen is (nog) niet voorzien, waardoor dit belangrijke patroon beperkt is toegepast. Bij toekomstige uitbreidingen kan dit een knelpunt worden.
    
- **Complexiteit van Event Sourcing**: Het gebruik van Event Sourcing voegde extra cognitieve en technische complexiteit toe. Hoewel dit leerzaam was, leidde het soms tot meer ontwikkelwerk dan strikt noodzakelijk voor een soloproject.
    
#### Lessen geleerd

- **DDD als denkkader is nuttig**: De methodologie dwingt tot het nadenken over domeingrenzen, entiteiten en processen, wat leidt tot beter gestructureerde software.
    
- **Pragmatische toepassing is belangrijk**: Niet elk DDD-patroon hoeft altijd toegepast te worden. In sommige gevallen werkte een eenvoudiger aanpak sneller en effectiever.
    
- **Waarde van samenwerking**: Een belangrijk inzicht is dat DDD veel krachtiger wordt in samenwerking met meerdere ontwikkelaars en domeinexperts, omdat discussies en validatie leiden tot betere, meer gedragen domeinmodellen.
    
#### Conclusie

Overall heeft het toepassen van DDD in DirectDeal geleid tot een **duidelijk gestructureerd microservicesysteem** met goed afgebakende verantwoordelijkheden, ondanks de beperkingen van een soloproject. Het project laat zien dat zelfs in kleinschalige contexten DDD waardevol kan zijn om complexiteit hanteerbaar te maken. Voor toekomstige iteraties of samenwerkingen met meerdere ontwikkelaars kan de DDD-aanpak nog effectiever worden ingezet, met name voor het verfijnen van de ubiquitous language, het inzetten van integratiepatronen zoals Anti-Corruption Layers en het optimaliseren van event-driven architectuur.

---
