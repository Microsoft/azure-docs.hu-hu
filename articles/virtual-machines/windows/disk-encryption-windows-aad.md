---
title: Azure Disk Encryption Windows rendszerű virtuális gépekhez való Azure AD-vel való használat (korábbi kiadás)
description: Ez a cikk útmutatást nyújt a windowsos IaaS Microsoft Azure lemeztitkosítás engedélyezéséhez.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 081ead434dbdc3b9c348e3fa35068a638bab6e62
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776860"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure Disk Encryption Windows rendszerű virtuális gépekhez való Azure AD-vel való használat (korábbi kiadás)

**A virtuális gépek új Azure Disk Encryption szükségtelenné teszi, hogy Azure AD-alkalmazásparamétert biztosítsunk a virtuális gépek lemeztitkosításának engedélyezéséhez. Az új kiadásban már nem kell Azure AD-beli hitelesítő adatokat megadnia a titkosítás engedélyezése lépés során. Minden új virtuális gépnek titkosítva kell lennie az Azure AD alkalmazásparaméterei nélkül az új kiadással. A virtuális gépek lemeztitkosításának új kiadással történő engedélyezésére vonatkozó utasításokért lásd: Azure Disk Encryption [Windows VMS-hez.](disk-encryption-windows.md) Az Azure AD-alkalmazásparaméterekkel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is az AAD-szintaxissal kell fenntartani őket.**


Számos lemeztitkosítási forgatókönyvet engedélyezhet, és a lépések a forgatókönyvtől függően változhatnak. A következő szakaszok a Windows IaaS virtuális gépek forgatókönyvét ismertetik részletesebben. A lemeztitkosítás használata előtt Azure Disk Encryption [előfeltételeit.](disk-encryption-overview-aad.md) 


>[!IMPORTANT]
> - A lemezek [titkosítása](snapshot-copy-managed-disk.md) előtt készítsen pillanatképet és/vagy készítsen biztonsági másolatot. A biztonsági másolatok biztosítják, hogy a titkosítás során váratlan hiba esetén helyreállítási lehetőség legyen lehetséges. A felügyelt lemezekkel virtuális gépek biztonsági mentést igényelnek a titkosítás előtt. A biztonsági mentés után a [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával titkosíthatja a felügyelt lemezeket a -skipVmBackup paraméter megadásával. A titkosított virtuális gépek biztonsági mentésének és visszaállításának további információiért lásd: Titkosított Azure-beli virtuális gépek biztonsági másolata [és visszaállítása.](../../backup/backup-azure-vms-encryption.md) 
>
> - A titkosítás titkosítása vagy letiltása a virtuális gép újraindítását okozhatja.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Titkosítás engedélyezése a Marketplace-ről létrehozott új IaaS virtuális gépeken
A lemeztitkosítást új IaaS Windows rendszerű virtuális gépeken az Azure Marketplace-ről engedélyezheti egy Resource Manager sablonnal. A sablon létrehoz egy új titkosított Windows rendszerű virtuális gépet a Windows Server 2012 katalógus rendszerképének használatával.

1. A Resource Manager [a](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)Deploy to Azure (Üzembe **helyezés az Azure-ban) elemre.**

2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, a paramétereket, a jogi feltételeket és a szerződést. Kattintson **a Vásárlás gombra** egy új IaaS virtuális gép üzembe helyezéséhez, ahol a titkosítás engedélyezve van.

3. A sablon üzembe helyezése után ellenőrizze a virtuális gép titkosítási állapotát az előnyben részesített módszerrel:
     - Ellenőrizze az Azure CLI-t az [az vm encryption show paranccsal.](/cli/azure/vm/encryption#az_vm_encryption_show) 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Ellenőrizze a Azure PowerShell a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmaggal. 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Válassza ki a virtuális gépet, majd kattintson a **Lemezek** elemre a **Beállítások** fejléc alatt a titkosítás állapotának ellenőrzéséhez a portálon. A Titkosítás **alatti** diagramon láthatja, hogy engedélyezve van-e. 
           ![Azure Portal – Lemeztitkosítás engedélyezve](../media/disk-encryption/disk-encryption-fig2.png)

A következő táblázat felsorolja Resource Manager Marketplace-forgatókönyvben található új virtuális gépek sablonparaméterét az Azure AD-ügyfélazonosító használatával:

| Paraméter | Leírás | 
| --- | --- |
| adminUserName | A virtuális gép rendszergazdai felhasználóneve. |
| adminPassword | A virtuális gép rendszergazdai felhasználói jelszava. |
| newStorageAccountName | Az operációs rendszer és az adat VHD-ket tároló tárfiók neve. |
| vmSize | A virtuális gép mérete. Jelenleg csak a Standard A, D és G sorozat támogatott. |
| virtualNetworkName | Annak a virtuális hálózatnak a neve, amelyhez a virtuális gép hálózati adapterének tartozni kell. |
| subnetName (alhálózat neve) | Annak a virtuális hálózatnak az alhálózata, amelyhez a virtuális gép hálózati adapterének tartozni kell. |
| AADClientID | Annak az Azure AD-alkalmazásnak az ügyfél-azonosítója, amely engedéllyel rendelkezik a titkos kulcsok kulcstartóba írásához. |
| AADClientSecret (AADClientSecret) | Az Azure AD-alkalmazás titkos ügyfélkulcsa, amely engedéllyel rendelkezik a titkos kulcsok kulcstartóba írásához. |
| keyVaultURL | Annak a kulcstartónak az URL-címe, amelybe a BitLocker-kulcsot fel kell tölteni. Ezt a parancsmag vagy az Azure CLI használatával `(Get-AzKeyVault -VaultName "MyKeyVault&quot; -ResourceGroupName &quot;MyKeyVaultResourceGroupName").VaultURI` kaphatja meg `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | A létrehozott BitLocker-kulcs titkosításához használt kulcstitkosítási kulcs URL-címe (nem kötelező). </br> </br>A KeyEncryptionKeyURL nem kötelező paraméter. A kulcstartóban tárolt adattitkosítási kulcs (passphrase secret) további védelme érdekében használhatja a saját KEK-et. |
| keyVaultResourceGroup | A kulcstartó erőforráscsoportja. |
| vmName | Annak a virtuális gépnek a neve, amelyről a titkosítási műveletet végre kell hajtanunk. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a> Titkosítás engedélyezése meglévő vagy futó IaaS Windows rendszerű virtuális gépeken
Ebben a forgatókönyvben engedélyezheti a titkosítást sablon, PowerShell-parancsmagok vagy CLI-parancsok használatával. A következő szakaszok részletesebben ismertetik a Azure Disk Encryption. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a> Titkosítás engedélyezése meglévő vagy futó virtuális gépeken Azure PowerShell 
A [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban. A Azure Disk Encryption-parancsmagokkal való titkosítás engedélyezésével kapcsolatos információkért tekintse meg a következő blogbejegyzéseket: [Explore Azure Disk Encryption with Azure PowerShell - Part 1](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell) and Explore Azure Disk Encryption with Azure PowerShell - Part [2](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell-part-2).

-  **Futó virtuális gép titkosítása titkos ügyféltitk használatával:** Az alábbi szkript inicializálja a változókat, és futtatja Set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoportnak, a virtuális gépnek, a kulcstartónak, az AAD-alkalmazásnak és a titkos ügyfélkulcsnak már létre kellett volna hozva előfeltételként. Cserélje le a MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM, a MySecureVault, a My-AAD-client-ID és a My-AAD-client-secret értékeket a saját értékeire.
     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Egy futó virtuális gép titkosítása KEK használatával az ügyfél titkos kódba burkolva:** Azure Disk Encryption lehetővé teszi egy meglévő kulcs megadását a kulcstartóban a titkosítás engedélyezése során létrehozott lemeztitkosítási titkos kulcsok becsomagolása érdekében. Ha kulcstitkosítási kulcs van megadva, a Azure Disk Encryption ezzel a kulccsal burk szóval burköli a titkosítási titkos kódokat, mielőtt Key Vault. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > A disk-encryption-keyvault paraméter értékének szintaxisa a teljes azonosító sztring: /subscriptions/[előfizetés-azonosító-guid]/resourceGroups/[erőforráscsoport-neve]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A key-encryption-key paraméter értékének szintaxisa a KEK teljes URI-ját használja a következő módon: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Az IaaS virtuális gép titkosítási állapotának ellenőrzéshez használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Lemeztitkosítás letiltása:** A titkosítás letiltásához használja a [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Titkosítás engedélyezése meglévő vagy futó virtuális gépeken az Azure CLI használatával
Az [az vm encryption enable paranccsal](/cli/azure/vm/encryption#az_vm_encryption_enable) engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban.

- **Futó virtuális gép titkosítása titkos ügyféltitk használatával:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Egy futó virtuális gép titkosítása KEK használatával az ügyfél titkos kódba burkolva:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A disk-encryption-keyvault paraméter értékének szintaxisa a teljes azonosító sztring: /subscriptions/[előfizetés-azonosító-guid]/resourceGroups/[erőforráscsoport-neve]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A key-encryption-key paraméter értékének szintaxisa a KEK teljes URI-ját használja a következő módon: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának ellenőrzéshez használja [az az vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>A Resource Manager használata
A lemeztitkosítást engedélyezheti meglévő vagy futó IaaS Windows rendszerű virtuális gépeken az [Azure-ban,](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)ha a Resource Manager-sablonnal titkosít egy futó Windows rendszerű virtuális gépet.


1. Az Azure gyorsindítási sablonban kattintson az Üzembe **helyezés az Azure-ban elemre.**

2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, a paramétereket, a jogi feltételeket és a szerződést. Kattintson **a Vásárlás gombra** a titkosítás engedélyezéséhez a meglévő vagy futó IaaS virtuális gépen.

A következő táblázat felsorolja Resource Manager Azure AD-ügyfélazonosítót tartalmazó meglévő vagy futó virtuális gépek sablonparaméterét:

| Paraméter | Leírás |
| --- | --- |
| AADClientID | Annak az Azure AD-alkalmazásnak az ügyfél-azonosítója, amely engedéllyel rendelkezik a titkos kulcsok kulcstartóba írásához. |
| AADClientSecret (AADClientSecret) | Annak az Azure AD-alkalmazásnak az ügyfélkulcsa, amely engedéllyel rendelkezik a titkos kulcsok kulcstartóba írásához. |
| keyVaultName | Annak a kulcstartónak a neve, amelybe a BitLocker-kulcsot fel kell tölteni. Ezt a parancsmag vagy az `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` Azure CLI-parancs használatával kaphatja meg `az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | A létrehozott BitLocker-kulcs titkosításához használt kulcstitkosítási kulcs URL-címe. Ez a paraméter nem kötelező, ha a UseExistingKek legördülő listában a **nokek** lehetőséget választja. Ha a **kek lehetőséget választja** a UseExistingKek legördülő listában, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| volumeType (kötet típusa) | A titkosítási művelet által érintett kötet típusa. Érvényes értékek: _OS,_ _Data_, és _All._ |
| sequenceVersion | A BitLocker-művelet sorozatverziója. Növelje ezt a verziószámot minden alkalommal, amikor lemeztitkosítási műveletet hajt végre ugyanazon a virtuális gépen. |
| vmName | Annak a virtuális gépnek a neve, amelyről a titkosítási műveletet végre kell hajtanunk. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Ügyfél által titkosított VHD-kből és titkosítási kulcsokból létrehozott új IaaS virtuális gépek
Ebben a forgatókönyvben engedélyezheti a titkosítást a Resource Manager, PowerShell-parancsmagok vagy CLI-parancsok használatával. A következő szakaszok részletesebben ismertetik a sablon Resource Manager CLI-parancsokat. 

Az Azure-ban használható, előre titkosított rendszerképek előkészítéséhez kövesse a függelékben található utasításokat. A rendszerkép létrehozása után a következő szakaszban található lépésekkel létrehozhat egy titkosított Azure-beli virtuális gépet.

* [Előre titkosított Windows virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"></a> Virtuális gépek titkosítása előre titkosított VIRTUÁLIS merevlemezekkel Azure PowerShell
A lemeztitkosítást a titkosított VHD-n a [Set-AzVMOSDisk PowerShell-parancsmag](/powershell/module/az.compute/set-azvmosdisk#examples)használatával engedélyezheti. Az alábbi példa néhány gyakori paramétert mutat be. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Titkosítás engedélyezése újonnan hozzáadott adatlemezen
A Windows [rendszerű virtuális gépekhez a PowerShell](attach-disk-ps.md)vagy a parancs használatával adhat hozzá [új Azure Portal.](attach-managed-disk-portal.md) 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Engedélyezze a titkosítást egy újonnan hozzáadott lemezen Azure PowerShell
 Ha a Windows rendszerű virtuális gépek új lemezét a PowerShell használatával titkosítja, meg kell adni egy új sorozatverziót. A sorozatverziónak egyedinek kell lennie. Az alábbi szkript létrehoz egy GUID-t a sorozatverzióhoz. Bizonyos esetekben előfordulhat, hogy az újonnan hozzáadott adatlemezt automatikusan titkosítja a Azure Disk Encryption bővítmény. Az automatikus titkosítás általában akkor történik meg, amikor a virtuális gép újraindul, miután az új lemez online állapotba kerül. Ezt általában az okozza, hogy a kötettípushoz az "Összes" lett megadva, amikor a lemeztitkosítás korábban futott a virtuális gépen. Ha automatikus titkosítás történik egy újonnan hozzáadott adatlemezen, javasoljuk, hogy futtasa újra a Set-AzVmDiskEncryptionExtension parancsmagot egy új sorozatverzióval. Ha az új adatlemez automatikusan titkosítva van, és nem szeretné, hogy titkosítva legyen, először az összes meghajtó titkosítását kell visszafejtenie, majd egy új sorozatverzióval kell újratitkosítania, amely az operációs rendszert adja meg a kötettípushoz. 
 

-  **Futó virtuális gép titkosítása titkos ügyféltitk használatával:** Az alábbi szkript inicializálja a változókat, és futtatja Set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép, a kulcstartó, az AAD-alkalmazás és a titkos ügyfélkulcs már létre lett hozva előfeltételként. Cserélje le a MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID és My-AAD-client-secret értékeket a saját értékeire. Ez a példa az "All" paramétert használja a -VolumeType paraméterhez, amely az operációs rendszert és az adatköteteket is tartalmazza. Ha csak az operációs rendszer kötetét szeretné titkosítani, használja az "OS" paramétert a -VolumeType paraméterhez. 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **Titkosítsa a futó virtuális gépeket a KEK használatával a titkos ügyfél titkos kódba burkolva:** Azure Disk Encryption segítségével megadhat egy meglévő kulcsot a kulcstartóban a titkosítás engedélyezése során létrehozott lemeztitkosítási titkos kulcsok becsomagolása érdekében. Ha kulcstitkosítási kulcs van megadva, a Azure Disk Encryption használja a titkosítási titkos kulcsok becsomagolása előtt, mielőtt Key Vault. Ez a példa az "All" paramétert használja a -VolumeType paraméterhez, amely az operációs rendszert és az adatköteteket is tartalmazza. Ha csak az operációs rendszer kötetét szeretné titkosítani, használja az "OS" paramétert a -VolumeType paraméterhez. 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > A disk-encryption-keyvault paraméter értékének szintaxisa a teljes azonosító sztring: /subscriptions/[előfizetés-azonosító-guid]/resourceGroups/[erőforráscsoport-neve]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A key-encryption-key paraméter értékének szintaxisa a KEK teljes URI-ját használja a következő módon: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Titkosítás engedélyezése újonnan hozzáadott lemezen az Azure CLI használatával
  Az Azure CLI-parancs automatikusan új sorozatverziót biztosít a titkosítás engedélyezéséhez a parancs futtatásakor. A volume-yype paraméter elfogadható értékei az Összes, az OPERÁCIÓS rendszer és az Adatok. Előfordulhat, hogy a kötettípus paraméterét operációs rendszerre vagy Adatokra kell módosítania, ha csak egy lemeztípust titkosít a virtuális géphez. A példák az "All" (Összes) paramétert használják kötettípus-paraméterként. 

-  **Futó virtuális gép titkosítása titkos ügyféltitk használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Egy futó virtuális gép titkosítása KEK használatával az ügyfél titkos kódba burkolva:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Engedélyezze a titkosítást az Azure AD-ügyfél tanúsítványalapú hitelesítésének használatával.
Az ügyfél-tanúsítvány hitelesítését KEK-ekkel vagy anélkül is használhatja. A PowerShell-szkriptek használata előtt már fel kell töltenie a tanúsítványt a kulcstartóba, és üzembe kell helyeznie a virtuális gépen. KEK használata esetén a KEK-nek már léteznie kell. További információért tekintse meg az előfeltételekről cikk Tanúsítványalapú hitelesítés az  [Azure AD-hez](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) című szakaszát.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Titkosítás engedélyezése tanúsítványalapú hitelesítéssel a Azure PowerShell

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Titkosítás engedélyezése tanúsítványalapú hitelesítés és KEK használatával Azure PowerShell

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Titkosítás letiltása
A titkosítást letilthatja az Azure PowerShell, az Azure CLI vagy egy Resource Manager használatával. 

- **Lemeztitkosítás letiltása a Azure PowerShell:** A titkosítás letiltásához használja a [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Titkosítás letiltása az Azure CLI-val:** A titkosítás letiltásához használja [az az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Tiltsa le a titkosítást Resource Manager sablonnal:** 

    1. Kattintson **a Deploy to Azure (Üzembe** helyezés az Azure-ban) elemre a [Disable disk encryption on running Windows VM (Lemeztitkosítás letiltása Windows rendszerű virtuális gépen) sablonból.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)
    2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a virtuális gépet, a jogi feltételeket és a szerződést.
    3.  Kattintson **a Vásárlás gombra** a lemeztitkosítás letiltásához egy futó Windows rendszerű virtuális gépen. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Disk Encryption áttekintés](disk-encryption-overview.md)
