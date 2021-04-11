---
title: Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása – Azure Key Vault
description: Részletes útmutató Key Vault tűzfalak és virtuális hálózatok konfigurálásához
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 40094c00a4b896756c5c0e51116e0ae33ae2a096
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580735"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása

Ez a cikk útmutatást nyújt a Azure Key Vault tűzfal konfigurálásához. Ez a dokumentum részletesen ismerteti a Key Vault tűzfal különböző konfigurációit, és részletes útmutatást ad arról, hogyan konfigurálhatja Azure Key Vault más alkalmazásokkal és Azure-szolgáltatásokkal való együttműködéshez.

További információ: [virtuális hálózati szolgáltatás-végpontok Azure Key Vaulthoz](overview-vnet-service-endpoints.md).

## <a name="firewall-settings"></a>Tűzfalbeállítások

Ez a szakasz a Azure Key Vault tűzfal különböző konfigurálási módjait fedi le.

### <a name="key-vault-firewall-disabled-default"></a>Key Vault tűzfal Letiltva (alapértelmezett)

Alapértelmezés szerint az új kulcstartó létrehozásakor a Azure Key Vault tűzfal le van tiltva. Minden alkalmazás és Azure-szolgáltatás elérheti a kulcstartót, és kéréseket küldhet a kulcstartónak. Vegye figyelembe, hogy ez a konfiguráció nem jelenti azt, hogy minden felhasználó el tudja végezni a kulcstartón végrehajtott műveleteket. A Key Vault továbbra is korlátozza a Key vaultban tárolt titkokat, kulcsokat és tanúsítványokat Azure Active Directory hitelesítési és hozzáférési házirendi engedélyek megkövetelésével. A Key Vault hitelesítésének részletesebb megismeréséhez tekintse meg a Key Vault-hitelesítés alapjairól [szóló dokumentumot.](./authentication-fundamentals.md) További információ: [hozzáférés Azure Key Vault tűzfal mögött](./access-behind-firewall.md).

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Key Vault tűzfal engedélyezve (csak megbízható szolgáltatások esetén)

Ha engedélyezi a Key Vault tűzfalat, a "megbízható Microsoft-szolgáltatások engedélyezése a tűzfal megkerüléséhez" lehetőséget kap. A megbízható szolgáltatások listája nem fedi le az egyes Azure-szolgáltatásokat. Az Azure DevOps például nem szerepel a megbízható szolgáltatások listáján. **Ez nem jelenti azt, hogy azok a szolgáltatások, amelyek nem jelennek meg a megbízható szolgáltatások listáján, nem megbízhatóak vagy nem biztonságosak.** A megbízható szolgáltatások listája magában foglalja azokat a szolgáltatásokat, amelyeken a Microsoft a szolgáltatáson futó összes kódot vezérli. Mivel a felhasználók egyéni kódokat írhatnak az Azure-szolgáltatásokban, például az Azure DevOps, a Microsoft nem biztosítja a szolgáltatáshoz tartozó keretrendelés létrehozását. Továbbá, mivel egy szolgáltatás megjelenik a megbízható szolgáltatás listán, nem jelenti azt, hogy az összes forgatókönyv esetében engedélyezett. 

Ha meg szeretné állapítani, hogy a használni kívánt szolgáltatás szerepel-e a megbízható szolgáltatás listán, tekintse meg a [következő dokumentumot.](./overview-vnet-service-endpoints.md#trusted-services)
Útmutató a [portálon, az Azure CLI-ben és a PowerShellben](https://docs.microsoft.com/azure/key-vault/general/network-security#use-the-azure-portal) található útmutatást követve

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Key Vault tűzfal engedélyezve (IPv4-címek és tartományok – statikus IP-címek)

Ha engedélyezni szeretné egy adott szolgáltatás számára a Key Vault elérését a Key Vault tűzfalon keresztül, akkor a Key Vault tűzfal engedélyezési listájában adhatja hozzá az IP-címét. Ez a konfiguráció a statikus IP-címeket vagy a jól ismert tartományokat használó szolgáltatások esetében ajánlott. Ebben az esetben legfeljebb 1000 CIDR tartomány létezik.

Ha engedélyezni szeretné az Azure-erőforrások IP-címét vagy tartományát, például egy webalkalmazást vagy logikai alkalmazást, hajtsa végre az alábbi lépéseket.

1. Bejelentkezés az Azure Portalra
1. Válassza ki az erőforrást (a szolgáltatás adott példánya)
1. Kattintson a "tulajdonságok" panelre a "beállítások" alatt.
1. Keresse meg az "IP-cím" mezőt.
1. Másolja ezt az értéket vagy tartományt, és adja meg a Key Vault tűzfal engedélyezési listájában.

Ha egy [teljes Azure-](https://www.microsoft.com/download/details.aspx?id=41653)szolgáltatást szeretne engedélyezni a Key Vault tűzfalon keresztül, használja az Azure nyilvánosan dokumentált adatközponti IP-címeinek listáját. Keresse meg a kívánt régióban a szolgáltatáshoz társított IP-címeket, majd adja hozzá ezeket az IP-címeket a Key Vault-tűzfalhoz a fenti lépések végrehajtásával.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Key Vault tűzfal engedélyezve (virtuális hálózatok – dinamikus IP-címek)

Ha egy Azure-erőforrást, például egy virtuális gépet szeretne engedélyezni a Key vaulton keresztül, akkor előfordulhat, hogy nem tud statikus IP-címeket használni, és előfordulhat, hogy nem szeretné engedélyezni az Azure Virtual Machines összes IP-címét a kulcstartóhoz való hozzáféréshez.

Ebben az esetben létre kell hoznia az erőforrást egy virtuális hálózaton belül, majd engedélyeznie kell az adott virtuális hálózatról és alhálózatról érkező forgalmat a kulcstartó eléréséhez. Ehhez hajtsa végre az alábbi lépéseket.

1. Bejelentkezés az Azure Portalra
1. Válassza ki a konfigurálni kívánt kulcstartót
1. Válassza a hálózatkezelés panelt
1. Válassza a + meglévő virtuális hálózat hozzáadása lehetőséget
1. Válassza ki azt a virtuális hálózatot és alhálózatot, amelyet engedélyezni szeretne a Key Vault-tűzfalon keresztül.

### <a name="key-vault-firewall-enabled-private-link"></a>Key Vault tűzfal engedélyezve (privát hivatkozás)

Ha meg szeretné tudni, hogyan kell konfigurálni a privát kapcsolati kapcsolatot a kulcstartóban, tekintse meg [a dokumentumot.](./private-link-service.md)

> [!IMPORTANT]
> A tűzfalszabályok érvénybe léptetése után a felhasználók csak akkor hajthatják végre Key Vault [adatsík](secure-your-key-vault.md#data-plane-access-control) -műveleteket, ha a kérésük engedélyezett virtuális hálózatokból vagy IPv4-címtartományok származnak. Ez a Azure Portal Key Vault elérésére is vonatkozik. Bár a felhasználók megkereshetik a kulcstartót a Azure Portalból, előfordulhat, hogy nem tudják listázni a kulcsokat, titkokat vagy tanúsítványokat, ha az ügyfélszámítógépük nem szerepel az engedélyezési listán. Ez hatással van a más Azure-szolgáltatások Key Vault választóra is. Előfordulhat, hogy a felhasználók megtekinthetik a kulcstárolók listáját, de nem listázják a kulcsokat, ha a tűzfalszabályok megakadályozzák az ügyfélszoftvert.

> [!NOTE]
> Vegye figyelembe a következő konfigurációs korlátozásokat:
> * Legfeljebb 127 virtuális hálózati szabály és 127 IPv4-szabály engedélyezett. 
> * Az IP-hálózati szabályok csak nyilvános IP-címek esetén engedélyezettek. A magánhálózati hálózatok számára fenntartott IP-címtartományok (az RFC 1918-ben meghatározottak szerint) nem engedélyezettek az IP-szabályokban. A magánhálózatok közé tartoznak a következők: **10.**, **172.16-31** és **192,168.** 
> * Jelenleg csak IPv4-címek támogatottak.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A Key Vault tűzfalak és virtuális hálózatok konfigurálása a Azure Portal használatával:

1. Keresse meg a védeni kívánt Key vaultot.
2. Válassza a **hálózatkezelés** lehetőséget, majd válassza a **tűzfalak és a virtuális hálózatok** fület.
3. **A hozzáférés engedélyezése lehetőségnél** válassza a **kiválasztott hálózatok** elemet.
4. Ha meglévő virtuális hálózatokat szeretne hozzáadni a tűzfalakhoz és a virtuális hálózati szabályokhoz, válassza a **+ meglévő virtuális hálózatok hozzáadása** elemet.
5. A megnyíló új panelen válassza ki azt az előfizetést, virtuális hálózatot és alhálózatot, amely számára engedélyezni kívánja a kulcstartó elérését. Ha a kiválasztott virtuális hálózatok és alhálózatok nem rendelkeznek engedélyezett szolgáltatási végpontokkal, erősítse meg, hogy engedélyezni szeretné a szolgáltatási végpontokat, majd válassza az **Engedélyezés** lehetőséget. Az érvénybe léptetés akár 15 percet is igénybe vehet.
6. Az **IP-hálózatok** területen adja hozzá az IPv4-címtartományokat az IPv4-címtartományok beírásával a [CIDR (osztály nélküli tartományok közötti útválasztás) jelöléssel](https://tools.ietf.org/html/rfc4632) vagy az egyes IP-címekkel.
7. Ha engedélyezni szeretné a Microsoft megbízható szolgáltatásainak a Key Vault tűzfal megkerülését, válassza az Igen lehetőséget. Az aktuális Key Vault megbízható szolgáltatások teljes listáját az alábbi hivatkozásra kattintva tekintheti meg. [Megbízható szolgáltatások Azure Key Vault](./overview-vnet-service-endpoints.md#trusted-services)
7. Kattintson a **Mentés** gombra.

Új virtuális hálózatokat és alhálózatokat is hozzáadhat, majd engedélyezheti az újonnan létrehozott virtuális hálózatok és alhálózatok szolgáltatási végpontját az **+ új virtuális hálózat hozzáadása** lehetőség kiválasztásával. Ezután kövesse az utasításokat.

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata 

A következőképpen konfigurálhatja Key Vault tűzfalakat és virtuális hálózatokat az Azure CLI használatával

1. [Telepítse az Azure CLI](/cli/azure/install-azure-cli) -t, és [Jelentkezzen be](/cli/azure/authenticate-azure-cli).

2. A rendelkezésre álló virtuális hálózati szabályok listázása. Ha nem állított be szabályokat ehhez a kulcstartóhoz, a lista üres lesz.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Egy meglévő virtuális hálózat és alhálózat Key Vaultának engedélyezése a szolgáltatási végpont számára.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Hálózati szabály hozzáadása egy virtuális hálózathoz és alhálózathoz.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Adjon hozzá egy IP-címtartományt, amelyből engedélyezi a forgalmat.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Ha a kulcstárolónak bármely megbízható szolgáltatás számára elérhetőnek kell lennie, állítsa a következőre: `bypass` `AzureServices` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Állítsa be a hálózati szabályokat úgy, hogy az alapértelmezett műveletet állítja be `Deny` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A következőképpen konfigurálhatja Key Vault tűzfalakat és virtuális hálózatokat a PowerShell használatával:

1. Telepítse a legújabb [Azure PowerShell](/powershell/azure/install-az-ps), és [Jelentkezzen be](/powershell/azure/authenticate-azureps).

2. A rendelkezésre álló virtuális hálózati szabályok listázása. Ha nem állított be szabályokat ehhez a kulcstartóhoz, a lista üres lesz.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. A szolgáltatás végpontjának engedélyezése meglévő virtuális hálózat és alhálózat Key Vaultához.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Hálózati szabály hozzáadása egy virtuális hálózathoz és alhálózathoz.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Adjon hozzá egy IP-címtartományt, amelyből engedélyezi a forgalmat.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Ha a kulcstárolónak bármely megbízható szolgáltatás számára elérhetőnek kell lennie, állítsa a következőre: `bypass` `AzureServices` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Állítsa be a hálózati szabályokat úgy, hogy az alapértelmezett műveletet állítja be `Deny` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Hivatkozások
* ARM-sablon referenciája: [Azure Key Vault ARM-sablon referenciája](/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI-parancsok: [az Key Vault Network-Rule](/cli/azure/keyvault/network-rule)
* Azure PowerShell parancsmagok: [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Következő lépések

* [Virtuális hálózati szolgáltatás végpontjai Key Vault](overview-vnet-service-endpoints.md)
* [Azure Key Vault biztonsági áttekintés](security-overview.md)
