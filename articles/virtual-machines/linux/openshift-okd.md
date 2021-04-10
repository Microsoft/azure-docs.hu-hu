---
title: OKD üzembe helyezése az Azure-ban
description: OKD üzembe helyezése az Azure-ban.
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: dc14b10081cf175581d29524dcea60c52763b03c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667217"
---
# <a name="deploy-okd-in-azure"></a>OKD üzembe helyezése az Azure-ban

Az Azure-ban kétféleképpen helyezhet üzembe OKD (korábban OpenShift Origin):

- Manuálisan telepítheti az összes szükséges Azure-infrastruktúra-összetevőt, majd követheti a [OKD dokumentációját](https://docs.okd.io).
- Használhat egy meglévő [Resource Manager-sablont](https://github.com/Microsoft/openshift-origin) is, amely leegyszerűsíti a OKD-fürt üzembe helyezését.

## <a name="deploy-using-the-okd-template"></a>Üzembe helyezés a OKD sablon használatával

A Resource Manager-sablonnal történő üzembe helyezéshez egy Parameters-fájl segítségével adja meg a bemeneti paramétereket. Az üzembe helyezés további testreszabásához a GitHub-tárházat kell megváltoztatnia, és módosítani a megfelelő elemeket.

Néhány gyakori testreszabási lehetőség, de nem korlátozódik a következőkre:

- Megerősített VM-méret (változó azuredeploy.json)
- Elnevezési konvenciók (változók a azuredeploy.json)
- OpenShift-fürtök, a Hosts fájlon keresztül módosítva (deployOpenShift.sh)

A [OKD sablonban](https://github.com/Microsoft/openshift-origin) több ág is elérhető a OKD különböző verzióihoz.  Igény szerint közvetlenül a tárházból is üzembe helyezhető, vagy a tárházat leválaszthatja, és egyéni módosításokat hajthat végre az üzembe helyezés előtt.

Használja a `appId` paraméterhez korábban létrehozott egyszerű szolgáltatásnév értékét `aadClientId` .

A következő példa egy azuredeploy.parameters.jsnevű paramétereket tartalmazó fájlt mutat be az összes szükséges bemenettel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Cserélje le a paramétereket a megadott adatokra.

A különböző kiadások különböző paraméterekkel rendelkezhetnek, ezért kérjük, ellenőrizze a használt ág szükséges paramétereit.

### <a name="deploy-using-azure-cli"></a>Üzembe helyezés az Azure CLI-vel


> [!NOTE] 
> A következő parancshoz Azure CLI-2.0.8 vagy újabb verzió szükséges. A CLI-verziót a `az --version` paranccsal ellenőrizheti. A CLI verziójának frissítéséhez lásd: az [Azure CLI telepítése](/cli/azure/install-azure-cli).

A következő példa a OKD-fürtöt és az összes kapcsolódó erőforrást egy openshiftrg nevű erőforráscsoporthoz helyezi üzembe a myOpenShiftCluster központi telepítési nevével. A sablont közvetlenül a GitHub-tárházból kell hivatkozni, amikor egy azuredeploy.parameters.jsnevű helyi paramétereket tartalmazó fájlt használ.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Az üzembe helyezés legalább 30 percet vesz igénybe, a telepített csomópontok teljes száma alapján. A OpenShift-konzol URL-címe és a OpenShift-főkiszolgáló DNS-neve a terminálra nyomtatódik a telepítés befejeződése után. Másik lehetőségként megtekintheti a központi telepítés kimenetek szakaszát is a Azure Portal.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Ha nem szeretné összekapcsolni a parancssort, amíg a telepítés befejeződik, adja hozzá a `--no-wait` csoport központi telepítésének egyik beállítását. A központi telepítés kimenete az erőforráscsoport telepítési szakaszában található Azure Portalból kérhető le.

## <a name="connect-to-the-okd-cluster"></a>Kapcsolódás a OKD-fürthöz

Az üzembe helyezés befejezésekor kapcsolódjon a OpenShift-konzolhoz a böngésző használatával `OpenShift Console Url` . Azt is megteheti, hogy SSH-t használ a OKD-főkiszolgálóval. Az alábbi példa az üzemelő példány kimenetét használja:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az az [Group delete](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot, a OpenShift-fürtöt és az összes kapcsolódó erőforrást, ha már nincs rájuk szükség.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Következő lépések

- [Üzembe helyezés utáni feladatok](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift-telepítés hibáinak megoldása](./openshift-container-platform-3x-troubleshooting.md)
- [A OKD első lépései](https://docs.okd.io)
