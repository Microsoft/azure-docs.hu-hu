---
title: A VMware virtuális gépek/fizikai kiszolgálók feladatátvétele az Azure-ból Azure Site Recovery
description: Ismerje meg, hogyan térhet vissza a helyszíni helyre az Azure-ba történő feladatátvétel után, a VMware virtuális gépek és a fizikai kiszolgálók Azure-ba való vész-helyreállítása során.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: aed015b67aa36e7678b31d7f2f047cb1e77c6a3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004194"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>A VMware virtuális gépek feladatátvétele a helyszíni helyre

Ez a cikk azt ismerteti, hogyan lehet visszaadni az Azure-beli virtuális gépeket egy helyszíni helyre a helyszíni virtuális gépek Azure-ba történő [feladatátvétele](site-recovery-failover.md) [Azure site Recovery](site-recovery-overview.md)használatával. A helyszíni rendszerre történő feladat-visszavétel után engedélyezheti a replikálást, hogy a helyszíni virtuális gépek megkezdődjön az Azure-ba való replikálás.

## <a name="before-you-start"></a>Előkészületek

1. További információ a VMware-feladat- [visszavétel](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Győződjön meg arról, hogy áttekintette és végrehajtotta a feladat- [visszavétel előkészítésének](vmware-azure-prepare-failback.md)lépéseit, valamint azt, hogy az összes szükséges összetevő telepítve van-e. Az összetevők közé tartozik egy folyamat-kiszolgáló az Azure-ban, egy helyszíni fő célkiszolgáló és egy VPN-helyek közötti kapcsolat (vagy ExpressRoute) a feladat-visszavételhez.
3. Győződjön meg arról, hogy végrehajtotta az ismételt védelemre és a feladat-visszavételre [vonatkozó követelményeket](vmware-azure-reprotect.md#before-you-begin) , valamint hogy [engedélyezte](vmware-azure-reprotect.md#enable-reprotection) az Azure-beli virtuális gépek védelmét, hogy azok az Azure-ból a helyszíni helyre replikálódnak. A virtuális gépeknek replikált állapotban kell lenniük a feladat-visszavétel érdekében.




## <a name="run-a-failover-to-fail-back"></a>Feladatátvétel futtatása a feladat-visszavétel érdekében

1. Győződjön meg arról, hogy az Azure-beli virtuális gépek újra vannak védve, és a helyszíni helyre replikálódnak.
    - A virtuális gépeknek legalább egy helyreállítási pontra van szükségük a feladat-visszavétel érdekében.
    - Ha egy helyreállítási tervet hajt végre, akkor a tervben szereplő összes gépnek rendelkeznie kell legalább egy helyreállítási ponttal.
2. A tárolóban > **replikált elemek** területen válassza ki a virtuális gépet. Kattintson a jobb gombbal a nem **tervezett feladatátvételt**> virtuális gépre.
3. A **feladatátvétel megerősítése lapon** ellenőrizze a feladatátvétel irányát (az Azure-ból).
4. Válassza ki a feladatátvételhez használni kívánt helyreállítási pontot.
    - Javasoljuk, hogy használja a **legújabb** helyreállítási pontot. Az alkalmazás-konzisztens pont a legutóbbi időponthoz kapcsolódik, és adatvesztést okoz.
    - A **legújabb** egy összeomlás-konzisztens helyreállítási pont.
    - A **legújabb** verzióban a virtuális gép a legutóbbi elérhető időponthoz képest feladatátvételt hajt végre. Ha egy helyreállítási tervben a több virtuális gépre kiterjedő konzisztencia-replikációs csoport van, a csoport minden virtuális gépe átadja a független legkésőbbi időpontját.
    - Ha egy alkalmazással konzisztens helyreállítási pontot használ, az egyes virtuális gépek a legújabb elérhető pontra kerülnek vissza. Ha egy helyreállítási terv replikációs csoporttal rendelkezik, minden egyes csoport a közös rendelkezésre álló helyreállítási pontra lesz helyreállítva.
5. A feladatátvétel megkezdődik. Site Recovery leállítja az Azure-beli virtuális gépeket.
6. A feladatátvétel befejezését követően győződjön meg róla, hogy minden a várt módon működik. Győződjön meg arról, hogy az Azure-beli virtuális gépek le vannak állítva. 
7. Minden ellenőrzött értékkel a feladatátvételi folyamat befejezéséhez kattintson a jobb gombbal a virtuális gépre > **véglegesítés** elemre. A véglegesítés eltávolítja a feladatátvétel során fellépett Azure-beli virtuális gépet. 

> [!NOTE]
> Windows rendszerű virtuális gépek esetén Site Recovery letiltja a VMware-eszközöket a feladatátvétel során. A Windows rendszerű virtuális gép feladat-visszavétele során a VMware-eszközök ismét engedélyezve lesznek. 




## <a name="reprotect-from-on-premises-to-azure"></a>Ismételt védelem a helyszínről az Azure-ba

A feladat-visszavétel véglegesítése után a rendszer törli az Azure-beli virtuális gépeket. A virtuális gép vissza van kapcsolva a helyszíni helyen, de nem védett. A virtuális gépek Azure-ba történő replikálásának megkezdéséhez az alábbiak szerint:

1. A tárolóban > **replikált elemek** területen válassza a sikertelen virtuális gépek lehetőséget, majd válassza az **ismételt védelem** lehetőséget.
2. Az Azure-ba történő adatküldéshez használt folyamat kiszolgálójának meghatározása.
3. Az ismételt védelem elindításához kattintson **az OK gombra** .

> [!NOTE]
> A helyszíni virtuális gép elindítása után akár 15 percet is igénybe vehet, amíg az ügynök vissza nem regisztrálja a konfigurációs kiszolgálót. Ebben az időszakban az ismételt védelem meghiúsul, és egy hibaüzenetet ad vissza, amely azt jelzi, hogy az ügynök nincs telepítve. Ha ez történik, várjon néhány percet, és állítsa be újra a védelemmel.

## <a name="next-steps"></a>Következő lépések

Az újravédelemi feladatok befejeződése után a helyszíni virtuális gép replikálódik az Azure-ba. Igény szerint [egy másik feladatátvételt is futtathat](site-recovery-failover.md) az Azure-ban.

