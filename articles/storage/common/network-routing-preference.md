---
title: Hálózati útválasztási beállítások konfigurálása (előzetes verzió)
titleSuffix: Azure Storage
description: Konfigurálja az Azure Storage-fiók hálózati útválasztási beállításait (előzetes verzió) annak megadásához, hogy a hálózati forgalom hogyan legyen átirányítva a fiókjához az interneten keresztül.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: santoshc
ms.reviewer: tamram
ms.subservice: common
ms.openlocfilehash: 5b4a1b1f27dff059090d78e24a6a0eca0bbbf01f
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85514194"
---
# <a name="configure-network-routing-preference-for-azure-storage-preview"></a>Hálózati útválasztási beállítások konfigurálása az Azure Storage-hoz (előzetes verzió)

Megadhatja az Azure Storage-fiókhoz tartozó hálózati [útválasztási](../../virtual-network/routing-preference-overview.md) beállításokat (előzetes verzió) annak megadásához, hogy a hálózati forgalom hogyan legyen átirányítva a fiókjához az interneten keresztül. Alapértelmezés szerint az internetről érkező forgalmat a rendszer a [Microsoft globális hálózatán](../../networking/microsoft-global-network.md)keresztül továbbítja a Storage-fiók nyilvános végpontjának. Az Azure Storage további lehetőségeket biztosít annak konfigurálására, hogy a rendszer hogyan irányítsa át a forgalmat a Storage-fiókjába.

Az útválasztási beállítások konfigurálása lehetővé teszi, hogy rugalmasan optimalizálja a forgalmat a prémium szintű hálózati teljesítmény vagy a költséghatékonyság érdekében. Útválasztási beállítások konfigurálásakor megadhatja, hogy alapértelmezés szerint a rendszer hogyan irányítsa át a forgalmat a Storage-fiók nyilvános végpontjának. A Storage-fiókhoz adott útválasztási végpontokat is közzéteheti.

## <a name="microsoft-global-network-versus-internet-routing"></a>Microsoft Global Network és Internet-Útválasztás

Alapértelmezés szerint az Azure-környezeten kívüli ügyfelek a Microsoft globális hálózatán keresztül férnek hozzá a Storage-fiókhoz. A Microsoft globális hálózata alacsony késleltetésű útvonal-kiválasztásra van optimalizálva, és magas megbízhatósággal biztosítja a prémium szintű hálózati teljesítményt. A bejövő és a kimenő forgalmat egyaránt az ügyfélhez legközelebb eső jelenléti pont (POP) alapján irányítja a rendszer. Ez az alapértelmezett útválasztási konfiguráció biztosítja, hogy a Storage-fiók felé irányuló és onnan érkező forgalom a Microsoft globális hálózatán keresztül haladjon át a hálózati teljesítmény maximalizálása érdekében.

A Storage-fiók útválasztási konfigurációját módosíthatja úgy, hogy a bejövő és a kimenő forgalmat is átirányítsa az Azure-környezeten kívüli ügyfelek felé, a Storage-fiókhoz legközelebb lévő POP használatával. Ez az útvonal leszűkíti a forgalom továbbítását a Microsoft globális hálózatán keresztül, és a lehető leghamarabb átadja az átvitelt az INTERNETSZOLGÁLTATÓnak. Ha ezt az útválasztási konfigurációt használja, a hálózati költségek csökkennek.

Az alábbi ábra azt mutatja be, hogy az ügyfél és a Storage-fiók közötti forgalom hogyan áramlik az egyes útválasztási beállításokhoz:

![Az Azure Storage útválasztási beállításainak áttekintése](media/network-routing-preference/routing-options-diagram.png)

További információ az útválasztási beállításokról az Azure-ban: [Mi az útválasztási preferencia (előzetes verzió)?](../../virtual-network/routing-preference-overview.md).

## <a name="routing-configuration"></a>Útválasztási konfiguráció

A Microsoft globális hálózat és az internetes útválasztás lehetőség közül választhat alapértelmezett útválasztási előnyként a Storage-fiók nyilvános végpontja számára. Az alapértelmezett útválasztási beállítások az Azure-on kívüli ügyfelektől érkező összes forgalomra érvényesek, és a Azure Data Lake Storage Gen2, blob Storage, Azure Files és statikus webhelyek végpontját érintik. Az útválasztási beállítások konfigurálása az Azure-várólisták vagy az Azure-táblák esetében nem támogatott.

A Storage-fiókhoz adott útválasztási végpontokat is közzéteheti. Az útválasztási végpontok közzétételekor az Azure Storage új nyilvános végpontokat hoz létre a Storage-fiókhoz, amely a kívánt útvonalon továbbítja a forgalmat. Ez a rugalmasság lehetővé teszi, hogy egy adott útvonalon keresztül irányítsa a forgalmat a Storage-fiókra az alapértelmezett útválasztási beállítások módosítása nélkül.

Például a "StorageAccountA" internetes útvonal-specifikus végpontjának közzététele a következő végpontokat teszi közzé a Storage-fiókhoz:

| Tárolási szolgáltatás        | Útvonal-specifikus végpont                                  |
| :--------------------- | :------------------------------------------------------- |
| Blob szolgáltatás           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| Fájlszolgáltatások           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| Statikus webhelyek        | `StorageAccountA-internetrouting.web.core.windows.net`   |

Ha rendelkezik egy olvasási hozzáféréssel rendelkező geo-redundáns tárolóval (RA-GRS) vagy egy olvasási hozzáférésű földrajzi zónával redundáns tárolási (RA-GZRS) Storage-fiókkal, akkor a közzétételi útvonal-specifikus végpontok automatikusan létrehozza a megfelelő végpontokat a másodlagos régióban olvasási hozzáférés céljából.

| Tárolási szolgáltatás        | Útvonal-specifikus írásvédett másodlagos végpont                        |
| :--------------------- | :----------------------------------------------------------------- |
| Blob szolgáltatás           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| Fájlszolgáltatások           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| Statikus webhelyek        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

A közzétett Route-specifikus végpontok kapcsolatainak karakterláncai a [Azure Portal](https://portal.azure.com)használatával másolhatók. Ezek a kapcsolatok a meglévő Azure Storage SDK-kkal és API-kkal közös kulcsos hitelesítéshez használhatók.

## <a name="about-the-preview"></a>Az előzetes verzió ismertetése

Az Azure Storage-hoz készült útválasztási beállítások a következő régiókban érhetők el:

- Dél-Franciaország
- USA északi középső régiója
- USA nyugati középső régiója

A következő ismert problémák hatással vannak az Azure Storage-hoz készült útválasztási beállításokra:

- A Microsoft globális hálózathoz tartozó útvonal-specifikus végponthoz tartozó hozzáférési kérelmek sikertelenek, HTTP-hiba: 404 vagy azzal egyenértékű. A Microsoft globális hálózaton keresztüli útválasztás a várt módon működik, ha a nyilvános végpont alapértelmezett útválasztási beállításaként van beállítva.

## <a name="pricing-and-billing"></a>Árak és számlázás

A díjszabással és a számlázással kapcsolatos részletekért tekintse meg a [Mi az útválasztási preferencia (előzetes verzió)](../../virtual-network/routing-preference-overview.md#pricing)című témakör **díjszabását** .

## <a name="next-steps"></a>Következő lépések

- [Mi az útválasztási preferencia (előzetes verzió)?](../../virtual-network/routing-preference-overview.md)
- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](storage-network-security.md)
- [Biztonsági javaslatok a blob Storage-hoz](../blobs/security-recommendations.md)
