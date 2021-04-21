---
title: Fürtök Azure HDInsight testreszabása szkriptműveletekkel
description: Egyéni összetevők hozzáadása HDInsight-fürtökhöz szkriptműveletek használatával. A szkriptműveletek olyan Bash-szkriptek, amelyek a fürtkonfiguráció testreszabásához használhatók. Vagy adjon hozzá további szolgáltatásokat és segédprogramokat, például a Hue-t, a Solrt vagy az R-t.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, contperf-fy21q2
ms.date: 03/09/2021
ms.openlocfilehash: d5500c04b4299c215eba843530dc84932fa10894
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775042"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Fürtök Azure HDInsight testreszabása szkriptműveletekkel

Azure HDInsight szkriptműveletek nevű  konfigurációs metódust biztosít, amely egyéni szkripteket hív meg a fürt testreszabásához. Ezek a szkriptek további összetevők telepítéséhez és a konfigurációs beállítások módosításához használhatók. A szkriptműveletek a fürt létrehozása során és után is használhatók.

A szkriptműveletek HDInsight-alkalmazásként Azure Marketplace közzé a Azure Marketplace is. A HDInsight-alkalmazásokkal kapcsolatos további információkért [lásd: HDInsight-alkalmazások közzététele a Azure Marketplace.](hdinsight-apps-publish-applications.md)

## <a name="understand-script-actions"></a>A szkriptműveletek

A szkript művelet egy Olyan Bash-szkript, amely egy HDInsight-fürt csomópontjain fut. A szkriptműveletek jellemzői és jellemzői a következők:

- A Bash-szkript URI-jának (a fájl elérésének helye) elérhetőnek kell lennie a HDInsight erőforrás-szolgáltatóról és a fürtről.
- A lehetséges tárolási helyek a következők:

   - Normál (nem ESP-) fürtök esetén:
     - Blob egy Azure Storage-fiókban, amely vagy a HDInsight-fürt elsődleges vagy további tárfiókja. A HDInsight mindkét tárfióktípushoz hozzáférést kap a fürt létrehozása során.
    
       > [!IMPORTANT]  
       > Ne váltsa át a tárkulcsot ezen az Azure Storage-fiókon, mivel ez az ott tárolt szkriptekkel való további szkriptműveleteket sikertelen lesz.

     - Data Lake Storage Gen1: A HDInsight szolgáltatásnévnek a használatával fér hozzá Data Lake Storage olvasási hozzáféréssel kell a szkripthez. A Bash-szkript URI formátuma `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` : . 

     - Data Lake Storage Gen2 szkriptműveletekhez nem ajánlott a használata. `abfs://` A nem támogatott a Bash-szkript URI-ja esetén. `https://` Az URI-k lehetségesek, de ezek nyilvános hozzáféréssel és a HDInsight erőforrás-szolgáltató számára megnyitott tűzfallal működnek, ezért nem ajánlottak.

     - Egy nyilvános fájlmegosztási szolgáltatás, amely elérési `https://` utakon keresztül érhető el. Ilyen például az Azure Blob, a GitHub vagy a OneDrive. Például az URI-ket lásd: [Példa szkriptműveleti parancsfájlok.](#example-script-action-scripts)

  - Az ESP-t támogató fürtök esetén `wasb://` a vagy a vagy az `wasbs://` `http[s]://` URI támogatott.

- A szkriptműveletek csak bizonyos csomóponttípusokon futtathatók. Ilyenek például az főcsomópontok vagy a munkavégző csomópontok.
- A szkriptműveletek megmaradhatnak vagy *alkalmi jellegűek is.*

  - A megőrzött szkriptműveletek nevének egyedinek kell lennie. A megőrzött szkriptek a fürthöz méretezési műveletekkel hozzáadott új munkavégző csomópontok testreszabására használhatók. A megőrzött szkriptek a méretezési műveletek során módosításokat is alkalmazhatnak egy másik csomóponttípusra. Ilyen például az fejcsomópont.
  - *Az alkalmi szkriptek* nem maradnak meg. A fürt létrehozása során használt szkriptműveletek automatikusan megmaradnak. A szkript futtatása után a rendszer nem alkalmazza őket a fürthöz hozzáadott munkavégző csomópontokra. Ezután előléptethet egy *alkalmi* szkriptet egy megőrzött szkriptbe, vagy lefokozhat egy megőrzött szkriptet *alkalmi szkriptként.* A sikertelen szkriptek nem lesznek megőrzve, még akkor sem, ha ön kifejezetten jelzi, hogy annak kell lennie.

- A szkriptműveletek elfogadják a szkript által a végrehajtás során használt paramétereket.
- A szkriptműveletek gyökérszintű jogosultságokkal futnak a fürtcsomópontokon.
- A szkriptműveletek a következő szolgáltatásokon, Azure Portal, Azure PowerShell Azure CLI-n vagy a HDInsight .NET SDK-n keresztül használhatók.
- A virtuális gépen található szolgáltatásfájlokat eltávolító vagy módosító szkriptműveletek hatással lehetnek a szolgáltatás állapotára és rendelkezésre állására.

A fürt megőrzi az összes futtatott szkript előzményeit. Az előzmények segítenek, ha meg kell találnia egy előléptetési vagy lefokozási műveletekhez szükséges szkript azonosítóját.

> [!IMPORTANT]  
> A szkriptművelet által végrehajtott módosításokat nem lehet automatikusan visszavonni. Manuálisan fordítsa vissza a módosításokat, vagy adjon meg egy parancsfájlt, amely megfordítja azokat.

## <a name="permissions"></a>Engedélyek

Tartományhoz csatlakozott HDInsight-fürt esetén két Apache Ambari-engedélyre van szükség, ha szkriptműveleteket használ a fürtön:

* **AMBARI. FUTTASSA \_ AZ EGYÉNI \_ PARANCSOT.** Az Ambari-rendszergazda szerepkör alapértelmezés szerint rendelkezik ezzel az engedéllyel.
* **FÜRT. FUTTASSA \_ AZ EGYÉNI \_ PARANCSOT.** Alapértelmezés szerint a HDInsight-fürt rendszergazdája és az Ambari-rendszergazda is rendelkezik ezzel az engedéllyel.

További információ a tartományhoz csatlakozott HDInsight-engedélyek kezelésével kapcsolatban: [HDInsight-fürtök](./domain-joined/apache-domain-joined-manage.md)kezelése a Enterprise Security Package.

## <a name="access-control"></a>Hozzáférés-vezérlés

Ha nem Ön az Azure-előfizetés rendszergazdája vagy tulajdonosa, a fiókjának legalább hozzáféréssel kell lennie a HDInsight-fürtöt tartalmazó `Contributor` erőforráscsoporthoz.

Ha valaki legalább Közreműködői hozzáféréssel rendelkezik az Azure-előfizetéshez, korábban regisztrálnia kell a szolgáltatót. A szolgáltató regisztrálása akkor történik meg, ha egy felhasználó közreműködői hozzáféréssel rendelkezik az előfizetéshez, és létrehoz egy erőforrást. Erőforrás létrehozása nélkül: Szolgáltató [regisztrálása a REST használatával.](/rest/api/resources/providers#Providers_Register)

További információ a hozzáférés-kezelésről:

- [Bevezetés a hozzáférés-kezelés Azure Portalon történő használatába](../role-based-access-control/overview.md)
- [Azure-szerepkörök hozzárendelése az Azure-előfizetés erőforrásaihoz való hozzáférés kezeléséhez](../role-based-access-control/role-assignments-portal.md)

## <a name="methods-for-using-script-actions"></a>Szkriptműveletek használatának módszerei

Konfigurálhat egy parancsfájl-műveletet, amely a fürt első létrehozásakor fut, vagy egy meglévő fürtön futtatja azt.

### <a name="script-action-in-the-cluster-creation-process"></a>Szkript művelet a fürt létrehozási folyamatában

A fürtök létrehozása során használt szkriptműveletek némileg eltérnek a meglévő fürtökön futtatott szkriptműveletektől:

- A szkript automatikusan megmarad.
- A szkript hibája a fürt létrehozási folyamatának sikertelenségéhez vezethet.

Az alábbi ábra bemutatja, hogy mikor fut a szkript művelet a létrehozási folyamat során:


:::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png" alt-text="Szakaszok a fürt létrehozása során" border="false":::

A szkript a HDInsight konfigurálása közben fut. A szkript párhuzamosan fut a fürt összes megadott csomópontján. Gyökér szintű jogosultságokkal fut a csomópontokon.

Olyan műveleteket is futtathat, mint a szolgáltatások leállítása és indítása, beleértve az Apache Hadoophoz kapcsolódó szolgáltatásokat is. Ha leállítja a szolgáltatásokat, a szkript befejezése előtt győződjön meg arról, hogy az Ambari és a Hadoophoz kapcsolódó egyéb szolgáltatások futnak. Ezek a szükséges szolgáltatások határozzák meg a fürt állapotát és állapotát a létrehozásuk során.

A fürtök létrehozása során egyszerre több szkriptműveletet is használhat. Ezeket a szkripteket a rendszer a megadott sorrendben hívja meg.

> [!IMPORTANT]  
> A szkriptműveleteknek 60 percen belül be kell fejezniük, vagy időkorrektaik vannak. A fürtök kiépítése során a szkript más beállítási és konfigurációs folyamatokkal egyidejűleg fut. Az erőforrásokért, például a processzoridőért vagy a hálózati sávszélességért való verseny miatt a szkript hosszabb ideig tarthat, mint a fejlesztési környezetben.
>
> A szkript futtatásához szükséges idő minimalizálása érdekében kerülje az olyan feladatokat, mint az alkalmazások letöltése és fordítása a forrásból. Alkalmazások előkomponense és a bináris fájl tárolása az Azure Storage-ban.

### <a name="script-action-on-a-running-cluster"></a>Szkript művelet egy futó fürtön

Egy már futó fürt szkripthibája nem váltja ki automatikusan hibás állapotba a fürtöt. A szkript befejezése után a fürtnek futó állapotba kell visszatérnie. Még ha a fürt futó állapotban is van, előfordulhat, hogy a sikertelen szkript hibásan működik. Előfordulhat például, hogy egy szkript törli a fürthöz szükséges fájlokat.

A szkriptműveletek gyökér szintű jogosultságokkal futnak. Mielőtt alkalmazza azt a fürtön, győződjön meg arról, hogy tisztában van a parancsfájlokkal.

Amikor parancsfájlt alkalmaz egy fürtre, a  fürt állapota Fut állapotról **Elfogadva állapotra változik.** Ezután a **HDInsight-konfigurációra** módosul, és végül vissza a **Futtatás** a sikeres szkriptekért beállításra. A szkript állapota a szkript műveletelőzményében van naplózva. Ezek az információk jelzik, hogy a szkript sikeres vagy sikertelen volt-e. A `Get-AzHDInsightScriptActionHistory` PowerShell-parancsmag például egy szkript állapotát jeleníti meg. A következő szöveghez hasonló információkat ad vissza:

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> Ha módosítja a fürt felhasználóját, rendszergazdáját és jelszavát a fürt létrehozása után, előfordulhat, hogy a fürtön futtatott szkriptműveletek sikertelenek lesznek. Ha vannak olyan megőrzött szkriptműveletek, amelyek munkavégző csomópontokat céloznak meg, ezek a szkriptek a fürt skálázása során meghiúsulhatnak.

## <a name="example-script-action-scripts"></a>Példa szkript-művelet szkriptek

A szkriptműveleti szkriptek a következő segédprogramokkal használhatók:

* Azure Portal
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

A HDInsight szkripteket biztosít a következő összetevők HDInsight-fürtökön való telepítéséhez:

| Name | Script |
| --- | --- |
| Azure Storage-fiók hozzáadása |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Lásd: [További tárfiókok hozzáadása a HDInsighthoz.](hdinsight-hadoop-add-storage.md) |
| A Hue telepítése |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Lásd: [Install and use Hue on HDInsight Hadoop clusters (A Hue telepítése és használata HDInsight Hadoop-fürtökön).](hdinsight-hadoop-hue-linux.md) |
| Hive-kódtárak előzetes betöltése |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Lásd: [Egyéni kódtárak Apache Hive HDInsight-fürt létrehozásakor.](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="script-action-during-cluster-creation"></a>Parancsprogram-művelet a fürt létrehozása során

Ez a szakasz a szkriptműveletek HDInsight-fürtök létrehozásakor való használatának különböző módjait ismerteti.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Szkript művelet használata a fürt létrehozásakor a Azure Portal

1. Hozzon létre egy fürtöt a Linux-alapú fürtök létrehozása a [HDInsightban](hdinsight-hadoop-create-linux-clusters-portal.md)a következő Azure Portal. A Konfiguráció **és díjszabás lapon** válassza a + Szkript művelet hozzáadása **lehetőséget.**

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png" alt-text="Azure Portal fürt szkript művelete":::

1. A Select __a script (Szkript kiválasztása) bejegyzéssel__ előre meghatározott szkriptet választhat ki. Ha egyéni szkriptet használ, válassza az Egyéni __lehetőséget.__ Ezután adja meg __a szkript nevét__ és __Bash-szkriptének__ URI-ját.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="Szkript hozzáadása a select script űrlapon":::

   Az alábbi táblázat az űrlap elemeit ismerteti:

   | Tulajdonság | Érték |
   | --- | --- |
   | Szkript kiválasztása | Ha saját szkriptet használ, válassza az Egyéni __lehetőséget.__ Ellenkező esetben válasszon egyet a megadott szkriptek közül. |
   | Name |Adja meg a szkript művelet nevét. |
   | Bash-szkript URI-ja |Adja meg a szkript URI-ját. |
   | Head/Worker/ZooKeeper |Adja meg a csomópontokat, amelyeken a szkript fut: **Head (Fej),** Worker (Feldolgozó) vagy  **ZooKeeper (ZooKeeper).** |
   | Paraméterek |Adja meg a paramétereket, ha a szkriptnek szüksége van rá. |

   A __Parancsfájl megőrzése műveletbejegyzéssel__ győződjön meg arról, hogy a szkript alkalmazva van a skálázás során.

1. A __szkript mentéshez__ válassza a Létrehozás lehetőséget. Ezután az __+__ Új elküldése lehetőség használatával hozzáadhat egy másik szkriptet.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png" alt-text="HDInsight – több szkriptművelet":::

   Ha végzett a szkriptek hozzáadásával, térjen vissza a **Konfiguráció és díjszabás lapra.**

1. A fürt létrehozásának további lépéseit a szokásos módon kell végrehajtani.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Szkript művelet használata Azure Resource Manager sablonokból

A szkriptműveletek a Azure Resource Manager is használhatók. Példa: [HDInsight Linux-fürt létrehozása és szkript-művelet futtatása.](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/)

Ebben a példában a szkript műveletet a következő kóddal adhatja hozzá:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

További információ a sablonok üzembe helyezéséről:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/templates/deploy-powershell.md)
- [Erőforrások üzembe helyezése Resource Manager sablonokkal és az Azure CLI-val](../azure-resource-manager/templates/deploy-cli.md)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Szkript művelet használata a fürt létrehozásakor Azure PowerShell

Ebben a szakaszban az [Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) parancsmag használatával fog szkripteket meghívni a fürt testreszabásához. Mielőtt elkezdené, győződjön meg arról, hogy telepíti és konfigurálja a Azure PowerShell. Ezeknek a PowerShell-parancsoknak a használhatja az [AZ modult.](/powershell/azure/)

A következő szkript bemutatja, hogyan alkalmazhat parancsfájl-műveletet, amikor fürtöt hoz létre a PowerShell használatával:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

A fürt létrehozása több percig is eltarthat.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Szkript művelet használata a fürt HDInsight .NET SDK-ból való létrehozása során

A HDInsight .NET SDK olyan ügyfélkódtárakat biztosít, amelyek megkönnyítik a HDInsight .NET-alkalmazásból való használatával való munkát. Kódmintát a Script [Actions (Szkriptműveletek) tartalmaz.](/dotnet/api/overview/azure/hdinsight#script-actions)

## <a name="script-action-to-a-running-cluster"></a>Szkript művelet futtatása egy futó fürtön

Ez a szakasz a szkriptműveletek futó fürtön való alkalmazását ismerteti.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Szkript művelet alkalmazása futó fürtre a Azure Portal

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) és keresse meg a fürtöt.

1. Az alapértelmezett nézet Beállítások alatt **válassza** a **Szkriptműveletek lehetőséget.**

1. A Szkriptműveletek lap **tetején** válassza az **+ Új elküldése lehetőséget.**

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png" alt-text="Szkript hozzáadása futó fürthöz":::

1. A Select __a script (Szkript kiválasztása) bejegyzéssel__ előre meghatározott szkriptet választhat ki. Ha egyéni szkriptet használ, válassza az Egyéni __lehetőséget.__ Ezután adja meg __a szkript nevét__ és __Bash-szkriptének__ URI-ját.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="Szkript hozzáadása a select script űrlapon":::

   Az alábbi táblázat az űrlap elemeit ismerteti:

   | Tulajdonság | Érték |
   | --- | --- |
   | Szkript kiválasztása | Ha saját szkriptet használ, válassza az __egyéni lehetőséget.__ Ellenkező esetben válasszon ki egy megadott szkriptet. |
   | Name |Adja meg a szkript művelet nevét. |
   | Bash-szkript URI-ja |Adja meg a szkript URI-ját. |
   | Head/Worker/Zookeeper |Adja meg a csomópontokat, amelyeken a szkript fut: **Head (Fej),** Worker (Feldolgozó) vagy  **ZooKeeper (ZooKeeper).** |
   | Paraméterek |Adja meg a paramétereket, ha a szkriptnek szüksége van rá. |

   A __Parancsfájl megőrzésének műveletbejegyzése__ segítségével győződjön meg arról, hogy a szkript alkalmazva van a skálázás során.

1. Végül kattintson a **Létrehozás** gombra a szkript fürtön való alkalmazáshoz.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Szkript művelet alkalmazása futó fürtre a Azure PowerShell

Ezeknek a PowerShell-parancsoknak a használhatja az [AZ modult.](/powershell/azure/) Az alábbi példa bemutatja, hogyan alkalmazhat parancsfájl-műveletet egy futó fürtre:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

A művelet befejezése után az alábbi szöveghez hasonló információkat kap:

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Szkript művelet alkalmazása futó fürtre az Azure CLI-ről

Mielőtt elkezdené, telepítse és konfigurálja az Azure CLI-t. Győződjön meg arról, hogy a legújabb verzióval van. További információ: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

1. Hitelesítés az Azure-előfizetésben:

   ```azurecli
   az login
   ```

1. Parancsfájl-művelet alkalmazása futó fürtre:

   ```azurecli
   az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
   ```

   Érvényes szerepkörök: `headnode` , `workernode` , , `zookeepernode` `edgenode` . Ha a szkriptet több csomóponttípusra kell alkalmazni, a szerepköröket szóközre kell elválasztani. Például: `--roles headnode workernode`.

   A szkript megőrzéséhez adja hozzá a következőt: `--persist-on-success` . A szkriptet később is megőrzheti a `az hdinsight script-action promote` használatával.

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Parancsfájl-művelet alkalmazása futó fürtre az REST API

Lásd: [Fürt REST API a Azure HDInsight.](/rest/api/hdinsight/hdinsight-cluster)

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Szkript művelet alkalmazása futó fürtre a HDInsight .NET SDK-ból

Példa szkriptek fürtre való alkalmazásához a .NET SDK használatával: Parancsfájl-művelet alkalmazása futó [Linux-alapú HDInsight-fürtön.](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)

## <a name="view-history-and-promote-and-demote-script-actions"></a>Előzmények megtekintése és szkriptműveletek le- és lefokozása

### <a name="the-azure-portal"></a>Azure Portal

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) és keresse meg a fürtöt.

1. Az alapértelmezett nézet Beállítások alatt **válassza** a **Szkriptműveletek lehetőséget.**

1. A fürt szkriptelőzményei a szkriptműveletek szakaszban megjelenik. Ez az információ tartalmazza a megőrzött szkriptek listáját. Az alábbi képernyőképen az látható, hogy a Solr-szkript fut ezen a fürtön. A képernyőképen nem vannak megőrzött szkriptek.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png" alt-text="A portál szkriptműveletei elküldik az előzményeket":::

1. Válasszon ki egy szkriptet az előzmények közül a **szkript Tulajdonságok** szakaszának megjelenítéséhez. A képernyő tetején újrafuttathatja vagy megléptetheti a szkriptet.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png" alt-text="Szkriptműveletek tulajdonságainak megléptetve":::

1. A műveletekhez a szkriptműveletek szakasz bejegyzéseitől jobbra található három pontra **(...**) is választhat.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png" alt-text="Megőrzött szkriptműveletek törlése":::

### <a name="azure-powershell"></a>Azure PowerShell

| Parancsmag | Függvény |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |A megőrzött szkriptműveletekkel kapcsolatos információk lekérése. Ez a parancsmag nem vonja vissza a szkript által végzett műveleteket, csak a megőrzött jelzőt távolítja el.|
| `Get-AzHDInsightScriptActionHistory` |A fürtre alkalmazott szkriptműveletek előzményeinek vagy egy adott szkript részleteinek lekérése. |
| `Set-AzHDInsightPersistedScriptAction` |`ad hoc`Parancsfájl-művelet megőrzött szkript műveletre való megléptetve. |
| `Remove-AzHDInsightPersistedScriptAction` |Lefokozás egy megőrzött szkript `ad hoc` műveletről műveletre. |

Az alábbi példaszkprogram bemutatja, hogyan lehet a parancsmagokkal előléptetni, majd lefokozást használni egy szkripthez.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Parancs | Leírás |
| --- | --- |
| [`az hdinsight script-action delete`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_delete) |Törli a fürt megadott megőrzött parancsfájl-műveletét. Ez a parancs nem vonja vissza a szkript által végzett műveleteket, csak a megőrzött jelzőt távolítja el.|
|[`az hdinsight script-action execute`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_execute)|Szkriptműveletek végrehajtása a megadott HDInsight-fürtön.|
| [`az hdinsight script-action list`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_list) |Felsorolja a megadott fürt összes megőrzött szkriptműveletét. |
|[`az hdinsight script-action list-execution-history`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_list_execution_history)|Felsorolja a megadott fürt összes szkript végrehajtási előzményeit.|
|[`az hdinsight script-action promote`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_promote)|Előlépteti a megadott alkalmi szkriptvégrehajtást egy megőrzött szkriptbe.|
|[`az hdinsight script-action show-execution-details`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_show_execution_details)|Lekérte az adott szkriptvégrehajtási azonosító szkriptvégrehajtási részleteit.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

A szkriptelőzmények fürtből való lekérésére, a parancsfájlok meg előléptethetők vagy lefokozhatóak a .NET SDK használatával való használatának példáiért lásd: Parancsfájl-művelet alkalmazása futó [Linux-alapú HDInsight-fürtön.](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)

> [!NOTE]  
> Ez a példa azt is bemutatja, hogyan telepíthet HDInsight-alkalmazásokat a .NET SDK használatával.

## <a name="next-steps"></a>Következő lépések

* [Szkript-műveletszk szkriptek fejlesztése a HDInsighthoz](hdinsight-hadoop-script-actions-linux.md)
* [További tároló hozzáadása EGY HDInsight-fürthöz](hdinsight-hadoop-add-storage.md)
* [Szkriptműveletek hibaelhárítása](troubleshoot-script-action.md)
