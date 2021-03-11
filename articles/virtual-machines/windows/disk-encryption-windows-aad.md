---
title: Azure Disk Encryption az Azure AD-vel a Windows rendszerű virtuális gépekhez (előző kiadás)
description: Ez a cikk a Windows IaaS virtuális gépek Microsoft Azure lemezes titkosításának engedélyezéséhez nyújt útmutatást.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3b7f6f63953ba09e57e4586c698e16b9abb8aa1c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555278"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure Disk Encryption az Azure AD-vel a Windows rendszerű virtuális gépekhez (előző kiadás)

**A Azure Disk Encryption új kiadása szükségtelenné teszi az Azure AD-alkalmazás paramétereinek biztosítását a virtuális gépek lemezes titkosításának engedélyezéséhez. Az új kiadással már nem szükséges az Azure AD-beli hitelesítő adatok megadása a titkosítás engedélyezése lépés során. Az új kiadás használatával az új virtuális gépeket az Azure AD-alkalmazás paramétereinek használata nélkül kell titkosítani. A VM Disk Encryption új kiadással való engedélyezésével kapcsolatos utasítások megtekintéséhez lásd: [Azure Disk Encryption Windows rendszerű virtuális gépekhez](disk-encryption-windows.md). Azok a virtuális gépek, amelyek már az Azure AD-alkalmazás paramétereivel lettek titkosítva, továbbra is támogatottak, és továbbra is fenn kell tartaniuk a HRE szintaxisát**


Számos lemezes titkosítási forgatókönyvet engedélyezhet, és a lépések a forgatókönyvtől függően változhatnak. A következő részek a Windows IaaS virtuális gépekkel kapcsolatos részletesebb forgatókönyveket ismertetik. A lemez titkosításának használata előtt a [Azure Disk Encryption előfeltételeket](disk-encryption-overview-aad.md) el kell végezni. 


>[!IMPORTANT]
> - Készítsen [pillanatképet](snapshot-copy-managed-disk.md) , és/vagy készítsen biztonsági másolatot a lemezek titkosítása előtt. A biztonsági másolatok gondoskodnak arról, hogy a rendszer egy helyreállítási lehetőséget akkor lehessen, ha nem várt hiba történik a titkosítás során. A felügyelt lemezekkel rendelkező virtuális gépek biztonsági mentést igényelnek a titkosítás megkezdése előtt. A biztonsági mentés után a [set-AzVMDiskEncryptionExtension parancsmag](/powershell/module/az.compute/set-azvmdiskencryptionextension) használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. További információ a titkosított virtuális gépek biztonsági mentéséről és visszaállításáról: [titkosított Azure-beli virtuális gép biztonsági mentése és visszaállítása](../../backup/backup-azure-vms-encryption.md). 
>
> - A titkosítás titkosítása vagy letiltása miatt előfordulhat, hogy a virtuális gép újraindul.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>A piactéren létrehozott új IaaS virtuális gépek titkosításának engedélyezése
Az Azure piactéren Resource Manager-sablonok használatával engedélyezheti a lemez titkosítását az új IaaS Windows rendszerű virtuális gépen. A sablon egy új titkosított Windows virtuális gépet hoz létre a Windows Server 2012 Gallery rendszerkép használatával.

1. A [Resource Manager-sablonban](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)kattintson az **üzembe helyezés az Azure-** ban lehetőségre.

2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, a paramétereket, a jogi feltételeket és a szerződést. Kattintson a **vásárlás** elemre egy olyan új IaaS virtuális gép üzembe helyezéséhez, amelyen engedélyezve van a titkosítás.

3. A sablon üzembe helyezése után ellenőrizze a virtuális gép titkosítási állapotát az előnyben részesített módszer használatával:
     - Az az [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) paranccsal ellenőrizze, hogy az Azure CLI-t használja-e. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - A [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmag használatával ellenőrizze, hogy Azure PowerShell-e. 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Válassza ki a virtuális gépet, majd kattintson a **Beállítások** fejléc alatt található **lemezekre** a titkosítási állapot ellenőrzéséhez a portálon. A táblázatban a **titkosítás** alatt látható, hogy engedélyezve van-e a diagram. 
           ![Azure Portal – a lemez titkosítása engedélyezve](../media/disk-encryption/disk-encryption-fig2.png)

A következő táblázat felsorolja az új virtuális gépekhez tartozó Resource Manager-sablon paramétereit az Azure AD ügyfél-azonosító használatával:

| Paraméter | Leírás | 
| --- | --- |
| adminUserName | A virtuális gép rendszergazdájának felhasználóneve. |
| adminPassword | A virtuális gép rendszergazdai felhasználói jelszava. |
| newStorageAccountName | Az operációs rendszer és az adatvirtuális merevlemezek tárolására szolgáló Storage-fiók neve. |
| vmSize | A virtuális gép mérete. Jelenleg csak A standard A, A D és a G sorozat támogatott. |
| virtualNetworkName | Annak a VNet a neve, amelyhez a virtuális hálózati adapternek tartoznia kell. |
| subnetName | Annak az alhálózatnak a neve a VNet, amelyhez a virtuális gép hálózati adapterének tartoznia kell. |
| AADClientID | Az Azure AD-alkalmazás ügyfél-azonosítója, amely jogosultsággal rendelkezik a Key vaultba való titkok írásához. |
| AADClientSecret | Az Azure AD-alkalmazás azon titkos kulcsa, amely a Key vaultba való titkos kódok írásához szükséges engedélyekkel rendelkezik. |
| keyVaultURL | Azon kulcstartó URL-címe, amelyre a BitLocker-kulcsot fel kell tölteni. A parancsmag `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` vagy az Azure CLI használatával kérheti le `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | A generált BitLocker-kulcs titkosításához használt kulcs-titkosítási kulcs URL-címe (nem kötelező). </br> </br>A KeyEncryptionKeyURL egy opcionális paraméter. Saját KEK-t is használhat a kulcstartóban lévő adattitkosítási kulcs (jelszó titka) további védelméhez. |
| keyVaultResourceGroup | A Key Vault erőforráscsoport. |
| vmName | Annak a virtuális gépnek a neve, amelyen a titkosítási műveletet végre szeretné állítani. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a> Titkosítás engedélyezése meglévő vagy futó IaaS Windows rendszerű virtuális gépeken
Ebben az esetben a titkosítást sablon, PowerShell-parancsmagok vagy CLI-parancsok használatával engedélyezheti. A következő részek részletesebben ismertetik a Azure Disk Encryption engedélyezésének módját. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a> Titkosítás engedélyezése meglévő vagy futó virtuális gépeken Azure PowerShell 
A [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban. A Azure Disk Encryption PowerShell-parancsmagok használatával történő titkosításának engedélyezésével kapcsolatos információkért tekintse meg a blogbejegyzések [megismerése Azure Disk Encryption a Azure PowerShell 1. részében](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell) , és [ismerkedjen meg Azure Disk Encryption Azure PowerShell 2. részével](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell-part-2).

-  **Egy futó virtuális gép titkosítása az ügyfél titkos** kódjával: Az alábbi szkript inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép, a Key Vault, a HRE alkalmazás és az ügyfél titkos kulcsa már előfeltételként lett létrehozva. Cserélje le a MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM, a MySecureVault, a My-HRE-Client-ID és a My-HRE-Client-Secret értékeket az értékekre.
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
- **Egy futó virtuális gép titkosítása a KEK használatával az ügyfél titkos kódjának becsomagolásához:** Azure Disk Encryption lehetővé teszi egy meglévő kulcs megadását a kulcstartóban a titkosítás engedélyezésekor létrehozott lemez-titkosítási titkok becsomagolásához. Ha a kulcs titkosítási kulcsa meg van adva, Azure Disk Encryption ezt a kulcsot használja a titkosítási titok becsomagolásához a Key Vaultba való írás előtt. 

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
   > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br> A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának vizsgálatához használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Lemez titkosításának letiltása:** A titkosítás letiltásához használja a [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Titkosítás engedélyezése meglévő vagy futó virtuális gépeken az Azure CLI-vel
Az az [VM encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) paranccsal engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban.

- **Egy futó virtuális gép titkosítása az ügyfél titkos kódjával:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Egy futó virtuális gép titkosítása a KEK használatával az ügyfél titkos kódjának becsomagolásához:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name] </br> A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának megtekintéséhez használja az az [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>A Resource Manager-sablon használata
Az Azure-ban meglévő vagy futó IaaS Windows rendszerű virtuális gépeken is engedélyezheti a lemez titkosítását, ha a [Resource Manager-sablonnal titkosít egy futó Windows rendszerű virtuális gépet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. Az Azure Gyorsindítás sablonon kattintson az **üzembe helyezés az Azure**-ban lehetőségre.

2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, a paramétereket, a jogi feltételeket és a szerződést. Kattintson a **vásárlás** gombra a titkosítás engedélyezéséhez a meglévő vagy futó IaaS virtuális gépen.

A következő táblázat a Resource Manager-sablon azon paramétereit sorolja fel, amelyek az Azure AD ügyfél-azonosítót használó meglévő vagy futó virtuális gépekhez használhatók:

| Paraméter | Leírás |
| --- | --- |
| AADClientID | Az Azure AD-alkalmazás ügyfél-azonosítója, amely jogosult a Key vaultba írni a titkos kulcsokat. |
| AADClientSecret | Azon Azure AD-alkalmazás ügyfél-titka, amely jogosult a Key vaultba írni a titkos kulcsokat. |
| keyVaultName | Annak a kulcstárolónak a neve, amelyre a BitLocker-kulcsot fel kell tölteni. A parancsmag `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` vagy az Azure CLI-parancs használatával kérheti le. `az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | A létrehozott BitLocker-kulcs titkosításához használt kulcs titkosítási kulcsának URL-címe. Ez a paraméter nem kötelező, ha a UseExistingKek legördülő listában a **nokek** lehetőséget választja. Ha a UseExistingKek legördülő listában a **KEK** elemet választja, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| volumeType | A titkosítási művelet végrehajtásához használt kötet típusa. Az érvényes értékek az _operációs rendszer_, _az adatok_ és _az összes_. |
| sequenceVersion | A BitLocker-művelet szekvenciális verziója. Minden alkalommal növelje ezt a verziószámot, amikor egy lemezes titkosítási műveletet hajt végre ugyanazon a virtuális gépen. |
| vmName | Annak a virtuális gépnek a neve, amelyen a titkosítási műveletet végre szeretné állítani. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Az ügyfél által titkosított VHD-és titkosítási kulcsokból létrehozott új IaaS virtuális gépek
Ebben a forgatókönyvben engedélyezheti a titkosítást a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával. A következő részek részletesebben ismertetik a Resource Manager-sablon és a CLI-parancsok részleteit. 

Az Azure-ban használható, előre titkosított rendszerképek előkészítéséhez használja a függelék utasításait. A rendszerkép létrehozása után a következő szakasz lépéseit követve hozzon létre egy titkosított Azure-beli virtuális gépet.

* [Előre titkosított Windows virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"></a> Virtuális gépek titkosítása előre titkosított virtuális merevlemezekkel Azure PowerShell
A titkosított virtuális merevlemez titkosítását a [set-AzVMOSDisk PowerShell-](/powershell/module/az.compute/set-azvmosdisk#examples)parancsmag használatával engedélyezheti. Az alábbi példa néhány gyakori paramétert biztosít. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Titkosítás engedélyezése újonnan hozzáadott adatlemezen
Új lemezt a PowerShell vagy [a Azure Portal](attach-managed-disk-portal.md) [használatával adhat hozzá a Windows rendszerű virtuális](attach-disk-ps.md)gépekhez. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>A titkosítás engedélyezése egy újonnan hozzáadott lemezen Azure PowerShell
 Amikor a PowerShell használatával titkosít egy új lemezt a Windows rendszerű virtuális gépekhez, meg kell adni egy új sorozatot. A Sequence verziójának egyedinek kell lennie. Az alábbi szkript létrehoz egy GUID azonosítót a Sequence verzióhoz. Bizonyos esetekben előfordulhat, hogy az újonnan hozzáadott adatlemezek automatikusan titkosítva lesznek a Azure Disk Encryption bővítménnyel. Az automatikus titkosítás általában akkor fordul elő, amikor a virtuális gép újraindul, miután az új lemez online állapotba kerül. Ez általában az okozza, hogy az "all" érték van megadva a kötet típusához, amikor a lemez titkosítása korábban futott a virtuális gépen. Ha az automatikus titkosítás egy újonnan hozzáadott adatlemezen történik, javasoljuk, hogy futtassa újra a Set-AzVmDiskEncryptionExtension parancsmagot új sorszámmal. Ha az új adatlemez automatikusan titkosítva van, és nem kívánja titkosítani, az összes meghajtót visszafejti, majd újratitkosítja egy új sorozat-verzióval, amely meghatározza a kötet típusának operációs rendszerét. 
 

-  **Egy futó virtuális gép titkosítása az ügyfél titkos** kódjával: Az alábbi szkript inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép, a Key Vault, a HRE alkalmazás és az ügyfél titkos kulcsa már előfeltételként lett létrehozva. Cserélje le a MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM, a MySecureVault, a My-HRE-Client-ID és a My-HRE-Client-Secret értékeket az értékekre. Ez a példa az "all" kifejezést használja a-VolumeType paraméterhez, amely az operációs rendszer és az adatköteteket is tartalmazza. Ha csak az operációs rendszer kötetét szeretné titkosítani, használja az "operációs rendszer" lehetőséget a-VolumeType paraméterhez. 

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
- **Egy futó virtuális gép titkosítása a KEK használatával az ügyfél titkos kódjának becsomagolásához:** Azure Disk Encryption lehetővé teszi egy meglévő kulcs megadását a kulcstartóban a titkosítás engedélyezésekor létrehozott lemez-titkosítási titkok becsomagolásához. Ha a kulcs titkosítási kulcsa meg van adva, Azure Disk Encryption ezt a kulcsot használja a titkosítási titok becsomagolásához a Key Vaultba való írás előtt. Ez a példa az "all" kifejezést használja a-VolumeType paraméterhez, amely az operációs rendszer és az adatköteteket is tartalmazza. Ha csak az operációs rendszer kötetét szeretné titkosítani, használja az "operációs rendszer" lehetőséget a-VolumeType paraméterhez. 

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
    > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br> A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>A titkosítás engedélyezése az újonnan hozzáadott lemezeken az Azure CLI-vel
  Az Azure CLI-parancs automatikusan megadja az új sorozat verzióját, amikor a parancs futtatásával engedélyezi a titkosítást. A Volume-yype paraméter elfogadható értékei az összes, az operációs rendszer és az adatok. Előfordulhat, hogy módosítania kell a Volume-type paramétert az operációs rendszerre vagy az adattípusra, ha csak egy lemezt kíván titkosítani a virtuális géphez. A példák az "all" értéket használják a Volume-Type paraméterhez. 

-  **Egy futó virtuális gép titkosítása az ügyfél titkos kódjával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Egy futó virtuális gép titkosítása a KEK használatával az ügyfél titkos kódjának becsomagolásához:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Titkosítás engedélyezése az Azure AD ügyféltanúsítvány-alapú hitelesítéssel.
Az ügyféltanúsítvány-alapú hitelesítést KEK-mel vagy anélkül is használhatja. A PowerShell-parancsfájlok használata előtt a tanúsítványt fel kell tölteni a Key vaultba, és telepíteni kell a virtuális gépre. Ha a KEK-t is használja, a KEK-nek már léteznie kell. További információ:  [tanúsítványalapú hitelesítés az Azure ad-](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) ben az előfeltételek című cikkben.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Titkosítás engedélyezése tanúsítványalapú hitelesítés használatával Azure PowerShell

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
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Titkosítás engedélyezése tanúsítványalapú hitelesítéssel és egy KEK és Azure PowerShell használatával

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
A titkosítást a Azure PowerShell, az Azure CLI vagy egy Resource Manager-sablonnal is letilthatja. 

- **Lemez titkosításának letiltása a Azure PowerShell:** A titkosítás letiltásához használja a [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Titkosítás letiltása az Azure CLI-vel:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **A titkosítás letiltása Resource Manager-sablonnal:** 

    1. Kattintson a **telepítés az Azure** -ba lehetőségre a [lemez titkosításának letiltása Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)  virtuálisgép-sablon futtatásához.
    2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a virtuális gépet, a jogi feltételeket és a szerződést.
    3.  Kattintson a **vásárlás** gombra a lemez titkosításának letiltásához egy futó WINDOWSOS virtuális gépen. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Disk Encryption áttekintése](disk-encryption-overview.md)
