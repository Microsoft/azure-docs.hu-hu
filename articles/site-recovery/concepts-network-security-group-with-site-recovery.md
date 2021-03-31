---
title: Hálózati biztonsági csoportok Azure Site Recoverykal | Microsoft Docs
description: Ismerteti, hogyan használhatók a hálózati biztonsági csoportok a Azure Site Recovery a vész-helyreállításhoz és az áttelepítéshez
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: harshacs
ms.openlocfilehash: 367aba09f84da1e227c08721077aa1b2132a62bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92367973"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Hálózati biztonsági csoportok Site Recoveryvel

A hálózati biztonsági csoportok a virtuális hálózatban lévő erőforrásokra irányuló hálózati forgalom korlátozására szolgálnak. A [hálózati biztonsági csoport (NSG)](../virtual-network/network-security-groups-overview.md#network-security-groups) olyan biztonsági szabályok listáját tartalmazza, amelyek engedélyezik vagy megtagadják a bejövő vagy kimenő hálózati forgalmat a forrás vagy a cél IP-címe, portja és protokollja alapján.

A Resource Manager-alapú üzemi modellben a NSG alhálózatokhoz vagy egyedi hálózati adapterekhez is társítható. Ha az NSG-t hozzárendelik egy alhálózathoz, a szabályok érvényesek lesznek az alhálózathoz csatlakozó összes erőforrásra. A forgalom tovább korlátozható úgy, hogy egy olyan alhálózaton belül társít egy NSG az egyes hálózati adapterekhez, amely már rendelkezik társított NSG.

Ez a cikk azt ismerteti, hogyan használhatók a hálózati biztonsági csoportok Azure Site Recovery használatával.

## <a name="using-network-security-groups"></a>Hálózati biztonsági csoportok használata

Egy adott alhálózat nulla vagy egy társított NSG is rendelkezhet. Az egyes hálózati adapterek esetében nulla vagy egy társított NSG is tartozhat. Így a virtuális gépek esetében hatékonyan rendelkezhet kettős forgalmi korlátozással, ha először egy alhálózathoz társít egy NSG, majd egy másik NSG a virtuális gép hálózati adapteréhez. Ebben az esetben a NSG-szabályok alkalmazása a forgalom irányát és az alkalmazott biztonsági szabályok prioritását határozza meg.

Vegyünk egy egyszerű példát egy virtuális géppel az alábbiak szerint:
-    A virtuális gép a **contoso-alhálózaton** belül helyezkedik el.
-    A **contoso alhálózat** az **alhálózat NSG** van társítva.
-    A virtuálisgép-hálózati adapter emellett a **virtuális gépek NSG** is társítva van.

![NSG Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

Ebben a példában a bejövő forgalom esetében először az alhálózat NSG lesz kiértékelve. Ezután a VM-NSG kiértékeli az alhálózati NSG keresztül engedélyezett forgalmat. A fordított érték a kimenő forgalomra vonatkozik, és a virtuális gép NSG először kell kiértékelni. Ezt követően a virtuálisgép-NSG keresztül engedélyezett forgalom kiértékelése az alhálózat NSG történik.

Ez lehetővé teszi a részletes biztonsági szabály alkalmazását. Előfordulhat például, hogy engedélyezni szeretné a bejövő internet-hozzáférést néhány alkalmazás virtuális géphez (például a felületi virtuális gépekhez) egy alhálózat alatt, de korlátozza a bejövő internet-hozzáférést más virtuális gépekhez (például adatbázishoz és más háttérbeli virtuális gépekhez). Ebben az esetben a virtuálisgép-NSG való hozzáférés megtagadásával enyhébb szabályokkal rendelkezhet az alhálózat NSG, engedélyezheti az internetes forgalmat, és korlátozhatja az egyes virtuális gépekhez való hozzáférést. Ugyanez alkalmazható a kimenő forgalomra is.

Az ilyen NSG-konfigurációk beállításakor győződjön meg arról, hogy a megfelelő prioritások érvényesek a [biztonsági szabályokra](../virtual-network/network-security-groups-overview.md#security-rules). A szabályok feldolgozása prioritási sorrendben történik. Az alacsonyabb sorszámúak feldolgozása a magasabb sorszámúak előtt történik, mivel az alacsonyabb sorszámok magasabb prioritást jelölnek. Ha az adatforgalom megfelel valamelyik szabálynak, a feldolgozás leáll. Ennek eredményeképp az olyan alacsonyabb prioritású (magasabb számú) szabályokat, amelyek attribútumai megegyeznek a magasabb prioritású szabályokéival, a rendszer nem dolgozza fel.

Nem biztos, hogy minden esetben tisztában lesz vele, ha a hálózati adapterrel és az azt tartalmazó alhálózattal is társítva van hálózati biztonsági csoport. A hálózati adapterre alkalmazott összesített szabályok ellenőrzéséhez tekintse meg a hálózati adapter [érvényes biztonsági szabályait](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) . Az [IP flow ellenőrzési](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) funkcióját az [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) is használhatja annak megállapítására, hogy a kommunikáció engedélyezett-e a hálózati adapterről vagy a hálózatról. Az eszköz megmutatja, hogy a kommunikáció engedélyezett-e, valamint azt is, hogy melyik hálózati biztonsági szabály engedélyezi vagy blokkolja a forgalmat.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Helyszíniről Azure-ba történő replikáció a NSG

Azure Site Recovery lehetővé teszi a vész-helyreállítást és az Azure-ba való áttelepítést helyszíni [Hyper-V virtuális gépek](hyper-v-azure-architecture.md), [VMWare virtuális gépek](vmware-azure-architecture.md)és [fizikai kiszolgálók](physical-azure-architecture.md)számára. A helyszíni és az Azure-beli forgatókönyvek esetében a replikációs adatok küldése és tárolása egy Azure Storage-fiókban történik. A replikáció során nem számítunk fel virtuális gépek díját. Amikor feladatátvételt futtat az Azure-ba, Site Recovery automatikusan létrehozza az Azure IaaS virtuális gépeket.

Ha a virtuális gépeket az Azure-ba történő feladatátvétel után hozták létre, a NSG a virtuális hálózatra és a virtuális gépekre irányuló hálózati forgalom korlátozására is használható. Site Recovery nem hoz létre NSG a feladatátvételi művelet részeként. Azt javasoljuk, hogy a feladatátvétel kezdeményezése előtt hozza létre a szükséges Azure-NSG. Ezután társíthatja a NSG a feladatátvétel során automatikusan átesett virtuális gépekhez, Site Recovery hatékony [helyreállítási terveivel](site-recovery-create-recovery-plans.md)rendelkező Automation-parancsfájlok használatával.

Ha például a feladatátvétel utáni virtuális gép konfigurációja hasonló a fentiekben ismertetett [forgatókönyvhöz](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) :
-    A cél Azure-régióban a DR tervezésének részeként létrehozhat **contoso VNet** és **contoso-alhálózatot** is.
-    Az **alhálózati NSG** , valamint a VIRTUÁLISGÉP- **NSG** is létrehozhatja és konfigurálhatja ugyanazon Dr-megtervezés részeként.
-    Az **ALHÁLÓZAT NSG** ezután azonnal társítva lehet a **contoso-alhálózathoz**, mivel a NSG és az alhálózat is már elérhető.
-    A **virtuális gépek NSG** a feladatátvétel során a helyreállítási tervek használatával társíthatók.

A NSG létrehozása és konfigurálása után javasoljuk, hogy futtasson egy [feladatátvételi tesztet](site-recovery-test-failover-to-azure.md) a megírt NSG-társítások és a feladatátvétel utáni Virtuálisgép-kapcsolat ellenőrzéséhez.

## <a name="azure-to-azure-replication-with-nsg"></a>Azure-ról Azure-ba történő replikáció a NSG

Azure Site Recovery lehetővé teszi az Azure-beli [virtuális gépek](azure-to-azure-architecture.md)vész-helyreállítását. Az Azure-beli virtuális gépek replikálásának engedélyezésekor Site Recovery a replika virtuális hálózatokat (beleértve az alhálózatokat és az átjáró-alhálózatokat) a céltartományban, valamint a forrás és a cél virtuális hálózatok közötti szükséges leképezéseket is létrehozhatja. A cél oldali hálózatokat és alhálózatokat előre is létrehozhatja, és a replikáció engedélyezése mellett is használhatja. Site Recovery a [feladatátvétel](azure-to-azure-tutorial-failover-failback.md)előtt nem hoz létre virtuális gépeket a cél Azure-régióban.

Az Azure-beli virtuális gépek replikálásához győződjön meg arról, hogy az Azure-régió NSG-szabályai lehetővé teszik a [Kimenő kapcsolatok](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) számára a replikálási forgalmat. Ezen [NSG konfiguráción](azure-to-azure-about-networking.md#example-nsg-configuration)keresztül is tesztelheti és ellenőrizheti ezeket a szükséges szabályokat.

A Site Recovery nem hozza létre vagy replikálja a NSG a feladatátvételi művelet részeként. Azt javasoljuk, hogy a feladatátvétel megkezdése előtt hozza létre a szükséges NSG a cél Azure-régióban. Ezután társíthatja a NSG a feladatátvétel során automatikusan átesett virtuális gépekhez, Site Recovery hatékony [helyreállítási terveivel](site-recovery-create-recovery-plans.md)rendelkező Automation-parancsfájlok használatával.

A korábban ismertetett [példa példáját](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) figyelembe véve:
-    Site Recovery létrehozhat **contoso VNet** és **contoso alhálózattal** rendelkező replikákat a cél Azure-régióban, ha a virtuális gép replikációja engedélyezve van.
-    Az **alhálózati NSG** és a VIRTUÁLISGÉP- **NSG** (például a **célként megadott alhálózat NSG** és a célként megadott virtuálisgép- **NSG**) kívánt replikáit a cél Azure-régióban is létrehozhatja, ami lehetővé teszi, hogy a megcélzott régióban szükséges további szabályok is érvényesek legyenek.
-    A **célként megadott ALHÁLÓZAT NSG** azonnal társítva lehet a célként megadott alhálózathoz, mivel a NSG és az alhálózat is már elérhető.
-    A **cél virtuális gépek NSG** a feladatátvétel során a helyreállítási tervekkel lehet társítani.

A NSG létrehozása és konfigurálása után javasoljuk, hogy futtasson egy [feladatátvételi tesztet](azure-to-azure-tutorial-dr-drill.md) a megírt NSG-társítások és a feladatátvétel utáni Virtuálisgép-kapcsolat ellenőrzéséhez.

## <a name="next-steps"></a>Következő lépések
-    További információ a [hálózati biztonsági csoportokról](../virtual-network/network-security-groups-overview.md#network-security-groups).
-    További információ a NSG [biztonsági szabályairól](../virtual-network/network-security-groups-overview.md#security-rules).
-    További információ az NSG [érvényes biztonsági szabályairól](../virtual-network/diagnose-network-traffic-filter-problem.md) .
-    További információ az alkalmazások feladatátvételének automatizálására szolgáló [helyreállítási tervekről](site-recovery-create-recovery-plans.md) .