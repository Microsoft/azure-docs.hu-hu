---
title: VPN Gateway vagy kapcsolat alaphelyzetbe állítása az IPsec-alagút létrehozásához
titleSuffix: Azure VPN Gateway
description: Hozzon létre egy kapcsolatot vagy egy VPN-átjárót az IPsec-alagutak visszaállításához.
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: adc2ffd63d73baaddce00324787df61061ea69dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726639"
---
# <a name="reset-a-vpn-gateway-or-a-connection"></a>VPN-átjáró vagy kapcsolat alaphelyzetbe állítása

Az Azure VPN Gateway vagy az átjáró kapcsolatának alaphelyzetbe állítása akkor hasznos, ha egy vagy több helyek közötti VPN-alagúton elveszíti a létesítmények közötti VPN-kapcsolatot. Ebben az esetben a helyszíni VPN-eszközei megfelelően működnek, de nem tudnak Ipsec-alagutakat létesíteni az Azure VPN Gateway átjárókkal. Ebből a cikkből megtudhatja, hogyan állíthatja alaphelyzetbe a VPN-átjárót vagy az átjárót

## <a name="what-happens-during-a-reset"></a>Mi történik az Alaphelyzetbe állítás során

### <a name="gateway-reset"></a>Átjáró alaphelyzetbe állítása

A VPN-átjáró két virtuálisgép-példányból áll, amelyek aktív-készenléti konfigurációban futnak. Az átjáró alaphelyzetbe állításakor újraindítja az átjárót, majd újra alkalmazza a létesítmények közötti konfigurációkat. Az átjáró megtartja azt a nyilvános IP-címet, amellyel rendelkezik. Ez azt jelenti, hogy a VPN-útválasztó konfigurációját nem kell frissíteni új nyilvános IP-címmel az Azure VPN Gatewayhez.

Amikor a parancsot az átjáró alaphelyzetbe állítására állítja ki, az Azure VPN Gateway aktuális aktív példánya azonnal újraindul. Az aktív példány feladatátvétele (újraindításkor) a készenléti példányra rövid hézagot eredményez. Ez a szünet nem tart tovább egy percnél.

Ha a kapcsolat nem áll vissza az első újraindítás után, adja ki ugyanezt a parancsot újra a második virtuálisgép-példány (az új aktív átjáró) újraindításához. Ha a két, újraindításra vonatkozó kérelem egymás után van kiadva, hosszabb szünet következhet be, amíg az aktív és a készenléti virtuálisgép-példány is újraindul. Ez a művelet a VPN-kapcsolaton túl hosszú időt eredményez, ami akár 30 – 45 percet is igénybe vehet, amíg a virtuális gépek újraindulnak.

Ha a két újraindítást követően továbbra is a létesítmények közötti kapcsolati problémák léptek fel, nyisson meg egy támogatási kérést a Azure Portal.

### <a name="connection-reset"></a>A kapcsolat alaphelyzetbe állt

Ha a kapcsolatok alaphelyzetbe állítását választja, az átjáró nem indul újra. A rendszer csak a kiválasztott kapcsolatokat állítja vissza és állítja vissza.

## <a name="reset-a-connection"></a>Kapcsolatok alaphelyzetbe állítása

A Azure Portal használatával egyszerűen alaphelyzetbe állíthatja a kapcsolatokat.

1. Navigáljon az alaphelyzetbe állítani kívánt **kapcsolódási** lehetőséghez. A kapcsolati erőforrást megkeresheti az **összes erőforrásban**, vagy az **"átjáró neve" – > kapcsolatok-> "kapcsolat neve"** lehetőséggel.
1. A **kapcsolatok** lapon válassza a bal oldali menü **Alaphelyzetbe állítás** elemét.
1. A **visszaállítás** **lapon kattintson az alaphelyzetbe** állítás gombra a kapcsolódás visszaállításához.

   :::image type="content" source="./media/reset-gateway/reset-connection.png" alt-text="Az alaphelyzetbe állítást bemutató képernyőkép.":::

## <a name="reset-a-vpn-gateway"></a>VPN-átjáró alaphelyzetbe állítása

Az átjáró alaphelyzetbe állítása előtt ellenőrizze az alábbi listán szereplő legfontosabb elemeket mindegyik IPsec helyek közötti (S2S) VPN-alagúthoz. Ha bármilyen eltérés van az elemek között, az S2S VPN-alagutak kapcsolata meg fog szakadni. A helyszíni és az Azure-beli VPN-átjárók konfigurációjának ellenőrzése és javítása a szükségtelen újraindítások és az átjárók egyéb munkakapcsolatainak megszakadásai miatt megszakad.

Az átjáró alaphelyzetbe állítása előtt ellenőrizze a következő elemeket:

* Az Azure VPN Gateway és a helyszíni VPN-átjáró internetes IP-címei (VIP-k) helyesen vannak konfigurálva az Azure és a helyszíni VPN-házirendekben.
* Az előre megosztott kulcsnak meg kell egyeznie az Azure VPN Gateway átjáróban és a helyszíni VPN-átjáróban.
* Ha adott IPsec/IKE-konfigurációt alkalmaz – például titkosítást, kivonatoló algoritmust vagy sérülés utáni titkosságvédelmet (PFS) – ügyeljen arra, hogy az Azure VPN Gateway és a helyszíni VPN-átjárók ugyanezzel a konfigurációval rendelkezzenek.

### <a name="azure-portal"></a><a name="portal"></a>Azure Portal

A Azure Portal segítségével alaphelyzetbe állíthatja a Resource Manager VPN-átjárót. Klasszikus átjáró alaphelyzetbe állításához tekintse meg a [klasszikus üzembe helyezési modell](#resetclassic)PowerShell-lépéseit.

[!INCLUDE [portal steps](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="powershell"></a><a name="ps"></a>PowerShell

#### <a name="resource-manager-deployment-model"></a>Resource Manager-alapú üzemi modell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az átjáró **alaphelyzetbe** állítására szolgáló parancsmag a következőt állítja be: AzVirtualNetworkGateway. Az Alaphelyzetbe állítás előtt győződjön meg arról, hogy a PowerShell legújabb verziója van telepítve a [parancsmagok használatával](/powershell/module/az.network). A következő példa egy VNet1GW nevű virtuális hálózati átjárót állít alaphelyzetbe a TestRG1 erőforráscsoporthoz:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Eredmény:

Ha visszatérési eredményt kap, akkor feltételezhető, hogy az átjáró alaphelyzetbe állítása sikeres volt. A visszatérési eredmény azonban semmis, ami explicit módon jelzi, hogy az Alaphelyzetbe állítás sikeres volt. Ha azt szeretné, hogy az előzmények alapján pontosan megtekinthető legyen az átjáró alaphelyzetbe állítása, megtekintheti ezeket az információkat a [Azure Portal](https://portal.azure.com). A portálon navigáljon a **"átjáró neve"-> Resource Health**.

#### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Klasszikus üzemi modell

Az átjáró **alaphelyzetbe** állítására szolgáló parancsmag a következőt állítja be: AzureVNetGateway. A Service Management Azure PowerShell-parancsmagjai helyileg kell telepíteni az asztalra. A Azure Cloud Shell nem használható. Az Alaphelyzetbe állítás előtt ellenőrizze, hogy rendelkezik-e a [Service Management (SM) PowerShell-parancsmagok](/powershell/azure/servicemanagement/install-azure-ps#azure-service-management-cmdlets)legújabb verziójával. A parancs használatakor győződjön meg arról, hogy a virtuális hálózat teljes nevét használja. A portál használatával létrehozott klasszikus virtuális hálózatok hosszú nevet igényelnek a PowerShell számára. A hosszú nevet a "Get-AzureVNetConfig-ExportToFile C:\Myfoldername\NetworkConfig.xml" paranccsal tekintheti meg.

Az alábbi példa alaphelyzetbe állítja a "Group TestRG1 TestVNet1" nevű virtuális hálózat átjáróját (amely egyszerűen "TestVNet1" a portálon):

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
```

Eredmény:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

### <a name="azure-cli"></a><a name="cli"></a>Azure CLI

Az átjáró alaphelyzetbe állításához használja az az [Network vnet-Gateway reset](/cli/azure/network/vnet-gateway) parancsot. A következő példa egy VNet5GW nevű virtuális hálózati átjárót állít alaphelyzetbe a TestRG5 erőforráscsoporthoz:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Eredmény:

Ha visszatérési eredményt kap, akkor feltételezhető, hogy az átjáró alaphelyzetbe állítása sikeres volt. A visszatérési eredmény azonban semmis, ami explicit módon jelzi, hogy az Alaphelyzetbe állítás sikeres volt. Ha azt szeretné, hogy az előzmények alapján pontosan megtekinthető legyen az átjáró alaphelyzetbe állítása, megtekintheti ezeket az információkat a [Azure Portal](https://portal.azure.com). A portálon navigáljon a **"átjáró neve"-> Resource Health**.
