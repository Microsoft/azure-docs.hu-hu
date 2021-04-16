---
title: Bérlők kezelése a Microsoft Ügyfélszerződés számlázási fiókjában – Azure
description: A cikk segítséget nyújt a számlázási fiókjához társított bérlők Microsoft Ügyfélszerződés kezeléséhez.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: dc34d0f12430838be29897ccc5cbeee382ecaa2b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107493078"
---
# <a name="manage-tenants-in-your-microsoft-customer-agreement-billing-account"></a>Bérlők kezelése a Microsoft Ügyfélszerződés számlázási fiókjában

A cikk segítséget nyújt a számlázási fiókjához társított bérlők Microsoft Ügyfélszerződés kezeléséhez. A bérlők kezelésére, az előfizetések átvitelére és a számlázási tulajdonjog felügyeletére vonatkozó információk segítségével biztosíthatja a számlázási környezet biztonságos hozzáférését.

## <a name="whats-a-tenant"></a>Mi az a bérlő?

A bérlő a szervezet digitális reprezentációja, és elsődlegesen egy tartományhoz van társítva, például Microsoft.com. Ez egy olyan környezet, amely Azure Active Directory lehetővé teszi, hogy felhasználói engedélyeket rendeljen hozzá az Azure-erőforrások és -számlázás kezeléséhez.

Az egyes bérlők elkülönülnek a többi bérlőtől, de engedélyezheti más bérlők vendégfelhasználói számára, hogy hozzáférjenek a bérlőhöz a költségek nyomon követése és a számlázás kezelése érdekében.

## <a name="how-tenants-and-subscriptions-relate-to-billing-account"></a>Hogyan kapcsolódnak a bérlők és az előfizetések a számlázási fiókhoz?

A saját Microsoft Ügyfélszerződés (számlázási fiók) használatával követheti nyomon a költségeket és kezelheti a számlázást. Minden számlázási fióknak van legalább egy számlázási profilja. A számlázási profil lehetővé teszi a számla és a fizetési mód kezelését. Alapértelmezés szerint minden számlázási profil egy számlaszakaszt tartalmaz. Szükség esetén több számlaszakaszt is létrehozhat a költségek csoportosítására, nyomon követésére és kezelésére.

- A számlázási fiók egyetlen bérlőhöz van társítva. Ez azt jelenti, hogy csak a bérlőhöz hozzáférő felhasználók férhetnek hozzá a számlázási fiókjához.
- Amikor létrehoz egy új Azure-előfizetést a számlázási fiókjához, az mindig a számlázási fiók bérlőjében jön létre. Az előfizetéseket azonban áthelyezheti más bérlőkbe. Más bérlőkből származó meglévő előfizetéseket is összekapcsolhat a számlázási fiókkal. Lehetővé teszi, hogy egyetlen bérlőn keresztül központilag kezelje a számlázást, miközben az erőforrásokat és az előfizetéseket az igényeinek megfelelően más bérlőkben tartja.

Az alábbi ábra bemutatja, hogyan kapcsolódnak a számlázási fiókok és az előfizetések a bérlőkhöz. A Contoso MCA számlázási fiókja az 1. bérlőhöz, míg a Contoso PAYG-fiókja a 2. bérlőhöz van társítva. Tegyük fel, hogy a Contoso az MCA számlázási fiókján keresztül szeretne fizetni a PAYG-előfizetésért. A számlázási tulajdonjog átadásával összekapcsolhatja az előfizetést az MCA számlázási fiókjával. Az előfizetés és az erőforrásai továbbra is a 2. bérlőhöz lesznek társítva, de az MCA számlázási fiók használatával lesznek kifizetve.

:::image type="content" source="./media/manage-tenants/billing-hierarchy-example.png" alt-text="Példa számlázási hierarchiát bemutató ábra." border="false" lightbox="./media/manage-tenants/billing-hierarchy-example.png":::

## <a name="manage-subscriptions-under-multiple-tenants-in-a-single-microsoft-customer-agreement"></a>Előfizetések kezelése több bérlő alatt egyetlen Microsoft Ügyfélszerződés

A számlázási tulajdonosok akkor hozhatnak létre előfizetéseket, ha megfelelő engedélyekkel rendelkezik [a](../manage/understand-mca-roles.md#subscription-billing-roles-and-tasks) számlázási fiókhoz. Alapértelmezés szerint a bérlői bérlőben Microsoft Ügyfélszerződés új előfizetések Microsoft Ügyfélszerződés vannak.

- Más bérlőkből származó előfizetéseket is összekapcsolhat a Microsoft Ügyfélszerződés számlázási fiókjával. Az előfizetés számlázási tulajdonjogának jegyzése csak a számlázási elrendezést módosítja. Ez nincs hatással a szolgáltatásbérlői vagy az Azure RBAC-szerepkörökre.
- Ha módosítania kell az előfizetés tulajdonosát a szolgáltatásbérlőben, az előfizetést egy másik [címtárba Azure Active Directory át.](../../role-based-access-control/transfer-subscription.md)

## <a name="add-guest-users-to-your-microsoft-customer-agreement-tenant"></a>Vendégfelhasználók hozzáadása a Microsoft Ügyfélszerződés bérlőhöz

Az ön számlázási bérlőjéhez Microsoft Ügyfélszerződés felhasználók vendégként kell meghívni a számlázási feladatokat egy másik bérlőből.

Ha meg akar hívni valakit vendégként, a felhasználónak olyan meglévő e-mail-címmel kell lennie, amely nem az Ön Azure Active Directory (AD) tartománytól. Az Azure AD e-mailt küld a vendégfelhasználónak a hitelesítésre mutató hivatkozással.

:::image type="content" source="./media/manage-tenants/guest-invitation-email.png" alt-text="Példa e-mailes meghívót bemutató képernyőkép." lightbox="./media/manage-tenants/guest-invitation-email.png" :::

Amikor egy felhasználót hozzáadnak a Microsoft Ügyfélszerződés bérlőhöz, el kell [fogadnia a meghívót.](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)

Amikor kiválasztják a Meghívó **elfogadása** hivatkozást, a rendszer kérni fogja, hogy hitelesítse magát az Azure-ral.

:::image type="content" source="./media/manage-tenants/authenticate.png" alt-text="Képernyőkép az Azure-hitelesítésre vonatkozó kérésről." lightbox="./media/manage-tenants/authenticate.png" :::

Ezután kiválasztják az **Elfogadás lehetőséget.**

:::image type="content" source="./media/manage-tenants/accept-invitation.png" alt-text="A meghívó elfogadására vonatkozó kérést bemutató képernyőkép." lightbox="./media/manage-tenants/accept-invitation.png" :::

Az elfogadásuk után megtekinthetik az Microsoft Ügyfélszerződés [számlázási fiókját a Cost Management + Billing.](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement)

:::image type="content" source="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" alt-text="Képernyőkép a Microsoft Ügyfélszerződés a számlázási fiókok listájáról." lightbox="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" :::

A vendégfelhasználók meghívásának engedélyezése az Azure AD beállításai alapján történik. A beállítások értéke a Szervezeti kapcsolatok lapon, a **Beállítások** **alatt látható.** Győződjön meg arról, hogy a beállítás ki van jelölve, ellenkező esetben a meghívás sikertelen lesz. További információ: [Vendégfelhasználói hozzáférési engedélyek korlátozása.](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)

:::image type="content" source="./media/manage-tenants/external-collaboration-settings.png" alt-text="Képernyőkép a Külső együttműködési beállításokról." lightbox="./media/manage-tenants/external-collaboration-settings.png" :::

> [!IMPORTANT]
> A vendégfelhasználók hozzáférést Microsoft Ügyfélszerződés bérlőhöz, ami biztonsági kockázatot jelenthet. További információ: A vendégfelhasználók alapértelmezett engedélyeinek [korlátozása.](../../active-directory/fundamentals/users-default-permissions.md#restrict-member-users-default-permissions)

## <a name="manage-multiple-microsoft-cloud-services-under-an-azure-ad-tenant"></a>Több Microsoft-felhőszolgáltatás kezelése egy Azure AD-bérlőben

Egyetlen Azure AD-bérlőn belül több felhőszolgáltatást is kezelhet a szervezet számára. A Microsoft összes felhőajánlatának felhasználói fiókjai az Azure AD-bérlőben vannak tárolva, amely felhasználói fiókokat és csoportokat tartalmaz. Az alábbi ábrán egy olyan szervezet látható, amely több szolgáltatást használ, és egy közös, fiókokat tartalmazó Azure AD-bérlőt használ. Minden szolgáltatás saját, kék szöveggel írt portállal rendelkezik, ahol a felhasználók kezelhetik szolgáltatásaikat.

:::image type="content" source="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png" alt-text="Diagram egy olyan szervezetről, amely több szolgáltatást használ, és egy közös Azure AD-bérlőt használ, amely fiókokat tartalmaz." border="false" lightbox="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png":::

## <a name="next-steps"></a>Következő lépések

A következő cikkekből megtudhatja, hogyan felügyeli a rugalmas számlázási tulajdonjogot, és hogyan biztosíthatja a hozzáférés biztonságos Microsoft Ügyfélszerződés.

- [Bérlő beállítása](../../active-directory/develop/quickstart-create-new-tenant.md)
- [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md)
- [Azure-előfizetés átvitele másik Azure AD-címtárba](../../role-based-access-control/transfer-subscription.md)
- [Vendég-hozzáférési engedélyek korlátozása (előzetes verzió) a Azure Active Directory](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)
- [Vendégfelhasználók hozzáadása a címtárhoz az Azure Portalon](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Mik az alapértelmezett felhasználói engedélyek a Azure Active Directory?](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Mi az az Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)