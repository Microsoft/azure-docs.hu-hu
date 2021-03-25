---
title: Távolról renderelt modell megtekintése
description: Az Azure távoli renderelés "Hello World" oktatóanyaga bemutatja, hogyan tekinthetők meg az Azure által távolról megjelenített modellek
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: dd3596dd7b2cd01e80d9cf4db9bbc179048e7fcf
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105035965"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>Oktatóanyag: távolról renderelt modell megtekintése

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Azure-beli távoli renderelés (ARR) példányának kiépítése
> * Renderelési munkamenet létrehozása és leállítása
> * Meglévő renderelési munkamenet újrafelhasználása
> * Csatlakozás és a munkamenetek közötti kapcsolat bontása
> * Modellek betöltése renderelési munkamenetbe

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

* Aktív utólagos elszámolású Azure-előfizetés [fiók létrehozása](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* Windows SDK 10.0.18362.0 [(letöltés)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A Visual Studio 2019 legújabb verziója [(letöltés)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(letöltés)](https://git-scm.com/downloads)
* Unity (lásd a támogatott verziók [rendszerkövetelményeit](../../../overview/system-requirements.md#unity) )
* Az Unity és a C# nyelv közbenső ismerete (például parancsfájlok és objektumok létrehozása panelekkel, az egység eseményeinek konfigurálása stb.)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>Azure-beli távoli renderelés (ARR) példányának kiépítése

Ahhoz, hogy hozzáférjen az Azure távoli renderelési szolgáltatáshoz, először [létre kell hoznia egy fiókot](../../../how-tos/create-an-account.md#create-an-account).

## <a name="create-a-new-unity-project"></a>Új Unity-projekt létrehozása

> [!TIP]
> Az [ARR Samples adattár](https://github.com/Azure/azure-remote-rendering) tartalmaz egy projektet, amely az összes oktatóanyagot befejezte, hivatkozásként használható. A teljes Unity projekthez tekintse meg a *Unity\Tutorial-Complete* .

Hozzon létre egy új projektet az Unity hub-ból.
Ebben a példában feltételezzük, hogy a projekt létrehozása egy **RemoteRendering** nevű mappában történik.

:::image type="content" source="./media/unity-new-project.PNG" alt-text="Új Unity-projekt":::

## <a name="include-the-azure-remote-rendering-package"></a>Az Azure távoli renderelési csomag belefoglalása

[Kövesse az](../../../how-tos/unity/install-remote-rendering-unity-package.md) Azure-beli távoli renderelési csomag Unity-projekthez való hozzáadásának utasításait.


## <a name="configure-the-camera"></a>A kamera konfigurálása

1. Válassza ki a **fő kamera** csomópontot.

1. A helyi menü megnyitásához kattintson a jobb gombbal az *átalakítás* összetevőre, és válassza az **Alaphelyzetbe állítás** lehetőséget:

    ![kamera átalakításának alaphelyzetbe állítása](./media/camera-reset-transform.png)

1. **Tiszta jelzők** beállítása a *folytonos színre*

1. **Háttér** beállítása *feketére* (#000000), teljes transzparens (0) alfa (A) értékkel

    ![Színkerék](./media/color-wheel-black.png)

1. A **kivágási síkok** a *közel = 0,3* és a *Far = 20* értékre állíthatók be. Ez azt jelenti, hogy a renderelés a 30 cm-nél közelebbi vagy 20 méternél nagyobb méretű klipet ábrázol.

    ![Unity kamera tulajdonságai](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>A projekt beállításainak módosítása

1. Nyissa meg a *> projekt beállításainak szerkesztése...*
1. Válassza a **minőség** lehetőséget a bal oldali lista menüben
1. Az összes platform **alapértelmezett minőségi szintjének** módosítása *alacsonyra*. Ez a beállítás lehetővé teszi a helyi tartalmak hatékonyabb megjelenítését, és nem befolyásolja a távolról renderelt tartalom minőségét.

    ![a projekt minőségi beállításainak módosítása](./media/settings-quality.png)

1. Válasszon **grafikát** a bal oldali lista menüből
1. Módosítsa a **szkriptek renderelési folyamatának** beállítását a *HybridRenderingPipeline*. \
    ![Képernyőkép, amely arra utal, hogy hol változtatja meg a szkriptek renderelési folyamatának beállításait a HybridRenderingPipeline értékre.](./media/settings-graphics-render-pipeline.png)\
    Előfordulhat, hogy a felhasználói felület nem tölti fel a csomagok közül a rendelkezésre álló folyamat-típusok listáját. Ha ez történik, a *HybridRenderingPipeline* eszközt kézzel kell húzni a mezőre: \
    ![a Project Graphics beállításainak módosítása](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > Ha nem tudja áthúzni a *HybridRenderingPipeline* objektumot a renderelési folyamat objektuma mezőbe (valószínűleg azért, mert a mező nem létezik!), győződjön meg arról, hogy a csomag konfigurációja tartalmazza a `com.unity.render-pipelines.universal` csomagot.

1. Válassza ki a **lejátszót** a bal oldali lista menüből
1. Válassza a Windows-ikonként jelölt **univerzális Windows-platform beállítások** lapot.
1. Módosítsa az **XR beállításait** a Windows vegyes valóságának támogatásához az alábbi ábrán látható módon:
    1. **Virtuális valóság támogatásának** engedélyezése
    1. Nyomja meg a "+" gombot, és vegye fel a **Windows vegyes valóságot**
    1. **Mélységi formátum** beállítása *16 bites mélységre*
    1. Győződjön meg arról, hogy engedélyezve van a **mélységi puffer megosztása**
    1. **Sztereó renderelési mód** beállítása *egyszeri pass-példányra*

    ![lejátszó beállításai](./media/xr-player-settings.png)

1. Ugyanebben az ablakban, az **XR beállításai** felett bontsa ki a **közzétételi beállítások** elemet.
1. Görgessen le a **képességek** menüponthoz, és válassza a következőket:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * **PrivateNetworkClientServer** (nem *kötelező*). Válassza ezt a lehetőséget, ha az Unity távoli hibakeresőt az eszközhöz szeretné kapcsolni.

1. A **támogatott eszközökhöz tartozó családok** területen engedélyezze a **holografikus** és **asztali**
1. A **Project Settings** panel lezárása vagy dokkolása
1. A *fájl->Build beállításainak* megnyitása
1. **Univerzális Windows-platform** kiválasztása
1. Konfigurálja úgy a beállításokat, hogy megfeleljenek az alább találhatóknak
1. Nyomja meg a **platform váltása** gombot. \
![összeállítási beállítások](./media/build-settings.png)
1. Az egység módosítása után lépjen a Build panelre.

## <a name="validate-project-setup"></a>A projekt beállításának ellenőrzése

A következő lépések végrehajtásával ellenőrizheti, hogy helyesek-e a projekt beállításai.

1. Válassza ki a **ValidateProject** bejegyzést a **RemoteRendering** menüből az Unity Editor eszköztáron.
1. A hibákért tekintse át a **ValidateProject** ablakot, és szükség esetén javítsa ki a projekt beállításait.

    ![Unity Editor projekt ellenőrzése](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>Parancsfájl létrehozása az Azure távoli renderelési kapcsolatok és az állapot koordinálásához

Az alábbi folyamatábrában négy alapvető szakasz mutatja be a távolról renderelt modellek megjelenítését. Az egyes fázisokat sorrendben kell végrehajtani. A következő lépés egy olyan parancsfájl létrehozása, amely kezeli az alkalmazás állapotát, és folytatja az egyes szükséges szakaszokat.

![ARR verem 0](./media/remote-render-stack-0.png)

1. A *projekt* ablaktábla **eszközök** területén hozzon létre egy új, *RemoteRenderingCore* nevű mappát. Ezután a *RemoteRenderingCore* belül hozzon létre egy másik, *parancsfájl* nevű mappát.

1. Hozzon létre egy új, **RemoteRenderingCoordinator** nevű [C#-szkriptet](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) .
A projektnek így kell kinéznie:

    ![Projekt-hierarchia](./media/project-structure.png)

    Ez a koordinátori parancsfájl nyomon követheti és felügyelheti a távoli renderelési állapotot. Megjegyzés: a kód egy részét az állapot fenntartására, a más összetevők működésének kiváltására, az események aktiválására és az olyan alkalmazásspecifikus adatok tárolására használja, amelyek nem kapcsolódnak *közvetlenül* az Azure távoli rendereléshez. Kiindulási pontként használja az alábbi kódot, és az oktatóanyag későbbi részében megkeresjük és Implementáljuk az adott Azure távoli renderelési kódot.

1. Nyissa meg a **RemoteRenderingCoordinator** a Kódszerkesztőben, és cserélje le a teljes tartalmat az alábbi kódra:

```cs
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Linq;
using System.Threading.Tasks;
using UnityEngine;
using UnityEngine.Events;

#if UNITY_WSA
using UnityEngine.XR.WSA;
#endif

/// <summary>
/// Remote Rendering Coordinator is the controller for all Remote Rendering operations.
/// </summary>

// Require the GameObject with a RemoteRenderingCoordinator to also have the ARRServiceUnity component
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRenderingCoordinator : MonoBehaviour
{
    public enum RemoteRenderingState
    {
        NotSet,
        NotInitialized,
        NotAuthorized,
        NoSession,
        ConnectingToExistingRemoteSession,
        ConnectingToNewRemoteSession,
        RemoteSessionReady,
        ConnectingToRuntime,
        RuntimeConnected
    }

    public static RemoteRenderingCoordinator instance;

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // The list of regions is available at https://docs.microsoft.com/azure/remote-rendering/reference/regions
    [SerializeField]
    private string accountDomain = "westus2.mixedreality.azure.com";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountAuthenticationDomain = "<enter your account authentication domain here>";
    public string AccountAuthenticationDomain
    {
        get => accountAuthenticationDomain.Trim();
        set => accountAuthenticationDomain = value;
    }   

    [SerializeField]
    private string accountKey = "<enter your account key here>";
    public string AccountKey {
        get => accountKey.Trim();
        set => accountKey = value;
    }

    // These settings are important. All three should be set as low as possible, while maintaining a good user experience
    // See the documentation around session management and the technical differences in session VM size
    [Header("New Session Defaults")]

    public RenderingSessionVmSize renderingSessionVmSize = RenderingSessionVmSize.Standard;
    public uint maxLeaseHours = 0;
    public uint maxLeaseMinutes = 20;

    [Header("Other Configuration")]

    [Tooltip("If you have a known active SessionID, you can fill it in here before connecting")]
    [SerializeField]
    private string sessionIDOverride;
    public string SessionIDOverride {
        get => sessionIDOverride.Trim();
        set => sessionIDOverride = value;
    }

    // When Automatic Mode is true, the coordinator will attempt to automatically proceed through the process of connecting and loading a model
    public bool automaticMode = true;

    public event Action RequestingAuthorization;
    public UnityEvent OnRequestingAuthorization = new UnityEvent();

    public event Action AuthorizedChanged;
    public UnityEvent OnAuthorizationChanged = new UnityEvent();
    private bool authorized;
    public bool Authorized
    {
        get => authorized;
        set
        {
            if (value == true) //This is a one-way value, once we're authorized it lasts until the app is shutdown.
            {
                authorized = value;
                AuthorizedChanged?.Invoke();
            }
        }
    }

    public delegate Task<SessionConfiguration> AccountInfoGetter();

    public static AccountInfoGetter ARRCredentialGetter
    {
        private get;
        set;
    }

    private RemoteRenderingState currentCoordinatorState = RemoteRenderingState.NotSet;
    public RemoteRenderingState CurrentCoordinatorState
    {
        get => currentCoordinatorState;
        private set
        {
            if (currentCoordinatorState != value)
            {
                currentCoordinatorState = value;
                Debug.Log($"State changed to: {currentCoordinatorState}");
                CoordinatorStateChange?.Invoke(currentCoordinatorState);
            }
        }
    }

    public static event Action<RemoteRenderingState> CoordinatorStateChange;

    public static RenderingSession CurrentSession => instance?.ARRSessionService?.CurrentActiveSession;

    private ARRServiceUnity arrSessionService;

    private ARRServiceUnity ARRSessionService
    {
        get
        {
            if (arrSessionService == null)
                arrSessionService = GetComponent<ARRServiceUnity>();
            return arrSessionService;
        }
    }

    private async Task<SessionConfiguration> GetDevelopmentCredentials()
    {
        Debug.LogWarning("Using development credentials! Not recommended for production.");
        return await Task.FromResult(new SessionConfiguration(AccountAuthenticationDomain, AccountDomain, AccountId, AccountKey));
    }

    /// <summary>
    /// Keep the last used SessionID, when launching, connect to this session if its available
    /// </summary>
    private string LastUsedSessionID
    {
        get
        {
            if (!string.IsNullOrEmpty(SessionIDOverride))
                return SessionIDOverride;

            if (PlayerPrefs.HasKey("LastUsedSessionID"))
                return PlayerPrefs.GetString("LastUsedSessionID");
            else
                return null;
        }
        set
        {
            PlayerPrefs.SetString("LastUsedSessionID", value);
        }
    }

    public void Awake()
    {
        //Forward events to Unity events
        RequestingAuthorization += () => OnRequestingAuthorization?.Invoke();
        AuthorizedChanged += () => OnAuthorizationChanged?.Invoke();

        //Attach to event
        AuthorizedChanged += RemoteRenderingCoordinator_AuthorizedChanged;

        if (instance == null)
            instance = this;
        else
            Destroy(this);

        CoordinatorStateChange += AutomaticMode;

        CurrentCoordinatorState = RemoteRenderingState.NotInitialized;
    }

    private void RemoteRenderingCoordinator_AuthorizedChanged()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.NotAuthorized)
            return; //This isn't valid from any other state than NotAuthorized


        //We just became authorized to connect to Azure
        InitializeSessionService();
    }

    /// <summary>
    /// Automatic mode attempts to automatically progress through the connection and loading steps. Doesn't handle error states.
    /// </summary>
    /// <param name="currentState">The current state</param>
    private async void AutomaticMode(RemoteRenderingState currentState)
    {
        if (!automaticMode)
            return;

        //Add a small delay for visual effect
        await Task.Delay(1500);
        switch (currentState)
        {
            case RemoteRenderingState.NotInitialized:
                InitializeARR();
                break;
            case RemoteRenderingState.NotAuthorized:
                RequestAuthorization();
                break;
            case RemoteRenderingState.NoSession:
                JoinRemoteSession();
                break;
            case RemoteRenderingState.RemoteSessionReady:
                ConnectRuntimeToRemoteSession();
                break;
        }
    }

    /// <summary>
    /// Initializes ARR, associating the main camera
    /// Note: This must be called on the main Unity thread
    /// </summary>
    public void InitializeARR()
    {
        //Implement me
    }

    /// <summary>
    /// Create a new remote session manager
    /// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
    /// </summary>
    public async void InitializeSessionService()
    {
        //Implement me
    }

    /// <summary>
    /// Trigger the event for checking authorization, respond to this event by prompting the user for authentication
    /// If authorized, set Authorized = true
    /// </summary>
    public void RequestAuthorization()
    {
        RequestingAuthorization?.Invoke();
    }

    public void BypassAuthorization()
    {
        Authorized = true;
    }

    /// <summary>
    /// Attempts to join an existing session or start a new session
    /// </summary>
    public async void JoinRemoteSession()
    {
        //Implement me
    }

    public void StopRemoteSession()
    {
        //Implement me
    }

    private async Task<bool> IsSessionAvailable(string sessionID)
    {
        var allSessions = await ARRSessionService.Client.GetCurrentRenderingSessionsAsync();
        return allSessions.SessionProperties.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
    }

    /// <summary>
    /// Connects the local runtime to the current active session, if there's a session available
    /// </summary>
    public void ConnectRuntimeToRemoteSession()
    {
        //Implement me
    }

    public void DisconnectRuntimeFromRemoteSession()
    {
        //Implement me
    }

    /// <summary>
    /// The session must have its runtime pump updated.
    /// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
    /// </summary>
    private void LateUpdate()
    {
        ARRSessionService?.CurrentActiveSession?.Connection?.Update();
    }

    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelPath">The model's path</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Implement me
        return null;
    }

    private async void OnRemoteSessionStatusChanged(ARRServiceUnity caller, RenderingSession session)
    {
        var properties = await session.GetPropertiesAsync();

        switch (properties.SessionProperties.Status)
        {
            case RenderingSessionStatus.Error:
            case RenderingSessionStatus.Expired:
            case RenderingSessionStatus.Stopped:
            case RenderingSessionStatus.Unknown:
                CurrentCoordinatorState = RemoteRenderingState.NoSession;
                break;
            case RenderingSessionStatus.Starting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
                break;
            case RenderingSessionStatus.Ready:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }

    private void OnLocalRuntimeStatusChanged(ConnectionStatus status, Result error)
    {
        switch (status)
        {
            case ConnectionStatus.Connected:
                CurrentCoordinatorState = RemoteRenderingState.RuntimeConnected;
                break;
            case ConnectionStatus.Connecting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
                break;
            case ConnectionStatus.Disconnected:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }
}
```

## <a name="create-the-azure-remote-rendering-gameobject"></a>Az Azure távoli renderelési GameObject létrehozása

A távoli renderelési koordinátor és a szükséges parancsfájl (*ARRServiceUnity*) mindkét MonoBehaviours, amelyet a jelenet egyik GameObject kell csatolni. A *ARRServiceUnity* parancsfájlt az ARR biztosítja, hogy a távoli munkamenetek eléréséhez és kezeléséhez használt ARR-funkciók nagy részét tegye elérhetővé.

1. Hozzon létre egy új GameObject a jelenetben (Ctrl + Shift + N vagy *GameObject->Create Empty*), és nevezze el **RemoteRenderingCoordinator**.
1. Adja hozzá a *RemoteRenderingCoordinator* parancsfájlt a **RemoteRenderingCoordinator** GameObject. \
![RemoteRenderingCoordinator-összetevő hozzáadása](./media/add-coordinator-script.png)
1. Erősítse meg, hogy a *ARRServiceUnity* parancsfájl, amely a Inspector *szolgáltatásként* jelenik meg, automatikusan hozzáadódik a GameObject. Ha kíváncsi, ez az eredmény a `[RequireComponent(typeof(ARRServiceUnity))]` **RemoteRenderingCoordinator** szkript elejétől.
1. Adja hozzá az Azure távoli renderelési hitelesítő adatait, a fiók hitelesítési tartományát és a fiók tartományát a koordinátori parancsfájlhoz: \
![Hitelesítő adatok hozzáadása](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>Az Azure távoli renderelésének inicializálása

Most, hogy már rendelkezünk a koordinátori keretrendszerrel, a négy fázist fogjuk megvalósítani a **távoli renderelés inicializálásával**.

![ARR verem 1](./media/remote-render-stack-1.png)

Az **inicializálás** azt mutatja be, hogy az Azure távoli renderelés melyik kamera-objektumot használja az állapot megjelenítéséhez és a **NotAuthorized** való előrehaladáshoz. Ez azt jelenti, hogy inicializálva van, de még nem rendelkezik jogosultsággal a munkamenethez való kapcsolódáshoz. Mivel az ARR-munkamenetek megkezdése költségekkel jár, meg kell erősítenie a felhasználót a folytatáshoz.

A **NotAuthorized** állapotának megadásakor a rendszer meghívja a **CheckAuthorization** , amely meghívja a **RequestingAuthorization** eseményt, és meghatározza, hogy melyik fiók hitelesítő adatait szeretné használni (a **AccountInfo** az osztály felső részén van meghatározva, és a fenti lépésben a Unity Inspector segítségével megadott hitelesítő adatokat használja.)

   > [!NOTE]
   > Az ARR nem támogatja a futtatókörnyezet újrafordítását. Ha módosítja a szkriptet, és menti azt, miközben a lejátszási mód aktív, az Unity befagyasztást eredményezhet, és kényszeríteni kell a feladat-kezelőn keresztüli leállítást. A parancsfájlok szerkesztése előtt mindig győződjön meg arról, hogy leállította a lejátszási módot.

1. Cserélje le a **InitializeARR** és a **InitializeSessionService** tartalmát az alábbi befejezett kóddal:

 ```cs
/// <summary>
/// Initializes ARR, associating the main camera
/// Note: This must be called on the main Unity thread
/// </summary>
public void InitializeARR()
{
    RemoteManagerUnity.InitializeManager(new RemoteUnityClientInit(Camera.main));

    CurrentCoordinatorState = RemoteRenderingState.NotAuthorized;
}

/// <summary>
/// Create a new remote session manager
/// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
/// </summary>
public async void InitializeSessionService()
{
    if (ARRCredentialGetter == null)
        ARRCredentialGetter = GetDevelopmentCredentials;

    var accountInfo = await ARRCredentialGetter.Invoke();

    ARRSessionService.OnSessionStatusChanged += OnRemoteSessionStatusChanged;

    ARRSessionService.Initialize(accountInfo);

    CurrentCoordinatorState = RemoteRenderingState.NoSession;
}
```

Ahhoz, hogy a **NotAuthorized** -ről a további **munkamenetre** lehessen jutni, általában egy modális párbeszédpanelt mutatunk be a felhasználónak, hogy kiválasszák (és ezt egy másik fejezetben tesszük). Egyelőre a **ByPassAuthentication** meghívásával automatikusan kikerüljük az engedélyezési ellenőrzéseket, amint a **RequestingAuthorization** esemény elindul.

1. Válassza ki a **RemoteRenderingCoordinator** GameObject, és keresse meg a **RemoteRenderingCoordinator** összetevő Ellenőrében elérhető **OnRequestingAuthorization** Unity eseményt.

1. Adjon hozzá egy új eseményt a jobb alsó sarokban található "+" gomb megnyomásával.
1. Húzza az összetevőt a saját eseményre, és hivatkozzon magára. \
![Hitelesítés mellőzése](./media/bypass-authorization-add-event.png)\
1. A legördülő listából válassza a **RemoteRenderingCoordinator-> BypassAuthorization**. \
![A kiválasztott RemoteRenderingCoordinator. BypassAuthorization beállítást megjelenítő képernyőkép.](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>Távoli munkamenet létrehozása vagy csatlakoztatása

A második lépés egy távoli renderelési munkamenet létrehozása vagy csatlakoztatása (további információért lásd a [távoli renderelési munkameneteket](../../../concepts/sessions.md) ).

![ARR verem 2](./media/remote-render-stack-2.png)

A távoli munkamenetben a modellek lesznek megjelenítve. A **JoinRemoteSession ()** metódus megpróbál csatlakozni egy meglévő munkamenethez, nyomon követve a **LastUsedSessionID** tulajdonsággal, vagy ha van hozzárendelve aktív munkamenet-azonosító a **SessionIDOverride**-on. A **SessionIDOverride** kizárólag hibakeresési célokra szolgál, csak akkor használható, ha ismeri a munkamenetet, és szeretne explicit módon csatlakozni hozzá.

Ha nem áll rendelkezésre munkamenet, új munkamenet jön létre. Egy új munkamenet létrehozása azonban időigényes művelet. Ezért csak szükség esetén kell létrehoznia a munkameneteket, és ha lehetséges, újra fel kell használni őket (lásd: [kereskedelmi használatra kész: munkamenet-készletezés, ütemezés és ajánlott eljárások](../commercial-ready/commercial-ready.md#fast-startup-time-strategies) a munkamenetek kezelésével kapcsolatban).

> [!TIP]
> A **StopRemoteSession ()** befejezi az aktív munkamenetet. A szükségtelen költségek elkerülése érdekében mindig le kell állítania a munkameneteket, ha már nincs rájuk szükség.

Az állapotú gép mostantól a rendelkezésre álló munkamenetek függvényében a **ConnectingToNewRemoteSession** vagy a **ConnectingToExistingRemoteSession** folyamatba kerül. Ha egy meglévő munkamenetet nyit meg, vagy új munkamenetet hoz létre, a rendszer elindítja a **ARRSessionService. OnSessionStatusChanged** eseményt, és végrehajtja a **OnRemoteSessionStatusChanged** metódust. Ideális esetben ez azt eredményezi, hogy az állapotot a gép **RemoteSessionReady**.

1. Új munkamenethez való csatlakozáshoz módosítsa a kódot a **JoinRemoteSession ()** és a **StopRemoteSession ()** metódus cseréjére az alábbi befejezett példákkal:

```cs
/// <summary>
/// Attempts to join an existing session or start a new session
/// </summary>
public async void JoinRemoteSession()
{
    //If there's a session available that previously belonged to us, and it's ready, use it. Otherwise start a new session.
    RenderingSessionProperties joinResult;
    if (await IsSessionAvailable(LastUsedSessionID))
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToExistingRemoteSession;
        joinResult = await ARRSessionService.OpenSession(LastUsedSessionID);
    }
    else
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
        joinResult = await ARRSessionService.StartSession(new RenderingSessionCreationOptions(renderingSessionVmSize, (int)maxLeaseHours, (int)maxLeaseMinutes));
    }

    if (joinResult.Status == RenderingSessionStatus.Ready || joinResult.Status == RenderingSessionStatus.Starting)
    {
        LastUsedSessionID = joinResult.Id;
    }
    else
    {
        //The session should be ready or starting, if it's not, something went wrong
        await ARRSessionService.StopSession();
        if(LastUsedSessionID == SessionIDOverride)
            SessionIDOverride = "";
        CurrentCoordinatorState = RemoteRenderingState.NoSession;
    }
}

public void StopRemoteSession()
{
    if (ARRSessionService.CurrentActiveSession != null)
    {
        ARRSessionService.CurrentActiveSession.StopAsync();
    }
}
```

Ha időt szeretne megtakarítani a munkamenetek újbóli használatával, ügyeljen arra, hogy inaktiválja az **automatikus leállítási munkamenetet** a *ARRServiceUnity* összetevőben. Ne feledje, hogy ez a munkamenetek futását is elhagyja, még akkor is, ha senki sem csatlakozik hozzájuk. A munkamenet addig futhat mindaddig, amíg a *MaxLeaseTime* a kiszolgáló leállása előtt (a *MaxLeaseTime* értéke módosítható a távoli renderelési Koordinátorban az *új munkamenet alapértelmezései* között). Ha azonban a kapcsolat bontásakor automatikusan leállítja az összes munkamenetet, meg kell várnia, hogy az új munkamenet minden alkalommal induljon el, ami valamivel hosszadalmas folyamat lehet.

> [!NOTE]
> A munkamenet leállítása azonnal érvénybe lép, és nem vonható vissza. Ha leállt, létre kell hoznia egy új munkamenetet ugyanazzal az indítási terheléssel.

## <a name="connect-the-local-runtime-to-the-remote-session"></a>A helyi futtatókörnyezet összekötése a távoli munkamenettel

Ezután az alkalmazásnak kapcsolódnia kell a helyi futtatókörnyezethez a távoli munkamenethez.

![ARR verem 3](./media/remote-render-stack-3.png)

Az alkalmazásnak a futtatókörnyezet és az aktuális munkamenet közötti kapcsolat eseményeiről is figyelnie kell. ezeket az állapot-változásokat a rendszer a **OnLocalRuntimeStatusChanged** kezeli. Ez a kód a **ConnectingToRuntime** állapotát fogja előre megtenni. A **OnLocalRuntimeStatusChanged**-ben való csatlakozás után az állapot továbbra is **RuntimeConnected**. A futtatókörnyezethez való csatlakozás utolsó olyan állapotban van, amely a koordinátorra vonatkozik, ami azt jelenti, hogy az alkalmazás az összes közös konfigurációval elkészült, és készen áll a betöltési és renderelési modellek munkamenet-specifikus működésének megkezdésére.

 1. Cserélje le a **ConnectRuntimeToRemoteSession ()** és a **DisconnectRuntimeFromRemoteSession ()** metódust az alábbi befejezett verzióra.
 1. Fontos megjegyezni az **LateUpdate** Unity metódust, valamint azt, hogy az aktuális aktív munkamenetet frissíti. Ez lehetővé teszi az aktuális munkamenet számára üzenetek küldését/fogadását és a keret pufferének frissítését a távoli munkamenetből fogadott keretekkel. Fontos, hogy az ARR megfelelően működjön.

```cs
/// <summary>
/// Connects the local runtime to the current active session, if there's a session available
/// </summary>
public void ConnectRuntimeToRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null)
    {
        Debug.LogError("Not ready to connect runtime");
        return;
    }

    //Connect the local runtime to the currently connected session
    //This session is set when connecting to a new or existing session

    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged += OnLocalRuntimeStatusChanged;
    ARRSessionService.CurrentActiveSession.ConnectAsync(new RendererInitOptions());
    CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
}

public void DisconnectRuntimeFromRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null || ARRSessionService.CurrentActiveSession.ConnectionStatus != ConnectionStatus.Connected)
    {
        Debug.LogError("Runtime not connected!");
        return;
    }

    ARRSessionService.CurrentActiveSession.Disconnect();
    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged -= OnLocalRuntimeStatusChanged;
    CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
}

/// <summary>
/// The session must have its runtime pump updated.
/// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
/// </summary>
private void LateUpdate()
{
    ARRSessionService?.CurrentActiveSession?.Connection?.Update();
}
```

> [!NOTE]
> A helyi futtatókörnyezet távoli munkamenethez való csatlakoztatása a jelenleg aktív munkameneten meghívott **frissítéstől** függ. Ha úgy találja, hogy az alkalmazás még soha nem halad előre a **ConnectingToRuntime** állapotban, győződjön meg róla, hogy rendszeresen hívja meg a **frissítést** az aktív munkamenetben.

## <a name="load-a-model"></a>Modell betöltése

A szükséges alapszintű létrehozás után készen áll a modell betöltésére a távoli munkamenetbe, és a keretek fogadásának megkezdése.

![A modell betöltésének és megtekintésének előkészítési folyamatát bemutató diagram.](./media/remote-render-stack-4.png)

A **LoadModel** metódus úgy van kialakítva, hogy elfogadja a modell elérési útját, a folyamatjelzőt és a szülő-átalakítást. Ezek az argumentumok a modellnek a távoli munkamenetbe való betöltéséhez használhatók, frissíti a felhasználót a betöltési folyamaton, majd a fölérendelt átalakító alapján a távolról megjelenített modellt.

1. Cserélje le a **LoadModel** metódust teljes egészében az alábbi kódra:

    ```cs
    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelName">The model's path</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
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
        var loadModelParams = new LoadModelFromSasOptions(modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

A fenti kód a következő lépéseket hajtja végre:

1. Hozzon létre egy [távoli entitást](../../../concepts/entities.md).
1. Hozzon létre egy helyi GameObject, amely a távoli entitást jelöli.
1. Konfigurálja úgy a helyi GameObject, hogy szinkronizálja az állapotát (azaz átalakítja) a távoli entitásba minden keretben.
1. Állítson be egy nevet, és adjon hozzá egy [**WorldAnchor**](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) a stabilizáció támogatásához.
1. Blob Storage modell adatainak betöltése a távoli entitásba.
1. A fölérendelt entitás visszaküldése a későbbi hivatkozáshoz.

## <a name="view-the-test-model"></a>A tesztelési modell megtekintése

Most már minden kód szükséges a távolról renderelt modell megtekintéséhez, a távoli renderelés összes szükséges szakasza befejeződött. Most hozzá kell adnia egy kis kódot a modell betöltési folyamatának elindításához.

![ARR verem 4](./media/remote-render-stack-5.png)

1. Adja hozzá a következő kódot a **RemoteRenderingCoordinator** osztályhoz, amely közvetlenül a **LoadModel** metódus alá esik:

    ```cs
    private bool loadingTestModel = false;
    [ContextMenu("Load Test Model")]
    public async void LoadTestModel()
    {
        if(CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if(loadingTestModel)
        {
            Debug.Log("Test model already loading or loaded!");
            return;
        }
        loadingTestModel = true;
    
        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("TestModelParent");
        testParent.transform.position = new Vector3(0f, 0f, 3f);
    
        // The 'built in engine path' is a special path that references a test model built into Azure Remote Rendering.
        await LoadModel("builtin://Engine", testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```
    
    Ez a kód egy GameObject hoz létre, amely szülőként működik a tesztelési modellben. Ezután meghívja a **LoadModel** metódust a "Builtin://Engine" modell betöltésére, amely egy, az Azure Remote rendering szolgáltatásba beépített eszköz, amely a renderelés tesztelésére szolgál.

1. Mentse a kódot.
1. A Unity Editor Lejátszás gombjára kattintva elindíthatja az Azure-beli távoli rendereléshez való csatlakozás folyamatát, és új munkamenetet hozhat létre.
1. A játék nézetében nem sokat fog látni, de a konzolon megjelenik az alkalmazás állapota változó. Ez várhatóan a következő időpontig fog haladni `ConnectingToNewRemoteSession` , és ott is marad, akár öt percig is.
1. Válassza ki a **RemoteRenderingCoordinator** GameObject, és tekintse meg a csatolt parancsfájlokat az ellenőrben. Tekintse meg a **szolgáltatás** -összetevő frissítését, ahogy az az inicializálási és a kapcsolati lépésein keresztül halad.
1. A konzol kimenetének figyelése – várakozás arra, hogy az állapot változása **RuntimeConnected**.
1. Ha a futtatókörnyezet csatlakoztatva van, kattintson a jobb gombbal a **RemoteRenderingCoordinator** a helyi menüben elérhetővé tenni. Ezután kattintson a **terhelési modell** beállítása lehetőségre a helyi menüben, amelyet a `[ContextMenu("Load Test Model")]` fenti kód része ad hozzá.

    ![Betöltés a helyi menüből](./media/load-test-model.png)

1. Tekintse meg a konzolon a **LoadModel** metódusnak átadott **ProgressHandler** kimenetét.
1. Tekintse meg a távolról renderelt modellt!

> [!NOTE]
> A távoli modell soha nem jelenik meg a jelenet nézetben, csak a játék nézetben. Ennek az az oka, hogy az ARR a képkockákat távolról teszi elérhetővé a game View kamera szempontjából, és nem ismeri a szerkesztői kamerát (a jelenet nézet megjelenítéséhez használatos).

## <a name="next-steps"></a>Következő lépések

![Modell betöltve](./media/test-model-rendered.png)

Gratulálunk! Létrehozott egy alapszintű alkalmazást, amely képes a távolról renderelt modellek megtekintésére az Azure távoli renderelés használatával. A következő oktatóanyagban integráljuk a MRTK, és importáljuk a saját modelljeiket.

> [!div class="nextstepaction"]
> [Next: felületek és egyéni modellek](../custom-models/custom-models.md)
