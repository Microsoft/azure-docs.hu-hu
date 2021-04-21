---
title: Útválasztási beállítások konfigurálása Azure Kubernetes-szolgáltatáshoz az Azure CLI használatával
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan konfigurálhatja az AKS-fürtöt útválasztási beállításokkal az Azure CLI használatával.
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
ms.openlocfilehash: 9eaad12e254150109498be0fac2f285f33a5965c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776572"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Kubernetes-fürt útválasztási beállításainak konfigurálása az Azure CLI használatával

Ez a cikk bemutatja, hogyan konfigurálhatja az útválasztási beállításokat internetszolgáltatói hálózaton **keresztül** (internetes beállítás) egy Kubernetes-fürthöz az Azure CLI használatával. Az útválasztási beállítás beállításához hozzon létre egy nyilvános IP-címet az **Internet" útválasztási beállítástípussal, majd használja azt az AKS-fürt létrehozásakor.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.49-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az alábbi példa létrehoz egy erőforráscsoportot az USA keleti **Azure-régiójában:**

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Hozzon létre egy nyilvános IP-címet internettípusú útválasztási **beállítással** [az az network public-ip create paranccsal.](/cli/azure/network/public-ip#az_network_public_ip_create)

A következő parancs egy új nyilvános IP-címet hoz **létre** internetes útválasztási beállítással az USA keleti **Azure-régiójában.**

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
>  Jelenleg az útválasztási beállítás csak az IPV4 nyilvános IP-címeket támogatja.

## <a name="get-the-id-of-public-ip-address"></a>Nyilvános IP-cím azonosítójának lekért neve

A következő parancs visszaadja az előző szakaszban létrehozott nyilvános IP-cím azonosítóját:
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>Kubernetes-fürt létrehozása nyilvános IP-címmel

A következő parancs létrehozza az AKS-fürtöt az előző szakaszban létrehozott nyilvános IP-címmel:

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>Az AKS-fürt üzembe helyezése néhány percet vesz igénybe.

Az ellenőrzéshez keresse meg a Azure Portal korábbi lépésében létrehozott nyilvános IP-címet, és látni fogja, hogy az IP-cím a Kubernetes-fürthöz társított terheléselosztáshoz van társítva, az alábbi módon:

 ![Útválasztási beállítás nyilvános IP-címe a Kuberneteshez](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Következő lépések

- További információ a nyilvános [IP-címek útválasztási beállítási beállításról.](routing-preference-overview.md) 
- [Konfigurálja egy virtuális gép útválasztási beállítását az Azure CLI használatával.](configure-routing-preference-virtual-machine-cli.md)
