---
title: Meglévő Azure-előfizetés hozzáadása a bérlőhöz – Azure AD
description: Útmutató meglévő Azure-előfizetés Azure Active Directory (Azure AD) bérlőhöz való hozzáadásához.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ac9553660aace8242c81b41fa2cc9171d28219
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594631"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Azure-előfizetés társítása vagy hozzáadása az Azure Active Directory-bérlőhöz

Az Azure-előfizetés megbízhatósági kapcsolatban áll Azure Active Directory (Azure AD) szolgáltatással. Egy előfizetés megbízik az Azure AD-ben a felhasználók, szolgáltatások és eszközök hitelesítéséhez.

Több előfizetés is megbízhat ugyanabban az Azure AD-címtárban. Az egyes előfizetések csak egyetlen címtárban bízhatnak meg.

Egy vagy több Azure-előfizetés képes megbízhatósági kapcsolatot létesíteni Azure Active Directory (Azure AD) egy példányával, hogy hitelesítse és engedélyezze a rendszerbiztonsági tag és eszközök Azure-szolgáltatásokkal való hitelesítését.  Ha egy előfizetés lejár, az Azure AD szolgáltatás megbízható példánya marad, de a rendszerbiztonsági tag elveszti az Azure-erőforrásokhoz való hozzáférést.

Amikor egy felhasználó regisztrál a Microsoft Cloud Service szolgáltatásra, létrejön egy új Azure AD-bérlő, és a felhasználó tagja lesz a globális rendszergazdai szerepkörnek. Ha azonban egy előfizetés tulajdonosa egy meglévő bérlőhöz csatlakozik az előfizetéshez, a tulajdonos nincs hozzárendelve a globális rendszergazdai szerepkörhöz.

Az összes felhasználó rendelkezik egyetlen *kezdőkönyvtár* -címtárral a hitelesítéshez. A felhasználók más címtárakban is lehetnek vendégként. Az Azure AD-ben az egyes felhasználók otthoni és vendég könyvtára is megtekinthető.

> [!Important]
> Ha egy másik címtárral társít egy előfizetést, az [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/role-assignments-portal.md) használatával hozzárendelt szerepkörökkel rendelkező felhasználók elvesztik a hozzáférésüket. A hagyományos előfizetés-rendszergazdák, köztük a szolgáltatásadminisztrátor és a társrendszergazdák is elveszítik a hozzáférésüket.
>
> Ha áthelyezi az Azure Kubernetes-szolgáltatási (ak-) fürtöt egy másik előfizetésbe, vagy áthelyezi a fürt tulajdonosának előfizetését egy új bérlőre, akkor a fürt elveszti a szerepkör-hozzárendelések és a szolgáltatásnév jogosultságai miatti működőképességét. További információ az AK-ról: [Azure Kubernetes Service (ak)](../../aks/index.yml).

## <a name="before-you-begin"></a>Előkészületek

Az előfizetés hozzárendelése vagy hozzáadása előtt végezze el a következő feladatokat:

- Tekintse át az alábbi módosításokat, amelyek az előfizetés hozzárendelése vagy hozzáadása után következnek be, és hogy milyen hatással lehet a következőkre:

  - Azok a felhasználók, akik az Azure RBAC-vel rendeltek szerepköröket, elvesztik a hozzáférést.
  - A szolgáltatás-rendszergazda és a Co-Administrators elvesztik a hozzáférést.
  - Ha rendelkezik kulcstartókkal, nem lesznek elérhetők, és a társítás után ki kell javítani őket.
  - Ha bármilyen felügyelt identitással rendelkezik olyan erőforrásokhoz, mint a Virtual Machines vagy a Logic Apps, a társítás után újra engedélyeznie kell vagy újra létre kell hoznia azokat.
  - Ha regisztrálva van Azure Stack, a társítás után újra regisztrálnia kell.
  - További információ: [Azure-előfizetés átadása egy másik Azure AD-címtárba](../../role-based-access-control/transfer-subscription.md).

- Jelentkezzen be egy olyan fiókkal, amely a következőket használja:

  - [Tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkör-hozzárendelést tartalmaz az előfizetéshez. További információ a tulajdonosi szerepkör hozzárendeléséről: Azure- [szerepkörök kiosztása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).
  - Az aktuális könyvtárban és az új könyvtárban is megtalálható. Az aktuális könyvtár társítva van az előfizetéshez. Az új könyvtárat az előfizetéshez társítja. További információ egy másik címtár elérésének beszerzéséről: [Azure Active Directory B2B együttműködési felhasználók hozzáadása a Azure Portal](../external-identities/add-users-administrator.md).

- Győződjön meg arról, hogy nem használ Azure Cloud Service Providers (CSP) előfizetést (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), a Microsoft belső előfizetését (MS-AZR-0015P) vagy egy Microsoft Azure for Students Starter-előfizetést (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Előfizetés hozzárendelése egy címtárhoz<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

A meglévő előfizetés Azure AD-címtárhoz való hozzárendeléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be, és válassza ki a használni kívánt előfizetést a [Azure Portal előfizetések lapján](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Válassza a **címtár módosítása** lehetőséget.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png" alt-text="Képernyőkép, amely megjeleníti az előfizetések lapját, és kiemelte a módosítási könyvtár lehetőséget.":::

1. Tekintse át a megjelenő figyelmeztetéseket, majd kattintson a **módosítás** gombra.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png" alt-text="Képernyőkép: a könyvtár módosítása egy minta könyvtárral és a módosítás gomb kiemelve.":::

   Miután megtörtént az előfizetés címtárának módosítása, megjelenik egy sikeres üzenet.

1. Az előfizetés lapon válassza a **könyvtárak váltása** lehetőséget, hogy megnyissa az új könyvtárat.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/directory-switcher.png" alt-text="Képernyőkép, amely a címtár-kapcsoló oldalát jeleníti meg a mintaadatok használatával.":::

   Több órát is igénybe vehet, hogy minden megfelelően megjelenjen. Ha úgy tűnik, hogy túl sokáig tart, ellenőrizze a **globális előfizetés szűrőjét**. Győződjön meg arról, hogy az áthelyezett előfizetés nem rejtett. Előfordulhat, hogy ki kell jelentkeznie a Azure Portal, majd újra be kell jelentkeznie az új könyvtár megtekintéséhez.

Az előfizetés könyvtárának módosítása szolgáltatás szintű művelet, így nem befolyásolja az előfizetés számlázási tulajdonjogát. Az eredeti könyvtár törléséhez át kell vinnie az előfizetés számlázási tulajdonosát egy új fiók Rendszergazdájába. További információ a számlázási tulajdonjog átadásáról: [Azure-előfizetés tulajdonjogának átadása másik fiókra](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Társítás utáni lépések

Miután hozzárendelt egy előfizetést egy másik címtárhoz, előfordulhat, hogy a következő feladatokat kell végrehajtania a műveletek folytatásához:

- Ha rendelkezik kulcstartóval, módosítania kell a Key Vault bérlői AZONOSÍTÓját. További információ: [Key Vault-bérlő azonosítójának módosítása az előfizetés áthelyezése után](../../key-vault/general/move-subscription.md).

- Ha a rendszer által hozzárendelt felügyelt identitásokat használta az erőforrásokhoz, újra engedélyeznie kell ezeket az identitásokat. Ha felhasználó által hozzárendelt felügyelt identitásokat használt, ezeket az identitásokat újra létre kell hoznia. A felügyelt identitások ismételt engedélyezése vagy újbóli létrehozása után újra létre kell hoznia az identitásokhoz rendelt engedélyeket. További információ: [Mik az Azure-erőforrások felügyelt identitásai?](../managed-identities-azure-resources/overview.md).

- Ha az előfizetést használó Azure Stack regisztrált, újra kell regisztrálnia. További információ: [Azure stack hub regisztrálása az Azure](/azure-stack/operator/azure-stack-registration)-ban.

- További információ: [Azure-előfizetés átadása egy másik Azure AD-címtárba](../../role-based-access-control/transfer-subscription.md).

## <a name="next-steps"></a>Következő lépések

- Új Azure AD-bérlő létrehozásához tekintse meg a rövid útmutató [: új bérlő létrehozása a Azure Active Directory-ben](active-directory-access-create-new-tenant.md)című témakört.

- Ha többet szeretne megtudni a Microsoft Azure az erőforrás-hozzáférés szabályozásáról, tekintse meg a [klasszikus előfizetés-rendszergazdai szerepköröket, az Azure-szerepköröket és az Azure ad rendszergazdai szerepköreit](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Ha többet szeretne megtudni a szerepkörök Azure AD-ben való hozzárendeléséről, tekintse meg a [rendszergazdai és nem rendszergazdai szerepkörök társítása a Azure Active Directoryhoz a felhasználók számára](active-directory-users-assign-role-azure-portal.md)című témakört.
