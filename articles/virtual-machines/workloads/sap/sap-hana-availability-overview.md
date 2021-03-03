---
title: SAP HANA rendelkezésre állás Azure-beli virtuális gépeken – áttekintés | Microsoft Docs
description: Ismerteti SAP HANA műveleteket az Azure natív virtuális gépeken.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a440344a93f9dd610774a33b3960ab1a71f57c65
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671584"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA magas rendelkezésre állás az Azure Virtual Machines szolgáltatásban

Számos Azure-képességet használhat az olyan kritikus fontosságú adatbázisok üzembe helyezéséhez, mint az Azure-beli virtuális gépeken SAP HANA. Ez a cikk útmutatást nyújt az Azure-beli virtuális gépeken üzemeltetett SAP HANA-példányok rendelkezésre állásának eléréséhez. A cikk bemutatja, hogyan valósítható meg az Azure-infrastruktúra az Azure-SAP HANA rendelkezésre állásának növeléséhez. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már ismeri az Azure-ban az infrastruktúra-szolgáltatás (IaaS) alapjait, beleértve a következőket: 

- Virtuális gépek és virtuális hálózatok üzembe helyezése a Azure Portal vagy a PowerShell használatával.
- Az Azure platformfüggetlen parancssori felület (Azure CLI) használata, beleértve a JavaScript Object Notation-(JSON-) Sablonok használatát is.

A cikk azt is feltételezi, hogy már ismeri a SAP HANA példányok telepítését, valamint a felügyelettel és az operációs SAP HANA példányokkal. Különösen fontos, hogy ismerkedjen meg a HANA rendszerreplikáció beállításával és műveleteivel. Ez olyan feladatokat is tartalmaz, mint például a SAP HANA adatbázisok biztonsági mentése és visszaállítása.

Ezek a cikkek jó áttekintést nyújtanak a SAP HANA Azure-beli használatáról:

- [Egypéldányos SAP HANA manuális telepítése Azure-beli virtuális gépeken](./hana-get-started.md)
- [Rendszerreplikáció beállítása SAP HANA Azure-beli virtuális gépeken](sap-hana-high-availability.md)
- [SAP HANA biztonsági mentése Azure-beli virtuális gépeken](./sap-hana-backup-guide.md)

Azt is érdemes elsajátítani, hogy ismerkedjen meg ezekkel a cikkekkel SAP HANA:

- [Magas rendelkezésre állás a SAP HANA számára](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Gyakori kérdések: SAP HANA magas rendelkezésre állása](https://archive.sap.com/documents/docs/DOC-66702)
- [Rendszerreplikáció végrehajtása SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2,0 SPS 01 Újdonságok: magas rendelkezésre állás](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Hálózati javaslatok SAP HANA rendszer-replikáláshoz](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Rendszerreplikáció SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA szolgáltatás automatikus újraindítása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [SAP HANA rendszerreplikáció konfigurálása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

A virtuális gépek Azure-beli üzembe helyezésének megismerése előtt a rendelkezésre állási architektúra az Azure-ban való meghatározása előtt javasoljuk, hogy olvassa el [Az Azure-beli Windows rendszerű virtuális gépek rendelkezésre állásának kezelését](../../manage-availability.md).

## <a name="service-level-agreements-for-azure-components"></a>Azure-összetevőkre vonatkozó szolgáltatási szintű szerződések

Az Azure különböző rendelkezésre állási SLA-kat tartalmaz különböző összetevőkhöz, például a hálózatkezeléshez, a tároláshoz és a virtuális gépekhez. Minden SLA dokumentálva van. További információ: [Microsoft Azure szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/). 

[A Virtual Machines SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) -ja három különböző, három különböző konfigurációra vonatkozó SLA-t ismertet:

- Egyetlen virtuális gép, amely az [Azure Premium SSD](../../managed-disks-overview.md) -ket használja az operációsrendszer-lemez és az összes adatlemez számára. Ez a beállítás havi 99,9%-os üzemidőt biztosít.
- Több (legalább két) virtuális gép, amelyek egy Azure-beli [rendelkezésre állási csoportba](../../windows/tutorial-availability-sets.md)vannak rendezve. Ez a beállítás havi 99,95%-os üzemidőt biztosít.
- Több (legalább kettő) virtuális gép, amely egy availablity- [zónában](../../../availability-zones/az-overview.md)van rendszerezve. Ez a beállítás havi 99,99%-os üzemidőt biztosít.

Mérje fel a rendelkezésre állási követelményt az Azure-összetevők által biztosított SLA-kat illetően. Ezután válassza ki a forgatókönyveit SAP HANA a szükséges szintű rendelkezésre állás eléréséhez.

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg [SAP HANA rendelkezésre állással egy Azure-régión belül](./sap-hana-availability-one-region.md).
- Ismerje meg az [Azure-régiók SAP HANA rendelkezésre állását](./sap-hana-availability-across-regions.md). 















