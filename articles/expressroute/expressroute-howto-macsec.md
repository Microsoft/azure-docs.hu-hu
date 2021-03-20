---
title: 'Azure-ExpressRoute: a MACsec konfigurálása'
description: Ez a cikk segítséget nyújt a MACsec konfigurálásában a peremhálózati útválasztók és a Microsoft peremhálózati útválasztói közötti kapcsolatok biztonságossá tételéhez.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/22/2019
ms.author: duau
ms.openlocfilehash: 8ee7f42a4b05def7c2239c7a1e5bcef54f6f7e50
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89395774"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>A MACsec konfigurálása az ExpressRoute Direct-portokon

Ez a cikk segítséget nyújt a MACsec konfigurálásához a peremhálózati útválasztók és a Microsoft peremhálózati útválasztói közötti kapcsolatok biztonságossá tételéhez a PowerShell használatával.

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt erősítse meg a következőket:

* Megérti a [ExpressRoute közvetlen kiépítési munkafolyamatait](expressroute-erdirect-about.md).
* Létrehozott egy [ExpressRoute Direct port-erőforrást](expressroute-howto-erdirect.md).
* Ha helyileg szeretné futtatni a PowerShellt, ellenőrizze, hogy a Azure PowerShell legújabb verziója van-e telepítve a számítógépen.

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Jelentkezzen be, és válassza ki a megfelelő előfizetést

A konfiguráció elindításához jelentkezzen be az Azure-fiókjába, és válassza ki a használni kívánt előfizetést.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. hozzon létre Azure Key Vault, MACsec titkokat és felhasználói identitást

1. Hozzon létre egy Key Vault példányt egy új erőforráscsoport MACsec-titkainak tárolására.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Ha már rendelkezik kulcstartóval vagy erőforráscsoporthoz, újra felhasználhatja őket. Azonban fontos, hogy engedélyezze a [ **Soft-delete** funkciót](../key-vault/general/soft-delete-overview.md) a meglévő kulcstartóban. Ha a Soft-delete nincs engedélyezve, a következő parancsokkal engedélyezheti a használatát:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Hozzon létre egy felhasználói identitást.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Ha New-AzUserAssignedIdentity nem ismerhető fel érvényes PowerShell-parancsmagként, telepítse a következő modult (rendszergazdai módban), majd futtassa újra a fenti parancsot.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Hozzon létre egy kapcsolati társítási kulcsot (CAK) és egy kapcsolati társítási kulcs nevét (CKN), és tárolja őket a kulcstartóban.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Rendelje hozzá a GET engedélyt a felhasználói identitáshoz.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Ez az identitás mostantól a Key vaultról szerezheti be a titkokat, például a CAK és a CKN.
5. A ExpressRoute által használandó felhasználói identitás beállítása.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. a MACsec konfigurálása a ExpressRoute közvetlen portjain

### <a name="to-enable-macsec"></a>A MACsec engedélyezése

Minden ExpressRoute közvetlen példány két fizikai porttal rendelkezik. Dönthet úgy is, hogy egyszerre engedélyezi a MACsec mindkét porton, vagy egyszerre engedélyezi a MACsec egy porton. Ha a ExpressRoute Direct szolgáltatás már használatban van, a lehető legkevesebb időt vesz igénybe (ha a forgalmat egy aktív portra irányítja, miközben a másik portot szervizeli).

1. Állítsa be a MACsec titkokat és a titkosítást, és társítsa a felhasználói identitást a porthoz, hogy a ExpressRoute-felügyeleti kód szükség esetén hozzáférhessen a MACsec-titkokhoz.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. Választható Ha a portok felügyeleti állapotban vannak, a következő parancsok futtatásával állíthatja be a portokat.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    Ezen a ponton a MACsec engedélyezve van a ExpressRoute Direct portokon a Microsoft oldalon. Ha még nem konfigurálta a peremhálózati eszközökön, akkor folytathatja a konfigurálását ugyanazzal a MACsec-titokkal és-titkosítással.

### <a name="to-disable-macsec"></a>A MACsec letiltása

Ha a MACsec már nem kívánja használni a ExpressRoute Direct-példányon, a következő parancsokat futtathatja a letiltásához.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

Ezen a ponton a MACsec le van tiltva a ExpressRoute Direct-portok között a Microsoft oldalon.

### <a name="test-connectivity"></a>Kapcsolat tesztelése
Miután konfigurálta a MACsec (beleértve a MACsec kulcs frissítését) a ExpressRoute Direct-portok esetében, [ellenőrizze](expressroute-troubleshooting-expressroute-overview.md) , hogy az áramkör BGP-munkamenetei futnak-e. Ha még nem rendelkezik a portok egyetlen áramkörével sem, hozzon létre egyet először, és állítsa be az Azure-beli privát társat vagy a Microsoft-partnert. Ha a MACsec helytelenül van konfigurálva, beleértve a MACsec, a hálózati eszközök és a Microsoft hálózati eszközei között, a 3. rétegben nem jelenik meg az ARP-feloldás a 2. rétegben és a BGP-ben. Ha minden megfelelően van konfigurálva, akkor a BGP-útvonalakat a megfelelő módon kell meghirdetni mindkét irányban és az alkalmazás adatfolyamatában, ennek megfelelően a ExpressRoute.

## <a name="next-steps"></a>Következő lépések
1. [ExpressRoute-kör létrehozása a ExpressRoute Direct-on](expressroute-howto-erdirect.md)
2. [ExpressRoute-áramkör összekapcsolása egy Azure-beli virtuális hálózattal](expressroute-howto-linkvnet-arm.md)
3. [ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
