# Datacenter
Här är en sammanfattning vad ett Datacenter består av. 

### 1. Kärnan: Servrar (Servers)

Servrarna är "arbetshästarna" som kör applikationer, processar data och utför beräkningar. De delas huvudsakligen in i tre typer:

* **Rackservrar:** Designade för att passa i standardrack och maximera utrymmet.
* **Blade-servrar:** Ännu mer kompakta; flera enheter delar på kraft och kyla i ett gemensamt chassi, vilket passar bra för högdensitetsberäkningar. 
* **Tower-servrar:** Liknar vanliga stationära datorer och används i mindre datacenter eller för specifika uppgifter.

### 2. Nätverksinfrastruktur (Networking)

Datacentrets "nervsystem" som kopplar samman servrar och omvärlden:

* **Switchar:** Låter servrar kommunicera internt.
* **Routers:** Kopplar upp datacentret mot internet och andra externa nätverk.
* **Brandväggar (Firewalls):** Fungerar som säkerhetsbarriärer mot hot och obehörig trafik.
* **Load balancers (Lastbalanserare):** Fördelar nätverkstrafiken jämnt över flera servrar för att förbättra prestanda och pålitlighet.

### 3. Datalagring (Data Storage)
Datacentrets "minnesbank" för både kapacitet och snabbhet (bestående av både traditionella hårddiskar/HDD och snabba SSD-enheter)

* **SAN (Storage Area Network):** Höghastighetsnätverk dedikerat till lagring med blocknivååtkomst. 
* **NAS (Network-Attached Storage):** Filbaserad lagring tillgänglig över nätverket.
* **DAS (Direct-Attached Storage):** Lagring direktkopplad till en enskild server.

### 4. Stödsystem och infrastruktur (Facility & Support)

För att hålla igång utrustningen och miljön på ett säkert sätt krävs omfattande system:

* **UPS (Uninterruptible Power Supply):** Reservkraft som säkerställer kontinuerlig drift vid strömavbrott.
* **PDU (Power Distribution Unit):** Distribuerar ström effektivt till utrustningen i racken.
* **Kylsystem:** Avlägsnar den enorma värme som servrarna genererar för att hålla optimal drifttemperatur.
* **Serverrack, skåp och upphöjda golv:** Fysiska strukturer för att organisera utrustning samt optimera luftflöde och kabeldragning.

### 5. Hantering och säkerhet (Management & Security)

* **KVM-switchar:** Låter administratörer styra flera servrar via en enda skärm, mus och tangentbord. 
* **Miljöövervakning:** Håller koll på temperatur, luftfuktighet och ström.
* **Fysisk säkerhet:** Kameror, biometriska skannrar och passerkontroller för att skydda tillgångarna.


## Miljöstandarder och riktlinjer
Här är en sammanfattning av det viktigaste ur varje källtext/ämne:

### 1. EU Code of Conduct for Data Centre Energy Efficiency

* **Syfte:** Ett frivilligt initiativ från EU-kommissionen för att minska energiförbrukningen och miljöpåverkan i datacenter i takt med att branschen växer.
* **Innehåll:** Riktlinjer, rekommendationer och best practices för operatörer och tillverkare.
* **Fokusområden:** Omfattar energieffektivitet i hela anläggningen, från servrar och lagring till kyla, eldistribution och byggnadens utformning.

### 2. ISO 50001 Energy Management Standard

* **Syfte:** En internationell standard som hjälper organisationer att upprätta och underhålla ett strukturerat energiledningssystem (EnMS).
* **Innehåll:** Tillhandahåller en ram för att kontinuerligt förbättra energiprestandan, energieffektiviteten och energianvändningen.
* **Kärna:** Bygger på förbättringscykeln PDCA (*Plan-Do-Check-Act*) och hjälper företag att identifiera energitjuvar, sätta upp mål och följa upp sin energiförbrukning systematiskt.

### 3. Uptime Institute Tier Standard Overview

* **Syfte:** En global branschstandard för att utvärdera tillförlitlighet, tillgänglighet och prestanda hos datacenterinfrastruktur.
* **Innehåll:** Klassificerar datacenter i fyra nivåer (Tier I till Tier IV):
* **Tier I:** Grundläggande infrastruktur med viss redundans, känslig för planering och oplanerade avbrott.
* **Tier II:** Komponentredundans (delvis reservkapacitet), vilket ger något högre skydd mot avbrott.
* **Tier III:** Underhållsbar utan avbrott (*concurrently maintainable*), med flera oberoende distributionsvägar.
* **Tier IV:** Fel tolerant (*fault tolerant*), vilket innebär att enskilda fel eller komponentbortfall inte påverkar den löpande driften.



### 4. PUE & Data Center Metrics Guide

* **Syfte:** Att mäta och utvärdera energieffektiviteten i datacenter för att kunna driva dem mer hållbart.
* **Centrala begrepp:**
* **PUE (Power Usage Effectiveness):** Det absolut vanligaste nyckeltalet. Det visar förhållandet mellan den totala energimängden som går till datacentret och den energi som faktiskt används av IT-utrustningen. Ett värde nära 1,0 är idealiskt.
* **DCiE (Data Center infrastructure Efficiency):** PUE uttryckt i procent (inversen av PUE), som visar hur stor andel av energin som går till IT-lasten.


* **Mätmetoder:** Ger vägledning i hur man samlar in data och mäter på rätt sätt för att jämföra prestanda över tid eller mellan olika anläggningar.

#### 2 st åtgärder för att följa EU Code of Conduct for Data Centres

För att ett företag ska leva upp till EU Code of Conduct (CoC) för energieffektiva datacenter krävs konkreta åtgärder inom drift och teknik. Här är två centrala åtgärder som ofta krävs:

* **Optimering av kylning och temperaturintervall:**
* *Åtgärd:* Höja drifttemperaturen i datahallarna i enlighet med rekommendationerna från ASHRAE (eller EU CoC-riktlinjerna) istället för att överkyla utrymmena. Att till exempel hålla en stabil temperatur i det högre spannet (t.ex. upp mot 27°C eller mer i kylluften) minskar energiförbrukningen i kylanläggningen (kylmaskiner, fläktar och pumpar) dramatiskt.
* *Företagsnytta:* Ger direkt mätbar sänkning av datacentrets PUE-värde och lägre energikostnader.


* **Investering i och uppföljning av energieffektiv hårdvara samt avstängning av ineffektiva servrar ("Zombie servers"):**
* *Åtgärd:* Upprätta en regelbunden inventeringsprocess för att identifiera och stänga ner servrar som inte längre används eller som har extremt låg utnyttjandegrad. Vid nyinköp ska energieffektiva servrar prioriteras.
* *Företagsnytta:* Minskar både den direkta energianvändningen och behovet av kylkapacitet, vilket är ett av huvudkraven i EU-initiativets "Best Practices".

---

### De viktigaste skillnaderna mellan EU Code of Conduct och ISO 50001

| Egenskap | EU Code of Conduct for Data Centres | ISO 50001 |
| --- | --- | --- |
| **Typ av ramverk** | Frivilligt program / uppförandekod med tekniska riktlinjer. | Formell, internationell certifieringsstandard. |
| **Målgrupp** | Specifikt utformat för **datacenteroperatörer och tillverkare**. | **Alla typer av organisationer** (industri, kontor, sjukhus, datacenter etc.). |
| **Fokusområde** | Mycket detaljerat kring just datacenterteknik (serverarkitektur, kyla, UPS, eldistribution). | Processinriktat ledningssystem för hela företagets energistyrning (policy, mål, rutiner, uppföljning). |
| **Kontroll / Certifiering** | Självutvärdering och årlig rapportering till EU-kommissionen för att behålla partnerstatus. | Kräver extern revision och certifiering av ackrediterat organ. |

---


## Fysisk och digital säkerhetsinfrastruktur för datacenter

Här är en sammanfattning om hur man designar en robust och säker fysisk samt digital säkerhetsarkitektur för datacenter. Den fokuserar på modeller, strategier och ramverk snarare än bara en enkel checklista.

### 1. Kärnmål och vägledande principer

* **De två pelarna:**
1. *Datasäkerhet:* Att skydda datan till varje pris.
2. *Serviceavailability (driftstabilitet):* Att säkerställa kontinuerlig tillgång till infrastruktur som el, kyla, temperaturkontroll och övervakning – data gör ingen nytta om den inte är tillgänglig.


* **Vägledande principer:**
* **Riskbaserat angreppssätt:** Identifiera, prioritera och hantera risker systematiskt.
* **Security by design:** Säkerheten ska integreras redan från planering och val av webbplats, inte läggas till som en eftertanke i slutet.
* **Ständig förbättring:** Arbeta efter PDCA-cykeln (*Plan-Do-Check-Act*) eftersom hotbilder ständigt förändras.



### 2. Skiktad säkerhet (Defense in Depth)

Datorcentrets säkerhet delas upp i fem skyddslager för att fördröja och stoppa obehöriga i flera steg:

* **Lager 1: Områdesskydd och perimetern:** Strategiskt val av plats (undvika högriskområden eller uppenbara profileringar), höga stängsel/murar (minst 7–10 fot), anti-klätterskydd, övervakningskameror med mörkerseende (IR/termisk) samt laser- eller rörelsesensorer.
* **Lager 2: Byggnadens skal och entréer:** Minsta möjliga antal in- och utgångar och användning av så kallade **man traps** (slussar med dubbla dörrar för att förhindra "tailgating" och isolera misstänkta personer). Bakgrundskontroller (BGV), rollbaserad åtkomstkontroll (RBAC) och principen om minsta behörighet (*least privilege*).
* **Lager 3: Datahallen (hjärtat):** Multifaktorsautentisering (MFA) med biometriska lösningar (som iris eller ansiktsigenkänning), 24/7-övervakning utan döda vinklar och kopplade larm.
* **Lager 4: Rack- och gångnivå:** Individuella smarta dörrar och lås på serverracken, säkra skåp samt dolt och skyddat kablage för att förhindra skadegörelse.
* **Lager 5: IT- och cybersäkerhet:** Systemintegritet, brandväggar, datakryptering, sårbarhetshantering och nätverksskydd.

### 3. Åtkomstkontroll (Authentication Toolkit)

Autentisering bygger på tre grundläggande faktorer som kombineras för att höja säkerheten:

1. *Något du kan* (t.ex. lösenord – svagast om det används ensamt).
2. *Något du har* (t.ex. passerkort).
3. *Något du är* (t.ex. biometri som fingeravtryck eller ansikte).
Genom att kombinera dessa (multifaktorsautentisering) säkerställs att stulna lösenord eller kort inte räcker för att ta sig in. 

### 4. Ramverk och operativ excellens

* **Standarder:** Videon nämner ramverk som **ISO/IEC 27001** för informationssäkerhet samt **NIST SP 800-53** som innehåller omfattande säkerhetskontroller. 
* **Drift och revision:** Säkerhetsavdelningen måste arbeta med strikt förändringshantering (*change management*), regelbundna katastrofövningar och incidentrespons samt återkommande säkerhetsrevisioner. 

