---
title: A Azure File Sync ügynök | kibocsátási megjegyzései Microsoft Docs
description: Olvassa el a Azure File Sync ügynök kibocsátási megjegyzéseit, amellyel központosíthatja a szervezet fájlmegosztását a Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 4/7/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 9c00b2d4d30ac417d58f2b69e4ba460789cf6583
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796824"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Az Azure File Sync ügynök kibocsátási megjegyzései
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. A Windows Server-telepítéseket az Azure-fájlmegosztás gyors gyorsítótáraivá alakítja át. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). Annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A jelen cikk az Azure File Sync ügynök támogatott verzióinak kibocsátási megjegyzéseit ismerteti.

## <a name="supported-versions"></a>Támogatott verziók
Az alábbi Azure File Sync ügynökverziók támogatottak:

| Mérföldkő | Az ügynök verziószáma | Kiadási dátum | Állapot |
|----|----------------------|--------------|------------------|
| V12 kiadás – [KB4568585](https://support.microsoft.com/topic/b9605f04-b4af-4ad8-86b0-2c490c535cfd)| 12.0.0.0 | 2021. március 26. | Támogatott – Flighting |
| 11.3-as verzió kiadása – [KB4539953](https://support.microsoft.com/topic/f68974f6-bfdd-44f4-9659-bf2d8a696c26)| 11.3.0.0 | 2021. április 7. | Támogatott |
| 11.2-es verzió kiadása – [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2021. február 2. | Támogatott |
| 11.1-es verzió kiadása – [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 2020. november 4. | Támogatott |
| 10.1-es verzió kiadása – [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 2020. június 5. | Támogatott – Az ügynök verziója 2021. június 7-én lejár |
| 2020. májusi összesítő frissítés – [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 2020. május 19. | Támogatott – Az ügynök verziója 2021. június 7-én lejár |
| 10-es verzió kiadása – [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 2020. április 9. | Támogatott – Az ügynök verziója 2021. június 7-én lejár |

## <a name="unsupported-versions"></a>Nem támogatott verziók
Az alábbi Azure File Sync ügynökverziók lejártak, és már nem támogatottak:

| Mérföldkő | Az ügynök verziószáma | Kiadási dátum | Állapot |
|----|----------------------|--------------|------------------|
| 9-es verzió kiadása | 9.0.0.0 - 9.1.0.0 | N/A | Nem támogatott – Az ügynök verziója 2021. február 16-án lejárt |
| V8-as kiadás | 8.0.0.0 | N/A | Nem támogatott – Az ügynök verziója 2021. január 12-én lejárt |
| V7 kiadás | 7.0.0.0 - 7.2.0.0 | N/A | Nem támogatott – Az ügynökverziók 2020. szeptember 1-én lejártak |
| 6-os verzió kiadása | 6.0.0.0 - 6.3.0.0 | N/A | Nem támogatott – Az ügynökverziók 2020. április 21-én lejártak |
| 5-ös verzió kiadása | 5.0.2.0 - 5.2.0.0 | N/A | Nem támogatott – Az ügynökverziók 2020. március 18-án lejártak |
| V4 kiadás | 4.0.1.0 - 4.3.0.0 | N/A | Nem támogatott – Az ügynökverziók 2019. november 6-án lejártak |
| V3 kiadás | 3.1.0.0 - 3.4.0.0 | N/A | Nem támogatott – Az ügynökverziók 2019. augusztus 19-én lejártak |
| Ga előtti ügynökök | 1.1.0.0 - 3.0.13.0 | N/A | Nem támogatott – Az ügynökverziók 2018. október 1-én lejártak |

### <a name="azure-file-sync-agent-update-policy"></a>Az Azure File Sync ügynökének frissítési szabályzata
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-12000"></a>Ügynök 12.0.0.0-s verziója
Az alábbi kibocsátási megjegyzések a Azure File Sync-ügynök 12.0.0.0-s verziójára (2021. március 26-án jelentek meg).

### <a name="improvements-and-issues-that-are-fixed"></a>Kijavított fejlesztések és problémák
- Új portálélmény a hálózati hozzáférési szabályzatok és a privát végponti kapcsolatok konfigurálásához
    - Most már használhatja a portált a Storage Sync Service nyilvános végponthoz való hozzáférés letiltására, valamint a privát végpontkapcsolatok jóváhagyására, elutasítására és eltávolítására. A hálózati hozzáférési szabályzat és a privát végpont kapcsolatainak konfigurálásához nyissa meg a Társzinkronizálási szolgáltatás portálját, nyissa meg a Beállítások szakaszt, és kattintson a Hálózat elemre.
 
- Felhőbeli rétegezés támogatása a 64KiB-t nagyobb kötetfürtök esetében
    - A felhőbeli rétegezés mostantól legfeljebb 2MiB méretű kötetfürtöt támogat a Server 2019-ben. További tudnivalókért lásd: Mi a rétegzett fájlok minimális [fájlmérete?](https://docs.microsoft.com/azure/storage/files/storage-sync-choose-cloud-tiering-policies#minimum-file-size-for-a-file-to-tier).
 
- A szolgáltatás és a tárfiók Azure File Sync késésének mérése
    - A Test-StorageSyncNetworkConnectivity parancsmag mostantól használható a szolgáltatás és a tárfiók késésének és sávszélességének Azure File Sync mérésére. Az Azure File Sync szolgáltatás és tárfiók késését a rendszer alapértelmezés szerint méri a parancsmag futtatásakor.  A rendszer a "-MeasureBandwidth" paraméter használatával méri a tárfiók feltöltési és letöltési sávszélességét.
 
        A szolgáltatás Azure File Sync és a tárfiók sávszélességének és késésének méréséhez például futtassa a következő PowerShell-parancsokat:
 
        ```powershell
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
        Test-StorageSyncNetworkConnectivity -MeasureBandwidth 
        ``` 
 
- Továbbfejlesztett hibaüzenetek a portálon, ha a kiszolgálóvégpont létrehozása sikertelen
    - Visszajelzést kaptunk, és továbbfejlesztettük a hibaüzeneteket és az útmutatást, ha a kiszolgálóvégpont létrehozása meghiúsul.
 
- Egyéb teljesítménnyel és megbízhatósággal kapcsolatos fejlesztések
    - Továbbfejlesztett módosításészlelési teljesítmény az Azure-fájlmegosztásban módosult fájlok észleléséhez.
    - Teljesítménnyel kapcsolatos fejlesztések az egyeztetési szinkronizálási munkamenetekhez. 
    - Szinkronizálási fejlesztések a hibák ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED és ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED csökkentése érdekében.
    - Kijavítottunk egy hibát, amely adatsérülést okoz, ha a felhőbeli rétegzés engedélyezve van, és a rétegzett fájlok másolása a Robocopy és a /B paraméter használatával történik.
    - Kijavítottunk egy hibát, amely miatt a fájlok rétege meghiúsulhat a Server 2019-ben, ha az Adatdeduplikáció engedélyezve van a köteten.
    - Kijavítottunk egy hibát, amely miatt az AFSDiag nem tudja tömöríteni a fájlokat, ha egy fájl nagyobb, mint 2GiB.

### <a name="evaluation-tool"></a>Kiértékelési eszköz
A Azure File Sync előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel az Azure File Sync eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely ellenőrzi a fájlrendszer és az adatkészlet esetleges problémáit, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítéssel és a használattal kapcsolatban lásd a tervezési útmutató [Kiértékelési](file-sync-planning.md#evaluation-cmdlet) eszköz című szakaszát. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
A Azure File Sync-ügynök Windows Serverrel való telepítésére és konfigurálásra vonatkozó további információkért lásd: [Planning for an Azure File Sync deployment](file-sync-planning.md) and How to deploy [Azure File Sync](file-sync-deployment-guide.md).

- Újraindításra van szükség olyan kiszolgálók esetén, amelyeken már van Azure File Sync ügynök telepítése.
- Az ügynök telepítőcsomagját emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a Nano Server üzembe helyezési lehetőség esetén.
- Az ügynök csak Windows Server 2019, Windows Server 2016 és Windows Server 2012 R2 rendszeren támogatott.
- Az ügynöknek legalább 2 GiB memóriára van szüksége. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni. További [információ:](file-sync-planning.md#recommended-system-resources) Ajánlott rendszererőforrások.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja az olyan köteten található kiszolgálóvégpontokat, amelyeken a rendszerkötet-információk (SVI) könyvtára tömörítve van. Ez a konfiguráció váratlan eredményekhez vezet.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információ: Hibaelhárítás [Azure File Sync.](file-sync-troubleshoot.md)
- A fájlkiszolgáló Resource Manager (FSRM) fájlképernyői végtelen szinkronizálási hibákat okozhatnak, ha a fájlok a fájlképernyő miatt le vannak tiltva.
- A sysprep nem támogatott olyan kiszolgálón való futtatása, Azure File Sync ügynök telepítve van, és váratlan eredményekhez vezethet. A Azure File Sync ügynököt a kiszolgálói lemezkép üzembe helyezése és a sysprep minibeállítás befejezése után kell telepíteni.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karaktereket tartalmaz. A [nem támogatott karakterek](file-sync-troubleshoot.md#handling-unsupported-characters) listájáért tekintse meg a hibaelhárítási útmutatót.
- Pontra végződő fájlok vagy könyvtárak.
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
 
### <a name="server-endpoint"></a>Kiszolgálóvégpont
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tárolja az operációs rendszer vagy az alkalmazás lapozófájlját egy kiszolgálóvégponton.
- A kiszolgáló neve a portálon nem frissül, ha a kiszolgálót átnevezik.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosításait. Az Azure-fájlmegosztáson végrehajtott módosításokat azonban először egy változásészlelési feladatnak Azure File Sync észlelni. A rendszer 24 óránként kezdeményez változásészlelési feladatot egy felhőbeli végponton. Az Azure-fájlmegosztásban módosított fájlok azonnali szinkronizálásához az [Invoke-AzStorageSyncChangeDetection PowerShell-parancsmag](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) használatával manuálisan kezdeményezhető az Azure-fájlmegosztás változásainak észlelése. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB legutóbbi módosítási idejét, és nem fognak a szinkronizálás által módosulni.
- A társzinkronizálási szolgáltatás és/vagy tárfiók áthelyezható egy másik erőforráscsoportba, előfizetésbe vagy Azure AD-bérlőbe. Miután áthelyezte a társzinkronizálási szolgáltatást vagy a tárfiókot, hozzáférést kell biztosítania a Microsoft.StorageSync alkalmazásnak a tárfiókhoz (lásd: Győződjön meg arról, hogy az Azure File Sync hozzáféréssel rendelkezik a [tárfiókhoz).](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > A felhőbeli végpont létrehozásakor a társzinkronizálási szolgáltatásnak és a tárfióknak ugyanabban az Azure AD-bérlőben kell lennie. A felhőbeli végpont létrehozása után a társzinkronizálási szolgáltatás és a tárfiók áthelyezható a különböző Azure AD-bérlőkbe.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- A fájlok robocopyval való másolása során használja a /COPY kapcsolót a fájlok időbélyegének megőrzéséhez. Ez biztosítja, hogy a régebbi fájlok a közelmúltban elért fájloknál hamarabb rétegzettek.

## <a name="agent-version-11300"></a>Ügynök 11.3.0.0-s verziója
A következő kibocsátási megjegyzések a 2021. április 7-én kiadott Azure File Sync 11.3.0.0-s verziójára vonatkozóak. Ezek a megjegyzések a 11.1.0.0-s verzióhoz felsorolt kibocsátási megjegyzéseken kívül szerepelnek.

### <a name="improvements-and-issues-that-are-fixed"></a>Kijavított fejlesztések és problémák 
Kijavítottunk egy hibát, amely adatsérülést okoz, ha a felhőbeli rétegzés engedélyezve van, és a rétegzett fájlok másolása a Robocopy és a /B paraméter használatával történik.

## <a name="agent-version-11200"></a>Ügynök 11.2.0.0-s verziója
Az alábbi kibocsátási megjegyzések a Azure File Sync ügynök 2021. február 2-i kiadásának 11.2.0.0-s verziójára vonatkozóak. Ezek a megjegyzések a 11.1.0.0-s verzióhoz felsorolt kibocsátási megjegyzéseken kívül szerepelnek.

### <a name="improvements-and-issues-that-are-fixed"></a>Kijavított fejlesztések és problémák 
- Ha egy szinkronizálási munkamenetet elemenkénti hibák nagy száma miatt megszakít, a szinkronizálás az új munkamenet indításakor az egyeztetésen keresztül is előfordulhat, ha a Azure File Sync szolgáltatás úgy dönt, hogy egyéni szinkronizálási munkamenetre van szükség az elemenkénti hibák kijavítása érdekében.
- A kiszolgáló a Register-AzStorageSyncServer parancsmaggal való regisztrálása "Nem kezelt kivétel" hibával meghiúsulhat.
- Új PowerShell-parancsmag (Add-StorageSyncAllowedServerEndpointPath) a kiszolgáló engedélyezett kiszolgálóvégpont-útvonalának konfiguráláshoz. Ez a parancsmag olyan helyzetekben hasznos, amikor a Azure File Sync-telepítést egy Felhőszolgáltató (CSP) vagy egy szolgáltató kezeli, és az ügyfél konfigurálni szeretné az engedélyezett kiszolgálóvégpont-útvonalakat egy kiszolgálón. Kiszolgálóvégpont létrehozásakor, ha a megadott elérési út nem található meg az engedélyezési listán, a kiszolgálóvégpont létrehozása sikertelen lesz. Vegye figyelembe, hogy ez egy választható funkció, és a kiszolgálóvégpont létrehozásakor alapértelmezés szerint az összes támogatott elérési út engedélyezve van.  

    
    - Egy engedélyezett kiszolgálóvégpont elérési útjának hozzáadásához futtassa a következő PowerShell-parancsokat a kiszolgálón:

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - A támogatott elérési utak listájának lekért listájához futtassa a következő PowerShell-parancsot:
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Az elérési út eltávolításához futtassa a következő PowerShell-parancsot:
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>Ügynök 11.1.0.0-s verziója
Az alábbi kibocsátási megjegyzések a Azure File Sync-ügynök 11.1.0.0-s verziójára (2020. november 4-én jelentek meg).

### <a name="improvements-and-issues-that-are-fixed"></a>Kijavított fejlesztések és problémák
- Új felhőbeli rétegezésmódok a kezdeti letöltés és a proaktív előhívás szabályozása érdekében
    - Kezdeti letöltési mód: Mostantól kiválaszthatja, hogyan szeretné letölteni a fájlokat az új kiszolgálóvégpontra. Szeretné, hogy az összes fájl rétegzett legyen, vagy a lehető legtöbb fájlt töltse le a kiszolgálóra az utolsó módosítás időbélyegzőjével? Ezt is meg tudja tenni! Nem használhat felhőbeli rétegezést? Most már dönthet úgy, hogy elkerüli a rétegzett fájlokat a rendszeren. További tudnivalókért lásd a Deploy Azure File Sync documentation (A virtuális gép üzembe helyezése) dokumentáció Create [a server endpoint](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) (Kiszolgálói végpont létrehozása) című szakaszát.
    - Proaktív előhívási mód: egy fájl létrehozásakor vagy módosításakor proaktívan elő lehet idézni az ugyanazon a szinkronizálási csoporton belül megadott kiszolgálókon. Ez lehetővé teszi, hogy a fájl használatra kész legyen minden megadott kiszolgálón. A világ különböző táján dolgozó csapatok ugyanazokon az adatokon dolgoznak? Engedélyezze a proaktív visszahívást, hogy amikor a csapat legközelebb reggel megérkezik, a csapat által egy másik időzónában frissített összes fájl letöltve és használatra kész. További tudnivalókért [](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) lásd a Deploy Azure File Sync documentation (Új és módosított fájlok proaktív visszahívása azure-fájlmegosztásból) című szakaszát.

- Alkalmazások kizárása a felhőbeli rétegezés utolsó hozzáférési ideje nyomon követéséből Mostantól kizárhatja az alkalmazásokat az utolsó hozzáférési idő nyomon követéséből. Amikor egy alkalmazás hozzáfér egy fájlhoz, a fájl utolsó hozzáférési ideje frissül a felhőbeli rétegzett adatbázisban. A fájlrendszert, például víruskeresőt beolvasó alkalmazások miatt minden fájlnak ugyanaz lesz az utolsó hozzáférési ideje, ami a rétegzett fájlokra is hatással van.

    Ha ki szeretne zárni alkalmazásokat az utolsó hozzáférés-követésből, adja hozzá a folyamat nevét a HeatTrackingProcessNameExclusionList beállításjegyzék-beállításhoz, amely a következő helyen HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

    Például: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

    > [!Note]  
    > Az adatdeduplikáció és a fájlkiszolgálói Resource Manager (FSRM) folyamat alapértelmezés szerint ki van zárva (nem kódolt), és a folyamatkizárási lista 5 percenként frissül.

- Egyéb teljesítménnyel és megbízhatósággal kapcsolatos fejlesztések
    - Továbbfejlesztett módosításészlelési teljesítmény az Azure-fájlmegosztásban módosult fájlok észleléséhez.
    - Továbbfejlesztett szinkronizálási feltöltési teljesítmény.
    - A kezdeti feltöltés most egy VSS-pillanatképből történik, amely csökkenti az elemenkénti hibákat és a szinkronizálási munkamenet hibáit.
    - A szinkronizálás megbízhatóságának fejlesztései bizonyos I/O-minták esetében.
    - Ki van javítva egy hiba, amely miatt a szinkronizálási adatbázis nem fog visszaesni az időben feladatátvételi fürtökön feladatátvétel esetén.
    - Továbbfejlesztett visszahívási teljesítmény rétegzett fájlok elérésekor.

### <a name="evaluation-tool"></a>Kiértékelési eszköz
A Azure File Sync előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel a Azure File Sync eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely ellenőrzi a fájlrendszer és az adatkészlet esetleges problémáit, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítéssel és a használattal kapcsolatban lásd a tervezési útmutató [Kiértékelési](../file-sync/file-sync-planning.md#evaluation-cmdlet) eszköz című szakaszát. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
A Azure File Sync-ügynök Windows Serverrel való telepítésére és konfigurálásra vonatkozó további információkért lásd: [Planning for an Azure File Sync deployment](../file-sync/file-sync-planning.md) and How to deploy [Azure File Sync](../file-sync/file-sync-deployment-guide.md).

- Az ügynök telepítőcsomagját emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a Nano Server üzembe helyezési lehetőség esetén.
- Az ügynök csak Windows Server 2019, Windows Server 2016 és Windows Server 2012 R2 rendszeren támogatott.
- Az ügynöknek legalább 2 GiB memóriára van szüksége. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni. További [információ:](../file-sync/file-sync-planning.md#recommended-system-resources) Ajánlott rendszererőforrások.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja az olyan köteten található kiszolgálóvégpontokat, amelyeken a rendszerkötet-információk (SVI) könyvtára tömörítve van. Ez a konfiguráció váratlan eredményekhez vezet.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információ: Hibaelhárítás [Azure File Sync.](../file-sync/file-sync-troubleshoot.md)
- A fájlkiszolgáló Resource Manager (FSRM) fájlképernyői végtelen szinkronizálási hibákat okozhatnak, ha a fájlok a fájlképernyő miatt le vannak tiltva.
- A sysprep nem támogatott olyan kiszolgálón való futtatása, Azure File Sync ügynök telepítve van, és váratlan eredményekhez vezethet. A Azure File Sync ügynököt a kiszolgálói lemezkép üzembe helyezése és a sysprep minibeállítás befejezése után kell telepíteni.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karaktereket tartalmaz. A [nem támogatott karakterek](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters) listájáért tekintse meg a hibaelhárítási útmutatót.
- Pontra végződő fájlok vagy könyvtárak.
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
 
### <a name="server-endpoint"></a>Kiszolgálóvégpont
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tárolja az operációs rendszer vagy az alkalmazás lapozófájlját egy kiszolgálóvégponton.
- A kiszolgáló neve a portálon nem frissül, ha a kiszolgálót átnevezik.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosításait. Az Azure-fájlmegosztáson végrehajtott módosításokat azonban először egy változásészlelési feladatnak Azure File Sync észlelni. A rendszer 24 óránként kezdeményez változásészlelési feladatot egy felhőbeli végponton. Az Azure-fájlmegosztásban módosított fájlok azonnali szinkronizálásához az [Invoke-AzStorageSyncChangeDetection PowerShell-parancsmag](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) használatával manuálisan kezdeményezhető az Azure-fájlmegosztás változásainak észlelése. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB legutóbbi módosítási idejét, és nem fognak a szinkronizálás által módosulni.
- A társzinkronizálási szolgáltatás és/vagy tárfiók áthelyezható egy másik erőforráscsoportba, előfizetésbe vagy Azure AD-bérlőbe. Miután áthelyezte a társzinkronizálási szolgáltatást vagy a tárfiókot, hozzáférést kell biztosítania a Microsoft.StorageSync alkalmazásnak a tárfiókhoz (lásd: Győződjön meg arról, hogy az Azure File Sync hozzáféréssel rendelkezik a [tárfiókhoz).](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > A felhőbeli végpont létrehozásakor a társzinkronizálási szolgáltatásnak és a tárfióknak ugyanabban az Azure AD-bérlőben kell lennie. A felhőbeli végpont létrehozása után a társzinkronizálási szolgáltatás és a tárfiók áthelyezható a különböző Azure AD-bérlőkbe.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- A fájlok robocopyval való másolása során használja a /COPY kapcsolót a fájlok időbélyegének megőrzéséhez. Ez biztosítja, hogy a régebbi fájlok a közelmúltban elért fájloknál hamarabb rétegzettek.
    > [!Warning]  
    > A Robocopy/B kapcsoló nem támogatott a Azure File Sync. Ha a Robocopy /B kapcsolót egy Azure File Sync-kiszolgálóvégponttal használja, mivel a forrás fájlsérüléshez vezethet.

## <a name="agent-version-10100"></a>Ügynök 10.1.0.0-s verziója
A következő kibocsátási megjegyzések a 2020. június 5-én kiadott Azure File Sync 10.1.0.0-s verziójára vonatkozóak. Ezek a megjegyzések a 10.0.0.0-s és 10.0.2.0-s verzióhoz felsorolt kibocsátási megjegyzéseken kívül szerepelnek.

### <a name="improvements-and-issues-that-are-fixed"></a>Kijavított fejlesztések és problémák

- Privát Azure-végpontok támogatása
    - A forgalom szinkronizálása a Társzinkronizálási szolgáltatással mostantól privát végpontra is elküldható. Ez lehetővé teszi az ExpressRoute- vagy VPN-kapcsolaton keresztüli bújtatást. További információ: [Hálózati Azure File Sync konfigurálása.](../file-sync/file-sync-networking-endpoints.md)
- A Files Synced (Szinkronizált fájlok) metrika mostantól nem a végén, hanem egy nagy méretű szinkronizálás futása közben jeleníti meg a folyamat előrehaladását.
- Egyéb megbízhatósági fejlesztések az ügynöktelepítés, a felhőbeli rétegezés, a szinkronizálás és a telemetria terén

## <a name="agent-version-10020"></a>Ügynök 10.0.2.0-s verziója
Az alábbi kibocsátási megjegyzések a Azure File Sync 2020. május 19-én kiadott 10.0.2.0-s verziójára vonatkozóak. Ezek a megjegyzések a 10.0.0.0-s verzióhoz felsorolt kibocsátási megjegyzéseken kívül szerepelnek.

A probléma megoldva ebben a kiadásban:  
- A Storage Sync Agent (FileSyncSvc) gyakran összeomlik a Azure File Sync 10-es Azure File Sync telepítése után.

> [!Note]  
>Ez a kiadás nem lett átküldve olyan kiszolgálókra, amelyek úgy vannak konfigurálva, hogy automatikusan frissülni tudjanak, amikor új verzió válik elérhetővé. A frissítés telepítéséhez használja a Microsoft Update vagy Microsoft Update katalógust (a telepítési utasításokért lásd: [KB4522412).](https://support.microsoft.com/help/4522412)

## <a name="agent-version-10000"></a>Ügynök 10.0.0.0-s verziója
Az alábbi kibocsátási megjegyzések a Azure File Sync ügynök 10.0.0.0-s verziójára (2020. április 9-én jelentek meg).

### <a name="improvements-and-issues-that-are-fixed"></a>Kijavított fejlesztések és problémák

- Továbbfejlesztett szinkronizálási folyamat a portálon
    - A V10-ügynök kiadásával a Azure Portal hamarosan elkezdi a futó szinkronizálási munkamenet típusát. Például kezdeti letöltés, rendszeres letöltés, háttér-visszahívás (gyors vészhelyreállítási esetek) és hasonlók. 

- Továbbfejlesztett felhőbeli rétegezés a portálon
    - Ha egyes fájlok rétegezése vagy visszahívása sikertelen, most már megtekintheti a rétegezés hibáit a kiszolgálóvégpont tulajdonságai között.
    - További felhőbeli rétegző információk érhetők el a kiszolgálóvégponthoz:
        - Helyi gyorsítótár mérete
        - Gyorsítótár-használat hatékonysága
        - A felhőbeli rétegezés szabályzatának részletei: kötetméret, aktuális szabad terület, vagy a legrégebbi fájl utolsó elérhető ideje a helyi gyorsítótárban.
    - Ezek a módosítások röviddel a 10-es verzió első kiadását Azure Portal követően megjelenik a verzióban.

- A Storage Sync szolgáltatás és/vagy tárfiók másik bérlőbe Azure Active Directory támogatása
    - Azure File Sync mostantól támogatja a Társzinkronizálási szolgáltatás és/vagy tárfiók másik erőforráscsoportba, előfizetésbe vagy Azure AD-bérlőbe való áthelyezését.
    
- Egyéb teljesítménnyel és megbízhatósággal kapcsolatos fejlesztések
    - Az Azure-fájlmegosztás módosításészlelése sikertelen lehet, ha a tárfiókon virtuális hálózat (VNET) és tűzfalszabályok vannak konfigurálva.
    - A visszahívással társított csökkentett memóriafelhasználás. 
    - Jobb teljesítmény az [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) parancsmag használata esetén.
    - Egyéb egyéb megbízhatósági fejlesztések. 
    
### <a name="evaluation-tool"></a>Kiértékelési eszköz
A Azure File Sync előtt ki kell értékelnie, hogy kompatibilis-e a rendszerrel az Azure File Sync eszköz használatával. Ez az eszköz egy Azure PowerShell parancsmag, amely ellenőrzi a fájlrendszerrel és az adatkészletkel kapcsolatos esetleges problémákat, például a nem támogatott karaktereket vagy az operációs rendszer nem támogatott verzióját. A telepítéssel és a használattal kapcsolatban lásd a tervezési útmutató [Kiértékelési](../file-sync/file-sync-planning.md#evaluation-cmdlet) eszköz című szakaszát. 

### <a name="agent-installation-and-server-configuration"></a>Az ügynök telepítése és a kiszolgáló konfigurálása
A Azure File Sync-ügynök Windows Serverrel való telepítésére és konfigurálásra vonatkozó további információkért lásd: [Planning for an Azure File Sync deployment](../file-sync/file-sync-planning.md) and How to deploy [Azure File Sync](../file-sync/file-sync-deployment-guide.md).

- Az ügynök telepítőcsomagját emelt szintű (rendszergazdai) engedélyekkel kell telepíteni.
- Az ügynök nem támogatott a Nano Server üzembe helyezési lehetőség esetén.
- Az ügynök csak Windows Server 2019, Windows Server 2016 és Windows Server 2012 R2 rendszeren támogatott.
- Az ügynöknek legalább 2 GiB memóriára van szüksége. Ha a kiszolgáló olyan virtuális gépen fut, amelyen engedélyezve van a dinamikus memória, a virtuális gépet legalább 2048 MiB memóriával kell konfigurálni.
- A Storage Sync Agent (FileSyncSvc) szolgáltatás nem támogatja az olyan köteten található kiszolgálóvégpontokat, amelyeken a rendszerkötet-információk (SVI) könyvtára tömörítve van. Ez a konfiguráció váratlan eredményekhez vezet.

### <a name="interoperability"></a>Együttműködési lehetőség
- A víruskeresők, a biztonsági mentést készítő és a rétegzett fájlokhoz hozzáférő más alkalmazások nem kívánt újrahívást okozhatnak, ha nem veszik figyelembe az offline attribútumot, és nem mellőzik ezen fájlok tartalmának olvasását. További információ: Hibaelhárítás [Azure File Sync.](../file-sync/file-sync-troubleshoot.md)
- A fájlkiszolgáló Resource Manager (FSRM) fájlképernyői végtelen szinkronizálási hibákat okozhatnak, ha a fájlok a fájlképernyő miatt le vannak tiltva.
- A sysprep nem támogatott olyan kiszolgálón való futtatása, Azure File Sync ügynök telepítve van, és váratlan eredményekhez vezethet. A Azure File Sync ügynököt a kiszolgálói lemezkép üzembe helyezése és a sysprep minibeállítás befejezése után kell telepíteni.

### <a name="sync-limitations"></a>A szinkronizálás korlátozásai
A következő elemek nem szinkronizálhatók, de a rendszer többi része továbbra is a szokott módon működik:
- Nem támogatott karaktereket tartalmaz. A [nem támogatott karakterek](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters) listájáért tekintse meg a hibaelhárítási útmutatót.
- Pontra végződő fájlok vagy könyvtárak.
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
 
### <a name="server-endpoint"></a>Kiszolgálóvégpont
- Kiszolgálóvégpontok csak NTFS-köteteken hozhatók létre. Az ReFS, FAT, FAT32 és más fájlrendszereket az Azure File Sync jelenleg nem támogatja.
- A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.
- A feladatátvételi fürtszolgáltatás csak fürtözött lemezekkel támogatott, megosztott fürtkötetekkel (CSV-kkel) nem.
- A kiszolgálóvégpontok nem ágyazhatók be. Ugyanazon a köteten azonban szerepelhetnek egymás mellett.
- Ne tárolja az operációs rendszer vagy az alkalmazás lapozófájlját kiszolgálóvégpont-helyen.
- A kiszolgáló neve a portálon nem frissül, ha a kiszolgálót átnevezik.

### <a name="cloud-endpoint"></a>Felhőbeli végpont
- Azure File Sync támogatja az Azure-fájlmegosztás közvetlen módosításait. Az Azure-fájlmegosztáson végrehajtott módosításokat azonban először egy változásészlelési feladatnak Azure File Sync észlelni. A rendszer 24 óránként kezdeményez változásészlelési feladatot egy felhőbeli végponton. Az Azure-fájlmegosztásban módosított fájlok azonnali szinkronizálásához az [Invoke-AzStorageSyncChangeDetection PowerShell-parancsmag](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) használatával manuálisan kezdeményezhető az Azure-fájlmegosztás változásainak észlelése. Emellett az Azure-fájlmegosztás REST protokollon keresztül végrehajtott módosításai nem frissítik az SMB legutóbbi módosítási idejét, és nem fognak a szinkronizálás által módosulni.
- A társzinkronizálási szolgáltatás és/vagy tárfiók áthelyezható egy másik erőforráscsoportba, előfizetésbe vagy Azure AD-bérlőbe. Miután áthelyezte a társzinkronizálási szolgáltatást vagy a tárfiókot, hozzáférést kell biztosítania a Microsoft.StorageSync alkalmazásnak a tárfiókhoz (lásd: Győződjön meg arról, hogy az Azure File Sync hozzáféréssel rendelkezik a [tárfiókhoz).](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > A felhőbeli végpont létrehozásakor a társzinkronizálási szolgáltatásnak és a tárfióknak ugyanabban az Azure AD-bérlőben kell lennie. A felhőbeli végpont létrehozása után a társzinkronizálási szolgáltatás és a tárfiók áthelyezható a különböző Azure AD-bérlőkbe.

### <a name="cloud-tiering"></a>Felhőbeli rétegzés
- Ha egy rétegzett fájlt a Robocopy használatával másik helyre másol, az eredményül kapott fájl nem lesz rétegzett. Előfordulhat, hogy az offline attribútum be lesz állítva, mert a Robocopy helytelenül belefoglalja ezt az attribútumot a másolási műveletekbe.
- A fájlok robocopyval való másolása során használja a /COPY kapcsolót a fájlok időbélyegének megőrzéséhez. Ez biztosítja, hogy a régebbi fájlok a közelmúltban elért fájloknál hamarabb rétegzettek.
