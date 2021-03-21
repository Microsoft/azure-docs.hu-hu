---
title: A terv életciklusának megismerése
description: Megtudhatja, hogy milyen életciklussal rendelkezik a tervrajzok definíciója, és részletezi az egyes szakaszokat, beleértve a terv-Hozzárendelések frissítését és eltávolítását
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 721a198b5732af01c6712e86bc0c8e8ef543b404
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98918517"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Egy Azure Blueprint életciklusának megismerése

Az Azure-ban sok erőforráshoz hasonlóan az Azure-tervrajzok egy tipikus és természetes életciklusa is van. Ezek létrehozása, üzembe helyezése és végleges törlése, ha már nincs rá szükség vagy releváns. Az Azure-tervrajzok támogatják a szabványos életciklus-műveleteket. Ezután olyan további állapotot biztosít, amely támogatja a közös folyamatos integrációt és a folyamatos üzembe helyezési folyamatokat olyan szervezetek számára, amelyek az infrastruktúrát kódként kezelik – a DevOps kulcsfontosságú elemeként.

A tervrajzok és a szakaszok teljes körű megismeréséhez a standard életciklust fogjuk lefedni:

> [!div class="checklist"]
> - Terv létrehozása és szerkesztése
> - A terv közzététele
> - A terv új verziójának létrehozása és szerkesztése
> - A terv új verziójának közzététele
> - A terv adott verziójának törlése
> - A terv törlése

## <a name="creating-and-editing-a-blueprint"></a>Terv létrehozása és szerkesztése

Tervrajz létrehozásakor vegyen fel összetevőket hozzá, mentse egy felügyeleti csoportba vagy előfizetésbe, és adjon meg egy egyedi nevet és egy egyedi verziót. A terv most már **Vázlat** módban van, és még nem rendelhető hozzá. A **Piszkozat** módban továbbra is frissíthető és módosítható.

A **Piszkozat** módban soha nem közzétett tervezetek egy másik ikont jelenítenek meg a **terv definíciók** lapján, amely **közzé lett téve**. A **legújabb verzió** **piszkozatként** jelenik meg a soha nem közzétett tervekhez.

Hozzon létre és szerkesszen egy tervet [Azure Portal](../create-blueprint-portal.md#create-a-blueprint) vagy [REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Tervezet közzététele

Ha az összes tervezett módosítás **vázlatos** módban történt, **közzé** kell tenni a hozzárendeléshez, és elérhetővé teheti azt. A terv **közzétett** verziója nem módosítható. A **Közzététel** után a terv egy másik ikonnal jelenik meg, mint a **tervezetek** , és a megadott verziószámot jeleníti meg a **legújabb verzió** oszlopban.

Tegyen közzé egy tervet [Azure Portal](../create-blueprint-portal.md#publish-a-blueprint) vagy [REST API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>A terv új verziójának létrehozása és szerkesztése

A terv **közzétett** verziója nem módosítható. A terv új verziója azonban felvehető a meglévő tervbe, és szükség szerint módosítható. Módosításokat végezhet egy meglévő terv szerkesztésével. Az új módosítások mentésekor a terv már nem **közzétett módosításokat** tartalmaz. Ezek a változások a terv új **vázlatos** változata.

Szerkesszen egy tervet a [Azure Portal](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>A terv új verziójának közzététele

A terv minden szerkesztett verzióját **közzé** kell tenni ahhoz, hogy hozzá lehessen rendelni. Ha nem **közzétett módosításokat** hajtottak végre egy tervben, de még nem **tették közzé**, **a terv szerkesztése gomb elérhető** a terv szerkesztése lapon. Ha a gomb nem látható, a terv már közzé van **téve** , és nincsenek **közzé nem tett módosítások**.

> [!NOTE]
> Egyetlen tervezet több **közzétett** verziót is tartalmazhat, amelyek mindegyike előfizetésekhez rendelhető.

Ha közzé szeretne tenni egy tervet **közzé nem tett változásokkal**, ugyanezeket a lépéseket követve közzétehet egy új tervet.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>A terv adott verziójának törlése

A tervrajzok minden verziója egy egyedi objektum, amelyet egyénileg lehet **közzétenni**. Ennek megfelelően a tervrajzok minden verziója törölhető is. A tervrajzok egy verziójának törlése semmilyen hatással nincs a terv más verzióira.

> [!NOTE]
> Aktív hozzárendelésekkel rendelkező tervet nem lehet törölni. Először törölje a hozzárendeléseket, majd törölje az eltávolítani kívánt verziót.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza ki a bal oldalon található **tervezet-definíciókat** , és a szűrési beállítások segítségével keresse meg azt a tervet, amelynek a verzióját törölni szeretné. Válassza ki a szerkesztési oldal megnyitásához.

1. Válassza a **közzétett verziók** fület, és keresse meg a törölni kívánt verziót.

1. Kattintson a jobb gombbal a törölni kívánt verzióra, majd válassza a **verzió törlése** lehetőséget.

## <a name="deleting-the-blueprint"></a>A terv törlése

A központi terv is törölhető. Az alapszintű terv törlése szintén törli az adott terv tervrajzait, beleértve a **vázlatos** és a **közzétett** tervezeteket is. A tervrajzok egy verziójának törléséhez hasonlóan az alapterv törlése nem távolítja el a tervrajzok bármely verziójának meglévő hozzárendeléseit.

> [!NOTE]
> Aktív hozzárendelésekkel rendelkező tervet nem lehet törölni. Először törölje a hozzárendeléseket, majd törölje az eltávolítani kívánt verziót.

Egy terv törlése a [Azure Portal](../create-blueprint-portal.md#delete-a-blueprint) vagy [REST API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Hozzárendelések

Az életciklus során több pont is van, és egy terv rendelhető hozzá egy előfizetéshez. Ha **közzéteszi** a terv egy verziójának a módját, az adott verziót hozzá lehet rendelni egy előfizetéshez. Ez az életciklus lehetővé teszi, hogy egy terv egy újabb verziójának fejlesztésekor felhasználja és aktívan hozzárendelje a tervrajzok verzióját.

A tervrajzok verziójának kiosztásakor fontos megérteni, hogy hol vannak hozzárendelve, és hogy milyen paramétereket rendeltek hozzájuk. A paraméterek lehetnek statikusak vagy dinamikusak. További információ: [statikus és dinamikus paraméterek](./parameters.md).

### <a name="updating-assignments"></a>Hozzárendelések frissítése

Tervezet hozzárendelésekor a hozzárendelés frissíthető. A meglévő hozzárendelések frissítése több okból is lehetséges, többek között:

- [Erőforrás-zárolás](./resource-locking.md) hozzáadása vagy eltávolítása
- [Dinamikus paraméterek](./parameters.md#dynamic-parameters) értékének módosítása
- A hozzárendelés frissítése a terv újabb **közzétett** verziójára

További információ: a [meglévő hozzárendelések frissítése](../how-to/update-existing-assignments.md).

### <a name="unassigning-assignments"></a>Hozzárendelések hozzárendelésének megszüntetése

Ha a terv már nem szükséges, a felügyeleti csoportból vagy előfizetésből nem lehet kiosztani. A terv hozzárendelésének megszüntetése során a következők történnek:

- A [terv erőforrás-zárolásának](./resource-locking.md) eltávolítása
- A terv-hozzárendelési objektum törlése
- Feltételes Ha egy **rendszerhez rendelt felügyelt identitást** használt, akkor azt is törli

> [!NOTE]
> A terv-hozzárendelés által üzembe helyezett összes erőforrás továbbra is érvényben marad, de az Azure-tervezetek már nem védik őket.

## <a name="next-steps"></a>Következő lépések

- A [statikus és dinamikus paraméterek](./parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](./sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](./resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).