---
title: IDology-integráció Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálhat egy Azure AD B2Cos online fizetési alkalmazást a IDology-ben. A IDology egy identitás-ellenőrzési és-ellenőrző szolgáltató több megoldással.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b6001932b6f53a60eda76b6136611a10011391a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96928681"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>Oktatóanyag a IDology konfigurálásához a Azure Active Directory B2C 

Ebben a példában a Azure AD B2C és a [IDology](https://www.idology.com/solutions/)integrálását ismertető útmutató nyújt útmutatást. A IDology egy identitás-ellenőrzési és-ellenőrző szolgáltató több megoldással. Ebben a példában a IDology ExpectID-megoldását fogjuk lefedni.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* [Egy Azure ad B2C bérlő](tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.

## <a name="scenario-description"></a>Forgatókönyv leírása

A IDology-integráció a következő összetevőket tartalmazza:

- Azure AD B2C – a felhasználó hitelesítő adatainak ellenőrzéséhez felelős engedélyezési kiszolgáló. Más néven identitás-szolgáltató.
- IDology – a IDology szolgáltatás elvégzi a felhasználó által megadott bemenetet, és ellenőrzi a felhasználó identitását.
- Egyéni REST API – ez az API implementálja az Azure AD és a IDology szolgáltatás közötti integrációt.

A következő architektúra-diagram a megvalósítást mutatja be.

![IDology architektúra diagram](media/partner-idology/idology-architecture-diagram.png)

| Lépés | Leírás |
|------|------|
|1     | A felhasználó megérkezik a bejelentkezési oldalra. |
|2     | A felhasználó kiválasztja a regisztrációs lehetőséget egy új fiók létrehozásához, és beírja az adatokat az oldalra. A Azure AD B2C a felhasználói attribútumokat gyűjti. |
|3     | Azure AD B2C meghívja a középső rétegbeli API-t, és átadja a felhasználói attribútumokat. |
|4     | A középső rétegbeli API gyűjti a felhasználói attribútumokat, és átalakítja azokat olyan formátumra, amelyet a IDOlogy API képes használni. Ezután elküldi az adatokat a IDology. |
|5     | A IDology felhasználja az adatokat, és feldolgozza azokat, majd visszaadja az eredményt a középső rétegbeli API-nak. |
|6     | A középső rétegbeli API feldolgozza az adatokat, és visszaküldi a vonatkozó adatokat Azure AD B2Cra. |
|7     | Azure AD B2C adatokat fogad a középső rétegbeli API-ból. Ha **hibát jelez** , hibaüzenet jelenik meg a felhasználó számára. Ha **sikeres** választ mutat, a rendszer hitelesíti a felhasználót, és beírja a könyvtárba. |
|      |      |

> [!NOTE]
> A Azure AD B2C megkérheti az ügyfelet a lépésenkénti hitelesítés elvégzésére is, de ez a forgatókönyv az oktatóanyag hatókörén kívül esik.

## <a name="onboard-with-idology"></a>IDology

1. A IDology számos megoldást kínál, amelyeket [itt](https://www.idology.com/solutions/)talál. Ebben a példában a ExpectID-t használjuk.

2. IDology-fiók létrehozásához forduljon a [IDologyhoz](https://www.idology.com/request-a-demo/microsoft-integration-signup/).

3. A fiók létrehozása után megkapja az API-konfigurációhoz szükséges információkat. A következő szakaszok ismertetik a folyamatot.

## <a name="integrate-with-azure-ad-b2c"></a>Integrálás az Azure AD B2C szolgáltatással

### <a name="part-1---deploy-the-api"></a>1. rész – az API üzembe helyezése

A megadott [API-kód](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/Api) üzembe helyezése egy Azure-szolgáltatásban. A kód a Visual studióból is közzétehető, ezeket az [utasításokat](/visualstudio/deployment/quickstart-deploy-to-azure)követve.

Szüksége lesz az üzembe helyezett szolgáltatás URL-címére az Azure AD konfigurálásához a szükséges beállításokkal.

### <a name="part-2---configure-the-api"></a>2. rész – az API konfigurálása 

Az Alkalmazásbeállítások [konfigurálhatók app Service az Azure-ban](../app-service/configure-common.md#configure-app-settings). Ezzel a módszerrel a beállítások biztonságosan konfigurálhatók egy adattárba való ellenőrzés nélkül. A REST API-hoz a következő beállításokat kell megadnia:

| Alkalmazásbeállítások | Forrás | Jegyzetek |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | IDology-fiók konfigurálása |     |
|IdologySettings:ApiPassword | IDology-fiók konfigurálása |     |
|WebApiSettings:ApiUsername |Adja meg az API-hoz tartozó felhasználónevet| A ExtId-konfigurációban használatos |
|WebApiSettings:ApiPassword | Adja meg az API-hoz tartozó jelszót | A ExtId-konfigurációban használatos

### <a name="part-3---create-api-policy-keys"></a>3. rész – API-szabályzatok kulcsainak létrehozása

A következő [dokumentum](secure-rest-api.md#add-rest-api-username-and-password-policy-keys) használatával hozzon létre két szabályzatot: egyet az API-felhasználónévhez, egyet pedig a fent megadott API-jelszóhoz.

A minta szabályzat ezeket a neveket használja:

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>4. rész – a Azure AD B2C házirend konfigurálása

1. Ezt a [dokumentumot](custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) követve töltse le a [LocalAccounts Starter Pack csomagot](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) , és konfigurálja a Azure ad B2C bérlő házirendjét. Kövesse az utasításokat, amíg el nem végzi az **egyéni házirend tesztelése** szakaszt.

2. Töltse le a két minta szabályzatot [itt](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy).

3. Frissítse a két minta házirendet:

   1. Mindkét szabályzat megnyitása:

      1. A szakaszban `Idology-ExpectId-API` frissítse a `ServiceUrl` metaadatok elemét a fent üzembe helyezett API helyére.

      1. Cserélje le a helyére `yourtenant` a Azure ad B2C bérlő nevét.
      Ha például a Azure AD B2C bérlő neve  `contosotenant` , cserélje le a összes példányát a következőre:  `yourtenant.onmicrosoft.com`   `contosotenant.onmicrosoft.com` .

   1. Nyissa meg a TrustFrameworkExtensions.xml fájlt:

      1. Keresse meg az elemet  `<TechnicalProfile Id="login-NonInteractive">` . Cserélje le mindkét példányát a  `IdentityExperienceFrameworkAppId`   korábban létrehozott IdentityExperienceFramework alkalmazás alkalmazás-azonosítójával.

      1. Cserélje le mindkét példányát a  `ProxyIdentityExperienceFrameworkAppId`   korábban létrehozott ProxyIdentityExperienceFramework alkalmazás alkalmazás-azonosítójával.

4. Cserélje le a SignInorSignUp.xmlt, és TrustFrameworkExtensions.xml korábban az 1. lépésben feltöltött Azure AD B2Cre, a két frissített mintavételi házirenddel.

> [!NOTE]
> Ajánlott eljárásként Azt javasoljuk, hogy az ügyfelek az attribútumok gyűjteménye lapon vegyenek fel beleegyező értesítéseket. Értesítse a felhasználókat arról, hogy az adatokat a harmadik féltől származó szolgáltatásoknak személyazonosság-ellenőrzés céljából küldi el a rendszer.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a Azure AD B2C bérlőt, és a **házirendek** területen válassza a **felhasználói folyamatok** elemet.

2. Válassza ki a korábban létrehozott **felhasználói folyamatot**.

3. Válassza a **felhasználói folyamat futtatása** lehetőséget, és válassza ki a beállításokat:

   1. **Alkalmazás** – válassza ki a regisztrált alkalmazást (minta: JWT).

   1. **Válasz URL-címe** – válassza ki az **átirányítási URL-címet**.

   1. Válassza a **felhasználói folyamat futtatása** lehetőséget.

4. Ugorjon végig a regisztrációs folyamaton, és hozzon létre egy fiókot.

5. Jelentkezzen ki.

6. Ugorjon végig a bejelentkezési folyamaton.

7. A **Folytatás** gombra való belépés után a IDology puzzle jelenik meg.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](custom-policy-get-started.md?tabs=applications)
