---
title: Gyakori metrikák autoskálázása
description: Megtudhatja, hogy mely metrikákat használják általában a Cloud Services, Virtual Machines és Web Apps automatikus skálázásához.
ms.topic: conceptual
ms.date: 12/6/2016
ms.subservice: autoscale
ms.openlocfilehash: f358efb8f99066dc1d7a564ad36d6ed1290853e0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617738"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Általános mérőszámok automatikus skálázása Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor az automatikus skálázás lehetővé teszi a futó példányok számának méretezését a telemetria-adatok (mérőszámok) alapján. Ez a dokumentum az esetlegesen használni kívánt általános mérőszámokat ismerteti. A Azure Portalban kiválaszthatja a skálázáshoz használandó erőforrás metrikáját. Más erőforrásokból is kiválaszthat bármely mérőszámot a skálázáshoz.

Azure Monitor az autoscale csak [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)és [API Management szolgáltatásokra](../../api-management/api-management-key-concepts.md)vonatkozik. Más Azure-szolgáltatások eltérő skálázási módszereket használnak.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>A Resource Manager-alapú virtuális gépek számítási mérőszámai
Alapértelmezés szerint a Resource Manager-alapú Virtual Machines és Virtual Machine Scale Sets alapszintű (gazdagép szintű) mérőszámokat bocsát ki. Emellett az Azure-beli virtuális gépek és VMSS diagnosztikai adatgyűjtésének konfigurálásakor az Azure diagnosztikai bővítmény a vendég-operációsrendszer-teljesítményszámlálók (általában "Vendég-OS mérőszámok") kitöltésére is lehetőséget nyújt.  Ezeket a metrikákat az autoskálázási szabályokban használhatja.

Az `Get MetricDefinitions` API/PoSH/CLI használatával megtekintheti a VMSS-erőforráshoz elérhető metrikákat.

Ha virtuálisgép-méretezési csoportokat használ, és nem jelenik meg egy adott metrika, akkor a diagnosztika bővítmény valószínűleg *le lesz tiltva* .

Ha egy adott metrika nem mintavételezés alatt áll, vagy a kívánt gyakorisággal kerül át, akkor frissítheti a diagnosztikai konfigurációt.

Ha az előző eset értéke TRUE (igaz), akkor tekintse át a [PowerShell használata a Windows rendszerű virtuális gépeken a PowerShell használatával történő Azure Diagnosticsának engedélyezéséhez](../../virtual-machines/extensions/diagnostics-windows.md) és frissítéséhez az Azure VM Diagnostics bővítményt a metrika engedélyezéséhez. Ez a cikk egy példa diagnosztikai konfigurációs fájlt is tartalmaz.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>A Resource Manager-alapú Windows és Linux rendszerű virtuális gépek gazdagép-metrikái
Az Azure-beli virtuális gép és a VMSS esetében alapértelmezés szerint a következő gazdagép-szintű metrikák vannak kibocsátva Windows-és Linux-példányokban. Ezek a metrikák írják le az Azure-beli virtuális gépet, de az Azure VM-gazdagépről gyűjtik, nem pedig a vendég virtuális gépen telepített ügynökön keresztül. Ezeket a metrikákat az automatikus skálázási szabályokban használhatja.

- [A Resource Manager-alapú Windows és Linux rendszerű virtuális gépek gazdagép-metrikái](../platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [A Resource Manager-alapú Windows-és Linux-VM Scale Sets gazdagép-metrikái](../platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-for-resource-manager-based-windows-vms"></a>Vendég operációs rendszer metrikái a Resource Manager-alapú Windows rendszerű virtuális gépekhez
Amikor létrehoz egy virtuális gépet az Azure-ban, a diagnosztika a diagnosztika bővítmény használatával engedélyezhető. A diagnosztikai bővítmény a virtuális gép belsejéből származó mérőszámok készletét bocsátja ki. Ez azt jelenti, hogy az alapértelmezés szerint nem kibocsátott mérőszámok autoskálázása is megtörténik.

A metrikák listáját a PowerShellben a következő parancs használatával hozhatja meg.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Riasztást a következő mérőszámokhoz hozhat létre:

| Metrika neve | Unit (Egység) |
| --- | --- |
| \Processor(_Total)\% Processor Time |Százalék |
| \Processor (_Total) \% Kiemelt idő |Százalék |
| \Processor (_Total) \% felhasználói idő |Százalék |
| \Processor információ (_Total) \Processor gyakorisága |Darabszám |
| \System\Processes |Darabszám |
| \Process (_Total) \Thread száma |Darabszám |
| \Process (_Total) \Handle száma |Darabszám |
| \Memory \% előjegyzett bájtok használatban |Százalék |
| \Memory\Available Bytes |Bájt |
| \Memory\Committed bájtok |Bájt |
| \Memory\Commit korlátja |Bájt |
| \Memory\Pool lapozható bájtok |Bájt |
| \Memory\Pool, nem lapozható bájtok |Bájt |
| \PhysicalDisk (_Total) \% lemez ideje |Százalék |
| \PhysicalDisk (_Total) \% lemez olvasási ideje |Százalék |
| \PhysicalDisk (_Total) \% lemez írási ideje |Százalék |
| \PhysicalDisk (_Total) \ átvitel másodpercenként |CountPerSecond |
| \PhysicalDisk (_Total) \ Olvasás gyakorisága másodpercenként |CountPerSecond |
| \PhysicalDisk (_Total) \ írás gyakorisága (művelet/mp) |CountPerSecond |
| \PhysicalDisk (_Total) \ sebesség (bájt/s) |BytesPerSecond |
| \PhysicalDisk (_Total) \ olvasási sebesség (bájt/s) |BytesPerSecond |
| \PhysicalDisk (_Total) \ írási sebesség (bájt/s) |BytesPerSecond |
| \PhysicalDisk (_Total) \ átlagos a lemez várólistájának hossza |Darabszám |
| \PhysicalDisk (_Total) \ átlagos lemez olvasási várólistájának hossza |Darabszám |
| \PhysicalDisk (_Total) \ átlagos lemez írási várólistájának hossza |Darabszám |
| \Logikai lemez (_Total) \% szabad terület |Százalék |
| \Logikai lemez (_Total) \Szabad hely (MB) |Darabszám |

### <a name="guest-os-metrics-linux-vms"></a>Vendég operációs rendszer mérőszámai Linux rendszerű virtuális gépek
Amikor létrehoz egy virtuális gépet az Azure-ban, a diagnosztika alapértelmezés szerint engedélyezve van a diagnosztikai bővítmény használatával.

A metrikák listáját a PowerShellben a következő parancs használatával hozhatja meg.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Riasztást a következő mérőszámokhoz hozhat létre:

| Metrika neve | Unit (Egység) |
| --- | --- |
| \Memory\AvailableMemory |Bájt |
| \Memory\PercentAvailableMemory |Százalék |
| \Memory\UsedMemory |Bájt |
| \Memory\PercentUsedMemory |Százalék |
| \Memory\PercentUsedByCache |Százalék |
| \Memory\PagesPerSec |CountPerSecond |
| \Memory\PagesReadPerSec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |Bájt |
| \Memory\PercentAvailableSwap |Százalék |
| \Memory\UsedSwap |Bájt |
| \Memory\PercentUsedSwap |Százalék |
| \Processor\PercentIdleTime |Százalék |
| \Processor\PercentUserTime |Százalék |
| \Processor\PercentNiceTime |Százalék |
| \Processor\PercentPrivilegedTime |Százalék |
| \Processor\PercentInterruptTime |Százalék |
| \Processor\PercentDPCTime |Százalék |
| \Processor\PercentProcessorTime |Százalék |
| \Processor\PercentIOWaitTime |Százalék |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CountPerSecond |
| \PhysicalDisk\ReadsPerSecond |CountPerSecond |
| \PhysicalDisk\WritesPerSecond |CountPerSecond |
| \PhysicalDisk\AverageReadTime |Másodperc |
| \PhysicalDisk\AverageWriteTime |Másodperc |
| \PhysicalDisk\AverageTransferTime |Másodperc |
| \PhysicalDisk\AverageDiskQueueLength |Darabszám |
| \NetworkInterface\BytesTransmitted |Bájt |
| \NetworkInterface\BytesReceived |Bájt |
| \NetworkInterface\PacketsTransmitted |Darabszám |
| \NetworkInterface\PacketsReceived |Darabszám |
| \NetworkInterface\BytesTotal |Bájt |
| \NetworkInterface\TotalRxErrors |Darabszám |
| \NetworkInterface\TotalTxErrors |Darabszám |
| \NetworkInterface\TotalCollisions |Darabszám |

## <a name="commonly-used-app-service-server-farm-metrics"></a>Gyakran használt App Service (kiszolgálófarm) metrikák
Az autoskálázást a gyakori webkiszolgáló-metrikák, például a http-várólista hossza alapján is elvégezheti. A metrika neve **HttpQueueLength**.  A következő szakasz felsorolja a rendelkezésre álló kiszolgálófarm (App Service) mérőszámait.

### <a name="web-apps-metrics"></a>Web Apps metrikák
A Web Apps metrikáinak listáját a PowerShellben a következő paranccsal hozhatja meg.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Ezeket a metrikákat riasztással vagy méretezhetővé teheti.

| Metrika neve | Unit (Egység) |
| --- | --- |
| CpuPercentage |Százalék |
| MemoryPercentage |Százalék |
| DiskQueueLength |Darabszám |
| HttpQueueLength |Darabszám |
| BytesReceived |Bájt |
| BytesSent |Bájt |

## <a name="commonly-used-storage-metrics"></a>Gyakran használt tárolási metrikák
A tárolási várólista hossza méretezhető, ami a tárolási várólistában lévő üzenetek száma. A tárolási várólista hossza egy speciális metrika, a küszöbérték pedig az üzenetek száma egy példányban. Ha például két példány van megadva, és ha a küszöbérték 100 értékre van állítva, akkor a skálázás akkor fordul elő, ha a várólistán lévő üzenetek száma összesen 200. A példányok száma 100, 120 és 80, vagy bármely más kombináció, amely akár 200-et, akár többet is felvesz.

Konfigurálja ezt a beállítást a Azure Portal a **Beállítások** panelen. A virtuálisgép-méretezési csoportok esetében frissítheti a Resource Manager-sablonban található autoskálázási beállítást úgy, hogy a *MetricName* *ApproximateMessageCount* használja, és a *metricResourceUri* azonosítóját adja át.

Egy klasszikus Storage-fiókkal például a metricTrigger az autoskálázási beállítás a következőket fogja tartalmazni:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Egy (nem klasszikus) Storage-fiók esetében a metricTrigger a következőket foglalja magában:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Gyakran használt Service Bus metrikák
Service Bus üzenetsor hosszával méretezheti a méretezést, amely a Service Bus üzenetsor üzeneteinek száma. Service Bus üzenetsor hossza egy speciális metrika, és a küszöbérték a példányok száma. Ha például két példány van megadva, és ha a küszöbérték 100 értékre van állítva, akkor a skálázás akkor fordul elő, ha a várólistán lévő üzenetek száma összesen 200. A példányok száma 100, 120 és 80, vagy bármely más kombináció, amely akár 200-et, akár többet is felvesz.

A virtuálisgép-méretezési csoportok esetében frissítheti a Resource Manager-sablonban található autoskálázási beállítást úgy, hogy a *MetricName* *ApproximateMessageCount* használja, és a *metricResourceUri* azonosítóját adja át.

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Service Bus esetében az erőforráscsoport fogalma nem létezik, de a Azure Resource Manager régiónként létrehoz egy alapértelmezett erőforráscsoportot. Az erőforráscsoport általában az "alapértelmezett-ServiceBus-[region]" formátumban van. Például: "default-ServiceBus-EastUS", "default-ServiceBus-WestUS", "default-ServiceBus-AustraliaEast" stb.
>
>

