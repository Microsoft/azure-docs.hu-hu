---
title: Az Azure Resource Manager áttekintése
description: Ismerteti, hogyan használja az Azure Resource Manager eszközt erőforrások telepítésére, felügyeletére és hozzáférés-vezérlésére az Azure portálon.
ms.topic: overview
ms.date: 03/25/2021
ms.custom: contperf-fy21q1,contperf-fy21q3-portal
ms.openlocfilehash: 6cd9aa82ad2f8a821ae82a361b3f11b72ca25f7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608544"
---
# <a name="what-is-azure-resource-manager"></a>Mi az Azure Resource Manager?

Az Azure Resource Manager az Azure üzembehelyezési és felügyeleti szolgáltatása. Egy olyan felügyeleti réteget biztosít, amely lehetővé teszi az Azure-fiókban lévő erőforrások létrehozását, frissítését és törlését. Az üzembe helyezés után az erőforrások biztonságossá tételéhez és rendszerezéséhez olyan felügyeleti funkciókat használhat, mint a hozzáférés-vezérlés, a zárolások és a címkék.

Azure Resource Manager sablonokkal (ARM-sablonok) kapcsolatos további tudnivalókért tekintse meg a [sablonok üzembe helyezésének áttekintése](../templates/overview.md)című témakört.

## <a name="consistent-management-layer"></a>Konzisztens felügyeleti réteg

Ha egy felhasználó egy kérelmet küld bármely Azure-eszközből, API-ból vagy SDK-ból, a Resource Manager fogadja a kérelmet. Ezután hitelesíti és engedélyezi a kérelmet. Majd továbbítja a kérelmet az Azure-szolgáltatás felé, amely végrehajtja a kért műveletet. Mivel az összes kérelem kezelése ugyanazon az API-n keresztül történik, az eredmények és lehetőségek azonosak a különböző eszközök esetében.

A következő képen látható, hogy az Azure Resource Manager milyen szerepet játszik az Azure-beli kérelmek kezelésében.

![A Resource Manager kérelmi modellje](./media/overview/consistent-management-layer.png)

A portálon elérhető összes képesség elérhető a PowerShell, az Azure CLI, a REST API-k és az ügyféloldali SDK-k segítségével is. Az eredetileg API-kon keresztül kiadott funkciók a kezdeti kiadástól számított 180 napig jelennek meg a portálon.

## <a name="terminology"></a>Terminológia

Ha új felhasználója az Azure Resource Managernek, találkozhat néhány olyan kifejezéssel, amelyet még nem ismer.

* **erőforrás** – az Azure-on keresztül elérhető felügyelhető elemek. Erőforrásnak számítanak például a virtuális gépek, tárfiókok, webalkalmazások, adatbázisok és virtuális hálózatok. Az erőforráscsoportok, előfizetések, felügyeleti csoportok és címkék is példák az erőforrásokra.
* **erőforráscsoport** – egy olyan tároló, amely egy Azure-megoldáshoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazza azokat az erőforrásokat, amelyeket csoportként szeretne kezelni. A cég szempontjai alapján Ön döntheti el, hogy mely erőforrások tartozzanak ugyanahhoz az erőforráscsoporthoz. Lásd: [erőforráscsoportok](#resource-groups).
* **erőforrás-szolgáltató** – az Azure-erőforrásokat ellátó szolgáltatás. Például egy általános erőforrás `Microsoft.Compute` -szolgáltató, amely a virtuális gép erőforrását adja meg. `Microsoft.Storage` egy másik gyakori erőforrás-szolgáltató. Lásd: [erőforrás-szolgáltatók és-típusok](resource-providers-and-types.md).
* **Resource Manager-sablon** – egy JavaScript Object Notation (JSON) fájl, amely egy vagy több erőforrást határoz meg egy erőforráscsoport, előfizetés, felügyeleti csoport vagy bérlő számára történő üzembe helyezéshez. A sablon erőforrások konzisztens és ismétlődő telepítésére használandó. Lásd: [template Deployment áttekintése](../templates/overview.md).
* **deklaratív szintaxis** – Egy olyan szintaxis, amellyel anélkül határozhatja meg, mit szeretne létrehozni, hogy ehhez programozási parancsok sorozatát kellene megírnia. A Resource Manager-sablon a deklaratív szintaxis egy példája. A fájlban meghatározhatja az Azure-ra telepíteni kívánt infrastruktúra tulajdonságait.  Lásd: [template Deployment áttekintése](../templates/overview.md).

## <a name="the-benefits-of-using-resource-manager"></a>A Resource Manager használatának előnyei

A Resource Managerrel a következő feladatokat végezheti el:

* Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével kezelheti.

* A megoldás összes erőforrását egy csoportként helyezheti üzembe, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.

* Többször ismét üzembe helyezheti a megoldást a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások üzembe helyezése konzisztens lesz.

* Meghatározhatja az erőforrások közötti függőségeket, hogy azok a megfelelő sorrendben legyenek üzembe helyezve.

* Hozzáférés-vezérlés alkalmazása minden szolgáltatáshoz, mivel az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) natív módon integrálva van a felügyeleti platformba.

* Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.

* Az azonos címkén osztozó erőforrások csoportjának költségeit megtekintve egyértelműen tekintheti át a szervezet számláit.

## <a name="understand-scope"></a>A hatókör bemutatása

Az Azure a hatókör négy szintjét biztosítja: [felügyeleti csoportok](../../governance/management-groups/overview.md), előfizetések, [erőforráscsoportok](#resource-groups)és erőforrások. Az alábbi ábra ezekre a rétegekre mutat egy példát.

![Felügyeleti szintek](./media/overview/scope-levels.png)

Felügyeleti beállításokat a hatókörszintek bármelyikéhez megadhat. A kiválasztott szint határozza meg, milyen széles körben lesz alkalmazva a beállítás. Az alacsonyabb szintek öröklik a magasabb szintek beállításait. Ha például alkalmaz egy [szabályzatot](../../governance/policy/overview.md) az előfizetésre, a rendszer az előfizetésben lévő összes erőforráscsoport és erőforrás esetében alkalmazza a szabályzatot. Ha az erőforráscsoport házirendjét alkalmazza, a rendszer az erőforráscsoport és az összes erőforrása esetében alkalmazza a házirendet. Egy másik erőforráscsoport azonban nem rendelkezik ezzel a szabályzat-hozzárendeléssel.

Üzembe helyezhet sablonokat a bérlőkön, a felügyeleti csoportokon, előfizetéseken vagy erőforráscsoportokon.

## <a name="resource-groups"></a>Erőforráscsoportok

Néhány fontos tényezőt érdemes figyelembe venni az erőforráscsoport meghatározásakor:

* Az erőforráscsoport összes erőforrásának ugyanazzal az életciklussal kell rendelkeznie. Egyszerre fogja őket telepíteni, frissíteni és törölni. Ha egy erőforrásnak, például egy kiszolgálónak eltérő üzemi ciklusban kell lennie, egy másik erőforráscsoporthoz kell tartoznia.

* Az egyes erőforrások csak egy erőforráscsoportban létezhetnek.

* Az erőforráscsoporthoz bármikor hozzáadhat, vagy onnan eltávolíthat egy erőforrást.

* Az erőforrásokat áthelyezheti az egyik erőforráscsoportból a másikba. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](move-resource-group-and-subscription.md).

* Az erőforráscsoport erőforrásai különböző régiókban találhatók, mint az erőforráscsoport.

* Egy erőforráscsoport létrehozásakor meg kell adnia az erőforráscsoport helyét. Most felmerülhet Önben a kérdés, hogy „Miért van szüksége egy erőforráscsoportnak helyre? Ha pedig az erőforrások rendelkezhetnek az erőforrástól eltérő hellyel, akkor miért számít egyáltalán az erőforráscsoport helye?” Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Amikor megad egy helyet az erőforráscsoporthoz, meg kell adnia, hogy hol tárolja a metaadatokat. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.

   Ha az erőforráscsoport régiója átmenetileg nem érhető el, az erőforráscsoport erőforrásai nem frissíthetők, mert a metaadatok nem érhetők el. A más régiókban lévő erőforrások továbbra is a várt módon fognak működni, de nem frissítheti őket. A megbízható alkalmazások létrehozásával kapcsolatos további információkért lásd: [megbízható Azure-alkalmazások tervezése](/azure/architecture/checklist/resiliency-per-service).

* Az erőforráscsoport segítségével meghatározhatja a hozzáférés-vezérlési hatókört felügyeleti műveletekhez. Az erőforráscsoportok kezeléséhez [Azure-szabályzatokat](../../governance/policy/overview.md), [Azure-szerepköröket](../../role-based-access-control/role-assignments-portal.md)vagy erőforrás- [zárolásokat](lock-resources.md)rendelhet hozzá.

* Címkéket is [alkalmazhat](tag-resources.md) egy erőforráscsoporthoz. Az erőforráscsoport erőforrásai nem öröklik ezeket a címkéket.

* Egy erőforrás más erőforráscsoportok erőforrásaihoz is csatlakozhat. Ez a forgatókönyv gyakori, ha a két erőforrás kapcsolódik, de nem ugyanaz az életciklus. Használhat például egy olyan webalkalmazást, amely egy másik erőforráscsoporthoz tartozó adatbázishoz kapcsolódik.

* Egy erőforráscsoport törlésekor a rendszer az erőforráscsoport összes erőforrását is törli. További információ arról, hogy a Azure Resource Manager hogyan hangolja össze ezeket a törléseket: [Azure Resource Manager erőforráscsoport és erőforrás törlése](delete-resource-group.md).

* Minden erőforráscsoport esetében akár 800 példányt is üzembe helyezhet. Bizonyos erőforrástípusok [mentesülnek az 800-es példány korlátján](resources-without-resource-group-limit.md). További információ: [erőforráscsoport korlátai](azure-subscription-service-limits.md#resource-group-limits).

* Bizonyos erőforrások létezhetnek egy erőforráscsoporton kívül is. Ezeket az erőforrásokat az [előfizetés](../templates/deploy-to-subscription.md), a [felügyeleti csoport](../templates/deploy-to-management-group.md)vagy a [bérlő](../templates/deploy-to-tenant.md)telepíti. Ezen hatókörök esetében csak bizonyos erőforrástípusok támogatottak.

* Erőforráscsoport létrehozásához használhatja a [portált](manage-resource-groups-portal.md#create-resource-groups), a [PowerShellt](manage-resource-groups-powershell.md#create-resource-groups), az [Azure CLI](manage-resource-groups-cli.md#create-resource-groups)-t vagy egy [ARM-sablont](../templates/deploy-to-subscription.md#resource-groups).

## <a name="resiliency-of-azure-resource-manager"></a>Azure Resource Manager rugalmassága

A Azure Resource Manager szolgáltatás a rugalmasság és a folyamatos rendelkezésre állás érdekében lett kialakítva. A REST API az erőforrás-kezelő és a vezérlési sík műveletei (a `management.azure.com` következőre küldött kérelmek):

* Régiók között elosztva. Egyes szolgáltatások regionálisak.

* Több Availability Zones rendelkező helyen Availability Zones (és régiókban) elosztva.

* Egyetlen logikai adatközponttól sem függ.

* A karbantartási tevékenységekhez soha nem került sor.

Ez a rugalmasság olyan szolgáltatásokra vonatkozik, amelyek a Resource Manageren keresztül kapják meg a kérelmeket. Például Key Vault a rugalmasság előnyeit.

## <a name="next-steps"></a>Következő lépések

* Az Azure-szolgáltatásokon alkalmazott korlátokkal kapcsolatos további információkért lásd: [Azure-előfizetés és-szolgáltatás korlátai, kvótái és megkötései](azure-subscription-service-limits.md).

* További információ az erőforrások áthelyezéséről: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](move-resource-group-and-subscription.md).

* Az erőforrások címkézésével kapcsolatos további tudnivalókért lásd: [címkék használata az Azure-erőforrások rendszerezéséhez](tag-resources.md).

* Az erőforrások zárolásának megismeréséhez lásd: [erőforrások zárolása a váratlan változtatások megelőzése érdekében](lock-resources.md).
