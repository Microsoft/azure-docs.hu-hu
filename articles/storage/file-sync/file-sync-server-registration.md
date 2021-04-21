---
title: Regisztrált kiszolgálók kezelése a Azure File Sync | Microsoft Docs
description: Ismerje meg, hogyan regisztrálhat windowsos kiszolgálót egy Azure File Sync-szinkronizálási szolgáltatással.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a85fb653636333beec5f53912a646b3e5619e37b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796952"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Regisztrált kiszolgálók kezelése a Azure File Sync
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ezt úgy teszi meg, hogy a Windows-kiszolgálókat az Azure-fájlmegosztás gyors gyorsítótárába alakítja. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A következő cikk bemutatja, hogyan regisztrálhat és kezelhet egy kiszolgálót egy Társzinkronizálási szolgáltatással. A [végpontok Azure File Sync](file-sync-deployment-guide.md) üzembe helyezési Azure File Sync információkért lásd: Azure File Sync üzembe helyezése.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Kiszolgáló regisztrálása vagy regisztrációjának megszinkronizálása a Storage Sync Service-sel
A kiszolgáló regisztrálása a Azure File Sync megbízhatósági kapcsolatot hoz létre a Windows Server és az Azure között. Ezzel a kapcsolattal aztán  kiszolgálóvégpontokat hozhat létre a kiszolgálón, amelyek adott mappákat képviselnek, amelyeket szinkronizálni kell egy Azure-fájlmegosztással (más néven felhőbeli *végponttal).* 

### <a name="prerequisites"></a>Előfeltételek
Ha regisztrálnia kell egy kiszolgálót egy Társzinkronizálási szolgáltatásban, először elő kell készítenie a kiszolgálót a szükséges előfeltételeknek:

* A kiszolgálón a Windows Server támogatott verziójának kell futnia. További információkért lásd: [Azure File Sync rendszerkövetelmények és együttműködési képesség.](file-sync-planning.md#windows-file-server-considerations)
* Győződjön meg arról, hogy a Storage Sync Service telepítve van. A Storage Sync Service üzembe helyezéséről további információért lásd: [How to deploy Azure File Sync](file-sync-deployment-guide.md).
* Győződjön meg arról, hogy a kiszolgáló csatlakozik az internethez, és hogy az Azure elérhető.
* Tiltsa le az IE fokozott biztonsági beállításait a rendszergazdák számára a Kiszolgálókezelő felhasználói felületén.
    
    ![A Kiszolgálókezelő felhasználói felülete az IE fokozott biztonsági konfiguráció kiemelésével](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Győződjön meg arról, Azure PowerShell modul telepítve van a kiszolgálón. Ha a kiszolgáló egy feladatátvevő fürt tagja, a fürt minden csomópontjának szüksége lesz az Az modulra. Az Az modul telepítésének további részleteit az Install and configure Azure PowerShell (Telepítés és [konfigurálás) Azure PowerShell.](/powershell/azure/install-Az-ps)

    > [!Note]  
    > Azt javasoljuk, hogy az Az PowerShell-modul legújabb verzióját használja a kiszolgálók regisztrálása/regisztrációjának a regisztrációja a regisztrációjukból. Ha az Az csomag korábban már telepítve van a kiszolgálón (és a PowerShell ezen a kiszolgálón 5.* vagy újabb verziójú), a parancsmaggal frissítheti `Update-Module` a csomagot. 
* Ha hálózati proxykiszolgálót használ a környezetben, konfigurálja a proxybeállításokat a kiszolgálón a szinkronizálási ügynök számára.
    1. A proxy IP-címének és portszámának meghatározása
    2. Szerkessze a következő két fájlt:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Adja hozzá az 1. ábrán (ez alatt a szakasz alatt) lévő sorokat a fenti két fájl /System.ServiceModel részében, amely a 127.0.0.1:8888 címet a megfelelő IP-címre módosítja (a 127.0.0.1 helyére), és a megfelelő portszámot (a 8888-asat cserélje le):
    4. Adja meg a WinHTTP proxybeállítást a parancssorból:
        * A proxy megjelenítése: netsh winhttp show proxy
        * A proxy beállítása: netsh winhttp set proxy 127.0.0.1:8888
        * A proxy alaphelyzetbe állítása: netsh winhttp reset proxy
        * ha ez az ügynök telepítése után van beállítva, indítsa újra a szinkronizálási ügynököt: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Kiszolgáló regisztrálása a Társzinkronizálási szolgáltatásban
Mielőtt egy kiszolgáló kiszolgálóvégpontként használható lenne *egy* Azure File Sync szinkronizálási csoportban, regisztrálni kell a *Storage Sync Service-be.*  Egy kiszolgáló egyszerre csak egy szinkronizálási szolgáltatással regisztrálható.

#### <a name="install-the-azure-file-sync-agent"></a>Az Azure File Sync-ügynök telepítése
1. [Töltse le a Azure File Sync ügynököt.](https://go.microsoft.com/fwlink/?linkid=858257)
2. Indítsa el a Azure File Sync ügynök telepítőjét.
    
    ![A Azure File Sync ügynök telepítője első panelje](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Győződjön meg arról, hogy a Azure File Sync ügynök frissítését a Microsoft Update. Ez azért fontos, mert a kiszolgálócsomag kritikus fontosságú biztonsági javításai és szolgáltatásfejlesztései a Microsoft Update.

    ![Győződjön Microsoft Update, hogy az ügynök Microsoft Update panelen engedélyezve van Azure File Sync ügynök telepítője](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Ha a kiszolgálót korábban még nem regisztrálták, a kiszolgálóregisztrációs felhasználói felület a telepítés befejezése után azonnal megjelenik.

> [!Important]  
> Ha a kiszolgáló egy feladatátvevő fürt tagja, a Azure File Sync ügynököt a fürt minden csomópontjára telepíteni kell.

#### <a name="register-the-server-using-the-server-registration-ui"></a>A kiszolgáló regisztrálása a kiszolgálóregisztrációs felhasználói felületen
1. Ha a kiszolgálóregisztrációs felhasználói felület nem indult el azonnal a Azure File Sync-ügynök telepítésének befejezése után, manuálisan is elindítható a `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` futtatásával.
2. Kattintson *a Bejelentkezés gombra* az Azure-előfizetés eléréséhez. 

    ![A kiszolgálóregisztrációs felhasználói felület nyitó párbeszédpanele](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Válassza ki a megfelelő előfizetést, erőforráscsoportot és tárolószinkronizálási szolgáltatást a párbeszédpanelen.

    ![A Társzinkronizálási szolgáltatás adatai](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Előzetes verzióban még egy bejelentkezés szükséges a folyamat befejezéséhez. 

    ![Bejelentkezési párbeszédpanel](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Ha a kiszolgáló egy feladatátvevő fürt tagja, minden kiszolgálónak futtatnia kell a kiszolgálóregisztrációt. Amikor megtekinti a regisztrált kiszolgálókat az Azure Portalon, a Azure File Sync automatikusan felismeri az egyes csomópontokat ugyanannak a feladatátvevő fürtnek a tagjaként, és megfelelően csoportosítja őket.

#### <a name="register-the-server-with-powershell"></a>A kiszolgáló regisztrálása a PowerShellben
A kiszolgálóregisztrációt a PowerShell használatával is elvégezheti. 

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>A kiszolgáló regisztrációjának a Storage Sync Service szolgáltatással való regisztrációjának a regisztrációja
A kiszolgálók szinkronizálási szolgáltatással való regisztrációjának a regisztrációja több lépésből áll. Vessünk egy pillantást a kiszolgáló regisztrációjának megfelelő módon való regisztrációjának a regisztrációra.

> [!Warning]  
> Ne kísérelje meg a szinkronizálással, a felhőbeli rétegezéssel vagy a Azure File Sync egyéb aspektusával kapcsolatos hibák elhárítását kiszolgáló regisztrációjának vagy regisztrációjának törlésével, illetve a kiszolgálóvégpont eltávolításával és újralétrehozásával, kivéve, ha a Microsoft mérnöke kifejezetten erre utasítja. A kiszolgáló regisztrációjának törlése és a kiszolgálóvégpont eltávolítása kioltó művelet, és a kiszolgálóvégpontokkal a kötetek rétegzett fájljai nem lesznek "újracsatlakozva" az Azure-fájlmegosztásban lévő helyükről a regisztrált kiszolgáló és kiszolgálóvégpont újbóli létrehozása után, ami szinkronizálási hibákat eredményez. Azt is vegye figyelembe, hogy a kiszolgálóvégpont-névtéren kívül található rétegzett fájlok véglegesen elveszhetnek. A rétegzett fájlok akkor is létezhetnek a kiszolgálóvégponton, ha a felhőbeli rétegezés soha nem volt engedélyezve.

#### <a name="optional-recall-all-tiered-data"></a>(Nem kötelező) Az összes rétegzett adat visszahívása
Ha azt szeretné, hogy a jelenleg rétegzett fájlok a Azure File Sync eltávolítása után is elérhetők legyen (azaz éles, és nem tesztkörnyezetről van szükség), hívja vissza a kiszolgálóvégpontokat tartalmazó kötetek összes fájlját. Tiltsa le a felhőbeli rétegezést az összes kiszolgálóvégponthoz, majd futtassa a következő PowerShell-parancsmagot:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Ha a kiszolgálóvégpontot üzemeltető helyi köteten nincs elegendő szabad hely az összes rétegzett adat visszahívása érdekében, a `Invoke-StorageSyncFileRecall` parancsmag sikertelen lesz.  

#### <a name="remove-the-server-from-all-sync-groups"></a>A kiszolgáló eltávolítása az összes szinkronizálási csoportból
Mielőtt törölné a kiszolgáló regisztrációját a Társzinkronizálási szolgáltatásban, a kiszolgálón található összes kiszolgálóvégpontot el kell távolítani. Ezt a következő Azure Portal:

1. Lépjen arra a társzinkronizálási szolgáltatásra, ahol a kiszolgáló regisztrálva van.
2. Távolítsa el a kiszolgáló összes kiszolgálóvégpontját a Társzinkronizálási szolgáltatás minden szinkronizálási csoportjából. Ehhez kattintson a jobb gombbal a megfelelő kiszolgálóvégpontra a szinkronizálási csoport panelen.

    ![Kiszolgálóvégpont eltávolítása egy szinkronizálási csoportból](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Ez egy egyszerű PowerShell-szkript használatával is meg lehet valósítható:

```powershell
Connect-AzAccount

$storageSyncServiceName = "<your-storage-sync-service>"
$resourceGroup = "<your-resource-group>"

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $storageSyncServiceName | ForEach-Object { 
    $syncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -ParentObject $syncGroup | Where-Object { $_.ServerEndpointName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -InputObject $_ 
    } 
}
```

#### <a name="unregister-the-server"></a>A kiszolgáló regisztrációjának a regisztrációja
Most, hogy minden adat elő lett idézve, és a kiszolgáló el lett távolítva az összes szinkronizálási csoportból, a kiszolgáló regisztrációja törölhető. 

1. A Azure Portal nyissa meg a  Társzinkronizálási szolgáltatás Regisztrált kiszolgálók szakaszát.
2. Kattintson a jobb gombbal arra a kiszolgálóra, amelyről le szeretné mondani a regisztrációt, majd kattintson a "Kiszolgáló regisztrációjának törlése" parancsra.

    ![Kiszolgáló regisztrációjának a regisztrációja](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Annak biztosítása Azure File Sync hogy az adatközpont jó szomszédja 
Mivel Azure File Sync ritkán ez az egyetlen szolgáltatás, amely fut az adatközpontban, előfordulhat, hogy korlátozni szeretné a hálózati és tárolási Azure File Sync.

> [!Important]  
> A túl alacsony beállítás hatással van a Azure File Sync és a felidézés teljesítményére.

### <a name="set-azure-file-sync-network-limits"></a>Hálózati Azure File Sync beállítása
A parancsmagok használatával Azure File Sync a hálózati `StorageSyncNetworkLimit` kihasználtságot.

> [!Note]  
> Rétegzett fájlok elérésekor a hálózati korlátok nem érvényesek.

Létrehozhat például egy új sávszélesség-korlátozást, hogy a Azure File Sync ne használjon 10 Mbps-t 9 és 17:00 között a munkahét során: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

A korlátot a következő parancsmag használatával láthatja:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

A hálózati korlátok eltávolításához használja a `Remove-StorageSyncNetworkLimit` et. A következő parancs például eltávolítja az összes hálózati korlátot:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Windows Server tárolási QoS használata 
Ha Azure File Sync windowsos virtualizálási gazdagépen futó virtuális gépen fut, a tárolási szolgáltatásminőség (tárolási szolgáltatásminőség) segítségével szabályozhatja a tároló I/O-használatát. A tárolási QoS-házirend beállítható maximumként (vagy korlátként, például a StorageSyncNetwork korlát fenti kényszerítésében) vagy minimálisként (vagy foglalásként). Ha maximális helyett minimumot ad meg, Azure File Sync a rendszer a rendelkezésre álló sávszélességet használja, ha más számítási feladatok nem használják. További információ: [Tárolási szolgáltatásminőség.](/windows-server/storage/storage-qos/storage-qos-overview)

## <a name="see-also"></a>Lásd még
- [Az Azure File Sync üzembe helyezésének megtervezése](file-sync-planning.md)
- [Az Azure File Sync üzembe helyezése](file-sync-deployment-guide.md)
- [Az Azure File Sync monitorozása](file-sync-monitoring.md)
- [Azure File Sync – hibaelhárítás](file-sync-troubleshoot.md)