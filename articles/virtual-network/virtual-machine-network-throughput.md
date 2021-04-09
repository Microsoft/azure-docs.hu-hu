---
title: Azure-beli virtuális gépek hálózati teljesítménye | Microsoft Docs
description: Ismerje meg az Azure-beli virtuális gépek hálózati átviteli sebességét, beleértve a sávszélesség lefoglalásának módját a virtuális gépek számára.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: cb128f9269895f04d1e0dad8e0c8d06c481e86c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576161"
---
# <a name="virtual-machine-network-bandwidth"></a>Virtuális gépek hálózati sávszélessége

Az Azure különféle virtuálisgép-méreteket és-típusokat kínál, amelyek mindegyike különböző teljesítménybeli képességekkel rendelkezik. Az egyik lehetőség a hálózati átviteli sebesség (vagy a sávszélesség), a megabit/másodperc (Mbps) alapján mérve. Mivel a virtuális gépek megosztott hardveren futnak, a hálózati kapacitást az azonos hardvert használó virtuális gépek között méltányosan kell megosztani. A nagyobb méretű virtuális gépek viszonylag nagyobb sávszélességet foglalnak le a kisebb virtuális gépeknél.
 
Az egyes virtuális gépek számára lefoglalt hálózati sávszélesség mérése a virtuális gépről érkező kimenő forgalomra vonatkozik. A virtuális gépet elhagyó összes hálózati forgalom beleszámít a lefoglalt korlát felé, a célhelytől függetlenül. Ha például egy virtuális gépen 1 000 MB/s korlát van, akkor ez a korlát azt határozza meg, hogy a kimenő forgalom az azonos virtuális hálózatban lévő másik virtuális géphez, vagy az Azure-on kívülre van-e szánva.
 
A bejövő forgalom nem mérhető vagy nem korlátozódik közvetlenül. Vannak azonban más tényezők, például a processzor-és tárolási korlátok, ami hatással lehet a virtuális gépeknek a bejövő adatok feldolgozására.

A gyorsított hálózatkezelés egy olyan szolgáltatás, amely a hálózati teljesítmény javítására szolgál, beleértve a késést, az átviteli sebességet és a CPU-kihasználtságot. Míg a gyorsított hálózatkezelés javíthatja a virtuális gépek átviteli sebességét, így csak a virtuális gép lefoglalt sávszélességére képes. A gyorsított hálózatkezeléssel kapcsolatos további tudnivalókért tekintse meg a Windows vagy [Linux](create-vm-accelerated-networking-cli.md) [rendszerű](create-vm-accelerated-networking-powershell.md) virtuális gépek gyorsított hálózatkezelését ismertető témakört.
 
Az Azure Virtual Machines szolgáltatásnak rendelkeznie kell egy, de több hálózati adapterrel is. A virtuális géphez lefoglalt sávszélesség az összes kimenő forgalom összege a virtuális géphez csatlakoztatott összes hálózati adapteren. Más szóval a lefoglalt sávszélesség virtuális gépenként történik, függetlenül attól, hogy hány hálózati adapter van csatlakoztatva a virtuális géphez. Ha szeretné megtudni, hogy a különböző Azure-beli virtuálisgép-méretek hány hálózati adaptert támogatnak, tekintse meg az Azure Windows-és [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - [alapú](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

## <a name="expected-network-throughput"></a>Várt hálózati teljesítmény

A várt kimenő átviteli sebesség és az egyes virtuálisgép-méretek által támogatott hálózati adapterek száma az Azure Windows-és [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - [alapú](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek méretei szerint van részletezve. Válasszon egy típust, például az általános célú elemet, majd válassza ki az eredményül kapott lapon lévő méret sorozatot (például a Dv2 sorozatot). Minden adatsorozat tartalmaz egy táblázatot, amely a hálózati specifikációkat tartalmazza az utolsó oszlopban, amely a hálózati **adapterek/várt hálózati teljesítmény (MB/s)**. 

Az átviteli sebesség korlátja a virtuális gépre vonatkozik. A következő tényezők nem érintik az átviteli sebességet:
- **Hálózati adapterek száma**: a sávszélesség korlátja a virtuális gépről érkező összes kimenő forgalom összesített értéke.
- **Gyorsított hálózatkezelés**: bár a szolgáltatás hasznos lehet a közzétett korlát elérésében, nem változtatja meg a korlátot.
- **Forgalmi cél**: minden célhely a kimenő korlát felé mutat.
- **Protokoll**: az összes protokollon keresztüli kimenő forgalom a korlát irányába számít.

## <a name="network-flow-limits"></a>Hálózati forgalom korlátai

A sávszélességen kívül a virtuális gépen lévő hálózati kapcsolatok száma az adott időpontban hatással lehet a hálózati teljesítményére. Az Azure hálózati verem a "flows" nevű adatstruktúrákban megőrzi a TCP/UDP-kapcsolat minden irányának állapotát. Egy tipikus TCP/UDP-kapcsolatok 2 folyamatot hoznak létre, egyet a bejövő és egy másikat a kimenő irányhoz. 

A végpontok közötti adatátvitel több folyamat létrehozását igényli az adatátvitelt végzők mellett. Néhány példa a DNS-feloldáshoz és a terheléselosztó Health-mintavételekhez létrehozott folyamatok számára létrehozott folyamatokra. Azt is vegye figyelembe, hogy a hálózati virtuális készülékek (NVA-EK), például az átjárók, a proxyk, a tűzfalak a készüléken megszakított kapcsolatok esetében jönnek létre, és a készülékből származnak. 

![TCP-beszélgetések forgalmának száma egy továbbító berendezésen keresztül](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-active-connections-recommendations"></a>A flow korlátai és az aktív kapcsolatok javaslatai

Napjainkban az Azure hálózati verem a virtuális gépekhez tartozó 1M teljes folyamatot (500k bejövő és 500k kimenő) is támogatja. A virtuális gépek által különböző forgatókönyvekben kezelhető aktív kapcsolatok teljes száma a következő.
- A VNET-hez tartozó virtuális gépek képesek az 500k ***aktív kapcsolatok összekapcsolására** az összes virtuálisgép-méret esetében, amely 500k _ *_aktív folyamattal rendelkezik az egyes irányokban_* *.  
- A hálózati virtuális berendezésekkel (NVA) rendelkező virtuális gépek, például az átjáró, a proxy, a tűzfal képesek a 250k ***aktív kapcsolatok**, valamint az *_egyes irányokban lévő_*, a következő ugráshoz tartozó új folyamat létrehozásakor az új kapcsolat beállítására a fenti ábrán látható módon. 

Ha elérte ezt a korlátot, a további kapcsolatok el lesznek dobva. A kapcsolat létesítése és a megszüntetési díjak is befolyásolhatják a hálózati teljesítményt, mivel a kapcsolat létesítési és megszüntetési megosztása a CPU-t a csomagok feldolgozására szolgáló rutinokkal. Azt javasoljuk, hogy a számítási feladatokat a várt forgalmi mintákkal és a teljesítménybeli igényeknek megfelelő méretezéssel bővítse.

A metrikák a [Azure monitorban](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines) érhetők el, hogy nyomon kövessék a hálózati folyamatok számát és a folyamat létrehozásának sebességét a virtuális GÉPEN vagy VMSS-példányokon.

![Képernyőfelvétel: Azure Monitor metrikáinak lapja, amely a bejövő és a kimenő adatforgalomra vonatkozó diagramot és összesítéseket tartalmazza.](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

A kapcsolat létesítése és a megszüntetési díjak is befolyásolhatják a hálózati teljesítményt, mivel a kapcsolat létesítési és megszüntetési megosztása a CPU-t a csomagok feldolgozására szolgáló rutinokkal. Azt javasoljuk, hogy a számítási feladatokat a várt forgalmi mintákkal és a teljesítménybeli igényeknek megfelelő méretezéssel bővítse. 

## <a name="next-steps"></a>Következő lépések

- [A hálózati teljesítmény optimalizálása egy virtuális gép operációs rendszere esetében](virtual-network-optimize-network-bandwidth.md)
- Egy virtuális gép [hálózati teljesítményének tesztelése](virtual-network-bandwidth-testing.md) .