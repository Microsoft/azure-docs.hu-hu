---
title: Összetevők és verziók Apache Hadoop – Azure HDInsight
description: Ismerje meg az Azure HDInsight Apache Hadoop összetevőit és verzióit.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 166c52660c9f47b8ecfce10f7e9c3b81ff067f35
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699368"
---
# <a name="azure-hdinsight-versions"></a>Azure HDInsight-verziók

A HDInsight Apache Hadoop környezeti összetevőket és HDInsight platformot egy fürtre telepített csomagba. További részletekért lásd: [Hogyan működik a HDInsight verziószámozása](hdinsight-overview-versioning.md).

## <a name="supported-hdinsight-versions"></a>Támogatott HDInsight-verziók

Ez a táblázat a Azure Portal és más, például a PowerShell, a CLI és a .NET SDK HDInsight elérhető verzióit sorolja fel.

| HDInsight verziója | VM OS | Kiadási dátum | Támogatás lejárati dátuma | Nyugdíjazás dátuma | Magas rendelkezésre állás |
| --- | --- | --- | --- | --- | --- |
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |24, 2018. szeptember | | |Igen |
| [HDInsight 3,6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |2017. április 4.      | * Június 30., 2021 |Június 30., 2021 |Igen |

* Kiterjesztjük bizonyos HDInsight 3,6-fürtök támogatási időkeretét. Lásd: [HDInsight 3,6 Component Versions](hdinsight-36-component-versioning.md).

## <a name="release-notes"></a>Kibocsátási megjegyzések

További kibocsátási megjegyzések a HDInsight legújabb verzióiban: [HDInsight kibocsátási megjegyzések](hdinsight-release-notes.md).

## <a name="support-options-for-hdinsight-versions"></a>HDInsight-verziók támogatási lehetőségei

A HDInsight standard szintű támogatást kínál, amely a Microsoft ügyfélszolgálatának és támogatásának által támogatott HDInsight-verzióval rendelkező időszakban van meghatározva.

A **támogatás lejárata** azt jelenti, hogy a Microsoft már nem nyújt támogatást az adott HDInsight-verzióhoz. És már nem érhető el a Azure Portal a fürt létrehozásához.

A **nyugdíjazás** azt jelenti, hogy egy HDInsight-verzió meglévő fürtjének futtatása továbbra is a következő:. Ezen verzió új fürtök nem hozhatók létre semmilyen módon, beleértve a CLI-t és az SDK-kat is. A vezérlési sík egyéb funkciói, például a manuális skálázás és az automatikus skálázás nem garantáltak a nyugdíjazási dátum utáni munkához. A támogatás nem érhető el a kivont verziókhoz.

## <a name="versioning-considerations"></a>Verziószámozási megfontolások
- Ha a fürtöt lemezképtel telepítette, a fürt nem frissül automatikusan az újabb lemezkép-verzióra. Új fürtök létrehozásakor a rendszer a legújabb rendszerkép-verziót fogja telepíteni.
- Az ügyfeleknek az új HDInsight-verzió használatakor tesztelni és ellenőrizniük kell, hogy az alkalmazások megfelelően futnak-e.
- A HDInsight fenntartja a jogot arra, hogy előzetes értesítés nélkül módosítsa az alapértelmezett verziót. Ha a verziótól függ, adja meg a HDInsight verzióját a fürtök létrehozásakor.
- A HDInsight az HDInsight verziójának kivonása előtt kihasználhatják az OSS-összetevők verzióját.

## <a name="next-steps"></a>Következő lépések

- [Fürt beállítása Apache Hadoop, Spark és más HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Munkahelyi Apache Hadoop HDInsight Windows rendszerű SZÁMÍTÓGÉPRŐL](hdinsight-hadoop-windows-tools.md)