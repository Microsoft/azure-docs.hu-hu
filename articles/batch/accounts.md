---
title: Batch-fiókok és Azure Storage-fiókok
description: Ismerkedjen meg Azure Batch-fiókokkal, és hogyan használják őket fejlesztési szempontból.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 20a2a28d0eaa2c7997ea93e66d07ecb99bf297a2
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791147"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>Batch-fiókok és Azure Storage-fiókok

Az Azure Batch fiók a Batch szolgáltatásban egyedileg azonosított entitás. A legtöbb batch-megoldás az [Azure Storage](../storage/index.yml) -t használja az erőforrás-fájlok és a kimeneti fájlok tárolására, így minden batch-fiók általában egy megfelelő Storage-fiókhoz van társítva.

## <a name="batch-accounts"></a>Batch-fiókok

Minden feldolgozás és erőforrás egy batch-fiókkal van társítva. Amikor az alkalmazás egy kérelmet továbbít a Batch szolgáltatás felé, a hitelesítést az Azure Batch-fiók, a fiók URL-címe és egy hozzáférési kulcs vagy Azure Active Directory-jogkivonat használatával hajtja végre a szolgáltatás.

Egyetlen batch-fiókban több batch-munkaterhelést is futtathat. A számítási feladatokat olyan batch-fiókok között is eloszthatja, amelyek ugyanabban az előfizetésben vannak, de különböző Azure-régiókban találhatók.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

Létrehozhat egy batch-fiókot a [Azure Portal](batch-account-create-portal.md) vagy programozott módon, például a [Batch Management .net-kódtár](batch-management-dotnet.md)használatával. Egy fiók létrehozásakor társíthatja azt egy Azure Storage-fiókhoz, a feladatokkal kapcsolatos bemeneti vagy kimeneti adatok, illetve alkalmazások tárolása céljából.

## <a name="azure-storage-accounts"></a>Azure Storage-fiókok

Az erőforrásfájlok és a kimeneti fájlok tárolására a legtöbb Batch-megoldás az Azure Storage-ot használja. Például a Batch-tevékenységek (beleértve a szabványos, az indítási, a feladat-előkészítési és a feladatkiadási tevékenységeket) általában olyan erőforrásfájlokat határoznak meg, amelyek egy tárfiókban találhatók. A Storage-fiókok a feldolgozott adatokat és a létrehozott kimeneti adatokat is tárolják.

A Batch az alábbi Azure Storage-fiókokat támogatja:

- Általános célú v2- (GPv2-) fiók
- Általános célú v1- (GPv1-) fiók
- Blob Storage-fiókok (jelenleg a virtuálisgép-konfigurációban lévő készletek esetén támogatott)

A Storage-fiókokkal kapcsolatos további információkért lásd: az [Azure Storage-fiók áttekintése](../storage/common/storage-account-overview.md).

A Batch-fiókhoz a Batch-fiók létrehozásakor vagy később is társíthat tárfiókot. Tárfiók kiválasztásakor vegye figyelembe a költségekre és teljesítményre vonatkozó követelményeket. A GPv2 és a Blob-tárfiók például magasabb [kapacitási és méretezhetőségi korlátokat](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) támogat a GPv1-hez képest. (Forduljon az Azure támogatási szolgálatához, hogy növelje a tárolási korlátot.) Ezek a Fiókbeállítások javíthatják azon batch-megoldások teljesítményét, amelyek nagy mennyiségű párhuzamos feladatot tartalmaznak a Storage-fiókba való beolvasás vagy a szolgáltatásba való írás során.

## <a name="next-steps"></a>További lépések

- További információ a [csomópontokról és a készletekről](nodes-and-pools.md).
- Megtudhatja, hogyan hozhat létre batch-fiókot a [Azure Portal](batch-account-create-portal.md)használatával.
