---
title: 'Oktatóanyag: a Microsoft Authentication Library (MSAL) használata az Androidhoz – megosztott eszköz mód használata | Azure'
titleSuffix: Microsoft identity platform
description: Ebből az oktatóanyagból megtudhatja, hogyan készítse elő az Android-eszközök megosztott módban történő futtatását, és futtasson egy első sorból álló munkavégző alkalmazást.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6a173ed4dae9237d8aae991c943817ed70246eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649055"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Oktatóanyag: megosztott eszköz üzemmód használata Android-alkalmazásokban

Ebben az oktatóanyagban az Android-fejlesztők és a Azure Active Directory (Azure AD) bérlői rendszergazdák megismerhetik a kód, a hitelesítő alkalmazás és a bérlő beállításait, amelyek szükségesek az Android-alkalmazások megosztott eszköz üzemmódjának engedélyezéséhez.

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * Mintakód letöltése
> * Megosztott eszköz üzemmód engedélyezése és észlelése
> * Egy vagy több fiók üzemmódjának észlelése
> * Felhasználói kapcsoló észlelése, globális bejelentkezés és kijelentkezés engedélyezése
> * Bérlő beállítása és az alkalmazás regisztrálása a Azure Portal
> * Android-eszköz beállítása megosztott eszköz módban
> * A mintaalkalmazás futtatása

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="developer-guide"></a>Fejlesztői útmutató

Az oktatóanyag ezen szakasza fejlesztői útmutatást nyújt a megosztott eszközök üzemmódjának megvalósításához egy Android-alkalmazásban a Microsoft Authentication Library (MSAL) használatával. Tekintse meg a [MSAL Android-oktatóanyagot](./tutorial-v2-android.md) , amelyből megtudhatja, hogyan integrálhatja a MSAL az Android-alkalmazással, hogyan jelentkezhet be egy felhasználó, meghívhatja a Microsoft Graphot, és kijelentkezhet

### <a name="download-the-sample"></a>A minta letöltése

A [minta alkalmazás](https://github.com/Azure-Samples/ms-identity-android-java/) klónozása a githubról. A minta [egy vagy több fiókos módban](./single-multi-account.md)is működhet.

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>A MSAL SDK hozzáadása a helyi Maven-tárházhoz

Ha nem használja a minta alkalmazást, adja hozzá a MSAL-függvénytárat a Build. gradle fájl függőségéhez, például:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Az alkalmazás konfigurálása megosztott eszköz üzemmód használatára

A konfigurációs fájl beállításával kapcsolatos további információkért tekintse meg a [konfigurációs dokumentációt](./msal-configuration.md) .

Állítsa be a beállítást a `"shared_device_mode_supported"` `true` MSAL konfigurációs fájljába.

Előfordulhat, hogy nem tervezi több fiókos üzemmód használatát. Ez akkor lehet lehetséges, ha nem megosztott eszközt használ, és a felhasználó egyszerre több fiókkal is bejelentkezhet az alkalmazásba. Ha igen, állítsa a következőre: `"account_mode"` `"SINGLE"` . Ez garantálja, hogy az alkalmazás mindig megkapja az alkalmazást `ISingleAccountPublicClientApplication` , és jelentősen leegyszerűsíti a MSAL-integrációt. Az alapértelmezett értéke `"account_mode"` `"MULTIPLE"` , ezért fontos, hogy módosítsa ezt az értéket a konfigurációs fájlban, ha a `"single account"` módot használja.

Íme egy példa arra a auth_config.jsfájlra, amelyet a minta alkalmazás **alkalmazás** > **fő** > **res** > **RAW** könyvtára tartalmaz:

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Megosztott eszköz üzemmódjának észlelése

A megosztott eszköz mód lehetővé teszi, hogy az Android-eszközöket több alkalmazott számára is meg lehessen osztani, miközben biztosítja az eszköz Microsoft Identity-alapú felügyeletét. Az alkalmazottak bejelentkezhetnek az eszközökre, és gyorsan hozzáférhetnek az ügyfelek adataihoz. Ha végzett a váltással vagy a feladattal, egyetlen kattintással kijelentkezhetnek a megosztott eszközön lévő összes alkalmazásból, és az eszköz azonnal készen áll a következő alkalmazott használatára.

Ezzel a paranccsal `isSharedDevice()` megállapíthatja, hogy egy alkalmazás egy megosztott eszköz módban lévő eszközön fut-e. Az alkalmazás ennek a jelzőnek a használatával állapíthatja meg, hogy ennek megfelelően kell-e módosítania az UX-t.

Az alábbi kódrészletből megtudhatja, hogyan használhatja `isSharedDevice()` .  Ez a `SingleAccountModeFragment` minta alkalmazás osztálya:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ? "Shared" : "Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>A PublicClientApplication objektum inicializálása

Ha `"account_mode":"SINGLE"` a MSAL konfigurációs fájlban be van állítva, akkor a visszaadott alkalmazás objektumát biztonságosan elvégezheti `ISingleAccountPublicCLientApplication` .

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config,
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});
```

### <a name="detect-single-vs-multiple-account-mode"></a>Egyetlen vagy több fiók üzemmódjának észlelése

Ha olyan alkalmazást ír, amely egy megosztott eszközön csak az első vonalas feldolgozók számára lesz használatban, javasoljuk, hogy az alkalmazás csak az egyfiókos üzemmódot támogassa. Ez magában foglalja a legtöbb olyan alkalmazást, amely a feladathoz tartozik, például a Medical Records-alkalmazások, a számlafogadó-alkalmazások és a legtöbb üzletági alkalmazás. Ez leegyszerűsíti a fejlesztést, mivel az SDK számos funkcióját nem kell elfogadnia.

Ha az alkalmazás több fiókot is támogat, és a megosztott eszköz módot is használja, akkor az alább látható módon be kell jelölnie a megfelelő felületet.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>A bejelentkezett felhasználó beolvasása, és annak megállapítása, hogy a felhasználó módosult-e az eszközön

A `loadAccount` metódus lekéri a bejelentkezett felhasználó fiókját. A `onAccountChanged` metódus meghatározza, hogy a bejelentkezett felhasználó módosult-e, és ha igen, törölje a következőt:

```java
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override
    public void onError(@NonNull Exception exception)
    {
    }
  }
}
```

### <a name="globally-sign-in-a-user"></a>Globális bejelentkezés felhasználóként

Az alábbi jelek az eszközön lévő felhasználókon keresztül más, a hitelesítő alkalmazással MSAL használó alkalmazásokhoz használhatók:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Felhasználó globális kijelentkezése

A következő eltávolítja a bejelentkezett fiókot, és törli a gyorsítótárazott jogkivonatokat nem csak az alkalmazásból, hanem a megosztott eszköz módban lévő eszközről is:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Rendszergazdai útmutató

A következő lépések leírják, hogyan állíthatja be az alkalmazást a Azure Portalban, és hogyan helyezheti üzembe az eszközt a megosztott eszközökön.

### <a name="register-your-application-in-azure-active-directory"></a>Az alkalmazás regisztrálása a Azure Active Directoryban

Először regisztráljon az alkalmazást a szervezeti bérlőn belül. Ezután adja meg az alábbi értékeket auth_config.json, hogy az alkalmazás megfelelően fusson.

Ennek módjáról az [alkalmazás regisztrálása](./tutorial-v2-android.md#register-your-application)című témakörben olvashat bővebben.

> [!NOTE]
> Az alkalmazás regisztrálása után a bal oldalon a gyors üzembe helyezési útmutatóban válassza az **Android** lehetőséget. Ez egy olyan oldalra mutat, amelyben meg kell adnia az alkalmazáshoz tartozó **csomag nevét** és **aláírási kivonatát** . Ezek nagyon fontosak annak biztosításához, hogy az alkalmazás konfigurációja működni fog. Ekkor megjelenik egy olyan konfigurációs objektum, amelyet használhat az alkalmazáshoz, amelyet a fájlra kivágja és beilleszt a auth_config.jsba.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-app.png" alt-text="Az Android-alkalmazások oldalának konfigurálása Azure Portal rövid útmutatóban":::

Válassza a **módosítás elvégzése nekem** lehetőséget, majd adja meg azokat az értékeket, amelyeket a rövid útmutató kér a Azure Portal. Ha elkészült, a rendszer létrehozza az összes szükséges konfigurációs fájlt.

:::image type="content" source="media/tutorial-v2-shared-device-mode/config-info.png" alt-text="A projekt oldalának konfigurálása Azure Portal rövid útmutatóban":::

## <a name="set-up-a-tenant"></a>Bérlő beállítása

Tesztelési célból állítsa be a következőt a bérlőben: legalább két alkalmazott, egy felhőalapú eszköz rendszergazdája és egy globális rendszergazda. A Azure Portalban állítsa be a felhőalapú eszköz rendszergazdáját a szervezeti szerepkörök módosításával. A Azure Portal a szervezeti szerepköröket a **Azure Active Directory**  >  **szerepkörök és rendszergazdák**  >  **Felhőbeli eszköz rendszergazdája** lehetőség kiválasztásával érheti el. Adja hozzá azokat a felhasználókat, akik számára az eszköz megosztott módba helyezhető.

## <a name="set-up-an-android-device-in-shared-mode"></a>Android-eszköz beállítása megosztott módban

### <a name="download-the-authenticator-app"></a>A hitelesítő alkalmazás letöltése

Töltse le a Microsoft Authenticator alkalmazást a Google Play áruházból. Ha már letöltötte az alkalmazást, győződjön meg arról, hogy a legújabb verzió.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Hitelesítő alkalmazás beállításai & a felhőben regisztrálja az eszközt

Indítsa el a hitelesítő alkalmazást, és navigáljon a főfiók lapra. Ha megjelenik a **fiók hozzáadása** lap, készen áll az eszköz megosztására.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-add-account.png" alt-text="Hitelesítő fiók hozzáadása képernyő":::

Nyissa meg a **Beállítások** ablaktáblát a jobb oldali menüsor használatával. Válassza az **eszköz regisztrálása** **munkahelyi & iskolai fiókok** alatt lehetőséget.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-settings.png" alt-text="Hitelesítő beállítások képernyő":::

Ha erre a gombra kattint, a rendszer arra kéri, hogy engedélyezze az eszköz névjegyeinek elérését. Ennek oka az, hogy az Android-fiók integrálva van az eszközön. Válassza az **Engedélyezés** lehetőséget.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png" alt-text="Hitelesítő hozzáférés megerősítése képernyő":::

A Felhőbeli eszköz rendszergazdájának meg kell adnia a szervezeti e-mail-címét **, vagy regisztrálnia kell megosztott eszközként**. Ezután kattintson a **regisztrálás megosztott eszközként** gombra, és adja meg a hitelesítő adataikat.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-device.png" alt-text="Eszköz regisztrációs képernyője az alkalmazásban":::

:::image type="content" source="media/tutorial-v2-shared-device-mode/sign-in.png" alt-text="A Microsoft bejelentkezési lapját ábrázoló alkalmazás képernyőképe":::

Az eszköz már megosztott módban van.

:::image type="content" source="media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png" alt-text="Az alkalmazás képernyője, amelyen engedélyezve van a megosztott eszköz üzemmód":::

 Az eszközön a bejelentkezések és a kijelentkezések globálisak lesznek, ami azt jelenti, hogy minden olyan alkalmazásra érvényesek, amely integrálva van a MSAL és a Microsoft Authenticator az eszközön. Mostantól telepítheti az alkalmazásokat az eszközre, amely megosztott eszköz üzemmódú szolgáltatásokat használ.

## <a name="view-the-shared-device-in-the-azure-portal"></a>A Azure Portal megosztott eszközének megtekintése

Az eszköz megosztott módban való üzembe helyezése után a rendszer a szervezete számára ismertté válik, és nyomon követheti a szervezeti bérlőn. A megosztott eszközök megtekintéséhez tekintse meg a Azure Portal Azure Active Directory paneljén található **JOIN (illesztés) típust** .

:::image type="content" source="media/tutorial-v2-shared-device-mode/registered-device-screen.png" alt-text="A Azure Portalban látható összes eszköz panel":::

## <a name="running-the-sample-app"></a>A minta alkalmazás futtatása

A minta alkalmazás egy egyszerű alkalmazás, amely meghívja a szervezet Graph APIét. Az első futtatáskor a rendszer megkéri, hogy az alkalmazás új legyen az alkalmazott fiókjában.

:::image type="content" source="media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png" alt-text="Az alkalmazás konfigurációs adatai képernyő":::

## <a name="next-steps"></a>Következő lépések

További információ a Microsoft Authentication Library és a megosztott eszköz mód használatáról Android-eszközökön:

> [!div class="nextstepaction"]
> [Megosztott eszköz mód Android-eszközökhöz](msal-android-shared-devices.md)
