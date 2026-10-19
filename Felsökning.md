# Felsökning

Här är en sammanfattning av var och en av de fem resurserna om loggning, felsökning och AI i Linux:

### 1. Grundläggande om loggar i Linux (Medium-artikel)

**Förståelse av Linux-loggar och rsyslog**
Artikeln ger en grundlig introduktion till hur loggar fungerar som det primära beviset vid felsökning och säkerhetsincidenter (SOC-analys).

* **Lagring och format:** Loggar sparas oftast i `/var/log` och delas upp efter funktion. Vissa loggar är i klartext (läses med `cat` eller `tail`), medan andra är binära (kräver specifika kommandon som `last` eller `who`).
* **Rsyslog:** Detta är hjärtat i Linux-loggningen – en demon som samlar in och dirigerar loggmeddelanden baserat på ett "Selector -> Action"-system. Den filtrerar loggar efter kategori (Facility) och prioritet (Priority) och bestämmer var de ska sparas eller om de ska skickas vidare till en extern server.
* **Logrotate:** Eftersom loggfiler ständigt växer används `logrotate` för att automatiskt arkivera och komprimera gamla loggar så att systemets lagringsutrymme inte tar slut, samtidigt som historisk data bevaras.

### 2. Var loggarna finns i Linux (Ubuntu-tutorial)

**Visning och övervakning av loggfiler**
Guiden fokuserar på praktisk hantering och lokalisering av loggar i Ubuntu.

* **Viktiga platser:** Den listar standardloggar som `auth.log` (inloggningar och sudo), `daemon.log` (bakgrundstjänster), `kern.log` (kärnan) och `syslog` (allmän systeminformation), samt applikationsloggar (t.ex. `/var/log/apache2/`).
* **Grafiska verktyg:** Visar hur man kan använda *GNOME System Log Viewer* (eller appen "Logs") för att smidigt läsa och övervaka loggar i ett grafiskt gränssnitt.
* **Kommandotolken (CLI):** Går igenom oumbärliga terminalkommandon som `cat` och `less` för att läsa filer, `head` och `tail` (speciellt `tail -f` för att övervaka loggar i realtid), samt `grep` för att söka efter specifika fel eller ord i loggfilerna.

### 3. Felsökning av system, applikationer och nätverk (Dev.to-artikel)

**Grundläggande Linux felsökningsguide**
En snabbreferens som listar de bästa verktygen för att isolera och lösa vanliga Linux-problem.

* **System och Processer:** Använd `journalctl` för loggar, `top` eller `ps aux` för att se processer, `strace` för att spåra systemanrop och `gdb` för att felsöka kompilerade program.
* **Nätverk:** Verktyg som `netstat` eller `ss` visar aktiva anslutningar, `ping`/`curl` testar uppkoppling och `iptables`/`ufw` används för att granska brandväggsregler.
* **Lagring och Minne:** `df` och `du` hjälper till att hitta vad som tar upp diskutrymme. `fsck` reparerar filsystem och `smartctl` kollar diskens hälsa. `free` och `vmstat` används för RAM-analys, medan `valgrind` kan upptäcka minnesläckor i kod.
* **Kärnan (Kernel):** Använd `dmesg` för att läsa meddelanden från operativsystemets kärna.

### 4. Användning av journalctl för logganalys (YouTube-video)

**Journalctl Basics (Learn Linux TV)**
Videon är en praktisk genomgång av `journalctl`, som är standardverktyget för att läsa loggar på distributioner som använder `systemd`.

* **Filtrering:** Istället för att se all loggdata på en gång (vilket blir överväldigande), kan man filtrera på specifika tjänster med växeln `-u` (t.ex. `journalctl -u sshd` för att bara se SSH-loggar).
* **Följa loggar:** Genom att använda `journalctl -f` ser man nya loggrader rulla in i realtid, vilket är perfekt vid live-felsökning.
* **Tidsbaserad sökning:** Man kan begränsa sökningen till specifika tidsramar med `--since` (t.ex. `--since yesterday` eller `--since "1 hour ago"`).
* **Lagringsutrymme:** Videon tipsar även om kommandot `journalctl --vacuum-size=500M` för att rensa bort gamla loggar och frigöra diskutrymme när loggdatabasen blivit för stor.

### 5. AI som hjälp i felsökning och logganalys (YouTube-video)

**Linux Troubleshooting with AI (Learn Linux TV)**
Videon diskuterar hur AI (som ChatGPT eller Claude) praktiskt kan lyfta ditt arbete som systemadministratör, med brasklappen att du *aldrig* ska dela känslig information och att AI ibland har fel.

* **Förklaring av loggar och output:** Du kan klistra in ett obegripligt felmeddelande från en loggfil, komplicerade filrättigheter eller output från ett terminalkommando och be AI förklara exakt vad det betyder och varför det kraschar.
* **Säkerhet & Konfiguration:** AI kan granska konfigurationsfiler (t.ex. SSH-konfigurationer) för att hitta säkerhetsbrister och föreslå härdning (hardening).
* **Kod och skript:** AI är fantastiskt för att hjälpa dig bygga komplexa terminalkommandon (genom "piping"), skriva systemd-timers, skapa anpassade bash-prompts eller lösa snåriga beroendeproblem (dependency hell) vid pakethantering.
* **Kontrollera svaren:** Guiden påminner starkt om att alltid ifrågasätta och verifiera AI:ns lösningar innan de körs i en produktionsmiljö, eftersom den ibland kan föreslå skadliga eller felaktiga åtgärder.
