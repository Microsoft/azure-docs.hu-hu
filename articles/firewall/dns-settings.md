---
title: Azure Firewall DNS-beállítások
description: A Azure Firewall a DNS-kiszolgáló és a DNS-proxy beállításainak használatával konfigurálhatja.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: d6a79e87e9999dd520358e0722011cf4e54d8c63
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546244"
---
# <a name="azure-firewall-dns-settings"></a>Azure Firewall DNS-beállítások

Beállíthat egy egyéni DNS-kiszolgálót, és engedélyezheti a DNS-proxyt a Azure Firewall számára. Konfigurálja ezeket a beállításokat a tűzfal telepítésekor, vagy a **DNS-beállítások** lapon később konfigurálja azokat.

## <a name="dns-servers"></a>DNS-kiszolgálók

A DNS-kiszolgáló karbantartja és feloldja a tartományneveket az IP-címekre. Alapértelmezés szerint a Azure Firewall Azure DNS használ a névfeloldáshoz. A **DNS-kiszolgáló** beállítással konfigurálhatja a saját DNS-kiszolgálókat Azure Firewall névfeloldáshoz. Egyetlen kiszolgálót vagy több kiszolgálót is beállíthat.

> [!NOTE]
> A Azure Firewall Managerrel felügyelt Azure Firewall-példányok esetében a DNS-beállítások a társított Azure Firewall házirendben vannak konfigurálva.

### <a name="configure-custom-dns-servers---azure-portal"></a>Egyéni DNS-kiszolgálók konfigurálása – Azure Portal

1. A Azure Firewall **Beállítások** területen válassza a **DNS-beállítások** elemet.
2. A **DNS-kiszolgálók** területen beírhatja vagy hozzáadhat olyan meglévő DNS-kiszolgálókat, amelyek korábban meg lettek adva a virtuális hálózaton.
3. Kattintson a **Mentés** gombra.

A tűzfal most átirányítja a DNS-forgalmat a megadott DNS-kiszolgálókra a névfeloldáshoz.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="A D N S-kiszolgálók beállításait bemutató képernyőkép.":::

### <a name="configure-custom-dns-servers---azure-cli"></a>Egyéni DNS-kiszolgálók konfigurálása – Azure CLI

Az alábbi példa frissíti az egyéni DNS-kiszolgálókkal Azure Firewall az Azure CLI használatával.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Ehhez a parancshoz `az network firewall` telepíteni kell az Azure CLI bővítményt `azure-firewall` . A parancs használatával telepítheti `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers---azure-powershell"></a>Egyéni DNS-kiszolgálók konfigurálása – Azure PowerShell

Az alábbi példa a Azure PowerShell használatával frissíti Azure Firewall egyéni DNS-kiszolgálókkal.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>DNS-proxy

Beállíthatja, hogy a Azure Firewall DNS-proxyként működjön. A DNS-proxy az ügyfél virtuális gépei által a DNS-kiszolgálóra irányuló DNS-kérések közti közvetítő. Ha egyéni DNS-kiszolgálót állít be, akkor engedélyezze a DNS-proxyt a DNS-feloldások elkerüléséhez, és engedélyezze a teljes tartománynevek (FQDN) szűrését a hálózati szabályokban.

:::image type="content" source="media/dns-settings/dns-proxy-2.png" alt-text="D N S proxy-konfiguráció egy egyéni D N kiszolgáló használatával.":::


Ha nem engedélyezi a DNS-proxyt, akkor előfordulhat, hogy az ügyféltől érkező DNS-kérések egy másik időpontban utaznak egy DNS-kiszolgálóra, vagy más választ adnak vissza a tűzfalhoz képest. A DNS-proxy Azure Firewall az ügyfelek kéréseinek elérési útjában az inkonzisztencia elkerülése érdekében.

Ha Azure Firewall DNS-proxy, két gyorsítótárazási függvény lehetséges:

- **Pozitív gyorsítótár**: a DNS-feloldás sikeres. A tűzfal a csomag vagy objektum TTL (time to Live) értékét használja. 

- **Negatív gyorsítótár**: a DNS-feloldás nem válaszol, vagy nincs megoldás. A tűzfal egy órára gyorsítótárazza ezt az információt.

A DNS-proxy az összes megoldott IP-címet tárolja a hálózati szabályok teljes tartománynevéről. Ajánlott eljárásként használjon olyan teljes tartományneveket, amelyek egy IP-címhez vannak feloldva.  

### <a name="dns-proxy-configuration"></a>DNS-proxy konfigurációja

A DNS-proxy konfigurálásához három lépés szükséges:
1. Engedélyezze a DNS-proxyt Azure Firewall DNS-beállításokban.
2. Igény szerint konfigurálhatja az egyéni DNS-kiszolgálót, vagy használhatja a megadott alapértelmezett értéket.
3. Konfigurálja a Azure Firewall magánhálózati IP-címet egyéni DNS-címként a virtuális hálózat DNS-kiszolgálójának beállításaiban. Ez a beállítás biztosítja, hogy a DNS-forgalom Azure Firewallre legyen irányítva.

#### <a name="configure-dns-proxy---azure-portal"></a>DNS-proxy konfigurálása – Azure Portal

A DNS-proxy konfigurálásához konfigurálnia kell a virtuális hálózati DNS-kiszolgálók beállítást a tűzfal magánhálózati IP-címének használatára. Ezután engedélyezze a DNS-proxyt a Azure Firewall **DNS-beállításokban**.

##### <a name="configure-virtual-network-dns-servers"></a>Virtuális hálózati DNS-kiszolgálók konfigurálása 

1. Válassza ki azt a virtuális hálózatot, ahol a DNS-forgalmat a Azure Firewall-példányon keresztül irányítja a rendszer.
2. A **Beállítások** területen válassza a **DNS-kiszolgálók** elemet.
3. A **DNS-kiszolgálók** területen válassza az **Egyéni** lehetőséget.
4. Adja meg a tűzfal magánhálózati IP-címét.
5. Kattintson a **Mentés** gombra.
6. Indítsa újra a virtuális hálózathoz csatlakozó virtuális gépeket, hogy azok hozzá legyenek rendelve az új DNS-kiszolgáló beállításaihoz. A virtuális gépek továbbra is a jelenlegi DNS-beállításokat használják, amíg újra nem indulnak.

##### <a name="enable-dns-proxy"></a>DNS-proxy engedélyezése

1. Válassza ki Azure Firewall példányát.
2. A **Beállítások** területen válassza a **DNS-beállítások** elemet.
3. Alapértelmezés szerint a **DNS-proxy** le van tiltva. Ha ez a beállítás engedélyezve van, a tűzfal figyeli a 53-es portot, és továbbítja a DNS-kéréseket a konfigurált DNS-kiszolgálókra.
4. Tekintse át a **DNS-kiszolgálók** konfigurációját, és győződjön meg arról, hogy a beállítások megfelelőek-e a környezetéhez.
5. Kattintson a **Mentés** gombra.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="A D N S proxy beállításait bemutató képernyőkép.":::

#### <a name="configure-dns-proxy---azure-cli"></a>DNS-proxy konfigurálása – Azure CLI

Az Azure CLI-vel konfigurálhatja a DNS-proxy beállításait a Azure Firewallban. Azt is megteheti, hogy a virtuális hálózatokat a DNS-kiszolgálóként való Azure Firewall használatára is frissíti.

##### <a name="configure-virtual-network-dns-servers"></a>Virtuális hálózati DNS-kiszolgálók konfigurálása

A következő példa úgy konfigurálja a virtuális hálózatot, hogy a Azure Firewall használja DNS-kiszolgálóként.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>DNS-proxy engedélyezése

A következő példa engedélyezi a DNS-proxy szolgáltatást a Azure Firewallban.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>DNS-proxy konfigurálása – Azure PowerShell

A Azure PowerShell a DNS-proxybeállítások Azure Firewall-ben történő konfigurálásához használható. Azt is megteheti, hogy a virtuális hálózatokat a DNS-kiszolgálóként való Azure Firewall használatára is frissíti.

##### <a name="configure-virtual-network-dns-servers"></a>Virtuális hálózati DNS-kiszolgálók konfigurálása

A következő példa konfigurálja a virtuális hálózatot a Azure Firewall DNS-kiszolgálóként való használatára.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>DNS-proxy engedélyezése

A következő példa engedélyezi a DNS-proxy szolgáltatást a Azure Firewallban.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Következő lépések

[FQDN-szűrés a hálózati szabályokban](fqdn-filtering-network-rules.md)
