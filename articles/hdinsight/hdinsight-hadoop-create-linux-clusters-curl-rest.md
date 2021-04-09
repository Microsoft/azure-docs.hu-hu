---
title: Apache Hadoop-fürtök létrehozása az Azure REST API használatával – Azure
description: Megtudhatja, hogyan hozhat létre HDInsight-fürtöket Azure Resource Manager-sablonok Azure-REST API való elküldésével.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 12/10/2019
ms.openlocfilehash: fa5675104d9614e1bd917585ea537c92dddd88cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945781"
---
# <a name="create-apache-hadoop-clusters-using-the-azure-rest-api"></a>Apache Hadoop-fürtök létrehozása az Azure REST API használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Megtudhatja, hogyan hozhat létre HDInsight-fürtöt egy Azure Resource Manager sablonnal és az Azure REST API használatával.

Az Azure REST API lehetővé teszi, hogy felügyeleti műveleteket hajtson végre az Azure platformon üzemeltetett szolgáltatásokon, beleértve az új erőforrások, például a HDInsight-fürtök létrehozását.

> [!NOTE]  
> A jelen dokumentumban ismertetett lépések a [curl ( https://curl.haxx.se/) ](https://curl.haxx.se/) segédprogrammal kommunikálnak az Azure REST API használatával.

## <a name="create-a-template"></a>Sablon létrehozása

Azure Resource Manager sablonok olyan JSON-dokumentumok, amelyek egy **erőforráscsoportot** és az összes erőforrást (például HDInsight) írják le. Ez a sablon alapú megközelítés lehetővé teszi, hogy meghatározza a HDInsight szükséges erőforrásokat egy sablonban.

A következő JSON-dokumentum a sablon és a paraméterek fájljának egyesítése [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password) , amely egy Linux-alapú fürtöt hoz létre jelszó használatával az SSH-felhasználói fiók védelméhez.

   ```json
   {
       "properties": {
           "template": {
               "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
               "contentVersion": "1.0.0.0",
               "parameters": {
                   "clusterType": {
                       "type": "string",
                       "allowedValues": ["hadoop",
                       "hbase",
                       "storm",
                       "spark"],
                       "metadata": {
                           "description": "The type of the HDInsight cluster to create."
                       }
                   },
                   "clusterName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the HDInsight cluster to create."
                       }
                   },
                   "clusterLoginUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                       }
                   },
                   "clusterLoginPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "sshUserName": {
                       "type": "string",
                       "metadata": {
                           "description": "These credentials can be used to remotely access the cluster."
                       }
                   },
                   "sshPassword": {
                       "type": "securestring",
                       "metadata": {
                           "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                       }
                   },
                   "clusterStorageAccountName": {
                       "type": "string",
                       "metadata": {
                           "description": "The name of the storage account to be created and be used as the cluster's storage."
                       }
                   },
                   "clusterWorkerNodeCount": {
                       "type": "int",
                       "defaultValue": 4,
                       "metadata": {
                           "description": "The number of nodes in the HDInsight cluster."
                       }
                   }
               },
               "variables": {
                   "defaultApiVersion": "2015-05-01-preview",
                   "clusterApiVersion": "2015-03-01-preview"
               },
               "resources": [{
                   "name": "[parameters('clusterStorageAccountName')]",
                   "type": "Microsoft.Storage/storageAccounts",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('defaultApiVersion')]",
                   "dependsOn": [],
                   "tags": {

                   },
                   "properties": {
                       "accountType": "Standard_LRS"
                   }
               },
               {
                   "name": "[parameters('clusterName')]",
                   "type": "Microsoft.HDInsight/clusters",
                   "location": "[resourceGroup().location]",
                   "apiVersion": "[variables('clusterApiVersion')]",
                   "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                   "tags": {

                   },
                   "properties": {
                       "clusterVersion": "3.6",
                       "osType": "Linux",
                       "clusterDefinition": {
                           "kind": "[parameters('clusterType')]",
                           "configurations": {
                               "gateway": {
                                   "restAuthCredential.isEnabled": true,
                                   "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                   "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                               }
                           }
                       },
                       "storageProfile": {
                           "storageaccounts": [{
                               "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                               "isDefault": true,
                               "container": "[parameters('clusterName')]",
                               "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                           }]
                       },
                       "computeProfile": {
                           "roles": [{
                               "name": "headnode",
                               "targetInstanceCount": "2",
                               "hardwareProfile": {
                                   "vmSize": "{}" 
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           },
                           {
                               "name": "workernode",
                               "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                               "hardwareProfile": {
                                   "vmSize": "{}"
                               },
                               "osProfile": {
                                   "linuxOperatingSystemProfile": {
                                       "username": "[parameters('sshUserName')]",
                                       "password": "[parameters('sshPassword')]"
                                   }
                               }
                           }]
                       }
                   }
               }],
               "outputs": {
                   "cluster": {
                       "type": "object",
                       "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                   }
               }
           },
           "mode": "incremental",
           "Parameters": {
               "clusterName": {
                   "value": "newclustername"
               },
               "clusterType": {
                   "value": "hadoop"
               },
               "clusterStorageAccountName": {
                   "value": "newstoragename"
               },
               "clusterLoginUserName": {
                   "value": "admin"
               },
               "clusterLoginPassword": {
                   "value": "changeme"
               },
               "sshUserName": {
                   "value": "sshuser"
               },
               "sshPassword": {
                   "value": "changeme"
               }
           }
       }
   }
   ```

Ez a példa a jelen dokumentum lépéseiben használatos. Cserélje le a **Parameters (paraméterek** ) szakasz example *értékeit* a fürt értékeire.

> [!IMPORTANT]  
> A sablon a munkavégző csomópontok alapértelmezett számát (4) használja egy HDInsight-fürthöz. Ha több mint 32 feldolgozó csomópontot tervez, ki kell választania egy fő csomópont-méretet legalább 8 maggal és 14 GB RAM-mal.
>
> További információ a csomópontméretekről és a velük járó költségekről: [A HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetésbe

Kövesse az [Azure CLI használatának első](/cli/azure/get-started-with-az-cli2) lépései című cikkben ismertetett lépéseket, és kapcsolódjon az előfizetéséhez a `az login` paranccsal.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

> [!NOTE]  
> Ezek a lépések a szolgáltatásnév létrehozása az Azure CLI- *vel* című részben olvashatók az egyszerű [szolgáltatásnév létrehozásához az erőforrások eléréséhez](/cli/azure/create-an-azure-service-principal-azure-cli) . Ezek a lépések létrehoznak egy egyszerű szolgáltatásnevet, amely az Azure REST API való hitelesítéshez használatos.

1. A parancssorból használja az alábbi parancsot az Azure-előfizetések listázásához.

   ```azurecli
   az account list --query '[].{Subscription_ID:id,Tenant_ID:tenantId,Name:name}'  --output table
   ```

    A listából válassza ki a használni kívánt előfizetést, és jegyezze fel a **Subscription_ID** és __Tenant_ID__ oszlopokat. Mentse ezeket az értékeket.

2. A következő parancs használatával hozzon létre egy alkalmazást a Azure Active Directory.

   ```azurecli
   az ad app create --display-name "exampleapp" --homepage "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your password> --query 'appId'
   ```

    Cserélje le a `--display-name` ,, és értékeit a `--homepage` `--identifier-uris` saját értékeire. Adja meg az új Active Directory bejegyzéshez tartozó jelszót.

   > [!NOTE]  
   > A `--home-page` és az `--identifier-uris` értékek nem szükségesek az interneten futó tényleges weblapra hivatkozni. Egyedi URI-azonosítóknak kell lenniük.

   A parancs által visszaadott érték az új alkalmazáshoz tartozó __alkalmazás azonosítója__ . Mentse ezt az értéket.

3. Használja az alábbi parancsot egy egyszerű szolgáltatásnév létrehozásához az **alkalmazás-azonosító** használatával.

   ```azurecli
   az ad sp create --id <App ID> --query 'objectId'
   ```

     A parancs által visszaadott érték az __objektumazonosító__. Mentse ezt az értéket.

4. Rendelje hozzá a **tulajdonosi** szerepkört az egyszerű szolgáltatáshoz az **objektumazonosító** érték használatával. Használja a korábban beszerzett **előfizetés-azonosítót** .

   ```azurecli
   az role assignment create --assignee <Object ID> --role Owner --scope /subscriptions/<Subscription ID>/
   ```

## <a name="get-an-authentication-token"></a>Hitelesítési jogkivonat beszerzése

Használja a következő parancsot egy hitelesítési jogkivonat lekéréséhez:

```bash
curl -X "POST" "https://login.microsoftonline.com/$TENANTID/oauth2/token" \
-H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=$APPID" \
--data-urlencode "grant_type=client_credentials" \
--data-urlencode "client_secret=$PASSWORD" \
--data-urlencode "resource=https://management.azure.com/"
```

`$TENANTID`A, `$APPID` a és a `$PASSWORD` értéket a korábban beszerzett vagy felhasznált értékekre állítja be.

Ha ez a kérelem sikeres, egy 200 sorozatú választ kap, és a válasz törzse JSON-dokumentumot tartalmaz.

A kérelem által visszaadott JSON-dokumentum egy **access_token** nevű elemet tartalmaz. **Access_token** értékét a rendszer a REST API felé irányuló kérelmek hitelesítésére használja.

```json
{
    "token_type":"Bearer",
    "expires_in":"3599",
    "expires_on":"1463409994",
    "not_before":"1463406094",
    "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
}
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Erőforráscsoport létrehozásához használja a következőt.

* Állítsa be `$SUBSCRIPTIONID` az egyszerű szolgáltatás létrehozásakor kapott előfizetés-azonosítót.
* Állítsa `$ACCESSTOKEN` az előző lépésben kapott hozzáférési jogkivonatra.
* A helyére írja `DATACENTERLOCATION` be azt az adatközpontot, amelyben létre kívánja hozni az erőforráscsoportot és az erőforrásokat. Például: "USA déli középső régiója".
* Állítsa be `$RESOURCEGROUPNAME` az ehhez a csoporthoz használni kívánt nevet:

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME?api-version=2015-01-01" \
    -H "Authorization: Bearer $ACCESSTOKEN" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DATACENTERLOCATION"
}'
```

Ha ez a kérelem sikeres, egy 200 sorozatú választ kap, és a válasz törzse tartalmaz egy JSON-dokumentumot, amely a csoportra vonatkozó információkat tartalmazza. A `"provisioningState"` elem a értéket tartalmazza `"Succeeded"` .

## <a name="create-a-deployment"></a>Központi telepítés létrehozása

A következő parancs használatával telepítse a sablont az erőforráscsoporthoz.

* Állítsa be `$DEPLOYMENTNAME` a központi telepítéshez használni kívánt nevet.

```bash
curl -X "PUT" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [!NOTE]  
> Ha a sablont egy fájlba mentette, a következő parancsot használhatja a helyett `-d "{ template and parameters}"` :
>
> `--data-binary "@/path/to/file.json"`

Ha ez a kérelem sikeres, egy 200 sorozatú választ kap, és a válasz törzse tartalmaz egy JSON-dokumentumot, amely az üzembe helyezési műveletre vonatkozó információkat tartalmaz.

> [!IMPORTANT]  
> A központi telepítés elküldve, de még nem fejeződött be. A telepítés befejezéséhez több percet is igénybe vehet, általában 15 kb.

## <a name="check-the-status-of-a-deployment"></a>Központi telepítés állapotának megkeresése

A központi telepítés állapotának megtekintéséhez használja a következő parancsot:

```bash
curl -X "GET" "https://management.azure.com/subscriptions/$SUBSCRIPTIONID/resourcegroups/$RESOURCEGROUPNAME/providers/microsoft.resources/deployments/$DEPLOYMENTNAME?api-version=2015-01-01" \
-H "Authorization: Bearer $ACCESSTOKEN" \
-H "Content-Type: application/json"
```

Ez a parancs egy JSON-dokumentumot ad vissza, amely a telepítési műveletre vonatkozó információkat tartalmaz. Az `"provisioningState"` elem tartalmazza a központi telepítés állapotát. Ha ez az elem tartalmazza a értékét `"Succeeded"` , a központi telepítés sikeresen befejeződött.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Következő lépések

Most, hogy sikeresen létrehozott egy HDInsight-fürtöt, a következő paranccsal megismerheti, hogyan dolgozhat a fürttel.

### <a name="apache-hadoop-clusters"></a>Fürtök Apache Hadoop

* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-fürtök

* [Ismerkedés az Apache HBase a HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java-alkalmazások fejlesztése az Apache HBase a HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Fürtök Apache Storm

* [Java-topológiák fejlesztése a HDInsight Apache Storméhez](storm/apache-storm-develop-java-topology.md)
* [Python-összetevők használata a HDInsight-ben Apache Storm](storm/apache-storm-develop-python-topology.md)
* [Topológiák üzembe helyezése és figyelése Apache Storm a HDInsight-on](storm/apache-storm-deploy-monitor-topology-linux.md)
