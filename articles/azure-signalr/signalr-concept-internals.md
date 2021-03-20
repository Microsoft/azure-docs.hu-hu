---
title: Az Azure SignalR szolgáltatás belső elemei
description: Ismerje meg az Azure Signaler szolgáltatás belső adatait, az architektúrát, a kapcsolatokat és az adatok továbbításának módját.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: afb63b76666f47217f9c19376d81aa4ed73991bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98572561"
---
# <a name="azure-signalr-service-internals"></a>Az Azure SignalR szolgáltatás belső elemei

Az Azure Signaler szolgáltatás a ASP.NET Core-Signal-keretrendszerre épül. A ASP.NET-jelzőt is támogatja a ASP.NET-jelző adatprotokolljának a ASP.NET Core keretrendszerre való újraimplementálása révén.

Könnyedén áttelepítheti a helyi ASP.NET Core jelző alkalmazást vagy a ASP.NET-jelző alkalmazást, hogy működjön a Signaler szolgáltatással, néhány sornyi kóddal.

Az alábbi ábra a jellemző architektúrát ismerteti, amikor a Signaler szolgáltatást az alkalmazáskiszolgáló használatával használja.

A rendszer a saját üzemeltetésű ASP.NET Core Signaler-alkalmazással kapcsolatban is tárgyalja a különbségeket.

![Architektúra](./media/signalr-concept-internals/arch.png)

## <a name="server-connections"></a>Kiszolgáló kapcsolatai

A saját üzemeltetésű ASP.NET Core jelző alkalmazás-kiszolgáló közvetlenül figyeli és csatlakoztatja az ügyfeleket.

A Signaler szolgáltatással az alkalmazáskiszolgáló már nem fogad állandó ügyfélkapcsolatokat, hanem a következőket:

1. A `negotiate` végpontokat az Azure signaler Service SDK teszi elérhetővé minden egyes hubhoz.
1. Ez a végpont válaszol az ügyfél egyeztetési kéréseire, és átirányítja az ügyfeleket a jelző szolgáltatásba.
1. Végül az ügyfelek csatlakozni fognak a Signaler szolgáltatáshoz.

További információ: [ügyfélkapcsolatok](#client-connections).

Az alkalmazáskiszolgáló elindítása után 
- ASP.NET Core-jelző esetében az Azure Signaler Service SDK 5 WebSocket-kapcsolatot nyit meg a Signaler szolgáltatáshoz. 
- A ASP.NET-jelző esetében az Azure Signaler Service SDK 5 WebSocket-kapcsolatot nyit meg a Signaler szolgáltatáshoz, és egy alkalmazás-WebSocket-kapcsolaton keresztül.

5 a WebSocket-kapcsolatok az alapértelmezett érték, amely a [konfigurációban](https://github.com/Azure/azure-signalr/blob/dev/docs/run-asp-net-core.md#connectioncount)módosítható.

Az ügyfelek felé irányuló és onnan érkező üzenetek a következő kapcsolatokba lesznek felépítve.

Ezek a kapcsolatok továbbra is csatlakoztatva lesznek a Signaler szolgáltatáshoz. Ha a kiszolgáló kapcsolata le van választva hálózati hiba esetén,
- a kiszolgálói kapcsolat által kiszolgált összes ügyfél leválasztása (További információ: az [ügyfél és a kiszolgáló közötti adatátvitelek](#data-transmit-between-client-and-server));
- a kiszolgáló kapcsolata automatikusan újracsatlakozik.

## <a name="client-connections"></a>Ügyfélkapcsolatok

A Signaler szolgáltatás használatakor az ügyfelek az alkalmazáskiszolgáló helyett a Signal szolgáltatáshoz csatlakoznak.
Az ügyfél és a jelző szolgáltatás közötti állandó kapcsolatok létrehozásához két lépés szükséges.

1. Az ügyfél egyeztető kérelmet küld az alkalmazáskiszolgáló számára. Az Azure Signaler Service SDK-val az alkalmazáskiszolgáló átirányítási választ ad vissza a Signaler szolgáltatás URL-címével és hozzáférési jogkivonatával.

- A ASP.NET Core jelző esetében egy tipikus átirányítási válasz a következőképpen néz ki:
    ```
    {
        "url":"https://test.service.signalr.net/client/?hub=chat&...",
        "accessToken":"<a typical JWT token>"
    }
    ```
- A ASP.NET-jelző esetében egy tipikus átirányítási válasz a következőképpen néz ki:
    ```
    {
        "ProtocolVersion":"2.0",
        "RedirectUrl":"https://test.service.signalr.net/aspnetclient",
        "AccessToken":"<a typical JWT token>"
    }
    ```

1. Az átirányítási válasz kézhezvétele után az ügyfél az új URL-cím és a hozzáférési jogkivonat használatával elindítja a normál folyamatot a Signal Service-hez való kapcsolódáshoz.

További információ a ASP.NET Core jelző [átviteli protokolljairól](https://github.com/aspnet/SignalR/blob/release/2.2/specs/TransportProtocols.md).

## <a name="data-transmit-between-client-and-server"></a>Az ügyfél és a kiszolgáló közötti adatküldés

Amikor egy ügyfél csatlakozik a Signaler szolgáltatáshoz, a szolgáltatási futtatókörnyezet az ügyfél kiszolgálására szolgáló kiszolgálói kapcsolatot fog találni
- Ez a lépés csak egyszer fordul elő, és az ügyfél és a kiszolgáló kapcsolatai között egy az egyhez típusú hozzárendelés.
- A leképezés a Signaler szolgáltatásban marad, amíg az ügyfél vagy a kiszolgáló nem bontja a kapcsolatot.

Ezen a ponton az alkalmazáskiszolgáló egy eseményt kap az új ügyféltől származó információkkal. A rendszer létrehoz egy logikai kapcsolódást az ügyfélhez az alkalmazáskiszolgáló használatával. Az adatcsatorna az ügyfél és az alkalmazáskiszolgáló között, a Signaler szolgáltatáson keresztül van létrehozva.

A signaler szolgáltatás adatokat küld az ügyféltől a párosítási alkalmazáskiszolgáló felé. Az alkalmazás-kiszolgálóról származó adatok a leképezett ügyfelek számára lesznek elküldve.

A signaler szolgáltatás nem menti vagy tárolja az ügyféladatokat, a rendszer a kapott összes vásárlói adatokat valós időben továbbítja a célkiszolgálóra vagy az ügyfeleknek.

Amint láthatja, az Azure Signaler szolgáltatás lényegében logikai szállítási réteg az alkalmazáskiszolgáló és az ügyfelek között. Az összes állandó kapcsolat kiszervezése a Signaler szolgáltatásba történik.
Az alkalmazáskiszolgáló csak az üzleti logikát kell kezelnie a hub osztályban, anélkül, hogy az ügyfélkapcsolatokkal kellene foglalkoznia.
