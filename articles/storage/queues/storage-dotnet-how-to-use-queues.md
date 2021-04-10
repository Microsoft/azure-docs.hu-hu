---
title: Az Azure Queue Storage használatának első lépései a .NET-Azure Storage használatával
description: Az Azure Queue Storage megbízható, aszinkron üzenetkezelést biztosít az alkalmazások összetevői között. A felhőbeli üzenetkezelésnek köszönhetően az alkalmazások összetevői függetlenül méretezhetők.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 10/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e32779e75480d365ec10e8c7f849fddd27c891c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275991"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Az Azure Queue Storage használatának első lépései a .NET-keretrendszerrel

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Áttekintés

Az Azure Queue Storage felhőalapú üzenetkezelést biztosít az alkalmazások összetevői között. A méretezhető alkalmazások tervezésekor az alkalmazás-összetevők gyakran le vannak választva, így egymástól függetlenül méretezhetők. A Queue Storage aszinkron üzenetküldést biztosít az alkalmazás-összetevők között, függetlenül attól, hogy a felhőben, az asztalon, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a folyamat munkafolyamatainak létrehozását is.

### <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése

Ez az oktatóanyag bemutatja, hogyan írhat .NET-kódot néhány gyakori forgatókönyvhöz az Azure Queue Storage használatával. Az ismertetett forgatókönyvek az üzenetsorok létrehozására és törlésére, valamint az üzenetsor üzeneteinek hozzáadására, olvasására és törlésére vonatkoznak.

**Az oktatóanyag áttekintésének becsült ideje:** 45 perc

### <a name="prerequisites"></a>Előfeltételek

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- [Azure Storage-fiók](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A következő lépésként állítsa be a fejlesztési környezetet a Visual Studióban, hogy kipróbálhassa az útmutatóban megadott példákat.

### <a name="create-a-windows-console-application-project"></a>Windows-konzolalkalmazás projekt létrehozása

Hozzon létre egy új Windows-konzolalkalmazást a Visual Studióban. A következő lépések bemutatják, hogyan hozhat létre egy Console-alkalmazást a Visual Studio 2019-ben. A lépések a Visual Studio más verziói esetén is hasonlók.

1. Válassza a **fájl**  >  **új**  >  **projekt** lehetőséget.
2. **Platform**  >  **Windows** kiválasztása
3. A **konzol alkalmazás kiválasztása (.NET-keretrendszer)**
4. Kattintson a **Tovább** gombra.
5. A **projekt neve** mezőben adja meg az alkalmazás nevét
6. Kattintson a **Létrehozás** elemre.

Az oktatóanyagban szereplő példák hozzáadhatók a konzolalkalmazás `Program.cs` fájljában található `Main()` metódushoz.

Az Azure Storage ügyféloldali kódtárait bármilyen típusú .NET-alkalmazásban használhatja, beleértve az Azure Cloud Service-t vagy a webalkalmazást, valamint az asztali és mobil alkalmazásokat is. Ebben az útmutatóban az egyszerűség kedvéért egy konzolalkalmazást használunk.

### <a name="use-nuget-to-install-the-required-packages"></a>A szükséges csomagok telepítése a NuGettel

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

A következő négy csomagra kell hivatkoznia a projektben az oktatóanyag elvégzéséhez:

- [Azure. Core kódtár a .net-hez](https://www.nuget.org/packages/azure.core/): Ez a csomag megosztott primitíveket, absztraktokat és segítőket biztosít a modern .net Azure SDK ügyféloldali kódtárak számára.
- [Azure. Storage. Common Client Library for .net](https://www.nuget.org/packages/azure.storage.common/): Ez a csomag a többi Azure Storage ügyféloldali kódtára által megosztott infrastruktúrát biztosít.
- [Azure. Storage. Queues ügyféloldali kódtár a .net](https://www.nuget.org/packages/azure.storage.queues/)-hez: Ez a csomag lehetővé teszi az Azure Queue Storage használatát az ügyfél által elérhető üzenetek tárolásához.
- A [.net-hez készült urationManager-függvénytárSystem.Configuration.Config](https://www.nuget.org/packages/system.configuration.configurationmanager/): Ez a csomag hozzáférést biztosít a konfigurációs fájlokhoz az ügyfélalkalmazások számára.

A NuGet a csomagok beszerzéséhez használható. Kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektre **megoldáskezelő**, majd válassza a **NuGet-csomagok kezelése** lehetőséget.
1. **Tallózás** kiválasztása
1. Keressen rá az online kifejezésre `Azure.Storage.Queues` , majd válassza a **telepítés** lehetőséget az Azure Storage ügyféloldali kódtár és függőségeinek telepítéséhez. Ez az Azure. Storage. Common és az Azure. Core kódtárakat is telepíti, amelyek a várólista-függvénytár függőségei.
1. Keresse meg az online kifejezést `System.Configuration.ConfigurationManager` , majd válassza a **telepítés** lehetőséget a Configuration Manager telepítéséhez.

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

A következő három csomagra kell hivatkoznia a projektben az oktatóanyag elvégzéséhez:

- [Microsoft. Azure. Storage. Common ügyféloldali kódtár a .net-hez](https://www.nuget.org/packages/microsoft.azure.storage.common/): Ez a csomag programozott hozzáférést biztosít a Storage-fiókban lévő adaterőforrásokhoz.
- [Microsoft Azure Queue Storage .net-hez készült ügyféloldali kódtára](https://www.nuget.org/packages/microsoft.azure.storage.queue/): ez az ügyféloldali kódtár lehetővé teszi az Azure Queue Storage használatát az ügyfél által elérhető üzenetek tárolásához.
- [A Microsoft Azure Configuration Manager könyvtár a .NET-hez](https://www.nuget.org/packages/microsoft.azure.configurationmanager/): Ez a csomag egy osztályt biztosít a konfigurációs fájlban található kapcsolati sztring elemzéséhez, függetlenül attól, hogy az alkalmazás hol fut.

A NuGet a csomagok beszerzéséhez használható. Kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektre **megoldáskezelő**, majd válassza a **NuGet-csomagok kezelése** lehetőséget.
1. **Tallózás** kiválasztása
1. Keressen rá az online kifejezésre `Microsoft.Azure.Storage.Queue` , majd válassza a **telepítés** lehetőséget az Azure Storage ügyféloldali kódtár és függőségeinek telepítéséhez. Ez a Microsoft. Azure. Storage. Common könyvtárat is telepíti, amely a várólista-függvénytár függősége.
1. Keressen rá az online kifejezésre `Microsoft.Azure.ConfigurationManager` , majd válassza a **telepítés** lehetőséget az Azure-Configuration Manager telepítéséhez.

---

### <a name="determine-your-target-environment"></a>A célkörnyezet meghatározása

Az útmutatóban lévő példákat kétféle környezetben futtathatja:

- A kódot futtathatja a felhőben, egy Azure Storage-fiókban.
- A kódot a Azurite Storage Emulator használatával futtathatja. A Azurite egy helyi környezet, amely egy Azure Storage-fiókot emulál a felhőben. A Azurite egy ingyenes lehetőség a kód tesztelésére és hibakeresésére, miközben az alkalmazás fejlesztés alatt áll. Az emulátor egy jól ismert fiókot és kulcsot használ. További információ: [a Azurite Emulator használata a helyi Azure Storage-fejlesztéshez és-teszteléshez](../common/storage-use-azurite.md).

> [!NOTE]
> A Storage Emulator megcélzásával elkerülheti az Azure Storage-hoz kapcsolódó költségeket. Ha azonban úgy dönt, hogy a felhőben egy Azure Storage-fiókot céloz meg, az oktatóanyag elvégzésének költségei elhanyagolva lesznek.

## <a name="get-your-storage-connection-string"></a>A Storage-beli kapcsolatok karakterláncának beolvasása

Az Azure Storage ügyféloldali kódtárai a .NET-támogatáshoz egy Storage-kapcsolódási karakterlánc használatával konfigurálja a végpontokat és a hitelesítő adatokat a tárolási szolgáltatások eléréséhez. További információ: a [Storage-fiók elérési kulcsainak kezelése](../common/storage-account-keys-manage.md).

### <a name="copy-your-credentials-from-the-azure-portal"></a>A hitelesítési adatok másolása az Azure Portalról

A mintakódnak hitelesítenie kell a tárfiókhoz való hozzáférést. A hitelesítéshez meg kell adni az alkalmazás számára a tárfiók hitelesítő adatait egy kapcsolati sztring formájában. A tárfiók hitelesítő adatainak megtekintéséhez a következőt kell tennie:

1. Navigáljon a [Azure Portal](https://portal.azure.com).
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet. Megjelennek a fiókhoz tartozó hozzáférési kulcsok, valamint az egyes kulcsokhoz tartozó kapcsolati sztringek.
4. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához. A kapcsolati sztring értékét hozzáadja egy környezeti változóhoz a következő lépés során.

    ![A kapcsolati sztring az Azure Portalról történő másolását bemutató képernyőkép](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

A kapcsolati sztringekkel kapcsolatos további információkért lásd: [Az Azure Storage kapcsolati sztringjének konfigurálása](../common/storage-configure-connection-string.md).

> [!NOTE]
> A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. Mindig ügyeljen a tárfiók kulcsának védelmére. Ne adja ki másoknak, ne kódolja fixen és ne mentse egy mások számára elérhető egyszerű szöveges fájlban. Ha azt gyanítja, hogy a kulcs biztonsága sérült, az Azure portál segítségével generálja újra.

A tárolási kapcsolati sztring egy konfigurációs fájlban tartható fenn a legjobban. A kapcsolati sztring konfigurálásához nyissa meg az `app.config` fájlt a Visual Studio Megoldáskezelőjében. Adja hozzá az `<appSettings>` itt látható elem tartalmát. Cserélje le a `connection-string` elemet a Storage-fiókból a portálon másolt értékre:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

A konfiguráció beállítása például hasonló lesz a következőhöz:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

A Azurite-tároló emulátorának megcélzásához használhatja a jól ismert fiók nevét és kulcsát leképező parancsikont. Ebben az esetben a kapcsolati sztring beállítása a következő:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>Hozzáadás irányelvekkel

Adja hozzá a következő `using` irányelveket a `Program.cs` fájl elejéhez:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-storage-client"></a>Az Queue Storage-ügyfél létrehozása

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Az [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient) osztály lehetővé teszi Queue Storage tárolt várólisták lekérését. A szolgáltatásügyfél létrehozásának egyik módja:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

Az [`CloudQueueClient`](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true) osztály lehetővé teszi Queue Storage tárolt várólisták lekérését. A szolgáltatásügyfél létrehozásának egyik módja:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

Most már készen áll arra, hogy olyan kódot írjon, amely beolvassa az adatokat, és adatokat ír Queue Storageba.

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Ez a példa egy üzenetsor létrehozását mutatja be:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>Üzenet beszúrása egy üzenetsorba

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Ha egy üzenetet egy meglévő várólistába szeretne beszúrni, hívja meg a [`SendMessage`](/dotnet/api/azure.storage.queues.queueclient.sendmessage) metódust. Egy üzenet lehet karakterlánc (UTF-8 formátumban) vagy egy bájtos tömb. A következő kód létrehoz egy várólistát (ha még nem létezik), és beszúr egy üzenetet:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

Ha egy üzenetet egy meglévő várólistába szeretne beszúrni, először hozzon létre egy újat [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Ezután hívja meg a [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) metódust. A `CloudQueueMessage` létrehozható egy karakterláncból (UTF-8 formátumban) vagy egy bájtos tömbből. Az alábbi kód létrehoz egy várólistát (ha még nem létezik), és beszúrja az üzenetet `Hello, World` : Ha egy meglévő várólistába kíván üzenetet beszúrni, először hozzon létre egy újat [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Ezután hívja meg a [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) metódust. A `CloudQueueMessage` létrehozható egy karakterláncból (UTF-8 formátumban) vagy egy bájtos tömbből. Az alábbi kód létrehoz egy várólistát (ha nem létezik), és beszúrja az üzenetet `Hello, World` :

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>Betekintés a következő üzenetbe

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

A várólistában lévő üzenetek a metódus meghívásával történő eltávolítása nélkül is betekintést nyerhetnek [`PeekMessages`](/dotnet/api/azure.storage.queues.queueclient.peekmessages) . Ha nem adja meg a `maxMessages` paraméter értékét, az alapértelmezett érték egy üzenetbe való betekintés.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

A várólista elején lévő üzenetbe való betekintés nélkül is betekintést nyerhet a várólistába [`PeekMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true) .

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Üzenetsorban található üzenet tartalmának módosítása

Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód frissíti az üzenetsorban található üzenetet az új tartalommal, és a láthatósági időkorlátot további 60 másodperccel bővíti. Elmenti az üzenethez társított feladat állapotát, és az ügyfél számára további egy percet biztosít az üzenet használatának folytatására. Ezzel a technikával nyomon követheti a várólista-üzenetek többlépéses munkafolyamatait anélkül, hogy az elejétől kellene kezdenie, ha a feldolgozási lépés hardverhiba vagy szoftver meghibásodása miatt meghiúsul. A rendszer általában nyilván tartja az újrapróbálkozások számát, és ha az üzenettel *n* alkalomnál többször próbálkoznak, akkor törlődik. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="dequeue-the-next-message"></a>A következő üzenet elküldése a sorból

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Egy üzenetsor egy üzenetének egy két lépésben történő elutasítása. A hívásakor [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) a következő üzenet jelenik meg egy várólistában. A visszaadott üzenet a `ReceiveMessages` várólistából beolvasott más kódokba is láthatatlanná válik. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Az üzenet várólistából való eltávolításának befejezéséhez meg kell hívni a következőt is: [`DeleteMessage`](/dotnet/api/azure.storage.queues.queueclient.deletemessage) . Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A kód meghívása `DeleteMessage` közvetlenül az üzenet feldolgozása után történik.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

A kód két lépésben elvégez egy üzenetsor-várólistán lévő üzenetet. A hívásakor [`GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true) a következő üzenet jelenik meg egy várólistában. A visszaadott üzenet a `GetMessage` várólistából beolvasott más kódokba is láthatatlanná válik. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Az üzenet várólistából való eltávolításának befejezéséhez meg kell hívni a következőt is: [`DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true) . Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A kód meghívása `DeleteMessage` közvetlenül az üzenet feldolgozása után történik.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

<!-- docutune:casing "Async-Await pattern" "Async and Await" -->

## <a name="use-the-async-await-pattern-with-common-queue-storage-apis"></a>A Async-Await minta használata általános Queue Storage API-kkal

Ez a példa azt szemlélteti, hogyan használható a Async-Await minta közös Queue Storage API-kkal. A minta meghívja az egyes metódusok aszinkron verzióját, ahogy azt az `Async` egyes módszerek utótagja jelzi. Aszinkron metódus használata esetén a Async-Await minta a hívás befejezéséig felfüggeszti a helyi végrehajtást. Ez a viselkedés lehetővé teszi, hogy az aktuális szál más feladatokkal foglalkozzon. Ennek segítségével elkerülhetők a szűk keresztmetszetek a teljesítményben, és az alkalmazás általános válaszkészsége is javul. A Async-Await minta .NET-ben való használatával kapcsolatos további információkért lásd: [aszinkron és várakozás (C# és Visual Basic)](/previous-versions/hh191443(v=vs.140))

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="use-additional-options-for-dequeuing-messages"></a>A dequeuing üzenetek további beállításainak használata

Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

A következő példában a metódus használatával [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) 20 üzenetet kap egy hívásban. Ezután az összes üzenetet feldolgozza egy `foreach` hurok használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az öt perc egyszerre indul el az összes üzenethez, így a hívása óta öt perc elteltével a `ReceiveMessages` rendszer a nem törölt üzeneteket ismét láthatóvá válik.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

A következő példában a metódus használatával [`GetMessages`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) 20 üzenetet kap egy hívásban. Ezután az összes üzenetet feldolgozza egy `foreach` hurok használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az öt perc egyszerre indul el az összes üzenethez, így a hívása óta öt perc elteltével a `GetMessages` rendszer a nem törölt üzeneteket ismét láthatóvá válik.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Megbecsülheti egy üzenetsorban található üzenetek számát. A [`GetProperties`](/dotnet/api/azure.storage.queues.queueclient.getproperties) metódus a várólista tulajdonságait adja vissza, beleértve az üzenetek darabszámát is. A [`ApproximateMessagesCount`](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount) tulajdonság a várólistában lévő üzenetek hozzávetőleges számát tartalmazza. Ez a szám nem kevesebb, mint a várólistában lévő üzenetek tényleges száma, de magasabb lehet.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

Megbecsülheti egy üzenetsorban található üzenetek számát. A [`FetchAttributes`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true) metódus a várólista attribútumait adja vissza, beleértve az üzenetek darabszámát is. A [`ApproximateMessageCount`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true) tulajdonság a metódus által lekért utolsó értéket adja vissza a `FetchAttributes` Queue Storage hívása nélkül.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>Üzenetsor törlése

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja [`Delete`](/dotnet/api/azure.storage.queues.queueclient.delete) meg a metódust a várólista-objektumon.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnetv11)

Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja [`Delete`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true) meg a metódust a várólista-objektumon.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte Queue Storage alapjait, az alábbi hivatkozásokat követve megismerheti az összetettebb tárolási feladatokat.

- Az elérhető API-kkal kapcsolatos részletes információkért tekintse meg az Queue Storage dokumentációját:
  - [Azure Storage ügyféloldali kódtára a .NET-hez – dokumentáció](/dotnet/api/overview/azure/storage)
  - [Az Azure Storage REST API referenciája](/rest/api/storageservices/)
- Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.
  - Az [Azure Table Storage használatának első lépései a .NET használatával](../../cosmos-db/tutorial-develop-table-dotnet.md) a strukturált adattároláshoz.
  - Az [Azure Blob Storage használatának első lépései a .NET használatával](../blobs/storage-quickstart-blobs-dotnet.md) a strukturálatlan adatmennyiségek tárolásához.
  - [Csatlakozzon az SQL Database adatbázishoz .NET (C#) használatával](../../azure-sql/database/connect-query-dotnet-core.md) a relációs adatok tárolásához.
- Megtudhatja, hogyan egyszerűsítheti az Azure Storage-ban az [Azure WEBJOBS SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)használatával írt kódot.
