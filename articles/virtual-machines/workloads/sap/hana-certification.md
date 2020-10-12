---
title: Az Azure-beli SAP HANA minősítése (nagyméretű példányok) | Microsoft Docs
description: SAP HANA minősítése az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a02f0e1b05b9de8105126d1c9e4e3f79057285f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "77617231"
---
# <a name="certification"></a>Tanúsítvány

A NetWeaver minősítés mellett az SAP speciális minősítést igényel a SAP HANA számára bizonyos infrastruktúrák, például az Azure IaaS SAP HANA támogatásához.

Az alapvető SAP-Megjegyzés a NetWeaver-on, valamint egy bizonyos fokú SAP HANA minősítéshez [SAP-megjegyzés #1928533 – SAP-alkalmazások az Azure-ban: támogatott termékek és Azure-beli VM-típusok](https://launchpad.support.sap.com/#/notes/1928533).

Az Azure-beli (nagyméretű példányok) SAP HANA tanúsítási rekordjai a [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) webhelyen találhatók. 

Az Azure-beli (nagyméretű példányok) SAP HANA a SAP HANA Certified IaaS Platforms webhelyen említettek szerint a Microsoft és az SAP ügyfelei számára lehetővé teszi a nagyméretű SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA vagy más SAP HANA számítási feladatok üzembe helyezését az Azure-ban. A megoldás alapja az SAP-HANA Certified dedikált hardver Stamp ([SAP HANA szabott adatközpont-integráció – TDI](https://scn.sap.com/docs/DOC-63140)). Ha SAP HANA TDI-vel konfigurált megoldást futtat, az összes SAP HANA-alapú alkalmazás (például az SAP Business Suite SAP HANA, SAP BW on SAP HANA, S4/HANA és BW4/HANA) a hardveres infrastruktúrán működik.

A virtuális gépeken futó SAP HANAhoz képest ez a megoldás előnyt élvez. Sokkal nagyobb mennyiségű memóriát biztosít. A megoldás engedélyezéséhez ismernie kell a következő kulcsfontosságú szempontokat:

- Az SAP-alkalmazás rétege és a nem SAP-alkalmazások a szokásos Azure-beli hardveres bélyegeken üzemeltetett virtuális gépeken futnak.
- A helyszíni infrastruktúra, az adatközpontok és az alkalmazások üzembe helyezése a ExpressRoute (ajánlott) vagy virtuális magánhálózati (VPN) kapcsolaton keresztül csatlakozik a felhőalapú platformhoz. A Active Directory és a DNS is ki van bővítve az Azure-ba.
- A HANA számítási feladatok SAP HANA adatbázis-példánya az Azure-beli SAP HANA fut (nagyméretű példányok). A nagyméretű példány bélyegzője az Azure hálózatkezeléséhez csatlakozik, így a virtuális gépeken futó szoftverek kezelhetik a HANA Large-példányban futó HANA-példányt.
- Az Azure-beli SAP HANA (nagyméretű példányok) hardvere dedikált hardvert biztosít az olyan IaaS, amelyeken SUSE Linux Enterprise Server vagy Red Hat Enterprise Linux előre telepített. A virtuális gépekhez hasonlóan az operációs rendszer további frissítései és karbantartása is az Ön felelőssége.
- A HANA vagy más, a HANA-példányok egységén való SAP HANA futtatásához szükséges további összetevők telepítése az Ön felelőssége. Az Azure-beli SAP HANA összes folyamatban lévő művelete és felügyelete is az Ön felelőssége.
- Az itt ismertetett megoldásokon kívül telepítheti az Azure-előfizetéshez tartozó egyéb összetevőket is, amelyek az Azure-ban (nagyméretű példányokon) SAP HANAhez kapcsolódnak. Ilyenek például az olyan összetevők, amelyek lehetővé teszik a vagy közvetlenül a SAP HANA adatbázissal való kommunikációt, például a Jump-kiszolgálók, az RDP-kiszolgálók, a SAP HANA Studio, az SAP-Data Services az SAP BI-forgatókönyvekhez vagy a hálózati figyelési megoldások
- Az Azure-ban a HANA nagyméretű példány támogatja a magas rendelkezésre állást és a vész-helyreállítási funkciókat.

**Következő lépések**
- [Rendelkezésre álló SKU-HLI](hana-available-skus.md) 