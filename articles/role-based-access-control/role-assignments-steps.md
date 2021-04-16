---
title: Azure-szerepkör hozzárendelésének lépései – Azure RBAC
description: Megismerheti az Azure-szerepkörök felhasználókhoz, csoportokhoz, szolgáltatásnévhez vagy felügyelt identitásokhoz való hozzárendelésének lépéseit az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 04/14/2021
ms.author: rolyon
ms.openlocfilehash: 40a17da6383fb1f368c74a82fefa71991cdc1b19
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517674"
---
# <a name="steps-to-assign-an-azure-role"></a>Az Azure-szerepkörök hozzárendelésének lépései

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)]Ez a cikk az Azure-szerepkörök hozzárendelésének magas szintű lépéseit ismerteti a [Azure Portal,](role-assignments-portal.md) [Azure PowerShell,](role-assignments-powershell.md) [az Azure CLI](role-assignments-cli.md)vagy a [REST API.](role-assignments-rest.md)

## <a name="step-1-determine-who-needs-access"></a>1. lépés: Annak meghatározása, hogy kinek van szüksége hozzáférésre

Először meg kell határoznia, hogy kinek van szüksége hozzáférésre. Hozzárendelhet egy szerepkört egy felhasználóhoz, csoporthoz, szolgáltatásnévhez vagy felügyelt identitáshoz. Ezt más néven *rendszerbiztonsági tagnak is nevezik.*

![Szerepkör-hozzárendelések rendszerbiztonsági tagjai](./media/shared/rbac-security-principal.png)

- Felhasználó – Egy Azure Active Directory-profillal rendelkező személy. Más bérlőkön lévő felhasználókhoz is hozzárendelhet szerepköröket. A más vállalatok felhasználóira vonatkozó információkért lásd: [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md).
- Csoport – Felhasználók az Azure Active Directoryban létrehozott csoportja. Ha egy csoporthoz rendel hozzá egy szerepkört, a csoportban lévő összes felhasználó rendelkezik majd az adott szerepkörrel. 
- Szolgáltatásnév – Alkalmazások vagy szolgáltatások által adott Azure-erőforrások elérésére használt biztonsági identitás. Úgy képzelhető el, mint egy alkalmazás *felhasználói identitása* (felhasználóneve és jelszava vagy tanúsítványa).
- Felügyelt identitás – A Azure Active Directory azure által automatikusan felügyelt identitás. A felügyelt identitásokat [általában](../active-directory/managed-identities-azure-resources/overview.md) felhőalkalmazások fejlesztésekor használják az Azure-szolgáltatásokban való hitelesítés hitelesítő adatainak kezeléséhez.

## <a name="step-2-select-the-appropriate-role"></a>2. lépés: A megfelelő szerepkör kiválasztása

Az engedélyek egy szerepkör-definícióba *vannak csoportosítva.* Ezt általában csak szerepkörnek *nevezik.* Számos beépített szerepkör közül választhat. Ha a beépített szerepkörök nem felelnek meg a cég vagy intézmény igényeinek, saját egyéni szerepköröket is létrehozhat.

![Szerepkör-hozzárendelések szerepkör-definíciója](./media/shared/rbac-role-definition.png)

Az alábbiakban négy alapvető beépített szerepkört sorolunk fel. Az első három minden erőforrástípusra alkalmazható.

- [Tulajdonos](built-in-roles.md#owner) – teljes hozzáféréssel rendelkezik az összes erőforráshoz, beleértve a hozzáférés mások számára való delegálásának jogát is.
- [Közreműködő](built-in-roles.md#contributor) – Bármilyen típusú Azure-erőforrást létrehozhat és felügyelhet, hozzáférést azonban nem adhat mások számára.
- [Olvasó](built-in-roles.md#reader) – Megtekintheti a meglévő Azure-erőforrásokat.
- [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) – kezelheti a felhasználók Azure-erőforrásokhoz való hozzáférését.

A többi beépített szerepkör adott Azure-erőforrások kezelését teszi lehetővé. Például a [Virtuális gépek közreműködője](built-in-roles.md#virtual-machine-contributor) szerepkör virtuális gépek létrehozását és kezelését teszi lehetővé.

1. Kezdje az Azure beépített szerepköreit [átfogó cikkel.](built-in-roles.md) A cikk tetején található táblázat a cikk későbbi részen található részletek indexe.

1. Ebben a cikkben keresse meg annak az erőforrásnak a szolgáltatáskategóriáját (például számítási, tárolási és adatbázisokat), amelyhez engedélyeket szeretne. A legegyszerűbben úgy találhatja meg, amit keres, ha megkeresi az oldalon a megfelelő kulcsszót, például a "blob", a "virtuális gép" stb. kulcsszót.

1. Tekintse át a szolgáltatáskategóriához felsorolt szerepköröket, és azonosítsa a szükséges műveleteket. Mindig mindig a legszigorúbb szerepkört kezdje.

    Ha például egy rendszerbiztonsági tagnak be kell olvasnia egy Azure Storage-fiókban lévő blobokat, de nincs szüksége írási hozzáférésre, akkor a [Storage-blobadatok](built-in-roles.md#storage-blob-data-reader) olvasója lehetőséget válassza a [Storage-blobadatok](built-in-roles.md#storage-blob-data-contributor) közreműködője helyett (és biztosan nem a Storage-blobadatok rendszergazdai szintű tulajdonosa [szerepkört).](built-in-roles.md#storage-blob-data-owner) A szerepkör-hozzárendeléseket később szükség szerint frissítheti.

1. Ha nem talál megfelelő szerepkört, létrehozhat egy [egyéni szerepkört.](custom-roles.md)

## <a name="step-3-identify-the-needed-scope"></a>3. lépés: A szükséges hatókör azonosítása

*A* hatókör azon erőforrások halmaza, amelyekre a hozzáférés vonatkozik. Az Azure-ban a hatókört négy szinten adhatja meg: [felügyeleti csoport,](../governance/management-groups/overview.md) [előfizetés,](../azure-resource-manager/management/overview.md#resource-groups)erőforráscsoport és erőforrás. A hatókörök szülő-gyermek (származtatott) kapcsolatokba vannak rendezve. A hierarchia minden szintje konkrétabb hatókört tesz lehetővé. A hatókör ezen szintjeinek bármelyikében hozzárendelhet szerepköröket. A kiválasztott szint határozza meg, hogy milyen széles körben lesz alkalmazva a szerepkör. Az alacsonyabb szintek öröklik a szerepköri engedélyeket a magasabb szintekről. 

![Szerepkör-hozzárendelés hatóköre](./media/shared/rbac-scope.png)

Amikor szerepkört rendel hozzá egy szülőhatókörhöz, az engedélyek öröklődik a gyermekhatókörökre. Például:

- Ha az Olvasó [szerepkört](built-in-roles.md#reader) hozzárendeli egy felhasználóhoz a felügyeleti csoport hatókörében, az a felhasználó a felügyeleti csoport összes előfizetésében mindent olvashat.
- Ha a [](built-in-roles.md#billing-reader) Számlázási olvasó szerepkört hozzárendeli egy csoporthoz az előfizetési hatókörben, a csoport tagjai az előfizetés minden erőforráscsoportja és erőforrása számlázási adatait olvashatják.
- Ha a [Közreműködő](built-in-roles.md#contributor) szerepkört hozzárendeli egy alkalmazáshoz az erőforrás-csoport hatókörében, az alkalmazás bármilyen típusú erőforrást kezelhet majd az adott erőforráscsoportban, az előfizetésben lévő többi erőforráscsoportban azonban nem.

 További információkért lásd a [hatókört.](scope-overview.md)

## <a name="step-4-check-your-prerequisites"></a>4. lépés: Az előfeltételek ellenőrzése

A szerepkörök hozzárendelése érdekében olyan felhasználóval kell bejelentkeznie, akihez hozzá van [](built-in-roles.md#owner) rendelve [](built-in-roles.md#user-access-administrator) egy szerepkör-hozzárendelési írási engedéllyel rendelkező szerepkör, például Tulajdonos vagy Felhasználói hozzáférés rendszergazdája a szerepkör hozzárendelésére kijelölt hatókörben. A szerepkör-hozzárendelések eltávolításához hasonlóképpen törlési engedéllyel kell rendelkeznie a szerepkör-hozzárendelések törléséhez.

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

Ha a felhasználói fiók nem rendelkezik engedéllyel egy szerepkör előfizetésen belüli hozzárendeléséhez, egy hibaüzenet jelenik meg, amely szerint a fiókja nem rendelkezik engedéllyel a "Microsoft.Authorization/roleAssignments/write" művelet végrehajtásához. Ebben az esetben vegye fel a kapcsolatot az előfizetés rendszergazdáival, mert ők rendelhetik hozzá az engedélyeket az Ön nevében.

Ha szolgáltatásnévvel rendel hozzá szerepköröket, a következő hibaüzenet jelenhet meg: "Nincs megfelelő jogosultsága a művelet végrehajtásához.". Ennek a hibának az az oka, hogy az Azure megpróbálja keresni a hozzárendelt identitást az Azure Active Directory -ban (Azure AD), és a szolgáltatásnév alapértelmezés szerint nem tudja olvasni az Azure AD-t. Ebben az esetben engedélyt kell adnunk a szolgáltatásnévnek a címtárban található adatok olvasására. Ha az Azure CLI-t használja, a hozzárendelt objektumazonosítóval is létrehozhatja a szerepkör-hozzárendelést, így kihagyhatja az Azure AD-kereséseket. További információ: [Az Azure RBAC hibaelhárítása.](troubleshooting.md)

## <a name="step-5-assign-role"></a>5. lépés Szerepkör hozzárendelése

A rendszerbiztonsági tag, a szerepkör és a hatókör már ismerve van, hozzárendelheti a szerepkört. A szerepköröket a következő szolgáltatásokkal rendelheti Azure Portal, Azure PowerShell Azure CLI, Azure SDK-k vagy REST API-k használatával. Minden előfizetésben **legfeljebb 2000** szerepkör-hozzárendeléssel rendelkezik. Ez a korlát magában foglalja az előfizetés, az erőforráscsoport és az erőforrás-hatókörök szerepkör-hozzárendelését. Minden felügyeleti csoportban **legfeljebb 500** szerepkör-hozzárendelést lehet hozzárendelni.

A szerepkörök hozzárendelésének részletes lépéseit az alábbi cikkekben talál.

- [Azure-szerepkörök hozzárendelése a Azure Portal](role-assignments-portal.md)
- [Azure-szerepkörök hozzárendelése Azure PowerShell](role-assignments-powershell.md)
- [Azure-szerepkörök hozzárendelése az Azure CLI használatával](role-assignments-cli.md)
- [Azure-szerepkörök hozzárendelése a REST API](role-assignments-rest.md)

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: Hozzáférés megadása egy felhasználó számára Azure-erőforrásokhoz a Azure Portal](quickstart-assign-role-user-portal.md)