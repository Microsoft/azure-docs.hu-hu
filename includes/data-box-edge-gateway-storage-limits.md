---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 87c28b906b1f810857fee86ba8a94a0834e1de68
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025744"
---
Ez a szakasz az Azure Storage szolgáltatásra vonatkozó korlátozásokat, valamint a Azure Files, az Azure Block blobok és az Azure Page Blobok számára szükséges elnevezési konvenciókat ismerteti az Azure Stack Edge/Data Box Gateway szolgáltatás esetében. Alaposan tekintse át a tárolási korlátokat, és kövesse az összes javaslatot.

Az Azure Storage szolgáltatás korlátaival és a megosztások, tárolók és fájlok elnevezésére vonatkozó ajánlott eljárásokkal kapcsolatos legfrissebb információkért keresse fel a következőt:

- [Tárolók elnevezése és hivatkozása](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Megosztások elnevezése és hivatkozása](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blobok és lapok blob-konvencióinak letiltása](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Ha vannak olyan fájlok vagy könyvtárak, amelyek túllépik az Azure Storage szolgáltatás korlátait, vagy nem felelnek meg az Azure Files/Blob elnevezési konvencióinak, akkor ezeket a fájlokat vagy könyvtárakat a rendszer nem tölti be az Azure Storage-ba az Azure Stack Edge/Data Box Gateway szolgáltatás használatával.