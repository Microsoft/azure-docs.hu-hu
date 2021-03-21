---
title: Ismerje meg, hogyan használhat egy Apache Hadoop Sandboxot, Emulator-Azure HDInsight
description: 'Az Apache Hadoop ökoszisztéma használatának megismeréséhez beállíthatja, hogy egy Azure-beli virtuális gépen Hadoop-munkaterületet állítson be a Hortonworks-ben. '
keywords: Hadoop Emulator, Hadoop homokozó
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: how-to
ms.date: 05/29/2019
ms.openlocfilehash: eb286adfd7267a78fcf80bcf5ad34f8f1cc9f493
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946614"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Ismerkedjen meg egy Apache Hadoop homokozóval, amely egy virtuális gépen futó emulátor

Megtudhatja, hogyan telepítheti a virtuális gépeken futó Hortonworks Apache Hadoop-munkaterületét a Hadoop-ökoszisztéma megismerésére. A homokozó helyi fejlesztési környezetet biztosít a Hadoop, a Hadoop elosztott fájlrendszer (HDFS) és a feladatok beküldésének megismeréséhez. Ha már ismeri a Hadoop-t, megkezdheti a Hadoop használatát az Azure-ban egy HDInsight-fürt létrehozásával. További információ az első lépésekről: Ismerkedés [a Hadoop szolgáltatással a HDInsight-on](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Előfeltételek

* [Oracle VirtualBox](https://www.virtualbox.org/). Töltse le és telepítse innen [.](https://www.virtualbox.org/wiki/Downloads)

## <a name="download-and-install-the-virtual-machine"></a>A virtuális gép letöltése és telepítése

1. Tallózással keresse meg a [Cloudera letöltéseit](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

1. A virtuális gépen a legújabb Hortonworks-sandbox letöltéséhez kattintson a **VirtualBox** ( **telepítés** ) elemre. Jelentkezzen be, vagy fejezze be a termék érdeklődési formáját.

1. A letöltés megkezdéséhez kattintson a **HDP a SANDBOX (legújabb)** gombra.

A sandbox beállításával kapcsolatos útmutatásért lásd: a [homokozó üzembe helyezése és telepítési útmutatója](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

A régebbi verziójú HDP letöltéséhez tekintse meg a **régebbi verziók** alatt található hivatkozásokat.

## <a name="start-the-virtual-machine"></a>A virtuális gép elindítása

1. Nyissa meg az Oracle VM VirtualBoxt.
1. A **fájl** menüben kattintson a **berendezés importálása** elemre, majd adja meg a Hortonworks-homokozó rendszerképét.
1. Válassza ki a Hortonworks Sandboxot, kattintson a **Start** gombra, majd a **normál indítás** elemre. Ha a virtuális gép befejezte a rendszerindítási folyamatot, a bejelentkezési utasításokat jeleníti meg.

    ![a VirtualBox Manager normál indítása](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. Nyisson meg egy webböngészőt, és navigáljon a megjelenő URL-címhez (általában `http://127.0.0.1:8888` ).

## <a name="set-sandbox-passwords"></a>A homokozó jelszavainak beállítása

1. A Hortonworks-munkaterületének első **lépések** lépése lapon válassza a **Speciális beállítások megjelenítése** lehetőséget. Az oldalon található információk használatával jelentkezzen be a homokozóba az SSH használatával. Használja a megadott nevet és jelszót.

   > [!NOTE]
   > Ha nem telepített SSH-ügyfelet, használhatja a virtuális gép által biztosított webalapú SSH-t **http://localhost:4200/** .

    Amikor először csatlakozik az SSH-val, a rendszer felszólítja a root fiók jelszavának módosítására. Adjon meg egy új jelszót, amelyet az SSH használatával történő bejelentkezéskor használ.

2. Miután bejelentkezett, adja meg a következő parancsot:

    ```bash
    ambari-admin-password-reset
    ```

    Ha a rendszer kéri, adjon meg egy jelszót a Ambari-rendszergazdai fiókhoz. Ez akkor használatos, amikor hozzáfér a Ambari webes felhasználói felületéhez.

## <a name="use-hive-commands"></a>Struktúra-parancsok használata

1. A következő parancs használatával indítsa el a kaptár rendszerhéját egy, a homokozóhoz csatlakozó SSH-kapcsolatban:

    ```bash
    hive
    ```

2. A rendszerhéj elindítása után a következő paranccsal tekintheti meg a homokozóban elérhető táblákat:

    ```hiveql
    show tables;
    ```

3. A következő paranccsal kérhet le 10 sort a `sample_07` táblából:

    ```hiveql
    select * from sample_07 limit 10;
    ```

## <a name="next-steps"></a>Következő lépések

* [Ismerje meg, hogyan használhatja a Visual studiót a Hortonworks homokozóval](./apache-hadoop-visual-studio-tools-get-started.md)

* [A Hortonworks-Homokozóhoz tartozó kötelek megismerése](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Hadoop-oktatóanyag – első lépések a HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)