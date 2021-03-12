---
title: Azure Active Directory SMART on FHIR proxy
description: Ez az oktatóanyag azt ismerteti, hogyan használható egy proxy a SMART on FHIR-alkalmazásokhoz a FHIR készült Azure API-val.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 04/02/2019
ms.openlocfilehash: 2e13a9fc32964781dda07e5534e5cab79868ddf0
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019453"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>Oktatóanyag: Azure Active Directory SMART on FHIR proxy

Az [intelligens on FHIR](https://docs.smarthealthit.org/) olyan Nyílt specifikációk összessége, amelyek a FHIR-kiszolgálókkal és a FHIR-felülettel rendelkező elektronikus orvosi nyilvántartási rendszerekkel integrálják a partneri alkalmazásokat. A specifikációk egyik fő célja, hogy leírja, hogyan kell felderíteni az alkalmazásnak egy FHIR-kiszolgáló hitelesítési végpontját, és el kell indítania egy hitelesítési sorozatot. 

A hitelesítés a OAuth2 alapul. Mivel azonban az intelligens on FHIR olyan paraméter-elnevezési konvenciókat használ, amelyek nem kompatibilisek azonnal a Azure Active Directory (Azure AD) szolgáltatással, a FHIR készült Azure API beépített Azure AD intelligens FHIR-proxyval rendelkezik, amely lehetővé teszi az intelligens FHIR indítási sorozatok egy részhalmazát. Pontosabban, a proxy engedélyezi az [EHR indítási sorozatot](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence).

Ez az oktatóanyag azt ismerteti, hogyan használható a proxy a SMART on FHIR-alkalmazásokhoz a FHIR készült Azure API-val.

## <a name="prerequisites"></a>Előfeltételek

- Az Azure API FHIR-példánya
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>Azure AD-regisztrációk konfigurálása

Az intelligens on FHIR megköveteli, hogy a `Audience` FHIR-szolgáltatás URI azonosítójának URI azonosítójának kell lennie. A FHIR készült Azure API szabványos konfigurációja a következő értékét használja: `Audience` `https://azurehealthcareapis.com` . Megadhat azonban egy értéket is, amely megfelel a FHIR szolgáltatás adott URL-címének (például `https://MYFHIRAPI.azurehealthcareapis.com` ). Erre akkor van szükség, amikor az intelligens on FHIR-proxyval dolgozik.

Szüksége lesz egy ügyfélalkalmazás regisztrálására is. A legtöbb intelligens on FHIR-alkalmazás egyoldalas JavaScript-alkalmazások. Ezért kövesse a [nyilvános Azure ad-ügyfélalkalmazás](register-public-azure-ad-client-app.md)konfigurálására vonatkozó utasításokat.

A lépések elvégzése után a következőket kell tennie:

- Olyan FHIR-kiszolgáló, amelynek RGE célközönsége a `https://MYFHIRAPI.azurehealthcareapis.com` (z), ahol a a `MYFHIRAPI` FHIR-példányhoz tartozó Azure API neve.
- Nyilvános ügyfélalkalmazás regisztrálása. Jegyezze fel az ügyfélalkalmazás alkalmazás-AZONOSÍTÓját.

## <a name="enable-the-smart-on-fhir-proxy"></a>Az intelligens on FHIR proxy engedélyezése

Engedélyezze az intelligens on FHIR proxyt az Azure API for FHIR-példány **hitelesítési** beállításainál az **intelligens on FHIR proxy** jelölőnégyzet bejelölésével:

![Az intelligens on FHIR proxy engedélyezésének kiválasztása](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>CORS engedélyezése

Mivel a legtöbb intelligens on FHIR-alkalmazás egyoldalas JavaScript-alkalmazások, engedélyezni kell az áttekinthető [erőforrás-megosztást (CORS)](configure-cross-origin-resource-sharing.md) a FHIR készült Azure API-hoz:

![CORS engedélyezésének kiválasztása](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>A válasz URL-címének konfigurálása

Az intelligens on FHIR proxy közvetítőként működik az intelligens on FHIR alkalmazás és az Azure AD között. A hitelesítési válasznak (a hitelesítési kódnak) az alkalmazás helyett az intelligens on FHIR proxyra kell lépnie. A proxy ezután továbbítja a választ az alkalmazásnak. 

A hitelesítési kód e kétlépéses továbbítása miatt meg kell adnia az Azure AD-ügyfélalkalmazás válasz-URL-címét (visszahívás) egy olyan URL-címhez, amely az intelligens on FHIR proxy válasz URL-CÍMÉnek kombinációja, valamint az intelligens on FHIR alkalmazás válaszának URL-címe. Az egyesített válasz URL-címe a következő űrlapot veszi át:

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

Ebben a válaszban a az `aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA` intelligens on FHIR alkalmazáshoz tartozó válasz URL-címének biztonságos, Base64 kódolású verziója. Az intelligens on FHIR app Launcher esetében, ha az alkalmazás helyileg fut, a válasz URL-címe: `https://localhost:5001/sampleapp/index.html` . 

Az egyesített válasz URL-címét a következőhöz hasonló parancsfájl használatával hozhatja meg:

```PowerShell
$replyUrl = "https://localhost:5001/sampleapp/index.html"
$fhirServerUrl = "https://MYFHIRAPI.azurewebsites.net"
$bytes = [System.Text.Encoding]::UTF8.GetBytes($ReplyUrl)
$encodedText = [Convert]::ToBase64String($bytes)
$encodedText = $encodedText.TrimEnd('=');
$encodedText = $encodedText.Replace('/','_');
$encodedText = $encodedText.Replace('+','-');

$newReplyUrl = $FhirServerUrl.TrimEnd('/') + "/AadSmartOnFhirProxy/callback/" + $encodedText
```

Adja hozzá a válasz URL-címét az Azure AD-hez korábban létrehozott nyilvános ügyfélalkalmazás számára:

![A nyilvános ügyfélhez konfigurált válasz URL-cím](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>Teszt-beteg beszerzése

A FHIR készült Azure API és az intelligens on FHIR proxy teszteléséhez legalább egy páciensnek rendelkeznie kell az adatbázisban. Ha még nem jelentkezett be az API-val, és nem rendelkezik az adatbázisban lévő adattal, kövesse a [FHIR API Poster oktatóanyagot](access-fhir-postman-tutorial.md) a páciens betöltéséhez. Jegyezze fel az adott beteg AZONOSÍTÓját.

## <a name="download-the-smart-on-fhir-app-launcher"></a>A SMART on FHIR app Launcher letöltése

Az [Azure adattárának nyílt forráskódú FHIR-kiszolgálója](https://github.com/Microsoft/fhir-server) egy egyszerű, intelligens on FHIR app launchert és egy, a FHIR-alapú intelligens alkalmazást tartalmaz. Ebben az oktatóanyagban helyileg használhatja ezt az intelligens szolgáltatást a FHIR-indítón a telepítés teszteléséhez.

A GitHub-tárház klónozásával megtekintheti az alkalmazást az alábbi parancsokkal:

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

Az alkalmazásnak néhány konfigurációs beállításra van szüksége, amely a ben állítható be `appsettings.json` :

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

Javasoljuk, hogy használja a `dotnet user-secrets` szolgáltatást:

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

Ezzel a paranccsal futtathatja az alkalmazást:

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>Az intelligens FHIR-proxy tesztelése

Miután elindította a SMART on FHIR app launchert, rámutathat a böngészőre `https://localhost:5001` , ahol a következő képernyő jelenik meg:

![SMART on FHIR app Launcher](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

Amikor beírja a **beteg**, a **találkozás** vagy a **gyakorló** információit, megfigyelheti, hogy az **indítási környezet** frissült. Ha a FHIR készült Azure API-t használja, az indítási környezet egyszerűen egy JSON-dokumentum, amely a betegekkel, a kezelővel és egyebekkel kapcsolatos információkat tartalmaz. Ez az indítási környezet Base64 kódolású, és a lekérdezési paraméterként továbbítja az intelligens on FHIR alkalmazásnak `launch` . Az intelligens on FHIR specifikáció szerint ez a változó átlátszatlan az intelligens on FHIR alkalmazásban, és átadásra kerül az identitás-szolgáltatónak. 

Az intelligens on FHIR proxy ezeket az információkat használja a jogkivonat-válasz mezőinek feltöltéséhez. Az intelligens on *FHIR alkalmazás ezeket a mezőket használhatja annak* szabályozására, hogy melyik beteg kér le és hogyan jeleníti meg az alkalmazást a képernyőn. Az intelligens on FHIR proxy a következő mezőket támogatja:

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

Ezeknek a mezőknek az az célja, hogy útmutatást nyújtsanak az alkalmazáshoz, de nem továbbítanak semmilyen biztonsági információt. Egy intelligens on FHIR alkalmazás figyelmen kívül hagyhatja őket.

Figyelje meg, hogy az intelligens on FHIR app Launcher az oldal alján található **indítási URL-** információkat frissíti. Válassza az **Indítás** lehetőséget a minta alkalmazás elindításához, és ehhez a mintához hasonlóan kell megjelennie:

![INTELLIGENS a FHIR alkalmazásban](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

Vizsgálja meg a jogkivonat válaszát, és tekintse meg, hogy a rendszer hogyan adja át az indítási környezet mezőit az alkalmazásnak.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban konfigurálta a Azure Active Directory SMART on FHIR proxyt. A FHIR készült Azure API-val és az Azure-hoz készült nyílt forráskódú FHIR-kiszolgálóval való intelligens FHIR-alkalmazásokkal kapcsolatos információkért tekintse meg a FHIR Server-minták tárházát a GitHubon:

>[!div class="nextstepaction"]
>[FHIR Server-minták](https://github.com/Microsoft/fhir-server-samples)
