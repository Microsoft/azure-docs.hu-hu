---
title: Stream Analytics feladatok összekötése az erőforrásokkal egy Azure-Virtual Network (VNET)
description: Ez a cikk azt ismerteti, hogyan lehet egy Azure Stream Analytics feladatot összekötni egy VNET található erőforrásokkal.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: a2d26689a3036f05d7c8e7f417fbbb447402aedc
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/24/2020
ms.locfileid: "97762860"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>Stream Analytics feladatok összekötése az erőforrásokkal egy Azure-Virtual Network (VNet)

A Stream Analytics feladatok a kimenő kapcsolatokat a bemeneti és kimeneti Azure-erőforrásokkal valós időben dolgozzák fel, és eredményeket hoznak létre. Ezek a bemeneti és kimeneti erőforrások (például az Azure Event Hubs és a Azure SQL Database) egy Azure tűzfal vagy egy Azure-Virtual Network (VNet) mögött lehetnek. Stream Analytics szolgáltatás olyan hálózatokból működik, amelyekről nem lehet közvetlenül belefoglalni a hálózati szabályokba.

Az ilyen helyzetekben azonban kétféleképpen lehet biztonságosan összekapcsolni a Stream Analytics feladatokat a bemeneti és kimeneti erőforrásaival.
* Privát végpontok használata Stream Analytics-fürtökben.
* Felügyelt identitás-hitelesítési mód használata a "megbízható szolgáltatások engedélyezése" hálózati beállítással párosítva.

A Stream Analytics-feladata nem fogad el bejövő kapcsolatokat.

## <a name="private-endpoints-in-stream-analytics-clusters"></a>Magánhálózati végpontok Stream Analytics-fürtökben.
[Stream Analytics-fürtök](https://docs.microsoft.com/azure/stream-analytics/cluster-overview) egyetlen bérlő dedikált számítási fürt, amelyen futtathatja stream Analytics feladatait. Felügyelt magánhálózati végpontokat hozhat létre a Stream Analytics-fürtben, amely lehetővé teszi a fürtön futó összes feladat számára, hogy biztonságos kimenő kapcsolatokat létesítsen a bemeneti és kimeneti erőforrásaihoz.

A privát végpontok létrehozása a Stream Analytics-fürtben [két lépésből álló művelet](https://docs.microsoft.com/azure/stream-analytics/private-endpoints). Ez a lehetőség közepes és nagy átviteli számítási feladatokhoz ideális, mivel a Stream Analytics-fürt minimális mérete 36 SUs (bár a 36 SUs a különböző előfizetésekben és környezetekben, például fejlesztési, tesztelési és éles környezetben) eltérő feladatok között is megosztható.

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>Felügyelt identitásos hitelesítés a "megbízható szolgáltatások engedélyezése" konfigurációval
Egyes Azure-szolgáltatások **lehetővé teszik a megbízható Microsoft-szolgáltatások** hálózatkezelési beállítását, amely lehetővé teszi, hogy a stream Analytics feladatok erős hitelesítéssel biztonságosan csatlakozzanak az erőforráshoz. Ez a beállítás lehetővé teszi a feladatok a bemeneti és kimeneti erőforrásokhoz való összekapcsolását anélkül, hogy Stream Analytics fürtöt és privát végpontokat kellene megadnia. Ha úgy konfigurálja a feladatot, hogy ezt a technikát használja, egy kétlépéses művelet:
* Felügyelt identitás-hitelesítési mód használata a Stream Analytics-feladatban megadott bemeneti vagy kimeneti beállítások konfigurálásához.
* Az adott Stream Analytics feladatok explicit módon hozzáférhetnek a cél erőforrásaihoz egy Azure-szerepkör hozzárendelésével a feladat rendszerhez rendelt felügyelt identitásához. 

A **megbízható Microsoft-szolgáltatások engedélyezésének** engedélyezése semmilyen feladathoz nem biztosít takaró-hozzáférést. Így teljes körűen szabályozhatja, hogy mely konkrét Stream Analytics feladatok férhetnek hozzá az erőforrásaihoz. 

A feladatok az alábbi Azure-szolgáltatásokhoz kapcsolódhatnak a következő módszer használatával:
1. [Blob Storage vagy Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity) – a feladatok Storage-fiókja, a streaming bemenete vagy kimenete lehet.
2. [Azure Event Hubs](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity) – a feladatok folyamatos átviteli bemenete vagy kimenete lehet.

Ha a feladatoknak más bemeneti vagy kimeneti típusokhoz kell csatlakozniuk, az egyetlen lehetőség, hogy Stream Analytics-fürtökben használja a magánhálózati végpontokat.

## <a name="next-steps"></a>További lépések

* [Privát végpontok létrehozása és eltávolítása Stream Analytics-fürtökben](https://docs.microsoft.com/azure/stream-analytics/private-endpoints)
* [Kapcsolódás Event Hubshoz a VNet felügyelt Identitásos hitelesítés használatával](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity)
* [Kapcsolódás blob Storage-hoz és ADLS Gen2 a VNet felügyelt Identitásos hitelesítés használatával](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity)
