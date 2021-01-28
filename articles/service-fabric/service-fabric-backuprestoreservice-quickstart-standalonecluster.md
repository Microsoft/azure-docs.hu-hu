---
title: Rendszeres biztonsági mentés/visszaállítás az önálló Azure-Service Fabric
description: Az alkalmazásadatok rendszeres biztonsági mentésének engedélyezéséhez használjon önálló Service Fabric rendszeres biztonsági mentési és visszaállítási funkcióját.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: d4abf1cd4561a40aaafa5c01865eb12882884422
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927949"
---
# <a name="periodic-backup-and-restore-in-a-standalone-service-fabric"></a>Rendszeres biztonsági mentés és visszaállítás önálló Service Fabric
> [!div class="op_single_selector"]
> * [Fürtök az Azure-on](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Önálló fürtök](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

A Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a megbízható, elosztott, a szolgáltatásokon alapuló felhőalapú alkalmazások fejlesztését és kezelését. Lehetővé teszi az állapot nélküli és állapot-nyilvántartó mikro-szolgáltatások futtatását is. Az állapot-nyilvántartó szolgáltatások megváltoztathatják a kérést és a választ, illetve a teljes tranzakciót túlmutató, mérvadó állapotot is. Ha egy állapot-nyilvántartó szolgáltatás egy katasztrófa miatt hosszabb ideig leáll vagy elveszti az adatokat, előfordulhat, hogy vissza kell állítania az állapotának újabb biztonsági mentését, hogy továbbra is biztosítson szolgáltatást a biztonsági mentés után.

Service Fabric replikálja az állapotot több csomópont között, így biztosítva, hogy a szolgáltatás nagyon elérhető legyen. A szolgáltatás továbbra is elérhető marad, még akkor is, ha a fürt egyik csomópontja meghibásodik. Bizonyos esetekben azonban továbbra is kívánatos, hogy a szolgáltatási adatmennyiség megbízható legyen a szélesebb körű meghibásodások ellen.
 
Előfordulhat például, hogy egy szolgáltatásnak biztonsági másolatot kell készítenie az adatairól, hogy megvédje a következő helyzetekben:
- Egy teljes Service Fabric fürt végleges elvesztése.
- Egy szolgáltatás-partíció replikái többségének végleges elvesztése
- Rendszergazdai hibák, amelyek miatt az állapot véletlenül törölve vagy sérült. Például egy megfelelő jogosultsággal rendelkező rendszergazda hibásan törli a szolgáltatást.
- A szolgáltatásban az adatsérülést okozó hibák. Ez például akkor fordulhat elő, ha egy szolgáltatási kód frissítése megkezdi a hibás adatgyűjtést egy megbízható gyűjteménybe. Ilyen esetben a kódnak és az adatfájlnak is korábbi állapotra kell visszaállítania.
- Offline adatfeldolgozás. Előfordulhat, hogy az üzleti intelligenciához tartozó adatok offline feldolgozását az adatok előállítására szolgáló szolgáltatástól függetlenül kell megtenni.

A Service Fabric egy beépített API-t biztosít a [biztonsági mentési és visszaállítási](service-fabric-reliable-services-backup-restore.md)műveletek elvégzéséhez. Az alkalmazások fejlesztői ezeket az API-kat használhatják a szolgáltatás állapotának rendszeres biztonsági mentésére. Emellett, ha a szolgáltatás-rendszergazdák egy adott időpontban szeretnének elindítani egy biztonsági mentést a szolgáltatáson kívülről (például az alkalmazás frissítése előtt), a fejlesztőknek API-ként kell kitenniük a biztonsági mentést (és a visszaállítást) a szolgáltatásból. A biztonsági mentések fenntartása további költségeket mutat. Tegyük fel például, hogy öt növekményes biztonsági mentést szeretne készíteni félévente, majd egy teljes biztonsági mentést. A teljes biztonsági mentés után törölheti az előző növekményes biztonsági mentéseket. Ennek a megközelítésnek további kódokat kell megadnia, amely az alkalmazásfejlesztés során további költségeket eredményez.

Az alkalmazásadatok rendszeres időközönkénti biztonsági mentése alapvető követelmény az elosztott alkalmazások kezeléséhez és az adatvesztéssel szembeni védelemhez, illetve a szolgáltatások rendelkezésre állásának tartós elvesztéséhez. Service Fabric egy opcionális biztonsági mentési és visszaállítási szolgáltatást biztosít, amely lehetővé teszi az állapot-nyilvántartó Reliable Services (beleértve a Actor Servicest is) rendszeres biztonsági mentését anélkül, hogy további kódokat kellene írnia. Emellett elősegíti a korábban készített biztonsági mentések visszaállítását is. 

A Service Fabric API-kat biztosít a következő, rendszeres biztonsági mentési és visszaállítási funkcióhoz kapcsolódó funkciók eléréséhez:

- Rendszeres biztonsági mentést ütemezhet a megbízható állapot-nyilvántartó szolgáltatásokról és Reliable Actorsekről a biztonsági mentés (külső) tárolóhelyekre történő feltöltésének támogatásával. Támogatott tárolási helyszínek
    - Azure Storage
    - Fájlmegosztás (helyszíni)
- Biztonsági másolatok enumerálása
- Egy partíció nem tervezett biztonsági másolatának elindítása
- Partíció visszaállítása a korábbi biztonsági mentés használatával
- Biztonsági mentések ideiglenes felfüggesztése
- Biztonsági mentések megőrzésének kezelése (közelgő)

## <a name="prerequisites"></a>Előfeltételek
* Service Fabric-fürtöt a Fabric 6,4-es vagy újabb verziójával. A szükséges csomag letöltésének lépéseiért tekintse meg ezt a [cikket](service-fabric-cluster-creation-for-windows-server.md) .
* X. 509 tanúsítvány a biztonsági másolatok tárolásához a tárolóhoz való kapcsolódáshoz szükséges titkok titkosításához. A [cikkből](service-fabric-windows-cluster-x509-security.md) megtudhatja, hogyan szerezzen be vagy hozzon létre egy önaláírt X. 509 tanúsítványt.

* Service Fabric Service Fabric SDK 3,0-es vagy újabb verziójának használatával létrehozott megbízható állapot-nyilvántartó alkalmazás. A .NET Core 2,0-ot célzó alkalmazások esetében az alkalmazást Service Fabric SDK 3,1-es vagy újabb verziójának használatával kell felépíteni.
* A konfigurációs hívások készítéséhez telepítse a Microsoft. ServiceFabric. PowerShell. http modult (előzetes verzió).

```powershell
    Install-Module -Name Microsoft.ServiceFabric.PowerShell.Http -AllowPrerelease
```

> [!NOTE]
> Ha a PowerShellGet verziója kisebb, mint a 1.6.0, frissítenie kell a *-AllowPrerelease* jelző támogatásának hozzáadásához:
>
> `Install-Module -Name PowerShellGet -Force`

* Győződjön meg arról, hogy a fürt a paranccsal van csatlakoztatva, `Connect-SFCluster` mielőtt konfigurációs kérelmet hozna a Microsoft. ServiceFabric. PowerShell. http modul használatával.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Biztonsági mentési és visszaállítási szolgáltatás engedélyezése
Először engedélyeznie kell a _biztonsági mentési és visszaállítási szolgáltatást_ a fürtben. Szerezze be a telepíteni kívánt fürt sablonját. Használhatja a [minta sablonokat](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples). Engedélyezze a _biztonsági mentési és visszaállítási szolgáltatást_ a következő lépésekkel:

1. Ellenőrizze, hogy a be `apiversion` van-e állítva a `10-2017` fürt konfigurációs fájljába, és ha nem, frissítse az alábbi kódrészletben látható módon:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Most engedélyezze a _biztonsági mentési és visszaállítási szolgáltatást_ úgy, hogy hozzáadja a következő `addonFeatures` szakaszt a `properties` szakaszban, ahogy az a következő kódrészletben látható: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Konfigurálja az X. 509 tanúsítványt a hitelesítő adatok titkosításához. Ez fontos annak biztosítása érdekében, hogy a tárolóhoz való kapcsolódáshoz megadott hitelesítő adatok titkosítottak maradjanak, mielőtt megőrzik. A titkosítási tanúsítvány konfigurálásához adja hozzá a következő `BackupRestoreService` szakaszt a `fabricSettings` szakaszhoz, ahogy az a következő kódrészletben látható: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Miután frissítette a fürt konfigurációs fájlját az előző módosításokkal, alkalmazza őket, és hagyja, hogy a telepítés/frissítés befejeződjön. Ha elkészült, a _biztonsági mentési és visszaállítási szolgáltatás_ elindul a fürtben. A szolgáltatás URI `fabric:/System/BackupRestoreService` -ja és a szolgáltatás a Service Fabric Explorer rendszerszolgáltatás szakasza alatt található. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Rendszeres biztonsági mentés engedélyezése megbízható állapot-nyilvántartó szolgáltatáshoz és Reliable Actors
A megbízható állapot-nyilvántartó szolgáltatás és a Reliable Actors rendszeres biztonsági mentésének engedélyezéséhez hajtsa végre a lépéseket. Az alábbi lépések feltételezik
- A fürt biztonsági mentési és visszaállítási service_ van konfigurálva.
- Megbízható állapot-nyilvántartó szolgáltatás van telepítve a fürtön. Ebben a rövid útmutatóban az alkalmazás URI-ja, `fabric:/SampleApp` az alkalmazáshoz tartozó megbízható állapot-nyilvántartó szolgáltatás URI azonosítója pedig `fabric:/SampleApp/MyStatefulService` . A szolgáltatás egyetlen partícióval van üzembe helyezve, a partíció azonosítója pedig `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7` .  

### <a name="create-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

Első lépésként létre kell hoznia egy biztonsági mentési szabályzatot, amely leírja a biztonsági mentés ütemezését, a biztonsági mentési adatok céljának tárolását, a szabályzat nevét, a teljes biztonsági mentési és adatmegőrzési szabályzatot a biztonsági mentési tár számára. 

A biztonsági mentési tár esetében hozzon létre fájlmegosztást, és adjon ReadWrite hozzáférést ehhez a fájlmegosztáshoz az összes Service Fabric csomópontos gépen. Ez a példa feltételezi, hogy a megosztás neve szerepel a következőben: `BackupStore` `StorageServer` .


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft. ServiceFabric. PowerShell. http modul használatával

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Rest-hívás a PowerShell használatával

Hajtsa végre a következő PowerShell-szkriptet a szükséges REST API új szabályzat létrehozásához való meghívásához.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer használata

1. A Service Fabric Explorerban navigáljon a biztonsági másolatok lapra, és válassza a műveletek > biztonsági mentési szabályzat létrehozása lehetőséget.

    ![Biztonsági mentési szabályzat létrehozása][6]

2. Adja meg az adatokat. Önálló fürtök esetén a fájlmegosztás ki kell választani.

    ![Biztonsági mentési szabályzat fájlmegosztás létrehozása][7]

### <a name="enable-periodic-backup"></a>Rendszeres biztonsági mentés engedélyezése
Miután meghatározta az alkalmazás adatvédelmi követelményeinek teljesítésére vonatkozó házirendet, a biztonsági mentési szabályzatot társítani kell az alkalmazáshoz. A követelménytől függően a biztonsági mentési szabályzat egy alkalmazáshoz, szolgáltatáshoz vagy partícióhoz társítható.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft. ServiceFabric. PowerShell. http modul használatával

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>Rest-hívás a PowerShell használatával
Hajtsa végre a következő PowerShell-szkriptet a szükséges REST API meghívásához a biztonsági mentési szabályzatnak a fenti lépésben létrehozott névvel való hozzárendeléséhez az `BackupPolicy1` alkalmazással `SampleApp` .

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer használata

1. Válasszon ki egy alkalmazást, és lépjen a művelet elemre. Kattintson az alkalmazás biztonsági másolatának engedélyezése/frissítése elemre.

    ![Alkalmazás biztonsági mentésének engedélyezése][3] 

2. Végül válassza ki a kívánt szabályzatot, és válassza a *biztonsági mentés engedélyezése* lehetőséget.

    ![Házirend kiválasztása][4]

### <a name="verify-that-periodic-backups-are-working"></a>Az időszakos biztonsági másolatok működésének ellenőrzése

Az alkalmazás biztonsági mentésének engedélyezése után a megbízható állapot-nyilvántartó szolgáltatásokhoz és az alkalmazáshoz Reliable Actors tartozó összes partíció rendszeres időközönként a kapcsolódó biztonsági mentési szabályzatnak megfelelően elindítja a biztonsági mentést.

![A Partition BackedUp állapotának eseménye][0]

### <a name="list-backups"></a>Biztonsági másolatok listázása

A megbízható állapot-nyilvántartó szolgáltatásokhoz tartozó összes partícióhoz társított biztonsági másolatok, valamint az alkalmazás Reliable Actors enumerálása a _GetBackups_ API használatával lehetséges. A követelményektől függően előfordulhat, hogy a biztonsági másolatok enumerálása alkalmazás, szolgáltatás vagy partíció számára lehetséges.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft. ServiceFabric. PowerShell. http modul használatával

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Rest-hívás a PowerShell használatával

Futtassa a következő PowerShell-szkriptet a HTTP API meghívásához, hogy enumerálja az alkalmazásban található összes partícióhoz létrehozott biztonsági másolatokat `SampleApp` .

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

A fenti Futtatás mintájának kimenete:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
FailureError            : 
```

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer használata

A Service Fabric Explorer biztonsági másolatainak megtekintéséhez navigáljon egy partícióra, és válassza a biztonsági másolatok lapot.

![Biztonsági másolatok enumerálása][5]

## <a name="limitation-caveats"></a>Korlátozás/kikötések
- Service Fabric PowerShell-parancsmagok előzetes verzió módban vannak.
- A Linuxon Service Fabric-fürtök támogatása nem támogatott.

## <a name="next-steps"></a>Következő lépések
- [Rendszeres biztonsági mentési konfiguráció ismertetése](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Biztonsági mentés visszaállítása REST API referenciája](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/create-bp-fileshare.png
