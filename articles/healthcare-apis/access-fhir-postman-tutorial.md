---
title: Poster FHIR-kiszolgáló az Azure-ban – Azure API a FHIR-hez
description: Ebben az oktatóanyagban végigvezeti azokat a lépéseket, amelyek a Poster használatához szükségesek ahhoz, hogy hozzáférjenek egy FHIR-kiszolgálóhoz. A Poster hasznos az API-kat használó alkalmazások hibakeresésében.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 02/01/2021
ms.openlocfilehash: 5771f95da7b63c6c16bb581b84cc28c4b838ec27
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259016"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Az Azure API elérése a Poster FHIR

Egy ügyfélalkalmazás egy [Rest APIon](https://www.hl7.org/fhir/http.html)keresztül fér hozzá egy FHIR API-hoz. Előfordulhat, hogy a FHIR-kiszolgálóval közvetlenül is kapcsolatba kell lépnie az alkalmazások létrehozásakor, például hibakeresési célokra. Ebből az oktatóanyagból megtudhatja, milyen lépéseket kell tennie a [Poster](https://www.getpostman.com/) használatához a FHIR-kiszolgálóhoz való hozzáféréshez. A Poster egy olyan eszköz, amelyet gyakran használnak az API-kat használó alkalmazások kiépítésekor való hibakereséshez.

## <a name="prerequisites"></a>Előfeltételek

- Egy FHIR-végpont az Azure-ban. Ezt beállíthatja a felügyelt Azure API-FHIR vagy az Azure-hoz készült nyílt forráskódú FHIR-kiszolgáló használatával. A felügyelt Azure API beállítása a FHIR [Azure Portal](fhir-paas-portal-quickstart.md), [POWERSHELL](fhir-paas-powershell-quickstart.md)vagy [Azure CLI](fhir-paas-cli-quickstart.md)használatával.
- Egy  [ügyfélalkalmazás](register-confidential-azure-ad-client-app.md) , amelyet a FHIR szolgáltatás eléréséhez fog használni.
- A FHIR szolgáltatás eléréséhez engedélyeket kapott (például "FHIR adatközreműködői") az ügyfélalkalmazás számára. További információ: [configure Azure RBAC for FHIR](https://docs.microsoft.com/azure/healthcare-apis/configure-azure-rbac)
- A Posting telepítése megtörtént. A következő címről kérheti le: [https://www.getpostman.com](https://www.getpostman.com)

## <a name="fhir-server-and-authentication-details"></a>FHIR-kiszolgáló és-hitelesítés részletei

A Poster használatához a következő adatokra van szükség:

- A FHIR-kiszolgáló URL-címe, például: `https://MYACCOUNT.azurehealthcareapis.com`
- A `Authority` FHIR-kiszolgáló identitás-szolgáltatója, például: `https://login.microsoftonline.com/{TENANT-ID}`
- A konfigurálva `audience` . Ez általában a FHIR-kiszolgáló URL-címe, például `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` vagy csak `https://azurehealthcareapis.com` .
- A `client_id` FHIR szolgáltatás eléréséhez használni kívánt [ügyfélalkalmazás](register-confidential-azure-ad-client-app.md) (vagy alkalmazás-azonosító).
- Az `client_secret` ügyfélalkalmazás (vagy alkalmazás titka).

Végezetül ellenőriznie kell, hogy az `https://www.getpostman.com/oauth2/callback` ügyfélalkalmazás regisztrált válasz URL-címe.

## <a name="connect-to-fhir-server"></a>Kapcsolódás a FHIR-kiszolgálóhoz

A Poster használatával tegye a következő `GET` kérelmet `https://fhir-server-url/metadata` :

![Poster metaadat-képesség nyilatkozata](media/tutorial-postman/postman-metadata.png)

A FHIR készült Azure API metaadat-URL-címe a következő: `https://MYACCOUNT.azurehealthcareapis.com/metadata` . Ebben a példában a FHIR-kiszolgáló URL-címe, a `https://fhirdocsmsft.azurewebsites.net` kiszolgáló képességi nyilatkozata pedig a következő címen érhető el: `https://fhirdocsmsft.azurewebsites.net/metadata` . A végpontnak hitelesítés nélkül kell elérhetőnek lennie.

Ha a korlátozott erőforrásokhoz próbál hozzáférni, a "hitelesítés sikertelen" választ kell kapnia:

![Sikertelen hitelesítés](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Hozzáférési jogkivonat beszerzése

Érvényes hozzáférési token beszerzéséhez válassza az "engedélyezés" és a "OAuth 2,0" TÍPUSt:

![OAuth 2,0 beállítása](media/tutorial-postman/postman-select-oauth2.png)

Nyomja meg az "új hozzáférési jogkivonat lekérése" elemet, és megjelenik egy párbeszédpanel:

![Új hozzáférési jogkivonat kérése](media/tutorial-postman/postman-request-token.png)

A következő részletekre lesz szüksége:

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
| Állam                 | `1234`                                                                                                            |                            |
| Ügyfél-hitelesítés | Ügyfél hitelesítő adatainak küldése a törzsben                                                                                 |                 

Nyomja meg a "kérelem token" elemet, és a rendszer végigvezeti a Azure Active Directory hitelesítési folyamaton, és egy tokent ad vissza a Poster-nek. Ha problémákat tapasztal, nyissa meg a Poster-konzolt (a "View->show Poster Console" menüpontban).

Görgessen le a visszaadott jogkivonat képernyőjén, és nyomja meg a "jogkivonat használata" elemet:

![Token használata](media/tutorial-postman/postman-use-token.png)

A tokent most fel kell tölteni a "hozzáférési jogkivonat" mezőbe, és kiválaszthatja a tokeneket a "rendelkezésre álló tokenek" lehetőségből. Ha az erőforrás-keresés megismétléséhez újra "küld" `Patient` , akkor a következő állapotot kell kapnia `200 OK` :

![200 OK](media/tutorial-postman/postman-200-OK.png)

Ebben az esetben az adatbázisban nincsenek betegek, és a keresés üres.

Ha a hozzáférési jogkivonatot egy hasonló eszközzel vizsgálja meg [https://jwt.ms](https://jwt.ms) , akkor a következőhöz hasonló tartalomnak kell megjelennie:

```jsonc
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

Hibaelhárítási helyzetekben a megfelelő célközönség ( `aud` jogcím) ellenőrzése jó kiindulópont. Ha a token a megfelelő kibocsátótól ( `iss` jogcím) származik, és rendelkezik a megfelelő célközönséggel ( `aud` jogcím), de továbbra sem tudja elérni a FHIR API-t, akkor valószínű, hogy a felhasználó vagy az egyszerű szolgáltatásnév ( `oid` jogcím) nem fér hozzá a FHIR adatsíkon. Javasoljuk, hogy az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával](configure-azure-rbac.md) rendeljen adatsíkok-szerepköröket a felhasználókhoz. Ha külső, másodlagos Azure Active Directory-bérlőt használ az adatsíkon, akkor [konfigurálnia kell a helyi RBAC-hozzárendeléseket](configure-local-rbac.md).

Az Azure CLI-vel a [FHIR készült Azure API-hoz is kaphat tokent](get-healthcare-apis-access-token-cli.md). Ha az Azure CLI-vel kapott tokent használ, használja a "tulajdonosi jogkivonat" engedélyezési típust, majd illessze be a jogkivonatot közvetlenül.

## <a name="inserting-a-patient"></a>Beteg beszúrása

Most, hogy érvényes hozzáférési jogkivonattal rendelkezik. Új beteget is beszúrhat. Váltson a "POST" metódusra, és adja hozzá a következő JSON-dokumentumot a kérelem törzsébe:

[!code-json[](samples/sample-patient.json)]

Kattintson a "Küldés" gombra, és látnia kell, hogy a beteg sikeresen létrejött:

![Képernyőkép, amely azt mutatja, hogy a beteg sikeresen létrejött.](media/tutorial-postman/postman-patient-created.png)

Ha megismétli a beteges keresést, a következőt kell látnia:

![Beteg létrehozva](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy FHIR API-t adott hozzá a Poster használatával. Olvassa el a támogatott funkciók szakasz támogatott API-funkcióit ismertető szakaszt.
 
>[!div class="nextstepaction"]
>[Támogatott funkciók](fhir-features-supported.md)
