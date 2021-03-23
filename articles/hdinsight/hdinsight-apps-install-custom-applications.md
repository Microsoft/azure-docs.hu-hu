---
title: Egyéni Apache Hadoop-alkalmazások telepítése az Azure HDInsight
description: Ismerje meg, hogyan telepíthet HDInsight-alkalmazásokat Apache Hadoop-fürtökhöz az Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 19dd5bf94b524ff3eb6eb601c77b503a0040bd75
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867644"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Egyéni Apache Hadoop-alkalmazások telepítése az Azure HDInsight

Ebből a cikkből megtudhatja, hogyan telepíthet egy [Apache Hadoop](https://hadoop.apache.org/) alkalmazást az Azure HDInsight, amely még nem lett közzétéve a Azure Portal. Az ebben a cikkben telepítendő alkalmazás [Hue](https://gethue.com/).

An méretű HDInsight alkalmazás egy olyan alkalmazás, amelyet a felhasználók telepíthetnek egy HDInsight-fürtre.  Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései.  

## <a name="prerequisites"></a>Előfeltételek

Ha egy meglévő HDInsight-fürtre kíván HDInsight-alkalmazásokat telepíteni, HDInsight-fürttel kell rendelkeznie. A fürt létrehozását lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md). HDInsight-alkalmazásokat HDInsight-fürt létrehozása közben is telepíthet.

## <a name="install-hdinsight-applications"></a>HDInsight-alkalmazások telepítése

HDInsight-alkalmazások fürt létrehozása közben vagy egy meglévő HDInsight-fürtre is telepíthetők. Azure Resource Manager-sablonok meghatározása: [MSDN: Install an HDInsight application](/rest/api/hdinsight/hdinsight-application) (MSDN: HDInsight-alkalmazás telepítése).

Az alkalmazás üzembe helyezéséhez szükséges fájlok (Hue esetén):

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): HDInsight-alkalmazás telepítésére szolgáló Resource Manager-sablon. Lásd: [MSDN: Install an HDInsight application](/rest/api/hdinsight/hdinsight-application) (MSDN: HDInsight-alkalmazás telepítése).
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Az élcsomópont konfigurálásához a Resource Manager-sablon által meghívott szkriptművelet.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): A hui-install_v0.sh fájlból meghívott bináris Hue-fájl.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): A hui-install_v0.sh fájlból meghívott bináris Hue-fájl.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): A hui-install_v0.sh fájlból meghívott webes mintaalkalmazás (Tomcat).

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>A Hue telepítése meglévő HDInsight-fürtre

1. Válassza ki az alábbi rendszerképet az Azure-ba való bejelentkezéshez, majd nyissa meg a Resource Manager-sablont a Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    A Resource Manager-sablon a következő helyen található: [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue) .  A Resource Manager-sablon írásának ismertetése: [MSDN: Install an HDInsight application](/rest/api/hdinsight/hdinsight-application) (MSDN: HDInsight-alkalmazás telepítése).

1. Válassza ki azt a meglévő **erőforráscsoportot** , amely a fürtöt tartalmazza a legördülő listából. Ugyanazt az erőforráscsoportot kell használnia, mint a fürtöt.

1. Adja meg annak a fürtnek a nevét, amelyre telepíteni kívánja az alkalmazást. Ennek létező fürtnek kell lennie.

1. Jelölje be az Elfogadom a **fenti feltételeket és kikötéseket** jelölőnégyzetet.

1. Válassza a **Beszerzés** lehetőséget.

A telepítés állapotát a portál irányítópultjára rögzített csempén vagy a portál értesítései között (ehhez kattintson a portál felső részén található harang ikonra) követheti.  Az alkalmazás telepítése nagyjából 10 percet vesz igénybe.

### <a name="to-install-hue-while-creating-a-cluster"></a>A Hue telepítése fürt létrehozása közben

1. Válassza ki az alábbi rendszerképet az Azure-ba való bejelentkezéshez, majd nyissa meg a Resource Manager-sablont a Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    A Resource Manager-sablon a következő helyen található: [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json) .  A Resource Manager-sablon írásának ismertetése: [MSDN: Install an HDInsight application](/rest/api/hdinsight/hdinsight-application) (MSDN: HDInsight-alkalmazás telepítése).

2. Kövesse a fürt létrehozására és a Hue telepítésére vonatkozó utasításokat. További információk a HDInsight-fürtökről: [Create Linux-based Hadoop clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsight szolgáltatásban).

### <a name="other-installation-methods"></a>Egyéb telepítési módszerek

A Resource Manager-sablonok meghívására az Azure Portalon kívül az [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) és az [Azure parancssori felület](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) is használható.

## <a name="validate-the-installation"></a>A telepítés ellenőrzése

Az alkalmazás telepítésének ellenőrzéséhez az alkalmazás állapotát az Azure Portalon követheti. Emellett az összes HTTP-végpontot is ellenőrizheti a vártnak megfelelően, és a weblapot, ha van ilyen.

A **Hue** esetében az alábbi lépéseket hajthatja végre:

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki azt a fürtöt, amelyre az alkalmazást telepítette.
1. A **Beállítások** menüben válassza az **alkalmazások** lehetőséget.
1. A tulajdonságok megtekintéséhez válassza a **színárnyalat** lehetőséget a listából.  
1. A webhely érvényesítéséhez válassza a weblap hivatkozást.

### <a name="azure-cli"></a>Azure CLI

Cserélje le `CLUSTERNAME` a és a `RESOURCEGROUP` értéket a megfelelő értékekre, majd adja meg az alábbi parancsokat:

* A HDInsight-fürt összes alkalmazásának felsorolása.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* A megadott alkalmazás tulajdonságainak beolvasása.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>A telepítési folyamat hibaelhárítása

Az alkalmazás telepítési állapotát a portál értesítései között (ehhez kattintson a portál felső részén található harang ikonra) követheti.

Ha egy alkalmazás telepítése nem sikerült, a következő három helyről láthatja a hibaüzeneteket és a hibakeresési információkat:

* HDInsight-alkalmazások: általános hibainformációk.

    Nyissa meg a fürtöt a portálon, és válassza az alkalmazások lehetőséget a beállítások közül:

    :::image type="content" source="./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png" alt-text="hdinsight applications application installation error":::

* HDInsight-parancsfájlművelet: ha a HDInsight-alkalmazás hibaüzenete parancsfájlművelet-hibát jelez, a hibáról további információkat a parancsfájlműveletek paneljén talál.

    Válassza a parancsfájl művelet lehetőséget a beállítások közül. A hibaüzeneteket megjelenítő parancsfájlművelet-előzmény

    :::image type="content" source="./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png" alt-text="hdinsight applications script action error":::

* Apache Ambari webes felhasználói felület: Ha a hiba oka a telepítési parancsfájl, használja a Ambari webes felhasználói felületét a telepítési parancsfájlok teljes naplóinak megtekintéséhez.

    További információ: parancsfájl- [műveletek hibakeresése](./troubleshoot-script-action.md).

## <a name="remove-hdinsight-applications"></a>HDInsight-alkalmazások eltávolítása

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki azt a fürtöt, amelyre az alkalmazást telepítette.
1. A **Beállítások** menüben válassza az **alkalmazások** lehetőséget.
1. Kattintson a jobb gombbal az eltávolítani kívánt alkalmazásra, majd válassza a **Törlés** lehetőséget.
1. Válassza az **Igen** lehetőséget a megerősítéshez.

### <a name="azure-cli"></a>Azure CLI

Cserélje le a, a `NAME` `CLUSTERNAME` és a `RESOURCEGROUP` értéket a megfelelő értékekre, majd írja be az alábbi parancsot:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>Következő lépések

* [MSDN: Install an HDInsight application](/rest/api/hdinsight/hdinsight-application) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan fejleszthet Resource Manager-sablonokat HDInsight-alkalmazások üzembe helyezéséhez.
* [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md): Megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat a fürtjeire.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Linux-alapú Apache Hadoop-fürtök létrehozása a HDInsight Resource Manager-sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md): megtudhatja, hogyan hívhat Resource Manager-sablonokat HDInsight-fürtök létrehozásához.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Üres élcsomópontok használata a HDInsightban): a cikk bemutatja, hogyan lehet üres élcsomópontot használni egy HDInsight-fürt elérésére, HDInsight-alkalmazások tesztelésére és HDInsight-alkalmazások üzemeltetésére.
