---
title: Útválasztási beállítások konfigurálása Azure Kubernetes-szolgáltatáshoz az Azure CLI használatával
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan konfigurálhat egy AK-fürtöt útválasztási beállításokkal az Azure CLI használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: ac70f48a3c484f8865c54e09c59662a14a259e74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679820"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Útválasztási beállítások konfigurálása Kubernetes-fürtökhöz az Azure CLI használatával

Ebből a cikkből megtudhatja, hogyan konfigurálhat útválasztási beállításokat az ISP Network (**Internet** Option) segítségével egy Kubernetes-fürthöz az Azure CLI használatával. Az útválasztási beállításokat úgy állítja be, hogy létrehoz egy nyilvános IP-címet az útválasztási preferencia típusaként * * Internet * * * *, majd használja az AK-fürt létrehozásakor.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.49 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az alábbi példa egy erőforráscsoportot hoz létre az **USA keleti** régiója Azure-régióban:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Hozzon létre egy nyilvános IP-címet az **internetes** típus útválasztási beállításával az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create)parancs használatával.

A következő parancs egy új nyilvános IP-címet hoz létre az **Internet** Routing PREFERENCIA az **USA keleti** régiója Azure-régióban.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```
> [!NOTE]
>  Az útválasztási beállítások jelenleg csak IPV4 nyilvános IP-címeket támogatnak.

## <a name="get-the-id-of-public-ip-address"></a>Nyilvános IP-cím AZONOSÍTÓjának beolvasása

A következő parancs az előző szakaszban létrehozott nyilvános IP-cím AZONOSÍTÓját adja vissza:
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>Kubernetes-fürt létrehozása a nyilvános IP-címmel

A következő parancs létrehozza az AK-fürtöt az előző szakaszban létrehozott nyilvános IP-címmel:

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>Az AK-fürt üzembe helyezése néhány percet vesz igénybe.

Az ellenőrzéshez keresse meg a Azure Portal korábbi lépésében létrehozott nyilvános IP-címet, és az IP-címet a Kubernetes-fürthöz társított terheléselosztó alapján fogja látni, ahogy az alábbi ábrán is látható:

 ![Útválasztási preferencia nyilvános IP-címe Kubernetes](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Következő lépések

- További információ az [útválasztási beállításokról a nyilvános IP-címekben](routing-preference-overview.md). 
- [Konfigurálja a virtuális gép útválasztási beállításait az Azure CLI használatával](configure-routing-preference-virtual-machine-cli.md).

