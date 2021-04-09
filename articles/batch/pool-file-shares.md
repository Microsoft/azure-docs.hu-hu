---
title: Azure-fájlmegosztás Azure Batch készletekhez
description: Azure Files-megosztás csatlakoztatása a számítási csomópontokból egy Linux-vagy Windows-készletben a Azure Batchban.
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: e5682e7ba853973592c3a650a06ce72615cec7b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735494"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Azure-fájlmegosztás használata batch-készlettel

A [Azure Files](../storage/files/storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást biztosít a felhőben, amely a Server Message Block (SMB) protokollon keresztül érhető el. Ez a cikk a készlet számítási csomópontjain található Azure-fájlmegosztás csatlakoztatására és használatára vonatkozó példákkal kapcsolatos információkat és kódokat tartalmaz.

## <a name="considerations-for-use-with-batch"></a>A Batch szolgáltatással való használat szempontjai

* Érdemes lehet egy Azure-fájlmegosztást használni, ha olyan készlettel rendelkezik, amely viszonylag alacsony számú párhuzamos feladatot futtat, ha nem prémium szintű Azure Filest használ. Tekintse át a [teljesítmény-és méretezési célokat](../storage/files/storage-files-scale-targets.md) annak megállapításához, hogy a Azure Files (amely egy Azure Storage-fiókot használ) a várt készlet mérete és az adategységek száma alapján kell-e használni. 

* Az Azure-fájlmegosztás [költséghatékony](https://azure.microsoft.com/pricing/details/storage/files/) , és egy másik régióba történő adatreplikálással is konfigurálható, így globálisan redundánsak. 

* Egy Azure-fájlmegosztást egyszerre csatlakoztathat egy helyszíni számítógépről. Azonban ügyeljen arra, hogy tisztában legyen a [párhuzamosságok következményeivel](../storage/blobs/concurrency-manage.md) , különösen a REST API-k használata esetén.

* Tekintse meg az Azure-fájlmegosztás általános [tervezési szempontjait](../storage/files/storage-files-planning.md) is.


## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

[Hozzon létre egy fájlmegosztást](../storage/files/storage-how-to-create-file-share.md) egy olyan Storage-fiókban, amely a Batch-fiókjához van csatolva, vagy egy különálló Storage-fiókban.

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>Azure-fájlmegosztás csatlakoztatása batch-készlethez

Tekintse meg a [virtuális fájlrendszer batch-készleten való csatlakoztatásának](virtual-file-mount.md)dokumentációját.

## <a name="next-steps"></a>Következő lépések

* A Batch-adatok olvasására és írására vonatkozó egyéb beállításokért lásd: a [feladat és a feladat kimenetének](batch-task-output.md)megőrzése.
* Lásd még a [Batch-hajógyár](https://github.com/Azure/batch-shipyard) eszközkészletét, amely tartalmazza a [hajógyári recepteket](https://github.com/Azure/batch-shipyard/tree/master/recipes) a Batch-tároló munkaterhelések fájlrendszerének üzembe helyezéséhez.