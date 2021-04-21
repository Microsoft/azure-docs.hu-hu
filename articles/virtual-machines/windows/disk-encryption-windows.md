---
title: Azure Disk Encryption-forgatókönyvek Windows rendszerű virtuális gépekhez
description: Ez a cikk útmutatást nyújt a Microsoft Azure lemeztitkosítás engedélyezéséhez Windows rendszerű virtuális gépeken különböző forgatókönyvek esetén
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 2d8173e8b79e8696c2a3e4a7ea722b2947b1aee6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776821"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Azure Disk Encryption-forgatókönyvek Windows rendszerű virtuális gépekhez

Azure Disk Encryption windowsos virtuális gépek (VM-ek) a Windows BitLocker funkcióját használják az operációsrendszer-lemez és az adatlemez teljes lemeztitkosítására. Emellett az ideiglenes lemez titkosítását is biztosítja, ha a VolumeType paraméter értéke All.

Azure Disk Encryption [integrálva van a Azure Key Vault](disk-encryption-key-vault.md) a lemeztitkosítási kulcsok és titkos kulcsok vezérlése és kezelése érdekében. A szolgáltatás áttekintését lásd: Azure Disk Encryption [windowsos virtuális gépekhez.](disk-encryption-overview.md)

Lemeztitkosítást csak a támogatott virtuálisgép-méretekkel és operációs rendszerekkel [működő virtuális gépekre alkalmazhat.](disk-encryption-overview.md#supported-vms-and-operating-systems) Az alábbi előfeltételeknek is meg kell felelnie:

- [Hálózati követelmények](disk-encryption-overview.md#networking-requirements)
- [Csoportházirend követelmények](disk-encryption-overview.md#group-policy-requirements)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Ha korábban már használta Azure Disk Encryption Azure AD-val a virtuális gép titkosításához, továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához. További [Azure Disk Encryption az Azure AD-val (korábbi kiadás) kapcsolatos](disk-encryption-overview-aad.md) információk. 
>
> - A lemezek [titkosítása](snapshot-copy-managed-disk.md) előtt készítsen pillanatképet és/vagy készítsen biztonsági másolatot. A biztonsági másolatok biztosítják, hogy a titkosítás során váratlan hiba esetén helyreállítási lehetőség legyen lehetséges. A felügyelt lemezekkel virtuális gépek biztonsági mentést igényelnek a titkosítás előtt. A biztonsági mentés után a [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával titkosíthatja a felügyelt lemezeket a -skipVmBackup paraméter megadásával. A titkosított virtuális gépek biztonsági mentésének és visszaállításának további információiért lásd: Titkosított Azure-beli virtuális gépek biztonsági másolata [és visszaállítása.](../../backup/backup-azure-vms-encryption.md) 
>
> - A titkosítás titkosítása vagy letiltása a virtuális gép újraindítását okozhatja.

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és csatlakozás az Azure-hoz

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Titkosítás engedélyezése meglévő vagy futó Windows rendszerű virtuális gépen
Ebben a forgatókönyvben engedélyezheti a titkosítást a Resource Manager sablon, PowerShell-parancsmagok vagy PARANCSSORi felületi parancsok használatával. Ha a virtuálisgép-bővítmény sémainformációira van szüksége, tekintse meg a [Windows Azure Disk Encryption bővítményhez való használatát.](../extensions/azure-disk-enc-windows.md)

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Titkosítás engedélyezése meglévő vagy futó virtuális gépeken Azure PowerShell 
A [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban. 

-  **Futó virtuális gép titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja Set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép és a kulcstartó már létre lett hozva előfeltételként. Cserélje le a MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM és MySecureVault értékeket a saját értékeire.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Futó virtuális gép titkosítása KEK használatával:** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > A disk-encryption-keyvault paraméter értékének szintaxisa a teljes azonosító sztring: /subscriptions/[előfizetés-azonosító-guid]/resourceGroups/[erőforráscsoport-neve]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A key-encryption-key paraméter értékének szintaxisa a KEK teljes URI-ját használja a következő módon: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Az IaaS virtuális gép titkosítási állapotának ellenőrzéshez használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Lemeztitkosítás letiltása:** A titkosítás letiltásához használja a [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. A windowsos virtuális gépek adatlemezeinek titkosítása nem a várt módon működik, ha az operációs rendszer és az adatlemezek egyaránt titkosítottak. Ehelyett tiltsa le a titkosítást az összes lemezen.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Titkosítás engedélyezése meglévő vagy futó virtuális gépeken az Azure CLI használatával
Az [az vm encryption enable paranccsal](/cli/azure/vm/encryption#az_vm_encryption_enable) engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban.

- **Futó virtuális gép titkosítása:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Futó virtuális gép titkosítása KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A disk-encryption-keyvault paraméter értékének szintaxisa a teljes azonosító sztring: /subscriptions/[előfizetés-azonosító-guid]/resourceGroups/[erőforráscsoport-neve]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A key-encryption-key paraméter értékének szintaxisa a KEK teljes URI-ját használja a következő módon: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának ellenőrzéshez használja [az az vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) parancsot. A windowsos virtuális gépek adatlemezeinek titkosítása nem a várt módon működik, ha az operációs rendszer és az adatlemezek egyaránt titkosítottak. Ehelyett tiltsa le a titkosítást az összes lemezen.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>A Resource Manager használata

A lemeztitkosítást engedélyezheti meglévő vagy futó IaaS Windows rendszerű virtuális gépeken az [Azure-ban,](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)ha a Resource Manager-sablonnal titkosít egy futó Windows rendszerű virtuális gépet.


1. Az Azure gyorsindítási sablonban kattintson az Üzembe **helyezés az Azure-ban elemre.**

2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a beállításokat, a jogi feltételeket és a szerződést. Kattintson **a Vásárlás gombra** a titkosítás engedélyezéséhez a meglévő vagy futó IaaS virtuális gépen.

Az alábbi táblázat a meglévő Resource Manager futó virtuális gépekhez használható sablonparamétereket sorolja fel:

| Paraméter | Leírás |
| --- | --- |
| vmName | A titkosítási művelet futtatásához használt virtuális gép neve. |
| keyVaultName | Annak a kulcstartónak a neve, amelybe a BitLocker-kulcsot fel kell tölteni. Ezt a parancsmag vagy az `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` Azure CLI-parancs használatával kaphatja meg `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | A kulcstartót tartalmazó erőforráscsoport neve|
|  keyEncryptionKeyURL | A kulcstitkosítási kulcs URL-címe a következő https:// &lt; keyvault-name &gt; .vault.azure.net/key/ &lt; kulcs-név &gt; formátumban: . Ha nem szeretne KEK-et használni, hagyja üresen ezt a mezőt. |
| volumeType (kötettípus) | A titkosítási művelet által érintett kötet típusa. Érvényes értékek: _OS,_ _Data_, és _All._ 
| forceUpdateTag | Minden alkalommal, amikor kényszeríteni kell a művelet futtatását, egyedi értéket, például GUID-t kell átadni. |
| resizeOSDisk | Át kell-e méretezni az operációs rendszer partícióját úgy, hogy az teljes operációsrendszer-vhD-t foglaljon el a rendszerkötet felosztása előtt. |
| location | Az összes erőforrás helyei. |

## <a name="enable-encryption-on-nvme-disks-for-lsv2-vms"></a>Titkosítás engedélyezése NVMe-lemezeken Lsv2 virtuális gépekhez

Ez a forgatókönyv az Azure Disk Encryption NVAMe-lemezeken az Lsv2 sorozatú virtuális gépeken való engedélyezését ismerteti.  Az Lsv2 sorozat helyi NVMe-tárolóval rendelkezik. A helyi NVMe-lemezek ideiglenesek, és ha leállítja/felszabadítja a virtuális gépet, az adatok elvesznek ezeken a lemezeken (lásd: [Lsv2 sorozat).](../lsv2-series.md)

Titkosítás engedélyezése NVMe-lemezeken:

1. Inicializálja az NVMe-lemezeket, és hozzon létre NTFS-köteteket.
1. Engedélyezze a titkosítást a virtuális gépen úgy, hogy a VolumeType paraméter értéke Mind. Ez lehetővé teszi az összes operációsrendszer- és adatlemez titkosítását, beleértve az NVMe-lemezeken található köteteket is. További információ: Titkosítás engedélyezése meglévő vagy futó Windows rendszerű [virtuális gépen.](#enable-encryption-on-an-existing-or-running-windows-vm)

A titkosítás a következő esetekben is megmarad az NVMe-lemezeken:
- Virtuális gép újraindítása
- Virtuálisgép-méretezési készlet rendszermemóta
- Operációs rendszer felcserélve

Az NVMe-lemezek nem inicializálva lesznek a következő forgatókönyvek:

- Virtuális gép elindítani a deallocation után
- A szolgáltatás kigyógyulása
- Backup

Ezekben az esetekben az NVMe-lemezeket a virtuális gép elindulása után kell inicializálni. Az NVMe-lemezek titkosításának engedélyezéséhez futtassa a parancsot az Azure Disk Encryption az NVMe-lemezek inicializálása után.

A Nem támogatott forgatókönyvek szakaszban [](#unsupported-scenarios) felsorolt forgatókönyvek mellett az NVMe-lemezek titkosítása nem támogatott a következő esetekben:

- AAD-vel Azure Disk Encryption virtuális gépek (korábbi kiadás)
- NVMe-lemezek tárolóhelyekkel
- Azure Site Recovery NVMe-lemezekkel (lásd: Azure-beli virtuális gépek Azure-régiók közötti vészhelyreállításának támogatási [mátrixa: Replikált gépek – tároló).](../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage)

## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Ügyfél által titkosított VHD-kből és titkosítási kulcsokból létrehozott új IaaS virtuális gépek

Ebben a forgatókönyvben létrehozhat egy új virtuális gépet egy előre titkosított VHD-ről és a kapcsolódó titkosítási kulcsokról PowerShell-parancsmagok vagy PARANCSSORi parancsok használatával. 

Kövesse az Előtitkos Windows vhD előkészítése [útmutatót.](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd) A rendszerkép létrehozása után a következő szakaszban található lépésekkel létrehozhat egy titkosított Azure-beli virtuális gépet.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Virtuális gépek titkosítása előre titkosított VHD-ket Azure PowerShell
A lemeztitkosítást a titkosított VHD-n a [Set-AzVMOSDisk PowerShell-parancsmag használatával engedélyezheti.](/powershell/module/az.compute/set-azvmosdisk#examples) Az alábbi példa néhány gyakori paramétert mutat be. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Titkosítás engedélyezése újonnan hozzáadott adatlemezen
A Windows [rendszerű virtuális gépekhez a PowerShell](attach-disk-ps.md)vagy a parancs használatával adhat hozzá [új Azure Portal.](attach-managed-disk-portal.md) 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Engedélyezze a titkosítást egy újonnan hozzáadott lemezen Azure PowerShell
 Ha a Windows rendszerű virtuális gépek új lemezét a PowerShell használatával titkosítja, meg kell adni egy új sorozatverziót. A sorozatverziónak egyedinek kell lennie. Az alábbi szkript létrehoz egy GUID-t a sorozatverzióhoz. Bizonyos esetekben előfordulhat, hogy az újonnan hozzáadott adatlemezt automatikusan titkosítja a Azure Disk Encryption bővítmény. Az automatikus titkosítás általában akkor történik meg, amikor a virtuális gép újraindul, miután az új lemez online állapotba kerül. Ezt általában az okozza, hogy a kötettípushoz az "Összes" lett megadva, amikor a lemeztitkosítás korábban futott a virtuális gépen. Ha automatikus titkosítás történik egy újonnan hozzáadott adatlemezen, javasoljuk, hogy futtasa újra a Set-AzVmDiskEncryptionExtension parancsmagot egy új sorozatverzióval. Ha az új adatlemez automatikusan titkosítva van, és nem szeretné, hogy titkosítva legyen, akkor először az összes meghajtót visszafejtenie kell, majd újra kell titkosítania egy új sorozatverzióval, amely az operációs rendszert adja meg a kötettípushoz. 
  
 

-  **Futó virtuális gép titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja Set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép és a kulcstartó már létre lett hozva előfeltételként. Cserélje le a MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM és MySecureVault értékeket a saját értékeire. Ez a példa az "All" paramétert használja a -VolumeType paraméterhez, amely az operációs rendszert és az adatköteteket is tartalmazza. Ha csak az operációs rendszer kötetét szeretné titkosítani, használja az "OS" paramétert a -VolumeType paraméterhez. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Futó virtuális gép titkosítása KEK használatával:** Ez a példa az "All" paramétert használja a -VolumeType paraméterhez, amely az operációs rendszert és az adatköteteket is tartalmazza. Ha csak az operációs rendszer kötetét szeretné titkosítani, használja az "OS" paramétert a -VolumeType paraméterhez.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > A disk-encryption-keyvault paraméter értékének szintaxisa a teljes azonosító sztring: /subscriptions/[előfizetés-azonosító-guid]/resourceGroups/[erőforráscsoport-neve]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A key-encryption-key paraméter értékének szintaxisa a KEK teljes URI-ját használja a következő módon: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Titkosítás engedélyezése újonnan hozzáadott lemezen az Azure CLI használatával
 Az Azure CLI-parancs automatikusan új sorozatverziót biztosít a titkosítás engedélyezéséhez a parancs futtatásakor. A példa az "All" (Összes) paramétert használja a kötettípus paraméterhez. Előfordulhat, hogy a kötettípus paraméterét operációs rendszerre kell módosítania, ha csak az operációsrendszer-lemezt titkosítja. A PowerShell-szintaxissal ellentétben a parancssori felület nem követeli meg, hogy a felhasználó egyedi sorozatverziót adjon meg a titkosítás engedélyezésekor. A CLI automatikusan létrehozza és felhasználja a saját egyedi sorozatverzió-értékét.   

-  **Futó virtuális gép titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Futó virtuális gép titkosítása KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Titkosítás letiltása
[!INCLUDE [disk-encryption-disable-encryption-powershell](../../../includes/disk-encryption-disable-powershell.md)]

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

Azure Disk Encryption a következő forgatókönyvekhez, funkciókhoz és technológiákhoz nem használható:

- A klasszikus virtuálisgép-létrehozási módszerrel létrehozott alapszintű virtuális gépek vagy virtuális gépek titkosítása.
- Szoftveralapú RAID-rendszerekkel konfigurált virtuális gépek titkosítása.
- A Windows Tárolóhelyekkel konfigurált Közvetlen tárolóhelyek (S2D) vagy Windows Server 2016 előtti verzióival konfigurált virtuális gépek titkosítása.
- Integráció egy helyszíni kulcskezelő rendszerrel.
- Azure Files (megosztott fájlrendszer).
- Hálózati fájlrendszer (NFS).
- Dinamikus kötetek.
- Windows Server-tárolók, amelyek dinamikus köteteket hoznak létre az egyes tárolókhoz.
- Aphemerális operációsrendszer-lemezek.
- Olyan megosztott/elosztott fájlrendszerek titkosítása, mint például a DFS, a GFS, a DRDB és a CephFS.
- Titkosított virtuális gép átköltöztetve egy másik előfizetésbe vagy régióba.
- Titkosított virtuális gép rendszerképének vagy pillanatképének létrehozása és használata további virtuális gépek üzembe helyezéséhez.
- M-sorozat virtuális gépei írásgyorsító lemezekkel.
- Az ADE alkalmazása olyan virtuális gépre, amely kiszolgálóoldali titkosítással, ügyfél által kezelt kulcsokkal [(SSE](../disk-encryption.md) + CMK) titkosított lemezekkel rendelkezik. Az SSE + CMK ADE-val titkosított virtuális gépen lévő adatlemezre való alkalmazása nem támogatott forgatókönyv.
- ADE-titkosítással vagy ADE-titkosítással valaha  is titkosított virtuális gép áttelepítése kiszolgálóoldali titkosításra ügyfél által felügyelt [kulcsokkal.](../disk-encryption.md)
- Feladatátvevő fürtökben tárolt virtuális gépek titkosítása.
- Az [Azure ultralemezek titkosítása.](../disks-enable-ultra-ssd.md)

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption áttekintés](disk-encryption-overview.md)
- [Azure Disk Encryption – mintaszkriptek](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption – hibaelhárítás](disk-encryption-troubleshooting.md)