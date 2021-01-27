---
title: VPN-eszközök konfigurációs parancsfájljainak letöltése S2S VPN-kapcsolatokhoz
description: Ez a cikk végigvezeti a VPN-eszközök konfigurációs parancsfájljainak a S2S VPN-kapcsolatokhoz való letöltésének lépéseit az Azure VPN Gateway Azure Resource Manager használatával.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: d2593c656e17310c5afb4a897b94cc8c12a618f0
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879494"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>VPN-eszközök konfigurációs parancsfájljainak letöltése S2S VPN-kapcsolatokhoz

Ez a cikk végigvezeti a VPN-eszközök konfigurációs parancsfájljainak a S2S VPN-kapcsolatokhoz való letöltésének lépéseit az Azure VPN Gateway Azure Resource Manager használatával. A következő ábra a magas szintű munkafolyamatot mutatja be.

![Letöltés – parancsfájl](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

A következő eszközök rendelkeznek elérhető parancsfájlokkal:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>Tudnivalók a VPN-eszközök konfigurációs parancsfájljairól

A létesítmények közötti VPN-kapcsolat egy Azure-beli VPN-átjáróból, egy helyszíni VPN-eszközből és egy, a kettőt összekötő IPsec S2S VPN-alagútból áll. A tipikus munkahelyi folyamat a következő lépéseket tartalmazza:

1. Azure VPN Gateway létrehozása és konfigurálása (virtuális hálózati átjáró)
2. Helyszíni hálózati és VPN-eszközt képviselő Azure-beli helyi hálózati átjáró létrehozása és konfigurálása
3. Azure VPN-kapcsolat létrehozása és konfigurálása az Azure VPN Gateway és a helyi hálózati átjáró között
4. A helyi hálózati átjáró által jelölt helyszíni VPN-eszköz konfigurálása a tényleges S2S VPN-alagút létrehozásához az Azure VPN Gateway használatával

Az Azure [Portal](./tutorial-site-to-site-portal.md), a [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)vagy a [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)használatával az 1 – 3. lépést végezheti el. Az utolsó lépés magában foglalja a helyszíni VPN-eszközök konfigurálását az Azure-on kívül. Ez a funkció lehetővé teszi, hogy letöltse a VPN-eszköz konfigurációs parancsfájlját az Azure VPN Gateway, a virtuális hálózat és a helyszíni hálózati címek előtagjai és a VPN-kapcsolat tulajdonságai, valamint a már kitöltött adatok megfelelő értékeivel. A parancsfájlt kiindulási pontként használhatja, vagy a parancsfájlt közvetlenül a helyszíni VPN-eszközökre is alkalmazhatja a konfigurációs konzolon keresztül.

> [!IMPORTANT]
> * Az egyes VPN-eszközök konfigurációs parancsfájljainak szintaxisa eltérő, és nagymértékben függ a modelltől és a belső vezérlőprogram verziójától. Különös figyelmet fordít az eszköz modelljére és a verzióra vonatkozó információkra az elérhető sablonokkal szemben.
> * Bizonyos paraméterek értékének egyedinek kell lennie az eszközön, és az eszköz elérése nélkül nem határozható meg. Az Azure által létrehozott konfigurációs parancsfájlok előre kitöltik ezeket az értékeket, de biztosítania kell, hogy a megadott értékek érvényesek legyenek az eszközön. Példák:
>    * Illesztőfelületek száma
>    * Hozzáférés-vezérlési listák száma
>    * Szabályzatok neve vagy száma stb.
> * Keresse meg a "**replace**" kulcsszót, amely beágyazva van a parancsfájlba, hogy megtalálja a parancsfájl alkalmazása előtt ellenőrizni kívánt paramétereket.
> * Néhány sablon tartalmaz egy "**tisztítási**" szakaszt, amely a konfigurációk eltávolítására is alkalmazható. A tisztítási szakaszt alapértelmezés szerint a rendszer megjegyzésekkel ellátva.

## <a name="download-the-configuration-script-from-azure-portal"></a>A konfigurációs parancsfájl letöltése Azure Portalról

Hozzon létre egy Azure VPN Gatewayt, egy helyi hálózati átjárót és egy kapcsolati erőforrást, amely összeköti a kettőt. A következő oldal végigvezeti a lépéseken:

* [Helyek közötti kapcsolat létrehozása az Azure Portalon](./tutorial-site-to-site-portal.md)

A kapcsolati erőforrás létrehozása után kövesse az alábbi utasításokat a VPN-eszköz konfigurációs parancsfájljainak letöltéséhez:

1. Egy böngészőben navigáljon a [Azure Portal](https://portal.azure.com) , és ha szükséges, jelentkezzen be az Azure-fiókjával
2. Lépjen a létrehozott kapcsolódási erőforráshoz. A kapcsolati erőforrások listáját a "minden szolgáltatás", majd a "HÁLÓZATKEZELÉS" és a "kapcsolatok" elemre kattintva tekintheti meg.

    ![kapcsolatok listája](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Kattintson a konfigurálni kívánt hálózatra.

    ![kapcsolatok – áttekintés](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Kattintson a "konfiguráció letöltése" hivatkozásra a kapcsolat áttekintése oldalon piros színnel kijelölve. Ekkor megnyílik a "konfiguráció letöltése" oldal.

    ![Letöltés – 1. parancsfájl](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Válassza ki a modell családját és a belső vezérlőprogram verzióját a VPN-eszközhöz, majd kattintson a "konfiguráció letöltése" gombra.

    ![download66 – 2. parancsfájl](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. A rendszer felszólítja a letöltött parancsfájl (szövegfájl) mentésére a böngészőből.
7. A konfigurációs parancsfájl letöltése után nyissa meg egy szövegszerkesztőben, és keresse meg a "Replace" kulcsszót, hogy azonosítsa és vizsgálja meg a cserélni kívánt paramétereket.

    ![Szerkesztés – parancsfájl](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Konfigurációs parancsfájl letöltése Azure PowerShell használatával



A Azure PowerShell használatával is letöltheti a konfigurációs parancsfájlt, ahogy az az alábbi példában is látható:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>A konfigurációs parancsfájl alkalmazása a VPN-eszközre

A konfigurációs parancsfájl letöltése és ellenőrzése után a következő lépés a parancsfájl alkalmazása a VPN-eszközre. A tényleges eljárás a VPN-eszköz és a modellek alapján változhat. Tekintse át a VPN-eszközök üzemeltetési kézikönyveit vagy a használati útmutató lapjait.

## <a name="next-steps"></a>További lépések

Folytassa a [helyek közötti kapcsolat](./tutorial-site-to-site-portal.md)konfigurálását.