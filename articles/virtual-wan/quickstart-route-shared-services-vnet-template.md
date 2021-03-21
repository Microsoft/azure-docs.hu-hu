---
title: 'Rövid útmutató: a megosztott szolgáltatásokhoz való irányítás ARM-sablonnal'
titleSuffix: Azure Virtual WAN
description: Ebből a rövid útmutatóból megtudhatja, hogyan állíthat be útvonalakat egy megosztott szolgáltatás VNet eléréséhez olyan számítási feladattal, amelyet minden VNet és elágazáshoz szeretne hozzáférni egy Azure Resource Manager-sablon (ARM-sablon) használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: quickstart
ms.date: 03/05/2021
ms.author: cherylmc
ms.custom: subject-armqs
ms.openlocfilehash: f325dd445e778bf03049d2c9e2e00fed7a427ccf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443954"
---
# <a name="quickstart-route-to-shared-services-vnets-using-an-arm-template"></a>Rövid útmutató: a megosztott szolgáltatások virtuális hálózatok való továbbítása ARM-sablon használatával

Ez a rövid útmutató azt ismerteti, hogyan használhatók egy Azure Resource Manager-sablon (ARM-sablon) a megosztott szolgáltatások VNet eléréséhez olyan számítási feladatokhoz, amelyeket minden VNet és ág (VPN/ER/P2S) számára szeretne elérni. Ilyen megosztott munkaterhelések például olyan virtuális gépeket tartalmazhatnak, amelyek olyan szolgáltatásokkal rendelkeznek, mint a tartományvezérlők vagy a fájlmegosztás, vagy az Azure [privát végpontján](../private-link/private-endpoint-overview.md)belül elérhető Azure-szolgáltatások.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f301-virtual-wan-with-route-tables%2fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* Ehhez a konfigurációhoz szükség van a nyilvánoskulcs-tanúsítványra. A mintaadatok a cikkben vannak megadva. A mintaadatok azonban csak a sablonra vonatkozó követelmények kielégítésére szolgálnak a P2S-átjáró létrehozása érdekében. Miután a sablon befejeződik, és az erőforrások üzembe helyezése megtörtént, a konfiguráció működéséhez frissítenie kell ezt a mezőt a saját tanúsítvány-adataival. Lásd: [felhasználói VPN-tanúsítványok](certificates-point-to-site.md#cer).

## <a name="review-the-template"></a><a name="review"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/301-virtual-wan-with-route-tables) közül származik. A cikk sablonja túl hosszú ahhoz, hogy megjelenjen itt. A sablon megtekintéséhez lásd: [azuredeploy.js](https://github.com/Azure/azure-quickstart-templates/blob/master/301-virtual-wan-with-route-tables/azuredeploy.json).

Ebben a rövid útmutatóban egy Azure-beli virtuális WAN-alapú, többközpontos üzembe helyezést fog létrehozni, beleértve az összes átjárót és a VNet-kapcsolatot. A bemeneti paraméterek listáját a rendszer szándékosan legalább egyszer megtartotta. Az IP-címzési séma a sablonban található változók módosításával módosítható. A forgatókönyvet részletesebben a [forgatókönyv: Shared Services VNet](scenario-shared-services-vnet.md) című cikk ismerteti.

:::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Üzembe helyezési architektúra" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

Ez a sablon egy teljesen működőképes Azure-beli virtuális WAN-környezetet hoz létre a következő erőforrásokkal:

* 2 különböző hubok eltérő régiókban.
* 4 Azure-beli virtuális hálózat (VNet).
* 2 VNet-kapcsolat minden VWan hub esetében.
* 1 pont – hely (P2S) VPN-átjáró az egyes csomópontokon.
* 1 helyek közötti (S2S) VPN-átjáró az egyes csomópontokon.
* 1 ExpressRoute-átjáró minden egyes központban.
* Az egyes csomópontokon RT_SHARED egyéni útválasztási táblák.
* RT_SHARED útválasztási táblák csoportosításához LBL_RT_SHARED címke.

Több Azure-erőforrás van definiálva a sablonban:

* [**Microsoft. Network/virtualwans**](/azure/templates/microsoft.network/virtualwans)
* [**Microsoft. Network/virtualhubs**](/azure/templates/microsoft.network/virtualhubs)
* [**Microsoft. Network/virtualnetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/hubvirtualnetworkconnections**](/azure/templates/microsoft.network/virtualhubs/hubvirtualnetworkconnections)
* [**Microsoft. Network/hubroutetables**](/azure/templates/microsoft.network/virtualhubs/hubRouteTables)
* [**Microsoft. Network/p2svpngateways**](/azure/templates/microsoft.network/p2svpngateways)
* [**Microsoft. Network/vpngateways**](/azure/templates/microsoft.network/vpngateways)
* [**Microsoft. Network/expressroutegateways**](/azure/templates/microsoft.network/expressroutegateways)
* [**Microsoft. Network/vpnserverconfigurations**](/azure/templates/microsoft.network/vpnServerConfigurations)

>[!NOTE]
> Ez az ARM-sablon nem hozza létre a hibrid kapcsolathoz szükséges ügyféloldali erőforrásokat. A sablon üzembe helyezése után továbbra is létre kell hoznia és konfigurálnia kell a P2S VPN-ügyfeleket, a VPN-ágakat (helyi helyeket) és a ExpressRoute-áramkörök összekapcsolását.
>

További sablonokat a következő témakörben talál: [Azure Gyorsindítás sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a><a name="deploy"></a>A sablon üzembe helyezése

A sablon megfelelő üzembe helyezéséhez a gombot kell használnia az Azure-ban való üzembe helyezéshez és a Azure Portal a többi módszer helyett, a következő okok miatt:

* A P2S-konfiguráció létrehozásához fel kell töltenie a legfelső szintű tanúsítványra vonatkozó adatokkal. Az adatmező nem fogadja el a tanúsítványokat a PowerShell vagy a parancssori felület használata esetén.
* Ez a sablon nem működik megfelelően a tanúsítvány-adatok feltöltése miatt Cloud Shell használatával.
* Emellett a portálon egyszerűen módosíthatja a sablont és a paramétereket, hogy az IP-címtartományok és más értékek is megfeleljenek.

1. Kattintson az **Üzembe helyezés az Azure-ban** lehetőségre.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f301-virtual-wan-with-route-tables%2fazuredeploy.json)
1. A sablon megtekintéséhez kattintson a **Sablon szerkesztése** lehetőségre. Ezen az oldalon módosíthatja az egyes értékeket, például a Címterület vagy bizonyos erőforrások nevét. Mentse a **mentést** a módosítások mentéséhez vagy az **elvetéshez**.
1. A sablon lapon adja meg az értékeket. Ehhez a sablonhoz a P2S nyilvános tanúsítványa szükséges. Ha gyakorlatként használja ezt a cikket, a következő adatok használhatók a. cer fájlból mint mintaadatokként mindkét hubhoz. A sablon futtatása és a telepítés befejezése után a P2S-konfiguráció használatához le kell cserélnie ezeket az információkat a nyilvános kulcsú [tanúsítvány adataival](certificates-point-to-site.md#cer) a saját üzembe helyezéséhez.

   ```certificate-data
   MIIC5zCCAc+gAwIBAgIQGxd3Av1q6LJDZ71e3TzqcTANBgkqhkiG9w0BAQsFADAW
   MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0yMDExMDkyMjMxNTVaFw0yMTExMDky
   MjUxNTVaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
   AAOCAQ8AMIIBCgKCAQEA33fFra/E0YmGuXLKmYcdvjsYpKwQmw8DjjDkbwhE9jcc
   Dp50e7F1P6Rxo1T6Hm3dIhEji+0QkP4Ie0XPpw0eW77+RWUiG9XJxGqtJ3Q4tyRy
   vBfsHORcqMlpV3VZOXIxrk+L/1sSm2xAc2QGuOqKaDNNoKmjrSGNVAeQHigxbTQg
   zCcyeuhFxHxAaxpW0bslK2hEZ9PhuAe22c2SHht6fOIDeXkadzqTFeV8wEZdltLr
   6Per0krxf7N2hFo5Cfz0KgWlvgdKLL7dUc9cjHo6b6BL2pNbLh8YofwHQOQbwt6H
   miAkEnx1EJ5N8AWuruUTByR2jcWyCnEAUSH41+nk4QIDAQABozEwLzAOBgNVHQ8B
   Af8EBAMCAgQwHQYDVR0OBBYEFJMgnJSYHH5AJ+9XB11usKRwjbjNMA0GCSqGSIb3
   DQEBCwUAA4IBAQBOy8Z5FBd/nvgDcjvAwNCw9h5RHzgtgQqDP0qUjEqeQv3ALeC+
   k/F2Tz0OWiPEzX5N+MMrf/jiYsL2exXuaPWCF5U9fu8bvs89GabHma8MGU3Qua2x
   Imvt0whWExQMjoyU8SNUi2S13fnRie9ZlSwNh8B/OIUUEtVhQsd4OfuZZFVH4xGp
   ibJMSMe5JBbZJC2tCdSdTLYfYJqrLkVuTjynXOjmz2JXfwnDNqEMdIMMjXzlNavR
   J8SNtAoptMOK5vAvlySg4LYtFyXkl0W0vLKIbbHf+2UszuSCijTUa3o/Y1FoYSfi
   eJH431YTnVLuwdd6fXkXFBrXDhjNsU866+hE
   ```

1. Ha végzett az értékek megadásával, válassza a **felülvizsgálat + létrehozás** lehetőséget.
1. A **felülvizsgálat + létrehozás** oldalon az érvényesítési fázisok után válassza a **Létrehozás** lehetőséget.
1. A telepítés befejezéséhez körülbelül 75 percet vesz igénybe. Az előrehaladást a sablon **áttekintése** oldalon tekintheti meg. Ha bezárta a portált, a rendszer a telepítést folytatja.

   :::image type="content" source="./media/quickstart-route-shared-services-template/template.png" alt-text="Az üzembe helyezés példája kész":::

## <a name="validate-the-deployment"></a><a name="validate"></a>Az üzembe helyezés ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki az **erőforráscsoportok** elemet a bal oldali ablaktáblán.
1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az **Áttekintés** oldalon a következőhöz hasonló példa jelenik meg: :::image type="content" source="./media/quickstart-route-shared-services-template/resources.png" alt-text="példa az erőforrásokra" lightbox="./media/quickstart-route-shared-services-template/resources.png":::

1. Kattintson a virtuális WAN-ra a hubok megtekintéséhez. A virtuális WAN lapon kattintson az egyes csomópontokra a kapcsolatok és az egyéb hub-információk megtekintéséhez.
   :::image type="content" source="./media/quickstart-route-shared-services-template/hub.png" alt-text="Hubok – példa" lightbox="./media/quickstart-route-shared-services-template/hub.png":::

## <a name="complete-the-hybrid-configuration"></a><a name="complete"></a>A hibrid konfiguráció befejezése

A sablon nem konfigurálja a hibrid hálózatokhoz szükséges összes beállítást. A követelményektől függően a következő konfigurációkat és beállításokat kell végrehajtania.

* [A VPN-ágak konfigurálása – helyi helyek](virtual-wan-site-to-site-portal.md#site)
* [A P2S VPN-konfiguráció befejezése](virtual-wan-point-to-site-portal.md)
* [A ExpressRoute-áramkörök összekötése](virtual-wan-expressroute-portal.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a létrehozott erőforrásokra, törölje őket. A virtuális WAN-erőforrások némelyikét bizonyos sorrendben törölni kell a függőségek miatt. A törlés körülbelül 30 percet vesz igénybe.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A P2S VPN-konfiguráció befejezése](virtual-wan-point-to-site-portal.md)

> [!div class="nextstepaction"]
> [A VPN-ágak konfigurálása – helyi helyek](virtual-wan-site-to-site-portal.md#site)

> [!div class="nextstepaction"]
> [A ExpressRoute-áramkörök összekötése](virtual-wan-expressroute-portal.md)
