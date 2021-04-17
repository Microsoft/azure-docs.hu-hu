---
title: A MARS-ügynök támogatási mátrixa
description: Ez a cikk Azure Backup a Microsoft Azure Recovery Services- (MARS-) ügynököt futtató gépek biztonsági Microsoft Azure támogatja.
ms.date: 04/09/2021
ms.topic: conceptual
ms.openlocfilehash: 20bca0e9ca9dfd735501e68bd0e5a6d69d2ef68e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576499"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Támogatási mátrix a Microsoft Azure Recovery Services- (MARS-) ügynökkel való biztonsági mentéshez

Az Azure Backup [a](backup-overview.md) helyszíni gépek és alkalmazások biztonsági és az Azure-beli virtuális gépek biztonsági használhatja. Ez a cikk összefoglalja a támogatási beállításokat és korlátozásokat, amikor a Microsoft Azure Recovery Services- (MARS-) ügynök használatával biztonsági adatokat hoz a gépekről.

## <a name="the-mars-agent"></a>A MARS-ügynök

Azure Backup MARS-ügynökkel biztonsági másolatot készít a helyszíni gépekről és az Azure-beli virtuális gépekről származó adatokról egy Azure-beli backup Recovery Services-tárolóba. A MARS-ügynök a következőre képes:

- Futtassa a helyszíni Windows rendszerű gépeket, hogy közvetlenül biztonsági mentést készítsen egy Biztonsági mentési helyreállítási tárba az Azure-ban.
- Futtassa a-t Windows rendszerű virtuális gépeken, hogy közvetlenül egy tárolóba tudjanak biztonságimentéseket futtatni.
- Futtassa a Microsoft Azure Backup (MABS) vagy egy System Center Data Protection Manager (DPM) kiszolgálón. Ebben a forgatókönyvben a gépek és a számítási feladatok biztonsági adatbázisa a MABS-re vagy a DPM-kiszolgálóra lesz visszamentve. A MARS-ügynök ezután biztonságimenti ezt a kiszolgálót egy Azure-beli tárolóba.

> [!NOTE]
>Azure Backup nem támogatja az óra automatikus beállítását a nyári időszámítás (DST) szerint. Módosítsa a házirendet úgy, hogy a nyári időszámítás figyelembe legyen véve, hogy ne legyen eltérés a tényleges időpont és az ütemezett biztonsági mentés ideje között.

A biztonsági mentési lehetőségek attól függnek, hogy az ügynök hol van telepítve. További információkért lásd a [Azure Backup MARS-ügynökkel való használatával.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) A MABS és a DPM biztonsági mentési architektúrával kapcsolatos információkért lásd: Biztonsági mentés [a DPM-be vagy a MABS-be.](backup-architecture.md#architecture-back-up-to-dpmmabs) Lásd még [a biztonsági mentési](backup-support-matrix-mabs-dpm.md) architektúra követelményeit.

**Telepítés** | **Részletek**
--- | ---
A legújabb MARS-ügynök letöltése | Letöltheti az ügynök legújabb verzióját a tárolóból, vagy [letöltheti közvetlenül.](https://aka.ms/azurebackup_agent)
Telepítés közvetlenül a gépre | A MARS-ügynököt telepítheti közvetlenül egy helyszíni Windows-kiszolgálóra vagy egy Windows rendszerű virtuális gépre, amely a támogatott operációs rendszerek [bármelyikét futtatja.](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)
Telepítés biztonsági mentési kiszolgálón | Amikor beállít egy DPM-et vagy MABS-t az Azure-ba való biztonsági frissítéshez, letölti és telepíti a MARS-ügynököt a kiszolgálóra. Az ügynököt a biztonsági mentési kiszolgáló támogatási [mátrixában](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) telepítheti a támogatott operációs rendszerekre.

> [!NOTE]
> Alapértelmezés szerint a biztonsági mentésre engedélyezett Azure-beli virtuális gépeken Azure Backup van. Ez a bővítmény a teljes virtuális gépről biztonsági menti. A MARS-ügynököt a bővítmény mellett egy Azure-beli virtuális gépen is telepítheti és futtathatja, ha a teljes virtuális gép helyett adott mappákról és fájlokról szeretne biztonsági mentéset.
> Amikor a MARS-ügynököt egy Azure-beli virtuális gépen futtatja, biztonsági mentésekor biztonsági mentése történik a virtuális gépen ideiglenes tárolóban lévő fájlokról vagy mappákról. A biztonsági mentések meghiúsulnak, ha a fájlokat vagy mappákat eltávolítják az ideiglenes tárolóból, vagy ha az ideiglenes tárolót eltávolítják.

## <a name="cache-folder-support"></a>Gyorsítótármappa támogatása

Ha a MARS-ügynökkel készít biztonsági adatokat, az ügynök pillanatképet készít az adatokról, és egy helyi gyorsítótármappában tárolja őket, mielőtt elküldi az adatokat az Azure-nak. A cache (scratch) mappa több követelményt is megfelel:

**Cache** | **Részletek**
--- | ---
Méret |  A gyorsítótármappában a szabad területnek a biztonsági mentési adatok teljes méretének legalább 5–10 százalékának kell lennie.
Hely | A gyorsítótár mappáját helyileg kell tárolni a biztonságimentett gépen, és online állapotban kell lennie. A gyorsítótármappa nem lehet hálózati megosztáson, cserélhető adathordozón vagy offline köteten.
Mappa | A gyorsítótármappa nem titkosítható deduplikált köteten vagy tömörített, ritka vagy újraelemzési ponttal rendelkezik mappában.
Helyváltozások | A gyorsítótár helyét úgy módosíthatja, hogy leállítja a biztonsági mentési motort ( ), és átmásolja `net stop bengine` a gyorsítótármappát egy új meghajtóra. (Győződjön meg arról, hogy az új meghajtón elegendő hely áll rendelkezésre.) Ezután frissítsen két beállításjegyzék-bejegyzést a **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** and **Config/CloudBackupProvider/ScratchLocation)** mappában az új helyre, és indítsa újra a motort.

## <a name="networking-and-access-support"></a>Hálózat és hozzáférés támogatása

### <a name="url-and-ip-access"></a>URL- és IP-hozzáférés

A MARS-ügynöknek hozzá kell férni a következő URL-címekhez:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net
- `www.msftconnecttest.com`

És ezekre az IP-címekre:

- 20.190.128.0/18
- 40.126.0.0/18

A fent felsorolt ÖSSZES URL-cím és IP-cím elérése a HTTPS protokollt használja a 443-as porton.

Az Azure-beli virtuális gépekről a MARS-ügynökkel készült fájlok és mappák biztonsági mentésekor az Azure-beli virtuális hálózatot is konfigurálni kell a hozzáférés engedélyezése érdekében. Ha hálózati biztonsági csoportokat (NSG-t) használ, az *AzureBackup* szolgáltatáscímkével engedélyezze a kimenő hozzáférést a Azure Backup. A Azure Backup címke mellett a hitelesítéshez és az adatátvitelhez is engedélyeznie kell a kapcsolódást, ehhez pedig hasonló [NSG-szabályokat](../virtual-network/network-security-groups-overview.md#service-tags) kell létrehoznia az Azure AD *(AzureActiveDirectory)* és az Azure Storage(*Storage*) számára. A következő lépések ismertetik a szabály létrehozásához szükséges folyamatot a Azure Backup címkéhez:

1. A **Minden szolgáltatás részen** válassza a Hálózati **biztonsági csoportok lehetőséget,** és válassza ki a hálózati biztonsági csoportot.
2. A Beállítások **alatt válassza a** Kimenő biztonsági szabályok **lehetőséget.**
3. Válassza a **Hozzáadás** lehetőséget. Adja meg az új szabály létrehozásához szükséges összes adatot a biztonsági szabály [beállításaiban leírtak szerint.](../virtual-network/manage-network-security-group.md#security-rule-settings) Győződjön meg **arról, hogy a Destination** (Cél) beállítása Service Tag *(Szolgáltatáscímke)* és **Destination service tag** (Cél szolgáltatáscímke) beállítása *AzureBackup.*
4. Válassza **a Hozzáadás** lehetőséget az újonnan létrehozott kimenő biztonsági szabály mentéshez.

Hasonlóképpen létrehozhat kimenő NSG-biztonsági szabályokat az Azure Storage-hoz és az Azure AD-hez. A szolgáltatáscímkékről további információt ebben a [cikkben talál.](../virtual-network/service-tags-overview.md)

### <a name="azure-expressroute-support"></a>Azure ExpressRoute támogatás

Az adatok biztonsági biztonsági Azure ExpressRoute a nyilvános társviszony-létesítés (elérhető a régi kapcsolathálózatok számára) és a Microsoft társviszony-létesítés. A privát társviszony-létesítésen keresztüli biztonsági mentés nem támogatott.

Nyilvános társviszony-létesítés esetén: A következő tartományokhoz/címekhez való hozzáférés biztosítása:

* URL-címek
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-címek
  * 20.190.128.0/18
  * 40.126.0.0/18

Microsoft-társviszony-létesítés esetén válassza ki a következő szolgáltatásokat/régiókat és a kapcsolódó közösségi értékeket:

- Azure Backup (a Recovery Services-tároló helye alapján)
- Azure Active Directory (12076:5060)
- Azure Storage (a Recovery Services-tároló helye alapján)

További információkért lásd az [ExpressRoute útválasztási követelményeit.](../expressroute/expressroute-routing.md#bgp)

>[!NOTE]
>A nyilvános társviszony-létesítés elavult az új kapcsolathálózatok számára.

### <a name="private-endpoint-support"></a>Privát végpont támogatása

Most már privát végpontok használatával biztonságosan biztonságimentheti az adatokat a kiszolgálókról a Helyreállítási tárba. Mivel Azure Active Directory nem támogatja a privát végpontokat, a Azure Active Directory ip-hez és FQDN-hez külön kell kimenő hozzáférést adni.

Amikor a MARS-ügynökkel biztonságimentéseket használ a helyszíni erőforrásokról, győződjön meg arról, hogy a helyszíni hálózat (amely tartalmazza a biztonságimentéshez szükséges erőforrásokat) társviszonyban áll a tároló privát végpontját tartalmazó Azure virtuális hálózattal. Ezután folytathatja a MARS-ügynök telepítését és a biztonsági mentés konfigurálását. Azonban gondoskodnia kell arról, hogy a biztonsági mentéssel kapcsolatos összes kommunikáció csak a társhálózaton keresztül történjen.

Ha a MARS-ügynök regisztrálása után eltávolítja a tároló privát végpontját, újra regisztrálnia kell a tárolót a tárolóval. Számukra nem kell leállítania a védelmet.

További információ a privát [végpontjairól a Azure Backup.](private-endpoints.md)

### <a name="throttling-support"></a>Szabályozás támogatása

**Szolgáltatás** | **Részletek**
--- | ---
Sávszélesség-vezérlés | Támogatott. A MARS-ügynökben használja a **Tulajdonságok módosítása parancsot** a sávszélesség beállításához.
Hálózati sávszélesség-szabályozás | Nem érhető el a Windows Server 2008 R2, Windows Server 2008 SP2 vagy Windows 7 rendszert futtató biztonságimentett gépekhez.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

>[!NOTE]
> A MARS-ügynök nem támogatja a Windows Server Core termékmagokat.

A MARS-ügynökkel közvetlenül az Azure-ba is biztonságimenthet az alább felsorolt operációs rendszereken, amelyeken futnak:

1. Helyszíni Windows-kiszolgálók
2. Windowst futtató Azure-beli virtuális gépek

Az operációs rendszereknek 64 bitesnek kell lennie, és a legújabb szervizcsomagokat és frissítéseket kell futniuk. Az alábbi táblázat összefoglalja ezeket az operációs rendszereket:

**Operációs rendszer** | **Fájlok/mappák** | **Rendszerállapot** | **Szoftver-/modulkövetelmények**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Igen | Nem |  A szoftver-/modulkövetelmények ellenőrzése a megfelelő kiszolgálóverzióban
Windows 8.1 (Enterprise, Pro)| Igen |Nem | A szoftver-/modulkövetelmények ellenőrzése a megfelelő kiszolgálóverzióban
Windows 8 (Enterprise, Pro) | Igen | Nem | A szoftver-/modulkövetelmények ellenőrzése a megfelelő kiszolgálóverzióban
Windows Server 2016 (Standard, Datacenter, Essentials) | Igen | Yes | - .NET 4.5 <br> – Windows PowerShell <br> - A legújabb kompatibilis Microsoft VC++ terjeszthető változat <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Igen | Yes | - .NET 4.5 <br> – Windows PowerShell <br> - A legújabb kompatibilis Microsoft VC++ terjeszthető változat <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 (Standard, Datacenter, Foundation) | Igen | Yes |- .NET 4.5 <br> -Windows PowerShell <br> - A legújabb kompatibilis Microsoft VC++ terjeszthető változat <br> - Microsoft Management Console (MMC) 3.0 <br> - Deployment Image Servicing and Management (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Igen | Nem | - .NET 4.5 <br> – Windows PowerShell <br> - A legújabb kompatibilis Microsoft VC++ terjeszthető változat <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2019 (Standard, Datacenter, Essentials) | Igen | Yes | - .NET 4.5 <br> – Windows PowerShell <br> - A legújabb kompatibilis Microsoft VC++ terjeszthető változat <br> - Microsoft Management Console (MMC) 3.0

További információ: [Támogatott MABS és DPM operációs rendszerek.](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)

### <a name="operating-systems-at-end-of-support"></a>Támogatás végén az operációs rendszerek

A következő operációs rendszerek támogatása véget ért, és erősen ajánlott az operációs rendszer frissítése a további védelem érdekében.

Ha a meglévő kötelezettségvállalások megakadályozzák az operációs rendszer frissítését, fontolja meg a Windows-kiszolgálók Azure-beli virtuális gépekre való áttelepítését, és az Azure-beli virtuális gépek biztonsági másolatai segítségével továbbra is biztosíthatja a védelmet. A [Windows-kiszolgáló áttelepítésére](https://azure.microsoft.com/migration/windows-server/) vonatkozó további információkért látogasson el ide az áttelepítési oldalra.

Helyszíni vagy üzemeltetett környezetek esetén, ahol nem tudja frissíteni az operációs rendszert, vagy nem tud az Azure-ba áttérni, aktiválja a kiterjesztett biztonsági frissítéseket a gépekhez, hogy továbbra is védve és támogatottan maradjon. Figyelje meg, hogy csak bizonyos kiadások jogosultak a kiterjesztett biztonsági frissítésekre. További [információért látogasson el a GYIK](https://www.microsoft.com/windows-server/extended-security-updates) oldalra.

| **Operációs rendszer**                                       | **Fájlok/mappák** | **Rendszerállapot** | **Szoftver-/modulkövetelmények**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Igen               | Nem                 | A szoftver-/modulkövetelmények ellenőrzése a megfelelő kiszolgálóverzióban |
| Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Igen               | Yes                | - .NET 3.5, .NET 4.5 <br>  – Windows PowerShell <br>  - Kompatibilis Microsoft VC++ terjeszthető <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Deployment Image Servicing and Management (DISM.exe) |
| Windows Server 2008 SP2 (Standard, Datacenter, Foundation)  | Igen               | Nem                 | - .NET 3.5, .NET 4.5 <br>  – Windows PowerShell <br>  - Kompatibilis Microsoft VC++ terjeszthető <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Deployment Image Servicing and Management (DISM.exe) <br>  - Virtual Server 2005 alap + KB948515 |

## <a name="backup-limits"></a>Biztonsági mentés korlátai

### <a name="size-limits"></a>Méretkorlátok

Azure Backup egy fájl vagy mappa típusú adatforrás méretét, amelyről biztonsági mentéset lehet tartani. Az egyetlen kötetről biztonságielt elemek mérete nem haladhatja meg a következő táblázatban összefoglalt méreteket:

**Operációs rendszer** | **Méretkorlát**
--- | ---
Windows Server 2012 vagy újabb |54 400 GB
Windows Server 2008 R2 SP1 |1700 GB
Windows Server 2008 SP2| 1700 GB
Windows 8 vagy újabb| 54 400 GB
Windows 7| 1700 GB

### <a name="minimum-retention-limits"></a>Minimális megőrzési korlátok

A különböző helyreállítási pontokhoz a következő minimális megőrzési időtartamok beállíthatók:

|Helyreállítási pont |Időtartam  |
|---------|---------|
|Napi helyreállítási pont    |   7 nap      |
|Heti helyreállítási pont     |    4 hét     |
|Havi helyreállítási pont    |   3 hónap      |
|Éves helyreállítási pont  |      1 év   |

### <a name="other-limitations"></a>Egyéb korlátozások

- A MARS nem támogatja több, azonos nevű gép védelmét egyetlen tárolóban.

## <a name="supported-file-types-for-backup"></a>Támogatott fájltípusok a biztonsági mentéshez

**Típus** | **Támogatás**
--- | ---
Titkosított<sup>*</sup>| Támogatott.
Tömörített | Támogatott.
Ritka | Támogatott.
Tömörített és ritka |Támogatott.
Rögzített hivatkozások| Nem támogatott. Kimarad.
Újraelemzési pont| Nem támogatott. Kimarad.
Titkosított és ritka |Nem támogatott. Kimarad.
Tömörített stream| Nem támogatott. Kimarad.
Ritka stream| Nem támogatott. Kimarad.
OneDrive (a szinkronizált fájlok ritka streamek)| Nem támogatott.
Engedélyezett Elosztott fájlrendszer replikációs szolgáltatása mappák | Nem támogatott.

\* Győződjön meg arról, hogy a MARS-ügynök hozzáfér a szükséges tanúsítványokhoz a titkosított fájlok eléréséhez. A rendszer kihagyja a nem elérhető fájlokat.

## <a name="supported-drives-or-volumes-for-backup"></a>A biztonsági mentéshez támogatott meghajtók vagy kötetek

**Meghajtó/kötet** | **Támogatás** | **Részletek**
--- | --- | ---
Csak olvasható kötetek| Nem támogatott | A Kötetmásolási árnyékszolgáltatás (VSS) csak akkor működik, ha a kötet írható.
Offline kötetek| Nem támogatott |A VSS csak akkor működik, ha a kötet online állapotban van.
Hálózati megosztás| Nem támogatott |A kötetnek helyinek kell lennie a kiszolgálón.
BitLocker által zárolt kötetek| Nem támogatott |A kötetet fel kell oldani a biztonsági mentés indítása előtt.
Fájlrendszer azonosítása| Nem támogatott |Csak az NTFS támogatott.
Cserélhető adathordozó| Nem támogatott |Minden biztonságimásolat-elem forrásának rögzített *állapotúnak kell* lennie.
Deduplikált meghajtók | Támogatott | Azure Backup konvertálja a deduplikált adatokat normál adatokká. Optimalizálja, titkosítja, tárolja és elküldi az adatokat a tárolóba.

## <a name="support-for-initial-offline-backup"></a>Kezdeti offline biztonsági mentés támogatása

Azure Backup támogatja *az offline átvitelt* a kezdeti biztonsági mentési adatok átviteléhez az Azure-ba lemezekkel. Ez a támogatás akkor hasznos, ha a kezdeti biztonsági mentés valószínűleg a terabájtok (TEBS) mérettartományában lesz. Az offline biztonsági mentés a következő esetén támogatott:

- A MARS-ügynököt futtató helyszíni gépeken lévő fájlok és mappák közvetlen biztonsági mentése.
- Számítási feladatok és fájlok biztonsági mentése DPM-kiszolgálóról vagy MABS-ről.

Az offline biztonsági mentés nem használható rendszerállapotfájlokhoz.

## <a name="support-for-data-restoration"></a>Adat-visszaállítás támogatása

Az azonnali visszaállítás [funkcióval](backup-instant-restore-capability.md) Azure Backup visszaállíthatja az adatokat, mielőtt a rendszer átmásolja az adatokat a tárolóba. A gépnek, amelyről biztonsági 4.5.2-es vagy újabb .NET-keretrendszer kell futnia.

A biztonsági másolatok nem állíthatók vissza olyan célgépre, amely az operációs rendszer egy korábbi verzióját futtatja. Például egy Windows 7 rendszerű számítógépről készült biztonsági másolat visszaállítható a Windows 8 újabb verzióra. A Windows 7 rendszerű számítógépeken Windows 8 nem állítható vissza biztonsági másolat.

## <a name="previous-mars-agent-versions"></a>A MARS-ügynök korábbi verziói

Az alábbi táblázatban az ügynök korábbi verziói és letöltési hivatkozásai vannak felsorolva. Javasoljuk, hogy frissítse az ügynök verzióját a legújabb verzióra, hogy kihasználja a legújabb szolgáltatásokat és az optimális teljesítményt.

**Versions** (Verziók) | **Tudásbáziscikkek**
--- | ---
[2.0.9145.0](https://download.microsoft.com/download/4/5/E/45EB38B4-2DA7-45FA-92E1-5CA1E23D18D1/MARSAgentInstaller.exe) | Nem elérhető
[2.0.9151.0](https://download.microsoft.com/download/7/1/7/7177B70A-51E8-434D-BDF2-FA3A09E917D6/MARSAgentInstaller.exe) | Nem elérhető
[2.0.9153.0](https://download.microsoft.com/download/3/D/D/3DD8A2FF-AC48-4A62-8566-B2C05F0BCCD0/MARSAgentInstaller.exe) | Nem elérhető
[2.0.9162.0](https://download.microsoft.com/download/0/1/0/010E598E-6289-47DB-872A-FFAF5030E6BE/MARSAgentInstaller.exe) | Nem elérhető
[2.0.9169.0](https://download.microsoft.com/download/f/7/1/f716c719-24bc-4337-af48-113baddc14d8/MARSAgentInstaller.exe) | [4515971](https://support.microsoft.com/help/4538314)
[2.0.9170.0](https://download.microsoft.com/download/1/8/7/187ca9a9-a6e5-45f0-928f-9a843d84aed5/MARSAgentInstaller.exe) | Nem elérhető
[2.0.9173.0](https://download.microsoft.com/download/7/9/2/79263a35-de87-4ba6-9732-65563a4274b6/MARSAgentInstaller.exe) | [4538314](https://support.microsoft.com/help/4538314)
[2.0.9177.0](https://download.microsoft.com/download/3/0/4/304d3cdf-b123-42ee-ad03-98fb895bc38f/MARSAgentInstaller.exe) | Nem elérhető
[2.0.9181.0](https://download.microsoft.com/download/6/6/9/6698bc49-e30b-4a3e-a1f4-5c859beafdcc/MARSAgentInstaller.exe) | Nem elérhető
[2.0.9190.0](https://download.microsoft.com/download/a/c/e/aceffec0-794e-4259-8107-92a3f6c10f55/MARSAgentInstaller.exe) | [4575948](https://support.microsoft.com/help/4575948)
[2.0.9195.0](https://download.microsoft.com/download/6/1/3/613b70a7-f400-4806-9d98-ae26aeb70be9/MARSAgentInstaller.exe) | [4582474](https://support.microsoft.com/help/4582474)
[2.0.9197.0](https://download.microsoft.com/download/2/7/5/27531ace-3100-43bc-b4af-7367680ea66b/MARSAgentInstaller.exe) | [4589598](https://support.microsoft.com/help/4589598)
[2.0.9207.0](https://download.microsoft.com/download/b/5/a/b5a29638-1cef-4906-b704-4d3d914af76e/MARSAgentInstaller.exe) | [5001305](https://support.microsoft.com/help/5001305)

>[!NOTE]
>A MARS-ügynök kisebb megbízhatósági és teljesítménnyel kapcsolatos fejlesztései nem tartalmaznak tudásbáziscikket.

## <a name="next-steps"></a>Következő lépések

- További információ a [MARS-ügynököt használó biztonsági mentési architektúráról.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
- Ismerje meg, mi támogatott a [MARS-ügynök MABS- vagy DPM-kiszolgálón való futtatásakor.](backup-support-matrix-mabs-dpm.md)
