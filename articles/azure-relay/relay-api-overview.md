---
title: A Azure Relay API áttekintése | Microsoft Docs
description: Ez a cikk az elérhető Azure Relay API-k (.NET Standard, .NET-keretrendszer, Node.js stb.) áttekintését tartalmazza.
ms.topic: article
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: 391bd74f2bce8721b6d6359f3990af494277aa45
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312994"
---
# <a name="available-relay-apis"></a>Elérhető Relay API-k

## <a name="runtime-apis"></a>Futásidejű API-k

A következő táblázat felsorolja az összes jelenleg elérhető Relay Runtime-ügyfelet.

A [További információ](#additional-information) szakasz további információkat tartalmaz az egyes futtatókörnyezeti függvénytárak állapotáról.

| Nyelv/platform | Elérhető funkció | Ügyfélcsomag | Adattár |
| --- | --- | --- | --- |
| .NET Standard | Hibrid kapcsolatok | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET-keretrendszer | WCF-továbbító | [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/A |
| Csomópont | Hibrid kapcsolatok | [WebSockets protokoll `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[WebSockets protokoll `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP-kérelmek: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>További információ

#### <a name="net"></a>.NET

A .NET-ökoszisztéma több futtatókörnyezettel rendelkezik, ezért a továbbításhoz több .NET-kódtár is tartozik. A .NET Standard könyvtár a .NET Core vagy a .NET-keretrendszer használatával futtatható, míg a .NET-keretrendszer könyvtára csak .NET-keretrendszerbeli környezetben futtatható. További információ a .NET-Keretrendszerekről: [Framework Versions](/dotnet/articles/standard/frameworks).

A .NET-keretrendszer könyvtára csak a WCF programozási modellt támogatja, és a WCF-átvitelen alapuló, védett bináris protokollra támaszkodik `net.tcp` . Ez a protokoll és a könyvtár a meglévő alkalmazásokkal való visszamenőleges kompatibilitás érdekében tart fenn.

A .NET Standard könyvtár a HTTP-és WebSocket-alapú Hibrid kapcsolatok-továbbító nyílt protokoll-definícióján alapul. A függvénytár támogatja a WebSockets szolgáltatással való adatátvitelt, valamint egy egyszerű kérelem-válasz API-kézmozdulatot a HTTP-kérések megválaszolásához. A [webes API](https://github.com/Azure/azure-relay-dotnet) -minta bemutatja, hogyan integrálható a Hibrid kapcsolatok az ASP.net Core webszolgáltatásokhoz.

#### <a name="nodejs"></a>Node.js

A fenti táblázatban felsorolt Hibrid kapcsolatok modulok lecserélik vagy módosítják a meglévő Node.js modulokat olyan alternatív megvalósításokkal, amelyek a helyi hálózati verem helyett a Azure Relay szolgáltatást figyelik.

A `hyco-https` modul módosítja és részben felülbírálja az alapszintű Node.js modulokat `http` `https` , és egy olyan HTTPS-figyelő megvalósítását biztosítja, amely kompatibilis az ezen alapmodulokra támaszkodó számos meglévő Node.js modullal és alkalmazással.

A `hyco-ws` és a `hyco-websocket` modulok a `ws` Node.js népszerű és moduljait módosítják, és olyan `websocket` alternatív figyelő-implementációkat biztosítanak, amelyek lehetővé teszik a modulok és alkalmazások számára, hogy az hibrid kapcsolatok Relay mögött működjenek.

Ezekről a modulokról az [Azure-Relay-Node](https://github.com/Azure/azure-relay-node) GitHub-tárházban találhat további információt.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Relayről, tekintse meg a következő hivatkozásokat:
* [Mi az Azure Relay?](relay-what-is-it.md)
* [Relay – gyakori kérdések](relay-faq.yml)
