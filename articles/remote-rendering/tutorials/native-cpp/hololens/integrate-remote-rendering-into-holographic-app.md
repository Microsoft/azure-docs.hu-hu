---
title: Távoli renderelés integrálása C++/DirectX11 holografikus alkalmazásba
description: Ismerteti, hogyan integrálható a távoli renderelés egy egyszerű C++/DirectX11 holografikus alkalmazásba a Visual Studio Project varázslóval.
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: a07a8a9c50e0f71daa48f65ebf8c2e7a7f166cc5
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594299"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>Oktatóanyag: távoli renderelés integrálása HoloLens holografikus alkalmazásba

Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * A Visual Studio használata a HoloLens üzembe helyezhető holografikus alkalmazások létrehozásához
> * Adja hozzá a szükséges kódrészleteket és a projekt beállításait a helyi renderelés távoli renderelt tartalommal való összevonásához

Ez az oktatóanyag a szükséges bitek natív mintához való hozzáadását ismerteti `Holographic App` , hogy kombinálja a helyi renderelést az Azure távoli rendereléssel. Ebben az alkalmazásban az egyetlen, a Visual Studióban található hibakeresési kimeneti panelen keresztüli visszajelzés, ezért javasoljuk, hogy a mintát a Visual studión belül indítsa el. A megfelelő alkalmazásbeli visszajelzések hozzáadása a minta hatókörén kívül esik, mert a dinamikus szövegmezők a semmiből való létrehozása sok kódolást eredményez. Jó kiindulási pont az osztály `StatusDisplay` , amely része a [távelérési](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content)szolgáltatásnak a githubon. Valójában az oktatóanyag előre definiált verziója az adott osztály helyi példányát használja.

> [!TIP]
> Az [ARR-minták tárháza](https://github.com/Azure/azure-remote-rendering) tartalmazza az oktatóanyag eredményét, amely a használatra kész Visual Studio-projekt. Emellett a felhasználói felületi osztályon keresztül is megfelelő hiba-és állapotjelentés-jelentéskészítéssel bővült `StatusDisplay` . Az oktatóanyagban az összes ARR-kiegészítés a hatókörén belül van `#ifdef USE_REMOTE_RENDERING`  /  `#endif` , így könnyen azonosítható a távoli renderelési hozzáadások.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

* Fiók adatai (fiókazonosító, fiók kulcsa, fiók tartománya, előfizetés azonosítója). Ha nem rendelkezik fiókkal, [hozzon létre egy fiókot](../../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0 [(letöltés)](https://developer.microsoft.com/windows/downloads/windows-10-sdk).
* A Visual Studio 2019 [(letöltés)](https://visualstudio.microsoft.com/vs/older-downloads/)legújabb verziója.
* [Visual Studio-eszközök vegyes valósághoz](/windows/mixed-reality/install-the-tools). A következő számítási *feladatok* telepítése kötelező:
  * **Asztali fejlesztés C++ nyelven**
  * **Univerzális Windows-platform (UWP) fejlesztése**
* A Windows vegyes valóság alkalmazás-sablonok a Visual studióhoz [(letöltés)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX).

## <a name="create-a-new-holographic-app-sample"></a>Új holografikus alkalmazás mintájának létrehozása

Első lépésként létrehozunk egy törzsoldatot, amely a távoli renderelési integráció alapját képezi. Nyissa meg a Visual studiót, és válassza az "új projekt létrehozása" lehetőséget, és keressen rá a "holografikus DirectX 11 alkalmazás (univerzális Windows) (C++/WinRT)" kifejezésre.

![Új projekt létrehozása](media/new-project-wizard.png)

Adja meg a kívánt projekt nevét, válasszon egy elérési utat, és válassza a létrehozás gombot.
Az új projektben állítsa át a konfigurációt **"hibakeresés/ARM64"** értékre. Most már képesnek kell lennie a csatlakoztatott HoloLens 2 eszközre való fordítására és üzembe helyezésére. Ha a HoloLens-on futtatja, egy forgó adatkockát kell látnia az Ön előtt.

## <a name="add-remote-rendering-dependencies-through-nuget"></a>Távoli renderelési függőségek hozzáadása a NuGet-n keresztül

A távoli renderelési képességek hozzáadásának első lépése az ügyféloldali függőségek hozzáadása. A kapcsolódó függőségek NuGet-csomagként érhetők el.
A Megoldáskezelő kattintson a jobb gombbal a projektre, és válassza az **"NuGet-csomagok kezelése..."** lehetőséget a helyi menüből.

A megkérdezett párbeszédpanelen keresse meg a **"Microsoft. Azure. RemoteRendering. cpp"** nevű NuGet-csomagot:

![NuGet-csomag tallózása](media/add-nuget.png)

majd adja hozzá a projekthez a csomag kiválasztásával, majd nyomja le a "telepítés" gombot.

A NuGet csomag hozzáadja a távoli renderelési függőségeket a projekthez. Ezek konkrétan a következők:
* Hivatkozás az ügyféloldali függvénytárhoz (RemoteRenderingClient. lib).
* A. dll-függőségek beállítása.
* Állítsa be a beágyazási könyvtár helyes elérési útját.

## <a name="project-preparation"></a>Projekt előkészítése

Kis módosításokat kell végezni a meglévő projektben. Ezek a változások finomak, de a távoli renderelés nélkül nem fog működni.

### <a name="enable-multithread-protection-on-directx-device"></a>A többszálas-védelem engedélyezése a DirectX-eszközön
Az `DirectX11` eszközön engedélyezve kell lennie a többszálas-védelemnek. Ennek módosításához nyissa meg a DeviceResources. cpp fájlt a "Common" mappában, majd szúrja be a következő kódot a függvény végére `DeviceResources::CreateDeviceResources()` :

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>Hálózati képességek engedélyezése az alkalmazás jegyzékfájljában
A hálózati képességeket explicit módon engedélyezni kell az üzembe helyezett alkalmazáshoz. A konfigurálás nélkül a kapcsolati lekérdezések végül időtúllépéseket eredményeznek. Az engedélyezéshez kattintson duplán a `package.appxmanifest` megoldás Explorerben található elemre. A következő felhasználói felületen lépjen a **képességek** lapra, és válassza a következőket:
* Internet (ügyfél & kiszolgáló)
* Internet (Ügyfél)

![Hálózati képességek](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>Távoli renderelés integrálása

Most, hogy elkészült a projekt, kezdhetjük a kóddal. Az alkalmazás megfelelő belépési pontja az osztály `HolographicAppMain` (fájl HolographicAppMain. h/CPP), mert az összes szükséges hookot tartalmaz az inicializáláshoz, a inicializáláshoz és a megjelenítéshez.

### <a name="includes"></a>Beágyazások

Kezdjük a szükséges beágyazások hozzáadásával. Adja hozzá a következőt a HolographicAppMain. h fájlhoz:

```cpp
#include <AzureRemoteRendering.h>
```

... és ezek a további `include` irányelvek a HolographicAppMain. cpp fájlhoz:

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
#include <windows.perception.spatial.h>
```

A kód egyszerűsége érdekében a következő névtér-parancsikont definiáljuk a HolographicAppMain. h fájl tetején, az `include` irányelvek után:

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

Ez a parancsikon hasznos, így nem kell mindenhol kiírni a teljes névteret, de az ARR-specifikus adatstruktúrákat továbbra is felismerheti. Természetesen a `using namespace...` direktívát is használhatja.

### <a name="remote-rendering-initialization"></a>Távoli renderelés inicializálása
 
Az alkalmazás élettartama alatt meg kell tartani néhány objektumot a munkamenethez stb. Az élettartam egybeesik az alkalmazás `HolographicAppMain` objektumának élettartamával, ezért az objektumokat tagokként adja hozzá az osztályhoz `HolographicAppMain` . A következő lépés a következő osztály tagjainak hozzáadása a HolographicAppMain. h fájlban:

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::RemoteRenderingClient> m_client;  // the client instance
    RR::ApiHandle<RR::RenderingSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RenderingConnection> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Connection()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

A tényleges megvalósítás jó kiindulópont az osztály konstruktora `HolographicAppMain` . Három inicializálási típust kell elvégeznie:
1. A távoli renderelési rendszer egyszeri inicializálása
1. Ügyfél létrehozása (hitelesítés)
1. Munkamenet létrehozása

Mindezt egymás után a konstruktorban hajtjuk végre. A valós használati esetekben azonban célszerű lehet ezeket a lépéseket külön elvégezni.

Adja hozzá a következő kódot a konstruktor törzsének elejéhez a HolographicAppMain. cpp fájlban:

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        clientInit.ConnectionType = RR::ConnectionType::General;
        clientInit.GraphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.ToolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.UnitsPerMeter = 1.0f;
        clientInit.Forward = RR::Axis::NegativeZ;
        clientInit.Right = RR::Axis::X;
        clientInit.Up = RR::Axis::Y;
        if (RR::StartupRemoteRendering(clientInit) != RR::Result::Success)
        {
            // something fundamental went wrong with the initialization
            throw std::exception("Failed to start remote rendering. Invalid client init data.");
        }
    }


    // 2. Create Client
    {
        // Users need to fill out the following with their account data and model
        RR::SessionConfiguration init;
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.RemoteRenderingDomain = "westus2.mixedreality.azure.com"; // <change to the region that the rendering session should be created in>
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to the region the account was created in>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created
        m_client = RR::ApiHandle(RR::RemoteRenderingClient(init));
    }

    // 3. Open/create rendering session
    {
        auto SessionHandler = [&](RR::Status status, RR::ApiHandle<RR::CreateRenderingSessionResult> result)
        {
            if (status == RR::Status::OK)
            {
                auto ctx = result->GetContext();
                if (ctx.Result == RR::Result::Success)
                {
                    SetNewSession(result->GetSession());
                }
                else
                {
                    SetNewState(AppConnectionStatus::ConnectionFailed, ctx.ErrorMessage.c_str());
                }
            }
            else
            {
                SetNewState(AppConnectionStatus::ConnectionFailed, "failed");
            }
        };

        // If we had an old (valid) session that we can recycle, we call async function m_client->OpenRenderingSessionAsync
        if (!m_sessionOverride.empty())
        {
            m_client->OpenRenderingSessionAsync(m_sessionOverride, SessionHandler);
            SetNewState(AppConnectionStatus::CreatingSession, nullptr);
        }
        else
        {
            // create a new session
            RR::RenderingSessionCreationOptions init;
            init.MaxLeaseInMinutes = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            m_client->CreateNewRenderingSessionAsync(init, SessionHandler);
            SetNewState(AppConnectionStatus::CreatingSession, nullptr);
        }
    }

    // Rest of constructor code:
    ...
}
```

A kód meghívja a member functions `SetNewSession` -t `SetNewState` , amelyet a következő bekezdésben fog megvalósítani, valamint a többi állapotjelző gépi kóddal együtt.

Vegye figyelembe, hogy a hitelesítő adatok a mintában rögzítettek, és ki kell tölteni a helyet ([fiókazonosító, fiók kulcsa, fiók tartománya](../../../how-tos/create-an-account.md#retrieve-the-account-information)és [távoli megjelenítési tartomány](../../../reference/regions.md)).

A destruktor törzsének végén a deinicializálás szimmetrikusan és fordított sorrendben történik:

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->Disconnect();
        m_session = nullptr;
    }

    // Destroy front end:
    m_client = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>Állapot gép

A távoli renderelés során a fő függvények a munkamenetek létrehozásához és a modellek betöltéséhez aszinkron függvények. Ehhez egy egyszerű állapotú gépre van szükségünk, amely lényegében a következő állapotokon keresztül automatikusan átvált:

*Inicializálás – > munkamenet létrehozása – > munkamenet indítása – > modell betöltése (folyamatban)*

Ennek megfelelően a következő lépésként egy kis állapotú gépi kezelést adunk hozzá az osztályhoz. Deklaráljuk a saját enumerálását `AppConnectionStatus` azon különböző állapotokhoz, amelyeken az alkalmazásunk lehet. Hasonló a szolgáltatáshoz `RR::ConnectionStatus` , de a sikertelen kapcsolódás további állapottal rendelkezik.

Adja hozzá a következő tagokat és függvényeket az osztály deklarációhoz:

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
        StartingSession,
        Connecting,
        Connected,

        // error state:
        ConnectionFailed,
    };

    class HolographicAppMain
    {
        ...
        // Member functions for state transition handling
        void OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error);
        void SetNewState(AppConnectionStatus state, const char* statusMsg);
        void SetNewSession(RR::ApiHandle<RR::RenderingSession> newSession);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        std::string m_statusMsg;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_sessionStarted = false;
        RR::ApiHandle<RR::SessionPropertiesAsync> m_sessionPropertiesAsync;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;
        double m_timeAtLastRESTCall = 0;
        bool m_needsCoordinateSystemUpdate = true;
    }
```

A. cpp fájl implementáció oldalán adja hozzá a következő Function szerveiket:

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSasOptions options;
    options.ModelUri = m_modelURI.c_str();
    options.Parent = nullptr;

    // start the async model loading
    m_api->LoadModelFromSasAsync(options,
        // completed callback
        [this](RR::Status status, RR::ApiHandle<RR::LoadModelResult> result)
        {
            m_modelLoadResult = RR::StatusToResult(status);
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
        },
        // progress update callback
            [this](float progress)
        {
            // progress callback
            m_modelLoadingProgress = progress;
            m_needsStatusUpdate = true;
        });
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, const char* statusMsg)
{
    m_currentStatus = state;
    m_statusMsg = statusMsg ? statusMsg : "";

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::StartingSession: appStatus = "StartingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, m_statusMsg.c_str());
    OutputDebugStringA(buffer);
}

void HolographicAppMain::SetNewSession(RR::ApiHandle<RR::RenderingSession> newSession)
{
    SetNewState(AppConnectionStatus::StartingSession, nullptr);

    m_sessionStartingTime = m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
    m_session = newSession;
    m_api = m_session->Connection();
    m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
    m_session->ConnectionStatusChanged([this](auto status, auto error)
        {
            OnConnectionStatusChanged(status, error);
        });

};

void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    const char* asString = RR::ResultToString(error);
    m_connectionResult = error;

    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, asString);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, asString);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, asString);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = false;
        break;
    default:
        break;
    }
    
}
```

### <a name="per-frame-update"></a>/Keret frissítése

Csak egyszer kell frissítenie az ügyfelet egy szimuláció után, és végre kell hajtania néhány további állapotot. `HolographicAppMain::Update`A függvény egy jó hookot biztosít a frame-alapú frissítésekhez.

#### <a name="state-machine-update"></a>Állapot számítógépének frissítése

Le kell kérdezni a munkamenet állapotát, és meg kell tudnia, hogy átvált-e az `Ready` állapotra. Ha sikeresen csatlakozott a csatlakozáshoz, a modell betöltését véglegesen elindítjuk a használatával `StartModelLoading` .

Adja hozzá a következő kódot a törzséhez `HolographicAppMain::Update` :

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();

        if (!m_sessionStarted)
        {
            // Important: To avoid server-side throttling of the requests, we should call GetPropertiesAsync very infrequently:
            const double delayBetweenRESTCalls = 10.0;

            // query session status periodically until we reach 'session started'
            if (m_sessionPropertiesAsync == nullptr && m_timer.GetTotalSeconds() - m_timeAtLastRESTCall > delayBetweenRESTCalls)
            {
                m_timeAtLastRESTCall = m_timer.GetTotalSeconds();
                m_session->GetPropertiesAsync([this](RR::Status status, RR::ApiHandle<RR::RenderingSessionPropertiesResult> propertiesResult)
                    {
                        if (status == RR::Status::OK)
                        {
                            auto ctx = propertiesResult->GetContext();
                            if (ctx.Result == RR::Result::Success)
                            {
                                auto res = propertiesResult->GetSessionProperties();
                                switch (res.Status)
                                {
                                case RR::RenderingSessionStatus::Ready:
                                {
                                    // The following ConnectAsync is async, but we'll get notifications via OnConnectionStatusChanged
                                    m_sessionStarted = true;
                                    SetNewState(AppConnectionStatus::Connecting, nullptr);
                                    RR::RendererInitOptions init;
                                    init.IgnoreCertificateValidation = false;
                                    init.RenderMode = RR::ServiceRenderMode::Default;
                                    m_session->ConnectAsync(init, [](RR::Status, RR::ConnectionStatus) {});
                                }
                                break;
                                case RR::RenderingSessionStatus::Error:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session error");
                                    break;
                                case RR::RenderingSessionStatus::Stopped:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session stopped");
                                    break;
                                case RR::RenderingSessionStatus::Expired:
                                    SetNewState(AppConnectionStatus::ConnectionFailed, "Session expired");
                                    break;
                                }
                            }
                            else
                            {
                                SetNewState(AppConnectionStatus::ConnectionFailed, ctx.ErrorMessage.c_str());
                            }
                        }
                        else
                        {
                            SetNewState(AppConnectionStatus::ConnectionFailed, "Failed to retrieve session status");
                        }
                        m_sessionPropertiesQueryInProgress = false; // next try
                    });                }
            }
        }
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
    }

    if (m_needsCoordinateSystemUpdate && m_stationaryReferenceFrame && m_graphicsBinding)
    {
        // Set the coordinate system once. This must be called again whenever the coordinate system changes.
        winrt::com_ptr<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem> ptr{ m_stationaryReferenceFrame.CoordinateSystem().as<ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem>() };
        m_graphicsBinding->UpdateUserCoordinateSystem(ptr.get());
        m_needsCoordinateSystemUpdate = false;
    }

    // Rest of the body:
    ...
}
```

#### <a name="coordinate-system-update"></a>Koordinátarendszer frissítése

Meg kell egyeznie a renderelési szolgáltatással a használni kívánt koordináta-rendszeren. A használni kívánt koordináta-rendszer eléréséhez a `m_stationaryReferenceFrame` függvény végén létre kell hozni a következőt: `HolographicAppMain::OnHolographicDisplayIsAvailableChanged` .

Ez a koordináta-rendszer általában nem változik, ezért ez egy egyszeri inicializálás. Az alkalmazásnak újra kell hívnia, ha az alkalmazása megváltoztatja a koordináta-számítógépet.

A fenti kód a függvényen belül egyszer beállítja a koordináta-rendszerét `Update` , amint mindkettő egy hivatkozási koordináta-rendszerrel és egy csatlakoztatott munkamenettel rendelkezik.

#### <a name="camera-update"></a>Kamera frissítése

Frissíteni kell a kamera-klipek síkokat úgy, hogy a kiszolgáló kameráját szinkronban tartsa a helyi kamerával. Ezt a függvény legvégén tehetjük `Update` :

```cpp
    ...
    if (m_isConnected)
    {
        // Any near/far plane values of your choosing.
        constexpr float fNear = 0.1f;
        constexpr float fFar = 10.0f;
        for (HolographicCameraPose const& cameraPose : prediction.CameraPoses())
        {
            // Set near and far to the holographic camera as normal
            cameraPose.HolographicCamera().SetNearPlaneDistance(fNear);
            cameraPose.HolographicCamera().SetFarPlaneDistance(fFar);
        }

        // The API to inform the server always requires near < far. Depth buffer data will be converted locally to match what is set on the HolographicCamera.
        auto settings = m_api->GetCameraSettings();
        settings->SetNearAndFarPlane(std::min(fNear, fFar), std::max(fNear, fFar));
        settings->SetEnableDepth(true);
    }

    // The holographic frame will be used to get up-to-date view and projection matrices and
    // to present the swap chain.
    return holographicFrame;
}

```

### <a name="rendering"></a>Renderelés

Az utolsó teendő a távoli tartalom megjelenítésének meghívása. Ezt a hívást a renderelési folyamat megfelelő pozíciójában kell végrehajtani, miután a renderelési cél törölve lett, és beállítja a nézőpontot. Szúrja be a következő kódrészletet a `UseHolographicCameraResources` Lock on Inside függvénybe `HolographicAppMain::Render` :

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);
        
        // ...

        // Existing check to test for valid camera:
        bool cameraActive = pCameraResources->AttachViewProjectionBuffer(m_deviceResources);


        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected && cameraActive)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>Minta futtatása

A mintának olyan állapotban kell lennie, amely lefordítja és futtatja a mintát.

Ha a minta megfelelően fut, az azt jelzi, hogy közvetlenül az Ön előtt, a munkamenet-létrehozás és a modell betöltését követően pedig az aktuális Head pozícióban található motor modell jelenik meg. A munkamenet-létrehozás és a modell betöltése akár néhány percet is igénybe vehet. A jelenlegi állapot csak a Visual Studio kimeneti paneljére íródik. Ezért javasoljuk, hogy a mintát a Visual studión belül indítsa el.

> [!CAUTION]
> Az ügyfél leválasztja a kiszolgálót, ha a Tick függvény nem hívása néhány másodpercig. Így a töréspontok kiváltása nagyon egyszerűvé teheti az alkalmazás leválasztását.

A megfelelő állapot megjelenítéséhez egy szöveges panelen tekintse meg az oktatóanyag előre definiált verzióját a GitHubon.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogy milyen lépéseket kell tennie ahhoz, hogy távoli renderelést lehessen felvenni egy Stock **holografikus alkalmazás** C++/DirectX11-mintába.
Saját modell átalakításához tekintse meg a következő rövid útmutatót:

> [!div class="nextstepaction"]
> [Gyorsútmutató: Modell konvertálása a renderelés előtt](../../../quickstarts/convert-model.md)