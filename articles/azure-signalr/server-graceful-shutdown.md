---
title: Az App Servert szabályosan állítsa le.
description: Ez a cikk a szabályosan leállítást jelző app Server-kiszolgálóról nyújt információkat.
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: d9dd7ce9cf321628598a7bb866c5d1b1a6fb0e1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98201671"
---
# <a name="server-graceful-shutdown"></a>Kiszolgáló biztonságos leállítása
A Microsoft Azure Signaler szolgáltatás két módot biztosít a kiszolgálók szabályosan leállítására. 

A funkció használatának legfőbb előnye, hogy megakadályozza, hogy az ügyfél váratlanul megtapasztalja a kapcsolódást. 

Ehelyett megvárhatja az ügyfélkapcsolatokat az üzleti logikához képest, vagy akár egy másik kiszolgálóra is áttelepítheti az adatvesztés nélkül. 

## <a name="how-it-works"></a>Működés

Általánosságban elmondható, hogy egy kecses leállítási folyamat négy szakaszból áll:

1. **A kiszolgáló offline állapotba állítása**

    Ez azt jelenti, hogy ez a kiszolgáló nem fog több ügyfélkapcsolatot irányítani.

2. **Trigger- `OnShutdown` hookok**

    A kiszolgáló tulajdonában lévő minden egyes hubhoz leállítási kampókat regisztrálhat.
    A rendszer a regisztrált megrendelés után közvetlenül az Azure Signaler szolgáltatás **FINACK** válasza után hívja meg őket, ami azt jelenti, hogy ez a kiszolgáló offline állapotban van az Azure Signaler szolgáltatásban.

    Ebben a szakaszban üzeneteket küldhet, vagy elvégezhet néhány tisztítási feladatot, ha az összes leállítási hookot végrehajtotta, a következő lépésre kerül sor.

3. **Várjon, amíg az összes ügyfél-kapcsolat befejeződött**, a választott módtól függ:

    **A WaitForClientsToClose mód beállítása**

    Az Azure Signaler szolgáltatás a meglévő ügyfeleket fogja tárolni.

    Előfordulhat, hogy úgy kell megterveznie a módot, mint a záró üzenet küldése az összes ügyfélnek, majd az ügyfelek dönthetnek arról, hogy mikor zárja be/szeretné újra a kapcsolatot.

    Olvassa el a [ChatSample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample) , amely "Exit" üzenetet küld a leállítási Hookban lévő ügyfél bezárásához.

    **A MigrateClients mód beállítása**

    Az Azure Signaler szolgáltatás megkísérli átirányítani az ügyfélkapcsolatot ezen a kiszolgálón egy másik érvényes kiszolgálóra. 
    
    Ebben a forgatókönyvben, `OnConnectedAsync` és az `OnDisconnectedAsync` új kiszolgálón és a régi kiszolgálón egy készlettel lesz aktiválva `IConnectionMigrationFeature` `HttpContext` , amely segítségével azonosíthatja, hogy az ügyfél-kapcsolódás migrálása megtörtént-e az áttelepített példányon. Ez különösen az állapot-nyilvántartó forgatókönyvek esetében hasznos lehet.

    Az ügyfél-kapcsolódást a rendszer azonnal áttelepíti az aktuális üzenet kézbesítése után, ami azt jelenti, hogy a következő üzenet át lesz irányítva az új kiszolgálóra.

4. **Kiszolgáló kapcsolatainak leállítása**

    Miután az összes ügyfélkapcsolatot lezárta/áttelepítette, vagy túllépte az időkorlátot (alapértelmezés szerint 30-as),

    A signaler Server SDK folytatja a leállítási folyamatot erre a szakaszra, és bezárja az összes kiszolgálói kapcsolatot.

    Az ügyfélkapcsolatok továbbra is el lesznek dobva, ha nem sikerült lezárni vagy áttelepíteni. Például nem fejeződött be a megfelelő célkiszolgáló vagy az aktuális ügyfél és kiszolgáló közötti üzenet.

## <a name="sample-codes"></a>Mintakód.

Adja hozzá a következő beállításokat, ha `AddAzureSignalR` :

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    option.GracefulShutdown.Mode = GracefulShutdownMode.WaitForClientsClose;
    // option.GracefulShutdown.Mode = GracefulShutdownMode.MigrateClients;
    option.GracefulShutdown.Timeout = TimeSpan.FromSeconds(30);

    option.GracefulShutdown.Add<Chat>(async (c) =>
    {
        await c.Clients.All.SendAsync("exit");
    });
});
```

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>konfigurálja `OnConnected` `OnDisconnected` a és a beállítást a kecses leállítás üzemmód beállításakor `MigrateClients` .

Bevezetünk egy "IConnectionMigrationFeature", amely jelzi, hogy a rendszer áttelepítette-e a kapcsolatokat.

```csharp
public class Chat : Hub {

    public override async Task OnConnectedAsync()
    {
        Console.WriteLine($"{Context.ConnectionId} connected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateTo}] {Context.ConnectionId} is migrated from {feature.MigrateFrom}.");
            // Your business logic.
        }

        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception e)
    {
        Console.WriteLine($"{Context.ConnectionId} disconnected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateFrom}] {Context.ConnectionId} will be migrated to {feature.MigrateTo}.");
            // Your business logic.
        }

        await base.OnDisconnectedAsync(e);
    }
}
```