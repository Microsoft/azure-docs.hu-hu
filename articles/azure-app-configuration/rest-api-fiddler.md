---
title: Azure Active Directory REST API – teszt a Hegedűs használatával
description: A Hegedűs használatával tesztelheti az Azure-alkalmazás konfigurációját REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 1142aa25212d87c5484963cda4e172df3d1fbafc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932608"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>Az Azure-alkalmazás konfigurációjának tesztelése a Hegedűs használatával REST API

A REST API a [Hegedűs](https://www.telerik.com/fiddler)használatával történő teszteléséhez meg kell adnia a kérések [hitelesítéséhez](./rest-api-authentication-hmac.md) szükséges HTTP-fejléceket. Az alábbi lépésekkel konfigurálhatja a hegedűst a REST API teszteléséhez, a hitelesítési fejlécek automatikus létrehozásával:

1. Győződjön meg arról, hogy a TLS 1,2 egy engedélyezett protokoll:
    1. Lépjen az **eszközök**  >  **Beállítások HTTPS menüpontra**  >  .
    1. Ellenőrizze, hogy a **https-forgalom visszafejtése** be van-e jelölve.
    1. A protokollok listájában adja hozzá a **TLS 1.2** -et, ha nincs jelen.
1. Nyissa meg a **Hegedűs parancsfájl-szerkesztőjét** vagy nyomja meg a **CTRL-R** billentyűt a Hegedűs
1. A függvény előtt adja hozzá a következő kódot az `Handlers` osztályban belül `OnBeforeRequest`

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. Adja hozzá a következő kódot a `OnBeforeRequest` függvény végén. Frissítse a hozzáférési kulcsot a TODO megjegyzésben jelzett módon.

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. A [Hegedűs zeneszerzője](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest) a kérések létrehozásához és elküldéséhez
