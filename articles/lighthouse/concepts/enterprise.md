---
title: Az Azure Lighthouse nagyvállalati forgatókönyvekben
description: Az Azure Lighthouse képességeinek használatával egyszerűsítheti a több-bérlős felügyeletet egy olyan vállalaton belül, amely több Azure AD-bérlőt használ.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 97b44f71750bdb533e889546f370a9b36ea5d3b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419354"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Az Azure Lighthouse nagyvállalati forgatókönyvekben

Az [Azure Lighthouse](../overview.md) gyakori forgatókönyve, amikor egy szolgáltató az ügyfelekhez tartozó Azure Active Directory (Azure ad) bérlők erőforrásait kezeli. Az Azure Lighthouse képességei is felhasználhatók a több-bérlős felügyelet egyszerűsítésére egy olyan vállalaton belül, amely több Azure AD-bérlőt is használ.

## <a name="single-vs-multiple-tenants"></a>Egyetlen és több bérlő

A legtöbb szervezet számára egyszerűbb a felügyelet egyetlen Azure AD-Bérlővel. Az egyik bérlőn belüli összes erőforrás lehetővé teszi a felügyeleti feladatok központosított kezelését a kijelölt felhasználók, felhasználói csoportok vagy az adott bérlőn belüli egyszerű szolgáltatásnév alapján. Ha lehetséges, javasoljuk, hogy egy bérlőt használjon a szervezete számára.

Előfordulhat, hogy néhány szervezetnek több Azure AD-bérlőt kell használnia. Ez lehet egy ideiglenes helyzet, ahogy a beszerzések megtörténtek, és a hosszú távú bérlői konszolidációs stratégia még nincs definiálva. Más esetekben előfordulhat, hogy a szervezeteknek a teljes független leányvállalatok, földrajzi vagy jogi követelmények, illetve egyéb megfontolások miatt folyamatosan több bérlőt kell fenntartaniuk.

Ha több-bérlős architektúrára van szükség, az Azure Lighthouse segítségével központosíthatja és egyszerűsítheti a felügyeleti műveleteket. Az Azure-beli [delegált erőforrás-kezelés](azure-delegated-resource-management.md)használatával a bérlők egy központi, méretezhető módon hajthatják végre a [több-bérlős felügyeleti funkciókat](cross-tenant-management-experience.md) .

## <a name="tenant-management-architecture"></a>Bérlői felügyeleti architektúra

Az Azure Lighthouse vállalaton belüli használatához meg kell határoznia, hogy melyik bérlő fogja tartalmazni a többi bérlőn felügyeleti műveleteket végrehajtó felhasználókat. Más szóval meg kell jelölnie egy bérlőt, mint a többi bérlőt kezelő bérlőt.

Tegyük fel például, hogy a szervezet egyetlen Bérlővel rendelkezik, amely az *a bérlőt* hívja meg. A szervezet ezután beolvassa a *B bérlőt* és a *C bérlőt*, és Önnek üzleti oka van, amely megköveteli, hogy külön bérlőként tartsák karban őket. Azonban ugyanazokat a házirend-definíciókat, biztonsági mentési eljárásokat és biztonsági folyamatokat szeretné használni, az azonos felhasználók által végrehajtott felügyeleti feladatokkal.

Mivel a bérlő már tartalmaz olyan felhasználókat a szervezetben, akik az A bérlőnek teljesítik ezeket a feladatokat, a B bérlőn belüli előfizetéseket és a C bérlőt is bevezetheti, amely lehetővé teszi, hogy ugyanazokat a felhasználókat a bérlők végezzék el a feladatok elvégzéséhez.

![Ábra: A bérlő felhasználóinak kezelése A B bérlő és A C bérlő erőforrásaival.](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Biztonsági és hozzáférési megfontolások

A legtöbb nagyvállalati forgatókönyv esetében érdemes egy teljes előfizetést delegálni az Azure Lighthouse-ra. Dönthet úgy is, hogy csak meghatározott erőforráscsoportokat delegál az előfizetésen belül.

Mindkét esetben ügyeljen arra, hogy [kövesse a legalacsonyabb jogosultsági szint elvét, amikor meghatározza, hogy mely felhasználók férhetnek hozzá a delegált erőforrásokhoz](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Így biztosíthatja, hogy a felhasználók csak a szükséges feladatok elvégzéséhez szükséges engedélyekkel rendelkezzenek, és csökkentik a véletlen hibák esélyét.

Az Azure Lighthouse csak logikai kapcsolatokat biztosít a bérlők és a felügyelt bérlők között, és nem fizikailag helyezi át az adatátvitelt és az erőforrásokat. Továbbá a hozzáférés mindig csak egy irányba mutat, a bérlő kezelése a felügyelt bérlők felé.  A kezelő bérlő felhasználói és csoportjai továbbra is használhatják a többtényezős hitelesítést a felügyelt bérlői erőforrásokon végzett felügyeleti műveletek végrehajtásakor.

A belső vagy külső irányítási és megfelelőségi guardrails rendelkező vállalatok az [Azure-tevékenység naplóit](../../azure-monitor/essentials/platform-logs-overview.md) használhatják az átláthatósági követelmények teljesítéséhez. Ha a vállalati bérlők felügyeleti és felügyelt bérlői kapcsolatokat létesítettek, az egyes bérlők felhasználói megtekinthetik a naplózott tevékenységeket a bérlők felügyelete alatt álló felhasználók által végrehajtott műveletek megtekintéséhez.

## <a name="onboarding-considerations"></a>Bevezetési megfontolások

Az előfizetések (vagy egy előfizetésen belüli erőforráscsoportok) üzembe helyezhetők az Azure Lighthouse szolgáltatásban Azure Resource Manager-sablonok vagy az Azure Marketplace-en közzétett felügyelt szolgáltatások használatával.

Mivel a nagyvállalati felhasználók általában közvetlen hozzáféréssel rendelkeznek a vállalat bérlői számára, és nincs szükség a felügyeleti ajánlatok forgalmazására vagy előléptetésére, az Azure Resource Manager-sablonok üzembe helyezése általában gyorsabban és egyszerűbben történik. Míg a [bevezetési útmutató](../how-to/onboard-customer.md) a szolgáltatókra és az ügyfelekre vonatkozik, a vállalatok ugyanezeket a folyamatokat használhatják a bérlők bevezetésére.

Ha szeretné, a vállalaton belüli bérlőket a [felügyelt szolgáltatások Azure Marketplace-re való közzétételével](../how-to/publish-managed-services-offers.md)lehet előkészíteni. Annak biztosítása érdekében, hogy az ajánlat csak a megfelelő bérlők számára legyen elérhető, győződjön meg arról, hogy a csomagok magánjellegűként vannak megjelölve. Egy privát csomaggal megadhatja a bevezetéshez tervezett összes bérlő előfizetés-azonosítóját, és senki más nem fogja tudni felvenni az ajánlatot.

## <a name="terminology-notes"></a>Terminológiai megjegyzések

A vállalaton belüli több-bérlős felügyelet esetén az Azure Lighthouse dokumentációjában található szolgáltatókra való hivatkozásokat a vállalaton belüli felügyeletre, azaz a bérlőre vonatkozik, amely a többi bérlő erőforrásait kezelő felhasználókat is tartalmazza az Azure Lighthouse használatával. Hasonlóképpen, az ügyfelekre való hivatkozásokat úgy is értelmezheti, hogy azokra a bérlőre érvényesek, amelyek delegálják az erőforrásokat a bérlők felügyelete alatt álló felhasználóknak.

Például a fent ismertetett példában az A bérlő a szolgáltató bérlője (a bérlők kezelése) és a B bérlő, a C bérlő pedig az ügyfél bérlője lehet.

Ennek a példának a folytatásaként a bérlő A megfelelő engedélyekkel rendelkező felhasználók [megtekinthetik és kezelhetik a delegált erőforrásokat](../how-to/view-manage-customers.md) a Azure Portal **saját ügyfelek** lapján. Hasonlóképpen, a B bérlő és a bérlő C felhasználója a megfelelő engedélyekkel [megtekintheti és kezelheti az a bérlőhöz delegált erőforrásokat](../how-to/view-manage-service-providers.md) a Azure Portal **szolgáltató** lapján.

## <a name="next-steps"></a>Következő lépések

- További információ a [bérlők közötti felügyeleti élményekről](cross-tenant-management-experience.md).
- Megismerheti az [Azure által delegált erőforrás-kezelés](azure-delegated-resource-management.md) részleteit.
