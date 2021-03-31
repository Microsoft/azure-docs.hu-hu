---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: a9d7f4f77d91abc88ea348e71a3d9c471b26a273
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103622321"
---
Az emulátor egyetlen rögzített fiókot és egy jól ismert hitelesítési kulcsot támogat a megosztott kulcsos hitelesítéshez. Ez a fiók és a kulcs az az egyetlen megosztott kulcsú hitelesítő adat, amelyet az emulátorsal való használatra engedélyeznek. Ezek a következők:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Az emulátor által támogatott hitelesítési kulcs kizárólag az ügyfél-hitelesítési kód működésének tesztelésére szolgál. Nem szolgál biztonsági céllal. A termelési Storage-fiók és a kulcs nem használható az emulátorral. A fejlesztési fiókot ne használja éles adatszolgáltatással.
>
> Az emulátor csak a HTTP protokollon keresztül támogatja a kapcsolatokat. Azonban a HTTPS az a javasolt protokoll, amellyel a termelési Azure Storage-fiók erőforrásaihoz férhet hozzá.
>

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>Kapcsolódás az Emulator-fiókhoz a parancsikon használatával

Az emulátorhoz az alkalmazásból való kapcsolódás legegyszerűbb módja egy kapcsolati karakterlánc konfigurálása az alkalmazás konfigurációs fájljában, amely hivatkozik a parancsikonra `UseDevelopmentStorage=true` . A parancsikon megegyezik az emulátor teljes kapcsolódási karakterlánccal, amely megadja a fiók nevét, a fiók kulcsát és az emulátor végpontját az egyes Azure Storage-szolgáltatásokhoz:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
```

A következő .NET-kódrészlet azt mutatja be, hogyan használhatja a parancsikont olyan metódusból, amely a kapcsolatok karakterláncát veszi igénybe. Például a [BlobContainerClient (karakterlánc, karakterlánc)](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) konstruktora egy kapcsolatot megadó karakterláncot használ.

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

Győződjön meg arról, hogy az emulátor fut, mielőtt meghívja a kódot a kódrészletben.
