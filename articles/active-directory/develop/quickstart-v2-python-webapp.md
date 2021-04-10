---
title: 'Gyors útmutató: bejelentkezés felvétele a Microsofttal egy Python-webalkalmazásba | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan jelentkezhet be egy Python-webalkalmazás a felhasználók számára, hogyan szerezhet be hozzáférési jogkivonatot a Microsoft Identity platformból, és meghívja a Microsoft Graph API-t.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: fb38140f09fc7c1eb2c40fc02e8c113cbc6f94a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103515"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Gyors útmutató: bejelentkezés felvétele a Microsofttal egy Python-webalkalmazásba

Ebben a rövid útmutatóban egy kód mintát tölt le és futtat, amely bemutatja, hogyan jelentkezhet be egy Python-webalkalmazás a felhasználók számára, és hogyan kérhet hozzáférési jogkivonatot a Microsoft Graph API meghívásához. A személyes Microsoft-fiókkal vagy egy Azure Active Directory (Azure AD-) szervezet fiókkal is bejelentkezhet az alkalmazásba.

Nézze meg, [Hogyan működik a minta](#how-the-sample-works) egy ábrán.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) vagy [Python 3 +](https://www.python.org/downloads/release/python-364/)
- [Lombik](http://flask.pocoo.org/), [lombik-munkamenet](https://pypi.org/project/Flask-Session/), [kérelmek](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
>
> A gyors üzembe helyezési alkalmazás elindításához két lehetőség közül választhat: Express (1. lehetőség) és manuális (2. lehetőség)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Lépjen a <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Alkalmazásregisztrációk</a> gyors üzembe helyezési élményhez.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Az új alkalmazás letöltéséhez és automatikus konfigurálásához kövesse az utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
>
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
> 1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
> 1. Adja meg az alkalmazás **nevét** , például: `python-webapp` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
> 1. A **támogatott fiókok típusai** területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
> 1. Válassza a **Regisztráció** lehetőséget.
> 1. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét későbbi használatra.
> 1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
> 1. Válassza **a platform**  >  **web** hozzáadása lehetőséget.
> 1. Hozzáadás `http://localhost:5000/getAToken` **átirányítási URI**-ként.
> 1. Válassza a **Konfigurálás** lehetőséget.
> 1. A **kezelés** területen jelölje ki a **tanúsítványokat & a titkos kulcsokat**  , és az **ügyfél titkai** szakaszban válassza az **új ügyfél titka** lehetőséget.
> 1. Írja be a kulcs leírását (például az alkalmazás titkos kódjához), hagyja meg az alapértelmezett lejáratot, és válassza a **Hozzáadás** lehetőséget.
> 1. Jegyezze fel az **ügyfél titkos kulcsának** **értékét** későbbi használatra.
> 1. A **kezelés** területen válassza az **API-engedélyek**  >  **Hozzáadás engedélyt**.
> 1. Győződjön meg arról, hogy a **Microsoft API** -k lap van kiválasztva.
> 1. A *gyakran használt Microsoft API* -k szakaszban válassza a **Microsoft Graph** lehetőséget.
> 1. A **delegált engedélyek** szakaszban győződjön meg arról, hogy a megfelelő engedélyek be vannak jelölve: **User. ReadBasic. All**. Ha szükséges, használja a keresőmezőt.
> 1. Kattintson az **engedélyek hozzáadása** gombra.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
>
> A jelen rövid útmutatóban a következő működéshez:
>
> 1. Adja meg a válasz URL-címét `http://localhost:5000/getAToken` .
> 1. Hozzon létre egy ügyfél titkot.
> 1. Adja hozzá Microsoft Graph API user. ReadBasic. All delegált engedélyét.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezzel az attribútummal

#### <a name="step-2-download-your-project"></a>2. lépés: A projekt letöltése
> [!div renderon="docs"]
> [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Töltse le a projektet, és bontsa ki a zip-fájlt egy helyi mappába a gyökérkönyvtárhoz közelebb – például **C:\Azure-Samples**
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>3. lépés: az alkalmazás konfigurálása
>
> 1. Csomagolja ki a zip-fájlt egy helyi mappába a gyökérmappa közelében (például: **C:\Azure-Samples**)
> 1. Ha integrált fejlesztési környezetet használ, nyissa meg a mintát a kedvenc IDE (opcionális).
> 1. Nyissa meg a **app_config.** a fájlt, amely megtalálható a gyökérkönyvtárban, és cserélje le a következő kódrészletre:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Ahol:
>
> - `Enter_the_Application_Id_here` – ez a regisztrált alkalmazás alkalmazásazonosítója.
> - `Enter_the_Client_Secret_Here`– a **tanúsítványokban létrehozott &** **titkos kulcs** a regisztrált alkalmazáshoz.
> - `Enter_the_Tenant_Name_Here` – a regisztrált alkalmazás **címtár-(bérlői) azonosítójának** értéke.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>3. lépés: a kód mintájának futtatása

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>4. lépés: a kód mintájának futtatása

1. Telepítenie kell a MSAL Python Library, a lombik Framework Flask-Sessions a kiszolgálóoldali munkamenet-kezeléshez és a pip-t használó kérelmekhez a következő módon:

    ```Shell
    pip install -r requirements.txt
    ```

2. App.py futtatása a rendszerhéjból vagy parancssorból:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Ez a rövid útmutató alkalmazás egy ügyfél titkos kulcsát használja, amely bizalmas ügyfélként azonosítja magát. Mivel az ügyfél titkos kulcsát egyszerű szövegként adja hozzá a Project-fájlokhoz, biztonsági okokból javasolt a tanúsítvány használata az ügyfél titkos kulcsa helyett, mielőtt az alkalmazást éles alkalmazásként venné fontolóra. A tanúsítványok használatáról a következő [utasításokban](./active-directory-certificate-credentials.md)talál további információt.

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése
![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>MSAL beolvasása
A MSAL az a könyvtár, amellyel a felhasználók bejelentkezhetnek, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kérhetnek.
A pip használatával hozzáadhat MSAL Pythont az alkalmazáshoz.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Az MSAL inicializálása
A MSAL Pythonra mutató hivatkozást úgy adhatja hozzá, hogy hozzáadja a következő kódot a fájl elejéhez, ahol a MSAL fogja használni:

```Python
import msal
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

További információ a többrészes forgatókönyvek felhasználói számára bejelentkező webalkalmazásokról.

> [!div class="nextstepaction"]
> [Forgatókönyv: a felhasználók által bejelentkezett webalkalmazás](scenario-web-app-sign-user-overview.md)
