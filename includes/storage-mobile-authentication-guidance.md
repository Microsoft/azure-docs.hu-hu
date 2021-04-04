---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: eeea7b1fed0c2a1f805e21b4dec9ec3cad7fc976
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996812"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Az alkalmazás konfigurálása az Azure Storage eléréséhez
Az alkalmazás két módon hitelesíthető a tárolási szolgáltatások eléréséhez:

* Megosztott kulcs: csak tesztelési célra használja a megosztott kulcsot
* Közös hozzáférésű aláírás (SAS): éles alkalmazásokhoz használható SAS használata

### <a name="shared-key"></a>Megosztott kulcsos
A megosztott kulcsos hitelesítés azt jelenti, hogy az alkalmazás a fiók nevét és a fiók kulcsát fogja használni a tárolási szolgáltatások eléréséhez. Ennek a könyvtárnak a használatának gyors bemutatásához a megosztott kulcsos hitelesítést fogjuk használni ebben az első lépésekben.

> [!WARNING] 
> **Csak a megosztott kulcsos hitelesítés használata tesztelési célokra.** A fiók neve és a fiók kulcsa, amely teljes olvasási/írási hozzáférést biztosít a társított Storage-fiókhoz, minden olyan személy számára elérhető lesz, aki letölti az alkalmazást. Ez nem jó gyakorlat, mivel a **nem** megbízható ügyfelek által feltört kulcs veszélye.
> 
> 

Megosztott kulcsos hitelesítés használatakor létre kell hoznia egy [kapcsolódási karakterláncot](../articles/storage/common/storage-configure-connection-string.md). A kapcsolatok karakterlánca a következőkből áll:  

* A **DefaultEndpointsProtocol** – http vagy https lehetőséget is választhat. A HTTPS használata azonban kifejezetten ajánlott.
* A **fióknév** – a Storage-fiók neve
* A **fiók kulcsa** – az [Azure Portalon](https://portal.azure.com)navigáljon a Storage-fiókjához, és kattintson a **kulcsok** ikonra az információk megkereséséhez.
* Választható **EndpointSuffix** – a különböző végponti utótagokkal (például az Azure China vagy az Azure szabályozásával) rendelkező régiókban használt tárolási szolgáltatásokhoz.

Íme egy példa a megosztott kulcsos hitelesítést használó kapcsolatok sztringre:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Közös hozzáférésű jogosultságkódok (SAS)
A mobil alkalmazások esetében a javasolt módszer az ügyfél által az Azure Storage szolgáltatáson keresztüli kérelem hitelesítésére közös hozzáférésű aláírás (SAS) használatával. Az SAS lehetővé teszi, hogy egy adott időszakra vonatkozóan ügyfél-hozzáférést biztosítson egy erőforráshoz egy megadott engedélyekkel.
A Storage-fiók tulajdonosaként egy SAS-t kell létrehoznia a mobil ügyfelek számára. Az SAS létrehozásához érdemes lehet egy különálló szolgáltatást írni, amely létrehozza az SAS-t az ügyfelek számára. Tesztelési célból a [Microsoft Azure Storage Explorer](https://storageexplorer.com) vagy az [Azure Portal](https://portal.azure.com) használatával létrehozhat sas-t. Az SAS létrehozásakor megadhatja azt az időintervallumot, ameddig az SAS érvényes, valamint a SAS által az ügyfélnek biztosított engedélyeket.

Az alábbi példa bemutatja, hogyan hozhatja ki a SAS-t a Microsoft Azure Storage Explorer használatával.

1. Ha még nem tette meg, [telepítse a Microsoft Azure Storage Explorer](https://storageexplorer.com)
2. Csatlakozzon az előfizetéséhez.
3. Kattintson a Storage-fiókra, majd kattintson a bal alsó sarokban található "műveletek" fülre. Kattintson a "közös hozzáférésű aláírás beolvasása" elemre a "kapcsolati karakterlánc" létrehozásához az SAS számára.
4. Íme egy példa egy olyan SAS-kapcsolatok sztringre, amely olvasási és írási jogosultságot biztosít a Storage-fiók blob szolgáltatásához tartozó szolgáltatás, tároló és objektum szintjén.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Amint láthatja, a SAS használatakor nem teszi közzé a fiókja kulcsát az alkalmazásában. Az SAS-vel és az ajánlott eljárásokkal kapcsolatos további információkért tekintse meg a [közös hozzáférési aláírások: az SAS-modell megismerése](../articles/storage/common/storage-sas-overview.md)című témakört.