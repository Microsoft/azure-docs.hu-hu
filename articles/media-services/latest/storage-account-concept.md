---
title: Azure Storage-fiókok
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan hozhat létre Azure Media Services használatával használható Azure Storage-fiókot.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: f37b453a294a0d0a7b9a99bfebe8f3eff09e8956
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291194"
---
# <a name="azure-storage-accounts"></a>Azure Storage-fiókok

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Az Azure-beli médiatartalmak kezelésének, titkosításának, kódolásának, elemzésének és továbbításának megkezdéséhez létre kell hoznia egy Media Services fiókot. A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot.

A Media Services-fióknak és az összes kapcsolódó tárfióknak azonos Azure-előfizetésben kell lennie. Erősen ajánlott a tárolási fiókok használata a Media Services-fiókkal megegyező helyen, így elkerülhetők a további késések és az adatforgalom költségei.

Egy **elsődleges** és tetszőleges számú **másodlagos** Storage-fiókot társíthat a Media Services-fiókhoz. A Media Services támogatja az **Általános célú v2-** (GPv2-) és az **Általános célú v1-** (GPv1-) fiókokat is. A blob only-fiókok nem engedélyezettek **elsődlegesként**.

Javasoljuk, hogy a GPv2 használja, így kihasználhatja a legújabb funkciókat és teljesítményt. További információ a Storage-fiókokról: az [Azure Storage-fiók áttekintése](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Csak a gyors elérési szint támogatott a Azure Media Serviceshoz való használathoz, bár a többi hozzáférési réteg is használható a tárolási költségek csökkentéséhez a nem aktívan használt tartalmak esetében.

Különböző SKU-ket is választhat a Storage-fiókhoz. További információ: Storage- [fiókok](/cli/azure/storage/account?view=azure-cli-latest). Ha a Storage-fiókokkal szeretne kísérletezni, használja a következőt: `--sku Standard_LRS` . Ha azonban az SKU-t éles környezetben választotta, érdemes figyelembe vennie `--sku Standard_RAGRS` , amely az üzletmenet folytonosságát biztosító földrajzi replikációt biztosít.

## <a name="assets-in-a-storage-account"></a>A Storage-fiókban lévő eszközök

A Media Services v3-as verziójában a Storage API-k segítségével tölthet fel fájlokat az eszközökre. További információ: [Azure Media Services v3-es eszközök](assets-concept.md).

> [!Note]
> Ne próbálja meg módosítani a Media Services SDK által létrehozott blob-tárolók tartalmát Media Services API-k használata nélkül.

## <a name="storage-side-encryption"></a>Tárolási oldal titkosítása

Az adategységek védelméhez az eszközöket a tárolási oldal titkosításával kell titkosítani. A következő táblázat bemutatja, hogyan működik a tárolási oldal titkosítása Media Services v3-ban:

|Titkosítási beállítás|Description|Media Services v3|
|---|---|---|
|Media Services Storage-titkosítás| AES-256 titkosítás, Media Services által felügyelt kulcs. |Nem támogatott. <sup>(1)</sup>|
|[Tárolási szolgáltatás titkosítása inaktív adatok esetén](../../storage/common/storage-service-encryption.md)|Az Azure Storage által kínált kiszolgálóoldali titkosítás, amelyet az Azure vagy az ügyfél kezel.|Támogatott.|
|[Storage ügyféloldali titkosítás](../../storage/common/storage-client-side-encryption.md)|Az Azure Storage által kínált ügyféloldali titkosítás, amelyet az ügyfél felügyel Key Vaultban.|Nem támogatott.|

<sup>1</sup> a Media Services v3-as verziójában a Storage encryption (AES-256 encryption) csak a visszamenőleges kompatibilitás érdekében támogatott, ha az eszközök Media Services v2-mel lettek létrehozva, ami azt jelenti, hogy a v3 a meglévő tárolók titkosított eszközeivel működik együtt, de nem teszi lehetővé újak létrehozását.

## <a name="storage-account-errors"></a>Storage-fiókok hibái

A Media Services-fiók Leválasztott állapota azt jelzi, hogy a fiók már nem rendelkezik hozzáféréssel egy vagy több csatolt tárfiókhoz a tárelérési kulcsok változása miatt. A Media Servicesnek naprakész tárelérési kulcsokra van szüksége a fiókbeli feladatok nagy részének végrehajtásához.

Az alábbiak az elsődleges forgatókönyvek, amelyek a Media Services-fiók a csatolt tárfiókokhoz való hozzáférésének megszűnését eredményezhetik.

|Probléma|Megoldás|
|---|---|
|A Media Services-fiók vagy a csatolt tárfiókok külön előfizetésekbe lettek migrálva. |Telepítse át a Storage-fiók (ok) t vagy Media Services fiókot úgy, hogy azok mind ugyanabban az előfizetésben legyenek. |
|A Media Services-fiók egy másik előfizetésben lévő csatolt tárfiókot használ, mivel ez egy régi Media Services-fiók, amikor ez még támogatott volt. Az összes korai Media Services fiók át lett konvertálva a modern Azure Resources Manager-alapú fiókokba, és leválasztott állapottal fog rendelkezni. |Telepítse át a Storage-fiókot vagy Media Services fiókot úgy, hogy azok mind ugyanabban az előfizetésben legyenek.|

## <a name="azure-storage-firewall"></a>Azure Storage-tűzfal

Azure Media Services nem támogatja a Storage-fiókok használatát az Azure Storage-tűzfallal vagy a [magánhálózati végpontokkal](../../storage/common/storage-network-security.md) .

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan csatolhat Storage-fiókot a Media Services-fiókjához, tekintse meg a [fiók létrehozása](./create-account-howto.md)című témakört.
