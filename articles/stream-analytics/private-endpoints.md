---
title: Privát végpontok létrehozása és törlése egy Azure Stream Analytics-fürtben
description: Ismerje meg, hogyan felügyelheti a privát végpontokat egy Azure Stream Analytics-fürtben.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 36d26195e78c74ed7b976cfdd48e1e965bca872b
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249784"
---
# <a name="create-and-delete-private-endpoints-in-an-azure-stream-analytics-cluster"></a>Privát végpontok létrehozása és törlése egy Azure Stream Analytics-fürtben

A fürtön futó Azure Stream Analytics-feladatokat tűzfal vagy Azure virtuális hálózat (VNet) mögötti bemeneti és kimeneti erőforrásokhoz csatlakoztathatja. Először létrehozza egy erőforrás, például Azure Event Hub vagy Azure SQL-adatbázis privát végpontját a Stream Analytics-fürtben. Ezután hagyja jóvá a privát végpont kapcsolatát a bemenetről vagy kimenetről.

Miután jóváhagyta a kapcsolatot, a Stream Analytics-fürtön futó feladatok a privát végponton keresztül érhetik el az erőforrást. Ebből a cikkből megtudhatja, hogyan hozhat létre és törölhet privát végpontokat a Stream Analytics-fürtökön. A Azure SQL Databasehoz, az Azure Storage-hoz, az Azure Data Lake Storage Gen2hoz, az Azure Event Hubhez és a Azure Service Bushoz is létrehozhat privát végpontokat. A többi szolgáltatáshoz tartozó privát végpontok hamarosan megjelennek. 

## <a name="create-private-endpoint-in-stream-analytics-cluster"></a>Privát végpont létrehozása Stream Analytics fürtben

Ebből a szakaszból megtudhatja, hogyan hozhat létre privát végpontot egy Stream Analytics-fürtben.

1. A Azure Portal keresse meg és válassza ki a Stream Analytics-fürtöt.

1. A **Beállítások** területen válassza a **privát végpontok** lehetőséget.

1. Válassza a **privát végpont hozzáadása** lehetőséget, és adja meg az alábbi adatokat, és válassza ki azt az erőforrást, amelyet egy privát végponton keresztül biztonságosan szeretne elérni.

   |Beállítás|Érték|
   |---|---|
   |Név|Adja meg a privát végpont nevét. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet.|
   |Kapcsolati módszer|Válassza a **Kapcsolódás egy Azure-erőforráshoz a címtárban** lehetőséget.<br><br>Kiválaszthatja az egyik erőforrását, hogy biztonságosan kapcsolódjon a privát végponthoz, vagy más erőforrásokhoz is csatlakozhat, ha az Ön által megosztott erőforrás-azonosítót vagy aliast használ.|
   |Előfizetés|Válassza ki előfizetését.|
   |Erőforrás típusa|Válassza ki az erőforráshoz [hozzárendelni kívánt erőforrás típusát](../private-link/private-endpoint-overview.md#private-link-resource).|
   |Erőforrás|Válassza ki azt az erőforrást, amelyhez csatlakozni szeretne a privát végpont használatával.|
   |Cél alerőforrás|A fent kiválasztott erőforrás alerőforrásának típusa, amelyet a privát végpont elérhet.|

   ![A privát végpontok létrehozásának élménye](./media/private-endpoints/create-private-endpoint.png)

1. Hagyja jóvá a hozzáférést a cél erőforrásból. Ha például az előző lépésben egy Azure SQL Database-példányhoz hozott létre egy privát végpontot, lépjen a SQL Database példányra, és tekintse meg a függőben lévő, jóvá nem hagyott kapcsolatokat. Eltarthat néhány percig, amíg a kapcsolódási kérelem megjelennek.

    ![privát végpont jóváhagyása](./media/private-endpoints/approve-private-endpoint.png)

1. Visszatérhet a Stream Analytics-fürthöz, és megtekintheti, hogy az állapot változása függőben van-e a függőben lévő **ügyfél-jóváhagyásról** a **függőben lévő DNS-beállításra** , hogy a **Befejezés**

## <a name="delete-a-private-endpoint-in-a-stream-analytics-cluster"></a>Privát végpont törlése Stream Analytics fürtben

1. A Azure Portal keresse meg és válassza ki a Stream Analytics-fürtöt.

1. A **Beállítások** területen válassza a **privát végpontok** lehetőséget.

1. Válassza ki a törölni kívánt privát végpontot, és válassza a **Törlés** lehetőséget.

   ![privát végpont törlése](./media/private-endpoints/delete-private-endpoint.png)

## <a name="next-steps"></a>Következő lépések

Most már áttekintheti, hogyan kezelhetők a Azure Stream Analytics fürtben található privát végpontok. Ezután megtudhatja, hogyan méretezheti a fürtöket, és hogyan futtathat feladatokat a fürtben:

* [Azure Stream Analytics-fürt méretezése](scale-cluster.md)
* [Stream Analytics feladatok kezelése Stream Analytics-fürtben](manage-jobs-cluster.md)
