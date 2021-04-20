---
title: Távolról renderelt modell megtekintése
description: A Hello World "Azure Remote Rendering oktatóanyag bemutatja, hogyan lehet megtekinteni egy, az Azure által távolról renderelt modellt
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: f43e5b77580b7071ce48b39190c26a53f99f8cf5
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740169"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>Oktatóanyag: Távolról renderelt modell megtekintése

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Üzembe Azure Remote Rendering (ARR) példányt
> * Renderelési munkamenet létrehozása és befejezése
> * Meglévő renderelési munkamenet újbóli újrafelhasználása
> * Csatlakozás és a munkamenetek leválasztása
> * Modellek betöltése renderelési munkamenetbe

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következőre lesz szüksége:

* Aktív használat alapú Azure-előfizetés [Fiók létrehozása](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* Windows SDK 10.0.18362.0 [(letöltés)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Az Visual Studio 2019 [legújabb verziója (letöltés)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(letöltés)](https://git-scm.com/downloads)
* Unity (lásd [a támogatott verziók](../../../overview/system-requirements.md#unity) rendszerkövetelményét)
* A Unity és a C# nyelv középhaladó ismerete (például szkriptek és objektumok létrehozása, prefabs használata, Unity-események konfigurálása stb.)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>Üzembe Azure Remote Rendering (ARR) példányt

A Azure Remote Rendering eléréséhez először létre kell hoznia [egy fiókot.](../../../how-tos/create-an-account.md#create-an-account)

## <a name="create-a-new-unity-project"></a>Új Unity-projekt létrehozása

> [!TIP]
> Az [ARR-mintatár](https://github.com/Azure/azure-remote-rendering) egy projektet tartalmaz, amely az összes befejezett oktatóanyagot tartalmazza. Referenciaként használható. A teljes *Unity-projektet keresse meg a Unity\Tutorial-Complete* mappában.

A Unity Hubban hozzon létre egy új projektet.
Ebben a példában feltételezzük, hogy a projekt egy **RemoteRendering** nevű mappában jön létre.

:::image type="content" source="./media/unity-new-project.PNG" alt-text="Új Unity-projekt":::

## <a name="include-the-azure-remote-rendering-package"></a>A Azure Remote Rendering csomagba

[Kövesse az utasításokat](../../../how-tos/unity/install-remote-rendering-unity-package.md) a Azure Remote Rendering Unity-projekthez való hozzáadásához.


## <a name="configure-the-camera"></a>A kamera konfigurálása

1. Válassza a **Main Camera csomópontot.**

1. A helyi menü megnyitásához kattintson a jobb gombbal az *Átalakítás* összetevőre, és válassza az Alaphelyzetbe **állítás** lehetőséget:

    ![kamera-átalakítás alaphelyzetbe állítása](./media/camera-reset-transform.png)

1. A **Clear jelzők beállítása Folytonos** *színre*

1. Állítsa **a Háttérszínt** *Fekete* (#000000) teljes mértékben átlátszó (0) alfa (A) beállításra

    ![Színkerék](./media/color-wheel-black.png)

1. Állítsa **a Vágólapra a** Közel = *0,3 és* a Távol = *20 értékeket.* Ez azt jelenti, hogy a renderelés a 30 cm-nél közelebbi vagy 20 méternél távolabbi geometriát levágja.

    ![Unity kameratulajdonságok](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>A projektbeállítások módosítása

1. Nyissa *meg > Projektbeállítások szerkesztése...*
1. Válassza **a Quality (Minőség)** lehetőséget a bal oldali listából
1. Módosítsa az **összes platform alapértelmezett minőségi** szintjét *Alacsonyra.* Ez a beállítás lehetővé teszi a helyi tartalom hatékonyabb renderelését, és nincs hatással a távolról renderelt tartalom minőségére.

    ![projektminőségi beállítások módosítása](./media/settings-quality.png)

1. A **bal oldali lista** menüjében válassza a Grafikák lehetőséget
1. Módosítsa **a Scriptable Rendering Pipeline (Szkriptelhető renderelési folyamat)** beállítást *HybridRenderingPipeline beállításra*.\
    ![Képernyőkép arról, hogy hol változott a Scriptable Rendering Pipeline beállítás a HybridRenderingPipeline beállításra.](./media/settings-graphics-render-pipeline.png)\
    Előfordulhat, hogy a felhasználói felület nem tölti fel az elérhető folyamattípusok listáját a csomagokból. Ha ez történik, a *HybridRenderingPipeline* eszközt manuálisan kell a mezőre húzni:\
    ![a projektgrafikonok beállításainak módosítása](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > Ha nem tudja áthúzni a *HybridRenderingPipeline* adateszközt a Renderelési folyamat adateszköze mezőbe (valószínűleg azért, mert a mező nem létezik!), győződjön meg arról, hogy a csomagkonfiguráció tartalmazza a `com.unity.render-pipelines.universal` csomagot.

1. A **bal oldali** lista menüjében válassza a Player (Lejátszó) lehetőséget
1. Válassza a **Univerzális Windows-platform beállítások lapját,** amelyet Windows ikon jelöl.
1. Módosítsa az **XR-beállításokat** a Windows Mixed Reality az alábbiak szerint:
    1. A **Virtual Reality támogatása**
    1. Nyomja meg a "+" gombot, és adjon **hozzá Windows Mixed Reality**
    1. Mélységformátum **beállítása** *16 bites mélységre*
    1. Győződjön meg arról, hogy a **Mélységi puffer megosztása** engedélyezve van
    1. A **Sztereikus renderelési mód beállítása** Single Pass *Instancedre*

    ![lejátszó beállításai](./media/xr-player-settings.png)

1. Ugyanabban az ablakban, az **XR-beállítások felett bontsa** ki a **Közzétételi beállítások gombra**
1. Görgessen le a **Képességek lapra,** és válassza a következő lehetőségeket:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception (Térbeli pont)**
    * **PrivateNetworkClientServer** *(nem kötelező).* Válassza ezt a lehetőséget, ha csatlakoztatni szeretné a Unity távoli hibakeresőt az eszközhöz.

1. A **Támogatott eszközcsaládok alatt** engedélyezze a **Holographic és** a Desktop **alkalmazást**
1. A Project **Settings panel bezárása vagy dokkolása**
1. A *Fájl->buildbeállítások megnyitása*
1. Válassza **a Univerzális Windows-platform**
1. Konfigurálja a beállításokat úgy, hogy azok megegyeznek az alábbi beállításokkal
1. Kattintson a **Switch Platform (Platformváltás)** gombra.\
![buildbeállítások](./media/build-settings.png)
1. Miután a Unity platformot változtat, zárja be a build panelt.

## <a name="validate-project-setup"></a>A projekt beállításának ellenőrzése

A projektbeállítások helyességét az alábbi lépésekkel ellenőrizheti.

1. Válassza a **ValidateProject** bejegyzést a **Unity szerkesztő eszköztárának RemoteRendering** menüjében.
1. Tekintse át **a ValidateProject ablakot** a hibákért, és szükség esetén javítsa ki a projektbeállításokat.

    ![Unity-szerkesztő projektérvényesítése](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>Szkript létrehozása a kapcsolat és Azure Remote Rendering koordinálása

A távolról renderelt modellek megjelenítésének négy alapvető szakasza van, amelyek az alábbi folyamatábrában vannak felvázolva. Minden szakaszt sorrendben kell végrehajtani. A következő lépés egy olyan szkript létrehozása, amely kezeli az alkalmazás állapotát, és végiglépked minden szükséges fázison.

![ARR-verem 0](./media/remote-render-stack-0.png)

1. A *Project (Projekt) ablaktáblán* az **Assets (Eszközök) alatt hozzon** létre egy *remoteRenderingCore* nevű új mappát. Ezután a *RemoteRenderingCore parancsmagon belül* hozzon létre egy másik, *Scripts nevű mappát.*

1. Hozzon létre egy **remoteRenderingCoordinator** nevű új [C#-szkriptet.](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)
A projektnek így kell kinéznie:

    ![Projekthierarchia](./media/project-structure.png)

    Ez a koordinátor-szkript nyomon követi és kezeli a távoli renderelés állapotát. Fontos megjegyezni, hogy a kód egy része az állapot fenntartására, a funkciók más összetevők számára történő  felfedésére, események aktiválására és az alkalmazásspecifikus adatok tárolására használatos, amelyek nem kapcsolódnak közvetlenül a Azure Remote Rendering. Használja kiindulási pontként az alábbi kódot, és az oktatóanyag későbbi Azure Remote Rendering implementálja az adott kódhoz.

1. Nyissa **meg a RemoteRenderingCoordinator kódot** a kódszerkesztőben, és cserélje le a teljes tartalmát az alábbi kódra:

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

    // Account
    // RemoteRenderingDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // For most people '<region>' is either 'westus2' or 'westeurope'
    [SerializeField]
    private string remoteRenderingDomain = "westus2.mixedreality.azure.com";
    public string RemoteRenderingDomain
    {
        get => remoteRenderingDomain.Trim();
        set => remoteRenderingDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountDomain = "<enter your account domain here>";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
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
                Debug.LogFormat(LogType.Log, LogOption.NoStacktrace, null, "{0}", $"State changed to: {currentCoordinatorState}");
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
        return await Task.FromResult(new SessionConfiguration(AccountDomain, RemoteRenderingDomain, AccountId, AccountKey));
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
        bool sessionAvailable = false;
        try
        {
            RenderingSessionPropertiesArrayResult result = await ARRSessionService.Client.GetCurrentRenderingSessionsAsync();
            if (result.ErrorCode == Result.Success)
            {
                RenderingSessionProperties[] properties = result.SessionProperties;
                if (properties != null)
                {
                    sessionAvailable = properties.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
                }
            }
            else
            {
                Debug.LogError($"Failed to get current rendering sessions. Error: {result.Context.ErrorMessage}");
            }
        }
        catch (RRException ex)
        {
            Debug.LogError($"Failed to get current rendering sessions. Error: {ex.Message}");
        }
        return sessionAvailable;
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

## <a name="create-the-azure-remote-rendering-gameobject"></a>A Azure Remote Rendering GameObject létrehozása

A távoli renderelési koordinátor és a hozzá szükséges szkript *(ARRServiceUnity)* egyaránt MonoBehaviours, amelyek egy GameObjecthez kell csatolva a jelenetben. Az *ARRServiceUnity* szkriptet az ARR biztosítja, hogy elérhetővé tegye az ARR távoli munkamenetekhez való csatlakozására és kezelésére szolgáló funkcióinak nagy része.

1. Hozzon létre egy új GameObjectet a jelenetben (Ctrl+Shift+N vagy *GameObject->Create Empty*) és nevezze el **RemoteRenderingCoordinator néven.**
1. Adja hozzá *a RemoteRenderingCoordinator* szkriptet a **RemoteRenderingCoordinator** GameObject.\ parancsprogramhoz.
![RemoteRenderingCoordinator összetevő hozzáadása](./media/add-coordinator-script.png)
1. Győződjön meg arról, hogy az inspectorban Szolgáltatásként megjelenő *ARRServiceUnity* szkript automatikusan hozzáadódik a GameObjecthez.  Ha érdekli, ez az eredmény a `[RequireComponent(typeof(ARRServiceUnity))]` **RemoteRenderingCoordinator szkript tetején** található.
1. Adja hozzá Azure Remote Rendering hitelesítő adatait, a fióktartományt és a Remote Rendering tartományt a koordinátor-szkripthez:\
![Hitelesítő adatok hozzáadása](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>Inicializálja Azure Remote Rendering

Most, hogy már van keretrendszerünk a koordinátorunkhoz, implementáljuk mind a négy szakaszt, kezdve az **Inicializálással Remote Rendering.**

![ARR stack 1](./media/remote-render-stack-1.png)

**Az inicializálás** Azure Remote Rendering jelzi, hogy melyik kameraobjektumot kell használni a rendereléshez, és a **NotAuthorized (Nemhorizált)** állapotgépre halad át. Ez azt jelenti, hogy inicializálva van, de még nem jogosult egy munkamenethez való csatlakozásra. Mivel egy ARR-munkamenet indítása költségeket is jár, meg kell erősítenie, hogy a felhasználó folytatni kívánja a műveletet.

A **NotAuthorized** állapot beírásakor a **CheckAuthorization** lesz meghívva, amely meghívja a **RequestingAuthorization** eseményt, és meghatározza, hogy mely fiók hitelesítő adatait kell használni ( Az **AccountInfo** az osztály tetején van meghatározva, és a Unity Inspectorban a fenti lépésben meghatározott hitelesítő adatokat használja).

   > [!NOTE]
   > Az ARR nem támogatja a futásidejű újrabefejtést. Ha módosítja és menti a szkriptet, miközben a play mód aktív, a Unity lefagyhat, és kényszerítenie kell a leállítást a feladatkezelőn keresztül. Mindig ellenőrizze, hogy leállította-e a lejátszás módot a szkriptek szerkesztése előtt.

1. Cserélje le az **InitializeARR** és **az InitializeSessionService** tartalmát az alábbi befejezett kódra:

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

A **NotAuthorized** és a **NoSession** között való előrehaladáshoz általában egy modális párbeszédpanelt szeretnénk bemutatni a felhasználónak, hogy kiválasztható (és ezt egy másik fejezetben fogjuk megtenni). Jelenleg automatikusan megkerüljük az engedélyezési ellenőrzést a **ByPassAuthentication** hívásával, amint a **RequestingAuthorization** esemény aktiválódik.

1. Válassza ki **a RemoteRenderingCoordinator** GameObject et, és keresse meg az **OnRequestingAuthorization** Unity-eseményt, amely a **RemoteRenderingCoordinator** összetevő Vizsgálójében található.

1. Adjon hozzá egy új eseményt a jobb alsó sarokban a "+" billentyű lenyomásával.
1. Húzza az összetevőt a saját eseményére, hogy hivatkozni fog magára.\
![Hitelesítés megkerülése](./media/bypass-authorization-add-event.png)\
1. A legördülő menüben válassza a **RemoteRenderingCoordinator -> BypassAuthorization**.\ lehetőséget.
![A kiválasztott RemoteRenderingCoordinator.BypassAuthorization beállítást bemutató képernyőkép.](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>Távoli munkamenet létrehozása vagy csatlakozás hozzá

A második fázis egy új munkamenet létrehozása vagy Remote Rendering (további Remote Rendering [munkamenetek](../../../concepts/sessions.md) létrehozása).

![ARR stack 2](./media/remote-render-stack-2.png)

A távoli munkamenetben renderelik a modelleket. A **JoinRemoteSession( ) metódus** egy meglévő, a **LastUsedSessionID** tulajdonsággal nyomon követni kívánt munkamenetet kísérel meg, vagy ha van hozzárendelt aktív munkamenet-azonosító a **SessionIDOverride tulajdonságban.** **A SessionIDOverride** csak hibakeresési célokra szolgál, csak akkor használja, ha tudja, hogy a munkamenet létezik, és explicit módon szeretne csatlakozni hozzá.

Ha nincsenek elérhető munkamenetek, a rendszer új munkamenetet hoz létre. Egy új munkamenet létrehozása azonban időigényes művelet. Ezért csak szükség esetén próbáljon munkameneteket létrehozni, és amikor csak lehetséges, használja újra őket (a munkamenetek kezelésével kapcsolatos további információkért lásd: Kereskedelmi felhasználásra kész: Munkamenetek készletezése, [ütemezése és](../commercial-ready/commercial-ready.md#fast-startup-time-strategies) ajánlott eljárások).

> [!TIP]
> **A StopRemoteSession() leállítja** az aktív munkamenetet. A szükségtelen díjak elkerülése érdekében mindig állítsa le a munkameneteket, amikor már nincs rájuk szükség.

Az állapotgép a rendelkezésre álló munkamenetek függvényében a **ConnectingToNewRemoteSession** vagy **a ConnectingToExistingRemoteSession** állapotba lép. Ha megnyit egy meglévő munkamenetet, vagy új munkamenetet hoz létre, az aktiválja az **ARRSessionService.OnSessionStatusChanged** eseményt, és végrehajtja az **OnRemoteSessionStatusChanged metódust.** Ideális esetben ez az állapotgép **RemoteSessionReady** állapotra való fejlesztését eredményezi.

1. Új munkamenethez való csatlakozáshoz módosítsa a kódot úgy, hogy az lecseréli a **JoinRemoteSession( )** és a **StopRemoteSession( )** metódusokat az alábbi példákra:

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

Ha időt szeretne megtakarékosni a munkamenetek újrahasználása által, inaktiválja az Automatikus **munkamenet-leállítás** beállítást az *ARRServiceUnity összetevőben.* Ne feledje, hogy ez akkor is futni hagyja a munkameneteket, ha senki sincs hozzájuk csatlakoztatva. A munkamenet addig futhat, amíg a *MaxLeaseTime* értéke le nem áll a kiszolgáló számára (a *MaxLeaseTime* értéke az Remote Rendering Koordinátorban módosítható az Új munkamenet alapértelmezései *alatt).* Ha azonban a kapcsolat bontásakor automatikusan leállít minden munkamenetet, meg kell várnia, hogy minden alkalommal elinduljon egy új munkamenet, ami egy kissé hosszadalmas folyamat lehet.

> [!NOTE]
> A munkamenet leállítása azonnal életbe lép, és nem vonható vissza. Ha leállt, létre kell hoznia egy új munkamenetet, ugyanazokkal az indítási terheléssel.

## <a name="connect-the-local-runtime-to-the-remote-session"></a>A helyi futásidő csatlakoztatása a távoli munkamenethez

Ezután az alkalmazásnak csatlakoztatnia kell a helyi futásidejűt a távoli munkamenethez.

![ARR-verem 3](./media/remote-render-stack-3.png)

Az alkalmazásnak a futásidő és az aktuális munkamenet közötti kapcsolattal kapcsolatos eseményeket is figyelnie kell; Ezeket az állapotváltozásokat az **OnLocalRuntimeStatusChanged kezeli.** Ez a kód a **ConnectingToRuntime állapotra lép.** Miután csatlakozott az **OnLocalRuntimeStatusChanged** alatt, az állapot **a RuntimeConnected állapotra változik.** A runtime-hoz való csatlakozás az utolsó állapot, amellyel a koordinátornak aggályai vannak, ami azt jelenti, hogy az alkalmazás az összes közös konfigurációval elkészült, és készen áll a modellek betöltésével és rendereléssel kapcsolatos munkamenet-specifikus munka megkezdésére.

 1. Cserélje le a **ConnectRuntimeToRemoteSession( ) és** **a DisconnectRuntimeFromRemoteSession( )** metódust az alábbi befejezett verziókra.
 1. Fontos, hogy jegyezze fel a **LateUpdate** Unity-metódust, és hogy ez frissíti az aktuális aktív munkamenetet. Ez lehetővé teszi, hogy az aktuális munkamenet üzeneteket küldjön/fogad, és frissítse a keretpuffert a távoli munkamenetből fogadott képkockákra. Az ARR megfelelő működése kritikus fontosságú.

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
> A helyi futásidő távoli munkamenethez való csatlakoztatása attól függ, hogy az **aktuálisan** aktív munkameneten a Frissítés van-e meghívva. Ha azt találja, hogy az alkalmazás soha nem halad tovább **a ConnectingToRuntime** állapoton túl, ellenőrizze, hogy az aktív munkamenetben rendszeresen hívja-e az Update-et. 

## <a name="load-a-model"></a>Modell betöltése

A szükséges alapokkal készen áll arra, hogy betöltsen egy modellt a távoli munkamenetbe, és elkezdje a képkockák fogadását.

![A modell betöltésének és megtekintésének folyamatát bemutató ábra.](./media/remote-render-stack-4.png)

A **LoadModel metódus** a modell elérési útjának, folyamatkezelőinek és szülő-átalakításának elfogadására lett kialakítva. Ezekkel az argumentumokkal tölt be egy modellt a távoli munkamenetbe, frissíti a felhasználót a betöltési folyamat során, és el tudja látni a távolról renderelt modellt a szülő-átalakítás alapján.

1. Cserélje le **a LoadModel metódust** teljes egészében az alábbi kódra:

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

A fenti kód a következő lépéseket végzi el:

1. Hozzon létre [egy távoli entitást.](../../../concepts/entities.md)
1. Hozzon létre egy helyi GameObjectet, amely a távoli entitást képviseli.
1. Konfigurálja a helyi GameObjectet úgy, hogy az állapota (vagyis átalakítás) szinkronizálva legyenek a távoli entitással minden keretben.
1. Adjon meg egy nevet, és adjon hozzá egy [**WorldAnchort**](https://docs.unity3d.com/550/Documentation/ScriptReference/VR.WSA.WorldAnchor.html) a stabilitáshoz.
1. Modelladatok betöltése a Blob Storage a távoli entitásba.
1. Adja vissza a szülőentitást későbbi referenciaként.

## <a name="view-the-test-model"></a>A tesztmodell megtekintése

Most már minden kód megvan, amely egy távolról renderelt modell megtekintéséhez szükséges, és a távoli rendereléshez szükséges mind a négy szakasz befejeződött. Most hozzá kell adnunk egy kis kódot a modellbetöltési folyamat elindítani.

![ARR verem 4](./media/remote-render-stack-5.png)

1. Adja hozzá a következő kódot a **RemoteRenderingCoordinator osztályhoz,** pont a **LoadModel** metódus alatt:

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
    
    Ez a kód létrehoz egy GameObjectet, amely a tesztmodell szülőjeként működik. Ezután a **LoadModel** metódust hívja meg a "builtin://Engine" modell betöltéséhez, amely a Azure Remote Rendering-ba épített eszköz a renderelés tesztelése céljából.

1. Mentse a kódot.
1. Nyomja le a Play gombot a Unity-szerkesztőben a csatlakozás Azure Remote Rendering új munkamenet létrehozásához.
1. A Game (Játék) nézetben azonban nem sokat fog látni, a konzolon azonban az alkalmazás változó állapota látható. Ez valószínűleg a (igaz) lesz, és akár öt percig is `ConnectingToNewRemoteSession` ott maradhat.
1. A **RemoteRenderingCoordinator** GameObject kiválasztásával láthatja a csatolt szkripteket a vizsgálóban. Figyelje **meg, ahogy** a szolgáltatás-összetevő frissülni fog az inicializálási és a kapcsolódási lépések során.
1. Figyelje a konzol kimenetét – várja meg, amíg az állapot **RuntimeConnected (Futtatás) állapotra vált.**
1. A futtatás csatlakoztatása után kattintson a jobb gombbal a **RemoteRenderingCoordinator** elemre az vizsgálóban a helyi menü felfedéshez. Ezután kattintson a **Load Test Model** (Modell betöltése) lehetőségre a helyi menüben, amelyet a fenti kód rész `[ContextMenu("Load Test Model")]` ad hozzá.

    ![Betöltés a helyi menüből](./media/load-test-model.png)

1. Figyelje meg a Konzolon a **LoadModel** metódusba átadott **ProgressHandler** kimenetét.
1. Tekintse meg a távolról renderelt modellt!

> [!NOTE]
> A távoli modell soha nem lesz látható a Scene (Jelenet) nézetben, csak a játéknézetben. Ennek az az oka, hogy az ARR távolról rendereli a képkockákat kifejezetten a Game view kamera szempontjából, és nem ismeri a Szerkesztő kameráját (amely a Scene nézet megjelenítésére használatos).

## <a name="next-steps"></a>Következő lépések

![Betöltött modell](./media/test-model-rendered.png)

Gratulálunk! Létrehozott egy alapszintű alkalmazást, amely képes távolról renderelt modelleket a Azure Remote Rendering. A következő oktatóanyagban integráljuk az MRTK-t, és importáljuk a saját modelljeinket.

> [!div class="nextstepaction"]
> [Következő: Felületek és egyéni modellek](../custom-models/custom-models.md)
