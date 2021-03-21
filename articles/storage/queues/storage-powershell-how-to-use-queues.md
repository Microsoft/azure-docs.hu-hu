---
title: Az Azure Queue Storage használata a PowerShellből – Azure Storage
description: Műveleteket hajthat végre az Azure Queue Storage PowerShell használatával. Az Azure Queue Storage segítségével nagy számú, HTTP/HTTPS által elérhető üzenetet tárolhat.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 05/15/2019
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fba288f76377e744b1fe21a52e03a43409c505bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97585615"
---
# <a name="how-to-use-azure-queue-storage-from-powershell"></a>Az Azure Queue Storage használata a PowerShell-lel

Az Azure Queue Storage egy olyan szolgáltatás, amely a világ bármely pontjáról HTTP vagy HTTPS használatával elérhető, nagy mennyiségű üzenetet tárol. Részletes információkat az [Azure Queue Storage bemutatása](storage-queues-introduction.md)című témakörben talál. Ez a cikk a közös Queue Storage műveleteket ismerteti. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
>
> - Üzenetsor létrehozása
> - Üzenetsor beolvasása
> - Üzenet hozzáadása
> - Üzenet elolvasása
> - Üzenet törlése
> - Üzenetsor törlése

Ehhez a útmutatóhoz a Azure PowerShell ( `Az` ) modul 0,7-es vagy újabb verziójára van szükség. `Get-Module -ListAvailable Az`A jelenleg telepített verzió megkereséséhez futtassa a parancsot. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.

A várólisták adatsíkja nem tartalmaz PowerShell-parancsmagokat. Az adatsík-műveletek, például az üzenetek hozzáadása, az üzenet olvasása és az üzenet törlése után a .NET Storage ügyféloldali kódtárat kell használnia, mivel az elérhető a PowerShellben. Hozzon létre egy üzenet objektumot, majd olyan parancsokat is használhat `AddMessage` , amelyekkel műveleteket végezhet az adott üzeneten. Ez a cikk bemutatja, hogyan teheti meg.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Helyszínek listájának beolvasása

Ha nem tudja, melyik helyet szeretné használni, kilistázhatja az elérhető helyeket. A megjelenő listában keresse meg a használni kívánt helyet. Ez a gyakorlat a következőt fogja használni: `eastus` . Ezt a változót későbbi felhasználás céljából tárolja a változóban `location` .

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal.

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoport nevét egy változóban tárolja későbbi használatra. Ebben a példában egy `howtoqueuesrg` nevű erőforráscsoport jön létre a `eastus` régióban.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Storage-fiók létrehozása

Hozzon létre egy szabványos általános célú Storage-fiókot a helyileg redundáns tárolással (LRS) a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)használatával. Szerezze be a használni kívánt Storage-fiókot definiáló Storage-fiók környezetét. Ha a tárfiókokkal való munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Az alábbi példa először kapcsolatot létesít az Azure Storage-val a Storage-fiók környezetével, amely magában foglalja a Storage-fiók nevét és a hozzá tartozó hozzáférési kulcsot. Ezután meghívja a [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue) parancsmagot a nevű várólista létrehozásához `howtoqueue` .

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Az Azure Queue Storage elnevezési konvenciókkal kapcsolatos információkért lásd: [várólisták és metaadatok elnevezése](/rest/api/storageservices/naming-queues-and-metadata).

## <a name="retrieve-a-queue"></a>Üzenetsor beolvasása

Lekérdezheti és lekérhet egy adott várólistát vagy egy Storage-fiókban lévő összes várólistát tartalmazó listát. Az alábbi példák azt mutatják be, hogyan kérhető le az összes várólista a Storage-fiókban, valamint egy adott várólista; mindkét parancs a [Get-AzStorageQueue](/powershell/module/az.storage/get-azstoragequeue) parancsmagot használja.

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Üzenet hozzáadása egy várólistához

Az üzenetsor tényleges üzeneteit befolyásoló műveletek a .NET Storage ügyféloldali kódtárat használják a PowerShellben elérhetőként. Ha üzenetet szeretne hozzáadni egy várólistához, hozzon létre egy új példányt az üzenet objektuma, [`Microsoft.Azure.Storage.Queue.CloudQueueMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage) osztály. Ezután hívja meg a [`AddMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage) metódust. A `CloudQueueMessage` létrehozható egy karakterláncból (UTF-8 formátumban) vagy egy bájtos tömbből.

Az alábbi példa bemutatja, hogyan adhat hozzá egy üzenetet a várólistához.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Ha a [Azure Storage Explorer](https://storageexplorer.com)használja, kapcsolódhat az Azure-fiókjához, és megtekintheti a Storage-fiókban lévő várólistákat, és részletesen megtekintheti a várólistán lévő üzeneteket.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Üzenet olvasása a sorból, majd a törlés

Az üzenetek beolvasása a legjobb – első kijelentkezési sorrendben. Ez nem garantált. Ha elolvassa az üzenetet a várólistából, a rendszer láthatatlanná válik a várólistában megtekintett összes többi folyamatnál. Ez biztosítja, hogy ha a kód hardver vagy szoftver meghibásodása miatt nem tudja feldolgozni az üzenetet, a kód egy másik példánya ugyanazt az üzenetet kapja, és próbálkozzon újra.

Ez a **láthatósági időkorlát** azt határozza meg, hogy az üzenet hányszor maradjon láthatatlan, mielőtt újra elérhetővé válik a feldolgozáshoz. Az alapértelmezett érték 30 mp.

A kód két lépésben beolvas egy üzenetet a várólistából. A metódus meghívásakor a [`Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) következő üzenet jelenik meg a várólistán. A visszaadott üzenet a `GetMessage` várólistából beolvasott más kódokba is láthatatlanná válik. Az üzenet várólistából való eltávolításának befejezéséhez hívja meg a [`Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage) metódust.

Az alábbi példában elolvashatja a három üzenetsor-üzenetet, majd várjon 10 másodpercet (a láthatatlanság időtúllépését). Ezután olvassa el újra a három üzenetet, és törölje az üzeneteket a hívásával `DeleteMessage` . Ha az üzenetek törlése után megpróbálja beolvasni a várólistát, a rendszer a következőt `$queueMessage` adja vissza: `$null` .

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Üzenetsor törlése

Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja meg a `Remove-AzStorageQueue` parancsmagot. Az alábbi példa bemutatja, hogyan törölheti a gyakorlatban használt adott várólistát a `Remove-AzStorageQueue` parancsmag használatával.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyakorlatban létrehozott összes eszköz eltávolításához távolítsa el az erőforráscsoportot. Így törli a csoportban lévő összes erőforrást is. Ebben az esetben eltávolítja a létrehozott Storage-fiókot és magát az erőforráscsoportot.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta az alapszintű Queue Storage felügyeletét a PowerShell-lel, beleértve a következőket:

> [!div class="checklist"]
>
> - Üzenetsor létrehozása
> - Üzenetsor beolvasása
> - Üzenet hozzáadása
> - A következő üzenet elolvasása
> - Üzenet törlése
> - Üzenetsor törlése

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell Storage-parancsmagok

- [Tárolási PowerShell-parancsmagok](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

- A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
