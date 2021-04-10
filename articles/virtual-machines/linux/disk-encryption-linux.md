---
title: Azure Disk Encryption-forgatókönyvek Linux rendszerű virtuális gépeken
description: Ez a cikk a Linux rendszerű virtuális gépek Microsoft Azure lemezes titkosításának engedélyezéséhez nyújt útmutatást különböző forgatókönyvek esetén
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 6ebec78d5a9e82cc8a2f6ceb020a8b9552d6311e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604015"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Azure Disk Encryption-forgatókönyvek Linux rendszerű virtuális gépeken

A Linux rendszerű virtuális gépek Azure Disk Encryption (VM) a Linux DM-Crypt szolgáltatásával biztosítja az operációsrendszer-lemez és az adatlemezek teljes lemezes titkosítását. Emellett az ideiglenes lemez titkosítását is biztosítja az EncryptFormatAll szolgáltatás használatakor.

Azure Disk Encryption [integrálva van Azure Key Vault](disk-encryption-key-vault.md) a lemezes titkosítási kulcsok és titkos kódok felügyeletéhez és kezeléséhez. A szolgáltatás áttekintését lásd: [Azure Disk Encryption Linux rendszerű virtuális gépekhez](disk-encryption-overview.md).

A lemezes titkosítást csak a [támogatott virtuálisgép-méretek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)virtuális gépei esetében lehet alkalmazni. A következő előfeltételeknek is teljesülniük kell:

- [A virtuális gépekre vonatkozó további követelmények](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Hálózati követelmények](disk-encryption-overview.md#networking-requirements)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview.md#encryption-key-storage-requirements)

Minden esetben készítsen [pillanatképet](snapshot-copy-managed-disk.md) és/vagy készítsen biztonsági másolatot a lemezek titkosítása előtt. A biztonsági másolatok gondoskodnak arról, hogy a rendszer egy helyreállítási lehetőséget akkor lehessen, ha nem várt hiba történik a titkosítás során. A felügyelt lemezekkel rendelkező virtuális gépek biztonsági mentést igényelnek a titkosítás megkezdése előtt. A biztonsági mentés után a [set-AzVMDiskEncryptionExtension parancsmag](/powershell/module/az.compute/set-azvmdiskencryptionextension) használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. További információ a titkosított virtuális gépek biztonsági mentéséről és visszaállításáról: [Azure Backup](../../backup/backup-azure-vms-encryption.md) . 

>[!WARNING]
> - Ha korábban már használta Azure Disk Encryption az Azure AD-vel egy virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Részletekért lásd: [Azure Disk Encryption az Azure ad-vel (előző kiadás)](disk-encryption-overview-aad.md) . 
>
> - A Linux operációs rendszer köteteinek titkosításakor a virtuális gépet nem szabad megfontolni. Határozottan javasoljuk, hogy az SSH-bejelentkezések elkerülése érdekében a titkosítási folyamat során ne akadályozza meg az összes olyan megnyitott fájl blokkolását, amelyet a titkosítási folyamat során el kell érnie. A folyamat ellenőrzéséhez használja a [Get-AzVMDiskEncryptionStatus PowerShell-](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot vagy a [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI-parancsot. Ez a folyamat várhatóan néhány órát is igénybe vehet egy 30 GB-OS kötetre, és további időt az adatkötetek titkosítására. Az adatmennyiség-titkosítási idő arányos lesz az adatkötetek méretétől és mennyiségétől, kivéve, ha az összes titkosítás formátuma beállítást használja. 
> - A Linux rendszerű virtuális gépek titkosításának letiltása csak az adatkötetek esetében támogatott. Ha az operációsrendszer-kötet titkosítva van, a rendszer nem támogatja az adatvagy operációsrendszer-köteteken.  

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és az Azure-hoz való kapcsolódás

Azure Disk Encryption engedélyezhető és felügyelhető az [Azure CLI](/cli/azure) -n és [Azure PowerShellon](/powershell/azure/new-azureps-module-az)keresztül. Ehhez helyileg kell telepítenie az eszközöket, és csatlakoznia kell az Azure-előfizetéséhez.

### <a name="azure-cli"></a>Azure CLI

Az [Azure CLI 2,0](/cli/azure) egy parancssori eszköz az Azure-erőforrások kezeléséhez. A CLI rugalmas lekérdezési adatokra lett tervezve, támogatja a hosszan futó műveleteket a nem blokkoló folyamatokban, és megkönnyíti a parancsfájlok futtatását. Az [Azure CLI telepítése](/cli/azure/install-azure-cli)című témakör lépéseit követve helyileg is telepítheti.

 

Ha be [szeretné jelentkezni az Azure-fiókjába az Azure CLI-vel](/cli/azure/authenticate-azure-cli), használja az az [login](/cli/azure/reference-index#az_login) parancsot.

```azurecli
az login
```

Ha ki szeretné választani a bejelentkezni kívánt bérlőt, használja a következőt:
    
```azurecli
az login --tenant <tenant>
```

Ha több előfizetéssel rendelkezik, és meg szeretne adni egy adott ilyet, szerezze be az előfizetések listáját az [az Account List](/cli/azure/account#az-account-list) paranccsal, és adja meg az az [Account set](/cli/azure/account#az-account-set)paranccsal.
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

További információ: Ismerkedés [Az Azure CLI 2,0](/cli/azure/get-started-with-azure-cli)-mel. 

### <a name="azure-powershell"></a>Azure PowerShell
Az [Azure PowerShell az modul](/powershell/azure/new-azureps-module-az) olyan parancsmagokat biztosít, amelyek a [Azure Resource Manager](../../azure-resource-manager/management/overview.md) modellt használják az Azure-erőforrások kezeléséhez. A böngészőben a [Azure Cloud Shell](../../cloud-shell/overview.md)használatával is használhatja, vagy telepítheti a helyi gépre a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című részben leírtak szerint. 

Ha már helyileg telepítette, győződjön meg arról, hogy a Azure Disk Encryption konfigurálásához a Azure PowerShell SDK legújabb verzióját használja. Töltse le [Azure PowerShell kiadás](https://github.com/Azure/azure-powershell/releases)legújabb verzióját.

Ha [Azure PowerShell használatával szeretne bejelentkezni az Azure-fiókjába](/powershell/azure/authenticate-azureps), használja a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmagot.

```powershell
Connect-AzAccount
```

Ha több előfizetéssel rendelkezik, és meg szeretne adni egyet, a [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) parancsmaggal listázhatja őket, majd a [set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmagot:

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

A [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) parancsmag futtatásával ellenőrizheti, hogy a megfelelő előfizetés van-e kiválasztva.

A Azure Disk Encryption-parancsmagok telepítésének megerősítéséhez használja a [Get-Command](/powershell/module/microsoft.powershell.core/get-command) parancsmagot:
     
```powershell
Get-command *diskencryption*
```
További információ: [Bevezetés a Azure PowerShell](/powershell/azure/get-started-azureps)használatába. 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen
Ebben az esetben a titkosítást a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával engedélyezheti. Ha a virtuálisgép-bővítményre vonatkozó séma-információra van szüksége, tekintse meg a [Linux-bővítmények Azure Disk Encryptionét](../extensions/azure-disk-enc-linux.md) ismertető cikket.

>[!IMPORTANT]
 >A felügyelt lemezes virtuálisgép-példányok pillanatképe és/vagy biztonsági mentése kötelező, a Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképe a portálról vagy [Azure Backupon](../../backup/backup-azure-vms-encryption.md)keresztül végezhető el. A biztonsági mentések biztosítják, hogy a titkosítás során fellépő váratlan hibák esetén a helyreállítási lehetőség is lehetséges legyen. A biztonsági mentést követően a Set-AzVMDiskEncryptionExtension parancsmag használható a felügyelt lemezek titkosítására a-skipVmBackup paraméter megadásával. A Set-AzVMDiskEncryptionExtension parancs a felügyelt lemezes virtuális gépeken meghiúsul, amíg biztonsági másolat készül, és ez a paraméter meg lett adva. 
>
>A titkosítás titkosítása vagy letiltása miatt előfordulhat, hogy a virtuális gép újraindul. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen az Azure CLI használatával 

Az [Azure CLI](/cli/azure/) parancssori eszköz telepítésével és használatával engedélyezheti a lemez titkosítását a titkosított virtuális merevlemezen. Használhatja a böngészőjében az [Azure Cloud Shell-lel](../../cloud-shell/overview.md), vagy telepítheti a helyi gépen, és használhatja bármely PowerShell-munkamenetben. Az Azure-ban meglévő vagy futó Linux rendszerű virtuális gépek titkosításának engedélyezéséhez használja a következő CLI-parancsokat:

Az az [VM encryption Enable](/cli/azure/vm/encryption#az_vm_encryption_show) parancs használatával engedélyezheti a titkosítást egy futó virtuális gépen az Azure-ban.

- **Futó virtuális gép titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Futó virtuális gép titkosítása a KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br>
A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy virtuális gép titkosítási állapotának megtekintéséhez használja az az [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. A titkosítás letiltása csak a Linux rendszerű virtuális gépek adatkötetein engedélyezett.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen a PowerShell használatával
A [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást egy futó virtuális gépen az Azure-ban. Készítsen [pillanatképet](snapshot-copy-managed-disk.md) és/vagy készítsen biztonsági másolatot a virtuális gépről [Azure Backup](../../backup/backup-azure-vms-encryption.md) a lemezek titkosítása előtt. A-skipVmBackup paraméter már meg van adva a PowerShell-parancsfájlokban egy futó Linux rendszerű virtuális gép titkosításához.

-  **Futó virtuális gép titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép és a Key Vault előfeltételként lett létrehozva. Cserélje le az MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értékeket az értékekre. Módosítsa a-VolumeType paramétert annak megadásához, hogy mely lemezek vannak titkosítva.

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
- **Futó virtuális gép titkosítása a KEK használatával:** Előfordulhat, hogy a-VolumeType paramétert kell hozzáadnia, ha adatlemezeket titkosít, és nem az operációsrendszer-lemezt. 

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
    > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br> A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 
    
- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** A virtuális gépek titkosítási állapotának vizsgálatához használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Lemez titkosításának letiltása:** A titkosítás letiltásához használja a [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. A titkosítás letiltása csak a Linux rendszerű virtuális gépek adatkötetein engedélyezett.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen sablon alapján

Az Azure-ban a [Resource Manager-sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)engedélyezheti a lemez titkosítását egy meglévő vagy futó Linux rendszerű virtuális gépen.

1. Az Azure rövid útmutató sablonjában kattintson az **üzembe helyezés az Azure-** ba lehetőségre.

2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, a paramétereket, a jogi feltételeket és a szerződést. A **Létrehozás** gombra kattintva engedélyezheti a titkosítást a meglévő vagy futó virtuális gépen.

Az alábbi táblázat a meglévő vagy futó virtuális gépek Resource Manager-sablonjának paramétereit sorolja fel:

| Paraméter | Leírás |
| --- | --- |
| vmName | A titkosítási műveletet futtató virtuális gép neve. |
| keyVaultName | Annak a kulcstárolónak a neve, amelyre a titkosítási kulcsot fel kell tölteni. A parancsmag `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` vagy az Azure CLI-parancs használatával kérheti le `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` .|
| keyVaultResourceGroup | A kulcstárolót tartalmazó erőforráscsoport neve. |
|  keyEncryptionKeyURL | A titkosítási kulcs titkosításához használt kulcs titkosítási kulcsának URL-címe. Ez a paraméter nem kötelező, ha a UseExistingKek legördülő listában a **nokek** lehetőséget választja. Ha a UseExistingKek legördülő listában a **KEK** elemet választja, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| volumeType | A titkosítási művelet végrehajtásához használt kötet típusa. Az érvényes értékek az _operációs rendszer_, _az adatok_ és _az összes_. 
| forceUpdateTag | Adjon meg egy egyedi értéket, például egy GUID-azonosítót, amikor a műveletnek kényszerített futtatást kell futtatnia. |
| location | Az összes erőforrás helyei. |

A Linux rendszerű virtuális gép lemezének titkosítási sablonjának konfigurálásával kapcsolatos további információkért lásd: [Azure Disk Encryption Linux rendszerhez](../extensions/azure-disk-enc-linux.md).

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>A EncryptFormatAll funkció használata a Linux rendszerű virtuális gépeken található adatlemezekhez

A **EncryptFormatAll** paraméter csökkenti a Linux-adatlemezek titkosításának idejét. A bizonyos feltételeknek megfelelő partíciók a jelenlegi fájlrendszerrel együtt lesznek formázva, majd a parancs végrehajtása előtt újra lecsatlakoznak a rendszerbe. Ha ki szeretne zárni egy olyan adatlemezt, amely megfelel a feltételeknek, leválaszthatja azt a parancs futtatása előtt.

 A parancs futtatása után a korábban csatlakoztatott meghajtók formázva lesznek, és a titkosítási réteg a most üres meghajtón lesz elindítva. Ha ez a beállítás be van jelölve, a virtuális géphez csatolt ideiglenes lemez is titkosítva lesz. Ha az ideiglenes lemez alaphelyzetbe áll, a rendszer a következő lehetőségnél újraformázza és újra titkosítja a virtuális gépet a Azure Disk Encryption megoldással. Az erőforrás-lemez titkosítása után a [Microsoft Azure Linux-ügynök](../extensions/agent-linux.md) nem fogja tudni kezelni az erőforrás lemezét, és engedélyezi a lapozófájlt, de manuálisan is konfigurálhatja a lapozófájlt.

>[!WARNING]
> A EncryptFormatAll nem használható, ha a virtuális gép adatkötetein szükség van a szükséges értékekre. A lemezek a titkosításból való leválasztásával zárhatók ki. Először próbálja ki először a EncryptFormatAll a tesztelési virtuális gépen, és tekintse át a funkció paraméterét és annak következményeit az éles virtuális gépen való kipróbálás előtt. A EncryptFormatAll beállítás az adatlemezt formázza, és a rajta lévő összes adattal elvész. A továbblépés előtt ellenőrizze, hogy a kizárni kívánt lemezek megfelelően le vannak-e választva. </br></br>
 >Ha ezt a paramétert a titkosítási beállítások frissítésekor állítja be, akkor a tényleges titkosítás előtt újraindítást eredményezhet. Ebben az esetben el kell távolítania azt a lemezt is, amelyet nem kíván formázni az fstab-fájlból. Hasonlóképpen, a titkosítási művelet megkezdése előtt adja hozzá a titkosítani kívánt partíciót az fstab-fájlhoz. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll feltételek
A (z) paraméter minden partíciót megnyit, és titkosítja őket, ha az alábbi feltételek **mindegyike** teljesül:
- Nem root/OS/rendszerindító partíció
- Még nincs titkosítva
- Nem egy BEK-kötet
- Nem RAID-kötet
- Nem egy LVM kötet
- Csatlakoztatva van

Titkosítsa a RAID vagy az LVM kötetet alkotó lemezeket a RAID vagy az LVM kötet helyett.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>A EncryptFormatAll paraméter használata az Azure CLI-vel
Az az [VM encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) parancs használatával engedélyezheti a titkosítást egy futó virtuális gépen az Azure-ban.

-  **Futó virtuális gép titkosítása EncryptFormatAll használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>A EncryptFormatAll paraméter használata PowerShell-parancsmaggal
Használja a [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmagot a EncryptFormatAll paraméterrel. 

**Futó virtuális gép titkosítása EncryptFormatAll használatával:** Az alábbi szkript például inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmagot a EncryptFormatAll paraméterrel. Az erőforráscsoport, a virtuális gép és a Key Vault előfeltételként lett létrehozva. Cserélje le az MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értékeket az értékekre.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>A EncryptFormatAll paraméter használata a logikai kötet-kezelővel (LVM) 
Javasoljuk, hogy legyen egy LVM-on-Crypt beállítás. Az alábbi példákban cserélje le az eszköz-elérési utat és a csatolási a használati esetre. Ezt a telepítést a következőképpen teheti meg:

1.  Adja hozzá a virtuális gépet alkotó adatlemezeket.

1. Formázza, csatlakoztassa és adja hozzá ezeket a lemezeket az fstab-fájlhoz.

1. Válasszon egy partíciós szabványt, hozzon létre egy partíciót, amely a teljes meghajtót felöleli, majd formázza a partíciót. Az Azure által generált symlink-ket itt fogjuk használni. A symlink-EK használata elkerüli az eszközök nevének változásával kapcsolatos problémákat. További információt az [eszközök neveivel kapcsolatos problémák elhárítása](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems) című cikkben talál.
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. A lemezek csatlakoztatása:

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    Hozzáadás az fstab-fájlhoz:

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. Futtassa a Azure PowerShell [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmagot a-EncryptFormatAll használatával a lemezek titkosításához.

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    Ha a kulcs titkosítási kulcsát (KEK) kívánja használni, adja át a KEK URI-JÁT és a kulcstartó ResourceID a-KeyEncryptionKeyUrl és a-KeyEncryptionKeyVaultId paraméterekbe:

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. Az LVM beállítása az új lemezek felett. Figyelje meg, hogy a titkosított meghajtók zárolása a virtuális gép elindítása után megtörtént. Ezért az LVM csatlakoztatását is el kell halasztani.

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Az ügyfél által titkosított VHD-és titkosítási kulcsokból létrehozott új virtuális gépek
Ebben az esetben PowerShell-parancsmagokkal vagy CLI-parancsokkal engedélyezheti a titkosítást. 

Használja az Azure Disk Encryption szolgáltatásban megjelenő utasításokat az Azure-ban használható előre titkosított rendszerképek előkészítéséhez. A rendszerkép létrehozása után a következő szakasz lépéseit követve hozzon létre egy titkosított Azure-beli virtuális gépet.

* [Előre titkosított linuxos virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >A felügyelt lemezes virtuálisgép-példányok pillanatképe és/vagy biztonsági mentése kötelező, a Azure Disk Encryption engedélyezése előtt. A felügyelt lemezről pillanatképet készíthet a portálról, vagy [Azure Backup](../../backup/backup-azure-vms-encryption.md) használható. A biztonsági mentések biztosítják, hogy a titkosítás során fellépő váratlan hibák esetén a helyreállítási lehetőség is lehetséges legyen. A biztonsági mentést követően a Set-AzVMDiskEncryptionExtension parancsmag használható a felügyelt lemezek titkosítására a-skipVmBackup paraméter megadásával. A Set-AzVMDiskEncryptionExtension parancs a felügyelt lemezes virtuális gépeken meghiúsul, amíg biztonsági másolat készül, és ez a paraméter meg lett adva. 
>
> A titkosítás titkosítása vagy letiltása miatt előfordulhat, hogy a virtuális gép újraindul. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Azure PowerShell használata a virtuális gépek titkosításához előre titkosított VHD-k használatával 
A titkosított virtuális merevlemez titkosítását a [set-AzVMOSDisk PowerShell-](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples)parancsmag használatával engedélyezheti. Az alábbi példa néhány gyakori paramétert biztosít. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Titkosítás engedélyezése újonnan hozzáadott adatlemezen

Új adatlemezt az [az VM Disk Attach](add-disk.md)vagy [a Azure Portal](attach-disk-portal.md)használatával adhat hozzá. A titkosítás előtt először csatlakoztatnia kell az újonnan csatlakoztatott adatlemezt. Meg kell kérnie az adatmeghajtó titkosítását, mivel a meghajtó használhatatlanná válik, amíg a titkosítás folyamatban van. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>A titkosítás engedélyezése az újonnan hozzáadott lemezeken az Azure CLI-vel

 Ha a virtuális gép korábban "all" titkosítással lett titkosítva, akkor a--Volume-Type paraméternek "all" értékűnek kell maradnia. Mind az operációs rendszer és az adatlemezek is szerepelnek. Ha a virtuális gépet korábban "operációs rendszer" típusú kötettel titkosítták, akkor a--Volume-type paramétert az "all" értékre kell módosítani, hogy az operációs rendszer és az új adatlemez is szerepelni fog. Ha a virtuális gépet csak az "adatmennyiség" mennyiségi típussal titkosították, akkor az alább látható módon "az" adatforgalom maradhat. Az új adatlemezek virtuális géphez való hozzáadása és csatolása nem elegendő a titkosítás előkészítéséhez. Az újonnan csatlakoztatott lemeznek a titkosítás engedélyezése előtt a virtuális gépen is formázottnak és megfelelően kell csatlakoztatnia. Linux rendszeren a lemezt egy [állandó blokk-eszköz nevével](../troubleshooting/troubleshoot-device-names-problems.md)kell csatlakoztatni az/etc/fstab-ben.  

A PowerShell-szintaxissal ellentétben a parancssori felület nem igényli, hogy a felhasználó egyedi sorozatot adjon meg a titkosítás engedélyezésekor. A CLI automatikusan generálja és felhasználja a saját egyedi sorszámának értékét.

-  **Egy futó virtuális gép adatköteteinek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Futó virtuális gép adatköteteinek titkosítása a KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>A titkosítás engedélyezése egy újonnan hozzáadott lemezen Azure PowerShell
 Ha a PowerShell használatával titkosít egy új lemezt a Linux rendszerhez, meg kell adni egy új sorozatot. A Sequence verziójának egyedinek kell lennie. Az alábbi szkript létrehoz egy GUID azonosítót a Sequence verzióhoz. Készítsen [pillanatképet](snapshot-copy-managed-disk.md) és/vagy készítsen biztonsági másolatot a virtuális gépről [Azure Backup](../../backup/backup-azure-vms-encryption.md) a lemezek titkosítása előtt. A-skipVmBackup paraméter már meg van adva a PowerShell-parancsfájlokban egy újonnan hozzáadott adatlemez titkosításához.
 

-  **Egy futó virtuális gép adatköteteinek titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép és a Key Vault már előfeltételként lett létrehozva. Cserélje le az MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értékeket az értékekre. A-VolumeType paraméter számára elfogadható értékek az összes, az operációs rendszer és az adatok. Ha a virtuális gépet korábban "OS" vagy "all" típusú kötettel titkosították, akkor a-VolumeType paramétert az "all" értékre kell módosítani, hogy az operációs rendszer és az új adatlemez is szerepelni fog.

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
- **Futó virtuális gép adatköteteinek titkosítása a KEK használatával:** A-VolumeType paraméter számára elfogadható értékek az összes, az operációs rendszer és az adatok. Ha a virtuális gépet korábban "operációs rendszer" vagy "all" típusú kötettel titkosítták, akkor a-VolumeType paramétert úgy kell módosítani, hogy az operációs rendszer és az új adatlemez is szerepeljen.

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
    > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[KVresource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br> A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Linux rendszerű virtuális gépek titkosításának letiltása
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A Azure Disk Encryption a következő Linux-forgatókönyvek, funkciók és technológiák esetében nem működik:

- A klasszikus virtuálisgép-létrehozási módszerrel létrehozott alapszintű virtuális gép vagy virtuális gépek titkosítása.
- Egy Linux rendszerű virtuális gép operációsrendszer-meghajtóján vagy adatmeghajtóján lévő titkosítás letiltása az operációs rendszer meghajtójának titkosítása esetén.
- Az operációsrendszer-meghajtó titkosítása Linux rendszerű virtuálisgép-méretezési csoportokhoz.
- Egyéni lemezképek titkosítása Linux rendszerű virtuális gépeken.
- Integráció egy helyszíni kulcskezelő rendszerrel.
- Azure Files (megosztott fájlrendszer).
- Hálózati fájlrendszer (NFS).
- Dinamikus kötetek.
- Ideiglenes operációsrendszer-lemezek.
- Megosztott/elosztott fájlrendszerek titkosítása, például (de nem kizárólag): DFS, GFS, DRDB és CephFS.
- Titkosított virtuális gép áthelyezése másik előfizetésre vagy régióba.
- Egy titkosított virtuális gép rendszerképének vagy pillanatképének létrehozása, és annak használata további virtuális gépek telepítéséhez.
- Kernel-összeomlási memóriakép (kdump).
- Oracle ACFS (ASM-fürt fájlrendszere).
- A Lsv2 sorozatú virtuális gépek NVMe lemezei (lásd: [Lsv2 sorozat](../lsv2-series.md)).
- Egy "beágyazott csatlakoztatási ponttal" rendelkező virtuális gép; Ez azt eredményezi, hogy több csatlakoztatási pont van egyetlen elérési úton (például "/1stmountpoint/Data/2stmountpoint").
- Egy virtuális gép, amely egy operációsrendszer-mappához csatlakoztatott adatmeghajtóval rendelkezik.
- Egy virtuális gép, amelyen a gyökérszintű (operációsrendszer-lemez) logikai kötet ki lett terjesztve adatlemez használatával.
- Az M sorozatú virtuális gépek írásgyorsító lemezzel.
- Az ADE alkalmazása olyan virtuális gépre, amely kiszolgálóoldali titkosítással titkosított lemezeket tartalmaz [az ügyfél által felügyelt kulcsokkal](../disk-encryption.md) (SSE + CMK). Az SSE és a CMK az ADE-sel titkosított virtuális gépek adatlemezére való alkalmazása nem támogatott forgatókönyv is.
- Az ADE-sel titkosított virtuális gépek áttelepítése, vagy az ADE-sel való titkosítása már **megtörtént,** az [ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosításhoz](../disk-encryption.md).
- Virtuális gépek titkosítása feladatátvevő fürtökben.

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption áttekintése](disk-encryption-overview.md)
- [Azure Disk Encryption – mintaszkriptek](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption – hibaelhárítás](disk-encryption-troubleshooting.md)