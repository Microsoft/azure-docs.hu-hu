---
title: 'Oktatóanyag: az Azure Queue Storage Queues használata a .NET-ben'
description: Útmutató az Azure Queue Storage a várólisták létrehozásához, valamint az üzenetek beszúrásához, lekéréséhez és törléséhez .NET-kóddal.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 06/09/2020
ms.topic: tutorial
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: dda6fb816fcba4ab9350edd956d895a3b9b204d4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276127"
---
# <a name="tutorial-work-with-azure-queue-storage-queues-in-net"></a>Oktatóanyag: az Azure Queue Storage Queues használata a .NET-ben

Az Azure Queue Storage felhőalapú várólistákat valósít meg az elosztott alkalmazások összetevői közötti kommunikáció engedélyezéséhez. Minden üzenetsor fenntart egy listát azokról az üzenetekről, amelyeket a küldő összetevő hozzáadhat, és amelyet egy fogadó összetevő dolgoz fel. A várólista használatával az alkalmazás azonnal méretezhető az igények kielégítése érdekében. Ez a cikk az Azure Queue Storage üzenetsor használatának alapvető lépéseit ismerteti.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> - Azure Storage-fiók létrehozása
> - Az alkalmazás létrehozása
> - Az Azure-ügyfél kódtárainak hozzáadása
> - Aszinkron kód támogatásának hozzáadása
> - Üzenetsor létrehozása
> - Üzenetek beszúrása egy várólistába
> - Üzenetek várólistára helyezése
> - Üres üzenetsor törlése
> - Parancssori argumentumok keresése
> - Készítsen buildet és futtassa az alkalmazást

## <a name="prerequisites"></a>Előfeltételek

- Szerezze be a platformfüggetlen [Visual Studio Code](https://code.visualstudio.com/download) Editor ingyenes példányát.
- Töltse le és telepítse a [.NET Core SDK](https://dotnet.microsoft.com/download) 3,1-es vagy újabb verzióját.
- Ha még nincs Azure-előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) a Kezdés előtt.

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Először hozzon létre egy Azure Storage-fiókot. A Storage-fiók létrehozásával kapcsolatos lépésenkénti útmutató: [Storage-fiók létrehozása](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json). Ez egy külön lépés, amelyet a rendszer az előfeltételekhez tartozó ingyenes Azure-fiók létrehozása után hajt végre.

## <a name="create-the-app"></a>Az alkalmazás létrehozása

Hozzon létre egy nevű .NET Core-alkalmazást `QueueApp` . Az egyszerűség kedvéért ez az alkalmazás az üzenetsor használatával küldi el és fogadja az üzeneteket.

1. A konzol ablakban (például a cmd, a PowerShell vagy az Azure CLI) a `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `QueueApp` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre, amely egyetlen nevű forrásfájl `Program.cs` .

   ```console
   dotnet new console -n QueueApp
   ```

2. Váltson az újonnan létrehozott `QueueApp` mappára, és fordítsa le az alkalmazást, hogy ellenőrizhesse, minden rendben van-e.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   A következő kimenethez hasonló eredményeknek kell megjelennie:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.63 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp3.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

<!-- markdownlint-disable MD023 -->

## <a name="add-the-azure-client-libraries"></a>Az Azure-ügyfél kódtárainak hozzáadása

1. Az parancs használatával adja hozzá az Azure Storage ügyféloldali kódtárait a projekthez `dotnet add package` .

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   Futtassa a következő parancsot a Project mappából a konzolablak ablakban.

   ```console
   dotnet add package Azure.Storage.Queues
   ```

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   Futtassa a következő parancsokat a Project mappából a konzol ablakában.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   ```

   ```console
   dotnet add package Microsoft.Azure.Storage.Queue
   ```
   ---

### <a name="add-using-statements"></a>Hozzáadás a using utasításokkal

1. A projekt könyvtárának parancssorában írja be a `code .` Visual Studio Code megnyitása az aktuális könyvtárban. Tartsa megnyitva a parancssori ablakot. A későbbiekben több parancs is futtatható. Ha a rendszer arra kéri, hogy adjon hozzá C#-eszközöket a létrehozáshoz és a hibakereséshez, kattintson az **Igen** gombra.

1. Nyissa meg a `Program.cs` forrásfájlt, és adja hozzá a következő névtereket közvetlenül az `using System;` utasítás után. Az alkalmazás ezekből a névterekről származó típusokat használ az Azure Storage-hoz való kapcsolódáshoz és a várólistákkal való munkavégzéshez.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_UsingStatements":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_UsingStatements":::

1. Mentse a `Program.cs` fájlt.

## <a name="add-support-for-asynchronous-code"></a>Aszinkron kód támogatásának hozzáadása

Mivel az alkalmazás Felhőbeli erőforrásokat használ, a kód aszinkron módon fut.

1. Frissítse a `Main` metódust aszinkron futtatásra. Cserélje le `void` a `async Task` értéket visszatérési értékre.

   ```csharp
   static async Task Main(string[] args)
   ```

1. Mentse a `Program.cs` fájlt.

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Mielőtt hívást kezdeményez az Azure API-khoz, meg kell kérnie a hitelesítő adatait a Azure Portal.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

### <a name="add-the-connection-string-to-the-app"></a>Adja hozzá a kapcsolódási karakterláncot az alkalmazáshoz

Adja hozzá a kapcsolati karakterláncot az alkalmazáshoz, hogy hozzáférhessen a Storage-fiókhoz.

1. Váltson vissza a Visual Studio Code-ra.

1. A `Main` metódusban cserélje le a `Console.WriteLine("Hello, World");` kódot a következő sorra, amely a környezeti változóból lekéri a kapcsolatok karakterláncát.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

1. Adja hozzá a következő kódot `Main` egy üzenetsor-objektum létrehozásához, amelyet később a küldési és fogadási metódusok továbbítanak.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

1. Mentse a fájlt.

## <a name="insert-messages-into-the-queue"></a>Üzenetek beszúrása a várólistába

Hozzon létre egy új metódust, amely üzenetet küld a várólistába.

1. Adja hozzá a következő `InsertMessageAsync` metódust a `Program` osztályhoz.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   Ez a metódus egy üzenetsor-hivatkozást adott át. Egy új várólista jön létre, ha még nem létezik, a hívásával [`CreateIfNotExistsAsync`](/dotnet/api/azure.storage.queues.queueclient.createifnotexistsasync) . Ezután hozzáadja a `newMessage` -t a várólistához a hívásával [`SendMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_InsertMessage":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   Ez a metódus egy üzenetsor-hivatkozást adott át. Egy új várólista jön létre, ha még nem létezik, a hívásával [`CreateIfNotExistsAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync) . Ezután hozzáadja a `newMessage` -t a várólistához a hívásával [`AddMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_InsertMessage":::

1. Nem **kötelező:** Alapértelmezés szerint az üzenet maximális élettartama hét napig van beállítva. Megadhatja az üzenet élettartamának pozitív számát. A következő kódrészlet olyan üzenetet hoz létre, amely **soha nem** jár le.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

    A nem lejáró üzenet hozzáadásához használja a `Timespan.FromSeconds(-1)` hívást a következőre: `SendMessageAsync` .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

    A nem lejáró üzenet hozzáadásához használja a `Timespan.FromSeconds(-1)` hívást a következőre: `AddMessageAsync` .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

1. Mentse a fájlt.

Az üzenetsor-üzenetnek olyan formátumúnak kell lennie, amely UTF-8 kódolással kompatibilis XML-kérelemmel rendelkezik. Egy üzenet akár 64 KB méretű is lehet. Ha egy üzenet bináris adatfájlt tartalmaz, [Base64 kódolással kódolja](/dotnet/api/system.convert.tobase64string) az üzenetet.

## <a name="dequeue-messages"></a>Üzenetek várólistára helyezése

Hozzon létre egy új metódust egy üzenet lekéréséhez a várólistából. Az üzenet sikeres fogadása után fontos, hogy törölje azt a sorból, hogy ne dolgozza fel egynél többször.

1. Adjon hozzá egy nevű új metódust `RetrieveNextMessageAsync` a `Program` osztályhoz.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   Ez a metódus az üzenetsor üzeneteit hívja [`ReceiveMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) meg, és az `1` első paramétert beolvasva csak a következő üzenetet kérdezi le a várólistán. Az üzenet fogadása után a hívásával törölje azt a várólistából [`DeleteMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.deletemessageasync) .

   Ha egy üzenetet küld a rendszer az SDK-nak a V12-es verzióval megelőzően, akkor az automatikusan Base64 kódolású lesz. A V12-es verziótól kezdve a funkciók el lettek távolítva. Ha a V12 SDK használatával kér le egy üzenetet, az nem lesz automatikusan Base64-kódolású. Explicit módon [Base64-dekódolja](/dotnet/api/system.convert.frombase64string) a tartalmat.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   Ez a metódus az üzenetsor üzeneteit hívja meg [`GetMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync) . Az üzenet fogadása után a hívásával törölje azt a várólistából [`DeleteMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync) .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

1. Mentse a fájlt.

## <a name="delete-an-empty-queue"></a>Üres üzenetsor törlése

Az ajánlott eljárás a projekt végén annak azonosítása, hogy továbbra is szüksége van-e a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Ha a várólista létezik, de üres, kérje meg a felhasználót, ha törölni szeretné.

1. Bontsa ki a `RetrieveNextMessageAsync` metódust, hogy az üres várólista törlésére vonatkozó kérést tartalmazzon.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

1. Mentse a fájlt.

## <a name="check-for-command-line-arguments"></a>Parancssori argumentumok keresése

Ha vannak olyan parancssori argumentumok, amelyek bekerültek az alkalmazásba, tegyük fel, hogy a várólistába kerülő üzenet jelenik meg. Összekapcsolja az argumentumokat egy karakterlánc létrehozásához. Adja hozzá ezt a karakterláncot az üzenetsor-sorhoz a `InsertMessageAsync` korábban hozzáadott metódus meghívásával.

Ha nincsenek parancssori argumentumok, próbálkozzon egy lekérési művelettel. Hívja `RetrieveNextMessageAsync` meg a metódust a várólista következő üzenetének lekéréséhez.

Végül várjon a felhasználói adatbevitelre, mielőtt meghívja a parancsot `Console.ReadLine` .

1. Bontsa ki a `Main` metódust a parancssori argumentumok kereséséhez, és várjon a felhasználói adatbevitelre.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_Main":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_Main":::

1. Mentse a fájlt.

## <a name="complete-code"></a>Teljes kód

Itt látható a projekt teljes kódjának listája.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_AllCode":::

   # <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_AllCode":::
   ---

## <a name="build-and-run-the-app"></a>Készítsen buildet és futtassa az alkalmazást

1. A projekt könyvtárának parancssorában futtassa a következő DotNet-parancsot a projekt felépítéséhez.

   ```console
   dotnet build
   ```

1. Miután a projekt sikeresen létrejött, a következő parancs futtatásával adja hozzá az első üzenetet a várólistához.

   ```console
   dotnet run First queue message
   ```

   A következő kimenetnek kell megjelennie:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

1. Futtassa az alkalmazást parancssori argumentum nélkül, hogy fogadja és eltávolítsa az első üzenetet a várólistában.

   ```console
   dotnet run
   ```

1. Folytassa az alkalmazás futtatását, amíg az összes üzenetet el nem távolítja. Ha még egyszer futtatja, egy üzenet jelenik meg arról, hogy a várólista üres, és a várólista törlésére vonatkozó kérés.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

1. Üzenetsor létrehozása
1. Üzenetek hozzáadása és eltávolítása egy várólistából
1. Azure Queue Storage üzenetsor törlése

További információért tekintse meg az Azure Queue Storage rövid útmutatóját.

> [!div class="nextstepaction"]
> [A portálra vonatkozó rövid útmutató](storage-quickstart-queues-portal.md)

- [A .NET-hez készült üzenetsor-bevezetés](storage-quickstart-queues-dotnet.md)
- [A Java-hoz készült üzenetsor-bevezető](storage-quickstart-queues-java.md)
- [A Pythonhoz készült üzenetsor-gyors bevezetés](storage-quickstart-queues-python.md)
- [A JavaScripthez kapcsolódó várakozási útmutató](storage-quickstart-queues-nodejs.md)
