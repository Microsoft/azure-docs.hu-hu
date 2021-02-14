---
title: 'Oktatóanyag: a Microsoft Identity platformot használó Android-alkalmazás létrehozása hitelesítéshez | Azure'
titleSuffix: Microsoft identity platform
description: Ebben az oktatóanyagban egy Android-alkalmazást hoz létre, amely a Microsoft Identity platform használatával jelentkezik be a felhasználókba, és hozzáférési jogkivonatot kap a Microsoft Graph API nevében való meghívásához.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 7d297d96ba764c812a3d4db6d9383122c73cfe31
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103141"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-application"></a>Oktatóanyag: bejelentkezés a felhasználókba és a Microsoft Graph API meghívása Android-alkalmazásból

Ebben az oktatóanyagban egy Android-alkalmazást hoz létre, amely integrálható a Microsoft Identity platformmal, hogy bejelentkezzen a felhasználókba, és szerezzen be egy hozzáférési jogkivonatot a Microsoft Graph API meghívásához.

Az oktatóanyag elvégzése után az alkalmazás elfogadja a személyes Microsoft-fiókok (például a outlook.com, a live.com és mások) bejelentkezési adatait, valamint a munkahelyi vagy iskolai fiókokat bármely olyan vállalattól vagy szervezettől, amely Azure Active Directoryt használ.

Ebben az oktatóanyagban: 

> [!div class="checklist"]
> * Androidos alkalmazás-projekt létrehozása *Android Studio*
> * Az alkalmazás regisztrálása a Azure Portalban
> * Kód hozzáadása a felhasználói bejelentkezés és a kijelentkezés támogatásához
> * Kód hozzáadása a Microsoft Graph API meghívásához
> * Az alkalmazás tesztelése

## <a name="prerequisites"></a>Előfeltételek

* Android Studio 3.5 +

## <a name="how-this-tutorial-works"></a>Az oktatóanyag működése

![Bemutatja, hogyan működik az oktatóanyag által generált minta alkalmazás](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Az oktatóanyagban szereplő alkalmazás bejelentkezik a felhasználók számára, és az Ön nevében kéri le az adatkérést. Ezek az adatok egy olyan védett API-n (Microsoft Graph API) keresztül érhetők el, amely az engedélyezést igényli, és a Microsoft Identity platform védi.

Pontosabban:

* Az alkalmazás egy böngészőn vagy a Microsoft Authenticatoron keresztül fog bejelentkezni a felhasználóba, és Intune Céges portál.
* A végfelhasználó el fogja fogadni az alkalmazás által kért engedélyeket.
* Az alkalmazás egy hozzáférési jogkivonatot fog kiadni a Microsoft Graph API-hoz.
* A hozzáférési jogkivonatot a rendszer a webes API-hoz tartozó HTTP-kérelemben fogja tartalmazni.
* Dolgozza fel a Microsoft Graph választ.

Ez a példa az Androidhoz készült Microsoft Authentication Library (MSAL) használatával valósítja meg a hitelesítést: [com. microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

A MSAL automatikusan megújítja a tokeneket, egyszeri bejelentkezést (SSO) tesz elérhetővé az eszköz más alkalmazásai között, és felügyeli a fiók (oka) t.

Ez az oktatóanyag bemutatja, hogyan használható az Android rendszerhez készült MSAL. Az egyszerűség kedvéért csak egy fiók üzemmódot használ. Összetettebb forgatókönyvek megismeréséhez tekintse meg a befejezett [Work Code mintát](https://github.com/Azure-Samples/ms-identity-android-java/) a githubon.

## <a name="create-a-project"></a>Projekt létrehozása
Ha még nem rendelkezik Android-alkalmazással, kövesse az alábbi lépéseket egy új projekt beállításához.

1. Nyissa meg Android Studio, majd válassza **az új Android Studio projekt indítása** lehetőséget.
2. Válassza az **alaptevékenység** lehetőséget, majd kattintson a **Tovább gombra**.
3. Adjon nevet az alkalmazásnak.
4. Mentse a csomag nevét. Később adja meg a Azure Portal.
5. Módosítsa a nyelvet a **Kotlin** -ből a **Java**-ra.
6. Állítsa a **minimális API** -szintet **API 19** vagy újabb értékre, majd kattintson a **Befejezés** gombra.
7. A Project nézetben a legördülő menüben válassza a **projekt** lehetőséget a forrás-és a nem forrásként szolgáló projektfájlok megjelenítéséhez, majd nyissa meg az **app/Build. gradle** fájlt, és állítsa a következőre: `targetSdkVersion` `28` .

## <a name="integrate-with-the-microsoft-authentication-library"></a>Integrálás a Microsoft hitelesítési függvénytárával

### <a name="register-your-application"></a>Az alkalmazás regisztrálása

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
1. Válassza a **Regisztráció** lehetőséget.
1. A **kezelés** területen válassza   >  **a hitelesítés Hozzáadás a platformhoz**  >  **Android** lehetőséget.
1. Adja meg a projekt csomagjának nevét. Ha letöltötte a kódot, akkor ez az érték `com.azuresamples.msalandroidapp` .
1. Az **Android-alkalmazás konfigurálása** lap **aláírás-kivonatolás** szakaszában válassza a **fejlesztési aláírás kivonatának létrehozása lehetőséget.** és másolja a Főeszköz parancsot a platformhoz való használatra.


     KeyTool.exe a Java Development Kit (JDK) részeként van telepítve. Az OpenSSL eszközt is telepítenie kell, hogy végrehajtsa a parancssori eszközt. További információkért tekintse [meg az Android dokumentációját a kulcs létrehozásához](https://developer.android.com/studio/publish/app-signing#generate-key) .

1. Adja meg a Főeszköz által generált **aláírási kivonatot** .
1. Válassza a **Konfigurálás** lehetőséget, és mentse az **Android-konfiguráció** lapon megjelenő MSAL- **konfigurációt** , hogy később is megadhatja azt az alkalmazás konfigurálásakor.  
1. Válassza a **Kész** lehetőséget.

### <a name="configure-your-application"></a>Az alkalmazás konfigurálása

1. A Android Studio projekt ablaktábláján navigáljon a **app\src\main\res** elemre.
1. Kattintson a jobb gombbal a **res** elemre, és válassza az **új**  >  **könyvtár** lehetőséget. Adja meg `raw` az új könyvtárnév nevet, majd kattintson az **OK** gombra.
1. Az **app**  >  **src**  >  **Main**  >  **res**  >  **RAW** fájlban hozzon létre egy nevű új JSON-fájlt, `auth_config_single_account.json` és illessze be a korábban mentett MSAL-konfigurációt.

    Az átirányítási URI alatt illessze be a következőt:
    ```json
      "account_mode" : "SINGLE",
    ```
    A konfigurációs fájlnak a következő példához hasonlónak kell lennie:
    ```json
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "broker_redirect_uri_registered" : true,
      "account_mode" : "SINGLE",
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

     Ez az oktatóanyag csak azt mutatja be, hogyan konfigurálható egy alkalmazás egy fiók módban. Tekintse meg a dokumentációt, ahol további információkat talál az egyetlen és a [több fiók üzemmódról](./single-multi-account.md) , valamint [az alkalmazás konfigurálásáról](./msal-configuration.md)

4. Az **app**  >  **src**  >  **Main**  >  **AndroidManifest.xmlban** adja hozzá az `BrowserTabActivity` alábbi tevékenységet az alkalmazás törzséhez. Ez a bejegyzés lehetővé teszi, hogy a Microsoft visszahívjon az alkalmazásba a hitelesítés befejezése után:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Helyettesítse be a Azure Portal regisztrált csomag nevét az `android:host=` értékhez.
    Helyettesítse be a Azure Portalban regisztrált kulcs kivonatát az `android:path=` értékhez. Az aláírási kivonat **nem** lehet URL-kódolású. Győződjön meg arról, hogy az `/` aláírás kivonatának elején van egy vezető.
    
    A "csomag neve" helyett a következőhöz `android:host` hasonló értéket kell kinéznie: "com. azuresamples. msalandroidapp".
    Az "aláírási kivonat" értékének a következőhöz `android:path` hasonlóan kell kinéznie: "/1wIqXSqBj7w + h11ZifsnqwgyKrY =".
    
    Ezeket az értékeket az alkalmazás regisztrációjának hitelesítés paneljén is megtalálhatja. Vegye figyelembe, hogy az átirányítási URI a következőhöz hasonlóan fog kinézni: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Míg az aláírás-kivonat az érték végén található URL-cím, az aláírás-kivonat **nem** lehet az értékben kódolt URL-cím `android:path` .

## <a name="use-msal"></a>MSAL használata

### <a name="add-msal-to-your-project"></a>MSAL hozzáadása a projekthez

1. A Android Studio projekt ablakban navigáljon az **app**  >  **src**  >  **Build. gradle** elemhez, és adja hozzá a következőket:

    ```gradle
    repositories{
        jcenter()
    }
    dependencies{
        implementation 'com.microsoft.identity.client:msal:2.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version")
    }
    ```
    [További információ a Microsoft Graph SDK-ról](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>A szükséges importálások

Adja hozzá a következőt az **app**  >  **src**  >  **fő** >  **Java**  >  **com. example (yourapp)**  >  **MainActivity. Java**

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>PublicClientApplication példányának példányai
#### <a name="initialize-variables"></a>Változók inicializálása
```java
private final static String[] SCOPES = {"Files.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
A `MainActivity` osztályon belül tekintse meg a következő onCreate () metódust a MSAL létrehozásához a használatával `SingleAccountPublicClientApplication` .

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount

```java
//When app comes to the foreground, load existing account to determine if user is signed in
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

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
}
```

### <a name="initializeui"></a>initializeUI
Hallgassa meg a gombokat, hívja meg a metódusokat, vagy a hibákat naplózza.
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);

        //Sign in user
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });

        //Interactive
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> A MSAL-ből való kijelentkezés eltávolítja az alkalmazással kapcsolatos összes ismert információt, de a felhasználó továbbra is aktív munkamenettel fog rendelkezni az eszközön. Ha a felhasználó ismét megkísérli a bejelentkezést, láthatják a bejelentkezési felhasználói felületet, de előfordulhat, hogy nem kell újra megadniuk a hitelesítő adataikat, mert az eszköz munkamenete még aktív.

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Az interaktív kérelmekhez használt visszahívás.

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Csendes kérelmekhez használt visszahívás
```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Microsoft Graph API meghívása

A következő kód bemutatja, hogyan hívhatja meg a GraphAPI a Graph SDK használatával.

### <a name="callgraphapi"></a>callGraphAPI

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Felhasználói felület hozzáadása
### <a name="activity"></a>Tevékenység
Ha ki szeretné próbálni a felhasználói felületet az oktatóanyagból, az alábbi módszerek útmutatást nyújtanak a szövegek frissítéséhez és a gombok figyeléséhez.

#### <a name="updateui"></a>updateUI
A bejelentkezési állapot alapján engedélyezheti vagy tilthatja le a gombokat, és beállíthatja a szöveget.
```java
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Módszer a felhasználói felületen lévő szöveg frissítésére a kijelentkezéshez.

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Layout

`activity_main.xml`A gombok és a szövegmezők megjelenítéséhez használandó minta fájl.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Az alkalmazás tesztelése

### <a name="run-locally"></a>Helyi futtatás

Az alkalmazás létrehozása és üzembe helyezése tesztelési eszközön vagy emulátoron. A bejelentkezéshez és az Azure AD-vagy személyes Microsoft-fiókokhoz tartozó jogkivonatok beszerzéséhez be kell tudnia jelentkezni.

A bejelentkezést követően az alkalmazás megjeleníti az Microsoft Graph végpont által visszaadott adatok megjelenítését `/me` .
4. PR
### <a name="consent"></a>Hozzájárulás

Amikor a felhasználó először jelentkezik be az alkalmazásba, a Microsoft Identity a kért engedélyekkel való beleegyezett. Néhány Azure AD-bérlő letiltotta a felhasználói beleegyezik, amely megköveteli, hogy a rendszergazdák az összes felhasználó nevében hozzájárulásukat adjanak. Ennek a forgatókönyvnek a támogatásához létre kell hoznia egy saját bérlőt, vagy rendszergazdai engedélyt kell kapnia.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az [alkalmazás regisztrálása](#register-your-application) lépésben létrehozott alkalmazás-objektumot.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

További információ a védett webes API-kat meghívó mobil alkalmazások létrehozásáról a többrészes forgatókönyvek sorozatában.

> [!div class="nextstepaction"]
> [Forgatókönyv: webes API-kat meghívó mobil alkalmazás](scenario-mobile-overview.md)
