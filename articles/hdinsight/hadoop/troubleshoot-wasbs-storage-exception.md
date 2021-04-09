---
title: Az elérni kívánt fiók nem támogatja a http-hibát az Azure HDInsight
description: Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 46063d5f2d9ff4b85914ad7c4cd74a2400298db0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943076"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>Az elérni kívánt fiók nem támogatja a http-hibát az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A következő hibaüzenet érkezik:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Ok

Több oka is van a hibaüzenet fogadásának:

* A Storage-fiókhoz engedélyezve van a [biztonságos átvitel](../../storage/common/storage-require-secure-transfer.md) , és a helytelen [URI-séma](../hdinsight-hadoop-linux-information.md#URI-and-scheme) van használatban.

* Egy fürtöt olyan Storage-fiókkal hoztak létre, amely *letiltotta* a biztonságos átvitelt. Ezt követően a biztonságos átvitel engedélyezve volt a Storage-fiókon.

## <a name="resolution"></a>Feloldás

Ha a biztonságos átvitel engedélyezve van az Azure Storage vagy a Data Lake Storage Gen2 számára, akkor az URI a következő lesz: `wasbs://` vagy `abfss://` .  Lásd még: [biztonságos átvitel](../../storage/common/storage-require-secure-transfer.md).

Új fürtök esetén olyan Storage-fiókot használjon, amely már rendelkezik a kívánt biztonságos átviteli beállítással. Ne módosítsa a biztonságos átvitel beállítást egy meglévő fürt által használt Storage-fiókhoz.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.