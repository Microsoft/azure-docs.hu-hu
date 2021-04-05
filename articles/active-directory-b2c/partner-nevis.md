---
title: Oktatóanyag a Azure Active Directory B2C és a Nevis konfigurálásához
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálhatja a Azure AD B2C hitelesítést a Nevis használatával a jelszóval nem rendelkező hitelesítéshez
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 282ec6a25dc381dc51f28534d272bae57d2e792e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98674993"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Útmutató a Nevis konfigurálásához a Azure Active Directory B2C a jelszóval nem rendelkező hitelesítéshez

Ebből az oktatóanyagból megtudhatja, hogyan terjesztheti ki a Azure AD B2Ct a  [Nevis](https://www.nevis.net/solution/authentication-cloud) használatával a jelszóval nem rendelkező hitelesítés engedélyezéséhez. A Nevis Mobile-First, teljes körűen márkás végfelhasználói élményt nyújt a Nevis-hozzáférési alkalmazással, amely erős ügyfél-hitelesítést biztosít, és megfelel a Payment Services direktíva 2 (PSD2) tranzakciós követelményeinek.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Egy Nevis [próbaverziós fiók](https://www.nevis-security.com/aadb2c/)

- Egy Azure AD-előfizetés. Ha még nem rendelkezik ilyennel, szerezzen be egy [ingyenes fiókot](https://azure.microsoft.com/free/).

- Egy [Azure ad B2C bérlő](./tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.

- Az [Egyéni házirendek](./custom-policy-get-started.md)használatára konfigurált Azure ad B2C környezet, ha a Nevis-t integrálni kívánja a regisztrációs házirend folyamatában.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az esetben adjon hozzá teljes értékű hozzáférési alkalmazást a háttérbeli alkalmazáshoz a jelszóval nem rendelkező hitelesítéshez. A következő összetevők alkotják a megoldást:

- Egy Azure AD B2C bérlő, amely egy kombinált bejelentkezési és regisztrációs szabályzattal rendelkezik a háttérrendszer számára
- Nevis-példány és a REST API a Azure AD B2C fokozása érdekében
- Saját márkás hozzáférési alkalmazás

A diagram a megvalósítást mutatja be.

![Magas szintű jelszó-bejelentkezési folyamat Azure AD B2C és Nevis](./media/partner-nevis/nevis-architecture-diagram.png)

|Lépés | Leírás |
|:-----| :-----------|
| 1. | A felhasználók Azure AD B2C bejelentkezési és regisztrációs szabályzattal próbálnak bejelentkezni vagy regisztrálni egy alkalmazásba.
| 2. | A regisztráció során a Nevis-hozzáférési alkalmazás egy QR-kóddal van regisztrálva a felhasználói eszközön. A rendszer a felhasználói eszközön létrehoz egy titkos kulcsot, amely a felhasználói kérések aláírására szolgál.
| 3. |  A Azure AD B2C REST-alapú technikai profilt használ a Nevis-megoldással való bejelentkezés elindításához.
| 4. | A bejelentkezési kérelmet a rendszer leküldéses üzenetként, QR-kóddal vagy mély hivatkozásként küldi el a hozzáférési alkalmazásnak.
| 5. | A felhasználó jóváhagyja a bejelentkezési kísérletet a biometrikus adatokkal. Ekkor a rendszer visszaküldi az üzenetet a Nevisnek, amely a tárolt nyilvános kulccsal ellenőrzi a bejelentkezést.
| 6. | Azure AD B2C egy utolsó kérelmet küld a Nevis-nek, hogy megbizonyosodjon róla, hogy a bejelentkezés sikeresen befejeződött.
| 7. |Azure AD B2C felhasználó sikeres/sikertelen üzenete alapján az alkalmazás hozzáférése/megtagadva.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>A Azure AD B2C-bérlő integrálása

### <a name="onboard-to-nevis"></a>Beléptetés a Nevisbe 

[Regisztráljon egy Nevis-fiókra](https://www.nevis-security.com/aadb2c/).
Két e-mailt fog kapni:

1. Felügyeleti fiók értesítése

2. Egy mobil alkalmazás meghívása.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Adja hozzá Azure AD B2C bérlőjét a Nevis-fiókjához

1. A Nevis felügyeleti fiók próbaverziós e-mail-címéből másolja a felügyeleti kulcsot a vágólapra.

2. Nyisson meg https://console.nevis.cloud/ egy böngészőben.

3. Jelentkezzen be a felügyeleti konzolra a kulccsal.

4. Válassza a **példány hozzáadása** lehetőséget

5. Válassza ki az újonnan létrehozott példányt a megnyitásához.

6. Az oldalsó navigációs sávon válassza az **Egyéni integrációk** lehetőséget.

7. Válassza az **Egyéni integráció hozzáadása** lehetőséget.

8. Az integráció neve mezőbe írja be a Azure AD B2C bérlő nevét.

9. URL/tartomány esetén adja meg a következőt: `https://yourtenant.onmicrosoft.com`

10. Kattintson a **Tovább** gombra.

>[!NOTE]
>A Nevis-hozzáférési tokent később kell megadnia.

11. Válassza a **Kész** lehetőséget.

### <a name="install-the-nevis-access-app-on-your-phone"></a>A Nevis-hozzáférési alkalmazás telepítése a telefonjára

1. A Nevis Mobile App próbaverziós e-mailben nyissa meg a **test Flight app** meghívását.

2. Telepítse az alkalmazást.

3. A Nevis-hozzáférési alkalmazás telepítéséhez kövesse a megadott utasításokat.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Azure AD B2C és Nevis integrálása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/).

2. Váltson a Azure AD B2C bérlőre. Győződjön meg arról, hogy a megfelelő bérlőt választotta, mivel a Azure AD B2C bérlő általában egy különálló bérlőn van.

3. A menüben válassza az **Identity Experience Framework (IEF) lehetőséget.**

4. **Házirend-kulcsok** kiválasztása

5. Válassza a **Hozzáadás** lehetőséget, és hozzon létre egy új kulcsot a következő beállításokkal:

      a. Válassza a **manuális** lehetőséget a beállításokban

      b. Név beállítása **AuthCloudAccessToken**

      c. Illessze be a korábban tárolt **Nevis hozzáférési tokent** a titkos mezőbe

      d. A kulcshasználat beállításnál válassza a **titkosítás** lehetőséget.

      e. Kattintson a **Létrehozás** elemre.

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>Az nevis.html konfigurálása és feltöltése az Azure Blob Storage-ba

1. A saját identitás környezetében (IDE) nyissa meg a [**házirend**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) mappát.

2. Nyissa meg a  [**nevis.html**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html) fájlt.

3. Cserélje le a  **authentication_cloud_urlt** a Nevis felügyeleti konzoljának URL-címére – `https://<instance_id>.mauth.nevis.cloud` .

4. **Mentse** a fájl módosításait.

5. Kövesse az [utasításokat](./customize-ui-with-html.md#2-create-an-azure-blob-storage-account) , és töltse fel az **nevis.html** -fájlt az Azure Blob Storage-ba.

6. Kövesse az [utasításokat](./customize-ui-with-html.md#3-configure-cors) , és engedélyezze a fájlhoz a több eredetű erőforrás-megosztást (CORS).

7. Miután a feltöltés befejeződött, és a CORS engedélyezve van, válassza ki a **nevis.html** fájlt a listában.

8. Az **Áttekintés** lapon, az **URL-cím** mellett válassza a **hivatkozás másolása** ikont.

9. Nyissa meg a hivatkozást egy új böngészőablakban, és győződjön meg róla, hogy egy szürke mező jelenik meg.

>[!NOTE]
>Később szüksége lesz a blob hivatkozására.

### <a name="customize-your-trustframeworkbasexml"></a>A TrustFrameworkBase.xml testreszabása

1. Az IDE-ben nyissa meg a [**házirend**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) mappát.

2. Nyissa meg a [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) fájlt.

3. Cserélje le az **yourtenant** -t az Azure-beli bérlői fiók nevére a **TenantId**.

4. Cserélje le a **yourtenant** -t az Azure-beli bérlői fiókjára a **PublicPolicyURI**-ben.

5. Az összes **authentication_cloud_url** példány cseréje a Nevis felügyeleti konzoljának URL-címére

6. **Mentse** a fájl módosításait.

### <a name="customize-your-trustframeworkextensionsxml"></a>A TrustFrameworkExtensions.xml testreszabása

1. Az IDE-ben nyissa meg a [**házirend**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) mappát.

2. Nyissa meg a [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) fájlt.

3. Cserélje le az **yourtenant** -t az Azure-beli bérlői fiók nevére a **TenantId**.

4. Cserélje le a **yourtenant** -t az Azure-beli bérlői fiókjára a **PublicPolicyURI**-ben.

5. A **BasePolicy** alatt, a **TenantId** a _yourtenant_ az Azure-beli bérlői fiók nevével is lecseréli.

6. A **BuildingBlocks** alatt cserélje le az **tartalomdefinícióban** -t a blob Storage-fiókjában lévő _nevis.html_ blob link URL-címére.

7. **Mentse** a fájlt.

### <a name="customize-your-signuporsigninxml"></a>A SignUpOrSignin.xml testreszabása

1. Az IDE-ben nyissa meg a [**házirend**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) mappát.

2. Nyissa meg a [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) fájlt.

3. Cserélje le az **yourtenant** -t az Azure-beli bérlői fiók nevére a **TenantId**.

4. Cserélje le a **yourtenant** -t az Azure-beli bérlői fiókjára a **PublicPolicyUri**-ben.

5. A **BasePolicy** alatt, a **TenantId**-ban az Azure-beli bérlői fiók nevével is lecseréli a **yourtenant** .

6. **Mentse** a fájlt.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Egyéni szabályzatok feltöltése a Azure AD B2Cba

1. Nyissa meg a [Azure ad B2C bérlő](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) kezdőlapját.

2. Válassza az **identitási élmény keretrendszert**.

3. Válassza az **egyéni házirend feltöltése** lehetőséget.

4. Válassza ki a módosított **TrustFrameworkBase.xml** fájlt.

5. Jelölje be az **egyéni házirend felülírása, ha már létezik** jelölőnégyzetet.
6. Válassza a **Feltöltés** lehetőséget.

7. **TrustFrameworkExtensions.xml** esetében ismételje meg az 5. és a 6. lépést.

8. **SignUpOrSignin.xml** esetében ismételje meg az 5. és a 6. lépést.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Fiók létrehozási és Nevis-hozzáférési alkalmazásának beállítása

1. Nyissa meg a [Azure ad B2C bérlő](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) kezdőlapját.

2. Válassza az **identitási élmény keretrendszert**.

3. Görgessen le az egyéni házirendekhez, és válassza a **B2C_1A_signup_signin** lehetőséget.

4. Válassza a **Futtatás most** lehetőséget.

5. Az előugró ablakban válassza a **regisztráció most** lehetőséget.

6. Adja meg az e-mail-címét.

7. Válassza az **ellenőrző kód küldése** lehetőséget.

8. Másolja át az ellenőrző kódot az e-mailben.

9. Válassza az **Ellenőrzés** lehetőséget.

10. Adja meg az űrlapot az új jelszóval és a megjelenítendő névvel.

11. Válassza a **Létrehozás** lehetőséget.

12. Ekkor megjelenik a QR-kód vizsgálata oldal.

13. A telefonján nyissa meg a **Nevis-hozzáférési alkalmazást**.

14. Válassza ki a **Face ID**-t.

15. Ha a képernyőn a **hitelesítő regisztrációja sikeres volt**, válassza a **Folytatás** lehetőséget.

16. A telefonján ismételje meg a hitelesítést.

17. Ekkor megjelenik a dekódolású jogkivonat részleteit megjelenítő [JWT.MS](https://jwt.ms) -Kezdőlap.

### <a name="test-the-pure-passwordless-sign-in"></a>A tiszta jelszó nélküli bejelentkezés tesztelése

1. Az **identitási élmény keretrendszere** területen válassza ki a **B2C_1A_signup_signin**.

2. Válassza a **Futtatás most** lehetőséget.

3. Az előugró ablakban válassza a **jelszó nélküli hitelesítés** lehetőséget.

4. Adja meg az e-mail-címét.

5. Válassza a **Folytatás** lehetőséget.

6. A telefonján az értesítések területen válassza a **Nevis-hozzáférési alkalmazás értesítés** lehetőséget.

7. Hitelesítés az arcával.

8. A rendszer automatikusan elvégzi a [JWT.MS](https://jwt.ms) kezdőlapján, amely megjeleníti a jogkivonatokat.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](./custom-policy-get-started.md?tabs=applications)