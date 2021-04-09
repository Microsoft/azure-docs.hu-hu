---
title: OPERÁCIÓSRENDSZER-javítási ütemterv konfigurálása az Azure HDInsight-fürtökhöz
description: Útmutató a Linux-alapú HDInsight-fürtök operációsrendszer-javítási ütemtervének konfigurálásához.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: 636caf592baa4df771f7cc50095911d0337456d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939380"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>A Linux-alapú HDInsight-fürtök operációsrendszer-javítási ütemtervének konfigurálása

> [!IMPORTANT]
> Az Ubuntu-lemezképek az új Azure HDInsight-fürt létrehozása során elérhetővé válnak a közzétételtől számított három hónapon belül. A futó fürtök nem automatikusan lettek kijavítani. Az ügyfeleknek parancsfájl-műveleteket vagy más mechanizmusokat kell használniuk a futó fürtök javításához. Ajánlott eljárásként futtathatja ezeket a parancsfájl-műveleteket, és közvetlenül a fürt létrehozása után is alkalmazhatja a biztonsági frissítéseket.

A HDInsight támogatást nyújt a fürtben lévő általános feladatok végrehajtásához, például az operációsrendszer-javítások, a biztonsági frissítések és a csomópontok újraindításának telepítéséhez. Ezek a feladatok a következő két parancsfájl használatával végezhetők el, amelyek [parancsfájl-műveletként](hdinsight-hadoop-customize-cluster-linux.md)futtathatók, és paraméterekkel konfigurálhatók:

- `schedule-reboots.sh` – Végezzen azonnali újraindítást, vagy ütemezzen egy újraindítást a fürtcsomópontokon.
- `install-updates-schedule-reboots.sh` – Telepítse az összes frissítést, csak a kernel + biztonsági frissítéseket, vagy csak a kernel frissítéseit.

> [!NOTE]  
> A parancsfájl-műveletek nem fogják automatikusan alkalmazni a frissítéseket az összes jövőbeli frissítési ciklusra. Futtassa a parancsfájlokat minden alkalommal, amikor új frissítéseket kell alkalmazni a frissítések telepítéséhez, majd indítsa újra a virtuális gépet.

## <a name="preparation"></a>Előkészítés

Az éles környezetben történő üzembe helyezést megelőzően egy reprezentatív, nem éles környezethez tartozó javítás. Dolgozzon ki egy tervet a rendszer megfelelő teszteléséhez a tényleges javítás előtt.

A fürttel létesített SSH-munkamenetből időről időre üzenetet kaphat, hogy a biztonsági frissítések elérhetők lesznek. Az üzenet valahogy így néz ki:

```
89 packages can be updated.
82 updates are security updates.

*** System restart required ***

Welcome to Spark on HDInsight.

```

A javítások opcionálisan és saját belátása szerint is megadhatók.

## <a name="restart-nodes"></a>Csomópontok újraindítása
  
A parancsfájl [-újraindítások](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh), beállítja a fürtön lévő gépeken végrehajtandó újraindítás típusát. A parancsfájl-művelet elküldésekor állítsa be úgy, hogy mindhárom csomópont-típusra vonatkozzon: a fő csomópont, a feldolgozói csomópont és a Zookeeper. Ha a parancsfájl nincs alkalmazva a csomópont típusára, az adott csomóponthoz tartozó virtuális gépek nem lesznek frissítve vagy újraindulva.

Az `schedule-reboots script` elfogad egy numerikus paramétert:

| Paraméter | Elfogadott értékek | Definíció |
| --- | --- | --- |
| Végrehajtandó újraindítás típusa | 1 vagy 2 | Az 1 érték lehetővé teszi az ütemezés újraindítását (12-24 órán belül ütemezve). A 2 érték azonnali újraindítást tesz lehetővé (5 perc). Ha nincs megadva paraméter, az alapértelmezett érték 1. |  

## <a name="install-updates-and-restart-nodes"></a>Frissítések telepítése és a csomópontok újraindítása

A szkript [Install-Updates-Schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) különböző típusú frissítések telepítésére és a virtuális gép újraindítására szolgáló lehetőségeket biztosít.

A `install-updates-schedule-reboots` parancsfájl két numerikus paramétert fogad el az alábbi táblázatban leírtak szerint:

| Paraméter | Elfogadott értékek | Definíció |
| --- | --- | --- |
| Telepítendő frissítések típusa | 0, 1 vagy 2 | A 0 érték csak a kernel frissítéseit telepíti. Az 1 érték a kernel + biztonsági frissítéseket telepíti, és 2 telepíti az összes frissítést. Ha nincs megadva paraméter, az alapértelmezett érték a 0. |
| Végrehajtandó újraindítás típusa | 0, 1 vagy 2 | A 0 érték letiltja az újraindítást. Az 1 érték lehetővé teszi az ütemezett újraindítást, a 2 pedig lehetővé teszi az azonnali újraindítást. Ha nincs megadva paraméter, az alapértelmezett érték a 0. A felhasználónak módosítania kell az 1. bemeneti paramétert a 2. paraméterre. |

> [!NOTE]
> Azt követően, hogy egy meglévő fürtre alkalmazza, meg kell jelölnie egy parancsfájlt. Ellenkező esetben a skálázási műveletekkel létrehozott új csomópontok az alapértelmezett javítási ütemtervet fogják használni. Ha a parancsfájlt a fürt létrehozási folyamatának részeként alkalmazza, a rendszer automatikusan megőrzi azt.

> [!NOTE]
> Az ütemezett újraindítási lehetőség a javított fürtcsomópontok automatikus működés közbeni újraindítását teszi elérhetővé 12 – 24 óra alatt, és figyelembe veszi a magas rendelkezésre állás, a frissítési tartomány és a tartalék tartomány szempontjait. Az ütemezett újraindítás nem szakítja meg a futó munkaterheléseket, de a csomópontok átmeneti állapotában is eltarthat, ha a csomópontok nem érhetők el, ami hosszabb feldolgozási időt vesz igénybe. 

## <a name="next-steps"></a>Következő lépések

A parancsfájl-műveletek használatával kapcsolatos konkrét lépésekért tekintse meg a [Linux-alapú HDInsight-fürtök testre szabása parancsfájl-művelettel](hdinsight-hadoop-customize-cluster-linux.md)című szakaszt a következő részekben:

- [Parancsfájl-művelet használata a fürt létrehozása során](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [Parancsfájl-művelet alkalmazása futó fürtre](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
