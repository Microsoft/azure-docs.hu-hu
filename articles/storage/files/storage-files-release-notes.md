---
title: Kibocsátási megjegyzések a Azure File Sync-ügynökhöz | Microsoft Docs
description: Olvassa el a Azure File Sync-ügynök kibocsátási megjegyzéseit, amelyekkel központilag központosíthatja a szervezet fájlmegosztást Azure Filesokban.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 4/7/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 4c046129293fcfbcea8ecaf98da72b9126dd540a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030338"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Az Azure File Sync ügynök kibocsátási megjegyzései
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. A Windows Server-telepítéseket az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). Annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A jelen cikk az Azure File Sync ügynök támogatott verzióinak kibocsátási megjegyzéseit ismerteti.

## <a name="supported-versions"></a>Támogatott verziók
A következő Azure File Sync-ügynök verziói támogatottak:

| Mérföldkő | Az ügynök verziószáma | Kiadási dátum | Állapot |
|----|----------------------|--------------|------------------|
| V12-es kiadás – [KB4568585](https://support.microsoft.com/topic/b9605f04-b4af-4ad8-86b0-2c490c535cfd)| 12.0.0.0 | Március 26., 2021 | Támogatott – Berepülés |
| V 11.3 kiadás – [KB4539953](https://support.microsoft.com/topic/f68974f6-bfdd-44f4-9659-bf2d8a696c26)| 11.3.0.0 | 2021. április 7. | Támogatott |
| V 11,2 kiadás – [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2021. február 2. | Támogatott |
| V 11.1 kiadás – [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | November 4., 2020 | Támogatott |
| V 10.1 kiadás – [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 2020. június 5. | Támogatott – az ügynök verziószáma 2021. június 7-én lejár |
| 2020. május kumulatív frissítés – [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | Május 19., 2020 | Támogatott – az ügynök verziószáma 2021. június 7-én lejár |
| V10-es kiadás – [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 2020. április 9. | Támogatott – az ügynök verziószáma 2021. június 7-én lejár |

## <a name="unsupported-versions"></a>Nem támogatott verziók
A következő Azure File Sync ügynök verziói lejártak, és már nem támogatottak:

| Mérföldkő | Az ügynök verziószáma | Kiadási dátum | Állapot |
|----|----------------------|--------------|------------------|
| V9-es kiadás | 9.0.0.0 - 9.1.0.0 | N/A | Nem támogatott – az ügynök verziója a 2021. február 16-án lejárt |
| V8-as kiadás | 8.0.0.0 | N/A | Nem támogatott – az ügynök verziója január 12-én lejárt, 2021 |
| V7-kiadás | 7.0.0.0 - 7.2.0.0 | N/A | Nem támogatott – az ügynök verziói 2020 szeptember 1-jén elévültek |
| V6-os kiadás | 6.0.0.0 – 6.3.0.0 | N/A | Nem támogatott – az ügynök verziószáma 2020. április 21-én lejárt |
| V5-kiadás | 5.0.2.0 – 5.2.0.0 | N/A | Nem támogatott – az ügynök verziói a 2020. március 18-án lejártak. |
| V4-kiadás | 4.0.1.0 – 4.3.0.0 | N/A | Nem támogatott – az ügynök verziói 2019 november 6-án lejártak |
| V3 kiadás | 3.1.0.0 – 3.4.0.0 | N/A | Nem támogatott – az ügynök verziói lejártak a 2019-es augusztus 19-én |
| Pre-GA ügynökök | 1.1.0.0 – 3.0.13.0 | N/A | Nem támogatott – az ügynök verziói lejártak 2018 október 1-jén |

### <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-12000"></a>Ügynök verziója 12.0.0.0
A következő kibocsátási megjegyzések a Azure File Sync-ügynök verziójának 12.0.0.0 vonatkoznak (2021. március 26.).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák
- Új portál a hálózati hozzáférési házirend és a magánhálózati végponti kapcsolatok konfigurálásához
    - Mostantól a portál használatával letilthatja a Storage Sync szolgáltatás nyilvános végpontjának elérését, valamint a privát végpontok kapcsolatainak jóváhagyását, elutasítását és eltávolítását. A hálózati hozzáférési házirend és a magánhálózati végpont kapcsolatainak konfigurálásához nyissa meg a Storage Sync szolgáltatás portált, lépjen a beállítások szakaszra, és kattintson a hálózat lehetőségre.
 
- A felhő-rétegek támogatása a 64KiB nagyobb méretű kötetek esetében
    - A felhő-rétegek mostantól támogatják a 2MiB a 2019-es kiszolgálón. További információ: [Mi a fájl minimális mérete a szinthez?](https://docs.microsoft.com/azure/storage/files/storage-sync-choose-cloud-tiering-policies#minimum-file-size-for-a-file-to-tier)
 
- Sávszélesség és késés mérése Azure File Sync szolgáltatás-és Storage-fiókhoz
    - A Test-StorageSyncNetworkConnectivity parancsmag mostantól felhasználható a késés és a sávszélesség mérésére a Azure File Sync szolgáltatás és a Storage-fiók számára. A Azure File Sync szolgáltatás és a Storage-fiók késését a rendszer alapértelmezés szerint a parancsmag futtatásakor méri.  A "-MeasureBandwidth" paraméter használatakor a rendszer a Storage-fiók sávszélességének feltöltését és letöltését méri.
 
        Ha például a sávszélességet és a késést szeretné mérni a Azure File Sync szolgáltatás és a Storage-fiók számára, futtassa a következő PowerShell-parancsokat:
 
        ```powershell
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
        Test-StorageSyncNetworkConnectivity -MeasureBandwidth 
        ``` 
 
- Továbbfejlesztett hibaüzenetek a portálon, ha a kiszolgálói végpont létrehozása meghiúsul
    - Meghallottuk visszajelzését, és javítottuk a hibaüzeneteket és útmutatást, amikor a kiszolgálói végpont létrehozása meghiúsul.
 
- A teljesítmény és a megbízhatóság különféle fejlesztése
    - Továbbfejlesztett változás-észlelési teljesítmény az Azure-fájlmegosztás által módosított fájlok észlelése érdekében.
    - Teljesítménybeli tökéletesítések az egyeztetések szinkronizálási munkameneteihez. 
    - A ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED és az ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED hibák csökkentése érdekében a szinkronizálási javításokat.
    - Kijavítva az adatsérülést okozó hiba, ha a felhőalapú rétegek engedélyezve vannak, és a rendszer a (z)/B paraméterrel másolja a többplatformos fájlokat a Robocopy használatával.
    - Kijavítva egy olyan hibát, amely miatt a fájlok sikertelenek lehetnek a 2019-es kiszolgálón, ha az deduplikálás engedélyezve van a köteten.
    - Javítva egy hiba, amely miatt a AFSDiag nem tudja tömöríteni a fájlokat, ha egy fájl nagyobb, mint a 2GiB.

### <a name="evaluation-tool"></a>Kiértékelési eszköz
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely a fájlrendszer és az adatkészlet esetleges problémáit ellenőrzi, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítési és használati utasításokért tekintse meg a tervezési útmutató [kiértékelési eszköz](./storage-sync-files-planning.md#evaluation-cmdlet) című szakaszát. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
A Azure File Sync ügynök Windows Serverrel való telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Azure file Sync központi telepítés tervezése](storage-sync-files-planning.md) és a [Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md).

- A meglévő Azure File Sync ügynök telepítésével rendelkező kiszolgálókhoz újraindításra van szükség.
- Az ügynök telepítési csomagját emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a nano Server-telepítés esetén.
- Az ügynök csak a Windows Server 2019, a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynök legalább 2 GiB memóriát igényel. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni. További információért lásd: [ajánlott rendszererőforrások](./storage-sync-files-planning.md#recommended-system-resources) .
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja az olyan köteteken található kiszolgálói végpontokat, amelyeken a rendszerkötet információi (SVI) könyvtára tömörítve van. Ez a konfiguráció nem várt eredményeket fog eredményezni.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információ: [Azure file Sync hibáinak megoldása](storage-sync-files-troubleshoot.md).
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha a fájlok a fájl képernyője miatt le vannak tiltva.
- A Sysprep futtatása olyan kiszolgálón, amelyen a Azure File Sync ügynök telepítve van, és nem várt eredményekhez vezethet. A Azure File Sync-ügynököt telepíteni kell a kiszolgálói lemezkép telepítése és a Sysprep minitelepítés befejezése után.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karaktereket tartalmazó fájlok. A nem támogatott karakterek listáját a [hibaelhárítási útmutatóban](storage-sync-files-troubleshoot.md#handling-unsupported-characters) találja.
- Egy ponttal lezáró fájlok vagy könyvtárak.
- A 2048 karakternél hosszabb elérési utak.
- Biztonsági leíró naplózáshoz használt rendszerhozzáférés-vezérlési lista (SACL) része.
- Kiterjesztett attribútumok.
- Alternatív adatstreamek.
- Újraelemzési pontok.
- Rögzített hivatkozások.
- A tömörítés (ha kiszolgálófájlon van beállítva) nem marad meg, amikor a módosításokat erre a fájlra szinkronizálja más végpontokból.
- Az EFS (vagy más felhasználói módú) titkosítással titkosított összes fájl, amely meggátolja, hogy a szolgáltatás beolvassa az adatokat.

    > [!Note]  
    > Az Azure File Sync mindig titkosítja az átvitt adatokat. Az adatok inaktív állapotban is mindig titkosítva vannak az Azure-ban.
 
### <a name="server-endpoint"></a>Kiszolgálói végpont
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Az Azure-fájlmegosztás által módosított fájlok azonnali szinkronizálásához a [Meghívási AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell-parancsmag használatával manuálisan indíthatja el az Azure-fájlmegosztás változásainak észlelését. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoport, előfizetés vagy Azure AD-bérlőbe. A Storage Sync szolgáltatás vagy a Storage-fiók áthelyezése után meg kell adnia a Microsoft. StorageSync alkalmazás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage-fiókhoz](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Felhőbeli végpont létrehozásakor a Storage Sync szolgáltatásnak és a Storage-fióknak ugyanabban az Azure AD-bérlőben kell lennie. A Felhőbeli végpont létrehozása után a Storage Sync szolgáltatás és a Storage-fiók áthelyezhető a különböző Azure AD-bérlők között.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Fájlok a Robocopy használatával történő másolásakor használja a/MIR beállítást a fájl időbélyegének megőrzéséhez. Ezzel biztosíthatja, hogy a régebbi fájlok a legutóbb használt fájlokhoz legyenek bontva.

## <a name="agent-version-11300"></a>Ügynök verziója 11.3.0.0
A következő kibocsátási megjegyzések a Azure File Sync ügynök verziójának 11.3.0.0 készültek, amely 2021. április 7-én jelent meg. Ezek a megjegyzések a 11.1.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák 
Kijavítva az adatsérülést okozó hiba, ha a felhőalapú rétegek engedélyezve vannak, és a rendszer a (z)/B paraméterrel másolja a többplatformos fájlokat a Robocopy használatával.

## <a name="agent-version-11200"></a>Ügynök verziója 11.2.0.0
A következő kibocsátási megjegyzések a Azure File Sync-ügynök verziójának 11.2.0.0 vonatkoznak, amely 2021. február 2-án jelent meg. Ezek a megjegyzések a 11.1.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák 
- Ha a szinkronizálási munkamenetet nagy számú/elem típusú hiba miatt megszakítják, akkor a szinkronizálás akkor is eltérhet, ha új munkamenet indul el, ha az Azure File Sync szolgáltatás meghatározza, hogy az egyes elemekkel kapcsolatos hibák kijavításához szükség van-e egyéni szinkronizálási munkamenetre.
- A kiszolgálók Register-AzStorageSyncServer parancsmaggal történő regisztrálása meghiúsulhat a "Kezeletlen kivétel" hibával.
- Új PowerShell-parancsmag (Add-StorageSyncAllowedServerEndpointPath) az engedélyezett kiszolgálói végpontok elérési útjának konfigurálásához egy kiszolgálón. Ez a parancsmag olyan esetekben hasznos, amikor a Azure File Sync üzembe helyezését egy felhőalapú megoldás-szolgáltató (CSP) vagy egy szolgáltató kezeli, és az ügyfél az engedélyezett kiszolgálói végpontok elérési útját szeretné konfigurálni egy kiszolgálón. Kiszolgálói végpont létrehozásakor, ha a megadott elérési út nem szerepel a engedélyezési, a kiszolgálói végpont létrehozása sikertelen lesz. Vegye figyelembe, hogy ez egy opcionális funkció, és az összes támogatott útvonal alapértelmezés szerint engedélyezett a kiszolgálói végpontok létrehozásakor.  

    
    - Az engedélyezett kiszolgálói végpont elérési útjának hozzáadásához futtassa a következő PowerShell-parancsokat a kiszolgálón:

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - A támogatott elérési utak listájának lekéréséhez futtassa a következő PowerShell-parancsot:
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Egy elérési út eltávolításához futtassa a következő PowerShell-parancsot:
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>Ügynök verziója 11.1.0.0
A következő kibocsátási megjegyzések a Azure File Sync ügynök verziójának 11.1.0.0 vonatkoznak (2020. november 4.).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák
- Új felhőalapú rétegek a kezdeti letöltés és a proaktív visszahívás vezérléséhez
    - Kezdeti letöltési mód: mostantól kiválaszthatja, hogyan szeretné először letölteni a fájlokat az új kiszolgálói végpontra. Szeretné, hogy az összes fájl felrakott legyen, vagy a lehető legtöbb fájlt letöltse a kiszolgálóra az utolsó módosítás időbélyegzője alapján? Ezt megteheti! Nem használható a felhőalapú rétegek használata? Most már dönthet úgy is, hogy elkerüli a többszintű fájlokat a rendszeren. További információért lásd: [kiszolgálói végpont létrehozása](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) szakasz, Azure file Sync dokumentációjának központi telepítése.
    - Proaktív visszahívás módja: Ha egy fájl létrehozása vagy módosítása történik, proaktív módon visszahívhatja azokat a kiszolgálókat, amelyeket az adott szinkronizálási csoporton belül megad. Így a fájl azonnal elérhető lesz az egyes megadott kiszolgálókon. A világ különböző csoportjain dolgozik ugyanazon az adategységen? Proaktív visszahívás engedélyezése, hogy amikor a csapat másnap reggel megérkezik, a rendszer letölti és készen áll a csapat által egy másik időzónában frissített összes fájl letöltésére. További információért lásd: az [új és módosított fájlok proaktív felidézése egy Azure fájlmegosztás](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) szakaszból a Azure file Sync dokumentációjának központi telepítése című részben.

- Alkalmazások kizárása a Felhőbeli rétegek utolsó hozzáférés időpontjának nyomon követésével mostantól kizárhatja a legutóbbi hozzáférési idő követésének alkalmazásait. Amikor egy alkalmazás hozzáfér egy fájlhoz, a rendszer a fájl utolsó elérési idejét frissíti a felhő-rétegű adatbázisban. Azok az alkalmazások, amelyek a fájlrendszert (például Anti-Virus) ellenőrzik, az összes fájlhoz ugyanazt az utolsó hozzáférési időt okozzák, ami hatással van a fájlok rétegbeli megadására.

    Ha ki szeretné zárni az alkalmazásokat az utolsó hozzáférés időpontjának nyomon követéséhez, adja hozzá a folyamat nevét a HeatTrackingProcessNameExclusionList beállításjegyzék-beállításhoz, amely a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync alatt található.

    Például: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

    > [!Note]  
    > Az deduplikálás és a Fájlkiszolgálói erőforrás-kezelő (FSRM) folyamata alapértelmezés szerint ki van zárva (Hard Coded), és a folyamat kizárási listája 5 percenként frissül.

- A teljesítmény és a megbízhatóság különféle fejlesztése
    - Továbbfejlesztett változás-észlelési teljesítmény az Azure-fájlmegosztás által módosított fájlok észlelése érdekében.
    - Továbbfejlesztett szinkronizációs feltöltési teljesítmény.
    - A kezdeti feltöltés mostantól egy VSS-pillanatképből történik, amely csökkenti az egyes elemek hibáit, és szinkronizálja a munkamenetek hibáit.
    - Szinkronizálhatja az egyes I/O-minták megbízhatósági változásait.
    - Javítva egy hiba, amely megakadályozza, hogy a szinkronizálási adatbázis a feladatátvételi fürtökön visszafelé visszalépjen a feladatátvevő fürtökön.
    - Továbbfejlesztett visszahívási teljesítmény egy többplatformos fájl elérésekor.

### <a name="evaluation-tool"></a>Kiértékelési eszköz
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely a fájlrendszer és az adatkészlet esetleges problémáit ellenőrzi, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítési és használati utasításokért tekintse meg a tervezési útmutató [kiértékelési eszköz](./storage-sync-files-planning.md#evaluation-cmdlet) című szakaszát. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
A Azure File Sync ügynök Windows Serverrel való telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Azure file Sync központi telepítés tervezése](storage-sync-files-planning.md) és a [Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md).

- Az ügynök telepítési csomagját emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a nano Server-telepítés esetén.
- Az ügynök csak a Windows Server 2019, a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynök legalább 2 GiB memóriát igényel. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni. További információért lásd: [ajánlott rendszererőforrások](./storage-sync-files-planning.md#recommended-system-resources) .
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja az olyan köteteken található kiszolgálói végpontokat, amelyeken a rendszerkötet információi (SVI) könyvtára tömörítve van. Ez a konfiguráció nem várt eredményeket fog eredményezni.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információ: [Azure file Sync hibáinak megoldása](storage-sync-files-troubleshoot.md).
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha a fájlok a fájl képernyője miatt le vannak tiltva.
- A Sysprep futtatása olyan kiszolgálón, amelyen a Azure File Sync ügynök telepítve van, és nem várt eredményekhez vezethet. A Azure File Sync-ügynököt telepíteni kell a kiszolgálói lemezkép telepítése és a Sysprep minitelepítés befejezése után.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karaktereket tartalmazó fájlok. A nem támogatott karakterek listáját a [hibaelhárítási útmutatóban](storage-sync-files-troubleshoot.md#handling-unsupported-characters) találja.
- Egy ponttal lezáró fájlok vagy könyvtárak.
- A 2048 karakternél hosszabb elérési utak.
- Biztonsági leíró naplózáshoz használt rendszerhozzáférés-vezérlési lista (SACL) része.
- Kiterjesztett attribútumok.
- Alternatív adatstreamek.
- Újraelemzési pontok.
- Rögzített hivatkozások.
- A tömörítés (ha kiszolgálófájlon van beállítva) nem marad meg, amikor a módosításokat erre a fájlra szinkronizálja más végpontokból.
- Az EFS (vagy más felhasználói módú) titkosítással titkosított összes fájl, amely meggátolja, hogy a szolgáltatás beolvassa az adatokat.

    > [!Note]  
    > Az Azure File Sync mindig titkosítja az átvitt adatokat. Az adatok inaktív állapotban is mindig titkosítva vannak az Azure-ban.
 
### <a name="server-endpoint"></a>Kiszolgálói végpont
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Az Azure-fájlmegosztás által módosított fájlok azonnali szinkronizálásához a [Meghívási AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell-parancsmag használatával manuálisan indíthatja el az Azure-fájlmegosztás változásainak észlelését. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoport, előfizetés vagy Azure AD-bérlőbe. A Storage Sync szolgáltatás vagy a Storage-fiók áthelyezése után meg kell adnia a Microsoft. StorageSync alkalmazás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage-fiókhoz](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Felhőbeli végpont létrehozásakor a Storage Sync szolgáltatásnak és a Storage-fióknak ugyanabban az Azure AD-bérlőben kell lennie. A Felhőbeli végpont létrehozása után a Storage Sync szolgáltatás és a Storage-fiók áthelyezhető a különböző Azure AD-bérlők között.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Fájlok a Robocopy használatával történő másolásakor használja a/MIR beállítást a fájl időbélyegének megőrzéséhez. Ezzel biztosíthatja, hogy a régebbi fájlok a legutóbb használt fájlokhoz legyenek bontva.
    > [!Warning]  
    > A Robocopy/B kapcsoló nem támogatott a Azure File Sync. A Robocopy/B kapcsoló és egy Azure File Sync kiszolgálói végpont használata esetén a forrás a fájl sérüléséhez vezethet.

## <a name="agent-version-10100"></a>Ügynök verziója 10.1.0.0
A következő kibocsátási megjegyzések a Azure File Sync ügynök verziójának 10.1.0.0, amely 2020. június 5-én jelent meg. Ezek a megjegyzések a 10.0.0.0 és a 10.0.2.0 verzióhoz tartozó kibocsátási megjegyzéseken kívül is szerepelnek.

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák

- Azure Private Endpoint-támogatás
    - A Storage Sync szolgáltatás felé irányuló forgalom szinkronizálása most már egy privát végpontra is elküldhető. Ez lehetővé teszi, hogy a bújtatás ExpressRoute vagy VPN-kapcsolaton keresztül történjen. További információ: [Azure file Sync hálózati végpontok konfigurálása](./storage-sync-files-networking-endpoints.md).
- A fájlok szinkronizált metrikája mostantól a folyamat végén is megjeleníti az előrehaladást, nem pedig a végponton.
- Az ügynök telepítésének, a felhő-rétegek, a szinkronizálás és a telemetria különböző megbízhatósági fejlesztése

## <a name="agent-version-10020"></a>Ügynök verziója 10.0.2.0
A következő kibocsátási megjegyzések a Azure File Sync ügynök verziójának 10.0.2.0 vonatkoznak, amely 2020. május 19-én jelent meg. Ezek a megjegyzések a 10.0.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

Ebben a kiadásban rögzített probléma:  
- A Storage Sync Agent (FileSyncSvc) a Azure File Sync v10-ügynök telepítése után gyakran összeomlik.

> [!Note]  
>Ezt a kiadást nem olyan kiszolgálók indították, amelyek automatikusan frissülnek, amikor új verzió válik elérhetővé. A frissítés telepítéséhez használja Microsoft Update vagy Microsoft Update katalógust (lásd: [KB4522412](https://support.microsoft.com/help/4522412) for Installation instructions).

## <a name="agent-version-10000"></a>Ügynök verziója 10.0.0.0
A következő kibocsátási megjegyzések az Azure File Sync-ügynök verziójának 10.0.0.0 vonatkoznak (2020. április 9.).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák

- Továbbfejlesztett szinkronizálási folyamat a portálon
    - A V10-es ügynök kiadásával a Azure Portal hamarosan megkezdi a futó szinkronizálási munkamenet típusának megjelenítését. Például kezdeti letöltés, normál letöltés, háttér-visszahívás (gyors vész-helyreállítási esetek) és hasonló. 

- Továbbfejlesztett felhőalapú többplatformos portál
    - Ha olyan fájlokkal rendelkezik, amelyek nem képesek a rétegek vagy a visszahívás megadására, most megtekintheti a rétegek hibáit a kiszolgálói végpont tulajdonságaiban.
    - A Felhőbeli végpontok további információi elérhetők a kiszolgálói végpontok számára:
        - Helyi gyorsítótár mérete
        - Gyorsítótár-használat hatékonysága
        - A felhőre vonatkozó szabályok részletei: kötet mérete, jelenlegi szabad terület, vagy a legrégebbi fájl utolsó elérési ideje a helyi gyorsítótárban.
    - Ezek a módosítások a kezdeti V10-es ügynök kiadása után röviddel a Azure Portalre kerülnek.

- A Storage Sync szolgáltatás és/vagy a Storage-fiók egy másik Azure Active Directory bérlőbe való áthelyezésének támogatása
    - Azure File Sync mostantól támogatja a Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezését egy másik erőforráscsoport, előfizetés vagy Azure AD-bérlő számára.
    
- A teljesítmény és a megbízhatóság különféle fejlesztése
    - Az Azure-fájlmegosztás észlelésének módosítása sikertelen lehet, ha a virtuális hálózat (VNET) és a tűzfalszabályok konfigurálva vannak a Storage-fiókban.
    - Csökkentett memóriahasználat a visszahíváshoz társítva. 
    - Jobb teljesítmény a [Meghívási-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) parancsmag használatakor.
    - Egyéb egyéb megbízhatósági változások. 
    
### <a name="evaluation-tool"></a>Kiértékelési eszköz
Azure File Sync telepítése előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync próbaverziós eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely a fájlrendszer és az adatkészlet esetleges problémáit ellenőrzi, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítési és használati utasításokért tekintse meg a tervezési útmutató [kiértékelési eszköz](./storage-sync-files-planning.md#evaluation-cmdlet) című szakaszát. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
A Azure File Sync ügynök Windows Serverrel való telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Azure file Sync központi telepítés tervezése](storage-sync-files-planning.md) és a [Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md).

- Az ügynök telepítési csomagját emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a nano Server-telepítés esetén.
- Az ügynök csak a Windows Server 2019, a Windows Server 2016 és a Windows Server 2012 R2 rendszeren támogatott.
- Az ügynök legalább 2 GiB memóriát igényel. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja az olyan köteteken található kiszolgálói végpontokat, amelyeken a rendszerkötet információi (SVI) könyvtára tömörítve van. Ez a konfiguráció nem várt eredményeket fog eredményezni.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információ: [Azure file Sync hibáinak megoldása](storage-sync-files-troubleshoot.md).
- A Fájlkiszolgálói erőforrás-kezelő (FSRM) fájlszűrők végtelen szinkronizálási hibákat okozhatnak, ha a fájlok a fájl képernyője miatt le vannak tiltva.
- A Sysprep futtatása olyan kiszolgálón, amelyen a Azure File Sync ügynök telepítve van, és nem várt eredményekhez vezethet. A Azure File Sync-ügynököt telepíteni kell a kiszolgálói lemezkép telepítése és a Sysprep minitelepítés befejezése után.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karaktereket tartalmazó fájlok. A nem támogatott karakterek listáját a [hibaelhárítási útmutatóban](storage-sync-files-troubleshoot.md#handling-unsupported-characters) találja.
- Egy ponttal lezáró fájlok vagy könyvtárak.
- A 2048 karakternél hosszabb elérési utak.
- Biztonsági leíró naplózáshoz használt rendszerhozzáférés-vezérlési lista (SACL) része.
- Kiterjesztett attribútumok.
- Alternatív adatstreamek.
- Újraelemzési pontok.
- Rögzített hivatkozások.
- A tömörítés (ha kiszolgálófájlon van beállítva) nem marad meg, amikor a módosításokat erre a fájlra szinkronizálja más végpontokból.
- Az EFS (vagy más felhasználói módú) titkosítással titkosított összes fájl, amely meggátolja, hogy a szolgáltatás beolvassa az adatokat.

    > [!Note]  
    > Az Azure File Sync mindig titkosítja az átvitt adatokat. Az adatok inaktív állapotban is mindig titkosítva vannak az Azure-ban.
 
### <a name="server-endpoint"></a>Kiszolgálói végpont
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Az Azure-fájlmegosztás által módosított fájlok azonnali szinkronizálásához a [Meghívási AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell-parancsmag használatával manuálisan indíthatja el az Azure-fájlmegosztás változásainak észlelését. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoport, előfizetés vagy Azure AD-bérlőbe. A Storage Sync szolgáltatás vagy a Storage-fiók áthelyezése után meg kell adnia a Microsoft. StorageSync alkalmazás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage-fiókhoz](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Felhőbeli végpont létrehozásakor a Storage Sync szolgáltatásnak és a Storage-fióknak ugyanabban az Azure AD-bérlőben kell lennie. A Felhőbeli végpont létrehozása után a Storage Sync szolgáltatás és a Storage-fiók áthelyezhető a különböző Azure AD-bérlők között.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Fájlok a Robocopy használatával történő másolásakor használja a/MIR beállítást a fájl időbélyegének megőrzéséhez. Ezzel biztosíthatja, hogy a régebbi fájlok a legutóbb használt fájlokhoz legyenek bontva.
