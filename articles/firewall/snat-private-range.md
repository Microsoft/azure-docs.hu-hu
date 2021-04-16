---
title: Azure Firewall SNAT magánhálózati IP-címtartományok
description: Az SNAT-hez IP-címtartományokat is konfigurálhat.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: 91d4d631376c03b668128936f3840ce1119f9b6f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482745"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall SNAT magánhálózati IP-címtartományok

Azure Firewall automatikus SNAT-t biztosít a nyilvános IP-címekre irányuló összes kimenő forgalomhoz. Alapértelmezés szerint a Azure Firewall nem hálózati szabályokkal használja az SNAT-t, ha a cél IP-cím [IANA RFC 1918-onként](https://tools.ietf.org/html/rfc1918)magánhálózati IP-címtartományban van. Az alkalmazásszabályok alkalmazása mindig transzparens [proxy használatával](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) történik, függetlenül a cél IP-címével.

Ez a logika jól működik, ha a forgalmat közvetlenül az internetre irányítja. Ha azonban engedélyezte a [](forced-tunneling.md)kényszerített bújtatást, az internetes forgalmat a rendszer az AzureFirewallSubnet egyik magánhálózati IP-címére SNAT-ként használja, elrejtve a forrást a helyszíni tűzfal elől.

Ha a szervezet nyilvános IP-címtartományt használ a magánhálózatok számára, a Azure Firewall SNAT az AzureFirewallSubnet egyik magánhálózati IP-címére használja a forgalmat. Konfigurálhatja azonban úgy a  Azure Firewall, hogy ne sNAT-t állítson be a nyilvános IP-címtartományhoz. Ha például egyedi IP-címet szeretne megadni, a következőként adhatja meg: `192.168.1.10` . Ip-címtartomány megadásához a következőt adhatja meg: `192.168.1.0/24` .

- Ha úgy Azure Firewall,  hogy a cél IP-címétől függetlenül soha ne használjon SNAT-t, használja a **0.0.0.0/0** magánhálózati IP-címtartományt. Ezzel a konfigurációval a Azure Firewall soha nem irányíthatja közvetlenül az internetre a forgalmat. 

- Ha a tűzfalat a célcímtől függetlenül mindig  SNAT-re konfigurálja, használja a **255.255.255.255/32** címet magánhálózati IP-címtartományként.

> [!IMPORTANT]
> A megadott privát címtartomány csak a hálózati szabályokra vonatkozik. Jelenleg az alkalmazásszabályok mindig SNAT-t használnak.

> [!IMPORTANT]
> Ha saját magánhálózati IP-címtartományokat szeretne megadni, és meg szeretné tartani az alapértelmezett IANA RFC 1918 címtartományokat, győződjön meg arról, hogy az egyéni lista tartalmazza az IANA RFC 1918 tartományt. 

Az SNAT magánhálózati IP-címeit az alábbi módszerekkel konfigurálhatja. A magánhálózati SNAT-címeket a konfigurációnak megfelelő módszerrel kell konfigurálnia. A tűzfal-házirendhez társított tűzfalaknak meg kell adniuk a tartományt a házirendben, és nem kell használniuk a `AdditionalProperties` értéket.


|Metódus            |Klasszikus szabályok használata  |Tűzfal-szabályzat használata  |
|---------|---------|---------|
|Azure Portal     | [Támogatott](#classic-rules-3)| [Támogatott](#firewall-policy-1)|
|Azure PowerShell     |[Konfigurálja `PrivateRange`](#classic-rules)|jelenleg nem támogatott|
|Azure CLI|[Konfigurálja `--private-ranges`](#classic-rules-1)|jelenleg nem támogatott|
|ARM-sablon     |[konfigurálás `AdditionalProperties` a tűzfaltulajdonságban](#classic-rules-2)|[konfigurálás `snat/privateRanges` tűzfal-házirendben](#firewall-policy)|


## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>SNAT magánhálózati IP-címtartományok konfigurálása – Azure PowerShell
### <a name="classic-rules"></a>Klasszikus szabályok

A tűzfalhoz Azure PowerShell IP-címtartományok megadásához használhatja a tűzfalat.

> [!NOTE]
> A `PrivateRange` tűzfal-házirendhez társított tűzfalaknál a tűzfaltulajdonság figyelmen kívül lesz hagyva. A tulajdonságát a SNAT magánhálózati IP-címtartományok konfigurálása – `SNAT` `firewallPolicies` [ARM-sablonban leírtak szerint kell használnia.](#firewall-policy)

#### <a name="new-firewall"></a>Új tűzfal

A klasszikus szabályokat használó új tűzfalak esetén a Azure PowerShell parancsmag a következő:

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> A Azure Firewall üzembe `New-AzFirewall` helyezéséhez meglévő virtuális hálózatra és nyilvános IP-címre van szükség. A [teljes üzembe helyezési útmutatót Azure Firewall és Azure PowerShell](deploy-ps.md) üzembe helyezési útmutatót itt láthatja: Deploy and configure Azure PowerShell using Azure PowerShell(Üzembe helyezés és konfigurálás)

> [!NOTE]
> Az IANAPrivateRanges az aktuális alapértelmezett értékekre van kibontva a Azure Firewall a többi tartomány hozzáadása közben. Ahhoz, hogy az IANAPrivateRanges alapértelmezett érték maradjon a priváttartomány-specifikációban, annak a specifikációban kell maradnia, ahogy az alábbi `PrivateRange` példákban látható.

További információ: [New-AzFirewall.](/powershell/module/az.network/new-azfirewall)

#### <a name="existing-firewall"></a>Meglévő tűzfal

Meglévő tűzfal klasszikus szabályokkal való konfiguráláshoz használja a következő Azure PowerShell parancsmagokat:

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>SNAT magánhálózati IP-címtartományok konfigurálása – Azure CLI
### <a name="classic-rules"></a>Klasszikus szabályok

Az Azure CLI-t használhatja a magánhálózati IP-címtartományok megadására a tűzfalhoz klasszikus szabályok használatával. 

#### <a name="new-firewall"></a>Új tűzfal

A klasszikus szabályokat használó új tűzfalak esetén az Azure CLI-parancs a következő:

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> Az Azure Firewall Azure CLI-paranccsal való üzembe helyezéséhez további konfigurációs lépésekre van szükség a nyilvános IP-címek és `az network firewall create` AZ IP-konfiguráció létrehozásához. A [teljes üzembe helyezési útmutatóért Azure Firewall Üzembe](deploy-cli.md) helyezés és konfigurálás az Azure CLI használatával.

> [!NOTE]
> Az IANAPrivateRanges az aktuális alapértelmezett értékekre van kibontva a Azure Firewall a többi tartomány hozzáadása közben. Ahhoz, hogy az IANAPrivateRanges alapértelmezett érték maradjon a priváttartomány-specifikációban, annak a specifikációban kell maradnia, ahogy az alábbi `private-ranges` példákban látható.

#### <a name="existing-firewall"></a>Meglévő tűzfal

Meglévő tűzfal klasszikus szabályokkal való konfiguráláshoz az Azure CLI-parancs a következő:

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>SNAT magánhálózati IP-címtartományok konfigurálása – ARM-sablon
### <a name="classic-rules"></a>Klasszikus szabályok

Az SNAT ARM-Template deployment konfigurálásakor a következőket használhatja a `additionalProperties` tulajdonsághoz:

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```
### <a name="firewall-policy"></a>Tűzfal-szabályzat

A tűzfal-szabályzathoz társított Azure Firewall-tűzfalak a 2020-11-01-es API-verzió óta támogatják az SNAT privát tartományokat. Jelenleg sablon használatával frissítheti a SNAT privát tartományát a tűzfal házirenden. A következő minta úgy konfigurálja a tűzfalat, hogy **mindig** SNAT-hálózati forgalmat generáljon:

```json
{ 

            "type": "Microsoft.Network/firewallPolicies", 
            "apiVersion": "2020-11-01", 
            "name": "[parameters('firewallPolicies_DatabasePolicy_name')]", 
            "location": "eastus", 
            "properties": { 
                "sku": { 
                    "tier": "Standard" 
                }, 
                "snat": { 
                    "privateRanges": [255.255.255.255/32] 
                } 
            } 
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>SNAT magánhálózati IP-címtartományok konfigurálása – Azure Portal
### <a name="classic-rules"></a>Klasszikus szabályok

A tűzfalhoz Azure Portal IP-címtartományok megadásához használhatja a tűzfalat.

1. Válassza ki az erőforráscsoportot, majd válassza ki a tűzfalat.
2. Az Áttekintés **lapon** a **Magánhálózati IP-tartományok** lapon válassza ki az **alapértelmezett IANA RFC 1918 értéket.**

   Megnyílik **a Privát IP-előtagok szerkesztése** lap:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Magánhálózati IP-előtagok szerkesztése":::

1. Alapértelmezés szerint **az IANAPrivateRanges** van konfigurálva.
2. Szerkessze a környezet magánhálózati IP-címtartományát, majd válassza a **Mentés lehetőséget.**

### <a name="firewall-policy"></a>Tűzfal-szabályzat

1.  Válassza ki az erőforráscsoportot, majd válassza ki a tűzfal-szabályzatot.
2.  A Beállítások oszlopban válassza a **Magánhálózati IP-tartományok (SNAT)** **lehetőséget.**

    Alapértelmezés szerint **használja az alapértelmezett beállítást Azure Firewall házirend-SNAT viselkedése** van kiválasztva. 
3. Az SNAT-konfiguráció testreszabásához törölje a jelölést, és a **SNAT** végrehajtása alatt válassza ki a környezet SNAT-jának végrehajtásához szükséges feltételeket.
      :::image type="content" source="media/snat-private-range/private-ip-ranges-snat.png" alt-text="Magánhálózati IP-tartományok (SNAT)":::


4.   Kattintson az **Alkalmaz** gombra.

## <a name="next-steps"></a>Következő lépések

- További információ [a Azure Firewall bújtatásról.](forced-tunneling.md)