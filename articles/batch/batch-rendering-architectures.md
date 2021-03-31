---
title: Az Azure megjelenítési referenciájának architektúrái
description: A Azure Batch és más Azure-szolgáltatások használatára szolgáló architektúrák a helyszíni Render farmok kibővítéséhez a felhőbe való Betöréssel
ms.date: 02/07/2019
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 784fd89c3dea88e25a2058713897c7a655c8a3af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83726519"
---
# <a name="reference-architectures-for-azure-rendering"></a>Az Azure rendering architektúrái

Ez a cikk magas szintű architektúrát ábrázoló ábrákat mutat be a helyszíni rendering-farmok Azure-ba történő kiterjesztéséhez, vagy "burst". A példák az Azure számítási, hálózati és tárolási szolgáltatásainak különböző lehetőségeit mutatják be.

## <a name="hybrid-with-nfs-or-cfs"></a>Hibrid NFS vagy CFS

Az alábbi ábrán egy hibrid forgatókönyv látható, amely a következő Azure-szolgáltatásokat tartalmazza:

* **Számítás** – Azure batch készlet vagy virtuálisgép-méretezési csoport.

* Helyszíni **hálózat** : Azure EXPRESSROUTE vagy VPN. Azure: Azure-VNet.

* **Storage** – bemeneti és kimeneti fájlok: az NFS vagy a CFS az Azure-beli virtuális gépek használatával, Azure file Sync vagy RSync segítségével szinkronizálva a helyszíni tárolóval. Másik lehetőség: a avere a helyszíni NAS-eszközökről az NFS-en keresztül bemeneti vagy kimeneti fájlokat vFXT.

  ![Cloud burst – hibrid NFS vagy CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Hibrid és Blobfuse

Az alábbi ábrán egy hibrid forgatókönyv látható, amely a következő Azure-szolgáltatásokat tartalmazza:

* **Számítás** – Azure batch készlet vagy virtuálisgép-méretezési csoport.

* Helyszíni **hálózat** : Azure EXPRESSROUTE vagy VPN. Azure: Azure-VNet.

* **Storage** – bemeneti és kimeneti fájlok: blob Storage, amely a számítási erőforrásokhoz van csatlakoztatva az Azure Blobfuse használatával.

  ![Cloud burst – hibrid és Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Hibrid számítás és tárolás

Az alábbi ábrán egy teljes mértékben csatlakoztatott hibrid forgatókönyv látható a számítási és a tárolási kapacitáshoz, és a következő Azure-szolgáltatásokat tartalmazza:

* **Számítás** – Azure batch készlet vagy virtuálisgép-méretezési csoport.

* Helyszíni **hálózat** : Azure EXPRESSROUTE vagy VPN. Azure: Azure-VNet.

* **Tárolás** – létesítmények közötti: avere vFXT. Helyszíni fájlok opcionális archiválása Azure Data Box használatával a blob Storage-ba vagy a helyszíni avere FXT a NAS-gyorsításhoz.

  ![Cloud burst – hibrid számítási és tárolási kapacitás](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Következő lépések

* További információ a [Render managerek](batch-rendering-render-managers.md) Azure batch használatával történő használatáról.

* További információ az [Azure-ban történő renderelés](batch-rendering-service.md)lehetőségeiről.