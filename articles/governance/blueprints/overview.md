---
title: Az Azure Blueprints áttekintése
description: Ismerje meg, hogyan hozhat létre, határozhat meg és helyezhet üzembe összetevőket az Azure-környezetben az Azure BluePrints szolgáltatásban.
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: f4ba77f5fcb376bf600d94997b0d6ba569f04f82
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919342"
---
# <a name="what-is-azure-blueprints"></a>Mi az az Azure Blueprints?

Hasonlóan ahhoz, ahogyan a tervekkel a mérnökök és az építészek felvázolják egy projekt tervezési paramétereit, az Azure Blueprints segítségével a felhőmérnökök és központi informatikai csoportok meghatározhatnak egy megismételhető Azure-erőforráskészletet, amely megvalósítja és betartja a vállalat szabványait, mintázatait és követelményeit. Az Azure-tervezetek lehetővé teszik a fejlesztői csapatok számára, hogy gyorsan építsenek és hozzanak létre olyan új környezeteket, amelyek megbízhatósági kapcsolatban állnak a szervezeti megfelelőségben a beépített összetevők, például a hálózatkezelés, a fejlesztés és a teljesítés felgyorsításához.

A tervekkel deklaratív módon, összehangoltan helyezhetők üzembe különböző erőforrássablonok és egyéb tervelemek, például a következők:

- Szerepkör-hozzárendelések
- Szabályzat-hozzárendelések
- Azure Resource Manager sablonok (ARM-sablonok)
- Erőforráscsoportok

Az Azure Blueprints szolgáltatást a globális terjesztésű [Azure Cosmos DB](../../cosmos-db/introduction.md) támogatja. A tervobjektumok több Azure-régióba vannak replikálva. Ez a replikálás alacsony késést, magas rendelkezésre állást és konzisztens hozzáférést biztosít a terv objektumaihoz, függetlenül attól, hogy az Azure-tervrajzok mely régiókban helyezik üzembe az erőforrásokat.

## <a name="how-its-different-from-arm-templates"></a>Miben különbözik az ARM-sablonoktól

A szolgáltatás úgy van megtervezve, hogy segítsen a _környezet beállításában_. Ez a beállítás gyakran erőforráscsoport, szabályzatok, szerepkör-hozzárendelések és ARM-sablonok központi telepítéseit tartalmazza. A terv egy olyan csomag, amely egyesíti az egyes _összetevők típusait_ , és lehetővé teszi a csomag összeállítását és verzióját, beleértve a folyamatos integrációs és folyamatos kézbesítési (CI/CD) folyamatokat. Végül minden csomag egy előfizetéshez van rendelve egyetlen műveletben, amely felügyelhető és nyomon követhető.

Szinte minden, amit az Azure-tervezetekben szeretne felvenni az üzembe helyezéshez, ARM-sablonnal is elvégezhető. Az ARM-sablon azonban olyan dokumentum, amely nem létezik natív módon az Azure-ban – mindegyiket helyileg vagy a verziókövetés tárolja. A sablonok felhasználhatók egy vagy több Azure-erőforrás üzembe helyezéséhez, az erőforrások üzembe helyezése után azonban a sablonnal létesített aktív kapcsolat megszűnik.

Az Azure-tervezetek esetében a terv meghatározása (mit _kell_ telepíteni) és a terv-hozzárendelés (mi _volt_ az üzembe helyezett) közötti kapcsolat megmarad. E kapcsolatnak köszönhetően hatékonyabban követhetők nyomon és naplózhatók az üzembe helyezett összetevők. Az Azure-tervrajzok egyszerre több előfizetést is frissíthetnek, amelyekre ugyanezen terv vonatkozik.

Nem kell választania egy ARM-sablon és egy terv között. Minden tervezet nulla vagy több ARM _-sablonból_ állhat. Ez a támogatás azt jelenti, hogy az ARM-sablonok könyvtárainak fejlesztésére és karbantartására tett korábbi erőfeszítések az Azure-tervezetekben újrafelhasználhatók.

## <a name="how-its-different-from-azure-policy"></a>Eltérések az Azure Policyhez képest

A tervek olyan csomagok vagy tárolók, amelyekben az Azure-felhőszolgáltatások megvalósításával kapcsolatos olyan fókuszspecifikus szabványok, minták és követelmények állíthatók össze, amelyek később újból felhasználhatók a konzisztencia és a megfelelőség fenntartása érdekében.

A [szabályzatok](../policy/overview.md) olyan alapértelmezett engedélyezési / explicit megtagadási rendszerek, amelyek az erőforrások tulajdonságaira összpontosítanak az üzembe helyezés során és a meglévő erőforrások esetében. Segítik a felhőszabályozást oly módon, hogy ellenőrzik, hogy az egy előfizetésen belüli erőforrások megfelelnek-e a követelményeknek és a szabványoknak.

A tervbe foglalt szabályzatokkal a terv hozzárendelése során megfelelő mintát vagy kialakítást hozhat létre. A szabályzat belefoglalásával arról is gondoskodhat, hogy a környezetben csak jóváhagyott vagy várt módosításokat lehessen végrehajtani, így folyamatosan biztosíthatja a terv céljának való megfelelőséget.

A szabályzatok a tervrajzok definíciójában szereplő _számos összetevő_ egyike lehet. A tervek a szabályzatokhoz és kezdeményezésekhez paraméterek használatát is támogatják.

## <a name="blueprint-definition"></a>Tervdefiníció

A terv összetevőkből áll _._ Az Azure-tervezetek jelenleg a következő erőforrásokat támogatják összetevőkként:

|Erőforrás  | Hierarchialehetőségek| Description  |
|---------|---------|---------|
|Erőforráscsoportok | Előfizetés | Hozzon létre egy új erőforráscsoportot, amelyet a tervben szereplő többi tervelem fog használni.  Ezek a helyőrző erőforráscsoportok lehetővé teszik az erőforrások rendszerezését, és a benne foglalt szabályzatok és szerepkör-hozzárendelési összetevők és ARM-sablonok hatókör-korlátozását. |
|ARM-sablon | Előfizetés, erőforráscsoport | A sablonok, beleértve a beágyazott és a csatolt sablonokat, összetett környezetek létrehozásához használatosak. Ilyen környezetek lehetnek például a SharePoint-farmok, az Azure Automation-állapotkonfigurációk vagy a Log Analytics-munkaterületek. |
|Szabályzat-hozzárendelés | Előfizetés, erőforráscsoport | Lehetővé teszi egy szabályzat vagy kezdeményezés hozzárendelését ahhoz az előfizetéshez, amelyhez a terv hozzá van rendelve. A szabályzatnak vagy kezdeményezésnek a terv meghatározási helyének hatókörén belül kell lennie. Ha a szabályzat vagy kezdeményezés paraméterekkel rendelkezik, ezeket a paramétereket a terv létrehozásakor vagy hozzárendelésekor lehet hozzárendelni. |
|Szerepkör-hozzárendelés | Előfizetés, erőforráscsoport | Egy meglévő felhasználót vagy csoportot hozzáadhat egy beépített szerepkörhöz, és ezzel biztosíthatja, hogy a megfelelő személyek mindig a megfelelő hozzáféréssel rendelkezzenek az erőforrásokhoz. A szerepkör-hozzárendelések meghatározhatók az egész előfizetésre, vagy leszűkíthetők egy bizonyos erőforráscsoportra, amely a tervben található. |

### <a name="blueprint-definition-locations"></a>Terv definíciós helyei

Tervdefiníció létrehozásakor meg kell határozni a terv mentési helyét. A tervrajzok olyan [felügyeleti csoportba](../management-groups/overview.md) vagy előfizetésbe menthetők, amelyhez **közreműködői** hozzáféréssel rendelkezik. Ha a hely egy felügyeleti csoport, a terv a felügyeleti csoport bármely alárendelt előfizetéséhez rendelhető.

### <a name="blueprint-parameters"></a>Tervparaméterek

A tervrajzok egy házirend/kezdeményezés vagy egy ARM-sablon számára is átadhatják a paramétereket. Amikor bármilyen _összetevőt_ ad hozzá a tervhez, a szerző eldönti, hogy mindegyik tervhozzárendeléshez megad egy meghatározott értéket, vagy hagyja, hogy az egyes tervhozzárendelések értéke hozzárendeléskor legyen megadva. Ez a fajta rugalmasság biztosítja azt a választási lehetőséget, hogy a terv minden felhasználásakor egy előre meghatározott értéket használunk-e, vagy hagyjuk, hogy az értékről a hozzárendeléskor lehessen dönteni.

> [!NOTE]
> A terv rendelkezhet saját paraméterekkel, de ezeket jelenleg csak úgy lehet létrehozni, ha a tervet REST API-ból hozzunk létre, nem a portálról.

További információkért tekintse meg a [tervparamétereket](./concepts/parameters.md) ismertető cikket.

### <a name="blueprint-publishing"></a>Tervek közzététele

Amikor létrehozunk egy tervet, az **Vázlat** módban van. Ha a terv készen áll a hozzárendelésre, **közzé kell tenni**. A közzétételhez meg kell adni egy **verzió** sztringet (ez betűket, számokat és kötőjeleket tartalmazhat, és legfeljebb 20 karakter hosszú lehet), valamint **változási megjegyzések** is megadhatók, de ez nem kötelező. A **verzió** megkülönbözteti a tervet annak jövőbeli módosított változataitól, és lehetővé teszi az egyes verziók hozzárendelését. A verziókezelés azt is jelenti, hogy egy adott terv különböző **verziói** is hozzárendelhetők ugyanahhoz az előfizetéshez. Ha a tervben további változások történnek, a **közzétett** 
 **verzió** továbbra is létezik, ahogy a nem **közzétett módosítások** is. Miután elvégeztük a kívánt módosításokat, a frissített tervet **közzétesszük** egy új és egyedi **verzióval**, amely innentől kezdve szintén hozzárendelhető lesz.

## <a name="blueprint-assignment"></a>Tervek hozzárendelése

A tervrajzok minden **közzétett** **verziója** hozzárendelhető (legfeljebb 90 karakter hosszú lehet) egy meglévő felügyeleti csoportba vagy előfizetésbe. A portálon alapértelmezés szerint a terv legutóbb **közzétett****verziója** érhető el. Ha összetevő-paraméterek vagy tervrajz-paraméterek vannak megadva, akkor a paraméterek a hozzárendelési folyamat során vannak meghatározva.

> [!NOTE]
> Ha a terv definícióját egy felügyeleti csoporthoz rendeli, azt jelenti, hogy a hozzárendelési objektum létezik a felügyeleti csoportban. Az összetevők üzembe helyezése továbbra is egy előfizetést céloz meg. A felügyeleti csoport hozzárendelésének végrehajtásához a [create vagy a Update REST API](/rest/api/blueprints/assignments/createorupdate) kell használni, és a kérés törzsének tartalmaznia kell egy értéket a `properties.scope` cél-előfizetés definiálásához.

## <a name="permissions-in-azure-blueprints"></a>Engedélyek az Azure Blueprintsben

A tervrajzok használatához engedélyeket kell biztosítania az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)használatával. Ha Azure Portal tervét szeretné olvasni vagy megtekinteni, a fióknak olvasási hozzáféréssel kell rendelkeznie ahhoz a hatókörhöz, ahol a terv definíciója található.

Tervek létrehozásához a fióknak a következő engedélyekkel kell rendelkeznie:

- `Microsoft.Blueprint/blueprints/write` – Tervdefiníció létrehozása
- `Microsoft.Blueprint/blueprints/artifacts/write` – Tervelemek létrehozása tervdefiníció alapján
- `Microsoft.Blueprint/blueprints/versions/write` – Terv közzététele

Tervek törléséhez a fióknak a következő engedélyekkel kell rendelkeznie:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> A terv definíciójának engedélyeit a felügyeleti csoport vagy az előfizetés hatókörén kell megadni vagy örökölni.

Tervek hozzárendeléséhez vagy a hozzárendelés megszüntetéséhez a fióknak a következő engedélyekkel kell rendelkeznie:

- `Microsoft.Blueprint/blueprintAssignments/write` – Terv hozzárendelése
- `Microsoft.Blueprint/blueprintAssignments/delete` – Terv hozzárendelésének megszüntetése

> [!NOTE]
> Mivel a tervhozzárendelések létrehozása előfizetésenként történik, a terv hozzárendelési és hozzárendelés-megszüntetési engedélyeit az előfizetés hatókörében kell megadni, vagy örökléssel kell biztosítani az előfizetési hatókörben.

A következő beépített szerepkörök érhetők el:

|Azure-szerepkör | Description |
|-|-|
|[Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) | A többi engedélyen kívül minden Azure Blueprint kapcsolódó engedélyt tartalmaz. |
|[Közreműködő](../../role-based-access-control/built-in-roles.md#contributor) | Más engedélyek mellett létrehozhat és törölhet tervrajz-definíciókat, de nem rendelkezik terv-hozzárendelési engedélyekkel. |
|[Tervezet közreműködője](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Kezelheti a terv definícióit, de nem rendelheti hozzá őket. |
|[Blueprint operátor](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Meglévő közzétett tervrajzokat is hozzárendelhet, de nem hozhat létre új terv-definíciókat. A terv-hozzárendelés csak akkor működik, ha a hozzárendelés felhasználó által hozzárendelt felügyelt identitással lett végrehajtva. |

Ha ezek a beépített szerepkörök nem felelnek meg a biztonsági követelményeinek, létrehozhat [egyéni szerepköröket](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Ha rendszer által hozzárendelt felügyelt identitást használ, az Azure-tervezetekhez tartozó egyszerű szolgáltatás **tulajdonosi** szerepkört igényel a hozzárendelt előfizetésben az üzembe helyezés engedélyezése érdekében. A portál használata esetén ezt a szerepkört a rendszer automatikusan biztosítja és visszavonja az üzembe helyezéshez. A REST API használata esetén a szerepkört manuálisan kell biztosítani, de az üzembe helyezés befejeződése a rendszer szintén automatikusan visszavonja. Ha felhasználó által hozzárendelt felügyelt identitást használ, csak a terv-hozzárendelést létrehozó felhasználónak van szüksége az `Microsoft.Blueprint/blueprintAssignments/write` engedélyre, amely a **tulajdonos** és a **Blueprint operátor** beépített szerepköreiben is megtalálható.

## <a name="naming-limits"></a>Elnevezési korlátok

Bizonyos mezőkhöz a következő korlátozások vonatkoznak:

|Objektum|Mező|Engedélyezett karakterek|Legfeljebb Hossz|
|-|-|-|-|
|Terv|Name|betűk, számok, kötőjelek és időszakok|48|
|Terv|Verzió|betűk, számok, kötőjelek és időszakok|20|
|Tervek hozzárendelése|Name|betűk, számok, kötőjelek és időszakok|90|
|Tervrajz-összetevő|Name|betűk, számok, kötőjelek és időszakok|48|

## <a name="video-overview"></a>Videó – áttekintés

Az Azure-tervezetek az alábbi áttekintést nyújtanak az Azure Fridays-ről. A videók letöltéséhez látogasson el az Azure Fridays szolgáltatásra, amely [áttekintést nyújt a Channel 9 Azure-tervezetekről](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) .

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Következő lépések

- [Terv létrehozása – portál](./create-blueprint-portal.md).
- [Terv létrehozása – PowerShell](./create-blueprint-powershell.md).
- [Hozzon létre egy tervet – REST API](./create-blueprint-rest-api.md).