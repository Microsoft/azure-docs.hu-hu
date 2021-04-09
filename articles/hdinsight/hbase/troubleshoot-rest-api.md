---
title: REST API az Apache HBase lekérdezéséhez az Azure HDInsight
description: Ez a cikk az Apache HBase-összetevők Azure HDInsight-fürtökön való interakciójának hibaelhárítási lépéseit ismerteti.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 636f84c4c6aa097288dc2fb5481dcedd6863409d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942874"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>REST API az Apache HBase lekérdezéséhez az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache HBase REST-felület használatával lekérdezheti az alapértelmezett eredményektől eltérő névtérben lévő táblázatot (500-es HTTP-állapot).

## <a name="cause"></a>Ok

A HBase REST API csak az alapértelmezett névtér használata esetén támogatott. Ez egy ismert probléma a HBase-névterek használatával kapcsolatban, illetve olyan hívások kezdeményezésére, amelyek a HDInsight REST-kiszolgálóval rendelkező, oszlopos családokkal rendelkező oszlopokra vonatkoznak. Ennek az az oka, hogy biztonsági probléma van az HDInsight-átjáróval. Ha az API-t használja névtérrel rendelkező tábla létrehozásához, oszlopos családokon keresztül éri el az oszlopokat, meg kell adnia a `:` karaktert, amely biztonsági problémát jelent az IIS-átjáró moduljában.

## <a name="mitigation"></a>Kockázatcsökkentés

Az átjáró/REST-kiszolgáló megkerüléséhez telepítse az alkalmazást egy olyan virtuális gépre, amely ugyanabban az Azure-VNet található, mint a HDInsight-fürt. Ezt követően közvetlenül az RPC-n keresztül tud kommunikálni a HBase (a REST-kiszolgáló megkerülésével), vagy a munkavégző csomópontokon futó különálló REST-kiszolgálókon a HDInsight-átjárók megkerülésével.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.