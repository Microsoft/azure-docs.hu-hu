---
title: Kibocsátási megjegyzések a Azure File Sync-ügynökhöz | Microsoft Docs
description: Olvassa el a Azure File Sync-ügynök kibocsátási megjegyzéseit, amelyekkel központilag központosíthatja a szervezet fájlmegosztást Azure Filesokban.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 4dd1941e6a749c2ccf7ca082ab89ef2a70baa0d6
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509536"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Az Azure File Sync ügynök kibocsátási megjegyzései
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. A Windows Server-telepítéseket az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). Annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A jelen cikk az Azure File Sync ügynök támogatott verzióinak kibocsátási megjegyzéseit ismerteti.

## <a name="supported-versions"></a>Támogatott verziók
A következő Azure File Sync-ügynök verziói támogatottak:

| Mérföldkő | Az ügynök verziószáma | Kiadási dátum | Állapot |
|----|----------------------|--------------|------------------|
| V 11,2 kiadás – [KB4539952](https://support.microsoft.com/en-us/help/4539952)| 11.2.0.0 | 2021. február 2. | Támogatott – Berepülés |
| V 11.1 kiadás – [KB4539951](https://support.microsoft.com/en-us/help/4539951)| 11.1.0.0 | November 4., 2020 | Támogatott |
| V 10.1 kiadás – [KB4522411](https://support.microsoft.com/en-us/help/4522411)| 10.1.0.0 | 2020. június 5. | Támogatott |
| 2020. május kumulatív frissítés – [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | Május 19., 2020 | Támogatott |
| V10-es kiadás – [KB4522409](https://support.microsoft.com/en-us/help/4522409)| 10.0.0.0 | 2020. április 9. | Támogatott |
| 2019. decemberi kumulatív frissítés – [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 2019. december 12. | Támogatott – az ügynök verziója a 2021. február 16-án lejár |
| V9-es kiadás – [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2019. december 2. | Támogatott – az ügynök verziója a 2021. február 16-án lejár |
| V8-as kiadás – [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 2019. október 8. | Támogatott – az ügynök verziója január 12-én lejár, 2021 |

## <a name="unsupported-versions"></a>Nem támogatott verziók
A következő Azure File Sync ügynök verziói lejártak, és már nem támogatottak:

| Mérföldkő | Az ügynök verziószáma | Kiadási dátum | Állapot |
|----|----------------------|--------------|------------------|
| V7-kiadás | 7.0.0.0 - 7.2.0.0 | N/A | Nem támogatott – az ügynök verziói 2020 szeptember 1-jén elévültek |
| V6-os kiadás | 6.0.0.0 – 6.3.0.0 | N/A | Nem támogatott – az ügynök verziószáma 2020. április 21-én lejárt |
| V5-kiadás | 5.0.2.0 – 5.2.0.0 | N/A | Nem támogatott – az ügynök verziói a 2020. március 18-án lejártak. |
| V4-kiadás | 4.0.1.0 – 4.3.0.0 | N/A | Nem támogatott – az ügynök verziói 2019 november 6-án lejártak |
| V3 kiadás | 3.1.0.0 – 3.4.0.0 | N/A | Nem támogatott – az ügynök verziói lejártak a 2019-es augusztus 19-én |
| Pre-GA ügynökök | 1.1.0.0 – 3.0.13.0 | N/A | Nem támogatott – az ügynök verziói lejártak 2018 október 1-jén |

### <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-11200"></a>Ügynök verziója 11.2.0.0
A következő kibocsátási megjegyzések a Azure File Sync-ügynök verziójának 11.2.0.0 vonatkoznak, amely 2021. február 2-án jelent meg. Ezek a megjegyzések a 11.1.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák 
- Ha a szinkronizálási munkamenetet nagy számú/elem típusú hiba miatt megszakították, akkor a szinkronizálás akkor is megtörténhet, ha új munkamenet indul el, ha a Azure File Sync szolgáltatás meghatározza, hogy az egyes elemekkel kapcsolatos hibák kijavításához szükség van-e egyéni szinkronizálási munkamenetre.
- A kiszolgálók Register-AzStorageSyncServer parancsmaggal történő regisztrálása meghiúsulhat a "Kezeletlen kivétel" hibával.
- Új PowerShell-parancsmag (Add-StorageSyncAllowedServerEndpointPath) az engedélyezett kiszolgálói végpontok elérési útjának konfigurálásához egy kiszolgálón. Ez a parancsmag olyan esetekben hasznos, amikor a Azure File Sync üzembe helyezését egy felhőalapú megoldás-szolgáltató (CSP) vagy egy szolgáltató kezeli, és az ügyfél az engedélyezett kiszolgálói végpontok elérési útját szeretné konfigurálni egy kiszolgálón. Kiszolgálói végpont létrehozásakor, ha a megadott elérési út nem szerepel az engedélyezési listán, a kiszolgálói végpont létrehozása sikertelen lesz. Vegye figyelembe, hogy ez egy opcionális funkció, és az összes támogatott útvonal alapértelmezés szerint engedélyezett a kiszolgálói végpontok létrehozásakor.  

    
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

## <a name="agent-version-9100"></a>Ügynök verziója 9.1.0.0
Az alábbi kibocsátási megjegyzések a 2019-es december 12-én kiadott Azure File Sync-ügynök verziójának 9.1.0.0 vonatkoznak. Ezek a megjegyzések a 9.0.0.0 verziójának kibocsátási megjegyzései mellett szerepelnek.

Ebben a kiadásban rögzített probléma:  
- A szinkronizálás a következő hibák egyikével meghiúsult a Azure File Sync Agent 9,0-es verzióra való frissítés után:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Ügynök verziója 9.0.0.0
A következő kibocsátási megjegyzések az Azure File Sync-ügynök verziójának 9.0.0.0 vonatkoznak (2019. december 2.).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák

- Önkiszolgáló visszaállítás támogatása
    - A felhasználók mostantól a köteten lévő önkiszolgáló visszaállítási funkció engedélyezése után is visszaállíthatják a többrétegű fájlokat (a lemezen lévő fájlokkal együtt) az előző verzió funkció használatával. A V9-es kiadás előtt a korábbi verzió funkció nem támogatott a többplatformos fájlokban. Ezt a funkciót külön kell engedélyezni az egyes köteteken, amelyeken engedélyezve van egy, a felhőalapú rétegek használatát engedélyező végpont. További információk:  
Önkiszolgáló [Visszaállítás az előző verziók és a VSS (Kötet árnyékmásolata szolgáltatás) használatával](./storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Nagyobb fájlmegosztás-méretek támogatása 
    - Azure File Sync mostantól legfeljebb 64TiB és 100 000 000 fájlt támogat egyetlen, szinkronizált névtérben.  
 
- Az deduplikálás támogatása a 2019-es kiszolgálón 
    - Az deduplikálás mostantól támogatott (függetlenül attól, hogy a felhő-rétegek engedélyezve vannak-e vagy le vannak tiltva a köteten egy vagy több kiszolgálói végponton) a Windows Server 2016 és a Windows Server 2019 rendszeren. Ha az 2019-es kiszolgálón a felhő-előírásokkal rendelkező köteteken lévő Adatmásolást szeretné támogatni, telepítenie kell a Windows Update [KB4520062](https://support.microsoft.com/help/4520062) . 
 
- Magasabb fájlméret a fájl számára a szinthez 
    - A fájlnak a réteghez való minimális mérete mostantól a fájlrendszer fürtjének méretétől függ (a fájlrendszer fürt méretének kétszerese). Alapértelmezés szerint például az NTFS fájlrendszerű fürt mérete 4KB, a fájlhoz tartozó minimális fájlméret pedig 8 kb. 
 
- Hálózati kapcsolat tesztelése parancsmag 
    - Azure File Sync konfiguráció részeként több szolgáltatás-végpontot is kapcsolatba kell lépnie. Mindegyiknek saját DNS-névvel kell rendelkeznie, amelynek a kiszolgálónak elérhetőnek kell lennie. Ezek az URL-címek arra a régióra is jellemzőek, amelyhez a kiszolgáló regisztrálva van. A kiszolgáló regisztrálását követően a kapcsolati teszt parancsmag (PowerShell és kiszolgáló regisztrálása) használható a kiszolgálóhoz tartozó összes URL-címmel folytatott kommunikáció tesztelésére. Ez a parancsmag segít elhárítani a hiányos kommunikációt, ami megakadályozza, hogy a kiszolgáló teljes mértékben működjön a Azure File Sync, és használható a proxy és a tűzfal konfigurációinak finomhangolására.  
 
        A hálózati kapcsolati teszt futtatásához futtassa a következő PowerShell-parancsokat: 
 
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- A kiszolgálói végpontok javításának eltávolítása, ha engedélyezve van a felhőalapú rétegek használata 
    - Ahogy korábban is, a kiszolgálói végpont eltávolítása nem eredményezi a fájlok eltávolítását az Azure-fájlmegosztás során. A helyi kiszolgálón található újraelemzési pontok viselkedése azonban megváltozott. A kiszolgálói végpontok eltávolításakor a rendszer törli az újraelemzési pontokat (a kiszolgálón nem helyi fájlok mutatóit). A teljes gyorsítótárban lévő fájlok a kiszolgálón maradnak. Ezzel a fejlesztéssel megakadályozható, hogy a rendszer az [árva rétegekből álló fájlokat](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) a kiszolgálói végpontok eltávolításakor megakadályozza. Ha a kiszolgálói végpontot újra létrehozza, a rendszer újból létrehozza a lépcsőzetes fájlok újraelemzési pontjait a kiszolgálón.  
 
- Teljesítmény-és megbízhatósági változások 
    - Csökkentett visszahívási hibák. A visszahívás mérete mostantól automatikusan igazodik a hálózati sávszélesség alapján. 
    - Továbbfejlesztett letöltési teljesítmény új kiszolgáló szinkronizálási csoportba való felvételekor. 
    - A korlátozási ütközések miatt nem szinkronizált fájlok csökkentése. 
    - A fájlok sikertelenek lesznek, vagy bizonyos helyzetekben váratlanul visszahívhatók, ha a kiszolgálói végpont elérési útja egy kötet csatlakoztatási pontja.
    
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
- A biztonsági leíró tulajdonosi hozzáférés-vezérlési lista (DACL) része, ha az 2 KB-nál nagyobb. (Ez csak akkor probléma, ha több mint 40 hozzáférés-vezérlési bejegyzés (ACE) van egyetlen elemen.)
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
- A többcsoportos fájlok elérhetetlenné válnak, ha a rendszer nem hívja meg a fájlokat a kiszolgálói végpont törlése előtt. A fájlok elérésének visszaállításához hozza létre újra a kiszolgálói végpontot. Ha a kiszolgáló-végpont törlése vagy a Felhőbeli végpont törlése óta 30 nap telt el, akkor a nem visszanevezett, többelemű fájlok használhatatlanná válhatnak. További információért lásd a kiszolgálói [végpont törlése után a kiszolgálón nem érhetők el a lépcsőzetes fájlok](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Az Azure-fájlmegosztás által módosított fájlok azonnali szinkronizálásához a [Meghívási AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell-parancsmag használatával manuálisan indíthatja el az Azure-fájlmegosztás változásainak észlelését. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoporthoz vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha áthelyezi a Storage-fiókot, meg kell adnia a hibrid File Sync szolgáltatás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage-fiókhoz](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Azure File Sync nem támogatja az előfizetés másik Azure AD-bérlőre való áthelyezését.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Fájlok a Robocopy használatával történő másolásakor használja a/MIR beállítást a fájl időbélyegének megőrzéséhez. Ezzel biztosíthatja, hogy a régebbi fájlok a legutóbb használt fájlokhoz legyenek bontva.
- Ha a pagefile.sys olyan köteten található, amelynél engedélyezve van a felhőalapú rétegek használata, előfordulhat, hogy a fájlok sikertelenek lehetnek. A pagefile.sys egy olyan köteten kell lennie, amely a felhőalapú rétegek letiltásával rendelkezik.

## <a name="agent-version-8000"></a>Ügynök verziója 8.0.0.0
A következő kibocsátási megjegyzések a Azure File Sync ügynök verziójának 8.0.0.0 vonatkoznak (2019. október 8.).

### <a name="improvements-and-issues-that-are-fixed"></a>Javított javítás és problémák

- Teljesítmény-javító funkciók visszaállítása
    - A helyreállítás gyorsabb helyreállítási ideje Azure Backupon keresztül. A visszaállított fájlok sokkal gyorsabban szinkronizálva lesznek Azure File Sync kiszolgálókkal. 
- Továbbfejlesztett felhőalapú többplatformos portál  
    - Ha olyan lépcsőzetes fájlokkal rendelkezik, amelyek nem tudnak visszahívni, most megtekintheti a visszahívási hibákat a kiszolgálói végpont tulajdonságaiban. Emellett a kiszolgálói végpont állapota mostantól hibát és enyhítő lépéseket jelez, ha a Felhőbeli rétegű szűrő illesztőprogramja nincs betöltve a kiszolgálón.
- Egyszerűbb ügynök telepítése
    - A Az\AzureRM PowerShell-modul már nem szükséges a kiszolgáló regisztrálásához, amely egyszerűbbé és gyors telepítést tesz szükségessé.
- A teljesítmény és a megbízhatóság különféle fejlesztése

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
- A biztonsági leíró tulajdonosi hozzáférés-vezérlési lista (DACL) része, ha az 2 KB-nál nagyobb. (Ez csak akkor probléma, ha több mint 40 hozzáférés-vezérlési bejegyzés (ACE) van egyetlen elemen.)
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
- A többcsoportos fájlok elérhetetlenné válnak, ha a rendszer nem hívja meg a fájlokat a kiszolgálói végpont törlése előtt. A fájlok elérésének visszaállításához hozza létre újra a kiszolgálói végpontot. Ha a kiszolgáló-végpont törlése vagy a Felhőbeli végpont törlése óta 30 nap telt el, akkor a nem visszanevezett, többelemű fájlok használhatatlanná válhatnak. További információért lásd a kiszolgálói [végpont törlése után a kiszolgálón nem érhetők el a lépcsőzetes fájlok](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tároljon operációs rendszer vagy alkalmazás lapozófájlját a kiszolgálói végpontok helyén.
- A kiszolgáló neve nem frissül a portálon, ha a kiszolgáló át lett nevezve.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosítását. Az Azure-fájlmegosztást érintő módosításokat azonban először egy Azure File Sync változás-észlelési feladatokkal kell felderíteni. A rendszer 24 óránként egyszer egy változás-észlelési feladatot kezdeményez a Felhőbeli végpontok számára. Az Azure-fájlmegosztás által módosított fájlok azonnali szinkronizálásához a [Meghívási AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell-parancsmag használatával manuálisan indíthatja el az Azure-fájlmegosztás változásainak észlelését. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB utolsó módosításának időpontját, és a szinkronizálás nem lesz látható változásként.
- A Storage Sync szolgáltatás és/vagy a Storage-fiók áthelyezhető egy másik erőforráscsoporthoz vagy előfizetésbe a meglévő Azure AD-bérlőn belül. Ha áthelyezi a Storage-fiókot, meg kell adnia a hibrid File Sync szolgáltatás elérését a Storage-fiókhoz (lásd: [Azure file Sync hozzáférése a Storage-fiókhoz](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > A Azure File Sync nem támogatja az előfizetés másik Azure AD-bérlőre való áthelyezését.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- Fájlok a Robocopy használatával történő másolásakor használja a/MIR beállítást a fájl időbélyegének megőrzéséhez. Ezzel biztosíthatja, hogy a régebbi fájlok a legutóbb használt fájlokhoz legyenek bontva.
