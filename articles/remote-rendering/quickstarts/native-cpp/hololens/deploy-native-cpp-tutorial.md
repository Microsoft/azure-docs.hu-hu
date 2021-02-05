---
title: Natív C++ oktatóanyag üzembe helyezése a HoloLens
description: Gyors útmutató, amely bemutatja, hogyan futtatható a natív C++ oktatóanyag a HoloLens-on
author: florianborn71
ms.author: flborn
ms.date: 06/08/2020
ms.topic: quickstart
ms.openlocfilehash: b469f0cae1e356c47bfe60af99c4fa2e73eab78d
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594027"
---
# <a name="quickstart-deploy-native-c-sample-to-hololens"></a>Gyors útmutató: natív C++ minta üzembe helyezése a HoloLens-ben

Ez a rövid útmutató ismerteti, hogyan telepítheti és futtathatja a natív C++ oktatóanyag-alkalmazást egy HoloLens 2 rendszeren.

E gyorsútmutató segítségével megtanulhatja a következőket:

> [!div class="checklist"]
>
>* Készítse elő az oktatóanyag-alkalmazást a HoloLens.
>* Módosítsa az ARR hitelesítő adatokat a forráskódban.
>* A minta üzembe helyezése és futtatása az eszközön.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy hozzáférjen az Azure távoli renderelési szolgáltatáshoz, először [létre kell hoznia egy fiókot](../../../how-tos/create-an-account.md).

A következő szoftvereket kell telepíteni:

* Windows SDK 10.0.18362.0 [(letöltés)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* A Visual Studio 2019 legújabb verziója [(letöltés)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio-eszközök vegyes valósághoz](/windows/mixed-reality/install-the-tools). A következő számítási *feladatok* telepítése kötelező:
  * **Asztali fejlesztés C++ nyelven**
  * **Univerzális Windows-platform (UWP) fejlesztése**
* GIT [(letöltés)](https://git-scm.com/downloads)

## <a name="clone-the-arr-samples-repository"></a>Az ARR-minták tárházának klónozása

Első lépésként a git-tárházat klónozottuk, amely a globális Azure-alapú távoli renderelési mintákat képezi. Nyisson meg egy parancssort (írja be `cmd` a Windows Start menüjét), és váltson arra a könyvtárba, ahol az ARR-minta projektet tárolni szeretné.

Futtassa az alábbi parancsot:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Az utolsó parancs létrehoz egy alkönyvtárat az ARR könyvtárban, amely az Azure távoli renderelés különböző mintáit tartalmazza.

A C++ HoloLens oktatóanyag a *NativeCpp/HoloLens* alkönyvtárban található.

## <a name="build-the-project"></a>A projekt felépítése

Nyissa meg a *NativeCpp/HoloLens* alkönyvtárában található *HolographicApp. SLN* fájlt a Visual Studio 2019-ben.

Váltson a Build konfigurációra a *hibakereséshez* (vagy *kiadáshoz*) és a *ARM64*. Győződjön meg arról is, hogy a hibakereső üzemmód az *eszközre* van beállítva, a *távoli gép* helyett:

![Visual Studio-konfiguráció](media/vs-config-native-cpp-tutorial.png)

Mivel a fiók hitelesítő adatai az oktatóanyag hardcoded találhatók, módosítsa azokat érvényes hitelesítő adatokra. Ehhez nyissa meg a fájlt a `HolographicAppMain.cpp` Visual Studióban, és módosítsa azt a részt, ahol az ügyfél a következő osztály konstruktorán belül jön létre `HolographicAppMain` :

```cpp
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
```

Pontosabban módosítsa a következő értékeket:
* `init.AccountId`, `init.AccountKey` és `init.AccountDomain` a fiókja adatait is használhatja. Lásd: a [fiókadatok beolvasása](../../../how-tos/create-an-account.md#retrieve-the-account-information).
* Itt adhatja meg, hogy hol szeretné létrehozni a távoli renderelési munkamenetet a karakterlánc régió részének más régiókban való módosításával, például: `init.RemoteRenderingDomain` `westus2` `"westeurope.mixedreality.azure.com"` .
* Emellett a `m_sessionOverride` meglévő munkamenet-azonosítóra is módosítható. A munkamenetek ezen a mintán kívül is létrehozhatók, például [a PowerShell-parancsfájl](../../../samples/powershell-example-scripts.md#script-renderingsessionps1) használatával vagy a [munkamenet-REST API](../../../how-tos/session-rest-api.md#create-a-session) közvetlen használatával.
Ha a mintát többször kell futtatni, akkor a mintán kívüli munkamenet létrehozása ajánlott. Ha a rendszer nem ad át munkamenetet, a minta minden indításkor létrehoz egy új munkamenetet, amely több percet is igénybe vehet.

Az alkalmazás most már lefordítható.

## <a name="launch-the-application"></a>Az alkalmazás indítása

1. Csatlakoztassa a HoloLenst a számítógépéhez egy USB-kábellel.
1. Kapcsolja be a HoloLens, és várjon, amíg megjelenik a Start menü.
1. Indítsa el a hibakeresőt a Visual Studióban (F5). Az alkalmazás automatikusan üzembe lesz helyezve az eszközön.

A minta alkalmazásnak el kell indulnia, és egy szöveges panelnek kell megjelennie, amely tájékoztatja Önt az aktuális alkalmazás állapotáról. Az állapot indítási időpontban egy új munkamenet indítása vagy egy meglévő munkamenethez való csatlakozás. A modell betöltésének befejezése után a beépített motor modell közvetlenül a feje pozíciójában jelenik meg. A motor modellje a megfelelő működést is lehetővé teszi a helyileg megjelenített forgó adatkockával.

 Ha később el szeretné indítani a mintát, azt a HoloLens Start menüjéből is megtalálhatja, de előfordulhat, hogy egy lejárt munkamenet-AZONOSÍTÓval van lefordítva.

## <a name="next-steps"></a>Következő lépések

Ez a rövid útmutató egy olyan oktatóanyag eredményein alapul, amely elmagyarázza, hogyan integrálható a távoli rendereléssel kapcsolatos összes darab egy készlet *holografikus alkalmazásba*. A szükséges lépések elsajátításához kövesse az alábbi oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: távoli renderelés integrálása HoloLens holografikus alkalmazásba](../../../tutorials/native-cpp/hololens/integrate-remote-rendering-into-holographic-app.md)