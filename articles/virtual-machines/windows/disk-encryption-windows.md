---
title: Azure Disk Encryption-forgatókönyvek Windows rendszerű virtuális gépekhez
description: Ez a cikk útmutatást nyújt a Windows rendszerű virtuális gépek Microsoft Azure lemezes titkosításának engedélyezéséhez különböző forgatókönyvek esetén
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: f91ff691948f2c55dd469ad121fab10d3a920eae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548427"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Azure Disk Encryption-forgatókönyvek Windows rendszerű virtuális gépekhez

A Windows rendszerű virtuális gépek (VM) Azure Disk Encryption a Windows BitLocker szolgáltatásával biztosítja az operációsrendszer-lemez és az adatlemez teljes lemezes titkosítását. Emellett az ideiglenes lemez titkosítását is biztosítja, ha a VolumeType paraméter mind.

Azure Disk Encryption [integrálva van Azure Key Vault](disk-encryption-key-vault.md) a lemezes titkosítási kulcsok és titkos kódok felügyeletéhez és kezeléséhez. A szolgáltatás áttekintését lásd: [Azure Disk Encryption Windows rendszerű virtuális gépekhez](disk-encryption-overview.md).

A lemezes titkosítást csak a [támogatott virtuálisgép-méretek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)virtuális gépei esetében lehet alkalmazni. A következő előfeltételeknek is teljesülniük kell:

- [Hálózati követelmények](disk-encryption-overview.md#networking-requirements)
- [Csoportházirend követelmények](disk-encryption-overview.md#group-policy-requirements)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Ha korábban már használta Azure Disk Encryption az Azure AD-vel egy virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Részletekért lásd: [Azure Disk Encryption az Azure ad-vel (előző kiadás)](disk-encryption-overview-aad.md) . 
>
> - Készítsen [pillanatképet](snapshot-copy-managed-disk.md) , és/vagy készítsen biztonsági másolatot a lemezek titkosítása előtt. A biztonsági másolatok gondoskodnak arról, hogy a rendszer egy helyreállítási lehetőséget akkor lehessen, ha nem várt hiba történik a titkosítás során. A felügyelt lemezekkel rendelkező virtuális gépek biztonsági mentést igényelnek a titkosítás megkezdése előtt. A biztonsági mentés után a [set-AzVMDiskEncryptionExtension parancsmag](/powershell/module/az.compute/set-azvmdiskencryptionextension) használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. További információ a titkosított virtuális gépek biztonsági mentéséről és visszaállításáról: [titkosított Azure-beli virtuális gép biztonsági mentése és visszaállítása](../../backup/backup-azure-vms-encryption.md). 
>
> - A titkosítás titkosítása vagy letiltása miatt előfordulhat, hogy a virtuális gép újraindul.

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és az Azure-hoz való kapcsolódás

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Titkosítás engedélyezése meglévő vagy Windows rendszerű virtuális gépen
Ebben az esetben a titkosítást a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával engedélyezheti. Ha a virtuálisgép-bővítményre vonatkozó séma-információra van szüksége, olvassa el a [Azure Disk Encryption a Windows-bővítményről szóló](../extensions/azure-disk-enc-windows.md) cikket.

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Titkosítás engedélyezése meglévő vagy futó virtuális gépeken Azure PowerShell 
A [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban. 

-  **Futó virtuális gép titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép és a Key Vault már előfeltételként lett létrehozva. Cserélje le az MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értéket az értékekre.

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
- **Futó virtuális gép titkosítása a KEK használatával:** 

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
   > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br> A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának vizsgálatához használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Lemez titkosításának letiltása:** A titkosítás letiltásához használja a [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. A windowsos virtuális gépek adatlemezeinek titkosítása nem a várt módon működik, ha az operációs rendszer és az adatlemezek egyaránt titkosítottak. A titkosítás letiltása az összes lemezen.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Titkosítás engedélyezése meglévő vagy futó virtuális gépeken az Azure CLI-vel
Az az [VM encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) paranccsal engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban.

- **Futó virtuális gép titkosítása:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Futó virtuális gép titkosítása a KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name] </br> A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának megtekintéséhez használja az az [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. A windowsos virtuális gépek adatlemezeinek titkosítása nem a várt módon működik, ha az operációs rendszer és az adatlemezek egyaránt titkosítottak. A titkosítás letiltása az összes lemezen.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>A Resource Manager-sablon használata

Az Azure-ban meglévő vagy futó IaaS Windows rendszerű virtuális gépeken is engedélyezheti a lemez titkosítását, ha a [Resource Manager-sablonnal titkosít egy futó Windows rendszerű virtuális gépet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. Az Azure Gyorsindítás sablonon kattintson az **üzembe helyezés az Azure**-ban lehetőségre.

2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a beállításokat, a jogi feltételeket és a szerződést. Kattintson a **vásárlás** gombra a titkosítás engedélyezéséhez a meglévő vagy futó IaaS virtuális gépen.

A következő táblázat a Resource Manager-sablon paramétereit sorolja fel a meglévő vagy futó virtuális gépekhez:

| Paraméter | Leírás |
| --- | --- |
| vmName | A titkosítási műveletet futtató virtuális gép neve. |
| keyVaultName | Annak a kulcstárolónak a neve, amelyre a BitLocker-kulcsot fel kell tölteni. A parancsmag `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` vagy az Azure CLI-parancs használatával kérheti le. `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | A kulcstárolót tartalmazó erőforráscsoport neve|
|  keyEncryptionKeyURL | A kulcs titkosítási kulcsának URL-címe, a https:// &lt; kulcstartó-név &gt; . Vault.Azure.net/Key/ &lt; kulcs-neve formátumban &gt; . Ha nem szeretne KEK-t használni, hagyja üresen ezt a mezőt. |
| volumeType | A titkosítási művelet végrehajtásához használt kötet típusa. Az érvényes értékek az _operációs rendszer_, _az adatok_ és _az összes_. 
| forceUpdateTag | Adjon meg egy egyedi értéket, például egy GUID-azonosítót, amikor a műveletnek kényszerített futtatást kell futtatnia. |
| resizeOSDisk | Ha az operációsrendszer-partíciót át szeretné méretezni a teljes operációsrendszer-lemez elfoglalásához a rendszerkötet felosztása előtt. |
| location | Az összes erőforrás helyei. |

## <a name="enable-encryption-on-nvme-disks-for-lsv2-vms"></a>Titkosítás engedélyezése NVMe-lemezeken Lsv2 virtuális gépekhez

Ez a forgatókönyv leírja, hogy a Lsv2 sorozatú virtuális gépek NVMe-lemezein hogyan engedélyezhető a Azure Disk Encryption.  A Lsv2 sorozat a helyi NVMe-tárolót is tartalmazza. A helyi NVMe-lemezek ideiglenesek, és a virtuális gép leállítása/felszabadítása után az adatvesztések megszakadnak (lásd: [Lsv2 sorozat](../lsv2-series.md)).

Titkosítás engedélyezése a NVMe-lemezeken:

1. Inicializálja a NVMe lemezeket, és hozzon létre NTFS-köteteket.
1. Engedélyezze a titkosítást a virtuális gépen a VolumeType paraméterrel, amely az összes értékre van állítva. Ezzel engedélyezi az összes operációs rendszer és adatlemez titkosítását, beleértve a NVMe lemezek által támogatott köteteket is. További információ: [a titkosítás engedélyezése meglévő vagy Windows rendszerű virtuális gépen](#enable-encryption-on-an-existing-or-running-windows-vm).

A titkosítás a következő esetekben marad a NVMe-lemezeken:
- Virtuális gép újraindítása
- Virtuálisgép-méretezési csoport rendszerképének alaphelyzetbe állítása
- Operációs rendszer cseréje

A NVMe lemezek a következő helyzetekben nem lesznek inicializálva:

- Virtuális gép elindítása a felszabadítás után
- Szolgáltatás-javító
- Backup

Ezekben a forgatókönyvekben a NVMe lemezeket inicializálni kell a virtuális gép elindítása után. Ha engedélyezni szeretné a titkosítást a NVMe-lemezeken, futtassa a parancsot a Azure Disk Encryption újbóli engedélyezéséhez a NVMe lemezek inicializálását követően.

A nem támogatott [forgatókönyvek](#unsupported-scenarios) szakaszban felsorolt forgatókönyvek mellett a NVMe lemezek titkosítása nem támogatott a következő esetekben:

- Azure Disk Encryption rendszerrel titkosított virtuális gépek HRE (korábbi kiadás)
- NVMe lemezek tárolása
- NVMe-lemezzel rendelkező SKU-Azure Site Recovery (lásd: [támogatási mátrix az Azure-beli virtuális gépek vész-helyreállításához az Azure-régiók között: replikált gépek – tároló](../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage)).

## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Az ügyfél által titkosított VHD-és titkosítási kulcsokból létrehozott új IaaS virtuális gépek

Ebben az esetben létrehozhat egy új virtuális gépet egy előre titkosított virtuális merevlemezről és a hozzá tartozó titkosítási kulcsokról a PowerShell-parancsmagok vagy a CLI-parancsok használatával. 

Használja az [előre titkosított Windows virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)című témakör utasításait. A rendszerkép létrehozása után a következő szakasz lépéseit követve hozzon létre egy titkosított Azure-beli virtuális gépet.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Virtuális gépek titkosítása előre titkosított virtuális merevlemezekkel Azure PowerShell
A titkosított virtuális merevlemez titkosítását a [set-AzVMOSDisk PowerShell-](/powershell/module/az.compute/set-azvmosdisk#examples)parancsmag használatával engedélyezheti. Az alábbi példa néhány gyakori paramétert biztosít. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Titkosítás engedélyezése újonnan hozzáadott adatlemezen
Új lemezt a PowerShell vagy [a Azure Portal](attach-managed-disk-portal.md) [használatával adhat hozzá a Windows rendszerű virtuális](attach-disk-ps.md)gépekhez. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>A titkosítás engedélyezése egy újonnan hozzáadott lemezen Azure PowerShell
 Amikor a PowerShell használatával titkosít egy új lemezt a Windows rendszerű virtuális gépekhez, meg kell adni egy új sorozatot. A Sequence verziójának egyedinek kell lennie. Az alábbi szkript létrehoz egy GUID azonosítót a Sequence verzióhoz. Bizonyos esetekben előfordulhat, hogy az újonnan hozzáadott adatlemezek automatikusan titkosítva lesznek a Azure Disk Encryption bővítménnyel. Az automatikus titkosítás általában akkor fordul elő, amikor a virtuális gép újraindul, miután az új lemez online állapotba kerül. Ez általában az okozza, hogy az "all" érték van megadva a kötet típusához, amikor a lemez titkosítása korábban futott a virtuális gépen. Ha az automatikus titkosítás egy újonnan hozzáadott adatlemezen történik, javasoljuk, hogy futtassa újra a Set-AzVmDiskEncryptionExtension parancsmagot új sorszámmal. Ha az új adatlemez automatikusan titkosítva van, és nem kívánja titkosítani, az összes meghajtót visszafejti, majd újratitkosítja egy új sorozat-verzióval, amely meghatározza a kötet típusának operációs rendszerét. 
  
 

-  **Futó virtuális gép titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép és a Key Vault már előfeltételként lett létrehozva. Cserélje le az MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értéket az értékekre. Ez a példa az "all" kifejezést használja a-VolumeType paraméterhez, amely az operációs rendszer és az adatköteteket is tartalmazza. Ha csak az operációs rendszer kötetét szeretné titkosítani, használja az "operációs rendszer" lehetőséget a-VolumeType paraméterhez. 

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
- **Futó virtuális gép titkosítása a KEK használatával:** Ez a példa az "all" kifejezést használja a-VolumeType paraméterhez, amely az operációs rendszer és az adatköteteket is tartalmazza. Ha csak az operációs rendszer kötetét szeretné titkosítani, használja az "operációs rendszer" lehetőséget a-VolumeType paraméterhez.

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
    > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br> A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>A titkosítás engedélyezése az újonnan hozzáadott lemezeken az Azure CLI-vel
 Az Azure CLI-parancs automatikusan megadja az új sorozat verzióját, amikor a parancs futtatásával engedélyezi a titkosítást. A példa az "all" értéket használja a Volume-Type paraméterhez. Előfordulhat, hogy módosítania kell a Volume-type paramétert az operációs rendszerre, ha csak az operációsrendszer-lemezt titkosítja. A PowerShell-szintaxissal ellentétben a parancssori felület nem igényli, hogy a felhasználó egyedi sorozatot adjon meg a titkosítás engedélyezésekor. A CLI automatikusan generálja és felhasználja a saját egyedi sorszámának értékét.   

-  **Futó virtuális gép titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Futó virtuális gép titkosítása a KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Titkosítás letiltása
[!INCLUDE [disk-encryption-disable-encryption-powershell](../../../includes/disk-encryption-disable-powershell.md)]

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A Azure Disk Encryption a következő forgatókönyvek, funkciók és technológiák esetében nem működik:

- A klasszikus virtuálisgép-létrehozási módszerrel létrehozott alapszintű virtuális gép vagy virtuális gépek titkosítása.
- Szoftveres RAID-rendszerekkel konfigurált virtuális gépek titkosítása.
- Közvetlen tárolóhelyek-(S2D-) vagy Windows Server-verziókkal konfigurált virtuális gépek titkosítása, mielőtt 2016 a Windows Storage Spaces szolgáltatással konfigurálva.
- Integráció egy helyszíni kulcskezelő rendszerrel.
- Azure Files (megosztott fájlrendszer).
- Hálózati fájlrendszer (NFS).
- Dinamikus kötetek.
- Windows Server-tárolók, amelyek mindegyik tárolóhoz dinamikus köteteket hoznak létre.
- Ideiglenes operációsrendszer-lemezek.
- Megosztott/elosztott fájlrendszerek titkosítása, például (de nem kizárólag a) DFS, a GFS, a DRDB és a CephFS.
- Titkosított virtuális gép áthelyezése másik előfizetésre vagy régióba.
- Egy titkosított virtuális gép rendszerképének vagy pillanatképének létrehozása, és annak használata további virtuális gépek telepítéséhez.
- Az M sorozatú virtuális gépek írásgyorsító lemezzel.
- Az ADE alkalmazása olyan virtuális gépre, amely kiszolgálóoldali titkosítással titkosított lemezeket tartalmaz [az ügyfél által felügyelt kulcsokkal](../disk-encryption.md) (SSE + CMK). Az SSE és a CMK az ADE-sel titkosított virtuális gépek adatlemezére való alkalmazása nem támogatott forgatókönyv is.
- Az ADE-sel titkosított virtuális gépek áttelepítése, vagy az ADE-sel való titkosítása már **megtörtént,** az [ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosításhoz](../disk-encryption.md).
- Azure-beli virtuálisgép- [méretek helyi ideiglenes lemez nélkül](../azure-vms-no-temp-disk.md); pontosabban a DV4, a Dsv4, a Ev4 és a Esv4.
- Virtuális gépek titkosítása feladatátvevő fürtökben.

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption áttekintése](disk-encryption-overview.md)
- [Azure Disk Encryption – mintaszkriptek](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption – hibaelhárítás](disk-encryption-troubleshooting.md)