---
title: Összetevők és verziók Apache Hadoop – Azure HDInsight
description: Ismerje meg az Azure HDInsight Apache Hadoop összetevőit és verzióit.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: dbd5b507fd4a7b2434158dbdc80584a7fd348732
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726583"
---
# <a name="azure-hdinsight-versions"></a>Azure HDInsight-verziók

A HDInsight Apache Hadoop környezeti összetevőket és HDInsight platformot egy fürtre telepített csomagba. További részletekért lásd: [Hogyan működik a HDInsight verziószámozása](hdinsight-overview-versioning.md).

## <a name="supported-hdinsight-versions"></a>Támogatott HDInsight-verziók

Ez a táblázat a Azure Portal és más, például a PowerShell, a CLI és a .NET SDK HDInsight elérhető verzióit sorolja fel.

| HDInsight verziója | VM OS | Kiadási dátum| Támogatás típusa | Támogatás lejárati dátuma | Nyugdíjazás dátuma | Magas rendelkezésre állás |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 16.0.4 LTS |24, 2018. szeptember | [Standard](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |Yes |
| [HDInsight 3,6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |2017. április 4.      | [Basic](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | Standard szintű támogatás lejárata – június 30-ig 2021 <br> Alapszintű támogatás lejárat – 2022. április 3. |2022. április 4. |Yes |

* 2021. július 1-től a Microsoft bizonyos HDI 3,6 típusú fürtökhöz biztosít alapszintű támogatás. Lásd: [HDInsight 3,6 Component Versions](hdinsight-36-component-versioning.md).

## <a name="release-notes"></a>Kibocsátási megjegyzések

További kibocsátási megjegyzések a HDInsight legújabb verzióiban: [HDInsight kibocsátási megjegyzések](hdinsight-release-notes.md).

## <a name="support-options-for-hdinsight-versions"></a>HDInsight-verziók támogatási lehetőségei

A támogatás olyan időszakra van meghatározva, amelyet a Microsoft ügyfél-és támogatási szolgálata a HDInsight-verzióval támogat. A HDInsight kétféle támogatást kínál: 
- A **standard szintű támogatás** olyan időszak, amelyben a Microsoft frissítéseket és támogatást biztosít a HDInsight-fürtökön.  
    Javasoljuk, hogy a legújabb teljes körűen támogatott verziót használja a megoldások létrehozásához. 
- **Alapszintű támogatás** az az időszak, amelyben a Microsoft korlátozott karbantartást biztosít az erőforrás-szolgáltató HDInsight. A HDInsight rendszerképek és a nyílt forráskódú szoftverek (OSS) összetevői nem lesznek szervizelve.   A HDInsight-fürtökön csak a kritikus biztonsági javítások lesznek kijavítva.  
  A Microsoft nem javasolja, hogy új fürtöket hozzon létre, vagy felépítse a friss megoldásokat, ha a verzió alapszintű támogatás. Javasoljuk, hogy a meglévő fürtöket a legújabb, teljes mértékben támogatott verzióra migrálja. 

A **támogatás lejárata** azt jelenti, hogy a Microsoft már nem nyújt támogatást az adott HDInsight-verzióhoz. Előfordulhat, hogy a fürt létrehozásához a Azure Portal már nem érhető el.

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
