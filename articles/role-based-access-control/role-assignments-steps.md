---
title: Azure-szerepkörök hozzárendelésének lépései – Azure RBAC
description: Megtudhatja, hogyan rendelhet Azure-szerepköröket felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz vagy felügyelt identitásokhoz az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 081335779ffc4b3a6ddf09e56b773c6d34b210be
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556033"
---
# <a name="steps-to-assign-an-azure-role"></a>Azure-szerepkörök hozzárendelésének lépései

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Ez a cikk az Azure-szerepkörök [Azure Portal](role-assignments-portal.md), [Azure PowerShell](role-assignments-powershell.md), az [Azure CLI](role-assignments-cli.md)vagy a [REST API](role-assignments-rest.md)használatával történő hozzárendelésének magas szintű lépéseit ismerteti.

## <a name="step-1-determine-who-needs-access"></a>1. lépés: annak meghatározása, hogy kinek van hozzáférése

Először meg kell határoznia, hogy kinek van szüksége hozzáférésre. Szerepkört hozzárendelhet egy felhasználóhoz, csoporthoz, egyszerű szolgáltatásnévhez vagy felügyelt identitáshoz. Ezt *rendszerbiztonsági tag* is nevezik.

![Szerepkör-hozzárendelések rendszerbiztonsági tagjai](./media/shared/rbac-security-principal.png)

- Felhasználó – Egy Azure Active Directory-profillal rendelkező személy. Más bérlőkön lévő felhasználókhoz is hozzárendelhet szerepköröket. A más vállalatok felhasználóira vonatkozó információkért lásd: [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md).
- Csoport – Felhasználók az Azure Active Directoryban létrehozott csoportja. Ha egy csoporthoz rendel hozzá egy szerepkört, a csoportban lévő összes felhasználó rendelkezik majd az adott szerepkörrel. 
- Szolgáltatásnév – Alkalmazások vagy szolgáltatások által adott Azure-erőforrások elérésére használt biztonsági identitás. Úgy képzelhető el, mint egy alkalmazás *felhasználói identitása* (felhasználóneve és jelszava vagy tanúsítványa).
- Felügyelt identitás – az Azure által automatikusan kezelt Azure Active Directory identitás. A Felhőbeli alkalmazások fejlesztése során jellemzően [felügyelt identitásokat](../active-directory/managed-identities-azure-resources/overview.md) használ az Azure-szolgáltatásokhoz való hitelesítéshez szükséges hitelesítő adatok kezeléséhez.

## <a name="step-2-select-the-appropriate-role"></a>2. lépés: válassza ki a megfelelő szerepkört

Az engedélyek egy *szerepkör-definícióba* vannak csoportosítva. Általában csak *szerepkörnek* nevezik. A számos beépített szerepkör listájából választhat. Ha a beépített szerepkörök nem felelnek meg a cég vagy intézmény igényeinek, saját egyéni szerepköröket is létrehozhat.

![Szerepkör-hozzárendelések szerepkör-definíciója](./media/shared/rbac-role-definition.png)

Az alábbiakban négy alapvető beépített szerepkört sorolunk fel. Az első három minden erőforrástípusra alkalmazható.

- [Tulajdonos](built-in-roles.md#owner) – teljes hozzáféréssel rendelkezik az összes erőforráshoz, beleértve a hozzáférés mások számára való delegálásának jogát is.
- [Közreműködő](built-in-roles.md#contributor) – Bármilyen típusú Azure-erőforrást létrehozhat és felügyelhet, hozzáférést azonban nem adhat mások számára.
- [Olvasó](built-in-roles.md#reader) – megtekintheti a meglévő Azure-erőforrásokat.
- [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) – kezelheti a felhasználók Azure-erőforrásokhoz való hozzáférését.

A többi beépített szerepkör adott Azure-erőforrások kezelését teszi lehetővé. Például a [Virtuális gépek közreműködője](built-in-roles.md#virtual-machine-contributor) szerepkör virtuális gépek létrehozását és kezelését teszi lehetővé.

1. Az [Azure beépített szerepköreivel](built-in-roles.md)megkezdheti az átfogó cikket. A cikk elején található táblázat a cikk későbbi részében látható.

1. Ebben a cikkben navigáljon a szolgáltatási kategóriához (például a számítási, tárolási és adatbázisokhoz) ahhoz az erőforráshoz, amelyhez engedélyeket szeretne adni. A legkönnyebben megtalálhatja, amit keres, általában az oldalon keres egy releváns kulcsszót, például a "blob", a "virtuális gép" és így tovább.

1. Tekintse át a szolgáltatási kategóriában felsorolt szerepköröket, és azonosítsa a szükséges műveleteket. Újra, mindig a legszigorúbb szerepkörrel kezdjen.

    Ha például egy rendszerbiztonsági tagnak blobokat kell beolvasnia egy Azure Storage-fiókban, de nincs szükség írási hozzáférésre, akkor a Storage blob- [Adatolvasó](built-in-roles.md#storage-blob-data-reader) helyett a Storage blob [adatkezelője](built-in-roles.md#storage-blob-data-contributor) elemet kell választania (és határozottan nem a rendszergazdai szintű [tár blob-adattulajdonosi](built-in-roles.md#storage-blob-data-owner) szerepkört). A szerepkör-hozzárendeléseket később bármikor frissítheti, ha szükséges.

1. Ha nem talál megfelelő szerepkört, létrehozhat [Egyéni szerepkört](custom-roles.md)is.

## <a name="step-3-identify-the-needed-scope"></a>3. lépés: a szükséges hatókör azonosítása

A *hatókör* azon erőforrások készlete, amelyekre a hozzáférés vonatkozik. Az Azure-ban négy szinten adhatja meg a hatókört: [felügyeleti csoport](../governance/management-groups/overview.md), előfizetés, [erőforráscsoport](../azure-resource-manager/management/overview.md#resource-groups)és erőforrás. A hatókörök szülő-gyermek (származtatott) kapcsolatokba vannak rendezve. A hierarchia minden szintje konkrétabb módon teszi elérhetővé a hatókört. A szerepköröket a hatókör ezen szintjein lehet hozzárendelni. A kiválasztott szint határozza meg, hogy a rendszer milyen széles körben alkalmazza a szerepkört. Az alacsonyabb szintek öröklik a szerepkör-engedélyeket a magasabb szintekről. 

![Szerepkör-hozzárendelés hatóköre](./media/shared/rbac-scope.png)

Amikor fölérendelt hatókörhöz rendel szerepkört, ezek az engedélyek a gyermek hatókörökre lesznek örökölve. Például:

- Ha az [olvasó](built-in-roles.md#reader) szerepkört a felügyeleti csoport hatókörében lévő felhasználóhoz rendeli, a felhasználó a felügyeleti csoportban lévő összes előfizetésben is elolvashatja a mindent.
- Ha a [Számlázási olvasó](built-in-roles.md#billing-reader) szerepkört egy csoporthoz rendeli az előfizetés hatókörében, akkor a csoport tagjai elolvashatják az előfizetésben lévő összes erőforráscsoport és erőforrás számlázási adatait.
- Ha a [Közreműködő](built-in-roles.md#contributor) szerepkört hozzárendeli egy alkalmazáshoz az erőforrás-csoport hatókörében, az alkalmazás bármilyen típusú erőforrást kezelhet majd az adott erőforráscsoportban, az előfizetésben lévő többi erőforráscsoportban azonban nem.

 További információ: a [hatókör megismerése](scope-overview.md).

## <a name="step-4-check-your-prerequisites"></a>4. lépés: Az Előfeltételek ellenőrzése

A szerepkörök hozzárendeléséhez olyan felhasználóval kell bejelentkeznie, aki szerepkör-hozzárendelések írási engedéllyel rendelkezik, például a [tulajdonos](built-in-roles.md#owner) vagy a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) a szerepkörhöz hozzárendelni kívánt hatókörben. Hasonlóképpen, ha a szerepkör-hozzárendelést el szeretné távolítani, a szerepkör-hozzárendelések törlésére vonatkozó engedéllyel kell rendelkeznie.

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

Ha a felhasználói fiókja nem jogosult szerepkör hozzárendelésére az előfizetésen belül, hibaüzenet jelenik meg arról, hogy a fiókja "nem rendelkezik engedéllyel a Microsoft. Authorization/roleAssignments/Write" művelet végrehajtásához. " Ebben az esetben vegye fel a kapcsolatot az előfizetés rendszergazdájával, mivel az engedélyeket az Ön nevében rendelheti hozzá.

## <a name="step-5-assign-role"></a>5. lépés Szerepkör hozzárendelése

Ha ismeri a rendszerbiztonsági tag, a szerepkör és a hatókört, hozzárendelheti a szerepkört. Szerepköröket a Azure Portal, a Azure PowerShell, az Azure CLI, az Azure SDK-k vagy a REST API-k használatával rendelhet hozzá. Az egyes előfizetésekben akár **2000** szerepkör-hozzárendelést is megadhat. Ez a korlát tartalmazza az előfizetés, az erőforráscsoport és az erőforrás-hatókörök szerepkör-hozzárendeléseit. Az egyes felügyeleti csoportokban akár **500** szerepkör-hozzárendelést is megadhat.

A szerepkörök hozzárendelésének részletes lépéseiért tekintse meg a következő cikkeket.

- [Azure-szerepkörök kiosztása a Azure Portal használatával](role-assignments-portal.md)
- [Azure-szerepkörök kiosztása Azure PowerShell használatával](role-assignments-powershell.md)
- [Azure-szerepkörök kiosztása az Azure CLI-vel](role-assignments-cli.md)
- [Azure-szerepkörök kiosztása a REST API használatával](role-assignments-rest.md)

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz a Azure Portal használatával](quickstart-assign-role-user-portal.md)