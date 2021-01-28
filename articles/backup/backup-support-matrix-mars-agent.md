---
title: A MARS-ügynök támogatási mátrixa
description: Ez a cikk a Microsoft Azure Recovery Services (MARS) ügynököt futtató gépek biztonsági mentésének Azure Backup támogatását foglalja össze.
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 2170440b7b47861b75801b8dbd334686b4cabc8b
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985614"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Támogatási mátrix a Microsoft Azure Recovery Services-(MARS-) ügynökkel való biztonsági mentéshez

A [Azure Backup szolgáltatás](backup-overview.md) segítségével biztonsági mentést készíthet a helyszíni gépekről és alkalmazásokról, valamint biztonsági mentést készíthet az Azure-beli virtuális gépekről. Ez a cikk összefoglalja a támogatási beállításokat és korlátozásokat, amikor a Microsoft Azure Recovery Services (MARS) ügynököt használja a gépek biztonsági mentéséhez.

## <a name="the-mars-agent"></a>A MARS-ügynök

Azure Backup a MARS-ügynök használatával készít biztonsági másolatot a helyszíni gépekről és az Azure-beli virtuális gépekről az Azure-beli biztonsági mentési Recovery Services-tárolóba. A MARS-ügynök a következőket teheti:

- A helyszíni Windows rendszerű gépeken futtathatók, így közvetlenül biztonsági mentést készíthetnek az Azure Backup Recovery Services-tárolójába.
- Futtassa a Windows rendszerű virtuális gépeken, hogy közvetlenül a tárolóba lehessen biztonsági mentést készíteni.
- Futtatás Microsoft Azure Backup-kiszolgálón (MABS) vagy a System Center Data Protection Manager (DPM) kiszolgálón. Ebben a forgatókönyvben a gépek és a számítási feladatok biztonsági mentést készítenek a MABS vagy a DPM-kiszolgálóra. A MARS-ügynök ezután biztonsági másolatot készít a kiszolgálóról egy Azure-tárolóba.

> [!NOTE]
>A Azure Backup nem támogatja az óra automatikus beállítását a nyári időszámításhoz (DST). Módosítsa úgy a szabályzatot, hogy a nyári időmegtakarítást figyelembe vegyék a tényleges idő és az ütemezett biztonsági mentés időbeli eltérésének megelőzése érdekében.

A biztonsági mentési lehetőségek attól függnek, hogy hol van telepítve az ügynök. További információ: [Azure Backup architektúra a Mars-ügynök használatával](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). A MABS és a DPM biztonsági mentési architektúrával kapcsolatos információkért lásd: [biztonsági mentés DPM vagy MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Lásd még a biztonsági mentési architektúra [követelményeit](backup-support-matrix-mabs-dpm.md) is.

**Telepítés** | **Részletek**
--- | ---
A legújabb MARS-ügynök letöltése | Az ügynök legújabb verzióját letöltheti a tárolóból, vagy [közvetlenül is letöltheti](https://aka.ms/azurebackup_agent).
Telepítés közvetlenül a gépen | A MARS-ügynököt közvetlenül egy helyszíni Windows Serverre vagy egy [támogatott operációs rendszert](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)futtató Windows rendszerű virtuális gépre is telepítheti.
Telepítés biztonsági mentési kiszolgálóra | A DPM vagy a MABS Azure-ba való biztonsági mentésének beállításakor letöltheti és telepítheti a MARS-ügynököt a kiszolgálón. A biztonsági mentési kiszolgáló támogatási mátrixában az ügynököt a [támogatott operációs rendszerekre](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) is telepítheti.

> [!NOTE]
> Alapértelmezés szerint a biztonsági mentésre engedélyezett Azure-beli virtuális gépekhez Azure Backup bővítmény telepítése szükséges. Ez a bővítmény biztonsági mentést készít a teljes virtuális gépről. A MARS-ügynököt telepítheti és futtathatja egy Azure-beli virtuális gépen a bővítmény mellett, ha a teljes virtuális gép helyett konkrét mappákról és fájlokról szeretne biztonsági mentést készíteni.
> Ha egy Azure-beli virtuális gépen futtatja a MARS-ügynököt, a biztonsági mentést készít a virtuális gépen lévő ideiglenes tárolóban található fájlokról vagy mappákról. A biztonsági mentések sikertelenek, ha a fájlok vagy mappák törlődnek az ideiglenes tárolóból, vagy ha az ideiglenes tároló el lett távolítva.

## <a name="cache-folder-support"></a>Gyorsítótár-mappák támogatása

Ha a MARS-ügynököt használja az adatok biztonsági mentésére, az ügynök pillanatképet készít az adatokról, és egy helyi gyorsítótár-mappában tárolja azt, mielőtt elküldi az adatokat az Azure-ba. A gyorsítótár (Scratch) mappája több követelményt is tartalmaz:

**Cache** | **Részletek**
--- | ---
Méret |  A gyorsítótár mappában lévő szabad területnek legalább 5 – 10%-ának kell lennie a biztonsági mentési adatai teljes méretének.
Hely | A gyorsítótár mappájának helyileg kell lennie a biztonsági mentés alatt álló gépen, és online állapotban kell lennie. A gyorsítótár mappája nem lehet hálózati megosztáson, cserélhető adathordozón vagy offline köteten.
Mappa | A gyorsítótár mappája nem titkosítható deduplikált köteten, vagy egy tömörített mappában, amely ritka, vagy újraelemzési ponttal rendelkezik.
Hely változásai | A gyorsítótár helyét úgy módosíthatja, hogy leállítja a biztonsági mentési motort ( `net stop bengine` ), és átmásolja a gyorsítótár mappát egy új meghajtóra. (Győződjön meg arról, hogy az új meghajtó elegendő lemezterülettel rendelkezik.) Ezután frissítsen két beállításjegyzékbeli bejegyzést a **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**config/ScratchLocation** és **config/CloudBackupProvider/ScratchLocation**) elemre az új helyre, és indítsa újra a motort.

## <a name="networking-and-access-support"></a>Hálózatkezelés és hozzáférés támogatása

### <a name="url-and-ip-access"></a>URL-cím és IP-hozzáférés

A MARS-ügynöknek hozzá kell férnie az alábbi URL-címekhez:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net
- `www.msftconnecttest.com`

És az alábbi IP-címekre:

- 20.190.128.0/18
- 40.126.0.0/18

A fentiekben felsorolt URL-címek és IP-címek elérése a HTTPS protokollt használja a 443-es porton.

Ha az Azure-beli virtuális gépekről a MARS-ügynök használatával készít biztonsági másolatot a fájlokról és mappákról, az Azure Virtual Network-t is konfigurálni kell, hogy engedélyezze a hozzáférést. Ha hálózati biztonsági csoportokat (NSG) használ, használja a *AzureBackup* szolgáltatás címkéjét, hogy engedélyezze a kimenő hozzáférést Azure Backuphoz. A Azure Backup címkén kívül az Azure AD-hoz (*AzureActiveDirectory*) és az Azure Storage-hoz (*Storage*) hasonló [NSG szabályok](../virtual-network/network-security-groups-overview.md#service-tags) létrehozásával is engedélyeznie kell a csatlakozást a hitelesítéshez és az adatátvitelhez. A következő lépések azt ismertetik, hogyan hozható létre szabály a Azure Backup címke számára:

1. A **minden szolgáltatás** területen lépjen a **hálózati biztonsági csoportok** elemre, és válassza ki a hálózati biztonsági csoportot.
2. A **Beállítások** területen válassza a **kimenő biztonsági szabályok** lehetőséget.
3. Válassza a **Hozzáadás** elemet. Adja meg az új szabály létrehozásához szükséges összes adatot a [biztonsági szabály beállításai](../virtual-network/manage-network-security-group.md#security-rule-settings)című témakörben leírtak szerint. Győződjön meg arról, hogy a **cél** a *Service tag* és a **cél szolgáltatás címkéje** *AzureBackup* értékre van állítva.
4. Válassza a **Hozzáadás** lehetőséget az újonnan létrehozott kimenő biztonsági szabály mentéséhez.

Hasonlóképpen NSG kimenő biztonsági szabályokat hozhat létre az Azure Storage és az Azure AD számára. A szolgáltatás címkével kapcsolatos további információkért tekintse meg [ezt a cikket](../virtual-network/service-tags-overview.md).

### <a name="azure-expressroute-support"></a>Azure ExpressRoute-támogatás

Az Azure ExpressRoute-on keresztül biztonsági mentést készíthet az adatairól a nyilvános (a régi áramkörökhöz elérhető) és a Microsoft-partnerek számára. A privát kapcsolaton keresztüli biztonsági mentés nem támogatott.

Nyilvános társítás esetén: a következő tartományokhoz/címekhez való hozzáférés biztosítása:

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

A Microsoft-partnerekkel válassza ki a következő szolgáltatásokat/régiókat és a vonatkozó közösségi értékeket:

- Azure Backup (az Recovery Services-tároló helyének megfelelően)
- Azure Active Directory (12076:5060)
- Azure Storage (az Recovery Services-tároló helyének megfelelően)

További információkért tekintse meg a [ExpressRoute útválasztási követelményeit](../expressroute/expressroute-routing.md#bgp).

>[!NOTE]
>Az új áramkörök esetében a nyilvános társítás elavult.

### <a name="private-endpoint-support"></a>Privát végpontok támogatása

Mostantól privát végpontokat is használhat az adatok biztonságos biztonsági mentésére a kiszolgálókról a Recovery Services-tárolóba. Mivel Azure Active Directory jelenleg nem támogatja a privát végpontokat, az IP-címeket és a Azure Active Directory szükséges teljes tartományneveket külön kell engedélyezni a kimenő hozzáféréshez.

Ha a MARS-ügynököt használja a helyszíni erőforrások biztonsági mentésére, győződjön meg arról, hogy a helyszíni hálózat (a biztonsági mentéshez szükséges erőforrásokat tartalmazza) a tárolóhoz tartozó privát végpontot tartalmazó Azure-VNet van társítva. Ezután továbbra is telepítheti a MARS-ügynököt, és konfigurálhatja a biztonsági mentést. Azonban gondoskodnia kell arról, hogy a biztonsági mentés minden kommunikációja csak a csak a hálózaton keresztül történjen.

Ha a MARS-ügynök regisztrálása után eltávolítja a tárolóhoz tartozó privát végpontokat, akkor újra regisztrálnia kell a tárolót a tárolóban. Nem kell leállítania a védelmet.

További információ a [Azure Backup magánhálózati végpontokról](private-endpoints.md).

### <a name="throttling-support"></a>Szabályozás támogatása

**Szolgáltatás** | **Részletek**
--- | ---
Sávszélesség-vezérlés | Támogatott. A MARS-ügynökben a sávszélesség módosításához használja a **Tulajdonságok módosítása** lehetőséget.
Hálózati sávszélesség-szabályozás | Nem érhető el a Windows Server 2008 R2, a Windows Server 2008 SP2 vagy a Windows 7 rendszerű biztonsági másolattal rendelkező gépekhez.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

>[!NOTE]
> A MARS-ügynök nem támogatja a Windows Server Core SKU-t.

A MARS-ügynök használatával biztonsági mentést készíthet közvetlenül az Azure-ra az alább felsorolt operációs rendszereken:

1. Helyszíni Windows-kiszolgálók
2. Windows rendszerű Azure-beli virtuális gépek

Az operációs rendszernek 64 bitesnek kell lennie, és a legújabb szervizcsomagokat és frissítéseket kell futtatnia. A következő táblázat összefoglalja ezeket az operációs rendszereket:

**Operációs rendszer** | **Fájlok/mappák** | **Rendszerállapot** | **Szoftver/modulra vonatkozó követelmények**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Igen | Nem |  A szoftver/modul követelményeinek megfelelő kiszolgálói verzió keresése
Windows 8,1 (Enterprise, Pro)| Igen |Nem | A szoftver/modul követelményeinek megfelelő kiszolgálói verzió keresése
Windows 8 (Enterprise, Pro) | Igen | Nem | A szoftver/modul követelményeinek megfelelő kiszolgálói verzió keresése
Windows Server 2016 (standard, Datacenter, Essentials) | Igen | Yes | -.NET 4,5 <br> – Windows PowerShell <br> – Legújabb kompatibilis Microsoft VC + + terjeszthető csomag <br> – Microsoft Management Console (MMC) 3,0
Windows Server 2012 R2 (standard, Datacenter, Foundation, Essentials) | Igen | Yes | -.NET 4,5 <br> – Windows PowerShell <br> – Legújabb kompatibilis Microsoft VC + + terjeszthető csomag <br> – Microsoft Management Console (MMC) 3,0
Windows Server 2012 (standard, Datacenter, Foundation) | Igen | Yes |-.NET 4,5 <br> – Windows PowerShell <br> – Legújabb kompatibilis Microsoft VC + + terjeszthető csomag <br> – Microsoft Management Console (MMC) 3,0 <br> -Telepítési lemezképek karbantartása és kezelése (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (standard, munkacsoport) | Igen | Nem | -.NET 4,5 <br> – Windows PowerShell <br> – Legújabb kompatibilis Microsoft VC + + terjeszthető csomag <br> – Microsoft Management Console (MMC) 3,0
Windows Server 2019 (standard, Datacenter, Essentials) | Igen | Yes | -.NET 4,5 <br> – Windows PowerShell <br> – Legújabb kompatibilis Microsoft VC + + terjeszthető csomag <br> – Microsoft Management Console (MMC) 3,0

További információ: [támogatott MABS és DPM operációs rendszerek](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

### <a name="operating-systems-at-end-of-support"></a>A támogatás végén lévő operációs rendszerek

A következő operációs rendszerek a támogatás végén találhatók, és erősen ajánlott az operációs rendszer frissítése, hogy továbbra is védve maradjon.

Ha a meglévő kötelezettségvállalások megakadályozzák az operációs rendszer frissítését, érdemes lehet áttelepíteni a Windows-kiszolgálókat az Azure-beli virtuális gépekre, és az Azure-beli virtuális gépek biztonsági mentéseit használják a védett A Windows Server áttelepítésével kapcsolatos további információkért tekintse meg az [áttelepítési oldalt](https://azure.microsoft.com/migration/windows-server/) .

Helyszíni vagy üzemeltetett környezetekben, ahol nem tudja frissíteni az operációs rendszert, vagy migrálni az Azure-ba, aktiválja a kiterjesztett biztonsági frissítéseket a gépek számára, hogy továbbra is védett és támogatott maradjon. Figyelje meg, hogy csak bizonyos kiadások jogosultak a kiterjesztett biztonsági frissítésekre. További információért látogasson el a [GYIK-oldalra](https://www.microsoft.com/windows-server/extended-security-updates) .

| **Operációs rendszer**                                       | **Fájlok/mappák** | **Rendszerállapot** | **Szoftver/modulra vonatkozó követelmények**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Igen               | Nem                 | A szoftver/modul követelményeinek megfelelő kiszolgálói verzió keresése |
| Windows Server 2008 R2 (standard, Enterprise, Datacenter, Foundation) | Igen               | Yes                | -.NET 3,5, .NET 4,5 <br>  – Windows PowerShell <br>  -Kompatibilis Microsoft VC + + terjeszthető csomag <br>  – Microsoft Management Console (MMC) 3,0 <br>  -Telepítési lemezképek karbantartása és kezelése (DISM.exe) |
| Windows Server 2008 SP2 (standard, Datacenter, Foundation)  | Igen               | Nem                 | -.NET 3,5, .NET 4,5 <br>  – Windows PowerShell <br>  -Kompatibilis Microsoft VC + + terjeszthető csomag <br>  – Microsoft Management Console (MMC) 3,0 <br>  -Telepítési lemezképek karbantartása és kezelése (DISM.exe) <br>  – Virtual Server 2005 Base + KB KB948515 |

## <a name="backup-limits"></a>Biztonsági mentési korlátok

### <a name="size-limits"></a>Méretkorlát

Azure Backup korlátozza a biztonsági mentésre használható fájl vagy mappa adatforrásának méretét. Az egyetlen kötetről készített biztonsági mentés nem lépheti túl az ebben a táblázatban összefoglalt méreteket:

**Operációs rendszer** | **Méretkorlát**
--- | ---
Windows Server 2012 vagy újabb |54 400 GB
Windows Server 2008 R2 SP1 |1 700 GB
Windows Server 2008 SP2| 1 700 GB
Windows 8 vagy újabb| 54 400 GB
Windows 7| 1 700 GB

### <a name="minimum-retention-limits"></a>Minimális megőrzési korlátok

A következő minimális megőrzési időtartamok állíthatók be a különböző helyreállítási pontokhoz:

|Helyreállítási pont |Időtartam  |
|---------|---------|
|Napi helyreállítási pont    |   7 nap      |
|Heti helyreállítási pont     |    4 hét     |
|Havi helyreállítási pont    |   3 hónap      |
|Éves helyreállítási pont  |      1 év   |

### <a name="other-limitations"></a>Egyéb korlátozások

- A MARS nem támogatja több azonos nevű gép védelmét egyetlen tárolóban.

## <a name="supported-file-types-for-backup"></a>A biztonsági mentéshez támogatott fájltípusok

**Típus** | **Támogatás**
--- | ---
Titkosított<sup>*</sup>| Támogatott.
Tömörített | Támogatott.
Ritka | Támogatott.
Tömörített és ritka |Támogatott.
Rögzített hivatkozások| Nem támogatott. Kimarad.
Újraelemzési pont| Nem támogatott. Kimarad.
Titkosított és ritka |Nem támogatott. Kimarad.
Tömörített Stream| Nem támogatott. Kimarad.
Ritka adatfolyam| Nem támogatott. Kimarad.
OneDrive (a szinkronizált fájlok ritka adatfolyamok)| Nem támogatott.
Elosztott fájlrendszer replikációs szolgáltatása engedélyezett mappák | Nem támogatott.

\* Győződjön meg arról, hogy a MARS-ügynök hozzáfér a szükséges tanúsítványokhoz a titkosított fájlok eléréséhez. A rendszer kihagyja a nem elérhető fájlokat.

## <a name="supported-drives-or-volumes-for-backup"></a>A biztonsági mentéshez támogatott meghajtók vagy kötetek

**Meghajtó/kötet** | **Támogatás** | **Részletek**
--- | --- | ---
Írásvédett kötetek| Nem támogatott | A kötet másolási árnyékmásolata szolgáltatás (VSS) csak akkor működik, ha a kötet írható.
Offline kötetek| Nem támogatott |A VSS csak akkor működik, ha a kötet online állapotban van.
Hálózati megosztás| Nem támogatott |A kötetnek helyinek kell lennie a kiszolgálón.
BitLocker által zárolt kötetek| Nem támogatott |A biztonsági mentés megkezdése előtt a kötetet fel kell oldani.
Fájlrendszer azonosítása| Nem támogatott |Csak az NTFS használata támogatott.
Cserélhető adathordozó| Nem támogatott |Az összes biztonságimásolat-tétel forrásának *rögzített* állapottal kell rendelkeznie.
Deduplikált meghajtók | Támogatott | Azure Backup a deduplikált adatmennyiséget a normál adatértékre konvertálja. Optimalizálja, titkosítja, tárolja és elküldi az adatokat a tárolónak.

## <a name="support-for-initial-offline-backup"></a>A kezdeti offline biztonsági mentés támogatása

Azure Backup támogatja az *Offline előkészítést* a kezdeti biztonsági mentési adatoknak az Azure-ba történő átviteléhez lemezek használatával. Ez a támogatás akkor hasznos, ha a kezdeti biztonsági mentés valószínűleg terabájt (TBs) tartományba esik. Az offline biztonsági mentés a következő módon támogatott:

- A fájlok és mappák közvetlen biztonsági mentése a MARS-ügynököt futtató helyszíni gépeken.
- Munkaterhelések és fájlok biztonsági mentése DPM-kiszolgálóról vagy MABS.

Az offline biztonsági mentés nem használható rendszerállapot-fájlokhoz.

## <a name="support-for-data-restoration"></a>Az Adathelyreállítás támogatása

Azure Backup [azonnali visszaállítási](backup-instant-restore-capability.md) funkciójával visszaállíthatja az adataikat a tárolóba való másolás előtt. A számítógépnek, amelyről biztonsági másolatot készít, .NET-keretrendszer 4.5.2-es vagy újabb verzióját kell futtatnia.

A biztonsági mentések nem állíthatók vissza olyan célszámítógépre, amely az operációs rendszer korábbi verzióját futtatja. Például egy Windows 7 rendszerű számítógépről készített biztonsági mentés visszaállítható a Windows 8 vagy újabb verzióra. A Windows 8 rendszert futtató számítógépekről azonban nem lehet visszaállítani a Windows 7 rendszert futtató számítógépeken.

## <a name="next-steps"></a>Következő lépések

- További információ [a Mars-ügynököt használó biztonsági mentési architektúráról](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Ismerje meg, hogy mi támogatott [a Mars-ügynök MABS vagy DPM-kiszolgálón való futtatásakor](backup-support-matrix-mabs-dpm.md).
