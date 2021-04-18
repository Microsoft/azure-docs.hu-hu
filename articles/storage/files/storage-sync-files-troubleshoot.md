---
title: Hibaelhárítási Azure File Sync | Microsoft Docs
description: A windowsos virtuális Azure File Sync gyakori problémáinak elhárítása, amelyek segítségével a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárába alakíthatja át.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 4/12/2021
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: bf74b3a1659547772368c9fb394eeab8321b5f5d
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599638"
---
# <a name="troubleshoot-azure-file-sync"></a>Azure-fájlok szinkronizálásának hibaelhárítása
A Azure File Sync központosíthatja a szervezet fájlmegosztását a Azure Files-ban, miközben a helyszíni fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását is biztosítja. Az Azure File Sync a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárává alakítja át. A Windows Serveren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl.: SMB, NFS vagy FTPS). Annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

Ez a cikk segítséget ad az üzembe helyezés során felmerülő problémák elhárításához és Azure File Sync megoldásához. Azt is ismertetjük, hogyan gyűjthetőek a rendszer fontos naplói, ha a probléma mélyebb kivizsgálására van szükség. Ha nem látja a választ a kérdésére, lépjen kapcsolatba velünk a következő csatornákon (eszkalációs sorrendben):

1. [Microsoft Q&Az Azure Storage kérdésoldala.](/answers/products/azure?product=storage)
2. [Azure Files UserVoice .](https://feedback.azure.com/forums/217298-storage/category/180670-files)
3. Microsoft ügyfélszolgálata. Új támogatási kérelem létrehozásához a súgó Azure Portal a  Súgó lapon válassza a Súgó **és** támogatás gombot, majd az Új **támogatási kérelem lehetőséget.**

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Probléma adódnak a kiszolgálón Azure File Sync (szinkronizálás, felhőbeli rétegezés stb.) problémáim vannak. Távolítsam el, majd hozzam létre újra a kiszolgálóvégpontomat?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Ügynöktelepítés és kiszolgálóregisztráció
<a id="agent-installation-failures"></a>**Ügynöktelepítési hibák elhárítása**  
Ha a Azure File Sync ügynök telepítése sikertelen, egy rendszergazda jogú parancssorban futtassa a következő parancsot a naplózás bekapcsoláshoz az ügynök telepítése során:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

A telepítési hiba okának megállapításához tekintse át a installer.log naplófájlt.

<a id="agent-installation-gpo"></a>**Az ügynök telepítése a következő hibával meghiúsul: A Storage Sync Agent telepítővarázsló idő előtt leállt egy hiba miatt**

Az ügynök telepítési naplójában a következő hiba kerül naplózásra:

```
CAQuietExec64:  + CategoryInfo          : SecurityError: (:) , PSSecurityException
CAQuietExec64:  + FullyQualifiedErrorId : UnauthorizedAccess
CAQuietExec64:  Error 0x80070001: Command line returned an error.
```

Ez a probléma akkor fordul elő, ha a [PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies#use-group-policy-to-manage-execution-policy) végrehajtási házirend csoportházirend használatával van konfigurálva, és a házirend-beállítás "Csak aláírt parancsfájlok engedélyezése" lesz. Az ügynökhöz mellékelt Azure File Sync alá van írva. A Azure File Sync ügynök telepítése meghiúsult, mert a telepítő a végrehajtási házirend megkerülése beállítással végzi el a szkript végrehajtását.

A probléma megoldásához ideiglenesen tiltsa le a [Parancsfájl-végrehajtás](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies#use-group-policy-to-manage-execution-policy) engedélyezése csoportházirend-beállítást a kiszolgálón. Az ügynök telepítésének befejezése után újra engedélyezhető a csoportházirend-beállítás.

<a id="agent-installation-on-DC"></a>**Az ügynök telepítése meghiúsul a Active Directory-tartomány Controlleren**  
Ha olyan Active Directory-tartományvezérlőre próbálja telepíteni a szinkronizálási ügynököt, amelynél az elsődleges tartományvezérlői szerepkör tulajdonosa Windows Server 2008 R2 vagy újabb operációsrendszer-verzióval van telepítve, előfordulhat, hogy a szinkronizálási ügynök telepítése sikertelen lesz.

A probléma megoldásához helyezze át az elsődleges tartományvezérlői szerepkört egy másik, Windows Server 2012 R2 vagy újabb rendszert futtató tartományvezérlőre, majd telepítse a syncet.

<a id="parameter-is-incorrect"></a>**A Windows Server 2012 R2-kötet elérése a következő hibával meghiúsul: A paraméter helytelen**  
Miután létrehozta a kiszolgálóvégpontot a Windows Server 2012 R2-ben, a kötet elérésekor a következő hiba történik:

driveletter:\ nem érhető el.  
A paraméter helytelen.

A probléma megoldásához telepítse a [KB2919355 tudásbázist,](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014) és indítsa újra a kiszolgálót. Ha a frissítés nem telepíthető, mert egy későbbi frissítés már telepítve van, a Windows Update oldalára lép, telepítse a Windows Server 2012 R2 legújabb frissítéseit, és indítsa újra a kiszolgálót.

<a id="server-registration-missing-subscriptions"></a>**A kiszolgálóregisztráció nem sorolja fel az összes Azure-előfizetést**  
Amikor a kiszolgálót a ServerRegistration.exe regisztrálja, az Azure-előfizetés legördülő gombra kattintva hiányzó előfizetések hiányoznak.

Ez a probléma azért merül fel, ServerRegistration.exe a rendszer csak az első 5 Azure AD-bérlőből fogja lekérni az előfizetéseket. 

A kiszolgálóregisztráció bérlői korlátjának növeléséhez hozzon létre egy ServerRegistrationTenantLimit nevű DWORD-értéket a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync 5-ösnél nagyobb értékkel.

A probléma megkerülő megoldásához használja a következő PowerShell-parancsokat a kiszolgáló regisztrálásához:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**A kiszolgálóregisztráció a következő üzenetet jeleníti meg: "Előfeltételek hiányoznak"**  
Ez az üzenet akkor jelenik meg, ha az Az vagy az AzureRM PowerShell-modul nincs telepítve a PowerShell 5.1-ben. 

> [!Note]  
> ServerRegistration.exe nem támogatja a PowerShell 6.x-et. A kiszolgáló regisztrálása Register-AzStorageSyncServer PowerShell 6.x parancsmagjának használatával.

Az Az vagy az AzureRM modul PowerShell 5.1-re való telepítéséhez hajtsa végre a következő lépéseket:

1. Írja be **a powershell** parancsot egy rendszergazda jogú parancssorból, és nyomja le az Enter billentyűt.
2. Telepítse a legújabb Az vagy AzureRM modult a dokumentáció alapján:
    - [Az modul (.NET 4.7.2 szükséges hozzá)](/powershell/azure/install-az-ps)
    - [AzureRM modul](https://go.microsoft.com/fwlink/?linkid=856959)
3. Futtassa ServerRegistration.exe, majd a varázsló befejezése után regisztrálja a kiszolgálót egy társzinkronizálási szolgáltatásban.

<a id="server-already-registered"></a>**A kiszolgálóregisztráció a következő üzenetet jeleníti meg: "Ez a kiszolgáló már regisztrálva van"** 

![Képernyőkép a Kiszolgálóregisztráció párbeszédpanelről a "kiszolgáló már regisztrálva van" hibaüzenettel](media/storage-sync-files-troubleshoot/server-registration-1.png)

Ez az üzenet akkor jelenik meg, ha a kiszolgálót korábban regisztrálták egy társzinkronizálási szolgáltatásban. Ha a kiszolgáló regisztrációját az aktuális társzinkronizálási szolgáltatásból le kell mondania, majd regisztrálnia kell egy új tárolószinkronizálási szolgáltatásra, kövesse a Kiszolgáló regisztrációjának megszinkronizálása [az](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)Azure File Sync.

Ha a kiszolgáló nem  szerepel a Társzinkronizálási szolgáltatás Regisztrált kiszolgálók listában, futtassa a következő PowerShell-parancsokat a kiszolgálón, amelyről le szeretné mondani a regisztrációt:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Ha a kiszolgáló egy fürt része, a választható *Reset-StorageSyncServer -CleanClusterRegistration* paraméterrel a fürtregisztrációt is eltávolíthatja.

<a id="web-site-not-trusted"></a>**Amikor regisztrálok egy kiszolgálót, számos "webhely nem megbízható" választ látok. miért?**  
Ez a probléma akkor fordul elő, **Internet Explorer fokozott** biztonsági szabályzat engedélyezve van a kiszolgálóregisztráció során. A Fokozott Internet Explorer biztonsági beállítások házirend helyes letiltására vonatkozó további információkért **lásd:** [Prepare Windows Server to use with Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) (A Windows Server előkészítése a Azure File Sync-sel való használatra) és How to deploy [Azure File Sync](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**A kiszolgáló nem szerepel a regisztrált kiszolgálók között a Azure Portal**  
Ha egy kiszolgáló nem  szerepel a Társzinkronizálási szolgáltatás regisztrált kiszolgálói listában:
1. Jelentkezzen be a regisztrálni kívánt kiszolgálóra.
2. Nyissa Fájlkezelő, majd nyissa meg a Storage Sync Agent telepítési könyvtárát (az alapértelmezett hely a C:\Program Files\Azure\StorageSyncAgent). 
3. Futtassa ServerRegistration.exe, majd a varázsló befejezésével regisztrálja a kiszolgálót egy társzinkronizálási szolgáltatásban.

## <a name="sync-group-management"></a>Szinkronizálási csoport kezelése

### <a name="cloud-endpoint-creation-errors"></a>Felhővégpont létrehozási hibái

<a id="cloud-endpoint-using-share"></a>**A felhőbeli végpont létrehozása meghiúsul a következő hibával: "A megadott Azure-fájlmegosztást már egy másik CloudEndpoint használja"**  
Ez a hiba akkor fordul elő, ha az Azure-fájlmegosztást már egy másik felhőbeli végpont használja. 

Ha ezt az üzenetet látja, és az Azure-fájlmegosztást jelenleg nem használja egy felhőbeli végpont, a következő lépésekkel törölje a Azure File Sync metaadatait az Azure-fájlmegosztáson:

> [!Warning]  
> A felhőbeli végpontok által jelenleg használt Azure-fájlmegosztások metaadatainak törlése a Azure File Sync meghiúsul. Ha ezt a fájlmegosztást egy másik szinkronizálási csoportban használja szinkronizálásra, a régi szinkronizálási csoportban lévő fájlok adatvesztése szinte biztos.

1. A Azure Portal az Azure-fájlmegosztást.  
2. Kattintson a jobb gombbal az Azure-fájlmegosztásra, majd válassza a **Metaadatok szerkesztése lehetőséget.**
3. Kattintson a jobb gombbal **a SyncService elemre,** majd válassza a **Törlés lehetőséget.**

<a id="cloud-endpoint-authfailed"></a>**A felhőbeli végpont létrehozása meghiúsul a következő hibával: "AuthorizationFailed"**  
Ez a hiba akkor fordul elő, ha a felhasználói fiók nem rendelkezik a felhőbeli végpont létrehozásához szükséges jogosultságokkal. 

Felhővégpont létrehozásához a felhasználói fióknak a következő Microsoft-engedélyezési engedélyekkel kell rendelkeznie:  
* Olvasás: Szerepkör-definíció lekérte
* Írás: Egyéni szerepkör-definíció létrehozása vagy frissítése
* Olvasás: Szerepkör-hozzárendelés lekért
* Írás: Szerepkör-hozzárendelés létrehozása

A következő beépített szerepkörök a szükséges Microsoft-engedélyezési engedélyekkel rendelkezik:  
* Tulajdonos
* Felhasználói hozzáférés rendszergazdája

Annak megállapításához, hogy a felhasználói fiók szerepköre rendelkezik-e a szükséges engedélyekkel:  
1. A Azure Portal válassza az **Erőforráscsoportok lehetőséget.**
2. Válassza ki azt az erőforráscsoportot, ahol a tárfiók található, majd válassza a **Hozzáférés-vezérlés (IAM) lehetőséget.**
3. Válassza a **Szerepkör-hozzárendelések** lapot.
4. Válassza ki **a felhasználói** fiók szerepkörét (például Tulajdonos vagy Közreműködő).
5. Az **Erőforrás-szolgáltató listában** válassza a **Microsoft-hitelesítés lehetőséget.** 
    * **A szerepkör-hozzárendelésnek** Olvasás **és** **Írás engedéllyel kell** rendelkeznie.
    * **A szerepkör-definíciónak** Olvasási **és** **Írási engedéllyel kell** rendelkeznie.

### <a name="server-endpoint-creation-and-deletion-errors"></a>Kiszolgálóvégpont létrehozásával és törlésével kapcsolatos hibák

<a id="-2134375898"></a>**A kiszolgálóvégpont létrehozása a következő hibával meghiúsul: "MgmtServerJobFailed" (hibakód: -2134375898 vagy 0x80c80226)**  
Ez a hiba akkor fordul elő, ha a kiszolgálóvégpont elérési útja a rendszerköteten van, és a felhőbeli rétegezés engedélyezve van. A felhőbeli rétegezésen a rendszerköteten nem támogatott. Egy kiszolgálóvégpont létrehozásához a rendszerköteten tiltsa le a felhőbeli rétegezést a kiszolgálóvégpont létrehozásakor.

<a id="-2147024894"></a>**A kiszolgálóvégpont létrehozása a következő hibával meghiúsul: "MgmtServerJobFailed" (hibakód: -2147024894 vagy 0x80070002)**  
Ez a hiba akkor következik be, ha a kiszolgálóvégpont megadott elérési útja nem érvényes. Ellenőrizze, hogy a megadott kiszolgálóvégpont elérési útja helyileg csatlakoztatott NTFS-kötet-e. Vegye figyelembe, hogy az Azure File Sync nem támogatja a leképezett meghajtókat kiszolgálóvégpontok elérési útjaként.

<a id="-2134375640"></a>**A kiszolgálóvégpont létrehozása a következő hibával meghiúsul: "MgmtServerJobFailed" (hibakód: -2134375640 vagy 0x80c80328)**  
Ez a hiba akkor következik be, ha a kiszolgálóvégpont megadott elérési útja nem egy NTFS-kötet. Ellenőrizze, hogy a megadott kiszolgálóvégpont elérési útja helyileg csatlakoztatott NTFS-kötet-e. Vegye figyelembe, hogy az Azure File Sync nem támogatja a leképezett meghajtókat kiszolgálóvégpontok elérési útjaként.

<a id="-2134347507"></a>**A kiszolgálóvégpont létrehozása a következő hibával meghiúsul: "MgmtServerJobFailed" (hibakód: -2134347507 vagy 0x80c8710d)**  
Ez a hiba azért fordult elő, mert az Azure File Sync nem támogatja a kiszolgálóvégpontokat olyan köteteken, amelyeken tömörítve van a rendszerkötet információit tartalmazó mappa. A probléma elhárításához bontsa ki a rendszerkötet információit tartalmazó mappát. Ha a rendszerkötet információit tartalmazó mappa az egyetlen tömörített mappa a köteten, hajtsa végre az alábbi lépéseket:

1. Töltse le a [PsExec](/sysinternals/downloads/psexec) eszközt.
2. Futtassa a következő parancsot egy rendszergazda jogú parancssorból a rendszerfiók alatt futó parancssor elindításához: **PsExec.exe -i -s -d cmd**
3. A rendszerfiók alatt futó parancssorban írja be a következő parancsot, és nyomja le az Enter billentyűt:   
    **cd /d "meghajtóbetűjel:\Rendszerkötet adatai"**  
    **compact /u /s**

<a id="-2134376345"></a>**A kiszolgálóvégpont létrehozása a következő hibával meghiúsul: "MgmtServerJobFailed" (hibakód: -2134376345 vagy 0x80C80067)**  
Ez a hiba a kiszolgálónkénti kiszolgálóvégpontok korlátjának elérésekor következik be. Az Azure File Sync jelenleg legfeljebb 30 kiszolgálóvégpontot támogat kiszolgálónként. További információ: Azure File Sync [elérése.](./storage-files-scale-targets.md#azure-file-sync-scale-targets)

<a id="-2134376427"></a>**A kiszolgálóvégpont létrehozása a következő hibával meghiúsul: "MgmtServerJobFailed" (hibakód: -2134376427 vagy 0x80c80015)**  
Ez a hiba akkor fordul elő, ha egy másik kiszolgálóvégpont már szinkronizálja a kiszolgálóvégpont megadott elérési útját. Az Azure File Sync nem támogatja, hogy ugyanazt a címtárat vagy kötetet több kiszolgálóvégpont szinkronizálja.

<a id="-2160590967"></a>**A kiszolgálóvégpont létrehozása a következő hibával meghiúsul: "MgmtServerJobFailed" (hibakód: -2160590967 vagy 0x80c80077)**  
Ez a hiba akkor következik be, ha a kiszolgálóvégpont elérési útja árva rétegzett fájlokat tartalmaz. Ha egy kiszolgálóvégpontot nemrég eltávolítottak, várjon, amíg az árva rétegzett fájlok törlése befejeződik. A rendszer a 6662-es eseményazonosítót naplózza a Telemetria eseménynaplójában, miután elkezdődött az árva rétegzett fájlok törlése. A rendszer a 6661-es eseményazonosítót naplózza, miután az árva rétegzett fájlok törlése befejeződött, és a kiszolgálóvégpont újra létrehozható az elérési úttal. Ha a kiszolgálóvégpont létrehozása meghiúsul a rétegzett fájlok tisztításának befejezése után, vagy ha a 6661-es azonosítójú esemény nem található [a](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) Telemetria eseménynaplójában az eseménynaplóváltás miatt, távolítsa el az árva rétegzett fájlokat a rétegzett fájlok rétegzett fájljainak a kiszolgálóvégpont törlése utáni törlését követően.

<a id="-2134347757"></a>**A kiszolgálóvégpont törlése sikertelen, és a következő hibaüzenet jelenik meg: "MgmtServerJobExpired" (hibakód: -2134347757 vagy 0x80c87013)**  
Ez a hiba akkor következik be, ha a kiszolgáló offline állapotban van, vagy nem rendelkezik hálózati kapcsolattal. Ha a kiszolgáló már nem érhető el, törölje a kiszolgáló regisztrációját a portálon, amivel törli a kiszolgálóvégpontokat. A kiszolgálóvégpont törléséhez kövesse [Azure File Sync a](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)Kiszolgáló regisztrációjának törlése a következővel: .

### <a name="server-endpoint-health"></a>Kiszolgálóvégpont állapota

<a id="server-endpoint-provisioningfailed"></a>**Nem lehet megnyitni a kiszolgálóvégpont tulajdonságainak lapját, vagy frissíteni a felhőbeli rétegezés szabályzatát**  
Ez a probléma akkor fordulhat elő, ha egy felügyeleti művelet meghiúsul a kiszolgálóvégponton. Ha a kiszolgálóvégpont tulajdonságainak lapja nem nyílik meg a Azure Portal, a kiszolgálóvégpont frissítése a kiszolgálóRól származó PowerShell-parancsokkal megoldhatja ezt a problémát. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**A kiszolgálóvégpont állapota "Nincs tevékenység" vagy "Függőben", a kiszolgáló állapota a regisztrált kiszolgálók panelen pedig "Offline"**  

Ez a probléma akkor fordulhat elő, ha a Storage Sync Monitor (AzureStorageSyncMonitor.exe) folyamata nem fut, vagy a kiszolgáló nem tud hozzáférni a Azure File Sync szolgáltatáshoz.

A portálon offline állapotúként megjelenő kiszolgálón keresse meg a 9301-es eseményazonosítót a telemetriai eseménynaplóban (amely a Eseménynapló Alkalmazások és szolgáltatások\Microsoft\FileSync\Agent mappájában található) annak megállapításához, hogy a kiszolgáló miért nem tud hozzáférni az Azure File Sync szolgáltatáshoz. 

- Ha **a GetNextJob** befejezve a következő állapottal: 0 van naplózva, a kiszolgáló kommunikálhat a Azure File Sync szolgáltatással. 
    - Nyissa meg a Feladatkezelőt a kiszolgálón, és ellenőrizze, hogy fut-e a Storage Sync Monitor-folyamat (AzureStorageSyncMonitor.exe). Ha a folyamat nem fut, először próbálja meg újraindítani a kiszolgálót. Ha a kiszolgáló újraindítása nem oldja meg a problémát, frissítsen az Azure File Sync-ügynök [legújabb verziójára](./storage-files-release-notes.md). 

- Ha a GetNextJob a következő állapottal fejeződött **be: -2134347756,** a kiszolgáló egy tűzfal, proxy vagy TLS-titkosítási csomag rendelési konfigurációja miatt nem tud kommunikálni az Azure File Sync szolgáltatással. 
    - Ha a kiszolgáló tűzfal mögött van, ellenőrizze, hogy engedélyezve van-e a 443-as port kimenő forgalma. Ha a tűzfal adott tartományokra korlátozza a forgalmat, ellenőrizze, hogy a tűzfal dokumentációjában felsorolt [tartományok](./storage-sync-files-firewall-and-proxy.md#firewall) elérhetők-e.
    - Ha a kiszolgáló proxy mögött található, konfigurálja a számítógép- vagy alkalmazásspecifikus proxybeállításokat a Proxy dokumentációjának [lépéseit követve.](./storage-sync-files-firewall-and-proxy.md#proxy)
    - A Test-StorageSyncNetworkConnectivity parancsmag használatával ellenőrizze a szolgáltatásvégpontokkal való hálózati kapcsolatot. További információ: [Szolgáltatásvégpontokkal való hálózati kapcsolat tesztelése.](./storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints)
    - Ha a TLS-rejtjelcsomag sorrendje konfigurálva van a kiszolgálón, csoportházirend vagy TLS-parancsmagok használatával adhat hozzá titkosítási csomagokat:
        - A csoportházirend használatával kapcsolatban lásd: [Configuring TLS Cipher Suite Order by using Csoportházirend.](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-group-policy)
        - A TLS-parancsmagok használatát lásd: [Configuring TLS Cipher Suite Order by using TLS PowerShell Cmdlets (TLS-titkosítási csomagok sorrendjének konfigurálása TLS PowerShell-parancsmagokkal).](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-tls-powershell-cmdlets)
    
        Azure File Sync jelenleg a következő titkosítási csomagokat támogatja a TLS 1.2 protokollhoz:  
        - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256  

- Ha a GetNextJob a következő állapottal fejeződött **be: -2134347764,** a kiszolgáló lejárt vagy törölt tanúsítvány miatt nem tud kommunikálni az Azure File Sync szolgáltatással.  
    - Futtassa a következő PowerShell-parancsot a kiszolgálón a hitelesítéshez használt tanúsítvány alaphelyzetbe állításhoz:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**A kiszolgálóvégpont állapota "Nincs tevékenység", a kiszolgáló állapota a regisztrált kiszolgálók panelen pedig "Online"**  

Ha a kiszolgálóvégpont állapota „Nincs tevékenység”, az azt jelenti, hogy a kiszolgálóvégpont az elmúlt két órában nem naplózta a szinkronizálási tevékenységeket.

A kiszolgáló aktuális szinkronizálási tevékenységének ellenőrzéséhez tekintse meg a Hogyan aktuális szinkronizálási munkamenet előrehaladásának [figyelése.](#how-do-i-monitor-the-progress-of-a-current-sync-session).

Előfordulhat, hogy egy kiszolgálóvégpont több órán keresztül nem naplóz szinkronizálási tevékenységet egy hiba vagy a rendszererőforrások hiánya miatt. Ellenőrizze, hogy Azure File Sync [ügynök legújabb verziója van-e](./storage-files-release-notes.md) telepítve. Ha a probléma továbbra is fennáll, nyisson egy támogatási kérést.

> [!Note]  
> Ha a kiszolgáló állapota a regisztrált kiszolgálók panelen "Offlineként jelenik meg", hajtsa végre a Kiszolgálóvégpontban dokumentált lépéseket"Nincs tevékenység" vagy ["Függőben"](#server-endpoint-noactivity) állapottal, és a regisztrált kiszolgálók panelen a kiszolgáló állapota "Offline" állapotú.

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Ha közvetlenül az Azure-fájlmegosztásban, SMB-kapcsolaton vagy a portálon keresztül hoztam létre egy fájlt, mennyi ideig tart a fájl szinkronizálása a szinkronizálási csoport kiszolgálóira?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**A kiszolgálóvégpont állapota több órán keresztül függő állapotban van**  
Ez a probléma akkor várható, ha létrehoz egy felhőbeli végpontot, és adatokat tartalmazó Azure-fájlmegosztást használ. Az Azure-fájlmegosztás változásait olvasó változásenumerációs feladatnak be kell fejeződnie, hogy a fájlok szinkronizálni tudjanak a felhő és a kiszolgáló végpontjai között. A feladat befejezésének ideje az Azure-fájlmegosztásban található névtér méretétől függ. A kiszolgálóvégpont állapotának a módosítási enumerálás befejezése után frissülni kell.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Hogyan szinkronizálási állapot figyelése?
# <a name="portal"></a>[Portál](#tab/portal1)
Az egyes szinkronizálási csoportokon belül részletezheti az egyes kiszolgálóvégpontokat, és láthatja az utolsó befejezett szinkronizálási munkamenetek állapotát. A zöld Állapot oszlop és a Nem szinkronizált fájlok értéke 0 azt jelzi, hogy a szinkronizálás a várt módon működik. Ha nem ez a helyzet, tekintse meg alább a gyakori szinkronizálási hibák listáját, valamint a nem szinkronizált fájlok kezeléséhez szükséges lépéseket. 

![Képernyőkép a Azure Portal](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Kiszolgáló](#tab/server)
Ugrás a kiszolgáló telemetriai naplóira, amelyek a következő Eseménynapló `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` találhatók: . A 9102-es esemény egy befejezett szinkronizálási munkamenetnek felel meg; A szinkronizálás legfrissebb állapotát a 9102-es azonosítójú legutóbbi eseményben keresse meg. A SyncDirection azt jelzi, hogy ez a munkamenet feltöltés vagy letöltés volt-e. Ha a HResult 0, akkor a szinkronizálási munkamenet sikeres volt. A nem nulla HResult azt jelenti, hogy hiba történt a szinkronizálás során; A gyakori hibák listáját alább láthatja. Ha a PerItemErrorCount nagyobb, mint 0, az azt jelenti, hogy egyes fájlok vagy mappák nem szinkronizáltak megfelelően. Lehetséges, hogy a HResult (HResult) 0, de a PerItemErrorCount (PerItemErrorCount) érték nagyobb, mint 0.

Az alábbi példa egy sikeres feltöltést mutat be. Az egyszerűség kedvéért az egyes 9102-es eseményekben szereplő értékek közül csak néhány szerepel az alábbiakban. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Ezzel szemben a sikertelen feltöltések a következő módon néznek ki:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Előfordulhat, hogy a szinkronizálási munkamenetek összességében meghiúsulnak, vagy nem nulla PerItemErrorCount értékük van, de továbbra is haladnak előre, és egyes fájlok szinkronizálása sikeresen megtörtént. Ez az Applied* (Alkalmazott) * mezőkben (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount és AppliedSizeBytes) látható, amelyekből látható, hogy a munkamenet mekkora része sikeres. Ha több szinkronizálási munkamenetet lát egy sorban, amelyek sikertelenek, de az Alkalmazott* szám növekszik, akkor a támogatási jegy megnyitása előtt meg kell adni a szinkronizálási időt, hogy újra megpróbáljon.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Hogyan monitorozhatom a jelenlegi szinkronizálási munkamenet állapotát?
# <a name="portal"></a>[Portál](#tab/portal1)
A szinkronizálási csoporton belül keresse meg a szóban forgó kiszolgálóvégpontot, és tekintse meg a Szinkronizálási tevékenység szakaszt az aktuális szinkronizálási munkamenetben feltöltött vagy letöltött fájlok számát. Vegye figyelembe, hogy az állapot körülbelül 5 perc késleltetéssel jelenik meg, és ha a szinkronizálási munkamenet elég kicsi ahhoz, hogy ezen időszakon belül befejeződjön, akkor előfordulhat, hogy nem jelenik meg a portálon. 

# <a name="server"></a>[Kiszolgáló](#tab/server)
Nézze meg a legutóbbi 9302-es eseményt a kiszolgáló telemetriai naplójában (a Eseménynapló mappában lépjen az Alkalmazás- és szolgáltatásnaplók\Microsoft\FileSync\Agent\Telemetry mappába). Ez az esemény jelzi az aktuális szinkronizálás munkamenet állapotát. A TotalItemCount azt jelzi, hogy hány fájlt kell szinkronizálni, az AppliedItemCount az eddig szinkronizált fájlok számát, a PerItemErrorCount pedig a nem szinkronizált fájlok számát (lásd alább az alábbi lépéseket).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Honnan tudhatom, hogy a kiszolgálók szinkronizálva vannak-e egymással?
# <a name="portal"></a>[Portál](#tab/portal1)
Egy adott szinkronizálási csoport minden kiszolgálója számára győződjön meg a következőről:
- A legutóbbi szinkronizálási kísérlet időbélyege a feltöltéshez és a letöltéshez is friss.
- A feltöltés és a letöltés állapota zöld.
- A Szinkronizálási tevékenység mezőben nagyon kevés fájl jelenik meg, vagy még nincs szinkronizálható fájl.
- A Fájlok nem szinkronizálva mező feltöltéshez és letöltéshez egyaránt 0.

# <a name="server"></a>[Kiszolgáló](#tab/server)
Nézze meg a befejezett szinkronizálási munkameneteket, amelyeket 9102-es események jelöltek meg az egyes kiszolgálók telemetriai eseménynaplójában (a Eseménynapló lépjen `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` a következőre: ). 

1. Minden adott kiszolgálón meg kell győződni arról, hogy a legújabb feltöltési és letöltési munkamenetek sikeresen befejeződtek. Ehhez ellenőrizze, hogy a HResult és a PerItemErrorCount érték egyaránt 0-e feltöltéshez és letöltéshez (a SyncDirection mező azt jelzi, hogy egy adott munkamenet feltöltési vagy letöltési munkamenet-e). Vegye figyelembe, hogy ha nem lát egy nemrégiben befejezett szinkronizálási munkamenetet, valószínűleg szinkronizálási munkamenet van folyamatban, ami várhatóan nagy mennyiségű adat hozzáadásakor vagy módosításakor várható.
2. Ha egy kiszolgáló teljesen naprakész a felhővel, és egyik irányban sem változik a szinkronizálás, üres szinkronizálási munkamenetek fognak látni. Ezeket feltöltési és letöltési események jelzik, amelyekben az összes Sync* mező (SyncFileCount, SyncDirCount, SyncTombstoneCount és SyncSizeBytes) nulla, ami azt jelenti, hogy nem volt mit szinkronizálni. Vegye figyelembe, hogy ezek az üres szinkronizálási munkamenetek nem mindig fordulnak elő nagy adatváltozású kiszolgálókon, mivel mindig van valami új szinkronizálás. Ha nincs szinkronizálási tevékenység, akkor 30 percenként kell lekövetkezni. 
3. Ha minden kiszolgáló naprakész a felhővel, tehát a legutóbbi feltöltési és letöltési munkameneteik üres szinkronizálási munkamenetek, akkor ésszerű bizonyossággal feltételezheti, hogy a rendszer egésze szinkronban van. 
    
Vegye figyelembe, hogy ha közvetlenül az Azure-fájlmegosztásban végrehajtott módosításokat, a Azure File Sync nem észleli ezt a módosítást, amíg a módosításenumerálás le nem fut, ami 24 óránként történik. Lehetséges, hogy egy kiszolgáló naprakésznek fogja mondani a felhővel kapcsolatban, amikor valójában hiányoznak a közvetlenül az Azure-fájlmegosztáson végzett legutóbbi módosítások. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Hogyan állapíthatom meg, hogy vannak-e olyan fájlok vagy mappák, amelyek szinkronizálása nem történik meg?
Ha a Kiszolgálón lévő PerItemErrorCount vagy a portálon a Fájlok nem szinkronizálva száma bármely szinkronizálási munkamenet esetén nagyobb 0-ásnál, az azt jelenti, hogy egyes elemek szinkronizálása sikertelen. A fájlok és mappák olyan tulajdonságokkal is rendelkeznek, amelyek megakadályozzák a szinkronizálást. Ezek a jellemzők állandóak, és explicit műveletet igényelnek a szinkronizálás folytatásához, például eltávolítják a nem támogatott karaktereket a fájlból vagy a mappanévből. Emellett átmenetiek is lehetnek, ami azt jelenti, hogy a fájl vagy mappa automatikusan folytatja a szinkronizálást; A megnyitott leíróval lévő fájlok például automatikusan folytatják a szinkronizálást a fájl bezárása után. Amikor a Azure File Sync ilyen problémát észlel, a rendszer egy hibanaplót hoz létre, amely elemezheti a jelenleg nem szinkronizált elemek listáját.

A hibák azonosításához futtassa a **FileSyncErrorsReport.ps1** PowerShell-szkriptet (amely a Azure File Sync-ügynök ügynöktelepítési könyvtárában található) a nyitott leírók, nem támogatott karakterek vagy egyéb problémák miatt nem szinkronizált fájlok azonosításához. Az ItemPath mező a fájl helyét mutatja a gyökérszinkronizálási könyvtárhoz viszonyítva. A javítási lépésekért tekintse meg alább a gyakori szinkronizálási hibák listáját.

> [!Note]  
> Ha a FileSyncErrorsReport.ps1 szkript "Nem található fájlhibák" hibaüzenetet ad vissza, vagy nem sorolja fel az elemenkénti hibákat a szinkronizálási csoportban, az ok a következő:
>
>- 1. ok: Az utolsó befejezett szinkronizálási munkamenetben nem történt elemenkénti hiba. A portált hamarosan frissíteni kell, hogy a 0 Nem szinkronizált fájlokat mutassa. 
>    - Ellenőrizze a Telemetria eseménynaplójában a [9102-es](?tabs=server%252cazure-portal#broken-sync) eseményazonosítót, és győződjön meg arról, hogy a PerItemErrorCount 0. 
>
>- 2. ok: A burkolt kiszolgálón túl sok elemenkénti hiba miatt az ItemResults eseménynapló, és az eseménynapló már nem tartalmaz hibákat ehhez a szinkronizálási csoporthoz.
>    - A probléma elkerülése érdekében növelje az ItemResults eseménynapló méretét. Az ItemResults eseménynapló a fájl "Alkalmazás- és szolgáltatásnaplók\Microsoft\FileSync\Agent" Eseménynapló. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Fájl-/könyvtárszinkronizálási hibák elhárítása
**ItemResults napló – elemenkénti szinkronizálási hibák**  

| Hresult | HRESULT (decimális) | Hibasztring | Probléma | Szervizelés |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | A kiszolgálón található rétegzett fájl nem érhető el. Ez a probléma akkor merül fel, amikor a rétegzett fájl visszahívása nem történik meg a kiszolgálóvégpont törlése előtt. | A probléma megoldásához lásd: A rétegzett fájlok nem érhetők el a kiszolgálón [a kiszolgálóvégpont törlése után.](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | A fájl- vagy könyvtárváltozást még nem lehet szinkronizálni, mert a függő mappa még nincs szinkronizálva. Ez az elem a függő módosítások szinkronizálása után lesz szinkronizálva. | Nincs szükség beavatkozásra. Ha a hiba több napig is fennáll, az FileSyncErrorsReport.ps1 PowerShell-szkript használatával állapítsa meg, hogy a függő mappa miért nincs még szinkronizálva. |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | A fájl- vagy könyvtárváltozást még nem lehet szinkronizálni, mert a függő mappa még nincs szinkronizálva. Ez az elem a függő módosítások szinkronizálása után lesz szinkronizálva. | Nincs szükség beavatkozásra. Ha a hiba több napig is fennáll, az FileSyncErrorsReport.ps1 PowerShell-szkript használatával állapítsa meg, hogy a függő mappa miért nincs még szinkronizálva. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | A fájl- vagy könyvtárváltozást még nem lehet szinkronizálni, mert a függő mappa még nincs szinkronizálva, és a szinkronizálási munkamenet sikertelen volt. Ez az elem a függő módosítások szinkronizálása után lesz szinkronizálva. | Nincs szükség beavatkozásra. Ha a hiba továbbra is fennáll, vizsgálja meg a szinkronizálási munkamenet sikertelenségét. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | A fájl vagy könyvtár neve érvénytelen. | Nevezze át a szóban forgó fájlt vagy könyvtárat. További [információ: Nem támogatott](?tabs=portal1%252cazure-portal#handling-unsupported-characters) karakterek kezelése. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | A fájl vagy könyvtár neve érvénytelen. | Nevezze át a szóban forgó fájlt vagy könyvtárat. További [információ: Nem támogatott](?tabs=portal1%252cazure-portal#handling-unsupported-characters) karakterek kezelése. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | A fájl nem szinkronizálható, mert használatban van. A fájl szinkronizálása akkor megy végbe, amikor már nem lesz használatban. | Nincs szükség beavatkozásra. Azure File Sync naponta egyszer létrehoz egy ideiglenes VSS-pillanatképet a kiszolgálón a megnyitott leírókat tároló fájlok szinkronizálása számára. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | A fájl megváltozott, de a szinkronizálás még nem észlelte a változást. A szinkronizálás a módosítás észlelése után helyreáll. | Nincs szükség beavatkozásra. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | A fájl törölve lett, és a szinkronizálás nem tud a módosításról. | Nincs szükség beavatkozásra. Ha a módosításészlelés észleli, hogy a fájl törölve lett, a Sync leállítja a hiba naplózását. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | Egy fájl vagy könyvtár törlése nem szinkronizálható, mert az elem már törölve lett a célhelyen, és a szinkronizálás nem tud a módosításról. | Nincs szükség beavatkozásra. A Sync leállítja a hiba naplózását, ha a módosításészlelés lefut a célhelyen, és a szinkronizálás észleli, hogy az elem törölve lett. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | A fájl vagy könyvtár ki lett hagyva, de a következő szinkronizálási munkamenet során szinkronizálva lesz. Ha ez a hiba az elem letöltésekor jelenik meg, a fájl vagy könyvtár neve valószínűleg nem lesz érvényes. | Nincs szükség műveletre, ha a fájl feltöltésekor a rendszer jelentést ad a hibáról. Ha a hiba a fájl letöltésekor jelenik meg, nevezze át a szóban forgó fájlt vagy könyvtárat. További [információ: Nem támogatott](?tabs=portal1%252cazure-portal#handling-unsupported-characters) karakterek kezelése. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | Egy fájl vagy könyvtár létrehozása nem szinkronizálható, mert az elem már létezik a célhelyen, és a szinkronizálás nem tud a módosításról. | Nincs szükség beavatkozásra. A szinkronizálás leállítja a hiba naplózását, amint a módosításészlelés lefut a célhelyen, és a szinkronizálás figyelembe veszi az új elemet. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | A fájl nem szinkronizálható, mert elérte az Azure-fájlmegosztás korlátját. | A probléma megoldásához tekintse meg a hibaelhárítási útmutató [Az Azure-fájlmegosztások](?tabs=portal1%252cazure-portal#-2134351810) tárhelykorlátja című szakaszát. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | A fájlt egy nem támogatott megoldás (például NTFS EFS) titkosítja. | A fájl visszafejtése és támogatott titkosítási megoldás használata. A támogatott megoldások listáját a Tervezési útmutató [Titkosítási megoldások](./storage-sync-files-planning.md#encryption) szakaszában találja. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | A fájl egy csak olvasható DFS-R replikációs mappában található. | A fájl egy csak olvasható DFS-R replikációs mappában található. Az Azure Files Sync nem támogatja az írásvédett DFS-R replikációs mappákban található kiszolgálói végpontokat. További információt a [tervezési útmutatóban](./storage-sync-files-planning.md#distributed-file-system-dfs) talál. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | A fájl törlés függő állapotban van. | Nincs szükség beavatkozásra. Az összes megnyitott fájlkezelő bezárulta után a fájl törlődik. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | A fájl nem szinkronizálható, mert a tárfiók tűzfal- és virtuális hálózati beállításai engedélyezve vannak, és a kiszolgálónak nincs hozzáférése a tárfiókhoz. | Adja hozzá a kiszolgáló IP-címét vagy virtuális hálózatát a telepítési útmutató [Tűzfal-](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) és virtuális hálózati beállítások konfigurálása című szakaszában található lépéseket követve. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | A fájl nem szinkronizálható, mert a biztonsági leíró mérete meghaladja a 64 KiB korlátot. | A probléma megoldásához távolítsa el a fájlhoz tartozó hozzáférés-vezérlési bejegyzéseket (ACE) a biztonsági leíró méretének csökkentése érdekében. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | A fájl váratlan hiba miatt nem szinkronizálható. | Ha a hiba több napig is fennáll, nyisson támogatási esetet. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | A fájl nem szinkronizálható, mert használatban van. A fájl szinkronizálása akkor megy végbe, amikor már nem lesz használatban. | Nincs szükség beavatkozásra. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | A fájl a szinkronizálás során módosult, ezért újra szinkronizálni kell. | Nincs szükség beavatkozásra. |
| 0x80070017 | -2147024873 | ERROR_CRC | A fájl CRC-hiba miatt nem szinkronizálható. Ez a hiba akkor fordulhat elő, ha egy rétegzett fájl nem lett visszahívva a kiszolgálóvégpont törlése előtt, vagy ha a fájl sérült. | A probléma megoldásához lásd: A rétegzett fájlok nem [érhetők](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) el a kiszolgálón a kiszolgálóvégpont törlése után az árva rétegzett fájlok eltávolításához. Ha a hiba az árva rétegzett fájlok eltávolítása után is fennáll, futtassa a [chkdsk](/windows-server/administration/windows-commands/chkdsk) fájlt a köteten. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | A fájl nem szinkronizálható, mert elérte az ütközési fájlok maximális számát. Azure File Sync fájlonként 100 ütközési fájlt támogat. További információ a fájlütközésekről: gyakori [Azure File Sync.](./storage-files-faq.md#afs-conflict-resolution) | A probléma megoldásához csökkentse az ütköző fájlok számát. A fájl szinkronizálása azonnal megtörténik, amint az ütköző fájlok száma 100 alá csökken. |

#### <a name="handling-unsupported-characters"></a>Nem támogatott karakterek kezelése
Ha az **FileSyncErrorsReport.ps1** PowerShell-szkript elemenkénti szinkronizálási hibákat jelez nem támogatott karakterek miatt (hibakód: 0x8007007b vagy 0x80c80255), távolítsa el vagy nevezze át a hibás karaktereket a megfelelő fájlnevekből. A PowerShell ezeket a karaktereket valószínűleg kérdőjelként vagy üres téglalapként írja ki, mivel a legtöbb karakter nem tartalmaz szabványos vizuális kódolást. 
> [!Note]  
> A [kiértékelési](storage-sync-files-planning.md#evaluation-cmdlet) eszközzel azonosíthatók a nem támogatott karakterek. Ha az adatkészlet több érvénytelen karaktert tartalmazó fájlt tartalmaz, a [ScanUnsupportedChars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) szkript használatával átnevezheti a nem támogatott karaktereket tartalmazó fájlokat.

Az alábbi táblázat tartalmazza az összes olyan Unicode-Azure File Sync, amely még nem támogatott.

| Karakterkészlet | Karakterek száma |
|---------------|-----------------|
| <ul><li>0x0000009D (osc operációs rendszer parancs)</li><li>0x00000090 (dcs eszközvezérlő sztring)</li><li>0x0000008F (ss3 single shift three)</li><li>0x00000081 (magas oktett-beállításkészlet)</li><li>0x0000007F (del delete)</li><li>0x0000008D (ri reverse line feed)</li></ul> | 6 |
| 0x0000FDD0 – 0x0000FDEF (arab bemutató űrlapok–a) | 32 |
| 0x0000FFF0 – 0x0000FFFF (speciális) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (nemcharacter)</li><li>0x0002FFFE - 0x0002FFFF = 2 (nemcharacter)</li><li>0x0003FFFE – 0x0003FFFF = 2 (nemcharacter)</li><li>0x0004FFFE - 0x0004FFFF = 2 (nemcharacter)</li><li>0x0005FFFE - 0x0005FFFF = 2 (nemcharacter)</li><li>0x0006FFFE - 0x0006FFFF = 2 (nemcharacter)</li><li>0x0007FFFE - 0x0007FFFF = 2 (nemcharacter)</li><li>0x0008FFFE - 0x0008FFFF = 2 (nemcharacter)</li><li>0x0009FFFE - 0x0009FFFF = 2 (nemcharacter)</li><li>0x000AFFFE - 0x000AFFFF = 2 (nemcharacter)</li><li>0x000BFFFE – 0x000BFFFF = 2 (nemcharacter)</li><li>0x000CFFFE – 0x000CFFFF = 2 (nem jellemző)</li><li>0x000DFFFE – 0x000DFFFF = 2 (nem jellemző)</li><li>0x000EFFFE – 0x000EFFFF = 2 (nincs definiálva)</li><li>0x000FFFFE – 0x000FFFFF = 2 (kiegészítő magánhasználati terület)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Gyakori szinkronizálási hibák
<a id="-2147023673"></a>**A szinkronizálási munkamenet megszakadt.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x800704c7 |
| **HRESULT (decimális)** | -2147023673 | 
| **Hibasztring** | ERROR_CANCELLED |
| **Szervizelés szükséges** | No |

A szinkronizálási munkamenetek több okból is meghiúsulhatnak, például a kiszolgáló újraindítása vagy frissítése, VSS-pillanatképek stb. miatt. Bár ez a hiba úgy tűnik, hogy nyomon követést igényel, ezt a hibát biztonságosan figyelmen kívül hagyhatja, kivéve, ha több órán át fennáll.

<a id="-2147012889"></a>**Nem lehetett kapcsolatot létesíteni a szolgáltatással.**    

| Hiba | Code |
|-|-|
| **Hresult** | 0x80072ee7 |
| **HRESULT (decimális)** | -2147012889 | 
| **Hibasztring** | WININET_E_NAME_NOT_RESOLVED |
| **Szervizelésre van szükség** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**A felhasználói kérést a szolgáltatás szabályozása alatt áll.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c8004c |
| **HRESULT (decimális)** | -2134376372 |
| **Hibasztring** | ECS_E_USER_REQUEST_THROTTLED |
| **Szervizelésre van szükség** | No |

Nincs szükség beavatkozásra; a kiszolgáló újra próbálkozik. Ha a hiba több órán keresztül fennáll, hozzon létre támogatási kérést.

<a id="-2134364043"></a>**A szinkronizálás le van tiltva, amíg a módosításészlelés be nem fejeződik a visszaállítás után**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c83075 |
| **HRESULT (decimális)** | -2134364043 |
| **Hibasztring** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Szervizelésre van szükség** | No |

Semmit nem kell tenni. Ha egy fájl vagy fájlmegosztás (felhőbeli végpont) visszaállítása a Azure Backup le lesz tiltva, amíg a módosításészlelés be nem fejeződik az Azure-fájlmegosztáson. A módosításészlelés a visszaállítás befejezése után azonnal fut, és az időtartam a fájlmegosztásban lévő fájlok számán alapul.

<a id="-2147216747"></a>**A szinkronizálási szolgáltatás meghiúsult, mert a szinkronizálási adatbázist eltávolította a memóriából.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80041295 |
| **HRESULT (decimális)** | -2147216747 |
| **Hibasztring** | SYNC_E_METADATA_INVALID_OPERATION |
| **Szervizelés szükséges** | No |

Ez a hiba általában akkor fordul elő, amikor egy biztonsági mentési alkalmazás létrehoz egy VSS-pillanatképet, és a szinkronizálási adatbázis el van távolítva. Ha a hiba több órán keresztül fennáll, hozzon létre támogatási kérést.

<a id="-2134364065"></a>**A Sync nem fér hozzá a felhővégpontban megadott Azure-fájlmegosztáshoz.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c8305f |
| **HRESULT (decimális)** | -2134364065 |
| **Hibasztring** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Szervizelés szükséges** | Yes |

Ez a hiba azért fordul elő, mert az Azure File Sync-ügynök nem fér hozzá az Azure-fájlmegosztáshoz, amelynek az lehet az oka, hogy az Azure-fájlmegosztás vagy az azt futtató tárfiók már nem létezik. A hiba elhárításához végezze el a következő lépéseket:

1. [Ellenőrizze, hogy létezik-e a tárfiók.](#troubleshoot-storage-account)
2. [Győződjön meg arról, hogy az Azure-fájlmegosztás létezik.](#troubleshoot-azure-file-share)
3. [Győződjön Azure File Sync, hogy rendelkezik hozzáféréssel a tárfiókhoz.](#troubleshoot-rbac)
4. [Győződjön meg arról, hogy a tárfiók tűzfal- és virtuális hálózati beállításai megfelelően vannak konfigurálva (ha engedélyezve vannak).](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**A szinkronizálás nem sikerült, mert a kérelem nem jogosult a művelet végrehajtásához.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c86044 |
| **HRESULT (decimális)** | -2134351804 |
| **Hibasztring** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Szervizelésre van szükség** | Yes |

Ez a hiba azért fordul elő, Azure File Sync ügynök nem jogosult hozzáférni az Azure-fájlmegosztáshoz. A hiba elhárításához végezze el a következő lépéseket:

1. [Ellenőrizze, hogy létezik-e a tárfiók.](#troubleshoot-storage-account)
2. [Győződjön meg arról, hogy az Azure-fájlmegosztás létezik.](#troubleshoot-azure-file-share)
3. [Győződjön meg arról, hogy a tárfiók tűzfal- és virtuális hálózati beállításai megfelelően vannak konfigurálva (ha engedélyezve vannak).](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Győződjön Azure File Sync, hogy rendelkezik hozzáféréssel a tárfiókhoz.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**A használt tárfióknév nem oldható fel.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80C83060 |
| **HRESULT (decimális)** | -2134364064 |
| **Hibasztring** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Szervizelésre van szükség** | Yes |

1. Ellenőrizze, hogy fel tudja-e oldani a tároló DNS-nevét a kiszolgálóról.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Ellenőrizze, hogy létezik-e a tárfiók.](#troubleshoot-storage-account)
3. [Győződjön meg arról, hogy a tárfiók tűzfal- és virtuális hálózati beállításai megfelelően vannak konfigurálva (ha engedélyezve vannak).](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Ismeretlen hiba történt a tárfiók elérésekor.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c8308a |
| **HRESULT (decimális)** | -2134364022 |
| **Hibasztring** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Szervizelés szükséges** | Yes |

1. [Ellenőrizze, hogy létezik-e a tárfiók.](#troubleshoot-storage-account)
2. [Győződjön meg arról, hogy a tárfiók tűzfal- és virtuális hálózati beállításai megfelelően vannak konfigurálva (ha engedélyezve vannak).](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**A szinkronizálás a tárfiók zárolt miatt meghiúsult.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c83092 |
| **HRESULT (decimális)** | -2134364014 |
| **Hibasztring** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Szervizelés szükséges** | Yes |

Ez a hiba azért fordul elő, mert a tárfiók csak olvasható [erőforrás-zárolással rendelkezik.](../../azure-resource-manager/management/lock-resources.md) A probléma megoldásához távolítsa el a tárfiók írásvédett erőforrás-zárolását. 

<a id="-1906441138"></a>**A szinkronizálás a szinkronizálási adatbázissal való probléma miatt meghiúsult.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x8e5e044e |
| **HRESULT (decimális)** | -1906441138 |
| **Hibasztring** | JET_errWriteConflict |
| **Szervizelés szükséges** | Yes |

Ez a hiba akkor fordul elő, ha probléma van a Azure File Sync. Ha ez a probléma bekövetkezik, hozzon létre egy támogatási kérést, és fel fogjuk lépni Önnel a probléma megoldásához.

<a id="-2134364053"></a>**A Azure File Sync telepített ügynök verziója nem támogatott.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80C8306B |
| **HRESULT (decimális)** | -2134364053 |
| **Hibasztring** | ECS_E_AGENT_VERSION_BLOCKED |
| **Szervizelésre van szükség** | Yes |

Ez a hiba akkor fordul elő, ha az Azure File Sync-ügynök kiszolgálóra telepített verziója nem támogatott. A probléma megoldásához [frissítsen]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) egy [támogatott ügynökverzióra.]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)

<a id="-2134351810"></a>**Elérte az Azure-fájlmegosztások tárhelykorlátját.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c8603e |
| **HRESULT (decimális)** | -2134351810 |
| **Hibasztring** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Szervizelésre van szükség** | Yes |

Ez a hiba az Azure-fájlmegosztások tárterületi korlátjának elérésekor következik be, ami akkor fordulhat elő, ha az Azure-fájlmegosztásra vonatkozó kvóta van érvényben, vagy ha az Azure-fájlmegosztás használata túllépi a korlátokat. További információért tekintse meg az [Azure-fájlmegosztások aktuális korlátait.](storage-files-scale-targets.md)

1. Keresse meg a szinkronizálási csoportot a Társzinkronizálási szolgáltatásban.
2. Válassza ki a felhőbeli végpontot a szinkronizálási csoportban.
3. Jegyezze fel az Azure-fájlmegosztás nevét a megnyitott panelen.
4. Válassza ki a társított tárfiókot. Ha ez a hivatkozás sikertelen, a hivatkozott tárfiók el lett távolítva.

    ![Képernyőkép a felhőbeli végpont részletek panelről, a tárfiókra mutató hivatkozással.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Válassza **a Fájlok** lehetőséget a fájlmegosztások listájának megtekintéséhez.
6. Kattintson a felhőbeli végpont által hivatkozott Azure-fájlmegosztás sorának végén található három pontra.
7. Ellenőrizze, hogy a **Használat** értéke a **Kvóta** alatt van-e. Vegye figyelembe, hogy hacsak nincs megadva alternatív kvóta, a kvóta megegyezik az Azure-fájlmegosztás [maximális méretével.](storage-files-scale-targets.md)

    ![Képernyőkép az Azure-fájlmegosztás tulajdonságairól.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Ha a megosztás megtelt, és nincs megadva kvóta, a probléma megoldásának egyik lehetséges módja, ha az aktuális kiszolgálói végpont összes almappáját saját szinkronizálási csoportba tartozó, önálló kiszolgálói végponttá alakítja. Ezáltal minden almappa külön Azure-fájlmegosztással fog szinkronizálni.

<a id="-2134351824"></a>**Az Azure-fájlmegosztás nem található.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c86030 |
| **HRESULT (decimális)** | -2134351824 |
| **Hibasztring** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Szervizelésre van szükség** | Yes |

Ez a hiba akkor fordul elő, ha az Azure-fájlmegosztás nem érhető el. Hibaelhárítás:

1. [Ellenőrizze, hogy létezik-e a tárfiók.](#troubleshoot-storage-account)
2. [Győződjön meg arról, hogy az Azure-fájlmegosztás létezik.](#troubleshoot-azure-file-share)

Ha az Azure-fájlmegosztás törölve lett, létre kell hoznia egy új fájlmegosztást, majd újra létre kell hoznia a szinkronizálási csoportot. 

<a id="-2134364042"></a>**A szinkronizálás fel van függesztve, amíg az Azure-előfizetés fel van függesztve.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80C83076 |
| **HRESULT (decimális)** | -2134364042 |
| **Hibasztring** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Szervizelés szükséges** | Yes |

Ez a hiba akkor fordul elő, ha az Azure-előfizetés fel van függesztve. A szinkronizálás újból engedélyezve lesz, ha az Azure-előfizetés visszaáll. További [információ: Miért van letiltva](../../cost-management-billing/manage/subscription-disabled.md) az Azure-előfizetésem, és hogyan aktiválható újra?

<a id="-2134375618"></a>**A tárfiókhoz tűzfal vagy virtuális hálózatok vannak konfigurálva.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c8033e |
| **HRESULT (decimális)** | -2134375618 |
| **Hibasztring** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **Szervizelésre van szükség** | Yes |

Ez a hiba akkor fordul elő, ha az Azure-fájlmegosztás nem érhető el egy tárfiók tűzfala miatt, vagy amiatt, hogy a tárfiók egy virtuális hálózathoz tartozik. Ellenőrizze, hogy a tárfiók tűzfal- és virtuális hálózati beállításai megfelelően vannak-e konfigurálva. További információ: [Configure firewall and virtual network settings (Tűzfal- és virtuális hálózati beállítások konfigurálása).](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) 

<a id="-2134375911"></a>**A szinkronizálás a szinkronizálási adatbázissal való probléma miatt meghiúsult.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c80219 |
| **HRESULT (decimális)** | -2134375911 |
| **Hibasztring** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Szervizelésre van szükség** | No |

Ez a hiba általában magától megoldódik, és a következő esetekben fordulhat elő:

* Nagy számú fájlváltozás történt a szinkronizálási csoport kiszolgálói között.
* Nagy számú hiba az egyes fájlokon és könyvtárakon.

Ha ez a hiba néhány óránál tovább fennáll, hozzon létre egy támogatási kérést, és fel fogjuk lépni Önnel a probléma megoldásához.

<a id="-2146762487"></a>**A kiszolgáló nem tudott biztonságos kapcsolatot létesíteni. A felhőszolgáltatás váratlan tanúsítványt kapott.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x800b0109 |
| **HRESULT (decimális)** | -2146762487 |
| **Hibasztring** | CERT_E_UNTRUSTEDROOT |
| **Szervizelés szükséges** | Yes |

Ez a hiba akkor fordulhat elő, ha a szervezet TLS-lezáró proxyt használ, vagy ha egy rosszindulatú entitás elfogja a kiszolgáló és a Azure File Sync közötti forgalmat. Ha biztos benne, hogy ez várható (mivel a szervezet TLS-lezáró proxyt használ), kihagyhatja a tanúsítvány-ellenőrzést egy beállításjegyzék-felülbírálással.

1. Hozza létre a SkipVerifyingPinnedRootCertificate beállításazonosítót.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Indítsa újra a szinkronizálási szolgáltatást a regisztrált kiszolgálón.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Ennek a beállításazonosítónak a beállításértéknek a beállításával a Azure File Sync-ügynök minden helyileg megbízható TLS-/SSL-tanúsítványt elfogad, amikor adatokat ad át a kiszolgáló és a felhőszolgáltatás között.

<a id="-2147012894"></a>**Nem lehetett kapcsolatot létesíteni a szolgáltatással.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80072ee2 |
| **HRESULT (decimális)** | -2147012894 |
| **Hibasztring** | WININET_E_TIMEOUT |
| **Szervizelés szükséges** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**A szinkronizálás hitelesítési probléma miatt meghiúsult.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c80300 |
| **HRESULT (decimális)** | -2134375680 |
| **Hibasztring** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Szervizelésre van szükség** | Yes |

Ez a hiba általában azért fordul elő, mert a kiszolgáló ideje helytelen. Ha a kiszolgáló virtuális gépen fut, ellenőrizze, hogy a gazdagépen megfelelő-e az idő.

<a id="-2134364040"></a>**A szinkronizálás a tanúsítvány lejárata miatt meghiúsult.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c83078 |
| **HRESULT (decimális)** | -2134364040 |
| **Hibasztring** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Szervizelésre van szükség** | Yes |

Ez a hiba akkor következik be, ha a hitelesítéshez használt tanúsítvány lejárt.

A tanúsítvány lejártának ellenőrzéséhez hajtsa végre a következő lépéseket:  
1. Nyissa meg a Tanúsítványok MMC beépülő modult, válassza a Számítógépfiók lehetőséget, és lépjen a Tanúsítványok (Helyi számítógép)\Személyes\Tanúsítványok pontra.
2. Ellenőrizze, hogy az ügyfél-hitelesítési tanúsítvány lejárt-e.

Ha az ügyfél-hitelesítési tanúsítvány lejárt, a probléma megoldásához végezze el az alábbi lépéseket:

1. Ellenőrizze, Azure File Sync ügynök 4.0.1.0-s vagy újabb verziója telepítve van-e.
2. Futtassa az alábbi PowerShell-parancsot a kiszolgálón: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**A szinkronizálás meghiúsult, mert a hitelesítési tanúsítvány nem található.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c80228 |
| **HRESULT (decimális)** | -2134375896 |
| **Hibasztring** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Szervizelés szükséges** | Yes |

Ez a hiba akkor következik be, ha a hitelesítéshez használt tanúsítvány nem található.

A probléma megoldásához végezze el az alábbi lépéseket:

1. Ellenőrizze, Azure File Sync ügynök 4.0.1.0-s vagy újabb verziója van-e telepítve.
2. Futtassa az alábbi PowerShell-parancsot a kiszolgálón: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**A szinkronizálás meghiúsult, mert a hitelesítési identitás nem található.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c83079 |
| **HRESULT (decimális)** | -2134364039 |
| **Hibasztring** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Szervizelés szükséges** | Yes |

Ez a hiba azért fordul elő, mert a kiszolgálói végpont törlése sikertelen volt, és a végpont részben törölt állapotban van. A probléma megoldásához próbálja meg ismét törölni a kiszolgálói végpontot.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**A kiszolgálóvégpontot tároló köteten kevés a lemezterület.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x8e5e0211 |
| **HRESULT (decimális)** | -1906441711 |
| **Hibasztring** | JET_errLogDiskFull |
| **Szervizelésre van szükség** | Yes |

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c8031a |
| **HRESULT (decimális)** | -2134375654 |
| **Hibasztring** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Szervizelésre van szükség** | Yes |

Ez a hiba akkor következik be, ha a kötet megtelt. Ez a hiba általában azért fordul elő, mert kiszolgálóvégponton kívüli fájlok foglalnak el lemezterületet a köteten. Szabadítson fel lemezterületet a köteten további kiszolgálóvégpontokkal, fájlok másik kötetre való áthelyezésével vagy a kiszolgálóvégpontot tároló kötet méretének növelésével.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**A szolgáltatás még nem áll készen a kiszolgálóvégponttal való szinkronizálásra.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c8300f |
| **HRESULT (decimális)** | -2134364145 |
| **Hibasztring** | ECS_E_REPLICA_NOT_READY |
| **Szervizelésre van szükség** | No |

Ez a hiba azért fordul elő, mert a felhőbeli végpont olyan tartalommal lett létrehozva, amely már létezik az Azure-fájlmegosztáson. Azure File Sync az Azure-fájlmegosztásban az összes tartalmat, mielőtt engedélyezték volna, hogy a kiszolgálóvégpont folytassa a kezdeti szinkronizálást.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**A szinkronizálás sok különálló fájl problémái miatt meghiúsult.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c8023b |
| **HRESULT (decimális)** | -2134375877 |
| **Hibasztring** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Szervizelésre van szükség** | Yes |

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c8021c |
| **HRESULT (decimális)** | -2134375908 |
| **Hibasztring** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Szervizelés szükséges** | Yes |

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c80253 |
| **HRESULT (decimális)** | -2134375853 |
| **Hibasztring** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Szervizelés szükséges** | Yes |

A szinkronizálási munkamenetek meghiúsulnak ezen hibák valamelyike miatt, ha sok fájl nem szinkronizálható elemenkénti hibákkal. Az elemenkénti hibák elhárításához [hajtsa](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) végre a Hogyan, hogy vannak-e olyan fájlok vagy mappák, amelyek nem szinkronizálódnak? című szakaszban. A szinkronizálási hibák ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED nyisson meg egy támogatási esetet.

> [!NOTE]
> Azure File Sync naponta egyszer létrehoz egy ideiglenes VSS-pillanatképet a kiszolgálón a megnyitott leírókat tároló fájlok szinkronizálása számára.

<a id="-2134376423"></a>**A szinkronizálás a kiszolgálóvégpont elérési útjának problémája miatt meghiúsult.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c80019 |
| **HRESULT (decimális)** | -2134376423 |
| **Hibasztring** | ECS_E_SYNC_INVALID_PATH |
| **Szervizelésre van szükség** | Yes |

Győződjön meg arról, hogy az elérési út létezik, helyi NTFS-köteten található, és nem újraelemzési pont vagy meglévő kiszolgálóvégpont.

<a id="-2134375817"></a>**A szinkronizálás meghiúsult, mert a szűrőillesztő verziója nem kompatibilis az ügynök verziójával**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80C80277 |
| **HRESULT (decimális)** | -2134375817 |
| **Hibasztring** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Szervizelésre van szükség** | Yes |

Ez a hiba akkor fordul elő, ha a felhőbeli rétegzési szűrő illesztőprogramjának (StorageSync.sys) betöltött verziója nem kompatibilis a társzinkronizálási ügynök (FileSyncSvc) szolgáltatással. Ha az Azure File Sync-ügynök frissítve lett, indítsa újra a kiszolgálót a telepítés befejezéséhez. Ha a hiba továbbra is fennáll, távolítsa el az ügynököt, indítsa újra a kiszolgálót, majd telepítse újra az Azure File Sync-ügynököt.

<a id="-2134376373"></a>**A szolgáltatás jelenleg nem érhető el.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c8004b |
| **HRESULT (decimális)** | -2134376373 |
| **Hibasztring** | ECS_E_SERVICE_UNAVAILABLE |
| **Szervizelésre van szükség** | No |

Ez a hiba azért következik be, mert az Azure File Sync szolgáltatás nem érhető el. Ez a hiba automatikusan megoldódik, ha az Azure File Sync szolgáltatás ismét elérhetővé válik.

<a id="-2146233088"></a>**A szinkronizálás egy kivétel miatt meghiúsult.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80131500 |
| **HRESULT (decimális)** | -2146233088 |
| **Hibasztring** | COR_E_EXCEPTION |
| **Szervizelésre van szükség** | No |

Ez a hiba akkor fordul elő, ha a szinkronizálás kivétel miatt meghiúsult. Ha a hiba több órán keresztül fennáll, hozzon létre egy támogatási kérést.

<a id="-2134364045"></a>**A szinkronizálás meghiúsult, mert a tárfiók átveszi a rel egy másik régióba.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c83073 |
| **HRESULT (decimális)** | -2134364045 |
| **Hibasztring** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Szervizelésre van szükség** | Yes |

Ez a hiba azért következik be, mert a tárfiók átadta a feladatait egy másik régiónak. Az Azure File Sync nem támogatja a tárfiókok feladatátvételi funkcióját. A Azure File Syncben felhőbeli végpontként használt Azure-fájlmegosztásokat tartalmazó tárfiókokon nem lehet feladatátvételt végezni. Feladatátvétel esetén a szinkronizálás leáll, és az újonnan rétegzett fájlok esetében váratlan adatvesztést is okozhat. A probléma megoldásához helyezze a tárfiókot az elsődleges régióba.

<a id="-2134375922"></a>**A szinkronizálás a szinkronizálási adatbázis átmeneti problémája miatt meghiúsult.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c8020e |
| **HRESULT (decimális)** | -2134375922 |
| **Hibasztring** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Szervizelésre van szükség** | No |

Ez a hiba a szinkronizálási adatbázis belső hibája miatt következik be. A szinkronizálási újrapróbálkozáskor ez a hiba automatikusan megoldódik. Ha a hiba hosszabb ideig fennáll, hozzon létre egy támogatási kérést, és kapcsolatba lépünk Önnel a probléma megoldásához.

<a id="-2134364024"></a>**A szinkronizálás a bérlő módosítása miatt Azure Active Directory meghiúsult**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c83088 |
| **HRESULT (decimális)** | -2134364024 | 
| **Hibasztring** | ECS_E_INVALID_AAD_TENANT |
| **Szervizelésre van szükség** | Yes |

Győződjön meg arról, hogy a legújabb Azure File Sync ügynökkel. A 10-es Azure File Sync támogatja az előfizetés másik bérlőre Azure Active Directory áthelyezését.
 
Ha már rendelkezik a legújabb ügynökverzióval, hozzáférést kell biztosítania a Microsoft.StorageSync alkalmazásnak a tárfiókhoz (lásd: Győződjön meg arról, hogy Azure File Sync rendelkezik hozzáféréssel a [tárfiókhoz).](#troubleshoot-rbac)

<a id="-2134364010"></a>**A szinkronizálás meghiúsult, mert a tűzfal és a virtuális hálózati kivétel nincs konfigurálva**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c83096 |
| **HRESULT (decimális)** | -2134364010 | 
| **Hibasztring** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Szervizelésre van szükség** | Yes |

Ez a hiba akkor fordul elő, ha a tűzfal és a virtuális hálózat beállításai engedélyezve vannak a tárfiókon, és a "Megbízható Microsoft-szolgáltatások hozzáférhetnek ehhez a tárfiókhoz" kivétel nincs bejelölve. A probléma megoldásához kövesse az üzembehelyezési útmutató [Tűzfal- és virtuális hálózati beállítások konfigurálása](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) című szakaszában szereplő lépéseket.

<a id="-2147024891"></a>**A szinkronizálás nem sikerült, mert a rendszerkötet-információs mappára vonatkozó engedélyek helytelenek.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80070005 |
| **HRESULT (decimális)** | -2147024891 |
| **Hibasztring** | ERROR_ACCESS_DENIED |
| **Szervizelésre van szükség** | Yes |

Ez a hiba akkor következhet be, ha az NT AUTHORITY\SYSTEM fiók nem rendelkezik engedélyekkel a rendszerkötet-információkat tartalmazó mappához a kiszolgálóvégpont kötetén. Vegye figyelembe, hogy ha az egyes fájlok szinkronizálása nem sikerül a ERROR_ACCESS_DENIED, hajtsa végre a Hibaelhárítás [fájlonként/könyvtár-szinkronizálási](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors) hibák esetén című szakaszban található lépéseket.

A probléma megoldásához végezze el az alábbi lépéseket:

1. Töltse le a [PsExec](/sysinternals/downloads/psexec) eszközt.
2. Futtassa a következő parancssort egy rendszergazda jogú parancssorból a parancssor rendszerfiókkal való megnyitásához: **PsExec.exe -i -s -d cmd** 
3. A rendszerfiók alatt futó parancssorból futtassa a következő parancsot annak megerősítéséhez, hogy az NT AUTHORITY\SYSTEM fióknak nincs hozzáférése a **cacls "meghajtóbetűjel:\system volume information" /T /C** rendszerkötet-információkat tartalmazó mappához.
4. Ha az NT AUTHORITY\SYSTEM fiók nem rendelkezik hozzáféréssel a rendszerkötet-információkat tartalmazó mappához, futtassa a következő parancsot: **cacls "meghajtóbetűjel:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - Ha a 4. lépés a hozzáférés megtagadása miatt meghiúsul, futtassa a következő parancsot a rendszerkötet-információkat tartalmazó mappa tulajdonjogának átvételéhez, majd ismételje meg a 4. lépést: **takeown /A /R /F "meghajtóbetűjel:\System Volume Information"**

<a id="-2134375810"></a>**A szinkronizálás nem sikerült, mert az Azure-fájlmegosztást törölték, majd újból létrehozták.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c8027e |
| **HRESULT (decimális)** | -2134375810 |
| **Hibasztring** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Szervizelésre van szükség** | Yes |

Ez a hiba azért fordul elő, mert az Azure File Sync nem támogatja az Azure-fájlmegosztások törlését és újbóli létrehozását ugyanazon szinkronizálási csoporton belül. 

A probléma megoldásához törölje és hozza újból létre a szinkronizálási csoportot a következő lépések elvégzésével:

1. Törölje a szinkronizálási csoportban található összes kiszolgálóvégpontot.
2. Törölje a felhőbeli végpontot. 
3. Törölje a szinkronizálási csoportot.
4. Ha a felhőbeli rétegezés engedélyezve volt egy kiszolgálóvégponton, törölje az árva rétegzett fájlokat a kiszolgálón a rétegzett fájlok nem érhetők el a kiszolgálón [a](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) kiszolgálóvégpont törlése után című szakaszban dokumentált lépések elvégzésével.
5. Hozza létre újra a szinkronizálási csoportot.

<a id="-2145844941"></a>**A szinkronizálás meghiúsult, mert a HTTP-kérés átirányítva lett**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80190133 |
| **HRESULT (decimális)** | -2145844941 |
| **Hibasztring** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Szervizelésre van szükség** | Yes |

Ez a hiba azért fordul elő, mert az Azure File Sync nem támogatja a HTTP-átirányítást (3xx állapotkód). A probléma megoldásához tiltsa le a HTTP-átirányítást a proxykiszolgálón vagy hálózati eszközön.

<a id="-2134364027"></a>**Időtúllépés történt az offline adatátvitel során, de még folyamatban van.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c83085 |
| **HRESULT (decimális)** | -2134364027 |
| **Hibasztring** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Szervizelésre van szükség** | No |

Ez a hiba akkor fordul elő, ha egy adatbetöltési művelet túllépi az időkorlátot. Ez a hiba figyelmen kívül hagyható, ha a szinkronizálás folyamatban van (az AppliedItemCount nagyobb, mint 0). Lásd Hogyan aktuális szinkronizálási munkamenet [állapotának figyelése.](#how-do-i-monitor-the-progress-of-a-current-sync-session).

<a id="-2134375814"></a>**A szinkronizálás meghiúsult, mert a kiszolgálóvégpont elérési útja nem található a kiszolgálón.**  

| Hiba | Code |
|-|-|
| **Hresult** | 0x80c8027a |
| **HRESULT (decimális)** | -2134375814 |
| **Hibasztring** | ECS_E_SYNC_ROOT_DIRECTORY_NOT_FOUND |
| **Szervizelés szükséges** | Yes |

Ez a hiba akkor fordul elő, ha a kiszolgálóvégpont elérési útjaként használt könyvtárat átnevezték vagy törölték. Ha a könyvtárat átnevezték, nevezze át a könyvtárat az eredeti névre, és indítsa újra a Storage Sync Agent szolgáltatást (FileSyncSvc).

Ha a címtár törölve lett, a következő lépésekkel távolítsa el a meglévő kiszolgálóvégpontot, és hozzon létre egy új kiszolgálóvégpontot egy új elérési úttal:

1. Távolítsa el a kiszolgálóvégpontot a szinkronizálási csoportból a Kiszolgálóvégpont [eltávolítása dokumentumban található lépéseket követve.](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint)
2. Hozzon létre egy új kiszolgálóvégpontot a szinkronizálási csoportban a Kiszolgálói végpont [hozzáadása dokumentum lépéseit követve.](./storage-sync-files-server-endpoint.md#add-a-server-endpoint)

### <a name="common-troubleshooting-steps"></a>Gyakori hibaelhárítási lépések
<a id="troubleshoot-storage-account"></a>**Ellenőrizze, hogy létezik-e a tárfiók.**  
# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Keresse meg a szinkronizálási csoportot a Társzinkronizálási szolgáltatásban.
2. Válassza ki a felhőbeli végpontot a szinkronizálási csoportban.
3. Jegyezze fel az Azure-fájlmegosztás nevét a megnyitott panelen.
4. Válassza ki a társított tárfiókot. Ha ez a hivatkozás sikertelen, a hivatkozott tárfiók el lett távolítva.
    ![Képernyőkép a felhőbeli végpont részletező panelről, a tárfiókra mutató hivatkozással.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Győződjön meg arról, hogy az Azure-fájlmegosztás létezik.**  
# <a name="portal"></a>[Portál](#tab/azure-portal)
1. A **tárfiók** főoldalára való visszatéréshez kattintson a bal oldali tartalomjegyzék Áttekintés gombjára.
2. Válassza **a Fájlok** lehetőséget a fájlmegosztások listájának megtekintéséhez.
3. Ellenőrizze, hogy a felhőbeli végpont által hivatkozott fájlmegosztás megjelenik-e a fájlmegosztások listájában (ezt fel kellett jegyeznie a fenti 1. lépésben).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Győződjön Azure File Sync, hogy rendelkezik hozzáféréssel a tárfiókhoz.**  
# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Kattintson **a Hozzáférés-vezérlés (IAM)** elemre a bal oldali tartalomjegyzékben.
1. Kattintson **a Szerepkör-hozzárendelések** fülre azonfelhasználók és alkalmazások (szolgáltatásnév) listájához, akik hozzáféréssel rendelkezik a tárfiókhoz.
1. Ellenőrizze, hogy a **Microsoft.StorageSync** **vagy a Hybrid File Sync Szolgáltatás (régi** alkalmazásnév) megjelenik-e az Olvasó és adatelérési **szerepkörű listában.** 

    ![Képernyőkép a hibrid File Sync szolgáltatásnévről a tárfiók hozzáférés-vezérlés lapján](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Ha **a Microsoft.StorageSync** File Sync **Hybrid File Sync szolgáltatás** nem jelenik meg a listában, hajtsa végre a következő lépéseket:

    - Kattintson a **Hozzáadás** parancsra.
    - A Szerepkör **mezőben** válassza az **Olvasó és adatelérés lehetőséget.**
    - A Select **(Kijelölés) mezőbe** írja be a **Microsoft.StorageSync parancsot,** válassza ki a szerepkört, majd kattintson a Save (Mentés) **gombra.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

## <a name="cloud-tiering"></a>Felhőbeli rétegzés 
A felhőbeli rétegezésben a hibáknak két útvonala van:

- A fájlok rétege sikertelen lehet, ami azt jelenti, Azure File Sync sikertelenül próbál rétegeni egy fájlt a Azure Files.
- A fájlok visszahívása sikertelen lehet, ami azt jelenti, hogy a Azure File Sync fájlrendszerszűrője (StorageSync.sys) nem tudja letölteni az adatokat, amikor egy felhasználó rétegzett fájlhoz próbál hozzáférni.

A hibáknak két fő osztálya lehet, amelyek bármelyik hibaútvonalon keresztül előfordulhatnak:

- Felhőbeli tárolási hibák
    - *Átmeneti tárolási szolgáltatás rendelkezésre állási problémái.* További információ: Azure [Storage szolgáltatói szerződés (SLA).](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)
    - *Nem érhető el az Azure-fájlmegosztás.* Ez a hiba általában akkor fordul elő, ha törli az Azure-fájlmegosztást, amikor az még mindig felhőbeli végpont egy szinkronizálási csoportban.
    - *Nem elérhető tárfiók.* Ez a hiba általában akkor fordul elő, ha törli a tárfiókot, miközben az továbbra is rendelkezik olyan Azure-fájlmegosztással, amely egy szinkronizálási csoport felhőbeli végpontja. 
- Kiszolgálóhibák 
  - *Azure File Sync fájlrendszerszűrő (StorageSync.sys) nincs betöltve.* A rétegezésre/visszahívási kérésekre való válaszadáshoz Azure File Sync fájlrendszerszűrőt. A szűrő betöltése több okból is előfordulhat, de a leggyakoribb ok az, hogy egy rendszergazda manuálisan eltávolítja a memóriából. A Azure File Sync fájlrendszerszűrőt mindig be kell tölteni, hogy a Azure File Sync megfelelően működjön.
  - *Hiányzó, sérült vagy egyéb módon hibás újraelemzési pont.* Az újraelemzési pont egy speciális adatstruktúra egy fájlon, amely két részből áll:
    1. Egy újraelemzési címke, amely azt jelzi az operációs rendszernek, hogy a Azure File Sync fájlrendszerszűrőnek (StorageSync.sys) valamilyen műveletet kell el kell látnia a fájl I/O-műveletével. 
    2. Adatok újraelemzése, amely azt jelzi, hogy a fájlrendszer szűri a fájl URI-ját a társított felhővégponton (az Azure-fájlmegosztáson). 
        
       Az újraelemzési pont sérülésének leggyakoribb módja az, ha egy rendszergazda megpróbálja módosítani a címkét vagy annak adatait. 
  - *Hálózati kapcsolati problémák.* A fájlok réteghez vagy visszahíváshoz a kiszolgálónak internetkapcsolattal kell rendelkezik.

A következő szakaszok ismertetik, hogyan háríthatja el a felhőbeli rétegezéssel kapcsolatos problémákat, és megállapíthatja, hogy a probléma a felhőalapú tárolással vagy a kiszolgálóval kapcsolatos.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>A kiszolgáló rétegzési tevékenységének monitorozása  
A kiszolgálón végzett rétegezés figyelése érdekében használja a 9003-as, 9016-os és 9029-es eseményazonosítót a Telemetria eseménynaplójában (amely a következő helyen található: Alkalmazások és szolgáltatások\Microsoft\FileSync\Agent a Eseménynapló).

- A 9003-as eseményazonosító hibaeloszlást biztosít a kiszolgálóvégponthoz. Például: Hibák teljes száma, Hibakód stb. Megjegyzés: hibakódonként egy esemény naplózása történik.
- A 9016-os eseményazonosító szellemes eredményeket ad egy kötethez. A szabad terület százalékos aránya például a következő: A munkamenetben szellemben lévő fájlok száma, a sikertelen fájlok száma stb.
- A 9029-es eseményazonosító a kiszolgálóvégponthoz biztosít munkamenet-információkat. Például a munkamenetben megkísérelt fájlok száma, a munkamenetben rétegzett fájlok száma, a már rétegzett fájlok száma stb.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>A kiszolgáló előhívási tevékenységének monitorozása
A kiszolgáló előhívási tevékenységének figyelése érdekében használja a 9005-ös, 9006-os, 9009-es és 9059-es eseményazonosítót a telemetriai eseménynaplóban (amely a következő helyen található: Applications and Services\Microsoft\FileSync\Agent in Eseménynapló).

- A 9005-ös eseményazonosító a kiszolgálóvégpont visszahívási megbízhatóságát biztosítja. Például: Az elért egyedi fájlok teljes száma, A sikertelen hozzáférésű egyedi fájlok összesen stb.
- A 9006-os eseményazonosító visszahívási hibák eloszlását biztosítja a kiszolgálóvégponthoz. Például: Összes sikertelen kérelem, Hibakód stb. Vegye figyelembe, hogy hibakódonként egy esemény naplózása történik.
- A 9009-es eseményazonosító visszahívási munkamenet-információkat biztosít a kiszolgálóvégponthoz. Például DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed stb.
- A 9059-es eseményazonosító alkalmazás-visszahívási elosztást biztosít a kiszolgálóvégponthoz. Például: ShareId, Application Name és TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>A rétegbe nem rétegzett fájlok hibaelhárítása
Ha a fájlok rétege nem lesz Azure Files:

1. A Eseménynapló tekintse át az Alkalmazások és szolgáltatások\Microsoft\FileSync\Agent mappában található telemetriai, működési és diagnosztikai eseménynaplókat. 
   1. Ellenőrizze, hogy a fájlok léteznek-e az Azure-fájlmegosztásban.

      > [!NOTE]
      > A rétegzett fájlokat szinkronizálni kell egy Azure-fájlmegosztással.

   2. Ellenőrizze, hogy a kiszolgáló rendelkezik-e internetkapcsolattal. 
   3. Ellenőrizze, hogy Azure File Sync -szűrőillesztők (StorageSync.sys és StorageSyncGuard.sys) futnak-e:
       - Rendszergazda jogú parancssorban futtassa a következőt: `fltmc` . Ellenőrizze, hogy StorageSync.sys és StorageSyncGuard.sys-e a fájlrendszerszűrő illesztőprogramjai.

> [!NOTE]
> A rendszer óránként egyszer naplózza a 9003-as eseményazonosítót a telemetriai eseménynaplóban, ha egy fájl rétege sikertelen (hibakódonként egy esemény naplózása történik). Tekintse meg [a Rétegezéssel kapcsolatos](#tiering-errors-and-remediation) hibák és szervizelés szakaszt, és ellenőrizze, hogy a hibakód tartalmazza-e a javítási lépéseket.

### <a name="tiering-errors-and-remediation"></a>Rétegzett hibák és szervizelés

| Hresult | HRESULT (decimális) | Hibasztring | Probléma | Szervizelés |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | A fájl rétege nem sikerült, mert a kezdeti feltöltés folyamatban van. | Nincs szükség beavatkozásra. A fájl a kezdeti feltöltés befejezése után rétegzett lesz. |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | A fájl rétege nem sikerült, mert használatban van. | Nincs szükség beavatkozásra. A fájl akkor lesz rétegezve, amikor már nincs használatban. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | A fájl rétege nem sikerült, mert ki van zárva a szinkronizálás miatt. | Nincs szükség beavatkozásra. A szinkronizálás kihagyási listájában található fájlok nem rétegezhetők. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | A fájl rétege nem sikerült, mert nem található a kiszolgálón. | Nincs szükség beavatkozásra. Ha a hiba továbbra is fennáll, ellenőrizze, hogy a fájl létezik-e a kiszolgálón. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | A fájl rétege nem sikerült, mert törölve lett az Azure-fájlmegosztásban. | Nincs szükség beavatkozásra. A fájl törölve lesz a kiszolgálón a következő letöltési szinkronizálási munkamenet futtatásakor. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | A fájl rétege hálózati probléma miatt nem sikerült. | Nincs szükség beavatkozásra. Ha a hiba továbbra is fennáll, ellenőrizze az Azure-fájlmegosztás hálózati kapcsolatát. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | A fájl rétege hálózati probléma miatt nem sikerült. | Nincs szükség beavatkozásra. Ha a hiba továbbra is fennáll, ellenőrizze az Azure-fájlmegosztás hálózati kapcsolatát. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | A fájl rétege hozzáférés-megtagadás miatti hiba miatt nem sikerült. Ez a probléma akkor merülhet fel, ha a fájl egy írásvédett DFS-R replikációs mappában található. | Az Azure Files Sync nem támogatja az írásvédett DFS-R replikációs mappákban található kiszolgálói végpontokat. További információt a [tervezési útmutatóban](./storage-sync-files-planning.md#distributed-file-system-dfs) talál. |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | A fájl rétege hálózati probléma miatt nem sikerült. | Nincs szükség beavatkozásra. Ha a hiba továbbra is fennáll, ellenőrizze az Azure-fájlmegosztás hálózati kapcsolatát. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | A fájl rétege nem sikerült, mert a fájl mérete kisebb, mint a támogatott méret. | Ha az ügynök verziója 9.0-snál régebbi, a minimális támogatott fájlméret 64 KB. Ha az ügynök verziója 9.0-s vagy újabb, a minimális támogatott fájlméret a fájlrendszer fürtméretén alapul (a fájlrendszer fürtméretének kétszerese). Ha például a fájlrendszerfürt mérete 4 kb, a fájl minimális mérete 8 kb. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | A fájl rétege egy Azure Storage-hiba miatt nem sikerült. | Ha a hiba sokáig fennáll, nyisson meg egy támogatási kérést. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | A fájl rétege nem sikerült, mert egyszerre volt előhívva. | Nincs szükség beavatkozásra. A fájl akkor lesz rétegezve, amikor a visszahívás befejeződik, és a fájl már nincs már használatban. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | A fájl rétege nem sikerült, mert nem szinkronizált az Azure-fájlmegosztással. | Nincs szükség beavatkozásra. A fájl akkor lesz rétegezve, miután szinkronizálva lett az Azure-fájlmegosztásba. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | A fájl rétegezése nem sikerült, mert a felhőbeli rétegezés szűrőillesztője (storagesync.sys) nem fut. | A probléma megoldásához nyisson meg egy rendszergazda jogú parancssort, és futtassa a következő parancsot: `fltmc load storagesync`<br>Ha a storagesync szűrőillesztő nem töltődik be az fltmc parancs futtatásakor, távolítsa el a Azure File Sync-ügynököt, indítsa újra a kiszolgálót, és telepítse újra a Azure File Sync ügynököt. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | A fájl rétege nem sikerült, mert nincs elegendő lemezterület a kiszolgálóvégpontot tároló köteten. | A probléma megoldásához szabadítson fel legalább 100 MB lemezterületet azon a köteten, ahol a kiszolgálóvégpont található. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | A fájl rétege nem sikerült, mert nem szinkronizált az Azure-fájlmegosztással. | Nincs szükség beavatkozásra. A fájl akkor lesz rétegezve, miután szinkronizálva lett az Azure-fájlmegosztásba. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | A fájl rétege nem sikerült, mert ez egy nem támogatott újraelemzési pont. | Ha a fájl egy adatdeduplikációs újraelemzési pont, kövesse a [tervezési útmutató](./storage-sync-files-planning.md#data-deduplication) lépéseit az adatdeduplikáció támogatásának engedélyezéséhez. A deduplikáción kívüli egyéb újraelemzési pontokat tartalmazó fájlok nem támogatottak és nem rétegezhetők.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | A fájl rétege nem sikerült, mert módosítva lett. | Nincs szükség beavatkozásra. A fájl akkor lesz rétegezve, ha a módosított fájl szinkronizálva lett az Azure-fájlmegosztásba. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | A fájl rétege nem sikerült, mert nem szinkronizált az Azure-fájlmegosztással. | Nincs szükség beavatkozásra. A fájl akkor lesz rétegezve, miután szinkronizálva lett az Azure-fájlmegosztásba. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | A fájl rétege hálózati probléma miatt nem sikerült. | Nincs szükség beavatkozásra. Ha a hiba továbbra is fennáll, ellenőrizze az Azure-fájlmegosztás hálózati kapcsolatát. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | A fájl rétege nem sikerült, mert módosítva lett. | Nincs szükség beavatkozásra. A fájl akkor lesz rétegezve, ha a módosított fájl szinkronizálva lett az Azure-fájlmegosztásba. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | A fájl rétege nem sikerült, mert nem áll rendelkezésre elegendő rendszererőforrás. | Ha a hiba továbbra is fennáll, vizsgálja meg, hogy melyik alkalmazás vagy kernelmódú illesztőprogram meríti ki a rendszererőforrásokat. |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | A fájl rétegezése egy I/O-hiba miatt nem sikerült, amikor a felhőbeli rétegzett adatbázisba ír. | Ha a hiba továbbra is fennáll, futtassa a chkdsk-et a köteten, és ellenőrizze a tárolóhardvert. |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | A fájl rétegezése nem sikerült, mert a felhőbeli rétegezésű adatbázis nem fut. | A probléma megoldásához indítsa újra a FileSyncSvc szolgáltatást vagy -kiszolgálót. Ha a hiba továbbra is fennáll, futtassa a chkdsk-et a köteten, és ellenőrizze a tárolóhardvert. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>A nem visszahívható fájlok hibaelhárítása  
Ha a fájlok visszahívása sikertelen:
1. A Eseménynapló tekintse át az Alkalmazások és szolgáltatások\Microsoft\FileSync\Agent mappában található telemetriai, működési és diagnosztikai eseménynaplókat.
    1. Ellenőrizze, hogy a fájlok léteznek-e az Azure-fájlmegosztásban.
    2. Ellenőrizze, hogy a kiszolgáló rendelkezik-e internetkapcsolattal. 
    3. Nyissa meg a Szolgáltatások MMC beépülő modult, és ellenőrizze, hogy fut-e a Storage Sync Agent szolgáltatás (FileSyncSvc).
    4. Ellenőrizze, hogy Azure File Sync -szűrőillesztők (StorageSync.sys és StorageSyncGuard.sys) futnak-e:
        - Rendszergazda jogú parancssorban futtassa a következőt: `fltmc` . Ellenőrizze, hogy StorageSync.sys és StorageSyncGuard.sys-e a fájlrendszerszűrő illesztőprogramjai.

> [!NOTE]
> A telemetriai eseménynapló óránként egyszer naplózza a 9006-os eseményazonosítót, ha egy fájl visszahívása sikertelen (hibakódonként egy eseményt naplóz). Tekintse meg [az Előhívási hibák és szervizelés szakaszt,](#recall-errors-and-remediation) és ellenőrizze, hogy a hibakód szerepel-e a javítási lépések között.

### <a name="recall-errors-and-remediation"></a>Hibák visszahívása és szervizelés

| Hresult | HRESULT (decimális) | Hibasztring | Probléma | Szervizelés |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | A fájl visszahívása I/O-időtúllépés miatt nem sikerült. Ez a probléma több okból is előfordulhat: a kiszolgálói erőforrások megkötései, a gyenge hálózati kapcsolat vagy egy Azure Storage-probléma (például szabályozás) miatt. | Nincs szükség beavatkozásra. Ha a hiba több órán keresztül fennáll, kérjük, nyisson meg egy támogatási esetet. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | A fájl visszahívása hálózati probléma miatt nem sikerült.  | Ha a hiba továbbra is fennáll, ellenőrizze az Azure-fájlmegosztás hálózati kapcsolatát. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | A fájl visszahívása nem sikerült, mert a kiszolgálóvégpont törölve lett. | A probléma megoldásához lásd: A rétegzett fájlok nem érhetők el a kiszolgálón [a kiszolgálóvégpont törlése után.](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | A fájl visszahívása egy hozzáférés megtagadva hiba miatt nem sikerült. Ez a hiba akkor fordulhat elő, ha a tárfiók tűzfalának és virtuális hálózatának beállításai engedélyezve vannak, és a kiszolgáló nem fér hozzá a tárfiókhoz. | A probléma megoldásához adja hozzá a kiszolgáló IP-címét vagy virtuális hálózatát a telepítési útmutató Tűzfal- és [virtuális](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) hálózati beállítások konfigurálása című szakaszában található lépéseket követve. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | A fájlt nem sikerült visszahívni, mert nem érhető el az Azure-fájlmegosztásban. | A probléma megoldásához ellenőrizze, hogy létezik-e a fájl az Azure-fájlmegosztásban. Ha a fájl létezik az Azure-fájlmegosztásban, frissítsen a legújabb Azure File Sync [ügynök verziójára.](./storage-files-release-notes.md#supported-versions) |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | A fájl visszahívása a tárfiók engedélyezési hibája miatt nem sikerült. | A probléma megoldásához ellenőrizze, Azure File Sync [rendelkezik-e hozzáféréssel a tárfiókhoz.](?tabs=portal1%252cazure-portal#troubleshoot-rbac) |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | A fájl visszahívása nem sikerült, mert az Azure-fájlmegosztás nem érhető el. | Ellenőrizze, hogy a fájlmegosztás létezik-e és elérhető-e. Ha a fájlmegosztást törölték és újból létrehozták, [](?tabs=portal1%252cazure-portal#-2134375810) hajtsa végre a Szinkronizálás szakaszban dokumentált lépéseket, mert az Azure-fájlmegosztást törölték, majd újból létrehozták a szinkronizálási csoport törléséhez és újbóli végrehajtásához. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | A fájl visszahívása nem sikerült, mert nem áll rendelkezésre elegendő rendszererőforrás. | Ha a hiba továbbra is fennáll, vizsgálja meg, hogy melyik alkalmazás vagy kernelmódú illesztőprogram meríti ki a rendszererőforrásokat. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | A fájl visszahívása nem sikerült, mert nincs elég memória. | Ha a hiba továbbra is fennáll, vizsgálja meg, melyik alkalmazás vagy kernelmódú illesztőprogram okozza a memóriahiányos állapotot. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | A fájl visszahívása nem sikerült, mert nincs elegendő lemezterület. | A probléma megoldásához szabadítson fel lemezterületet a köteten a fájlok másik kötetre való áthelyezésével, növelje a kötet méretét, vagy kényszerítse a fájlok rétegezését az Invoke-StorageSyncCloudTiering parancsmaggal. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>A rétegzett fájlokat nem lehet elérni a kiszolgálón a kiszolgáló végpontjának törlését követően
A kiszolgálón lévő rétegzett fájlok elérhetetlenné válnak, ha a rendszer nem hívja vissza a fájlokat a kiszolgálóvégpont törlése előtt.

Naplózott hibák, ha a rétegzett fájlok nem érhetők el
- Fájl szinkronizálása esetén a -2147942467 (0x80070043 - ERROR_BAD_NET_NAME) hibakód lesz naplózva az ItemResults eseménynaplóban
- Fájl visszahívásakor a -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND) hibakód lesz naplózva a RecallResults eseménynaplóban

A rétegzett fájlokhoz való hozzáférés visszaállítása a következő feltételek teljesülése esetén lehetséges:
- A kiszolgálóvégpont az elmúlt 30 napban lett törölve
- A felhőbeli végpont nem lett törölve 
- A fájlmegosztás nem lett törölve
- A szinkronizálási csoport nem lett törölve

Ha a fenti feltételek teljesülnek, visszaállíthatja a kiszolgálón található fájlokhoz való hozzáférést, ha 30 napon belül ismételten létrehozza a kiszolgálón a kiszolgálóvégpontot ugyanazzal az útvonallal. 

Ha a fenti feltételek nem teljesülnek, a hozzáférés visszaállítása nem lehetséges, mert a kiszolgálón található rétegzett fájlok már elárvultak. Az árva rétegzett fájlok eltávolításához kövesse az alábbi utasításokat.

**Megjegyzések**
- Ha a rétegzett fájlok nem érhetők el a kiszolgálón, a teljes fájlnak elérhetőnek kell lennie, ha közvetlenül hozzáfér az Azure-fájlmegosztáshoz.
- Ha meg szeretné akadályozni az árva rétegzett fájlokat a jövőben, kövesse a kiszolgálóvégpont törlése [kiszolgálóvégpont](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint) törlésekor dokumentumban található lépéseket.

<a id="get-orphaned"></a>**Árva rétegzett fájlok listájának lehívása** 

1. Ellenőrizze Azure File Sync hogy telepítve van-e az ügynök 5.1-es vagy újabb verziója.
2. Az árva rétegzett fájlok listához futtassa az alábbi PowerShell-parancsokat:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Mentse a OrphanTieredFiles.txt kimeneti fájlt arra az esetre, ha a fájlokat vissza kell állítani a biztonsági másolatból a törlésük után.

<a id="remove-orphaned"></a>**Árva rétegzett fájlok eltávolítása** 

*1. lehetőség: Az árva rétegzett fájlok törlése*

Ez a beállítás törli az árva rétegzett fájlokat a Windows Serveren, de el kell távolítania a kiszolgálóvégpontot, ha az 30 nap után fennáll, vagy egy másik szinkronizálási csoporthoz csatlakozik. Fájlütközések akkor fordulnak elő, ha a kiszolgálóvégpont újbóli létrehozása előtt a fájlok frissülnek a Windows Serveren vagy az Azure-fájlmegosztáson.

1. Ellenőrizze Azure File Sync hogy telepítve van-e az ügynök 5.1-es vagy újabb verziója.
2. Biztonsági másolat készítése az Azure-fájlmegosztásról és a kiszolgálóvégpont helyéről.
3. Távolítsa el a kiszolgálóvégpontot a szinkronizálási csoportból (ha létezik) a Kiszolgálói végpont [eltávolítása dokumentumban található lépéseket követve.](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint)

> [!Warning]  
> Ha a kiszolgálóvégpontot a Remove-StorageSyncOrphanedTieredFiles-parancsmag használata előtt nem távolítja el, az árva rétegzett fájl törlése a kiszolgálón törli az Azure-fájlmegosztásban található teljes fájlt. 

4. Az árva rétegzett fájlok listához futtassa a következő PowerShell-parancsokat:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Mentse a OrphanTieredFiles.txt kimeneti fájlt arra az esetre, ha a fájlokat vissza kell állítani a biztonsági másolatból a törlésük után.
6. Az árva rétegzett fájlok törléséhez futtassa a következő PowerShell-parancsokat:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Megjegyzések** 
- A kiszolgálón módosított, az Azure-fájlmegosztással nem szinkronizált rétegzett fájlok törlődnek.
- Az elérhető (nem árva) rétegzett fájlok nem törlődnek.
- A nem rétegzett fájlok a kiszolgálón maradnak.

7. Nem kötelező: A 3. lépésben törölt kiszolgálóvégpont újbóli létrehozása.

*2. lehetőség: Az Azure-fájlmegosztás csatlakoztatása és a kiszolgálón árva fájlok másolása*

Ez a beállítás nem igényli a kiszolgálóvégpont eltávolítását, de elegendő lemezterületet igényel a teljes fájlok helyi másolásához.

1. [Csatlakoztassa](./storage-how-to-use-files-windows.md) az Azure-fájlmegosztást arra a Windows Serverre, amely árva rétegzett fájlokat tartalmaz.
2. Az árva rétegzett fájlok listához futtassa a következő PowerShell-parancsokat:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. A kimeneti OrphanTieredFiles.txt a kiszolgálón található árva rétegzett fájlok azonosításához.
4. Írja felül az árva rétegzett fájlokat úgy, hogy a teljes fájlt átmásolja az Azure-fájlmegosztásból a Windows Serverre.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Kiszolgálóról váratlanul elő visszahívott fájlok hibaelhárítása  
A víruskereső, a biztonsági mentés és a sok fájlt olvasó egyéb alkalmazások nem szándékolt visszahívásokat okoznak, kivéve, ha nem tartják tiszteletben az offline attribútum kihagyása és a fájlok tartalmának olvasásának kihagyása miatt. Az offline fájlok kihagyását támogató termékek esetében a kihagyás segíthet elkerülni a nem kívánt visszahívásokat a víruskeresések, a biztonsági mentési feladatok és a hasonló műveletek során.

Érdeklődjön a szoftverszállítónál, hogy megtudja, hogyan konfigurálhatja a megoldást az offline fájlok olvasásának kihagyására.

A nem szándékolt visszahívások más forgatókönyvekben is előfordulhatnak, például amikor fájlokat böngész a Fájlkezelő. Ha a kiszolgálón található Fájlkezelőben felhőalapú rétegzett fájlokat tartalmazó mappákat nyit meg, az nem kívánt visszahívásokat eredményezhet. Ez még gyakrabban előfordul, ha engedélyezve van egy víruskereső megoldás a kiszolgálón.

> [!NOTE]
>Használja a 9059-es eseményazonosítót a telemetriai eseménynaplóban annak meghatározásához, hogy melyik alkalmazás(ok) okozza a visszahívásokat. Ez az esemény alkalmazás-visszahívási elosztást biztosít a kiszolgálóvégponthoz, és óránként egyszer naplózva lesz.

### <a name="tls-12-required-for-azure-file-sync"></a>A TLS 1.2 a Azure File Sync

A TLS-beállításokat a kiszolgáló beállításjegyzék-beállításai között [tudja megtekinteni.](/windows-server/security/tls/tls-registry-settings) 

Ha proxyt használ, tekintse át a proxy dokumentációját, és ellenőrizze, hogy a TLS 1.2 használatára van-e konfigurálva.

## <a name="general-troubleshooting"></a>Általános hibaelhárítás
Ha a kiszolgálón Azure File Sync problémákba ütközik, először kövesse az alábbi lépéseket:
1. A Eseménynapló tekintse át a telemetriai, működési és diagnosztikai eseménynaplókat.
    - A szinkronizálási, rétegzési és visszahívási problémákat a rendszer az Alkalmazások és szolgáltatások\Microsoft\FileSync\Agent mappában található telemetriai, diagnosztikai és működési eseménynaplókba naplózza.
    - A kiszolgáló kezelésével kapcsolatos problémákat (például a konfigurációs beállításokat) az Alkalmazások és szolgáltatások\Microsoft\FileSync\Management mappában található működési és diagnosztikai eseménynaplókban naplózza a rendszer.
2. Ellenőrizze, Azure File Sync szolgáltatás fut-e a kiszolgálón:
    - Nyissa meg a Szolgáltatások MMC beépülő modult, és ellenőrizze, hogy fut-e a Storage Sync Agent szolgáltatás (FileSyncSvc).
3. Ellenőrizze, hogy Azure File Sync -szűrőillesztők (StorageSync.sys és StorageSyncGuard.sys) futnak-e:
    - Egy rendszergazda jogú parancssorban futtassa a következőt: `fltmc` . Ellenőrizze, hogy StorageSync.sys és StorageSyncGuard.sys fájlrendszerszűrő-illesztőprogramok listája.

Ha a probléma nem oldódik meg, futtassa az AFSDiag eszközt, és küldje el a .zip fájl kimenetét az esethez rendelt támogatási szakembernek további diagnosztizálás érdekében.

Az AFSDiag futtatásához hajtsa végre az alábbi lépéseket.

Az ügynök 11-es vagy újabb verziója:
1. Nyisson meg egy emelt szintű PowerShell-ablakot, majd futtassa a következő parancsokat (minden parancs után nyomja le az Enter billentyűt):

    > [!NOTE]
    >Az AFSDiag a naplók gyűjtése előtt létrehozza a kimeneti könyvtárat és egy ideiglenes mappát, és a végrehajtás után törli az ideiglenes mappát. Adjon meg egy kimeneti helyet, amely nem tartalmaz adatokat.
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. Reprodukálja a problémát. Ha elkészült, írja be a **D billentyűt.**
3. A naplókat és nyomkövetési fájlokat tartalmazó .zip-fájl a megadott kimeneti könyvtárba lesz mentve. 

Az ügynök 10-es és korábbi verzióihoz:
1. Hozzon létre egy könyvtárat, ahová az AFSDiag kimenete mentve lesz (például C:\Output).
    > [!NOTE]
    >Az AFSDiag a naplók gyűjtése előtt törli a kimeneti könyvtár összes tartalmát. Adjon meg egy kimeneti helyet, amely nem tartalmaz adatokat.
2. Nyisson meg egy emelt szintű PowerShell-ablakot, majd futtassa a következő parancsokat (minden parancs után nyomja le az Enter billentyűt):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. A rendszermag mód Azure File Sync adja meg az **1** értéket (ha másként nincs megadva, a részletes nyomkövetések létrehozásához), majd nyomja le az Enter billentyűt.
4. A felhasználói Azure File Sync nyomkövetési szintjeként adja meg az **1** értéket (ha másként nincs megadva, további részletes nyomkövetések létrehozásához), majd nyomja le az Enter billentyűt.
5. Reprodukálja a problémát. Ha elkészült, írja be a **D billentyűt.**
6. A naplókat és nyomkövetési fájlokat tartalmazó .zip-fájl a megadott kimeneti könyvtárba lesz mentve.


## <a name="see-also"></a>Lásd még
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
- [Azure Files gyakori kérdések](storage-files-faq.md)
- [Azure Files-problémák hibaelhárítása Windowson](storage-troubleshoot-windows-file-connection-problems.md)
- [Azure Files-problémák hibaelhárítása Linuxon](storage-troubleshoot-linux-file-connection-problems.md)
