# 6. Uitvoeringsmethode

## 6.1. Uitvoerstappen

### 🌐 GCP(Google Cloud Platform) Omgeving (met de webconsole)

 Hoe je zonder `gcloud CLI`, alleen met de GCP-webconsole, een GKE-cluster, statisch IP en Cloud Build CI/CD triggers aanmaakt en beheert.


#### ✅ 1. GKE-cluster aanmaken

1. Ga naar de GCP-console → klik op **Kubernetes Engine**: [link](https://console.cloud.google.com/kubernetes/list)
    
2. Klik op **[Cluster maken]**
    
3. Stel in:
    
    - **Clusternaam**: bijvoorbeeld `directdeal-cluster`
        
    - **Locatietype**: kies bijv. `eenzone` (bv. `europe-west12-a`)
        
    - **Releasekanaal**: `Regulier` of `Stabiel`
        
    - **Aantal nodes**: 1 à 2 aanbevolen
        
4. Klik op **Maken**

#### ✅ 2. Statisch IP-adres reserveren

> Dit IP-adres gebruik je later in je Kubernetes Service(nginx) manifest bij `loadBalancerIP`.

1. Ga naar GCP-console → **VPC-netwerk > Externe IP-adressen**: [link](https://console.cloud.google.com/networking/addresses/list)
    
2. Klik op **[Statisch IP-adres reserveren]**
    
3. Stel in:
    
    - **Naam**: bijvoorbeeld `directdeal-static-ip`
        
    - **Regio**: dezelfde regio als GKE-cluster (bv. `europe-west12-a`)
        
    - **Netwerkservice-laag**: Premium
        
    - **IP-type**: IPv4
        
4. Klik op **Reserveren**


#### ✅ 3. Cloud Build trigger aanmaken (voor CI/CD)

> Elke commit op GitHub triggert automatisch een volledig CI/CD-proces, waarbij de code wordt gebouwd, getest en uitgerold zonder handmatige tussenkomst.

1. Ga naar GCP-console → **Cloud Build > Triggers**: [link](https://console.cloud.google.com/cloud-build/triggers)
    
2. Klik op **[Trigger maken]**
    
3. Stel in:
    
    - **Triggernaam**: bijvoorbeeld `trigger-directdeal-cloudbuild`
        
    - **Bronrepository**: GitHub → klik op **Verbinden** → link je GitHub-account (eenmalig)
        
    - **Repository selecteren**: kies je projectrepo
        
    - **Gebeurtenis**: `Push naar branch`
        
    - **Branch-regex**: `^main$` of je gebruikte branchnaam
        
    - **Buildconfiguratie**: `cloudbuild.yaml` gebruiken
        
4. Voeg omgevingsvariabelen toe (Substitutions):
    
    - `_DEPLOY_REGION`: je deployment region
        
    - `_GKE_CLUSTER`: `je cluster-id`
        
    - `_STATIC_IP`: je statische IP-adres
        
5. Klik op **Maken**
    

💡 Vanaf nu wordt bij elke push naar `main` automatisch de build en deployment gestart.

#### ✅ 4. Open de site
Ga naar: [http://<static_ip>:9000](http://<static_ip>:9000/)

---

### 🖥️ Lokaal in Linux-omgeving

> Test lokaal met minikube.


#### ✅ 1. Klonen van de repository
```bash
$> git clone https://github.com/nieuwmijnleven/directdeal.git
$> cd ./directdeal
```
---

#### ✅ 2. Minikube Installeren
Volg de instructies om Minikube op Ubuntu te installeren.
[Install Minikube on Ubuntu](https://phoenixnap.com/kb/install-minikube-on-ubuntu)


#### ✅ 3. Services starten
```bash
$> ./start-direct-deal-service.sh
```
> Dit proces kan langer duren, afhankelijk van de specificaties van uw systeem.
> U kunt de status van alle pods bekijken met het commando: 
```bash
$> kubectl get pods
```

#### ✅ 4. Open de site
Ga naar: [http://localhost:9000](http://localhost:9000/)

---


## 6.2. Demonstratie (schermafbeeldingen of video)

Hieronder enkele schermafbeeldingen van het werkende systeem:

> 🎥 Voor een volledige demo-video, zie de [README](https://github.com/nieuwmijnleven/directdeal#schermafbeeldingen)

---

