---
title: Üres peremhálózati csomópontok használata Apache Hadoop fürtökön az Azure HDInsight
description: Üres peremhálózati csomópont hozzáadása egy HDInsight-fürthöz. Ügyfélként használható, majd tesztelje vagy üzemelteti a HDInsight-alkalmazásait.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: 4061df2dfcfb4cf993e2312fb3438de66863a476
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942683"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Üres peremhálózati csomópontok használata Apache Hadoop-fürtökön a HDInsight-ben

Megtudhatja, hogyan adhat hozzá üres peremhálózati csomópontot egy HDInsight-fürthöz. Az üres peremhálózati csomópont egy Linux rendszerű virtuális gép, amelyen ugyanaz az ügyféleszközök van telepítve és konfigurálva, mint a átjárócsomópontokkal. De nem fut [Apache Hadoop](./hadoop/apache-hadoop-introduction.md) szolgáltatás. Használhatja a peremhálózati csomópontot a fürt eléréséhez, az ügyfélalkalmazások teszteléséhez és az ügyfélalkalmazások üzemeltetéséhez.

A fürt létrehozásakor hozzáadhat egy üres peremhálózati csomópontot egy meglévő HDInsight-fürthöz egy új fürthöz. Üres peremhálózati csomópont hozzáadását Azure Resource Manager sablon használatával végezheti el.  Az alábbi minta azt mutatja be, hogyan történik a sablon használata:

```json
"resources": [
    {
        "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
        "type": "Microsoft.HDInsight/clusters/applications",
        "apiVersion": "2015-03-01-preview",
        "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
        "properties": {
            "marketPlaceIdentifier": "EmptyNode",
            "computeProfile": {
                "roles": [{
                    "name": "edgenode",
                    "targetInstanceCount": 1,
                    "hardwareProfile": {
                        "vmSize": "{}"
                    }
                }]
            },
            "installScriptActions": [{
                "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                "uri": "[parameters('installScriptAction')]",
                "roles": ["edgenode"]
            }],
            "uninstallScriptActions": [],
            "httpsEndpoints": [],
            "applicationType": "CustomApplication"
        }
    }
],
```

Ahogy az a mintában is látható, egy [parancsfájl-művelet](hdinsight-hadoop-customize-cluster-linux.md) meghívásával további konfigurálást végezhet. Például az [Apache Hue](hdinsight-hadoop-hue-linux.md) telepítése a peremhálózati csomóponton. A parancsfájl-művelet parancsfájljának nyilvánosan elérhetőnek kell lennie a weben.  Ha például a szkriptet az Azure Storage tárolja, használja a nyilvános tárolókat vagy a nyilvános blobokat.

Az Edge-csomópont virtuális gép méretének meg kell felelnie a HDInsight-fürt munkavégző csomópontjának a virtuálisgép-méret követelményeinek. A munkavégző csomópontok ajánlott virtuálisgép-méreteit lásd: [Apache Hadoop-fürtök létrehozása a HDInsight-ben](hdinsight-hadoop-provision-linux-clusters.md#cluster-type).

Miután létrehozott egy peremhálózati csomópontot, az SSH használatával kapcsolódhat a peremhálózati csomóponthoz, és az ügyféleszközök futtatásával érheti el a Hadoop-fürtöt a HDInsight-ben.

> [!WARNING]
> Az Edge-csomópontra telepített egyéni összetevők a Microsofttól kereskedelmileg ésszerű támogatást kapnak. Ez az észlelt problémák megoldásához vezethet. Vagy ha további segítségre van szüksége, közösségi erőforrásokra is utalhat. A következőkben néhány, a Közösségtől származó Súgó közül a legaktívabb helyek közül:
>
> * [Microsoft Q&HDInsight] (/Answers/topics/azure-hdinsight.html
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Ha Apache-technológiát használ, lehet, hogy az Apache-projekt webhelyein [https://apache.org](https://apache.org) , például a [Apache Hadoop](https://hadoop.apache.org/) webhelyen tud segítséget találni.

> [!IMPORTANT]
> Az Ubuntu-lemezképek elérhetővé válnak az új HDInsight-fürt létrehozásakor a közzétételtől számított 3 hónapon belül. Január 2019-ig a fürtök (beleértve az Edge-csomópontokat is) **nem** automatikus javítással működnek. Az ügyfeleknek parancsfájl-műveleteket vagy más mechanizmusokat kell használniuk a futó fürtök javításához.  További információ: operációsrendszer- [javítás a HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Peremhálózati csomópont hozzáadása meglévő fürthöz

Ebben a szakaszban egy Resource Manager-sablon használatával ad hozzá egy peremhálózati csomópontot egy meglévő HDInsight-fürthöz.  A Resource Manager-sablon a [githubon](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/)érhető el. A Resource Manager-sablon egy parancsfájl-műveletet hív meg, amely a következő helyen található: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh . A parancsfájl nem hajt végre műveleteket.  Ez egy Resource Manager-sablonban való hívási parancsfájl-művelet bemutatását mutatja be.

1. Válassza ki az alábbi rendszerképet az Azure-ba való bejelentkezéshez, majd nyissa meg a Azure Resource Manager sablont a Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Konfigurálja a következő tulajdonságokat:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|Válassza ki a fürt létrehozásához használt Azure-előfizetést.|
    |Erőforráscsoport|Válassza ki a meglévő HDInsight-fürthöz használt erőforráscsoportot.|
    |Hely|Válassza ki a meglévő HDInsight-fürt helyét.|
    |Fürt neve|Adja meg egy meglévő HDInsight-fürt nevét.|

1. Győződjön meg **arról, hogy elfogadom a fenti feltételeket és kikötéseket**, majd válassza a  **vásárlás** lehetőséget a peremhálózati csomópont létrehozásához.

> [!IMPORTANT]  
> Ügyeljen arra, hogy kiválassza a meglévő HDInsight-fürthöz tartozó Azure-erőforráscsoportot.  Ellenkező esetben a következő hibaüzenet jelenik meg: "nem lehet végrehajtani a kért műveletet a beágyazott erőforráson. A (z &lt; ) "ClusterName> szülő erőforrás nem található."

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Peremhálózati csomópont hozzáadása fürt létrehozásakor

Ebben a szakaszban egy Resource Manager-sablon használatával HDInsight-fürtöt hoz létre egy peremhálózati csomóponttal.  A Resource Manager-sablon az [Azure gyorsindítási sablonok galériájában](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)található. A Resource Manager-sablon egy parancsfájl-műveletet hív meg, amely a következő helyen található: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh . A parancsfájl nem hajt végre műveleteket.  Ez egy Resource Manager-sablonban való hívási parancsfájl-művelet bemutatását mutatja be.

1. Ha még nem rendelkezik ilyennel, hozzon létre egy HDInsight-fürtöt.  Lásd: [a Hadoop használatának első lépései a HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

1. Válassza ki az alábbi rendszerképet az Azure-ba való bejelentkezéshez, majd nyissa meg a Azure Resource Manager sablont a Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Konfigurálja a következő tulajdonságokat:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|Válassza ki a fürt létrehozásához használt Azure-előfizetést.|
    |Erőforráscsoport|Hozzon létre egy új erőforráscsoportot a fürthöz.|
    |Hely|Válasszon egy helyet az erőforráscsoportnak.|
    |Fürt neve|Adja meg a létrehozandó új fürt nevét.|
    |Fürt bejelentkezési felhasználóneve|Adja meg a Hadoop HTTP-felhasználónevét.  Az alapértelmezett név az **admin**.|
    |Fürt bejelentkezési jelszava|Adja meg a Hadoop HTTP-felhasználói jelszavát.|
    |SSH-Felhasználónév|Adja meg az SSH-Felhasználónév nevét. Az alapértelmezett név a **sshuser**.|
    |SSH-jelszó|Adja meg az SSH-felhasználó jelszavát.|
    |Parancsfájl telepítése művelet|Tartsa meg az alapértelmezett értéket a jelen cikkben leírtak szerint.|

    Egyes tulajdonságok a következő sablonban lettek hardcoded: fürt típusa, fürt munkavégző csomópontok száma, Edge-csomópont mérete és peremhálózati csomópont neve.

1. Győződjön meg **arról, hogy elfogadom a fenti feltételeket és kikötéseket**, majd válassza a  **vásárlás** lehetőséget a fürt peremhálózati csomóponttal való létrehozásához.

## <a name="add-multiple-edge-nodes"></a>Több peremhálózati csomópont hozzáadása

Több peremhálózati csomópontot is hozzáadhat egy HDInsight-fürthöz.  A többoldalas csomópontok konfigurálása csak Azure Resource Manager sablonok használatával végezhető el.  A cikk elején tekintse meg a sablon mintáját.  Frissítse a **targetInstanceCount** , hogy tükrözze a létrehozni kívánt peremhálózati csomópontok számát.

## <a name="access-an-edge-node"></a>Hozzáférés egy peremhálózati csomóponthoz

Az Edge Node SSH-végpontja &lt;> EdgeNodeName. &lt; ClusterName> – ssh.azurehdinsight.net:22.  Például: new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

A peremhálózati csomópont alkalmazásként jelenik meg a Azure Portalon.  A portálon megtekintheti a peremhálózati csomópont SSH használatával való eléréséhez szükséges információkat.

**Az Edge Node SSH-végpontjának ellenőrzése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight fürtöt egy peremhálózati csomóponttal.
3. Válassza az **alkalmazások** lehetőséget. Ekkor látnia kell a peremhálózati csomópontot.  Az alapértelmezett név a **New-élcsomópontok**.
4. Válassza ki a peremhálózati csomópontot. Meg kell tekintenie az SSH-végpontot.

**A kaptár használata a peremhálózati csomóponton**

1. Csatlakozzon az élcsomóponthoz SSH használatával. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Miután az SSH-val csatlakozott az Edge-csomóponthoz, a következő parancs használatával nyissa meg a kaptár-konzolt:

    ```console
    hive
    ```

3. Futtassa a következő parancsot a kaptár-táblák megjelenítéséhez a fürtben:

    ```hiveql
    show tables;
    ```

## <a name="delete-an-edge-node"></a>Peremhálózati csomópont törlése

A Azure Portalból törölhet egy peremhálózati csomópontot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a HDInsight fürtöt egy peremhálózati csomóponttal.
3. Válassza az **alkalmazások** lehetőséget. Ekkor megjelenik a peremhálózati csomópontok listája.  
4. Kattintson a jobb gombbal a törölni kívánt peremhálózati csomópontra, majd válassza a **Törlés** lehetőséget.
5. Válassza az **Igen** lehetőséget a megerősítéshez.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan adhat hozzá egy Edge-csomópontot, és hogyan érheti el a peremhálózati csomópontot. További tudnivalókért olvassa el a következő cikket:

* [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md): Megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat a fürtjeire.
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): megtudhatja, hogyan helyezhet üzembe egy közzé nem tett HDInsight alkalmazást a HDInsight.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](/rest/api/hdinsight/hdinsight-application) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Linux-alapú Apache Hadoop-fürtök létrehozása a HDInsight Resource Manager-sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md): megtudhatja, hogyan hívhat Resource Manager-sablonokat HDInsight-fürtök létrehozásához.