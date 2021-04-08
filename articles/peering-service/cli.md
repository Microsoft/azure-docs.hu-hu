---
title: Az Azure CLI-vel való társítási szolgáltatás előzetes verziójának regisztrálása
description: Ismerje meg, hogyan regisztrálhat egy társ-szolgáltatási kapcsolatot az Azure CLI használatával
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: e7b696ba052b2aca9e14628327c07275845607ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540586"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Társ-szolgáltatási kapcsolatok regisztrálása az Azure CLI használatával

Az Azure társközi szolgáltatás olyan hálózati szolgáltatás, amely a Microsoft Cloud Services, például a Microsoft 365, a Dynamics 365, a szolgáltatott szoftver (SaaS) szolgáltatásai, az Azure vagy bármely, a nyilvános interneten keresztül elérhető Microsoft-szolgáltatás számára teszi lehetővé az ügyfelek kapcsolódását. Ebből a cikkből megtudhatja, hogyan regisztrálhat egy társ-szolgáltatási kapcsolatot az Azure CLI használatával.

- Ehhez a cikkhez az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Futtassa az [az version](/cli/azure/reference-index#az_version) parancsot a telepített verzió és a függő kódtárak megkereséséhez. A legújabb verzióra az [az upgrade](/cli/azure/reference-index#az_upgrade) paranccsal frissíthet.

## <a name="prerequisites"></a>Előfeltételek 

A következőkkel kell rendelkeznie:

### <a name="azure-account"></a>Azure-fiók

Érvényes és aktív Microsoft Azure fiókkal kell rendelkeznie. Ez a fiók szükséges a társ-szolgáltatási kapcsolatok beállításához. Az egyenrangú szolgáltatás az Azure-előfizetéseken belüli erőforrás.

### <a name="connectivity-provider"></a>Kapcsolatszolgáltató

A hálózat Microsoft hálózattal való összekapcsolásához egy internetszolgáltatóval vagy egy internetes Exchange-partnerrel is dolgozhat.

Győződjön meg arról, hogy a kapcsolati szolgáltatók a Microsofttal együttműködtek.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

### <a name="1-select-your-subscription"></a>1. Válassza ki az előfizetését

Válassza ki azt az előfizetést, amelyhez regisztrálni kívánja a társ-szolgáltatási kapcsolatot.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

Ha még nem rendelkezik erőforráscsoporthoz, létre kell hoznia egyet a társ-szolgáltatási kapcsolatok regisztrálása előtt. Hozzon létre egy erőforráscsoportot a következő parancs futtatásával:

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. regisztrálja az előfizetését az erőforrás-szolgáltató és a szolgáltatás jelzővel

Mielőtt továbblép a társ-szolgáltatási kapcsolatok Azure CLI használatával történő regisztrálásának lépéseire, regisztrálja előfizetését az erőforrás-szolgáltató és a szolgáltatás jelölővel az Azure CLI használatával. Az Azure CLI-parancsok itt vannak megadva:

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. a társ-szolgáltatási kapcsolatok regisztrálása

Regisztrálja a társ-szolgáltatási kapcsolatot az alábbi parancsok használatával az Azure CLI-n keresztül. Ez a példa a myPeeringService nevű társ-szolgáltatási kapcsolatot regisztrálja.

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. regisztrálja az előtagot

Regisztrálja a kapcsolat szolgáltatója által biztosított előtagot az alábbi parancsok Azure CLI-n keresztül történő végrehajtásával. Ez a példa regisztrálja az számára nevű előtagot.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni a társ-szolgáltatási kapcsolatban, tekintse meg a következő témakört: [társ-szolgáltatási kapcsolatok](connection.md).
- A társ-szolgáltatási kapcsolatok telemetria kapcsolatos további tudnivalókért lásd: társítási [szolgáltatás kapcsolódási telemetria](connection-telemetry.md).
- A telemetria méréséhez lásd: a [kapcsolatok telemetria mérése](measure-connection-telemetry.md).
- Ha Azure PowerShell használatával szeretné regisztrálni a kapcsolatot, tekintse meg a következő témakört: [társ-szolgáltatási kapcsolatok regisztrálása – Azure PowerShell](powershell.md).
- Ha a Azure Portal használatával szeretné regisztrálni a kapcsolatot, tekintse meg a következő témakört: [társ-szolgáltatási kapcsolatok regisztrálása – Azure Portal](azure-portal.md).
