---
title: SAP HANA telepítése Azure-beli virtuális gépeken | Microsoft Docs "
description: Útmutató SAP HANA Azure-beli virtuális gépeken való telepítéséhez
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: 54bfd7c6e691efbd754e9ea2b804e16b3e07ebbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670975"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>SAP HANA telepítése Azure-beli virtuális gépeken
## <a name="introduction"></a>Bevezetés
Ez az útmutató a HANA Azure-beli virtuális gépeken való üzembe helyezésének megfelelő erőforrásaira mutat. Ez az útmutató arra a dokumentációs erőforrásokra mutat, amelyet a SAP HANA Azure-beli virtuális gépen való telepítése előtt ellenőriznie kell. Így a megfelelő lépések végrehajtásával az Azure-beli virtuális gépeken SAP HANA támogatott konfigurációval végződhet.  

> [!NOTE]
> Ez az útmutató ismerteti SAP HANA üzembe helyezését az Azure-beli virtuális gépeken. További információ a SAP HANA a HANA nagyméretű példányain való üzembe helyezéséről: [SAP HANA (nagyméretű példányok) telepítése és konfigurálása az Azure](./hana-installation.md)-ban.
 
## <a name="prerequisites"></a>Előfeltételek
Az útmutató emellett azt is feltételezi, hogy már ismeri a következőket:
* SAP HANA és SAP NetWeaver, valamint a helyszíni telepítésének módja.
* SAP HANA-és SAP-alkalmazási példányok telepítése és üzemeltetése az Azure-ban.
* A dokumentációban ismertetett fogalmak és eljárások:
   * Az Azure-beli SAP üzembe helyezésének megtervezése, amely magában foglalja az Azure Virtual Network tervezését és az Azure Storage használatát. Lásd: [SAP NetWeaver az Azure Virtual Machines – tervezési és megvalósítási útmutató](./planning-guide.md)
   * Üzembe helyezési alapelvek és a virtuális gépek üzembe helyezésének módjai az Azure-ban. Lásd: [Azure Virtual Machines Deployment for SAP](./deployment-guide.md)
   * Magas rendelkezésre állási fogalmak a SAP HANA az [Azure-beli virtuális gépek magas rendelkezésre állása SAP HANA](./sap-hana-availability-overview.md)

## <a name="step-by-step-before-deploying"></a>Lépésről lépésre a telepítés előtt
Ebben a szakaszban azokat a különböző lépéseket láthatja, amelyeket a SAP HANA Azure-beli virtuális gépen való telepítésének megkezdése előtt kell végrehajtania. A rendszer enumerálja a sorrendet, és az alábbi módon követi nyomon:

1. Nem minden lehetséges üzembe helyezési forgatókönyv támogatott az Azure-ban. Ezért tekintse meg a dokumentum SAP-számítási feladatait az Azure-beli [virtuális gépek által támogatott forgatókönyvekben](./sap-planning-supported-configurations.md) a SAP HANA üzembe helyezésével kapcsolatban. Ha a forgatókönyv nem szerepel a felsorolásban, azt feltételezni kell, hogy nem lett tesztelve, és ennek eredményeképpen nem támogatott
2. Feltételezve, hogy van egy durva ötlete a SAP HANA üzembe helyezéséhez szükséges memóriára vonatkozóan, meg kell találnia egy illeszkedő Azure-beli virtuális gépet. Nem minden olyan virtuális gép, amely az SAP NetWeaver tanúsítvánnyal rendelkezik, az [SAP-támogatási megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533)SAP HANA minősítéssel rendelkezik. SAP HANA Certified Azure-beli virtuális gépekhez tartozó igazság forrása a webhely [SAP HANA a hardver könyvtára](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Az **S** -től kezdődő egységek a [HANA Large instances](./hana-overview-architecture.md) egységek, nem pedig az Azure-beli virtuális gépek.
3. A különböző Azure-beli virtuálisgép-típusok a SUSE Linux vagy a Red Hat Linux operációs rendszerhez eltérő minimális operációsrendszer-kiadásokkal rendelkeznek. A webhely [SAP HANA a hardver könyvtára](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)lapon kattintson a SAP HANA Certified egység listán szereplő bejegyzésre az egység részletes adatának beszerzéséhez. A támogatott HANA-alapú számítási feladatok mellett a SAP HANA ezen egységei által támogatott operációsrendszer-kiadások is megjelennek
4. Az operációs rendszer kiadásainál bizonyos minimális kernel-kiadásokat kell figyelembe vennie. Ezek a minimális kiadások a következő SAP-támogatási megjegyzésekben vannak dokumentálva:
    - [SAP-támogatás Megjegyzés #2814271 SAP HANA biztonsági mentés nem sikerül az Azure-ban ellenőrzőösszeg-hiba esetén](https://launchpad.support.sap.com/#/notes/2814271)
    - [SAP-támogatás Megjegyzés #2753418 lehetséges teljesítmény-romlás az időzítő tartaléka miatt](https://launchpad.support.sap.com/#/notes/2753418)
    - [SAP-támogatás Megjegyzés #2791572 teljesítmény romlása az Azure-beli Hyper-V VDSO-támogatásának hiánya miatt](https://launchpad.support.sap.com/#/notes/2791572)
4. A virtuálisgép-típus esetén támogatott operációsrendszer-kiadás alapján ellenőriznie kell, hogy a kívánt SAP HANA-kiadás támogatott-e az adott operációsrendszer-kiadásban. A különböző operációsrendszer-kiadásokkal rendelkező SAP HANA-kiadások támogatási mátrixának beolvasása az [SAP-támogatási megjegyzés #2235581](https://launchpad.support.sap.com/#/notes/2235581) .
5. Előfordulhat, hogy az Azure-beli virtuális gép típusának, az operációs rendszer kiadásának és a SAP HANA kiadásnak egy érvényes kombinációját találta, az SAP termék rendelkezésre állási mátrixában kell bejelentkeznie. Az SAP rendelkezésre állási mátrixában megtudhatja, hogy támogatott-e a SAP HANA-adatbázison futtatni kívánt SAP-termék.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Lépésenkénti virtuálisgép-telepítés és vendég operációs rendszer szempontjai
Ebben a fázisban végig kell haladnia a virtuális gép (ek) üzembe helyezésének lépésein a HANA telepítéséhez, és végül a telepítés után optimalizálni kell a kiválasztott operációs rendszert.

1. Válassza ki az alaprendszerképet az Azure-katalógusból. Ha a SAP HANA saját operációsrendszer-rendszerképét szeretné létrehozni, ismernie kell a sikeres SAP HANA telepítéshez szükséges különböző csomagokat. Ellenkező esetben javasoljuk, hogy a SUSE és a Red Hat rendszerképeket használja az SAP-hez, vagy SAP HANA ki az Azure képtárat. Ezek a lemezképek tartalmazzák a HANA sikeres telepítéséhez szükséges csomagokat. Az operációs rendszer szolgáltatójának támogatási szerződése alapján ki kell választania egy olyan rendszerképet, amelyben saját licencet hoz. Vagy olyan operációsrendszer-rendszerképet is választhat, amely támogatást tartalmaz
2. Ha olyan vendég operációs rendszer lemezképét választotta, amelyhez saját licenc szükséges, regisztrálnia kell az operációsrendszer-lemezképet az előfizetésében, hogy letöltse és alkalmazza a legújabb javításokat. Ez a lépés a nyilvános internet-hozzáférést fogja igényelni. Hacsak nem állítja be a saját példányát (például egy Azure-beli SMT-kiszolgálót).
3. Döntse el a virtuális gép hálózati konfigurációját. További információkat a dokumentumban SAP HANA az infrastruktúra- [konfigurációk és-műveletek az Azure](./hana-vm-operations.md)-ban című témakörben olvashat. Ne feledje, hogy az Azure-beli virtuális hálózati kártyákhoz nem lehet hozzárendelni hálózati átviteli kvótákat. Ennek eredményeképpen az egyetlen célja, hogy a forgalmat különböző Vnic keresztül irányítsa, biztonsági megfontolásokon alapul. Bízunk abban, hogy a több Vnic és a biztonsági szempontok által kényszerített követelmények között a forgalmi útválasztás összetettsége közötti támogatott kompromisszumot találja.
3. Alkalmazza a legújabb javításokat az operációs rendszerre a virtuális gép üzembe helyezése és regisztrálása után. Regisztrálva van a saját előfizetésében. Ha olyan rendszerképet választ, amely tartalmazza az operációs rendszer támogatását, akkor a virtuális gépnek már elérhetőnek kell lennie a javításokhoz. 
4. Alkalmazza a SAP HANAhoz szükséges dallamokat. Ezek a dallamok a következő SAP-támogatási megjegyzésekben szerepelnek:

    - [SAP-támogatás Megjegyzés #2694118 – Red Hat Enterprise Linux HA Add-On az Azure-ban](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP-támogatás Megjegyzés #1984787 – SUSE LINUX Enterprise Server 12: telepítési megjegyzések](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP-támogatás Megjegyzés #2578899-SUSE Linux Enterprise Server 15: telepítési Megjegyzés](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP-támogatás Megjegyzés #2002167-Red Hat Enterprise Linux 7. x: telepítés és frissítés](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP-támogatás Megjegyzés #2292690-SAP HANA DB: ajánlott operációsrendszer-beállítások a RHEL 7 rendszerhez](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [SAP-támogatás Megjegyzés #2772999-Red Hat Enterprise Linux 8. x: telepítés és konfigurálás](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP-támogatás Megjegyzés #2777782-SAP HANA DB: ajánlott operációsrendszer-beállítások a RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP-támogatás Megjegyzés #2455582-Linux: SAP-alkalmazások futtatása a GCC 6. x-szel](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [SAP-támogatás Megjegyzés #2382421 – a hálózati konfiguráció optimalizálása a HANA-és az operációs rendszer szintjén](https://launchpad.support.sap.com/#/notes/2382421)

1. Válassza ki SAP HANA Azure Storage-típusát. Ebben a lépésben a SAP HANA telepítésének tárolási elrendezését kell eldöntenie. A csatlakoztatott Azure-lemezeket vagy a natív Azure NFS-megosztásokat fogja használni. Az Azure-beli tárolási típusok, amelyek használhatók, illetve különböző Azure-beli tárolási típusok kombinációi, [SAP HANA Azure-beli virtuális gépek tárolási konfigurációjában](./hana-vm-operations-storage.md)vannak dokumentálva. Végezze el a kiindulási pontként dokumentált konfigurációkat. A nem éles rendszerek esetében előfordulhat, hogy az alacsonyabb átviteli sebesség vagy a IOPS konfigurálható. Éles környezetben előfordulhat, hogy egy kicsit nagyobb átviteli sebességet és IOPS kell konfigurálnia.
2. Az M-sorozat vagy a Mv2-Series virtuális gépek használata esetén győződjön meg arról, hogy konfigurálta az [Azure-írásgyorsító](../../how-to-enable-write-accelerator.md) az adatbázis-kezelői tranzakciós naplókat tartalmazó kötetek számára, illetve az Ismétlési naplókat. Vegye figyelembe a írásgyorsító korlátozásait dokumentált módon.
2. Győződjön meg arról, hogy az [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van-e az üzembe helyezett virtuális gépeken.

> [!NOTE]
> A különböző SAP-hangolási profilokban vagy a megjegyzésekben leírtak szerint nem minden parancs futtatása sikerült az Azure-ban. A virtuális gépek energiagazdálkodási módjának manipulálására szolgáló parancsok általában hibát jeleznek, mivel a mögöttes Azure-gazdagép hardverének energiagazdálkodási módja nem módosítható.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Azure-beli virtuális gépekre jellemző lépésenkénti előkészületek
Az Azure-specifikus szolgáltatások egyike egy Azure-beli virtuálisgép-bővítmény telepítése, amely figyeli az SAP-gazdagép ügynökének figyelési szolgáltatásait. A figyelési bővítmény telepítésének részletei a következőkben vannak dokumentálva:

-  A 2191498-es [SAP-Megjegyzés](https://launchpad.support.sap.com/#/notes/2191498/E) a Linux rendszerű Azure-beli virtuális gépekkel bővített SAP-monitorozást ismerteti 
-  A 1102124-es [SAP-Megjegyzés](https://launchpad.support.sap.com/#/notes/1102124/E) a Linux rendszeren futó SAPOSCOL kapcsolatos információkat tárgyalja 
-  A 2178632-es [SAP-Megjegyzés](https://launchpad.support.sap.com/#/notes/2178632/E) a legfontosabb monitorozási metrikákat ismerteti Microsoft Azure
-  [Azure Virtual Machines üzembe helyezés az SAP NetWeaver-ben](./deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA telepítés
Az üzembe helyezett Azure-beli virtuális gépek és a regisztrált és konfigurált operációs rendszerek a SAP HANA az SAP telepítésének megfelelően telepíthetők. A dokumentáció beszerzésének elkezdéséhez érdemes megkezdeni az SAP-webhely [HANA-erőforrásait](https://www.sap.com/products/s4hana-erp.html?btp=9d3e6f82-d8ab-4122-8d2d-bf4971217afd)

Az Azure Premium Storage vagy Ultra Disk közvetlen csatlakoztatott lemezeit használó kibővített konfigurációk SAP HANA olvassa el a dokumentumban [SAP HANA az infrastruktúra-konfigurációk és-műveletek az Azure](./hana-vm-operations.md#configuring-azure-infrastructure-for-sap-hana-scale-out) -ban című dokumentumot.


## <a name="additional-resources-for-sap-hana-backup"></a>További források SAP HANA biztonsági mentéshez
Az Azure-beli virtuális gépeken SAP HANA adatbázisok biztonsági mentésével kapcsolatos információkért lásd:
* [Az Azure-beli SAP HANA biztonsági mentési útmutatója Virtual Machines](./sap-hana-backup-guide.md)
* [SAP HANA Azure Backup a fájl szintjén](./sap-hana-backup-file-level.md)

## <a name="next-steps"></a>Következő lépések
Olvassa el a dokumentációt:

- [SAP HANA infrastruktúrakonfigurációk és -műveletek az Azure-ban](./hana-vm-operations.md)
- [SAP HANA Azure-beli virtuális gépek tárkonfigurációi](./hana-vm-operations-storage.md)
