---
title: Alkalmazások közzététele az interneten Application Gateway és Azure Firewall használatával
description: Alkalmazások közzététele az interneten Application Gateway és Azure Firewall használatával
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6c22d1bae4f1d116aa52846880498c7c2a425174
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738718"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>Alkalmazások közzététele az interneten Application Gateway és Azure Firewall használatával

Ez a dokumentum ismerteti, hogyan tehet elérhetővé alkalmazásokat az interneten Application Gateway és Azure Firewall használatával. Ha egy Azure Spring Cloud Service-példányt telepít a virtuális hálózatba, a szolgáltatási példányon lévő alkalmazások csak a magánhálózaton érhetők el. Ahhoz, hogy az alkalmazások elérhetők legyenek az interneten, integrálni kell az **Azure Application Gateway** és opcionálisan a **Azure Firewall** használatával.

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure CLI verziója 2.0.4 vagy újabb verzió](/cli/azure/install-azure-cli).

## <a name="define-variables"></a>Változók meghatározása

Adja meg az [Azure Spring Cloud üzembe helyezése az Azure Virtual Networkben (VNet Injection) című részben](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)létrehozott erőforráscsoport és virtuális hálózat változóit. A valós környezet alapján testreszabhatja az értékeket.

```
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="login-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure CLI-be, és válassza ki az aktív előfizetését.

```
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

A létrehozandó **Azure-Application Gateway** ugyanahhoz a virtuális hálózathoz csatlakozik, mint a--vagy a virtuális hálózat – az Azure Spring Cloud Service-példányhoz. Először hozzon létre egy új alhálózatot a virtuális hálózat Application Gatewayához a használatával `az network vnet subnet create` , és hozzon létre egy nyilvános IP-címet is a Application Gateway használatával `az network public-ip create` .

```
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>Alkalmazásátjáró létrehozása

Hozzon létre egy Application Gatewayt a használatával `az network application-gateway create` , és adja meg az alkalmazás privát teljes tartománynevét (FQDN) a háttér-készletben lévő kiszolgálóként. Ezután frissítse a HTTP-beállítást a használatával az `az network application-gateway http-settings update` állomásnév használata a háttér-készletből.

```
APPLICATION_GATEWAY_NAME='my-app-gw'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true
```

Az Azure az Application Gateway létrehozásához akár 30 percet is igénybe vehet. Miután létrejött, a háttér állapotának használatával keresse meg a következőt: `az network application-gateway show-backend-health` .  Ez azt vizsgálja, hogy az Application Gateway eléri-e az alkalmazást a saját teljes tartománynevén keresztül.

```
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

A kimenet a háttérbeli készlet kifogástalan állapotát jelzi.

```
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>Hozzáférés az alkalmazáshoz az Application Gateway előtér nyilvános IP-címének használatával

Kérje le az Application Gateway nyilvános IP-címét a használatával `az network public-ip show` .

```
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

Másolja és illessze be a nyilvános IP-címet a böngésző címsorába.

  ![Alkalmazás nyilvános IP-címe](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>Lásd még

- [Az Azure Spring Cloud hibaelhárítása a VNET-ben](spring-cloud-troubleshooting-vnet.md)
- [Az Azure Spring Cloud VNET való futtatásának felhasználói feladatai](spring-cloud-vnet-customer-responsibilities.md)