# AI assisterad felsökning och verifiering

Mall för AI felsökningslogg:

## AI-Felsökningslogg

**Problem/Felmeddelande:** [Klistra in loggen från systemctl/journalctl]
* **Formulerad Prompt:** [Vad du frågade AI:n]
* **AI-verktygets förslag:** [Kort sammanfattning av AI:ns svar]
* **Kritisk granskning & Verifiering:** [Ser det rimligt ut, googla/testa commands]
  * Fungerade förslaget? (Ja/Nej)
  * Innehöll förslaget hallucinationer eller osäkra kommandon? (beskriv) 
* **Slutgiltig åtgärd:** [Vad du faktiskt körde i din VM för att lösa problemet]
---

## Praktisk AI-assisterad felsökning & verifiering

Mål: Praktiskt öva på att använda AI för att felsöka ett simulerat serverfel, verifiera förslaget i din virtuella maskin (VM) och dokumentera resultatet samt eventuella avvikelser.

#### 1. Simulera ett driftstopp

**Testa att framkalla ett stopp i SSH-tjänsten och lägg till ett syntaxsfel i /etc/ssh/sshd_config.**

**Skapa en säkerhetskopia av konfigurationen:** `sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak`
**Lägg till syntaxfel och starta om tjänsten:** `echo "OgiltigtKommando Ja" | sudo tee -a /etc/ssh/sshd_config`
`sudo systemctl restart ssh`

#### 2. Generera en diagnostik-prompt

**Kopiera felmeddelandet från systemctl status ssh eller journalctl -xeu ssh.
Formulera en strukturera prompt till valfritt AI-verktyg:**

*"Jag får följande felmeddelande på min Ubuntu Server vid start av SSH: [KLISTRA IN LOGG]. Vad är den mest sannolika orsaken och hur åtgärdar jag det?"*

#### 3. Kritisk granskning & Testning
**AI-svaret:** Loggen i din skärmdump visar att `systemd` försökte starta SSH-tjänsten fem gånger i rad (`restart counter is at 5`), men att varje försök misslyckades direkt (`exit-code`).

Den absolut vanligaste orsaken till detta är ett **syntaxfel eller stavfel i konfigurationsfilen** (`/etc/ssh/sshd_config`), en ogiltig sökväg till en nyckel eller att den port SSH försöker använda redan är upptagen. `systemd` talar bara om *att* tjänsten kraschade, inte *varför*.

Eftersom åtgärden kräver terminalkommandon följer du stegen nedan för att hitta och lösa felet:

1. **Testa SSH-konfigurationen direkt**
Kör följande kommando i terminalen för att tvinga `sshd` att läsa in konfigurationen och skriva ut det exakta felet på skärmen:
```bash
sudo sshd -t

```
*Verifiering:* Kommandot pekar ut exakt vilken rad och vilket värde i `/etc/ssh/sshd_config` (eller i `/etc/ssh/sshd_config.d/`) som orsakar kraschen. Om kommandot inte ger någon utskrift alls är konfigurationen felfri.
2. **Åtgärda felet i konfigurationsfilen**
Öppna filen som angavs i felmeddelandet (oftast huvudfilen):
```bash
sudo nano /etc/ssh/sshd_config

```
Gå till radnumret som visades i steg 1, rätta till stavfelet eller kommentera bort den felaktiga raden med `#`. Spara med `Ctrl + O`, tryck `Enter` och avsluta med `Ctrl + X`.
*Verifiering:* Kör `sudo sshd -t` igen. När kommandot avslutas helt utan textutskrift är syntaxen godkänd.
3. **Kontrollera portkonflikt (endast om `sshd -t` inte visade något fel)**
Om konfigurationen är felfri beror kraschen oftast på att en annan tjänst redan lyssnar på port 22 (eller den port du ställt in), eller att mappen `/run/sshd` saknas. Kontrollera detaljerade loggar för själva SSH-processen:
```bash
sudo journalctl -u ssh -n 20 --no-pager

```
*Verifiering:* Du ser det specifika felmeddelandet från `sshd`, till exempel `Bind to port 22 failed: Address already in use` eller `Missing privilege separation directory: /run/sshd`.
4. **Starta om SSH-tjänsten**
När felet är åtgärdat återställer du felräknaren och startar tjänsten på nytt:
```bash
sudo systemctl reset-failed ssh
sudo systemctl start ssh

```
*Verifiering:* Kör `sudo systemctl status ssh`. Statusen ska nu visas som **active (running)** i grön text.


**Utvärdera AI-verktygets lösningsförslag. Stämmer kommandona med din nuvarande OS-version?
Testkör åtgärden i din VM och verifiera om tjänsten startar.**
- Kommandona stämmde med min nuvarande OS och jag testkörde åtgärden och det gick felfritt. Inga frågetecken heller gällande kommandona, Bra att säkerhetskopiera innan man labbar, särskilt i en sådan övning. 


