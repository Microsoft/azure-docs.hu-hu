---
title: Nem lehet létrehozni Jupyter Notebook az Azure HDInsight
description: Ismerje meg az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: d5f6612e22522452efb5eecd14c6c825bf29e6d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938679"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Nem lehet létrehozni Jupyter Notebook az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Jupyter Notebook indításakor a következőket tartalmazó hibaüzenet jelenik meg:

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Ok

A verziószám nem egyezik.

## <a name="resolution"></a>Feloldás

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nyissa meg `_version.py` a következő parancs futtatásával:

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. Módosítsa az **5** – **4** értékre, hogy a módosított sor a következőképpen jelenik meg:

    ```python
    version_info = (4, 0, 3)
    ```

    Mentse a módosításokat a **CTRL + X**, **Y**, **ENTER** billentyűkombináció megadásával.

1. Egy webböngészőből nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER` , ahol a a `CLUSTERNAME` fürt neve.

1. Indítsa újra a Jupyter szolgáltatást.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
