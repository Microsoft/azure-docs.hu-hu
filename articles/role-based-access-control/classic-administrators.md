---
title: Klasszikus Azure-előfizetés rendszergazdái
description: Ismerteti, hogyan lehet hozzáadni vagy módosítani az Azure Co-Administrator és a szolgáltatás-rendszergazdai szerepköröket, valamint a fiók rendszergazdájának megtekintését.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b61636d6048f63ae962d4f755a29c02e6785d5e1
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557537"
---
# <a name="azure-classic-subscription-administrators"></a>Klasszikus Azure-előfizetés rendszergazdái

A Microsoft azt javasolja, hogy az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével kezelje az Azure-erőforrásokhoz való hozzáférést. Ha azonban továbbra is a klasszikus üzemi modellt használja, akkor klasszikus előfizetés-rendszergazdai szerepkört kell használnia: a szolgáltatás-rendszergazdát és a társ-rendszergazdát. További információ: Azure Resource Manager és [klasszikus üzembe helyezés](../azure-resource-manager/management/deployment-models.md).

Ez a cikk azt ismerteti, hogyan lehet hozzáadni vagy módosítani a Co-Administrator és a szolgáltatás-rendszergazdai szerepköröket, valamint a fiók rendszergazdájának megtekintését.

## <a name="add-a-co-administrator"></a>Társadminisztrátor hozzáadása

> [!TIP]
> Csak akkor kell hozzáadnia egy Co-Administrator, ha a felhasználónak az [Azure Service Management PowerShell-modul](/powershell/module/servicemanagement/azure.service)használatával kell kezelnie a klasszikus Azure-telepítéseket. Ha a felhasználó csak a klasszikus erőforrások kezeléséhez használja a Azure Portal, nem kell hozzáadnia a felhasználó klasszikus rendszergazdáját.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) szolgáltatás-rendszergazdaként vagy társ-rendszergazdaként.

1. Nyissa meg az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nézetet, és válasszon ki egy előfizetést.

    Co-Administrators csak az előfizetés hatókörében lehet hozzárendelni.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **klasszikus rendszergazdák** fülre.

    ![A klasszikus rendszergazdákat megnyitó képernyőkép](./media/classic-administrators/classic-administrators.png)

1. Kattintson a **Hozzáadás**  >  a **társ-rendszergazda** hozzáadásához elemre a közös rendszergazdák hozzáadása panel megnyitásához.

    Ha a társ-rendszergazda hozzáadása lehetőség le van tiltva, akkor nem rendelkezik a megfelelő engedélyekkel.

1. Válassza ki a hozzáadni kívánt felhasználót, és kattintson a **Hozzáadás** gombra.

    ![A társ-rendszergazda hozzáadására szolgáló képernyőkép](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Vendég felhasználó hozzáadása Co-Administratorként

Ha egy vendég felhasználót szeretne hozzáadni a társ-rendszergazdaként, kövesse az előző, a [társ-rendszergazda hozzáadása](#add-a-co-administrator) szakaszban leírt lépéseket. A vendég felhasználónak meg kell felelnie a következő feltételeknek:

- A vendég felhasználónak rendelkeznie kell egy jelenléttel a címtárban. Ez azt jelenti, hogy a felhasználó megkapta a címtárat, és elfogadta a meghívást.

Ha további információt szeretne arról, hogyan adhat hozzá egy vendég felhasználót a címtárhoz, tekintse meg [a Azure Active Directory B2B Collaboration-felhasználók hozzáadása a Azure Portal-ben](../active-directory/external-identities/add-users-administrator.md)című témakört.

### <a name="differences-for-guest-users"></a>A vendég felhasználói közötti különbségek

Előfordulhat, hogy a Co-Administrator szerepkörhöz rendelt vendég felhasználók a Co-Administrator szerepkörrel rendelkező felhasználókhoz képest eltéréseket tapasztalnak. Vegyük példaként a következő esetet:

- Az A felhasználó Azure AD-fiókkal (munkahelyi vagy iskolai fiókkal) az Azure-előfizetések szolgáltatás-rendszergazdája.
- A B felhasználó Microsoft-fiók rendelkezik.
- Az A felhasználó hozzárendeli a Co-Administrator szerepkört a B felhasználóhoz.
- A B felhasználó szinte mindent megtehet, de nem tudja regisztrálni az alkalmazásokat, vagy megkeresni a felhasználókat az Azure AD-címtárban.

Azt várná, hogy a B felhasználó mindent tud kezelni. Ennek a különbségnek az az oka, hogy a Microsoft-fiók a tag felhasználója helyett vendég felhasználóként adja hozzá az előfizetéshez. A vendég felhasználók a felhasználókhoz képest eltérő alapértelmezett engedélyekkel rendelkeznek az Azure AD-ben. Például a felhasználók más felhasználókat is olvashatnak az Azure AD-ben, és a vendég felhasználói nem. A tagok felhasználói regisztrálhatnak új egyszerű szolgáltatásokat az Azure AD-ben, és a vendég felhasználói nem rendelkezhetnek.

Ha egy vendégnek el kell tudnia hajtani ezeket a feladatokat, lehetséges megoldás a vendég felhasználói igényeknek megfelelő Azure AD-szerepkörök hozzárendelésére. Az előző forgatókönyvben például hozzárendelheti a [címtár-olvasók](../active-directory/roles/permissions-reference.md#directory-readers) szerepkört más felhasználók olvasásához és az [alkalmazás fejlesztői](../active-directory/roles/permissions-reference.md#application-developer) szerepkörének hozzárendeléséhez, hogy az egyszerű szolgáltatásokat lehessen létrehozni. További információ a tagok és a vendég felhasználókról és azok engedélyeiről: [Mi az alapértelmezett felhasználói engedély a Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md) További információ a vendégek hozzáférésének biztosításáról: [Azure-szerepkörök hozzárendelése külső vendég felhasználókhoz a Azure Portal használatával](role-assignments-external-users.md).

Vegye figyelembe, hogy az [Azure beépített szerepkörei](../role-based-access-control/built-in-roles.md) eltérnek az [Azure ad szerepköreitől](../active-directory/roles/permissions-reference.md). A beépített szerepkörök nem biztosítanak hozzáférést az Azure AD-hoz. További információ: [a különböző szerepkörök megismerése](../role-based-access-control/rbac-and-directory-admin-roles.md).

A felhasználók és a vendég felhasználók összevetésével kapcsolatos információkért tekintse [meg a mi az alapértelmezett felhasználói engedélyek a Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)című témakört.

## <a name="remove-a-co-administrator"></a>Co-Administrator eltávolítása

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) szolgáltatás-rendszergazdaként vagy társ-rendszergazdaként.

1. Nyissa meg az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nézetet, és válasszon ki egy előfizetést.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **klasszikus rendszergazdák** fülre.

1. Vegyen fel egy pipát az eltávolítani kívánt Co-Administrator mellett.

1. Kattintson az **Eltávolítás** elemre.

1. A megjelenő üzenetablakban kattintson az **Igen** gombra.

    ![A társ-rendszergazda eltávolítására szolgáló képernyőkép](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Megváltoztathatja a szolgáltatás-rendszergazdát.

Csak a fiók rendszergazdája módosíthatja az előfizetés szolgáltatás-rendszergazdáját. Alapértelmezés szerint az Azure-előfizetésre való regisztráláskor a szolgáltatás rendszergazdája megegyeznek a fiók rendszergazdájával. A fiók-rendszergazdai szerepkörrel rendelkező felhasználó nem fér hozzá a Azure Portalhoz. A szolgáltatás-rendszergazdai szerepkörrel rendelkező felhasználó teljes hozzáféréssel rendelkezik a Azure Portalhoz. Ha a fiók rendszergazdája és a szolgáltatás rendszergazdája ugyanaz a felhasználó, és egy másik felhasználóra módosítja a szolgáltatás-rendszergazdát, akkor a fiók rendszergazdája elveszti a hozzáférést Azure Portal. A fiók rendszergazdája azonban mindig használhatja a Account centert a szolgáltatás-rendszergazda saját magukra való visszaállítására.

Az alábbi lépéseket követve módosíthatja a szolgáltatás-rendszergazdát a Azure Portalban.

1. Ellenőrizze, hogy a forgatókönyv támogatott-e, [Ha ellenőrzi a szolgáltatás-rendszergazda módosításának korlátozásait](#limitations-for-changing-the-service-administrator).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként.

1. Nyissa meg **Cost Management + számlázást** , és válasszon egy előfizetést.

1. A bal oldali navigációs sávon kattintson a **Tulajdonságok** elemre.

1. Kattintson a **szolgáltatás-rendszergazda** lehetőségre.

    ![A Azure Portal előfizetés tulajdonságait ábrázoló képernyőkép](./media/classic-administrators/service-admin.png)

1. A szolgáltatás-adminisztrátor **szerkesztése** lapon adja meg az új szolgáltatás-rendszergazda e-mail-címét.

    ![A szolgáltatás-rendszergazda szerkesztése lapot ábrázoló képernyőfelvétel](./media/classic-administrators/service-admin-edit.png)

1. Kattintson az **OK** gombra a módosítás mentéséhez.

### <a name="limitations-for-changing-the-service-administrator"></a>A szolgáltatás-rendszergazda módosításának korlátai

Azure-előfizetések esetében csak egy szolgáltatás-rendszergazda lehet. A szolgáltatás-rendszergazda módosítása attól függően eltérő lesz, hogy a fiók rendszergazdája Microsoft-fiók-e, vagy az Azure AD-fiók (munkahelyi vagy iskolai fiók).

| Fiók-rendszergazdai fiók | Megváltoztathatja a szolgáltatás rendszergazdáját egy másik Microsoft-fiók? | Megváltoztathatja a szolgáltatás-rendszergazdát egy olyan Azure AD-fiókra, amely ugyanabban a címtárban van? | Megváltoztathatja a szolgáltatás rendszergazdáját egy másik címtárban lévő Azure AD-fiókra? |
| --- | --- | --- | --- |
| Microsoft-fiók | Igen | Nem | Nem |
| Azure AD-fiók | Igen | Igen | Nem |

Ha a fiók rendszergazdája Azure AD-fiók, akkor a szolgáltatás-rendszergazdát egy olyan Azure AD-fiókra módosíthatja, amely ugyanabban a címtárban van, de nem egy másik címtárban. Például abby@contoso.com megváltoztathatja a szolgáltatás-rendszergazdát bob@contoso.com , de nem módosíthatja a szolgáltatás-rendszergazdát, hogy a john@notcontoso.com john@notcontoso.com contoso.com-címtárban ne legyen jelen.

A Microsoft-fiókokkal és az Azure AD-fiókokkal kapcsolatos további információkért lásd: [Mi az Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md)

## <a name="view-the-account-administrator"></a>A fiókadminisztrátor megtekintése

A fiók rendszergazdája az a felhasználó, aki először regisztrált az Azure-előfizetésre, és felelős az előfizetés számlázási tulajdonosának. Az előfizetés rendszergazdai fiókjának módosításához tekintse meg az [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../cost-management-billing/manage/billing-subscription-transfer.md)című témakört.

A fiók rendszergazdájának megtekintéséhez kövesse az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Nyissa meg **Cost Management + számlázást** , és válasszon egy előfizetést.

1. A bal oldali navigációs sávon kattintson a **Tulajdonságok** elemre.

    Az előfizetés fiókjának rendszergazdája megjelenik a **fiók** adminisztrátora mezőben.

    ![A fiók rendszergazdáját ábrázoló képernyőfelvétel](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Következő lépések

* [A különböző szerepkörök ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Azure-szerepkörök kiosztása a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)
* [Azure-előfizetés-rendszergazdák hozzáadása vagy módosítása](../cost-management-billing/manage/add-change-subscription-administrator.md)