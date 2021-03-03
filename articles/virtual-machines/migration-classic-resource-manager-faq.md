---
title: A klasszikusból Azure Resource Manager-alapú környezetbe való migrálásra vonatkozó gyakori kérdések
description: Gyakori kérdések a klasszikusról Azure Resource Manager-re való áttelepítésről.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 4e211389809644dc1c573b3d717ff0e478494b89
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670350"
---
# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>A klasszikusból Azure Resource Manager-alapú környezetbe való migrálásra vonatkozó gyakori kérdések

> [!IMPORTANT]
> Napjainkban a IaaS virtuális gépek 90%-a [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)használ. 2020. február 28-án a klasszikus virtuális gépek elavultak, és 2023. március 1-jén teljesen megszűnnek. [További]( https://aka.ms/classicvmretirement) információ erről az elavult szolgáltatásról, valamint arról, [hogy Ön hogyan befolyásolja Önt](./classic-vm-deprecation.md#how-does-this-affect-me).

## <a name="what-is-azure-service-manager-and-what-does-it-mean-by-classic"></a>Mi az Azure Service Manager és mit jelent a klasszikus?

A "klasszikus" szó a IaaS VM-ben (klasszikus) az Azure Service Manager (ASM) által felügyelt virtuális gépekre vonatkozik. Az Azure Service Manager (ASM) az Azure régi ellenőrző gépe, amely felelős a virtuális gépek létrehozásához, kezeléséhez, törléséhez és más vezérlési sík műveletek végrehajtásához. 

## <a name="what-is-azure-resource-manager"></a>Mi az Azure Resource Manager?

A [Azure Resource Manager](../azure-resource-manager/management/overview.md) az Azure legújabb ellenőrző gépe, amely a virtuális gépek létrehozásához, kezeléséhez, törléséhez és más vezérlési sík műveletek végrehajtásához felelős. 

## <a name="what-is-the-time-required-for-migration"></a>Mennyi idő szükséges az áttelepítéshez?

Az áttelepítés megtervezése és végrehajtása nagy mértékben függ az architektúra összetettségétől, és eltarthat néhány hónapig.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>Mi a IaaS virtuális gépek (klasszikus) új ügyfelének definíciója?

Azok az ügyfelek, akik nem rendelkeznek a IaaS virtuális gépekkel (klasszikus) az előfizetésekben, 2020 februárjában (egy hónappal az elavultság megkezdése előtt) új ügyfélnek számítanak. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>Mi a IaaS Virtual Machines (klasszikus) meglévő ügyfelének definíciója?

Azok az ügyfelek, akik aktívak vagy leállítottak, de a IaaS virtuális gépeket (klasszikus) a 2020. február hónapjában foglalták le, egy meglévő ügyfélnek számítanak. Csak a 2023. március 1. előtt kapják meg a virtuális gépeket az Azure Service Managerról Azure Resource Managerba. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Miért kapok hibaüzenetet, amely a "NewClassicVMCreationNotAllowedForSubscription"?

A nyugdíjazási folyamat részeként a IaaS VM (klasszikus) már nem érhető el az új ügyfelek számára. Új ügyfelekként azonosította Önt, ezért a művelet nem volt engedélyezve. Javasoljuk Azure Resource Manager használatát. Ha Azure Resource Manager használatával nem tud Azure-beli virtuális gépeket használni, forduljon az ügyfélszolgálathoz, és adja hozzá az előfizetését az engedélyezési listához.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Érinti ez a migrálási terv az Azure virtuális gépeken futó meglévő szolgáltatásaimat és alkalmazásaimat? 

A IaaS virtuális gépek (klasszikus) 2023. március 1-ig nem. A IaaS virtuális gépek (klasszikus) teljes körűen támogatott szolgáltatások, általánosan elérhetők. Továbbra is használhatja ezeket az erőforrásokat, és akár ki is terjesztheti működését a Microsoft Azure-ban. 2023. március 1-től ezek a virtuális gépek teljes mértékben kimaradnak, és minden aktív vagy lefoglalt virtuális gép leáll & fel lesz foglalva.

Más klasszikus erőforrások, például a Cloud Services (klasszikus), a Storage-fiókok (klasszikus) stb. nem lesznek hatással.

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Mi történik a virtuális gépeimmel, ha nem tervezek migrálni a közeljövőben? 

2023. március 1-től a IaaS virtuális gépek (klasszikus) teljes mértékben ki lesznek vonva, és minden aktív vagy lefoglalt virtuális gép leáll & fel lesz foglalva. Az üzleti hatás elkerülése érdekében az áttelepítés megtervezése még ma megkezdődött, és 2023. március 1. előtt elvégezhető. Nem elavult a meglévő klasszikus API-k, Cloud Services és az erőforrás-modell. Szeretnénk könnyebbé tenni a migrálást, figyelembe véve a Resource Manager-alapú üzemi modellben elérhető fejlett szolgáltatásokat. Javasoljuk, hogy az erőforrások áttelepítésének megtervezése Azure Resource Manager. 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Milyen hatással lesz a migrálási terv a meglévő eszközállományomra? 

Az eszközállomány a Resource Manager-alapú üzemi modellre való frissítése az egyik legjelentősebb változás, amellyel számolnia kell a migrálási tervben.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Milyen hosszú lesz a felügyeleti sík leállása? 

Ez a migrált erőforrások számától függ. A kisebb környezetek esetében (néhány tucat virtuális gépig) a teljes migrálás kevesebb mint egy órát tart majd. Nagyobb méretű környezetek esetében (virtuális gépek százai) a migrálás több óráig is eltarthat.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Visszaválthatok majd, miután a migrált erőforrások véglegesítve lettek a Resource Managerben? 

A migrálás bármikor megszakítható, amíg az erőforrások előkészített állapotban vannak. Az erőforrásoknak a véglegesítés művelettel való sikeres migrálását követően a visszaállás nem támogatott.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Visszafordíthatom a migrálást, ha a véglegesítési művelet meghiúsul? 

A migrálás nem szakítható meg, ha a véglegesítés művelet meghiúsul. Minden migrálási művelet, beleértve a véglegesítés műveletet is, idempotens. Ezért azt javasoljuk, hogy rövid idő elteltével próbálkozzon újra a művelettel. Ha továbbra is hibát észlel, hozzon létre egy támogatási jegyet.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Kell új ExpressRoute-kapcsolatcsoportot beszereznem, ha az IaaS-t a Resource Manager alatt kell használnom? 

Nem. Nemrégiben lehetővé tettük [az ExpressRoute-kapcsolatcsoportok áthelyezését a klasszikusból a Resource Manager-alapú üzemi modellbe](../expressroute/expressroute-move.md). Ha már rendelkezik ExpressRoute-kapcsolatcsoporttal, nem kell újat vásárolnia.

## <a name="what-if-i-had-configured-azure-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Mi a teendő, ha az Azure szerepköralapú hozzáférés-vezérlési szabályzatokat konfiguráltam a klasszikus IaaS-erőforrásokhoz? 

A migrálás során az erőforrások át lesznek alakítva klasszikusból Resource Manager-alapú erőforrásokká. Ezért azt javasoljuk, hogy tervezze meg az Azure RBAC-szabályzat frissítéseit, amelyek az áttelepítés után történnek.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Biztonsági másolatot készítettem a klasszikus virtuális gépekről egy tárolóban. Áttelepíthetem a virtuális gépeimet a klasszikus módból Resource Manager módba, hogy egy Recovery Services-tárolóban védjem őket?

Ha a virtuális gépet a klasszikusról Resource Manager üzemmódba helyezi át, a Migrálás előtt készített biztonsági másolatok nem települnek át az újonnan áttelepített Resource Manager-alapú virtuális gépre. Ha azonban szeretné megőrizni a klasszikus virtuális gépek biztonsági mentését, kövesse az alábbi lépéseket az áttelepítés előtt. 

1. A Recovery Services- **tárolóban lépjen a biztonsági mentési elemek** panelre, és válassza ki a virtuális gépet. 
2. Kattintson a biztonsági mentés leállítása elemre. Válassza a "biztonsági másolati adat megőrzése" lehetőséget a legördülő menüben.

> [!NOTE]
> Ezzel a beállítással leállíthatja a virtuális gép védelmének jövőbeli biztonsági mentési feladatait. A Azure Backup szolgáltatás azonban megőrzi a biztonsági mentés alatt álló helyreállítási pontokat.  A helyreállítási pontok megőrzéséhez a tárolóban kell fizetnie (lásd a részletek [Azure Backup díjszabását](https://azure.microsoft.com/pricing/details/backup/) ). Szükség esetén visszaállíthatja a virtuális gépet. Ha úgy dönt, hogy folytatja a virtuális gépek védelmét, akkor a *biztonsági mentés folytatása* lehetőséget használhatja.
>
>

A virtuális gép áttelepíthető Resource Manager módba, 

1. Törölje a biztonsági mentés/pillanatkép bővítményt a virtuális gépről.
2. Telepítse át a virtuális gépet a klasszikus módból a Resource Manager módba. A virtuális gépnek megfelelő tároló és hálózat adatait is mindenképpen telepítse át Resource Manager módba.

Ha az áttelepített virtuális gépre biztonsági mentést szeretne készíteni, a [biztonsági mentés engedélyezéséhez](../backup/quick-backup-vm-portal.md#enable-backup-on-a-vm)lépjen a virtuális gép kezelése panelre.

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Ellenőrizhetem valahol, hogy az előfizetésem vagy az erőforrásaim esetében lehetséges-e a migrálás? 

Igen. A platform által támogatott migrálás esetében a migrálás előkészítésének első lépése annak ellenőrzése, hogy az adott erőforrások esetében lehetséges-e a migrálás. Ha az ellenőrzési művelet meghiúsul, értesítést kap a migrálást akadályozó összes tényezőről.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Mi történik, ha kvótahibát tapasztalok az IaaS-erőforrások migrálásra való előkészítése során? 

Javasoljuk, hogy szakítsa meg a migrálási folyamatot, és adjon fel egy támogatási jegyet a kvóta növelésére a régióban, ahová a virtuális gépeket migrálja. A kvótakérelem jóváhagyását követően tovább folytathatja a migrálási folyamat lépéseinek végrehajtását.

## <a name="how-do-i-report-an-issue"></a>Hogyan jelenthetem be a hibákat? 

Tegye fel kérdéseit, és kérdezze meg a [Microsoft Q&a virtuális](/answers/topics/azure-virtual-machines.html)gépekre való áttelepítéssel kapcsolatos kérdéseit a fórumbejegyzést classiciaasmigration kulcsszóval. Javasoljuk, hogy minden kérdését ezen a fórumon tegye fel. Ha rendelkezik támogatási szerződéssel, támogatási jegyet is feladhat.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Mit tehetek, ha nem tetszenek az erőforrások a platform által a migrálás során választott nevei? 

A migrálás az összes olyan erőforrás nevét megőrzi, amelyeknek kifejezetten adott nevet a klasszikus üzemi modellben. Egyes esetekben új erőforrások jönnek létre. Például: minden virtuális géphez létrejön egy hálózati adapter. A migrálás során létrehozott új erőforrások nevének megadása jelenleg nem támogatott. Ha szeretné, adja le szavazatait erre a szolgáltatásra az [Azure visszajelzési fórumon](https://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Migrálhatom az engedélyezési hivatkozásokkal rendelkező előfizetések között használt ExpressRoute-kapcsolatcsoportokat? 

Az előfizetések közötti engedélyezési hivatkozásokat használó ExpressRoute-kapcsolatcsoportok állásidő nélküli automatikus migrálása nem lehetséges. Az ezek manuális migrálására vonatkozóan vannak útmutatóink. A szükséges lépésekért és további információkért lásd: [ExpressRoute-kapcsolatcsoportok migrálása a klasszikusból a Resource Manager-alapú üzemi modellbe](../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>A következő üzenet jelenik meg: *"a virtuális gép az ügynök általános állapotát nem üzemkész állapotba jelenti. Ezért a virtuális gép nem telepíthető át. Győződjön meg arról, hogy a virtuálisgép-ügynök az ügynök teljes állapotát készként* jelenti, vagy *a virtuális gép olyan bővítményt tartalmaz, amelynek állapota nem a virtuális gépről származik. Ezért ez a virtuális gép nem telepíthető át. "*

Ez az üzenet akkor jelenik meg, ha a virtuális gép nem rendelkezik kimenő internetkapcsolattal. A virtuálisgép-ügynök a kimenő kapcsolaton keresztül éri el az Azure-tárfiókot az ügynök állapotának öt percenkénti frissítéséhez.


## <a name="next-steps"></a>Következő lépések

Linux esetén:

* [A IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Manager](./migration-classic-resource-manager-overview.md)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md)
* [A IaaS-erőforrások migrálása a Klasszikusból a Azure Resource Managerba a PowerShell használatával](migration-classic-resource-manager-ps.md)
* [A CLI használatával IaaS-erőforrásokat telepíthet át a klasszikusról Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Közösségi eszközök a IaaS-erőforrások Klasszikusból Azure Resource Managerba való áttelepítésének támogatásához](migration-classic-resource-manager-community-tools.md)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md)

Windows esetén:

* [A IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md)
* [A IaaS-erőforrások migrálása a Klasszikusból a Azure Resource Managerba a PowerShell használatával](migration-classic-resource-manager-ps.md)
* [A CLI használatával IaaS-erőforrásokat telepíthet át a klasszikusról Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Közösségi eszközök a IaaS-erőforrások Klasszikusból Azure Resource Managerba való áttelepítésének támogatásához](migration-classic-resource-manager-community-tools.md)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md)