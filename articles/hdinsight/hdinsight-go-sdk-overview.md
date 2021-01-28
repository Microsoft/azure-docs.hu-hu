---
title: Az Azure HDInsight SDK for go
description: Az Azure HDInsight SDK for Go és a Apache Hadoop-fürtök használatára szolgáló anyagminta
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18, devx-track-azurecli
ms.date: 01/03/2020
ms.openlocfilehash: dc8528c40fe7e3b32e2f38152afc8abb0ae18ba5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945987"
---
# <a name="hdinsight-sdk-for-go-preview"></a>HDInsight SDK for go (előzetes verzió)

## <a name="overview"></a>Áttekintés
Az HDInsight SDK for go olyan osztályokat és függvényeket biztosít, amelyek lehetővé teszik a HDInsight-fürtök kezelését. A művelet magában foglalja a létrehozásához, törléséhez, frissítéséhez, listázásához, átméretezéséhez, parancsfájl-műveletek végrehajtásához, figyeléséhez, a HDInsight-fürtök tulajdonságainak beolvasásához és egyebekhez.

> [!NOTE]  
>Az SDK GoDoc-segédanyaga itt is [elérhető](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight).

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

* Egy [ `go get` eszköz](https://github.com/golang/go/wiki/GoGetTools).
* [Ugrás](https://golang.org/dl/).

## <a name="sdk-installation"></a>SDK-telepítés

A GOPATH-helyről futtassa a következőt: `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Hitelesítés

Az SDK-t először hitelesítenie kell az Azure-előfizetésével.  Az alábbi példát követve hozzon létre egy szolgáltatásnevet, és használja hitelesítésre. Ennek elvégzése után az a egy példánya lesz `ClustersClient` , amely számos funkciót tartalmaz (az alábbi részekben ismertetett), amelyek felügyeleti műveletek végrehajtásához használhatók.

> [!NOTE]  
> Az alábbi példán kívül más módszerekkel is elvégezheti a hitelesítést, amelyek esetleg jobban illeszkednek az igényeihez. Az összes függvényt itt találja: [a hitelesítési függvények a go nyelvhez készült Azure SDK](/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Hitelesítési példa egyszerű szolgáltatásnév használatával

Először jelentkezzen be [Azure Cloud Shellra](https://shell.azure.com/bash). Győződjön meg arról, hogy jelenleg az előfizetést használja, amelyben a szolgáltatásnevet létre kívánja hozni.

```azurecli-interactive
az account show
```

Az előfizetési adatok JSON-ként jelennek meg.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Ha nem jelentkezett be a megfelelő előfizetésbe, válassza ki a megfelelőt a futtatásával: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Ha még nem regisztrálta a HDInsight erőforrás-szolgáltatót egy másik függvény (például egy HDInsight-fürt létrehozásával a Azure Portal), ezt egyszer kell megtennie a hitelesítéshez. Ezt a [Azure Cloud Shell](https://shell.azure.com/bash) a következő parancs futtatásával teheti meg:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Ezután válassza ki az egyszerű szolgáltatásnév nevét, és hozza létre a következő paranccsal:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Az egyszerű szolgáltatásnév adatai JSON-ként jelennek meg.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Másolja az alábbi kódrészletet, és töltse ki a,,, `TENANT_ID` `CLIENT_ID` `CLIENT_SECRET` és `SUBSCRIPTION_ID` karakterláncokat a JSON-ből, amelyet a parancs futtatása után adott vissza az egyszerű szolgáltatásnév létrehozásához.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Fürtkezelés

> [!NOTE]  
> Ez a szakasz feltételezi, hogy már hitelesítette és létrehozta a `ClusterClient` példányt, és tárolja azt egy nevű változóban `client` . A hitelesítéshez és a beszerzéséhez `ClusterClient` a fenti hitelesítési szakaszban talál útmutatást.

### <a name="create-a-cluster"></a>Fürt létrehozása

A meghívásával új fürtöt hozhat létre `client.Create()` . 

#### <a name="example"></a>Példa

Ez a példa azt szemlélteti, hogyan lehet létrehozni egy [Apache Spark](https://spark.apache.org/) fürtöt két fő csomóponttal és egy feldolgozó csomóponttal.

> [!NOTE]  
> Először létre kell hoznia egy erőforráscsoportot és egy Storage-fiókot az alább leírtak szerint. Ha már létrehozta ezeket, akkor kihagyhatja ezeket a lépéseket.

##### <a name="creating-a-resource-group"></a>Erőforráscsoport létrehozása

A [Azure Cloud Shell](https://shell.azure.com/bash) használatával létrehozhat egy erőforráscsoportot a futtatásával

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Tárfiók létrehozása

A [Azure Cloud Shell](https://shell.azure.com/bash) a futtatásával hozhatja létre a Storage-fiókot:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Ezután futtassa a következő parancsot a Storage-fiók kulcsainak lekéréséhez (a fürt létrehozásához szükség lesz rá):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
Az alábbi go kódrészlet egy Spark-fürtöt hoz létre két fő csomóponttal és egy feldolgozói csomóponttal. Adja meg az üres változókat a megjegyzésekben leírtak szerint, és szabadítson fel más paramétereket az igényeinek megfelelően.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Fürt részleteinek beolvasása

Egy adott fürt tulajdonságainak beolvasása:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Példa

Ezzel ellenőrizheti `get` , hogy sikeresen létrehozta-e a fürtöt.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

A kimenetnek az alábbihoz hasonlóan kell kinéznie:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Fürtök listázása

#### <a name="list-clusters-under-the-subscription"></a>Az előfizetés alá tartozó fürtök listázása

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Fürtök listázása erőforráscsoport szerint

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> Mindkettőt `List()` és `ListByResourceGroup()` egy struct-t ad vissza `ClusterListResultPage` . A következő oldal beszerzéséhez hívhatja a következőt: `Next()` . Ez csak akkor ismételhető `ClusterListResultPage.NotDone()` `false` meg, ha a visszaadja az alábbi példában látható módon.

#### <a name="example"></a>Példa

A következő példa az aktuális előfizetés összes fürtjének tulajdonságait kinyomtatja:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Fürt törlése

Fürt törlése:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Fürthöz tartozó címkék frissítése

Egy adott fürt címkéit például a következőképpen frissítheti:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Példa

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Fürt átméretezése

Egy adott fürthöz tartozó munkavégző csomópontok számának átméretezéséhez egy új méretet kell megadnia:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Fürtmonitorozás

A HDInsight Management SDK a fürtök figyelésének kezelésére is használható az Operations Management Suite (OMS) használatával.

Hasonlóan ahhoz, ahogy a `ClusterClient` felügyeleti műveletekhez létrehozott, létre kell hoznia egy, a `ExtensionClient` figyelési műveletekhez használni kívánt műveletet. A fenti hitelesítési szakasz befejezése után a következőhöz hasonló módon hozhat létre `ExtensionClient` :

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Az alábbi figyelési példák feltételezik, hogy már inicializálta a `ExtensionClient` nevűt `extClient` , és beállította a `Authorizer` fentebb látható módon.

### <a name="enable-oms-monitoring"></a>OMS-figyelés engedélyezése

> [!NOTE]  
> A OMS-figyelés engedélyezéséhez meglévő Log Analytics munkaterülettel kell rendelkeznie. Ha még nem hozott létre egyet, itt megtudhatja, hogyan teheti meg ezt: [hozzon létre egy log Analytics munkaterületet a Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

A OMS-figyelés engedélyezése a fürtön:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>OMS-figyelés állapotának megtekintése

A fürt OMS állapotának lekérése:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>OMS-figyelés letiltása

A OMS letiltása a fürtön:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Parancsfájlok műveletei

A HDInsight egy parancsfájl-műveletek nevű konfigurációs függvényt biztosít, amely egyéni parancsfájlokat hív meg a fürt testreszabásához.

> [!NOTE]  
> A parancsfájl-műveletek használatáról további információt itt talál: [Linux-alapú HDInsight-fürtök testreszabása parancsfájl-műveletek használatával](./hdinsight-hadoop-customize-cluster-linux.md)

### <a name="execute-script-actions"></a>Parancsfájl-műveletek végrehajtása

Parancsfájl-műveleteket futtathat egy adott fürtön, például:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

A "parancsfájl törlése művelet" és a "megőrzött parancsfájl-műveletek listázása" művelet esetén létre kell hoznia egy `ScriptActionsClient` , a `ClusterClient` felügyeleti műveletekhez való használathoz hasonlóan. Miután elvégezte a fenti hitelesítési szakaszt, a következőhöz hasonló módon hozhat létre `ScriptActionsClient` :

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Az alábbi parancsfájl-műveletek feltételezik, hogy már inicializálta a hívott nevet, `ScriptActionsClient` `scriptActionsClient` és beállította a `Authorizer` fentebb látható módon.

### <a name="delete-script-action"></a>Parancsfájl-művelet törlése

Megadott megőrzött parancsfájl törlési művelete egy adott fürtön:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Megőrzött parancsfájl-műveletek listázása

> [!NOTE]  
> Mindkettő `ListByCluster()` egy struct-t ad vissza `ScriptActionsListPage` . A következő oldal beszerzéséhez hívhatja a következőt: `Next()` . Ez csak akkor ismételhető `ClusterListResultPage.NotDone()` `false` meg, ha a visszaadja az alábbi példában látható módon.

A megadott fürt összes megőrzött parancsfájl-műveletének listázása:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Példa

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="list-all-scripts-execution-history"></a>Az összes parancsfájl végrehajtási előzményeinek listázása

Ehhez a művelethez létre kell hoznia egy `ScriptExecutionHistoryClient` -t, hasonlóan ahhoz, ahogyan a `ClusterClient` a felügyeleti műveletekhez való használatra készült. Miután elvégezte a fenti hitelesítési szakaszt, a következőhöz hasonló módon hozhat létre `ScriptActionsClient` :

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Az alábbi feltételezések azt feltételezik, hogy már inicializálta a `ScriptExecutionHistoryClient` hívását `scriptExecutionHistoryClient` , és a `Authorizer` fentebb látható módon beállította azt.

A megadott fürt összes parancsfájl-végrehajtási előzményeinek listázása:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Példa

Ez a példa az összes korábbi parancsfájl-végrehajtás részleteit nyomtatja ki.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a [GoDoc-anyagmintával](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight). A GoDocs az SDK összes funkciója számára biztosít dokumentációt.
