---
title: Azure Functions kötési bővítmények regisztrálása
description: Megtudhatja, hogyan regisztrálhat egy Azure Functions-kötési bővítményt a környezete alapján.
author: craigshoemaker
ms.topic: reference
ms.date: 08/16/2020
ms.author: cshoe
ms.openlocfilehash: 942ca3229808b57894598c3477e9dc97e40e8c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88689557"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions kötési bővítmények regisztrálása

A Azure Functions 2. x verziótól kezdődően a függvények futtatókörnyezete alapértelmezés szerint csak HTTP-és időzítő-eseményindítókat tartalmaz. Más [Eseményindítók és kötések](./functions-triggers-bindings.md) külön csomagokként érhetők el.

A .NET Class Library functions-alkalmazások a projektben NuGet-csomagként telepített kötéseket használnak. A bővítményi csomagok lehetővé teszik, hogy a non-.NET functions alkalmazások ugyanazt a kötést használják, anélkül, hogy a .NET-infrastruktúrával kellene foglalkoznia.

A következő táblázat azt mutatja be, hogy mikor és hogyan regisztrálja a kötéseket.

| Fejlesztési környezet |Regisztráció<br/> a functions 1. x  |Regisztráció<br/> a functions 3. x/2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatikus|Automatikus<sup>*</sup>|
|Non-.NET nyelvek|Automatikus|[Bővítmények](#extension-bundles) használata (ajánlott) vagy [explicit módon telepítse a bővítményeket](#explicitly-install-extensions)|
|C# Class Library a Visual Studióval|[NuGet-eszközök használata](#vs)|[NuGet-eszközök használata](#vs)|
|C# Class Library a Visual Studio Code használatával|N/A|[A .NET Core parancssori felülete használata](#vs-code)|

<sup>*</sup> A portál bővítmény-csomagokat használ.

## <a name="access-extensions-in-non-net-languages"></a>Hozzáférési bővítmények a non-.NET-nyelveken

A Java, a JavaScript, a PowerShell, a Python és az egyéni kezelő függvények alkalmazásai esetében ajánlott a bővítmények használata a kötések eléréséhez. Azokban az esetekben, amelyekben nem használhatók bővítmények, explicit módon telepítheti a kötési bővítményeket.

### <a name="extension-bundles"></a><a name="extension-bundles"></a>Kiterjesztési csomagok

A bővítményi csomagok lehetővé teszik a kötési bővítmények kompatibilis készletének hozzáadását a Function alkalmazáshoz. A kiterjesztési csomagokat az alkalmazás *host.js* fájljában engedélyezheti.

A bővítmények a functions futtatókörnyezet 2. x vagy újabb verzióival is használhatók.

A kiterjesztési csomagok verziószáma. Az egyes verziók a kötési bővítmények egy adott készletét tartalmazzák, amelyeket a rendszer a közös munkához ellenőriz. Válasszon egy köteg-verziót az alkalmazásban szükséges bővítmények alapján.

Ha bővítményt szeretne hozzáadni a Function alkalmazáshoz, adja hozzá a `extensionBundle` szakaszt *host.jsa* következőhöz:. Sok esetben a Visual Studio Code és a Azure Functions Core Tools automatikusan hozzáadja az Ön számára.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

A következő táblázat az alapértelmezett *Microsoft. Azure. functions. ExtensionBundle* csomag jelenleg elérhető verzióit sorolja fel, és a hozzájuk tartozó bővítményekre mutató hivatkozásokat tartalmaz.

| Csomag verziója | host.jsverziójának verziója | Mellékelt bővítmények |
| --- | --- | --- |
| 1. x | `[1.*, 2.0.0)` | Lásd a köteg létrehozásához használt [extensions.js](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) . |
| 2. x | `[2.*, 3.0.0)` | Lásd a köteg létrehozásához használt [extensions.js](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) . |

> [!NOTE]
> Ha a host.json egyéni verziót is megadhat, akkor azt javasoljuk, hogy a megfelelő verziót használja a táblázatból.

### <a name="explicitly-install-extensions"></a><a name="explicitly-install-extensions"></a>Bővítmények explicit telepítése

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>Bővítmények telepítése a NuGet .NET-nyelveken

A C# szintű függvénytár-alapú függvények projekthez közvetlenül kell telepíteni a bővítményeket. A bővítmény-csomagokat kifejezetten olyan projektekhez tervezték, amelyek nem rendelkeznek C# szintű függvénytár-alapú.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a> C \# osztályú könyvtár a Visual Studióval

A **Visual Studióban** a Package Manager konzolon telepítheti a csomagokat az [Install-Package](/nuget/tools/ps-ref-install-package) paranccsal, ahogy az alábbi példában is látható:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Az adott kötéshez használt csomag neve az adott kötéshez tartozó hivatkozási cikkben található. Példaként tekintse meg a [Service Bus kötési útmutató csomagok szakaszát](functions-bindings-service-bus.md#functions-1x).

Cserélje le a `<TARGET_VERSION>` példát a csomag egy adott verziójára, például: `3.0.0-beta5` . Az érvényes verziók a [NuGet.org](https://nuget.org)-on található egyedi csomag oldalain találhatók. Az 1. x vagy 2. x függvényeknek megfelelő főverziók a kötésre vonatkozó hivatkozási cikkben vannak megadva.

Ha `Install-Package` egy kötésre mutató hivatkozást használ, nem kell használni a [bővítmény-csomagokat](#extension-bundles). Ez a megközelítés kifejezetten a Visual Studióban létrehozott osztályok könyvtáraira vonatkozik.

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> C# szintű függvénytár a Visual Studio Code-ban

A **Visual Studio Code**-ban telepítsen egy C# Class Library-projekthez tartozó csomagokat a parancssorból a a .net Core parancssori felülete a [csomag hozzáadása](/dotnet/core/tools/dotnet-add-package) parancs használatával. Az alábbi példa bemutatja, hogyan adhat hozzá kötéseket:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

A a .NET Core parancssori felülete csak Azure Functions 2. x fejlesztéshez használható.

A helyére írja be annak a `<BINDING_TYPE_NAME>` csomagnak a nevét, amely a szükséges kötést tartalmazza. A kívánt kötési hivatkozásról szóló cikket a [támogatott kötések listájában](./functions-triggers-bindings.md#supported-bindings)találja.

Cserélje le a `<TARGET_VERSION>` példát a csomag egy adott verziójára, például: `3.0.0-beta5` . Az érvényes verziók a [NuGet.org](https://nuget.org)-on található egyedi csomag oldalain találhatók. Az 1. x vagy 2. x függvényeknek megfelelő főverziók a kötésre vonatkozó hivatkozási cikkben vannak megadva.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Az Azure Function trigger és a kötési példa](./functions-bindings-example.md)
