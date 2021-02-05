---
title: A Remote Rendering beállítása a Unityben
description: Az Azure távoli renderelés inicializálása egy Unity-projektben
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 48f01058d8e879a9610e76638215214c059982fa
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594214"
---
# <a name="set-up-remote-rendering-for-unity"></a>A Remote Rendering beállítása a Unityben

Az Azure Remote rendering (ARR) egységben való engedélyezéséhez olyan dedikált módszereket biztosítunk, amelyek gondoskodnak bizonyos egységekre jellemző szempontokról.

## <a name="startup-and-shutdown"></a>Indítás és leállítás

A távoli renderelés inicializálásához használja a következőt: `RemoteManagerUnity` . Ez az osztály az általános `RenderingConnection` , de már megvalósítja az egység-specifikus adatokat. Az Unity például egy adott koordináta-rendszer használatával működik. A hívásakor `RemoteManagerUnity.Initialize` a rendszer a megfelelő konvenciót fogja beállítani. A híváshoz azt is meg kell adnia, hogy milyen Unity kamerát kell használni a távolról renderelt tartalom megjelenítéséhez.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

A távoli renderelés leállítása érdekében hívja meg a következőt: `RemoteManagerStatic.ShutdownRemoteRendering()` .

Miután `RenderingSession` létrehozta és kiválasztotta az elsődleges renderelési munkamenetet, regisztrálnia kell a következővel `RemoteManagerUnity` :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Teljes példa kód

Az alábbi kód az Azure Remote rendering Unity-ben történő inicializálásához szükséges összes lépést mutatja be:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
SessionConfiguration sessionConfig = new SessionConfiguration();
// ... fill out sessionConfig ...
RemoteRenderingClient client = new RemoteRenderingClient(sessionConfig);

// start a session
CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(new RenderingSessionCreationOptions(RenderingSessionVmSize.Standard, 0, 30));
RenderingSession session = result.Session;

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

await session.ConnectAsync(new RendererInitOptions());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Kényelmi függvények

### <a name="session-state-events"></a>Munkamenet-állapot eseményei

`RemoteManagerUnity.OnSessionUpdate` eseményeket bocsát ki a munkamenet-állapotának megváltozásakor, további részletekért tekintse meg a kód dokumentációját.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` a egy opcionális összetevő a telepítés és a munkamenet-kezelés egyszerűsítéséhez. Olyan beállításokat tartalmaz, amelyekkel automatikusan leállíthatja a munkamenetet, amikor az alkalmazás kilép, vagy a lejátszás mód ki van zárva a szerkesztőben, valamint szükség esetén automatikusan megújítja a munkamenet-bérletet. Gyorsítótárazza az adatforrásokat, például a munkamenet tulajdonságait (lásd a `LastProperties` változót), és megjeleníti az eseményeket a munkamenet-állapot változásaihoz és a munkamenet-hibákhoz.

Egyszerre csak egy példánya lehet `ARRServiceUnity` . Ez azt jelenti, hogy néhány gyakori funkció megvalósításával gyorsabban kezdheti meg a lépéseket. A nagyobb alkalmazások esetében érdemes lehet ezeket a dolgokat is előnyben részesíteni, mégis.

Példa a beállításra és a használatról: útmutató a `ARRServiceUnity` [távolról renderelt modellek megtekintéséhez](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="next-steps"></a>Következő lépések

* [A Unity Remote Rendering-csomagjának telepítése](install-remote-rendering-unity-package.md)
* [Oktatóanyag: távolról megjelenített modellek megtekintése](../../tutorials/unity/view-remote-models/view-remote-models.md)
