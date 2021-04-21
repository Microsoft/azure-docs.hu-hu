---
title: Tűzfalak Azure Key Vault virtuális hálózatok konfigurálása – Azure Key Vault
description: Részletes útmutató a tűzfalak és virtuális Key Vault konfiguráláshoz
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8352deb00f6954d862b9e44646cce1604e2c5428
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749617"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Tűzfalak Azure Key Vault virtuális hálózatok konfigurálása

Ez a cikk útmutatást nyújt a tűzfal Azure Key Vault konfiguráláshoz. Ez a dokumentum részletesen ismerteti a Key Vault tűzfal különböző konfigurációit, és részletes útmutatást nyújt a Azure Key Vault más alkalmazásokkal és Azure-szolgáltatásokkal való használatra való konfiguráláshoz.

További információ: Virtuális [hálózati szolgáltatásvégpont a Azure Key Vault.](overview-vnet-service-endpoints.md)

## <a name="firewall-settings"></a>Tűzfalbeállítások

Ez a szakasz a tűzfal konfigurálható Azure Key Vault különböző módjait fedi le.

### <a name="key-vault-firewall-disabled-default"></a>Key Vault tűzfal le van tiltva (alapértelmezés)

Alapértelmezés szerint új kulcstartó létrehozásakor a Azure Key Vault tűzfal le van tiltva. Minden alkalmazás és Azure-szolgáltatás hozzáférhet a kulcstartóhoz, és kéréseket küldhet a kulcstartónak. Vegye figyelembe, hogy ez a konfiguráció nem jelenti azt, hogy bármely felhasználó műveleteket hajt végre a kulcstartón. A kulcstartó továbbra is a key vaultban tárolt titkos kulcsokra, kulcsokra és tanúsítványokra korlátozza a Azure Active Directory és hozzáférési szabályzat engedélyeinek megkövetelével. A Key Vault-hitelesítéssel való részletesebb információkért lásd a Key Vault-hitelesítés alapjait [itt.](./authentication-fundamentals.md) További információ: [Azure Key Vault tűzfal mögötti hozzáférési adatok.](./access-behind-firewall.md)

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Key Vault tűzfal engedélyezve (csak megbízható szolgáltatások)

Ha engedélyezi a Key Vault tűzfalat, lehetősége lesz engedélyezni, hogy a megbízható Microsoft-szolgáltatások megkerüljék ezt a tűzfalat. A megbízható szolgáltatások listája nem fed le minden egyes Azure-szolgáltatást. Az Azure DevOps például nem szerepel a megbízható szolgáltatások listáján. **Ez nem jelenti azt, hogy a megbízható szolgáltatások listáján nem megjelenő szolgáltatások nem megbízhatók vagy nem biztonságosak.** A megbízható szolgáltatások listája magában foglalja azokat a szolgáltatásokat, ahol a Microsoft a szolgáltatáson futó összes kódot szabályozza. Mivel a felhasználók egyéni kódot írhatnak olyan Azure-szolgáltatásokban, mint az Azure DevOps, a Microsoft nem biztosít lehetőséget arra, hogy jóváhagyást hozzon létre a szolgáltatáshoz. Továbbá csak azért, mert egy szolgáltatás megjelenik a megbízható szolgáltatások listáján, nem jelenti azt, hogy minden forgatókönyvben engedélyezve van. 

Annak megállapításához, hogy egy használni próbált szolgáltatás szerepel-e a megbízható szolgáltatások listáján, tekintse meg a következő [dokumentumot itt.](./overview-vnet-service-endpoints.md#trusted-services)
Útmutatásért kövesse az itt található, a Portalra, az Azure CLI-re és a [PowerShellre vonatkozó utasításokat.](https://docs.microsoft.com/azure/key-vault/general/network-security#use-the-azure-portal)

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Key Vault tűzfal engedélyezése (IPv4-címek és -tartományok – Statikus IP-címek)

Ha engedélyezni szeretné, hogy egy adott szolgáltatás a Key Vault tűzfalon keresztül hozzáférjen a kulcstartóhoz, hozzáadhatja annak IP-címét a Key Vault tűzfalának engedélyezett listájához. Ez a konfiguráció statikus IP-címeket vagy jól ismert tartományokat használ. Ebben az esetben legfeljebb 1000 CIDR-tartomány lehet.

Ha engedélyeznie kell egy IP-címet vagy egy Azure-erőforrás tartományát, például egy webalkalmazást vagy egy logikai alkalmazást, hajtsa végre a következő lépéseket.

1. Bejelentkezés az Azure Portalra
1. Válassza ki az erőforrást (a szolgáltatás adott példányát)
1. Kattintson a Tulajdonságok panelre a Beállítások alatt
1. Keresse meg az "IP Address" (IP-cím) mezőt.
1. Másolja ki ezt az értéket vagy tartományt, és írja be a Key Vault-tűzfal engedélyezése listába.

Ha egy teljes Azure-szolgáltatást engedélyezni Key Vault tűzfalon keresztül, használja az Azure nyilvánosan dokumentált adatközponti IP-címeinek [listáját itt.](https://www.microsoft.com/download/details.aspx?id=41653) Keresse meg a kívánt szolgáltatáshoz társított IP-címeket a kívánt régióban, és adja hozzá ezeket az IP-címeket a Key Vault tűzfalához a fenti lépések segítségével.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Key Vault tűzfal engedélyezése (virtuális hálózatok – dinamikus IP-k)

Ha egy Azure-erőforrást , például egy virtuális gépet szeretne engedélyezni a kulcstartón keresztül, előfordulhat, hogy nem tud statikus IP-címeket használni, és nem szeretné engedélyezni az Azure Virtual Machines összes IP-címét a kulcstartó eléréséhez.

Ebben az esetben létre kell hoznia az erőforrást egy virtuális hálózaton belül, majd engedélyeznie kell az adott virtuális hálózatból és alhálózatból származó forgalom számára a kulcstartóhoz való hozzáférést. Ehhez hajtsa végre a következő lépéseket.

1. Bejelentkezés az Azure Portalra
1. Válassza ki a konfigurálni kívánt kulcstartót
1. Válassza a "Hálózat" panelt
1. Válassza a "+ Meglévő virtuális hálózat hozzáadása" lehetőséget
1. Válassza ki azt a virtuális hálózatot és alhálózatot, amely számára engedélyezni szeretné a Key Vault tűzfalát.

### <a name="key-vault-firewall-enabled-private-link"></a>Key Vault tűzfal engedélyezve (Private Link)

A privát kapcsolati kapcsolat kulcstartón való konfigurálásának mikéntjére itt található a [dokumentum.](./private-link-service.md)

> [!IMPORTANT]
> A tűzfalszabályok életbe lépését követően [a](security-overview.md#privileged-access) felhasználók csak akkor hajthatnak végre Key Vault adatsíkműveleteket, ha a kéréseik engedélyezett virtuális hálózatokról vagy IPv4-címtartományból származnak. Ez vonatkozik a Key Vault hozzáférése Azure Portal. Bár a felhasználók megkereshetnek egy kulcstartót a Azure Portal, előfordulhat, hogy nem tudják listába sorolni a kulcsokat, titkos kulcsokat vagy tanúsítványokat, ha az ügyfélszámítógépük nem szerepel az engedélyezett listában. Ez hatással van a Key Vault Azure-szolgáltatások által nyújtott szolgáltatásválasztóra is. Előfordulhat, hogy a felhasználók láthatják a kulcstartók listáját, a kulcsok listáját azonban nem, ha a tűzfalszabályok megakadályozzák az ügyfélszámítógépüket.

> [!NOTE]
> Vegye figyelembe az alábbi konfigurációs korlátozásokat:
> * Legfeljebb 127 virtuális hálózati szabály és 127 IPv4-szabály engedélyezett. 
> * Az IP-hálózati szabályok csak nyilvános IP-címekhez engedélyezettek. A magánhálózatok számára fenntartott IP-címtartományok (az RFC 1918-ban meghatározottak szerint) nem engedélyezettek az IP-szabályokban. A magánhálózatok a következő címekkel **kezdődnek: 10.**, **172.16-31**, **és 192.168.**. 
> * Jelenleg csak az IPv4-címek támogatottak.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A következő módon konfigurálhatja a Key Vault tűzfalakat és virtuális hálózatokat a Azure Portal:

1. Keresse meg a biztonságossá kívánt kulcstartót.
2. Válassza **a Hálózat lehetőséget,** majd a **Tűzfalak és virtuális hálózatok** lapot.
3. A **Hozzáférés engedélyezése innen: alatt** válassza a Kijelölt hálózatok **lehetőséget.**
4. Ha meglévő virtuális hálózatokat szeretne hozzáadni a tűzfalakhoz és a virtuális hálózati szabályokhoz, válassza **a + Meglévő virtuális hálózatok hozzáadása lehetőséget.**
5. A megnyíló új panelen válassza ki azt az előfizetést, virtuális hálózatokat és alhálózatokat, amelyek hozzáférését engedélyezni szeretné a kulcstartóhoz. Ha a kiválasztott virtuális hálózatok és alhálózatok nincsenek engedélyezve a szolgáltatásvégpontokkal, erősítse meg, hogy engedélyezni szeretné a szolgáltatásvégpontokat, majd válassza az **Engedélyezés lehetőséget.** A effektus 15 percet is igénybe vehet.
6. Az **IP Networks (IP-hálózatok)** területen adja hozzá az IPv4-címtartományokat. Ehhez írja be az IPv4-címtartományok szöveget a [CIDR (Classless Inter-domain Routing) írási](https://tools.ietf.org/html/rfc4632) vagy egyéni IP-címekbe.
7. Ha engedélyezni szeretné, hogy a Microsoft Megbízható szolgáltatások megkerüljék a Key Vault tűzfalat, válassza az "Igen" lehetőséget. A megbízható szolgáltatások aktuális Key Vault tekintse meg a következő hivatkozást. [Azure Key Vault szolgáltatások](./overview-vnet-service-endpoints.md#trusted-services)
7. Kattintson a **Mentés** gombra.

Új virtuális hálózatokat és alhálózatokat is hozzáadhat, majd engedélyezheti a szolgáltatásvégpontokat az újonnan létrehozott virtuális hálózatok és alhálózatok számára a + Új virtuális hálózat **hozzáadása lehetőség kiválasztásával.** Ezután kövesse az utasításokat.

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata 

A következő módon konfigurálhatja a Key Vault tűzfalakat és virtuális hálózatokat az Azure CLI használatával

1. [Telepítse az Azure CLI-t,](/cli/azure/install-azure-cli) és [jelentkezzen be.](/cli/azure/authenticate-azure-cli)

2. Sorolja fel az elérhető virtuális hálózati szabályokat. Ha még nem adott meg szabályokat ehhez a kulcstartóhoz, a lista üres lesz.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Szolgáltatásvégpont engedélyezése a Key Vault virtuális hálózaton és alhálózaton.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Adjon hozzá egy hálózati szabályt egy virtuális hálózathoz és egy alhálózathoz.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Adjon hozzá egy IP-címtartományt, amelyről engedélyezi a forgalmat.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Ha a kulcstartónak elérhetőnek kell lennie bármely megbízható szolgáltatás számára, állítsa a `bypass` következőre: `AzureServices` .
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Kapcsolja be a hálózati szabályokat az alapértelmezett művelet `Deny` beállításával.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A következő módon konfigurálhatja a Key Vault tűzfalakat és virtuális hálózatokat a PowerShell használatával:

1. Telepítse a legújabb [Azure PowerShell,](/powershell/azure/install-az-ps)és [jelentkezzen be.](/powershell/azure/authenticate-azureps)

2. Az elérhető virtuális hálózati szabályok felsorolása. Ha nem adott meg szabályokat ehhez a kulcstartóhoz, a lista üres lesz.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Engedélyezze a szolgáltatásvégpontot Key Vault meglévő virtuális hálózaton és alhálózaton található virtuális gépeken.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Adjon hozzá egy hálózati szabályt egy virtuális hálózathoz és alhálózathoz.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Adjon hozzá egy IP-címtartományt, amelyről engedélyezi a forgalmat.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Ha a kulcstartónak elérhetőnek kell lennie bármely megbízható szolgáltatás számára, állítsa a `bypass` következőre: `AzureServices` .
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Kapcsolja be a hálózati szabályokat az alapértelmezett művelet `Deny` beállításával.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Hivatkozások
* ARM-sablonreferenciák: [Azure Key Vault ARM-sablonreferenciája](/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI-parancsok: [az keyvault network-rule](/cli/azure/keyvault/network-rule)
* Azure PowerShell: [Get-AzKeyVault,](/powershell/module/az.keyvault/get-azkeyvault) [Add-AzKeyVaultNetworkRule,](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule) [Remove-AzKeyVaultNetworkRule,](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule) [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Következő lépések

* [Virtuális hálózati szolgáltatásvégpont az Key Vault](overview-vnet-service-endpoints.md)
* [Azure Key Vault biztonsági áttekintés](security-overview.md)
