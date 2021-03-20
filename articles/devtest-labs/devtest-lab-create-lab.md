---
title: Labor létrehozása az Azure DevTest Labs szolgáltatásban | Microsoft Docs
description: Ez a cikk végigvezeti a labor létrehozásának folyamatán a Azure Portal és a Azure DevTest Labs használatával.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 9fa33a59dd35bfe3469f30f2349f8a08c45bd5e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92058343"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Labor létrehozása az Azure DevTest Labs szolgáltatásban

Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet erőforráscsoportokat, például virtuális gépeket (VM-eket) magában foglaló infrastruktúra, amely korlátok és kvóták meghatározásával segíti ezen erőforráscsoportok jobb kezelését. Ez a cikk végigvezeti a tesztkörnyezet Azure Portalon való létrehozásának folyamatán.

## <a name="prerequisites"></a>Előfeltételek

Labor létrehozásához a következőkre van szüksége:

* Azure-előfizetés. Az Azure megvásárlási lehetőségeinek ismertetése: [Az Azure megvásárlása](https://azure.microsoft.com/pricing/purchase-options/) vagy [Ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/). Az előfizetés tulajdonosának kell lennie a labor létrehozásához.

## <a name="get-started-with-azure-devtest-labs-in-minutes"></a>Percek alatt elsajátíthatja az Azure DevTest Labs használatát]

A következő hivatkozásra kattintva átkerül a Azure Portal lapra, amely lehetővé teszi, hogy új labort hozzon létre a Azure DevTest Labsban.

[Percek alatt elsajátíthatja az Azure DevTest Labs használatát]](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="fill-out-settings-for-your-new-account"></a>Adja meg az új fiók beállításait

A **DevTest Labs létrehozása** lapon adja meg a következő beállításokat.

> [!TIP]
> Minden oldal alján talál egy hivatkozást, amely lehetővé teszi **egy sablon letöltését az automatizáláshoz**.

### <a name="basic-settings"></a>Alapbeállítások

Alapértelmezés szerint az **alapbeállítások** lap jelenik meg. 

![Alapszintű beállítások](./media/devtest-lab-create-lab/basic-settings.png)

Adja meg az alábbi értékeket:

|Név|Leírás|
|---|---|
|**Előfizetés** | Kötelező. Válassza ki a laborhoz társítani kívánt **Előfizetést**.|
|**Erőforráscsoport**| Kötelező. Adja meg a laborhoz tartozó **erőforráscsoport nevét** . Ha még nem létezik, hozzon létre egy újat.|
|**Labor neve**| Kötelező. Adja meg a labor **nevét** .|
|**Hely**|Kötelező. Válassza ki azt a helyet, ahol a labort tárolni szeretné.|
|**Nyilvános környezetek**| Lásd: [nyilvános környezetek konfigurálása és használata](devtest-lab-configure-use-public-environments.md).

### <a name="auto-shutdown-settings"></a>Automatikus leállítás beállításai

Váltson az **automatikus leállítás** lapra, és tekintse meg a beállításait. Az automatikus leállítás lehetővé teszi, hogy a laborban lévő összes gépet automatikusan leállítsa minden nap ütemezett időpontban.

![Automatikus leállítás lap](./media/devtest-lab-create-lab/auto-shutdown.png)

A lapon engedélyezheti az **automatikus leállítást** , és meghatározhatja a tesztkörnyezet összes virtuális gépe automatikus leállításának paramétereit. Az automatikus rendszerleállítási funkció elsősorban költségkímélő szolgáltatás, amelynek segítségével megadhatja a virtuális gép automatikus leállásának időpontját. A tesztkörnyezet létrehozása után módosíthatja az automatikus rendszerleállítás beállításait, ha követi az [Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet szabályzatainak kezelését](./devtest-lab-set-lab-policy.md#set-auto-shutdown) ismertető témakörben leírt lépéseket.

### <a name="networking"></a>Hálózatkezelés

Labor létrehozásakor a rendszer létrehoz egy alapértelmezett hálózatot. Váltson a **hálózatkezelés** lapra, hogy módosítsa/konfigurálja a beállítást, ahogy szeretné. Válasszon például egy meglévő virtuális hálózatot.

![Hálózatkezelés lap ](./media/devtest-lab-create-lab/networking.png)

### <a name="tags"></a>Címkék

Írja be a **Címkék****NÉV** és **ÉRTÉK** információit, ha a laborban létrehozni kívánt minden erőforráshoz hozzáadott egyéni címkéket szeretne létrehozni. A címkék hasznos segítséget nyújtanak a laborerőforrások kategória alapján való kezeléséhez és rendezéséhez. A címkékről további információért (beleértve a labor létrehozása után a címkék hozzáadását) lásd: [Címke hozzáadása laborhoz](devtest-lab-add-tag.md).

![Címkék lap ](./media/devtest-lab-create-lab/tags.png)

### <a name="review-and-create"></a>Áttekintés és létrehozás

Ha elkészült, válassza a **Létrehozás** lehetőséget. A labor létrehozási folyamatának állapotát a portál oldal jobb felső sarkában látható **értesítések** terület megtekintésével figyelheti. 

![Létrehozás lap](./media/devtest-lab-create-lab/create-1.png)

## <a name="completed-the-creation"></a>Befejeződött a létrehozás

Ha elkészült, az **erőforrás ugrása** gomb jelenik meg az oldal alján és az értesítési ablakban. Másik lehetőségként frissítse az **DevTest Labs** oldalt, hogy az újonnan létrehozott labor megjelenjen a laborok listájában.  

![A szolgáltatás létrehozása](./media/devtest-lab-create-lab/create-2.png)

Nyomja le az **erőforrás** gomb megnyomása gombot, és az új DevTest Labs-fiók kezdőlapján jelenik meg.

![Erőforrás](./media/devtest-lab-create-lab/go-to-resource.png)

A **DevTest Labs** -t a Azure Portal is megkeresheti. Válassza ki az új fiókot a listából, és szerezze be a kezdőlapot. 

![A szolgáltatás létrehozva](./media/devtest-lab-create-lab/created.png)

## <a name="next-steps"></a>Következő lépések

A labor létrehozása után ezeket a lépéseket érdemes figyelembe venni:

* [Biztonságos hozzáférés a laborhoz](devtest-lab-add-devtest-user.md)
* [Tesztkörnyezet-házirendek beállítása](devtest-lab-set-lab-policy.md)
* [Tesztkörnyezet létrehozása](devtest-lab-create-template.md)
* [Egyéni összetevők létrehozása a virtuális gépekhez](devtest-lab-artifact-author.md)
* [Virtuális gép hozzáadása laborhoz](devtest-lab-add-vm.md)

