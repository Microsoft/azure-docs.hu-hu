---
title: Az Azure Resource Graph áttekintése
description: Ismerje meg, hogy az Azure Resource Graph szolgáltatás hogyan teszi lehetővé az erőforrások összetett lekérdezését az előfizetések és a bérlők között.
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: b5df124d07b8ecfb20f5dec08830d8156e8df2cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919141"
---
# <a name="what-is-azure-resource-graph"></a>Mi az az Azure Resource Graph?

Az Azure Resource Graph egy Azure-beli szolgáltatás, amely az Azure Erőforrás-kezelés kibővítésére szolgál azáltal, hogy hatékony és teljesítménybeli erőforrás-feltárást biztosít, és lehetővé teszi az adott előfizetések nagy léptékű lekérdezését, így hatékonyan szabályozhatja a környezetét. Ezek a lekérdezések a következő funkciókat biztosítják:

- Erőforrások lekérdezése az erőforrás-tulajdonságok alapján végzett összetett szűréssel, csoportosítással és rendezéssel.
- Az erőforrások az irányítási követelmények alapján iteratív.
- Szabályzatok alkalmazásából adódó következmények felmérése kiterjedt felhőkörnyezetben.
- [Részletes adatok az erőforrás-tulajdonságok](./how-to/get-resource-changes.md) (előzetes verzió) változásairól.

Ez a dokumentáció mindegyik funkciót részletesen tárgyalja.

> [!NOTE]
> Az Azure Resource Graph a Azure Portal keresési sávját, az új "minden erőforrás" funkciót és a Azure Policy [változási előzményeinek](../policy/how-to/determine-non-compliance.md#change-history) 
>  _vizuális diff_ elemeit használja. Ez úgy lett kialakítva, hogy segítse az ügyfeleknek a nagyméretű környezetek kezelését.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hogyan egészíti ki a Resource Graph az Azure Resource Managert

A Resource Manager jelenleg támogatja az alapvető erőforrások mezőin keresztüli lekérdezéseket, különösen az erőforrás neve, az azonosító, a típus, az erőforráscsoport, az előfizetés és a hely alapján. A Resource Manager emellett olyan létesítményeket is biztosít, amelyekkel az egyes erőforrás-szolgáltatók meghívhatók a részletes tulajdonságok egy erőforrással egyidejűleg.

Az Azure Resource Graph segítségével az erőforrás-szolgáltatók egyenkénti hívása nélkül is hozzáférhet az általuk visszaadott tulajdonságokhoz. A támogatott erőforrástípusok listáját a [tábla és az erőforrás típusára vonatkozó referenciában](./reference/supported-tables-resources.md)tekintheti meg. A támogatott erőforrástípusok megjelenítésének másik módja az [Azure Resource Graph Explorer sémakezelő böngészője](./first-query-portal.md#schema-browser).

Az Azure Resource Graph segítségével a következőket teheti:

- Az erőforrás-szolgáltatók által visszaadott tulajdonságok elérése anélkül, hogy az egyes erőforrás-szolgáltatóknak külön hívásokat kellene végezniük.
- Megtekintheti az Erőforrás változási előzményeinek utolsó 14 napját, és megtekintheti, hogy milyen tulajdonságok változtak és mikor. (előzetes verzió)

> [!NOTE]
> _Előzetes_ `type` verzióként egyes objektumokhoz további, nem Resource Manager-tulajdonságok érhetők el. További információ: [kiterjesztett tulajdonságok (előzetes verzió)](./concepts/query-language.md#extended-properties).

## <a name="how-resource-graph-is-kept-current"></a>Az erőforrás-gráf naprakészen tartása

Az Azure-erőforrások frissítésekor a változás erőforrás-kezelője értesíti az erőforrás-diagramot.
Az erőforrás-gráf ezután frissíti az adatbázisát. Az erőforrás-gráf emellett normál _teljes vizsgálatot_ is végez. Ez a vizsgálat biztosítja, hogy az erőforrás-gráf-adat aktuális, ha kimaradt értesítések vannak, vagy ha egy erőforrást a Resource Manageren kívülről frissítenek.

> [!NOTE]
> Az erőforrás-gráf a `GET` -t használja az egyes erőforrás-szolgáltatók legújabb, nem előzetes VERZIÓJÚ API-jával a tulajdonságok és értékek összegyűjtéséhez. Ennek eredményeképpen előfordulhat, hogy a várt tulajdonság nem érhető el. Bizonyos esetekben a használt API-verzió felülbírálva lett, hogy az eredményekben még több aktuális vagy széles körben használt tulajdonság legyen elérhető. A környezet teljes listájáért tekintse meg az [API-verzió megjelenítése az egyes erőforrástípus](./samples/advanced.md#apiversion) -mintákhoz című témakört.

## <a name="the-query-language"></a>A lekérdezőnyelv

Most, hogy jobban megértette az Azure-erőforrások Gráfját, nézzük meg, hogyan lehet lekérdezéseket felépíteni.

Fontos tisztában lenni azzal, hogy az Azure Resource Graph lekérdezési nyelve az Azure Adatkezelő által használt [Kusto-lekérdezési nyelven](/azure/data-explorer/data-explorer-overview) alapul.

Első lépésként olvassa el az Azure Resource Graphfal használható műveleteket és funkciókat ismertető, [a Resource Graph lekérdezőnyelve](./concepts/query-language.md) című cikket. Az erőforrások tallózását az [erőforrások kezeléséről](./concepts/explore-resources.md) szóló cikk írja le.

## <a name="permissions-in-azure-resource-graph"></a>Engedélyek az Azure Resource Graphban

Az erőforrás-gráf használatához megfelelő jogokkal kell rendelkeznie az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) szolgáltatásban, legalább olvasási hozzáféréssel a lekérdezni kívánt erőforrásokhoz. Ha nem rendelkezik legalább `read` engedélyekkel az Azure-objektumhoz vagy -objektumcsoporthoz, a rendszer nem ad vissza eredményeket.

> [!NOTE]
> Az erőforrás-diagram a rendszerbiztonsági tag számára elérhető előfizetéseket használja a bejelentkezés során. Egy aktív munkamenet során hozzáadott új előfizetés erőforrásainak megtekintéséhez a rendszerbiztonsági tag frissítenie kell a környezetet. Ez a művelet automatikusan megtörténik a kijelentkezéskor és vissza.

Az Azure CLI és Azure PowerShell olyan előfizetéseket használ, amelyekhez a felhasználónak hozzáférése van. REST API közvetlen használatakor az előfizetési listát a felhasználó kapja meg. Ha a felhasználó hozzáfér a listában szereplő egyik előfizetéshez, a rendszer visszaadja a lekérdezés eredményét azon előfizetések esetében, amelyekhez a felhasználónak hozzáférése van. Ez a viselkedés ugyanaz, mint az erőforráscsoportok meghívásakor [– listázza](/rest/api/resources/resourcegroups/list) \- azokat az erőforráscsoportokat, amelyekhez hozzáférése van, anélkül, hogy az eredmény részleges lenne. Ha nincsenek olyan előfizetések az előfizetések listájában, amelyhez a felhasználó megfelelő jogosultsággal rendelkezik, a válasz _403_ (tiltott).

> [!NOTE]
> Az **előzetes** verzió REST API verziója esetében `2020-04-01-preview` Előfordulhat, hogy az előfizetések listája ommitted.
> Ha mind a `subscriptions` , mind a `managementGroupId` tulajdonság nincs definiálva a kérelemben, a _hatókör_ a bérlőre lesz beállítva. További információ: [a lekérdezés hatóköre](./concepts/query-language.md#query-scope).

## <a name="throttling"></a>Throttling

Ingyenes szolgáltatásként az erőforrás-gráfra irányuló lekérdezések szabályozva vannak, így biztosítva a legjobb élményt és a válaszadási időt az összes ügyfél számára. Ha a szervezet a nagyméretű és gyakori lekérdezésekhez Graph API erőforrást szeretné használni, használja a "visszajelzés" lehetőséget az erőforrás- [gráf portál oldalon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Adja meg az üzleti esetét, és válassza a "Microsoft e-mail küldése a visszajelzéshez" jelölőnégyzetet, hogy a csapat felvegye Önnel a kapcsolatot.

Az erőforrás-gráf a lekérdezéseket a felhasználói szinten szabályozza. A szolgáltatás válasza a következő HTTP-fejléceket tartalmazza:

- `x-ms-user-quota-remaining` (int): a felhasználó fennmaradó erőforrás-kvótája. Ez az érték leképezi a lekérdezések darabszámát.
- `x-ms-user-quota-resets-after` (óó: PP: mm): az időtartam, amíg a felhasználó kvótájának felhasználását vissza nem állítja

További információ: [útmutatás a szabályozott kérelmekhez](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Az első lekérdezés futtatása

A Azure Portal részét képező Azure Resource Graph Explorer lehetővé teszi, hogy közvetlenül a Azure Portalban futtassa az erőforrás-gráf lekérdezéseit. Dinamikus diagramként rögzítheti az eredményeket, hogy valós idejű dinamikus információkat nyújtson a portál munkafolyamatainak. További információ: [első lekérdezés az Azure Resource Graph Explorerben](./first-query-portal.md).

Az erőforrás-gráf támogatja az Azure CLI-t, a Azure PowerShell, a Pythonhoz készült Azure SDK-t és egyebeket. A lekérdezés minden nyelv esetében azonos szerkezetű. Ismerje meg, hogyan engedélyezhető az erőforrás-gráf a következővel:

- [Azure Portal és Resource Graph Explorer](./first-query-portal.md) 
- [Azure CLI](./first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](./first-query-powershell.md#add-the-resource-graph-module)
- [Python](./first-query-python.md#add-the-resource-graph-library)

## <a name="next-steps"></a>Következő lépések

- További információ a [lekérdezési nyelvről](./concepts/query-language.md).
- Tekintse meg az [alapszintű lekérdezésekben](./samples/starter.md)használt nyelvet.
- Lásd: speciális alkalmazások a [speciális lekérdezésekben](./samples/advanced.md).
