---
title: Gyorsútmutató – Azure SignalR Service REST API
description: Ismerje meg, hogyan használhatja a REST API-t az Azure SignalR-szolgáltatással a mintákat követően. A REST API specifikációjának részletei.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 17371e3bd426ea81b5e7e07610aac0073ea972c9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74157679"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Rövid útmutató: Valós idejű üzenetek szétküldése konzolalkalmazásból

Az Azure SignalR szolgáltatás [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) biztosításával támogatja a kiszolgáló és ügyfél közötti kommunikációs forgatókönyveket, például a szétküldést. Bármelyik programozási nyelvet választhatja, amely képes REST API-t hívni. Küldhet üzenetet minden csatlakoztatott ügyfélnek, név szerint egy adott ügyfélnek, vagy ügyfelek egy csoportjának.

Ebből a rövid útmutatóból megtudhatja, hogyan küldhet üzeneteket parancssori alkalmazásból a csatlakoztatott ügyfélalkalmazásoknak C#-ban.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató macOS, Windows vagy Linux rendszeren is futtatható.

* [.NET Core SDK](https://www.microsoft.com/net/download/core)
* Az Ön által választott szöveg- vagy kódszerkesztő.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com/> webhelyen az Azure-fiókjával.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Amíg a szolgáltatás üzembe helyezése folyamatban van, térjünk át a kód előkészítésére. Klónozza a [mintaalkalmazást a GitHubról](https://github.com/aspnet/AzureSignalR-samples.git), állítsa be a SignalR Service kapcsolati sztringjét, és futtassa helyileg az alkalmazást.

1. Nyisson meg egy git terminálablakot. Váltson át arra a mappára, ahová a mintaprojektet klónozni szeretné.

1. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

Ez a minta egy konzolalkalmazás, amely az Azure SignalR szolgáltatás használatát mutatja be. Kétféle üzemmódot biztosít:

- Kiszolgáló üzemmód: egyszerű parancsok használatával hívja meg az Azure SignalR szolgáltatás REST API-ját.
- Ügyfél üzemmód: csatlakozik az Azure SignalR szolgáltatáshoz, és üzeneteket fogad a kiszolgálótól.

Azt is megtalálhatja, hogyan hozhat létre hozzáférési jogkivonatot az Azure SignalR szolgáltatással való hitelesítéshez.

### <a name="build-the-executable-file"></a>A végrehajtható fájl létrehozása

A példához a MacOS osx.10.13-x64 rendszert használjuk. A más platformokon való létrehozáshoz tekintse meg a [referenciákat](https://docs.microsoft.com/dotnet/core/rid-catalog).

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Indítson el egy ügyfelet

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Indítson el egy kiszolgálót

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>Futtassa a mintát közzététel nélkül

A következő parancsot is futtathatja kiszolgálók vagy ügyfelek elindításához

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Használjon titkos felhasználói titkos kulcsokat a Kapcsolati sztring megadásához

A `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` futtatását a minta gyökérkönyvtárában végezheti. Ezután már nem lesz szüksége a `-c "<ConnectionString>"` lehetőségre.

## <a name="usage"></a>Használat

A kiszolgáló indítása után a paranccsal küldjön üzenetet:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Több ügyfelet is indíthat különböző ügyfélnevekkel.

## <a name="integration-with-third-party-services"></a><a name="usage"> </a> Integráció külső szolgáltatásokkal

Az Azure SignalR szolgáltatás lehetővé teszi, hogy külső szolgáltatásokat lehessen a rendszerbe integrálni.

### <a name="definition-of-technical-specifications"></a> A műszaki specifikációk meghatározása

Az alábbi táblázat ismerteti a jelenleg támogatott REST API-k összes verzióját. Az egyes verziók definíciófájlját is megtalálja

Verzió | API-állapot | Ajtó | Specifikusság
--- | --- | --- | ---
`1.0-preview` | Elérhető | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Elérhető | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

Az egyes verziókhoz elérhető API-k listáját az alábbi lista tartalmazza.

API | `1.0-preview` | `1.0`
--- | --- | ---
[Szétküldés mindenkinek](#broadcast) | **&#x2713;** | **&#x2713;**
[Szétküldés egy csoportnak](#broadcast-group) | **&#x2713;** | **&#x2713;**
Szétküldés néhány csoportnak | **&#x2713;** (elavult) | `N / A`
[Küldés adott felhasználóknak](#send-user) | **&#x2713;** | **&#x2713;**
Küldés néhány felhasználónak | **&#x2713;** (elavult) | `N / A`
[Felhasználó hozzáadása egy csoporthoz](#add-user-to-group) | `N / A` | **&#x2713;**
[Felhasználó eltávolítása egy csoportból](#remove-user-from-group) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Szétküldés mindenkinek

Verzió | API HTTP-metódus | Kérés URL-címe | A kérés törzse
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | Lásd fent

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Szétküldés egy csoportnak

Verzió | API HTTP-metódus | Kérés URL-címe | A kérés törzse
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | Lásd fent

<a name="send-user"> </a>
### <a name="sending-to-specific-users"></a>Küldés adott felhasználóknak

Verzió | API HTTP-metódus | Kérés URL-címe | A kérés törzse
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Lásd fent

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Felhasználó hozzáadása egy csoporthoz

Verzió | API HTTP-metódus | Kérés URL-címe
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Felhasználó eltávolítása egy csoportból

Verzió | API HTTP-metódus | Kérés URL-címe
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a REST API-t a SignalR szolgáltatás valós idejű üzeneteinek az ügyfeleknek történő közvetítésére. Ezután további információ arról, hogyan fejlesztheti és telepítheti az Azure Functionst a REST API-ra épülő SignalR-szolgáltatáskötéssel.

> [!div class="nextstepaction"]
> [Azure-függvények fejlesztése az Azure SignalR-szolgáltatás kötései vel](signalr-quickstart-azure-functions-csharp.md)
