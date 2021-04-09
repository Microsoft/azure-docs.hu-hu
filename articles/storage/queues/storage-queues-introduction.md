---
title: Az Azure Queue Storage bemutatása – Azure Storage
description: Tekintse meg az Azure Queue Storage egy nagy mennyiségű üzenet tárolására szolgáló szolgáltatást. A Queue Storage szolgáltatás URL-formátumot, Storage-fiókot, üzenetsor-t és üzenetet tartalmaz.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 03/18/2020
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8c5c97fbb72934dd99ec784ccf8e08eec059c31b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590579"
---
# <a name="what-is-azure-queue-storage"></a>Mi az Azure Queue Storage?

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. A HTTP-vagy HTTPS-t használó hitelesített hívásokon keresztül érheti el az üzeneteket a világ bármely pontján. Egy üzenetsor-üzenet akár 64 KB méretű is lehet. Egy üzenetsor akár több millió üzenetet is tartalmazhat, akár egy Storage-fiók teljes kapacitási korlátját. A várólistákat általában arra használják, hogy egy várakozó munkafolyamatot hozzon létre aszinkron feldolgozásra.

## <a name="queue-storage-concepts"></a>Queue Storage fogalmak

Queue Storage a következő összetevőket tartalmazza:

![A Storage-fiók, a várólisták és az üzenetek közötti kapcsolatot bemutató diagram.](./media/storage-queues-introduction/queue1.png)

- **URL-formátum:** Az üzenetsorok a következő URL-formátummal érhetők el:

  `https://<storage account>.queue.core.windows.net/<queue>`

  Az ábra egyik üzenetsora a következő URL-címmel érhető el:

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Storage-fiók:** Az Azure Storage-hoz való összes hozzáférés egy Storage-fiókon keresztül történik. A Storage-fiók kapacitásával kapcsolatos további információkért lásd [a szabványos Storage-fiókok skálázhatósági és teljesítménybeli céljait](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)ismertető témakört.

- **Üzenetsor:** Az üzenetsorok üzenetek készleteit tartalmazzák. A **várólista nevének csak kisbetűsnek kell** lennie. További információ a várólisták elnevezéséről: [várólisták és metaadatok elnevezése](/rest/api/storageservices/naming-queues-and-metadata).

- **Üzenet:** Egy legfeljebb 64 KB méretű, tetszőleges méretű üzenet. Az 2017-07-29-es verzió előtt az engedélyezett maximális élettartam hét nap. A 2017-07-29-es vagy újabb verzió esetén a maximális élettartam lehet bármilyen pozitív szám, vagy-1, amely azt jelzi, hogy az üzenet nem jár le. Ha a paraméter nincs megadva, az alapértelmezett élettartam hét nap.

## <a name="next-steps"></a>Következő lépések

- [Tárfiók létrehozása](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [A Queue Storage használatának első lépései a .NET használatával](storage-dotnet-how-to-use-queues.md)
- [A Queue Storage használatának első lépései a Java használatával](storage-java-how-to-use-queue-storage.md)
- [A Queue Storage használatának első lépései a Python használatával](storage-python-how-to-use-queue-storage.md)
- [Ismerkedés a Queue Storage használatával Node.js](storage-nodejs-how-to-use-queues.md)
