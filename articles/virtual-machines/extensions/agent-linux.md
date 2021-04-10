---
title: Az Azure Linux VM-ügynök áttekintése
description: Ismerje meg, hogyan telepítheti és konfigurálhatja a Linux-ügynököt (waagent) a virtuális gép Azure Fabric-vezérlővel való interakciójának kezeléséhez.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: linux
ms.date: 10/17/2016
ms.openlocfilehash: e8851ddd5211536394614727d990a2b52d32bfcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565377"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Az Azure Linux-ügynök megismerése és használata

A Microsoft Azure Linux-ügynök (waagent) kezeli a Linux & a FreeBSD kiépítés és a virtuális gép interakcióját az Azure Fabric Controllerrel. A kiépítési funkciókat biztosító Linux-ügynökön kívül az Azure is lehetőséget nyújt a Cloud-init használatára a Linux operációs rendszerekhez. A Linux-ügynök a következő funkciókat biztosítja a Linux és a FreeBSD IaaS üzemelő példányokhoz:

> [!NOTE]
> További információ: [readme](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Rendszerkép kiépítés**
  
  * Felhasználói fiók létrehozása
  * SSH-hitelesítési típusok konfigurálása
  * Nyilvános SSH-kulcsok és kulcspár üzembe helyezése
  * Az állomásnév beállítása
  * Az állomásnév közzététele a platform DNS-ben
  * SSH-gazdagép kulcsának ujjlenyomatának jelentése a platformnak
  * Erőforrás-lemezek kezelése
  * Az erőforrás lemezének formázása és csatlakoztatása
  * A swap-terület konfigurálása
* **Hálózat**
  
  * Kezeli az útvonalakat a platform DHCP-kiszolgálókkal való kompatibilitás javítása érdekében
  * Biztosítja a hálózati adapter nevének stabilitását
* **Kernel**
  
  * Virtuális NUMA konfigurálása (kernel <letiltása `2.6.37` )
  * A Hyper-V entrópia használata a/dev/Random
  * SCSI-időtúllépések konfigurálása a gyökérszintű eszközhöz (amely távoli lehet)
* **Diagnosztika**
  
  * Konzol átirányítása a soros portra
* **SCVMM üzemelő példányok**
  
  * Észleli és beindítja a Linux VMM-ügynökét, ha System Center Virtual Machine Manager 2012 R2 környezetben fut
* **VM-bővítmény**
  
  * A Microsoft és partnerei által a szoftverek és a konfiguráció automatizálásának lehetővé tételéhez a Linux VM (IaaS) segítségével létrehozott összetevő behelyezése
  * A virtuálisgép-bővítmény referenciájának megvalósítása [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Kommunikáció
A platformról az ügynökre irányuló információáramlás két csatornán keresztül történik:

* Egy rendszerindítási idő csatolt DVD-je a IaaS üzemelő példányokhoz. Ez a DVD tartalmaz egy OVF-kompatibilis konfigurációs fájlt, amely a tényleges SSH-kulcspárt nem tartalmazza az összes kiépítési információt.
* Egy TCP-végpont, amely a központi telepítés és a topológia konfigurációjának megszerzéséhez használt REST API teszi közzé.

## <a name="requirements"></a>Követelmények
A következő rendszerek lettek tesztelve, és ismertek az Azure Linux-ügynökkel való együttműködésre:

> [!NOTE]
> Ez a lista a [támogatott disztribúciók](../linux/endorsed-distros.md)hivatalos listájától eltérő lehet.
> 
> 

* CoreOS
* CentOS 6.3 +
* Red Hat Enterprise Linux 6.7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Egyéb támogatott rendszerek:

* FreeBSD 10 + (Azure Linux Agent v 2.0.10 +)

A Linux-ügynök a megfelelő működés érdekében néhány rendszercsomagtól függ:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Fájlrendszer segédprogramok: sfdisk, fdisk, mkfs, részben
* Jelszó-eszközök: chpasswd, sudo
* Text Processing Tools: sed, grep
* Hálózati eszközök: IP-útvonal
* Kernel-támogatás az UDF-fájlrendszerek csatlakoztatásához.

Győződjön meg arról, hogy a virtuális gép rendelkezik hozzáféréssel az IP-168.63.129.16. További információ: [Mi az IP-168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).


## <a name="installation"></a>Telepítés
Az Azure Linux-ügynök telepítésének és frissítésének előnyben részesített módja, ha egy RPM vagy egy DEB-csomagot használ a terjesztési csomag adattárában. Az összes [támogatott terjesztési szolgáltató](../linux/endorsed-distros.md) integrálja az Azure Linux-ügynök csomagját a lemezképbe és a tárházba.

A speciális telepítési lehetőségekről, például a forrásról vagy az egyéni helyekre vagy előtagokra történő telepítésről a [githubon elérhető Azure Linux-ügynök](https://github.com/Azure/WALinuxAgent) tárházában tájékozódhat.

## <a name="command-line-options"></a>Command-Line beállítások
### <a name="flags"></a>Zászlók
* részletes: a megadott parancs részletességének fokozása
* kényszerített: az interaktív megerősítés kihagyása egyes parancsokhoz

### <a name="commands"></a>Parancsok
* Súgó: felsorolja a támogatott parancsokat és jelzőket.
* megszüntetés: megkísérli a rendszer tisztítását, és alkalmassá tenni az újratelepítésre. A következő művelet törölhető:
  
  * Minden SSH-gazdagép kulcsa (ha a kiépítés. a RegenerateSshHostKeyPair a konfigurációs fájlban "y".
  * Névkiszolgáló-konfiguráció a/etc/resolv.conf-ben
  * Rendszergazdai jelszó a/etc/Shadow (ha a kiépítés. a DeleteRootPassword értéke "y" a konfigurációs fájlban)
  * Gyorsítótárazott DHCP-ügyfelek címbérletei
  * Alaphelyzetbe állítja a gazdagép nevét a localhost. LOCALDOMAIN értékre

> [!WARNING]
> A megszüntetés nem garantálja, hogy a rendszerkép törlődik az összes bizalmas adatról, és alkalmas az újraterjesztésre.
> 
> 

* kiépítés + felhasználó: mindent végrehajt (fent), és törli az utolsó kiépített felhasználói fiókot (a/var/lib/waagent-ből) és a kapcsolódó adatokkal is. Ez a paraméter egy olyan rendszerkép kihelyezése, amely korábban az Azure-ban lett kiépítve, így rögzíthető és újra felhasználható.
* verzió: a waagent verzióját jeleníti meg
* serialconsole: a GRUB beállítása a ttyS0 (az első soros port) megjelölésére a rendszerindító konzolként. Ez biztosítja, hogy a rendszer elküldje a kernel-rendszerindítási naplókat a soros portra, és elérhetővé teszi a hibakereséshez
* démon: futtassa a waagent démonként a platformmal való interakció kezeléséhez. Ez az argumentum a waagent init parancsfájl waagent van megadva.
* indítás: waagent futtatása háttérbeli folyamatként

## <a name="configuration"></a>Konfiguráció
A konfigurációs fájl (/etc/waagent.conf) szabályozza a waagent műveleteit. A következő példában egy minta konfigurációs fájl látható:

```config
Provisioning.Enabled=y
Provisioning.DeleteRootPassword=n
Provisioning.RegenerateSshHostKeyPair=y
Provisioning.SshHostKeyPairType=rsa
Provisioning.MonitorHostName=y
Provisioning.DecodeCustomData=n
Provisioning.ExecuteCustomData=n
Provisioning.AllowResetSysUser=n
Provisioning.PasswordCryptId=6
Provisioning.PasswordCryptSaltLength=10
ResourceDisk.Format=y
ResourceDisk.Filesystem=ext4
ResourceDisk.MountPoint=/mnt/resource
ResourceDisk.MountOptions=None
ResourceDisk.EnableSwap=n
ResourceDisk.SwapSizeMB=0
LBProbeResponder=y
Logs.Verbose=n
OS.RootDeviceScsiTimeout=300
OS.OpensslPath=None
HttpProxy.Host=None
HttpProxy.Port=None
AutoUpdate.Enabled=y
```

A következő különböző konfigurációs beállítások vannak leírva. A konfigurációs beállítások három típusból állnak; Boolean, string vagy Integer. A logikai konfigurációs beállítások "y" vagy "n" értékkel adhatók meg. A "None" kulcsszót a következő részletekkel lehet használni néhány karakterlánc típusú konfigurációs bejegyzésnél:

**Kiépítés. engedélyezve:**  
```txt
Type: Boolean  
Default: y
```
Ez lehetővé teszi, hogy a felhasználó engedélyezze vagy tiltsa le az ügynök kiépítési funkcióit. Az érvényes értékek: "y" vagy "n". Ha a kiépítés le van tiltva, a rendszer megőrzi az SSH-gazdagépet és a felhasználói kulcsokat a rendszerképben, és figyelmen kívül hagyja az Azure üzembe helyezési API-ban megadott konfigurációkat.

> [!NOTE]
> A `Provisioning.Enabled` paraméter alapértelmezés szerint "n" értékre van beállítva az Ubuntu Felhőbeli Rendszerképeken, amelyek az üzembe helyezéshez a Cloud-init szolgáltatást használják.
> 
> 

**Kiépítés. DeleteRootPassword:**  
```txt
Type: Boolean  
Default: n
```
Ha be van állítva, a/etc/Shadow fájl legfelső szintű jelszava törlődik a létesítési folyamat során.

**Kiépítés. RegenerateSshHostKeyPair:**  
```txt
Type: Boolean  
Default: y
```
Ha be van állítva, az összes SSH-gazdagép kulcspár (ECDSA, DSA és RSA) törölve lett a kiépítési folyamat során a/etc/ssh/. És egyetlen új kulcspár jön létre.

Az új kulcspár titkosítási típusa a kiépítési. SshHostKeyPairType bejegyzés által konfigurálható. Egyes disztribúciók minden hiányzó titkosítási típushoz újra létrehoznak SSH-kulcspárt az SSH-démon újraindításakor (például újraindítás után).

**Kiépítés. SshHostKeyPairType:**  
```txt
Type: String  
Default: rsa
```
Ez beállítható egy olyan titkosítási algoritmus típusára, amelyet a virtuális gépen futó SSH démon támogat. A jellemzően támogatott értékek a következők: "RSA", "DSA" és "ECDSA". A Windows "putty.exe" nem támogatja a "ECDSA". Ha tehát a Windows rendszerű számítógépeken szeretné putty.exe használni a Linux-telepítést, használja az "RSA" vagy a "DSA" kifejezést.

**Kiépítés. MonitorHostName:**  
```txt
Type: Boolean  
Default: y
```
Ha be van állítva, a waagent figyeli a Linux rendszerű virtuális gépet az állomásnév változásaihoz (az "állomásnév" parancs által visszaadottak szerint), és automatikusan frissíti a rendszerkép hálózati konfigurációját, hogy tükrözze a változást. Ahhoz, hogy a név a DNS-kiszolgálókra legyen leküldve, a hálózat újraindul a virtuális gépen. Ez az internetkapcsolat rövid elvesztését eredményezi.

**Kiépítés. DecodeCustomData**  
```txt
Type: Boolean  
Default: n
```
Ha be van állítva, a waagent dekódolja a Base64-CustomData.

**Provisioning.ExecuteCustomData**  
```txt
Type: Boolean  
Default: n
```
Ha be van állítva, a waagent a kiépítés után hajtja végre a CustomData.

**Kiépítés. AllowResetSysUser**
```txt
Type: Boolean
Default: n
```
Ez a beállítás lehetővé teszi, hogy a rendszer alaphelyzetbe állítsa a sys-felhasználó jelszavát. az alapértelmezett érték le van tiltva.

**Kiépítés. PasswordCryptId**  
```txt
Type: String  
Default: 6
```
A crypt által a jelszó-kivonat létrehozásakor használt algoritmus.  
 1 – MD5  
 2a – Blowfish  
 5 – SHA-256  
 6 – SHA-512  

**Kiépítés. PasswordCryptSaltLength**  
```txt
Type: String  
Default: 10
```
A jelszó-kivonatok létrehozásakor használt véletlenszerű só hossza.

**ResourceDisk. format:**  
```txt
Type: Boolean  
Default: y
```
Ha be van állítva, a platform által biztosított erőforrás-lemez formázása és csatlakoztatása waagent történik, ha a felhasználó által a "ResourceDisk. FileSystem" típusban kért fájlrendszer típusa nem "NTFS". A lemezen elérhető egyetlen Linux (83) típusú partíció. A partíció nem formázott, ha sikeresen csatlakoztatható.

**ResourceDisk. filesystem:**  
```txt
Type: String  
Default: ext4
```
Ez határozza meg az erőforrás-lemez fájlrendszerének típusát. A támogatott értékek a Linux-disztribúciók szerint változnak. Ha a karakterlánc X, akkor a mkfs. X-nek jelen kell lennie a Linux-rendszerképben. A SLES 11 lemezképek általában az "ext3"-et használják. A FreeBSD-lemezképek itt az "UFS2"-t használják.

**ResourceDisk. csatlakoztatási pont:**  
```txt
Type: String  
Default: /mnt/resource 
```
Ez határozza meg az erőforrás-lemez csatlakoztatásának elérési útját. Az erőforrás-lemez egy *ideiglenes* lemez, és a virtuális gép kiépítése után kiüríthető.

**ResourceDisk.MountOptions**  
```txt
Type: String  
Default: None
```
Megadja a Mount-o parancsnak átadandó lemez csatlakoztatási beállításait. Ez az értékek vesszővel tagolt listája, pl.:. "nodev, nosuid". A részletekért lásd a csatlakoztatás (8) című témakört.

**ResourceDisk.EnableSwap:**  
```txt
Type: Boolean  
Default: n
```
Ha be van állítva, a rendszer egy lapozófájlt (/swapfile) hoz létre az erőforrás-lemezen, és hozzáadja a rendszerswap-területhez.

**ResourceDisk.SwapSizeMB:**  
```txt
Type: Integer  
Default: 0
```
A lapozófájl mérete megabájtban.

**Naplók. verbose:**  
```txt
Type: Boolean  
Default: n
```
Ha be van állítva, a naplózási részletesség növelése megtörténik. A Waagent naplókat készít a/var/log/waagent.log, és a rendszerszintű logrotate funkciót használja a naplók elforgatásához.

**OS. EnableRDMA**  
```txt
Type: Boolean  
Default: n
```
Ha be van állítva, az ügynök megkísérli a telepítését, majd betölteni egy olyan RDMA kernel-illesztőprogramot, amely megfelel az alapul szolgáló hardveren található belső vezérlőprogram verziójának.

**OS. RootDeviceScsiTimeout:**  
```txt
Type: Integer  
Default: 300
```
Ezzel a beállítással az operációsrendszer-lemezen és az adatmeghajtókon másodpercek alatt megadhatja az SCSI-időtúllépést. Ha nincs beállítva, a rendszer az alapértelmezett értékeket használja.

**OS. OpensslPath:**  
```txt
Type: String  
Default: None
```
Ezzel a beállítással megadhatja a titkosítási műveletekhez használandó OpenSSL bináris fájl alternatív elérési útját.

**Http. Host, http. port**  
```txt
Type: String  
Default: None
```
Ha be van állítva, az ügynök ezt a proxykiszolgálót használja az Internet eléréséhez. 

**AutoUpdate. enabled**
```txt
Type: Boolean
Default: y
```
Engedélyezheti vagy letilthatja az automatikus frissítést a cél állapotának feldolgozásához; az alapértelmezett érték engedélyezve.



## <a name="ubuntu-cloud-images"></a>Ubuntu Felhőbeli rendszerképek
Az Ubuntu Cloud images a [Cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) használatával számos olyan konfigurációs feladatot hajt végre, amelyeket egyébként az Azure Linux-ügynök kezel. A következő különbségek érvényesek:

* **Kiépítés.** az alapértelmezett érték "n" a Cloud-init szolgáltatást használó Ubuntu Felhőbeli rendszerképeken a kiépítési feladatok végrehajtásához.
* A következő konfigurációs paraméterek nincsenek hatással a Cloud-initt használó Ubuntu Cloud images-lemezképekre az erőforrás-lemez és a swap-terület kezeléséhez:
  
  * **ResourceDisk. Format**
  * **ResourceDisk. filesystem**
  * **ResourceDisk. csatlakoztatási pont**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* További információt a következő forrásokban talál az erőforrás-lemez csatlakoztatási pontjának konfigurálásához és az Ubuntu Felhőbeli rendszerképeken a kiépítés során felcserélt területről:
  
  * [Ubuntu wiki: swap-partíciók konfigurálása](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Egyéni adatbevitel egy Azure-beli virtuális gépre](../windows/tutorial-automate-vm-deployment.md)