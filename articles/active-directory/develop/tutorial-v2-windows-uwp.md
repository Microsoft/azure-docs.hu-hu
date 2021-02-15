---
title: 'Oktatóanyag: a Microsoft Identity platformot használó Univerzális Windows-platform-(UWP-) alkalmazás létrehozása hitelesítéshez | Azure'
titleSuffix: Microsoft identity platform
description: Ebben az oktatóanyagban olyan UWP-alkalmazást hoz létre, amely a Microsoft Identity platform használatával bejelentkezik a felhasználókba, és hozzáférési jogkivonatot kap a Microsoft Graph API nevében való meghívásához.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 6383f63d2118d8618f07bf3cb6cd08a0b16140f3
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102648"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Oktatóanyag: a Microsoft Graph API meghívása egy Univerzális Windows-platform-(UWP-) alkalmazásból

Ebben az oktatóanyagban létrehoz egy natív Univerzális Windows-platform (UWP) alkalmazást, amely bejelentkezik a felhasználók számára, és lekéri a hozzáférési jogkivonatot a Microsoft Graph API meghívásához. 

Az útmutató végén az alkalmazás személyes fiókok használatával meghívja a védett API-t. Ilyenek például a outlook.com, a live.com és mások. Az alkalmazás a munkahelyi és iskolai fiókokat is meghívja bármely olyan vállalattól vagy szervezettől, amely Azure Active Directory (Azure AD).

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * *Univerzális Windows-platform (UWP)* projekt létrehozása a Visual Studióban
> * Az alkalmazás regisztrálása a Azure Portalban
> * Kód hozzáadása a felhasználói bejelentkezés és a kijelentkezés támogatásához
> * Kód hozzáadása a Microsoft Graph API meghívásához
> * Az alkalmazás tesztelése

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) telepítve van a [univerzális Windows-platform fejlesztési](/windows/uwp/get-started/get-set-up) számítási feladattal

## <a name="how-this-guide-works"></a>Az útmutató működése

![Bemutatja, hogyan működik az oktatóanyag által generált minta alkalmazás](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Ez az útmutató egy minta UWP-alkalmazást hoz létre, amely lekérdezi a Microsoft Graph API-t. Ebben az esetben a rendszer egy jogkivonatot ad hozzá a HTTP-kérelmekhez az engedélyezési fejléc használatával. A Microsoft hitelesítési függvénytár kezeli a tokenek beszerzését és megújítását.

## <a name="nuget-packages"></a>NuGet-csomagok

Ez az útmutató a következő NuGet-csomagot használja:

|Kódtár|Description|
|---|---|
|[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)| Microsoft Authentication Library|
|[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph)|Microsoft Graph ügyféloldali kódtár|

## <a name="set-up-your-project"></a>A projekt beállítása

Ez a szakasz részletesen ismerteti a Windows asztali .NET-alkalmazások (XAML) integrálását a Microsofttal való bejelentkezéssel. Ezután az alkalmazás olyan webes API-kat tud lekérdezni, amelyek jogkivonatot igényelnek, például a Microsoft Graph API-t.

Ez az útmutató egy olyan alkalmazást hoz létre, amely lekérdezi az Microsoft Graph API-t és egy gombot a kijelentkezéshez. Emellett a hívások eredményét tartalmazó szövegmezőket is megjeleníti.

> [!Tip]
> Ha meg szeretné tekinteni az oktatóanyagban felépített projekt befejezett verzióját, [letöltheti azt a githubról](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip).

### <a name="create-your-application"></a>Az alkalmazás létrehozása

1. Nyissa meg a Visual studiót, és válassza **az új projekt létrehozása** lehetőséget.
1. A **create a New Project (új projekt létrehozása**) területen válassza az **üres alkalmazás (univerzális Windows)** lehetőséget a C# és a **tovább** gombra.
1. Az **új projekt konfigurálása** lapon adja meg az alkalmazás nevét, majd válassza a **Létrehozás** lehetőséget.
1. Ha a rendszer kéri, az **új univerzális Windows-platform projektben** válassza ki a **cél** és a **minimális** verziók bármely verzióját, és kattintson **az OK gombra**.

   ![Minimális és a célként megadott verziók](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-the-microsoft-authentication-library-to-your-project"></a>A Microsoft hitelesítési könyvtár hozzáadása a projekthez

1. A Visual Studióban válassza az **eszközök**  >  **NuGet Package** Manager  >  **csomagkezelő konzolt**.
1. Másolja és illessze be a következő parancsokat a **Package Manager konzol** ablakában:

    ```powershell
    Install-Package Microsoft.Identity.Client
    Install-Package Microsoft.Graph
    ```

   > [!NOTE]
   > Az első parancs telepíti a [Microsoft Authentication Library-t (MSAL.net)](https://aka.ms/msal-net). A MSAL.NET a Microsoft Identity platform által védett API-khoz hozzáférő felhasználói jogkivonatok beszerzését, gyorsítótárazását és frissítését végzi. A második parancs telepíti [Microsoft Graph .net Ügyféloldali kódtárat](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , hogy hitelesítse a kérelmeket Microsoft Graph és hívásokat kezdeményez a szolgáltatásnak.

### <a name="create-your-applications-ui"></a>Az alkalmazás felhasználói felületének létrehozása

A Visual Studio a projekt sablonjának részeként létrehozza a *Főoldal. XAML* -t. Nyissa meg ezt a fájlt, majd cserélje le az alkalmazás **rács** csomópontját a következő kódra:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-the-microsoft-authentication-library-to-get-a-token-for-the-microsoft-graph-api"></a>Az Microsoft Graph API-hoz tartozó jogkivonat beszerzése a Microsoft hitelesítési kódtár használatával

Ez a szakasz bemutatja, hogyan kérhető le jogkivonat a Microsoft Graph API-hoz a Microsoft hitelesítési kódtár használatával. Módosítsa a *MainPage.XAML.cs* -fájlt.

1. A *MainPage.XAML.cs*-ben adja hozzá az alábbi hivatkozásokat:

    ```csharp
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Cserélje le az `MainPage` osztályt a következő kódra:

    ```csharp
    public sealed partial class MainPage : Page
    {

        //Set the scope for API call to user.read
        private string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        private const string ClientId = "[Application Id pasted from the application registration portal]";

        private const string Tenant = "common"; // Alternatively "[Enter your tenant, as obtained from the Azure portal, e.g. kko365.onmicrosoft.com]"
        private const string Authority = "https://login.microsoftonline.com/" + Tenant;

        // The MSAL Public client app
        private static IPublicClientApplication PublicClientApp;

        private static string MSGraphURL = "https://graph.microsoft.com/v1.0/";
        private static AuthenticationResult authResult;

        public MainPage()
        {
            this.InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                // Sign in user using MSAL and obtain an access token for Microsoft Graph
                GraphServiceClient graphClient = await SignInAndInitializeGraphServiceClient(scopes);

                // Call the /me endpoint of Graph
                User graphUser = await graphClient.Me.Request().GetAsync();

                // Go back to the UI thread to make changes to the UI
                await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    ResultText.Text = "Display Name: " + graphUser.DisplayName + "\nBusiness Phone: " + graphUser.BusinessPhones.FirstOrDefault()
                                      + "\nGiven Name: " + graphUser.GivenName + "\nid: " + graphUser.Id
                                      + "\nUser Principal Name: " + graphUser.UserPrincipalName;
                    DisplayBasicTokenInfo(authResult);
                    this.SignOutButton.Visibility = Visibility.Visible;
                });
            }
            catch (MsalException msalEx)
            {
                await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalEx}");
            }
            catch (Exception ex)
            {
                await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
                return;
            }
        }
                /// <summary>
        /// Signs in the user and obtains an access token for Microsoft Graph
        /// </summary>
        /// <param name="scopes"></param>
        /// <returns> Access Token</returns>
        private static async Task<string> SignInUserAndGetTokenUsingMSAL(string[] scopes)
        {
            // Initialize the MSAL library by building a public client application
            PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .WithUseCorporateNetwork(false)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                 .WithLogging((level, message, containsPii) =>
                 {
                     Debug.WriteLine($"MSAL: {level} {message} ");
                 }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
                .Build();

            // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.
            IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
            IAccount firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                  .ExecuteAsync()
                                                  .ConfigureAwait(false);

            }
            return authResult.AccessToken;
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése<a name="more-information"></a>

A `AcquireTokenInteractive` metódus egy olyan ablakot eredményez, amely arra kéri a felhasználókat, hogy jelentkezzenek be. Az alkalmazások általában megkövetelik, hogy a felhasználók interaktívan jelentkezzenek be az első alkalommal egy védett erőforráshoz való hozzáféréshez. Előfordulhat, hogy be kell jelentkezniük, amikor a token beszerzésére irányuló csendes művelet meghiúsul. Ilyen például, ha egy felhasználó jelszava lejárt.

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `AcquireTokenSilent` metódus felhasználói beavatkozás nélkül kezeli a tokenek beszerzését és megújítását. Ha `AcquireTokenInteractive` első alkalommal futtatja a programot, és felszólítja a felhasználót a hitelesítő adatokra, használja a `AcquireTokenSilent` metódust a későbbi hívások jogkivonatának igényléséhez. Ez a metódus csendes módon szerzi be a jogkivonatokat. A Microsoft hitelesítési függvénytár kezeli a jogkivonat-gyorsítótárat és a megújítást.

A metódus végül `AcquireTokenSilent` sikertelen lesz. A hiba okai közé tartozik az a felhasználó, aki kijelentkezett vagy módosította a jelszavát egy másik eszközön. Ha a Microsoft Authentication Library azt észleli, hogy a probléma interaktív műveletet igényel, `MsalUiRequiredException` kivételt jelez. Az alkalmazás két módon tudja kezelni ezt a kivételt:

* Azonnal meghívja az alkalmazást `AcquireTokenInteractive` . Ez a hívás azt eredményezi, hogy a rendszer felszólítja a felhasználót, hogy jelentkezzen be. Ezt a módszert általában olyan online alkalmazásokhoz használja, amelyeken nincs elérhető offline tartalom a felhasználó számára. Az irányított telepítő által generált minta követi a mintát. A minta első futtatásakor ez a művelet jelenik meg.

   Mivel egyetlen felhasználó sem használta az alkalmazást, `accounts.FirstOrDefault()` Null értéket tartalmaz, és `MsalUiRequiredException` kivételt dob.

   A mintában szereplő kód a kivételt a hívásával kezeli `AcquireTokenInteractive` . Ez a hívás azt eredményezi, hogy a rendszer felszólítja a felhasználót, hogy jelentkezzen be.

* Az alkalmazás vizuálisan jelzi a felhasználóknak, hogy be kell jelentkezniük. Ezután kiválaszthatják a megfelelő időpontot a bejelentkezéshez. Az alkalmazás később újra próbálkozik `AcquireTokenSilent` . Ezt a módszert akkor használja, ha a felhasználók más alkalmazás-funkciókat is használhatnak megszakítás nélkül. Ilyen eset például, ha offline tartalom érhető el az alkalmazásban. Ebben az esetben a felhasználók dönthetnek arról, hogy mikor szeretnének bejelentkezni. Az alkalmazás újra próbálkozik, `AcquireTokenSilent` miután a hálózat átmenetileg nem volt elérhető.

### <a name="instantiate-the-microsoft-graph-service-client-by-obtaining-the-token-from-the-signinuserandgettokenusingmsal-method"></a>A Microsoft Graph szolgáltatás ügyfelének példánya a token SignInUserAndGetTokenUsingMSAL metódussal való beszerzésével

Adja hozzá a következő új metódust a *MainPage.XAML.cs*:

```csharp
      /// <summary>
     /// Sign in user using MSAL and obtain a token for Microsoft Graph
     /// </summary>
     /// <returns>GraphServiceClient</returns>
     private async static Task<GraphServiceClient> SignInAndInitializeGraphServiceClient(string[] scopes)
     {
         GraphServiceClient graphClient = new GraphServiceClient(MSGraphURL,
             new DelegateAuthenticationProvider(async (requestMessage) =>
             {
                 requestMessage.Headers.Authorization = new AuthenticationHeaderValue("bearer", await SignInUserAndGetTokenUsingMSAL(scopes));
             }));

         return await Task.FromResult(graphClient);
     }
```

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>További információ a védett API-k REST-hívásáról

Ebben a példában a `GetGraphServiceClient` metódus `GraphServiceClient` egy hozzáférési jogkivonat használatával példányát hozza létre. Ezt követően a `GraphServiceClient` rendszer a felhasználó profiljának adatait kérdezi le a **Me** végpontról.

### <a name="add-a-method-to-sign-out-the-user"></a>Metódus hozzáadása a felhasználó kijelentkezéséhez

A felhasználó kijelentkezéséhez adja hozzá a következő metódust a *MainPage.XAML.cs*:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
    IAccount firstAccount = accounts.FirstOrDefault();

    try
    {
        await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            ResultText.Text = "User has signed out";
            this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            });
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing out user: {ex.Message}";
        }
    }
```

A MSAL.NET aszinkron metódusokat használ a jogkivonatok beszerzéséhez vagy a fiókok kezeléséhez. Ennek megfelelően a felhasználói FELÜLETi szál felhasználói felületi műveleteinek támogatása. Ez a hívás oka `Dispatcher.RunAsync` és a meghívott óvintézkedések `ConfigureAwait(false)` .

#### <a name="more-information-about-signing-out"></a>További információ a kijelentkezésről<a name="more-information-on-sign-out"></a>

A `SignOutButton_Click` metódus eltávolítja a felhasználót a Microsoft Authentication Library felhasználói gyorsítótárából. Ez a módszer gyakorlatilag azt jelzi, hogy a Microsoft hitelesítési függvénytár elfelejti az aktuális felhasználót. A jogkivonat beszerzésére vonatkozó jövőbeli kérések csak akkor lesznek sikeresek, ha az interaktív.

Az ebben a példában szereplő alkalmazás egyetlen felhasználót támogat. A Microsoft hitelesítési függvénytár olyan forgatókönyveket támogat, amelyekben a felhasználó több fiókon is bejelentkezhet. Ilyen például egy e-mail-alkalmazás, amelyben a felhasználók több fiókkal rendelkeznek.

### <a name="display-basic-token-information"></a>Alapszintű jogkivonat adatainak megjelenítése

Adja hozzá a következő metódust a *MainPage.XAML.cs* a jogkivonat alapvető adatainak megjelenítéséhez:

```csharp
/// <summary>
/// Display basic information contained in the token. Needs to be called from the UI thread.
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

#### <a name="more-information"></a>További információ<a name="more-information-1"></a>

Az **OpenID Connect** használatával beszerzett azonosító tokenek a felhasználóhoz tartozó információk kis részhalmazát is tartalmazzák. `DisplayBasicTokenInfo` a jogkivonatban található alapvető információkat jeleníti meg. Ez az információ tartalmazza a felhasználó megjelenítendő nevét és AZONOSÍTÓját. Tartalmazza továbbá a jogkivonat lejárati dátumát és a hozzáférési jogkivonatot jelölő karakterláncot is. Ha többször is bejelöli a **hívás Microsoft Graph API** -t, látni fogja, hogy ugyanazt a tokent használták újra a későbbi kérésekhez. Azt is megtekintheti, hogy mikor kell megújítani a tokent, ha a Microsoft hitelesítési függvénytár eldönti, hogy a lejárati dátum kiterjeszthető

### <a name="display-message"></a>Üzenet megjelenítése

Adja hozzá a következő új metódust a *MainPage.XAML.cs*:

```csharp
/// <summary>
/// Displays a message in the ResultText. Can be called from any thread.
/// </summary>
private async Task DisplayMessageAsync(string message)
{
     await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
         () =>
         {
             ResultText.Text = message;
         });
     }
```

## <a name="register-your-application"></a>Az alkalmazás regisztrálása

Most regisztrálja az alkalmazást:

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** , például: `UWP-App-calling-MSGraph` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
1. A **támogatott fióktípus** területen válassza a **fiókok lehetőséget bármely szervezeti címtárban (bármely Azure ad-címtár-több-bérlős) és a személyes Microsoft-fiókokat (például Skype, Xbox)**. 
1. Válassza a **Regisztráció** lehetőséget.
1. Az Áttekintés oldalon keresse meg az **alkalmazás (ügyfél) azonosítójának** értékét, és másolja. Lépjen vissza a Visual studióba, nyissa meg a *MainPage.XAML.cs*, és cserélje le az értéket ezzel az `ClientId` értékkel.

Az alkalmazás hitelesítésének konfigurálása:

1. A <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>a **kezelés** területen válassza a **hitelesítés**  >  **a platform hozzáadása** lehetőséget, majd válassza a **mobil-és asztali alkalmazások** lehetőséget.
1. Az **átirányítási URI** -k szakaszban adja meg a értéket `https://login.microsoftonline.com/common/oauth2/nativeclient` .
1. Válassza a **Konfigurálás** lehetőséget.

API-engedélyek konfigurálása az alkalmazáshoz:

1. A **kezelés** területen válassza az **API-engedélyek**  >  **Hozzáadás engedélyt**.
1. Válassza a **Microsoft Graph** lehetőséget.
1. Válassza a **delegált engedélyek** lehetőséget, keresse meg a *User. Read* parancsot, és ellenőrizze, hogy a **felhasználó. Read** van-e kiválasztva.
1. Ha módosította a módosításokat, válassza az **engedélyek hozzáadása** lehetőséget a mentéshez.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Integrált hitelesítés engedélyezése összevont tartományokon (nem kötelező)

Az integrált Windows-hitelesítés engedélyezéséhez összevont Azure AD-tartománnyal való használat esetén az alkalmazás jegyzékfájljának engedélyeznie kell a további képességeket. Térjen vissza az alkalmazáshoz a Visual Studióban.

1. Nyissa meg a *Package. appxmanifest*.
1. Válassza a **képességek** lehetőséget, és engedélyezze a következő beállításokat:

   * **Vállalati hitelesítés**
   * **Magánhálózatok (ügyfél & kiszolgáló)**
   * **Megosztott felhasználói tanúsítványok**

> [!IMPORTANT]
> Az [integrált Windows-hitelesítés](https://aka.ms/msal-net-iwa) alapértelmezés szerint nincs konfigurálva ehhez a mintához. A kérést `Enterprise Authentication` vagy `Shared User Certificates` képességeket igénylő alkalmazások magasabb szintű ellenőrzést igényelnek a Windows áruházban. Emellett nem minden fejlesztő szeretné végrehajtani a magasabb szintű ellenőrzést. Ezt a beállítást csak akkor engedélyezze, ha integrált Windows-hitelesítésre van szüksége egy összevont Azure AD-tartománnyal.

## <a name="alternate-approach-to-using-withdefaultredirecturi"></a>A WithDefaultRedirectURI () használatának alternatív megközelítése

Az aktuális példában a `WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")` metódust használjuk. A használatához `WithDefaultRedirectURI()` hajtsa végre a következő lépéseket:

1. A *MainPage.XAML.cs*-ben cserélje le a (z) `WithRedirectUri` `WithDefaultRedirectUri` :

   **Jelenlegi kód**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority(Authority)
       .WithUseCorporateNetwork(false)
       .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();

   ```

   **Frissített kód**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority("https://login.microsoftonline.com/common")
       .WithUseCorporateNetwork(false)
       .WithDefaultRedirectUri()
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();
   ```

2.  Keresse meg az alkalmazás visszahívási URI-JÁT úgy, hogy hozzáadja a `redirectURI` mezőt a *MainPage.XAML.cs* -ben, és beállítja a töréspontot:

    ```csharp

    public sealed partial class MainPage : Page
    {
            ...

            string redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker
                                .GetCurrentApplicationCallbackUri().ToString();
            public MainPage()
            {
                ...
            }
           ...
    }

    ```

    Futtassa az alkalmazást, majd másolja az értéket a `redirectUri` töréspont találata után. Az értéknek a következő értékhez hasonlóan kell kinéznie: `ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/`

    Ezután eltávolíthatja a kód sorát, mert csak egyszer szükséges, hogy beolvassa az értéket.

3. Az alkalmazás regisztrációs portálján adja hozzá a visszaadott értéket a **RedirectUri** -ben a **hitelesítés** ablaktáblán.

## <a name="test-your-code"></a>A kód tesztelése

Az alkalmazás teszteléséhez válassza az **F5** billentyűt a projekt futtatásához a Visual Studióban. Megjelenik a fő ablak:

![Alkalmazás felhasználói felülete](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Ha készen áll a tesztelésre, válassza a **Microsoft Graph API meghívása** lehetőséget. Ezután jelentkezzen be egy Azure AD-beli szervezeti fiókkal vagy egy Microsoft-fiók, például live.com vagy outlook.com használatával. Amikor a felhasználó először futtatja ezt a tesztet, az alkalmazás egy ablakot jelenít meg, amely arra kéri a felhasználót, hogy jelentkezzen be.

### <a name="consent"></a>Hozzájárulás

Amikor először jelentkezik be az alkalmazásba, megjelenik egy beleegyezési képernyő, amely az alábbi képhez hasonlóan jelenik meg. Válassza az **Igen** lehetőséget a hozzáféréshez való explicit beleegyezéshez:

![Hozzáférési beleegyezési képernyő](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Várt eredmények

A Microsoft Graph API-hívás által visszaadott felhasználói profil adatait az **API-hívás eredményei** képernyőn láthatja:

![API-hívás eredményei képernyő](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

A jogkivonat `AcquireTokenInteractive` `AcquireTokenSilent` - **információ** mezőben a vagy a használatával beszerzett jogkivonatra vonatkozó alapvető információk is megjelennek:

|Tulajdonság  |Formátum  |Leírás |
|---------|---------|---------|
|`Username` |`user@domain.com` |A felhasználót azonosító Felhasználónév.|
|`Token Expires` |`DateTime` |A jogkivonat lejárati ideje. A Microsoft hitelesítési függvénytár a token szükség szerinti megújításával kiterjeszti a lejárati dátumot.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörökről és a delegált engedélyekről

A Microsoft Graph API-nak a `user.read` hatókört kell beolvasnia a felhasználó profiljához. Ezt a hatókört alapértelmezés szerint a rendszer minden olyan alkalmazásban hozzáadja, amely az alkalmazás regisztrációs portálján van regisztrálva. A Microsoft Graph és az egyéni API-k egyéb API-jai a háttér-kiszolgáló számára további hatókörökre is szükség lehet. A Microsoft Graph API például megköveteli a `Calendars.Read` hatókört a felhasználó naptárának listázásához.

Ha egy alkalmazás kontextusában szeretné elérni a felhasználó naptárait, adja hozzá a `Calendars.Read` delegált engedélyt az alkalmazás regisztrációs adataihoz. Ezután adja hozzá a `Calendars.Read` hatókört a `acquireTokenSilent` híváshoz.

A rendszer a hatókörök számának növelésével további hozzájárulásokat is kérhet a felhasználóknak.

## <a name="known-issues"></a>Ismert problémák

### <a name="issue-1"></a>1. probléma

Az alábbi hibaüzenetek egyike jelenik meg, amikor bejelentkezik az alkalmazásba egy összevont Azure AD-tartományban:

* "Nem található érvényes ügyféltanúsítvány a kérelemben."
* "A felhasználó tanúsítványtárolójában nem található érvényes tanúsítvány."
* "Próbálkozzon újra egy másik hitelesítési módszer kiválasztásával."

**OK:** A vállalati és a tanúsítvány képességei nem engedélyezettek.

**Megoldás:** Kövesse az [integrált hitelesítés engedélyezése összevont tartományokon (nem kötelező)](#enable-integrated-authentication-on-federated-domains-optional)című témakör lépéseit.

### <a name="issue-2"></a>2. probléma

Az [integrált hitelesítést az összevont tartományokon](#enable-integrated-authentication-on-federated-domains-optional) engedélyezheti, és a Windows Hello használatával a Windows 10 rendszerű számítógépeken is megpróbálhatja bejelentkezni egy olyan környezetre, amelyben többtényezős hitelesítés van konfigurálva. Megjelenik a tanúsítványok listája. Ha úgy dönt, hogy a PIN-kódot használja, a PIN-kód ablak soha nem jelenik meg.

**OK:** Ez a probléma a Windows 10-es asztali számítógépeken futó UWP-alkalmazásokban a web Authentication Broker ismert korlátozása. Jól működik a Windows 10 Mobile-ban.

**Áthidaló megoldás:** Válassza **a bejelentkezés más beállításokkal lehetőséget**. Ezután válassza a **Bejelentkezés felhasználónévvel és jelszóval** lehetőséget. Válassza **a jelszó megadása** lehetőséget. Ezután folytassa a telefonos hitelesítési folyamaton.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

További információ a Microsoft Authentication Library (MSAL) használatáról a .NET-alkalmazásokban történő engedélyezéshez és hitelesítéshez:

> [!div class="nextstepaction"]
> [A Microsoft Authentication Library (MSAL) áttekintése](msal-overview.md)
