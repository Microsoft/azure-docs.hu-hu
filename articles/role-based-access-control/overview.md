---
title: Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?
description: Az Azure szerepköralapú hozzáférés-vezérlésének (Azure RBAC) áttekintése. Szerepkör-hozzárendelések használata az Azure-erőforrásokhoz való hozzáférés szabályozásához.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: overview
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.custom: contperf-fy21q1, azuread-video-2020
ms.openlocfilehash: 4241e476b2f778ff63057d0491b5dc8666c7520c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650864"
---
# <a name="what-is-azure-role-based-access-control-azure-rbac"></a>Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?

A felhőbeli erőforrások hozzáférésének kezelése kritikus fontosságú a felhőt használó vállalatok számára. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével felügyelheti, hogy ki férhet hozzá az Azure-erőforrásokhoz, mit tehet az erőforrásokkal, és milyen területekhez férhet hozzá.

Az Azure RBAC egy [Azure Resource Managerra](../azure-resource-manager/management/overview.md) épülő engedélyezési rendszer, amely részletes hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz.

Ez a videó az Azure RBAC gyors áttekintését nyújtja.

>[!VIDEO https://www.youtube.com/embed/Dzhm-garKBM]

## <a name="what-can-i-do-with-azure-rbac"></a>Mire használhatom az Azure RBAC-t?

Íme néhány példa arra, hogy mit tehet az Azure RBAC:

- Az egyik felhasználó számára engedélyezheti az előfizetésben lévő virtuális gépek, a másik felhasználó számára pedig a virtuális hálózatok kezelését
- Engedélyezheti egy adatbázis-rendszergazdai csoport számára az előfizetésben lévő SQL-adatbázisok kezelését.
- Engedélyezheti egy felhasználó számára az egy adott erőforráscsoportba tartozó összes erőforrás, például a virtuális gépek, a webhelyek és az alhálózatok kezelését.
- Engedélyezheti egy alkalmazás számára az egy adott erőforráscsoportba tartozó összes erőforrás elérését.

## <a name="how-azure-rbac-works"></a>Az Azure RBAC működése

Az Azure RBAC használatával az erőforrásokhoz való hozzáférés vezérlése az Azure-szerepkörök hozzárendelésével végezhető el. Ez a legfontosabb elképzelés az, hogy a rendszer hogyan érvényesíti az engedélyeket. A szerepkör-hozzárendelés három elemből áll: rendszerbiztonsági tagból, szerepkör-definícióból és hatókörből.

### <a name="security-principal"></a>Rendszerbiztonsági tag

A *rendszerbiztonsági tag* egy olyan objektum, amely egy, az Azure-erőforrásokhoz hozzáférést igénylő felhasználót, csoportot, szolgáltatásnevet vagy felügyelt identitást jelöl. Ehhez a rendszerbiztonsági tag bármelyikéhez hozzárendelhet egy szerepkört.

![Szerepkör-hozzárendelések rendszerbiztonsági tagjai](./media/shared/rbac-security-principal.png)

### <a name="role-definition"></a>Szerepkör-definíció

A *szerepkör-definíció* engedélyek gyűjteménye. Általában csak *szerepkörnek* nevezik. A szerepkör-definíció a végrehajtható műveletek listáját tartalmazza (például olvasás, írás és törlés). A szerepkör lehet magas szintű (például tulajdonos) vagy specifikus (például virtuálisgép-olvasó).

![Szerepkör-hozzárendelések szerepkör-definíciója](./media/shared/rbac-role-definition.png)

Az Azure számos [beépített szerepkört](built-in-roles.md) tartalmaz, amelyeket használhat. Például a [Virtuális gépek közreműködője](built-in-roles.md#virtual-machine-contributor) szerepkör virtuális gépek létrehozását és kezelését teszi lehetővé. Ha a beépített szerepkörök nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját [Egyéni Azure-szerepköröket](custom-roles.md)is.

Ez a videó gyors áttekintést nyújt a beépített szerepkörökről és az egyéni szerepkörökről.

>[!VIDEO https://www.youtube.com/embed/I1mefHptRgo]

Az Azure olyan adatműveletekkel rendelkezik, amelyek lehetővé teszik az adatokhoz való hozzáférés megadását egy adott objektumon belül. Ha például egy felhasználó adatolvasási hozzáféréssel rendelkezik egy tárfiókhoz, akkor olvashatja az adott tárfiókban lévő blobokat vagy üzeneteket.

További információ: az [Azure szerepkör-definíciók ismertetése](role-definitions.md).

### <a name="scope"></a>Hatókör

A *hatókör* azon erőforrások készlete, amelyekre a hozzáférés vonatkozik. Szerepkörök hozzárendelésekor hatókör meghatározásával tovább korlátozhatja az engedélyezett műveletek körét. Ez akkor hasznos, ha egy [webhely közreműködői](built-in-roles.md#website-contributor)szerepkört szeretne tenni, de csak egy erőforráscsoporthoz.

Az Azure-ban négy szinten adhatja meg a hatókört: [felügyeleti csoport](../governance/management-groups/overview.md), előfizetés, [erőforráscsoport](../azure-resource-manager/management/overview.md#resource-groups)vagy erőforrás. A hatókörök szülő-gyermek (származtatott) kapcsolatokba vannak rendezve. A szerepköröket a hatókör ezen szintjein lehet hozzárendelni.

![Szerepkör-hozzárendelés hatóköre](./media/shared/rbac-scope.png)

A hatókörrel kapcsolatos további információkért lásd a [hatókör ismertetése](scope-overview.md)című témakört.

### <a name="role-assignments"></a>Szerepkör-hozzárendelések

A *szerepkör-hozzárendelés* az a folyamat, amellyel a szerepkör-definíciót egy adott hatókörhöz tartozó felhasználóhoz, csoporthoz, egyszerű szolgáltatáshoz vagy felügyelt identitáshoz csatlakoztathatja a hozzáférés biztosítása céljából. A hozzáférés szerepkör-hozzárendelés létrehozásával biztosítható, és a szerepkör-hozzárendelés törlésével vonható vissza.

Az alábbi ábrán egy példa látható szerepkör-hozzárendelésre. Ebben a példában a Marketing csoporthoz hozzárendeltük a [Közreműködő](built-in-roles.md#contributor) szerepkört a gyógyszer-értékesítési erőforráscsoportra vonatkozóan. Ez azt jelenti, hogy a Marketing csoportba tartozó felhasználók bármilyen Azure-erőforrást létrehozhatnak és kezelhetnek a gyógyszer-értékesítési erőforráscsoportban. A Marketing felhasználói a gyógyszer-értékesítési erőforráscsoporton kívüli erőforrásokhoz nem rendelkeznek hozzáféréssel, ha nem tagjai másik szerepkör-hozzárendelésnek is.

![Hozzáférés-vezérlés szerepkör-hozzárendeléssel](./media/overview/rbac-overview.png)

Szerepköröket a Azure Portal, az Azure CLI, az Azure PowerShell, az Azure SDK-k vagy a REST API-k használatával rendelhet hozzá.

További információ: Azure- [szerepkör hozzárendelésének lépései](role-assignments-steps.md).

## <a name="multiple-role-assignments"></a>Több szerepkör-hozzárendelés

Tehát mi történik, ha több átfedésben lévő szerepkör-hozzárendelés van? Az Azure RBAC egy adalékanyag-modell, így az érvényes engedélyek a szerepkör-hozzárendelések összege. Vegye figyelembe a következő példát, amikor egy felhasználó megkapja a közreműködő szerepkört az előfizetés hatókörében, valamint az olvasó szerepkört egy erőforráscsoporthoz. A közreműködői engedélyek és az olvasói engedélyek összege ténylegesen az előfizetés közreműködői szerepköre. Ezért ebben az esetben az olvasó szerepkör-hozzárendelésnek nincs hatása.

![Több szerepkör-hozzárendelés](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Megtagadás-hozzárendelések

Korábban az Azure RBAC egy csak engedélyezési modell volt, amely nem rendelkezik megtagadás nélkül, de most az Azure RBAC korlátozott módon támogatja a megtagadási hozzárendeléseket. A szerepkör-hozzárendeléshez hasonlóan a *megtagadási hozzárendelések* egy adott hatókörben egy felhasználóhoz, csoporthoz, egyszerű szolgáltatáshoz vagy felügyelt identitáshoz tartozó megtagadási műveletet rendelnek a hozzáférés megtagadása céljából. A szerepkör-hozzárendelések olyan műveletek készletét határozzák meg, amelyek *engedélyezve* vannak, míg a megtagadási hozzárendelések olyan műveletek készletét határozzák meg, amelyek *nem engedélyezettek*. Vagyis a megtagadás-hozzárendelések akkor is meggátolják, hogy a felhasználók elvégezzék a megadott műveleteket, ha egy szerepkör-hozzárendelés hozzáférést nyújt azokhoz. A megtagadás-hozzárendelések elsőbbséget élveznek a szerepkör-hozzárendelésekkel szemben.

További információ: az [Azure megtagadási hozzárendeléseinek megismerése](deny-assignments.md).

## <a name="how-azure-rbac-determines-if-a-user-has-access-to-a-resource"></a>Hogyan határozza meg az Azure RBAC, hogy egy felhasználó hozzáfér-e egy erőforráshoz

Az Azure RBAC a következő magas szintű lépésekkel határozza meg, hogy van-e hozzáférése egy erőforráshoz a felügyeleti síkon. Ennek ismerete hozzáférési problémák elhárításakor lehet hasznos.

1. A felhasználó (vagy szolgáltatásnév) jogkivonatot szerez be az Azure Resource Managerhez.

    A jogkivonatban szerepelnek a felhasználó csoporttagságai (beleértve a tranzitív csoporttagságokat is).

1. A felhasználó REST API-hívást indít az Azure Resource Managerhez a csatolt jogkivonattal.

1. Az Azure Resource Manager lekéri az azon erőforrásra érvényes összes szerepkör-hozzárendelést és megtagadás-hozzárendelést, amelyen a művelet történik.

1. Az Azure Resource Manager leszűkíti a felhasználóra vagy a csoportjára érvényes szerepkör-hozzárendeléseket, és meghatározza, hogy a felhasználó milyen szerepkörökkel rendelkezik ehhez az erőforráshoz.

1. Az Azure Resource Manager meghatározza, hogy az API-hívásban lévő művelet szerepel-e a felhasználó ezen erőforrásra vonatkozó szerepköreiben.

1. Ha a felhasználó nem rendelkezik szerepkörrel a kért hatókörben lévő művelettel, a hozzáférés nem adható meg. Ha rendelkezik a megfelelő szerepkörrel az Azure Resource Manager ellenőrzi, hogy van-e érvényes megtagadás-hozzárendelés.

1. Ha van érvényes megtagadás-hozzárendelés, megtagadja a hozzáférést. Egyéb esetben hozzáférést biztosít.

## <a name="license-requirements"></a>Licenckövetelmények

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Következő lépések

- [Azure-szerepkörök kiosztása a Azure Portal használatával](role-assignments-portal.md)
- [A különböző szerepkörök ismertetése](rbac-and-directory-admin-roles.md)
- [Felhőalapú bevezetési keretrendszer: erőforrás-hozzáférés kezelése az Azure-ban](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
