---
title: Fájlok áthelyezése Azure-beli linuxos virtuális gépekről és az SCP használatával
description: A fájlokat biztonságosan helyezheti át az Azure-ban lévő Linux rendszerű virtuális gépekre az SCP és egy SSH-kulcspár használatával.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/12/2017
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 83b57055ee7a3fedab014abeab96520c3877b843
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558440"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Fájlok áthelyezése Linux rendszerű virtuális gépről SCP használatával

Ez a cikk bemutatja, hogyan helyezhetők át fájlok a munkaállomásról egy Azure Linux rendszerű virtuális gépre vagy egy Azure linuxos virtuális gépről a munkaállomásra a biztonságos másolás (SCP) használatával. A fájlok a munkaállomások és a Linux rendszerű virtuális gépek közötti áthelyezése gyorsan és biztonságosan, kritikus fontosságú az Azure-infrastruktúra kezeléséhez. 

Ebben a cikkben egy, az Azure-ban üzembe helyezett Linux rendszerű virtuális gépre van szükség az [SSH nyilvános és titkos kulcs fájljainak](mac-create-ssh-keys.md)használatával. Szükség van egy SCP-ügyfélre is a helyi számítógépen. Az SSH-ra épül, és a legtöbb Linux-és Mac-számítógép alapértelmezett bash-rendszerhéjában és néhány Windows-rendszerhéjban szerepel.

## <a name="quick-commands"></a>Gyors parancsok

Fájl másolása a Linux rendszerű virtuális gépre

```bash
scp file azureuser@azurehost:directory/targetfile
```

Fájl másolása a Linux rendszerű virtuális gépről

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Részletes bemutató

Példaként egy Linux rendszerű virtuális gépre helyezünk át egy Azure-beli konfigurációs fájlt, és lehívhatjuk a naplófájlok könyvtárát az SCP és SSH kulcsok használatával.   

## <a name="ssh-key-pair-authentication"></a>SSH-kulcspár hitelesítése

Az SCP SSH-t használ a szállítási réteghez. Az SSH kezeli a hitelesítést a cél gazdagépen, és a fájlt egy, az SSH-val alapértelmezés szerint elérhető titkosított alagútba helyezi át. Az SSH-hitelesítéshez felhasználóneveket és jelszavakat lehet használni. Az SSH nyilvános és titkos kulcsú hitelesítés azonban ajánlott biztonsági eljárás. Miután az SSH hitelesítette a kapcsolódást, az SCP megkezdi a fájl másolását. A megfelelően konfigurált `~/.ssh/config` és az SSH nyilvános és titkos kulcsok használatával az scp-kapcsolatok csak kiszolgálónév (vagy IP-cím) használatával állíthatók be. Ha csak egy SSH-kulccsal rendelkezik, a SZOLGÁLTATÁSKAPCSOLÓDÁSI pont a könyvtárban keresi a `~/.ssh/` könyvtárat, és alapértelmezés szerint a virtuális gépre való bejelentkezéshez használja.

A nyilvános és a titkos SSH-kulcsok konfigurálásával kapcsolatos további információkért `~/.ssh/config` lásd: [ssh-kulcsok létrehozása](mac-create-ssh-keys.md).

## <a name="scp-a-file-to-a-linux-vm"></a>SZOLGÁLTATÁSKAPCSOLÓDÁSI pont egy fájl Linux rendszerű virtuális géphez

Az első példában egy Azure konfigurációs fájlt másolunk egy olyan linuxos virtuális gépre, amely az automatizálás üzembe helyezésére szolgál. Mivel ez a fájl tartalmazza az Azure API hitelesítő adatait, köztük a titkokat is, fontos a biztonság. Az SSH által biztosított titkosított alagút védi a fájl tartalmát.

A következő parancs átmásolja a helyi *. Azure/config* fájlt egy FQDN- *MyServer.eastus.cloudapp.Azure.com* rendelkező Azure-beli virtuális gépre. A rendszergazdai Felhasználónév az Azure-beli virtuális gépen az *azureuser*. A fájl a */Home/azureuser/* könyvtárra irányul. Helyettesítse be a saját értékeit ebben a parancsban.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SZOLGÁLTATÁSKAPCSOLÓDÁSI pont egy Linux-alapú virtuális gépről

Ebben a példában a Linux rendszerű virtuális gépről másolja a naplófájlok könyvtárát a munkaállomásra. A naplófájlok tartalmazhatnak bizalmas vagy titkos adatokat is. Az SCP használata azonban biztosítja a naplófájlok tartalmának titkosítását. Az SCP használatával a fájlok átvitele a legegyszerűbb módszer a naplók és a fájlok a munkaállomásra való lekérésére, miközben azt is biztonságossá teszi.

A következő parancs fájlokat másol az Azure-beli virtuális gép */Home/azureuser/logs/* könyvtárába a helyi/tmp könyvtárba:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

A `-r` jelző arra utasítja az scp-t, hogy rekurzív módon másolja a fájlokat és a címtárakat a parancsban felsorolt könyvtár pontjából.  Azt is figyelje meg, hogy a parancssori szintaxis hasonló a `cp` másolási parancshoz.

## <a name="next-steps"></a>Következő lépések

* [A VMAccess-bővítmény használatával kezelheti a felhasználókat, SSH-t, és megvizsgálhatja vagy kijavíthatja az Azure Linux virtuális gépek lemezeit](../extensions/vmaccess.md?toc=/azure/virtual-machines/linux/toc.json)
