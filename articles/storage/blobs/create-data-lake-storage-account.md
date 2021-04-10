---
title: Hozzon létre egy Storage-fiókot Azure Data Lake Storage Gen2
description: Megtudhatja, hogyan hozhat létre Azure Data Lake Storage Gen2 használatával használható Storage-fiókot.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 712f1dc0679ee49791831e782fb68c39a757870a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624337"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2hez használandó Storage-fiók létrehozása

Data Lake Storage Gen2 képességek használatához hozzon létre egy hierarchikus névteret tartalmazó Storage-fiókot.

## <a name="choose-a-storage-account-type"></a>Válassza ki a Storage-fiók típusát

A Data Lake Storage képességek a következő típusú Storage-fiókok esetén támogatottak:

- Általános célú v2
- BlockBlobStorage

További információ a közöttük való választásról: a [Storage-fiók áttekintése](../common/storage-account-overview.md).

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>Hierarchikus névtérrel rendelkező Storage-fiók létrehozása

Hozzon létre egy [általános célú v2-fiókot](../common/storage-account-create.md) vagy egy [BlockBlobStorage](storage-blob-create-account-block-blob.md) -fiókot, amelyen engedélyezve van a **hierarchikus névtér** beállítása.

A fiók létrehozásakor Data Lake Storage képességek zárolásának feloldásához engedélyezze a **hierarchikus névtér** beállítást a **Storage-fiók létrehozása** lap **speciális** lapján. A fiók létrehozásakor engedélyeznie kell ezt a beállítást. Ezt követően nem engedélyezheti.

A következő képen a **Storage-fiók létrehozása** lapon látható ez a beállítás.

> [!div class="mx-imgBorder"]
> ![Hierarchikus névtér beállítása](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Ha van olyan meglévő Storage-fiókja, amelyet Data Lake Storagehoz kíván használni, és a hierarchikus névtér beállítás le van tiltva, akkor át kell telepítenie az új Storage-fiókba, amelyen engedélyezve van a beállítás.

> [!NOTE]
> **Az adatvédelem** és a **hierarchikus névtér** nem engedélyezhető egyszerre.

## <a name="next-steps"></a>Következő lépések

- [Tárfiókok áttekintése](../common/storage-account-overview.md)
- [A Azure Data Lake Storage Gen2 használata big data követelményekhez](data-lake-storage-data-scenarios.md)
- [Hozzáférés-vezérlés a 2. generációs Azure Data Lake Storage-ben](data-lake-storage-access-control.md)
