---
title: Oktatóanyag – Azure Active Directory B2C konfigurálása a Zscaler
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálhatja Azure AD B2C hitelesítését a Zscaler.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: dbeb3cd4fccf80f434e6c7ac08c658632f64b135
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096853"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>Oktatóanyag: a Zscaler privát hozzáférésének konfigurálása Azure Active Directory B2C

Ebből az oktatóanyagból megtudhatja, hogyan integrálható Azure Active Directory B2C (Azure AD B2C) hitelesítés a [Zscaler Private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access)használatával. A ZPA házirend-alapú, biztonságos hozzáférést biztosít a privát alkalmazásokhoz és eszközökhöz a virtuális magánhálózat (VPN) költséghatékony, problémamentes vagy biztonsági kockázata nélkül. A Zscaler biztonságos hibrid hozzáférési ajánlata nulla támadási felületet tesz lehetővé a felhasználók felé irányuló alkalmazások számára, ha az Azure AD B2C kombinálva van.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következőkre lesz szüksége:

- Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.  
- [Egy Azure ad B2C bérlő](./tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.  
- [Egy ZPA-előfizetés](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview).

## <a name="scenario-description"></a>Forgatókönyv leírása

A ZPA-integráció a következő összetevőket tartalmazza:

- **Azure ad B2C**: a felhasználó hitelesítő adatainak ellenőrzéséhez felelős identitásszolgáltató. Egy új felhasználó regisztrálásához is felelős.  
- **ZPA**: a webalkalmazás biztonságossá tételére szolgáló szolgáltatás, amely a [megbízhatósági hozzáférés](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.)kikényszerítésével gondoskodik.  
- **A webalkalmazás**: az a szolgáltatás, amelyet a felhasználó megpróbál elérni.

Az alábbi ábra azt mutatja be, hogyan integrálható a ZPA a Azure AD B2Cokkal.

![A Zscaler architektúra ábrája, amely bemutatja, hogyan integrálható a ZPA a Azure AD B2Cával.](media/partner-zscaler/zscaler-architecture-diagram.png)

A sorozatot a következő táblázat ismerteti:

|Lépés | Leírás |
| :-----:| :-----------|
| 1 | A felhasználó egy ZPA felhasználói portálon vagy egy ZPA Browser-Access alkalmazásban érkezik meg.
| 2 | A ZPA megköveteli a felhasználói környezet információit, mielőtt eldöntheti, hogy engedélyezi-e a felhasználó számára a webalkalmazás elérését. A felhasználó hitelesítéséhez a ZPA SAML-átirányítást végez a Azure AD B2C bejelentkezési oldalára.  
| 3 | A felhasználó megérkezik az Azure AB B2C bejelentkezési oldalára. Új felhasználók regisztrálhatnak a fiók létrehozására, és a meglévő felhasználók bejelentkeznek a meglévő hitelesítő adataikkal. Azure AD B2C érvényesíti a felhasználó identitását.
| 4 | A sikeres hitelesítés után Azure AD B2C átirányítja a felhasználót a ZPA-re az SAML-kijelentéssel együtt. A ZPA ellenőrzi az SAML-kijelentést, és beállítja a felhasználói környezetet.
| 5 | A ZPA kiértékeli a felhasználó hozzáférési szabályzatait. Ha a felhasználó hozzáférhet a webalkalmazáshoz, a kapcsolat továbbítása engedélyezett.

## <a name="onboard-to-zpa"></a>Beléptetés a ZPA

Ez az oktatóanyag feltételezi, hogy már rendelkezik egy működő ZPA-telepítővel. Ha megkezdi a ZPA-t, tekintse meg a [ZPA részletes konfigurációs útmutatóját](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-zpa-with-azure-ad-b2c"></a>A ZPA integrálása Azure AD B2C

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>1. lépés: Azure AD B2C konfigurálása identitásszolgáltató a ZPA-ben

Ha Azure AD B2C identitásszolgáltató szeretné konfigurálni [a ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign), tegye a következőket:

1. Jelentkezzen be a [ZPA felügyeleti portálra](https://admin.private.zscaler.com).

1. Nyissa meg az **Adminisztráció**  >  **identitásszolgáltató konfigurációját**.

1. Válassza a **identitásszolgáltató-konfiguráció hozzáadása** elemet.

   Megnyílik az **identitásszolgáltató-konfiguráció hozzáadása** panel.

   ![Képernyőkép a "identitásszolgáltató-konfiguráció hozzáadása" panel "identitásszolgáltató Information" (információk megadása) lapján.](media/partner-zscaler/add-idp-configuration.png)

1. Válassza a **identitásszolgáltató információ** fület, majd tegye a következőket:

   a. A **név** mezőbe írja be a **Azure ad B2C**.  
   b. Az **egyszeri bejelentkezés** területen válassza a **felhasználó** lehetőséget.  
   c. A **tartományok** legördülő listában válassza ki azokat a hitelesítési tartományokat, amelyeket hozzá szeretne rendelni ehhez a identitásszolgáltató.

1. Kattintson a **Tovább** gombra.

1. Válassza az **SP-metaadatok** fület, majd tegye a következőket:

   a. A **szolgáltató URL-címe** alatt másolja vagy jegyezze fel az értéket későbbi használatra.  
   b. A **szolgáltatói entitás azonosítója** területen másolja vagy jegyezze fel az értéket későbbi használatra.

   ![Képernyőkép a "identitásszolgáltató-konfiguráció hozzáadása" panel "SP-metaadatok" lapjáról.](media/partner-zscaler/sp-metadata.png)

1. Válassza a **szüneteltetés** lehetőséget.

A Azure AD B2C konfigurálása után a identitásszolgáltató többi konfigurációja folytatódik.

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>2. lépés: egyéni szabályzatok konfigurálása a Azure AD B2Cban

>[!Note]
>Ez a lépés csak akkor szükséges, ha még nem konfigurált egyéni házirendeket. Ha már rendelkezik egy vagy több egyéni házirenddel, akkor kihagyhatja ezt a lépést.

Ha egyéni házirendeket szeretne konfigurálni a Azure AD B2C bérlőn, tekintse meg az [Egyéni szabályzatok beszerzése a Azure Active Directory B2C](./custom-policy-get-started.md)című témakört.

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>3. lépés: a ZPA SAML-alkalmazásként való regisztrálása Azure AD B2C

Az SAML-alkalmazások Azure AD B2C-ben való konfigurálásával kapcsolatban lásd: [SAML-alkalmazás regisztrálása Azure ad B2Cban](./saml-service-provider.md). 

A ["szabályzat feltöltése"](./saml-service-provider.md#upload-your-policy)lépésben másolja vagy jegyezze fel a IDENTITÁSSZOLGÁLTATÓ SAML-metaadatokat, amelyet a Azure ad B2C használ. Erre később még szüksége lesz.

Kövesse az ["alkalmazás konfigurálása Azure ad B2Cban"](./saml-service-provider.md#configure-your-application-in-azure-ad-b2c)című lépés útmutatását. Az 4,2. lépésben frissítse az alkalmazás jegyzékfájljának tulajdonságait a következőképpen:

- **IdentifierUris** esetében: használja az "1.6. b" lépésben korábban másolt vagy feljegyzett szolgáltatói entitás azonosítóját.  
- A **samlMetadataUrl** esetében: hagyja ki ezt a tulajdonságot, mert a ZPA nem tartalmaz SAML-metaadatok URL-címét.  
- **ReplyUrlsWithType** esetében: használja az "1.6. a" lépésben korábban másolt vagy feljegyzett szolgáltatói URL-címet.  
- A **logoutUrl** esetében: hagyja ki ezt a tulajdonságot, mert a ZPA nem támogatja a kijelentkezési URL-címet.

A többi lépés nem vonatkozik erre az oktatóanyagra.

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>4. lépés: a identitásszolgáltató SAML-metaadatok kibontása Azure AD B2Cból

Ezután be kell szereznie egy SAML-metaadatok URL-címét a következő formátumban:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

Vegye figyelembe, hogy a `<tenant-name>` Azure ad B2C bérlő neve, és az az `<policy-name>` Egyéni SAML-szabályzat neve, amelyet az előző lépésben hozott létre.

Az URL-cím lehet például a következő: `https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata` .

Nyisson meg egy webböngészőt, és lépjen az SAML-metaadatok URL-címére. Kattintson a jobb gombbal az oldal bármely pontjára, válassza a **Mentés másként** lehetőséget, majd mentse a fájlt a számítógépre a következő lépésben való használatra.

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>5. lépés: a identitásszolgáltató-konfiguráció befejezése a ZPA

Fejezze be a [identitásszolgáltató-konfigurációt a ZPA felügyeleti portálon](https://help.zscaler.com/zpa/configuring-idp-single-sign) , amelyet korábban részben konfigurált az "1. lépés: az Azure ad B2C konfigurálása IDENTITÁSSZOLGÁLTATÓ a ZPA-ben" című témakörben.

1. A [ZPA felügyeleti portálján](https://admin.private.zscaler.com)lépjen a **felügyelet**  >  **identitásszolgáltató-konfiguráció** elemre.

1. Válassza ki az "1. lépés" területen konfigurált identitásszolgáltató, majd válassza a **Folytatás** lehetőséget.

1. A **identitásszolgáltató-konfiguráció hozzáadása** panelen válassza a **Létrehozás identitásszolgáltató** lapot, majd tegye a következőket:

   a. A **identitásszolgáltató metaadat-fájlja** alatt töltse fel a korábban mentett metaadat-fájlt a "4. lépés: a identitásszolgáltató SAML-metaadatok kibontása a következőből: Azure ad B2C".  
   b. Ellenőrizze, hogy az identitásszolgáltató-konfiguráció **állapota** **engedélyezve** van-e.  
   c. Válassza a **Mentés** lehetőséget.

   ![Képernyőkép az "identitásszolgáltató létrehozása" lapról az "identitásszolgáltató-konfiguráció hozzáadása" panelen.](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>A megoldás tesztelése

Nyissa meg a ZPA felhasználói portált vagy egy böngésző-hozzáférési alkalmazást, és tesztelje a regisztrálási vagy bejelentkezési folyamatot. A tesztnek sikeres SAML-hitelesítést kell eredményeznie.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](./custom-policy-get-started.md)
- [SAML-alkalmazás regisztrálása Azure AD B2C](./saml-service-provider.md)
- [Részletes konfigurációs útmutató a ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [Identitásszolgáltató konfigurálása egyszeri bejelentkezéshez](https://help.zscaler.com/zpa/configuring-idp-single-sign)