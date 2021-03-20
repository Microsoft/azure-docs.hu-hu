---
title: Biztonsági mentés visszaállítása az Azure-ban Service Fabric
description: Használja az Service Fabric rendszeres biztonsági mentési és visszaállítási funkcióját az alkalmazásadatok biztonsági másolatából származó adatok visszaállításához.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 8566d82ef0d91caff47ff17a9cb12fcdc8241884
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928027"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Biztonsági mentés visszaállítása az Azure-ban Service Fabric

Az Azure Service Fabric a megbízható állapot-nyilvántartó szolgáltatások és a Reliable Actors a kérések és válaszok tranzakciójának befejeződése után megváltoztathatatlan, mérvadó állapotot tarthatnak fenn. Előfordulhat, hogy egy állapot-nyilvántartó szolgáltatás egy katasztrófa miatt hosszabb ideig leáll, vagy elveszíti az információkat. Ha ez történik, a szolgáltatást a legújabb elfogadható biztonsági mentésből kell visszaállítani, hogy továbbra is működőképes legyen.

Beállíthat például egy szolgáltatást úgy, hogy biztonsági másolatot készít az adatairól a következő helyzetekben történő védelem érdekében:

- Vész- **helyreállítási eset**: egy teljes Service Fabric fürt végleges elvesztése.
- **Adatvesztés esetén**: a szolgáltatás-partíció replikáinak végleges elvesztése.
- **Adatvesztés esetén**: véletlen törlés vagy a szolgáltatás sérülése. A rendszergazda például hibásan törli a szolgáltatást.
- **Adatsérülés esetén**: a szolgáltatás hibái adatsérülést okozhatnak. Például előfordulhat, hogy az adatsérülés akkor fordul elő, amikor a szolgáltatási kód frissítése hibás adatot ír egy megbízható gyűjteménybe. Ebben az esetben előfordulhat, hogy a kódot és az adathalmazt is vissza kell állítania egy korábbi állapotba.

## <a name="prerequisites"></a>Előfeltételek

- A visszaállítás elindításához engedélyezni kell a _hiba-elemzési szolgáltatást (FAS)_ a fürtön.
- A Backup _Restore szolgáltatás (BRS)_ létrehozta a biztonsági mentést.
- A RESTORE utasítás csak partíción indítható el.
- A konfigurációs hívások készítéséhez telepítse a Microsoft. ServiceFabric. PowerShell. http modult (előzetes verzió).

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> Ha a PowerShellGet verziója kisebb, mint a 1.6.0, frissítenie kell a *-AllowPrerelease* jelző támogatásának hozzáadásához:
>
> `Install-Module -Name PowerShellGet -Force`

- Győződjön meg arról, hogy a fürt a paranccsal van csatlakoztatva, `Connect-SFCluster` mielőtt konfigurációs kérelmet hozna a Microsoft. ServiceFabric. PowerShell. http modul használatával.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>Aktivált visszaállítás

A visszaállítás a következő esetekben indítható el:

- Adatvisszaállítás a vész- _helyreállításhoz_.
- _Adatsérülés/adatvesztés_.

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Adatvisszaállítás vész-helyreállítás esetén

Ha egy teljes Service Fabric-fürt elvész, helyreállíthatja az adatokat a megbízható állapot-nyilvántartó szolgáltatás partíciói számára, és Reliable Actors. A kívánt biztonsági mentést kiválaszthatja a listából, ha [GetBackupAPI használ a biztonsági mentési tár részleteivel](/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation). A biztonsági mentés enumerálása lehet egy alkalmazás, szolgáltatás vagy partíció.

A következő példában feltételezzük, hogy az elveszett fürt ugyanaz a fürt, amely a [megbízható állapot-nyilvántartó szolgáltatás és a Reliable Actors rendszeres biztonsági mentésének engedélyezése](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)című részében szerepel. Ebben az esetben a a `SampleApp` biztonsági mentési szabályzattal van telepítve, és a biztonsági másolatok az Azure Storage-ba vannak konfigurálva.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft. ServiceFabric. PowerShell. http modul használatával

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest-hívás a PowerShell használatával

Hajtson végre egy PowerShell-szkriptet a REST API használatára az alkalmazásban található összes partícióhoz létrehozott biztonsági másolatok listájának visszaküldéséhez `SampleApp` . Az API-nak szüksége van a biztonsági mentési tár adataira az elérhető biztonsági másolatok listázásához.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
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
*
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
*
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

A visszaállítás elindításához válassza ki az egyik biztonsági mentést. A vész-helyreállítási aktuális biztonsági mentés például a következő biztonsági mentés lehet:

```
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
```

A Restore API esetében meg kell adnia a _biztonsági másolat azonosítója_ és a _BackupLocation_ adatait.

A [partíciós sémában](service-fabric-concepts-partitioning.md#get-started-with-partitioning)részletezett módon ki kell választania egy célként megadott partíciót is a másodlagos fürtben. A másodlagos fürt biztonsági mentését a rendszer visszaállítja a partíciós sémában megadott partícióra az eredeti elveszett fürtből.

Ha a partíció-azonosító a másodlagos fürtön van, akkor az `1c42c47f-439e-4e09-98b9-88b8f60800c6` eredeti fürtözött partíció-azonosítóhoz rendelhető hozzá, ha `974bd92a-b395-4631-8a7f-53bd4ae9cf22` összehasonlítja a magas kulcsot és az alacsony kulcsot a _tartományon kívüli particionáláshoz (UniformInt64Partition)_.

A _nevesített particionáláshoz_ a név értékét a rendszer összehasonlítja a másodlagos fürtben található cél partíció azonosításához.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft. ServiceFabric. PowerShell. http modul használatával

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest-hívás a PowerShell használatával

A visszaállítást a biztonsági mentési fürt partícióján a következő [visszaállítási API](/rest/api/servicefabric/sfclient-api-restorepartition)használatával kérheti le:

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

A visszaállítások állapotát nyomon követheti a TrackRestoreProgress használatával.

> [!NOTE]
> Ha a PowerShell használatával állítja vissza a partíciót, ha a backuplocation "$", a "~" utasítás használatával megmenekül
>

### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer használata
Service Fabric Explorerről is indíthat visszaállítást. Győződjön meg arról, hogy engedélyezve van-e a speciális mód a Service Fabric Explorer beállításokban.
1. Válassza ki a kívánt partíciókat, majd kattintson a műveletek elemre. 
2. Válassza a partíció visszaállítása lehetőséget, és adja meg az Azure-beli adatokat:

    ![Partíció visszaállításának triggere][2]

    vagy fájlmegosztás:

    ![Partíció-visszaállítási fájlmegosztás][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Adatvesztés _adatsérülése_ /  esetén

_Adatvesztés_ vagy _adatsérülés_ esetén a megbízható állapot-nyilvántartó szolgáltatáshoz és Reliable Actors partícióhoz tartozó biztonsági másolatok visszaállíthatók a kiválasztott biztonsági másolatokra.

A következő példa a [megbízható állapot-nyilvántartó szolgáltatás és a Reliable Actors rendszeres biztonsági mentésének engedélyezése](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Ebben a példában egy biztonsági mentési szabályzat van engedélyezve a partícióhoz, és a szolgáltatás biztonsági mentést készít a kívánt gyakorisággal az Azure Storage-ban.

Válasszon ki egy biztonsági másolatot a  [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups)kimenetéről. Ebben a forgatókönyvben a biztonsági mentés ugyanabból a fürtből jön létre, mint korábban.

A visszaállítás elindításához válasszon ki egy biztonsági másolatot a listából. Az _adatvesztés_ aktuális / _adatsérülése_ esetén válassza a következő biztonsági mentést:

```
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
```

A Restore API esetében adja meg a _biztonsági másolat azonosítója_ és a _BackupLocation_ adatait. A fürtön engedélyezve van a biztonsági mentés, így a Service Fabric _Backup Restore Service (BRS)_ a megfelelő tárolási helyet azonosítja a társított biztonsági mentési szabályzatból.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft. ServiceFabric. PowerShell. http modul használatával

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>Rest-hívás a PowerShell használatával

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

A visszaállítási folyamat nyomon követhető a TrackRestoreProgress használatával.

> [!NOTE]
> Ha a PowerShell használatával állítja vissza a partíciót, ha a backuplocation "$", a "~" utasítás használatával megmenekül
>

## <a name="track-restore-progress"></a>Visszaállítási folyamat nyomon követése

Egy megbízható állapot-nyilvántartó szolgáltatás vagy megbízható szereplő particionálása egyszerre csak egy visszaállítási kérelmet fogad el. Egy partíció csak az aktuális visszaállítási kérelem befejeződése után fogad el egy másik kérést. Egyszerre több visszaállítási kérelem is aktiválható különböző partíciókon.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft. ServiceFabric. PowerShell. http modul használatával

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>Rest-hívás a PowerShell használatával

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

A visszaállítási kérelem a következő sorrendben halad:

1. **Elfogadva**: egy _elfogadott_ visszaállítási állapot azt jelzi, hogy a kért partíció helyes kérési paraméterekkel lett aktiválva.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **Inprogress**: egy _Inprogress_ visszaállítási állapot azt jelzi, hogy a partícióban található visszaállítás a kérelemben említett biztonsági másolattal történik. A partíció a _dataloss_ állapotot jelenti.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Sikeres**, **sikertelen** vagy **időtúllépés**: a kért visszaállítás a következő állapotok bármelyikében elvégezhető. Az egyes állapotok a következő jelentőségű és válasz részletekkel bírnak:
    - **Sikeres**: a _sikeres_ visszaállítási állapot visszanyert partíciós állapotot jelez. A partíció a _RestoredEpoch_ és a _RestoredLSN_ állapotot az UTC időponttal együtt jelenti.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Hiba**: _sikertelen_ visszaállítási állapot a visszaállítási kérelem hibáját jelzi. A hiba oka jelentett.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Időtúllépés**: az _időkorlát_ -visszaállítási állapot azt jelzi, hogy a kérelem időtúllépést jelez. Hozzon létre egy új visszaállítási kérést nagyobb [RestoreTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). Az alapértelmezett időkorlát 10 perc. Győződjön meg arról, hogy a partíció nem adatvesztési állapotban van, mielőtt újra kéri a visszaállítást.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatikus visszaállítás

A Service Fabric-fürtben megbízható állapot-nyilvántartó szolgáltatás és Reliable Actors partíciók állíthatók be az _automatikus visszaállításhoz_. A biztonsági mentési szabályzat értéke `AutoRestore` _true (igaz_). Az _automatikus visszaállítás_ engedélyezése automatikusan visszaállítja az adatok biztonsági mentését a legutóbbi partícióról az adatvesztés jelentésekor. További információkért lásd:

- [Automatikus visszaállítás engedélyezése a biztonsági mentési házirendben](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [RestorePartition API-referenciák](/rest/api/servicefabric/sfclient-api-restorepartition)
- [GetPartitionRestoreProgress API-referenciák](/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>Következő lépések
- [Rendszeres biztonsági mentési konfiguráció ismertetése](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Biztonsági mentés visszaállítása REST API referenciája](/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png
