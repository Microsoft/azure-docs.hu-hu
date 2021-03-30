---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179337"
---
Ha olyan közös hozzáférési aláírási (SAS) URL-címmel rendelkezik, amely hozzáférést biztosít egy Storage-fiók erőforrásaihoz, használhatja az SAS-t egy kapcsolati karakterláncban. Mivel az SAS tartalmazza a kérelem hitelesítéséhez szükséges információkat, az SAS-vel rendelkező kapcsolati sztring biztosítja a protokollt, a szolgáltatási végpontot, valamint az erőforrás eléréséhez szükséges hitelesítő adatokat.

Megosztott elérési aláírást tartalmazó kapcsolati sztring létrehozásához a karakterláncot a következő formátumban kell megadni:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Az egyes szolgáltatási végpontok nem kötelezőek, de a kapcsolatok karakterláncának legalább egyet tartalmaznia kell.

> [!NOTE]
> Ajánlott eljárás a HTTPS használata SAS használatával.
>
> Ha egy konfigurációs fájlban lévő SAS-t ad meg a kapcsolódási karakterláncban, előfordulhat, hogy az URL-ben speciális karaktereket kell kódolnia.
>
>

### <a name="service-sas-example"></a>Service SAS-példa
Az alábbi példa egy, a blob Storage-hoz készült Service SAS-t tartalmazó kapcsolatok sztringet tartalmaz:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

És íme egy példa arra, hogy ugyanaz a kapcsolatok karakterlánca a speciális karakterek kódolásával:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Fiók SAS-példa
Íme egy példa egy olyan, a kapcsolatok karakterláncára, amely egy fiók SAS-t tartalmaz a Blobhoz és a file Storage-hoz. Vegye figyelembe, hogy mindkét szolgáltatáshoz végpontok vannak megadva:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

Íme egy példa arra, hogy ugyanazt a kapcsolatok sztringet URL-kódolással:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

