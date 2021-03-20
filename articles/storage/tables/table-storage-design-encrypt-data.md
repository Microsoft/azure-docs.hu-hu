---
title: Azure Storage-tábla adatai titkosítása | Microsoft Docs
description: Tudnivalók az Azure Storage-beli táblázatos adattitkosításról. A .NET Azure Storage ügyféloldali kódtára lehetővé teszi a karakterlánc-entitások titkosítását a INSERT és a Replace műveletekhez.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.subservice: tables
ms.openlocfilehash: b921be718bfeb5eb95d4a802fb4d2a8cdd0946c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88236777"
---
# <a name="encrypt-table-data"></a>Tábla adatai titkosítása
A .NET Azure Storage ügyféloldali kódtára támogatja a karakterlánc-entitások tulajdonságainak az INSERT és a Replace műveletekhez való titkosítását. A titkosított karakterláncok a szolgáltatásban bináris tulajdonságokként tárolódnak, és a visszafejtés után vissza lesznek konvertálva karakterlánccá.    

A táblák esetében a titkosítási házirenden kívül a felhasználóknak meg kell adniuk a titkosítani kívánt tulajdonságokat. Ezt megteheti egy [EncryptProperty] attribútum megadásával (a TableEntity-ből származó POCO-entitások esetén) vagy a kérési beállításokban található titkosítási feloldóval. A titkosítási feloldó egy olyan delegált, amely egy partíciós kulcsot, egy sor kulcsot és egy tulajdonság nevét veszi át, és egy logikai értéket ad vissza, amely jelzi, hogy a tulajdonságot titkosítani kell-e. A titkosítás során az ügyféloldali kódtár ezt az információt használja annak eldöntéséhez, hogy a rendszer titkosítsa-e a tulajdonságot a drótba való írás során. A delegált emellett a tulajdonságok titkosítását is lehetővé teszi. (Ha például X, akkor titkosítsa az A tulajdonságot, máskülönben az A és B tulajdonságokat titkosítja.) Az entitások olvasása vagy lekérdezése során nem szükséges megadni ezeket az információkat.

## <a name="merge-support"></a>Egyesítés támogatása

Az egyesítés jelenleg nem támogatott. Mivel előfordulhat, hogy a tulajdonságok egy részhalmaza korábban egy másik kulccsal lett titkosítva, egyszerűen egyesítve az új tulajdonságokat, és a metaadatokat az adatvesztéssel frissíti. Az egyesítéshez szükség van további szolgáltatási hívásokra, hogy beolvassa a szolgáltatásból a már meglévő entitást, vagy egy új kulcsot használjon egy tulajdonságban, amelyek közül mindkettő nem alkalmas a teljesítményre.     

További információ a táblák adatainak titkosításáról: [ügyféloldali titkosítás és Azure Key Vault Microsoft Azure Storagehoz](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Következő lépések

- [Tábla kialakítási mintái](table-storage-design-patterns.md)
- [Kapcsolatok modellezése](table-storage-design-modeling.md)
- [Kapcsolatok modellezése](table-storage-design-modeling.md)
- [Tervezés adatmódosításhoz](table-storage-design-for-modification.md)
