---
title: Adatmodell a Azure Backup diagnosztikai eseményeihez
description: Ez az adatmodell a diagnosztikai események Log Analyticsba (LA) történő küldésének erőforrás-specifikus módjára hivatkozik.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 0d75af6d2b41aad0b5f821dd1f6409b30f7ca531
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499594"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Adatmodell a Azure Backup diagnosztikai eseményeihez

> [!NOTE]
>
> Egyéni jelentéskészítési nézetek létrehozásához ajánlott a [rendszerfunkciókat Azure monitor naplókon](backup-reports-system-functions.md) használni az alább felsorolt nyers táblák használata helyett.

## <a name="coreazurebackup"></a>CoreAzureBackup

Ez a táblázat az alapszintű biztonsági mentési entitásokkal, például a tárolókkal és a biztonsági másolati elemekkel kapcsolatos információkat tartalmaz.

| **Mező**                         | **Adattípus** | **Leírás**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Szöveg          | Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-AZONOSÍTÓját. |
| OperationName                     | Szöveg          | Ez a mező az aktuális művelet – BackupItem, BackupItemAssociation vagy ProtectedContainer nevét jelöli. |
| Kategória                          | Szöveg          | Ez a mező a Azure Monitor naplókba leküldett diagnosztikai adatkategóriát jelöli. Például: CoreAzureBackup. |
| AgentVersion                      | Szöveg          | Az ügynök biztonsági mentési vagy védelmi ügynökének verziószáma (SC DPM és MABS esetén) |
| AzureBackupAgentVersion           | Szöveg          | A Azure Backup ügynök verziója a biztonságimásolat-felügyeleti kiszolgálón |
| AzureDataCenter                   | Szöveg          | Az adatközpont, amelyben a tároló található                       |
| BackupItemAppVersion              | Szöveg          | A biztonsági mentési tétel alkalmazásának verziója                       |
| BackupItemFriendlyName            | Szöveg          | A biztonsági mentési tétel rövid neve                             |
| BackupItemName                    | Szöveg          | A biztonsági másolati tétel neve                                      |
| BackupItemProtectionState         | Szöveg          | A biztonsági mentési elem védelmi állapota                          |
| BackupItemFrontEndSize            | Szöveg          | A biztonsági mentési tétel előtér-mérete                            |
| BackupItemType                    | Szöveg          | A biztonságimásolat-tétel típusa. Például: VM, fájlmappa             |
| BackupItemUniqueId                | Szöveg          | A biztonsági másolati tétel egyedi azonosítója                         |
| BackupManagementServerType        | Szöveg          | A biztonságimásolat-felügyeleti kiszolgáló típusa, mint a MABS, SC DPM     |
| BackupManagementServerUniqueId    | Szöveg          | A biztonságimásolat-felügyeleti kiszolgáló egyedi azonosítására szolgáló mező      |
| BackupManagementType              | Szöveg          | A kiszolgáló szolgáltatói típusa a biztonsági mentési feladatokhoz. Például: IaaSVM, fájlmappa |
| BackupManagementServerName        | Szöveg          | A biztonságimásolat-felügyeleti kiszolgáló neve                         |
| BackupManagementServerOSVersion   | Szöveg          | A biztonságimásolat-felügyeleti kiszolgáló operációs rendszerének verziója                   |
| BackupManagementServerVersion     | Szöveg          | A biztonságimásolat-felügyeleti kiszolgáló verziója                      |
| LatestRecoveryPointLocation       | Szöveg          | A biztonsági mentési tétel legutóbbi helyreállítási pontjának helye    |
| LatestRecoveryPointTime           | DateTime      | A biztonsági mentési tétel legutóbbi helyreállítási pontjának dátuma   |
| OldestRecoveryPointLocation       | Szöveg          | A biztonsági mentési tétel legrégebbi helyreállítási pontjának helye    |
| OldestRecoveryPointTime           | DateTime      | A biztonsági mentési tétel legutóbbi helyreállítási pontjának dátuma   |
| PolicyUniqueId                    | Szöveg          | A szabályzat azonosítására szolgáló egyedi azonosító                             |
| ProtectedContainerFriendlyName    | Szöveg          | A védett kiszolgáló rövid neve                        |
| ProtectedContainerLocation        | Szöveg          | A védett tároló a helyszínen vagy az Azure-ban található-e |
| ProtectedContainerName            | Szöveg          | A védett tároló neve                            |
| ProtectedContainerOSType          | Szöveg          | A védett tároló operációs rendszerének típusa                          |
| ProtectedContainerOSVersion       | Szöveg          | A védett tároló operációs rendszerének verziója                        |
| ProtectedContainerProtectionState | Szöveg          | A védett tároló védelmi állapota                  |
| ProtectedContainerType            | Szöveg          | Azt jelzi, hogy a védett tároló kiszolgáló vagy tároló-e  |
| ProtectedContainerUniqueId        | Szöveg          | Egyedi azonosító, amely a védett tároló azonosítására szolgál a DPM, a MABS-t használó virtuális gépek kivételével. |
| ProtectedContainerWorkloadType    | Szöveg          | A védett tároló biztonsági mentésének típusa. Például: IaaSVMContainer |
| ProtectionGroupName               | Szöveg          | A védelmi csoport neve, amelyben a biztonsági másolati elem védett, az SC DPM és a MABS esetében, ha van ilyen. |
| ResourceGroupName                 | Szöveg          | Az erőforrás erőforráscsoport (például Recovery Services tároló) az összegyűjtött adatokhoz |
| Sémaverzióval                     | Szöveg          | Ez a mező a séma aktuális verzióját jelöli. **V2** |
| SecondaryBackupProtectionState    | Szöveg          | Azt jelzi, hogy engedélyezve van-e a másodlagos védelem a biztonsági mentési elemmel kapcsolatban  |
| Állapot                             | Szöveg          | A biztonságimásolat-elem objektumának állapota. Például: aktív, törölve |
| StorageReplicationType            | Szöveg          | A tár tárolási replikálásának típusa. Például: GeoRedundant |
| SubscriptionId                    | Szöveg          | Az erőforrás (például Recovery Services tároló) előfizetés-azonosítója, amelybe az adatok gyűjtése történik |
| VaultName                         | Szöveg          | A tároló neve                                            |
| VaultTags                         | Szöveg          | A tár erőforrásához társított Címkék                    |
| VaultUniqueId                     | Szöveg          | A tár egyedi azonosítója                             |
| SourceSystem                      | Szöveg          | Az aktuális adatforrásrendszer – Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Ez a táblázat a riasztással kapcsolatos mezők részleteit tartalmazza.

| **Mező**                      | **Adattípus** | **Leírás**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Szöveg          | Annak az erőforrásnak az egyedi azonosítója, amelyről az adatokat gyűjti. Például egy Recovery Services tár erőforrás-azonosítója |
| OperationName                  | Szöveg          | Az aktuális művelet neve. Például: riasztás            |
| Kategória                       | Szöveg          | Azure Monitor naplókba leküldett diagnosztikai adatkategóriák – AddonAzureBackupAlerts |
| AlertCode                      | Szöveg          | Riasztás típusának egyedi azonosítására szolgáló kód                     |
| AlertConsolidationStatus       | Szöveg          | Annak megállapítása, hogy a riasztás konszolidált riasztás-e, vagy sem         |
| AlertOccurrenceDateTime        | DateTime      | A riasztás létrehozásának dátuma és időpontja                     |
| AlertRaisedOn                  | Szöveg          | A riasztást kiváltó entitás típusa                        |
| AlertSeverity                  | Szöveg          | A riasztás súlyossága. Például kritikus                 |
| AlertStatus                    | Szöveg          | A riasztás állapota. Például: aktív                     |
| AlertTimeToResolveInMinutes    | Szám        | A riasztás feloldásához szükséges idő. Üres az aktív riasztásokhoz.     |
| AlertType                      | Szöveg          | A riasztás típusa. Például: biztonsági mentés                           |
| AlertUniqueId                  | Szöveg          | A generált riasztás egyedi azonosítója                    |
| BackupItemUniqueId             | Szöveg          | A riasztáshoz társított biztonsági mentési tétel egyedi azonosítója |
| BackupManagementServerUniqueId | Szöveg          | A biztonságimásolat-felügyeleti kiszolgáló egyedi azonosítására szolgáló mező, ha van ilyen, a biztonsági mentési elem védelme |
| BackupManagementType           | Szöveg          | Szolgáltató típusa a biztonsági mentési feladatot végző kiszolgáló számára, például IaaSVM, fájlmappa |
| CountOfAlertsConsolidated      | Szám        | Konszolidált riasztások száma összevont riasztás esetén  |
| ProtectedContainerUniqueId     | Szöveg          | A riasztáshoz társított védett kiszolgáló egyedi azonosítója |
| RecommendedAction              | Szöveg          | A riasztás feloldásához javasolt művelet                      |
| Sémaverzióval                  | Szöveg          | A séma jelenlegi verziója, például **v2**            |
| Állapot                          | Szöveg          | A riasztási objektum aktuális állapota, például aktív, törölve |
| StorageUniqueId                | Szöveg          | A tárolási entitás azonosítására használt egyedi azonosító                |
| VaultUniqueId                  | Szöveg          | A riasztáshoz kapcsolódó tár azonosítására szolgáló egyedi azonosító    |
| SourceSystem                   | Szöveg          | Az aktuális adatforrásrendszer – Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Ez a táblázat az alapszintű védett példányokkal kapcsolatos mezőket tartalmazza.

| **Mező**                      | **Adattípus** | **Leírás**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Szöveg          | Annak az erőforrásnak az egyedi azonosítója, amelyről az adatokat gyűjti. Például egy Recovery Services tár erőforrás-azonosítója |
| OperationName                  | Szöveg          | A művelet neve, például ProtectedInstance         |
| Kategória                       | Szöveg          | Azure Monitor naplókba leküldett diagnosztikai adatkategóriák – AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Szöveg          | A biztonsági másolati tétel egyedi azonosítója                                 |
| BackupManagementServerUniqueId | Szöveg          | A biztonságimásolat-felügyeleti kiszolgáló egyedi azonosítására szolgáló mező, ha van ilyen, a biztonsági mentési elem védelme |
| BackupManagementType           | Szöveg          | Szolgáltató típusa a biztonsági mentési feladatot végző kiszolgáló számára, például IaaSVM, fájlmappa |
| ProtectedContainerUniqueId     | Szöveg          | A feladatot futtató védett tároló azonosítására szolgáló egyedi azonosító |
| ProtectedInstanceCount         | Szöveg          | A társított biztonsági másolati elemhez vagy a védett tárolóhoz tartozó védett példányok száma az adott napon és időpontban |
| Sémaverzióval                  | Szöveg          | A séma jelenlegi verziója, például **v2**            |
| Állapot                          | Szöveg          | A biztonsági mentési elem objektumának állapota, például aktív, törölve |
| VaultUniqueId                  | Szöveg          | A védett példányhoz társított védett tároló egyedi azonosítója |
| SourceSystem                   | Szöveg          | Az aktuális adatforrásrendszer – Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Ez a táblázat a feladatokkal kapcsolatos mezők részleteit tartalmazza.

| **Mező**                      | **Adattípus** | **Leírás**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Szöveg          | Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója |
| OperationName                  | Szöveg          | Ez a mező az aktuális művelet nevét jelöli – feladat    |
| Kategória                       | Szöveg          | Ez a mező a Azure Monitor naplókba leküldett diagnosztikai adatkategóriákat jelöli – AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Szöveg          | Mező, amely megadja, hogy a feladattípus ad hoc vagy ütemezett           |
| BackupItemUniqueId             | Szöveg          | A tárolási entitáshoz kapcsolódó biztonsági mentési elem azonosítására használt egyedi azonosító |
| BackupManagementServerUniqueId | Szöveg          | A tárolási entitáshoz kapcsolódó biztonságimásolat-felügyeleti kiszolgáló azonosítására használt egyedi azonosító |
| BackupManagementType           | Szöveg          | Szolgáltatói típus a biztonsági mentés végrehajtásához, például: IaaSVM, fájlmappa, amelyhez ez a feladattípus tartozik |
| DataTransferredInMB            | Szám        | A feladatokhoz tartozó MB-ban továbbított adatok                          |
| JobDurationInSecs              | Szám        | Feladatok teljes időtartama másodpercben                                |
| JobFailureCode                 | Szöveg          | Hiba történt a hibakód karakterlánca miatt, mert a művelet sikertelen volt    |
| JobOperation                   | Szöveg          | A művelet, amelynek a feladata fut, például biztonsági mentés, visszaállítás, biztonsági mentés konfigurálása |
| JobOperationSubType            | Szöveg          | A feladat műveletének altípusa. Például: "log", a napló biztonsági mentési feladata esetén |
| JobStartDateTime               | DateTime      | A feladatok futtatásának dátuma és időpontja                       |
| Feladat állapota                      | Szöveg          | A Befejezett feladatok állapota, például befejezett, sikertelen   |
| JobUniqueId                    | Szöveg          | A feladatot azonosító egyedi azonosító                                |
| ProtectedContainerUniqueId     | Szöveg          | A feladathoz társított védett kiszolgáló egyedi azonosítója |
| RecoveryJobDestination         | Szöveg          | A helyreállítási feladatok célja, amelyben az adatok helyreállítása történik   |
| RecoveryJobRPDateTime          | DateTime      | A helyreállított helyreállítási pont létrehozásának dátuma és időpontja |
| RecoveryJobLocation            | Szöveg          | A helyreállított helyreállítási pont tárolási helye |
| RecoveryLocationType           | Szöveg          | A helyreállítási hely típusa                                |
| Sémaverzióval                  | Szöveg          | A séma jelenlegi verziója, például **v2**            |
| Állapot                          | Szöveg          | A feladatütemezés aktuális állapota, például aktív, törölve |
| VaultUniqueId                  | Szöveg          | A feladathoz társított védett tároló egyedi azonosítója |
| SourceSystem                   | Szöveg          | Az aktuális adatforrásrendszer – Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

Ez a táblázat a házirendekkel kapcsolatos mezőkről tartalmaz információkat.

| **Mező**                       | **Adattípus**  | **Leírás**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Szöveg           | Annak az erőforrásnak az egyedi azonosítója, amelyről az adatokat gyűjti. Például egy Recovery Services tár erőforrás-azonosítója |
| OperationName                   | Szöveg           | A művelet neve, például házirend vagy PolicyAssociation |
| Kategória                        | Szöveg           | Azure Monitor naplókba leküldett diagnosztikai adatkategóriák – AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Szöveg           | A hét azon napjai, amikor a biztonsági mentések ütemezése megtörtént            |
| BackupFrequency                 | Szöveg           | A biztonsági másolatok futtatásának gyakorisága. Például: napi, heti |
| BackupManagementType            | Szöveg           | A kiszolgáló szolgáltatói típusa a biztonsági mentési feladatokhoz. Például: IaaSVM, fájlmappa |
| BackupManagementServerUniqueId  | Szöveg           | A biztonságimásolat-felügyeleti kiszolgáló egyedi azonosítására szolgáló mező, ha van ilyen, a biztonsági mentési elem védelme |
| BackupTimes                     | Szöveg           | A biztonsági másolatok ütemezésének dátuma és időpontja                     |
| DailyRetentionDuration          | Egész szám   | A beállított biztonsági másolatok teljes megőrzési időtartama (nap)      |
| DailyRetentionTimes             | Szöveg           | A napi megőrzés konfigurálásának dátuma és időpontja            |
| DiffBackupDaysOfTheWeek         | Szöveg           | A hét napjai az SQL-különbözeti biztonsági mentésekhez az Azure-beli virtuális gép biztonsági mentésében |
| DiffBackupFormat                | Szöveg           | Az SQL-alapú különbözeti biztonsági másolatok formátuma az Azure virtuális gép biztonsági mentésében   |
| DiffBackupRetentionDuration     | Tizedes tört | Az SQL Azure-beli virtuális gépek biztonsági mentésének megőrzési időtartama |
| DiffBackupTime                  | Idő           | Az SQL Azure-beli virtuális gépek biztonsági mentésének ideje     |
| LogBackupFrequency              | Tizedes tört | SQL-naplók biztonsági másolatainak gyakorisága                            |
| LogBackupRetentionDuration      | Tizedes tört | Az SQL Azure-beli virtuális gép biztonsági mentésében tárolt biztonsági másolatok megőrzési időtartama |
| MonthlyRetentionDaysOfTheMonth  | Szöveg           | A hónap hete, amikor a havi megőrzés konfigurálva van.  Például: első, utolsó |
| MonthlyRetentionDaysOfTheWeek   | Szöveg           | A havi megőrzésre kiválasztott hét napjai              |
| MonthlyRetentionDuration        | Szöveg           | A beállított biztonsági másolatok teljes megőrzési időtartama (hónap)    |
| MonthlyRetentionFormat          | Szöveg           | A havi megőrzés konfigurációjának típusa Például naponta, hetente, hetente |
| MonthlyRetentionTimes           | Szöveg           | A havi megőrzés konfigurálásának dátuma és időpontja           |
| MonthlyRetentionWeeksOfTheMonth | Szöveg           | A hónap hete, amikor a havi megőrzés konfigurálva van.   Például: első, utolsó |
| PolicyName                      | Szöveg           | A megadott házirend neve                                   |
| PolicyUniqueId                  | Szöveg           | A szabályzat azonosítására szolgáló egyedi azonosító                             |
| PolicyTimeZone                  | Szöveg           | Az időzóna, amelyben a szabályzat időmezői meg vannak adva a naplókban |
| RetentionDuration               | Szöveg           | Konfigurált biztonsági másolatok megőrzési időtartama                    |
| RetentionType                   | Szöveg           | Megőrzés típusa                                            |
| Sémaverzióval                   | Szöveg           | Ez a mező a séma aktuális verzióját jelöli, **v2** |
| Állapot                           | Szöveg           | A házirend-objektum aktuális állapota. Például: aktív, törölve |
| SynchronisationFrequencyPerDay  | Egész szám   | Napok száma egy nap során a rendszer az SC DPM és a MABS esetében szinkronizálja a fájlok biztonsági mentését |
| VaultUniqueId                   | Szöveg           | Azon tár egyedi azonosítója, amelyhez ez a szabályzat tartozik          |
| WeeklyRetentionDaysOfTheWeek    | Szöveg           | A heti megőrzéshez kiválasztott hét napjai               |
| WeeklyRetentionDuration         | Tizedes tört | Heti megőrzési időtartam összesen hetekben a konfigurált biztonsági másolatok esetében |
| WeeklyRetentionTimes            | Szöveg           | A heti megőrzés konfigurálásának dátuma és időpontja            |
| YearlyRetentionDaysOfTheMonth   | Szöveg           | Az éves megőrzéshez kiválasztott hónap dátuma             |
| YearlyRetentionDaysOfTheWeek    | Szöveg           | Az éves megőrzéshez kiválasztott hét napjai               |
| YearlyRetentionDuration         | Tizedes tört | Teljes megőrzési időtartam években a konfigurált biztonsági másolatok esetében     |
| YearlyRetentionFormat           | Szöveg           | Az évenkénti megőrzéshez szükséges konfiguráció típusa, például naponta, hetente, hetente |
| YearlyRetentionMonthsOfTheYear  | Szöveg           | Az év hónapos megőrzésre kiválasztott hónapja             |
| YearlyRetentionTimes            | Szöveg           | Az éves adatmegőrzés konfigurálásának dátuma és időpontja            |
| YearlyRetentionWeeksOfTheMonth  | Szöveg           | Az éves megőrzéshez kiválasztott hónap hete             |
| SourceSystem                    | Szöveg           | Az aktuális adatforrásrendszer – Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Ez a táblázat a Storage szolgáltatással kapcsolatos mezők részleteit tartalmazza.

| **Mező**                      | **Adattípus** | **Leírás**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Szöveg          | Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója |
| OperationName                  | Szöveg          | Ez a mező az aktuális művelet – tár vagy StorageAssociation nevét jelöli. |
| Kategória                       | Szöveg          | Ez a mező a Azure Monitor naplókba leküldett diagnosztikai adatkategóriákat jelöli – AddonAzureBackupStorage |
| BackupItemUniqueId             | Szöveg          | Egyedi azonosító, amely a DPM, MABS használatával biztonsági mentést végző virtuális gépek biztonsági mentési elemének azonosítására szolgál. |
| BackupManagementServerUniqueId | Szöveg          | A biztonságimásolat-felügyeleti kiszolgáló egyedi azonosítására szolgáló mező, ha van ilyen, a biztonsági mentési elem védelme |
| BackupManagementType           | Szöveg          | A kiszolgáló szolgáltatói típusa a biztonsági mentési feladatokhoz. Például: IaaSVM, fájlmappa |
| PreferredWorkloadOnVolume      | Szöveg          | A számítási feladatok, amelyekhez ez a kötet az előnyben részesített tároló      |
| ProtectedContainerUniqueId     | Szöveg          | A biztonsági mentési elemmel társított védett tároló egyedi azonosítója |
| Sémaverzióval                  | Szöveg          | A séma verziója. Például: **v2**                   |
| Állapot                          | Szöveg          | A biztonságimásolat-elem objektumának állapota. Például: aktív, törölve |
| StorageAllocatedInMBs          | Szám        | A megfelelő biztonsági mentési tétel által lefoglalt tárterület a lemez típusú megfelelő tárolóban |
| StorageConsumedInMBs           | Szám        | A megfelelő tároló biztonsági mentési eleme által felhasznált tárterület mérete |
| StorageName                    | Szöveg          | A tárolási entitás neve. Például: E:\                      |
| StorageTotalSizeInGBs          | Szöveg          | A Storage-entitás által felhasznált tárterület teljes mérete GB-ban     |
| StorageType                    | Szöveg          | Tárolási típus, például felhő, kötet, lemez             |
| StorageUniqueId                | Szöveg          | A tárolási entitás azonosítására használt egyedi azonosító                |
| VaultUniqueId                  | Szöveg          | A tárolási entitáshoz kapcsolódó tár azonosítására használt egyedi azonosító |
| VolumeFriendlyName             | Szöveg          | A tárolási kötet rövid neve                          |
| SourceSystem                   | Szöveg          | Az aktuális adatforrásrendszer – Azure                    |

## <a name="valid-operation-names-for-each-table"></a>Az egyes táblák érvényes műveleti nevei

A fenti táblázatokban szereplő összes rekordhoz tartozik egy társított **művelet neve**. A művelet neve leírja a rekord típusát (és azt is, hogy a tábla mely mezői vannak feltöltve a rekordhoz). Minden táblázat (kategória) egy vagy több különböző műveleti nevet támogat. Az alábbi táblázat az egyes fenti táblák támogatott műveleti neveit összegzi.

| **Tábla neve/kategória**                   | **Támogatott műveletek nevei** | **Leírás**              |
| ------------------------------------------- | ------------------------------|----------------------------- |
| CoreAzureBackup | BackupItem | Egy olyan rekordot jelöl, amely egy adott biztonsági másolati elem összes adatát tartalmazza, például az azonosítót, a nevet, a típust stb. |
| CoreAzureBackup | BackupItemAssociation | A biztonsági másolati elemek és a hozzá tartozó védett tároló (ha van ilyen) közötti leképezést jelöli. |
| CoreAzureBackup | BackupItemFrontEndSizeConsumption | A biztonsági másolati elemek és az előtér-méret közötti leképezést jelöli. |
| CoreAzureBackup | ProtectedContainer | Egy olyan rekordot jelöl, amely egy adott védett tároló összes adatát tartalmazza, például az azonosítót, a nevet, a típust stb. |
| CoreAzureBackup | ProtectedContainerAssociation | Egy védett tároló és a biztonsági mentéshez használt tár közötti leképezést jelöl. |
| CoreAzureBackup | Tároló | Egy olyan rekordot jelöl, amely az adott tár összes adatát tartalmazza. AZONOSÍTÓ, név, címkék, hely stb. |
| CoreAzureBackup | RecoveryPoint | Egy olyan rekordot jelöl, amely egy adott biztonsági mentési tétel legrégebbi és utolsó helyreállítási pontját tartalmazza. |
| AddonAzureBackupJobs | Feladat |  Egy olyan rekordot jelöl, amely egy adott feladat összes adatát tartalmazza. Például: feladatütemezés, Kezdési idő, állapot stb. |
| AddonAzureBackupAlerts | Riasztás | Egy olyan rekordot jelöl, amely egy adott riasztás összes adatát tartalmazza. Például a riasztás létrehozásának ideje, súlyossága, állapota stb.  |
| AddonAzureBackupStorage | Tárolás | Egy olyan rekordot jelöl, amely egy adott tárolási entitás összes adatát tartalmazza. Például: tárolási név, típus stb. |
| AddonAzureBackupStorage | StorageAssociation | A biztonsági másolati elemek és a biztonsági mentési elemek által felhasznált teljes felhőalapú tárterület közötti leképezést jelöli. |
| AddonAzureBackupProtectedInstance | ProtectedInstance | Egy olyan rekordot jelöl, amely az egyes tárolók vagy biztonsági másolati elemek védett példányainak darabszámát tartalmazza. Az Azure-beli virtuális gépek biztonsági mentése esetén a védett példányok száma a biztonsági mentési elem szintjén, a védett tároló szintjén elérhető egyéb munkaterhelések esetén is elérhető. |
| AddonAzureBackupPolicy | Szabályzat |  Egy olyan rekordot jelöl, amely a biztonsági mentési és adatmegőrzési szabályzat összes adatát tartalmazza. Például: azonosító, név, megőrzési beállítások stb. |
| AddonAzureBackupPolicy | PolicyAssociation | A biztonsági másolati elem és a rajta alkalmazott biztonsági mentési házirend közötti leképezést jelöli. |   

Gyakran szükség van a különböző táblák közötti összekapcsolásra, valamint a különböző rekordokra, amelyek ugyanahhoz a táblához tartoznak (a művelet neve alapján megkülönböztetve) az elemzéshez szükséges összes mező beszerzéséhez. Az első lépésekhez tekintse meg a [minta lekérdezéseit](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries) . 

## <a name="next-steps"></a>Következő lépések

- [Ismerje meg, hogyan küldhet diagnosztikai információkat Log Analytics](./backup-azure-diagnostic-events.md)
- [Megtudhatja, hogyan írhat lekérdezéseket az erőforrás-specifikus táblákon](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries)