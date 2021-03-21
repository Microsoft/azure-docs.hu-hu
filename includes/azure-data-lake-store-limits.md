---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 7098f23e9b5b6f56fbbe761335afc65375aea680
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96509219"
---
**Azure Data Lake Storage Gen2** nem dedikált szolgáltatás-vagy tárolási fióktípus. Ez a big data elemzésekhez dedikált képességek legújabb kiadása.  Ezek a képességek egy általános célú v2-vagy BlockBlobStorage-fiókban érhetők el, és a fiók **hierarchikus névtér** funkciójának engedélyezésével szerezhetők be. A méretezési célokért tekintse meg ezeket a cikkeket. 

- [Méretezési célok a blob Storage](../articles/storage/blobs/scalability-targets.md#scale-targets-for-blob-storage)-hoz.
- [A standard Storage-fiókokra vonatkozó célkitűzések méretezése](../articles/storage/common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#scale-targets-for-standard-storage-accounts).

A **Azure Data Lake Storage Gen1** egy dedikált szolgáltatás. Ez egy nagyvállalati szintű Hyper-Scale adattár big data analitikus számítási feladatokhoz. A Data Lake Storage Gen1 használatával bármilyen méretű, típusú és feldolgozási sebességű adatot rögzíthet egyetlen helyen az operatív és a felderítő elemzéshez. A Data Lake Storage Gen1-fiókban tárolható adatmennyiségnek nincs korlátja.

| **Erőforrás** | **Korlátot** | **Megjegyzések** |
| --- | --- | --- |
| Data Lake Storage Gen1 fiókok maximális száma, előfizetések régiónként |10 | A korlát növelésének igényléséhez forduljon az ügyfélszolgálathoz. |
| Hozzáférési ACL-ek maximális száma fájl vagy mappa alapján |32 | Ez egy rögzített korlát. Csoportok használata a kevesebb bejegyzéssel való hozzáférés kezelésére. |
| Alapértelmezett ACL-ek maximális száma fájl vagy mappa alapján |32 | Ez egy rögzített korlát. Csoportok használata a kevesebb bejegyzéssel való hozzáférés kezelésére. |