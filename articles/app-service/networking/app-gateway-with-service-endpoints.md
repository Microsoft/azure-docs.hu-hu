---
title: Application Gateway integráció a szolgáltatási végpontokkal – Azure App Service | Microsoft Docs
description: Leírja, hogyan integrálható a Application Gateway a szolgáltatás-végpontokkal védett Azure App Serviceokkal.
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
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 58886a8f7dc505a7e68d69eb00b4a2ebd776dd5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98209856"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Application Gateway integráció a szolgáltatási végpontokkal
A App Service három változata van, amelyek némileg eltérő konfigurációt igényelnek az Azure Application Gateway-nal való integrációhoz. A variációk közé tartoznak a rendszeres App Service – többek között a több-bérlős, belső Load Balancer (ILB) App Service Environment (Bevezetés) és a külső betekintő is. Ez a cikk bemutatja, hogyan konfigurálhatja azt App Service (több-bérlős), és megvitathatja a ILB és a külső beadással kapcsolatos szempontokat.

## <a name="integration-with-app-service-multi-tenant"></a>Integráció App Service (több-bérlős)
App Service (több-bérlős) nyilvános internettel rendelkező végponttal rendelkezik. A [szolgáltatás-végpontok](../../virtual-network/virtual-network-service-endpoints-overview.md) használatával csak az Azure-Virtual Networkon belül egy adott alhálózatról engedélyezheti a forgalmat, és minden mást blokkolhat. A következő forgatókönyvben ezt a funkciót fogjuk használni annak biztosítására, hogy egy App Service-példány csak adott Application Gateway-példányról kapjon forgalmat.

:::image type="content" source="./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png" alt-text="Az ábrán az Azure-Virtual Network egy Application Gateway található, és a tűzfal ikonján keresztül áramlik az alkalmazások példányaira App Service.":::

A konfiguráció két részből áll, a App Service és a Application Gateway létrehozása mellett. Az első rész a szolgáltatás-végpontok engedélyezése azon Virtual Network alhálózatában, ahol a Application Gateway üzembe lett helyezve. A szolgáltatási végpontok biztosítják, hogy az alhálózatot elhagyó összes hálózati forgalom az adott alhálózati AZONOSÍTÓval legyen felcímkézve a App Service. A második rész az adott webalkalmazás hozzáférési korlátozásának beállítása annak biztosítására, hogy csak az adott alhálózati AZONOSÍTÓval címkézett forgalom legyen engedélyezett. A beállítástól függően különböző eszközök használatával is konfigurálhatja.

## <a name="using-azure-portal"></a>Az Azure Portal használata
A Azure Portal segítségével négy lépést követve üzembe helyezheti és konfigurálhatja a telepítőt. Ha meglévő erőforrásai vannak, akkor kihagyhatja az első lépéseket.
1. Hozzon létre egy App Servicet a App Service dokumentációjában található rövid útmutatók valamelyikével, például: [.net Core](../quickstart-dotnetcore.md) rövid útmutató
2. Hozzon létre egy Application Gateway a [portál](../../application-gateway/quick-create-portal.md)rövid útmutatójának használatával, de hagyja ki a háttérbeli célok hozzáadása szakaszt.
3. Konfigurálja [app Service háttérként a Application Gatewayban](../../application-gateway/configure-web-app-portal.md), de hagyja ki a hozzáférés korlátozása szakaszt.
4. Végül hozza létre a [hozzáférési korlátozást a szolgáltatási végpontok használatával](../../app-service/app-service-ip-restrictions.md#set-a-service-endpoint-based-rule).

Most már elérheti a App Servicet a Application Gatewayon keresztül, de ha közvetlenül próbál hozzáférni a App Servicehoz, egy 403 HTTP-hibaüzenetet kell kapnia, amely azt jelzi, hogy a webhely le van állítva.

![A képernyőkép a 403-es hiba szövegét jeleníti meg – tiltott.](./media/app-gateway-with-service-endpoints/website-403-forbidden.png)

## <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablonok használata
A [Resource Manager-alapú telepítési sablon][template-app-gateway-app-service-complete] teljes forgatókönyvet fog kiépíteni. A forgatókönyv egy App Service-példányból áll, amely szolgáltatás-végpontokkal van lezárva, és a hozzáférési korlátozás csak a Application Gateway érkező forgalom fogadására szolgál. A sablon számos intelligens alapértéket és egyedi Postfix-értéket tartalmaz, amelyeket az erőforrás-nevekhez adnak hozzá, hogy egyszerű legyen. A felülbíráláshoz a tárház klónozása vagy a sablon letöltése és szerkesztése szükséges. 

A sablon alkalmazásához használhatja a sablon leírásában található üzembe helyezés az Azure-ban gombot, vagy használhatja a megfelelő PowerShell/CLI-t.

## <a name="using-azure-command-line-interface"></a>Az Azure parancssori felületének használata
Az [Azure CLI-minta](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) kiépít egy app Service a szolgáltatás-végpontokkal és a hozzáférés korlátozásával, hogy csak a Application Gateway érkező forgalmat fogadja. Ha egy meglévő Application Gatewayból csak egy meglévő App Service forgalmát kell elkülöníteni, a következő parancs elegendő.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

Az alapértelmezett konfigurációban a parancs biztosítja a szolgáltatás-végpont konfigurációjának telepítését az alhálózatban, valamint a App Service hozzáférési korlátozását.

## <a name="considerations-for-ilb-ase"></a>A ILB-vel kapcsolatos megfontolások
A ILB-betanító nem teszi elérhetővé az internetet, és a példány és a Application Gateway közötti forgalom már el van különítve a Virtual Network. Az alábbi [útmutató a ILB-bevezetőt](../environment/integrate-with-application-gateway.md) konfigurálja, és Azure Portal használatával integrálható egy Application Gateway. 

Ha biztosítani szeretné, hogy csak az Application Gateway alhálózatról érkező forgalom jusson el a központhoz, konfigurálhat egy hálózati biztonsági csoportot (NSG), amely hatással van a központba tartozó összes webalkalmazásra. A NSG megadhatja az alhálózat IP-tartományát és opcionálisan a portokat (80/443). Győződjön meg arról, hogy nem bírálja felül a NSG megfelelő működéséhez [szükséges szabályokat](../environment/network-info.md#network-security-groups) .

Ha el szeretné különíteni a forgalmat egy egyéni webalkalmazáshoz, IP-alapú hozzáférési korlátozásokat kell használnia, mivel a szolgáltatási végpontok nem fognak működni a központilag. Az IP-címnek a Application Gateway példány magánhálózati IP-címének kell lennie.

## <a name="considerations-for-external-ase"></a>Külső benyújtás szempontjai
A külső kiegészítő szolgáltatás nyilvános terhelésű terheléselosztó, például több-bérlős App Service. A szolgáltatási végpontok nem működnek a beadáshoz, ezért az IP-alapú hozzáférési korlátozásokat az Application Gateway példány nyilvános IP-címével kell használnia. Külső kiegészítő szolgáltatás a Azure Portal használatával történő létrehozásához [kövesse ezt a](../environment/create-external-ase.md) rövid útmutatót

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Azure Resource Manager sablon a teljes forgatókönyvhöz"

## <a name="considerations-for-kuduscm-site"></a>Az kudu/SCM-hely szempontjai
Az SCM-hely, más néven kudu, egy rendszergazdai hely, amely minden webalkalmazáshoz megtalálható. Nem lehet visszafordítani az SCM-hely proxyját, és a legvalószínűbb, hogy az egyes IP-címekre vagy egy adott alhálózatra is le szeretné zárni.

Ha ugyanazokat a hozzáférési korlátozásokat szeretné használni, mint a fő hely, akkor a következő parancs használatával örökli a beállításokat.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Ha egyéni hozzáférési korlátozásokat szeretne beállítani az SCM-helyhez, hozzáférési korlátozásokat adhat hozzá az alábbihoz hasonló--SCM-site jelzővel.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Következő lépések
További információ a App Service Environmentről: [app Service Environment dokumentáció](/azure/app-service/environment).

A webalkalmazás további védelme érdekében a Application Gateway webalkalmazási tűzfallal kapcsolatos információk az [Azure webalkalmazási tűzfal dokumentációjában](../../web-application-firewall/ag/ag-overview.md)találhatók.
