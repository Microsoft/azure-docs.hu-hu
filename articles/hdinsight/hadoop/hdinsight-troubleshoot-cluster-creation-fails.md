---
title: Fürtök létrehozásával kapcsolatos hibák elhárítása az Azure HDInsight
description: Ismerje meg, hogyan oldhatja meg az Apache cluster-létrehozási problémákat az Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: e12b96883ae26b6c10e3622c35914ce498afca48
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944433"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Fürtök létrehozásával kapcsolatos hibák elhárítása az Azure HDInsight

A fürtök létrehozásával kapcsolatos hibák leggyakoribb kiváltó okai a következők:

- Engedélyekkel kapcsolatos problémák
- Erőforrás-házirendre vonatkozó korlátozások
- Tűzfalak
- Erőforrás-zárolások
- Nem támogatott összetevő-verziók
- Storage-fiók nevének korlátozásai
- Szolgáltatások kimaradásai

## <a name="permissions-issues"></a>Engedélyekkel kapcsolatos problémák

Ha Azure Data Lake Storage Gen2 használ, és a következő hibaüzenetet kapja `AmbariClusterCreationFailedErrorCode` : " :::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support."::: ", nyissa meg a Azure Portal, lépjen a Storage-fiókjába, és a Access Control (iam) területen győződjön meg arról, hogy a **Storage blob-adatközreműködői** vagy a **Storage blob-adatok tulajdonosi** szerepköre hozzárendelt egy hozzáférést az előfizetés **felhasználóhoz rendelt felügyelt identitásához** . Részletes utasításokért lásd a [Data Lake Storage Gen2 felügyelt identitásra vonatkozó engedélyek beállítása](../hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2) című témakört.

Ha Azure Data Lake Storage Gen1 használ, tekintse meg a telepítési és konfigurálási utasításokat [Azure Data Lake Storage Gen1 az Azure HDInsight-fürtök használatával](../hdinsight-hadoop-use-data-lake-storage-gen1.md)című témakört. A Data Lake Storage Gen1 HBase-fürtök esetén nem támogatott, és a HDInsight 4,0-es verziójában nem támogatott.

Ha az Azure Storage-t használja, győződjön meg arról, hogy a Storage-fiók neve érvényes a fürt létrehozásakor.

## <a name="resource-policy-restrictions"></a>Erőforrás-házirendre vonatkozó korlátozások

Az előfizetés-alapú Azure-szabályzatok nem tagadhatják meg a nyilvános IP-címek létrehozását. A HDInsight-fürt létrehozásához két nyilvános IP-cím szükséges.  

Az alábbi házirendek általánosságban befolyásolhatják a fürtök létrehozását:

* A házirendek meggátolják az IP-címek & terheléselosztó létrehozását az előfizetésen belül.
* Házirend a Storage-fiók létrehozásának megakadályozása.
* A hálózati erőforrások törlését megakadályozó szabályzat (IP-/Load Balancer).

## <a name="firewalls"></a>Tűzfalak

A virtuális hálózati vagy a Storage-fiókhoz tartozó tűzfalak megtagadhatják a HDInsight-felügyeleti IP-címekkel folytatott kommunikációt.

Az alábbi táblázatban lévő IP-címekről érkező forgalom engedélyezése.

| Forrás IP-címe | Cél | Irány |
|---|---|---|
| 168.61.49.99 | *: 443 | Bejövő |
| 23.99.5.239 | *: 443 | Bejövő |
| 168.61.48.131 | *: 443 | Bejövő |
| 138.91.141.162 | *: 443 | Bejövő |

Adja hozzá a fürthöz tartozó régióhoz tartozó IP-címeket is. Az egyes Azure-régiók címeinek listáját a [HDInsight-felügyeleti IP-címek](../hdinsight-management-ip-addresses.md) című részben tekintheti meg.

Ha expressz útvonalat vagy saját egyéni DNS-kiszolgálót használ, tekintse meg [a virtuális hálózat megtervezése az Azure HDInsight – több hálózat csatlakoztatása](../hdinsight-plan-virtual-network-deployment.md#multinet)című témakört.

## <a name="resources-locks"></a>Erőforrások zárolása  

Győződjön meg arról, hogy a [virtuális hálózat és az erőforráscsoport nem zárolható](../../azure-resource-manager/management/lock-resources.md). A fürtöket nem lehet létrehozni vagy törölni, ha az erőforráscsoport zárolva van. 

## <a name="unsupported-component-versions"></a>Nem támogatott összetevő-verziók

Győződjön meg arról, hogy az [Azure HDInsight támogatott verzióját](../hdinsight-component-versioning.md) és a megoldás bármely [Apache Hadoop összetevőjét](../hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions) használja.  

## <a name="storage-account-name-restrictions"></a>Storage-fiók nevének korlátozásai

A Storage-fiókok neve nem lehet hosszabb 24 karakternél, és nem tartalmazhat speciális karaktert. Ezek a korlátozások az alapértelmezett tárolónévre is vonatkoznak a tárfiókban.

Más elnevezési korlátozások is érvényesek a fürtök létrehozására. További információért lásd a [fürt nevének korlátozásait](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name).

## <a name="service-outages"></a>Szolgáltatások kimaradásai

Győződjön meg arról, hogy az [Azure állapota](https://status.azure.com) esetleges kimaradások vagy szolgáltatási problémák esetén is fennáll.

## <a name="next-steps"></a>Következő lépések

* [Azure HDInsight kiterjesztése Azure virtuális hálózat használatával](../hdinsight-plan-virtual-network-deployment.md)
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Az Azure Storage és az Azure HDInsight-fürtök együttes használata](../hdinsight-hadoop-use-blob-storage.md)
* [Fürtök beállítása a HDInsightban Apache Hadoop, Apache Spark, Apache Kafka stb. használatával](../hdinsight-hadoop-provision-linux-clusters.md)
