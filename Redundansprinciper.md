# Redundansprinciper

### RAID-lägen (Redundant Array of Independent Disks)

Sammanfattning av **RAID-lägen** (Redundant Array of Independent Disks) och hur olika konfigurationer fungerar.

* **RAID 0 (Striping):** Delar upp data över minst två diskar. Det ger **hög prestanda och snabb läs- och skrivhastighet**, men helt **ingen redundans** eller feltolerans – om en disk går sönder förloras all data.


* **RAID 1 (Spegling):** Speglar exakt samma data på två eller fler diskar. Detta ger **hög datatäthet och säkerhet** (om en disk kraschar finns en komplett kopia kvar), men lagringskapaciteten halveras eftersom utrymmet dubbleras.


* **RAID 5 (Striping med distribuerad paritet):** Kräver minst tre diskar. Data och paritetsinformation sprids ut över diskarna, vilket ger en bra balans mellan **prestanda, lagringskapacitet och felaktighetsskydd** (systemet klarar att en disk går sönder utan dataförlust).


* **RAID 10 (eller RAID 1+0):** En kombination av RAID 0 och RAID 1 som kräver minst fyra diskar. Den erbjuder både **hög prestanda** (genom striping) och **hög säkerhet** (genom spegling), men är dyrare i drift eftersom halva lagringskapaciteten går åt till spegling.


### Datacenter-redundans

Redundans i datacenter handlar om att ha extra system och utrustning för att säkerställa att driften inte avbryts om en komponent går sönder:

* **N (Need):** Betyder att det finns exakt så mycket kraft eller utrustning som krävs för att driva anläggningen under normal drift, helt utan extra marginaler eller reservdelar. Om en komponent slutar fungera uppstår ett avbrott.
* **N+1:** Innebär att det finns den grundläggande kapacitet som behövs (N) plus minst en extra enhet (en reserv) för att ta över om en primär komponent fallerar.
* **2N:** Betyder fullständig dubblering (två oberoende system, ofta kallat A- och B-sidor). Om det ena systemet slutar fungera helt kan det andra ta över omedelbart utan avbrott.
* **2N+1:** Kombinerar full dubblering (2N) med extra reservkapacitet (+1), vilket ger extremt hög tillförlitlighet och säkerhet.
