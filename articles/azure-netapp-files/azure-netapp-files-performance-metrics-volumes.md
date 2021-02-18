---
title: Ajánlott teljesítményteszt-tesztek – Azure NetApp Files
description: Ismerje meg a mennyiségi teljesítményre és mérőszámokra vonatkozó teljesítményteszt-tesztelési javaslatokat Azure NetApp Files használatával.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: f73091552a78760024189b173897913edca724bb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593413"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Az Azure NetApp Files teljesítménytesztjével kapcsolatos javaslatok

Ez a cikk a mennyiségi teljesítményre és a mérőszámokra vonatkozó teljesítményteszt-tesztelési javaslatokat tartalmaz Azure NetApp Files használatával.

## <a name="overview"></a>Áttekintés

Egy Azure NetApp Files kötet teljesítmény-jellemzőinek megismeréséhez használhatja a nyílt forráskódú eszköz [Fio](https://github.com/axboe/fio) , amellyel számos különböző számítási feladatot szimulálhat. A FIO Linux-és Windows-alapú operációs rendszerekre is telepíthető.  Kiváló eszköz egy kötet IOPS és átviteli sebességének gyors pillanatképének lekéréséhez.

### <a name="vm-instance-sizing"></a>Virtuálisgép-példányok méretezése

A legjobb eredmény érdekében ügyeljen arra, hogy a tesztek elvégzéséhez megfelelő méretű virtuálisgép-(VM-) példányt használjon. Az alábbi példák egy Standard_D32s_v3 példányt használnak. A virtuálisgép-példányok méretével kapcsolatos további információkért lásd: Windows rendszerű [virtuális gépek méretei az Azure-ban](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Windows-alapú virtuális gépekhez, valamint a Linux rendszerű [virtuális gépek méretei az Azure-ban Linux-alapú virtuális gépekhez](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp Files kötet méretezése

Győződjön meg arról, hogy a megfelelő szolgáltatási szint és a kötet kvótájának mérete a várt teljesítményszint szerint van kiválasztva. További információ: [Azure NetApp Files szolgáltatási szintjei](azure-netapp-files-service-levels.md) .

### <a name="virtual-network-vnet-recommendations"></a>A Virtual Network (VNet) javaslatai

A teljesítményteszt tesztelését ugyanabban a VNet kell végrehajtania, mint Azure NetApp Files. Az alábbi példa a javaslatot mutatja be:

![VNet javaslatok](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>A FIO telepítése

A FIO bináris formátumban, Linux és Windows rendszereken egyaránt elérhető. A kívánt platformra való telepítéséhez kövesse a [Fio](https://github.com/axboe/fio) bináris csomagok szakaszát.

## <a name="fio-examples-for-iops"></a>FIO-példák IOPS 

Az ebben a szakaszban szereplő FIO-példák a következő beállítást használják:
* Virtuálisgép-példány mérete: D32s_v3
* Kapacitási készlet szolgáltatási szintje és mérete: prémium/50 TiB
* Mennyiségi kvóta mérete: 48 TiB

Az alábbi példák a FIO véletlenszerű olvasási és írási műveleteit mutatják be.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k-blokk mérete 100% véletlenszerű olvasás

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Kimenet: 68k olvasási IOPS megjelenítve

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k-blokk mérete 100% véletlenszerű írás

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Kimenet: 73k írási IOPS megjelenítve

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>FIO-példák a sávszélességre

Az ebben a szakaszban szereplő példák a FIO szekvenciális olvasási és írási műveleteit mutatják be.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64 KB-os blokk mérete 100% szekvenciális olvasás

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Kimenet: 11,8 Gbit/s átviteli sebesség látható

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64 KB-os blokk mérete 100% szekvenciális írás

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Kimenet: 12,2 Gbit/s átviteli sebesség látható

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Mennyiségi mérőszámok

Azure NetApp Files teljesítményadatok Azure Monitor számlálók használatával érhetők el. A számlálók a Azure Portal és a REST API GET kérelmeken keresztül érhetők el. 

A korábbi adatokat a következő információkhoz tekintheti meg:
* Olvasási késleltetés átlagos késése 
* Írási késleltetés átlagos késése 
* IOPS olvasása (átlag)
* Írási IOPS (átlag)
* Kötet logikai mérete (átlag)
* Kötet pillanatképének mérete (átlag)

### <a name="using-azure-monitor"></a>Az Azure Monitor használata 

A metrikák lapon a következő ábrán látható módon férhet hozzá Azure NetApp Files számlálók:

![Azure Monitor metrikák](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Irányítópultot is létrehozhat Azure Monitor a Azure NetApp Fileshoz a metrikák lapra, a NetApp szűrésére, valamint a fontos kötetek megadására: 

![Az Azure Monitor irányítópultja](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>API-hozzáférés Azure Monitor

Azure NetApp Files számlálókat REST API-hívások segítségével érheti el. A kapacitási készletek és kötetek számlálóinak megjelenítéséhez tekintse meg [Azure monitor: Microsoft. NetApp/netAppAccounts/capacityPools/Volumes (támogatott mérőszámok](../azure-monitor/essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) ) című témakört.

A következő példa egy GET URL-címet mutat be a logikai kötetek méretének megtekintéséhez:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Következő lépések

- [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
- [Teljesítménytesztek Linuxhoz](performance-benchmarks-linux.md)