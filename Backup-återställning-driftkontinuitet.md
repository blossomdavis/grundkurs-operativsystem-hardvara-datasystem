# Backup, återställning & driftkontinuitet

## 1. Grundläggande begrepp & strategier

### Recovery Time Objective (RTO) vs. Recovery Point Objective (RPO)

RTO och RPO är de två centrala mätvärdena inom katastrofåterställning (Disaster Recovery). De sätter kraven på hur snabbt ett system måste återställas och hur mycket dataförlust verksamheten kan tolerera.

* **RPO (Recovery Point Objective) – Tillåten dataförlust:**
RPO mäter tid **bakåt** från det att ett avbrott eller en krasch inträffar. Det definierar den maximala tidsrymd av dataförändringar som får gå förlorad sedan den senaste säkerhetskopian.
* *Exempel:* Om RPO är 4 timmar måste säkerhetskopiering ske minst var fjärde timme. Om systemet kraschar kl. 12:00 och senaste backupen togs kl. 10:00 har 2 timmars data gått förlorad, vilket är godkänt enligt marginalen.


* **RTO (Recovery Time Objective) – Tillåten återställningstid:**
RTO mäter tid **framåt** från avbrottstillfället. Det anger hur lång tid det maximalt får ta från att felet uppstår till att systemet och tjänsten är fullt återställda och i drift igen.
* *Exempel:* Om RTO är 2 timmar måste IT-avdelningen ha återställt systemet och återupptagit driften inom 2 timmar från avbrottet.



**Skillnaden i korthet:** RPO avser **dataförlust** (vad som förloras), medan RTO avser **downtime** (hur länge systemet ligger nere).

### 3-2-1 Backup-regeln

3-2-1-regeln är den klassiska branschstandarden för backup-arkitektur. Syftet är att eliminera enskilda felkällor (*Single Points of Failure*).

1. **3 kopior av datan:** Behåll ett original samt minst två separata säkerhetskopior.
2. **2 olika lagringsmedia:** Lagra kopiorna på minst två olika typer av medium (t.ex. lokal NVMe/SSD och en nätverkslagring/NAS eller bandenhet) för att skydda mot hårdvarufel kopplade till en specifik teknik.
3. **1 kopia Off-site:** Förvara minst en säkerhetskopia på en annan fysisk plats eller i molnet. Detta skyddar mot lokala katastrofer som brand, översvämning, stöld eller ransomware-angrepp i det lokala nätverket.

*(Modern utökning: **3-2-1-1-0**, där den extra 1:an står för en oföränderlig/frånkopplad kopia (Immutable/Air-gapped) och 0 står för noll fel vid automatisk återställningsverifiering).*

---

## 2. Backup-typer och tekniker

### Full, Differensierad & Inkrementell backup

Säkerhetskopiering delas in i tre huvudtyper som balanserar lagringsutrymme, tidsåtgång vid backup och återställningstid (RTO).

* **Full backup (Fullständig):** Kopierar all vald data vid varje tillfälle.
* *Fördel:* Enklast och snabbast återställning – endast en enda backupfil eller datauppsättning behövs.
* *Nackdel:* Tar längst tid att utföra och kräver störst lagringsutrymme.


* **Differensierad backup (Differential):** Kopierar all data som ändrats sedan den *senaste fullständiga* backupen.
* *Fördel:* Snabbare återställning än inkrementell; kräver endast den senaste fullständiga backupen plus den senaste differensierade backupen.
* *Nackdel:* Storleken på backupen växer successivt för varje dag fram till nästa fullständiga backup.


* **Inkrementell backup (Incremental):** Kopierar endast data som har ändrats sedan den *senaste säkerhetskopieringen* (oavsett om det var en full eller inkrementell köring).
* *Fördel:* Snabbast att utföra och kräver minst lagringsutrymme per backup-tillfälle.
* *Nackdel:* Mest komplex återställning med högre RTO. För att återställa krävs den senaste fullständiga backupen samt *samtliga* efterföljande inkrementella delar i exakt ordning.



| Backup-typ | Utförandetid | Lagringsbehov | Återställningskomplexitet (RTO) | Krävs vid återställning |
| --- | --- | --- | --- | --- |
| **Full** | Lång | Högt | Låg (Snabbast) | Endast sista Full |
| **Differensierad** | Medel | Medel | Låg/Medel | Sista Full + Sista Differensierad |
| **Inkrementell** | Snabb | Lågt | Högre (Långsammare) | Sista Full + Alla mellanliggande Inkrementella |

### Linux Backup-verktyg

För Linux-miljöer används både inbyggda standardverktyg och dedikerade lösningar med avancerad funktion:

* **`rsync` (Remote Sync):**
Verktyg för filbaserad synkronisering lokalt eller över nätverk (via SSH). Använder en delta-överföringsalgoritm som endast skickar de delar av filen som faktiskt ändrats.
* *Bäst för:* Filspegling, enkla skriptade säkerhetskopior och lokala eller fjärrstyrda datakopior.


* **`tar` (Tape Archive):**
Klassiskt verktyg som samlar filer och kataloger till en enskild arkivfil (`.tar`), ofta kombinerat med komprimering (`gzip`, `xz`). Bevarar filrättigheter och katalogstrukturer exakt.
* *Bäst för:* Manuella arkiveringar, ögonblicksbilder av konfigurationsfiler och paketpaketering.


* **`borg` (BorgBackup):**
Modert backup-verktyg med inbyggd blockbaserad deduplicering, autentisering och AES-256-kryptering. Borg delar upp filer i variabla block och lagrar endast unika block, vilket minskar lagringsbehovet vid historisk säkerhetskopiering.
* *Bäst för:* Regelbunden, krypterad och utrymmeseffektiv säkerhetskopiering till lokala eller avlägsna servrar.

---

## 3. Driftkontinuitet och verifierad återställning

### Beredskapsplanering (Disaster Recovery Planning)

Disaster Recovery Planning (DRP) omfattar de dokumenterade processer och rutiner som krävs för att återställa IT-infrastrukturen vid allvarliga incidenter såsom cyberangrepp, hårdvaruhaverier eller naturkatastrofer.

* **Verksamhetsanalys (BIA - Business Impact Analysis):** Identifiering av kritiska system och fastställande av specifik RTO och RPO för varje enskild tjänst.
* **Återställningsinstruktioner (Runbooks):** Tydliga steg-för-steg-instruktioner för hur servrar, nätverk och databaser återställs. Instruktionerna måste vara skrivna så att de kan utföras under hög stress eller av ersättande personal.
* **Ansvars- och kommunikationskedja:** Fastställda roller för vem som har mandat att utlysa katastrofläge, vem som ansvarar för tekniska åtgärder samt hur kommunikation sker internt och externt.

### Verifierad återställning (Validate Recovery Readiness)

Säkerhetskopiering är inte fullständig förrän återställningsförmågan har verifierats. Kontinuerlig testning säkerställer att backup-datan faktiskt är användbar.

* **Automatiserade återställningstester:** Användning av verktyg som automatiskt återställer säkerhetskopior i en isolerad sandlådemiljö, startar upp virtuella maskiner och genomför kontroll av applikations- och databasintegritet.
* **Regelbundna DR-övningar:** Genomförande av planerade skarpa eller simulerade återställningsövningar för att mäta om den faktiska återställningstiden matchar organisationens uppsatta RTO- och RPO-mål.
* **Dataintegritets- och nyckelhantering:** Löpande validering av att säkerhetskopior inte har korrumperats (via checksummor) samt verifiering av att nödvändiga krypteringsnycklar och behörigheter finns tillgängliga vid en återställning.

---

#### Övning 1 - Simulera backupdisk i vår VM

För att simulera en separat backupdisk i vår Ubuntu VM så skapar vi en extra virtuell disk som "Ubuntu använder" som övnings- och backupdisk. Den här disken är en fil på värddatorn, inte en fysisk disk. I VirtualBox skapas den via Virtual Media Manager, och sedan ansluts den till VM:ens lagringsstyrenhet.

#### 1. Skapa den virtuella disken i VirtualBox & koppla in den

Stäng av Ubuntu-VM:en helt – pausa eller spara inte bara dess tillstånd.

Öppna VirtualBox. Öppna Tools/Verktyg → Virtual Media Manager/Mediahanterare. I vissa versioner kan du också gå via VM:ens Settings/Inställningar → Storage/Lagring och välja att skapa en ny hårddisk.

Välj Hard Disks/Hårddiskar och klicka Create/Skapa.

Välj diskformat. VDI är ett bra val om disken bara ska användas i VirtualBox.

Välj Dynamically allocated/Dynamiskt allokerad för en övningsdisk. Då växer diskfilen på värddatorn efter hand, upp till den angivna maxstorleken. Vi skapar en mindre storlek för denna övning, välj 5 GB.

Ge disken ett tydligt namn, till exempel ubuntu-backup.vdi, och klicka Finish/Slutför

För att koppla in disken i VirtualBox markerar du Ubuntu VM:en och öppnar Settings/Inställningar → Storage/Lagring.

Markera VM:ens SATA Controller. (Om ingen SATA-styrenhet finns, lägg till en med " + "-knappen/ikonen för att lägga till lagringsstyrenhet/controller.)

Klicka på ikonen för att lägga till hårddisk bredvid styrenheten.

Välj Choose Existing Disk/Välj befintlig disk och välj ubuntu-backup.vdi som du nyss skapade. Alternativt kan du skapa disken härifrån direkt.

Bekräfta med OK och starta Ubuntu-VM:en.


#### 2. Skapa partition och formatera den virtuella disken i Ubuntu

För att kunna använda vår nya disk så måste vi preparera den genom att formatera den till ett passande filsystem och partitionera den. Varning: Formatering raderar innehållet på den valda partitionen. Kontrollera noga att du arbetar med den nya, tomma disken och inte någon annan disk.

1. Öppna Terminal och kör först:

`lsblk -o NAME,SIZE,FSTYPE,LABEL,MOUNTPOINTS`

Identifiera den nya disken genom storleken.
I exemplet nedan antar vi att den heter /dev/sdb och att den nya partitionen blir /dev/sdb1 (dina namn kan vara annorlunda.)


2. Skapa partitionen:

`sudo fdisk /dev/sdb`

I fdisk skriver du n för en ny partition och följer standardvalen för att använda hela disken. Skriv sedan w för att spara ändringarna.
 

3. Formatera därefter partitionen:

`sudo mkfs.ext4 -L BACKUP /dev/sdb1`

4. Skapa en monteringspunkt och montera den:

`sudo mkdir -p /mnt/backup
sudo mount /dev/sdb1 /mnt/backup`

5. Kontrollera att den är monterad:

`df -h /mnt/backup`

Kommandona för att partitionera, formatera och montera förutsätter att du byter ut enhetsnamnen (/dev/sdb) mot det som faktiskt visas hos dig i steg 1.

 
#### Övning 2 - Genomföra och verifiera återställning i VM

Mål: Praktiskt skapa en säkerhetskopia av en viktig katalog på din Ubuntu VM, simulera en dataförlust genom att radera katalogen, samt genomföra och verifiera återställningen enligt en strukturerad rutin (kursmål 10 och 12).
Huvudmoment och instruktioner

Skapa testdata

Skapa en eller flera kataloger, tex /home/student/viktig_data/ och lägg till tre testfiler med textinnehåll.

Genomför säkerhetskopiering (tar eller rsync)

Skapa ett komprimerat arkiv av katalogen och spara det på vår separata backup-disk (/mnt/backup/)

`sudo tar -cvzf /mnt/backup/viktig_data_$(date +%Y%m%m).tar.gz /home/student/viktig_data/`

Simulera incident (Dataförlust)

Radera ursprungskatalogen permanent:

`rm -rf /home/student/viktig_data/`

Återställ data & Verifiera integritet

Återställ arkivet till sin ursprungliga plats.

Verifiera att alla tre filer finns kvar och att innehållet i dem stämmer överens med originalfilerna.


##### Dokumentera rutinen
Dokumentera den tekniska rutinen för backup och återställning, så att arbetet kan utföras på samma sätt varje gång. Beskriv kort vem i verksamheten som ansvarar, hur backupen kontrolleras och var dokumentationen ska finnas.

Skriv en enkel instruktion för återställning från arkivet, till exempel i 3–5 steg:
