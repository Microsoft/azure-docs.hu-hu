---
title: Privát végpont konfigurálása
titleSuffix: Azure Machine Learning
description: A Azure Private Link használatával biztonságosan elérheti a Azure Machine Learning munkaterületét egy virtuális hálózatról.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 02/09/2021
ms.openlocfilehash: 5e13c97427736d60f300d2d7b502c6f3e15fb481
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861444"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Munkaterület Azure Private Link konfigurálása Azure Machine Learning munkaterülethez

Ebből a dokumentumból megtudhatja, hogyan használhatja a Azure Private Link a Azure Machine Learning munkaterülettel. A virtuális hálózatokhoz való virtuális hálózatok létrehozásával kapcsolatos Azure Machine Learning lásd: Virtuális hálózatok elkülönítése [és adatvédelmi áttekintése](how-to-network-security-overview.md)

Azure Private Link lehetővé teszi, hogy privát végpont használatával csatlakozzon a munkaterülethez. A privát végpont magánhálózati IP-címek készlete a virtuális hálózaton belül. Ezt követően korlátozhatja a munkaterülethez való hozzáférést, hogy az csak a magánhálózati IP-címeken keresztül következhet be. Private Link csökkenti az adatok kiszivárgásának kockázatát. A privát végpontokkal kapcsolatos további információkért tekintse meg a Azure Private Link [cikket.](../private-link/private-link-overview.md)

> [!IMPORTANT]
> Azure Private Link nincs hatással az Azure-beli vezérlősíkra (felügyeleti műveletekre), például a munkaterület törlésére vagy a számítási erőforrások kezelésére. Például egy számítási cél létrehozása, frissítése vagy törlése. Ezeket a műveleteket a rendszer a szokásos módon, a nyilvános interneten keresztül végzi el. Az adatsík műveletei, például az Azure Machine Learning stúdió, API-k (beleértve a közzétett folyamatokat is) vagy az SDK a privát végpontot használják.
>
> Ha Mozilla Firefox böngészőt használ, problémákba ütközhet, amikor megpróbálja elérni a munkaterület privát végpontját. Ez a probléma a Mozilla HTTPS-protokollon keresztüli DNS-ével kapcsolatos lehet. Áthidaló megoldásként javasoljuk, Microsoft Edge vagy a Google Chrome böngészőt használja.

## <a name="prerequisites"></a>Előfeltételek

* Ha privát kapcsolattal rendelkező munkaterületet tervez használni egy ügyfél által felügyelt kulccsal, ezt a funkciót egy támogatási jegy használatával kell igényelnie. További információ: [Kvóták kezelése és növelése.](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)

* A privát végpont létrehozásához meglévő virtuális hálózat szükséges. A privát [végpontok hozzáadása](../private-link/disable-private-endpoint-network-policy.md) előtt le kell tiltania a hálózati házirendeket a privát végpontok számára.
## <a name="limitations"></a>Korlátozások

* A Azure Machine Learning nem használható privát kapcsolatokkal a Azure Government régióban.
* Ha engedélyezi a nyilvános hozzáférést egy privát kapcsolattal védett munkaterületen, és Azure Machine Learning stúdió használja a nyilvános interneten keresztüli hozzáférést, előfordulhat, hogy egyes funkciók, például a tervező nem férnek hozzá az adataihoz. Ez a probléma akkor fordul elő, ha az adatokat egy, a virtuális hálózat mögött védett szolgáltatás tárolja. Például egy Azure Storage-fiók.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Privát végpontot használó munkaterület létrehozása

Privát végponttal a következő módszerek egyikével hozhat létre munkaterületet. Ezen módszerek mindegyikéhez __egy meglévő virtuális hálózatra van szükség:__

> [!TIP]
> Ha egyidejűleg szeretne létrehozni egy munkaterületet, egy privát végpontot és egy virtuális hálózatot, tekintse meg a Következőt: Munkaterület létrehozása Azure Resource Manager-sablonnal a [Azure Machine Learning.](how-to-create-workspace-template.md)

# <a name="python"></a>[Python](#tab/python)

A Azure Machine Learning Python SDK biztosítja a [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) osztályt, amely a [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) használatával használható egy privát végponttal létrehozott munkaterülethez. Ehhez az osztályhoz egy meglévő virtuális hálózatra van szükség.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A [gépi tanuláshoz használt Azure CLI-bővítmény](reference-azure-machine-learning-cli.md) az [az ml workspace create parancsot](/cli/azure/ml/workspace#az_ml_workspace_create) biztosítja. A parancs alábbi paraméterei használhatók egy magánhálózattal létrehozott munkaterület létrehozásához, de ehhez egy meglévő virtuális hálózatra van szükség:

* `--pe-name`: A létrehozott privát végpont neve.
* `--pe-auto-approval`: Azt határozza meg, hogy a munkaterület privát végponti kapcsolatait automatikusan jóvá kell-e hagyni.
* `--pe-resource-group`: Az erőforráscsoport, amely a privát végpontot létrehozza. Egy csoportnak kell lennie, amely a virtuális hálózatot tartalmazza.
* `--pe-vnet-name`: A meglévő virtuális hálózat, amely a privát végpontot létrehozza.
* `--pe-subnet-name`: Annak az alhálózatnak a neve, amely a privát végpontot létrehozza. Az alapértelmezett érték `default`.

Ezek a paraméterek a create parancshoz szükséges egyéb paraméterek mellett vannak. A következő parancs például egy új munkaterületet hoz létre az USA nyugati régiójában egy meglévő erőforráscsoport és virtuális hálózat használatával:

```azurecli
az ml workspace create -r myresourcegroup \
    -l westus \
    -n myworkspace \
    --pe-name myprivateendpoint \
    --pe-auto-approval \
    --pe-resource-group myresourcegroup \
    --pe-vnet-name myvnet \
    --pe-subnet-name mysubnet
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az __alkalmazás Hálózat Azure Machine Learning stúdió__ segítségével konfigurálhatja a privát végpontot. Ehhez azonban egy meglévő virtuális hálózatra van szükség. További információ: [Munkaterületek létrehozása a portálon.](how-to-manage-workspace.md)

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Privát végpont hozzáadása munkaterülethez

Privát végpont meglévő munkaterülethez való hozzáadásához használja az alábbi módszerek egyikét:

> [!WARNING]
>
> Ha a munkaterülethez meglévő számítási célok vannak társítva, és nem ugyanazon a virtuális hálózaton vannak, ahol a privát végpont létre lett hozva, nem fognak működni.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

A példában használt osztályokkal és metódusokkal kapcsolatos további információkért lásd: [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) [és Workspace.add_private_endpoint.](/python/api/azureml-core/azureml.core.workspace(class)#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A [gépi tanuláshoz elérhető Azure CLI-bővítmény](reference-azure-machine-learning-cli.md) az [az ml workspace private-endpoint add](/cli/azure/ml/workspace/private-endpoint#az_ml_workspace_private_endpoint_add) parancsot biztosítja.

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

A portál Azure Machine Learning munkaterületén válassza a  Privát végponti kapcsolatok, majd a + Privát __végpont lehetőséget.__ A mezők használatával hozzon létre egy új privát végpontot.

* A Régió __kiválasztásakor válassza__ ki ugyanazt a régiót, mint a virtuális hálózat. 
* Az __Erőforrástípus kiválasztásakor__ használja a __Microsoft.MachineLearningServices/workspaces et.__ 
* Állítsa az __Erőforrást__ a munkaterület nevére.

Végül válassza a __Létrehozás lehetőséget__ a privát végpont létrehozásához.

---

## <a name="remove-a-private-endpoint"></a>Privát végpont eltávolítása

Privát végpont munkaterületről való eltávolításához használja az alábbi módszerek egyikét:

# <a name="python"></a>[Python](#tab/python)

Privát [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) eltávolításához használja a Workspace.delete_private_endpoint_connection.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A [gépi tanuláshoz elérhető Azure CLI-bővítmény](reference-azure-machine-learning-cli.md) az [az ml workspace private-endpoint delete parancsot](/cli/azure/ml/workspace/private-endpoint#az_ml_workspace_private_endpoint_delete) biztosítja.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Machine Learning munkaterületén válassza a Privát végponti kapcsolatok lehetőséget, majd válassza ki az eltávolítani kívánt végpontot. Végül válassza az __Eltávolítás lehetőséget.__

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Munkaterület használata privát végponton

Mivel a munkaterülettel való kommunikáció csak a virtuális hálózatról engedélyezett, a munkaterületet használni képes fejlesztési környezetnek a virtuális hálózat tagjának kell lennie. Például egy virtuális gép a virtuális hálózatban.

> [!IMPORTANT]
> A kapcsolat ideiglenes megszakadása elkerülése érdekében a Microsoft a DNS-gyorsítótár kiürítését javasolja a munkaterülethez csatlakozó gépeken az Private Link. 

Az Azure-Virtual Machines lásd a Virtual Machines [dokumentációját.](../virtual-machines/index.yml)

## <a name="enable-public-access"></a>Nyilvános hozzáférés engedélyezése

Bizonyos helyzetekben előfordulhat, hogy engedélyezni szeretné, hogy valaki nyilvános végponton keresztül csatlakozzon az Ön biztonságos munkaterületéhez a virtuális hálózat helyett. Miután konfigurált egy munkaterületet egy privát végponttal, opcionálisan engedélyezheti a munkaterülethez való nyilvános hozzáférést. Ezzel nem távolítja el a privát végpontot. A VNet mögötti összetevők közötti összes kommunikáció továbbra is biztonságos. A VNeten keresztüli privát hozzáférés mellett csak a munkaterülethez engedélyezi a nyilvános hozzáférést.

> [!WARNING]
> A nyilvános végponton keresztüli csatlakozáskor a Studio egyes funkciói nem fognak tudni hozzáférni az adatokhoz. Ez a probléma akkor fordul elő, ha az adatokat egy, a virtuális hálózat mögött védett szolgáltatás tárolja. Például egy Azure Storage-fiók. Azt is vegye figyelembe, hogy a Jupyter/JupyterLab/RStudio számítási példány működése és a notebookok futtatása nem fog működni.

A privát kapcsolat használatára képes munkaterülethez való nyilvános hozzáférés engedélyezéséhez kövesse az alábbi lépéseket:

# <a name="python"></a>[Python](#tab/python)

Privát [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) eltávolításához használja a Workspace.delete_private_endpoint_connection.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A [gépi tanuláshoz elérhető Azure CLI-bővítmény](reference-azure-machine-learning-cli.md) az [az ml workspace update parancsot](/cli/azure/ml/workspace#az_ml_workspace_update) biztosítja. A munkaterülethez való nyilvános hozzáférés engedélyezéséhez adja hozzá a `--allow-public-access true` paramétert.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ezt a funkciót jelenleg nem lehet engedélyezni a portálon.

---


## <a name="next-steps"></a>Következő lépések

* A hálózati munkaterület biztonságossá Azure Machine Learning virtuális hálózatok elkülönítését és adatvédelmét [áttekintő cikkben talál további](how-to-network-security-overview.md) információt.

* Ha egyéni DNS-megoldást tervez használni a virtuális hálózatban, tekintse meg a munkaterület egyéni DNS-kiszolgálóval való [használatát.](how-to-custom-dns.md)
