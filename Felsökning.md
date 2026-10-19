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
### 0. Gör ingen skada (Backups & Snapshots)

Innan du börjar ändra konfigurationer, ta bort filer eller köra reparationskommandon:
Om du är i en virtuell miljö (VMware/Hyper-V/VirtualBox) – ta en snapshot! I produktion: Säkerställ att det finns en backup.

### 1. Informationsinhämtning: Läs systemets "Svarta låda"

Gissa aldrig vad felet är, låt operativsystemet berätta det för dig.

| Mål | Ubuntu (Linux) | Windows 11 / Server |
| --- | --- | --- |
| **Allmänna systemloggar** | `journalctl -f` (följ live)<br>

<br>`tail -f /var/log/syslog` | **Event Viewer** -> Windows Logs -> System / Application |
| **Säkerhet & Inloggning** | `tail -f /var/log/auth.log` | **Event Viewer** -> Security (Leta efter ID 4624/4625) |
| **Specifik tjänst/app** | `journalctl -u sshd --since yesterday` | PowerShell: `Get-WinEvent -LogName Application -MaxEvents 50` |
| **Historiska krascher** | `journalctl -b -1` (loggar från förra uppstarten) | **Event Viewer** -> System (Filtrera på "Critical" eller "Error") |

---

### 2. Prestanda och Resursövervakning

När maskinen är seg, fryser eller beter sig konstigt.

| Mål | Ubuntu (Linux) | Windows 11 / Server |
| --- | --- | --- |
| **CPU & RAM (Live)** | `top` eller `htop` | **Task Manager** eller **Resource Monitor** (`resmon`) |
| **Kontrollera RAM** | `free -h` | PowerShell: `Get-Process | Sort-Object WorkingSet -Descending` |
| **Disk & Utrymme** | `df -h`<br>

<br>`du -sh /var/log/*` (hitta stora filer) | PowerShell: `Get-Volume` |
| **Detaljerad process** | `strace -p <PID>` | **Sysinternals Process Monitor (ProcMon)** |

---

### 3. Nätverk Grundläggande (Anslutningar & Portar)

*Verification:* Kan du nå maskinen lokalt men inte externt? Då är det nästan alltid brandväggen.

| Mål | Ubuntu (Linux) | Windows 11 / Server |
| --- | --- | --- |
| **Lyssnar tjänsten?** | `ss -tuln` | `netstat -ano` (Kolla PID i Task Manager) |
| **Testa port** | `curl -v http://IP` eller `telnet IP port` | PowerShell: `Test-NetConnection -ComputerName IP -Port 80` |
| **Brandväggsstatus** | `sudo ufw status verbose` | PowerShell: `Get-NetFirewallProfile` |
| **IP-konfiguration** | `ip a` | `ipconfig /all` |

---

### 4. Nätverk Avancerat (OSI, DNS & DHCP)

Följ alltid OSI-modellen: Börja nerifrån (Lager 1: Fysisk kabel/VM-nätverkskort) och jobba dig uppåt mot applikationen (Lager 7).

| Mål | Ubuntu (Linux) | Windows 11 / Server |
| --- | --- | --- |
| **Kolla DNS-uppslag** | `dig domän.se` eller `nslookup domän.se` | PowerShell: `Resolve-DnsName domän.se` eller `nslookup` |
| **Rensa DNS-cache** | `sudo systemd-resolve --flush-caches` | `ipconfig /flushdns` |
| **Förnya IP (DHCP)** | `sudo dhclient -r` (släpp) sedan `sudo dhclient` | `ipconfig /release` sedan `ipconfig /renew` |
| **Spåra trafikväg** | `traceroute IP` | `tracert IP` |

*Verification:* Om `ping 8.8.8.8` (Google) fungerar, men `ping google.com` misslyckas, har du ett DNS-problem.

---

### 5. Domän- och Katalogtjänster (Active Directory & GPO)

Primärt för Windows Server-miljöer när inloggningar misslyckas eller rättigheter inte appliceras.

| Mål | Kommando / Verktyg (Windows) |
| --- | --- |
| **Kolla domänhälsa** | `dcdiag` (Körs på domänkontrollanten för att hitta AD-replikeringsfel) |
| **Framtvinga GPO** | `gpupdate /force` (Kör på klienten för att hämta senaste policyn) |
| **Vilka GPO:er gäller?** | `gpresult /r` (sammanfattning) eller `gpresult /h report.html` (detaljerad vy) |
| **Kolla domän-trust** | PowerShell: `Test-ComputerSecureChannel -Verbose` (Om False, datorn har tappat AD-kopplingen) |

*Verification:* Om `Test-ComputerSecureChannel` returnerar "True" är den säkra kanalen mellan datorn och domänkontrollanten intakt.

---

### 6. Pakethantering och Uppdateringar ("Dependency Hell")

När installationer avbryts, uppdateringar fastnar eller paket är korrupta.

| Mål | Ubuntu (Linux) | Windows 11 / Server |
| --- | --- | --- |
| **Laga trasiga paket** | `sudo apt --fix-broken install` | Kör "Windows Update Troubleshooter" i inställningar |
| **Tvinga omkonfiguration** | `sudo dpkg --configure -a` | `sfc /scannow` (Reparerar korrupta systemfiler) |
| **Rensa paket-cache** | `sudo apt clean` och `sudo apt autoremove` | Radera innehållet i `C:\Windows\SoftwareDistribution\Download` |
| **Reparera OS-avbild** | *(Krävs sällan på samma sätt i Linux)* | `DISM /Online /Cleanup-Image /RestoreHealth` |

*Verification:* Kör uppdateringskommandot igen (`apt update && apt upgrade` eller sök efter Windows Updates). Går det igenom utan felkoder är beroendeproblemet löst.

---

### 7. Uppstartsproblem och Krascher (BSoD/Kernel Panic)

När systemet vägrar starta upp till inloggningsskärmen.

| Mål | Ubuntu (Linux) | Windows 11 / Server |
| --- | --- | --- |
| **Felsäkert läge** | Håll inne `Shift` eller `Esc` vid boot för att nå GRUB. Välj "Advanced options" -> "Recovery mode". | Tryck F8 (äldre) eller avbryt uppstarten 3 ggr för att nå "Advanced Startup Options" -> Safe Mode. |
| **Trasigt filsystem?** | Boota från Live-USB. Kör `sudo fsck /dev/sdX` på den trasiga partitionen. | Starta i Recovery. Öppna CMD och kör `chkdsk C: /f /r`. |
| **Analysera kraschdump** | Läs `/var/crash/` eller granska förra booten med `journalctl -b -1`. | Öppna filer i `C:\Windows\Minidump` med programmet **WinDbg** för att se vilken drivrutin som orsakade BSoD. |

---

### 8. Rättigheter och Säkerhet (Den tysta mördaren)

När en tjänst borde fungera men får "Access Denied".

| Mål | Ubuntu (Linux) | Windows 11 / Server |
| --- | --- | --- |
| **Kolla filägare** | `ls -la /sökväg/till/fil` | Högerklicka -> Egenskaper -> Säkerhet |
| **Sätt rättigheter** | `chown användare:grupp fil` och `chmod 755 fil` | Redigera NTFS-rättigheter via GUI eller `icacls` i CMD |
| **Kolla säkerhetsmodul** | Leta efter `DENIED` i dmesg: `dmesg | grep apparmor` | Starta terminalen "Som Administratör" (UAC blockerar annars vissa skript) |

---

### 9. AI som Felsökningsmentor

Som student är AI ditt bästa verktyg för att förstå *varför* något är trasigt:

1. **Avkoda felmeddelanden:** Klistra in obegripliga BSoD-hexkoder (t.ex. `0x0000007B`) eller Linux-stacktraces och be om orsaken.
2. **Kommando-anatomi:** Be AI förklara flaggor. Ex: *"Vad gör flaggorna i `chkdsk /f /r`?"*
3. **Automatisering:** Be AI skriva basen för ett PowerShell- eller Bash-skript för att samla in loggar automatiskt.
4. **SÄKERHETSREGEL:** Byt alltid ut riktiga IP-adresser, domännamn, användarnamn och lösenord mot `[MASKERAT]` innan du klistrar in loggar i en AI.
