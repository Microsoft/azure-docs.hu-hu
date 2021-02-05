---
title: 'Gyors útmutató: bejelentkezés felvétele a Microsofttal Android-alkalmazásba | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogy az Android-alkalmazások hogyan hívhatnak meg olyan API-t, amely a Microsoft Identity platform által kiadott hozzáférési jogkivonatokat igényel.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/15/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.openlocfilehash: 49c69a79b4841f13623c9e6ab3daea72dd7fde26
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583654"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Rövid útmutató: Felhasználók bejelentkeztetése és a Microsoft Graph API meghívása Android-alkalmazásokból

Ebben a rövid útmutatóban egy kód mintát tölt le és futtat, amely bemutatja, hogyan jelentkezhet be egy Android-alkalmazás a felhasználók számára, és hogyan kérhet hozzáférési jogkivonatot a Microsoft Graph API meghívásához. 

Nézze meg, [Hogyan működik a minta](#how-the-sample-works) egy ábrán.

Az alkalmazásokat a Azure Active Directory alkalmazásnak kell képviselnie, hogy a Microsoft Identity platform jogkivonatokat biztosítson az alkalmazásnak.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Android Studio
* Android 16 +

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy az ebben a rövid útmutatóban szereplő kód minta működjön, adjon hozzá egy **átirányítási URI** -t, amely kompatibilis a hitelesítési közvetítővel.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-android/green-check.png) Az alkalmazás már konfigurálva van ezekkel az attribútumokkal
>
> ### <a name="step-2-download-the-project"></a>2. lépés: A projekt letöltése
> [!div class="sxs-lookup" renderon="portal"]
> Futtassa a projektet Android Studio használatával.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projektet az alkalmazás tulajdonságainak értékével konfiguráltuk, és készen áll a futtatásra.
> A minta alkalmazás az **Egyfiókos mód** képernyőn indul el. Alapértelmezés szerint az alapértelmezett hatókör, a **User. Read**, amelyet a rendszer akkor használ, amikor a Microsoft Graph API-hívás során beolvassa a saját profil adatait. Alapértelmezés szerint a Microsoft Graph API-hívás URL-címe van megadva. Ha szeretné, mindkettőt módosíthatja.
>
> ![MSAL minta alkalmazás, amely egyetlen és több fiók használatát mutatja be](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Az alkalmazás menü használatával válthat egy és több fiók közötti mód között.
>
> Önálló fiók módban jelentkezzen be egy munkahelyi vagy otthoni fiókkal:
>
> 1. Válassza a **Graph-adatok interaktív beolvasása** lehetőséget a felhasználó hitelesítő adatainak megadásához. A képernyő alján megjelenik a Microsoft Graph API felé irányuló hívás kimenete.
> 2. Miután bejelentkezett, válassza a **Graph-adatok csendes beolvasása** lehetőséget, hogy meghívja a Microsoft Graph API-t anélkül, hogy újra kellene kérnie a felhasználót a hitelesítő adatok megadására. A képernyő alján megjelenik a Microsoft Graph API felé irányuló hívás kimenete.
>
> Több fiókos módban is megismételheti ugyanezeket a lépéseket.  Emellett eltávolíthatja a bejelentkezett fiókot is, amely eltávolítja az adott fiók gyorsítótárazott jogkivonatait is.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>1. lépés: a minta alkalmazás beszerzése
>
> [Töltse le a kódot](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).
>
> ## <a name="step-2-run-the-sample-app"></a>2. lépés: a minta alkalmazás futtatása
>
> Válassza ki emulátorát vagy fizikai eszközét Android Studio **elérhető eszközök** legördülő menüből, és futtassa az alkalmazást.
>
> A minta alkalmazás az **Egyfiókos mód** képernyőn indul el. Alapértelmezés szerint az alapértelmezett hatókör, a **User. Read**, amelyet a rendszer akkor használ, amikor a Microsoft Graph API-hívás során beolvassa a saját profil adatait. Alapértelmezés szerint a Microsoft Graph API-hívás URL-címe van megadva. Ha szeretné, mindkettőt módosíthatja.
>
> ![MSAL minta alkalmazás, amely egyetlen és több fiók használatát mutatja be](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Az alkalmazás menü használatával válthat egy és több fiók közötti mód között.
>
> Önálló fiók módban jelentkezzen be egy munkahelyi vagy otthoni fiókkal:
>
> 1. Válassza a **Graph-adatok interaktív beolvasása** lehetőséget a felhasználó hitelesítő adatainak megadásához. A képernyő alján megjelenik a Microsoft Graph API felé irányuló hívás kimenete.
> 2. Miután bejelentkezett, válassza a **Graph-adatok csendes beolvasása** lehetőséget, hogy meghívja a Microsoft Graph API-t anélkül, hogy újra kellene kérnie a felhasználót a hitelesítő adatok megadására. A képernyő alján megjelenik a Microsoft Graph API felé irányuló hívás kimenete.
>
> Több fiókos módban is megismételheti ugyanezeket a lépéseket.  Emellett eltávolíthatja a bejelentkezett fiókot is, amely eltávolítja az adott fiók gyorsítótárazott jogkivonatait is.

## <a name="how-the-sample-works"></a>A minta működése
![A minta alkalmazás képernyőképe](media/quickstart-v2-android/android-intro.svg)


A kód töredékekre van rendezve, amelyek bemutatják, hogyan írhat egyetlen és több fiókot MSAL-alkalmazásba. A kód fájljai a következőképpen vannak rendszerezve:

| Fájl  | Útmutató ehhez:  |
|---------|---------|
| MainActivity | A felhasználói felület kezelése |
| MSGraphRequestWrapper  | A Microsoft Graph API meghívása a MSAL által biztosított jogkivonat használatával |
| MultipleAccountModeFragment  | Inicializál egy többfiókos alkalmazást, betölti a felhasználói fiókot, és lekéri a tokent a Microsoft Graph API meghívásához |
| SingleAccountModeFragment | Inicializál egy egyfiókos alkalmazást, betölti a felhasználói fiókot, és lekéri a tokent a Microsoft Graph API meghívásához |
| res/auth_config_multiple_account.jsbekapcsolva  | A több fiók konfigurációs fájlja |
| res/auth_config_single_account.jsbekapcsolva  | Az egyetlen fiók konfigurációs fájlja |
| Gradle-parancsfájlok/Build. Range (modul: alkalmazás) | A MSAL-függvénytár függőségei itt lesznek hozzáadva |

Most megtekintjük ezeket a fájlokat részletesebben, és megvizsgáljuk a MSAL-specifikus kódokat.

### <a name="adding-msal-to-the-app"></a>MSAL hozzáadása az alkalmazáshoz

A MSAL ([com. microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) az a könyvtár, amellyel a felhasználók bejelentkezhetnek, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kérhetnek. Gradle 3.0 + telepíti a könyvtárat, amikor hozzáadja a következőt a **Gradle-szkriptek**  >  **Build. Gradle (modul: alkalmazás)** alatt a **függőségek** területen:

```gradle
implementation 'com.microsoft.identity.client:msal:2.+'
```

Ezt a Build. gradle (modul: alkalmazás) projektben láthatja:

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:2.+'
    ...
}
```

Ez arra utasítja a Gradle, hogy töltsön le és építsen MSAL a Maven Central-ból.

### <a name="msal-imports"></a>MSAL-importálások

A MSAL-könyvtárhoz kapcsolódó importálások `com.microsoft.identity.client.*` .  Láthatja például, hogy `import com.microsoft.identity.client.PublicClientApplication;` melyik a `PublicClientApplication` osztály névtere, amely a nyilvános ügyfélalkalmazás.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment. Java

Ez a fájl bemutatja, hogyan hozhat létre egyetlen MSAL alkalmazást, és hogyan hívhat meg Microsoft Graph API-t.

Az egyfiókos alkalmazásokat csak egyetlen felhasználó használja.  Előfordulhat például, hogy csak egy fiókkal rendelkezik, amelybe bejelentkezett a leképezési alkalmazásba.

#### <a name="single-account-msal-initialization"></a>Egy fiók MSAL inicializálása

A-ben a-ben `auth_config_single_account.json` `onCreateView()` egyetlen fiók `PublicClientApplication` jön létre a fájlban tárolt konfigurációs adatok használatával `auth_config_single_account.json` .  Így inicializálhatja a MSAL-függvénytárat egy egyfiókos MSAL-alkalmazásban való használatra:

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                 * This test app assumes that the app is only going to support one account.
                 * This requires "account_mode" : "SINGLE" in the config json file.
                 **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>Bejelentkezés felhasználóként

A-ben `SingleAccountModeFragment.java` a felhasználónak bejelentkezni kívánt kód a `initializeUI()` `signInButton` kattintson a kezelő elemre.

`signIn()`A tokenek beszerzésére tett kísérlet előtt hívja meg a hívást. `signIn()` úgy viselkedik, mintha a `acquireToken()` meghívásra kerül, ami interaktív kérést eredményez a felhasználónak a bejelentkezéshez.

A felhasználó bejelentkezése aszinkron művelet. A rendszer visszahívást küld, amely meghívja a Microsoft Graph API-t, és a felhasználó bejelentkezése után frissíti a felhasználói FELÜLETET:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Felhasználó kijelentkezése

A alkalmazásban `SingleAccountModeFragment.java` a felhasználó kijelentkezésére szolgáló kód a `initializeUI()` `signOutButton` kattintson a kezelő elemre.  A felhasználó aláírása aszinkron művelet. A felhasználó aláírása törli a fiókhoz tartozó jogkivonat-gyorsítótárat is. A felhasználói fiók kijelentkezése után a rendszer visszahívást hoz létre a felhasználói felület frissítéséhez:

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>Jogkivonat interaktív vagy csendes beszerzése

Ha a lehető legkevesebb kérést szeretné bemutatni a felhasználónak, akkor a rendszer általában csendes esetben lekérdezi a tokent. Ha hiba merül fel, próbálja meg interaktív módon beolvasni a tokent. Az alkalmazás első meghívásakor az `signIn()` hatékonyan hívja a `acquireToken()` -t, amely felszólítja a felhasználót a hitelesítő adatok megadására.

Bizonyos esetekben előfordulhat, hogy a felhasználónak meg kell adnia a fiókját, meg kell adnia a hitelesítő adatait, vagy jóvá kell hagynia az alkalmazás által kért engedélyeket:

* Amikor a felhasználó először jelentkezik be az alkalmazásba
* Ha a felhasználó alaphelyzetbe állítja a jelszavát, meg kell adnia a hitelesítő adataikat
* Ha a beleegyezést visszavonják
* Ha az alkalmazás kifejezetten beleegyezést igényel
* Ha az alkalmazás első alkalommal kér hozzáférést egy erőforráshoz
* Ha MFA-vagy más feltételes hozzáférési szabályzatokra van szükség

A jogkivonat interaktív beszerzéséhez használt kód, amely a felhasználót érintő felhasználói FELÜLETtel rendelkezik, a (z), a (z)  `SingleAccountModeFragment.java` `initializeUI()` `callGraphApiInteractiveButton` kezelője elemre kattintva:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Ha a felhasználó már be van jelentkezve, `acquireTokenSilentAsync()` lehetővé teszi az alkalmazások számára, hogy a jogkivonatokat csendes módon igényeljenek `initializeUI()` , a `callGraphApiSilentButton` Click Handler:

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Fiók betöltése

A fiók betöltésére szolgáló kód a `SingleAccountModeFragment.java` következő címen található: `loadAccount()` .  A felhasználó fiókjának betöltése aszinkron művelet, ezért a fiók betöltésének, módosításának vagy hibájának beolvasásakor visszahívást kell adni a MSAL.  A következő kód is kezeli `onAccountChanged()` , amely akkor fordul elő, ha egy fiókot eltávolítanak, a felhasználó egy másik fiókra vált, és így tovább.

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>Hívás Microsoft Graph

Ha egy felhasználó be van jelentkezve, a Microsoft Graph hívása egy HTTP-kérésen keresztül történik, `callGraphAPI()` amely a ben van definiálva `SingleAccountModeFragment.java` . Ez a függvény egy burkoló, amely leegyszerűsíti a mintát olyan feladatok elvégzésével, mint például a hozzáférési jogkivonat lekérése `authenticationResult` és a hívás MSGraphRequestWrapper való becsomagolása, valamint a hívás eredményének megjelenítése.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.jsbekapcsolva

Ez egy olyan MSAL-alkalmazás konfigurációs fájlja, amely egyetlen fiókot használ.

A mezők magyarázatát az [androidos MSAL konfigurációs fájljának megismerése ](msal-configuration.md) című részben találja.

Figyelje meg a jelenlétét `"account_mode" : "SINGLE"` , amely az alkalmazást egyetlen fiók használatára konfigurálja.

`"client_id"` előre konfigurálva van, hogy a Microsoft által fenntartott alkalmazás-objektum regisztrációját használja.
`"redirect_uri"`előre konfigurálva van, hogy a kód mintájában megadott aláíró kulcsot használja.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment. Java

Ez a fájl bemutatja, hogyan hozhat létre több fiókból álló MSAL-alkalmazást, és hogyan hívhat meg Microsoft Graph API-t.

Egy több fiókból álló alkalmazás például egy olyan levelező alkalmazás, amely lehetővé teszi, hogy több felhasználói fiókkal, például munkahelyi fiókkal és személyes fiókkal is működjön.

#### <a name="multiple-account-msal-initialization"></a>Több fiók MSAL inicializálása

A fájlban a alkalmazásban `MultipleAccountModeFragment.java` `onCreateView()` egy több Account app Object ( `IMultipleAccountPublicClientApplication` ) jön létre a következő helyen tárolt konfigurációs adatokkal `auth_config_multiple_account.json file` :

```java
// Creates a PublicClientApplication object with res/raw/auth_config_multiple_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccounts();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

A létrehozott `MultipleAccountPublicClientApplication` objektum egy Class tag-változóban van tárolva, így a MSAL-könyvtárral folytatott kommunikációhoz használható a tokenek beszerzése és a felhasználói fiók betöltése és eltávolítása.

#### <a name="load-an-account"></a>Fiók betöltése

A több fiókból származó alkalmazások általában `getAccounts()` a MSAL-műveletekhez használandó fiókot hívják meg. A fiók betöltéséhez szükséges kód a `MultipleAccountModeFragment.java` következő fájlban található:  `loadAccounts()` .  A felhasználó fiókjának betöltése aszinkron művelet. Így a visszahívás a fiók betöltését, változásait vagy hiba esetén felmerülő helyzeteket kezeli.

```java
/**
 * Load currently signed-in accounts, if there's any.
 **/
private void loadAccounts() {
    if (mMultipleAccountApp == null) {
        return;
    }

    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>Jogkivonat interaktív vagy csendes beszerzése

Bizonyos esetekben előfordulhat, hogy a felhasználónak meg kell adnia a fiókját, meg kell adnia a hitelesítő adatait, vagy jóvá kell hagynia az alkalmazás által kért engedélyeket:

* Az első alkalommal, amikor felhasználók bejelentkeznek az alkalmazásba
* Ha a felhasználó alaphelyzetbe állítja a jelszavát, meg kell adnia a hitelesítő adataikat
* Ha a beleegyezést visszavonják
* Ha az alkalmazás kifejezetten beleegyezést igényel
* Ha az alkalmazás első alkalommal kér hozzáférést egy erőforráshoz
* Ha MFA-vagy más feltételes hozzáférési szabályzatokra van szükség

A több fiókból álló alkalmazások általában interaktív módon szerzik be a jogkivonatokat, amely a felhasználót tartalmazó felhasználói FELÜLETtel van ellátva `acquireToken()` .  A jogkivonat interaktív beolvasásához szükséges kód a `MultipleAccountModeFragment.java` fájljában `initializeUI()` , a `callGraphApiInteractiveButton` Click Handler:

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Az alkalmazásoknak nem kell minden alkalommal bejelentkezniük, amikor jogkivonatot kérnek. Ha a felhasználó már be van jelentkezve, `acquireTokenSilentAsync()` lehetővé teszi, hogy az alkalmazások a felhasználó értesítése nélkül igényeljenek jogkivonatokat a fájlban látható módon `MultipleAccountModeFragment.java` , a `initializeUI()` kattintson a `callGraphApiSilentButton` kezelőben:

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
    accountList.get(accountListSpinner.getSelectedItemPosition()),
    AUTHORITY,
    getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>Fiók eltávolítása

A fiók eltávolításához és a fiókhoz tartozó gyorsítótárazott jogkivonatokhoz tartozó kód a `MultipleAccountModeFragment.java` `initializeUI()` fiók eltávolítása gomb kezelőjének fájljában található. Mielőtt eltávolít egy fiókot, szüksége lesz egy fiók objektumra, amelyet a (z) és a MSAL metódusokból szerezhet be `getAccounts()` `acquireToken()` . Mivel a fiókok eltávolítása aszinkron művelet, a visszahívás a `onRemoved` felhasználói felület frissítésére szolgál.

```java
/**
 * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
 **/
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccounts();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.jsbekapcsolva

Ez egy olyan MSAL-alkalmazás konfigurációs fájlja, amely több fiókot használ.

Az [androidos MSAL konfigurációs fájljának megismeréséhez ](msal-configuration.md) tekintse meg a különböző mezők magyarázatát.

A konfigurációs fájl [auth_config_single_account.jstól](#auth_config_single_accountjson) eltérően ez a konfigurációs fájl nem `"account_mode" : "MULTIPLE"` `"account_mode" : "SINGLE"` azért van, mert ez egy több fiókból álló alkalmazás.

`"client_id"` előre konfigurálva van, hogy a Microsoft által fenntartott alkalmazás-objektum regisztrációját használja.
`"redirect_uri"`előre konfigurálva van, hogy a kód mintájában megadott aláíró kulcsot használja.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

Lépjen be az Android-oktatóanyagba, amelyben létrehoz egy olyan Android-alkalmazást, amely a Microsoft Identity platform hozzáférési jogkivonatát kapja, és a használatával hívja meg a Microsoft Graph API-t.

> [!div class="nextstepaction"]
> [Oktatóanyag: bejelentkezés a felhasználókba és a Microsoft Graph meghívása Android-alkalmazásból](tutorial-v2-android.md)
