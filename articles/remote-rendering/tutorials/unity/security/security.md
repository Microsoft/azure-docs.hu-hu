---
title: Az Azure Remote Rendering biztonságossá tétele és a modellek tárolása
description: Egy alkalmazás Remote Rendering tartalom biztonságossá tétele érdekében
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: d30ab051e58573daefd16f178feb4fc94f2ec83f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835469"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>Oktatóanyag: Az Azure Remote Rendering és a modelltárolás biztonságossá tétele

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Biztonságos Azure Blob Storage, amely Azure Remote Rendering modelleket tartalmaz
> * Hitelesítés az Azure AD-val a Azure Remote Rendering eléréséhez
> * Azure-beli hitelesítő adatok használata Azure Remote Rendering hitelesítéshez

## <a name="prerequisites"></a>Előfeltételek

* Ez az oktatóanyag az Oktatóanyag: Anyagok, megvilágítás és hatások [finomítására épül.](..\materials-lighting-effects\materials-lighting-effects.md)

## <a name="why-additional-security-is-needed"></a>Miért van szükség további biztonságra?

Az alkalmazás aktuális állapota és az Azure-erőforrásokhoz való hozzáférése a következő:

![Kezdeti biztonság](./media/security-one.png)

Az "AccountID + AccountKey" és az "URL + SAS-jogkivonat" is alapvetően együtt tárolja a felhasználónevet és a jelszót. Ha például az "AccountID + AccountKey" elérhetővé lenne téve, a támadók triviálisan használná az ARR-erőforrásokat az Ön engedélye nélkül, az Ön rovására.

## <a name="securing-your-content-in-azure-blob-storage"></a>Tartalom biztonságossá tétele Azure Blob Storage

Azure Remote Rendering a megfelelő konfigurációval biztonságosan elérhetik Azure Blob Storage-fájl tartalmát. Lásd: [How-to: Link storage accounts](../../../how-tos/create-an-account.md#link-storage-accounts) to configure your Azure Remote Rendering instance with your blob storage accounts (A tárfiókok összekapcsolása a Azure Remote Rendering-példány a Blob Storage-fiókokkal).

Csatolt blobtárolók használata esetén némileg eltérő módszereket fog használni a modellek betöltéséhez:

```cs
var loadModelParams = new LoadModelFromSasOptions(modelPath, modelEntity);
var task = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams);
```

A fenti sorok a `FromSas` params és a munkamenet-művelet verzióját használják. Ezeket nem SAS-verziókká kell konvertálni:

```cs
var loadModelParams = new LoadModelOptions(storageAccountPath, blobContainerName, modelPath, modelEntity);
var task = ARRSessionService.CurrentActiveSession.Connection.LoadModelAsync(loadModelParams);
```

Módosítsuk a **RemoteRenderingCoordinatort** egy egyéni modell betöltéséhez egy csatolt Blob Storage-fiókból.

1. Ha még nem, a Tárfiókok csatolása– dokumentumban adjon engedélyt az [ARR-példánynak](../../../how-tos/create-an-account.md#link-storage-accounts) a Blob Storage eléréséhez.
1. Adja hozzá a következő módosított **LoadModel** metódust a **RemoteRenderingCoordinator** metódushoz az aktuális **LoadModel metódus** alatt:

    ```cs
    /// <summary>
    /// Loads a model from blob storage that has been linked to the ARR instance
    /// </summary>
    /// <param name="storageAccountName">The storage account name, this contains the blob containers </param>
    /// <param name="blobContainerName">The blob container name, i.e. arroutput</param>
    /// <param name="modelPath">The relative path inside the container to the model, i.e. test/MyCustomModel.arrAsset</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns></returns>
    public async Task<Entity> LoadModel(string storageAccountName, string blobContainerName, string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Connection.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelOptions($"{storageAccountName}.blob.core.windows.net", blobContainerName, modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

    A legtöbb esetben ez a kód megegyezik az eredeti metódussal, de a metódus SAS-verzióját a nem `LoadModel` SAS-verziókra cseréljük.

    A és a `storageAccountName` további `blobContainerName` bemenetek is hozzá vannak adva az argumentumhoz. Ezt az új **LoadModel** metódust egy másik, az első oktatóanyagban létrehozott **LoadTestModel** metódushoz hasonló metódusból hívjuk meg.

1. Adja hozzá a következő metódust a **RemoteRenderingCoordinatorhoz a** **LoadTestModel után**

    ```cs
    private bool loadingLinkedCustomModel = false;

    [SerializeField]
    private string storageAccountName;
    public string StorageAccountName {
        get => storageAccountName.Trim();
        set => storageAccountName = value;
    }

    [SerializeField]
    private string blobContainerName;
    public string BlobContainerName {
        get => blobContainerName.Trim();
        set => blobContainerName = value;
    }

    [SerializeField]
    private string modelPath;
    public string ModelPath {
        get => modelPath.Trim();
        set => modelPath = value;
    }

    [ContextMenu("Load Linked Custom Model")]
    public async void LoadLinkedCustomModel()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if (loadingLinkedCustomModel)
        {
            Debug.Log("Linked Test model already loading or loaded!");
            return;
        }
        loadingLinkedCustomModel = true;

        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("LinkedCustomModel");
        testParent.transform.position = new Vector3(0f, 0f, 3f);

        await LoadModel(StorageAccountName, BlobContainerName, ModelPath, testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```

    Ez a kód három további sztringváltozót ad hozzá a **RemoteRenderingCoordinator összetevőhez.**
    ![Képernyőkép a RemoteRenderingCoordinator összetevő Tárfiók neve, Blobtároló neve és Modell elérési útja elemről.](./media/storage-account-linked-model.png)

1. Adja hozzá az értékeket a **RemoteRenderingCoordinator összetevőhez.** A modellátalakítás [rövid útmutatója után](../../../quickstarts/convert-model.md)az értékeknek a következőnek kell lennie:

    * **Tárfiók neve:** A tárfiók neve, a tárfiókhoz választott globálisan egyedi név. A rövid útmutatóban ez *az arrtutorialstorage* volt, az Ön értéke más lesz.
    * **Blobtároló neve:** arroutput, Blob Storage tároló
    * **Modell elérési** útja: Az "outputFolderPath" és az "outputAssetFileName" fájlban meghatározott "outputAssetFileName" *arrconfig.jsfájlban.* A rövid útmutatóban ez a következő volt: "outputFolderPath":"converted/robot", "outputAssetFileName": "robot.arrAsset". Ez a "converted/robot/robot.arrAsset" modellútvonal-értéket eredményezné, az Ön értéke eltérő lesz.

    >[!TIP]
    > Ha [a  ](../../../quickstarts/convert-model.md#run-the-conversion)Conversion.ps1szkriptet a "-UseContainerSas" argumentum nélkül futtatja, a szkript az SAS-jogkivonat helyett a fenti értékek mindegyikét az Ön számára fogja kiadni. ![Csatolt modell](./media/converted-output.png)
1. Egy ideig távolítsa el vagy tiltsa le a GameObject **TestModel modellt,** hogy helyet ásson az egyéni modellnek.
1. A jelenet lejátszása és csatlakozás egy távoli munkamenethez.
1. Kattintson a jobb gombbal a **RemoteRenderingCoordinator elemre, és** válassza **a Csatolt egyéni modell betöltése lehetőséget.**
    ![Csatolt modell betöltése](./media/load-linked-model.png)

Ezek a lépések megnövelték az alkalmazás biztonságát azáltal, hogy eltávolítják az SAS-jogkivonatot a helyi alkalmazásból.

Az alkalmazás aktuális állapota és az Azure-erőforrásokhoz való hozzáférése így néz ki:

![Nagyobb biztonság](./media/security-two.png)

Van még egy "jelszó", az AccountKey, amit el kell távolítani a helyi alkalmazásból. Ez egy AAD Azure Active Directory használatával használhatja.

## <a name="azure-active-directory-azure-ad-authentication"></a>Azure Active Directory (Azure AD) hitelesítése

Az AAD-hitelesítés lehetővé teszi annak megállapítását, hogy mely személyek vagy csoportok használják az ARR-t szabályozottabb módon. Az ARR beépített támogatást nyújt a hozzáférési [jogkivonatok](../../../../active-directory/develop/access-tokens.md) elfogadásához fiókkulcs használata helyett. A hozzáférési jogkivonatokat időkorlikát jelző, felhasználóspecifikus kulcsnak is felhasználhatja, amely csak a kért erőforrás bizonyos részeit oldja fel.

A **RemoteRenderingCoordinator** szkript egy **ARRCredentialGetter** nevű delegáltat tartalmaz, amely egy olyan metódust tartalmaz, amely **egy SessionConfiguration** objektumot ad vissza, amellyel konfigurálható a távoli munkamenet-kezelés. Hozzárendelhet egy másik metódust az **ARRCredentialGetterhez,** amely lehetővé teszi egy Azure bejelentkezési folyamat használatát, és egy **SessionConfiguration** objektumot hoz létre, amely egy Azure-hozzáférési jogkivonatot tartalmaz. Ez a hozzáférési jogkivonat a bejelentkező felhasználóra vonatkozik.

1. Kövesse a [How To: Configure authentication - Authentication for deployed applications (Útmutató:](../../../how-tos/authentication.md#authentication-for-deployed-applications)Hitelesítés konfigurálása – Hitelesítés az üzembe helyezett alkalmazások esetében) útmutatót, pontosabban az Azure Spatial Anchors-dokumentáció Azure AD-felhasználóhitelesítéssel kapcsolatos dokumentációjában szereplő utasításokat [fogja követni.](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication) Ez magában foglalja egy új Azure Active Directory regisztrálását és az ARR-példányhoz való hozzáférés konfigurálását.
1. Az új AAD-alkalmazás konfigurálása után ellenőrizze, hogy az AAD-alkalmazás az alábbi rendszerképekhez hasonlít-e:

    **AAD-alkalmazás -> hitelesítése** ![ Alkalmazáshitelesítés](./media/app-authentication-public.png)

    **AAD-alkalmazás –> API-engedélyek** ![ Alkalmazás API-k](./media/request-api-permissions-step-five.png)

1. A fiók Remote Rendering után ellenőrizze, hogy a konfiguráció az alábbi képen láthatóhoz hasonló-e:

    **AAR -> AccessControl (IAM)** ![ ARR-szerepkör](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > A *tulajdonosi* szerepkör nem elegendő a munkamenetek ügyfélalkalmazáson keresztüli kezeléséhez. Minden felhasználóhoz, aki számára a munkamenetek kezelését szeretné biztosítani, meg kell adnia a Remote Rendering **szerepkört.** Minden olyan felhasználónak, aki munkameneteket szeretne kezelni és modelleket konvertálni, meg kell adnia a Remote Rendering **szerepkört.**

Most, hogy a dolgok Azure-oldalán van, módosítanunk kell, hogy a kód hogyan csatlakozzon az AAR szolgáltatáshoz. Ezt a **BaseARRAuthentication** egy példányának megvalósításával tejük meg, amely egy új **SessionConfiguration** objektumot ad vissza. Ebben az esetben a fiókadatok az Azure Hozzáférési jogkivonattal lesznek konfigurálva.

1. Hozzon létre egy **új, AADAuthentication nevű szkriptet,** és cserélje le a kódját a következőre:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Identity.Client;
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using UnityEngine;
    
    public class AADAuthentication : BaseARRAuthentication
    {
        [SerializeField]
        private string activeDirectoryApplicationClientID;
        public string ActiveDirectoryApplicationClientID
        {
            get => activeDirectoryApplicationClientID.Trim();
            set => activeDirectoryApplicationClientID = value;
        }
    
        [SerializeField]
        private string azureTenantID;
        public string AzureTenantID
        {
            get => azureTenantID.Trim();
            set => azureTenantID = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingDomain;
        public string AzureRemoteRenderingDomain
        {
            get => azureRemoteRenderingDomain.Trim();
            set => azureRemoteRenderingDomain = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingAccountDomain;
        public string AzureRemoteRenderingAccountDomain
        {
            get => azureRemoteRenderingAccountDomain.Trim();
            set => azureRemoteRenderingAccountDomain = value;
        }    
    
        public override event Action<string> AuthenticationInstructions;
    
        string authority => "https://login.microsoftonline.com/" + AzureTenantID;
    
        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";
    
        string[] scopes => new string[] { "https://sts." + AzureRemoteRenderingAccountDomain + "/mixedreality.signin" };
    
        public void OnEnable()
        {
            RemoteRenderingCoordinator.ARRCredentialGetter = GetAARCredentials;
            this.gameObject.AddComponent<ExecuteOnUnityThread>();
        }
    
        public async override Task<SessionConfiguration> GetAARCredentials()
        {
            var result = await TryLogin();
            if (result != null)
            {
                Debug.Log("Account signin successful " + result.Account.Username);
    
                var AD_Token = result.AccessToken;
    
                return await Task.FromResult(new SessionConfiguration(AzureRemoteRenderingAccountDomain, AzureRemoteRenderingDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
            }
            else
            {
                Debug.LogError("Error logging in");
            }
            return default;
        }
    
        private Task DeviceCodeReturned(DeviceCodeResult deviceCodeDetails)
        {
            //Since everything in this task can happen on a different thread, invoke responses on the main Unity thread
            ExecuteOnUnityThread.Enqueue(() =>
            {
                // Display instructions to the user for how to authenticate in the browser
                Debug.Log(deviceCodeDetails.Message);
                AuthenticationInstructions?.Invoke(deviceCodeDetails.Message);
            });
    
            return Task.FromResult(0);
        }
    
        public override async Task<AuthenticationResult> TryLogin()
        {
            var clientApplication = PublicClientApplicationBuilder.Create(ActiveDirectoryApplicationClientID).WithAuthority(authority).WithRedirectUri(redirect_uri).Build();
            AuthenticationResult result = null;
            try
            {
                var accounts = await clientApplication.GetAccountsAsync();
    
                if (accounts.Any())
                {
                    result = await clientApplication.AcquireTokenSilent(scopes, accounts.First()).ExecuteAsync();
    
                    return result;
                }
                else
                {
                    try
                    {
                        result = await clientApplication.AcquireTokenWithDeviceCode(scopes, DeviceCodeReturned).ExecuteAsync(CancellationToken.None);
                        return result;
                    }
                    catch (MsalUiRequiredException ex)
                    {
                        Debug.LogError("MsalUiRequiredException");
                        Debug.LogException(ex);
                    }
                    catch (MsalServiceException ex)
                    {
                        Debug.LogError("MsalServiceException");
                        Debug.LogException(ex);
                    }
                    catch (MsalClientException ex)
                    {
                        Debug.LogError("MsalClientException");
                        Debug.LogException(ex);
                        // Mitigation: Use interactive authentication
                    }
                    catch (Exception ex)
                    {
                        Debug.LogError("Exception");
                        Debug.LogException(ex);
                    }
                }
            }
            catch (Exception ex)
            {
                Debug.LogError("GetAccountsAsync");
                Debug.LogException(ex);
            }
    
            return null;
        }
    }
    ```

>[!NOTE]
> Ez a kód semmilyen módon nem teljes, és nem áll készen kereskedelmi alkalmazásokhoz. Például legalább a kijelentkező képességet is valószínűleg hozzá szeretné adni. Ez az ügyfélalkalmazás által biztosított `Task RemoveAsync(IAccount account)` módszerrel használhatja. Ez a kód csak oktatóanyagban való használatra szolgál, az implementációja csak az alkalmazásra vonatkozik.

A kód először az **AquireTokenSilent** használatával próbálja meg csendesen lekért jogkivonatot. Ez sikeres lesz, ha a felhasználó korábban már hitelesítette az alkalmazást. Ha nem sikerül, lépjen tovább egy felhasználó által is érintett stratégiára.

Ebben a kódban az eszköz [kódfolyamával](../../../../active-directory/develop/v2-oauth2-device-code.md) szerezünk be egy hozzáférési jogkivonatot. Ez a folyamat lehetővé teszi, hogy a felhasználó bejelentkezik az Azure-fiókjába egy számítógépen vagy mobileszközön, és az eredményül kapott jogkivonatot visszaküldje a HoloLens-alkalmazásnak.

Az osztály legfontosabb része az ARR szempontjából a következő sor:

```cs
return await Task.FromResult(new SessionConfiguration(AzureRemoteRenderingAccountDomain, AzureRemoteRenderingDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

Itt létrehozunk egy új **SessionConfiguration** objektumot a távoli renderelési tartomány, a fiókazonosító, a fióktartomány és a hozzáférési jogkivonat használatával. Az ARR szolgáltatás ezután ezt a jogkivonatot használja távoli renderelési munkamenetek lekérdezéséhez, létrehozásához és csatlakozáshoz, ha a felhasználó a korábban konfigurált szerepköralapú engedélyek alapján van engedélyezve.

Ezzel a módosítással az alkalmazás aktuális állapota és az Azure-erőforrásokhoz való hozzáférése a következő:

![Még nagyobb biztonság](./media/security-three.png)

Mivel a felhasználói hitelesítő adatokat a rendszer nem tárolja az eszközön (vagy ebben az esetben még az eszközön is), nagyon alacsony a kitettség kockázata. Az eszköz mostantól felhasználóspecifikus, időkorrekta hozzáférési jogkivonattal fér hozzá az ARR-hez, amely hozzáférés-vezérléssel (IAM) fér hozzá a Blob Storage. Ez a két lépés teljesen eltávolította a "jelszavakat" a forráskódból, és jelentősen megnövelte a biztonságot. Ez azonban nem a legbiztonságosabb, a modell és a munkamenet-kezelés webszolgáltatásba való áthelyezésével tovább javíthatja a biztonságot. A további biztonsági szempontokat a Kereskedelmi készenlét [fejezet tárgyalja.](../commercial-ready/commercial-ready.md)

### <a name="testing-aad-auth"></a>Az AAD-hitelesítés tesztelése

A Unity-szerkesztőben, ha az AAD-hitelesítés aktív, minden alkalommal hitelesítenie kell magát, amikor elindítja az alkalmazást. Az eszközön a hitelesítési lépés először történik meg, és csak akkor lesz rá szükség, amikor a jogkivonat lejár vagy érvénytelenné válik.

1. Adja hozzá **az AADAuthentication** összetevőt a **RemoteRenderingCoordinator** GameObject elemhez.

    ![AAD-hitelesítési összetevő](./media/azure-active-directory-auth-component.png)

1. Adja meg az Ügyfél-azonosító és a Bérlőazonosító értékét. Ezek az értékek az alkalmazásregisztráció Áttekintő oldalán találhatók:

    * **Active Directory** alkalmazás ügyfél-azonosítója az AAD-alkalmazásregisztrációban *található alkalmazás- (ügyfél-)* azonosító (lásd az alábbi képet).
    * **Az Azure-bérlő** azonosítója az AAD-alkalmazásregisztrációban található címtár- *(bérlő-)* azonosító (lásd az alábbi képet).
    * **Azure Remote Rendering tartomány** megegyezik a **RemoteRenderingCoordinator** tartományában használt Remote Rendering tartományával.
    * **Azure Remote Rendering a fiókazonosító** megegyezik  a **RemoteRenderingCoordinatorhoz használt fiókazonosítóval.**
    * **Azure Remote Rendering fióktartomány** **megegyezik** a **RemoteRenderingCoordinatorban használt fióktartománysal.**

    ![Képernyőkép az alkalmazás- (ügyfél-) azonosítóról és a címtár- (bérlő-) azonosítóról.](./media/app-overview-data.png)

1. A Unity-szerkesztőben nyomja le a Play billentyűt, és járuljon hozzá egy munkamenet futtatásához.
    Mivel az **AADAuthentication** összetevő rendelkezik nézetvezérlővel, automatikusan csatlakoztatva van, hogy megjelenítsen egy kérést a munkamenet-engedélyezési modális panel után.
1. Kövesse az AppMenu jobb oldalon található panel **utasításait.**
    Az alábbihoz hasonlónak kell megjelennie: Ábra, amely az AppMenu jobb oldalon megjelenő ![ utasításpanelt mutatja.](./media/device-flow-instructions.png)
    
    Miután megadta a megadott kódot a másodlagos eszközön (vagy ugyanazon az eszközön a böngészőben), és bejelentkezik a hitelesítő adataival, a rendszer egy hozzáférési jogkivonatot ad vissza a kérelmező alkalmazásnak, ebben az esetben a Unity-szerkesztőnek.

Ezután az alkalmazás mindennek a szokásos módon kell haladni. Ellenőrizze a Unity-konzolon, hogy vannak-e hibák, ha nem a várt módon halad végig a szakaszokon.

## <a name="build-to-device"></a>Felépítés az eszközre

Ha MSAL-t használó alkalmazást hoz létre egy eszközön, bele kell foglalnia egy fájlt a projekt **Assets (Eszközök) mappájába.** Ez segít a fordítónak az alkalmazás megfelelő összeállításában az *oktatóanyagMicrosoft.Identity.Client.dll* eszközökben található **kódtitkok használatával.**

1. Adjon hozzá egy új fájlt az **Assets** (Eszközök) **link.xml**
1. Adja hozzá a következőt a fájlhoz:

    ```xml
    <linker>
        <assembly fullname="Microsoft.Identity.Client" preserve="all"/>
        <assembly fullname="System.Runtime.Serialization" preserve="all"/>
        <assembly fullname="System.Core">
            <type fullname="System.Linq.Expressions.Interpreter.LightLambda" preserve="all" />
        </assembly>
    </linker>
    ```

1. Mentse a módosításokat.

Kövesse a Rövid útmutató: Unity-minta üzembe helyezése [a HoloLensben – A](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project)mintaprojekt buildelésével a HoloLensbe való buildelését.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag-készlet többi része fogalmi témaköröket tartalmaz egy éles használatra kész alkalmazás létrehozásához, amely a Azure Remote Rendering.

> [!div class="nextstepaction"]
> [Következő: Kereskedelmi készenlét](../commercial-ready/commercial-ready.md)