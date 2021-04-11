---
title: Azure Monitor naplók rendszerfunkciói
description: Egyéni lekérdezések írása Azure Monitor naplókon a rendszerfunkciók használatával
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: acb45e6ad0250a1f8d10377fdd509e40051f25b9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564908"
---
# <a name="system-functions-on-azure-monitor-logs"></a>Azure Monitor naplók rendszerfunkciói

A Azure Backup a Log Analytics (LA) munkaterületeken alapértelmezés szerint elérhető, System functions vagy Solution függvények nevű függvényeket biztosít.
 
Ezek a függvények a (z) LA [nyers Azure Backup tábláiban](./backup-azure-reports-data-model.md) található adatokon működnek, és olyan formázott adatokat adnak vissza, amelyek segítségével egyszerűen lekérheti az összes biztonsági mentéssel kapcsolatos entitás információit egyszerű lekérdezések használatával. A felhasználók paramétereket adhatnak át ezen függvények számára a függvények által visszaadott adatok szűréséhez. 

Az egyéni jelentések létrehozásához a System functions használata ajánlott a biztonsági mentési adatok lekérdezéséhez az LA munkaterületeken, mivel ezek számos előnnyel rendelkeznek, ahogy az alábbi szakaszban is talál.

## <a name="benefits-of-using-system-functions"></a>A System functions használatának előnyei

* **Egyszerűbb lekérdezések**: a függvények használata segít csökkenteni a lekérdezésekben szükséges illesztések számát. Alapértelmezés szerint a függvények "összeolvasztott" sémákat adnak vissza, amelyek az entitásra (a biztonsági mentési példányra, a feladatra, a tárolóra stb.) vonatkozó összes adatot tartalmazzák. Ha például a biztonsági mentési elem neve és a hozzá tartozó tároló alapján kell lekérnie a sikeres biztonsági mentési feladatok listáját, akkor a **_AzureBackup_getJobs ()** függvény egyszerű hívása megadja az összes feladatra vonatkozó információt. Másfelől a nyers táblák lekérdezéséhez a [AddonAzureBackupJobs](./backup-azure-reports-data-model.md#addonazurebackupjobs) és a [CoreAzureBackup](./backup-azure-reports-data-model.md#coreazurebackup) táblák között több illesztést is el kell végeznie.

* **Simább áttérés az örökölt diagnosztikai eseményről**: a System functions segítségével zökkenőmentesen áttérhet az [örökölt diagnosztikai eseménytől](./backup-azure-diagnostic-events.md#legacy-event) (AzureBackupReport AzureDiagnostics módban) az [erőforrás-specifikus eseményekre](./backup-azure-diagnostic-events.md#diagnostics-events-available-for-azure-backup-users). A Azure Backup által biztosított összes rendszerfunkció lehetővé teszi egy olyan paraméter megadását, amely lehetővé teszi, hogy a függvény csak az erőforrás-specifikus táblákból kérdezze le az adatokat, vagy az örökölt táblából és az erőforrás-specifikus táblákból (a rekordok deduplikálása) adatokat is lekérdezzen.
    * Ha sikeresen áttelepítette az erőforrás-specifikus táblákat, dönthet úgy, hogy kizárja az örökölt táblát a függvény által lekérdezve.
    * Ha jelenleg az áttelepítés folyamatában van, és vannak olyan adatai az örökölt táblákban, amelyekhez szükség van az elemzéshez, dönthet úgy, hogy az örökölt táblát is tartalmazza. Ha az áttérés befejeződött, és már nincs szüksége az örökölt táblából származó adatokra, egyszerűen frissítheti a lekérdezésekben a függvénynek átadott paraméter értékét, hogy kizárja az örökölt táblát.
    * Ha továbbra is csak az örökölt táblázatot használja, akkor a függvények továbbra is működni fognak, ha úgy dönt, hogy az örökölt táblát ugyanazon a paraméteren keresztül adja meg. Ajánlott azonban a legkorábbi [erőforrás-specifikus táblákra váltani](./backup-azure-diagnostic-events.md#steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace) .

* **Csökkenti az egyéni lekérdezések feltörésének lehetőségét**: Ha Azure Backup bevezeti a mögöttes La táblák sémájának fejlesztéseit a jövőbeli jelentési forgatókönyvek kielégítéséhez, a függvények definíciója is frissül, hogy figyelembe vegye a séma módosításait. Így ha rendszerfunkciókat használ az egyéni lekérdezések létrehozásához, a lekérdezések nem lesznek megszakítva, még akkor is, ha a táblák mögöttes sémája módosul.

> [!NOTE]
> A rendszerfunkciókat a Microsoft tartja karban, és a definíciók nem szerkeszthetők a felhasználók számára. Ha szerkeszthető függvények szükségesek, a [mentett függvények](../azure-monitor/logs/functions.md) a La-ben is létrehozhatók.

## <a name="types-of-system-functions-offered-by-azure-backup"></a>A Azure Backup által kínált rendszerfunkciók típusai

* **Core functions**: ezek olyan függvények, amelyek segítségével lekérdezheti a legfontosabb Azure Backup entitásokat, például a biztonsági mentési példányokat, a tárolókat, a szabályzatokat, a feladatokat és a számlázási entitásokat. A **_AzureBackup_getBackupInstances** függvény például a környezet összes olyan biztonsági mentési példányának listáját adja vissza, amely a legutóbbi befejezett nap (UTC) szerint szerepel. A fenti alapfunkciókhoz tartozó paraméterek és visszaadott sémák a jelen cikkben olvashatók.

* **Trend functions**: ezek olyan függvények, amelyek a biztonsági mentéssel kapcsolatos entitások (például biztonsági mentési példányok, számlázási csoportok) korábbi rekordjait adják vissza, és lehetővé teszik a napi, heti és havi trend-információk lekérését a legfontosabb metrikákkal (például a darabszámot, a tárterületet), amelyek ezekre az entitásokra vonatkoznak. Az egyes trend függvények paramétereit és visszaadott sémáját a cikk az alábbi táblázat tartalmazza.

> [!NOTE]
> A System functions jelenleg legfeljebb az utolsó befejezett napra (UTC) küldi vissza az adatokból. Az aktuális részleges nap adatát nem adja vissza a rendszer. Tehát ha az aktuális nap rekordjait szeretné lekérdezni, akkor a nyers LA táblákat kell használnia.


## <a name="list-of-system-functions"></a>Rendszerfunkciók listája

### <a name="core-functions"></a>A Core függvények

#### <a name="_azurebackup_getvaults"></a>_AzureBackup_GetVaults ()

Ez a függvény a LA munkaterülethez társított Azure-környezetben található összes Recovery Services tároló listáját adja vissza.

**Paraméterek**

| **Paraméter neve** | **Leírás** | **Kötelező?** | **Példa értéke** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Ezt a paramétert csak akkor használja, ha a RangeEnd paramétert csak akkor használja, ha a RangeStart és a RangeEnd közötti időszakban az összes tárolóval kapcsolatos rekordot be kell olvasni. Alapértelmezés szerint a RangeStart és a RangeEnd értéke null, ami azt eredményezi, hogy a függvény csak a legújabb rekordot kéri le az egyes tárolók esetében. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Ezt a paramétert csak akkor használja, ha a RangeStart paramétert csak akkor használja, ha a RangeStart és a RangeEnd közötti időszakban az összes tárolóval kapcsolatos rekordot be kell olvasni. Alapértelmezés szerint a RangeStart és a RangeEnd értéke null, ami azt eredményezi, hogy a függvény csak a legújabb rekordot kéri le az egyes tárolók esetében. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos előfizetések esetében, ahol a biztonsági mentési adatokat megtalálhatók. Az előfizetési azonosítók vesszővel tagolt listájának megadása a függvény paramétereként csak a megadott előfizetésekben lévő tárolók lekérését segíti. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az összes előfizetésben való keresését teszi lehetővé. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos olyan régió esetében, ahol a biztonsági másolat adatai léteznek. A függvények vesszővel tagolt listájának megadása a függvény paraméterének használatával csak a megadott régiókban található tárolókat kérdezheti le. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az összes régióban lévő rekordokra keres rá. | N | "eastus, westus"|
| VaultList    |Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos készlethez. Ha a tár neveinak vesszővel tagolt listáját adja meg, a függvény paramétereként csak a megadott tárolóra vonatkozó rekordok olvashatók be. Alapértelmezés szerint a paraméter értéke "*", amellyel a függvény a rekordok között keresi az összes tárolót. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Ezzel a paraméterrel szűrheti a függvény kimenetét egy adott tár típusához tartozó rekordokra. Jelenleg az egyetlen támogatott tár típus a "Microsoft. Recoveryservices szolgáltatónál/Vaults", amely a paraméter alapértelmezett értéke. | N | "Microsoft. Recoveryservices szolgáltatónál/Vaults"|
| ExcludeLegacyEvent  | Ezzel a paraméterrel adhatja meg, hogy az örökölt AzureDiagnostics táblában kívánja-e lekérdezni az adatlekérdezést. Ha a paraméter értéke false (hamis), a függvény a AzureDiagnostics táblából és az erőforrás-specifikus táblákból is lekérdezi az adatait. Ha a paraméter értéke TRUE (igaz), a függvény csak az erőforrás-specifikus táblákból kérdez le adatokból. Az alapértelmezett érték true (igaz). | N | true |

**Visszaadott mezők**

| **Mező neve** | **Leírás** |
| -------------- | --------------- |
| UniqueId | A tár egyedi AZONOSÍTÓját jelölő elsődleges kulcs |
| Id | A tár Azure Resource Manager (ARM) azonosítója |
| Name | A tároló neve |
| SubscriptionId | Annak az előfizetésnek az azonosítója, amelyben a tároló létezik |
| Hely | A tároló létezésének helye |
| VaultStore_StorageReplicationType | A tárolóhoz társított tárolási replikációs típus |
| Címkék | A tár címkéi |
| TimeGenerated | A rekord időbélyege |
| Típus |  A tároló típusa, amely "Microsoft. Recoveryservices szolgáltatónál/Vaults"|

#### <a name="_azurebackup_getpolicies"></a>_AzureBackup_GetPolicies ()

Ez a függvény visszaadja az Azure-környezetben használt biztonsági mentési szabályzatok listáját, valamint az egyes szabályzatok részletes információit, például az adatforrás típusát, a tárolási replikálás típusát és így tovább.

**Paraméterek**

| **Paraméter neve** | **Leírás** | **Kötelező?** | **Példa értéke** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Ezt a paramétert csak akkor használja, ha a RangeStart paraméterrel együtt szeretné beolvasni az összes házirendtel kapcsolatos rekordot a RangeStart és a RangeEnd közötti időszakban. Alapértelmezés szerint a RangeStart és a RangeEnd értéke null, ami azt eredményezi, hogy a függvény csak a legújabb rekordot kéri le az egyes házirendekhez. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Ezt a paramétert csak akkor használja, ha a RangeStart paramétert csak akkor használja, ha a RangeStart és a RangeEnd közötti időszakban az összes szabályzattal kapcsolatos rekordot be kell olvasni. Alapértelmezés szerint a RangeStart és a RangeEnd értéke null, ami azt eredményezi, hogy a függvény csak a legújabb rekordot kéri le az egyes házirendekhez. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos előfizetések esetében, ahol a biztonsági mentési adatokat megtalálhatók. Az előfizetési azonosítók vesszővel tagolt listájának megadása a függvény paramétereként csak azokat a házirendeket kéri le, amelyek a megadott előfizetésekben szerepelnek. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az összes előfizetésben való keresését teszi lehetővé. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos olyan régió esetében, ahol a biztonsági másolat adatai léteznek. Ha egy vesszővel tagolt régiót ad meg a függvény paraméterként, akkor csak azokat a házirendeket kéri le, amelyek a megadott régiókban találhatók. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az összes régióban lévő rekordokra keres rá. | N | "eastus, westus"|
| VaultList    |Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos készlethez. Ha a tárolók vesszővel tagolt listáját adja meg paraméterként a függvényhez, az csak a megadott tárakhoz tartozó szabályzatok rekordjainak lekérését segíti. Alapértelmezés szerint a paraméter értéke "*", ami lehetővé teszi, hogy a függvény megkeresse a házirendek rekordjait az összes tárban. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Ezzel a paraméterrel szűrheti a függvény kimenetét egy adott tár típusához tartozó rekordokra. Jelenleg az egyetlen támogatott tár típus a "Microsoft. Recoveryservices szolgáltatónál/Vaults", amely a paraméter alapértelmezett értéke. | N | "Microsoft. Recoveryservices szolgáltatónál/Vaults"|
| ExcludeLegacyEvent  | Ezzel a paraméterrel adhatja meg, hogy az örökölt AzureDiagnostics táblában kívánja-e lekérdezni az adatlekérdezést. Ha a paraméter értéke false (hamis), a függvény a AzureDiagnostics táblából és az erőforrás-specifikus táblákból is lekérdezi az adatait. Ha a paraméter értéke TRUE (igaz), a függvény csak az erőforrás-specifikus táblákból kérdez le adatokból. Az alapértelmezett érték true (igaz). | N | true |
| BackupSolutionList | Ezzel a paraméterrel szűrheti a függvény kimenetét az Azure-környezetben használt biztonsági mentési megoldások bizonyos készlete számára. Ha például az "Azure virtuális gép biztonsági mentése, SQL in Azure VM Backup, DPM" értéket adja meg a paraméter értékeként, a függvény csak azokat a rekordokat adja vissza, amelyek az Azure-beli virtuális gépek biztonsági mentésével, az Azure-beli virtuális gép biztonsági mentésével vagy a DPM az Azure Backup szolgáltatással kapcsolatos biztonsági mentéssel kapcsolatban állnak. Alapértelmezés szerint a paraméter értéke "*", amely a biztonsági mentési jelentések által támogatott biztonsági mentési megoldásokhoz tartozó rekordokat ad vissza (a támogatott értékek a következők: "Azure-beli virtuális gép biztonsági mentése", "SQL az Azure virtuális gép biztonsági mentése", "SAP HANA az Azure virtuális gép biztonsági mentése", "Azure Storage (Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server", vagy egy vesszővel elválasztott kombinációja ezen értékek bármelyikének). | N | "Azure-beli virtuális gépek biztonsági mentése, SQL az Azure VM Backup, DPM, Azure Backup Agent" |

**Visszaadott mezők**

| **Mező neve** | **Leírás** |
| -------------- | --------------- |
| UniqueId | A házirend egyedi AZONOSÍTÓját jelölő elsődleges kulcs |
| Id | A szabályzat Azure Resource Manager (ARM) azonosítója |
| Name | A házirend neve |
| Biztonsági mentési megoldás | Az a biztonsági mentési megoldás, amelyhez a házirend társítva van. Ilyen például az Azure virtuális gépek biztonsági mentése, az SQL az Azure-beli virtuális gépek biztonsági mentése és így tovább. |
| TimeGenerated | A rekord időbélyege |
| VaultUniqueId | A Szabályzathoz társított tárolóra hivatkozó külső kulcs |
| VaultResourceId | A Szabályzathoz társított tár Azure Resource Manager (ARM) azonosítója |
| VaultName | A Szabályzathoz társított tár neve |
| VaultTags | A Szabályzathoz társított tár címkéi |
| VaultLocation | A Szabályzathoz társított tár helye |
| VaultSubscriptionId | A Szabályzathoz társított tár előfizetés-azonosítója |
| VaultStore_StorageReplicationType | A házirendhez társított tár tárolási replikációs típusa |
| VaultType | A tároló típusa, amely "Microsoft. Recoveryservices szolgáltatónál/Vaults" |
| Extendedproperties példányt paraméterként | A szabályzat további tulajdonságai |

#### <a name="_azurebackup_getjobs"></a>_AzureBackup_GetJobs ()

Ez a függvény az összes biztonsági mentési és visszaállítási kapcsolódó feladat listáját adja vissza, amelyek egy adott időtartományban lettek aktiválva, valamint részletes információk az egyes feladatokról, például a feladat állapotáról, a feladat időtartamáról, az átvitt adatokról stb.

**Paraméterek**

| **Paraméter neve** | **Leírás** | **Kötelező?** | **Példa értéke** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Használja ezt a paramétert a RangeEnd paraméterrel együtt a RangeStart és a RangeEnd közötti időszakban elindított összes feladat listájának lekéréséhez. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Használja ezt a paramétert a RangeStart paraméterrel együtt a RangeStart és a RangeEnd közötti időszakban elindított összes feladat listájának lekéréséhez. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos előfizetések esetében, ahol a biztonsági mentési adatokat megtalálhatók. Az előfizetési azonosítók vesszővel tagolt listájának megadása a függvény paramétereként csak azokat a feladatokat kérdezi le, amelyek a megadott előfizetésekben lévő tárolókkal vannak társítva. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az összes előfizetésben való keresését teszi lehetővé. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos olyan régió esetében, ahol a biztonsági másolat adatai léteznek. Ha egy vesszővel tagolt régiót ad meg a függvény paraméterként, akkor csak azokat a feladatokat kéri le, amelyek a megadott régiókban található tárolókkal vannak társítva. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az összes régióban lévő rekordokra keres rá. | N | "eastus, westus"|
| VaultList    | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos készlethez. Ha a tárolók vesszővel tagolt listáját adja meg paraméterként, ez a függvény segít beolvasni a csak a megadott tárakra vonatkozó feladatokat. Alapértelmezés szerint ennek a paraméternek az értéke "*", ami lehetővé teszi, hogy a függvény az összes tárolóban keressen feladatokat. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Ezzel a paraméterrel szűrheti a függvény kimenetét egy adott tár típusához tartozó rekordokra. Jelenleg az egyetlen támogatott tár típus a "Microsoft. Recoveryservices szolgáltatónál/Vaults", amely a paraméter alapértelmezett értéke. | N | "Microsoft. Recoveryservices szolgáltatónál/Vaults"|
| ExcludeLegacyEvent  | Ezzel a paraméterrel adhatja meg, hogy az örökölt AzureDiagnostics táblában kívánja-e lekérdezni az adatlekérdezést. Ha a paraméter értéke false (hamis), a függvény a AzureDiagnostics táblából és az erőforrás-specifikus táblákból is lekérdezi az adatait. Ha a paraméter értéke TRUE (igaz), a függvény csak az erőforrás-specifikus táblákból kérdez le adatokból. Az alapértelmezett érték true (igaz). | N | true |
| BackupSolutionList | Ezzel a paraméterrel szűrheti a függvény kimenetét az Azure-környezetben használt biztonsági mentési megoldások bizonyos készlete számára. Ha például az "Azure virtuális gép biztonsági mentése, SQL in Azure VM Backup, DPM" értéket adja meg a paraméter értékeként, a függvény csak azokat a rekordokat adja vissza, amelyek az Azure-beli virtuális gépek biztonsági mentésével, az Azure-beli virtuális gép biztonsági mentésével vagy a DPM az Azure Backup szolgáltatással kapcsolatos biztonsági mentéssel kapcsolatban állnak. Alapértelmezés szerint a paraméter értéke "*", amely a biztonsági mentési jelentések által támogatott biztonsági mentési megoldásokhoz tartozó rekordokat ad vissza (a támogatott értékek a következők: "Azure-beli virtuális gép biztonsági mentése", "SQL az Azure virtuális gép biztonsági mentése", "SAP HANA az Azure virtuális gép biztonsági mentése", "Azure Storage (Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server", vagy egy vesszővel elválasztott kombinációja ezen értékek bármelyikének). | N | "Azure-beli virtuális gépek biztonsági mentése, SQL az Azure VM Backup, DPM, Azure Backup Agent" |
| JobOperationList | Ezzel a paraméterrel szűrheti a függvény kimenetét egy adott típusú feladathoz. Például biztonsági mentés vagy visszaállítás. Alapértelmezés szerint a paraméter értéke "*", amely lehetővé teszi a függvénynek a biztonsági mentési és visszaállítási feladatok keresését. | N | Hát | 
| JobStatusList | Ezzel a paraméterrel szűrheti a függvény kimenetét egy adott feladat állapotára vonatkozóan. Például: befejezett, sikertelen és így tovább. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az állapottól függetlenül keresi az összes feladatot. | N | "Sikertelen, CompletedWithWarnings" |
| JobFailureCodeList | Ezzel a paraméterrel szűrheti a függvény kimenetét egy adott hibakódhoz. Alapértelmezés szerint a paraméter értéke "*", amellyel a függvény az összes feladatra rákeres, a hibakódtól függetlenül. | N | Sikeres
| DatasourceSetName | Ezzel a paraméterrel szűrheti a függvény kimenetét egy adott szülő erőforrásra. Ha például a "testvm" virtuális géphez tartozó Azure-beli virtuális gépek biztonsági mentési példányaiban szeretné visszaállítani az SQL-t, akkor a paraméter értékeként a _testvm_ értéket kell megadnia. Alapértelmezés szerint az érték a "*", amely lehetővé teszi, hogy a függvény az összes biztonsági mentési példányon keressen rekordokat. | N | testvm |
| BackupInstanceName | Ezzel a paraméterrel megkeresheti a feladatokat egy adott biztonsági mentési példányon név alapján. Alapértelmezés szerint az érték a "*", amely lehetővé teszi, hogy a függvény az összes biztonsági mentési példányon keressen rekordokat. | N | testvm |
| ExcludeLog | Ezzel a paraméterrel kizárhatja a függvény által visszaadott naplók feladatait (a lekérdezési teljesítményben segít). Alapértelmezés szerint a paraméter értéke TRUE (igaz), így a függvény kizárja a naplóbeli feladatokat. | N | true


**Visszaadott mezők**

| **Mező neve** | **Leírás** |
| -------------- | --------------- |
| UniqueId | A feladatokhoz tartozó egyedi azonosítót jelölő elsődleges kulcs |
| OperationCategory | Az elvégzendő művelet kategóriája. Például: biztonsági mentés, visszaállítás |
| Művelet | Az elvégzendő művelet részletei. Például: napló (a naplók biztonsági mentéséhez)|
| Állapot | A feladatokhoz tartozó állapot. Például: befejezett, sikertelen, CompletedWithWarnings |
| ErrorTitle | A feladatokhoz tartozó hibakód |
| StartTime | A feladatok elindításának dátuma és időpontja |
| DurationInSecs | A feladattípus időtartama másodpercben |
| DataTransferredInMBs | A feladatokból az MBs-ben továbbított adatok |
| RestoreJobRPDateTime | A helyreállított helyreállítási pont létrehozásának dátuma és időpontja |
| RestoreJobRPLocation | A helyreállított helyreállítási pont tárolási helye |
| BackupInstanceUniqueId | A feladatokhoz társított biztonsági mentési példányra hivatkozó külső kulcs |
| BackupInstanceId | A feladatokhoz társított biztonsági mentési példány Azure Resource Manager (ARM) azonosítója |
| BackupInstanceFriendlyName | A feladatokhoz társított biztonsági mentési példány neve |
| DatasourceResourceId | A feladathoz társított mögöttes adatforrás Azure Resource Manager (ARM) azonosítója. A virtuális gép Azure Resource Manager (ARM) azonosítója például |
| DatasourceFriendlyName | A feladathoz társított mögöttes adatforrás rövid neve |
| DatasourceType | A feladatokhoz társított adatforrás típusa. Például "Microsoft. számítás/virtualMachines" |
| BackupSolution | A biztonsági mentési megoldás, amelyhez a feladattípus társítva van. Ilyen például az Azure virtuális gépek biztonsági mentése, az SQL az Azure-beli virtuális gépek biztonsági mentése és így tovább. |
| DatasourceSetResourceId | Az adatforrás szülő erőforrásának Azure Resource Manager (ARM) azonosítója (ahol alkalmazható). Ha például egy Azure-beli virtuális gép adatforrásához tartozik egy SQL, akkor ez a mező tartalmazza annak a virtuális gépnek a Azure Resource Manager (ARM) AZONOSÍTÓját, amelyben a SQL Database létezik |
| DatasourceSetType | Az adatforrás szülő erőforrásának típusa (ahol alkalmazható). Ha például egy Azure-beli virtuális gép adatforrása SAP HANA, ez a mező a Microsoft. számítási/virtualMachines lesz, mivel a szülő erőforrás egy Azure-beli virtuális gép |
| VaultResourceId | A feladatokhoz társított tár Azure Resource Manager (ARM) azonosítója |
| VaultUniqueId | A feladatokhoz társított tárolóra hivatkozó külső kulcs |
| VaultName | A feladatokhoz társított tár neve |
| VaultTags | A feladatokhoz társított tár címkéi |
| VaultSubscriptionId | A feladatokhoz társított tár előfizetés-azonosítója |
| VaultLocation | A feladatokhoz társított tár helye |
| VaultStore_StorageReplicationType | A feladatokhoz társított tár tárolási replikációs típusa |
| VaultType | A tároló típusa, amely "Microsoft. Recoveryservices szolgáltatónál/Vaults" |
| TimeGenerated | A rekord időbélyege |

#### <a name="_azurebackup_getbackupinstances"></a>_AzureBackup_GetBackupInstances ()

Ez a függvény visszaadja a Recovery Services-tárolóhoz társított biztonsági mentési példányok listáját, valamint az egyes biztonsági másolatok részletes információit, például a Felhőbeli tárterület felhasználását, a kapcsolódó házirendet stb.

**Paraméterek**

| **Paraméter neve** | **Leírás** | **Kötelező?** | **Példa értéke** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Ezt a paramétert csak akkor használja, ha a RangeEnd paramétert csak akkor használja, ha a RangeStart és a RangeEnd közötti időszakban az összes biztonsági mentési példányhoz kapcsolódó rekordot be kell olvasni. Alapértelmezés szerint a RangeStart és a RangeEnd értéke null, ami azt eredményezi, hogy a függvény csak az egyes biztonsági mentési példányok legújabb rekordját kéri le. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Ezt a paramétert csak akkor használja, ha a RangeStart paramétert csak akkor használja, ha a RangeStart és a RangeEnd közötti időszakban az összes biztonsági mentési példányhoz kapcsolódó rekordot be kell olvasni. Alapértelmezés szerint a RangeStart és a RangeEnd értéke null, ami azt eredményezi, hogy a függvény csak az egyes biztonsági mentési példányok legújabb rekordját kéri le. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos előfizetések esetében, ahol a biztonsági mentési adatokat megtalálhatók. Az előfizetési azonosítók vesszővel tagolt listájának megadása a függvény paramétereként csak azokat a biztonsági mentési példányokat kéri le, amelyek a megadott előfizetésekben találhatók. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az összes előfizetésben való keresését teszi lehetővé. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos olyan régió esetében, ahol a biztonsági másolat adatai léteznek. Ha egy vesszővel tagolt régiót ad meg a függvény paraméterként, akkor csak azokat a biztonsági mentési példányokat kéri le, amelyek a megadott régiókban találhatók. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az összes régióban lévő rekordokra keres rá. | N | "eastus, westus"|
| VaultList    |Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos készlethez. Ha a tárolók nevének vesszővel tagolt listáját adja meg, a függvény paramétereként a csak a megadott tárakra vonatkozó biztonsági mentési példányok rekordjait kérdezheti le. Alapértelmezés szerint a paraméter értéke "*", ami lehetővé teszi, hogy a függvény a biztonsági mentési példányok rekordjait keresse meg az összes tárban. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Ezzel a paraméterrel szűrheti a függvény kimenetét egy adott tár típusához tartozó rekordokra. Jelenleg az egyetlen támogatott tár típus a "Microsoft. Recoveryservices szolgáltatónál/Vaults", amely a paraméter alapértelmezett értéke. | N | "Microsoft. Recoveryservices szolgáltatónál/Vaults"|
| ExcludeLegacyEvent  | Ezzel a paraméterrel adhatja meg, hogy az örökölt AzureDiagnostics táblában kívánja-e lekérdezni az adatlekérdezést. Ha a paraméter értéke false (hamis), a függvény a AzureDiagnostics táblából és az erőforrás-specifikus táblákból is lekérdezi az adatait. Ha a paraméter értéke TRUE (igaz), a függvény csak az erőforrás-specifikus táblákból kérdez le adatokból. Az alapértelmezett érték true (igaz). | N | true |
| BackupSolutionList | Ezzel a paraméterrel szűrheti a függvény kimenetét az Azure-környezetben használt biztonsági mentési megoldások bizonyos készlete számára. Ha például az "Azure virtuális gép biztonsági mentése, SQL in Azure VM Backup, DPM" értéket adja meg a paraméter értékeként, a függvény csak azokat a rekordokat adja vissza, amelyek az Azure-beli virtuális gépek biztonsági mentésével, az Azure-beli virtuális gép biztonsági mentésével vagy a DPM az Azure Backup szolgáltatással kapcsolatos biztonsági mentéssel kapcsolatban állnak. Alapértelmezés szerint a paraméter értéke "*", amely a biztonsági mentési jelentések által támogatott biztonsági mentési megoldásokhoz tartozó rekordokat ad vissza (a támogatott értékek a következők: "Azure-beli virtuális gép biztonsági mentése", "SQL az Azure virtuális gép biztonsági mentése", "SAP HANA az Azure virtuális gép biztonsági mentése", "Azure Storage (Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server", vagy egy vesszővel elválasztott kombinációja ezen értékek bármelyikének). | N | "Azure-beli virtuális gépek biztonsági mentése, SQL az Azure VM Backup, DPM, Azure Backup Agent" |
| ProtectionInfoList | Ezzel a paraméterrel adhatja meg, hogy csak azokat a biztonsági mentési példányokat kívánja-e felvenni, amelyeknek aktív a védelme, vagy olyan példányokat is, amelyekhez a rendszer leállította a védelmet, valamint azokat a példányokat, amelyeken A támogatott értékek a következők egyike: "Protected", "ProtectionStopped", "InitialBackupPending" vagy vesszővel tagolt kombinációja. Alapértelmezés szerint az érték a "*", amely a védelem részleteitől függetlenül megkeresi a függvényt az összes biztonsági mentési példányra. | N | Védett |
| DatasourceSetName | Ezzel a paraméterrel szűrheti a függvény kimenetét egy adott szülő erőforrásra. Ha például a "testvm" virtuális géphez tartozó Azure-beli virtuális gépek biztonsági mentési példányaiban szeretné visszaállítani az SQL-t, akkor a paraméter értékeként a _testvm_ értéket kell megadnia. Alapértelmezés szerint az érték a "*", amely lehetővé teszi, hogy a függvény az összes biztonsági mentési példányon keressen rekordokat. | N | testvm |
| BackupInstanceName | Ezzel a paraméterrel megkeresheti egy adott biztonsági mentési példányt név alapján. Alapértelmezés szerint az érték "*", amely a függvénynek az összes biztonsági mentési példányra való keresését végzi. | N | testvm |
| DisplayAllFields | Ezzel a paraméterrel adhatja meg, hogy csak a függvény által visszaadott mezők egy részhalmazát kívánja-e lekérni. Ha a paraméter értéke false (hamis), akkor a függvény eltávolítja a tárolási és adatmegőrzési pont kapcsolódó információit a függvény kimenetéről. Ez akkor hasznos, ha a függvényt egy nagyobb lekérdezés közbenső lépéseként használja, és a lekérdezés teljesítményének optimalizálásához olyan oszlopokat távolít el, amelyek nem szükségesek az elemzéshez. Alapértelmezés szerint a paraméter értéke TRUE (igaz), ami lehetővé teszi, hogy a függvény a biztonsági mentési példányhoz tartozó összes mezőt visszaállítsa. | N | true |

**Visszaadott mezők**

| **Mező neve** | **Leírás** |
| -------------- | --------------- |
| UniqueId | A biztonsági mentési példány egyedi AZONOSÍTÓját jelölő elsődleges kulcs |
| Id | A biztonsági mentési példány Azure Resource Manager (ARM) azonosítója |
| FriendlyName | A biztonsági mentési példány rövid neve |
| ProtectionInfo | Információk a biztonsági mentési példány védelmi beállításairól. Például a védelem konfigurálva, a védelem leállt, a kezdeti biztonsági mentés függőben van |
| LatestRecoveryPoint | A biztonsági mentési példányhoz társított legutóbbi helyreállítási pont dátuma és időpontja |
| OldestRecoveryPoint | A biztonsági mentési példányhoz társított legrégebbi helyreállítási pont dátuma és időpontja |
| SourceSizeInMBs | A biztonsági mentési példány előtérbeli mérete (MB) |
| VaultStore_StorageConsumptionInMBs | A tároló standard szintű biztonsági mentési példánya által felhasznált felhőalapú tárterület összesen |
| DataSourceFriendlyName | A biztonsági mentési példányhoz tartozó adatforrás rövid neve |
| BackupSolution | Biztonsági mentési megoldás, amelyhez a biztonsági mentési példány társítva van. Ilyen például az Azure virtuális gépek biztonsági mentése, az SQL az Azure-beli virtuális gépek biztonsági mentése és így tovább. |
| DatasourceType | A biztonsági mentési példánynak megfelelő adatforrás típusa. Például "Microsoft. számítás/virtualMachines" |
| DatasourceResourceId | A biztonsági mentési példányhoz tartozó mögöttes adatforrás Azure Resource Manager (ARM) azonosítója. A virtuális gép Azure Resource Manager (ARM) azonosítója például |
| DatasourceSetFriendlyName | Az adatforrás szülő erőforrásának rövid neve (ahol alkalmazható). Ha például egy Azure-beli virtuális gép adatforrásához tartozik egy SQL, akkor ez a mező tartalmazza annak a virtuális gépnek a nevét, amelyben a SQL Database létezik |
| DatasourceSetResourceId | Az adatforrás szülő erőforrásának Azure Resource Manager (ARM) azonosítója (ahol alkalmazható). Ha például egy Azure-beli virtuális gép adatforrásához tartozik egy SQL, akkor ez a mező tartalmazza annak a virtuális gépnek a Azure Resource Manager (ARM) AZONOSÍTÓját, amelyben a SQL Database létezik |
| DatasourceSetType | Az adatforrás szülő erőforrásának típusa (ahol alkalmazható). Ha például egy Azure-beli virtuális gép adatforrása SAP HANA, ez a mező a Microsoft. számítási/virtualMachines lesz, mivel a szülő erőforrás egy Azure-beli virtuális gép |
| PolicyName | A biztonsági mentési példányhoz társított házirend neve |
| PolicyUniqueId | A biztonsági mentési példánnyal társított szabályzatra hivatkozó külső kulcs  |
| PolicyId | A biztonsági mentési példánnyal társított szabályzat Azure Resource Manager (ARM) azonosítója |
| VaultResourceId | A biztonsági mentési példányhoz társított tár Azure Resource Manager (ARM) azonosítója |
| VaultUniqueId | A biztonsági mentési példánnyal társított tárolóra hivatkozó külső kulcs |
| VaultName | A biztonsági mentési példányhoz társított tár neve |
| VaultTags | A biztonsági mentési példánnyal társított tár címkéi |
| VaultSubscriptionId | A biztonsági mentési példányhoz társított tár előfizetés-azonosítója |
| VaultLocation | A biztonsági mentési példányhoz társított tár helye |
| VaultStore_StorageReplicationType | A biztonsági másolathoz társított tároló tárolási replikációs típusa |
| VaultType | A tároló típusa, amely "Microsoft. Recoveryservices szolgáltatónál/Vaults" |
| TimeGenerated | A rekord időbélyege |

#### <a name="_azurebackup_getbillinggroups"></a>_AzureBackup_GetBillingGroups ()

Ez a függvény az összes biztonsági mentéssel kapcsolatos számlázási entitás (számlázási csoportok) listáját adja vissza, valamint a legfontosabb számlázási összetevőkre vonatkozó információkat, például a frontend méretét és a teljes felhőalapú tárolást.

**Paraméterek**

| **Paraméter neve** | **Leírás** | **Kötelező?** | **Példa értéke** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Ezt a paramétert csak akkor használja, ha a RangeEnd paramétert csak akkor használja, ha a RangeStart és a RangeEnd közötti időszakban az összes számlázási csoporttal kapcsolatos rekordot be kell olvasni. Alapértelmezés szerint a RangeStart és a RangeEnd értéke null, ami azt eredményezi, hogy a függvény csak az egyes számlázási csoportok legújabb rekordját kéri le. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Ezt a paramétert csak akkor használja, ha a RangeStart paramétert csak akkor használja, ha a RangeStart és a RangeEnd közötti időszakban az összes számlázási csoporttal kapcsolatos rekordot be kell olvasni. Alapértelmezés szerint a RangeStart és a RangeEnd értéke null, ami azt eredményezi, hogy a függvény csak az egyes számlázási csoportok legújabb rekordját kéri le. | N |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos előfizetések esetében, ahol a biztonsági mentési adatokat megtalálhatók. Az előfizetési azonosítók vesszővel tagolt listájának megadása a függvény paramétereként csak azokat a számlázási csoportokat kéri le, amelyek szerepelnek a megadott előfizetésekben. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az összes előfizetésben való keresését teszi lehetővé. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos olyan régió esetében, ahol a biztonsági másolat adatai léteznek. Ha egy vesszővel tagolt régiót ad meg a függvény paraméterként, akkor csak azokat a számlázási csoportokat kéri le, amelyek a megadott régiókban találhatók. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az összes régióban lévő rekordokra keres rá. | N | "eastus, westus"|
| VaultList    |Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos készlethez. Ha a tárolók nevének vesszővel tagolt listáját adja meg, a függvény paramétereként a csak a megadott tárakra vonatkozó biztonsági mentési példányok rekordjait kérdezheti le. Alapértelmezés szerint a paraméter értéke "*", ami lehetővé teszi, hogy a függvény a számlázási csoportok rekordjait keresse meg az összes tárban. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Ezzel a paraméterrel szűrheti a függvény kimenetét egy adott tár típusához tartozó rekordokra. Jelenleg az egyetlen támogatott tár típus a "Microsoft. Recoveryservices szolgáltatónál/Vaults", amely a paraméter alapértelmezett értéke. | N | "Microsoft. Recoveryservices szolgáltatónál/Vaults"|
| ExcludeLegacyEvent  | Ezzel a paraméterrel adhatja meg, hogy az örökölt AzureDiagnostics táblában kívánja-e lekérdezni az adatlekérdezést. Ha a paraméter értéke false (hamis), a függvény a AzureDiagnostics táblából és az erőforrás-specifikus táblákból is lekérdezi az adatait. Ha a paraméter értéke TRUE (igaz), a függvény csak az erőforrás-specifikus táblákból kérdez le adatokból. Az alapértelmezett érték true (igaz). | N | true |
| BackupSolutionList | Ezzel a paraméterrel szűrheti a függvény kimenetét az Azure-környezetben használt biztonsági mentési megoldások bizonyos készlete számára. Ha például az "Azure virtuális gép biztonsági mentése, SQL in Azure VM Backup, DPM" értéket adja meg a paraméter értékeként, a függvény csak azokat a rekordokat adja vissza, amelyek az Azure-beli virtuális gépek biztonsági mentésével, az Azure-beli virtuális gép biztonsági mentésével vagy a DPM az Azure Backup szolgáltatással kapcsolatos biztonsági mentéssel kapcsolatban állnak. Alapértelmezés szerint a paraméter értéke "*", amely a biztonsági mentési jelentések által támogatott biztonsági mentési megoldásokhoz tartozó rekordokat ad vissza (a támogatott értékek a következők: "Azure-beli virtuális gép biztonsági mentése", "SQL az Azure virtuális gép biztonsági mentése", "SAP HANA az Azure virtuális gép biztonsági mentése", "Azure Storage (Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server", vagy egy vesszővel elválasztott kombinációja ezen értékek bármelyikének). | N | "Azure-beli virtuális gépek biztonsági mentése, SQL az Azure VM Backup, DPM, Azure Backup Agent" |
| BillingGroupName | Ezzel a paraméterrel megkeresheti egy adott számlázási csoportot név szerint. Alapértelmezés szerint az érték a "*", amely a függvény keresését végzi az összes számlázási csoportnál. | N | testvm |

**Visszaadott mezők**

| **Mező neve** | **Leírás** |
| -------------- | --------------- |
| UniqueId | A számlázási csoport egyedi AZONOSÍTÓját jelölő elsődleges kulcs |
| FriendlyName | A számlázási csoport rövid neve |
| Name | A számlázási csoport neve |
| Típus | A számlázási csoport típusa. Például: ProtectedContainer vagy BackupItem |
| SourceSizeInMBs | A számlázási csoport frontend-mérete (MB) |
| VaultStore_StorageConsumptionInMBs | A standard szintű számlázási csoport által felhasznált Felhőbeli tárterület összesen |
| BackupSolution | Az a biztonsági mentési megoldás, amelyhez a számlázási csoport társítva van. Ilyen például az Azure virtuális gépek biztonsági mentése, az SQL az Azure-beli virtuális gépek biztonsági mentése és így tovább. |
| VaultResourceId | A számlázási csoporthoz társított tár Azure Resource Manager (ARM) azonosítója |
| VaultUniqueId | A számlázási csoporthoz társított tárolóra hivatkozó külső kulcs |
| VaultName | A számlázási csoporthoz társított tár neve |
| VaultTags | A számlázási csoporthoz társított tár címkéi |
| VaultSubscriptionId | A számlázási csoporthoz társított tár előfizetés-azonosítója |
| VaultLocation | A számlázási csoporthoz társított tár helye |
| VaultStore_StorageReplicationType | A számlázási csoporthoz társított tár tárolási replikációs típusa |
| VaultType | A tároló típusa, amely "Microsoft. Recoveryservices szolgáltatónál/Vaults" |
| TimeGenerated | A rekord időbélyege |
| Extendedproperties példányt paraméterként | A számlázási csoport további tulajdonságai |

### <a name="trend-functions"></a>Trend függvények

#### <a name="_azurebackup_getbackupinstancestrends"></a>_AzureBackup_GetBackupInstancesTrends ()

Ez a függvény az egyes biztonsági mentési példányok korábbi rekordjait adja vissza, így megtekintheti a biztonsági másolatok számával és a tárterület-használattal kapcsolatos napi, heti és havi trendeket, több részletességi szinten.

**Paraméterek**

| **Paraméter neve** | **Leírás** | **Kötelező?** | **Példa értéke** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Használja ezt a paramétert a RangeEnd paraméterrel együtt a RangeStart és a RangeEnd közötti időszakban az összes biztonsági mentési példányhoz kapcsolódó rekord lekéréséhez. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Használja ezt a paramétert a RangeStart paraméterrel együtt a RangeStart és a RangeEnd közötti időszakban az összes biztonsági mentési példányhoz kapcsolódó rekord lekéréséhez. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList   | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos előfizetések esetében, ahol a biztonsági mentési adatokat megtalálhatók. Az előfizetési azonosítók vesszővel tagolt listájának megadása a függvény paramétereként csak azokat a biztonsági mentési példányokat kéri le, amelyek a megadott előfizetésekben találhatók. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az összes előfizetésben való keresését teszi lehetővé. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos olyan régió esetében, ahol a biztonsági másolat adatai léteznek. Ha egy vesszővel tagolt régiót ad meg a függvény paraméterként, akkor csak azokat a biztonsági mentési példányokat kéri le, amelyek a megadott régiókban találhatók. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az összes régióban lévő rekordokra keres rá. | N | "eastus, westus"|
| VaultList    |Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos készlethez. Ha a tárolók nevének vesszővel tagolt listáját adja meg, a függvény paramétereként a csak a megadott tárakra vonatkozó biztonsági mentési példányok rekordjait kérdezheti le. Alapértelmezés szerint a paraméter értéke "*", ami lehetővé teszi, hogy a függvény a biztonsági mentési példányok rekordjait keresse meg az összes tárban. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Ezzel a paraméterrel szűrheti a függvény kimenetét egy adott tár típusához tartozó rekordokra. Jelenleg az egyetlen támogatott tár típus a "Microsoft. Recoveryservices szolgáltatónál/Vaults", amely a paraméter alapértelmezett értéke. | N | "Microsoft. Recoveryservices szolgáltatónál/Vaults"|
| ExcludeLegacyEvent  | Ezzel a paraméterrel adhatja meg, hogy az örökölt AzureDiagnostics táblában kívánja-e lekérdezni az adatlekérdezést. Ha a paraméter értéke false (hamis), a függvény a AzureDiagnostics táblából és az erőforrás-specifikus táblákból is lekérdezi az adatait. Ha a paraméter értéke TRUE (igaz), a függvény csak az erőforrás-specifikus táblákból kérdez le adatokból. Az alapértelmezett érték true (igaz). | N | true |
| BackupSolutionList | Ezzel a paraméterrel szűrheti a függvény kimenetét az Azure-környezetben használt biztonsági mentési megoldások bizonyos készlete számára. Ha például az "Azure virtuális gép biztonsági mentése, SQL in Azure VM Backup, DPM" értéket adja meg a paraméter értékeként, a függvény csak azokat a rekordokat adja vissza, amelyek az Azure-beli virtuális gépek biztonsági mentésével, az Azure-beli virtuális gép biztonsági mentésével vagy a DPM az Azure Backup szolgáltatással kapcsolatos biztonsági mentéssel kapcsolatban állnak. Alapértelmezés szerint a paraméter értéke "*", amely a biztonsági mentési jelentések által támogatott biztonsági mentési megoldásokhoz tartozó rekordokat ad vissza (a támogatott értékek a következők: "Azure-beli virtuális gép biztonsági mentése", "SQL az Azure virtuális gép biztonsági mentése", "SAP HANA az Azure virtuális gép biztonsági mentése", "Azure Storage (Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server", vagy egy vesszővel elválasztott kombinációja ezen értékek bármelyikének). | N | "Azure-beli virtuális gépek biztonsági mentése, SQL az Azure VM Backup, DPM, Azure Backup Agent" |
| ProtectionInfoList | Ezzel a paraméterrel adhatja meg, hogy csak azokat a biztonsági mentési példányokat kívánja-e felvenni, amelyeknek aktív a védelme, vagy olyan példányokat is, amelyekhez a rendszer leállította a védelmet, valamint azokat a példányokat, amelyeken A támogatott értékek a következők egyike: "Protected", "ProtectionStopped", "InitialBackupPending" vagy vesszővel tagolt kombinációja. Alapértelmezés szerint az érték a "*", amely a védelem részleteitől függetlenül megkeresi a függvényt az összes biztonsági mentési példányra. | N | Védett |
| DatasourceSetName | Ezzel a paraméterrel szűrheti a függvény kimenetét egy adott szülő erőforrásra. Ha például a "testvm" virtuális géphez tartozó Azure-beli virtuális gépek biztonsági mentési példányaiban szeretné visszaállítani az SQL-t, akkor a paraméter értékeként a _testvm_ értéket kell megadnia. Alapértelmezés szerint az érték a "*", amely lehetővé teszi, hogy a függvény az összes biztonsági mentési példányon keressen rekordokat. | N | testvm |
| BackupInstanceName | Ezzel a paraméterrel megkeresheti egy adott biztonsági mentési példányt név alapján. Alapértelmezés szerint az érték "*", amely a függvénynek az összes biztonsági mentési példányra való keresését végzi. | N | testvm |
| DisplayAllFields | Ezzel a paraméterrel adhatja meg, hogy csak a függvény által visszaadott mezők egy részhalmazát kívánja-e lekérni. Ha a paraméter értéke false (hamis), akkor a függvény eltávolítja a tárolási és adatmegőrzési pont kapcsolódó információit a függvény kimenetéről. Ez akkor hasznos, ha a függvényt egy nagyobb lekérdezés közbenső lépéseként használja, és a lekérdezés teljesítményének optimalizálásához olyan oszlopokat távolít el, amelyek nem szükségesek az elemzéshez. Alapértelmezés szerint a paraméter értéke TRUE (igaz), ami lehetővé teszi, hogy a függvény a biztonsági mentési példányhoz tartozó összes mezőt visszaállítsa. | N | true |
| AggregationType | Ezzel a paraméterrel adhatja meg, hogy milyen időrészletességgel kell lekérni az adatokat. Ha a paraméter értéke "Daily", a függvény naponta egy rekordot ad vissza, amely lehetővé teszi a tárterület-felhasználás és a biztonsági másolatok számának napi tendenciáinak elemzését. Ha a paraméter értéke "Weekly", a függvény hetente ad vissza egy rekordot, amely lehetővé teszi a heti trendek elemzését. Hasonlóképpen, a havi trendek elemzéséhez a "havi" lehetőséget is megadhatja. Az alapértelmezett érték a "Daily". Ha több időtartományban tekinti meg az adatokat, ajánlott a "hetente" vagy "havonta" használni a jobb lekérdezési teljesítményhez és a könnyű trendek elemzéséhez. | N | Heti |

**Visszaadott mezők**

| **Mező neve** | **Leírás** |
| -------------- | --------------- |
| UniqueId | A biztonsági mentési példány egyedi AZONOSÍTÓját jelölő elsődleges kulcs |
| Id | A biztonsági mentési példány Azure Resource Manager (ARM) azonosítója |
| FriendlyName | A biztonsági mentési példány rövid neve |
| ProtectionInfo | Információk a biztonsági mentési példány védelmi beállításairól. Például a védelem konfigurálva, a védelem leállt, a kezdeti biztonsági mentés függőben van |
| LatestRecoveryPoint | A biztonsági mentési példányhoz társított legutóbbi helyreállítási pont dátuma és időpontja |
| OldestRecoveryPoint | A biztonsági mentési példányhoz társított legrégebbi helyreállítási pont dátuma és időpontja |
| SourceSizeInMBs | A biztonsági mentési példány előtérbeli mérete (MB) |
| VaultStore_StorageConsumptionInMBs | A tároló standard szintű biztonsági mentési példánya által felhasznált felhőalapú tárterület összesen |
| DataSourceFriendlyName | A biztonsági mentési példányhoz tartozó adatforrás rövid neve |
| BackupSolution | Biztonsági mentési megoldás, amelyhez a biztonsági mentési példány társítva van. Ilyen például az Azure virtuális gépek biztonsági mentése, az SQL az Azure-beli virtuális gépek biztonsági mentése és így tovább. |
| DatasourceType | A biztonsági mentési példánynak megfelelő adatforrás típusa. Például "Microsoft. számítás/virtualMachines" |
| DatasourceResourceId | A biztonsági mentési példányhoz tartozó mögöttes adatforrás Azure Resource Manager (ARM) azonosítója. A virtuális gép Azure Resource Manager (ARM) azonosítója például |
| DatasourceSetFriendlyName | Az adatforrás szülő erőforrásának rövid neve (ahol alkalmazható). Ha például egy Azure-beli virtuális gép adatforrásához tartozik egy SQL, akkor ez a mező tartalmazza annak a virtuális gépnek a nevét, amelyben a SQL Database létezik |
| DatasourceSetResourceId | Az adatforrás szülő erőforrásának Azure Resource Manager (ARM) azonosítója (ahol alkalmazható). Ha például egy Azure-beli virtuális gép adatforrásához tartozik egy SQL, akkor ez a mező tartalmazza annak a virtuális gépnek a Azure Resource Manager (ARM) AZONOSÍTÓját, amelyben a SQL Database létezik |
| DatasourceSetType | Az adatforrás szülő erőforrásának típusa (ahol alkalmazható). Ha például egy Azure-beli virtuális gép adatforrása SAP HANA, ez a mező a Microsoft. számítási/virtualMachines lesz, mivel a szülő erőforrás egy Azure-beli virtuális gép |
| PolicyName | A biztonsági mentési példányhoz társított házirend neve |
| PolicyUniqueId | A biztonsági mentési példánnyal társított szabályzatra hivatkozó külső kulcs  |
| PolicyId | A biztonsági mentési példánnyal társított szabályzat Azure Resource Manager (ARM) azonosítója |
| VaultResourceId | A biztonsági mentési példányhoz társított tár Azure Resource Manager (ARM) azonosítója |
| VaultUniqueId | A biztonsági mentési példánnyal társított tárolóra hivatkozó külső kulcs |
| VaultName | A biztonsági mentési példányhoz társított tár neve |
| VaultTags | A biztonsági mentési példánnyal társított tár címkéi |
| VaultSubscriptionId | A biztonsági mentési példányhoz társított tár előfizetés-azonosítója |
| VaultLocation | A biztonsági mentési példányhoz társított tár helye |
| VaultStore_StorageReplicationType | A biztonsági másolathoz társított tároló tárolási replikációs típusa |
| VaultType | A tároló típusa, amely "Microsoft. Recoveryservices szolgáltatónál/Vaults" |
| TimeGenerated | A rekord időbélyege |

#### <a name="_azurebackup_getbillinggroupstrends"></a>_AzureBackup_GetBillingGroupsTrends ()

Ez a függvény az egyes számlázási entitások korábbi rekordjait adja vissza, így több részletességi szinten tekintheti meg a alapszintű és a tárhely-használattal kapcsolatos napi, heti és havi trendeket.

**Paraméterek**

| **Paraméter neve** | **Leírás** | **Kötelező?** | **Példa értéke** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Használja ezt a paramétert a RangeEnd paraméterrel együtt a RangeStart és a RangeEnd közötti időszakban az összes számlázási csoporttal kapcsolatos rekord lekéréséhez. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Használja ezt a paramétert a RangeStart paraméterrel együtt a RangeStart és a RangeEnd közötti időszakban az összes számlázási csoporttal kapcsolatos rekord lekéréséhez. | Y |"2021-03-10 00:00:00"|
| VaultSubscriptionList  | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos előfizetések esetében, ahol a biztonsági mentési adatokat megtalálhatók. Az előfizetési azonosítók vesszővel tagolt listájának megadása a függvény paramétereként csak azokat a számlázási csoportokat kéri le, amelyek szerepelnek a megadott előfizetésekben. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az összes előfizetésben való keresését teszi lehetővé. | N | "00000000-0000-0000-0000-000000000000, 11111111-1111-1111-1111-111111111111"|
| VaultLocationList     | Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos olyan régió esetében, ahol a biztonsági másolat adatai léteznek. Ha egy vesszővel tagolt régiót ad meg a függvény paraméterként, akkor csak azokat a számlázási csoportokat kéri le, amelyek a megadott régiókban találhatók. Alapértelmezés szerint a paraméter értéke "*", amely a függvénynek az összes régióban lévő rekordokra keres rá. | N | "eastus, westus"|
| VaultList    |Ezzel a paraméterrel szűrheti a függvény kimenetét egy bizonyos készlethez. Ha a tárolók nevének vesszővel tagolt listáját adja meg, a függvény paramétereként a csak a megadott tárakra vonatkozó biztonsági mentési példányok rekordjait kérdezheti le. Alapértelmezés szerint a paraméter értéke "*", ami lehetővé teszi, hogy a függvény a számlázási csoportok rekordjait keresse meg az összes tárban. | N |"vault1, vault2, vault3"|
| VaultTypeList     | Ezzel a paraméterrel szűrheti a függvény kimenetét egy adott tár típusához tartozó rekordokra. Jelenleg az egyetlen támogatott tár típus a "Microsoft. Recoveryservices szolgáltatónál/Vaults", amely a paraméter alapértelmezett értéke. | N | "Microsoft. Recoveryservices szolgáltatónál/Vaults"|
| ExcludeLegacyEvent  | Ezzel a paraméterrel adhatja meg, hogy az örökölt AzureDiagnostics táblában kívánja-e lekérdezni az adatlekérdezést. Ha a paraméter értéke false (hamis), a függvény a AzureDiagnostics táblából és az erőforrás-specifikus táblákból is lekérdezi az adatait. Ha a paraméter értéke TRUE (igaz), a függvény csak az erőforrás-specifikus táblákból kérdez le adatokból. Az alapértelmezett érték true (igaz). | N | true |
| BackupSolutionList | Ezzel a paraméterrel szűrheti a függvény kimenetét az Azure-környezetben használt biztonsági mentési megoldások bizonyos készlete számára. Ha például az "Azure virtuális gép biztonsági mentése, SQL in Azure VM Backup, DPM" értéket adja meg a paraméter értékeként, a függvény csak azokat a rekordokat adja vissza, amelyek az Azure-beli virtuális gépek biztonsági mentésével, az Azure-beli virtuális gép biztonsági mentésével vagy a DPM az Azure Backup szolgáltatással kapcsolatos biztonsági mentéssel kapcsolatban állnak. Alapértelmezés szerint a paraméter értéke "*", amely a biztonsági mentési jelentések által támogatott biztonsági mentési megoldásokhoz tartozó rekordokat ad vissza (a támogatott értékek a következők: "Azure-beli virtuális gép biztonsági mentése", "SQL az Azure virtuális gép biztonsági mentése", "SAP HANA az Azure virtuális gép biztonsági mentése", "Azure Storage (Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server", vagy egy vesszővel elválasztott kombinációja ezen értékek bármelyikének). | N | "Azure-beli virtuális gépek biztonsági mentése, SQL az Azure VM Backup, DPM, Azure Backup Agent" |
| BillingGroupName | Ezzel a paraméterrel megkeresheti egy adott számlázási csoportot név szerint. Alapértelmezés szerint az érték a "*", amely a függvény keresését végzi az összes számlázási csoportnál. | N | testvm |
| AggregationType | Ezzel a paraméterrel adhatja meg, hogy milyen időrészletességgel kell lekérni az adatokat. Ha a paraméter értéke "naponta", a függvény naponta egy rekordot ad vissza, amely lehetővé teszi a tárterület-felhasználás és a előtér-méret napi trendjeinek elemzését. Ha a paraméter értéke "Weekly", a függvény hetente ad vissza egy rekordot, amely lehetővé teszi a heti trendek elemzését. Hasonlóképpen, a havi trendek elemzéséhez a "havi" lehetőséget is megadhatja. Az alapértelmezett érték a "Daily". Ha több időtartományban tekinti meg az adatokat, ajánlott a "hetente" vagy "havonta" használni a jobb lekérdezési teljesítményhez és a könnyű trendek elemzéséhez. | N | Heti |

**Visszaadott mezők**

| **Mező neve** | **Leírás** |
| -------------- | --------------- |
| UniqueId | A számlázási csoport egyedi AZONOSÍTÓját jelölő elsődleges kulcs |
| FriendlyName | A számlázási csoport rövid neve |
| Name | A számlázási csoport neve |
| Típus | A számlázási csoport típusa. Például: ProtectedContainer vagy BackupItem |
| SourceSizeInMBs | A számlázási csoport frontend-mérete (MB) |
| VaultStore_StorageConsumptionInMBs | A standard szintű számlázási csoport által felhasznált Felhőbeli tárterület összesen |
| BackupSolution | Az a biztonsági mentési megoldás, amelyhez a számlázási csoport társítva van. Ilyen például az Azure virtuális gépek biztonsági mentése, az SQL az Azure-beli virtuális gépek biztonsági mentése és így tovább. |
| VaultResourceId | A számlázási csoporthoz társított tár Azure Resource Manager (ARM) azonosítója |
| VaultUniqueId | A számlázási csoporthoz társított tárolóra hivatkozó külső kulcs |
| VaultName | A számlázási csoporthoz társított tár neve |
| VaultTags | A számlázási csoporthoz társított tár címkéi |
| VaultSubscriptionId | A számlázási csoporthoz társított tár előfizetés-azonosítója |
| VaultLocation | A számlázási csoporthoz társított tár helye |
| VaultStore_StorageReplicationType | A számlázási csoporthoz társított tár tárolási replikációs típusa |
| VaultType | A tároló típusa, amely "Microsoft. Recoveryservices szolgáltatónál/Vaults" |
| TimeGenerated | A rekord időbélyege |
| Extendedproperties példányt paraméterként | A számlázási csoport további tulajdonságai |

## <a name="sample-queries"></a>Példák a lekérdezésekre

Az alábbiakban néhány példa olvasható, amely segítséget nyújt a System functions használatának megkezdésében.

- Az összes sikertelen Azure-beli virtuális gép biztonsági mentési feladata egy adott időtartományban

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06") //call function with RangeStart and RangeEnd parameters set, and other parameters with default value
    | where BackupSolution=="Azure Virtual Machine Backup" and Status=="Failed"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Az összes SQL-napló biztonsági mentési feladata egy adott időtartományban

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06","*","*","*","*",true,"*","*","*","*","*","*",false) //call function with RangeStart and RangeEnd parameters set, ExcludeLog parameter as false, and other parameters with default value
    | where BackupSolution=="SQL in Azure VM Backup" and Operation=="Log"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- A "testvm" virtuális gép által felhasznált biztonsági mentési tár heti trendje

    ````Kusto
    _AzureBackup_GetBackupInstancesTrends("2021-01-01", "2021-03-06","*","*","*","*",false,"*","*","*","*",true, "Weekly") //call function with RangeStart and RangeEnd parameters set, AggregationType parameter as Weekly, and other parameters with default value
    | where BackupSolution == "Azure Virtual Machine Backup"
    | where FriendlyName == "testvm"
    | project TimeGenerated, VaultStore_StorageConsumptionInMBs
    | render timechart 
    ````

## <a name="next-steps"></a>Következő lépések
[További információ a biztonsági mentési jelentésekről](./configure-reports.md)