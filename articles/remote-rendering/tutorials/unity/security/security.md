---
title: Az Azure Remote Rendering biztonságossá tétele és a modellek tárolása
description: Távoli renderelési alkalmazás megerősítése a tartalom biztonságossá tételéhez
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: b1bcba264589d6cbe9b4f671e1e4f2c9b1dbf2c5
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594248"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>Oktatóanyag: az Azure távoli renderelés és a modell tárterületének védelme

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Azure-beli távoli renderelési modelleket tartalmazó Blob Storage biztonságossá tétele
> * Hitelesítés az Azure AD-vel az Azure távoli renderelési példányának eléréséhez
> * Azure-beli hitelesítő adatok használata az Azure távoli renderelési hitelesítéshez

## <a name="prerequisites"></a>Előfeltételek

* Ez az oktatóanyag a következő [oktatóanyagra épül: az anyagok, a világítás és a hatások finomítása](..\materials-lighting-effects\materials-lighting-effects.md).

## <a name="why-additional-security-is-needed"></a>Miért van szükség további biztonságra

Az alkalmazás jelenlegi állapota és az Azure-erőforrásokhoz való hozzáférése a következőképpen néz ki:

![Kezdeti biztonság](./media/security-one.png)

A "AccountID + AccountKey" és az "URL + SAS-token" egyaránt alapvetően a felhasználónevet és a jelszót tárolja együtt. Ha például a "AccountID + AccountKey" elérhetővé vált, a támadók az ARR-erőforrásokat az Ön költségeinek megadása nélkül használhatják fel.

## <a name="securing-your-content-in-azure-blob-storage"></a>A tartalom biztonságossá tétele az Azure Blob Storageban

Az Azure-alapú távoli renderelés a megfelelő konfigurációval biztonságosan fér hozzá az Azure-Blob Storage tartalmához. További információ [: Storage-fiókok csatolása](../../../how-tos/create-an-account.md#link-storage-accounts) az Azure távoli renderelési példányának a blob Storage-fiókokkal való konfigurálásához.

Ha csatolt blob Storage-t használ, a modellek betöltéséhez némileg eltérő módszereket fog használni:

```cs
var loadModelParams = new LoadModelFromSasOptions(modelPath, modelEntity);
var task = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams);
```

A fenti sorok a `FromSas` params és a munkamenet művelet verzióját használják. Ezeket át kell alakítani a nem SAS-verzióra:

```cs
var loadModelParams = new LoadModelOptions(storageAccountPath, blobContainerName, modelPath, modelEntity);
var task = ARRSessionService.CurrentActiveSession.Connection.LoadModelAsync(loadModelParams);
```

Módosítsa a **RemoteRenderingCoordinator** egy egyéni modell betöltéséhez egy csatolt blob Storage-fiókból.

1. Ha még nem tette meg, hajtsa végre a következő [témakört: csatolja a Storage-fiókokat](../../../how-tos/create-an-account.md#link-storage-accounts) az ARR-példány engedélyének megadásához az blob Storage-példány eléréséhez.
1. Adja hozzá a következő módosított **LoadModel** metódust, hogy a **RemoteRenderingCoordinator** közvetlenül az aktuális **LoadModel** metódus alá kerüljön:

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

    A legtöbb esetben ez a kód megegyezik az eredeti `LoadModel` módszerrel, azonban a METÓDUS sas-verzióját lecserélte a nem sas-verzióra.

    A további bemenetek `storageAccountName` és `blobContainerName` az argumentumokhoz is hozzá lettek adva. Ezt az új **LoadModel** metódust egy másik, az első oktatóanyagban létrehozott **LoadTestModel** -metódushoz hasonlóan hívjuk.

1. Adja hozzá a következő metódust a **RemoteRenderingCoordinator** közvetlenül a **LoadTestModel** után

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

    Ez a kód három további karakterlánc-változót hoz létre a **RemoteRenderingCoordinator** összetevőhöz.
    ![Képernyőfelvétel: a Storage-fiók neve, a blob-tároló neve és a RemoteRenderingCoordinator összetevő modell-elérési útja.](./media/storage-account-linked-model.png)

1. Adja hozzá az értékeket a **RemoteRenderingCoordinator** összetevőhöz. Miután követte a [modell átalakításának](../../../quickstarts/convert-model.md)gyors útmutatóját, a következő értékeket kell megadni:

    * **Storage-fiók neve**: a Storage-fiók neve, a Storage-fiókhoz választott globálisan egyedi név. A rövid útmutatóban ez a *arrtutorialstorage*, az értéke eltérő lesz.
    * **Blob-tároló neve**: arroutput, a blob Storage tároló
    * **Modell elérési útja**: a "outputFolderPath" és a "outputAssetFileName" kombinációja definiálva van a fájl *arrconfig.js* . A rövid útmutatóban ez a "outputFolderPath": "Converted/robot", "outputAssetFileName": "robot. arrAsset". Ennek eredményeképpen a modell elérési útja "konvertált/robot/robot. arrAsset" értékre vált, az értéke eltérő lesz.

    >[!TIP]
    > Ha [a **Conversion.ps1**](../../../quickstarts/convert-model.md#run-the-conversion) parancsfájlt a "-UseContainerSas" argumentum nélkül futtatja, a parancsfájl az SAS-token helyett a fenti értékeket fogja kiadni. ![Csatolt modell](./media/converted-output.png)
1. Az idő alatt távolítsa el vagy tiltsa le a GameObject **TestModel**, hogy helyet szabadítson fel az egyéni modell betöltéséhez.
1. Próbálja ki a jelenetet, és kapcsolódjon egy távoli munkamenethez.
1. Kattintson a jobb gombbal a **RemoteRenderingCoordinator** , és válassza a **társított egyéni modell betöltése** lehetőséget.
    ![Csatolt modell betöltése](./media/load-linked-model.png)

Ezek a lépések megnövelték az alkalmazás biztonságát azáltal, hogy eltávolítják az SAS-jogkivonatot a helyi alkalmazásból.

Az alkalmazás jelenlegi állapota és az Azure-erőforrásokhoz való hozzáférése most így néz ki:

![Jobb biztonság](./media/security-two.png)

Még egy "jelszó", a AccountKey a helyi alkalmazásból való eltávolításához. Ez Azure Active Directory (HRE) hitelesítés használatával végezhető el.

## <a name="azure-active-directory-azure-ad-authentication"></a>Azure Active Directory (Azure AD) hitelesítés

A HRE-hitelesítés lehetővé teszi annak meghatározását, hogy mely személyek vagy csoportok használják az ARR-t egy ellenőrzött módon. Az ARR beépített támogatást nyújt a [hozzáférési jogkivonatok](../../../../active-directory/develop/access-tokens.md) elfogadásához a fiók kulcsának használata helyett. Azt is megteheti, hogy a hozzáférési tokeneket időkorlátos, felhasználó-specifikus kulcsként tekinti át, amely csak a kért erőforrás bizonyos részeit oldja fel.

A **RemoteRenderingCoordinator** parancsfájlhoz tartozik egy **ARRCredentialGetter** nevű delegált, amely egy olyan metódust tartalmaz, amely egy **SessionConfiguration** objektumot ad vissza, amely a távoli munkamenetek felügyeletének konfigurálására szolgál. Hozzárendelhet egy másik módszert a **ARRCredentialGetter**, amely lehetővé teszi, hogy Azure bejelentkezési folyamatot használjon, amely egy Azure hozzáférési tokent tartalmazó **SessionConfiguration** objektumot hoz létre. Ez a hozzáférési jogkivonat a bejelentkezett felhasználóra jellemző lesz.

1. Kövesse a következő [témakört: az üzembe helyezett alkalmazások hitelesítési](../../../how-tos/authentication.md#authentication-for-deployed-applications)hitelesítésének konfigurálása, pontosabban követheti az Azure térbeli horgonyok dokumentációjában az [Azure ad felhasználói hitelesítésben](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication)felsorolt utasításokat. Ebbe beletartozik egy új Azure Active Directory alkalmazás regisztrálása és az ARR-példányhoz való hozzáférés konfigurálása.
1. Miután konfigurálta az új HRE alkalmazást, tekintse meg a HRE-alkalmazást az alábbi képekkel:

    **HRE alkalmazás – > hitelesítés** ![ Alkalmazás-hitelesítés](./media/app-authentication-public.png)

    **HRE alkalmazás – > API-engedélyek** ![ Alkalmazás API-k](./media/request-api-permissions-step-five.png)

1. A távoli renderelési fiók konfigurálása után a konfiguráció a következő képhez hasonlóan néz ki:

    **Éves tevékenység – > AccessControl (iam)** ![ ARR-szerepkör](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > A *tulajdonosi* szerepkör nem elegendő a munkamenetek kezeléséhez az ügyfélalkalmazás használatával. Minden olyan felhasználó számára, aki számára engedélyezni szeretné a munkamenetek kezelését, meg kell adnia a szerepkör **távoli renderelési ügyfelét**. Minden, a munkameneteket kezelő és a modellek átalakítására szolgáló felhasználó számára meg kell adnia a szerepkör **távoli renderelési rendszergazdáját**.

A dolgok Azure-oldalán most módosítani kell, hogy a kód hogyan kapcsolódjon az éves tevékenységi szolgáltatáshoz. Ezt a **BaseARRAuthentication** egy példányának megvalósításával tesszük, amely egy új **SessionConfiguration** objektumot ad vissza. Ebben az esetben a fiók adatai az Azure hozzáférési jogkivonattal lesznek konfigurálva.

1. Hozzon létre egy **AADAuthentication** nevű új parancsfájlt, és cserélje le a kódját a következőre:

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
        private string accountDomain;
        public string AccountDomain
        {
            get => accountDomain.Trim();
            set => accountDomain = value;
        }

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
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingAccountAuthenticationDomain;
        public string AzureRemoteRenderingAccountAuthenticationDomain
        {
            get => azureRemoteRenderingAccountAuthenticationDomain.Trim();
            set => azureRemoteRenderingAccountAuthenticationDomain = value;
        }

        public override event Action<string> AuthenticationInstructions;

        string authority => "https://login.microsoftonline.com/" + AzureTenantID;

        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";

        string[] scopes => new string[] { "https://sts." + AzureRemoteRenderingAccountAuthenticationDomain + "/mixedreality.signin" };

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

                return await Task.FromResult(new SessionConfiguration(AzureRemoteRenderingAccountAuthenticationDomain, AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
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
> Ez a kód nem fejeződött be, és nem áll készen a kereskedelmi alkalmazásokra. A minimális lehetőség például a kijelentkezés lehetőségét is felveheti. Ezt az `Task RemoveAsync(IAccount account)` ügyfélalkalmazás által biztosított módszer használatával teheti meg. Ez a kód csak az oktatóanyag használatára szolgál. a megvalósítás az alkalmazásra is vonatkozik.

A kód először a **AquireTokenSilent** használatával kísérli meg a token lekérését. Ez sikeres lesz, ha a felhasználó korábban hitelesítette az alkalmazást. Ha nem sikerül, lépjen tovább a felhasználó által érintett stratégiára.

Ebben a kódban az [eszköz kódját](../../../../active-directory/develop/v2-oauth2-device-code.md) használjuk a hozzáférési token beszerzéséhez. Ez a folyamat lehetővé teszi, hogy a felhasználó bejelentkezzen az Azure-fiókjába egy számítógépre vagy mobileszközön, és az eredményül kapott tokent visszaküldje a HoloLens alkalmazásnak.

Ennek az osztálynak a legfontosabb része az ARR perspektívából ez a sor:

```cs
return await Task.FromResult(new SessionConfiguration(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

Itt hozzunk létre egy új **SessionConfiguration** -objektumot a fiók tartománya, a fiókazonosító, a fiók hitelesítési tartománya és a hozzáférési jogkivonat használatával. Ezt a tokent az ARR szolgáltatás a távoli renderelési munkamenetek lekérdezéséhez, létrehozásához és csatlakoztatásához használja, feltéve, hogy a felhasználó a korábban konfigurált szerepköralapú engedélyek alapján van engedélyezve.

Ezzel a változással az alkalmazás jelenlegi állapota és az Azure-erőforrásokhoz való hozzáférése a következőképpen néz ki:

![Még jobb biztonság](./media/security-three.png)

Mivel a felhasználói hitelesítő adatok nem tárolódnak az eszközön (vagy ebben az esetben még nem szerepel az eszközön), a kitettség kockázata nagyon alacsony. Most az eszköz egy felhasználó-specifikus, időkorlátos hozzáférési tokent használ az ARR eléréséhez, amely hozzáférés-vezérlést (IAM) használ a Blob Storage eléréséhez. Ez a két lépés teljesen eltávolította a "jelszavakat" a forráskódból, és jelentős mértékben növelte a biztonságot. Ez azonban nem az elérhető legnagyobb biztonság, a modell és a munkamenet-kezelés webszolgáltatásba való áthelyezése tovább növeli a biztonságot. A [kereskedelmi készültségi](../commercial-ready/commercial-ready.md) fejezetben további biztonsági megfontolásokat is ismertetünk.

### <a name="testing-aad-auth"></a>HRE-hitelesítés tesztelése

Ha a HRE-hitelesítés aktív, az Unity Editorban az alkalmazás indításakor minden alkalommal hitelesítenie kell magát. Az eszközön a hitelesítési lépés az első alkalommal fog történni, és csak akkor szükséges, ha a jogkivonat lejár vagy érvénytelenné válik.

1. Adja hozzá a **AADAuthentication** összetevőt a **RemoteRenderingCoordinator** -GameObject.

    ![HRE-hitelesítési összetevő](./media/azure-active-directory-auth-component.png)

1. Adja meg az ügyfél-azonosító és a bérlő AZONOSÍTÓjának értékét. Ezek az értékek az alkalmazás regisztrációjának Áttekintés oldalán találhatók:

    * A **fiók tartománya** ugyanaz a tartomány, amelyet a **RemoteRenderingCoordinator** fiókjának tartományában használt.
    * **Active Directory alkalmazás ügyfél-azonosítója** a HRE alkalmazás-regisztrációjában (lásd az alábbi képen) található *Application (Client) azonosítót* .
    * Az **Azure-bérlő azonosítója** a HRE-alkalmazás regisztrációjában található *címtár-(bérlői) azonosító* (lásd az alábbi képet).
    * Az **Azure távoli renderelési fiók azonosítója** megegyezik a **REMOTERENDERINGCOORDINATOR** használt **fiók azonosítójával** .
    * A **fiók hitelesítési tartománya** megegyezik a **RemoteRenderingCoordinator** használt **fiók-hitelesítési tartománnyal** .

    ![Képernyőkép, amely kiemeli az alkalmazás (ügyfél) AZONOSÍTÓját és könyvtárát (bérlői AZONOSÍTÓját).](./media/app-overview-data.png)

1. Kattintson a Play (lejátszás) gombra az Unity Editorban, és a munkamenet futtatásához.
    Mivel a **AADAuthentication** összetevő rendelkezik egy nézet-vezérlővel, az automatikusan összekapcsolva jelenik meg a munkamenet-engedélyezési modális panel utáni rákérdezéssel.
1. Kövesse a panelen található utasításokat a **AppMenu** jobb oldalán.
    Ehhez a következőhöz hasonlónak kell megjelennie: ![ illusztráció, amely a AppMenu jobb oldalán megjelenő utasítás panelt jeleníti meg.](./media/device-flow-instructions.png)
    Miután megadta a megadott kódolást a másodlagos eszközön (vagy ugyanazon az eszközön található böngészőn), és bejelentkezik a hitelesítő adataival, a rendszer egy hozzáférési jogkivonatot ad vissza a kérelmező alkalmazásnak, ebben az esetben az Unity Editorban.
1. Ezen pont után az alkalmazás minden elemének a szokásos módon kell megjelennie. Ha nem halad a várt módon, ellenőrizze az Unity-konzolt.

## <a name="build-to-device"></a>Kiépítés eszközre

Ha MSAL használatával hoz létre alkalmazást, a projekt **eszközök** mappájában szerepelnie kell egy fájlnak. Ez segít a fordítónak helyesen felépíteni az alkalmazást az **oktatóanyag-eszközökben** foglalt *Microsoft.Identity.Client.dll* használatával.

1. Adjon hozzá egy új fájlt a **link.xml** nevű **eszközökhöz** .
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

Kövesse a rövid [útmutatóban található lépéseket: Unity minta üzembe helyezése HoloLens – a HoloLens való kiépítéshez](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project).

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag hátralévő része elméleti témákat tartalmaz az Azure távoli renderelést használó, éles használatra kész alkalmazások létrehozásához.

> [!div class="nextstepaction"]
> [Következő: kereskedelmi készültség](../commercial-ready/commercial-ready.md)