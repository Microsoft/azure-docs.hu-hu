---
title: Feltételes hozzáférés hozzáadása egy felhasználói folyamathoz Azure AD B2C
description: Megtudhatja, hogyan adhat hozzá feltételes hozzáférést a Azure AD B2C felhasználói folyamatokhoz. A többtényezős hitelesítési (MFA) beállítások és a feltételes hozzáférési házirendek konfigurálása a felhasználói folyamatokban a házirendek betartatása és a kockázatos bejelentkezések szervizelése érdekében.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 02/23/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e87899010660eac11166275bdfd61151bb12c10f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686937"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Feltételes hozzáférés hozzáadása a felhasználói folyamatokhoz Azure Active Directory B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

A feltételes hozzáférés felvehető a Azure Active Directory B2C felhasználói folyamataiba, hogy a kockázatos bejelentkezéseket az alkalmazásaihoz lehessen kezelni. Az Identitáskezelés és a feltételes hozzáférés Azure AD B2Cban való integrációja lehetővé teszi olyan házirendek beállítását, amelyek azonosítják a kockázatos bejelentkezési viselkedést, és olyan házirendeket kényszerítenek, amelyek további műveletet igényelnek a felhasználótól vagy a rendszergazdától. Ezek a Azure AD B2C felhasználói folyamatok feltételes hozzáférését lehetővé tevő összetevők:

- **Felhasználói folyamat**. Hozzon létre egy felhasználói folyamatot, amely végigvezeti a felhasználót a bejelentkezési és a regisztrációs folyamaton. A felhasználói folyamat beállításainál jelezze, hogy aktiválni kell-e a feltételes hozzáférési szabályzatokat, ha a felhasználó követi a felhasználói folyamatot.
- **Alkalmazás, amely a felhasználókat a felhasználói folyamatra irányítja**. Konfigurálja úgy az alkalmazást, hogy a felhasználók számára a megfelelő regisztrációs és bejelentkezési felhasználói folyamatra irányítsa a felhasználói folyamat végpontjának megadásával az alkalmazásban.
- **Feltételes hozzáférési szabályzat**. [Hozzon létre egy feltételes hozzáférési szabályzatot](conditional-access-identity-protection-setup.md) , és határozza meg azokat az alkalmazásokat, amelyekre alkalmazni szeretné a szabályzatot. Ha a felhasználó követi az alkalmazás bejelentkezési vagy regisztrációs folyamatát, a feltételes hozzáférési szabályzat Identity Protection-jeleket használ a kockázatos bejelentkezések azonosítására, és szükség esetén megadja a megfelelő szervizelési műveleteket.

A feltételes hozzáférés a felhasználói folyamatok legújabb verzióiban támogatott. Létrehozhat feltételes hozzáférési szabályzatokat az új felhasználói folyamatok létrehozásához, vagy hozzáadhatja őket a meglévő felhasználói folyamatokhoz, ha a verzió támogatja a feltételes hozzáférést. Ha feltételes hozzáférést ad hozzá egy meglévő felhasználói folyamathoz, két beállítást kell áttekintenie:

- **Többtényezős hitelesítés (MFA)**: a felhasználók mostantól egy egyszeri kódot is használhatnak SMS-ben vagy hangon, vagy egy egyszeri jelszót e-mailben a többtényezős hitelesítéshez. Az MFA-beállítások függetlenek a feltételes hozzáférési beállításoktól. Az MFA-t úgy állíthatja be, hogy **mindig** be legyen állítva, hogy az MFA mindig kötelező legyen a feltételes hozzáférés beállítástól függetlenül. Az MFA beállítható **úgy is, hogy az** MFA csak akkor legyen kötelező, ha az aktív feltételes hozzáférési szabályzat megköveteli.

- **Feltételes hozzáférés**: a beállításnak mindig **be kell jelentkeznie**. Ezt a beállítást általában a hibaelhárítás vagy az áttelepítés során, vagy az örökölt implementációk esetén kell **kikapcsolni** .

További információ az [Identity Protectionről és a feltételes hozzáférésről](conditional-access-identity-protection-overview.md) Azure ad B2Cban, illetve a [beállításáról](conditional-access-identity-protection-setup.md).

## <a name="prerequisites"></a>Előfeltételek

- A kockázatos bejelentkezési szabályzatok létrehozásához Azure AD B2C Premium 2 szükséges. A prémium P1-bérlők létrehozhatnak helyet, alkalmazást vagy csoporton alapuló házirendeket.
- Tesztelési célból [regisztrálhat](tutorial-register-applications.md) egy `https://jwt.ms` Microsoft tulajdonú webalkalmazást, amely egy jogkivonat dekódolású tartalmát jeleníti meg (a jogkivonat tartalma soha nem hagyja el a böngészőt). 
- Ha kockázatos bejelentkezést szeretne szimulálni, töltse le a TOR böngészőt, és próbálja meg bejelentkezni a felhasználói folyamat végpontba.
- A következő beállítások használatával [hozzon létre egy feltételes hozzáférési szabályzatot](conditional-access-identity-protection-setup.md):
   
  - **Felhasználók és csoportok** esetében válassza a felhasználó tesztelése lehetőséget (ne jelölje ki az **összes felhasználót** , vagy tiltsa le a bejelentkezést).
  - **Felhőalapú alkalmazások vagy műveletek** esetében válassza az **alkalmazások kiválasztása** lehetőséget, majd válassza ki a függő entitás alkalmazását.
  - A feltételek beállításnál válassza a **bejelentkezési kockázat** és a **magas**, **közepes** és **alacsony** kockázati szintek lehetőséget.
  - A **támogatás** mezőben válassza a **hozzáférés letiltása** lehetőséget.

      ![Kockázatészlelések](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

::: zone pivot="b2c-user-flow"

## <a name="add-conditional-access-to-a-new-user-flow"></a>Feltételes hozzáférés hozzáadása egy új felhasználói folyamathoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. A **házirendek** területen válassza a **felhasználói folyamatok** lehetőséget, majd válassza az **új felhasználói folyamat** elemet.
1. A **felhasználói folyamat létrehozása** lapon válassza ki a felhasználói folyamat típusát.
1. **A verzió kiválasztása** területen válassza az **ajánlott** lehetőséget, majd válassza a **Létrehozás** lehetőséget. ([További](user-flow-versions.md) információ a felhasználói folyamatok verzióiról.)

    ![Felhasználói folyamat létrehozása lap Azure Portal a tulajdonságok kiemelve](./media/tutorial-create-user-flows/select-version.png)

1. Adja meg a felhasználói folyamat **nevét** . Például: *signupsignin1*.
1. Az **Identity Providers** szakaszban válassza ki azokat az identitás-szolgáltatókat, amelyeket engedélyezni szeretne ehhez a felhasználói folyamathoz.
2. A **többtényezős hitelesítés** szakaszban válassza ki a kívánt **MFA-módszert**, majd az **MFA-kényszerítés** területen válassza a **feltételes (ajánlott)** lehetőséget.
 
   ![Többtényezős hitelesítés konfigurálása](media/conditional-access-user-flow/configure-mfa.png)

1. A **feltételes hozzáférés** szakaszban jelölje be a **feltételes hozzáférési házirendek betartatása** jelölőnégyzetet.

   ![Feltételes hozzáférési beállítások konfigurálása](media/conditional-access-user-flow/configure-conditional-access.png)

1. A **felhasználói attribútumok és jogcímek** szakaszban válassza ki azokat a jogcímeket és attribútumokat, amelyeket szeretne összegyűjteni, majd a felhasználótól a regisztráció során elküldeni. Válassza például a **továbbiak megjelenítése** lehetőséget, majd válassza az attribútumok és jogcímek lehetőséget az **ország/régió** és a **megjelenítendő név** mezőben. Válassza az **OK** lehetőséget.

    ![Attribútumok és jogcímek kiválasztása lap három jogcímek kiválasztásával](./media/conditional-access-user-flow/configure-user-attributes-claims.png)

1. A felhasználói folyamat hozzáadásához kattintson a **Létrehozás** gombra. A *B2C_1* előtagja automatikusan előtagértéke a nevet.

## <a name="add-conditional-access-to-an-existing-user-flow"></a>Feltételes hozzáférés hozzáadása meglévő felhasználói folyamathoz

> [!NOTE]
> A meglévő felhasználói folyamatnak olyan verziónak kell lennie, amely támogatja a feltételes hozzáférést. A felhasználói folyamatok ezen verziói **ajánlott** címkével rendelkeznek.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.

1. A **házirendek** területen válassza a **felhasználói folyamatok** elemet. Ezután válassza ki a felhasználói folyamatot.

1. Válassza a **Tulajdonságok** lehetőséget, és győződjön meg arról, hogy a felhasználói folyamat támogatja a feltételes hozzáférést a **Tulajdonságok** lehetőség kiválasztásával, és a **feltételes hozzáférés** feliratú beállítás keresésével.
 
   ![Az MFA és a feltételes hozzáférés konfigurálása a tulajdonságok között](media/conditional-access-user-flow/add-conditional-access.png)

1. A **többtényezős hitelesítés** szakaszban válassza ki a kívánt **MFA-módszert**, majd az **MFA-kényszerítés** területen válassza a **feltételes (ajánlott)** lehetőséget.
 
1. A **feltételes hozzáférés** szakaszban jelölje be a **feltételes hozzáférési házirendek betartatása** jelölőnégyzetet.

1. Kattintson a **Mentés** gombra.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

A felhasználói folyamat feltételes hozzáférésének teszteléséhez [hozzon létre egy feltételes hozzáférési szabályzatot](conditional-access-identity-protection-setup.md) , és engedélyezze a feltételes hozzáférést a felhasználói folyamatokban a fent leírtak szerint. 


### <a name="run-the-user-flow"></a>A felhasználói folyamat futtatása

1. Válassza ki a létrehozott felhasználói folyamatot az Áttekintés oldal megnyitásához, majd válassza a **felhasználói folyamat futtatása** lehetőséget. Az **alkalmazás** alatt válassza a *webapp1* lehetőséget. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .

   ![Felhasználói folyamat futtatása lap a portálon a felhasználói folyamat futtatása gomb kiemelve](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Másolja az URL-címet a **felhasználói folyamat végpontjának futtatása** alatt.

1. Ha kockázatos bejelentkezést szeretne szimulálni, nyissa meg a [Tor böngészőt](https://www.torproject.org/download/) , és használja az előnézet lépésben másolt URL-címet a regisztrált alkalmazásba való bejelentkezéshez.

1. Adja meg a kért információkat a bejelentkezési lapon, majd próbálja meg bejelentkezni. A rendszer visszaküldi a tokent, `https://jwt.ms` és megjelenik Önnek. A dekódolású jwt.ms-tokenben látnia kell, hogy a bejelentkezés le lett tiltva:

   ![Blokkolt bejelentkezés tesztelése](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Feltételes hozzáférés hozzáadása a Szabályzathoz

A [githubon](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access)megtalálhatja például a feltételes hozzáférési szabályzatot.

Azt is megtudhatja, hogyan [határozhat meg egy feltételes hozzáférési technikai profilt egy egyéni szabályzatban](conditional-access-technical-profile.md).

::: zone-end

## <a name="next-steps"></a>Következő lépések

[Felhasználói felület testreszabása Azure AD B2C felhasználói folyamatban](customize-ui-with-html.md)
