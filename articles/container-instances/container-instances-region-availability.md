---
title: Erőforrás rendelkezésre állása régiónként
description: A Azure Container Instances szolgáltatás számítási és memória-erőforrásainak rendelkezésre állása különböző Azure-régiókban.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: a415a739cd9c1e2ca39ebeaef1d8903ab72cf0c4
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831280"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Erőforrások rendelkezésre állása Azure Container Instances Azure-régiókban

Ez a cikk részletesen ismerteti Azure Container Instances számítási, memória-és tárolási erőforrások rendelkezésre állását az Azure-régiókban és a célként megadott operációs rendszer alapján. A Azure Container Instances elérhető régiói általános listáját az [elérhető régiók](https://azure.microsoft.com/regions/services/)című részben tekintheti meg.

A megjelenített értékek a [tároló csoportok](container-instances-container-groups.md)üzembe helyezése során elérhető maximális erőforrások. Az értékek a kiadvány aktuális időpontjában jelennek meg.

> [!NOTE]
> Az ezen erőforrás-korlátokon belül létrehozott tároló csoportok a telepítési régión belül rendelkezésre állásra vonatkoznak. Amikor egy régió nagy terhelés alatt áll, hibát észlelhet a példányok üzembe helyezésekor. Az ilyen üzembe helyezési hibák enyhítése érdekében próbálja meg alacsonyabb erőforrás-beállításokkal üzembe helyezni a példányokat, vagy próbálja megismételni a telepítést egy későbbi időpontban, vagy egy másik régióban, amely elérhető erőforrásokkal rendelkezik.

További információ a központi telepítések kvótái és egyéb korlátairól: a [Azure Container instances kvótái és korlátai](container-instances-quotas.md).

## <a name="linux-container-groups"></a>Linux-tárolók csoportjai

A következő régiók és maximális erőforrások elérhetők a Linux-tárolókkal rendelkező tárolók számára az általános telepítések, az [Azure virtuális hálózati](container-instances-vnet.md) telepítések és a [GPU-erőforrásokkal](container-instances-gpu.md) (előzetes verzió) üzemelő példányok számára.

> [!IMPORTANT]
> Egy adott régióban a maximális erőforrások eltérőek a telepítéstől függően. Előfordulhat például, hogy egy adott régió esetében a processzor és a memória mérete eltérő lehet egy Azure-beli virtuális hálózatban, mint az általános telepítésnél. Ugyanez a régió a GPU-erőforrásokkal rendelkező üzemelő példányok esetében is eltérő értékeket tartalmazhat. Ellenőrizze a központi telepítési típust, mielőtt ellenőrzi az alábbi táblákat a régiójában lévő maximális értékekhez.

| Region | Max CPU (Max. CPU) | Maximális memória (GB) | VNET Max CPU | VNET maximális mérete (GB) | Tárterület (GB) | GPU SKU-i (előzetes verzió) |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: |
| Kelet-Ausztrália | 4 | 16 | 4 | 16 | 50 | N/A |
| Dél-Brazília | 4 | 16 | 2 | 8 | 50 | N/A |
| Közép-Kanada | 4 | 16 | 4 | 16 | 50 | N/A |
| Közép-India | 4 | 16 | 4 | 4 | 50 | V100 |
| Az USA középső régiója | 4 | 16 | 4 | 16 | 50 | N/A |
| Kelet-Ázsia | 4 | 16 | 4 | 16 | 50 | N/A |
| USA keleti régiója | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| USA 2. keleti régiója | 4 | 16 | 4 | 16 | 50 | N/A |
| Közép-Franciaország | 4 | 16 | 4 | 16 | 50 | N/A |
| Középnyugat-Németország | 3 | 16 | N.A. | N.A. | 50 | N/A |
| Kelet-Japán | 2 | 8 | 4 | 16 | 50 | N/A |
| Dél-Korea középső régiója | 4 | 16 | N.A. | N.A. | 50 | N/A |
| USA északi középső régiója | 2 | 3.5 | 4 | 16 | 50 | K80, P100, V100 |
| Észak-Európa | 4 | 16 | 4 | 16 | 50 | K80 |
| USA déli középső régiója | 4 | 16 | 4 | 16 | 50 | N/A |
| Délkelet-Ázsia | 4 | 16 | 4 | 16 | 50 | P100, V100 |
| Dél-India | 4 | 16 | N.A. | N.A. | 50 | N/A |
| Az Egyesült Királyság déli régiója | 4 | 16 | 4 | 16 | 50 | N/A |
| Észak-Egyesült Arab | 3 | 16 | N.A. | N.A. | 50 | N/A |
| USA nyugati középső régiója| 4 | 16 | 4 | 16 | 50 | N/A |
| Nyugat-Európa | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| USA nyugati régiója | 4 | 16 | 4 | 16 | 50 | N/A |
| USA 2. nyugati régiója | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |

A következő maximális erőforrások érhetők el egy [GPU-erőforrásokkal](container-instances-gpu.md) (előzetes verzió) üzembe helyezett tároló csoport számára.

> [!IMPORTANT]
> Jelenleg a GPU-erőforrásokkal üzemelő példányok nem támogatottak az Azure-beli virtuális hálózatok üzembe helyezése során, és csak Linux-tárolók csoportjain érhetők el.

| GPU SKU-i | GPU-szám | Max CPU (Max. CPU) | Maximális memória (GB) | Tárterület (GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100, V100 | 1 | 6 | 112 | 50 |
| P100, V100 | 2 | 12 | 224 | 50 |
| P100, V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Windows-tárolók csoportjai

A következő régiók és maximális erőforrások elérhetők a [támogatott és előzetes](container-instances-faq.md#what-windows-base-os-images-are-supported) verziójú Windows Server-tárolókkal rendelkező tároló-csoportok számára.

> [!IMPORTANT]
> Jelenleg a Windows-tárolók csoportjaival üzemelő példányok nem támogatottak az Azure-beli virtuális hálózatok üzembe helyezése közben.

###  <a name="windows-server-2016"></a>Windows Server 2016

> [!NOTE]
> További információ az 1B, 2B és 3B gazdagépekről: a [gazdagép és a tároló verziószámának kompatibilitása](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) .

| Region | 1B/2B maximális CPU | 1B/2B maximális memória (GB) |3B Max CPU | 3B maximális memória (GB) | Tárterület (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Kelet-Ausztrália | 2 | 8 | 2 | 8 | 20 |
| Dél-Brazília | 4 | 16 | 4 | 16 | 20 |
| Közép-Kanada | 2 | 8 | 2 | 3.5 | 20 |
| Közép-India | 2 | 3.5 | 2 | 3.5 | 20 |
| Az USA középső régiója | 2 | 3.5 | 2 | 3.5 | 20 |
| Kelet-Ázsia | 2 | 3.5 | 2 | 3.5 | 20 |
| USA keleti régiója | 4 | 16 | 2 | 8 | 20 |
| USA 2. keleti régiója | 2 | 3.5 | 4 | 16 | 20 |
| Kelet-Japán | 4 | 16 | 4 | 16 | 20 |
| Dél-Korea középső régiója | 4 | 16 | 4 | 16 | 20 |
| USA északi középső régiója | 4 | 16 | 4 | 16 | 20 |
| Észak-Európa | 2 | 8 | 2 | 8 | 20 |
| USA déli középső régiója | 2 | 3.5 | 2 | 8 | 20 |
| Délkelet-Ázsia | N.A. | N.A. | 2 | 3.5 | 20 |
| Dél-India | 2 | 3.5 | 2 | 3.5 | 20 |
| Az Egyesült Királyság déli régiója | 2 | 8 | 2 | 3.5 | 20 |
| USA nyugati középső régiója | 4 | 16 | 2 | 8 | 20 |
| Nyugat-Európa | 4 | 16 | 4 | 16 | 20 |
| USA nyugati régiója | 4 | 16 | 2 | 8 | 20 |
| USA 2. nyugati régiója | 2 | 8 | 2 | 3.5 | 20 |


### <a name="windows-server-2019-ltsc"></a>Windows Server 2019 LTSC

> [!NOTE]
> További információ az 1B, 2B és 3B gazdagépekről: a [gazdagép és a tároló verziószámának kompatibilitása](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) .

| Region | 1B/2B maximális CPU | 1B/2B maximális memória (GB) |3B Max CPU | 3B maximális memória (GB) | Tárterület (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Kelet-Ausztrália | 4 | 16 | 4 | 16 | 20 |
| Dél-Brazília | 4 | 16 | 4 | 16 | 20 |
| Közép-Kanada | 4 | 16 | 4 | 16 | 20 |
| Közép-India | 4 | 16 | 4 | 16 | 20 |
| Az USA középső régiója | 4 | 16 | 4 | 16 | 20 |
| Kelet-Ázsia | 4 | 16 | 4 | 16 | 20 |
| USA keleti régiója | 4 | 16 | 4 | 16 | 20 |
| USA 2. keleti régiója | 2 | 3.5 | 2 | 3.5 | 20 |
| Közép-Franciaország | 4 | 16 | 4 | 16 | 20 |
| Kelet-Japán | N.A. | N.A. | 4 | 16 | 20 |
| Dél-Korea középső régiója | 4 | 16 | 4 | 16 | 20 |
| USA északi középső régiója | 4 | 16 | 4 | 16 | 20 |
| Észak-Európa | 4 | 16 | 4 | 16 | 20 |
| USA déli középső régiója | 4 | 16 | 4 | 16 | 20 |
| Délkelet-Ázsia | 4 | 16 | 4 | 16 | 20 |
| Dél-India | 4 | 16 | 4 | 16 | 20 |
| Az Egyesült Királyság déli régiója | 4 | 16 | 4 | 16 | 20 |
| USA nyugati középső régiója | 4 | 16 | 4 | 16 | 20 |
| Nyugat-Európa | 4 | 16 | 4 | 16 | 20 |
| USA nyugati régiója | 4 | 16 | 4 | 16 | 20 |
| USA 2. nyugati régiója | 2 | 8 | 4 | 16 | 20 |

## <a name="next-steps"></a>Következő lépések

Tájékoztassa a csapatot, ha további régiókat szeretne látni, vagy megnövelt erőforrás-elérhetőséget a [aka.MS/ACI/feedback](https://aka.ms/aci/feedback)címen.

A tároló-példányok telepítésével kapcsolatos hibaelhárításról lásd: [üzembe helyezési problémák elhárítása Azure Container instances](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
