---
title: Az Apache Hadoop & biztonságos átviteli tárterületet biztosít – Azure HDInsight
description: Megtudhatja, hogyan hozhat létre biztonságos átvitelű Azure-tárfiókokkal rendelkező HDInsight-fürtöket.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: 22804015ebf0344c00e60c88f780fe22ba440b52
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774988"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop-fürtök biztonságos átviteli tárfiókokkal a Azure HDInsight

A [biztonságos átvitelt szükséges](../storage/common/storage-require-secure-transfer.md) funkció megnöveli az Azure Storage-fiók biztonságát azáltal, hogy minden, a fiókra beérkező kérést biztonságos kapcsolat használatára kényszerít. Ezt a funkciót és a wasbs sémát csak a HDInsight-fürt 3.6-os vagy újabb verziója támogatja.

> [!IMPORTANT]
> A biztonságos tárterület-átvitel fürt létrehozása utáni engedélyezése hibákat eredményezhet a tárfiók használatával, és nem ajánlott. Jobb, ha új fürtöt hoz létre egy olyan tárfiókkal, amely már engedélyezve van a biztonságos átvitelhez.

## <a name="storage-accounts"></a>Tárfiókok

### <a name="azure-portal"></a>Azure Portal

Alapértelmezés szerint a biztonságos átvitelre van szükség tulajdonság engedélyezve, amikor tárfiókot hoz létre a Azure Portal.

Meglévő tárfiók frissítéséhez a Azure Portal [lásd: Biztonságos átvitel megkövetelve a Azure Portal.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account)

### <a name="powershell"></a>PowerShell

A [New-AzStorageAccount PowerShell-parancsmag](/powershell/module/az.storage/new-azstorageaccount)esetében győződjön meg arról, hogy a paraméter `-EnableHttpsTrafficOnly` értéke `1` .

Meglévő tárfiók PowerShell használatával történő frissítéséhez lásd: [Biztonságos átvitel megkövetele a PowerShell használatával.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)

### <a name="azure-cli"></a>Azure CLI

Az az [storage account create](/cli/azure/storage/account#az_storage_account_create)Azure CLI-parancs esetében győződjön meg arról, hogy a paraméter értéke `--https-only` `true` .

Egy meglévő tárfiók Azure CLI-val való frissítéséhez [lásd: Biztonságos átvitel megkövetele az Azure CLI-val.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)

## <a name="add-additional-storage-accounts"></a>További tárfiókok hozzáadása

További, biztonságos átvitel használatára képes tárfiókok hozzáadására számos lehetőség áll rendelkezésre:

* Az előző szakaszban ismertetett Azure Resource Manager-sablon módosítása.
* Egy fürt létrehozása az [Azure Portal](https://portal.azure.com) használatával és a kapcsolódó tárfiók megadása.
* Szkriptműveleteket használata további, biztonságos átvitel használatára képes tárfiókok meglévő HDInsight-fürthöz történő hozzáadásához. További információkért lásd: [Add additional storage accounts to HDInsight](hdinsight-hadoop-add-storage.md) (További tárfiókok hozzáadása a HDInsighthoz).

## <a name="next-steps"></a>Következő lépések

* Az Azure Storage (WASB) használata [az Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) helyett alapértelmezett adattárként
* További információt az Azure Storage HDInsight általi használatáról [az Azure Storage és a HDInsight együttes használatát](hdinsight-hadoop-use-blob-storage.md) ismertető cikkben talál.
* További információ a HDInsightba való adatfeltöltésről: [Adatok feltöltése a HDInsightba](hdinsight-upload-data.md).