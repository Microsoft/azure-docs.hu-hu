---
title: Azure Disk Encryption – mintaszkriptek
description: Ez a cikk a Linux rendszerű virtuális gépek Microsoft Azure lemezes titkosításának függeléke.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: f11677d9ebc31f1c1f7cc6332b07b69f8e35ad52
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561177"
---
# <a name="azure-disk-encryption-sample-scripts-for-linux-vms"></a>A Linux rendszerű virtuális gépekre Azure Disk Encryption minta szkriptek

Ez a cikk példákat tartalmaz az előre titkosított virtuális merevlemezek és egyéb feladatok előkészítéséhez.  

> [!NOTE]
> Minden parancsfájl az ADE legújabb, nem HRE verziójára hivatkozik, kivéve a következőt:.

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>PowerShell-parancsfájlok Azure Disk Encryptionhoz 

- **Az előfizetésben található összes titkosított virtuális gép listázása**
  
  Az összes ADE-titkosítású virtuális gép és a bővítmény verziója megtalálható az előfizetésben található összes erőforráscsoport számára ezen a [PowerShell-parancsfájl](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VM.ps1)használatával.

  Másik lehetőségként ezek a parancsmagok az összes ADE-titkosítású virtuális gépet megjelenítik (a bővítmény verziószáma nem):

   ```azurepowershell-interactive
   $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
   $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
   Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
   ```

- **Az előfizetéshez tartozó összes titkosított VMSS-példány listázása**
    
    Az összes ADE-titkosítású VMSS-példányt és a bővítmény verzióját a jelen [PowerShell-parancsfájl](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VMSS.ps1)használatával az előfizetésben található összes erőforráscsoporthoz megtalálhatja.

- **A Key vaultban lévő virtuális gépek titkosításához használt összes lemez-titkosítási titok listázása** 

   ```azurepowershell-interactive
   Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
   ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Az Azure Disk Encryption előfeltételek PowerShell-parancsfájl használata
Ha már ismeri a Azure Disk Encryption előfeltételeit, használhatja az [Azure Disk Encryption előfeltételek PowerShell-szkriptet](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). A PowerShell-szkriptek használatára példát a [virtuális gépek titkosítása](disk-encryption-powershell-quickstart.md)– gyors útmutató című témakörben talál. A parancsfájl egy szakaszának megjegyzéseit eltávolíthatja a meglévő erőforráscsoporthoz tartozó meglévő virtuális gépek összes lemezének titkosításához a 211. sorban kezdődően. 

A következő táblázat a PowerShell-parancsfájlban használható paramétereket mutatja be: 


|Paraméter|Leírás|Kötelező?|
|------|------|------|
|$resourceGroupName| Azon erőforráscsoport neve, amelyhez a kulcstartó tartozik.  Ha az egyik nem létezik, akkor létrejön egy ilyen nevű új erőforráscsoport.| Igaz|
|$keyVaultName|Annak a kulcstartónak a neve, amelyben a titkosítási kulcsokat el kell helyezni. Ha az egyik nem létezik, akkor a rendszer létrehoz egy új tárat ezzel a névvel.| Igaz|
|$location|A kulcstartó helye. Győződjön meg arról, hogy a kulcstároló és a virtuális gépek titkosítva vannak ugyanazon a helyen. A helyek listáját a következővel érheti el: `Get-AzLocation`.|Igaz|
|$subscriptionId|A használni kívánt Azure-előfizetés azonosítója.  Az előfizetés-azonosítóját a következővel érheti el: `Get-AzSubscription`.|Igaz|
|$aadAppName|Annak az Azure AD-alkalmazásnak a neve, amelyet a rendszer a kulcstartók írásához fog használni. Ha a megadott néven még nem létezik alkalmazás, a rendszer létrehoz egyet a beírt néven. Ha az alkalmazás már létezik, adja át a aadClientSecret paramétert a parancsfájlnak.|Hamis|
|$aadClientSecret|A korábban létrehozott Azure AD-alkalmazás ügyfél-titka.|Hamis|
|$keyEncryptionKeyName|A kulcstartóban nem kötelező kulcs-titkosítási kulcs neve. Ha az egyik nem létezik, a rendszer létrehoz egy új kulcsot a névvel.|Hamis|

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Virtuális gépek titkosítása vagy visszafejtése Azure AD-alkalmazás nélkül

- [A lemez titkosításának engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Titkosítás letiltása futó Linux rendszerű virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - A titkosítás letiltása csak a Linux rendszerű virtuális gépek adatkötetein engedélyezett.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Virtuális gépek titkosítása vagy visszafejtése Azure AD-alkalmazással (korábbi kiadás)
 
- [A lemez titkosításának engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Titkosítás letiltása futó Linux rendszerű virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - A titkosítás letiltása csak a Linux rendszerű virtuális gépek adatkötetein engedélyezett. 


- [Új titkosított felügyelt lemez létrehozása egy előre titkosított VHD/Storage-blobból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Létrehoz egy új titkosított felügyelt lemezt, amely egy előre titkosított VHD-t és a hozzá tartozó titkosítási beállításokat biztosított.

## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>OPERÁCIÓSRENDSZER-meghajtó titkosítása futó Linux rendszerű virtuális gépen

### <a name="prerequisites-for-os-disk-encryption"></a>Az operációsrendszer-lemez titkosításának előfeltételei

* A virtuális gépnek az operációsrendszer-lemez titkosításával kompatibilis disztribúciót kell használnia a [Azure Disk Encryption támogatott operációs rendszerek](disk-encryption-overview.md#supported-vms) listájában. 
* A virtuális gépet a piactér rendszerképből kell létrehozni Azure Resource Manager.
* Legalább 4 GB RAM memóriával rendelkező Azure-beli virtuális gép (az ajánlott méret 7 GB).
* (RHEL és CentOS esetén) SELinux letiltása. A SELinux letiltásához tekintse meg a "4.4.2. A SELinux letiltása a [SELinux felhasználói és rendszergazdai útmutatójában](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) a virtuális gépen.
* A SELinux letiltását követően legalább egyszer indítsa újra a virtuális gépet.

### <a name="steps"></a>Lépések
1. Hozzon létre egy virtuális gépet a korábban megadott eloszlások egyikének használatával.

   A CentOS 7,2 esetében az operációs rendszer lemezének titkosítása egy speciális rendszerkép használatával támogatott. A rendszerkép használatához a virtuális gép létrehozásakor a "7.2 n" kifejezést kell megadni SKU-ként:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Konfigurálja a virtuális gépet az igényeinek megfelelően. Ha az összes (OS + összes) meghajtót titkosítani fogja, az adatmeghajtókat meg kell adni és csatlakoztatni kell a/etc/fstab.

   > [!NOTE]
   > Az UUID =... az adatmeghajtók az/etc/fstab-ben való megadásához a blokk eszköz nevének megadása helyett (például/dev/sdb1). A titkosítás során a meghajtók megváltoznak a virtuális gépen. Ha a virtuális gép a blokkoló eszközök meghatározott sorrendjére támaszkodik, a titkosítás után nem fogja tudni csatlakoztatni azokat.

3. Jelentkezzen ki az SSH-munkamenetből.

4. Az operációs rendszer titkosításához a titkosítás engedélyezésekor az **összes** vagy az **operációs rendszer** volumeType kell megadni.

   > [!NOTE]
   > A szolgáltatásként nem futtató összes felhasználói terület folyamatát le `systemd` kell ölni `SIGKILL` . Indítsa újra a virtuális gépet. Ha egy futó virtuális gépen engedélyezi az operációsrendszer-lemez titkosítását, tervezze meg a virtuális gépek leállását.

5. A [következő szakaszban](#monitoring-os-encryption-progress)található utasítások alapján rendszeres időközönként figyelje a titkosítás előrehaladását.

6. Miután Get-AzVmDiskEncryptionStatus megjeleníti a "VMRestartPending" kifejezést, indítsa újra a virtuális gépet úgy, hogy bejelentkezik rá, vagy a portál, a PowerShell vagy a parancssori felület használatával.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   A rendszer újraindítása előtt azt javasoljuk, hogy mentse a virtuális gép [rendszerindítási diagnosztikát](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) .

## <a name="monitoring-os-encryption-progress"></a>Operációs rendszer titkosítási folyamatának figyelése
Az operációs rendszer titkosítási folyamatát háromféle módon követheti nyomon:

* Használja a `Get-AzVmDiskEncryptionStatus` parancsmagot, és vizsgálja meg a feladatnézetben mezőt:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Miután a virtuális gép eléri az "operációsrendszer-lemez titkosítását", a Premium Storage-beli virtuális gépen körülbelül 40 – 50 percet vesz igénybe.

  A [probléma #388](https://github.com/Azure/WALinuxAgent/issues/388) a WALinuxAgent-ben, `OsVolumeEncrypted` és az `DataVolumesEncrypted` `Unknown` egyes disztribúciókban is megjelenik. A WALinuxAgent 2.1.5-es és újabb verzióiban ez a probléma automatikusan rögzített. Ha `Unknown` a kimenetben látható, akkor a Azure erőforrás-kezelő használatával ellenőrizheti a lemez titkosításának állapotát.

  Nyissa meg a [Azure erőforrás-kezelő](https://resources.azure.com/), majd bontsa ki ezt a hierarchiát a bal oldali kiválasztási panelen:

  ```
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ```                

  A InstanceView görgessen lefelé a meghajtók titkosítási állapotának megtekintéséhez.

  ![Virtuálisgép-példány nézet](./media/disk-encryption/vm-instanceview.png)

* Tekintse meg a [rendszerindítási diagnosztikát](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Az ADE kiterjesztésből származó üzeneteket előre fel kell oldani `[AzureDiskEncryption]` .

* Jelentkezzen be a virtuális gépre SSH-n keresztül, és szerezze be a bővítmény naplóját:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Javasoljuk, hogy ne jelentkezzen be a virtuális gépre, amíg az operációs rendszer titkosítása folyamatban van. A naplók másolása csak akkor, ha a másik két módszer meghiúsult.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Előre titkosított linuxos virtuális merevlemez előkészítése
Az előre titkosított virtuális merevlemezek előkészítése a terjesztéstől függően változhat. Az Ubuntu 16, az openSUSE 13,2 és a CentOS 7 előkészítésének példái érhetők el. 

### <a name="ubuntu-16"></a>Ubuntu 16
Az alábbi lépéseket követve konfigurálja a titkosítást a terjesztés telepítése során:

1. Válassza a **titkosított kötetek konfigurálása** a lemezek particionálásakor lehetőséget.

   ![Ubuntu 16,04 telepítő – titkosított kötetek konfigurálása](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Hozzon létre egy különálló rendszerindító meghajtót, amely nem titkosítható. Titkosítsa a legfelső szintű meghajtót.

   ![Ubuntu 16,04 telepítő – a titkosítani kívánt eszközök kiválasztása](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Adjon meg egy jelszót. Ez az a jelszó, amelyet a Key vaultba töltött fel.

   ![Ubuntu 16,04 telepítő – hozzáférési kód megadása](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Particionálás befejezése.

   ![Ubuntu 16,04 telepítő – particionálás befejezése](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Amikor elindítja a virtuális gépet, és a rendszer jelszót kér, használja a 3. lépésben megadott jelszót.

   ![Ubuntu 16,04 telepítő – jelszó megadása rendszerindításkor](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Készítse elő a virtuális gépet az Azure-ba való feltöltéshez [ezen utasítások](./create-upload-ubuntu.md?toc=/azure/virtual-machines/linux/toc.json)használatával. Ne futtassa az utolsó lépést (a virtuális gép kiépítése).

Az alábbi lépések végrehajtásával konfigurálhatja a titkosítást az Azure-nal való együttműködéshez:

1. Hozzon létre egy fájlt a/usr/local/sbin/azure_crypt_key. sh fájlban a következő parancsfájl tartalmával. Ügyeljen arra, hogy a jelszót az Azure által használt jelszó-fájlnév adja meg.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. Módosítsa a */etc/crypttab* található Crypt-konfigurációt. Ennek így kell kinéznie:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Végrehajtható engedélyek hozzáadása a parancsfájlhoz:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. */Etc/initramfs-tools/modules* szerkesztése sorok hozzáfűzésével:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. A futtatásával `update-initramfs -u -k all` frissítse a initramfs, hogy az `keyscript` érvénybe lépne.

7. Most már kiépítheti a virtuális gépet.

   ![Ubuntu 16,04 telepítő-Update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Folytassa a következő lépéssel, és töltse fel a VHD-t az Azure-ba.

### <a name="opensuse-132"></a>openSUSE 13,2
A titkosítás konfigurálásához végezze el a következő lépéseket:
1. A lemezek particionálásakor válassza a **kötet csoport titkosítása** lehetőséget, majd adja meg a jelszót. Ez a jelszó, amelyet fel fog tölteni a kulcstartóba.

   ![openSUSE 13,2 telepítő – kötet csoportjának titkosítása](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Indítsa el a virtuális gépet a jelszavával.

   ![openSUSE 13,2 telepítő – jelszó megadása rendszerindításkor](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Készítse elő a virtuális GÉPET az Azure-ba való feltöltéshez a [SLES-vagy openSUSE-alapú virtuális gép Azure-ban történő előkészítésének](./suse-create-upload-vhd.md?toc=/azure/virtual-machines/linux/toc.json#prepare-opensuse-131)utasításait követve. Ne futtassa az utolsó lépést (a virtuális gép kiépítése).

A titkosítás az Azure-ban való működésének konfigurálásához hajtsa végre a következő lépéseket:
1. Szerkessze a/etc/Dracut.conf, és adja hozzá a következő sort:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Tegye megjegyzésbe ezeket a sorokat a fájl/usr/lib/Dracut/modules.d/90crypt/Module-Setup.sh végéig:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Fűzze hozzá a következő sort a/usr/lib/Dracut/modules.d/90crypt/parse-crypt.sh fájl elejéhez:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   És az összes előfordulásának módosítása:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   erre:
   ```bash
    if [ 1 ]; then
   ```
4. Szerkessze a/usr/lib/Dracut/modules.d/90crypt/cryptroot-Ask.sh, és fűzze hozzá a "# Open LUKS Device":

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Futtassa `/usr/sbin/dracut -f -v` a parancsot a initrd frissítéséhez.

6. Most kiépítheti a virtuális gépet, és feltöltheti a VHD-t az Azure-ba.

### <a name="centos-7-and-rhel-7"></a>CentOS 7 és RHEL 7

A titkosítás konfigurálásához végezze el a következő lépéseket:
1. Válassza **a saját adatai titkosítása a** lemezek particionálásakor lehetőséget.

   ![CentOS 7 telepítő – telepítési cél](./media/disk-encryption/centos-encrypt-fig1.png)

2. Győződjön meg arról, hogy a gyökérszintű partícióhoz a **titkosítás** van kiválasztva.

   ![CentOS 7 telepítő – a gyökérszintű partíció titkosításának kiválasztása](./media/disk-encryption/centos-encrypt-fig2.png)

3. Adjon meg egy jelszót. Ez az a jelszó, amelyet fel fog tölteni a kulcstartóba.

   ![CentOS 7 telepítő – hozzáférési kód megadása](./media/disk-encryption/centos-encrypt-fig3.png)

4. Amikor elindítja a virtuális gépet, és a rendszer jelszót kér, használja a 3. lépésben megadott jelszót.

   ![CentOS 7 telepítő – jelszó megadása a csomagtartóban](./media/disk-encryption/centos-encrypt-fig4.png)

5. Készítse elő a virtuális gépet az Azure-ba való feltöltéshez az [Azure-hoz készült CentOS-alapú virtuális gép előkészítése](./create-upload-centos.md?toc=/azure/virtual-machines/linux/toc.json#centos-70)című témakör "CentOS 7.0 +" utasításával. Ne futtassa az utolsó lépést (a virtuális gép kiépítése).

6. Most kiépítheti a virtuális gépet, és feltöltheti a VHD-t az Azure-ba.

A titkosítás az Azure-ban való működésének konfigurálásához hajtsa végre a következő lépéseket:

1. Szerkessze a/etc/Dracut.conf, és adja hozzá a következő sort:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Tegye megjegyzésbe ezeket a sorokat a fájl/usr/lib/Dracut/modules.d/90crypt/Module-Setup.sh végéig:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Fűzze hozzá a következő sort a/usr/lib/Dracut/modules.d/90crypt/parse-crypt.sh fájl elejéhez:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   És az összes előfordulásának módosítása:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   felhasználóként a(z)
   ```bash
    if [ 1 ]; then
   ```
4. Szerkessze a/usr/lib/Dracut/modules.d/90crypt/cryptroot-Ask.sh, és fűzze hozzá a következőt a "# Open LUKS-eszköz" után:
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Futtassa a "/usr/sbin/Dracut-f-v" parancsot a initrd frissítéséhez.

    ![CentOS 7 telepítő – a/usr/sbin/Dracut-f-v futtatása](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Titkosított virtuális merevlemez feltöltése Azure Storage-fiókba
DM-Crypt titkosítás engedélyezése után a helyi titkosított virtuális merevlemezt fel kell tölteni a Storage-fiókjába.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Töltse fel az előre titkosított virtuális gép titkos kulcsát a kulcstartóba
Ha egy Azure AD-alkalmazás (előző kiadás) használatával titkosít, a korábban beszerzett lemez-titkosítási titkot titkos kulcsként kell feltölteni a kulcstartóba. A Key vaultnak rendelkeznie kell a lemez titkosításával és az Azure AD-ügyfélhez engedélyezett engedélyekkel.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>A lemez titkosítási titka nem titkosított KEK-sel
A titkos kulcs a Key vaultban történő beállításához használja a [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A jelszó Base64 karakterláncként van kódolva, majd feltöltve a kulcstartóba. Továbbá győződjön meg arról, hogy a következő címkék vannak beállítva, amikor létrehozza a titkos kulcsot a kulcstartóban.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Használja a `$secretUrl` következő lépésben az operációsrendszer- [lemez csatlakoztatását a KEK használata nélkül](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>A lemez titkosítási titka egy KEK-sel titkosítva
Mielőtt feltölti a titkos kulcsot a kulcstartóba, lehetősége van arra, hogy titkosítsa azt egy kulcs titkosítási kulcs használatával. A wrap [API](/rest/api/keyvault/wrapkey) használatával először Titkosítsa a titkos kulcsot a kulcs titkosítási kulcsával. Ennek a körbefuttatási műveletnek a kimenete Base64 URL-kódolású karakterlánc, amelyet ezután titkos kulcsként tölthet fel a [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) parancsmag használatával.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

A `$KeyEncryptionKey` `$secretUrl` következő lépésben használja az operációsrendszer- [lemez csatlakoztatását a KEK használatával](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Titkos URL-cím megadása operációsrendszer-lemez csatlakoztatásakor

###  <a name="without-using-a-kek"></a>KEK használata nélkül
Az operációsrendszer-lemez csatlakoztatása közben át kell adnia a következőt: `$secretUrl` . Az URL-cím a "lemez-titkosítási titok, amely nem titkosított KEK-sel" című szakaszban lett létrehozva.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>KEK használata
Az operációsrendszer-lemez csatlakoztatásakor adja meg a és a kapcsolót `$KeyEncryptionKey` `$secretUrl` . Az URL-címet a "lemez titkosítási titka egy KEK-lel titkosítva" szakasz hozta létre.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
