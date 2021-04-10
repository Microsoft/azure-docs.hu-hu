---
title: Nem sikerült beolvasni az Apache-beli fonal-naplót az Azure HDInsight
description: Hibaelhárítási lépések és lehetséges megoldások az Azure HDInsight-fürtökkel való interakció során felmerülő problémákhoz.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 02a79de8aee169f5f702d5fae67194c62363e8c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943048"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Forgatókönyv: nem sikerült beolvasni az Apache-beli fonal-naplót az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A Storage-fiókból talált Apache-alapú fonal-naplók nem emberi olvashatóságot mutatnak. A fájl-elemző nem működik, és a következő hibaüzenetet jeleníti meg:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Ok

Az Apache-beli fonal-napló formátumba van összesítve `IndexFile` , amelyet a fájl-elemző nem támogat.

## <a name="resolution"></a>Feloldás

1. Egy webböngészőből nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net` , ahol a a `CLUSTERNAME` fürt neve.

1. A Ambari felhasználói felületén navigáljon **a**  >    >  **speciális**  >  **speciális fonál-helyhez**.

1. A WASB Storage esetében: az alapértelmezett értéke a (z `yarn.log-aggregation.file-formats` `IndexedFormat,TFile` ). Módosítsa az értéket a következőre: `TFile` .

1. A ADLS Storage esetében: az alapértelmezett értéke a (z `yarn.nodemanager.log-aggregation.compression-type` `gz` ). Módosítsa az értéket a következőre: `none` .

1. Mentse a módosítást, és indítsa újra az összes érintett szolgáltatást.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.