---
title: Azure Files használata Linux rendszeren | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathat egy Azure-fájlmegosztást az SMB protokollon keresztül a Linuxon. Tekintse meg az előfeltételek listáját. Tekintse át az SMB biztonsági szempontjait Linux-ügyfeleken.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 5161d8e169a7eb9e757dfbfa71fa697880e1806e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673687"
---
# <a name="use-azure-files-with-linux"></a>Az Azure Files használata Linux rendszerrel
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztás az [SMB kernel-ügyféllel](https://wiki.samba.org/index.php/LinuxCIFS)is csatlakoztatható Linux-disztribúciókban. Ebből a cikkből megtudhatja, hogyan csatlakoztathat egy Azure-fájlmegosztást: igény szerint a `mount` paranccsal és a rendszerindítással, ha létrehoz egy bejegyzést a alkalmazásban `/etc/fstab` .

Az Azure-fájlmegosztás Linux rendszeren való csatlakoztatásának ajánlott módja az SMB 3,0 használata. Alapértelmezés szerint a Azure Files az átvitel során titkosítást igényel, amelyet csak az SMB 3,0 támogat. A Azure Files támogatja az SMB 2,1-et is, amely nem támogatja az átvitel közbeni titkosítást, de biztonsági okokból nem csatlakoztathatja az Azure-fájlmegosztást egy másik Azure-régióból vagy helyszíni SMB 2,1-ből. Ha az alkalmazása kifejezetten az SMB 2,1-t igényli, kevés az oka annak, hogy a legnépszerűbb, legutóbb kiadott Linux-disztribúciók az SMB 3,0 támogatását használják:  

| Linux-disztribúció | SMB 2.1 <br>(Ugyanazon az Azure-régióban lévő virtuális gépekre csatlakoztatások) | SMB 3.0 <br>(A helyszíni és a régiók közötti) |
| --- | :---: | :---: |
| Ubuntu | 14.04 + | 16.04 + |
| Red Hat Enterprise Linux (RHEL) | 7 + | 7.5 + |
| CentOS | 7 + |  7.5 + |
| Debian | 8 + | 10+ |
| openSUSE | 13.2 + | 42.3 + |
| SUSE Linux Enterprise Server | 12+ | 12 SP2 + |

Ha a fenti táblázatban nem szereplő Linux-disztribúciót használ, ellenőrizheti, hogy a Linux-disztribúció támogatja-e az SMB 3,0-et a titkosítással a Linux kernel verziójának ellenőrzésével. Az SMB 3,0 titkosítással lett hozzáadva a Linux kernel 4,11-es verziójához. A `uname` parancs a használatban lévő Linux kernel verzióját fogja visszaadni:

```bash
uname -r
```

## <a name="prerequisites"></a>Előfeltételek
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Győződjön meg arról, hogy a CIFS-utils csomag telepítve van.**  
    A CIFS-utils csomag a kiválasztott Linux-disztribúcióban telepíthető a Package Manager használatával. 

    **Ubuntu** -és **Debian-alapú** disztribúciók esetén használja a `apt` Package Managert:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    **Fedora**, **Red Hat Enterprise Linux 8 +** és **CentOS 8 +** esetén használja a `dnf` Package Managert:

    ```bash
    sudo dnf install cifs-utils
    ```

    A **Red Hat Enterprise Linux** és a **CentOS** régebbi verzióiban használja a `yum` Package Managert:

    ```bash
    sudo yum install cifs-utils 
    ```

    Az **openSUSE**-ben használja a `zypper` csomagkezelő eszközt:

    ```bash
    sudo zypper install cifs-utils
    ```

    Más disztribúciók esetén használja a megfelelő csomagkezelő vagy [fordítás forrásból elemet](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download) .

* **Az Azure parancssori felület (CLI) legújabb verziója.** Az Azure CLI telepítésével kapcsolatos további információkért lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és az operációs rendszer kiválasztása. Ha inkább a Azure PowerShell modult szeretné használni a PowerShell 6 +-ban, akkor az alábbi utasításokat azonban az Azure CLI-hez mutatjuk be.

* **Győződjön meg arról**, hogy a 445-es port nyitva van: az SMB a 445-es TCP-porton keresztül kommunikál, és ellenőrizze, hogy a tűzfal nem blokkolja-e az ügyfélszámítógép TCP-445 portjait.  Cserélje le `<your-resource-group>` , `<your-storage-account>` majd futtassa a következő parancsfájlt:
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Ha a kapcsolódás sikeres volt, a következő kimenethez hasonlónak kell megjelennie:

    ```ouput
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Ha nem tudja megnyitni a 445-as portot a vállalati hálózaton, vagy ha az INTERNETSZOLGÁLTATÓ ezt letiltja, akkor VPN-kapcsolattal vagy ExpressRoute kell használnia a 445-es porton való működéshez. További információ: [hálózati megfontolások az Azure-fájlmegosztás közvetlen eléréséhez](storage-files-networking-overview.md).

## <a name="mounting-azure-file-share"></a>Azure-fájlmegosztás csatlakoztatása
Ha egy Azure-fájlmegosztást Linux-disztribúcióval szeretne használni, létre kell hoznia egy könyvtárat, amely az Azure-fájlmegosztás csatlakoztatási pontként szolgál. Egy csatlakoztatási pont bárhol létrehozható a Linux rendszeren, de ez a közös konvenció a/mnt. alatt történő létrehozásához. A csatlakoztatási pont után a parancs használatával `mount` férhet hozzá az Azure-fájlmegosztás eléréséhez.

Szükség esetén egyszerre több csatlakoztatási pontra is csatlakoztathatja az Azure-fájlmegosztást.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Igény szerinti Azure-fájlmegosztás csatlakoztatása a következővel: `mount`
1. **Hozzon létre egy mappát a csatlakoztatási ponthoz**: cserélje le `<your-resource-group>` , `<your-storage-account>` és a `<your-file-share>` megfelelő információkkal a környezetéhez:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. Az **Azure-fájlmegosztás csatlakoztatásához használja a mount parancsot**. Az alábbi példában a helyi Linux-fájl és a mappa engedélyei alapértelmezett 0755, ami azt jelenti, hogy a tulajdonos (fájl/könyvtár Linux-tulajdonos) olvasási, írási és végrehajtási jogosultsággal rendelkezik, olvasás és végrehajtás a tulajdonos csoportban lévő felhasználók számára, valamint olvasás és végrehajtás mások számára a rendszeren. A `uid` és a `gid` csatlakoztatási lehetőségekkel beállíthatja a csatlakoztatás felhasználói azonosítóját és a csoport azonosítóját. `dir_mode`A és a használatával is igény `file_mode` szerint állíthatja be az egyéni engedélyeket. További információ az engedélyek beállításáról: [UNIX numerikus jelölés](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) a wikipedia-ben. 

    ```bash
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > A fenti csatlakoztatási parancs az SMB 3,0-vel együtt csatlakoztatható. Ha a Linux-disztribúció nem támogatja az SMB 3,0 titkosítást, vagy ha az csak az SMB 2,1-et támogatja, akkor csak a Storage-fiókkal azonos régióban lévő Azure-beli virtuális gépekről lehet csatlakoztatni. Ha az Azure-fájlmegosztást olyan Linux-disztribúción szeretné csatlakoztatni, amely nem támogatja az SMB 3,0 titkosítást, [le kell tiltania a Storage-fiók titkosítását](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Ha elkészült az Azure-fájlmegosztás használatával, `sudo umount $mntPath` a használatával leválaszthatja a megosztást.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Állandó csatlakozási pont létrehozása az Azure-fájlmegosztás számára a következővel: `/etc/fstab`
1. **Hozzon létre egy mappát a csatlakoztatási ponthoz**: egy csatlakozási ponthoz tartozó mappa bárhol létrehozható a fájlrendszerben, de ez a közös konvenció a/mnt. alatt történő létrehozásához. A következő parancs például létrehoz egy új könyvtárat, lecseréli `<your-resource-group>` , `<your-storage-account>` és a `<your-file-share>` megfelelő információkat a környezetéhez:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Hozzon létre egy hitelesítőadat-fájlt a fájlmegosztás felhasználónevének (a Storage-fiók neve) és a jelszó (a Storage-fiók kulcsa) tárolásához.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Módosítsa a hitelesítő fájl engedélyeit úgy, hogy csak a root tudja olvasni vagy módosítani a jelszót tartalmazó fájlt.** Mivel a Storage-fiók kulcsa lényegében egy felügyelői jelszó a Storage-fiókhoz, a fájl engedélyeinek megadásával, hogy csak a gyökér férhet hozzá, fontos, hogy az alacsonyabb jogosultságú felhasználók ne tudják beolvasni a Storage-fiók kulcsát.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. A **következő parancs használatával fűzze hozzá a következő sort `/etc/fstab`** az alkalmazáshoz: az alábbi példában a helyi Linux-fájl és a mappa engedélyei alapértelmezett 0755, ami azt jelenti, hogy olvasás, írás és végrehajtás a tulajdonos számára (a fájl/könyvtár Linux tulajdonosa alapján), olvasás és végrehajtás a tulajdonos csoportba tartozó felhasználók számára, valamint olvasás és végrehajtás mások számára a rendszeren. A `uid` és a `gid` csatlakoztatási lehetőségekkel beállíthatja a csatlakoztatás felhasználói azonosítóját és a csoport azonosítóját. `dir_mode`A és a használatával is igény `file_mode` szerint állíthatja be az egyéni engedélyeket. További információ az engedélyek beállításáról: [UNIX numerikus jelölés](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) a wikipedia-ben.

    ```bash
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > A fenti csatlakoztatási parancs az SMB 3,0-vel együtt csatlakoztatható. Ha a Linux-disztribúció nem támogatja az SMB 3,0 titkosítást, vagy ha az csak az SMB 2,1-et támogatja, akkor csak a Storage-fiókkal azonos régióban lévő Azure-beli virtuális gépekről lehet csatlakoztatni. Ha az Azure-fájlmegosztást olyan Linux-disztribúción szeretné csatlakoztatni, amely nem támogatja az SMB 3,0 titkosítást, [le kell tiltania a Storage-fiók titkosítását](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>Az Azure-fájlmegosztás (ok) automatikus csatlakoztatása a AutoFS használatával

1. **Győződjön meg arról, hogy a AutoFS-csomag telepítve van.**  

    A AutoFS csomag a Package Manager használatával telepíthető a Linux-disztribúcióban. 

    **Ubuntu** -és **Debian-alapú** disztribúciók esetén használja a `apt` Package Managert:
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    **Fedora**, **Red Hat Enterprise Linux 8 +** és **CentOS 8 +** esetén használja a `dnf` Package Managert:
    ```bash
    sudo dnf install autofs
    ```
    A **Red Hat Enterprise Linux** és a **CentOS** régebbi verzióiban használja a `yum` Package Managert:
    ```bash
    sudo yum install autofs 
    ```
    Az **openSUSE**-ben használja a `zypper` csomagkezelő eszközt:
    ```bash
    sudo zypper install autofs
    ```
2. **Csatlakozási pont létrehozása a megosztás (ok) hoz**:
   ```bash
    sudo mkdir /fileshares
    ```
3. **Kréta – új egyéni AutoFS konfigurációs fájl**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **Adja hozzá a következő bejegyzéseket a/etc/Auto.fileshares**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **Adja hozzá a következő bejegyzést a/etc/Auto.Master**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **AutoFS újraindítása**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **Hozzáférés a megosztáshoz kijelölt mappához**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>A Linux biztonságossá tétele
Az Azure-fájlmegosztás Linux rendszeren való csatlakoztatásához a 445-es portnak elérhetőnek kell lennie. Számos szervezet blokkolja a 445-ös portot az SMB 1 eredendő biztonsági kockázatai miatt. Az SMB 1, más néven CIFS (Common Internet File System) egy örökölt fájlrendszeri protokoll, amely számos Linux-disztribúciót tartalmaz. Az SMB-1 egy elavult, nem hatékony és legfőképpen nem biztonságos protokoll. A jó hír az, hogy Azure Files nem támogatja az SMB 1 használatát, és a Linux kernel 4,18-es verziójától kezdődően a Linux lehetővé teszi az SMB 1 letiltását. Mindig [erősen ajánlott](https://aka.ms/stopusingsmb1) az SMB 1 letiltása a Linux-ügyfeleken az SMB-fájlmegosztás éles környezetben való használata előtt.

A Linux kernel 4,18-es verziótól kezdődően az SMB kernel modul, amely `cifs` örökölt okokból készült, egy új modul-paramétert (más néven a különböző külső dokumentumok által *paramétert* ) tesz elérhetővé `disable_legacy_dialects` . Bár a Linux kernel 4,18-ben jelent meg, egyes gyártók backported ezt a változást az általuk támogatott régebbi kerneleken. A kényelmes használat érdekében a következő táblázat részletezi a modul paraméterének rendelkezésre állását a gyakori Linux-disztribúciók esetében.

| Disztribúció | Az SMB 1 letiltása |
|--------------|-------------------|
| Ubuntu 14.04 – 16.04 | No |
| Ubuntu 18.04 | Yes |
| Ubuntu 19.04 + | Yes |
| Debian 8-9 | No |
| Debian 10 + | Yes |
| Fedora 29 + | Yes |
| CentOS 7 | No | 
| CentOS 8 + | Yes |
| Red Hat Enterprise Linux 6. x-7. x | No |
| Red Hat Enterprise Linux 8 + | Yes |
| openSUSE LEAP 15,0 | No |
| openSUSE ugrás 15.1 + | Yes |
| openSUSE Tumbleweed | Yes |
| SUSE Linux Enterprise 11. x-12. x | No |
| SUSE Linux Enterprise 15 | No |
| SUSE Linux Enterprise 15,1 | No |

A következő parancs használatával megtekintheti, hogy a Linux-disztribúciója támogatja-e a `disable_legacy_dialects` modul paraméterét.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

A parancsnak a következő üzenetet kell kiadnia:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Az SMB 1 letiltása előtt ellenőriznie kell, hogy az SMB-modul jelenleg nincs-e betöltve a rendszeren (ez automatikusan megtörténik, ha az SMB-megosztást csatlakoztatta). Ezt a következő paranccsal teheti meg, amelynek kimenete nem történik meg, ha az SMB nincs betöltve:

```bash
lsmod | grep cifs
```

A modul eltávolításához először válassza le az összes SMB-megosztást (a `umount` fent ismertetett parancs használatával). A rendszeren található összes csatlakoztatott SMB-megosztást a következő paranccsal azonosíthatja:

```bash
mount | grep cifs
```

Miután leválasztotta az összes SMB-fájlmegosztást, biztonságosan eltávolíthatja a modult. Ezt a `modprobe` paranccsal teheti meg:

```bash
sudo modprobe -r cifs
```

A (z) paranccsal manuálisan is betöltheti a modult az SMB 1 memóriából `modprobe` :

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Végezetül megtekintheti az SMB-modult a paraméterrel, ha megtekinti a betöltött paramétereket a következő helyen `/sys/module/cifs/parameters` :

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Ha tartósan le szeretné tiltani az SMB 1-et az Ubuntu-és a Debian-alapú disztribúcióban, létre kell hoznia egy új fájlt (ha még nem rendelkezik más modulokra vonatkozó egyéni beállításokkal), akkor a beállítással kell megadnia `/etc/modprobe.d/local.conf` . Ezt a következő paranccsal teheti meg:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Az SMB-modul betöltésével ellenőrizheti, hogy ez működik-e:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>Következő lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure Filesról:

* [Azure Files üzembe helyezésének tervezése](storage-files-planning.md)
* [Gyakori kérdések](./storage-files-faq.md)
* [Hibaelhárítás](storage-troubleshoot-linux-file-connection-problems.md)