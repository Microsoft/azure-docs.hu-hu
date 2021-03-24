---
title: 'Gyors útmutató: Azure Management ügyféloldali kódtár a .NET-hez'
description: Ebben a rövid útmutatóban megismerheti a .NET-hez készült Azure Management ügyféloldali kódtárat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 3/22/2021
ms.author: pafarley
ms.openlocfilehash: 1ecfd58e961e70182bfc2e0cc8eaf77f5053eb77
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879578"
---
[Dokumentáció](/dotnet/api/overview/azure/cognitiveservices/management)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/)  |  [Példák](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="c-prerequisites"></a>C# előfeltételek

* Érvényes Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást. A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `azure-management-quickstart` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájl: *program. cs*. 

```console
dotnet new console -n azure-management-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```console
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az alkalmazás könyvtárában telepítse a .NET-hez készült Azure Management ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.

### <a name="import-libraries"></a>Kódtárak importálása

Nyissa meg a *program. cs* fájlt, és adja hozzá a következő `using` utasításokat a fájl elejéhez:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Adja hozzá a következő mezőket a program gyökeréhez *. cs* , és töltse fel az értékeit a létrehozott egyszerű szolgáltatásnév és az Azure-fiók adatai alapján.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

Ezután a **Main** metódusban használja ezeket az értékeket egy **CognitiveServicesManagementClient** objektum létrehozásához. Ez az objektum szükséges az összes Azure-felügyeleti művelethez.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>Hívás-felügyeleti módszerek

Adja hozzá a következő kódot a **Main** metódushoz a rendelkezésre álló erőforrások listázásához, egy minta erőforrás létrehozásához, a saját tulajdonú erőforrások listázásához, majd a minta erőforrás törléséhez. Ezeket a metódusokat a következő lépésekben fogja meghatározni.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-c"></a>Cognitive Services-erőforrás létrehozása (C#)

Új Cognitive Services erőforrás létrehozásához és előfizetéséhez használja a **create** metódust. Ez a metódus új számlázható erőforrást hoz létre az Ön által beadott erőforráscsoporthoz. Az új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás típusát, valamint az árképzési szintet (vagy SKU-t) és egy Azure-helyet. A következő metódus ezeket argumentumként fogadja el, és létrehoz egy erőforrást.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Válasszon ki egy szolgáltatást és egy díjszabási szintet

Új erőforrás létrehozásakor ismernie kell a használni kívánt szolgáltatás típusát, valamint a kívánt [árképzési szintet](https://azure.microsoft.com/pricing/details/cognitive-services/) (vagy SKU-t). Ezt és egyéb információkat paraméterekként fogja használni az erőforrás létrehozásakor. A következő módszer meghívásával megkeresheti a rendelkezésre álló kognitív szolgáltatások listáját a parancsfájlban:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Erőforrások megtekintése

Ha az Azure-fiók összes erőforrását (az összes erőforráscsoportot) szeretné megtekinteni, használja a következő metódust:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>Erőforrás törlése

A következő metódus törli a megadott erőforrást az adott erőforráscsoporthoz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```dotnet
dotnet run
```

## <a name="see-also"></a>Lásd még

* Lásd: **[kérelmek hitelesítése az Azure-Cognitive Services](../../authentication.md)** a Cognitive Services biztonságos kezeléséhez.
* Ismerje meg, **[Mi az Azure Cognitive Services?](../../what-are-cognitive-services.md)** a különböző kategóriák listájának lekéréséhez Cognitive Serviceson belül.
* A Cognitive Services által támogatott természetes nyelvek listájának megjelenítéséhez tekintse meg a **[természetes nyelvi támogatás](../../language-support.md)** című témakört.
* Lásd: a **[Cognitive Services használata tárolóként](../../cognitive-services-container-support.md)** a Cognitive Services helyszíni használatának megismeréséhez.
* A Cognitive Services használatának költségeinek **[kiszámításához tekintse meg a Cognitive Services költségeinek tervezése és kezelése](../../plan-manage-costs.md)** című témakört.
* A Management SDK-val kapcsolatos további részletekért tekintse meg **[Cognitive Services Management SDK dokumentációját](/dotnet/api/overview/azure/cognitiveservices/management)** .
