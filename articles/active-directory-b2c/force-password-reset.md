---
title: Kényszerített jelszó-visszaállítási folyamat konfigurálása Azure AD B2C
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan állíthat be kényszerített jelszó-visszaállítási folyamatot a Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0477153b466702bec0fa2d5221fee1e054d06314
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033766"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Kényszerített jelszó-visszaállítási folyamat beállítása Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Rendszergazdaként [alaphelyzetbe állíthatja a felhasználó jelszavát](manage-users-portal.md#reset-a-users-password) , ha a felhasználó elfelejti a jelszavát. Vagy szeretné kényszeríteni őket a jelszó alaphelyzetbe állításához. Ebből a cikkből megtudhatja, hogyan kényszerítheti a jelszó-visszaállítást ezekben a forgatókönyvekben.

## <a name="overview"></a>Áttekintés

Amikor egy rendszergazda visszaállít egy felhasználó jelszavát a Azure Portalon keresztül, a [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) attribútum értéke a következőre van állítva: `true` .

A [bejelentkezési és a regisztrációs útvonal](add-sign-up-and-sign-in-policy.md) ellenőrzi ennek az attribútumnak az értékét. Miután a felhasználó befejezte a bejelentkezést, ha az attribútum be van állítva `true` , a felhasználónak vissza kell állítania a jelszavát. Ezután az attribútum értéke a Back (vissza) értékre van állítva `false` .

![Jelszó-visszaállítási folyamat kényszerítése](./media/force-password-reset/force-password-reset-flow.png)

A jelszó-visszaállítási folyamat az Azure AD B2C helyi fiókjaira vonatkozik, amelyek [e-mail-címet](identity-provider-local.md#email-sign-in) vagy [felhasználónevet](identity-provider-local.md#username-sign-in) használnak a bejelentkezéshez jelszóval.

### <a name="force-a-password-reset-after-90-days"></a>Jelszó alaphelyzetbe állításának kényszerítése 90 nap után

Rendszergazdaként beállíthatja, hogy a felhasználó jelszava 90 napra legyen leállítva az [MS Graph](microsoft-graph-operations.md)használatával. 90 nap után a rendszer automatikusan beállítja a [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) attribútum értékét `true` . A felhasználó jelszavának lejárati házirendjének beállításával kapcsolatos további információkért lásd: [jelszóházirend attribútum](user-profile-attributes.md#password-policy-attribute).

Miután beállította a jelszó-elévülési szabályzatot, a jelen cikkben leírtak szerint konfigurálnia kell a kényszerített jelszó-visszaállítási folyamatot is.  

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-policy"></a>A házirend konfigurálása

::: zone pivot="b2c-user-flow"

A **kényszerített jelszó-visszaállítási** beállítás engedélyezése a regisztrálási vagy bejelentkezési felhasználói folyamatokban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **felhasználói folyamatok** lehetőséget.
1. Válassza ki a testreszabni kívánt regisztrációt és bejelentkezést, illetve a bejelentkezési felhasználói folyamatot (az **ajánlott** típus).
1. A **Beállítások** menü bal oldali menüjében válassza a **Tulajdonságok** lehetőséget.
1. A **jelszó bonyolultsága** területen válassza a **kényszerített jelszó-visszaállítás** lehetőséget.
1. Kattintson a **Mentés** gombra.

### <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) felhasználói rendszergazdaként vagy jelszó-rendszergazdaként. További információ az elérhető szerepkörökről: [rendszergazdai szerepkörök Kiosztása Azure Active Directory-ben](../active-directory/roles/permissions-reference.md#all-roles).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Felhasználók** lehetőséget. Keresse meg és válassza ki a jelszó-visszaállítás teszteléséhez használni kívánt felhasználót, majd válassza a **jelszó alaphelyzetbe állítása** lehetőséget.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **felhasználói folyamatok** lehetőséget.
1. Válassza ki a tesztelni kívánt regisztrációs vagy bejelentkezési felhasználói folyamatot (az **ajánlott** típus).
1. Válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Jelentkezzen be azzal a felhasználói fiókkal, amelynek a jelszavát alaphelyzetbe állítja.
1. Most meg kell változtatnia a felhasználó jelszavát. Módosítsa a jelszót, és válassza a **Folytatás** lehetőséget. A rendszer visszaküldi a tokent, `https://jwt.ms` és megjelenik Önnek.

::: zone-end

::: zone pivot="b2c-custom-policy"

1. Példa a jelszó kényszerített visszaállítására a [githubon](https://github.com/azure-ad-b2c/samples/tree/master/policies/force-password-reset).
1. Minden fájlban cserélje le a karakterláncot a `yourtenant` Azure ad B2C bérlő nevére. Ha például a B2C-bérlő neve *contosob2c*, az összes példánya `yourtenant.onmicrosoft.com` lesz `contosob2c.onmicrosoft.com` .
1. Töltse fel a házirend-fájlokat a következő sorrendben: a kiterjesztési szabályzat `TrustFrameworkExtensionsCustomForcePasswordReset.xml` , majd a függő entitás házirendje `SignUpOrSigninCustomForcePasswordReset.xml` .

### <a name="test-the-policy"></a>A szabályzat tesztelése

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) felhasználói rendszergazdaként vagy jelszó-rendszergazdaként. További információ az elérhető szerepkörökről: [rendszergazdai szerepkörök Kiosztása Azure Active Directory-ben](../active-directory/roles/permissions-reference.md#all-roles).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Felhasználók** lehetőséget. Keresse meg és válassza ki a jelszó-visszaállítás teszteléséhez használni kívánt felhasználót, majd válassza a **jelszó alaphelyzetbe állítása** lehetőséget.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. A **szabályzatok** területen válassza az **identitási élmény keretrendszere** elemet.
1. Válassza ki a `B2C_1A_signup_signin_Custom_ForcePasswordReset` szabályzatot a megnyitásához. 
1. **Alkalmazás** esetén válasszon ki egy [korábban regisztrált](troubleshoot-custom-policies.md#troubleshoot-the-runtime)webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **Futtatás most** gombra.
1. Jelentkezzen be azzal a felhasználói fiókkal, amelynek a jelszavát alaphelyzetbe állítja.
1. Most meg kell változtatnia a felhasználó jelszavát. Módosítsa a jelszót, és válassza a **Folytatás** lehetőséget. A rendszer visszaküldi a tokent, `https://jwt.ms` és megjelenik Önnek.

::: zone-end

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy önkiszolgáló [jelszó-visszaállítást](add-password-reset-policy.md).
