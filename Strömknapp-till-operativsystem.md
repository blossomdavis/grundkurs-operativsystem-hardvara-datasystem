# Strömknapp till operativsystem

---

## 1. Uppstarten: Hur datorn hittar sig själv

Innan ett operativsystem (som Windows eller Linux) kan laddas måste hårdvaran veta vad den ska göra. Här finns två regelböcker: den gamla (Legacy) och den nya (UEFI).

|  | Den Gamla Skolan | Den Moderna Standarden (Idag) |
| --- | --- | --- |
| **System** | **Legacy BIOS** | **UEFI** (Unified Extensible Firmware Interface) |
| **Partitionering** | **MBR** (Master Boot Record) | **GPT** (GUID Partition Table) |
| **Begränsningar** | Max 2 TB diskar, max 4 partitioner. Ingen nätverksgrafik. | Stöd för enorma diskar, 128+ partitioner, grafiska gränssnitt. |
| **Hur den bootar** | Läser in den absolut första fysiska sektorn på hårddisken för att hitta kod. | Läser en specifik liten partition på disken (kallad *EFI System Partition*). |

**Gyllene regeln i branschen:** Blanda aldrig dessa. Kör du UEFI ska disken formateras med GPT. Försöker du mixa dem kommer servern inte att starta.

---

---

## 2. Nästa nivå: Infrastruktur i praktiken

Som IT-tekniker sitter du sällan med ett USB-minne och installerar en dator i taget. Du jobbar med *skalbarhet* och *säkerhet*. Här är de koncept som bygger vidare på UEFI/GPT-grunden:

### PXE-Boot (Network Booting)

I en företagsmiljö installerar vi operativsystem via nätverket. Detta kallas för **PXE** (Preboot eXecution Environment).
Istället för att UEFI letar efter sin uppstartsfil på den lokala hårddisken, säger vi åt nätverkskortet att ropa ut i nätverket: *"Finns det någon server här som kan ge mig ett operativsystem?"*. En distributionsserver svarar och skickar över installationsfilerna direkt via nätverkskabeln. Detta är hur du kan ominstallera 500 datorer över en natt.

### Secure Boot (Säker uppstart)

Detta är en kritisk säkerhetsfunktion som bara är möjlig med **UEFI**. Innan Secure Boot kunde avancerade virus (rootkits) lägga sig *innan* Windows startade, vilket gjorde dem omöjliga för vanliga antivirus att hitta. Med Secure Boot måste varje kodsnutt som körs under uppstarten vara kryptografiskt signerad och godkänd, annars vägrar UEFI att ladda den.

### Filsystem (Hur datan organiseras)

När GPT väl har delat in (partitionerat) din hårddisk måste den formateras med ett filsystem för att kunna hålla filer.

* **FAT32:** Ett extremt gammalt men superenkelt format. *Krävs* för din lilla EFI-partition så att UEFI kan läsa den.
* **NTFS:** Standard för Windows.
* **ext4 / ZFS / XFS:** Standardfilsystem för Linuxservrar, där ZFS är enormt populärt för stora lagringsservrar (SAN/NAS) eftersom det har inbyggt skydd mot dataförlust.

### Virtualisering & Hypervisors

Idag installerar vi nästan aldrig Windows eller Linux direkt på hårdvaran (så kallad "bare metal"). Istället installerar vi en **Hypervisor** (till exempel VMware ESXi, Proxmox eller Microsoft Hyper-V).

Hypervisorn är ett operativsystem vars enda jobb är att agera värd för *virtuella maskiner* (VMs). Det fina här är att även en virtuell maskin har sitt eget virtuella UEFI och sin egen virtuella GPT-disk. Så exakt samma koncept som du lär dig för fysiska datorer gäller även i molnet och i virtualiserade kluster.
