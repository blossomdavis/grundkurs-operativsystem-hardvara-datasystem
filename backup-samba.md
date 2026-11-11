# Hantering av backup med samba

Labbövning: Hantering av backups med samba-filserver

I denna övning ska vi använda vår Ubuntu filserver VM som lagring för våra backups som tas av vår Windows Server. 

Mål

I den här övningen ska du:

    Konfigurera en dedikerad backup-användare på Ubuntu SMB-filservern.

    Montera den delade Samba-mappen som en nätverksenhet i Windows Server.

    Använda Windows Server Backup (WSB) / PowerShell för att automatiskt ta en backup av en viktig systemdel (t.ex. systemtillstånd/System State eller en specifik produktionskatalog) och spara den direkt på Ubuntu-filservern.

 
1. Skapa en dedikerar samba användare i ubuntu

Detta gör du på samma sätt som vi övat på tidigare. Du kan exempelvis döpa användaren till "backupuser" och använda denna som användarens delade mapp "/srv/samba/backups".

2. Montera din samba-share permanent på din Windows SErver VM

    Öppna PowerShell som Administratör på Windows Server.

    Spara inloggningsuppgifterna för Ubuntu-användaren i Windows Credential Manager så att monteringen överlever omstarter:

    cmdkey /add:192.168.1.50 /user:backupuser /pass:SäkertLösenord123

    (Ersätt 192.168.1.50 med din Ubuntu-servers IP-adress om du har valt en annan)

    Montera mappen som en nätverksenhet med powershell (t.ex. enheten B:):

    New-PSDrive -Name "B" -PSProvider FileSystem -Root "\\192.168.1.50\backups" -Persist

3. Skapa en test mapp för backup

På din Windows Server, skapa en mapp i "Documents" med 3 test filer inuti (tomma eller påhittade filer och innehåll).

 
4. Ta automatiska backups med Windows Server Backup (WSB)

Om ni inte hittar "Windows Server Backup"-verktyget i Server Manager -> Tools, så kan ni ladda ner det i powershell enligt länken.

Ladda ner Windows Server BackupLinks to an external site.


1. Gå in i Windows Server Backup och klicka på "Local BAckup" till vänster.

2. Klicka sen på Backup schedule till höger och skapa ett passande schema för backups på dina testfiler. Glöm inte att spara dom på vår ubuntu filserver.
