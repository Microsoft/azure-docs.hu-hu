---
title: 'Rövid útmutató: Microsoft Graph hívása Java-démonból | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan szerezhet be egy Java-alkalmazás hozzáférési jogkivonatot, és hogyan hívhat meg a Microsoft Identity platform-végpont által védett API-t az alkalmazás saját identitásával
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/22/2021
ms.author: nacanuma
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 196b80a704b8a270a4cbb7d3505d5f9be1e23479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99820320"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-java-console-app-using-apps-identity"></a>Gyors útmutató: token beszerzése és Microsoft Graph API meghívása egy Java-konzol alkalmazásból az alkalmazás identitásával

Ebben a rövid útmutatóban egy kódrészletet tölt le és futtat, amely bemutatja, hogyan szerezhet be egy Java-alkalmazás hozzáférési tokent az alkalmazás identitásával a Microsoft Graph API meghívásához és a címtárban lévő [felhasználók listájának](/graph/api/user-list) megjelenítéséhez. A kód minta azt mutatja be, hogy a felügyelet nélküli feladatok vagy a Windows-szolgáltatások alkalmazás-identitással futtathatók a felhasználó identitása helyett. 

> [!div renderon="docs"]
> ![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-java-daemon/java-console-daemon.svg)

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához a következőkre lesz szüksége:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 vagy újabb
- [Maven](https://maven.apache.org/)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése

> [!div renderon="docs" class="sxs-lookup"]
>
> A gyors üzembe helyezési alkalmazás elindításához két lehetőség közül választhat: Express (1. lehetőség) és manuális (2. lehetőség)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Lépjen a <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaDaemonQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Alkalmazásregisztrációk</a> gyors üzembe helyezési élményhez.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
> 1. Adja meg az alkalmazás **nevét** , például: `Daemon-console` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
> 1. Válassza a **Regisztráció** lehetőséget.
> 1. A **kezelés** területen válassza a  **tanúsítványok & Secrets** elemet.
> 1. Az **ügyfél** titkos kulcsa területen válassza az **új ügyfél titka** elemet, adjon meg egy nevet, majd válassza a **Hozzáadás** lehetőséget. A titkos értéket egy biztonságos helyen rögzítheti egy későbbi lépésben való használatra.
> 1. A **kezelés** területen válassza az **API-engedélyek**  >  **Hozzáadás engedélyt**. Válassza a **Microsoft Graph** lehetőséget.
> 1. Válassza ki az **alkalmazás engedélyeit**.
> 1. A **felhasználó** csomópont alatt válassza a **felhasználó. olvasás. mind** lehetőséget, majd kattintson az **engedélyek hozzáadása** lehetőségre.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-quickstart-app"></a>A Gyorsindítás alkalmazás letöltése és konfigurálása
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>1. lépés: az alkalmazás konfigurálása Azure Portalban
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, létre kell hoznia egy ügyfél-titkos kulcsot, és hozzá kell adnia Graph API **felhasználóját. Read. All** Application engedély.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-netcore-daemon/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-java-project"></a>2. lépés: a Java-projekt letöltése

> [!div renderon="docs"]
> [A Java Daemon-projekt letöltése](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-the-java-project"></a>3. lépés: a Java-projekt konfigurálása
>
> 1. Csomagolja ki a zip-fájlt egy helyi mappába a lemez gyökerének közelében (például: *C:\Azure-Samples*).
> 1. Navigáljon az almappában **msal-Client-hitelesítőadat-Secret**.
> 1. Szerkessze a *src\main\resources\application.properties* , és cserélje le a mezők értékeit, `AUTHORITY` `CLIENT_ID` és `SECRET` a következő kódrészletet:
>
>    ```
>    AUTHORITY=https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/
>    CLIENT_ID=Enter_the_Application_Id_Here
>    SECRET=Enter_the_Client_Secret_Here
>    ```
>    Ahol:
>    - `Enter_the_Application_Id_Here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.
>    - `Enter_the_Tenant_Id_Here` – cserélje le ezt az értéket a **bérlői azonosító** vagy a **bérlő nevére** (például contoso.microsoft.com).
>    - `Enter_the_Client_Secret_Here` – cserélje le ezt az értéket az 1. lépésben létrehozott ügyfél-titkos kulcsra.
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítójának**, a **bérlői azonosító** értékének megkereséséhez lépjen az alkalmazás **Áttekintés** lapjára a Azure Portal. Új kulcs létrehozásához nyissa meg a **tanúsítványok & titkok** lapot.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>3. lépés: rendszergazdai engedély

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>4. lépés: rendszergazdai engedély

Ha ezen a ponton próbálja meg futtatni az alkalmazást, *HTTP 403-Tiltott* hibaüzenetet kap: `Insufficient privileges to complete the operation` . Ez a hiba azért fordul elő, mert bármely *csak alkalmazásra vonatkozó engedélyhez* rendszergazdai hozzájárulás szükséges: a címtár globális rendszergazdájának hozzájárulást kell adnia az alkalmazáshoz. Válassza ki az alábbi lehetőségek egyikét a szerepkörtől függően:

##### <a name="global-tenant-administrator"></a>Globális bérlői rendszergazda

> [!div renderon="docs"]
> Ha Ön globális bérlői rendszergazda, nyissa meg a Azure Portal **Alkalmazásregisztrációk** **API-engedélyek** lapját, és válassza a rendszergazdai jóváhagyás megadása a (z) **{bérlő neve} számára** lehetőséget (ahol a (z) {bérlő neve} a címtár neve).

> [!div renderon="portal" class="sxs-lookup"]
> Ha Ön globális rendszergazda, az **API-engedélyek** lapon válassza **a rendszergazdai jóváhagyás megadása Enter_the_Tenant_Name_Herehoz** lehetőséget.
> > [!div id="apipermissionspage"]
> > [Ugrás az API-engedélyek oldalra]()

##### <a name="standard-user"></a>Általános jogú felhasználó

Ha Ön a bérlő általános jogú felhasználója, kérje meg a globális rendszergazdát, hogy adja meg az alkalmazáshoz tartozó rendszergazdai jóváhagyást. Ehhez adja meg a következő URL-címet a rendszergazdának:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Ahol:
>> * `Enter_the_Tenant_Id_Here` – cserélje le ezt az értéket a **bérlői azonosító** vagy a **bérlő nevére** (például contoso.microsoft.com).
>> * `Enter_the_Application_Id_Here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>4. lépés: az alkalmazás futtatása

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>5. lépés: az alkalmazás futtatása

A mintát közvetlenül is tesztelheti az IDE-ből származó ClientCredentialGrant. Java fő metódusának futtatásával.

A rendszerhéjból vagy parancssorból:

```
$ mvn clean compile assembly:single
```

Ez létrehozza a msal-Client-Credential-Secret-1.0.0. jar fájlt a/Targets könyvtárban. Futtassa ezt az alábbi Java-végrehajtható fájllal:

```
$ java -jar msal-client-credential-secret-1.0.0.jar
```

A futtatást követően az alkalmazásnak meg kell jelennie a konfigurált bérlőben lévő felhasználók listájának.


> [!IMPORTANT]
> Ez a rövid útmutató alkalmazás egy ügyfél titkos kulcsát használja, amely bizalmas ügyfélként azonosítja magát. Mivel az ügyfél titkos kulcsát egyszerű szövegként adja hozzá a Project-fájlokhoz, biztonsági okokból javasolt a tanúsítvány használata az ügyfél titkos kulcsa helyett, mielőtt az alkalmazást éles alkalmazásként venné fontolóra. A tanúsítványok használatával kapcsolatos további információkért tekintse meg ezeket az [utasításokat](https://github.com/Azure-Samples/ms-identity-java-daemon/tree/master/msal-client-credential-certificate) a minta ugyanazon GitHub-tárházában, de a második mappában **msal-Client-hitelesítőadat-Certificate**.

## <a name="more-information"></a>További információ

### <a name="msal-java"></a>MSAL Java

A [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java) az a könyvtár, amellyel a felhasználók bejelentkezhetnek, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kérhetnek. A leírtak szerint ez a rövid útmutató a jogkivonatokat az alkalmazás saját identitásának használatával kéri le a delegált engedélyek helyett. Az ebben az esetben használt hitelesítési folyamat az *[ügyfél hitelesítő adatai OAuth folyamat](v2-oauth2-client-creds-grant-flow.md)*. A MSAL Java Daemon-alkalmazásokkal való használatáról [ebben a cikkben](scenario-daemon-overview.md)talál további információt.

Vegyen fel MSAL4J az alkalmazásba a Maven vagy a Gradle használatával a függőségek kezeléséhez azáltal, hogy az alábbi módosításokat hajtja végre az alkalmazás pom.xml (Maven) vagy a Build. Gradle (Gradle) fájlon.

pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

A Build. gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Vegyen fel egy hivatkozást a Java-MSAL a következő kód hozzáadásával a fájl elejére, ahol a MSAL4J fogja használni:

```Java
import com.microsoft.aad.msal4j.*;
```

Ezután inicializálja az MSAL-t az alábbi kóddal:

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

> | Ahol: |Leírás |
> |---------|---------|
> | `CLIENT_SECRET` | Az Azure Portalon az alkalmazáshoz létrehozott ügyfél-titkos kulcs. |
> | `CLIENT_ID` | Az Azure Portalon regisztrált alkalmazás **alkalmazásazonosítója (ügyfél-azonosítója)**. Ezt az értéket az alkalmazás **Áttekintés** oldalán találja az Azure Portalon. |
> | `AUTHORITY`    | A felhasználó által hitelesítendő STS-végpont. A nyilvános felhő esetében általában a (z) `https://login.microsoftonline.com/{tenant}` {bérlő} a bérlő vagy a bérlői azonosító neve.|

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Ha a tokent az alkalmazás identitásával szeretné kérelmezni, használja a következő `acquireToken` metódust:

```Java
IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
```

> |Ahol:| Leírás |
> |---------|---------|
> | `SCOPE` | A kért hatóköröket tartalmazza. A bizalmas ügyfelek esetében ennek a hasonló formátumot kell használnia, ha `{Application ID URI}/.default` azt jelzi, hogy a kért hatókörök a Azure Portalban beállított alkalmazás-objektumban statikusan vannak meghatározva (Microsoft Graph, `{Application ID URI}` pont – `https://graph.microsoft.com` ). Az egyéni webes API-k esetében az `{Application ID URI}` Azure portalon **ALKALMAZÁSREGISZTRÁCIÓK** az **API közzététele** című részében van definiálva.|

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

A Daemon-alkalmazásokkal kapcsolatos további tudnivalókért tekintse meg a forgatókönyv kezdőlapját.

> [!div class="nextstepaction"]
> [Webes API-kat meghívó Daemon-alkalmazás](scenario-daemon-overview.md)
