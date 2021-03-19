---
title: Az Azure Disk Encryption engedélyezése Linux rendszerű virtuális gépekhez
description: Ez a cikk a Linux rendszerű virtuális gépek Microsoft Azure lemezes titkosításának engedélyezéséhez nyújt útmutatást.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 81c026893c3185c6c9f960cdb6acb2d0c2d49cc4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580351"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption Linux rendszerű virtuális gépekhez 

Az Azure Disk Encryption segít az adatok biztonságos megőrzésében a vállalat által előírt biztonsági és megfelelőségi követelmények kielégítése érdekében. A Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) szolgáltatásával biztosítja a kötetek titkosítását az Azure Virtual Machines (VM) operációsrendszer-és adatlemezei számára, és integrálva van [Azure Key Vault](../../key-vault/index.yml) a lemezes titkosítási kulcsok és titkos kódok felügyeletéhez és kezeléséhez.

Azure Disk Encryption a zóna rugalmas, ugyanúgy, mint Virtual Machines. Részletekért lásd: [Availability Zones támogató Azure-szolgáltatások](../../availability-zones/az-region.md).

Ha [Azure Security Center](../../security-center/index.yml)használ, a rendszer riasztást küld, ha nem titkosított virtuális gépek vannak. A riasztások magas súlyosságot mutatnak, és a javasolt a virtuális gépek titkosítása.

![Azure Security Center lemez titkosítási riasztása](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Ha korábban már használta Azure Disk Encryption az Azure AD-vel egy virtuális gép titkosításához, továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Részletekért lásd: [Azure Disk Encryption az Azure ad-vel (előző kiadás)](disk-encryption-overview-aad.md) . 
> - Bizonyos javaslatok növelhetik az adatok, a hálózat vagy a számítási erőforrások használatát, ami további licenc-vagy előfizetési költségeket eredményezhet. Érvényes aktív Azure-előfizetéssel kell rendelkeznie ahhoz, hogy erőforrásokat hozzon létre az Azure-ban a támogatott régiókban.

A Linux rendszerű [virtuális gépek létrehozása és](disk-encryption-cli-quickstart.md) titkosítása az Azure CLI gyors üzembe helyezésével, valamint a [linuxos virtuális gép létrehozása és titkosítása Azure PowerShell](disk-encryption-powershell-quickstart.md)gyors üzembe helyezéssel néhány perc alatt elsajátíthatja a Linux Azure Disk Encryptionének alapjait.

## <a name="supported-vms-and-operating-systems"></a>Támogatott virtuális gépek és operációs rendszerek

### <a name="supported-vms"></a>Támogatott virtuális gépek

A Linux rendszerű virtuális gépek [számos méretben](../sizes.md)érhetők el. A Azure Disk Encryption az 1. és a 2. generációs virtuális gépek esetében támogatott. A Premium Storage szolgáltatással rendelkező virtuális gépek esetében Azure Disk Encryption is elérhető.

Lásd: Azure-beli [virtuális gépek mérete helyi ideiglenes lemez nélkül](../azure-vms-no-temp-disk.md).

Az Azure Disk Encryption nem érhető el az [alapszintű, a sorozatú](https://azure.microsoft.com/pricing/details/virtual-machines/series/)virtuális gépeken, illetve a minimális memória követelményeinek nem megfelelő virtuális gépeken:

| Virtuális gép | Minimális memória követelménye |
|--|--|
| Linux rendszerű virtuális gépek, ha csak az adatkötetek titkosítását| 2 GB |
| Linux rendszerű virtuális gépek az adatok és az operációsrendszer-kötetek titkosításakor, valamint a gyökér (/) fájlrendszer használatának helye 4 GB vagy kevesebb | 8 GB |
| Linux rendszerű virtuális gépek az adatok és az operációsrendszer-kötetek titkosítása esetén, valamint a gyökér (/) fájlrendszer használatának helye meghaladja a 4GB-ot | A rendszerindító fájlrendszer használata * 2. Például egy 16 GB-os rendszerszintű fájlrendszer-használat legalább 32GB RAM memóriát igényel |

Miután az operációsrendszer-lemez titkosítási folyamata befejeződött a Linux rendszerű virtuális gépeken, a virtuális gép beállítható úgy, hogy kevesebb memóriával fusson.

További kivételeket a [Azure Disk Encryption: nem támogatott forgatókönyvek](disk-encryption-linux.md#unsupported-scenarios)című témakörben talál.

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Azure Disk Encryption az [Azure által támogatott Linux-disztribúciók](endorsed-distros.md)egy részhalmazán támogatott, amely az összes Linux-kiszolgáló lehetséges disztribúciójának részhalmaza.

![A Azure Disk Encryptiont támogató Linux Server-disztribúciók Venn ábrája](./media/disk-encryption/ade-supported-distros.png)

Az Azure által nem támogatott Linux Server-disztribúciók nem támogatják a Azure Disk Encryption; a támogatottak közül csak a következő disztribúciók és verziók támogatják a Azure Disk Encryption:


| Publisher | Ajánlat | Termékváltozat | URN | Titkosításhoz támogatott kötet típusa |
| --- | --- |--- | --- |
| Canonical | Ubuntu | 18,04 – LTS | Canonical: UbuntuServer: 18.04-LTS: legújabb | Operációs rendszer és az adatlemez |
| Canonical | Ubuntu 18.04 | 18,04 – NAPONTA – LTS | Canonical: UbuntuServer: 18.04-DAILY-LTS: legújabb | Operációs rendszer és az adatlemez |
| Canonical | Ubuntu 16.04 | 16,04 – NAPONTA – LTS | Canonical: UbuntuServer: 16.04-DAILY-LTS: legújabb | Operációs rendszer és az adatlemez |
| Canonical | Ubuntu-14.04.5</br>[Az Azure-ban beállított kernel 4,15-es vagy újabb verzióra frissült](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Canonical: UbuntuServer: 14.04.5-LTS: legújabb | Operációs rendszer és az adatlemez |
| Canonical | Ubuntu-14.04.5</br>[Az Azure-ban beállított kernel 4,15-es vagy újabb verzióra frissült](disk-encryption-troubleshooting.md) | 14.04.5 – NAPI – LTS | Canonical: UbuntuServer: 14.04.5-DAILY-LTS: legújabb | Operációs rendszer és az adatlemez |
| RedHat | RHEL 8 – LVM | 8 – LVM | RedHat: RHEL: 8 – LVM: legújabb | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RedHat | RHEL 8,2 | 8.2 | RedHat: RHEL: 8.2: legújabb | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RedHat | RHEL 8,1 | 8.1 | RedHat: RHEL: 8.1: legújabb | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RedHat | RHEL 7 – LVM | 7 – LVM | RedHat: RHEL: 7 – LVM: 7.8.2020111201 | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RedHat | RHEL 7,8 | 7,8 | RedHat: RHEL: 7,8: legújabb | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RedHat | RHEL 7,7 | 7.7 | RedHat: RHEL: 7.7: legújabb | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RedHat | RHEL 7,6 | 7.6 | RedHat: RHEL: 7.6: legutóbbi | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RedHat | RHEL 7.5 | 7,5 | RedHat: RHEL: 7.5: legújabb | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RedHat | RHEL 7,4 | 7,4 | RedHat: RHEL: 7.4: legújabb | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RedHat | RHEL 7,3 | 7.3 | RedHat: RHEL: 7.3: legújabb | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RedHat | RHEL 7,2 | 7.2 | RedHat: RHEL: 7.2: legújabb | Operációs rendszer és az adatlemez (lásd az alábbi megjegyzést) |
| RedHat | RHEL 6,8 | 6.8 | RedHat: RHEL: 6.8: legújabb | Adatlemez (lásd az alábbi megjegyzést) |
| RedHat | RHEL 6,7 | 6.7 | RedHat: RHEL: 6.7: legújabb | Adatlemez (lásd az alábbi megjegyzést) |
| OpenLogic | CentOS 8 – LVM | 8 – LVM | OpenLogic: CentOS-LVM: 8-LVM: legújabb | Operációs rendszer és az adatlemez |
| OpenLogic | CentOS 8,2 | 8_2 | OpenLogic: CentOS: 8_2: legújabb | Operációs rendszer és az adatlemez |
| OpenLogic | CentOS 8,1 | 8_1 | OpenLogic: CentOS: 8_1: legújabb | Operációs rendszer és az adatlemez |
| OpenLogic | CentOS 7 – LVM | 7 – LVM | OpenLogic: CentOS-LVM: 7-LVM: 7.8.2020111100 | Operációs rendszer és az adatlemez |
| OpenLogic | CentOS 7,8 | 7,8 | OpenLogic: CentOS: 7_8: legújabb | Operációs rendszer és az adatlemez |
| OpenLogic | CentOS 7,7 | 7.7 | OpenLogic: CentOS: 7.7: legújabb | Operációs rendszer és az adatlemez |
| OpenLogic | CentOS 7,6 | 7.6 | OpenLogic: CentOS: 7.6: legújabb | Operációs rendszer és az adatlemez |
| OpenLogic | CentOS 7.5 | 7,5 | OpenLogic: CentOS: 7.5: legújabb | Operációs rendszer és az adatlemez |
| OpenLogic | CentOS 7.4 | 7,4 | OpenLogic: CentOS: 7.4: legújabb | Operációs rendszer és az adatlemez |
| OpenLogic | CentOS 7,3 | 7.3 | OpenLogic: CentOS: 7.3: legújabb | Operációs rendszer és az adatlemez |
| OpenLogic | CentOS 7.2 n | 7.2 n | OpenLogic: CentOS: 7.2 n: legújabb | Operációs rendszer és az adatlemez |
| OpenLogic | CentOS 7,1 | 7.1 | OpenLogic: CentOS: 7.1: legújabb | Csak adatlemez |
| OpenLogic | CentOS 7,0 | 7,0 | OpenLogic: CentOS: 7.0: legújabb | Csak adatlemez |
| OpenLogic | CentOS 6,8 | 6.8 | OpenLogic: CentOS: 6.8: legújabb | Csak adatlemez |
| SUSE | openSUSE 42,3 | 42,3 | SUSE: openSUSE-LEAP: 42.3: legújabb | Csak adatlemez |
| SUSE | SLES 12 – SP4 | 12 – SP4 | SUSE: SLES: 12-SP4: legújabb | Csak adatlemez |
| SUSE | SLES HPC 12 – SP3 | 12 – SP3 | SUSE: SLES-HPC: 12-SP3: legújabb | Csak adatlemez |

> [!NOTE]
> Az új Azure Disk Encryption implementációja RHEL operációs rendszer és adatlemez esetén támogatott a RHEL7 utólagos elszámolású lemezképekhez.  
>
> Az ADE-t a saját előfizetéssel rendelkező RHEL is támogatja, de csak az előfizetés regisztrálása **után** . További információkért lásd: az [Azure-beli saját előfizetéssel rendelkező Gold-lemezképek Red Hat Enterprise Linux](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>További virtuális gépekre vonatkozó követelmények

Azure Disk Encryption megköveteli, hogy a dm-crypt és a VFAT modulok jelen legyenek a rendszeren. A VFAT eltávolítása vagy letiltása az alapértelmezett rendszerképből megakadályozza, hogy a rendszer beolvassa a kulcs kötetét, és beszerezze a lemezek zárolásának feloldásához szükséges kulcsot a későbbi újraindítások során. A VFAT modul a rendszerből való eltávolítására, illetve az operációsrendszer-csatolási/-mappák az adatmeghajtókon való kibővítésének betartatására szolgáló rendszer-megerősítési lépések nem kompatibilisek a Azure Disk Encryptionokkal. 

A titkosítás engedélyezése előtt a titkosítani kívánt adatlemezeknek megfelelően szerepelniük kell a/etc/fstab. Bejegyzések létrehozásakor használja a "sikertelen" lehetőséget, és válassza ki az állandó blokk eszköz nevét (ahogy az "/dev/sdX" formátumban nem lehet ugyanahhoz a lemezhez hozzárendelni az újraindítások között, különösen a titkosítás után, a viselkedésről részletesebben lásd: Linux rendszerű [virtuális gép eszköz nevének módosítása](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)).

Győződjön meg arról, hogy az/etc/fstab-beállítások megfelelően vannak konfigurálva a csatlakoztatáshoz. Ezen beállítások konfigurálásához futtassa a Mount-a parancsot, vagy indítsa újra a virtuális gépet, és aktiválja az újracsatlakoztatást. Ha a művelet befejeződött, ellenőrizze a lsblk parancs kimenetét annak ellenőrzéséhez, hogy a meghajtó továbbra is csatlakoztatva van-e. 

- Ha az/etc/fstab fájl nem csatlakoztatja megfelelően a meghajtót a titkosítás engedélyezése előtt, Azure Disk Encryption nem fogja tudni megfelelően csatlakoztatni.
- A Azure Disk Encryption folyamat a csatlakoztatási adatokat az/etc/fstab és a saját konfigurációs fájljába helyezi át a titkosítási folyamat részeként. Ne felébressze, hogy az adatmeghajtó titkosításának befejeződése után nem kell megtekinteni az/etc/fstabből hiányzó bejegyzést.
- A titkosítás megkezdése előtt mindenképpen állítsa le az összes olyan szolgáltatást és folyamatot, amely a csatlakoztatott adatlemezekre írhat, és tiltsa le őket, hogy újraindítás után ne induljon el automatikusan. Ezek megtarthatják a fájlok megnyitását ezeken a partíciókon, így megakadályozva a titkosítási eljárás újracsatlakoztatását, ami hibát okoz a titkosításban. 
- Az újraindítás után időt vesz igénybe a Azure Disk Encryption folyamat az újonnan titkosított lemezek csatlakoztatására. Újraindítás után nem lesznek azonnal elérhetők. A folyamatnak időre van szüksége az indításhoz, a zárolás feloldásához és a titkosított meghajtók csatlakoztatásához, mielőtt más folyamatok számára elérhetővé válik. Ez a folyamat a rendszerjellemzőktől függően több mint egy percet is igénybe vehet a rendszerindítás után.

Íme egy példa az adatlemezek csatlakoztatásához és a szükséges/etc/fstab bejegyzések létrehozásához használt parancsokra:

```bash
UUID0="$(blkid -s UUID -o value /dev/sda1)"
UUID1="$(blkid -s UUID -o value /dev/sda2)"
mkdir /data0
mkdir /data1
echo "UUID=$UUID0 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "UUID=$UUID1 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
## <a name="networking-requirements"></a>Hálózati követelmények

A Azure Disk Encryption funkció engedélyezéséhez a Linux rendszerű virtuális gépeknek meg kell felelniük a hálózati végpont következő konfigurációs követelményeinek:
  - Ahhoz, hogy jogkivonatot kapjon a kulcstartóhoz való kapcsolódáshoz, a linuxos virtuális gépnek képesnek kell lennie csatlakozni egy Azure Active Directory végponthoz, a \[ login.microsoftonline.com \] .
  - A titkosítási kulcsok a kulcstartóba való írásához a linuxos virtuális gépnek csatlakoznia kell a Key Vault-végponthoz.
  - A linuxos virtuális gépnek képesnek kell lennie csatlakozni egy Azure Storage-végponthoz, amely az Azure-bővítmény adattárát és a VHD-fájlokat tároló Azure Storage-fiókot üzemelteti.
  -  Ha a biztonsági házirend korlátozza az Azure-beli virtuális gépekről az internetre való hozzáférést, az előző URI-t megoldhatja, és konfigurálhat egy adott szabályt, hogy engedélyezze a kimenő kapcsolatot az IP-címekkel. További információ: [Azure Key Vault tűzfal mögött](../../key-vault/general/access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Titkosítási kulcs tárolási követelményei  

Azure Disk Encryption a lemez titkosítási kulcsainak és titkainak szabályozásához és kezeléséhez Azure Key Vault szükséges. A Key vaultnak és a virtuális gépeknek ugyanabban az Azure-régióban és-előfizetésben kell lenniük.

További információ: [Key Vault létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminológia

Az alábbi táblázat az Azure Disk Encryption dokumentációjában használt általános kifejezéseket ismerteti:

| Terminológia | Definíció |
| --- | --- |
| Azure Key Vault | Key Vault egy kriptográfiai, kulcskezelő szolgáltatás, amely a szövetségi Information Processing Standards (FIPS) ellenőrzött hardveres biztonsági modulokon alapul. Ezek a szabványok segítenek megvédeni a titkosítási kulcsokat és a bizalmas titkokat. További információkért tekintse meg a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját, és [hozzon létre és konfiguráljon egy Key vaultot a Azure Disk Encryptionhoz](disk-encryption-key-vault.md). |
| Azure CLI | [Az Azure CLI](/cli/azure/install-azure-cli) az Azure-erőforrások parancssorból történő kezelésére és felügyeletére van optimalizálva.|
| DM-Crypt |A [dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) a Linux-alapú, transzparens lemezes titkosítási alrendszer, amely lehetővé teszi a lemezes titkosítás használatát a Linux rendszerű virtuális gépeken. |
| Kulcs titkosítási kulcsa (KEK) | Az aszimmetrikus kulcs (RSA 2048), amellyel védetté teheti vagy becsomagolhatja a titkos kulcsot. Megadhatja a hardveres biztonsági modul (HSM) által védett kulcsot vagy szoftveresen védett kulcsot. További információkért tekintse meg a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját, és [hozzon létre és konfiguráljon egy Key vaultot a Azure Disk Encryptionhoz](disk-encryption-key-vault.md). |
| PowerShell-parancsmagok | További információ: [Azure PowerShell parancsmagok](/powershell/azure/). |


## <a name="next-steps"></a>Következő lépések

- [Rövid útmutató – linuxos virtuális gép létrehozása és titkosítása az Azure CLI-vel ](disk-encryption-cli-quickstart.md)
- [Rövid útmutató – Linux rendszerű virtuális gép létrehozása és titkosítása Azure PowerShell](disk-encryption-powershell-quickstart.md) 
- [Azure Disk Encryption-forgatókönyvek Linux rendszerű virtuális gépeken](disk-encryption-linux.md)
- [Előfeltételként Azure Disk Encryption parancssori felület parancsfájlja](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption előfeltételek PowerShell-parancsfájl](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz](disk-encryption-key-vault.md)