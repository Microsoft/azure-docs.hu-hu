---
title: Igény szerinti biztonsági mentés az Azure Service Fabric
description: Használja a Service Fabric biztonsági mentési és visszaállítási funkcióját az alkalmazásadatok igény szerinti biztonsági mentéséhez.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d7986c8cd8d0714215c7b4dc57170be346e627ed
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928034"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Igény szerinti biztonsági mentés az Azure Service Fabric

A megbízható állapot-nyilvántartó szolgáltatások és a Reliable Actors adatok biztonsági mentésével felgyorsíthatja a katasztrófák vagy az adatvesztési helyzetek megoldását.

Az Azure Service Fabric rendelkezik az [adatfeldolgozás rendszeres biztonsági mentéséhez](service-fabric-backuprestoreservice-quickstart-azurecluster.md) szükséges funkciókkal és az adatbiztonsági mentéssel. Az igény szerinti biztonsági mentés azért hasznos, mert a  / mögöttes szolgáltatásban vagy annak környezetében tervezett változások miatt védelmet biztosít az adatvesztési _adatsérülés_ ellen.

Az igény szerinti biztonsági mentési funkciók hasznosak lehetnek a szolgáltatások állapotának rögzítéséhez, mielőtt manuálisan elindítja a szolgáltatás-vagy szolgáltatási környezet műveletét. Ha például a szolgáltatás verziófrissítése vagy lefokozása során módosítja a szolgáltatás bináris fájljait. Ebben az esetben az igény szerinti biztonsági mentés segít megvédeni az adatsérülést az alkalmazás kódjának hibáival.
## <a name="prerequisites"></a>Előfeltételek

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


## <a name="triggering-on-demand-backup"></a>Igény szerinti biztonsági mentés indítása

Az igény szerinti biztonsági mentés a biztonságimásolat-fájlok feltöltéséhez szükséges tárolási adatokat igényli. Az igény szerinti biztonsági mentés helyét az időszakos biztonsági mentési házirendben vagy egy igény szerinti biztonsági mentési kérelemben adhatja meg.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Az igény szerinti biztonsági mentés egy rendszeres biztonsági mentési szabályzat által meghatározott tárterületre

Az időszakos biztonsági mentési szabályzat úgy is beállítható, hogy egy megbízható állapot-nyilvántartó szolgáltatás vagy megbízható szereplő egy partícióját használja a tárterület további, igény szerinti biztonsági mentéséhez.

A következő eset a [megbízható állapot-nyilvántartó szolgáltatás és a Reliable Actors rendszeres biztonsági mentésének engedélyezése](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)című forgatókönyv folytatása. Ebben az esetben a biztonsági mentési szabályzatot egy partíció használatára engedélyezi, és a biztonsági mentés az Azure Storage szolgáltatásban beállított gyakorisággal történik.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft. ServiceFabric. PowerShell. http modul használatával

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>Rest-hívás a PowerShell használatával

A [BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) API használatával beállíthatja az indítást az igény szerinti biztonsági mentéshez a partíció-azonosítóhoz `974bd92a-b395-4631-8a7f-53bd4ae9cf22` .

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

A [GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API használatával engedélyezheti az [igény szerinti biztonsági mentési folyamat](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)nyomon követését.

### <a name="on-demand-backup-to-specified-storage"></a>Igény szerinti biztonsági mentés a megadott tárolóra

Az igény szerinti biztonsági mentést egy megbízható állapot-nyilvántartó szolgáltatás vagy megbízható szereplő számára is kérheti. Adja meg a tárolási adatokat az igény szerinti biztonsági mentési kérelem részeként.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft. ServiceFabric. PowerShell. http modul használatával

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>Rest-hívás a PowerShell használatával

A [BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition) API használatával beállíthatja az indítást az igény szerinti biztonsági mentéshez a partíció-azonosítóhoz `974bd92a-b395-4631-8a7f-53bd4ae9cf22` . A következő Azure Storage-információk belefoglalása:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

A [GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API-val beállíthatja az [igény szerinti biztonsági mentési folyamat](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)nyomon követését.

### <a name="using-service-fabric-explorer"></a>Service Fabric Explorer használata
Győződjön meg arról, hogy engedélyezve van-e a speciális mód a Service Fabric Explorer beállításokban.
1. Válassza ki a kívánt partíciókat, majd kattintson a műveletek elemre. 
2. Válassza a partíciós biztonsági mentés indítása lehetőséget, és adja meg az Azure-beli adatokat:

    ![Partíció biztonsági mentésének indítása][0]

    vagy fájlmegosztás:

    ![Partíció biztonsági mentésének fájlmegosztás][1]

## <a name="tracking-on-demand-backup-progress"></a>Igény szerinti biztonsági mentési folyamat nyomon követése

Egy megbízható állapot-nyilvántartó szolgáltatás vagy megbízható szereplő particionálása egyszerre csak egy igény szerinti biztonsági mentési kérelmet fogad el. Egy másik kérelem csak az aktuális, igény szerinti biztonsági mentési kérelem befejeződése után fogadható el.

A különböző partíciók igény szerinti biztonsági mentési kérelmeket is indíthatnak egyszerre.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell a Microsoft. ServiceFabric. PowerShell. http modul használatával

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>Rest-hívás a PowerShell használatával

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Az igény szerinti biztonsági mentési kérelmek a következő állapotokban lehetnek:

- **Elfogadva**: a biztonsági mentés elindult a partíción, és folyamatban van.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Sikeres**, **sikertelen** vagy **időtúllépés**: a kért igény szerinti biztonsági mentés a következő állapotok bármelyikében elvégezhető:
  - **Sikeres**: a _sikeres biztonsági mentési állapot_ azt jelzi, hogy a partíció állapota sikeresen mentve. A válasz _BackupEpoch_ és _BackupLSN_ biztosít a partícióhoz, valamint az UTC időpontját.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Hiba**: a _hiba_ biztonsági mentési állapota azt jelzi, hogy hiba történt a partíció állapotának biztonsági mentése során. A hiba oka a válaszban szerepel.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Időtúllépés**: egy _időtúllépési_ biztonsági mentési állapot azt jelzi, hogy a partíciós állapot biztonsági mentése nem hozható létre egy adott időtartamon belül. Az alapértelmezett időtúllépési érték 10 perc. Új, igény szerinti biztonsági mentési kérelem kezdeményezése nagyobb [BackupTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) ebben a forgatókönyvben.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Következő lépések

- [Az időszakos biztonsági mentési konfiguráció ismertetése](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [BackupRestore REST API referenciája](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png
