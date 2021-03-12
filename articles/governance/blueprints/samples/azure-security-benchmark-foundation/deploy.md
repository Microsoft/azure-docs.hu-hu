---
title: Az Azure Security teljesítményteszt Foundation tervének üzembe helyezési mintája
description: Üzembe helyezheti az Azure Security teljesítményteszt Foundation tervezetének lépéseit, beleértve a tervrajz-összetevő paraméterének részleteit.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: af41dd50c976ac6c0570b8a089211fa310ef4ef1
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232613"
---
# <a name="deploy-the-azure-security-benchmark-foundation-blueprint-sample"></a>Az Azure Security teljesítményteszt Foundation tervének üzembe helyezési mintája

Az Azure Security teljesítményteszt Foundation alaptervi mintájának üzembe helyezéséhez a következő lépéseket kell elvégeznie:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free), mielőtt hozzákezd.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először a tervminta implementálásához hozzon létre egy új tervet a környezetében, és kiindulópontként használja a mintát.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg az **Azure Security teljesítményteszt** alaptervének mintáját _más minták_ területen, majd válassza **a minta használata** lehetőséget.

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve**: adjon meg egy nevet az Azure Security teljesítményteszt Foundation Blueprint minta példányának.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az oldal tetején lévő _Összetevők_ lapot, vagy az oldal alján lévő **Következő: Összetevők** lehetőséget.

1. Tekintse át a tervmintát alkotó összetevők listáját. Számos összetevőnek olyan paraméterei vannak, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor végzett a tervminta áttekintésével.

## <a name="publish-the-sample-copy"></a>Ugyanazon másolat közzététele

A tervminta másolata létrejött a környezetében. **Piszkozat** módban jött létre, és **közzé kell tenni**, mielőtt hozzárendelhetné és üzembe helyezhetné. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás az Azure Security teljesítményteszt Foundation tervből is áthelyezhető.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki az oldal tetején található **Terv közzététele** lehetőséget. A jobb oldalt megjelenő új oldalon adja meg a tervminta másolatának **verzióját**. Ez a tulajdonság akkor hasznos, ha később módosításokat végez. Adjon meg olyan **módosítási megjegyzéseket** , mint az "első verzió, amely az Azure Security teljesítményteszt Foundation tervezetének mintája alapján lett közzétéve." Ezután válassza a lap alján található **Közzététel** lehetőséget.

## <a name="assign-the-sample-copy"></a>Ugyanazon másolat hozzárendelése

Miután a tervezet mintájának **közzététele** sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ebben a lépésben adja meg a paramétereket, hogy a tervminta másolatának minden üzemelő példánya egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki a Tervdefiníció oldal tetején található **Terv hozzárendelése** lehetőséget.

1. Adja meg a tervhozzárendelés paraméterértékeit:

   - Alapvető beállítások
       - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, és a terv mintájának másolatát mentette. Ha egynél több előfizetést választ ki, mindegyikhez létrejön egy hozzárendelés a beírt paraméterekkel.
     - **Hozzárendelés neve**: a név előre ki van töltve a terv neve alapján.
       Módosítsa igény szerint, vagy hagyja meg az eredetit.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást.
     - Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja.
       További információ: [felügyelt identitások az Azure-erőforrásokhoz](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: válasszon egy **közzétett** verziót a terv mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezetének megfelelő tervzárolási beállítást. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Válassza ki az alapértelmezett, _rendszer által hozzárendelt_ felügyelt identitás vagy a _felhasználó által hozzárendelt_ felügyelt identitás lehetőséget.

   - Tervparaméterek

     Az ebben a szakaszban meghatározott paramétereket a tervdefinícióban lévő számos összetevő használja a konzisztencia érdekében.
    
     - **Erőforrások és erőforráscsoportok előtagja**: ezt a karakterláncot használja a rendszer az összes erőforrás-és erőforráscsoport-név előtagjaként.
     - **Hub neve**: a központ neve
     - **Napló megőrzése (nap)**: a naplók megőrzésének napjainak száma; a "0" bejegyzés megtartja a naplókat határozatlan ideig
     - Központi **telepítés**: "true" vagy "false" érték megadásával adhatja meg, hogy a hozzárendelés üzembe helyezi-e az architektúra hub-összetevőit.
     - **Központ helye**: a hub erőforráscsoport helye
     - **Cél IP-címei**: cél IP-címek a kimenő kapcsolathoz; IP-címek vagy IP-címtartomány-előtagok vesszővel tagolt listája
     - **Network Watcher neve**: a Network Watcher erőforrás neve
     - **Network Watcher erőforráscsoport neve**: a Network Watcher erőforráscsoport neve
     - **DDoS-védelem engedélyezése**: adja meg az "igaz" vagy a "false" értéket annak megadásához, hogy DDoS Protection engedélyezve van-e a virtuális hálózaton
     
    > [!NOTE] 
    > Ha Network Watcher már engedélyezve van, javasoljuk, hogy használja a meglévő Network Watcher erőforráscsoportot. Meg kell adnia a meglévő Network Watcher erőforráscsoport helyét is a (z) **Network Watcher erőforráscsoport helye** összetevő-paraméterhez.

   - Összetevő paraméterei

     Az ebben a szakaszban megadott paraméterek arra az összetevőre érvényesek, amelyben meg lettek határozva. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza az oldal alján lévő **Hozzárendelés** lehetőséget. Létrejön a terv hozzárendelése, és megkezdődik az összetevő üzembe helyezése. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának ellenőrzéséhez nyissa meg a terv hozzárendelését.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervminták **ingyenesek**. Az Azure-erőforrások [díjszabása termékalapú](https://azure.microsoft.com/pricing/). A [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a tervminta által üzembe helyezett erőforrások futtatásának költségét.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a terv paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|Hubhoz tartozó erőforráscsoport|Erőforráscsoport|Erőforráscsoport neve|Zárolt – előtag összefűzése a hub nevével|
|Hubhoz tartozó erőforráscsoport|Erőforráscsoport|Erőforráscsoport helye|Zárolt – a hub helyét használja|
|Azure Firewall sablon|Resource Manager-sablon|Azure Firewall magánhálózati IP-cím||
|Azure Log Analytics és diagnosztikai sablon|Resource Manager-sablon|Log Analytics munkaterület helye|Log Analytics munkaterület létrehozásának helye; `Get-AzLocation | Where-Object Providers -like 'Microsoft.OperationalInsights' | Select DisplayName` az elérhető régiók megtekintéséhez futtassa az Azure PowersShell|
|Azure Log Analytics és diagnosztikai sablon|Resource Manager-sablon|Azure Automation fiók azonosítója (nem kötelező)|Automation-fiók erőforrás-azonosítója; egy Log Analytics és egy Automation-fiók közötti társított szolgáltatás létrehozásához használatos|
|Azure hálózati biztonsági csoport sablonja|Resource Manager-sablon|NSG-adatforgalmi naplók engedélyezése|A NSG-naplók engedélyezéséhez vagy letiltásához adja meg a "true" vagy a "false" értéket.|
|Azure Virtual Network hub-sablon|Resource Manager-sablon|Virtuális hálózati címek előtagja|Virtuális hálózati címek előtagja a hub Virtual Network számára|
|Azure Virtual Network hub-sablon|Resource Manager-sablon|Tűzfal alhálózati címének előtagja|Tűzfal alhálózati címének előtagja a hub virtuális hálózathoz|
|Azure Virtual Network hub-sablon|Resource Manager-sablon|Megerősített alhálózat címének előtagja|Megerősített alhálózat címének előtagja a hub Virtual Network számára|
|Azure Virtual Network hub-sablon|Resource Manager-sablon|Átjáró alhálózati címének előtagja|Átjáró alhálózati címének előtagja a hub virtuális hálózathoz|
|Azure Virtual Network hub-sablon|Resource Manager-sablon|Felügyeleti alhálózat címének előtagja|A hub virtuális hálózat felügyeleti alhálózati címének előtagja|
|Azure Virtual Network hub-sablon|Resource Manager-sablon|Ugrási mező alhálózati címének előtagja|Ugrási mező alhálózati címének előtagja a hub Virtual Network számára|
|Azure Virtual Network hub-sablon|Resource Manager-sablon|Alhálózati címek nevei (nem kötelező)|A hub virtuális hálózatra telepítendő alhálózati nevek tömbje; például: "subnet1", "subnet2 alhálózattal"|
|Azure Virtual Network hub-sablon|Resource Manager-sablon|Alhálózati címek előtagjai (nem kötelező)|IP-cím előtagjainak tömbje a hub virtuális hálózat opcionális alhálózatai számára; például: "10.0.7.0/24", "10.0.8.0/24"|
|Küllős erőforráscsoport|Erőforráscsoport|Erőforráscsoport neve|Zárolt – előtag összefűzése küllős névvel|
|Küllős erőforráscsoport|Erőforráscsoport|Erőforráscsoport helye|Zárolt – a hub helyét használja|
|Azure Virtual Network küllős sablon|Resource Manager-sablon|Küllő üzembe helyezése|Adja meg az "igaz" vagy a "false" értéket annak megadásához, hogy a hozzárendelés üzembe helyezi-e az architektúra küllős összetevőit.|
|Azure Virtual Network küllős sablon|Resource Manager-sablon|Hub-előfizetés azonosítója|Az az előfizetés-azonosító, amelyben a hub telepítve van; az alapértelmezett érték az az előfizetés, amelyben a terv definíciója található|
|Azure Virtual Network küllős sablon|Resource Manager-sablon|Küllő neve|A küllő neve|
|Azure Virtual Network küllős sablon|Resource Manager-sablon|Virtual Network címzési előtag|Virtual Network a küllős virtuális hálózat címzési előtagja|
|Azure Virtual Network küllős sablon|Resource Manager-sablon|Alhálózat címének előtagja|Küllős virtuális hálózat alhálózati címének előtagja|
|Azure Virtual Network küllős sablon|Resource Manager-sablon|Alhálózati címek nevei (nem kötelező)|A küllős virtuális hálózathoz telepítendő alhálózati nevek tömbje; például: "subnet1", "subnet2 alhálózattal"|
|Azure Virtual Network küllős sablon|Resource Manager-sablon|Alhálózati címek előtagjai (nem kötelező)|IP-cím előtagjainak tömbje a küllős virtuális hálózat választható alhálózatai számára; például: "10.0.7.0/24", "10.0.8.0/24"|
|Azure Virtual Network küllős sablon|Resource Manager-sablon|Küllő üzembe helyezése|Adja meg az "igaz" vagy a "false" értéket annak megadásához, hogy a hozzárendelés üzembe helyezi-e az architektúra küllős összetevőit.|
|Azure Network Watcher-sablon|Resource Manager-sablon|Network Watcher helye|A Network Watcher erőforrás helye|
|Azure Network Watcher-sablon|Resource Manager-sablon|Network Watcher erőforráscsoport helye|Ha a Network Watcher már engedélyezve van, akkor a paraméter értékének **meg kell** egyeznie a meglévő Network Watcher erőforráscsoport helyével.|

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a hibába ütközik `The resource group 'NetworkWatcherRG' failed to deploy due to the
following error: Invalid resource group location '{location}'. The Resource group already exists in
location '{location}'.` , ellenőrizze, hogy a terv paraméter **Network Watcher erőforráscsoport neve** megadja a meglévő Network Watcher erőforráscsoport nevét, valamint azt, hogy az összetevő-paraméter **Network Watcher erőforráscsoport helye** megadja a meglévő Network Watcher erőforráscsoport helyét.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az Azure Security teljesítményteszt Foundation alaptervi minta üzembe helyezésének lépéseit, tekintse meg az alábbi cikket az architektúra megismeréséhez:

> [!div class="nextstepaction"]
> [Az Azure Security teljesítményteszt Foundation tervezete – áttekintés](./index.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
