---
title: Webalkalmazás-oktatóanyag – a FHIR készült Azure API-hoz való csatlakozás tesztelése
description: Ezek az oktatóanyagok egy egyszerű webalkalmazás üzembe helyezésének példáját mutatják be. Az oktatóanyag ezen szakasza végigvezeti a FHIR-kiszolgáló és a Poster közötti csatlakozás tesztelésén.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 1c64468a2e420734ca51a5b9308bb52e13712c51
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018885"
---
# <a name="testing-the-fhir-api"></a>A FHIR API tesztelése
Az előző két lépésben üzembe helyezte az Azure API-t a FHIR számára, és regisztrálta az ügyfélalkalmazás alkalmazását. Most már készen áll arra, hogy tesztelje, hogy a FHIR készült Azure API be van-e állítva az ügyfélalkalmazás számára. 

## <a name="retrieve-capability-statement"></a>Képességek beolvasása utasítás
Először is megkapjuk az Azure API FHIR-hez készült funkciójának utasításait. 
1. Poster megnyitása
1. A https://. azurehealthcareapis.com/metadata lekérésével kérje le a képességi utasítást \<FHIR-SERVER-NAME> . Az alábbi képen a FHIR-kiszolgáló neve **fhirserver**.

![Képesség nyilatkozata](media/tutorial-web-app/postman-capability-statement.png)

A következő lépésben megpróbálunk beolvasni egy beteget. A beteg beolvasásához írja be a következőt: GET https:// \<FHIR-SERVER-NAME> . azurehealthcareapis.com/Patient. 401 jogosulatlan hibaüzenetet fog kapni. Ez a hiba azért van, mert nem bizonyította, hogy hozzáféréssel kell rendelkeznie a beteg-adatszolgáltatásokhoz.

## <a name="get-patient-from-fhir-server"></a>FHIR-kiszolgálóról származó páciens beszerzése
![Sikertelen beteg](media/tutorial-web-app/postman-patient-authorization-failed.png)

A hozzáféréshez hozzáférési jogkivonatra van szükség.
1. A Poster területen válassza az **Engedélyezés** lehetőséget, és állítsa be a típust a **OAuth 2.0** értékre
1. Válassza az **új hozzáférési jogkivonat beolvasása** elemet.
1. Töltse ki a mezőket, és válassza a **kérelem tokenje** elemet. Alább láthatja az oktatóanyag egyes mezőinek értékeit.

|Mező                |Érték                                                               |
|---------------------|--------------------------------------------------------------------|
|Jogkivonat neve           |A token neve                                               |
|Engedélyezési típus           |Engedélyezési kód                                                  |
|Visszahívási URL         |https://www.getpostman.com/oauth2/callback                          |
|Hitelesítési URL-cím             |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID>/oauth2/? Resource = https:// \<FHIR-SERVER-NAME> . azurehealthcareapis.com|
|Hozzáférési jogkivonat URL-címe     |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID>/oauth2/token|
|Ügyfél-azonosító            |Az előző lépések során másolt ügyfél-azonosító             |
|Titkos ügyfélkulcs        |\<BLANK>                                                            |
|Hatókör                |\<BLANK>                                                            |
|Állam                |1234                                                                |
|Ügyfél-hitelesítés|Ügyfél hitelesítő adatainak küldése a törzsben                                     |

4. Jelentkezzen be a hitelesítő adataival, és válassza az **elfogadás** lehetőséget.
1. Görgessen le az eredményre, és válassza a **token használata** lehetőséget.
1. Válassza az újbóli **Küldés** lehetőséget a lap tetején, és ezúttal az eredmény ![ sikerességét.](media/tutorial-web-app/postman-patient-authorization-success.png)

## <a name="post-patient-into-fhir-server"></a>A FHIR-kiszolgálóra való beküldése
Most már van hozzáférése, létrehozhat egy új beteget. Íme egy példa egy egyszerű, a FHIR-kiszolgálóba felvenni kívánt páciensre. Írja be az alábbi kódot a posta **Body (törzs** ) szakaszába.

``` json
    {
        "resourceType": "Patient",
        "active": true,
        "name": [
            {
                "use": "official",
                "family": "Kirk",
                "given": [
                    "James",
                    "Tiberious"
                ]
            },
            {
                "use": "usual",
                "given": [
                    "Jim"
                ]
            }
        ],
        "gender": "male",
        "birthDate": "1960-12-25"
    }
```
Ez a bejegyzés létrehoz egy új pácienst a FHIR-kiszolgálón, James Tiberious Kirk néven.
![Post beteg](media/tutorial-web-app/postman-post-patient.png)

Ha a fenti lépések lekérése után ismét beolvas egy beteget, megjelenik James Tiberious Kirk a kimenetben.

## <a name="troubleshooting-access-issues"></a>Hozzáférési problémák elhárítása
Ha a fenti lépések bármelyike során problémákba ütközött, tekintse át a Azure Active Directory és a FHIR készült Azure API-val együtt összeállított dokumentumokat. 

* Az [Azure ad és az Azure API for FHIR](azure-ad-hcapi.md) – ez a dokumentum a Azure Active Directory alapvető alapelveit ismerteti, valamint azt, hogy miként működik együtt a FHIR készült Azure API-val.
* [Hozzáférési jogkivonat érvényesítése](azure-ad-hcapi-token-validation.md) – ez a útmutató részletesen ismerteti a hozzáférési tokenek érvényesítését és a hozzáférési problémák megoldásához szükséges lépéseket.

## <a name="next-steps"></a>Következő lépések
Most, hogy sikeresen tud csatlakozni az ügyfélalkalmazáshoz, készen áll a webalkalmazás megírására.

>[!div class="nextstepaction"]
>[Webalkalmazás írása](tutorial-web-app-write-web-app.md)



