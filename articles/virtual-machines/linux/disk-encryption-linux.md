---
title: Azure Disk Encryption-forgatókönyvek Linux rendszerű virtuális gépeken
description: Ez a cikk útmutatást nyújt a linuxos Microsoft Azure lemeztitkosítás engedélyezéséhez különböző forgatókönyvek esetén
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: f014c07a319cbb07497cba01699b93d092255b93
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771504"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Azure Disk Encryption-forgatókönyvek Linux rendszerű virtuális gépeken

Azure Disk Encryption linuxos virtuális gépek (VM-ek) a Linux DM-Crypt funkcióját használják az operációsrendszer-lemez és az adatlemezek teljes lemeztitkosítására. Emellett titkosítja az ideiglenes lemezt az EncryptFormatAll funkció használata esetén.

Azure Disk Encryption [integrálva van a Azure Key Vault](disk-encryption-key-vault.md) a lemeztitkosítási kulcsok és titkos kulcsok vezérlése és kezelése érdekében. A szolgáltatás áttekintését lásd: Azure Disk Encryption [linuxos virtuális gépekhez.](disk-encryption-overview.md)

Lemeztitkosítást csak a támogatott virtuálisgép-méretekkel és operációs rendszerekkel [működő virtuális gépekre alkalmazhat.](disk-encryption-overview.md#supported-vms-and-operating-systems) A következő előfeltételeknek is meg kell felelnie:

- [A virtuális gépekre vonatkozó további követelmények](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Hálózati követelmények](disk-encryption-overview.md#networking-requirements)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview.md#encryption-key-storage-requirements)

A lemezek titkosítása [](snapshot-copy-managed-disk.md) előtt minden esetben készítsen pillanatképet és/vagy készítsen biztonsági másolatot. A biztonsági másolatok biztosítják, hogy a titkosítás során váratlan hiba esetén helyreállítási lehetőség legyen lehetséges. A felügyelt lemezekkel virtuális gépek biztonsági mentést igényelnek a titkosítás előtt. A biztonsági mentés után a [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával titkosíthatja a felügyelt lemezeket a -skipVmBackup paraméter megadásával. A titkosított virtuális gépek biztonsági mentésének és visszaállításának további információiért tekintse meg a Azure Backup [cikket.](../../backup/backup-azure-vms-encryption.md) 

>[!WARNING]
> - Ha korábban már használta Azure Disk Encryption Azure AD-val a virtuális gép titkosításához, akkor továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához. További [Azure Disk Encryption az Azure AD-val (korábbi kiadás) kapcsolatos](disk-encryption-overview-aad.md) részleteket. 
>
> - Linux operációsrendszer-kötetek titkosítása esetén a virtuális gépet nem elérhetőnek kell tekinteni. Határozottan javasoljuk, hogy kerülje az SSH-bejelentkezéseket, amíg a titkosítás folyamatban van, hogy elkerülje a titkosítási folyamat során elérni szükséges nyitott fájlok blokkolásával kapcsolatos problémákat. A folyamat előrehaladásának ellenőrzéshez használja a [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell-parancsmagot vagy a [vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) CLI parancsot. Ez a folyamat várhatóan néhány órát fog igénybe venni egy 30 GB-os operációsrendszer-köteten, és további időt is igénybe fog venni az adatkötetek titkosítása során. Az adatmennyiség titkosítási ideje arányos az adatkötetek méretével és mennyiségével, kivéve, ha az összes titkosítási formátumot használja. 
> - A titkosítás letiltása Linux rendszerű virtuális gépeken csak adatkötetek esetén támogatott. Az adat- vagy operációsrendszer-kötetek nem támogatják, ha az operációs rendszer kötete titkosítva van.  

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és csatlakozás az Azure-hoz

Azure Disk Encryption Azure [CLI](/cli/azure) és a használatával engedélyezhetők és kezelhetők [Azure PowerShell.](/powershell/azure/new-azureps-module-az) Az eszközöket helyileg kell telepítenie, és csatlakoznia kell az Azure-előfizetéséhez.

### <a name="azure-cli"></a>Azure CLI

Az [Azure CLI 2.0](/cli/azure) egy parancssori eszköz az Azure-erőforrások kezeléséhez. A CLI rugalmasan lekérdezi az adatokat, támogatja a hosszú ideig futó műveleteket nem blokkoló folyamatokként, és megkönnyíti a szkriptek futtatását. Helyileg is telepítheti az Azure CLI telepítése [lépéseit követve.](/cli/azure/install-azure-cli)

 

Az [Azure-fiókba az Azure CLI-val](/cli/azure/authenticate-azure-cli)való bejelentkezéshez használja az az [login](/cli/azure/reference-index#az_login) parancsot.

```azurecli
az login
```

Ha ki szeretne választani egy bérlőt, amelybe be szeretne jelentkezni, használja a következőt:
    
```azurecli
az login --tenant <tenant>
```

Ha több előfizetéssel rendelkezik, és meg szeretne adni egy adott előfizetést, szerezze be az [előfizetési](/cli/azure/account#az_account_list) listáját az az account listával, és adja meg az [az account set értéket.](/cli/azure/account#az_account_set)
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

További információ: [Az Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)használatának első lépések. 

### <a name="azure-powershell"></a>Azure PowerShell
Az [Azure PowerShell az modul](/powershell/azure/new-azureps-module-az) olyan parancsmagokat biztosít, [](../../azure-resource-manager/management/overview.md) amelyek a Azure Resource Manager használatával kezelik az Azure-erőforrásokat. Használhatja a böngészőjében a [Azure Cloud Shell,](../../cloud-shell/overview.md)vagy telepítheti a helyi gépére az Install the Azure PowerShell (A következő modul [telepítése) Azure PowerShell használatával.](/powershell/azure/install-az-ps) 

Ha már telepítve van helyileg, győződjön meg arról, hogy a Azure PowerShell SDK legújabb verzióját használja a Azure Disk Encryption. Töltse le a legújabb Azure PowerShell [kiadását.](https://github.com/Azure/azure-powershell/releases)

Az [Azure-fiókba való bejelentkezéshez használja Azure PowerShell](/powershell/azure/authenticate-azureps) [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmagot.

```powershell
Connect-AzAccount
```

Ha több előfizetéssel rendelkezik, és szeretne megadni egyet, használja a [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) parancsmagot a listához, majd a [Set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmagot:

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

A [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) parancsmag futtatása ellenőrzi, hogy a megfelelő előfizetés lett-e kiválasztva.

Annak megerősítéséhez Azure Disk Encryption hogy a parancsmagok telepítve vannak, használja a [Get-command](/powershell/module/microsoft.powershell.core/get-command) parancsmagot:
     
```powershell
Get-command *diskencryption*
```
További információ: [Ismerkedés a Azure PowerShell.](/powershell/azure/get-started-azureps) 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen
Ebben a forgatókönyvben engedélyezheti a titkosítást a Resource Manager, PowerShell-parancsmagok vagy CLI-parancsok használatával. Ha a virtuálisgép-bővítmény sémainformációira van szüksége, tekintse meg a [Linux Azure Disk Encryption bővítményhez való használatát.](../extensions/azure-disk-enc-linux.md)

>[!IMPORTANT]
 >A felügyelt lemezen alapuló virtuálisgép-példányok pillanatképét és/vagy biztonsági mentését kötelező a szolgáltatáson kívül, illetve a virtuális gépek engedélyezése Azure Disk Encryption. A felügyelt lemezről pillanatképet készíthet a portálról vagy a [Azure Backup.](../../backup/backup-azure-vms-encryption.md) A biztonsági másolatok biztosítják, hogy a titkosítás során váratlan hiba esetén lehetséges legyen a helyreállítási beállítás. A biztonsági mentés után a Set-AzVMDiskEncryptionExtension parancsmag a -skipVmBackup paraméter megadásával használható a felügyelt lemezek titkosítására. A Set-AzVMDiskEncryptionExtension parancs sikertelen lesz a felügyelt lemezen alapuló virtuális gépeken, amíg meg nem történt a biztonsági mentés, és meg nem lett adva ez a paraméter. 
>
>A titkosítás titkosítása vagy letiltása a virtuális gép újraindítását okozhatja. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen az Azure CLI használatával 

A lemeztitkosítást a titkosított VIRTUÁLIS merevlemezen az [Azure CLI](/cli/azure/) parancssori eszköz telepítésével és használatával engedélyezheti. Használhatja a böngészőjében az [Azure Cloud Shell-lel](../../cloud-shell/overview.md), vagy telepítheti a helyi gépen, és használhatja bármely PowerShell-munkamenetben. Ha engedélyezni szeretné a titkosítást az Azure-beli meglévő vagy futó Linux rendszerű virtuális gépeken, használja a következő CLI-parancsokat:

Az [az vm encryption enable paranccsal](/cli/azure/vm/encryption#az_vm_encryption_show) engedélyezheti a titkosítást egy futó azure-beli virtuális gépen.

- **Futó virtuális gép titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Futó virtuális gép titkosítása KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A disk-encryption-keyvault paraméter értékének szintaxisa a teljes azonosító sztring: /subscriptions/[előfizetés-azonosító-guid]/resourceGroups/[erőforráscsoport-neve]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
A key-encryption-key paraméter értékének szintaxisa a KEK teljes URI-ját használja a következő módon: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy virtuális gép titkosítási állapotának ellenőrzéshez használja [az az vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) parancsot. A titkosítás letiltása csak Linux rendszerű virtuális gépek adatkötetén engedélyezett.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen a PowerShell használatával
A [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást az Azure-ban futó virtuális gépeken. A [lemezek titkosítása](snapshot-copy-managed-disk.md) előtt pillanatfelvételt készíthet [és/vagy](../../backup/backup-azure-vms-encryption.md) biztonsági Azure Backup virtuális gépről. A -skipVmBackup paraméter már meg van adva a PowerShell-szkriptek között egy futó Linux rendszerű virtuális gép titkosításához.

-  **Futó virtuális gép titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja Set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép és a kulcstartó előfeltételként jött létre. Cserélje le a MyVirtualMachineResourceGroup, MySecureVM és MySecureVault értékeket a saját értékeire. Módosítsa a -VolumeType paramétert annak megadásához, hogy mely lemezeket szeretné titkosítani.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Futó virtuális gép titkosítása KEK használatával:** Előfordulhat, hogy a -VolumeType paramétert kell hozzáadnia, ha adatlemezeket titkosít, és nem az operációsrendszer-lemezt. 

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > A disk-encryption-keyvault paraméter értékének szintaxisa a teljes azonosító sztring: /subscriptions/[előfizetés-azonosító-guid]/resourceGroups/[erőforráscsoport-neve]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A key-encryption-key paraméter értékének szintaxisa a KEK teljes URI-ját használja a következő módon: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** A virtuális gép titkosítási állapotának ellenőrzéshez használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Lemeztitkosítás letiltása:** A titkosítás letiltásához használja a [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. A titkosítás letiltása csak Linux rendszerű virtuális gépek adatkötetén engedélyezett.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen sablon használatával

A lemeztitkosítást az Azure-ban meglévő vagy futó Linux rendszerű virtuális gépeken a következő [sablon Resource Manager engedélyezheti:](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Az **Azure gyorsindítási sablonban** kattintson az Üzembe helyezés az Azure-ban lehetőségre.

2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, a paramétereket, a jogi feltételeket és a szerződést. Kattintson **a Létrehozás gombra** a titkosítás meglévő vagy futó virtuális gépen való engedélyezéséhez.

A következő táblázat felsorolja Resource Manager meglévő vagy futó virtuális gépek sablonparaméterét:

| Paraméter | Leírás |
| --- | --- |
| vmName | A titkosítási művelet futtatásához használt virtuális gép neve. |
| keyVaultName | Annak a kulcstartónak a neve, amelybe a titkosítási kulcsot fel kell tölteni. Ezt a parancsmag vagy az `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` Azure CLI-parancs használatával kaphatja `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` meg.|
| keyVaultResourceGroup | A kulcstartót tartalmazó erőforráscsoport neve. |
|  keyEncryptionKeyURL | A titkosítási kulcs titkosításához használt kulcstitkosítási kulcs URL-címe. Ez a paraméter nem kötelező, ha a UseExistingKek legördülő listában a **nokek** lehetőséget választja. Ha a **kek lehetőséget választja** a UseExistingKek legördülő listában, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| volumeType (kötet típusa) | A titkosítási művelet által érintett kötet típusa. Érvényes értékek: _OS,_ _Data_, és _All._ 
| forceUpdateTag | Minden alkalommal, amikor kényszeríteni kell a műveletet, olyan egyedi értéket ad át, mint egy GUID. |
| location | Az összes erőforrás helyei. |

További információ a Linux rendszerű virtuális gépek lemeztitkosítási sablonjának konfigurálásával kapcsolatban: Azure Disk Encryption [Linux rendszeren.](../extensions/azure-disk-enc-linux.md)

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Az EncryptFormatAll funkció használata linuxos virtuális gépeken lévő adatlemezek esetén

Az **EncryptFormatAll** paraméter csökkenti a Linux-adatlemezek titkosításának idejét. A rendszer a jelenlegi fájlrendszerekkel együtt formázza a megadott feltételeknek megfelelő partíciókat, majd újra leválasztja őket arra a pontra, ahol a parancs végrehajtása előtt voltak. Ha ki szeretne zárni egy olyan adatlemezt, amely megfelel a feltételeknek, a parancs futtatása előtt leválaszthatja azt.

 A parancs futtatása után a korábban csatlakoztatott meghajtók formázva lesznek, és a titkosítási réteg az mostantól üres meghajtón indul el. Ha ezt a beállítást választja, a virtuális géphez csatolt ideiglenes lemez is titkosítva lesz. Ha az ideiglenes lemezt alaphelyzetbe állítja, a virtuális gép újraformálhatja és újratitkosítani fogja a Azure Disk Encryption a következő lehetőségnél. Az erőforráslemez titkosítása után az [Microsoft Azure Linux-ügynök](../extensions/agent-linux.md) nem tudja kezelni az erőforráslemezt és engedélyezni a felcserélési fájlt, de manuálisan is konfigurálhatja a felcserélési fájlt.

>[!WARNING]
> Az EncryptFormatAll nem használható, ha egy virtuális gép adatkötetén szükség van adatokra. A lemezek leválasztása után kizárhatja a lemezeket a titkosításból. Először egy teszt virtuális gépen próbálja ki az EncryptFormatAll paramétert, majd az éles virtuális gépen való tesztelés előtt értse meg a funkcióparamétert és annak kihatásait. Az EncryptFormatAll beállítás formázi az adatlemezt, és a rajta lévő összes adat elveszni fog. Mielőtt továbblépne, ellenőrizze, hogy a kizárni kívánt lemezek megfelelően vannak-e leválasztva. </br></br>
 >Ha ezt a paramétert a titkosítási beállítások frissítése közben használja, előfordulhat, hogy a tényleges titkosítás előtt újraindul. Ebben az esetben a nem formázni kívánt lemezt is el kell távolítania az fstab fájlból. Hasonlóképpen, a titkosítási művelet megkezdése előtt hozzá kell adni a titkosítani kívánt partíciót az fstab fájlhoz. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll feltétel
A paraméter az összes partíciót titkosítja, ha  megfelelnek az alábbi feltételeknek:
- Nem gyökér-/operációsrendszer-/rendszerindítási partíció
- Még nincs titkosítva
- Nem BEK-kötet
- Nem RAID-kötet
- Nem LVM-kötet
- Csatlakoztatva van

A RAID- vagy LVM-kötet helyett a RAID- vagy LVM-kötetet alkotó lemezeket titkosítsa.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Az EncryptFormatAll paraméter használata az Azure CLI-val
Az [az vm encryption enable paranccsal](/cli/azure/vm/encryption#az_vm_encryption_enable) engedélyezheti a titkosítást egy futó azure-beli virtuális gépen.

-  **Futó virtuális gép titkosítása az EncryptFormatAll használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Az EncryptFormatAll paraméter használata PowerShell-parancsmaggal
Használja a [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmagot az EncryptFormatAll paraméterrel. 

**Futó virtuális gép titkosítása az EncryptFormatAll használatával:** Az alábbi szkript például inicializálja a változókat, és futtatja Set-AzVMDiskEncryptionExtension az EncryptFormatAll paraméterrel. Az erőforráscsoport, a virtuális gép és a kulcstartó előfeltételként jött létre. Cserélje le a MyVirtualMachineResourceGroup, MySecureVM és MySecureVault értékeket a saját értékeire.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "data" -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Az EncryptFormatAll paraméter használata a Logikaikötet-kezelővel (LVM) 
Javasoljuk az LVM on-crypt beállítását. Az alábbi példákban cserélje le az eszközútvonalat és a csatlakoztatási pontokat az Ön által használt esetnek megfelelőkre. Ez a beállítás a következőképpen történik:

1.  Adja hozzá a virtuális gépet alkotó adatlemezeket.

1. Formázza, csatlakoztassa és adja hozzá ezeket a lemezeket az fstab fájlhoz.

1. Válasszon egy partíciós szabványt, hozzon létre egy partíciót, amely a teljes meghajtón átível, majd formázza a partíciót. Itt az Azure által létrehozott symlinkeket használjuk. A symlinkek használatával elkerülhetők az eszköznevekkel kapcsolatos problémák. További információt az Eszköznevek hibaelhárítása – problémák [elhárítása cikkben](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems) talál.
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. Csatlakoztassa a lemezeket:

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    Hozzáadás az fstab fájlhoz:

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. Futtassa Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmagot az -EncryptFormatAll parancsmaggal a lemezek titkosításához.

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    Ha kulcstitkosítási kulcsot (KEK) szeretne használni, adja át a KEK URI-ét és a kulcstartó ResourceID azonosítóit a -KeyEncryptionKeyUrl és -KeyEncryptionKeyVaultId paramétereknek:

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. Állítsa be az LVM-et ezeken az új lemezeken. Vegye figyelembe, hogy a titkosított meghajtók zárolása a virtuális gép rendszerindításának befejezése után lesz feloldva. Ezért az LVM-csatlakoztatást is késleltetni kell.

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Ügyfél által titkosított VHD-ről és titkosítási kulcsokról létrehozott új virtuális gépek
Ebben a forgatókönyvben a titkosítást PowerShell-parancsmagokkal vagy CLI-parancsokkal engedélyezheti. 

Kövesse az Azure Disk Encryptionre vonatkozó utasításokat az Azure-ban használható, előre titkosított rendszerképek előkészítéséhez. A rendszerkép létrehozása után a következő szakaszban található lépésekkel létrehozhat egy titkosított Azure-beli virtuális gépet.

* [Előre titkosított Linux virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >A felügyelt lemezen alapuló virtuálisgép-példányok pillanatképét és/vagy biztonsági mentését kötelező a szolgáltatáson kívül, illetve a virtuális gépek engedélyezése Azure Disk Encryption. A felügyelt lemezről pillanatképet készíthet a portálról, vagy Azure Backup [is.](../../backup/backup-azure-vms-encryption.md) A biztonsági másolatok biztosítják, hogy a titkosítás során váratlan hiba esetén lehetséges legyen a helyreállítási beállítás. A biztonsági mentés után a Set-AzVMDiskEncryptionExtension parancsmag a -skipVmBackup paraméter megadásával használható a felügyelt lemezek titkosítására. A Set-AzVMDiskEncryptionExtension parancs sikertelen lesz a felügyelt lemezen alapuló virtuális gépeken, amíg meg nem történt a biztonsági mentés, és meg nem lett adva ez a paraméter. 
>
> A titkosítás titkosítása vagy letiltása a virtuális gép újraindítását okozhatja. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>A Azure PowerShell virtuális gépek titkosítása előtitkos VHD-ekkel 
A lemeztitkosítást a titkosított VHD-n a [Set-AzVMOSDisk PowerShell-parancsmag](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples)használatával engedélyezheti. Az alábbi példa néhány gyakori paramétert mutat be. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Titkosítás engedélyezése újonnan hozzáadott adatlemezen

Hozzáadhat egy új adatlemezt az [az vm disk attach](add-disk.md), vagy az Azure Portal. [](attach-disk-portal.md) A titkosítás előtt először csatlakoztatnia kell az újonnan csatlakoztatott adatlemezt. Az adat meghajtótitkosítást kell kérnie, mivel a meghajtó nem lesz használható, amíg a titkosítás folyamatban van. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Titkosítás engedélyezése újonnan hozzáadott lemezen az Azure CLI használatával

 Ha a virtuális gépet korábban "All" titkosítással titkosították, akkor a --volume-type paraméternek az "All" (Összes) értéknek kell maradnia. Mindegyik magában foglalja az operációs rendszert és az adatlemezeket is. Ha a virtuális gép korábban "OS" kötettípussal lett titkosítva, akkor a --volume-type paramétert "All" (Összes) paraméterre kell módosítani, hogy az operációs rendszer és az új adatlemez is benne legyen. Ha a virtuális gép csak az "Adatok" kötettípussal lett titkosítva, akkor az "Adatok" maradhat az alább látható módon. Új adatlemez hozzáadása és csatlakoztatása egy virtuális géphez nem elegendő a titkosítás előkészítéséhez. A titkosítás engedélyezése előtt az újonnan csatlakoztatott lemezt is formázni és megfelelően csatlakoztatni kell a virtuális gépen belül. Linux rendszeren a lemezt az /etc/fstab fájlhoz kell csatlakoztatni egy állandó [blokkeszköznévvel.](../troubleshooting/troubleshoot-device-names-problems.md)  

A PowerShell-szintaxissal ellentétben a parancssori felület nem követeli meg, hogy a felhasználó egyedi sorozatverziót adjon meg a titkosítás engedélyezésekor. A CLI automatikusan létrehozza és felhasználja a saját egyedi sorozatverzió-értékét.

-  **Egy futó virtuális gép adatkötetének titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Egy futó virtuális gép adatkötetének titkosítása kEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Engedélyezze a titkosítást egy újonnan hozzáadott lemezen Azure PowerShell
 Ha a PowerShell használatával titkosít egy új lemezt Linux rendszeren, meg kell adni egy új sorozatverziót. A sorozatverziónak egyedinek kell lennie. Az alábbi szkript létrehoz egy GUID-t a sorozatverzióhoz. A [lemezek titkosítása](snapshot-copy-managed-disk.md) előtt pillanatfelvételt készíthet [és/vagy](../../backup/backup-azure-vms-encryption.md) biztonsági Azure Backup virtuális gépről. A -skipVmBackup paraméter már meg van adva a PowerShell-szkriptek között az újonnan hozzáadott adatlemez titkosításához.
 

-  **Egy futó virtuális gép adatkötetének titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja Set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép és a kulcstartó már létre lett hozva előfeltételként. Cserélje le a MyVirtualMachineResourceGroup, MySecureVM és MySecureVault értékeket a saját értékeire. A -VolumeType paraméter elfogadható értékei az Összes, az Operációs rendszer és az Adatok. Ha a virtuális gépet korábban "OS" vagy "All" kötettípussal titkosították, akkor a -VolumeType paramétert "All" (Összes) típusra kell módosítani, hogy az operációs rendszer és az új adatlemez is benne legyen.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Egy futó virtuális gép adatkötetének titkosítása kEK használatával:** A -VolumeType paraméter elfogadható értékei az Összes, az Operációs rendszer és az Adatok. Ha a virtuális gépet korábban "OS" vagy "All" kötettípussal titkosították, akkor a -VolumeType paramétert All (Összes) értékre kell módosítani, hogy az operációs rendszer és az új adatlemez is benne legyen.

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > A disk-encryption-keyvault paraméter értékének szintaxisa a teljes azonosító sztring: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A key-encryption-key paraméter értékének szintaxisa a KEK teljes URI-ját használja a következő módon: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Linux rendszerű virtuális gépek titkosításának letiltása
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

Azure Disk Encryption a következő Linux-forgatókönyvekben, -funkciókban és -technológiákban nem működik:

- A klasszikus virtuálisgép-létrehozási módszerrel létrehozott alapszintű virtuális gépek vagy virtuális gépek titkosítása.
- A titkosítás letiltása egy Linux rendszerű virtuális gép operációsrendszer-meghajtón vagy adat meghajtón, ha az operációsrendszer-meghajtó titkosítva van.
- Az operációsrendszer-meghajtó titkosítása Linux rendszerű virtuálisgép-méretezési készletekhez.
- Egyéni rendszerképek titkosítása Linux rendszerű virtuális gépeken.
- Integráció helyszíni kulcskezelő rendszerrel.
- Azure Files (megosztott fájlrendszer).
- Hálózati fájlrendszer (NFS).
- Dinamikus kötetek.
- Aphemerális operációsrendszer-lemezek.
- A megosztott/elosztott fájlrendszerek titkosítása, például (de nem kizárólagosan): DFS, GFS, DRDB és CephFS.
- Titkosított virtuális gép átköltöztet egy másik előfizetésbe vagy régióba.
- Egy titkosított virtuális gép rendszerképének vagy pillanatképének létrehozása és használata további virtuális gépek üzembe helyezéséhez.
- Kernel összeomlási memóriaképe (kdump).
- Oracle ACFS (ASM-fürt fájlrendszere).
- Az Lsv2 sorozatú virtuális gépek NVMe lemezei (lásd: [Lsv2 sorozat).](../lsv2-series.md)
- Virtuális gép "beágyazott csatlakoztatási pontokkal"; ez azt jelenti, hogy több csatlakoztatási pont egyetlen útvonalon (például "/1stmountpoint/data/2stmountpoint").
- Olyan virtuális gép, amely egy operációsrendszer-mappa tetején csatlakoztatott adat meghajtóval van csatlakoztatva.
- Olyan virtuális gép, amelyen a gyökér (operációsrendszer-lemez) logikai kötet adatlemez használatával lett kiterjesztve.
- M-sorozat virtuális gépei írásgyorsító lemezekkel.
- Az ADE alkalmazása olyan virtuális gépre, amely kiszolgálóoldali titkosítással, ügyfél által felügyelt kulcsokkal [(SSE](../disk-encryption.md) + CMK) titkosított lemezekkel rendelkezik. Az SSE + CMK ADE-val titkosított virtuális gépek adatlemezén való alkalmazása nem támogatott forgatókönyv.
- ADE-val titkosított vagy ADE-val valaha  is titkosított virtuális gép áttelepítése kiszolgálóoldali titkosításra ügyfél által kezelt [kulcsokkal.](../disk-encryption.md)
- Feladatátvevő fürtökben tárolt virtuális gépek titkosítása.
- Az [Azure ultralemezek titkosítása.](../disks-enable-ultra-ssd.md)

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption áttekintés](disk-encryption-overview.md)
- [Azure Disk Encryption – mintaszkriptek](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption – hibaelhárítás](disk-encryption-troubleshooting.md)