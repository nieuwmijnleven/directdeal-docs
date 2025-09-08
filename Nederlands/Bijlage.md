### 12.1 Codelijsten

De volledige broncode van het DirectDeal-project is beschikbaar in de volgende GitHub-repository:  
[https://github.com/nieuwmijnleven/directdeal](https://github.com/nieuwmijnleven/directdeal)

De repository bevat de implementatie van alle microservices binnen het systeem, gestructureerd volgens het hexagonale architectuurpatroon en Domain-Driven Design principes. 

directdeal/
├── direct-deal-account-service/       ← Gebruikersaccountservice
├── direct-deal-chatting-service/      ← Chatservice
├── direct-deal-gateway/               ← API Gateway
├── direct-deal-sale-service/          ← Verkoopservice
├── direct-deal-sale-catalog-service/  ← Catalogusservice
├── direct-deal-transaction-history-service/ ← Transactiegeschiedenisservice
├── direct-deal-ui/                    ← Gebruikersinterface
└── direct-deal-common/                ← Gemeenschappelijke modules

Hieronder een overzicht van de belangrijkste directories en bestanden:

- **direct-deal-account-service/**  
    Beheert gebruikersauthenticatie, JWT-tokengeneratie en gebruikersbeheer.
    
- **direct-deal-chatting-service/**  
    Implementeert real-time chatfunctionaliteit tussen gebruikers.
    
- **direct-deal-common/**  
    Bevat gedeelde componenten en utiliteitsklassen die door meerdere services worden hergebruikt.
   
- **direct-deal-sale-service/**  
    Bevat de code voor de Sale-context, inclusief event sourcing implementatie, domeinaggregaten, command handlers en event handlers.
    
- **direct-deal-catalog-service/**  
    Implementatie van de Catalog-context die leesmodellen onderhoudt gebaseerd op events van Sale, inclusief MongoDB-querydatastore.
    
- **direct-deal-transaction-history-service/**  
    Realiseert het transactiegeschiedenisoverzicht met Kafka-consumptie en eigen datastore.

Elke microservice bevat verder typische Spring Boot-configuratiebestanden zoals `application.yml` en is voorzien van eigen `Dockerfile` en Kubernetes manifests voor deployment.

Door deze modulaire structuur kunnen ontwikkelaars zich richten op de specifieke bounded context van elke service, met duidelijk gescheiden domeinlogica en infrastructuurcomponenten.

---

### **12.2. Databaseschema**

Hier wordt een overzicht gegeven van de gebruikte databasemodellen per microservice, inclusief de belangrijkste tabellen/collecties en hun relaties:

- **MySQL** (gebruik voor algemene data zoals gebruikersaccounts, transacties in sommige contexten)

```sql
CREATE TABLE AUTHORITY (
    AUTHORITY_NAME VARCHAR(30) NOT NULL,
    PRIMARY KEY (AUTHORITY_NAME)
);

CREATE TABLE ACCOUNT (
    ACCOUNT_ID BIGINT NOT NULL AUTO_INCREMENT,
    ACCOUNT_EMAIL VARCHAR(64) NOT NULL UNIQUE,
    ACCOUNT_PASSWORD_HASH VARCHAR(60) NOT NULL,
    ACCOUNT_NAME VARCHAR(30) NOT NULL,
    ACCOUNT_ACTIVATED BOOLEAN NOT NULL,
    
    CREATED_BY VARCHAR(50) NOT NULL,
    CREATED_DATE DATETIME(6) NOT NULL,
    LAST_MODIFIED_BY VARCHAR(50) NOT NULL,
    LAST_MODIFIED_DATE DATETIME(6) NOT NULL,
    
    PRIMARY KEY (ACCOUNT_ID)
);

CREATE TABLE ACCOUNT_AUTHORITY (
    ACCOUNT_ID BIGINT NOT NULL,
    AUTHORITY_NAME VARCHAR(30) NOT NULL,
    
    PRIMARY KEY (ACCOUNT_ID, AUTHORITY_NAME),
    
    CONSTRAINT FK_ACCOUNT_AUTHORITY_ACCOUNT
        FOREIGN KEY (ACCOUNT_ID)
        REFERENCES ACCOUNT (ACCOUNT_ID)
        ON DELETE CASCADE,

    CONSTRAINT FK_ACCOUNT_AUTHORITY_AUTHORITY
        FOREIGN KEY (AUTHORITY_NAME)
        REFERENCES AUTHORITY (AUTHORITY_NAME)
        ON DELETE CASCADE
);

CREATE TABLE CHATTING_ROOM (
    CHATTING_ROOM_ID BIGINT NOT NULL AUTO_INCREMENT,
    CHATTING_ROOM_TITLE VARCHAR(128) NOT NULL,
    CHATTING_ROOM_ITEM_ID VARCHAR(36) NOT NULL,
    CHATTING_ROOM_SELLER_ID VARCHAR(50) NOT NULL,
    CHATTING_ROOM_CUMSTOMER_ID VARCHAR(50) NOT NULL,
    CHATTING_ROOM_CREATED_DATE DATETIME(6) NOT NULL,

    PRIMARY KEY (CHATTING_ROOM_ID),
    UNIQUE KEY UK_CHATTING_ROOM_UNIQUE (
        CHATTING_ROOM_ITEM_ID,
        CHATTING_ROOM_SELLER_ID,
        CHATTING_ROOM_CUMSTOMER_ID
    )
);

CREATE TABLE CHATTING_MESSAGE (
    CHATTING_MESSAGE_ID BIGINT NOT NULL AUTO_INCREMENT,
    CHATTING_ROOM_ID BIGINT NOT NULL,
    CHATTING_MESSAGE_TALKER_ID VARCHAR(50) NOT NULL,
    CHATTING_MESSAGE_TEXT VARCHAR(512) NOT NULL,
    CHATTING_MESSAGE_CREATED_DATE DATETIME(6) NOT NULL,
    CHATTING_MESSAGE_SENT BOOLEAN NOT NULL,

    PRIMARY KEY (CHATTING_MESSAGE_ID),
    
    CONSTRAINT FK_CHATTING_MESSAGE_ROOM_ID
        FOREIGN KEY (CHATTING_ROOM_ID)
        REFERENCES CHATTING_ROOM (CHATTING_ROOM_ID)
        ON DELETE CASCADE
);

CREATE TABLE ITEM_CATEGORY (
    ITEM_CATEGORY_ID BIGINT NOT NULL AUTO_INCREMENT,
    ITEM_CATEGORY_NAME VARCHAR(128) NOT NULL,
    PARENT_CATEGORY_ID BIGINT,
    
    PRIMARY KEY (ITEM_CATEGORY_ID),
    
    CONSTRAINT FK_ITEM_CATEGORY_PARENT
        FOREIGN KEY (PARENT_CATEGORY_ID)
        REFERENCES ITEM_CATEGORY (ITEM_CATEGORY_ID)
        ON DELETE SET NULL,

    INDEX IDX_ITEM_CATEGORY_PARENT (PARENT_CATEGORY_ID)
);

CREATE TABLE IMAGE_UPLOAD_STATUS (
    IMAGE_UPLOAD_STATUS_ID CHAR(36) NOT NULL,
    IMAGE_UPLOAD_STATUS_RESULT VARCHAR(10) NOT NULL,
    IMAGE_UPLOAD_STATUS_CREATED_DATE DATETIME(6) NOT NULL,
    
    PRIMARY KEY (IMAGE_UPLOAD_STATUS_ID)
);

CREATE TABLE FAVORITE_ITEM (
    FAVORITE_ITEM_ID BIGINT NOT NULL AUTO_INCREMENT,
    USER_ID VARCHAR(64) NOT NULL,
    ITEM_ID VARCHAR(36) NOT NULL,
    CREATED_DATE DATETIME(6),
    
    PRIMARY KEY (FAVORITE_ITEM_ID),
    
    UNIQUE KEY UK_FAVORITE_ITEM_USER_ITEM (USER_ID, ITEM_ID)
);

CREATE TABLE TRANSACTION_HISTORY (
    TRANSACTION_ID BIGINT NOT NULL AUTO_INCREMENT,
    TRANSACTION_ITEM_SELLER_ID VARCHAR(64) NOT NULL,
    TRANSACTION_ITEM_ID CHAR(36) NOT NULL,
    TRANSACTION_ITEM_TITLE VARCHAR(128) NOT NULL,
    TRANSACTION_ITEM_CATEGORY VARCHAR(128) NOT NULL,
    TRANSACTION_ITEM_TARGET_PRICE BIGINT UNSIGNED NOT NULL,
    TRANSACTION_ITEM_MAIN_IMAGE VARCHAR(41) NOT NULL,
    TRANSACTION_ITEM_BUYER_ID VARCHAR(64),
    TRANSACTION_ITEM_COMPLETION_TIME DATETIME(6) NOT NULL,
    
    PRIMARY KEY (TRANSACTION_ID),
    
    INDEX IDX_TRANSACTION_HISTORY_SELLER_ITEM (
        TRANSACTION_ITEM_ID,
        TRANSACTION_ITEM_SELLER_ID
    )
);

CREATE TABLE BUY_HISTORY (
    BUY_ID BIGINT NOT NULL AUTO_INCREMENT,
    BUY_ITEM_BUYER_ID VARCHAR(64) NOT NULL,
    BUY_ITEM_ID CHAR(36) NOT NULL,
    BUY_ITEM_TITLE VARCHAR(128) NOT NULL,
    BUY_ITEM_CATEGORY VARCHAR(128) NOT NULL,
    BUY_ITEM_TARGET_PRICE BIGINT UNSIGNED NOT NULL,
    BUY_ITEM_MAIN_IMAGE VARCHAR(41) NOT NULL,
    BUY_ITEM_SELLER VARCHAR(64) NOT NULL,
    BUY_ITEM_COMPLETION_TIME DATETIME(6) NOT NULL,
    
    PRIMARY KEY (BUY_ID),
    
    INDEX IDX_BUY_HISTORY_BUYER_ITEM (
        BUY_ITEM_BUYER_ID,
        BUY_ITEM_ID
    )
);

```
- **MongoDB** voor query-databases in Catalog Contexten, gebaseerd op geprojecteerde domeinevents

	Hoewel MongoDB een NoSQL-database is en dus schemaloos werkt, zijn de schema’s in een Mongoose-achtige stijl opgesteld om de structuur van elk document beter begrijpelijk te maken voor de lezer.

	```json
{
  "collection": "SALE_LIST",
  "schema": {
    "_id": {
      "bsonType": "string",
      "description": "Unique identifier in UUID format (36 characters)"
    },
    "title": {
      "bsonType": "string",
      "maxLength": 128,
      "description": "Product title"
    },
    "category": {
      "bsonType": "string",
      "maxLength": 128,
      "description": "Product category"
    },
    "targetPrice": {
      "bsonType": "long",
      "minimum": 1,
      "description": "Target selling price"
    },
    "discountable": {
      "bsonType": "bool",
      "description": "Whether the price is negotiable"
    },
    "mainImage": {
      "bsonType": "string",
      "maxLength": 41,
      "description": "Main image file name"
    },
    "status": {
      "bsonType": "string",
      "enum": ["AVAILABLE", "RESERVED", "SOLD_OUT", "DELETED"],
      "description": "Sales status (Enum)"
    },
    "createdDate": {
      "bsonType": "date",
      "description": "Creation timestamp"
    },
    "lastModifiedDate": {
      "bsonType": "date",
      "description": "Last modified timestamp"
    }
  }
}
```

```json
{
  "collection": "SALE_ITEM",
  "schema": {
    "_id": {
      "bsonType": "string",
      "description": "Unique identifier in UUID format (36 characters)"
    },
    "ownerId": {
      "bsonType": "string",
      "maxLength": 64,
      "description": "Owner (seller) ID"
    },
    "title": {
      "bsonType": "string",
      "maxLength": 128,
      "description": "Product title"
    },
    "category": {
      "bsonType": "string",
      "maxLength": 128,
      "description": "Product category"
    },
    "targetPrice": {
      "bsonType": "long",
      "minimum": 1,
      "description": "Target selling price"
    },
    "discountable": {
      "bsonType": "bool",
      "description": "Whether the price is negotiable"
    },
    "text": {
      "bsonType": "string",
      "maxLength": 1024,
      "description": "Detailed description"
    },
    "images": {
      "bsonType": "array",
      "items": {
        "bsonType": "string"
      },
      "description": "List of additional image paths"
    },
    "status": {
      "bsonType": "string",
      "enum": ["AVAILABLE", "RESERVED", "SOLD_OUT", "DELETED"],
      "description": "Sales status (Enum)"
    },
    "createdDate": {
      "bsonType": "date",
      "description": "Creation timestamp"
    },
    "lastModifiedDate": {
      "bsonType": "date",
      "description": "Last modified timestamp"
    }
  }
}

	```
- **MongoDB** als event store voor de Sale Context, met een schema gericht op event logging en aggregaatstate reconstructie
    
- **Kafka** topicschema’s voor event streaming en communicatie
    