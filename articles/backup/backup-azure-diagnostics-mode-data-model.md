---
title: Azure Monitor naplók adatmodellje
description: Ebből a cikkből megtudhatja, hogyan Azure Monitor Log Analytics adatmodell adatait Azure Backup adatokra vonatkozóan.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 004c5a6c0c2c4dcfcf13134bd5a5143ba647048f
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500988"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics adatmodell Azure Backup-adattípushoz

A Log Analytics adatmodell használatával egyéni riasztásokat hozhat létre a Log Analyticsból.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> * Ez az adatmodell a diagnosztikai események Log Analytics (LA) való küldésének Azure Diagnostics módjára hivatkozik. Az új erőforrás-specifikus mód adatmodelljének megismeréséhez tekintse meg a következő cikket: [Azure Backup diagnosztikai események Adatmodellje](./backup-azure-reports-data-model.md)
> * Egyéni jelentéskészítési nézetek létrehozásához ajánlott a [rendszerfunkciókat Azure monitor naplókon](backup-reports-system-functions.md) használni az alább felsorolt nyers táblák használata helyett.

## <a name="using-azure-backup-data-model"></a>Azure Backup adatmodell használata

A következő mezőket használhatja az adatmodell részeként vizualizációk, egyéni lekérdezések és irányítópultok létrehozásához a követelmények szerint.

### <a name="alert"></a>Riasztás

Ez a táblázat a riasztással kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| AlertUniqueId_s |Szöveg |A generált riasztás egyedi azonosítója |
| AlertType_s |Szöveg |A riasztás típusa, például biztonsági mentés |
| AlertStatus_s |Szöveg |A riasztás állapota, például aktív |
| AlertOccurrenceDateTime_s |Dátum/idő |A riasztás létrehozásának dátuma és időpontja |
| AlertSeverity_s |Szöveg |A riasztás súlyossága, például kritikus |
|AlertTimeToResolveInMinutes_s    | Szám        |A riasztás feloldásához szükséges idő. Üres az aktív riasztásokhoz.         |
|AlertConsolidationStatus_s   |Szöveg         |Annak megállapítása, hogy a riasztás konszolidált riasztás-e, vagy sem         |
|CountOfAlertsConsolidated_s     |Szám         |Konszolidált riasztások száma összevont riasztás esetén          |
|AlertRaisedOn_s     |Szöveg         |A riasztást kiváltó entitás típusa         |
|AlertCode_s     |Szöveg         |Riasztás típusának egyedi azonosítására szolgáló kód         |
|RecommendedAction_s   |Szöveg         |A riasztás feloldásához javasolt művelet         |
| EventName_s |Szöveg |Az esemény neve. Mindig AzureBackupCentralReport |
| BackupItemUniqueId_s |Szöveg |A riasztáshoz társított biztonsági mentési tétel egyedi azonosítója |
| SchemaVersion_s |Szöveg |A séma jelenlegi verziója, például **v2** |
| State_s |Szöveg |A riasztási objektum aktuális állapota, például aktív, törölve |
| BackupManagementType_s |Szöveg |Szolgáltatói típus a biztonsági mentés végrehajtásához, például IaaSVM, fájlmappa, amelyhez ez a riasztás tartozik |
| OperationName |Szöveg |Az aktuális művelet neve, például riasztás |
| Kategória |Szöveg |A Azure Monitor naplókba leküldett diagnosztikai adatkategória. Mindig AzureBackupReport |
| Erőforrás |Szöveg |Ez az az erőforrás, amelybe begyűjti az adatokat, Recovery Services tár nevét jeleníti meg |
| ProtectedContainerUniqueId_s |Szöveg |A riasztáshoz társított védett kiszolgáló egyedi azonosítója (ProtectedServerUniqueId_s v1-ben)|
| VaultUniqueId_s |Szöveg |A riasztáshoz társított védett tároló egyedi azonosítója |
| SourceSystem |Szöveg |Az aktuális adatforrásrendszer – Azure |
| ResourceId |Szöveg |Annak az erőforrásnak az egyedi azonosítója, amelyről az adatokat gyűjti. Például egy Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Szöveg |Az erőforrás előfizetés-azonosítója (pl. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceGroup |Szöveg |Erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató, amelybe az adatok gyűjtése történik. Például: Microsoft. Recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Az az erőforrástípus, amelybe az adatok gyűjtése történik. Például: tárolók |

### <a name="backupitem"></a>BackupItem

Ez a táblázat a biztonsági másolati elemek kapcsolódó mezőinek részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Az esemény neve. Mindig AzureBackupCentralReport |  
| BackupItemUniqueId_s |Szöveg |A biztonsági másolati tétel egyedi azonosítója |
| BackupItemId_s |Szöveg |A biztonsági másolati elemek azonosítója (ez a mező csak a v1-séma esetében) |
| BackupItemName_s |Szöveg |A biztonságimásolat-tétel neve |
| BackupItemFriendlyName_s |Szöveg |A biztonságimásolat-tétel rövid neve |
| BackupItemType_s |Szöveg |A biztonsági mentési elem típusa, például virtuális gép, fájlmappa |
| BackupItemProtectionState_s |Szöveg |A biztonsági mentési elem védelmi állapota |
| BackupItemAppVersion_s |Szöveg |A biztonsági mentési tétel alkalmazásának verziója |
| ProtectionState_s |Szöveg |A biztonsági másolati elem jelenlegi védelmi állapota, például védett, ProtectionStopped |
| ProtectionGroupName_s |Szöveg | A védelmi csoport neve, amelyben a biztonsági másolati elem védett, az SC DPM és a MABS esetében, ha van ilyen.|
| SecondaryBackupProtectionState_s |Szöveg |Azt jelzi, hogy engedélyezve van-e a másodlagos védelem a biztonsági mentési elemmel kapcsolatban|
| SchemaVersion_s |Szöveg |A séma verziója, például **v2** |
| State_s |Szöveg |A biztonsági mentési elem objektumának állapota, például aktív, törölve |
| BackupManagementType_s |Szöveg |Szolgáltatói típus a biztonsági mentés végrehajtásához, például IaaSVM, fájlmappa, amelyhez ez a biztonsági mentési elem tartozik |
| OperationName |Szöveg |A művelet neve, például BackupItem |
| Kategória |Szöveg |A Azure Monitor naplókba leküldett diagnosztikai adatkategória. Mindig AzureBackupReport |
| Erőforrás |Szöveg |Az az erőforrás, amelybe az adatokat gyűjti, például Recovery Services tár neve |
| SourceSystem |Szöveg |Az aktuális adatforrásrendszer – Azure |
| ResourceId |Szöveg |Az összegyűjtött adatok erőforrás-azonosítója, például Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Szöveg |Az erőforrás előfizetés-azonosítója (pl.:. Recovery Services-tároló) a gyűjtött adatokhoz |
| ResourceGroup |Szöveg |Az erőforrás erőforráscsoport (pl.: Recovery Services-tároló) a gyűjtött adatokhoz |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató az összegyűjtött adatokhoz, például: Microsoft. Recoveryservices szolgáltatónál |
| ResourceType |Szöveg |A gyűjtött adatokhoz tartozó erőforrás típusa, például tárolók |

### <a name="backupitemassociation"></a>BackupItemAssociation

Ez a táblázat a különböző entitásokkal rendelkező biztonsági mentési elemek társításával kapcsolatos részleteket tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező az esemény nevét jelöli. Mindig AzureBackupCentralReport |  
| BackupItemUniqueId_s |Szöveg |A biztonsági másolati tétel egyedi azonosítója |
| SchemaVersion_s |Szöveg |Ez a mező a séma aktuális verzióját jelöli. **V2** |
| State_s |Szöveg |A biztonsági mentési elem társítási objektumának aktuális állapota, például aktív, törölve |
| BackupManagementType_s |Szöveg |Szolgáltató típusa a biztonsági mentési feladatot végző kiszolgáló számára, például IaaSVM, fájlmappa |
| BackupItemSourceSize_s |Szöveg | A biztonsági mentési tétel előtér-mérete |
| BackupManagementServerUniqueId_s |Szöveg | A biztonságimásolat-felügyeleti kiszolgáló egyedi azonosítására szolgáló mező, ha van ilyen, a biztonsági mentési elem védelme |
| Kategória |Szöveg |Ez a mező a Log Analyticsba leküldett diagnosztikai adatkategóriákat jelöli. AzureBackupReport |
| OperationName |Szöveg |Ez a mező az aktuális művelet nevét jelöli – BackupItemAssociation |
| Erőforrás |Szöveg |Ez az az erőforrás, amelybe begyűjti az adatokat, Recovery Services tár nevét jeleníti meg |
| ProtectedContainerUniqueId_s |Szöveg |A biztonsági mentési elemmel társított védett kiszolgáló egyedi azonosítója (ProtectedServerUniqueId_s v1) |
| VaultUniqueId_s |Szöveg |A biztonsági mentési tételt tartalmazó tároló egyedi azonosítója |
| SourceSystem |Szöveg |Az aktuális adatforrásrendszer – Azure |
| ResourceId |Szöveg |Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Szöveg |Az erőforrás előfizetés-azonosítója (pl.:. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceGroup |Szöveg |Az erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató az összegyűjtött adatokhoz, például: Microsoft. Recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Az adatok gyűjtésére szolgáló erőforrás típusa, például tárolók |

### <a name="backupmanagementserver"></a>BackupManagementServer

Ez a táblázat a különböző entitásokkal rendelkező biztonsági mentési elemek társításával kapcsolatos részleteket tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
|BackupManagementServerName_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgáló neve        |
|AzureBackupAgentVersion_s     |Szöveg         |A Azure Backup ügynök verziója a biztonságimásolat-felügyeleti kiszolgálón          |
|BackupManagementServerVersion_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgáló verziója|
|BackupManagementServerOSVersion_s    |Szöveg            |A biztonságimásolat-felügyeleti kiszolgáló operációs rendszerének verziója|
|BackupManagementServerType_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgáló típusa, mint MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgáló egyedi azonosítására szolgáló mező       |
| SourceSystem |Szöveg |Az aktuális adatforrásrendszer – Azure |
| ResourceId |Szöveg |Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Szöveg |Az erőforrás előfizetés-azonosítója (pl.:. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceGroup |Szöveg |Az erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató az összegyűjtött adatokhoz, például: Microsoft. Recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Az adatok gyűjtésére szolgáló erőforrás típusa, például tárolók |

### <a name="job"></a>Feladat

Ez a táblázat a feladatokkal kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Az esemény neve. Mindig AzureBackupCentralReport |
| BackupItemUniqueId_s |Szöveg |A biztonsági másolati tétel egyedi azonosítója |
| SchemaVersion_s |Szöveg |A séma verziója, például **v2** |
| State_s |Szöveg |A feladatütemezés aktuális állapota, például aktív, törölve |
| BackupManagementType_s |Szöveg |Szolgáltató típusa a biztonsági mentési feladatot végző kiszolgáló számára, például IaaSVM, fájlmappa |
| OperationName |Szöveg |Ez a mező az aktuális művelet nevét jelöli – feladat |
| Kategória |Szöveg |Ez a mező a Azure Monitor naplókba leküldett diagnosztikai adatkategóriákat jelöli. AzureBackupReport |
| Erőforrás |Szöveg |Ez az az erőforrás, amelybe begyűjti az adatokat, Recovery Services tár nevét jeleníti meg |
| ProtectedServerUniqueId_s |Szöveg |A feladathoz társított védett kiszolgáló egyedi azonosítója |
| ProtectedContainerUniqueId_s |Szöveg | A feladatot futtató védett tároló azonosítására szolgáló egyedi azonosító |
| VaultUniqueId_s |Szöveg |A védett tároló egyedi azonosítója |
| JobOperation_s |Szöveg |A művelet, amelynek a feladata fut, például biztonsági mentés, visszaállítás, biztonsági mentés konfigurálása |
| JobStatus_s |Szöveg |A Befejezett feladatok állapota, például befejezett, sikertelen |
| JobFailureCode_s |Szöveg |Hiba történt a hibakód karakterlánca miatt, mert a művelet sikertelen volt |
| JobStartDateTime_s |Dátum/idő |A feladatok futtatásának dátuma és időpontja |
| BackupStorageDestination_s |Szöveg |A biztonsági mentési tár célja, például felhő, lemez  |
| AdHocOrScheduledJob_s |Szöveg | Mező, amely megadja, hogy a feladattípus ad hoc vagy ütemezett |
| JobDurationInSecs_s | Szám |Feladatok teljes időtartama másodpercben |
| DataTransferredInMB_s | Szám |A feladatokhoz tartozó MB-ban továbbított adatok|
| JobUniqueId_g |Szöveg |A feladatot azonosító egyedi azonosító |
| RecoveryJobDestination_s |Szöveg | A helyreállítási feladatok célja, amelyben az adatok helyreállítása történik |
| RecoveryJobRPDateTime_s |DateTime | A helyreállított helyreállítási pont létrehozásának dátuma és időpontja |
| RecoveryJobRPLocation_s |Szöveg | A helyreállított helyreállítási pont tárolási helye|
| SourceSystem |Szöveg |Az aktuális adatforrásrendszer – Azure |
| ResourceId |Szöveg |Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója|
| SubscriptionId |Szöveg |Az erőforrás előfizetés-azonosítója (pl. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceGroup |Szöveg |Erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató, amelybe az adatok gyűjtése történik. Például: Microsoft. Recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Az az erőforrástípus, amelybe az adatok gyűjtése történik. Például: tárolók |

### <a name="policy"></a>Szabályzat

Ez a táblázat a házirendekkel kapcsolatos mezőkről tartalmaz információkat.

| Mező | Adattípus | Alkalmazható verziók | Leírás |
| --- | --- | --- | --- |
| EventName_s |Szöveg ||Ez a mező az esemény nevét jelöli. Mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg ||Ez a mező a séma aktuális verzióját jelöli. **V2** |
| State_s |Szöveg ||A házirend-objektum aktuális állapota, például aktív, törölve |
| BackupManagementType_s |Szöveg ||Szolgáltató típusa a biztonsági mentési feladatot végző kiszolgáló számára, például IaaSVM, fájlmappa |
| OperationName |Szöveg ||Ez a mező az aktuális műveleti szabályzat nevét jelöli. |
| Kategória |Szöveg ||Ez a mező a Azure Monitor naplókba leküldett diagnosztikai adatkategóriákat jelöli. AzureBackupReport |
| Erőforrás |Szöveg ||Ez az az erőforrás, amelybe begyűjti az adatokat, Recovery Services tár nevét jeleníti meg |
| PolicyUniqueId_g |Szöveg ||A szabályzat azonosítására szolgáló egyedi azonosító |
| PolicyName_s |Szöveg ||A megadott házirend neve |
| BackupFrequency_s |Szöveg ||A biztonsági másolatok futtatásának gyakorisága (például naponta, hetente) |
| BackupTimes_s |Szöveg ||A biztonsági másolatok ütemezésének dátuma és időpontja |
| BackupDaysOfTheWeek_s |Szöveg ||A hét azon napjai, amikor a biztonsági mentések ütemezése megtörtént |
| RetentionDuration_s |Egész szám ||Konfigurált biztonsági másolatok megőrzési időtartama |
| DailyRetentionDuration_s |Egész szám ||A beállított biztonsági másolatok teljes megőrzési időtartama (nap) |
| DailyRetentionTimes_s |Szöveg ||A napi megőrzés konfigurálásának dátuma és időpontja |
| WeeklyRetentionDuration_s |Tizedes tört ||Heti megőrzési időtartam összesen hetekben a konfigurált biztonsági másolatok esetében |
| WeeklyRetentionTimes_s |Szöveg ||A heti megőrzés konfigurálásának dátuma és időpontja |
| WeeklyRetentionDaysOfTheWeek_s |Szöveg ||A heti megőrzéshez kiválasztott hét napjai |
| MonthlyRetentionDuration_s |Tizedes tört ||A beállított biztonsági másolatok teljes megőrzési időtartama (hónap) |
| MonthlyRetentionTimes_s |Szöveg ||A havi megőrzés konfigurálásának dátuma és időpontja |
| MonthlyRetentionFormat_s |Szöveg ||A havi megőrzés konfigurációjának típusa, például naponta, hetente, hetente |
| MonthlyRetentionDaysOfTheWeek_s |Szöveg ||A havi megőrzésre kiválasztott hét napjai |
| MonthlyRetentionWeeksOfTheMonth_s |Szöveg ||A hónap hete, amikor a havi megőrzés konfigurálva van, például: első, utolsó |
| YearlyRetentionDuration_s |Tizedes tört ||Teljes megőrzési időtartam években a konfigurált biztonsági másolatok esetében |
| YearlyRetentionTimes_s |Szöveg ||Az éves adatmegőrzés konfigurálásának dátuma és időpontja |
| YearlyRetentionMonthsOfTheYear_s |Szöveg ||Az év hónapos megőrzésre kiválasztott hónapja |
| YearlyRetentionFormat_s |Szöveg ||Az évenkénti megőrzéshez szükséges konfiguráció típusa, például naponta, hetente, hetente | |
| YearlyRetentionDaysOfTheMonth_s |Szöveg ||Az éves megőrzéshez kiválasztott hónap dátuma |
| SynchronisationFrequencyPerDay_s |Egész szám |v2|Napok száma egy nap során a rendszer az SC DPM és a MABS esetében szinkronizálja a fájlok biztonsági mentését |
| DiffBackupFormat_s |Szöveg |v2|Az SQL-alapú különbözeti biztonsági másolatok formátuma az Azure virtuális gép biztonsági mentésében |
| DiffBackupTime_s |Idő |v2|Az SQL Azure-beli virtuális gépek biztonsági mentésének ideje|
| DiffBackupRetentionDuration_s |Tizedes tört |v2|Az SQL Azure-beli virtuális gépek biztonsági mentésének megőrzési időtartama|
| LogBackupFrequency_s |Tizedes tört |v2|SQL-naplók biztonsági másolatainak gyakorisága|
| LogBackupRetentionDuration_s |Tizedes tört |v2|Az SQL Azure-beli virtuális gép biztonsági mentésében tárolt biztonsági másolatok megőrzési időtartama|
| DiffBackupDaysofTheWeek_s |Szöveg |v2|A hét napjai az SQL-különbözeti biztonsági mentésekhez az Azure-beli virtuális gép biztonsági mentésében|
| SourceSystem |Szöveg ||Az aktuális adatforrásrendszer – Azure |
| ResourceId |Szöveg ||Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Szöveg ||Az erőforrás előfizetés-azonosítója (pl. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceGroup |Szöveg ||Erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Szöveg ||Erőforrás-szolgáltató, amelybe az adatok gyűjtése történik. Például: Microsoft. Recoveryservices szolgáltatónál |
| ResourceType |Szöveg ||Az az erőforrástípus, amelybe az adatok gyűjtése történik. Például: tárolók |

### <a name="policyassociation"></a>PolicyAssociation

Ez a táblázat a különböző entitásokkal rendelkező házirend-társítások részleteit tartalmazza.

| Mező | Adattípus | Alkalmazható verziók | Leírás |
| --- | --- | --- | --- |
| EventName_s |Szöveg ||Ez a mező az esemény nevét jelöli. Mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg ||Ez a mező a séma aktuális verzióját jelöli. **V2** |
| State_s |Szöveg ||A házirend-objektum aktuális állapota, például aktív, törölve |
| BackupManagementType_s |Szöveg ||Szolgáltató típusa a biztonsági mentési feladatot végző kiszolgáló számára, például IaaSVM, fájlmappa |
| OperationName |Szöveg ||Ez a mező az aktuális művelet nevét jelöli – PolicyAssociation |
| Kategória |Szöveg ||Ez a mező a Azure Monitor naplókba leküldett diagnosztikai adatkategóriákat jelöli. AzureBackupReport |
| Erőforrás |Szöveg ||Ez az az erőforrás, amelybe begyűjti az adatokat, Recovery Services tár nevét jeleníti meg |
| PolicyUniqueId_g |Szöveg ||A szabályzat azonosítására szolgáló egyedi azonosító |
| VaultUniqueId_s |Szöveg ||Azon tár egyedi azonosítója, amelyhez ez a szabályzat tartozik |
| BackupManagementServerUniqueId_s |Szöveg |v2 |A biztonságimásolat-felügyeleti kiszolgáló egyedi azonosítására szolgáló mező, ha van ilyen, a biztonsági mentési elem védelme        |
| SourceSystem |Szöveg ||Az aktuális adatforrásrendszer – Azure |
| ResourceId |Szöveg ||Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Szöveg ||Az erőforrás előfizetés-azonosítója (pl. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceGroup |Szöveg ||Erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Szöveg ||Erőforrás-szolgáltató, amelybe az adatok gyűjtése történik. Például: Microsoft. Recoveryservices szolgáltatónál |
| ResourceType |Szöveg ||Az az erőforrástípus, amelybe az adatok gyűjtése történik. Például: tárolók |

### <a name="protected-container"></a>Védett tároló

Ez a táblázat a védett tárolók alapszintű mezőit tartalmazza. (ProtectedServer volt a v1-ben)

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Szöveg | Védett tároló egyedi azonosítására szolgáló mező |
| ProtectedContainerOSType_s |Szöveg |A védett tároló operációs rendszerének típusa |
| ProtectedContainerOSVersion_s |Szöveg |A védett tároló operációs rendszerének verziója |
| AgentVersion_s |Szöveg |Az ügynök biztonsági mentési vagy védelmi ügynökének verziószáma (SC DPM és MABS esetén) |
| BackupManagementType_s |Szöveg |A biztonsági mentést végző szolgáltató típusa. Például: IaaSVM, fájlmappa |
| EntityState_s |Szöveg |A védett kiszolgáló objektum jelenlegi állapota. Például: aktív, törölve |
| ProtectedContainerFriendlyName_s |Szöveg |A védett kiszolgáló rövid neve |
| ProtectedContainerName_s |Szöveg |A védett tároló neve |
| ProtectedContainerWorkloadType_s |Szöveg |A védett tároló biztonsági mentésének típusa. Például: IaaSVMContainer |
| ProtectedContainerLocation_s |Szöveg |A védett tároló a helyszínen vagy az Azure-ban található-e |
| ProtectedContainerType_s |Szöveg |Azt jelzi, hogy a védett tároló kiszolgáló vagy tároló-e |
| ProtectedContainerProtectionState_s "  |Szöveg |A védett tároló védelmi állapota |

### <a name="storage"></a>Tárolás

Ez a táblázat a Storage szolgáltatással kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| CloudStorageInBytes_s |Tizedes tört |Biztonsági másolatok által használt Felhőbeli biztonsági mentési tár, amely a legutóbbi érték alapján lett kiszámítva (ez a mező csak a v1-séma esetében)|
| ProtectedInstances_s |Tizedes tört |A előtér-tároló kiszámításához használt védett példányok száma a számlázásban, a legutóbbi érték alapján számítva |
| EventName_s |Szöveg |Ez a mező az esemény nevét jelöli. Mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ez a mező a séma aktuális verzióját jelöli. **V2** |
| State_s |Szöveg |A tárolási objektum aktuális állapota, például aktív, törölve |
| BackupManagementType_s |Szöveg |Szolgáltató típusa a biztonsági mentési feladatot végző kiszolgáló számára, például IaaSVM, fájlmappa |
| OperationName |Szöveg |Ez a mező a jelenlegi művelet – tár nevét jelöli. |
| Kategória |Szöveg |Ez a mező a Azure Monitor naplókba leküldett diagnosztikai adatkategóriákat jelöli. AzureBackupReport |
| Erőforrás |Szöveg |Ez az az erőforrás, amelybe begyűjti az adatokat, Recovery Services tár nevét jeleníti meg |
| ProtectedServerUniqueId_s |Szöveg |Azon védett kiszolgáló egyedi azonosítója, amelyhez a tárterületet ki kell számítani |
| VaultUniqueId_s |Szöveg |A tár tárolójának egyedi azonosítója kiszámítva |
| SourceSystem |Szöveg |Az aktuális adatforrásrendszer – Azure |
| ResourceId |Szöveg |Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Szöveg |Az erőforrás előfizetés-azonosítója (pl. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceGroup |Szöveg |Erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató, amelybe az adatok gyűjtése történik. Például: Microsoft. Recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Az az erőforrástípus, amelybe az adatok gyűjtése történik. Például: tárolók |
| StorageUniqueId_s |Szöveg |A tárolási entitás azonosítására használt egyedi azonosító |
| StorageType_s |Szöveg |Tárolási típus, például felhő, kötet, lemez |
| StorageName_s |Szöveg |A tárolási entitás neve, például E:\ |
| StorageTotalSizeInGBs_s |Szöveg |A Storage-entitás által felhasznált tárterület teljes mérete GB-ban|

### <a name="storageassociation"></a>StorageAssociation

Ez a táblázat a tárterületet más entitásokhoz összekötő alapszintű tárterülettel kapcsolatos mezőket tartalmaz.

| Mező | Adattípus | Leírás |
| --- | --- |  --- |
| StorageUniqueId_s |Szöveg |A tárolási entitás azonosítására használt egyedi azonosító |
| SchemaVersion_s |Szöveg |Ez a mező a séma aktuális verzióját jelöli. **V2** |
| BackupItemUniqueId_s |Szöveg |A tárolási entitáshoz kapcsolódó biztonsági mentési elem azonosítására használt egyedi azonosító |
| BackupManagementServerUniqueId_s |Szöveg |A tárolási entitáshoz kapcsolódó biztonságimásolat-felügyeleti kiszolgáló azonosítására használt egyedi azonosító|
| VaultUniqueId_s |Szöveg |A tárolási entitáshoz kapcsolódó tár azonosítására használt egyedi azonosító|
| StorageConsumedInMBs_s |Szám|A megfelelő tároló biztonsági mentési eleme által felhasznált tárterület mérete |
| StorageAllocatedInMBs_s |Szám |A megfelelő biztonsági mentési tétel által lefoglalt tárterület a lemez típusú megfelelő tárolóban|

### <a name="vault"></a>Tároló

Ez a táblázat a tárolóval kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező az esemény nevét jelöli. Mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ez a mező a séma aktuális verzióját jelöli. **V2** |
| State_s |Szöveg |A tár objektum aktuális állapota, például aktív, törölve |
| OperationName |Szöveg |Ez a mező az aktuális művelet (Vault) nevét jelöli. |
| Kategória |Szöveg |Ez a mező a Azure Monitor naplókba leküldett diagnosztikai adatkategóriákat jelöli. AzureBackupReport |
| Erőforrás |Szöveg |Ez az az erőforrás, amelybe begyűjti az adatokat, Recovery Services tár nevét jeleníti meg |
| VaultUniqueId_s |Szöveg |A tár egyedi azonosítója |
| VaultName_s |Szöveg |A tároló neve |
| AzureDataCenter_s |Szöveg |Az adatközpont, ahol a tár található |
| StorageReplicationType_s |Szöveg |A tár tárolási replikálásának típusa, például GeoRedundant |
| SourceSystem |Szöveg |Az aktuális adatforrásrendszer – Azure |
| ResourceId |Szöveg |Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Szöveg |Az erőforrás előfizetés-azonosítója (pl. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceGroup |Szöveg |Erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató, amelybe az adatok gyűjtése történik. Például: Microsoft. Recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Az az erőforrástípus, amelybe az adatok gyűjtése történik. Például: tárolók |

### <a name="backup-management-server"></a>Biztonságimásolat-felügyeleti kiszolgáló

Ez a táblázat a biztonságimásolat-felügyeleti kiszolgálók alapszintű mezőit tartalmazza.

|Mező  |Adattípus  | Leírás  |
|---------|---------|----------|
|BackupManagementServerName_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgáló neve        |
|AzureBackupAgentVersion_s     |Szöveg         |A Azure Backup ügynök verziója a biztonságimásolat-felügyeleti kiszolgálón          |
|BackupManagementServerVersion_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgáló verziója|
|BackupManagementServerOSVersion_s     |Szöveg            |A biztonságimásolat-felügyeleti kiszolgáló operációs rendszerének verziója|
|BackupManagementServerType_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgáló típusa, mint MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgáló egyedi azonosítására szolgáló mező       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Ez a tábla meghatározza, hogy egy kötet milyen munkaterheléshez van társítva.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| StorageUniqueId_s |Szöveg |A tárolási entitás azonosítására használt egyedi azonosító |
| BackupItemType_s |Szöveg |Azok a munkaterhelések, amelyekhez ez a kötet az előnyben részesített tároló|

### <a name="protectedinstance"></a>ProtectedInstance

Ez a táblázat az alapszintű védett példányokkal kapcsolatos mezőket tartalmazza.

| Mező | Adattípus |Alkalmazható verziók | Leírás |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Szöveg |v2|Egyedi azonosító, amely a DPM, MABS használatával biztonsági mentést végző virtuális gépek biztonsági mentési elemének azonosítására szolgál.|
| ProtectedContainerUniqueId_s |Szöveg |v2|Egyedi azonosító, amely a védett tároló azonosítására szolgál a DPM, a MABS-t használó virtuális gépek kivételével.|
| ProtectedInstanceCount_s |Szöveg |v2|A társított biztonsági másolati elemhez vagy a védett tárolóhoz tartozó védett példányok száma az adott napon és időpontban|

### <a name="recoverypoint"></a>RecoveryPoint

Ez a tábla a helyreállítási pontok alapszintű kapcsolódó mezőit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| BackupItemUniqueId_s |Szöveg |Egyedi azonosító, amely a DPM, MABS használatával biztonsági mentést végző virtuális gépek biztonsági mentési elemének azonosítására szolgál.|
| OldestRecoveryPointTime_s |Szöveg |A biztonsági mentési tétel legrégebbi helyreállítási pontjának dátuma|
| OldestRecoveryPointLocation_s |Szöveg |A biztonsági mentési tétel legrégebbi helyreállítási pontjának helye|
| LatestRecoveryPointTime_s |Szöveg |A biztonsági mentési tétel legutóbbi helyreállítási pontjának dátuma|
| LatestRecoveryPointLocation_s |Szöveg |A biztonsági mentési tétel legutóbbi helyreállítási pontjának helye|

## <a name="sample-kusto-queries"></a>Példa Kusto-lekérdezésekre

Az alábbi példák segítséget nyújtanak a Azure Diagnostics táblában található Azure Backup-adathoz tartozó lekérdezések írásához:

- Minden sikeres biztonsági mentési feladat

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Az összes sikertelen biztonsági mentési feladat

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Minden sikeres Azure-beli virtuális gép biztonsági mentési feladata

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Az összes sikeres SQL-napló biztonsági mentési feladata

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Az összes sikeres Azure Backup ügynök feladatai

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

## <a name="v1-schema-vs-v2-schema"></a>V1 Schema vs v2 séma

Korábban a Azure Backup-ügynök és az Azure-beli virtuális gép biztonsági mentésére vonatkozó diagnosztikai adatait a rendszer a ***v1 séma** _ néven említett sémában Azure Diagnostics táblába küldték. Ezt követően új oszlopok lettek hozzáadva a más forgatókönyvek és munkaterhelések támogatásához, és a diagnosztikai adatok egy új sémában (_ *_v2 Schema_* *) lettek leküldve.  

A visszamenőleges kompatibilitás miatt a Azure Backup-ügynök és az Azure virtuális gép biztonsági mentésének diagnosztikai adatait jelenleg a v1-es és v2-es sémában is elküldi Azure Diagnostics táblázatba (a v1 séma mostantól egy elavult útvonalon érhető el). A naplózási lekérdezésekben a SchemaVersion_s = = "v1" rekordok szűrésével azonosíthatja, hogy mely rekordok Log Analytics v1 sémában.

A fent ismertetett [adatmodellben](#using-azure-backup-data-model) tekintse meg a harmadik oszlop "Description" utasítását, és azonosítsa, hogy mely oszlopok csak a v1 sémához tartoznak.

### <a name="modifying-your-queries-to-use-the-v2-schema"></a>A lekérdezések módosítása a v2 séma használatára

Mivel a v1-séma elavult elérési úton van, ajánlott csak a v2 sémát használni a Azure Backup diagnosztikai adatain lévő összes egyéni lekérdezésben. Az alábbi példa bemutatja, hogyan frissítheti a lekérdezéseket a v1-séma függőségének eltávolítására:

1. Azonosítsa, hogy a lekérdezés olyan mezőt használ-e, amely csak a v1 sémára vonatkozik. Tegyük fel, hogy van egy lekérdezése, amely felsorolja az összes biztonsági mentési elemet és a hozzájuk társított védett kiszolgálókat a következőképpen:

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | distinct BackupItemUniqueId_s, ProtectedServerUniqueId_s
    ````

    A fenti lekérdezés a ProtectedServerUniqueId_s mezőt használja, amely csak a v1 sémára alkalmazható. Ennek a mezőnek a v2-sémája ProtectedContainerUniqueId_s (lásd a fenti táblázatokat). A BackupItemUniqueId_s mező még a v2 séma esetében is alkalmazható, és ugyanez a mező használható ebben a lekérdezésben.

2. Frissítse a lekérdezést, hogy a v2 séma mezőinek nevét használja. Javasoljuk, hogy az összes lekérdezésben használja a **SchemaVersion_s = = "v2"** szűrőt, hogy csak a v2-sémának megfelelő rekordokat elemezze a lekérdezés:

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | where SchemaVersion_s=="V2"
    | distinct BackupItemUniqueId_s, ProtectedContainerUniqueId_s
    ````

## <a name="next-steps"></a>Következő lépések

Az adatmodell áttekintése után megkezdheti az [Egyéni lekérdezések létrehozását](../azure-monitor/visualize/tutorial-logs-dashboards.md) Azure monitor naplókban a saját irányítópultjának létrehozásához.
