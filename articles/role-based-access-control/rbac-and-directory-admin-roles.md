---
title: Klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure AD-szerepkörök
description: Ismerteti az Azure-beli különböző szerepköröket, a klasszikus előfizetés-rendszergazdai szerepköröket, az Azure-szerepköröket és a Azure Active Directory (Azure AD) szerepköröket
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2021
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro;
ms.openlocfilehash: 0b43f30c25767a135b98b756d61ed2535e1fbd22
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092200"
---
# <a name="classic-subscription-administrator-roles-azure-roles-and-azure-ad-roles"></a>Klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure AD-szerepkörök

Ha még csak ismerkedik az Azure-ral, a különféle Azure-beli szerepkörök működése kissé összetettnek tűnhet. Ez a cikk segítséget nyújt az alábbi szerepkörök áttekintéséhez, és ahhoz, hogy mikor érdemes használni őket:
- A hagyományos előfizetés-rendszergazdai szerepkörök
- Azure-szerepkörök
- Azure Active Directory (Azure AD) szerepkörök

## <a name="how-the-roles-are-related"></a>Hogyan kapcsolódnak egymáshoz a szerepkörök?

Az Azure szerepköreinek megismeréséhez érdemes tudni azok előzményeit is. Az Azure első megjelenésekor az erőforrásokhoz való hozzáférés kezeléséhez mindössze három rendszergazdai szerepkör állt rendelkezésre: a fiókadminisztrátor, a szolgáltatás-rendszergazda és a társadminisztrátor. Később az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lett hozzáadva. Az Azure RBAC egy viszonylag új engedélyezési rendszer, amely részletes hozzáférés-kezelési lehetőségeket nyújt az Azure-erőforrásokhoz. Az Azure RBAC számos beépített szerepkört tartalmaz, amelyek különböző hatókörökhöz rendelhetők, és lehetővé teszik saját egyéni szerepköreinek létrehozását. Az Azure AD-ban (például felhasználók, csoportok és tartományok) lévő erőforrások kezeléséhez több Azure AD-szerepkör is tartozik.

A következő ábra a klasszikus előfizetés-rendszergazdai szerepkörök, az Azure-szerepkörök és az Azure AD-szerepkörök összekapcsolásának részletes nézete.

![A különböző Azure-beli szerepkörök](./media/rbac-and-directory-admin-roles/rbac-admin-roles.png)


## <a name="classic-subscription-administrator-roles"></a>A hagyományos előfizetés-rendszergazdai szerepkörök

Az Azure három hagyományos előfizetés-rendszergazdai szerepköre a fiókadminisztrátor, a szolgáltatás-rendszergazda és a társadminisztrátor. A hagyományos előfizetés-rendszergazdák teljes körű hozzáféréssel rendelkeznek az Azure-előfizetéshez. Az Azure Portal, Azure Resource Manager API-k és a klasszikus üzemi modell segítségével végzik az erőforrások felügyeletét. Az Azure-beli regisztrációhoz használt fiók lesz automatikusan a fiókadminisztrátor és a szolgáltatás-rendszergazda. Ezután további társadminisztrátorok is hozzáadhatók. A szolgáltatás-rendszergazda és a Co-Administrators egyenértékű jogosultsággal rendelkeznek azon felhasználók számára, akik a tulajdonosi szerepkört (Azure-szerepkört) társították az előfizetés hatókörében. Az alábbi tábla a három hagyományos előfizetés-rendszergazdai szerepkör közötti különbségeket ismerteti.

| Hagyományos előfizetés-adminisztrátor | Korlát | Engedélyek | Jegyzetek |
| --- | --- | --- | --- |
| Fiókadminisztrátor | Azure-fiókonként 1 | <ul><li>A [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) számlázásának kezelése</li><li>Az összes előfizetést egyetlen fiókból kezelheti.</li><li>Új előfizetéseket hozhat létre.</li><li>Megszüntetheti az előfizetéseket.</li><li>Módosíthatja az előfizetés számlázási lehetőségeit.</li><li>Megváltoztathatja a szolgáltatás-rendszergazdát.</li></ul> | Elméleti szinten az előfizetés számlázási tulajdonosa. |
| Szolgáltatás-rendszergazda | Azure-előfizetésenként 1 | <ul><li>Kezelheti a szolgáltatásokat az [Azure Portalon](https://portal.azure.com).</li><li>Előfizetés megszakítása</li><li>Felhasználókat rendelhet hozzá a társadminisztrátor szerepkörhöz.</li></ul> | Alapértelmezés szerint új előfizetések esetén a fiókadminisztrátor a szolgáltatás-rendszergazda is egyben.<br>A szolgáltatás-rendszergazda ugyanolyan szintű hozzáféréssel rendelkezik az előfizetés hatókörében, mint a Tulajdonos szerepkörrel rendelkező felhasználók.<br>A szolgáltatásadminisztrátor teljes hozzáféréssel rendelkezik az Azure Portalhoz. |
| Társadminisztrátor | Előfizetésenként 200 | <ul><li>Ugyanazokkal a hozzáférési jogosultságokkal rendelkezik, mint a szolgáltatás-rendszergazda, de nem módosíthatja az előfizetések és az Azure-címtárak közötti társítást.</li><li>Felhasználókat rendelhet hozzá a társadminisztrátori szerepkörhöz, de nem változathatja meg a szolgáltatás-rendszergazda személyét.</li></ul> | A társadminisztrátor ugyanolyan szintű hozzáféréssel rendelkezik az előfizetés hatókörében, mint a Tulajdonos szerepkörrel rendelkező felhasználók. |

A Azure Portal a **klasszikus rendszergazdák** lapon kezelheti Co-Administrators vagy megtekintheti a szolgáltatás-rendszergazdát.

![A klasszikus Azure-előfizetés rendszergazdái a Azure Portal](./media/rbac-and-directory-admin-roles/subscription-view-classic-administrators.png)

A Azure Portal megtekintheti vagy módosíthatja a szolgáltatás-rendszergazdát, vagy megtekintheti a fiók rendszergazdáját az előfizetés tulajdonságok paneljén.

![Fiókadminisztrátor és szolgáltatás-rendszergazda az Azure Portalon](./media/rbac-and-directory-admin-roles/account-admin.png)

További információ: [klasszikus Azure-előfizetés rendszergazdái](classic-administrators.md).

### <a name="azure-account-and-azure-subscriptions"></a>Azure-fiók és Azure-előfizetések

Egy Azure-fiók egy számlázási kapcsolatot jelent. Az Azure-fiók egy felhasználói identitásból, egy vagy több Azure-előfizetésből és az azokhoz kapcsolódó Azure-erőforrásokból áll. A fiókot létrehozó személy lesz a fiókon belül létrehozott összes előfizetés fiókadminisztrátora. Ez a személy egyben az előfizetés alapértelmezett szolgáltatás-rendszergazdája is lesz.

Az Azure-előfizetés segít az Azure-erőforrásokhoz való hozzáférés rendezésében. Ezenfelül az előfizetés révén azt is megszabhatja, hogy hogyan szeretne jelentést készíteni az erőforrások használatáról, hogy hogyan számlázzák ki azt Önnek, illetve, hogy hogyan szeretne fizetni érte. Az egyes előfizetésekhez eltérő számlázási és fizetési beállítások tartozhatnak, így irodánként, részlegenként, projektenként stb. különböző előfizetéseket és csomagokat használhat. Minden szolgáltatás egy előfizetéshez tartozik, programozott műveletekhez pedig szükség lehet az előfizetés-azonosítóra.

Minden előfizetés egy Azure AD-címtárhoz van társítva. Az előfizetés társított könyvtárának megkereséséhez nyissa meg az **előfizetéseket** a Azure Portal, majd válasszon egy előfizetést a könyvtár megtekintéséhez.

A fiókok és előfizetések kezelése a [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)történik.

## <a name="azure-roles"></a>Azure-szerepkörök

Az Azure RBAC az [Azure Resource Managerre](../azure-resource-manager/management/overview.md) épülő engedélyezési rendszer, amely részletes hozzáférés-kezelési lehetőségeket nyújt például a számítási és a tárolási Azure-erőforrásokhoz. Az Azure RBAC több mint 70 beépített szerepkört tartalmaz. Négy alapvető Azure-szerepkör létezik. Az első három minden erőforrástípusra vonatkozik:

| Azure-szerepkör | Engedélyek | Jegyzetek |
| --- | --- | --- |
| [Tulajdonos](built-in-roles.md#owner) | <ul><li>Teljes hozzáféréssel rendelkezik az összes erőforráshoz.</li><li>Hozzáférést delegálhat mások számára.</li></ul> | A szolgáltatás-rendszergazda és társadminisztrátor Tulajdonos szerepkört kap az előfizetés hatókörében.<br>Minden erőforrástípusra alkalmazható. |
| [Közreműködő](built-in-roles.md#contributor) | <ul><li>Bármilyen típusú Azure-erőforrást létrehozhat és kezelhet.</li><li>Új bérlő létrehozása Azure Active Directory</li><li>Nem adhat hozzáférést mások számára.</li></ul> | Minden erőforrástípusra alkalmazható. |
| [Olvasó](built-in-roles.md#reader) | <ul><li>Megtekintheti az Azure-erőforrásokat.</li></ul> | Minden erőforrástípusra alkalmazható. |
| [Felhasználói hozzáférés adminisztrátora](built-in-roles.md#user-access-administrator) | <ul><li>Kezelheti az Azure-erőforrásokhoz való felhasználói hozzáférést.</li></ul> |  |

A többi beépített szerepkör adott Azure-erőforrások kezelését teszi lehetővé. Például a [Virtuális gépek közreműködője](built-in-roles.md#virtual-machine-contributor) szerepkör virtuális gépek létrehozását és kezelését teszi lehetővé. Az összes beépített szerepkör listáját itt tekintheti meg: az [Azure beépített szerepkörei](built-in-roles.md).

Csak a Azure Portal és a Azure Resource Manager API-k támogatják az Azure RBAC-t. Az Azure-szerepkörökhöz rendelt felhasználók, csoportok és alkalmazások nem használhatják a [klasszikus Azure üzembehelyezési modell API](../azure-resource-manager/management/deployment-models.md)-jait.

A Azure Portal az Azure RBAC használó szerepkör-hozzárendelések megjelennek a **hozzáférés-vezérlés (iam)** panelen. Ez a panel a portálon, például a felügyeleti csoportokban, előfizetésekben, erőforráscsoportok és különböző erőforrásokban található meg.

![A Hozzáférés-vezérlés (IAM) panel az Azure Portalon](./media/rbac-and-directory-admin-roles/access-control-role-assignments.png)

Amikor a **szerepkörök** lapra kattint, megjelenik a beépített és az egyéni szerepkörök listája.

![Beépített szerepkörök az Azure Portalon](./media/rbac-and-directory-admin-roles/roles-list.png)

További információ: [Azure-beli szerepkör-hozzárendelés hozzáadása vagy eltávolítása az Azure Portal használatával](role-assignments-portal.md).

## <a name="azure-ad-roles"></a>Azure AD-szerepkörök

Az Azure AD-szerepkörök használatával kezelhetők az Azure AD-erőforrások egy olyan címtárban, mint például a felhasználók létrehozása vagy szerkesztése, a rendszergazdai szerepkörök kiosztása, a felhasználói jelszavak alaphelyzetbe állítása, a felhasználói licencek kezelése és a tartományok kezelése. Az alábbi táblázat a fontosabb Azure AD-szerepköröket ismerteti.

| Azure AD-szerepkör | Engedélyek | Jegyzetek |
| --- | --- | --- |
| [Globális rendszergazda](../active-directory/roles/permissions-reference.md#global-administrator-permissions) | <ul><li>Kezeli az Azure Active Directory összes rendszergazdai funkciójához való hozzáférést, valamint az Azure Active Directoryban összevont szolgáltatásokat.</li><li>Rendszergazdai szerepköröket rendelhet másokhoz.</li><li>Bármely felhasználó és az összes többi rendszergazda jelszavát visszaállíthatja.</li></ul> | Az Azure Active Directory-bérlőre regisztráló személy lesz a globális rendszergazda. |
| [Felhasználói rendszergazda](../active-directory/roles/permissions-reference.md#user-administrator) | <ul><li>A felhasználók és csoportok minden összetevőjét létrehozhatja és kezelheti.</li><li>Támogatási jegyek kezelése</li><li>Monitorozhatja a szolgáltatás állapotát.</li><li>Módosíthatja a felhasználók, az ügyfélszolgálati rendszergazdák és egyéb felhasználói rendszergazdák jelszavát.</li></ul> |  |
| [Számlázási rendszergazda](../active-directory/roles/permissions-reference.md#billing-administrator) | <ul><li>Vásárlásokat hajthat végre.</li><li>Előfizetések kezelése</li><li>Támogatási jegyek kezelése</li><li>Monitorozhatja a szolgáltatás állapotát.</li></ul> |  |

A Azure Portal az Azure AD-szerepkörök listáját láthatja a **szerepkörök és rendszergazdák** panelen. Az összes Azure AD-szerepkör listáját lásd: [rendszergazdai szerepkör engedélyei Azure Active Directory](../active-directory/roles/permissions-reference.md).

![Azure AD-szerepkörök a Azure Portal](./media/rbac-and-directory-admin-roles/directory-admin-roles.png)

## <a name="differences-between-azure-roles-and-azure-ad-roles"></a>Az Azure-szerepkörök és az Azure AD-szerepkörök közti különbségek

Az Azure-szerepkörök magas szinten szabályozzák az Azure-erőforrások kezelésére vonatkozó engedélyeket, míg az Azure AD-szerepkörök a Azure Active Directory erőforrások kezeléséhez szükséges engedélyeket szabályozzák. A következő táblázat a fontosabb különbségeket ismerteti.

| Azure-szerepkörök | Azure AD-szerepkörök |
| --- | --- |
| Azure-erőforrásokhoz való hozzáférés kezelése | Az Azure Active Directory-erőforrásokhoz való hozzáférést kezelik. |
| Támogatják az egyéni szerepköröket. | Támogatják az egyéni szerepköröket. |
| A hatókör több szinten adható meg (kezelési csoport, előfizetés, erőforráscsoport, erőforrás). | A hatókör a bérlő szintje. |
| A szerepkörre vonatkozó információk az Azure Portalon, az Azure CLI-ben, az Azure PowerShellben, az Azure Resource Manager-sablonokban vagy a REST API-n érhetők el. | A szerepkör-információk az Azure felügyeleti portálon, Microsoft 365 felügyeleti központban, Microsoft Graphban, a AzureAD PowerShellben érhetők el. |

### <a name="do-azure-roles-and-azure-ad-roles-overlap"></a>Átfedésben vannak az Azure-szerepkörök és az Azure AD-szerepkörök?

Alapértelmezés szerint az Azure-szerepkörök és az Azure AD-szerepkörök nem fedik át az Azure-t és az Azure AD-t. Ha azonban a globális rendszergazda megemeli a hozzáférését az **Azure-erőforrások hozzáférés-kezelésének** kiválasztásával a Azure Portalban, a globális rendszergazda a [felhasználói hozzáférés rendszergazdai](built-in-roles.md#user-access-administrator) szerepkört (Azure-szerepkör) kapja az adott bérlő összes előfizetéséhez. A felhasználói hozzáférés rendszergazdája szerepkörrel a felhasználó hozzáférést biztosíthat mások számára Azure-erőforrásokhoz. Ez a kapcsoló az előfizetésekhez való hozzáférés visszanyeréséhez lehet hasznos. További információ: jogosultságszint- [emelési hozzáférés az összes Azure-előfizetés és-felügyeleti csoport kezeléséhez](elevate-access-global-admin.md).

Számos Azure AD-szerepkör az Azure AD-t és Microsoft 365-t, például a globális rendszergazdai és a felhasználói rendszergazdai szerepköröket öleli fel. Ha például a globális rendszergazdai szerepkör tagja, globális rendszergazdai képességekkel rendelkezik az Azure AD-ben és a Microsoft 365ban, például a Microsoft Exchange és a Microsoft SharePoint módosításával. Alapértelmezés szerint azonban a globális rendszergazda nem rendelkezik hozzáféréssel az Azure-erőforrásokhoz.

![Azure RBAC és Azure AD-szerepkörök](./media/rbac-and-directory-admin-roles/azure-office-roles.png)

## <a name="next-steps"></a>További lépések

- [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](overview.md)
- [Rendszergazdai szerepkörök engedélyezése az Azure Active Directoryban](../active-directory/roles/permissions-reference.md)
- [Klasszikus Azure-előfizetés rendszergazdái](classic-administrators.md)
