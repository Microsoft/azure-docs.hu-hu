---
title: Azure Spring Cloud Access alkalmazás virtuális hálózaton
description: Alkalmazás elérése egy Azure Spring-felhőben virtuális hálózatban.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: 37c8b4bc186c217ecb27638f5f50297102345de7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878206"
---
# <a name="access-your-application-in-a-private-network"></a>Hozzáférés az alkalmazáshoz egy magánhálózati hálózaton

Ez a dokumentum azt ismerteti, hogyan érheti el az alkalmazáshoz tartozó végpontot egy privát hálózaton.  A hozzáféréshez létre kell hoznia egy **Azure saját DNS zónát** az előfizetésben a saját IP-címéhez tartozó teljes tartománynév (FQDN) fordítása/feloldása érdekében.

Ha az Azure Spring Cloud Service-példányban lévő alkalmazásokhoz **végpontot rendel hozzá** a virtuális hálózatban, a végpont egy privát FQDN. A tartomány csak a magánhálózaton érhető el. Az alkalmazások és szolgáltatások az alkalmazás-végpontot használják. Ezek közé tartozik az [alkalmazások és telepítések megtekintése](spring-cloud-howto-staging-environment.md#view-apps-and-deployments)című témakörben leírt **tesztelési végpont** . A **log streaming**, amely a [stream Azure Spring Cloud app-naplók valós időben történő továbbításában](spring-cloud-howto-log-streaming.md)van leírva, csak a magánhálózaton belül működik.

## <a name="create-a-private-dns-zone"></a>Privát DNS-zóna létrehozása

Az alábbi eljárás egy magánhálózati DNS-zónát hoz létre egy alkalmazáshoz a magánhálózaton.

1. Nyissa meg az Azure Portalt. A felső keresőmezőbe keressen **saját DNS zónákat**, és válassza ki **saját DNS zónákat** az eredményből.

2. A **saját DNS zónák** lapon válassza a **+ Hozzáadás** lehetőséget.

3. Töltse ki az űrlapot az **saját DNS zóna létrehozása** lapon. Adja **<span></span>** meg a Private.azuremicroservices.IO **nevet** a zóna neveként.

4. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

5. Válassza a **Létrehozás** lehetőséget.

A zóna létrehozása eltarthat néhány percig.

## <a name="link-the-virtual-network"></a>A virtuális hálózat összekapcsolása

Ha a magánhálózati DNS-zónát a virtuális hálózathoz szeretné kapcsolni, létre kell hoznia egy virtuális hálózati kapcsolatot.

1. Válassza ki a fent létrehozott privát DNS-zóna erőforrását: **<span>Private.azuremicroservices.IO</span>** 

2. A bal oldali ablaktáblán válassza a **virtuális hálózati kapcsolatok** elemet.

3. Válassza a **Hozzáadás** lehetőséget.

4. Adja meg az **Azure-Spring-Cloud-DNS-link** **nevet a hivatkozás nevéhez**.

5. A **Virtual Network (virtuális hálózat**) területen válassza ki az [Azure Spring Cloud üzembe helyezése az Azure-beli virtuális hálózaton (VNet-befecskendezés)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)című részben leírtaknak megfelelően létrehozott virtuális hálózatot.

    ![Virtuális hálózati kapcsolat hozzáadása](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. Kattintson az **OK** gombra.

## <a name="create-dns-record"></a>DNS-rekord létrehozása

Ha a magánhálózati DNS-zónát a DNS lefordítására/feloldására szeretné használni, létre kell hoznia egy "A" típusú rekordot a zónában.

1. Válassza ki az [Azure Spring Cloud üzembe helyezése az Azure Virtual Networkben (VNet Injection) című](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)cikkben leírtak szerint létrehozott virtuális hálózati erőforrást.

2. A **csatlakoztatott eszközök** keresése mezőben adja meg a *kubernetes-Internal* értéket.

3. A szűrt eredményben keresse meg a szolgáltatási futtatókörnyezet **alhálózatához** csatlakoztatott **eszközt** , és másolja ki az **IP-címét**. Ebben a példában az IP-cím *10.1.0.7*.

    [![DNS-rekord ](media/spring-cloud-access-app-vnet/create-dns-record.png) létrehozása](media/spring-cloud-access-app-vnet/create-dns-record.png)

Vagy lekérheti az IP-címet a következő az CLI-paranccsal:

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. Válassza ki a fent létrehozott saját DNS-zóna erőforrást: **<span>Private.azuremicroservices.IO</span>**.

5. Válassza a **+ Rekordhalmaz** lehetőséget.

6. A **rekordtípus hozzáadása** mezőben adja meg vagy válassza ki az alábbi adatokat:

    |Beállítás     |Érték                                                                      |
    |------------|---------------------------------------------------------------------------|
    |Név        |Be *\**                                                                 |
    |Típus        |Válasszon ki **egy**                                                               |
    |TTL         |Adja meg az *1* értéket                                                                  |
    |TTL-egység    |**Óra** kiválasztása                                                           |
    |IP-cím  |Adja meg a 3. lépésben átmásolt IP-címet. A mintában adja meg a *10.1.0.7*.    |

    Ez után válassza az **OK** gombot.

    ![Privát DNS-zóna rekordjának hozzáadása](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>Saját FQDN kiosztása az alkalmazáshoz

Miután elvégezte a [Service-alkalmazások kiépítésében és üzembe helyezésében](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)leírt eljárást, saját teljes tartománynevet rendelhet hozzá az alkalmazáshoz.

1. Válassza ki a virtuális hálózatban üzembe helyezett Azure Spring Cloud Service-példányt, majd nyissa meg a bal oldali menü **alkalmazások** lapját.

2. Válassza ki az alkalmazást az **Áttekintés** oldal megjelenítéséhez.

3. Válassza a **végpont hozzárendelés** lehetőséget, ha privát FQDN-t szeretne hozzárendelni az alkalmazáshoz. Ez eltarthat néhány percig.

    ![Privát végpont kiosztása](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. A hozzárendelt privát FQDN (címkével ellátott **URL**) mostantól elérhető. Csak a magánhálózaton belül érhető el, az internetről nem.

## <a name="access-application-private-fqdn"></a>Hozzáférési alkalmazás privát FQDN-je

A hozzárendelést követően elérheti az alkalmazás privát teljes tartománynevét a magánhálózaton. Létrehozhat például egy Jumpbox-gépet ugyanabban a virtuális hálózatban vagy egy társ virtuális hálózatban, és a Jumpbox-gépen a privát FQDN elérhető.

![Privát végpont elérése a vnet-ben](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>Következő lépések

- [Alkalmazások közzététele az interneten – Application Gateway és Azure Firewall használatával](spring-cloud-expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>Lásd még

- [Az Azure Spring Cloud hibaelhárítása a VNET-ben](spring-cloud-troubleshooting-vnet.md)
- [Az Azure Spring Cloud VNET való futtatásának felhasználói feladatai](spring-cloud-vnet-customer-responsibilities.md)