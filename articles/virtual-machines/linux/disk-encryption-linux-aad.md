---
title: Azure Disk Encryption linuxos IaaS Azure AD alkalmazás (korábbi kiadás)
description: Ez a cikk útmutatást nyújt a linuxos IaaS Microsoft Azure lemeztitkosítás engedélyezéséhez.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 94440b71eee1ff9dcc4a86733582e3e5f57f6a00
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764620"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Az Azure Disk Encryption Azure AD-vel való használatának engedélyezése Linux rendszerű virtuális gépeken (korábbi kiadás)

Az új Azure Disk Encryption szükségtelenné teszi, hogy Azure Active Directory (Azure AD) alkalmazásparamétert biztosítsunk a virtuális gép lemeztitkosításának engedélyezéséhez. Az új kiadással már nem kell Azure AD-beli hitelesítő adatokat megadnia a titkosítás engedélyezése lépés során. Az új kiadással minden új virtuális gépnek titkosítva kell lennie az Azure AD alkalmazásparaméterei nélkül. A virtuális gépek lemeztitkosításának új kiadással való engedélyezésével Azure Disk Encryption [linuxos virtuális gépekhez.](disk-encryption-linux.md) Az Azure AD-alkalmazásparaméterekkel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is az AAD-szintaxissal kell karbantartani őket.

Számos lemeztitkosítási forgatókönyvet engedélyezhet, és a lépések a forgatókönyvtől függően változhatnak. A következő szakaszok részletesebben ismertetik a linuxos IaaS-alapú virtuális gépek forgatókönyvét. Lemeztitkosítást csak a támogatott virtuálisgép-méretekkel és operációs rendszerekkel [működő virtuális gépekre alkalmazhat.](disk-encryption-overview.md#supported-vms-and-operating-systems) A következő előfeltételeknek is meg kell felelnie:

- [A virtuális gépekre vonatkozó további követelmények](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Hálózat és Csoportházirend](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Készítse [el](snapshot-copy-managed-disk.md)a pillanatképet, készítse el a biztonsági mentést, vagy mindkettőt a lemezek titkosítása előtt. A biztonsági másolatok biztosítják, hogy a titkosítás során váratlan hiba esetén helyreállítási lehetőség legyen lehetséges. A felügyelt lemezekkel virtuális gépek biztonsági mentést igényelnek a titkosítás előtt. A biztonsági mentés után az Set-AzVMDiskEncryptionExtension-skipVmBackup paraméter megadásával titkosíthatja a felügyelt lemezeket. A titkosított virtuális gépek biztonsági mentésének és visszaállításának további információiért lásd: [Azure Backup.](../../backup/backup-azure-vms-encryption.md) 

>[!WARNING]
 > - Ha korábban már használta [Azure Disk Encryption Azure AD-alkalmazással](disk-encryption-overview-aad.md) a virtuális gép titkosításához, akkor továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához. Ezen a titkosított [](disk-encryption-overview.md) virtuális gépen Azure Disk Encryption nem használhatja a virtuális gépet, mert ez nem támogatott forgatókönyv, ami azt jelenti, hogy az Azure AD-alkalmazásról való váltás ehhez a titkosított virtuális géphez még nem támogatott.
 > - Annak érdekében, hogy a titkosítási titkos kulcsok ne lépik át a régiók határait, Azure Disk Encryption a kulcstartónak és a virtuális gépeknek ugyanabban a régióban kell lennie. Hozzon létre és használjon egy kulcstartót, amely ugyanabban a régióban található, mint a titkosítani szükséges virtuális gép.
 > - A Linux operációsrendszer-kötetek titkosítása néhány órát is igénybe vehet. A Linux operációsrendszer-kötetek titkosítása az adatkötetnél hosszabb ideig tart.
> - A Linux operációsrendszer-kötetek titkosítva a virtuális gépet nem elérhetőnek kell tekinteni. Erősen ajánlott elkerülni az SSH-bejelentkezéseket, amíg a titkosítás folyamatban van, hogy ne blokkolja a titkosítási folyamat során elérni szükséges fájlokat. A folyamat előrehaladásának ellenőrzéshez használja a [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) vagy [a vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) parancsot. Ez a folyamat várhatóan néhány órát fog igénybe venni egy 30 GB-os operációsrendszer-köteten, és további időt is igénybe fog venni az adatkötetek titkosítása során. Az adatmennyiség titkosítási ideje arányos az adatkötetek  méretével és mennyiségével, kivéve, ha az összes titkosítási formátumot használja. 
 > - A titkosítás letiltása Linux rendszerű virtuális gépeken csak adatkötetek esetén támogatott. Az adatok és az operációsrendszer-kötetek nem támogatják, ha az operációs rendszer kötete titkosítva van. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"></a> Titkosítás engedélyezése meglévő vagy futó IaaS Linux rendszerű virtuális gépen

Ebben a forgatókönyvben engedélyezheti a titkosítást a Azure Resource Manager sablon, PowerShell-parancsmagok vagy Azure CLI-parancsok használatával. 

>[!IMPORTANT]
 >A felügyelt lemezen alapuló virtuálisgép-példányról a virtuális gépek engedélyezése előtt és kívül is kötelező pillanatképet készíteni, vagy biztonsági Azure Disk Encryption. Pillanatfelvételt készíthet a felügyelt lemezről a Azure Portal, vagy használhatja a [Azure Backup.](../../backup/backup-azure-vms-encryption.md) A biztonsági másolatok biztosítják, hogy a titkosítás során váratlan hiba esetén lehetséges legyen a helyreállítási beállítás. A biztonsági mentés után a Set-AzVMDiskEncryptionExtension -skipVmBackup paraméter megadásával titkosítsa a felügyelt lemezeket. A Set-AzVMDiskEncryptionExtension parancs meghiúsul a felügyelt lemezalapú virtuális gépeken, amíg biztonsági mentés nem készül, és meg nem adja ezt a paramétert. 
>
>A titkosítás titkosítása vagy letiltása a virtuális gép újraindítását okozhatja. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen az Azure CLI használatával 
A lemeztitkosítást a titkosított VIRTUÁLIS merevlemezen az [Azure CLI 2.0](/cli/azure) parancssori eszköz telepítésével és használatával engedélyezheti. Használhatja a böngészőjében az [Azure Cloud Shell-lel](../../cloud-shell/overview.md), vagy telepítheti a helyi gépen, és használhatja bármely PowerShell-munkamenetben. Ha engedélyezni szeretné a titkosítást meglévő vagy futó IaaS Linux rendszerű virtuális gépeken az Azure-ban, használja a következő CLI-parancsokat:

Az [az vm encryption enable paranccsal](/cli/azure/vm/encryption#az_vm_encryption_enable) engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban.

-  **Futó virtuális gép titkosítása titkos ügyfélkvóta használatával:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Titkosítsa a futó virtuális gépeket a KEK használatával az ügyfél titkos kódba burkolva:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > A disk-encryption-keyvault paraméter értékének szintaxisa a teljes azonosító sztring: /subscriptions/[előfizetés-azonosító-guid]/resourceGroups/[erőforráscsoport-neve]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br> A key-encryption-key paraméter értékének szintaxisa a KEK teljes URI-ját használja a következő módon: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának ellenőrzéshez használja [az az vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) parancsot. 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja [az az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) parancsot. A titkosítás letiltása csak Linux rendszerű virtuális gépek adatkötetén engedélyezett.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"></a> Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen a PowerShell használatával
A [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban. A lemezek [titkosítása](snapshot-copy-managed-disk.md) előtt pillanatfelvételt készíthet, vagy biztonsági másolatot készíthet Azure Backup [virtuális](../../backup/backup-azure-vms-encryption.md) gépről. A -skipVmBackup paraméter már meg van adva a PowerShell-szkriptek között egy futó Linux rendszerű virtuális gép titkosításához.

- **Futó virtuális gép titkosítása titkos ügyféltitk használatával:** A következő szkript inicializálja a változókat, és futtatja Set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép, a kulcstartó, az Azure AD-alkalmazás és a titkos ügyfélkulcs már létre lett hozva előfeltételként. Cserélje le a MyVirtualMachineResourceGroup, a MyKeyVaultResourceGroup, a MySecureVM, a MySecureVault, a My-AAD-client-ID és a My-AAD-client-secret értékeket a saját értékeire. Módosítsa a -VolumeType paramétert annak megadásához, hogy mely lemezeket szeretné titkosítani.

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Titkosítsa a futó virtuális gépeket a KEK használatával az ügyfél titkos kulcsának becsomagolása során:** Azure Disk Encryption segítségével megadhat egy meglévő kulcsot a kulcstartóban a titkosítás engedélyezése során létrehozott lemeztitkosítási titkos kulcsok becsomagolása érdekében. Ha kulcstitkosítási kulcs van megadva, a Azure Disk Encryption használja a titkosítási titkos kulcsok becsomagolása során, mielőtt a kulcstartóba ír. Módosítsa a -VolumeType paramétert annak megadásához, hogy mely lemezeket szeretné titkosítani. 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > A disk-encryption-keyvault paraméter értékének szintaxisa a teljes azonosító sztring: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  A key-encryption-key paraméter értékének szintaxisa a KEK teljes URI-ját használja a következő módon: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Az IaaS virtuális gépek titkosítási állapotának ellenőrzéshez használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Lemeztitkosítás letiltása:** A titkosítás letiltásához használja a [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. A titkosítás letiltása csak Linux rendszerű virtuális gépek adatkötetén engedélyezett.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"></a> Titkosítás engedélyezése egy meglévő vagy futó IaaS Linux rendszerű virtuális gépen sablon használatával

A lemeztitkosítást egy meglévő vagy futó IaaS Linux rendszerű virtuális gépen engedélyezheti az Azure-ban a Resource Manager [használatával.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)

1. Az **Azure gyorsindítási sablonban** válassza az Üzembe helyezés az Azure-ban lehetőséget.

2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, a paramétereket, a jogi feltételeket és a szerződést. Válassza **a Létrehozás** lehetőséget a titkosítás engedélyezéséhez a meglévő vagy futó IaaS virtuális gépen.

A következő táblázat felsorolja Resource Manager Azure AD-ügyfélazonosítót tartalmazó meglévő vagy futó virtuális gépek sablonparaméterét:

| Paraméter | Leírás |
| --- | --- |
| AADClientID | Annak az Azure AD-alkalmazásnak az ügyfél-azonosítója, amely engedéllyel rendelkezik a titkos kulcsok kulcstartóba írásához. |
| AADClientSecret | Annak az Azure AD-alkalmazásnak az ügyfélkulcsa, amely engedéllyel rendelkezik a titkos kulcsok kulcstartóba írásához. |
| keyVaultName | Annak a kulcstartónak a neve, amelybe a kulcsot fel kell tölteni. Ezt az Azure CLI parancsával kaphatja `az keyvault show --name "MySecureVault" --query KVresourceGroup` meg. |
|  keyEncryptionKeyURL | A létrehozott kulcs titkosításához használt kulcstitkosítási kulcs URL-címe. Ez a paraméter nem kötelező, ha a **UseExistingKek** legördülő listában a **nokek** lehetőséget választja. Ha a **kek lehetőséget választja** a **UseExistingKek** legördülő listában, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| volumeType (kötet típusa) | A titkosítási művelet által érintett kötet típusa. Az érvényes támogatott értékek az _operációs rendszer vagy_ a _Mind._ (A korábbi előfeltételek szakaszban tekintse meg a támogatott Linux-disztribúciókat és azok operációsrendszer- és adatlemez-verzióit.) |
| sequenceVersion | A BitLocker-művelet sorozatverziója. Növelje ezt a verziószámot minden alkalommal, amikor lemeztitkosítási műveletet hajt végre ugyanazon a virtuális gépen. |
| vmName | Annak a virtuális gépnek a neve, amelyről a titkosítási műveletet végre kell hajtanunk. |
| Hozzáférési kód | Adjon meg egy erős jelszót adattitkosítási kulcsként. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Az EncryptFormatAll funkció használata adatlemezek esetén Linux IaaS virtuális gépeken
Az EncryptFormatAll paraméter csökkenti a Linux-adatlemezek titkosításának idejét. A megadott feltételeknek megfelelő partíciók formázva vannak (az aktuális fájlrendszerrel együtt). Ezután újra a parancs végrehajtása előtti helyhez lesz újra leválasztva. Ha ki szeretne zárni egy olyan adatlemezt, amely megfelel a feltételeknek, a parancs futtatása előtt leválaszthatja azt.

 A parancs futtatása után a korábban csatlakoztatott összes meghajtó formázva lesz. Ezután a titkosítási réteg a most üres meghajtó tetején kezdődik. Ha ezt a beállítást választja, a virtuális géphez csatolt ideiglenes lemez is titkosítva lesz. Ha a rendszer alaphelyzetbe állítja a Azure Disk Encryption meghajtót, a rendszer újraformálja és újratitkosítsa a virtuális géphez a következő lehetőségnél.

>[!WARNING]
> Az EncryptFormatAll nem használható, ha egy virtuális gép adatkötetén szükség van adatokra. A lemezek leválasztása után kizárhatja a lemezeket a titkosításból. Először próbálja ki az EncryptFormatAll paramétert egy teszt virtuális gépen, hogy megértse a funkcióparamétert és annak implicitását, mielőtt kipróbálja az éles virtuális gépen. Az EncryptFormatAll beállítás úgy formázi az adatlemezt, hogy az összes rajta lévő adat elveszni fog. A folytatás előtt ellenőrizze, hogy a kizárni kívánt lemezek megfelelően vannak-e leválasztva. </br></br>
 >Ha ezt a paramétert a titkosítási beállítások frissítése közben adja meg, az a tényleges titkosítás előtt újraindításhoz vezethet. Ebben az esetben a nem formázni kívánt lemezt is el szeretné távolítani az fstab fájlból. Hasonlóképpen, a titkosítási művelet kezdeményezése előtt hozzá kell adni a titkosítani kívánt partíciót az fstab fájlhoz. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"></a> EncryptFormatAll feltétel
A paraméter végigmegy az összes partíción, és  titkosítja azokat, ha megfelelnek az alábbi feltételeknek: 
- Nem gyökér-/operációsrendszer-/rendszerindítási partíció
- Még nincs titkosítva
- Nem BEK-kötet
- Nem RAID-kötet
- Nem LVM-kötet
- Csatlakoztatva van

A RAID- vagy LVM-kötet helyett a RAID- vagy LVM-kötetet alkotó lemezeket titkosítsa.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"></a> Az EncryptFormatAll paraméter használata sablonnal
Az EncryptFormatAll beállításhoz használjon egy linuxos virtuális gépet titkosító és az AzureDiskEncryption erőforrás **encryptionOperation** mezőjét megíró, már használt Azure Resource Manager-sablont.

1. Példaként használja a Resource Manager [linuxos IaaS](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm)virtuális gép titkosításához. 
2. Az **Azure gyorsindítási sablonban** válassza az Üzembe helyezés az Azure-ban lehetőséget.
3. Módosítsa **az EncryptionOperation** mezőt **EnableEncryption -ről** **EnableEncryptionFormatAl-ra.**
4. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, az egyéb paramétereket, a jogi feltételeket és a szerződést. Válassza **a Létrehozás** lehetőséget a titkosítás engedélyezéséhez a meglévő vagy futó IaaS virtuális gépen.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"></a> Az EncryptFormatAll paraméter használata PowerShell-parancsmaggal
Használja a [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmagot az EncryptFormatAll paraméterrel.

Futó virtuális gép titkosítása titkos ügyfélkulát **és EncryptFormatAll használatával:** A következő szkript például inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension az EncryptFormatAll paraméterrel. Az erőforráscsoport, a virtuális gép, a kulcstartó, az Azure AD-alkalmazás és a titkos ügyfélkulcs már létre lett hozva előfeltételként. Cserélje le a MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID és My-AAD-client-secret értékeket a saját értékeire.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"></a> Az EncryptFormatAll paraméter használata a Logikaikötet-kezelővel (LVM) 
Javasoljuk az LVM on-crypt beállítását. Az alábbi példákban cserélje le az eszközútvonalat és a csatlakozási pontokat az Ön által használt esetnek megfelelőre. Ez a beállítás a következőképpen történik:

- Adja hozzá a virtuális gépet alkotó adatlemezeket.
- Formázza, csatlakoztassa és adja hozzá ezeket a lemezeket az fstab fájlhoz.

    1. Formázza az újonnan hozzáadott lemezt. Itt az Azure által létrehozott symlinkeket használjuk. A symlinkek használatával elkerülhetők az eszköznevekkel kapcsolatos problémák. További információ: [Eszköznevekkel kapcsolatos problémák elhárítása.](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)
    
        ```console
        mkfs -t ext4 /dev/disk/azure/scsi1/lun0
        ```

    2. Csatlakoztassa a lemezeket.

        ```console
        mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint
        ```

    3. Adja hozzá az fstab-hez.

        ```console
        echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab
        ```

    4. Futtassa a Set-AzVMDiskEncryptionExtension PowerShell-parancsmagot az -EncryptFormatAll parancsmaggal a lemezek titkosításához.

       ```azurepowershell-interactive
        Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
        ```

    5. Állítsa be az LVM-et ezeken az új lemezeken. Vegye figyelembe, hogy a titkosított meghajtók zárolása a virtuális gép rendszerindításának befejezése után lesz feloldva. Ezért az LVM-csatlakoztatást is késleltetni kell.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"></a> Ügyfél által titkosított VHD-kből és titkosítási kulcsokból létrehozott új IaaS virtuális gépek
Ebben a forgatókönyvben engedélyezheti a titkosítást a Resource Manager sablon, PowerShell-parancsmagok vagy PARANCSSORi felületi parancsok használatával. A következő szakaszok részletesebben ismertetik a sablon Resource Manager CLI-parancsokat. 

Az Azure-ban használható, előre titkosított rendszerképek előkészítéséhez kövesse a függelékben található utasításokat. A rendszerkép létrehozása után a következő szakaszban található lépésekkel létrehozhat egy titkosított Azure-beli virtuális gépet.

* [Előre titkosított Linux virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >A felügyelt lemezen alapuló virtuálisgép-példányról a virtuális gépek engedélyezése előtt és kívül is kötelező pillanatképet készíteni, vagy biztonsági Azure Disk Encryption. A felügyelt lemezről pillanatképet készíthet a portálról, vagy használhatja a [Azure Backup.](../../backup/backup-azure-vms-encryption.md) A biztonsági másolatok biztosítják, hogy a titkosítás során váratlan hiba esetén lehetséges legyen a helyreállítási beállítás. A biztonsági mentés után a Set-AzVMDiskEncryptionExtension -skipVmBackup paraméter megadásával titkosítsa a felügyelt lemezeket. A Set-AzVMDiskEncryptionExtension parancs meghiúsul a felügyelt lemezalapú virtuális gépeken, amíg biztonsági mentés nem készül, és meg nem adja ezt a paramétert. 
>
>A titkosítás titkosítása vagy letiltása a virtuális gép újraindítását okozhatja.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"></a> Az Azure PowerShell IaaS virtuális gépek titkosítása előtitkos VHD-ekkel 
A lemeztitkosítást a titkosított VHD-n a [Set-AzVMOSDisk PowerShell-parancsmag](/powershell/module/az.compute/set-azvmosdisk#examples)használatával engedélyezheti. Az alábbi példa néhány gyakori paramétert mutat be. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Titkosítás engedélyezése újonnan hozzáadott adatlemezen
Új adatlemezt az [az vm disk attach](add-disk.md) vagy az Azure Portal. [](attach-disk-portal.md) A titkosítás előtt először csatlakoztatnia kell az újonnan csatlakoztatott adatlemezt. Az adat meghajtótitkosítást kell kérnie, mert a meghajtó nem lesz használható, amíg a titkosítás folyamatban van. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Titkosítás engedélyezése újonnan hozzáadott lemezen az Azure CLI használatával
 Ha a virtuális gépet korábban "All" titkosítással titkosították, akkor a --volume-type paraméternek az All (Mind) paraméternek kell maradnia. Mindegyik magában foglalja az operációs rendszert és az adatlemezeket is. Ha a virtuális gépet korábban "OS" kötettípussal titkosították, akkor a --volume-type paramétert All (Mind) paraméterre kell módosítani, hogy az operációs rendszer és az új adatlemez is benne legyen. Ha a virtuális gép csak az "Adatok" kötettípussal lett titkosítva, akkor az adatok maradhatnak az itt szemléltetettek szerint. Új adatlemez hozzáadása és csatlakoztatása egy virtuális géphez nem elegendő a titkosítás előkészítéséhez. A titkosítás engedélyezése előtt az újonnan csatlakoztatott lemezt is formázni és megfelelően csatlakoztatni kell a virtuális géphez. Linux rendszeren a lemezt az /etc/fstab fájlhoz kell csatlakoztatni egy állandó [blokkeszköznévvel.](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems) 

A PowerShell-szintaxissal ellentétben a parancssori felület nem követeli meg, hogy egyedi sorozatverziót adjon meg a titkosítás engedélyezésekor. A CLI automatikusan létrehozza és felhasználja a saját egyedi sorozatverzió-értékét.

-  **Futó virtuális gép titkosítása titkos ügyféltitk használatával:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Titkosítsa a futó virtuális gépeket a KEK használatával az ügyfél titkos kulcsának becsomagolása során:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Engedélyezze a titkosítást egy újonnan hozzáadott lemezen Azure PowerShell
 Ha a PowerShell használatával titkosít egy új lemezt Linux rendszeren, meg kell adni egy új sorozatverziót. A sorozatverziónak egyedinek kell lennie. Az alábbi szkript létrehoz egy GUID azonosítót a sorozatverzióhoz. 
 

-  **Futó virtuális gép titkosítása titkos ügyféltitk használatával:** A következő szkript inicializálja a változókat, és futtatja Set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép, a kulcstartó, az Azure AD-alkalmazás és a titkos ügyfélkulcs már létre lett hozva előfeltételként. Cserélje le a MyVirtualMachineResourceGroup, a MyKeyVaultResourceGroup, a MySecureVM, a MySecureVault, a My-AAD-client-ID és a My-AAD-client-secret értékeket a saját értékeire. A -VolumeType paraméter adatlemezre van beállítva, nem az operációsrendszer-lemezre. Ha a virtuális gépet korábban "OS" vagy "All" kötettípussal titkosították, akkor a -VolumeType paramétert Az összesre kell módosítani, hogy az operációs rendszer és az új adatlemez is benne legyen.

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
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **Titkosítsa a futó virtuális gépeket a KEK használatával az ügyfél titkos kódba burkolva:** Azure Disk Encryption lehetővé teszi egy meglévő kulcs megadását a kulcstartóban a titkosítás engedélyezése során létrehozott lemeztitkosítási titkos kulcsok becsomagolása érdekében. Ha kulcstitkosítási kulcs van megadva, a Azure Disk Encryption a kulcs használatával becsomagolja a titkosítási titkos kódokat, mielőtt a kulcstartóba ír. A -VolumeType paraméter adatlemezre van beállítva, nem az operációsrendszer-lemezre. Ha a virtuális gépet korábban "OS" vagy "All" kötettípussal titkosították, akkor a -VolumeType paramétert All (Mind) paraméterre kell módosítani, hogy az operációs rendszer és az új adatlemez is bekerül a fájlba.

     ```azurepowershell
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
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> A disk-encryption-keyvault paraméter értékének szintaxisa a teljes azonosító sztring: /subscriptions/[előfizetés-azonosító-guid]/resourceGroups/[erőforráscsoport-neve]/providers/Microsoft.KeyVault/vaults/[keyvault-name]. </br> </br>
A key-encryption-key paraméter értékének szintaxisa a KEK teljes URI-ját használja a következő módon: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Linux rendszerű virtuális gépek titkosításának letiltása
A titkosítás letiltásához használhatja a Azure PowerShell, az Azure CLI-t vagy egy Resource Manager sablont. 

>[!IMPORTANT]
>A linuxos virtuális gépeken Azure Disk Encryption titkosítás letiltása csak adatkötetek esetén támogatott. Az adat- vagy operációsrendszer-kötetek nem támogatják, ha az operációs rendszer kötete titkosított. 

- **Lemeztitkosítás letiltása a Azure PowerShell:** A titkosítás letiltásához használja a [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Titkosítás letiltása az Azure CLI-val:** A titkosítás letiltásához használja [az az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) parancsot. 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Tiltsa le a titkosítást Resource Manager sablonnal:** A titkosítás letiltásához használja [a Titkosítás letiltása futó Linux](https://aka.ms/decrypt-linuxvm) rendszerű virtuális gépen sablont.
     1. Válassza **az Üzembe helyezés az Azure-ban lehetőséget.**
     2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a virtuális gépet, a jogi feltételeket és a szerződést.
     3. Válassza **a Vásárlás lehetőséget** a lemeztitkosítás letiltásához egy futó Windows rendszerű virtuális gépen. 


## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption Linuxhoz – áttekintés](disk-encryption-overview-aad.md)
- [Kulcstartó létrehozása és konfigurálása Azure Disk Encryption Azure AD-val (korábbi kiadás)](disk-encryption-key-vault-aad.md)