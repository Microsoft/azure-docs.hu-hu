---
title: Az Azure Remote Rendering biztonságossá tétele és a modellek tárolása
description: Egy alkalmazás Remote Rendering tartalom biztonságossá tétele érdekében
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 6e595f7ff313ff85a12209e8c124b9aa376b20b6
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739745"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>Oktatóanyag: A Azure Remote Rendering és a modelltárolás biztonságossá tétele

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Biztonságos Azure Blob Storage, amely Azure Remote Rendering modelleket tartalmaz
> * Hitelesítés az Azure AD-val a Azure Remote Rendering eléréséhez
> * Azure-beli hitelesítő adatok használata Azure Remote Rendering hitelesítéshez

## <a name="prerequisites"></a>Előfeltételek

* Ez az oktatóanyag az Oktatóanyag: Anyagok, megvilágítás és hatások [finomítását ismertető oktatóanyagra épül.](..\materials-lighting-effects\materials-lighting-effects.md)

## <a name="why-additional-security-is-needed"></a>Miért van szükség további biztonságra?

Az alkalmazás aktuális állapota és az Azure-erőforrásokhoz való hozzáférése a következő:

![Kezdeti biztonság](./media/security-one.png)

Az "AccountID + AccountKey" és az "URL + SAS-jogkivonat" is alapvetően együtt tárolja a felhasználónevet és a jelszót. Ha például az "AccountID + AccountKey" elérhetővé lenne téve, a támadók triviálisan használná az ARR-erőforrásokat az Ön engedélye nélkül.

## <a name="securing-your-content-in-azure-blob-storage"></a>Tartalom biztonságossá tétele Azure Blob Storage

Azure Remote Rendering a megfelelő konfigurációval biztonságosan hozzáférhet Azure Blob Storage-fájl tartalmaihoz. Lásd: [How-to: Link storage accounts](../../../how-tos/create-an-account.md#link-storage-accounts) to configure your Azure Remote Rendering instance with your blob storage accounts (A tárfiókok összekapcsolása a Azure Remote Rendering példányt a Blob Storage-fiókokkal).

Csatolt blobtároló használata esetén némileg eltérő módszereket fog használni a modellek betöltéséhez:

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

1. Ha még nem, a Tárfiókok csatolása dokumentumban adjon engedélyt az [ARR-példánynak](../../../how-tos/create-an-account.md#link-storage-accounts) a Blob Storage eléréséhez.
1. Adja hozzá a következő módosított **LoadModel** metódust a **RemoteRenderingCoordinator metódushoz** az aktuális **LoadModel metódus** alatt:

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

1. Adja hozzá a következő metódust a **RemoteRenderingCoordinator metódushoz a** **LoadTestModel után**

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
    ![Képernyőkép a RemoteRenderingCoordinator összetevő Tárfiók neve, Blobtároló neve és Modell elérési útja elemével.](./media/storage-account-linked-model.png)

1. Adja hozzá az értékeket a **RemoteRenderingCoordinator összetevőhez.** A modellátalakítás [gyorsútmutatója után](../../../quickstarts/convert-model.md)a következő értékeknek kell lennie:

    * **Tárfiók neve:** A tárfiók neve, a tárfiókhoz választott globálisan egyedi név. A rövid útmutatóban ez *az arrtutorialstorage* volt, az Ön értéke más lesz.
    * **Blobtároló neve:** arroutput, Blob Storage tároló
    * **Modell elérési** útja: Az "outputFolderPath" és az "outputAssetFileName" fájlban meghatározott "outputAssetFileName" *arrconfig.jsfájlban.* A rövid útmutatóban ez a következő volt: "outputFolderPath":"converted/robot", "outputAssetFileName": "robot.arrAsset". Ennek eredményeként a Modell elérési útja értéke "converted/robot/robot.arrAsset" lesz, az Ön értéke eltérő lesz.

    >[!TIP]
    > Ha [a  ](../../../quickstarts/convert-model.md#run-the-conversion)Conversion.ps1szkriptet a "-UseContainerSas" argumentum nélkül futtatja, a szkript az SAS-jogkivonat helyett a fenti értékek mindegyikét kiadja az Ön számára. ![Csatolt modell](./media/converted-output.png)
1. Egy ideig távolítsa el vagy tiltsa le a GameObject **TestModel** modellt, hogy helyet ad az egyéni modell betöltésének.
1. Lejátszhatja a jelenet, és csatlakozhat egy távoli munkamenethez.
1. Kattintson a jobb gombbal a **RemoteRenderingCoordinator** elemre, és válassza **a Csatolt egyéni modell betöltése lehetőséget.**
    ![Csatolt modell betöltése](./media/load-linked-model.png)

Ezek a lépések megnövelték az alkalmazás biztonságát azáltal, hogy eltávolítják az SAS-jogkivonatot a helyi alkalmazásból.

Az alkalmazás jelenlegi állapota és az Azure-erőforrásokhoz való hozzáférése így néz ki:

![Nagyobb biztonság](./media/security-two.png)

Van még egy "jelszó", az AccountKey, amit el kell távolítani a helyi alkalmazásból. Ez az (AAD Azure Active Directory hitelesítéssel használhatja.

## <a name="azure-active-directory-azure-ad-authentication"></a>Azure Active Directory (Azure AD) hitelesítése

Az AAD-hitelesítés lehetővé teszi annak megállapítását, hogy mely személyek vagy csoportok használják szabályozottabb módon az ARR-t. Az ARR beépített támogatást nyújt a hozzáférési jogkivonatok elfogadásához [fiókkulcs](../../../../active-directory/develop/access-tokens.md) használata helyett. A hozzáférési jogkivonatok időkorccsal korlátozott, felhasználóspecifikus kulcsként is fel vannak gondoljon, amely csak a kért erőforrás bizonyos részeit oldja fel.

A **RemoteRenderingCoordinator** szkript egy **ARRCredentialGetter** nevű delegáltat tartalmaz, amely egy olyan metódust tartalmaz, amely **egy SessionConfiguration** objektumot ad vissza, amellyel konfigurálható a távoli munkamenet-kezelés. Hozzárendelhet egy másik metódust az **ARRCredentialGetterhez,** amely lehetővé teszi egy Azure bejelentkezési folyamat használatát, és egy **SessionConfiguration** objektumot hoz létre, amely egy Azure-hozzáférési jogkivonatot tartalmaz. Ez a hozzáférési jogkivonat a bejelentkező felhasználóra vonatkozik.

1. Kövesse a [How To: Configure authentication - Authentication for deployed applications (Útmutató:](../../../how-tos/authentication.md#authentication-for-deployed-applications)Hitelesítés konfigurálása – Hitelesítés az üzembe helyezett alkalmazásokhoz) útmutatót, konkrétan az Azure Spatial Anchors-dokumentáció Azure AD-felhasználóhitelesítéssel kapcsolatos dokumentációjában szereplő utasításokat [fogja követni.](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication) Ez magában foglalja egy új alkalmazás Azure Active Directory és az ARR-példányhoz való hozzáférés konfigurálását.
1. Az új AAD-alkalmazás konfigurálása után ellenőrizze, hogy az AAD-alkalmazás a következő képekhez hasonlít-e:

    **AAD-alkalmazás -> hitelesítése** ![ Alkalmazáshitelesítés](./media/app-authentication-public.png)

    **AAD-alkalmazás –> API-engedélyek** ![ Alkalmazás API-k](./media/request-api-permissions-step-five.png)

1. A fiók Remote Rendering után ellenőrizze, hogy a konfiguráció az alábbi képen láthatóhoz hasonló:

    **AAR -> AccessControl (IAM)** ![ ARR-szerepkör](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > A *tulajdonosi* szerepkör nem elegendő a munkamenetek ügyfélalkalmazáson keresztüli kezeléséhez. Minden olyan felhasználó számára, aki számára a munkamenetek kezelését szeretné biztosítani, meg kell adnia a Remote Rendering **szerepkört.** Minden olyan felhasználónak, aki munkameneteket szeretne kezelni és modelleket konvertálni, meg kell adnia a Remote Rendering **szerepkört.**

Most, hogy a dolgok Azure-oldalán van, módosítanunk kell, hogy a kód hogyan csatlakozzon az AAR szolgáltatáshoz. Ezt a **BaseARRAuthentication** egy példányának megvalósításával tejük meg, amely egy új **SessionConfiguration** objektumot ad vissza. Ebben az esetben a fiókadatok az Azure Hozzáférési jogkivonattal lesznek konfigurálva.

1. Hozzon létre egy **új szkriptet AADAuthentication** névvel, és cserélje le a kódját a következőre:

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
> Ez a kód egyáltalán nem teljes, és nem áll készen kereskedelmi alkalmazáshoz. Előfordulhat például, hogy legalább a kijelentkező képességet is hozzá szeretné adni. Ez az ügyfélalkalmazás által biztosított `Task RemoveAsync(IAccount account)` metódussal használhatja. Ez a kód csak oktatóanyag-használatra szolgál, az implementációja csak az alkalmazásra vonatkozik.

A kód először csendesen próbálja meg lehozni a jogkivonatot az **AquireTokenSilent használatával.** Ez sikeres lesz, ha a felhasználó korábban már hitelesítette ezt az alkalmazást. Ha nem sikerül, lépjen tovább egy felhasználó által is érintett stratégiára.

Ehhez a kódhoz az [eszközkód-folyamatot](../../../../active-directory/develop/v2-oauth2-device-code.md) használjuk egy hozzáférési jogkivonat beszerzéséhez. Ez a folyamat lehetővé teszi, hogy a felhasználó bejelentkezett Azure-fiókjába egy számítógépen vagy mobileszközön, és az eredményül kapott jogkivonatot visszaküldje a HoloLens-alkalmazásnak.

Az osztály legfontosabb része az ARR szempontjából a következő sor:

```cs
return await Task.FromResult(new SessionConfiguration(AzureRemoteRenderingAccountDomain, AzureRemoteRenderingDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

Itt egy új **SessionConfiguration** objektumot hozunk létre a távoli renderelési tartomány, a fiókazonosító, a fióktartomány és a hozzáférési jogkivonat használatával. Ezt a jogkivonatot ezután az ARR szolgáltatás használja távoli renderelési munkamenetek lekérdezésére, létrehozására és csatlakozására, ha a felhasználó a korábban konfigurált szerepköralapú engedélyek alapján van engedélyezve.

Ezzel a módosítással az alkalmazás aktuális állapota és az Azure-erőforrásokhoz való hozzáférése a következő:

![Még nagyobb biztonság](./media/security-three.png)

Mivel a felhasználói hitelesítő adatokat a rendszer nem tárolja az eszközön (vagy ebben az esetben még az eszközön is), a kitettségi kockázat nagyon alacsony. Az eszköz most egy felhasználóspecifikus, időkorrekált hozzáférési jogkivonattal fér hozzá az ARR-hez, amely hozzáférés-vezérléssel (IAM) fér hozzá a Blob Storage. Ez a két lépés teljesen eltávolította a "jelszavakat" a forráskódból, és jelentősen megnövelte a biztonságot. Ez azonban nem a legbiztonságosabb, a modell és a munkamenet-kezelés webszolgáltatásba való áthelyezésével tovább javíthatja a biztonságot. A további biztonsági szempontokról a Kereskedelmi készenlét [fejezetben olvashat.](../commercial-ready/commercial-ready.md)

### <a name="testing-aad-auth"></a>Az AAD-hitelesítés tesztelése

A Unity-szerkesztőben, ha az AAD-hitelesítés aktív, minden alkalommal hitelesítenie kell magát, amikor elindítja az alkalmazást. Az eszközön a hitelesítési lépés az első alkalommal történik meg, és csak akkor lesz rá szükség, amikor a jogkivonat lejár vagy érvénytelenül.

1. Adja hozzá **az AADAuthentication** összetevőt a **RemoteRenderingCoordinator** GameObjecthez.

    ![AAD-hitelesítési összetevő](./media/azure-active-directory-auth-component.png)

1. Adja meg az Ügyfél-azonosító és a Bérlőazonosító értékét. Ezek az értékek az alkalmazásregisztráció Áttekintés oldalán találhatók:

    * Active Directory alkalmazás **ügyfél-azonosítója** az AAD-alkalmazásregisztrációban található *alkalmazás-(ügyfél-)* azonosító (lásd az alábbi képet).
    * **Az Azure-bérlőazonosító** az AAD-alkalmazásregisztrációban található címtár- *(bérlő-)* azonosító (lásd az alábbi képet).
    * **Azure Remote Rendering tartomány** megegyezik a **RemoteRenderingCoordinator** tartományában használt Remote Rendering tartományával.
    * **Azure Remote Rendering fiókazonosító** **megegyezik** a **RemoteRenderingCoordinator fiókazonosítójával.**
    * **Azure Remote Rendering fióktartomány** ugyanaz a  fióktartomány, mint amit a **RemoteRenderingCoordinatorban használt.**

    ![Képernyőkép az alkalmazás -azonosítójának (ügyfél- és címtár- (bérlő-) azonosítójának kiemeléséhez.](./media/app-overview-data.png)

1. A Unity-szerkesztőben nyomja le a Play billentyűt, és járuljon hozzá egy munkamenet futtatásához.
    Mivel az **AADAuthentication** összetevő rendelkezik nézetvezérlővel, automatikusan csatlakoztatva van, hogy megjelenítsen egy kérést a munkamenet-engedélyezési modális panel után.
1. Kövesse az AppMenu jobb oldalon található panel **utasításait.**
    Az alábbihoz hasonlót kell látnia: Az AppMenu jobb oldalon megjelenő utasításpanelt bemutató ![ ábra.](./media/device-flow-instructions.png)
    Miután megadta a megadott kódot a másodlagos eszközön (vagy ugyanazon az eszközön egy böngészőben), és bejelentkezett a hitelesítő adataival, a rendszer egy hozzáférési jogkivonatot ad vissza a kérelmező alkalmazásnak, ebben az esetben a Unity Editornak.
1. Ezután az alkalmazás mindennek a szokásos módon kell működnie. Ellenőrizze a Unity-konzolon, hogy vannak-e hibák, ha nem a várt módon halad végig a szakaszokon.

## <a name="build-to-device"></a>Felépítés az eszközre

Ha egy alkalmazást az MSAL eszközre való használatával hoz létre, bele kell foglalnia egy fájlt a projekt **Assets (Eszközök) mappájába.** Ez segít a fordítónak az alkalmazás megfelelő összeállításában az *oktatóanyagMicrosoft.Identity.Client.dll* eszközökben **található kódtitkok használatával.**

1. Adjon hozzá egy új fájlt az **Assets (Eszközök)** **link.xml**
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

Kövesse a Következő rövid [útmutatóban található lépéseket: Unity-minta üzembe helyezése a HoloLensben – A](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project)Mintaprojekt létrehozása a HoloLensbe való buildelésével.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag-készlet többi része fogalmi témaköröket tartalmaz egy éles használatra kész alkalmazás létrehozásához, amely a Azure Remote Rendering.

> [!div class="nextstepaction"]
> [Következő: Kereskedelmi készenlét](../commercial-ready/commercial-ready.md)