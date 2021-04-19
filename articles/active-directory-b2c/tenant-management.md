---
title: A Azure Active Directory B2C
titleSuffix: Azure Active Directory B2C
description: Ismerje meg, hogyan kezelheti a Azure Active Directory B2C bérlőt. Megtudhatja, hogy a Azure AD B2C mely Azure AD-szolgáltatásokat támogatják, hogyan kezelheti az erőforrásokat rendszergazdai szerepkörökkel, és hogyan adhat hozzá munkahelyi fiókokat és vendégfelhasználókat a Azure AD B2C bérlőjéhez.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c3ea17a4f6dc2fb5134c6ceb1ae37d25e0881365
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715455"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>A Azure Active Directory B2C kezelése

A Azure Active Directory B2C (Azure AD B2C) a bérlő a fogyasztói felhasználók címtárát képviseli. Minden Azure AD B2C bérlő eltérő, és elkülönül a többi bérlőtől Azure AD B2C bérlőtől. A Azure AD B2C bérlő eltér a Azure Active Directory bérlőtől, amely lehet, hogy már rendelkezik ilyen bérlővel. Ebből a cikkből megtudhatja, hogyan kezelheti a Azure AD B2C bérlőt.

## <a name="supported-azure-ad-features"></a>Támogatott Azure AD-funkciók

Azure AD B2C az Azure AD platformra támaszkodik. A következő Azure AD-funkciók használhatók a Azure AD B2C bérlőben.

|Szolgáltatás  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [Csoportok](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | A csoportokkal kezelhetők a rendszergazdai és a felhasználói fiókok.| A csoportok a rendszergazdai fiókok kezelésére használhatók. [A fogyasztói fiókok](user-overview.md#consumer-user) nem támogatják a csoportokat. |
| [Vendég External Identities meghívása](../active-directory//external-identities/add-users-administrator.md)| Vendégfelhasználókat hívhat meg, és konfigurálhat External Identities, például a Facebook- és Google-fiókokkal való összevonást és bejelentkezést. | Az alkalmazások eléréséhez Microsoft-fiók Azure AD-felhasználót vagy egy Azure AD-felhasználót vendégként meghívhat az Azure AD-bérlőhöz. Fogyasztói [fiókok esetén](user-overview.md#consumer-user)a felhasználói folyamatok Azure AD B2C egyéni szabályzatok használatával kezelheti a felhasználókat, és regisztrálhet vagy bejelentkezhet olyan külső identitásszolgáltatókkal, mint a Google vagy a Facebook. |
| [Szerepkörök és rendszergazdák](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| Rendszergazdai és felhasználói fiókok esetén teljes mértékben támogatott. | A felhasználói fiókok nem támogatják [a szerepköröket.](user-overview.md#consumer-user) A fogyasztói fiókok nem férnek hozzá azure-erőforrásokhoz.|
| [Egyéni tartománynevek](../active-directory/roles/permissions-reference.md#) |  Egyéni Azure AD-tartományokat csak rendszergazdai fiókokhoz használhat. | [A felhasználói fiókok](user-overview.md#consumer-user) bejelentkezhet felhasználónévvel, telefonszámmal vagy bármilyen e-mail-címmel. Az átirányítási [URL-címekben](custom-domain.md) egyéni tartományokat is használhat.|
| [Feltételes hozzáférés](../active-directory/roles/permissions-reference.md#) | Teljes körűen támogatott a rendszergazdai és felhasználói fiókok esetében. | Az Azure AD feltételes hozzáférési funkcióinak [](user-overview.md#consumer-user) egy része támogatott a fogyasztói fiókok esetében, de az egyéni tartomány Azure AD B2C [meg.](conditional-access-user-flow.md)|

## <a name="other-azure-resources-in-your-tenant"></a>Egyéb Azure-erőforrások a bérlőben

A Azure AD B2C nem létesíthet más Azure-erőforrásokat, például virtuális gépeket, Azure-webalkalmazásokat vagy Azure Functions-függvényeket. Ezeket az erőforrásokat az Azure AD-bérlőben kell létrehoznia.

## <a name="azure-ad-b2c-accounts-overview"></a>Azure AD B2C fiókok áttekintése

A következő típusú fiókokat lehet létrehozni egy Azure AD B2C bérlőben:

Egy Azure AD B2C bérlőben több fióktípus is létezik, amelyek a felhasználói fiókok áttekintését ismertető cikkben Azure Active Directory B2C [meg.](user-overview.md)

- **Munkahelyi fiók** – A munkahelyi fiókok hozzáférhetnek a bérlők erőforrásaihoz, rendszergazdai szerepkörük pedig felügyelheti a bérlőket.
- **Vendégfiók** – A vendégfiókok csak Microsoft-fiók vagy Azure Active Directory, amely alkalmazások elérésére vagy bérlők kezelésére használható.
- **Fogyasztói fiók** – A felhasználói fiókot a fiókkal regisztrált alkalmazások felhasználója Azure AD B2C.

További információ ezekről a fióktípusokról: A felhasználói fiókok [áttekintése a Azure Active Directory B2C.](user-overview.md) Minden olyan felhasználónak, aki a Azure AD B2C-bérlő kezeléséhez lesz hozzárendelve, Azure AD felhasználói fiókkal kell rendelkezik, hogy hozzáférjen az Azure-hoz kapcsolódó szolgáltatásokhoz. Ilyen felhasználót úgy adhat hozzá, hogy létrehoz egy fiókot [(munkahelyi](#add-an-administrator-work-account) fiókot) [](#invite-an-administrator-guest-account) a Azure AD B2C-bérlőben, vagy meghívja őket a Azure AD B2C bérlőjébe vendégfelhasználóként.

## <a name="use-roles-to-control-resource-access"></a>Az erőforrás-hozzáférés vezérlése szerepkörökkel

A hozzáférés-vezérlési stratégia megtervezésekor a legjobb, ha a felhasználókhoz a legkevésbé kiemelt szerepkört rendeli hozzá az erőforrások eléréséhez. Az alábbi táblázat a bérlő elsődleges erőforrásait Azure AD B2C és a kezelésükhöz legmegfelelőbb rendszergazdai szerepköröket ismerteti.

|Erőforrás  |Leírás  |Szerepkör  |
|---------|---------|---------|
|[Alkalmazásregisztrációk](tutorial-register-applications.md) | A webes, mobil- és natív alkalmazásregisztrációk minden aspektusát létrehozhatja és kezelheti a Azure AD B2C.|[Alkalmazás-rendszergazda](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Identitásszolgáltatók](add-identity-provider.md)| Konfigurálja [a helyi identitásszolgáltatót](identity-provider-local.md) és a külső közösségi vagy vállalati identitásszolgáltatókat. | [Külső identitásszolgáltató rendszergazdája](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[API-összekötők](add-api-connector.md)| A felhasználói folyamatok webes API-okkal való integrálásával testre szabhatja a felhasználói élményt, és külső rendszerekkel integrálhatja azt.|[Külső azonosító felhasználói folyamatának attribútum-rendszergazdája](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Vállalati védjegyezés](customize-ui.md#configure-company-branding)| Felhasználói folyamatoldalak testreszabása.| [Globális rendszergazda](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Felhasználói attribútumok](user-flow-custom-attributes.md)| Az összes felhasználói folyamat számára elérhető egyéni attribútumok hozzáadása vagy törlése.| [Külső azonosító felhasználói folyamatának attribútum-rendszergazdája](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|Felhasználók kezelése| Az [ebben a cikkben](manage-users-portal.md) leírtak szerint kezelheti a fogyasztói fiókokat és a rendszergazdai fiókokat.| [Felhasználói rendszergazda](../active-directory/roles/permissions-reference.md#user-administrator)|
|Szerepkörök és rendszergazdák| Szerepkör-hozzárendelések kezelése a Azure AD B2C címtárban. Hozzon létre és kezeljen olyan csoportokat, amelyek hozzárendelhetőek Azure AD B2C szerepkörökhöz. |[Globális rendszergazda,](../active-directory/roles/permissions-reference.md#global-administrator) [kiemelt szerepkör rendszergazdája](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[Felhasználói folyamatok](user-flow-overview.md)|Az olyan gyakori identitásfeladatok gyors konfigurálása és engedélyezése, mint a regisztráció, a bejelentkezés és a profilszerkesztés.| [Külső azonosító felhasználói folyamatának attribútum-rendszergazdája](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Egyéni szabályzatok](user-flow-overview.md)| Az összes egyéni szabályzat létrehozása, olvasása, frissítése és törlése a Azure AD B2C.| [B2C IEF-házirend-rendszergazda](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[Szabályzatkulcsok](policy-keys-overview.md)|Titkosítási kulcsok hozzáadása és kezelése az egyéni házirendek által használt jogkivonatok, titkos ügyfélkulcsok, tanúsítványok és jelszavak aláírásához és hitelesítéséhez.|[B2C IEF kulcskészlet-rendszergazda](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|


## <a name="add-an-administrator-work-account"></a>Rendszergazda hozzáadása (munkahelyi fiók)

Új rendszergazdai fiók létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) rendszergazdai vagy kiemelt szerepkör-rendszergazdai engedélyekkel.
1. Válassza ki **a Címtár és előfizetés** szűrőt a felső menüben, majd válassza ki azt a címtárat, amely Azure AD B2C bérlőt.
1. Az **Azure-szolgáltatások alatt** válassza a **Azure AD B2C** lehetőséget. Vagy a keresőmezővel megkeresheti és kiválaszthatja a **Azure AD B2C.**
1. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
1. Válassza **az Új felhasználó lehetőséget.**
1. A Felhasználó **lapon** adja meg a felhasználó adatait:

   - **Név**. Kötelező. Az új felhasználó vezeték- és vezetékneve. Például *MaryRel.*
   - **Felhasználónév:**. Kötelező. Az új felhasználó felhasználóneve. Például: `mary@contoso.com`.
     A felhasználónév tartomány részének a kezdeti alapértelmezett tartománynevet *\<yourdomainname> (.onmicrosoft.com) kell használnia.*
   - **Groups (Csoportok).** Másik lehetőségként hozzáadhatja a felhasználót egy vagy több meglévő csoporthoz. A felhasználót később csoportokhoz is hozzáadhatja. 
   - **Címtárbeli** szerepkör: Ha rendszergazdai Azure AD-engedélyekre van szüksége a felhasználó számára, hozzáadhatja őket egy Azure AD-szerepkörhöz. Hozzárendelheti a felhasználót egy globális rendszergazda vagy egy vagy több korlátozott rendszergazdai szerepkörhöz az Azure AD-ban. További információ a szerepkörök hozzárendelésről: Szerepkörök használata [az erőforrás-hozzáférés vezérlése érdekében.](#use-roles-to-control-resource-access)
   - **Feladat adatai:** Itt vagy később is hozzáadhat további információkat a felhasználóról. 

1. Másolja ki a Jelszó mezőben megadott automatikusan **létrehozott** jelszót. Ezt a jelszót meg kell adnia a felhasználónak az első bejelentkezéshez.
1. Válassza a **Létrehozás** lehetőséget.

A rendszer létrehoz egy felhasználót, és hozzáadja a Azure AD B2C bérlőhöz. Előnyösebb, ha az ön bérlőjéhez legalább egy natív munkahelyi Azure AD B2C globális rendszergazdai szerepkört rendel. Ez a fiók break-glass fióknak tekinthető.

## <a name="invite-an-administrator-guest-account"></a>Rendszergazda meghívása (vendégfiók)

Új vendégfelhasználót is meghívhat a bérlő kezeléséhez. A vendégfiók az előnyben részesített lehetőség, ha a szervezet az Azure AD-t is használja, mert az identitás életciklusa külsőleg is kezelhető. 

Felhasználó meghíváshoz kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) rendszergazdai vagy kiemelt szerepkör-rendszergazdai engedélyekkel.
1. Válassza ki **a címtár és előfizetés** szűrőt a felső menüben, majd válassza ki azt a címtárat, amely Azure AD B2C bérlőt.
1. Az **Azure-szolgáltatások alatt** válassza a **Azure AD B2C** lehetőséget. Vagy a keresőmezővel megkeresheti és kiválaszthatja a **Azure AD B2C.**
1. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
1. Válassza **az Új vendégfiók lehetőséget.**
1. A Felhasználó **lapon** adja meg a felhasználó adatait:

   - **Név**. Kötelező. Az új felhasználó vezeték- és vezetékneve. Például *MaryRel.*
   - **E-mail-cím:**. Kötelező. A meghívott felhasználó e-mail-címe. Például: `mary@contoso.com`.   
   - **Személyes üzenet:** Személyes üzenetet ad hozzá, amely szerepelni fog a meghívó e-mailben.
   - **Groups (Csoportok).** Másik lehetőségként hozzáadhatja a felhasználót egy vagy több meglévő csoporthoz. A felhasználót később csoportokhoz is hozzáadhatja.
   - **Címtárbeli** szerepkör: Ha rendszergazdai Azure AD-engedélyekre van szüksége a felhasználó számára, hozzáadhatja őket egy Azure AD-szerepkörhöz. A felhasználót hozzárendelheti egy globális rendszergazda vagy egy vagy több korlátozott rendszergazdai szerepkörhöz az Azure AD-ban. További információ a szerepkörök hozzárendelésről: [Szerepkörök használata az erőforrás-hozzáférés vezérlése érdekében.](#use-roles-to-control-resource-access)
   - **Feladat adatai:** Itt vagy később is hozzáadhat további információkat a felhasználóról.

1. Válassza a **Létrehozás** lehetőséget.

A rendszer meghívó e-mailt küld a felhasználónak. A bejelentkezéshez a felhasználónak el kell fogadnia a meghívót.

### <a name="resend-the-invitation-email"></a>Meghívó e-mail újraküldése

Ha a vendég nem kapja meg a meghívó e-mailt, vagy a meghívó lejárt, újraküldheti a meghívót. A meghívó e-mail alternatívájaként közvetlen hivatkozást is adhat a vendégnek a meghívás elfogadásához. A meghívó újraküldése és a közvetlen hivatkozás lekérése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki **a címtár és előfizetés** szűrőt a felső menüben, majd válassza ki azt a címtárat, amely Azure AD B2C bérlőt.
1. Az **Azure-szolgáltatások alatt** válassza a **Azure AD B2C** lehetőséget. Vagy a keresőmezővel megkeresheti és kiválaszthatja a **Azure AD B2C.**
1. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
1. Keresse meg és válassza ki azt a felhasználót, aki számára újra el szeretné küldeni a meghívót.
1. A **Felhasználói | A Profil** lap Identitás **lapján** válassza a **(Kezelés) lehetőséget.**
    
    ![A vendégfiók meghívó e-mail-címének újraküldését bemutató képernyőkép.](./media/tenant-management/guest-account-resend-invite.png)

1. A **Meghívó újraküldése? beállításhoz** válassza az **Igen lehetőséget.** Ha **biztosan újra szeretne küldeni** egy meghívót? üzenet jelenik meg, válassza az **Igen lehetőséget.**
1. Azure AD B2C elküldi a meghívót. A meghívó URL-címét ki is másolhatja, és közvetlenül meg is használhatja a vendégnek.
    
    ![A meghívó URL-címének lekérését bemutató képernyőkép.](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Hozzárendelhet egy szerepkört, amikor létrehoz [egy felhasználót, vagy](#add-an-administrator-work-account) meghív [egy vendégfelhasználót.](#invite-an-administrator-guest-account) Hozzáadhat egy szerepkört, módosíthatja a szerepkört, vagy eltávolíthat egy szerepkört egy felhasználótól:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) rendszergazdai vagy kiemelt szerepkör-rendszergazdai engedélyekkel.
1. Válassza ki **a címtár és előfizetés** szűrőt a felső menüben, majd válassza ki azt a címtárat, amely Azure AD B2C bérlőt.
1. Az **Azure-szolgáltatások alatt** válassza a **Azure AD B2C** lehetőséget. Vagy a keresőmezővel megkeresheti és kiválaszthatja a **Azure AD B2C.**
1. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
1. Válassza ki azt a felhasználót, aki szerepköreit módosítani szeretné. Ezután válassza a **Hozzárendelt szerepkörök lehetőséget.**
1. Válassza **a Hozzárendelések hozzáadása** lehetőséget, válassza ki a hozzárendelni kívánt szerepkört (például alkalmazás-rendszergazda ), majd válassza a Hozzáadás **lehetőséget.** 

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

Ha el kell távolítania egy szerepkör-hozzárendelést egy felhasználótól, kövesse az alábbi lépéseket:

1. Válassza **Azure AD B2C** lehetőséget, válassza a **Felhasználók lehetőséget,** majd keresse meg és válassza ki a felhasználót.
1. Válassza a **Hozzárendelt szerepkörök lehetőséget.** Válassza ki az eltávolítani kívánt szerepkört, például *alkalmazás-rendszergazda,* majd válassza a **Hozzárendelés eltávolítása lehetőséget.**

## <a name="review-administrator-account-role-assignments"></a>Rendszergazdai fiókok szerepkör-hozzárendelésének áttekintése

A naplózási folyamat részeként általában áttekinti, hogy mely felhasználók vannak hozzárendelve a címtár adott Azure AD B2C számára. A következő lépésekkel naplót kap, hogy mely felhasználókhoz van jelenleg hozzárendelve kiemelt szerepkör.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) rendszergazdai vagy kiemelt szerepkör-rendszergazdai engedélyekkel.
1. Válassza ki **a címtár és előfizetés** szűrőt a felső menüben, majd válassza ki azt a címtárat, amely Azure AD B2C bérlőt.
1. Az **Azure-szolgáltatások alatt** válassza a **Azure AD B2C** lehetőséget. Vagy a keresőmezővel megkeresheti és kiválaszthatja a **Azure AD B2C.**
1. A **Kezelés alatt** válassza a Szerepkörök és rendszergazdák **lehetőséget.**
1. Válasszon ki egy szerepkört, például **globális rendszergazda.** A **szerepkör | A Hozzárendelések** lap az adott szerepkörhöz hozzárendelt felhasználókat sorolja fel.

## <a name="delete-an-administrator-account"></a>Rendszergazdai fiók törlése

Meglévő felhasználó törléséhez szerepkör-hozzárendelési *globális rendszergazda* kell lennie. A globális rendszergazdák bármely felhasználót törölhetik, beleértve a többi rendszergazdát is. *A felhasználói rendszergazdák* bármely nem rendszergazdai felhasználót törölhet.

1. A címtárban Azure AD B2C válassza a **Felhasználók** lehetőséget, majd válassza ki a törölni kívánt felhasználót.
1. Válassza **a Törlés,** majd az **Igen** lehetőséget a törlés megerősítéséhez.

A rendszer törli a felhasználót, és többé nem jelenik meg a **Felhasználók – Minden felhasználó lapon.** A felhasználó a következő **30** napra látható a Törölt felhasználók lapon, és ez idő alatt visszaállítható. A felhasználók visszaállításával kapcsolatos további információkért lásd: Nemrég törölt felhasználó visszaállítása vagy [eltávolítása a Azure Active Directory.](../active-directory/fundamentals/active-directory-users-restore.md)

## <a name="protect-administrative-accounts"></a>Rendszergazdai fiókok védelme

A nagyobb biztonság érdekében javasoljuk, hogy az összes rendszergazdai fiókot többtényezős hitelesítéssel (MFA) védje. Az MFA egy személyazonosság-ellenőrzési folyamat a bejelentkezés során, amely az azonosítás egy újabb formáját kéri a felhasználótól, például egy ellenőrző kódot a mobileszközén, vagy egy kérést a Microsoft Authenticator alkalmazásában.

![A bejelentkezési képernyőképen használt hitelesítési módszerek](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

Engedélyezheti az [Azure AD alapértelmezett biztonsági beállítását,](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) hogy minden rendszergazdai fiók MFA-t használjon.



## <a name="next-steps"></a>Következő lépések

- [Azure Active Directory B2C-bérlő létrehozása az Azure Portalon](tutorial-create-tenant.md)

