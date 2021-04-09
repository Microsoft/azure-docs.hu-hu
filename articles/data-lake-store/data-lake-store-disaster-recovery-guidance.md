---
title: Vész-helyreállítási útmutató a Azure Data Lake Storage Gen1hoz | Microsoft Docs
description: Megtudhatja, hogyan teheti meg az adatok az egész régióra kiterjedő leállások vagy véletlen törlések további védelme érdekében a Azure Data Lake Storage Gen1 helyileg redundáns tárolásán túl.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: 48136f8d9172c3674e849e24efca4ae5070f83ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109119"
---
# <a name="high-availability-and-disaster-recovery-guidance-for-data-lake-storage-gen1"></a>Magas rendelkezésre állású és vész-helyreállítási útmutatás a Data Lake Storage Gen1

A Data Lake Storage Gen1 helyileg redundáns tárolást (LRS) biztosít. Ezért a Data Lake Storage Gen1-fiókban lévő adatai az automatizált replikák segítségével rugalmasan megtalálhatók az adatközponton belüli átmeneti hardveres hibák esetén. Ez biztosítja a tartósságot és a magas rendelkezésre állást, és megfelel a Data Lake Storage Gen1 SLA-nak. Ez a cikk útmutatást nyújt arra vonatkozóan, hogy miként lehet további védelemmel ellátni az adatait az egész régióra kiterjedő vagy véletlen törléssel.

## <a name="disaster-recovery-guidance"></a>Vészhelyreállítási útmutató

Fontos, hogy előkészítse a vész-helyreállítási tervet. Tekintse át az ebben a cikkben található információkat, valamint ezeket a további forrásokat, hogy segítsen a saját terv létrehozásában.

* [Vészhelyreállítás és magas szintű rendelkezésre állás az Azure-alkalmazásokhoz](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Műszaki útmutató az Azure rugalmasságáról](/azure/architecture/framework/resiliency/overview)

### <a name="best-practice-recommendations"></a>Ajánlott eljárások

Azt javasoljuk, hogy a kritikus fontosságú adatait egy másik régióban lévő másik Data Lake Storage Gen1-fiókba másolja, és a vész-helyreállítási terv igényeihez igazított gyakorisággal. Többféle módon másolhatók az adatmásolások, például a [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), a [Azure PowerShell](data-lake-store-get-started-powershell.md)vagy a [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Az Azure Data Factory hasznos szolgáltatás az adatáthelyezési folyamatok rendszeres létrehozásához és üzembe helyezéséhez.

Ha regionális leállás következik be, akkor az adatai abban a régióban érhetők el, ahol az adatait másolták. A [Azure Service Health irányítópultját](https://azure.microsoft.com/status/) figyelheti az Azure-szolgáltatások állapotának az egész világon való meghatározásához.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Adatsérülés vagy véletlen törlés helyreállításával kapcsolatos útmutató

Míg Data Lake Storage Gen1 adatrugalmasságot biztosít az automatizált replikák használatával, ez nem akadályozza meg az alkalmazás (vagy a fejlesztők/felhasználók) számára az adatok sérülését vagy véletlen törlését.

A véletlen törlés megelőzése érdekében javasoljuk, hogy először állítsa be a Data Lake Storage Gen1 fiókjának megfelelő hozzáférési házirendeket. Ez magában foglalja az [Azure-erőforrás-zárolások](../azure-resource-manager/management/lock-resources.md) alkalmazásával a fontos erőforrások zárolását, valamint a fiók és a fájl szintű hozzáférés-vezérlés alkalmazását az elérhető [Data Lake Storage Gen1 biztonsági funkciók](data-lake-store-security-overview.md)használatával. Javasoljuk továbbá, hogy a kritikus fontosságú adatairól rendszeresen készítsen másolatot a [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) vagy [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) egy másik Data Lake Storage Gen1 fiók, mappa vagy Azure-előfizetés használatával. Ezzel helyreállíthatók az adatsérülések vagy -törlések. Az Azure Data Factory hasznos szolgáltatás az adatáthelyezési folyamatok rendszeres létrehozásához és üzembe helyezéséhez.

A [diagnosztikai naplózást](data-lake-store-diagnostic-logs.md) is engedélyezheti egy Data Lake Storage Gen1-fiókhoz az adathozzáférés naplózási nyomvonalának gyűjtéséhez. A naplózási információk arról tájékoztatnak, hogy ki vagy módosított egy fájlt.

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés a Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)