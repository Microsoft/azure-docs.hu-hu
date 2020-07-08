---
title: Apache Hadoop & biztonságos átvitelű tároló – Azure HDInsight
description: Megtudhatja, hogyan hozhat létre biztonságos átvitelű Azure-tárfiókokkal rendelkező HDInsight-fürtöket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c1e5ca8b0bb828e5e8ce896bba6a5278266b118e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77560082"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Biztonságos átvitelű Storage-fiókkal rendelkező fürtök Apache Hadoop az Azure HDInsight

A [biztonságos átvitelt szükséges](../storage/common/storage-require-secure-transfer.md) funkció megnöveli az Azure Storage-fiók biztonságát azáltal, hogy minden, a fiókra beérkező kérést biztonságos kapcsolat használatára kényszerít. Ezt a funkciót és a wasbs sémát csak a HDInsight-fürt 3.6-os vagy újabb verziója támogatja.

> [!IMPORTANT]
> A Biztonságos tár átvitelének engedélyezése a fürt létrehozása után hibákat eredményezhet a Storage-fiókkal, és nem ajánlott. Jobb megoldás, ha olyan Storage-fiókkal szeretne új fürtöt létrehozni, amelyen már engedélyezve van a biztonságos átvitel.

## <a name="storage-accounts"></a>Tárfiókok

### <a name="azure-portal"></a>Azure Portal

Alapértelmezés szerint a biztonságos átvitel szükséges tulajdonság engedélyezve van, amikor Azure Portal-ben hoz létre egy Storage-fiókot.

Ha Azure Portal használatával szeretne frissíteni egy meglévő Storage-fiókot, tekintse meg [a biztonságos átvitel Megkövetelése Azure Portalsal](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account)című témakört.

### <a name="powershell"></a>PowerShell

A [New-AzStorageAccount PowerShell-](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)parancsmag esetén győződjön meg arról, hogy a paraméter értéke `-EnableHttpsTrafficOnly` `1` .

Egy meglévő Storage-fiók PowerShell-lel való frissítéséhez lásd: [biztonságos átvitel megkövetelése a PowerShell](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)-lel.

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI-parancs az [Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create), győződjön meg róla, hogy a paraméter értéke `--https-only` `true` .

Meglévő Storage-fiók Azure CLI-vel való frissítéséhez lásd: [biztonságos átvitel megkövetelése az Azure CLI-vel](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

## <a name="add-additional-storage-accounts"></a>További tárfiókok hozzáadása

További, biztonságos átvitel használatára képes tárfiókok hozzáadására számos lehetőség áll rendelkezésre:

* Az előző szakaszban ismertetett Azure Resource Manager-sablon módosítása.
* Egy fürt létrehozása az [Azure Portal](https://portal.azure.com) használatával és a kapcsolódó tárfiók megadása.
* Szkriptműveleteket használata további, biztonságos átvitel használatára képes tárfiókok meglévő HDInsight-fürthöz történő hozzáadásához. További információkért lásd: [Add additional storage accounts to HDInsight](hdinsight-hadoop-add-storage.md) (További tárfiókok hozzáadása a HDInsighthoz).

## <a name="next-steps"></a>További lépések

* Az Azure Storage (WASB) használata [Apache HADOOP HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) helyett alapértelmezett adattárként
* További információt az Azure Storage HDInsight általi használatáról [az Azure Storage és a HDInsight együttes használatát](hdinsight-hadoop-use-blob-storage.md) ismertető cikkben talál.
* További információ a HDInsightba való adatfeltöltésről: [Adatok feltöltése a HDInsightba](hdinsight-upload-data.md).
