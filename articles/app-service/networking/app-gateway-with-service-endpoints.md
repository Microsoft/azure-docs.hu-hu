---
title: Application Gateway szolgáltatásvégpontokkal való integráció – Azure App Service | Microsoft Docs
description: A Application Gateway szolgáltatásvégpontokkal Azure App Service integrációt ismerteti.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: f1d517ba37bbef95d1863485c8c3b6313f196c11
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374913"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Application Gateway szolgáltatásvégpontokkal való integráció
Az integrációnak három változata App Service, amelyek némileg eltérő konfigurációt igényelnek az integrációhoz a Azure Application Gateway. A változatok közé App Service ( más néven több-bérlős, belső Load Balancer (ILB) App Service Environment (ASE) és külső ASE. Ez a cikk bemutatja, hogyan konfigurálhatja az ILB App Service (több-bérlős) környezetekkel, és ismerteti az ILB-sel és a külső ASE környezetekkel kapcsolatos szempontokat.

## <a name="integration-with-app-service-multi-tenant"></a>Integráció App Service (több-bérlős)
App Service (több-bérlős) nyilvános internetkapcsolattal rendelkezik végponttal. A [szolgáltatásvégpontokkal egy](../../virtual-network/virtual-network-service-endpoints-overview.md) Azure-beli virtuális hálózatban csak egy adott alhálózatról Virtual Network minden mást blokkolhat. A következő forgatókönyvben ezzel a funkcióval biztosítjuk, hogy egy App Service-példány csak egy adott példányról fogad Application Gateway forgalmat.

:::image type="content" source="./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png" alt-text="Az ábra azt mutatja, hogy az internet egy Azure-Application Gateway-Virtual Network található virtuális hálózatba áramlik, és onnan egy tűzfalikonon keresztül a felhőben lévő alkalmazáspéldányok App Service.":::

Ennek a konfigurációnak két része van a App Service és a Application Gateway. Az első rész a szolgáltatásvégpontokat annak a szolgáltatásnak az alhálózatán engedélyezi Virtual Network a Application Gateway üzembe helyezése. A szolgáltatásvégpontokkal biztosítható, hogy az alhálózatot az alhálózat felé App Service forgalom az adott alhálózati azonosítóval legyen megcímkézve. A második rész az adott webalkalmazás hozzáférési korlátozásának beállítása annak biztosítása érdekében, hogy csak az adott alhálózat-azonosítóval címkézett forgalom legyen engedélyezett. Konfigurálhatja különböző eszközökkel, attól függően, hogy milyen előnyben részesítheti.

## <a name="using-azure-portal"></a>Az Azure Portal használata
A Azure Portal lépésekkel négy lépést kell követnie a telepítés építéshez és konfiguráláshoz. Ha rendelkezik meglévő erőforrásokkal, kihagyhatja az első lépéseket.
1. Hozzon App Service a dokumentációban található gyorsútmutatók App Service,például a [.NET Core gyorsútmutatóval.](../quickstart-dotnetcore.md)
2. Hozzon létre Application Gateway a portál gyors [üzembe helyezési](../../application-gateway/quick-create-portal.md)útmutatójának használatával, de hagyja ki a Háttércélok hozzáadása szakaszt.
3. Konfigurálja [App Service háttérként a](../../application-gateway/configure-web-app-portal.md)Application Gateway, de hagyja ki a Hozzáférés korlátozása szakaszt.
4. Végül hozza létre [a hozzáférési korlátozást szolgáltatásvégpontokkal.](../../app-service/app-service-ip-restrictions.md#set-a-service-endpoint-based-rule)

Most már hozzáférhet a App Service Application Gateway-on keresztül, de ha megpróbálja közvetlenül elérni az App Service-t, 403-as HTTP-hibaüzenetet kell kapnia, amely jelzi, hogy a webhely le van állítva.

![A 403-as – Tiltott hiba szövegét bemutató képernyőkép.](./media/app-gateway-with-service-endpoints/website-403-forbidden.png)

## <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablonok használata
A [Resource Manager üzembe helyezési sablon][template-app-gateway-app-service-complete] kiépít egy teljes forgatókönyvet. A forgatókönyv egy szolgáltatásvégpontokkal zárolt App Service és hozzáférési korlátozást tartalmaz, amely csak a szolgáltatástól Application Gateway. A sablon számos intelligens alapértelmezett értéket és egyedi utótagot tartalmaz, amelyek egyszerűvé tehetővé tehetőek az erőforrásnevekben. A felülbírálásukhoz klónoznia kell az tárat, vagy le kell töltenie a sablont, és szerkesztenie kell azt. 

A sablon alkalmazáshoz használhatja a sablon leírásában található Üzembe helyezés az Azure-ban gombot, vagy használhatja a megfelelő PowerShellt/CLI-t.

## <a name="using-azure-command-line-interface"></a>Az Azure parancssori felület használata
Az [Azure CLI-minta](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) üzembe fog App Service szolgáltatásvégpontokkal és hozzáférési korlátozásokkal zárolt virtuális Application Gateway. Ha csak el kell különítenie egy meglévő App Service a meglévő Application Gateway, a következő parancs elegendő.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

Az alapértelmezett konfigurációban a parancs biztosítja a szolgáltatásvégpont konfigurációjának beállítását az alhálózaton, valamint a hozzáférés korlátozását a App Service.

## <a name="considerations-for-ilb-ase"></a>Megfontolandó szempontok az ILB ASE-hez
Az ILB ASE nincs kitéve az internetnek, és a példány és egy Application Gateway közötti forgalom ezért már el van különítve a Virtual Network. Az alábbi [útmutató egy](../environment/integrate-with-application-gateway.md) ILB ASE-t konfigurál, és integrálja azt egy Application Gateway használatával Azure Portal. 

Ha biztosítani szeretné, hogy csak az Application Gateway-alhálózatról származó forgalom érje el az ASE-t, konfigurálhat egy hálózati biztonsági csoportot (NSG-t), amely az ASE összes webalkalmazását érinti. Az NSG-hez megadhatja az alhálózat IP-tartományát és opcionálisan a portokat (80/443). Győződjön meg arról, hogy nem bírálta felül az ASE megfelelő működéséhez szükséges [NSG-szabályokat.](../environment/network-info.md#network-security-groups)

Az egyes webalkalmazások forgalmának elkülönítéséhez IP-alapú hozzáférési korlátozásokat kell használnia, mivel a szolgáltatásvégpont nem fog működni az ASE-hez. Az IP-címnek a nyilvános példány magánhálózati IP Application Gateway kell lennie.

## <a name="considerations-for-external-ase"></a>Megfontolandó szempontok a külső ASE környezethez
A külső ASE rendelkezik nyilvános terheléselelosztással, például több-bérlős App Service. A szolgáltatásvégpont nem működik az ASE-hez, ezért ip-alapú hozzáférési korlátozásokat kell használnia a Application Gateway nyilvános IP-címével. Ha külső ASE-t a Azure Portal, kövesse ezt a [rövid útmutatót](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Azure Resource Manager sablon a teljes forgatókönyvhöz"

## <a name="considerations-for-kuduscm-site"></a>Megfontolandó szempontok a Kudu/scm-helyhez
Az scm webhely, más néven Kudu, egy felügyeleti webhely, amely minden webalkalmazáshoz létezik. Az scm-hely fordított proxyját nem lehet fordított valószínűséggel különálló IP-címekre vagy egy adott alhálózatra is zárolni.

Ha a fő helyével azonos hozzáférési korlátozásokat szeretne használni, a következő paranccsal örökölheti a beállításokat.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Ha egyéni hozzáférési korlátozásokat szeretne beállítani az SCM-helyhez, az alábbihoz hasonló módon hozzáadhat hozzáférési korlátozásokat az --scm-site jelzővel.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Következő lépések
További információt a dokumentációban App Service Environment a [App Service Environment talál.](/azure/app-service/environment)

A webalkalmazás további biztonságossá Web Application Firewall a Application Gateway kapcsolatos információkat az Azure Web Application Firewall [dokumentációjában találhat.](../../web-application-firewall/ag/ag-overview.md)
