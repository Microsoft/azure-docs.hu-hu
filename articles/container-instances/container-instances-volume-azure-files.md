---
title: Kötet Azure Files csatlakoztatása tárolócsoporthoz
description: Megtudhatja, hogyan csatlakoztathat Azure Files kötetet az állapot megőrzéséhez a Azure Container Instances
ms.topic: article
ms.date: 03/24/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c541d4faa8728d99fd07396bc056a3e69dc93fe8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763738"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben

Az Azure Container Instances alapértelmezés szerint állapot nélküli. Ha a tároló újraindul, összeomlik vagy leáll, az összes állapota elveszett. Ha azt szeretné, hogy az állapot a tároló élettartamán túl is megmaradjon, külső tárból kell csatlakoztatnia egy kötetet. Ahogy az ebben a cikkben is látható, Azure Container Instances a használatával létrehozott [Azure-fájlmegosztást Azure Files.](../storage/files/storage-files-introduction.md) Azure Files Azure Storage-ban üzemeltetett, teljes körűen felügyelt fájlmegosztásokat kínál, amelyek az iparági szabvány Server Message Block (SMB) protokollon keresztül érhetők el. Az Azure-fájlmegosztások és a Azure Container Instances hasonló fájlmegosztási funkciókat kínálnak, mint az Azure-fájlmegosztások Azure-beli virtuális gépekkel való használata.

## <a name="limitations"></a>Korlátozások

* A megosztásokat csak Azure Files Linux-tárolókhoz csatlakoztathatja. A Linux- és Windows-tárolócsoportok szolgáltatástámogatása közötti különbségekről az áttekintésben [talál további információt.](container-instances-overview.md#linux-and-windows-containers)
* Az Azure-fájlmegosztás kötetének csatlakoztatásához a Linux-tárolót rootként kell *futtatni.*
* Az Azure-fájlmegosztások kötet-csatlakoztatásai a CIFS-támogatásra korlátozódnak.

> [!NOTE]
> A Azure Files tárolópéldányhoz való csatlakoztatása hasonló a Docker-kötéses [csatlakoztatáshoz.](https://docs.docker.com/storage/bind-mounts/) Ha olyan tárolókönyvtárba csatlakoztat egy megosztást, amelyben fájlok vagy könyvtárak vannak, a csatlakoztatás elfedi a fájlokat vagy könyvtárakat, így a tároló futtatása közben nem lesznek elérhetőek.
>

> [!IMPORTANT]
> Ha tárolócsoportokat helyez üzembe egy Azure Virtual Network, hozzá [](../virtual-network/virtual-network-service-endpoints-overview.md) kell adni egy szolgáltatásvégpontot az Azure Storage-fiókhoz.

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Az Azure-fájlmegosztást először létre kell hoznia, hogy aztán megoszthassa az Azure Container Instances-példánnyal. Az alábbi szkript futtatásával hozzon létre egy tárfiókot a fájlmegosztás és maga a megosztás tárolásához. A tárfiók nevének globálisan egyedinek kell lennie, ezért a szkript hozzáad egy véletlenszerű értéket az alapsztringhez.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Tároló hitelesítő adatainak lekérése

Ahhoz, hogy az Azure-fájlmegosztást kötetként csatlakoztassa az Azure Container Instancesben, három értékre lesz szüksége: a tárfiók nevére, a megosztás nevére és tárelérési kulcsra.

* **Tárfiók neve** – Ha az előző szkriptet használta, a tárfiók neve a változóban `$ACI_PERS_STORAGE_ACCOUNT_NAME` lett tárolva. A fiók nevének a következőt kell begépelnie:

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **Megosztás neve** – Ez az érték már ismert (az `acishare` előző szkriptben meghatározottak szerint)

* **Tárfiókkulcs** – Ez az érték a következő paranccsal található meg:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>Tároló üzembe helyezése és kötet csatlakoztatása – CLI

Ha az Azure CLI használatával kötetként csatlakoztatni egy Azure-fájlmegosztást egy tárolóban, a tároló az az container create használatával való létrehozásakor adja meg a megosztást és a kötet csatlakoztatási [pontját.][az-container-create] Ha követte az előző lépéseket, csatlakoztathatja a korábban létrehozott megosztást a következő paranccsal egy tároló létrehozásához:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

Az `--dns-name-label` értéknek egyedinek kell lennie abban az Azure-régióban, ahol a tárolópéldányt létrehozza. Frissítse az előző parancsban található értéket, ha a parancs végrehajtásakor **DNS-névcímke-hibaüzenetet** kap.

## <a name="manage-files-in-mounted-volume"></a>Csatlakoztatott köteten lévő fájlok kezelése

A tároló indításakor a Microsoft [aci-hellofiles][aci-hellofiles] rendszerképen keresztül üzembe helyezett egyszerű webalkalmazással létrehozhat kis méretű szövegfájlokat az Azure-fájlmegosztásban a megadott csatlakoztatási útvonalon. Az [az container show][az-container-show] paranccsal szerezze be a webalkalmazás teljes tartománynevét (FQDN):

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

Miután az alkalmazással szövegeket ment, a Azure Portal vagy a [][storage-explorer] Microsoft Azure Storage Explorer-hez hasonló eszközzel lekérheti és megvizsgálhatja a fájlmegosztásba írt fájlokat vagy fájlokat. [][portal]

## <a name="deploy-container-and-mount-volume---yaml"></a>Tároló üzembe helyezése és kötet csatlakoztatása – YAML

Tárolócsoportot is üzembe helyezhet, és kötetet csatlakoztathat egy tárolóban az Azure CLI és egy [YAML-sablon használatával.](container-instances-multi-container-yaml.md) A YAML-sablonnal történő üzembe helyezés az előnyben részesített módszer a több tárolóból álló tárolócsoportok üzembe helyezésekor.

Az alábbi YAML-sablon meghatároz egy tárolócsoportot, amely egy tárolót hoz létre a `aci-hellofiles` lemezképpel. A tároló csatlakoztatja a korábban kötetként létrehozott Azure-fájlmegosztási *acishare-t.* Ahol jelezve van, adja meg a fájlmegosztást tároló tárfiók nevét és tárkulcsát. 

Ahogy a CLI-példában is, az értéknek egyedinek kell lennie abban az `dnsNameLabel` Azure-régióban, ahol a tárolópéldányt létrehozza. Szükség esetén frissítse a YAML-fájlban található értéket.

```yaml
apiVersion: '2019-12-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

A YAML-sablonnal való üzembe helyezéshez mentse az előző YAML-fájlt egy nevű fájlba, majd hajtsa végre az az container create parancsot a `deploy-aci.yaml` [][az-container-create] `--file` paraméterrel:

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Tároló üzembe helyezése és kötet csatlakoztatása – Resource Manager

A CLI és a YAML üzembe helyezése mellett üzembe helyezhet egy tárolócsoportot, és csatlakoztathat egy kötetet egy tárolóban egy Azure [Resource Manager sablon használatával.](/azure/templates/microsoft.containerinstance/containergroups)

Először töltse ki a tömböt a sablon `volumes` tárolócsoport `properties` szakaszában. 

Ezután minden olyan tárolóhoz, amelyhez csatlakoztatni szeretné a kötetet, töltse ki a tárolódefiníció szakaszában `volumeMounts` `properties` található tömböt.

Az alábbi Resource Manager sablon meghatároz egy tárolócsoportot, amely egy tárolót hoz létre a `aci-hellofiles` lemezképpel. A tároló csatlakoztatja a korábban kötetként létrehozott Azure-fájlmegosztási *acishare-t.* Ahol jelezve van, adja meg a fájlmegosztást tároló tárfiók nevét és tárkulcsát. 

Ahogy az előző példákban is, az értéknek egyedinek kell lennie abban az `dnsNameLabel` Azure-régióban, ahol a tárolópéldányt létrehozza. Szükség esetén frissítse a sablonban megadott értéket.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

A Resource Manager sablonnal való üzembe helyezéshez mentse az előző JSON-t egy nevű fájlba, majd hajtsa végre az az deployment group create parancsot a `deploy-aci.json` [][az-deployment-group-create] `--template-file` paraméterrel:

```azurecli
# Deploy with Resource Manager template
az deployment group create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Több kötet csatlakoztatása

Ha több kötetet is csatlakoztatnia kell egy [](/azure/templates/microsoft.containerinstance/containergroups)tárolópéldányhoz, üzembe kell helyeznie egy Azure Resource Manager-sablonnal, egy YAML-fájllal vagy más programozott módszerrel. Sablon vagy YAML-fájl használata esetén adja meg a megosztás részleteit, és határozza meg a köteteket úgy, hogy a fájl szakaszában a tömböt `volumes` `properties` adja meg. 

Ha például létrehozott két *share1* és *share2* nevű Azure Files-megosztást a *myStorageAccount* tárfiókban, a Resource Manager-sablonban a tömb a következőhöz hasonlóan fog `volumes` megjelenni:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Ezután a tárolócsoport minden olyan tárolója esetén, amelyben csatlakoztatni szeretné a köteteket, töltse ki a tárolódefiníció szakaszában `volumeMounts` `properties` található tömböt. Ez csatlakoztatja például a korábban definiált két kötetet, a *myvolume1* és *a myvolume2* kötetet:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan csatlakoztatható más kötettípusok a Azure Container Instances:

* [EmptyDir kötet csatlakoztatása a Azure Container Instances](container-instances-volume-emptydir.md)
* [GitRepo-kötet csatlakoztatása a Azure Container Instances](container-instances-volume-gitrepo.md)
* [Titkos kötet csatlakoztatása a Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
