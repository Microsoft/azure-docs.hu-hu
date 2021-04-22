---
title: Rövid útmutató – Azure SignalR Service REST API
description: Ismerje meg, hogyan használhatja REST API a Azure SignalR Service alábbi mintákkal. Az adatspecifikáció REST API le.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: dfcbb00ec20797248f41cc1676809f3198d51527
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866160"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Rövid útmutató: Valós idejű üzenetek szétküldése konzolalkalmazásból

Az Azure SignalR szolgáltatás [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) biztosításával támogatja a kiszolgáló és ügyfél közötti kommunikációs forgatókönyveket, például a szétküldést. Bármelyik programozási nyelvet választhatja, amely képes REST API-t hívni. Küldhet üzenetet minden csatlakoztatott ügyfélnek, név szerint egy adott ügyfélnek, vagy ügyfelek egy csoportjának.

Ebből a rövid útmutatóból megtudhatja, hogyan küldhet üzeneteket parancssori alkalmazásból a csatlakoztatott ügyfélalkalmazásoknak C#-ban.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató macOS, Windows vagy Linux rendszeren is futtatható.

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* Az Ön által választott szöveg- vagy kódszerkesztő.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsapi).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com/> webhelyen az Azure-fiókjával.

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsapi).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsapi).

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Amíg a szolgáltatás üzembe helyezése folyamatban van, térjünk át a kód előkészítésére. Klónozza a [mintaalkalmazást a GitHubról](https://github.com/aspnet/AzureSignalR-samples.git), állítsa be a SignalR Service kapcsolati sztringjét, és futtassa helyileg az alkalmazást.

1. Nyisson meg egy git terminálablakot. Váltson át arra a mappára, ahová a mintaprojektet klónozni szeretné.

1. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```
Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsapi).

## <a name="build-and-run-the-sample"></a>A minta létrehozása és futtatása

Ez a minta egy konzolalkalmazás, amely az Azure SignalR szolgáltatás használatát mutatja be. Kétféle üzemmódot biztosít:

- Kiszolgáló üzemmód: egyszerű parancsok használatával hívja meg az Azure SignalR szolgáltatás REST API-ját.
- Ügyfél üzemmód: csatlakozik az Azure SignalR szolgáltatáshoz, és üzeneteket fogad a kiszolgálótól.

Azt is megtalálhatja, hogyan hozhat létre hozzáférési jogkivonatot az Azure SignalR szolgáltatással való hitelesítéshez.

### <a name="build-the-executable-file"></a>A végrehajtható fájl létrehozása

A példához a MacOS osx.10.13-x64 rendszert használjuk. A más platformokon való létrehozáshoz tekintse meg a [referenciákat](/dotnet/core/rid-catalog).

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

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsapi).

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

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsapi).

## <a name="usage"></a>Használat

A kiszolgáló elindulás után az paranccsal küldjön üzenetet:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Több ügyfelet is indíthat különböző ügyfélnevekkel.

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsapi).

## <a name="integration-with-third-party-services"></a><a name="usage"> </a> Integráció külső szolgáltatásokkal

Az Azure SignalR szolgáltatás lehetővé teszi, hogy külső szolgáltatásokat lehessen a rendszerbe integrálni.

### <a name="definition-of-technical-specifications"></a> A műszaki specifikációk meghatározása

Az alábbi táblázat ismerteti a jelenleg támogatott REST API-k összes verzióját. Az egyes verziók definíciófájlját is megtalálja

Verzió | API-állapot | Ajtó | Specifikusság
--- | --- | --- | ---
`1.0-preview` | Elérhető | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Elérhető | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

Az egyes verziókhoz elérhető API-k listáját az alábbi lista tartalmazza.

API | 1.0-s előzetes verzió | 1.0
--- | --- | ---
[Szétküldés mindenkinek](#broadcast) | **&#x2713;** | **&#x2713;**
[Szétküldés egy csoportnak](#broadcast-group) | **&#x2713;** | **&#x2713;**
Szétküldés néhány csoportnak | **&#x2713;** (elavult) | `N / A`
[Küldés egy felhasználónak](#send-user) | **&#x2713;** | **&#x2713;**
Küldés néhány felhasználónak | **&#x2713;** (elavult) | `N / A`
[Felhasználó hozzáadása egy csoporthoz](#add-user-to-group) | `N / A` | **&#x2713;**
[Felhasználó eltávolítása egy csoportból](#remove-user-from-group) | `N / A` | **&#x2713;**
[Felhasználó meglétének ellenőrzése](#check-user-existence) | `N / A` | **&#x2713;**
[Felhasználó eltávolítása az összes csoportból](#remove-user-from-all-groups) | `N / A` | **&#x2713;**
[Küldés kapcsolatra](#send-connection) | `N / A` | **&#x2713;**
[Kapcsolat hozzáadása csoporthoz](#add-connection-to-group) | `N / A` | **&#x2713;**
[Kapcsolat eltávolítása egy csoportból](#remove-connection-from-group) | `N / A` | **&#x2713;**
[Ügyfélkapcsolat bezárása](#close-connection) | `N / A` | **&#x2713;**
[Service Health](#service-health) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Szétküldés mindenkinek

Verzió | API HTTP-metódus | URL-cím kérése | A kérés törzse
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | Lásd fent

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Szétküldés egy csoportnak

Verzió | API HTTP-metódus | URL-cím kérése | A kérés törzse
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | Lásd fent

<a name="send-user"> </a>
### <a name="sending-to-a-user"></a>Küldés egy felhasználónak

Verzió | API HTTP-metódus | URL-cím kérése | A kérés törzse
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Lásd fent

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Felhasználó hozzáadása egy csoporthoz

Verzió | API HTTP-metódus | URL-cím kérése
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Felhasználó eltávolítása egy csoportból

Verzió | API HTTP-metódus | URL-cím kérése
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="check-user-existence"> </a>
### <a name="check-user-existence-in-a-group"></a>Felhasználó meglétének ellenőrzése egy csoportban

API-verzió | API HTTP-metódus | URL-cím kérése
---|---|---
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups/<group-name>`
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>` 

Válasz állapotkódja | Description
---|---
`200` | A felhasználó létezik
`404` | A felhasználó nem létezik

<a name="remove-user-from-all-groups"> </a>
### <a name="remove-a-user-from-all-groups"></a>Felhasználó eltávolítása az összes csoportból

API-verzió | API HTTP-metódus | URL-cím kérése
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups`

<a name="send-connection"> </a>
### <a name="send-message-to-a-connection"></a>Üzenet küldése egy kapcsolatnak

API-verzió | API HTTP-metódus | URL-cím kérése | Kérelem törzse
---|---|---|---
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>` | `{ "target":"<method-name>", "arguments":[ ... ] }`

<a name="add-connection-to-group"> </a>
### <a name="add-a-connection-to-a-group"></a>Kapcsolat hozzáadása csoporthoz

API-verzió | API HTTP-metódus | URL-cím kérése
---|---|---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="remove-connection-from-group"> </a>
### <a name="remove-a-connection-from-a-group"></a>Kapcsolat eltávolítása egy csoportból

API-verzió | API HTTP-metódus | URL-cím kérése
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="close-connection"> </a>
### <a name="close-a-client-connection"></a>Ügyfélkapcsolat bezárása

API-verzió | API HTTP-metódus | URL-cím kérése
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>?reason=<close-reason>`

<a name="service-health"> </a>
### <a name="service-health"></a>Service Health

API-verzió | API HTTP-metódus | URL-cím kérése
---|---|---                             
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/health`

Válasz állapotkódja | Description
---|---
`200` | Szolgáltatás jó
`5xx` | Szolgáltatáshiba

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsapi).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsapi).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használható a REST API valós idejű üzenetek SignalR Service ügyfelekre. Ezután többet is megtudhat arról, hogyan fejleszthet és helyezhet Azure Functions üzembe SignalR Service kötéssel, amely a REST API.

> [!div class="nextstepaction"]
> [Fejlesztés Azure Functions kötések Azure SignalR Service használatával](signalr-quickstart-azure-functions-csharp.md)
