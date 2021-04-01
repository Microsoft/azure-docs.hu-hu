---
title: Az API-k API Management a OAuth 2,0 és az Azure AD használatával
titleSuffix: Azure API Management
description: Megtudhatja, hogyan védheti meg az Azure API Management webes API-hátteréhez való hozzáférést a OAuth 2,0 felhasználói engedélyezésével és Azure Active Directory
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: article
ms.date: 09/23/2020
ms.author: apimpm
ms.custom: contperf-fy21q1
ms.openlocfilehash: face4beab450e92be76b2bb90e45625e025de6ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97027917"
---
# <a name="protect-a-web-api-backend-in-azure-api-management-by-using-oauth-20-authorization-with-azure-ad"></a>A webes API-k elleni védelem az Azure API Managementban az Azure AD-vel való OAuth 2,0-engedélyezés használatával 

Ez az útmutató bemutatja, hogyan konfigurálhatja [Azure API Management](api-management-key-concepts.md) -példányát az API-k biztosításához a [OAuth 2,0 protokoll és a Azure Active Directory (Azure ad)](../active-directory/develop/active-directory-v2-protocols.md)használatával. 

> [!NOTE]
> Ez a funkció a API Management **fejlesztői**, **alapszintű**, **standard** és **prémium** szintjein érhető el.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következőket kell tennie:

- Egy API Management-példány
- A API Management példányt használó, közzétett API
- Azure AD-bérlő

## <a name="overview"></a>Áttekintés

A következő lépések gyors áttekintést nyújtanak a lépésekről:

1. Regisztráljon egy alkalmazást (backend-app) az Azure AD-ben az API képviseletéhez.
1. Regisztráljon egy másik alkalmazást (ügyfélalkalmazás) az Azure AD-ben az API-t meghívó ügyfélalkalmazás megjelenítéséhez.
1. Az Azure AD-ben adja meg az engedélyeket, hogy az ügyfélalkalmazás meghívja a háttér-alkalmazást.
1. Konfigurálja úgy a fejlesztői konzolt, hogy az OAuth 2,0 felhasználói hitelesítés használatával hívja meg az API-t.
1. Adja hozzá a **validate-JWT** szabályzatot az OAuth-jogkivonat ellenőrzéséhez minden bejövő kérelemnél.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Alkalmazás regisztrálása az Azure AD-ben az API képviseletéhez

Az API-k Azure AD-vel való ellátásához először regisztráljon egy, az API-t képviselő alkalmazást az Azure AD-ben. A következő lépésekkel regisztrálja az alkalmazást a Azure Portal használatával. Az alkalmazások regisztrálásával kapcsolatos további információkért lásd: gyors üzembe helyezés [az alkalmazásban webes API-k közzététele céljából](../active-directory/develop/quickstart-configure-app-expose-web-apis.md).

1. Az alkalmazás regisztrálásához lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **Alkalmazásregisztrációk**.

1. Válassza az **új regisztráció** lehetőséget. 

1. Amikor megjelenik az **alkalmazás regisztrálása lap** , adja meg az alkalmazás regisztrációs adatait:

   - A **név** szakaszban adjon meg egy értelmezhető nevet, amely megjelenik az alkalmazás felhasználói számára (például *háttér-alkalmazás*). 
   - A **támogatott fióktípus** szakaszban válasszon egy olyan beállítást, amely megfelel a forgatókönyvnek. 

1. Hagyja üresen az **átirányítási URI** szakaszt.

1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához. 

1. Az alkalmazás **– Áttekintés** oldalon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és jegyezze fel később.

1. Válassza ki az **API közzététele** lehetőséget, és állítsa be az **alkalmazás-azonosító URI-ját** az alapértelmezett értékre. Jegyezze fel ezt az értéket később.

1. Kattintson a **hatókör hozzáadása** gombra a **hatókör** hozzáadása lap megjelenítéséhez. Ezután hozzon létre egy új hatókört, amelyet az API támogat (például: `Files.Read` ).

1. A hatókör létrehozásához kattintson a **hatókör hozzáadása** gombra. Ismételje meg ezt a lépést az API által támogatott összes hatókör hozzáadásához.

1. A hatókörök létrehozásakor jegyezze fel őket egy későbbi lépésben való használatra. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Egy másik alkalmazás regisztrálása az Azure AD-ben egy ügyfélalkalmazás képviseletéhez

Az API-t meghívó összes ügyfélalkalmazás alkalmazásként kell regisztrálni az Azure AD-ben. Ebben a példában az ügyfélalkalmazás a **fejlesztői konzol** a API Management fejlesztői portálon. 

Egy másik alkalmazás regisztrálása az Azure AD-ben a fejlesztői konzol képviseletéhez:

1. Az alkalmazás regisztrálásához lépjen a [Azure Portal](https://portal.azure.com) .

1. Keresse meg és válassza ki a **Alkalmazásregisztrációk**.

1. Válassza az **új regisztráció** lehetőséget.

1. Amikor megjelenik az **alkalmazás regisztrálása lap** , adja meg az alkalmazás regisztrációs adatait:

   - A **név** szakaszban adjon meg egy értelmezhető nevet, amely megjelenik az alkalmazás felhasználói számára (például *Client-app*). 
   - A **támogatott fiókok típusai** szakaszban válassza a **fiókok lehetőséget bármely szervezeti címtárban (bármely Azure ad-címtár – több-bérlő)**. 

1. Az **átirányítási URI** szakaszban válassza ki `Web` és hagyja üresen az URL mezőt.

1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához. 

1. Az alkalmazás **– Áttekintés** oldalon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és jegyezze fel később.

1. Hozzon létre egy ügyfél-titkot az alkalmazás számára egy későbbi lépésben való használatra.

   1. Az ügyfélalkalmazás oldalain válassza a **tanúsítványok & titkok** lehetőséget, és válassza az **új ügyfél titka** lehetőséget.

   1. Az **ügyfél titkos kulcsának hozzáadása** területen adjon meg egy **leírást**. Válassza ki a kulcs lejárati idejét, majd válassza a **Hozzáadás** lehetőséget.

A titkos kód létrehozásakor jegyezze fel a kulcs értékét egy későbbi lépésben való használatra. 

## <a name="grant-permissions-in-azure-ad"></a>Engedélyek megadása az Azure AD-ben

Most, hogy regisztrált két alkalmazást az API és a fejlesztői konzol képviseletére, adja meg az engedélyeket, hogy az ügyfél-alkalmazás meghívni lehessen a háttér-alkalmazást.  

1. Lépjen a [Azure Portal](https://portal.azure.com) az ügyfélalkalmazás engedélyeinek megadásához. Keresse meg és válassza ki a **Alkalmazásregisztrációk**.

1. Válassza ki az ügyfélalkalmazás. Ezután az alkalmazás oldalain válassza az **API-engedélyek** lehetőséget.

1. Válassza **az engedély hozzáadása** lehetőséget.

1. Az **API kiválasztása** területen válassza a **saját API**-k elemet, majd keresse meg és válassza ki a háttér-alkalmazás elemet.

1. A **delegált engedélyek** területen válassza ki a háttér-alkalmazás megfelelő engedélyeit, majd válassza az **engedélyek hozzáadása** lehetőséget.

1. Ha szükséges, az **API-engedélyek** lapon válassza a **rendszergazdai jóváhagyás megadása a \<your-tenant-name> következőhöz** lehetőséget, hogy a jóváhagyást adjon a címtár összes felhasználója nevében. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>A OAuth 2,0 felhasználói hitelesítés engedélyezése a Fejlesztői konzolon

Ezen a ponton létrehozta az alkalmazásait az Azure AD-ben, és megfelelő engedélyeket kapott ahhoz, hogy az ügyfél-alkalmazás meghívja a háttér-alkalmazást. 

Ebben a példában a fejlesztői konzol az ügyfél-alkalmazás. A következő lépések azt ismertetik, hogyan engedélyezhető a OAuth 2,0 felhasználói hitelesítés a Fejlesztői konzolon. 

1. A Azure Portalban keresse meg a API Management-példányát.

1. Válassza a **OAuth 2,0**  >  **Hozzáadás** lehetőséget.

1. Adja **meg a megjelenítendő nevet** és a **leírást**.

1. Az **ügyfél-regisztrációs lap URL-címéhez** adja meg a helyőrző értékét, például: `http://localhost` . Az **ügyfél-regisztrációs oldal URL-címe** arra a lapra mutat, amelyet a felhasználók a saját fiókjaik létrehozására és konfigurálására használhatnak az ezt támogató OAuth 2,0-szolgáltatók számára. Ebben a példában a felhasználók nem hoznak létre és nem konfigurálja a saját fiókjaikat, ezért Ehelyett helyőrzőt használ.

1. Az **engedélyezési engedélyek típusainál** válassza az **engedélyezési kód** lehetőséget.

1. Az **engedélyezési végpont URL-címének** és a **jogkivonat-végpont URL-címének** megadása. Olvassa be ezeket az értékeket az Azure AD-bérlő **végpontok** lapján. Nyissa meg ismét a **Alkalmazásregisztrációk** lapot, és válassza a **végpontok** lehetőséget.


1. Másolja az **OAuth 2,0 engedélyezési végpontot**, és illessze be az **engedélyezési végpont URL-címébe** . Válassza a **post** lehetőséget az engedélyezési kérelem metódusa alatt.

1. Másolja a **OAuth 2,0 token-végpontot**, és illessze be a **jogkivonat-végpont URL-** szövegmezőbe. 

   >[!IMPORTANT]
   > Használjon **v1** -es vagy **v2** -es végpontokat. Azonban attól függően, hogy melyik verziót választja, az alábbi lépés eltérő lesz. A v2-végpontok használatát javasoljuk. 

1. Ha **v1** -es végpontokat használ, adjon hozzá egy **erőforrás** nevű Body paramétert. A paraméter értékeként használja a háttérbeli alkalmazás **alkalmazás-azonosítóját** . 

1. Ha **v2** -végpontokat használ, használja az **Alapértelmezett hatókör** mezőben a háttér-alkalmazáshoz létrehozott hatókört. Ügyeljen arra is, hogy a tulajdonság értékét az [`accessTokenAcceptedVersion`](../active-directory/develop/reference-app-manifest.md#accesstokenacceptedversion-attribute) `2` [alkalmazás jegyzékfájljában](../active-directory/develop/reference-app-manifest.md)állítsa be.

1. Ezután határozza meg az ügyfél hitelesítő adatait. Az ügyfél-alkalmazás hitelesítő adatai.

1. Az **ügyfél-azonosítóhoz** használja az ügyfél-alkalmazás **alkalmazás-azonosítóját** .

1. Az **ügyfél titkos** kulcsaként használja az ügyfélhez korábban létrehozott kulcsot. 

1. Az ügyfél titkos kulcsát közvetlenül a hitelesítési kód engedélyezési típusának **redirect_url** kell megadnia. Jegyezze fel ezt az URL-címet.

1. Válassza a **Létrehozás** lehetőséget.

1. Térjen vissza az ügyfél-alkalmazás regisztrálásához Azure Active Directory, és válassza a **hitelesítés** lehetőséget.

1. A **platform-konfigurációk** területen kattintson a **platform hozzáadása** elemre, és válassza ki a típust **webesként**, illessze be a **redirect_urlt** az **átirányítási URI** alatt, majd kattintson a **Konfigurálás** gombra a mentéshez.

Most, hogy konfigurált egy OAuth 2,0-es engedélyezési kiszolgálót, a fejlesztői konzol hozzáférési jogkivonatokat szerezhet be az Azure AD-ből. 

A következő lépés a OAuth 2,0 felhasználói engedélyezésének engedélyezése az API-hoz. Ez lehetővé teszi, hogy a fejlesztői konzol tudja, hogy a felhasználó nevében be kell szereznie egy hozzáférési jogkivonatot, mielőtt meghívja az API-t.

1. Keresse meg API Management-példányát, és nyissa meg az **API-kat**.

1. Válassza ki a védelemmel ellátni kívánt API-t. Például: `Echo API`.

1. Válassza a **Beállítások lehetőséget**.

1. A **Biztonság** területen válassza a **OAuth 2,0** elemet, és válassza ki a korábban konfigurált OAuth 2,0-kiszolgálót. 

1. Kattintson a **Mentés** gombra.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Sikerült meghívni az API-t a fejlesztői portálról

> [!NOTE]
> Ez a szakasz nem vonatkozik a fejlesztői portált nem támogató **felhasználási** szintjére.

Most, hogy az OAuth 2,0 felhasználói hitelesítés engedélyezve van az API-ban, a fejlesztői konzol a felhasználó nevében szerzi be a hozzáférési jogkivonatot, mielőtt meghívja az API-t.

1. Tallózással keresse meg az API alatt található összes műveletet a fejlesztői portálon, és válassza a **kipróbálás** lehetőséget. Ez a fejlesztői konzolra mutat.

1. Jegyezze fel, hogy az **engedélyezési** szakasz egy új eleme, amely az imént hozzáadott engedélyezési kiszolgálónak felel meg.

1. Válassza az engedélyezési **kód** lehetőséget az engedélyezés legördülő listából, és a rendszer felszólítja, hogy jelentkezzen be az Azure ad-bérlőbe. Ha már bejelentkezett a fiókkal, előfordulhat, hogy a rendszer nem kérdezi le.

1. A sikeres bejelentkezést követően a rendszer egy `Authorization` fejlécet ad hozzá a kérelemhez, amely az Azure ad hozzáférési jogkivonatával rendelkezik. A következő egy minta token (Base64 kódolású):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

1. Az API meghívásához válassza a **Küldés** lehetőséget.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>JWT-érvényesítési házirend konfigurálása a kérelmek előzetes engedélyezéséhez

Ezen a ponton, amikor egy felhasználó megpróbál hívást készíteni a fejlesztői konzolról, a rendszer felszólítja a felhasználót, hogy jelentkezzen be. A fejlesztői konzol egy hozzáférési jogkivonatot szerez be a felhasználó nevében, és tartalmazza az API-nak benyújtott kérelemben szereplő jogkivonatot.

Mi a teendő, ha valaki jogkivonat vagy érvénytelen token nélkül hívja meg az API-t? Ha például az API-t a fejléc nélkül szeretné hívni `Authorization` , a hívás továbbra is elérhető lesz. Ennek az az oka, hogy API Management ezen a ponton nem érvényesíti a hozzáférési jogkivonatot. Egyszerűen átadja a `Authorization` fejlécet a háttér-API-nak.

A [JWT szabályzat érvényesítésével](./api-management-access-restriction-policies.md#ValidateJWT) engedélyezze a kérelmeket a API Managementban, az egyes bejövő kérelmek hozzáférési jogkivonatának ellenőrzésével. Ha egy kérelem nem rendelkezik érvényes jogkivonattal, API Management blokkolja. Adja hozzá például a következő szabályzatot a `<inbound>` szabályzat szakaszához `Echo API` . Ellenőrzi a célközönség jogcímet egy hozzáférési jogkivonatban, és hibaüzenetet ad vissza, ha a jogkivonat érvénytelen. A házirendek konfigurálásával kapcsolatos információkért lásd: [házirendek beállítása vagy szerkesztése](./set-edit-policies.md).


```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

> [!NOTE]
> Ez `openid-config` az URL-cím a v1-végpontnak felel meg. A v2 `openid-config` -végponthoz használja a következő URL-címet:
>
> `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`.

## <a name="build-an-application-to-call-the-api"></a>Alkalmazás létrehozása az API meghívásához

Ebben az útmutatóban a fejlesztői konzolt használta a API Managementban, mint a `Echo API` OAuth 2,0 által védettként hívható minta ügyfélalkalmazás. Ha többet szeretne megtudni az alkalmazások létrehozásáról és a 2,0-es OAuth megvalósításáról, tekintse meg a [Azure Active Directory](../active-directory/develop/sample-v2-code.md).

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Active Directory és a OAuth 2.0-s](../active-directory/develop/authentication-vs-authorization.md)verzióról.
- További [videók](https://azure.microsoft.com/documentation/videos/index/?services=api-management) a API Managementról.
- A háttér-szolgáltatás biztonságossá tételének egyéb módjaival kapcsolatban lásd: [kölcsönös Tanúsítványos hitelesítés](./api-management-howto-mutual-certificates.md).
- [Hozzon létre egy API Management Service-példányt](./get-started-create-service-instance.md).
- [Az első API kezelése](./import-and-publish.md).
