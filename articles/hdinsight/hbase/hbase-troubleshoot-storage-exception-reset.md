---
title: Tárolási kivétel az Azure HDInsight-beli kapcsolatok alaphelyzetbe állítása után
description: Tárolási kivétel az Azure HDInsight-beli kapcsolatok alaphelyzetbe állítása után
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/08/2019
ms.openlocfilehash: 82cad7fc68d650e5f525a8722d3e2f3e9865f456
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98936743"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Forgatókönyv: tárolási kivétel az Azure HDInsight-beli kapcsolatok visszaállítása után

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Nem hozható létre új Apache HBase tábla.

## <a name="cause"></a>Ok

Egy tábla csonkítása során probléma merült fel a tárolási kapcsolatban. A tábla bejegyzése törölve lett a HBase metaadat-táblában. Egyetlen blob-fájl sem lett törölve.

Bár a tárolóban nem található a mappa nevű blob `/hbase/data/default/ThatTable` . A WASB illesztőprogramja megtalálta a fenti blob-fájl létezését, és nem teszi lehetővé, hogy a létrehozott Blobok létre legyenek hozva `/hbase/data/default/ThatTable` , mert feltételezte, hogy a szülő mappák is megtalálhatók, így a tábla létrehozása sikertelen lesz.

## <a name="resolution"></a>Feloldás

1. Az Apache Ambari felhasználói felületén indítsa újra az aktív HMaster. Ez lehetővé teszi, hogy a két készenléti HMaster az aktív legyen, és az új aktív HMaster újra betölti a metaadatok táblázatának adatait. Így nem fogja látni a `already-deleted` táblázatot a HMaster felhasználói felületén.

1. Az árva blob-fájlt a felhasználói felületi eszközökről, például a Cloud Explorer böngészőből vagy a hasonló parancs futtatásával találhatja meg `hdfs dfs -ls /xxxxxx/yyyyy` . Futtassa `hdfs dfs -rmr /xxxxx/yyyy` a parancsot a blob törléséhez. Például: `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Most létrehozhat egy azonos nevű új táblát a HBase-ben.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.