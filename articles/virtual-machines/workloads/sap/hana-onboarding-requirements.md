---
title: Az Azure-beli SAP HANA bevezetési követelményei (nagyméretű példányok) | Microsoft Docs
description: Az Azure-beli SAP HANA bevezetési követelményei (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16608facab27fc18924cf3a5c6da7e35db048f76
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675608"
---
# <a name="onboarding-requirements"></a>Előkészítési követelmények

Ez a lista a SAP HANA Azure-ban (nagyobb példányokon) való futtatásához szükséges követelményeket állítja össze.

**Microsoft Azure**

- Azure-előfizetés, amely az Azure-beli SAP HANAhoz (nagyméretű példányok) kapcsolható.
- A Microsoft Premier szintű támogatási szerződése. Az SAP Azure-ban való futtatásával kapcsolatos konkrét információk: [SAP-támogatás megjegyzés #2015553 – SAP on Microsoft Azure: az előfeltételek támogatása](https://launchpad.support.sap.com/#/notes/2015553). Ha HANA nagyméretű példányokat használ 384 és több processzorral, akkor a Premier támogatási szerződést is ki kell terjesztenie, hogy tartalmazza a Azure Rapid Response.
- Az SAP-méretezési gyakorlat elvégzése után a HANA nagy példányszámú SKU-ra vonatkozó adatok ismerete szükséges.

**Hálózati kapcsolat**

- ExpressRoute a helyszínről az Azure-ba: Ha a helyszíni adatközpontot az Azure-hoz szeretné csatlakoztatni, ügyeljen arra, hogy legalább 1 GB/s-os kapcsolatot rendeljen az INTERNETSZOLGÁLTATÓtól. A HANA nagyméretű példány-egységek és az Azure közötti kapcsolat a ExpressRoute technológiát is használja. Ez a ExpressRoute-kapcsolat a HANA nagyméretű példány-egységek és az Azure között szerepel a HANA nagyméretű példányainak díjszabásában, beleértve az adott ExpressRoute-áramkörre vonatkozó összes adatbevitelt és kimenő díjat is. Ezért ügyfeleinknek a helyszíni és az Azure közötti ExpressRoute-kapcsolaton túli többletköltségek nem merülnek fel.

**Operációs rendszer**

- SUSE Linux Enterprise Server 12 licence az SAP-alkalmazásokhoz.

   > [!NOTE] 
   > A Microsoft által szállított operációs rendszer nincs regisztrálva a SUSE-ben. Nincs csatlakoztatva az előfizetés-kezelő eszköz példányához.

- SUSE Linux előfizetés-kezelési eszköz üzembe helyezése az Azure-ban egy virtuális gépen. Ez az eszköz lehetővé teszi az Azure-beli (nagyméretű példányok) SAP HANAának regisztrálását és frissítését a SUSE használatával. (Nincs internet-hozzáférés a HANA nagyméretű példány adatközpontjában.) 
- A SAP HANA Red Hat Enterprise Linux 6,7 vagy 7. x licence.

   > [!NOTE]
   > A Microsoft által szállított operációs rendszer nincs regisztrálva a Red Hat-ban. Nem csatlakozik egy Red Hat Subscription Manager-példányhoz.

- Red Hat előfizetés-kezelő üzembe helyezése az Azure-ban egy virtuális gépen. A Red Hat előfizetés-kezelője lehetővé teszi az Azure-beli (nagyméretű példányok) SAP HANAának regisztrálását és a Red Hat által történő frissítését. (Nincs közvetlen internet-hozzáférés az Azure nagyméretű példány-bélyegzőn üzembe helyezett bérlőn belülről.)
- Az SAP megköveteli, hogy a Linux-szolgáltatóval is rendelkezzen támogatási szerződéssel. Ezt a követelményt nem távolítja el a HANA nagyméretű példány megoldása, vagy az a tény, hogy az Azure-ban futtatja a Linuxot. A linuxos Azure Gallery-rendszerképektől eltérően a szolgáltatás díját *nem* tartalmazza a HANA nagyméretű példány megoldási ajánlata. A Linux-terjesztővel a támogatási szerződésekre vonatkozó SAP követelményeinek teljesítése az Ön felelőssége. 
   - SUSE Linux esetén tekintse meg a támogatási szerződések követelményeit az [SAP-megjegyzésekben #1984787-SUSE Linux Enterprise Server 12: telepítési megjegyzések](https://launchpad.support.sap.com/#/notes/1984787) és [sap-Megjegyzés #1056161 – SUSE prioritás támogatása SAP-alkalmazásokhoz](https://launchpad.support.sap.com/#/notes/1056161).
   - A Red Hat Linux esetén a megfelelő előfizetési szintnek kell lennie, amely a HANA nagyméretű példány operációs rendszereinek támogatását és szolgáltatásait is tartalmazza. A Red Hat az SAP-megoldás Red Hat Enterprise Linux-előfizetését javasolja. Lásd: https://access.redhat.com/solutions/3082481 . 

A különböző Linux-verziókkal rendelkező különböző SAP HANA verziók támogatási mátrixa: [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Az operációs rendszer és a HLI belső vezérlőprogram/illesztőprogram-verzióinak kompatibilitási mátrixa esetén tekintse át az [operációs rendszer verziófrissítése a HLI](os-upgrade-hana-large-instance.md)-ben című témakört.


> [!IMPORTANT] 
> A II. típusú egységeknél ezen a ponton csak a SLES 12 SP2 operációsrendszer-verziója támogatott. 


**Adatbázis**

- A SAP HANA (platform vagy Enterprise Edition) licencei és szoftvertelepítési összetevői.

**Alkalmazások**

- Licencek és szoftvertelepítési összetevők minden olyan SAP-alkalmazáshoz, amely SAP HANA és kapcsolódó SAP-támogatási szerződésekhez csatlakozik.
- Licencek és szoftvertelepítési összetevők minden olyan nem SAP-alkalmazáshoz, amelyet a SAP HANA Azure-beli (nagyméretű példányos) környezetekben és a kapcsolódó támogatási szerződésekben használtak.

**Szakértelem**

- Az Azure IaaS és összetevőinek megismerése és ismerete.
- Az SAP-munkaterhelések Azure-ban való üzembe helyezésének és megismerésének tapasztalatai.
- SAP HANA a telepítés hitelesített személyes.
- SAP-építészi ismeretek az SAP HANA magas rendelkezésre állásának és vész-helyreállítási megoldásának kialakításához.

**SAP**

- A várt érték az, hogy Ön SAP-ügyfél, és rendelkezik az SAP-vel kötött támogatási szerződéssel.
- A HANA nagyméretű példányos SKU-k Type II osztályának megvalósításához különösen a SAP HANA és a nagyméretű Felskálázási hardverek végleges konfigurációit kell megkeresni.

**Következő lépések**
- Tekintse át [SAP HANA (nagyméretű példányok) architektúráját az Azure-](hana-architecture.md) ban