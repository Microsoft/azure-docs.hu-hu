---
title: Rendszeres biztonsági mentés és visszaállítás az Azure Service Fabricben
description: Az alkalmazásadatok rendszeres biztonsági mentésének engedélyezéséhez használja Service Fabric rendszeres biztonsági mentési és visszaállítási funkcióját.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: 42097b50277e78b3f0e8f5e61a2bf70cc08dbc02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103198714"
---
# <a name="periodic-backup-and-restore-in-an-azure-service-fabric-cluster"></a>Rendszeres biztonsági mentés és visszaállítás egy Azure Service Fabric-fürtben
> [!div class="op_single_selector"]
> * [Fürtök az Azure-on](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Önálló fürtök](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

A Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a megbízható, elosztott, szolgáltatásokon alapuló felhőalapú alkalmazások fejlesztését és kezelését. Lehetővé teszi az állapot nélküli és állapot-nyilvántartó mikro-szolgáltatások futtatását is. Az állapot-nyilvántartó szolgáltatások megváltoztathatják a kérést és a választ, illetve a teljes tranzakciót túlmutató, mérvadó állapotot is. Ha egy állapot-nyilvántartó szolgáltatás hosszú ideig leáll, vagy egy katasztrófa miatt veszíti el az adatokat, előfordulhat, hogy vissza kell állítania az állapotának egy korábbi biztonsági mentését, hogy továbbra is biztosítson szolgáltatást a biztonsági mentés után.

Service Fabric replikálja az állapotot több csomópont között, így biztosítva, hogy a szolgáltatás nagyon elérhető legyen. A szolgáltatás továbbra is elérhető marad, még akkor is, ha a fürt egyik csomópontja meghibásodik. Bizonyos esetekben azonban továbbra is kívánatos, hogy a szolgáltatási adatmennyiség megbízható legyen a szélesebb körű meghibásodások ellen.
 
Előfordulhat például, hogy a szolgáltatás biztonsági másolatot szeretne készíteni az adatairól, hogy megvédje a következő helyzetekben:
- Egy teljes Service Fabric-fürt végleges elvesztése esetén.
- Egy szolgáltatás-partíció replikái többségének végleges elvesztése
- Rendszergazdai hibák, amelyek miatt az állapot véletlenül törölve vagy sérült. Például egy megfelelő jogosultsággal rendelkező rendszergazda hibásan törli a szolgáltatást.
- A szolgáltatásban az adatsérülést okozó hibák. Ez például akkor fordulhat elő, ha egy szolgáltatási kód frissítése megkezdi a hibás adatgyűjtést egy megbízható gyűjteménybe. Ilyen esetben a kódnak és az adatfájlnak is korábbi állapotra kell visszaállítania.
- Offline adatfeldolgozás. Előfordulhat, hogy az üzleti intelligenciához tartozó adatok offline feldolgozását az adatok előállítására szolgáló szolgáltatástól függetlenül kell megtenni.

A Service Fabric egy beépített API-t biztosít a [biztonsági mentési és visszaállítási](service-fabric-reliable-services-backup-restore.md)időponthoz. Az alkalmazások fejlesztői ezeket az API-kat használhatják a szolgáltatás állapotának rendszeres biztonsági mentésére. Emellett, ha a szolgáltatás-rendszergazdák egy adott időpontban szeretnének elindítani egy biztonsági mentést a szolgáltatáson kívülről, például az alkalmazás frissítése előtt, a fejlesztőknek API-ként kell kitenniük a biztonsági mentést (és a visszaállítást) a szolgáltatásból. A biztonsági mentések fenntartása további költségeket mutat. Tegyük fel például, hogy öt növekményes biztonsági mentést szeretne készíteni félévente, majd egy teljes biztonsági mentést. A teljes biztonsági mentés után törölheti az előző növekményes biztonsági mentéseket. Ennek a megközelítésnek további kódokat kell megadnia, amely az alkalmazásfejlesztés során további költségeket eredményez.

A Service Fabric biztonsági mentési és visszaállítási szolgáltatása lehetővé teszi az állapot-nyilvántartó szolgáltatásokban tárolt információk egyszerű és automatikus biztonsági mentését. Az alkalmazásadatok rendszeres biztonsági mentése alapvető fontosságú az adatvesztés és a szolgáltatás nem rendelkezésre állása elleni védelemhez. Service Fabric egy opcionális biztonsági mentési és visszaállítási szolgáltatást biztosít, amely lehetővé teszi az állapot-nyilvántartó Reliable Services (beleértve a Actor Servicest is) rendszeres biztonsági mentését anélkül, hogy további kódokat kellene írnia. Emellett elősegíti a korábban készített biztonsági mentések visszaállítását is. 


A Service Fabric API-kat biztosít a következő, rendszeres biztonsági mentési és visszaállítási funkcióhoz kapcsolódó funkciók eléréséhez:

- Rendszeres biztonsági mentést ütemezhet a megbízható állapot-nyilvántartó szolgáltatásokról és Reliable Actorsekről a biztonsági mentés (külső) tárolóhelyekre történő feltöltésének támogatásával. Támogatott tárolási helyszínek
    - Azure Storage
    - Fájlmegosztás (helyszíni)
- Biztonsági másolatok enumerálása
- Egy partíció ad hoc biztonsági másolatának elindítása
- Partíció visszaállítása a korábbi biztonsági mentés használatával
- Biztonsági mentések ideiglenes felfüggesztése
- Biztonsági mentések megőrzésének kezelése (közelgő)

## <a name="prerequisites"></a>Előfeltételek
* Service Fabric-fürtöt a Fabric 6,4-es vagy újabb verziójával. Tekintse át ezt a [cikket](service-fabric-cluster-creation-via-arm.md) Service Fabric-fürt Azure-erőforrás-sablonnal történő létrehozásának lépésein.
* X. 509 tanúsítvány a biztonsági másolatok tárolásához a tárolóhoz való kapcsolódáshoz szükséges titkok titkosításához. A [cikkből](service-fabric-cluster-creation-via-arm.md) megtudhatja, hogyan kérhet le vagy hozhat létre X. 509 tanúsítványt.
* Service Fabric Service Fabric SDK 3,0-es vagy újabb verziójának használatával létrehozott megbízható állapot-nyilvántartó alkalmazás. A .NET Core 2,0-ot célzó alkalmazások esetében az alkalmazást Service Fabric SDK 3,1-es vagy újabb verziójának használatával kell felépíteni.
* Azure Storage-fiók létrehozása az alkalmazások biztonsági másolatainak tárolásához.
* A konfigurációs hívások készítéséhez telepítse a Microsoft. ServiceFabric. PowerShell. http modult (előzetes verzió).

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
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

### <a name="using-azure-portal"></a>Az Azure Portal használata

`Include backup restore service`Jelölje be a jelölőnégyzetet `+ Show optional settings` a `Cluster Configuration` lapon.

![Biztonsági mentési visszaállítási szolgáltatás engedélyezése a portálon][1]


### <a name="using-azure-resource-manager-template"></a>Azure Resource Manager sablon használata
Először engedélyeznie kell a _biztonsági mentési és visszaállítási szolgáltatást_ a fürtben. Szerezze be a telepíteni kívánt fürt sablonját. Használhatja a [minta sablonokat](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) , vagy létrehozhat egy Resource Manager-sablont. Engedélyezze a _biztonsági mentési és visszaállítási szolgáltatást_ a következő lépésekkel:

1. Győződjön meg arról, hogy az az `apiversion` **`2018-02-01`** `Microsoft.ServiceFabric/clusters` erőforráshoz van beállítva, és ha nem, frissítse az alábbi kódrészletben látható módon:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Most engedélyezze a _biztonsági mentési és visszaállítási szolgáltatást_ úgy, hogy hozzáadja a következő `addonFeatures` szakaszt a `properties` szakaszban, ahogy az a következő kódrészletben látható: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Konfigurálja az X. 509 tanúsítványt a hitelesítő adatok titkosításához. Ez fontos annak biztosítása érdekében, hogy a tárolóhoz való kapcsolódáshoz megadott hitelesítő adatok titkosítva maradjanak, mielőtt megmaradnak. A titkosítási tanúsítvány konfigurálásához adja hozzá a következő `BackupRestoreService` szakaszt a `fabricSettings` szakaszhoz, ahogy az a következő kódrészletben látható: 

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

4. Miután frissítette a fürt sablonját az előző módosításokkal, alkalmazza őket, és hagyja, hogy a telepítés/frissítés befejeződjön. Ha elkészült, a _biztonsági mentési és visszaállítási szolgáltatás_ elindul a fürtben. A szolgáltatás URI `fabric:/System/BackupRestoreService` -ja és a szolgáltatás a Service Fabric Explorer rendszerszolgáltatás szakasza alatt található. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Rendszeres biztonsági mentés engedélyezése megbízható állapot-nyilvántartó szolgáltatáshoz és Reliable Actors
A megbízható állapot-nyilvántartó szolgáltatás és a Reliable Actors rendszeres biztonsági mentésének engedélyezéséhez hajtsa végre a lépéseket. Az alábbi lépések feltételezik
- A fürt az X. 509 biztonsági _mentési és visszaállítási szolgáltatással_ történő beállítás.
- Megbízható állapot-nyilvántartó szolgáltatás van telepítve a fürtön. Ebben a rövid útmutatóban az alkalmazás URI-ja, `fabric:/SampleApp` az alkalmazáshoz tartozó megbízható állapot-nyilvántartó szolgáltatás URI azonosítója pedig `fabric:/SampleApp/MyStatefulService` . A szolgáltatás egyetlen partícióval van üzembe helyezve, a partíció azonosítója pedig `974bd92a-b395-4631-8a7f-53bd4ae9cf22` .
- Az ügyféltanúsítvány és a rendszergazdai szerepkör telepítve van a _saját_ (_személyes_) tárolójában a _CurrentUser_ tanúsítványtárolójának a gépen, ahonnan az alábbi parancsfájlok meghívására kerül sor. Ez a példa a `1b7ebe2174649c45474a4819dafae956712c31d3` tanúsítvány ujjlenyomatát használja. További információ az Ügyféltanúsítványok használatáról: [Service Fabric ügyfelek szerepköralapú hozzáférés-vezérlése](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

Első lépésként létre kell hoznia egy biztonsági mentési szabályzatot, amely leírja a biztonsági mentés ütemezését, a biztonsági mentési adatok céljának tárolását, a szabályzat nevét, a teljes biztonsági mentési és adatmegőrzési szabályzatot a biztonsági mentési tár számára. 

A biztonsági mentési tár esetében használja a fent létrehozott Azure Storage-fiókot. A tároló a `backup-container` biztonsági másolatok tárolására van konfigurálva. A rendszer létrehoz egy ilyen nevű tárolót, ha az még nem létezik, a biztonsági mentés feltöltése során. Töltse `ConnectionString` fel az Azure Storage-fiókhoz tartozó érvényes, a Storage-fiók `account-name` nevét és a `account-key` Storage-fiók kulcsát tartalmazó karakterláncot.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft. ServiceFabric. PowerShell. http modul használatával

Futtassa az alábbi PowerShell-parancsmagokat az új biztonsági mentési szabályzat létrehozásához. Cserélje le a `account-name` nevet a Storage-fiók nevére és a `account-key` Storage-fiókja kulcsára.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Rest-hívás a PowerShell használatával

Hajtsa végre a következő PowerShell-szkriptet a szükséges REST API új szabályzat létrehozásához való meghívásához. Cserélje le a `account-name` nevet a Storage-fiók nevére és a `account-key` Storage-fiókja kulcsára.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
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
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

```

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer használata

1. A Service Fabric Explorerban navigáljon a biztonsági másolatok lapra, és válassza a műveletek > biztonsági mentési szabályzat létrehozása lehetőséget.

    ![Biztonsági mentési szabályzat létrehozása][6]

2. Adja meg az adatokat. Azure-fürtök esetén ki kell választani a AzureBlobStore.

    ![Biztonsági mentési szabályzat létrehozása Azure Blob Storage][7]

### <a name="enable-periodic-backup"></a>Rendszeres biztonsági mentés engedélyezése
Miután definiálta a biztonsági mentési szabályzatot az alkalmazás adatvédelmi követelményeinek teljesítéséhez, a biztonsági mentési szabályzatot társítani kell az alkalmazáshoz. A követelménytől függően a biztonsági mentési szabályzat egy alkalmazáshoz, szolgáltatáshoz vagy partícióhoz társítható.

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
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer használata
Győződjön meg arról, hogy a Service Fabric Explorer [speciális módja](service-fabric-visualizing-your-cluster.md#backup-and-restore) engedélyezve van 

1. Válasszon ki egy alkalmazást, és lépjen a művelet elemre. Kattintson az alkalmazás biztonsági másolatának engedélyezése/frissítése elemre.

    ![Alkalmazás biztonsági mentésének engedélyezése][3]

2. Végül válassza ki a kívánt szabályzatot, és kattintson a biztonsági mentés engedélyezése elemre.

    ![Házirend kiválasztása][4]


### <a name="verify-that-periodic-backups-are-working"></a>Az időszakos biztonsági másolatok működésének ellenőrzése

Miután engedélyezte a biztonsági mentést az alkalmazás szintjén, a megbízható állapot-nyilvántartó szolgáltatásokhoz és az alkalmazáshoz Reliable Actors tartozó összes partíció rendszeresen elindul a biztonsági mentési szabályzatnak megfelelően. 

![A Partition BackedUp állapotának eseménye][0]

### <a name="list-backups"></a>Biztonsági másolatok listázása

A megbízható állapot-nyilvántartó szolgáltatásokhoz tartozó összes partícióhoz társított biztonsági másolatok, valamint az alkalmazás Reliable Actors enumerálása a _GetBackups_ API használatával lehetséges. Egy alkalmazáshoz, szolgáltatáshoz vagy partícióhoz tartozó biztonsági másolatok enumerálása is lehetséges.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft. ServiceFabric. PowerShell. http modul használatával

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Rest-hívás a PowerShell használatával

Futtassa a következő PowerShell-szkriptet a HTTP API meghívásához, hogy enumerálja az alkalmazásban található összes partícióhoz létrehozott biztonsági másolatokat `SampleApp` .

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

A fenti Futtatás mintájának kimenete:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 

BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 

BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
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

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/creation-bp.png
