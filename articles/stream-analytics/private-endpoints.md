---
title: Felügyelt privát végpontok létrehozása és törlése egy Azure Stream Analytics-fürtben
description: Ismerje meg, hogyan felügyelheti a privát végpontokat egy Azure Stream Analytics-fürtben.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 9939130782594c03a497d98ce6cd9b33b28eadec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101718403"
---
# <a name="create-and-delete-managed-private-endpoints-in-an-azure-stream-analytics-cluster"></a>Felügyelt privát végpontok létrehozása és törlése egy Azure Stream Analytics-fürtben

A fürtön futó Azure Stream Analytics-feladatokat tűzfal vagy Azure virtuális hálózat (VNet) mögötti bemeneti és kimeneti erőforrásokhoz csatlakoztathatja. Először létre kell hoznia egy felügyelt magánhálózati végpontot egy erőforráshoz, például az Azure Event hub vagy a Azure SQL Database a Stream Analytics-fürtben. Ezután hagyja jóvá a privát végpont kapcsolatát a bemenetről vagy kimenetről.

Miután jóváhagyta a kapcsolatot, a Stream Analytics-fürtön futó feladatok a privát végponton keresztül érhetik el az erőforrást. Ebből a cikkből megtudhatja, hogyan hozhat létre és törölhet privát végpontokat a Stream Analytics-fürtökön. A Azure SQL Databasehoz, az Azure Storage-hoz, az Azure Data Lake Storage Gen2hoz, az Azure Event Hubhez és a Azure Service Bushoz is létrehozhat privát végpontokat. A többi szolgáltatáshoz tartozó privát végpontok hamarosan megjelennek. 

## <a name="create-managed-private-endpoint-in-stream-analytics-cluster"></a>Felügyelt privát végpont létrehozása Stream Analytics fürtben

Ebből a szakaszból megtudhatja, hogyan hozhat létre privát végpontot egy Stream Analytics-fürtben.

1. A Azure Portal keresse meg és válassza ki a Stream Analytics-fürtöt.

1. A **Beállítások** területen válassza a **felügyelt magánhálózati végpontok** lehetőséget.

1. Válassza az **új** lehetőséget, majd adja meg az alábbi adatokat, és válassza ki azt az erőforrást, amelyet biztonságosan szeretne elérni egy privát végponton keresztül.

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

## <a name="delete-a-managed-private-endpoint-in-a-stream-analytics-cluster"></a>Felügyelt privát végpont törlése egy Stream Analytics-fürtben

1. A Azure Portal keresse meg és válassza ki a Stream Analytics-fürtöt.

1. A **Beállítások** területen válassza a **felügyelt magánhálózati végpontok** lehetőséget.

1. Válassza ki a törölni kívánt privát végpontot, és válassza a **Törlés** lehetőséget.

   ![privát végpont törlése](./media/private-endpoints/delete-private-endpoint.png)

## <a name="next-steps"></a>Következő lépések

Most már áttekintheti, hogyan kezelhetők a Azure Stream Analytics fürtben található privát végpontok. Ezután megtudhatja, hogyan méretezheti a fürtöket, és hogyan futtathat feladatokat a fürtben:

* [Azure Stream Analytics-fürt méretezése](scale-cluster.md)
* [Stream Analytics feladatok kezelése Stream Analytics-fürtben](manage-jobs-cluster.md)
