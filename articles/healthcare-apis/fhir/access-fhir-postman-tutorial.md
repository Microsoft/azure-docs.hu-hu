---
title: Poster FHIR-kiszolgáló az Azure-ban – Azure API a FHIR-hez
description: Ebben az oktatóanyagban végigvezeti a Poster használatával egy FHIR-kiszolgáló eléréséhez szükséges lépéseken. A Poster hasznos az API-kat használó alkalmazások hibakeresésében.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 03/16/2021
ms.openlocfilehash: e9031dc77054a2bbac8015bbbdd7b9ed2a35e84f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043342"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Az Azure API elérése a Poster FHIR

Egy ügyfélalkalmazás egy [Rest APIon](https://www.hl7.org/fhir/http.html)keresztül elérheti az Azure API-t a FHIR. A kérések elküldéséhez, a válaszok megtekintéséhez és az alkalmazás hibakereséséhez az eszköz létrehozásakor használja az Ön által választott API-tesztelési eszközt. Ebben az oktatóanyagban végigvezeti a FHIR-kiszolgáló [Poster](https://www.getpostman.com/)használatával való elérésének lépésein. 

## <a name="prerequisites"></a>Előfeltételek

- Egy FHIR-végpont az Azure-ban. 

   Az Azure API FHIR (felügyelt szolgáltatás) való üzembe helyezéséhez használhatja a [Azure Portal](fhir-paas-portal-quickstart.md), a [PowerShellt](fhir-paas-powershell-quickstart.md)vagy az [Azure CLI](fhir-paas-cli-quickstart.md)-t.
- Egy regisztrált, [bizalmas ügyfélalkalmazás](register-confidential-azure-ad-client-app.md) a FHIR szolgáltatás eléréséhez.
- A FHIR szolgáltatás eléréséhez engedélyeket kapott a bizalmas ügyfélalkalmazás, például "FHIR adatközreműködői". További információ: [Az Azure RBAC konfigurálása a FHIR](./configure-azure-rbac.md).
- A Posting telepítése megtörtént. 
    
    További információ a Poster-ról: Ismerkedés [a Poster szolgáltatással](https://www.getpostman.com).

## <a name="fhir-server-and-authentication-details"></a>FHIR-kiszolgáló és-hitelesítés részletei

A Poster használatához a következő hitelesítési paraméterek szükségesek:

- A FHIR-kiszolgáló URL-címe, például: `https://MYACCOUNT.azurehealthcareapis.com`

- A `Authority` FHIR-kiszolgáló identitás-szolgáltatója, például: `https://login.microsoftonline.com/{TENANT-ID}`

- A beállított `audience` érték általában a FHIR-kiszolgáló URL-címe, például `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` vagy `https://azurehealthcareapis.com` .

- A `client_id` FHIR szolgáltatás eléréséhez használt [bizalmas ügyfélalkalmazás](register-confidential-azure-ad-client-app.md) vagy alkalmazás azonosítója.

- A `client_secret` bizalmas ügyfélalkalmazás vagy alkalmazás titkos kulcsa.

Végezetül ellenőriznie kell, hogy az `https://www.getpostman.com/oauth2/callback` ügyfélalkalmazás regisztrált válasz URL-címe.

## <a name="connect-to-fhir-server"></a>Kapcsolódás a FHIR-kiszolgálóhoz

Nyissa meg a Poster-t, majd válassza a **beolvasás** lehetőséget a kérelem elvégzéséhez `https://fhir-server-url/metadata` .

![Poster metaadat-képesség nyilatkozata](media/tutorial-postman/postman-metadata.png)

A FHIR készült Azure API metaadat-URL-címe a következő: `https://MYACCOUNT.azurehealthcareapis.com/metadata` . 

Ebben a példában a FHIR-kiszolgáló URL-címe `https://fhirdocsmsft.azurewebsites.net` , a kiszolgáló képességi nyilatkozata pedig a következő címen érhető el: `https://fhirdocsmsft.azurewebsites.net/metadata` . Ez a végpont hitelesítés nélkül érhető el.

Ha a korlátozott erőforrásokhoz próbál hozzáférni, a "hitelesítés sikertelen" válasz jelenik meg.

![Sikertelen hitelesítés](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Hozzáférési jogkivonat beszerzése
Érvényes hozzáférési jogkivonat beszerzéséhez válassza az **Engedélyezés** lehetőséget, és válassza a **OAuth 2,0** elemet a **típus** legördülő menüből.

![OAuth 2,0 beállítása](media/tutorial-postman/postman-select-oauth2.png)

Válassza az **Get New Access Token** (Új hozzáférési jogkivonat beszerzése) lehetőséget.

![Új hozzáférési jogkivonat kérése](media/tutorial-postman/postman-request-token.png)

Az **új hozzáférési jogkivonat lekérése** párbeszédpanelen adja meg a következő adatokat:

| Mező                 | Példaérték                                                                                                   | Megjegyzés                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Jogkivonat neve            | MYTOKEN                                                                                                         | A kiválasztott név          |
| Engedélyezési típus            | Engedélyezési kód                                                                                              |                            |
| Visszahívási URL          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| Hitelesítési URL-cím              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` a `https://MYACCOUNT.azurehealthcareapis.com` FHIR készült Azure API-hoz |
| Hozzáférési jogkivonat URL-címe      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| Ügyfél-azonosító             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | Alkalmazásazonosító             |
| Titkos ügyfélkulcs         | `XXXXXXXX`                                                                                                        | Titkos ügyfél kulcsa          |
| Hatókör | `<Leave Blank>` |
| Állapot                |  `1234`                                                                                                           |                            |
| Ügyfél-hitelesítés | Ügyfél hitelesítő adatainak küldése a törzsben                                                                                 |                 

Válassza ki a **kérelem tokenjét** a Azure Active Directory hitelesítési folyamaton keresztüli irányításhoz, és a rendszer visszaadja a tokent a Poster-nek. Ha hitelesítési hiba történik, további részletekért tekintse meg a Poster-konzolt. **Megjegyzés**: a menüszalagon válassza a **nézet** lehetőséget, majd kattintson a **Poster-konzol megjelenítése** lehetőségre. A Poster-konzol billentyűparancsa **ALT-CTRL + C**.

Görgessen lefelé a visszaadott jogkivonat képernyő megjelenítéséhez, majd válassza a **token használata** lehetőséget.

![Token használata](media/tutorial-postman/postman-use-token.png)

Az újonnan feltöltött token megtekintéséhez tekintse meg a **hozzáférési jogkivonat** mezőt. Ha a **Küldés** gombra kattint az `Patient` erőforrás-keresés megismétléséhez, a rendszer visszaadja az **állapotot** `200 OK` . A visszaadott állapot `200 OK` egy sikeres HTTP-kérést jelez.

![200 OK](media/tutorial-postman/postman-200-OK.png)

A *beteg keresési* példájában nincsenek olyan páciensek az adatbázisban, amelyekben a keresési eredmény üres.

A hozzáférési tokent a [JWT.MS](https://jwt.ms)hasonló eszközzel ellenőrizheti. Alább látható egy példa a tartalomra.

```json
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

Hibaelhárítási helyzetekben a megfelelő célközönség ( `aud` jogcím) ellenőrzése jó kiindulópont. Ha a token a megfelelő kiállítótól ( `iss` jogcím) származik, és rendelkezik a megfelelő célközönséggel ( `aud` jogcím), de továbbra sem tudja elérni a FHIR API-t, akkor valószínű, hogy a felhasználó vagy az egyszerű szolgáltatásnév ( `oid` jogcím) nem fér hozzá a FHIR adatsíkon. Javasoljuk, hogy az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](configure-azure-rbac.md) használatával rendeljen adatsíkok-szerepköröket a felhasználókhoz. Ha külső, másodlagos Azure Active Directory-bérlőt használ az adatsíkon, konfigurálnia kell a FHIR-hozzárendelések [helyi RBAC](configure-local-rbac.md) .

Az Azure CLI-vel a [FHIR készült Azure API](get-healthcare-apis-access-token-cli.md)-hoz is kaphat tokent. Ha az Azure CLI-vel kapott tokent használ, használja az engedélyezési típus *tulajdonosi jogkivonatát*. Illessze be közvetlenül a tokent.

## <a name="inserting-a-patient"></a>Beteg beszúrása

Egy érvényes hozzáférési jogkivonat használatával új beteget szúrhat be. A Poster alkalmazásban módosítsa a metódust a **post** lehetőség kiválasztásával, majd adja hozzá a következő JSON-dokumentumot a kérelem törzsébe.

[!code-json[](../samples/sample-patient.json)]

A **Küldés** gombra kattintva megállapíthatja, hogy a beteg létrehozása sikeres volt-e.

![Képernyőkép, amely azt mutatja, hogy a beteg sikeresen létrejött.](media/tutorial-postman/postman-patient-created.png)

Ha megismétli a beteges keresést, a következőt kell látnia:

![Beteg létrehozva](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a Poster használatával elértük a FHIR készült Azure API-t. További információ a FHIR-funkciókhoz készült Azure API-ról:
 
>[!div class="nextstepaction"]
>[Támogatott funkciók](fhir-features-supported.md)
