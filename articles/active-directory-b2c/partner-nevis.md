---
title: Oktatóanyag a nevis Azure Active Directory B2C konfigurálásról
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan integrálhatja Azure AD B2C hitelesítést a Nevis használatával a jelszó nélküli hitelesítéshez
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 42e244249ecb0539637918ae2439bdb4f5da4b38
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588479"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Oktatóanyag: A Nevis konfigurálása Azure Active Directory B2C jelszó nélküli hitelesítéshez

Ebből a minta oktatóanyagból megtudhatja, hogyan bővítheti a Azure AD B2C  [a Nevis](https://www.nevis.net/en/solution/authentication-cloud) használatával a jelszó nélküli hitelesítés engedélyezéséhez. A Nevis mobileszközökre ható, teljesen márkanevű végfelhasználói élményt nyújt a Nevis Access alkalmazással, amely erős ügyfél-hitelesítést biztosít, és megfelel a Payment Services Directive 2 (PSD2) tranzakciós követelményeinek.

## <a name="prerequisites"></a>Előfeltételek

A első lépésekhez a következőre lesz szüksége:

- [Nevis-próbafiók](https://www.nevis-security.com/aadb2c/)

- Egy Azure AD-előfizetés. Ha még nincs fiókja, szerezze be az [ingyenes fiókot.](https://azure.microsoft.com/free/)

- Egy [Azure AD B2C,](./tutorial-create-tenant.md) amely az Azure-előfizetéséhez van kapcsolva.

- Egyéni szabályzatok Azure AD B2C konfigurálva, ha a Nevist integrálni szeretné a bejelentkezési szabályzat folyamatába. [](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben a forgatókönyvben teljes körűen védjegyes hozzáférést biztosít a háttéralkalmazáshoz a jelszó nélküli hitelesítéshez. A megoldást a következő összetevők teszik ki:

- Egy Azure AD B2C bérlő, a háttérbe való kombinált bejelentkezési és bejelentkezési szabályzatokkal
- A Nevis-példány és REST API a Azure AD B2C
- Saját védjegyes hozzáférési alkalmazása

Az ábrán az implementáció látható.

![Magas szintű jelszó-bejelentkezési folyamat Azure AD B2C És Nevis használatával](./media/partner-nevis/nevis-architecture-diagram.png)

|Lépés | Description |
|:-----| :-----------|
| 1. | A felhasználók bejelentkezési és bejelentkezési szabályzatokkal próbálnak Azure AD B2C bejelentkezni vagy bejelentkezni egy alkalmazásba.
| 2. | A regisztráció során a Nevis Access alkalmazás egy QR-kóddal regisztrálva lesz a felhasználói eszközön. A rendszer létrehoz egy titkos kulcsot a felhasználói eszközön, és a felhasználói kérések aláírásra használatos.
| 3. |  Azure AD B2C RESTful technikai profillal indítja el a bejelentkezést a Nevis megoldással.
| 4. | A bejelentkezési kérelem leküldéses üzenetként, QR-kódként vagy mélyhivatkozásként lesz elküldve a hozzáférési alkalmazásnak.
| 5. | A felhasználó jóváhagyja a bejelentkezési kísérletet a biometriával. A rendszer ezután egy üzenetet küld vissza a Nevisnek, amely a tárolt nyilvános kulccsal ellenőrzi a bejelentkezést.
| 6. | Azure AD B2C egy utolsó kérést küld a Nevisnek annak megerősítésére, hogy a bejelentkezés sikeresen befejeződött.
| 7. |A felhasználótól kapott sikeres/sikertelen Azure AD B2C a felhasználó hozzáférést kap vagy megtagadja az alkalmazáshoz.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Saját Azure AD B2C integrálása

### <a name="onboard-to-nevis"></a>A Nevisre való feleskedő 

[Regisztráljon egy Nevis-fiókot.](https://www.nevis-security.com/aadb2c/)
Két e-mailt fog kapni:

1. Felügyeleti fiók értesítése

2. Mobilalkalmazás-meghívó.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>A Azure AD B2C hozzáadása a Nevis-fiókhoz

1. A Nevis felügyeleti fiók próba e-mail-címében másolja a felügyeleti kulcsot a vágólapra.

2. Nyissa https://console.nevis.cloud/ meg a következőt egy böngészőben: .

3. Jelentkezzen be a felügyeleti konzolra a kulcsával.

4. Válassza **a Példány hozzáadása lehetőséget**

5. Válassza ki az újonnan létrehozott példányt a megnyitásához.

6. Az oldalsó navigációs sávon válassza az **Egyéni integrációk lehetőséget.**

7. Válassza **az Egyéni integráció hozzáadása lehetőséget.**

8. Az Integráció neve alatt adja meg Azure AD B2C bérlő nevét.

9. AZ URL/Tartomány mezőben adja meg a következőt: `https://yourtenant.onmicrosoft.com`

10. Kattintson a **Tovább** gombra.

>[!NOTE]
>Később szüksége lesz a Nevis hozzáférési jogkivonatra.

11. Válassza a **Kész** lehetőséget.

### <a name="install-the-nevis-access-app-on-your-phone"></a>A Nevis Access alkalmazás telepítése a telefonra

1. A Nevis mobilalkalmazás próbaverziós e-mail-címében nyissa meg a **Test Flight alkalmazás meghívóját.**

2. Telepítse az alkalmazást.

3. A Nevis Access alkalmazás telepítéséhez kövesse a megadott utasításokat.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Integráció Azure AD B2C Nevis-sel

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/).

2. Váltson a Azure AD B2C bérlőjéhez. Győződjön meg arról, hogy a megfelelő bérlőt választotta ki, mivel Azure AD B2C bérlő általában egy külön bérlőben található.

3. A menüben válassza a Identity Experience Framework **(IEF) lehetőséget**

4. **Szabályzatkulcsok kiválasztása**

5. Válassza **a Hozzáadás** lehetőséget, és hozzon létre egy új kulcsot a következő beállításokkal:

      a. A **Manuális lehetőség** kiválasztása a Beállítások között

      b. A Name (Név) beállítása **AuthCloudAccessToken (AuthCloudAccessToken)**

      c. Illessze be a korábban tárolt **Nevis hozzáférési jogkivonatot** a Secret (Titkos) mezőbe

      d. A Kulcshasználat beállításhoz válassza a Titkosítás **lehetőséget.**

      e. Kattintson a **Létrehozás** elemre.

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>Az azure-nevis.htmkonfigurálása és feltöltése az Azure Blob Storage-ba

1. Az identitáskörnyezetben (IDE) ugrás a [**szabályzat mappára.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Nyissa meg [**nevis.html fájlt.**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html)

3. Cserélje le  **a authentication_cloud_url** a Nevis felügyeleti konzol URL-címére – `https://<instance_id>.mauth.nevis.cloud` .

4. **Mentse** a fájl módosításait.

5. Kövesse az [utasításokat,](./customize-ui-with-html.md#2-create-an-azure-blob-storage-account) és töltse **nevis.html fájlt** az Azure Blob Storage-ba.

6. Kövesse az [utasításokat,](./customize-ui-with-html.md#3-configure-cors) és engedélyezze az eredetközi erőforrás-megosztást (CORS) ehhez a fájlhoz.

7. Ha a feltöltés befejeződött és a CORS engedélyezve van, válassza kinevis.htm **l** fájlt a listából.

8. Az Áttekintés **lap** URL-címe mellett válassza a hivatkozás  **másolása ikont.**

9. Nyissa meg a hivatkozást egy új böngészőlapon, és győződjön meg arról, hogy szürke mező jelenik meg.

>[!NOTE]
>Később szüksége lesz a blobhivatkozásra.

### <a name="customize-your-trustframeworkbasexml"></a>A TrustFrameworkBase.xml

1. Az IDE-ben kattintson a [**szabályzat mappára.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Nyissa meg [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) fájlt.

3. Cserélje **le a bérlőt** az Azure-bérlőfiók nevére a **TenantId (Bérlőazonosító) helyére.**

4. Cserélje **le a bérlőt** az Azure-bérlőfiók nevére a **PublicPolicyURI-ban.**

5. Cserélje le **authentication_cloud_url** összes új példányt a Nevis felügyeleti konzol URL-címére

6. **Mentse** a fájl módosításait.

### <a name="customize-your-trustframeworkextensionsxml"></a>A TrustFrameworkExtensions.xml

1. Az IDE-ben kattintson a [**szabályzat mappára.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Nyissa meg [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) fájlt.

3. Cserélje **le a bérlőt** az Azure-bérlőfiók nevére a **TenantId (Bérlőazonosító) helyére.**

4. Cserélje **le a bérlőt** az Azure-bérlőfiók nevére a **PublicPolicyURI-ban.**

5. A **BasePolicy alatt a**  **TenantId (Bérlőazonosító)** alatt cserélje le a bérlőt az Azure-bérlői fiók nevére.

6. A **BuildingBlocks (Építőblokkok)** alatt cserélje le a **LoadUri** helyére a blob-nevis.htm _blob-tárfiókban található blobhivatkozás_ URL-címét.

7. **Mentse a** fájlt.

### <a name="customize-your-signuporsigninxml"></a>A SignUpOrSignin.xml

1. Az IDE-ben kattintson a [**szabályzat mappára.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Nyissa meg [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) fájlt.

3. Cserélje **le a bérlőt** az Azure-bérlői fiók nevére a **TenantId (Bérlőazonosító) helyére.**

4. Cserélje **le a bérlőt** az Azure-bérlőfiók nevére a **PublicPolicyUri fájlban.**

5. A **BasePolicy alatt a**  **TenantId (Bérlőazonosító)** alatt cserélje le a bérlőt az Azure-bérlői fiók nevére.

6. **Mentse a** fájlt.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Egyéni szabályzatok feltöltése a Azure AD B2C

1. Nyissa meg Azure AD B2C [bérlője](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) kezdőlapját.

2. Válassza a **Identity Experience Framework** lehetőséget.

3. Válassza az **Egyéni szabályzat feltöltése lehetőséget.**

4. Válassza ki **TrustFrameworkBase.xml** módosított fájlját.

5. Jelölje be **az Egyéni szabályzat felülírása, ha már létezik jelölőnégyzetet.**
6. Válassza a **Feltöltés** lehetőséget.

7. Ismételje meg az 5. és a 6. lépést **aTrustFrameworkExtensions.xml.**

8. Ismételje meg az 5. és a 6. lépést **aSignUpOrSignin.xml.**

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Fiók létrehozásának tesztelése és Nevis Access-alkalmazás beállítása

1. Nyissa meg Azure AD B2C [bérlője](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) kezdőlapját.

2. Válassza a **Identity Experience Framework** lehetőséget.

3. Görgessen le az Egyéni szabályzatok lapra, és válassza a **B2C_1A_signup_signin.**

4. Válassza a **Futtatás most lehetőséget.**

5. Az előugró ablakban válassza a **Regisztráció lehetőséget.**

6. Adja meg az e-mail-címét.

7. Válassza az **Ellenőrző kód küldése lehetőséget.**

8. Másolja át az e-mailben megadott ellenőrzőkódot.

9. Válassza az **Ellenőrzés** lehetőséget.

10. Töltse ki az űrlapot az új jelszóval és a Megjelenített névvel.

11. Válassza a **Létrehozás** lehetőséget.

12. Meg fog jelenni a QR-kódvizsgálati oldal.

13. A telefonon nyissa meg a **Nevis Access alkalmazást.**

14. Válassza a **Face ID lehetőséget.**

15. Ha a képernyőn az **Authenticator regisztrációja sikeres volt,** válassza a **Folytatás lehetőséget.**

16. A telefonon hitelesítse magát újra az arcával.

17. Meg fog jelenni a kódban [jwt.ms](https://jwt.ms) jogkivonat részleteit megjelenítő kezdőlap.

### <a name="test-the-pure-passwordless-sign-in"></a>A tiszta jelszó nélküli bejelentkezés tesztelése

1. A **Identity Experience Framework** alatt válassza a **B2C_1A_signup_signin** lehetőséget.

2. Válassza a **Futtatás most lehetőséget.**

3. Az előugró ablakban válassza a **Jelszó nélküli hitelesítés lehetőséget.**

4. Adja meg az e-mail-címét.

5. Válassza a **Folytatás** lehetőséget.

6. A telefonján az értesítések között válassza a **Nevis Access alkalmazásértesítés lehetőséget.**

7. Hitelesítsen az arcával.

8. Automatikusan meg fog jelenni a jogkivonatokat [megjelenítő jwt.ms](https://jwt.ms) kezdőlapja.

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket.

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Az egyéni szabályzatok első lépések a Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
