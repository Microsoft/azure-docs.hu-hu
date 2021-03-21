---
title: Az Azure Linux-ügynök frissítése a GitHubról
description: Ismerje meg, hogyan frissítheti az Azure Linux-ügynököt Linux rendszerű virtuális gépén az Azure-ban
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 08/02/2017
ms.openlocfilehash: a274435d6e0fc32bdf5b2ab04702ed971d7c5175
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558984"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Az Azure Linux-ügynök frissítése egy virtuális gépen

Az Azure [Linux-ügynök](https://github.com/Azure/WALinuxAgent) Azure-beli Linux rendszerű virtuális gépen való frissítéséhez a következőket kell tennie:

- Egy futó Linux rendszerű virtuális gép az Azure-ban.
- Kapcsolódás az adott linuxos virtuális géphez SSH használatával.

Először a Linux disztribúciós tárházban érdemes megkeresni a csomagokat. Lehetséges, hogy az elérhető csomag nem a legújabb verzió, azonban az AutoUpdate engedélyezése biztosítja, hogy a Linux-ügynök mindig megkapja a legújabb frissítést. Ha problémába ütközik a Package Managers szolgáltatásból történő telepítéssel, akkor a disztribúció gyártójának támogatást kell kérnie.

> [!NOTE]
> További információ: [támogatott Linux-disztribúciók az Azure](../linux/endorsed-distros.md) -ban

A továbblépés előtt ellenőrizze, hogy az [Azure-beli virtuálisgép-ügynökök minimális verziója támogatott-](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) e.


## <a name="ubuntu"></a>Ubuntu

A csomag aktuális verziójának keresése

```bash
apt list --installed | grep walinuxagent
```

Frissítési csomag gyorsítótára

```bash
sudo apt-get -qq update
```

A csomag legújabb verziójának telepítése

```bash
sudo apt-get install walinuxagent
```

Győződjön meg arról, hogy az automatikus frissítés engedélyezve van. Először ellenőrizze, hogy engedélyezve van-e a következő:

```bash
cat /etc/waagent.conf
```

Az "AutoUpdate. enabled" megkeresése. Ha ezt a kimenetet látja, engedélyezve van a következő:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

A Futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

A 14,04-es waagengt szolgáltatás újraindítása

```bash
initctl restart walinuxagent
```

A 16,04/17,04 waagent szolgáltatás újraindítása

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat/CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

A csomag aktuális verziójának keresése

```bash
sudo yum list WALinuxAgent
```

Elérhető frissítések keresése

```bash
sudo yum check-update WALinuxAgent
```

A csomag legújabb verziójának telepítése

```bash
sudo yum install WALinuxAgent
```

Győződjön meg arról, hogy az automatikus frissítés engedélyezve van 

Először ellenőrizze, hogy engedélyezve van-e a következő:

```bash
cat /etc/waagent.conf
```

Az "AutoUpdate. enabled" megkeresése. Ha ezt a kimenetet látja, engedélyezve van a következő:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

A Futtatás engedélyezése:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

A waagent szolgáltatás újraindítása

```
sudo service waagent restart
```

## <a name="rhelcentos-7"></a>RHEL/CentOS 7

A csomag aktuális verziójának keresése

```bash
sudo yum list WALinuxAgent
```

Elérhető frissítések keresése

```bash
sudo yum check-update WALinuxAgent
```

A csomag legújabb verziójának telepítése

```bash
sudo yum install WALinuxAgent  
```

Győződjön meg arról, hogy az automatikus frissítés engedélyezve van. Először ellenőrizze, hogy engedélyezve van-e a következő:

```bash
cat /etc/waagent.conf
```

Az "AutoUpdate. enabled" megkeresése. Ha ezt a kimenetet látja, engedélyezve van a következő:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

A Futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

A waagent szolgáltatás újraindítása

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

A csomag aktuális verziójának keresése

```bash
zypper info python-azure-agent
```

Az elérhető frissítések keresése. A fenti kimenet megmutatja, hogy a csomag naprakész-e.

A csomag legújabb verziójának telepítése

```bash
sudo zypper install python-azure-agent
```

Győződjön meg arról, hogy az automatikus frissítés engedélyezve van 

Először ellenőrizze, hogy engedélyezve van-e a következő:

```bash
cat /etc/waagent.conf
```

Az "AutoUpdate. enabled" megkeresése. Ha ezt a kimenetet látja, engedélyezve van a következő:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

A Futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

A waagent szolgáltatás újraindítása

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

A csomag aktuális verziójának keresése

```bash
zypper info python-azure-agent
```

Elérhető frissítések keresése

A fenti kimenetben ez megmutatja, hogy a csomag naprakész-e.

A csomag legújabb verziójának telepítése

```bash
sudo zypper install python-azure-agent
```

Győződjön meg arról, hogy az automatikus frissítés engedélyezve van 

Először ellenőrizze, hogy engedélyezve van-e a következő:

```bash
cat /etc/waagent.conf
```

Az "AutoUpdate. enabled" megkeresése. Ha ezt a kimenetet látja, engedélyezve van a következő:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

A Futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

A waagent szolgáltatás újraindítása

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse"/Debian 7 "stretch"

A csomag aktuális verziójának keresése

```bash
dpkg -l | grep waagent
```

Frissítési csomag gyorsítótára

```bash
sudo apt-get -qq update
```

A csomag legújabb verziójának telepítése

```bash
sudo apt-get install waagent
```

Ügynök automatikus frissítésének engedélyezése a Debian ezen verziója nem rendelkezik >= 2.0.16 verzióval, ezért az AutoUpdate nem érhető el. A fenti parancs kimenete megmutatja, hogy a csomag naprakész-e.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie"/Debian 9 "stretch"

A csomag aktuális verziójának keresése

```bash
apt list --installed | grep waagent
```

Frissítési csomag gyorsítótára

```bash
sudo apt-get -qq update
```

A csomag legújabb verziójának telepítése

```bash
sudo apt-get install waagent
```

Győződjön meg arról, hogy az automatikus frissítés engedélyezve van-e. Ellenőrizze, hogy engedélyezve van-e a következő:

```bash
cat /etc/waagent.conf
```

Az "AutoUpdate. enabled" megkeresése. Ha ezt a kimenetet látja, engedélyezve van a következő:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

A Futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 és Oracle Linux 7

Oracle Linux esetén győződjön meg arról, hogy az `Addons` adattár engedélyezve van. Szerkessze a fájlt `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) vagy `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux), és módosítsa a sort a következőre `enabled=0` `enabled=1` : **[ol6_addons]** vagy **[ol7_addons]** .

Ezután az Azure Linux-ügynök legújabb verziójának telepítéséhez írja be a következőt:

```bash
sudo yum install WALinuxAgent
```

Ha nem találja a bővítmény tárházat, egyszerűen hozzáadhatja ezeket a sorokat a. repo fájl végén a Oracle Linux kiadásának megfelelően:

Oracle Linux 6 virtuális gép esetén:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Oracle Linux 7 virtuális gép esetén:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Ezután írja be a következőt:

```bash
sudo yum update WALinuxAgent
```

Általában ez minden, amire szüksége van, de ha valamilyen okból nem kell közvetlenül telepítenie https://github.com , kövesse az alábbi lépéseket.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>A Linux-ügynök frissítése, ha nem létezik ügynök-csomag a terjesztéshez

A wget telepítése (vannak olyan disztribúciók, amelyek nem telepítik alapértelmezés szerint, például a Red Hat, a CentOS és a Oracle Linux 6,4-es és 6,5-es verziókat) a parancssorba való beírásával `sudo yum install wget` .

### <a name="1-download-the-latest-version"></a>1. Töltse le a legújabb verziót
Nyissa meg [Az Azure Linux Agent kiadását a githubon](https://github.com/Azure/WALinuxAgent/releases) egy weblapon, és keresse meg a legújabb verziószámot. (Az aktuális verziót megkeresheti a beírásával `waagent --version` .)

A 2.2. x vagy újabb verziónál írja be a következőt:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

A következő sor a 2.2.0-as verziót használja példaként:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. az Azure Linux-ügynök telepítése

A 2.2. x verzióhoz használja a következőt: Előfordulhat, hogy először telepítenie kell a csomagot `setuptools` – lásd [itt](https://pypi.python.org/pypi/setuptools). Majd futtassa ezt:

```bash
sudo python setup.py install
```

Győződjön meg arról, hogy az automatikus frissítés engedélyezve van. Először ellenőrizze, hogy engedélyezve van-e a következő:

```bash
cat /etc/waagent.conf
```

Az "AutoUpdate. enabled" megkeresése. Ha ezt a kimenetet látja, engedélyezve van a következő:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

A Futtatás engedélyezése:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Indítsa újra a waagent szolgáltatást
A legtöbb Linux-disztribúció esetében:

```bash
sudo service waagent restart
```

Ubuntu esetén használja a következőt:

```bash
sudo service walinuxagent restart
```

A CoreOS használja a következőt:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. az Azure Linux-ügynök verziójának megerősítése
    
```bash
waagent -version
```

A CoreOS esetében előfordulhat, hogy a fenti parancs nem működik.

Látni fogja, hogy az Azure Linux-ügynök verziója frissítve lett az új verzióra.

Az Azure Linux-ügynökkel kapcsolatos további információkért lásd: [Azure Linux Agent readme](https://github.com/Azure/WALinuxAgent).
