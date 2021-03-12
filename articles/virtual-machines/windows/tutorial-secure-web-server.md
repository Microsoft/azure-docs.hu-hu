---
title: 'Oktatóanyag: Windows-webkiszolgáló védelme TLS/SSL-tanúsítványokkal az Azure-ban'
description: Ebből az oktatóanyagból megtudhatja, hogyan használható a Azure PowerShell egy olyan Windows rendszerű virtuális gép biztonságossá tételéhez, amely az IIS-webkiszolgálót a Azure Key Vault tárolt TLS/SSL-tanúsítványokkal futtatja.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.subservice: security
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a1009d24c1fb8712e88075097460ad179629f478
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557403"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-tlsssl-certificates-stored-in-key-vault"></a>Oktatóanyag: webkiszolgáló védelme Azure-beli Windows rendszerű virtuális gépen a Key Vault tárolt TLS/SSL-tanúsítványokkal

> [!NOTE]
> Jelenleg ez a doc csak általánosított lemezképek esetében működik. Ha ezt az oktatóanyagot egy speciális lemezzel próbálja meg használni, hibaüzenetet fog kapni. 

A webkiszolgálók biztonságossá tételéhez Transport Layer Security (TLS), korábbi nevén SSL (SSL) tanúsítvány használható a webes forgalom titkosításához. Ezek a TLS/SSL-tanúsítványok Azure Key Vault tárolhatók, és lehetővé teszik a tanúsítványok biztonságos központi telepítését az Azure-beli Windows rendszerű virtuális gépekre (VM). Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Azure Key Vault létrehozása;
> * tanúsítvány létrehozása vagy feltöltése a Key Vaultba;
> * Virtuális gép létrehozása és az IIS-webkiszolgáló telepítése
> * A tanúsítvány behelyezése a virtuális gépre és az IIS konfigurálása TLS-kötéssel


## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.


## <a name="overview"></a>Áttekintés
Az Azure Key Vault megvédi a titkosítási kulcsokat és titkos kódokat, például a tanúsítványokat és jelszavakat. A Key Vault leegyszerűsíti a tanúsítványkezelési folyamatot, valamint lehetővé teszi a tanúsítványokhoz hozzáférő kulcsok feletti teljes körű felügyeletet. Létrehozhat egy önaláírt tanúsítványt a Key Vaultban, vagy feltölthet egy meglévő, megbízható tanúsítványt.

Beépített tanúsítványokat tartalmazó egyéni virtuális gép rendszerkép használata helyett a futó virtuális gépeken helyezi el a tanúsítványokat. Ez a folyamat biztosítja, hogy a legnaprakészebb tanúsítványok legyenek telepítve a webkiszolgálókon az üzembe helyezés alatt. Ha frissíti vagy lecseréli a tanúsítványt, nem kell új rendszerképet is létrehoznia a virtuális gépekről. A további virtuális gépek létrehozásakor a rendszer automatikusan alkalmazza a legújabb tanúsítványokat. A tanúsítványok a folyamat során egyszer sem hagyják el az Azure platformot, és nem jelennek meg szkriptekben, a parancssori előzményekben vagy sablonokban.


## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;
Key Vault és tanúsítványok létrehozása előtt hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával. A következő példában létrehozunk egy *myResourceGroupSecureWeb* nevű erőforráscsoportot az *USA keleti régiója* helyen:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Ezután hozzon létre egy Key Vaultt a [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Mindegyik Key Vaultnak egyedi névvel kell rendelkeznie, amely csak kisbetűkből állhat. Cserélje le a `mykeyvault` nevet a következő példában a saját egyedi Key Vault-névre:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Tanúsítvány létrehozása és tárolása a Key Vaultban
Éles használatra a megbízható szolgáltató által aláírt érvényes tanúsítványt kell importálnia az [import-AzKeyVaultCertificate](/powershell/module/az.keyvault/import-azkeyvaultcertificate)használatával. Ebben az oktatóanyagban a következő példa bemutatja, hogyan hozhat létre olyan önaláírt tanúsítványt az [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/add-azkeyvaultcertificate) , amely a [New-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy)alapértelmezett tanúsítvány-házirendjét használja. 

```azurepowershell-interactive
$policy = New-AzKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Először a [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) paranccsal állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most már létrehozhatja a virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm)használatával. Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre az *USA keleti régiója* helyen. Ha még nem léteznek, létrejönnek a támogató hálózati erőforrások. A biztonságos webes forgalom engedélyezéséhez a parancsmag megnyitja a *443*-as portot is.

```azurepowershell-interactive
# Create a VM
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

A virtuális gép létrehozása néhány percig tart. Az utolsó lépés az Azure Custom script bővítmény használatával telepíti az IIS-webkiszolgálót a [set-AzVmExtension](/powershell/module/az.compute/set-azvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Tanúsítvány hozzáadása egy virtuális géphez a Key Vaultból
A tanúsítvány Key Vaultból virtuális géphez való hozzáadásához szerezze be a tanúsítvány AZONOSÍTÓját a [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret)használatával. Adja hozzá a tanúsítványt a virtuális géphez az [Add-AzVMSecret](/powershell/module/az.compute/add-azvmsecret)használatával:

```azurepowershell-interactive
$certURL=(Get-AzKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Az IIS konfigurálása a tanúsítvány használatára
Az egyéni szkriptek bővítményét újra használhatja a [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) az IIS konfigurációjának frissítéséhez. Ez a frissítés alkalmazza a Key Vaultból beszúrt tanúsítványt az IIS-re, és konfigurálja a webes kötést:

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>A biztonságos webalkalmazás tesztelése
Szerezze be a virtuális gép nyilvános IP-címét a [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). A következő példa a korábban létrehozott `myPublicIP` IP-címét kéri le:

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Most nyisson meg egy webböngészőt, és írja be a `https://<myPublicIP>` címet a címsorba. Ha önaláírt tanúsítványt használt, a biztonsági figyelmeztetés elfogadásához válassza a **részletek** lehetőséget, majd **lépjen a weblapra**:

![Webböngésző biztonsági figyelmeztetésének elfogadása](./media/tutorial-secure-web-server/browser-warning.png)

Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

![Futó biztonságos IIS-hely megtekintése](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy olyan IIS-webkiszolgálót biztosított, amely Azure Key Vaultban tárolt TLS/SSL-tanúsítvánnyal rendelkezik. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Key Vault létrehozása;
> * tanúsítvány létrehozása vagy feltöltése a Key Vaultba;
> * Virtuális gép létrehozása és az IIS-webkiszolgáló telepítése
> * A tanúsítvány behelyezése a virtuális gépre és az IIS konfigurálása TLS-kötéssel

Kövesse ezt a hivatkozást az előre felépített virtuálisgép-szkriptek mintáinak megtekintéséhez.

> [!div class="nextstepaction"]
> [Windows rendszerű virtuális gép szkriptjeinek mintái](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)