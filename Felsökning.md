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



# Fusk-blad

## 1. Informationsinhämtning: Läs systemets "Svarta låda"

Börja alltid här. Gissa aldrig vad felet är, låt operativsystemet berätta det för dig.

| Mål | Ubuntu (Linux) | Windows 11 / Server |
| --- | --- | --- |
| **Allmänna systemloggar** | `journalctl -f` (följ live)<br>

<br>`tail -f /var/log/syslog` | **Event Viewer** (Loggboken) -> Windows Logs -> System / Application |
| **Säkerhet & Inloggning** | `tail -f /var/log/auth.log`<br>

<br>(Sök efter "Failed password") | **Event Viewer** -> Security.<br>

<br>Leta efter Event ID **4624** (lyckad) / **4625** (misslyckad). |
| **Specifik tjänst/app** | `journalctl -u sshd --since yesterday` | PowerShell: `Get-WinEvent -LogName Application -MaxEvents 50` |
| **Hårdvara/Kärna** | `dmesg | tail` |

> **Pro-tips för Windows:** I Server-miljöer är PowerShell din bästa vän för loggar. Cmdleten `Get-WinEvent` är Windows motsvarighet till `journalctl`.

---

### 2. Prestanda och Resursövervakning

Om maskinen är seg, kraschar eller beter sig konstigt, kontrollera systemets "vitalparametrar".

| Mål | Ubuntu (Linux) | Windows 11 / Server |
| --- | --- | --- |
| **CPU & RAM (Live)** | `top` eller `htop` (lättare att läsa) | **Task Manager** (Ctrl+Shift+Esc) eller **Resource Monitor** (`resmon`) |
| **Kontrollera RAM** | `free -h` | PowerShell: `Get-Process | Sort-Object WorkingSet -Descending` |
| **Disk & Utrymme** | `df -h` (översikt)<br>

<br>`du -sh /var/log/*` (hitta stora filer) | Den här datorn (GUI)<br>

<br>PowerShell: `Get-Volume` |
| **Detaljerad process** | `strace -p <PID>` (fördjupad felsökning) | **Sysinternals Process Monitor (ProcMon)** |

---

### 3. Nätverk och Tjänster

När en maskin är igång men inte går att nå över nätverket.

*Verification:* Kan du nå maskinen lokalt men inte externt? Då är det nästan alltid brandväggen.

| Mål | Ubuntu (Linux) | Windows 11 / Server |
| --- | --- | --- |
| **Lyssnar tjänsten?** | `ss -tuln` | `netstat -ano` (Hitta PID och kolla i Task Manager vilken app det är) |
| **Testa port/uppkoppling** | `curl -v http://IP` eller `telnet IP port` | PowerShell: `Test-NetConnection -ComputerName IP -Port 80` |
| **Brandväggsstatus** | `sudo ufw status verbose` | PowerShell: `Get-NetFirewallProfile` eller GUI: "Windows Defender Firewall" |
| **Kolla IP-konfiguration** | `ip a` | `ipconfig /all` |

---

### 4. Rättigheter och Säkerhet (Den tysta mördaren)

Ett av de absolut vanligaste problemen inom IT-infrastruktur är att en tjänst eller användare saknar rättigheter att läsa/skriva till en fil, och felet visas inte alltid tydligt.

* **Ubuntu:**
* Kontrollera vem som äger filen: `ls -la /sökväg/till/fil`
* Ändra ägare/rättigheter: `chown användare:grupp fil` och `chmod 755 fil`
* *Glöm inte AppArmor!* Ubuntu använder säkerhetsmodulen AppArmor. Om en tjänst (t.ex. MySQL) vägrar starta trots rätt filrättigheter, kolla `dmesg` efter "apparmor="DENIED".


* **Windows:**
* Högerklicka på mappen -> Egenskaper -> Säkerhet (NTFS-rättigheter).
* Tänk på **UAC** (User Account Control). Även om du är inloggad som administratör i Windows måste du ibland aktivt välja "Kör som administratör" för att terminalen (PowerShell/CMD) ska få fulla rättigheter.



---

### 5. Använd AI som din Seniora Mentor (För studenter)

Eftersom du studerar är AI (ChatGPT, Claude, Copilot) ett fantastiskt verktyg för att accelerera ditt lärande, men det måste användas smart:

1. **Översätt obegripliga fel:** Får du en Windows Event-logg med en kryptisk hexadecimal felkod (t.ex. `0x80070005`) eller en lång Java-stacktrace i Ubuntu? Klistra in den i AI:n och be: *"Förklara detta fel enkelt och ge mig 3 steg för att felsöka det."*
2. **Lär dig kommandots anatomi:** Istället för att bara kopiera ett kommando, be AI:n: *"Förklara vad varje flagga i kommandot `netstat -ano | findstr :80` gör."*
3. **Bygg skript:** Be AI:n hjälpa dig skriva PowerShell-skript (Windows) eller Bash-skript (Ubuntu) för att automatisera din felsökning.
4. **SÄKERHETSREGEL:** Klistra **aldrig** in inloggningsuppgifter, API-nycklar eller konfidentiella nätverksstrukturer. Ersätt alltid IP-adresser och användarnamn med `[Dolt]` innan du skickar prompten.

---

### 6. Felsökningsloopen & Eskalering

När du fastnar (och det kommer du göra), följ denna tankemodell:

1. **Vad har ändrats nyligen?** (90% av alla fel beror på en nyligen genomförd uppdatering eller konfigurationsändring).
2. **Dela upp problemet (Divide & Conquer):** Fungerar det lokalt på servern? Om ja -> Nätverksfel. Om nej -> Server/Applikationsfel.
3. **Googla rätt:** Sök på *exakta* felmeddelanden inom citattecken, t.ex. `"Event ID 4625" "Windows Server 2022"`.
4. **Eskalera/Backa:** Om du lagt mer än 1-2 timmar utan framsteg på en labb:
* Återställ din snapshot och börja om (ofta snabbast i en lärmiljö).
* Skriv ner vad du har testat och fråga din lärare eller kurskamrater. Att kunna beskriva *vad* du redan felsökt är en extremt viktig färdighet i arbetslivet.
