---
title: Rugalmasság & magas rendelkezésre állás
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy a magas rendelkezésre állású konfiguráció segítségével hogyan teheti hatékonyabbá a Azure Machine Learning erőforrásokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/16/2020
ms.openlocfilehash: 7a1a63893e6e2988fc5f21e84f21c74315d856b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93325474"
---
# <a name="increase-azure-machine-learning-resiliency"></a>Növelje Azure Machine Learning rugalmasságot



Ebből a cikkből megtudhatja, hogyan teheti hatékonyabbá a Microsoft Azure Machine Learning erőforrásait magas rendelkezésre állású konfigurációk használatával. A magas rendelkezésre állástól függően a Azure Machine Learning Azure-szolgáltatásokat is konfigurálhatja. Ez a cikk a magas rendelkezésre álláshoz konfigurálható szolgáltatásokat azonosítja, és az erőforrások konfigurálásával kapcsolatos további információkra mutató hivatkozásokat tartalmaz.

> [!NOTE]
> Azure Machine Learning önmagában nem biztosít vész-helyreállítási lehetőséget.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Az Azure-szolgáltatások megismerése Azure Machine Learning

Azure Machine Learning több Azure-szolgáltatástól függ, és több réteget is tartalmaz. Ezen szolgáltatások némelyike az Ön (ügyfél) előfizetésében van kiépítve. Ön felelős a szolgáltatások magas rendelkezésre állásának konfigurálásához. Más szolgáltatások létrehozása Microsoft-előfizetésekben és a Microsoft által felügyelt. 

Az Azure-szolgáltatások a következők:

* **Azure Machine learning infrastruktúra**: a Azure Machine learning munkaterület Microsoft által felügyelt környezete.

* **Társított erőforrások**: az előfizetésben kiépített erőforrások Azure Machine learning munkaterület létrehozása során. Ezek az erőforrások például az Azure Storage, a Azure Key Vault, a Azure Container Registry és a Application Insights. Ezen erőforrások magas rendelkezésre állási beállításainak konfigurálását végzi.
  * Az alapértelmezett tároló olyan adatokat tartalmaz, mint például a modell, a betanítási adat és az adatkészlet.
  * Key Vault rendelkezik az Azure Storage, a Container Registry és az adattárak hitelesítő adataival.
  * Container Registry tartalmaz egy Docker-rendszerképet a képzési és következtetési környezetekhez.
  * A Application Insights a Azure Machine Learning figyelésére szolgál.

* **Számítási erőforrások**: a munkaterület telepítése után létrehozott erőforrások. Létrehozhat például egy számítási példányt vagy számítási fürtöt egy Machine Learning modell betanításához.
  * Számítási példány és számítási fürt: a Microsoft által felügyelt modell-fejlesztési környezetek.
  * Egyéb erőforrások: a Microsoft számítástechnikai erőforrásai, amelyeket Azure Machine Learninghoz csatolhat, például az Azure Kubernetes Service (ak), a Azure Databricks, a Azure Container Instances és az Azure HDInsight. Ezen erőforrások magas rendelkezésre állási beállításainak konfigurálását végzi.

* **További adattárak**: Azure Machine learning további adattárakat is csatlakoztathat, például az Azure Storage-t, a Azure Data Lake Storaget és a Azure SQL Database a betanítási információkhoz.  Ezek az adattárak az előfizetésen belül vannak kiépítve. A magas rendelkezésre állási beállítások konfigurálását végzi.

Az alábbi táblázat a Microsoft által felügyelt Azure-szolgáltatásokat mutatja be, amelyeket Ön kezel, és amelyek alapértelmezés szerint nagyon elérhetők.

| Szolgáltatás | Felügyeli | Magas rendelkezésre állás alapértelmezés szerint |
| ----- | ----- | ----- |
| **Azure Machine Learning infrastruktúra** | Microsoft | |
| **Társított erőforrások** |
| Azure Storage | Ön | |
| Key Vault | Ön | ✓ |
| Container Registry | Ön | |
| Application Insights | Ön | NA |
| **Számítási erőforrások** |
| Számítási példány | Microsoft |  |
| Számítási fürt | Microsoft |  |
| Egyéb számítási erőforrások, például AK, <br>Azure Databricks, Container Instances, HDInsight | Ön |  |
| **További adattárak** , például Azure Storage, SQL Database,<br> Azure Database for PostgreSQL, Azure Database for MySQL, <br>Azure Databricks fájlrendszer | Ön | |

A cikk további része azokat a műveleteket ismerteti, amelyeket el kell végeznie ezeknek a szolgáltatásoknak a nagyfokú rendelkezésre állásához.

## <a name="associated-resources"></a>Társított erőforrások

> [!IMPORTANT]
> A Azure Machine Learning nem támogatja az alapértelmezett Storage-fiók feladatátvételét a Geo-redundáns tárolással (GRS), a Geo-zóna redundáns tárolásával (GZRS), az olvasási hozzáférésű geo-redundáns tárolással (RA-GRS) vagy az olvasási hozzáférésű geo-Zone-redundáns tárolással (RA-GZRS).

Győződjön meg arról, hogy az egyes erőforrások magas rendelkezésre állási beállításait az alábbi dokumentációra hivatkozva konfigurálja:

* **Azure Storage**: a magas rendelkezésre állási beállítások konfigurálásához tekintse meg az [Azure Storage redundancia](../storage/common/storage-redundancy.md)című témakört.
* **Key Vault**: a Key Vault alapértelmezés szerint magas rendelkezésre állást biztosít, és nincs szükség felhasználói beavatkozásra.  Lásd: [Azure Key Vault rendelkezésre állás és redundancia](../key-vault/general/disaster-recovery-guidance.md).
* **Container Registry**: válassza a prémium szintű beállításjegyzék lehetőséget a Geo-replikációhoz. Lásd: [geo-replikáció a Azure Container Registryban](../container-registry/container-registry-geo-replication.md).
* **Application Insights**: a Application Insights nem biztosít magas rendelkezésre állási beállításokat. Az adatmegőrzési időszak és a részletek módosításához tekintse [meg a Application Insights adatgyűjtési,-megőrzési és-tárolási adatokat](../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Számítási erőforrások

Győződjön meg arról, hogy az egyes erőforrások magas rendelkezésre állási beállításait az alábbi dokumentációra hivatkozva konfigurálja:

* **Azure Kubernetes szolgáltatás**: az [Azure Kubernetes szolgáltatásban (ak) az üzletmenet folytonosságával és a vész-helyreállítással kapcsolatos ajánlott eljárások](../aks/operator-best-practices-multi-region.md) , valamint a [rendelkezésre állási zónákat használó Azure Kubernetes Service (ak)-fürt létrehozása](../aks/availability-zones.md)című témakörben talál. Ha az AK-fürt a Azure Machine Learning Studio, az SDK vagy a CLI használatával lett létrehozva, a régiók közötti magas rendelkezésre állás nem támogatott.
* **Azure Databricks**: tekintse [meg a Azure Databricks fürtökre vonatkozó regionális vész-helyreállítást](/azure/databricks/scenarios/howto-regional-disaster-recovery).
* **Container instances**: a Orchestrator felelős a feladatátvételért. Lásd: [Azure Container instances és Container-](../container-instances/container-instances-orchestrator-relationship.md)szervezők.
* **HDInsight**: Tekintse meg [Az Azure HDInsight által támogatott magas rendelkezésre állású szolgáltatásokat](../hdinsight/hdinsight-high-availability-components.md).

## <a name="additional-data-stores"></a>További adattárak

Győződjön meg arról, hogy az egyes erőforrások magas rendelkezésre állási beállításait az alábbi dokumentációra hivatkozva konfigurálja:

* **Azure Blob Container/Azure Files/Data Lake Storage Gen2**: ugyanaz, mint az alapértelmezett tároló.
* **Data Lake Storage Gen1**: tekintse [meg a magas rendelkezésre állást és a vész-helyreállítási útmutatót a Data Lake Storage Gen1hoz](../data-lake-store/data-lake-store-disaster-recovery-guidance.md).
* **SQL Database**: tekintse [meg a Azure SQL Database és az SQL felügyelt példány magas rendelkezésre állását](../azure-sql/database/high-availability-sla.md).
* **Azure Database for PostgreSQL**: tekintse [meg a Azure Database for PostgreSQL-egyetlen kiszolgáló magas rendelkezésre állási fogalmait](../postgresql/concepts-high-availability.md).
* **Azure Database for MySQL**: tekintse [meg az üzletmenet folytonosságának megismerése Azure Database for MySQL-ben](../mysql/concepts-business-continuity.md)című témakört.
* **Azure Databricks fájlrendszer**: tekintse [meg a Azure Databricks fürtökre vonatkozó regionális vész-helyreállítást](/azure/databricks/scenarios/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Ha a felhasználó által felügyelt kulcsot adja meg egy Azure Machine Learning munkaterület telepítéséhez, Azure Cosmos DB az előfizetésen belül is kiépíthető. Ebben az esetben Ön a felelős a magas rendelkezésre állási beállítások konfigurálásához. Tekintse meg [a Azure Cosmos db magas rendelkezésre állását](../cosmos-db/high-availability.md).

## <a name="next-steps"></a>Következő lépések

Ha a magas rendelkezésre állási beállításokkal szeretné telepíteni a Azure Machine Learning a kapcsolódó erőforrásokkal, használjon egy [Azure Resource Manager sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).