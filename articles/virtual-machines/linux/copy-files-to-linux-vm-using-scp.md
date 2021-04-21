---
title: Fájlok áthelyezése virtuális gépre és virtuális gépről SCP használatával
description: Biztonságosan áthelyezi a fájlokat az Azure-beli Linux rendszerű virtuális gépekre és gépekről az SCP és egy SSH-kulcspár használatával.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/20/2021
ms.author: cynthn
ms.subservice: ''
ms.openlocfilehash: edfc44f79cff25486fde6326ac954fe5b575d846
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816439"
---
# <a name="use-scp-to-move-files-to-and-from-a-linux-vm"></a>Fájlok áthelyezése linuxos virtuális gépről linuxos virtuális gépre SCP használatával 

Ez a cikk bemutatja, hogyan lehet fájlokat áthelyezni a munkaállomásról egy Azure-beli linuxos virtuális gépre vagy egy Azure Linux rendszerű virtuális gépről a munkaállomásra a Biztonságos másolás (SCP) használatával. A fájlok munkaállomás és Linux rendszerű virtuális gép közötti gyors és biztonságos mozgatása kritikus fontosságú az Azure-infrastruktúra kezeléséhez. 

Ehhez a cikkhez szüksége lesz egy, az Azure-ban nyilvános és titkos SSH-kulcsfájlokkal üzembe helyezett Linux rendszerű [virtuális gépre.](mac-create-ssh-keys.md) Szüksége lesz egy SCP-ügyfélre is a helyi számítógépen. Az SSH-ra épül, és a legtöbb Linux és Mac számítógép, valamint a PowerShell alapértelmezett Bash-rendszerhéjában található.


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

Példaként egy Azure-beli konfigurációs fájlt áthelyezünk egy Linux rendszerű virtuális gépre, és leküldünk egy naplófájl könyvtárát SCP- és SSH-kulcsok használatával.   

## <a name="ssh-key-pair-authentication"></a>SSH-kulcspáros hitelesítés

Az SCP SSH-t használ az átviteli réteghez. Az SSH kezeli a hitelesítést a cél gazdagépen, és a fájlt egy alapértelmezés szerint az SSH által biztosított titkosított alagútba áthelyezi. Az SSH-hitelesítéshez felhasználónév és jelszó használható. Az ajánlott biztonsági eljárás azonban az SSH nyilvános és titkos kulcsos hitelesítése. Miután az SSH hitelesítette a kapcsolatot, az SCP megkezdi a fájl másolását. Megfelelően konfigurált és nyilvános és titkos SSH-kulcsokkal az SCP-kapcsolat egyszerűen a kiszolgálónév `~/.ssh/config` (vagy IP-cím) használatával létesítható. Ha csak egy SSH-kulcsa van, az SCP a címtárban keres, és alapértelmezés szerint a segítségével jelentkezik be a `~/.ssh/` virtuális gépre.

A és az SSH nyilvános és titkos kulcsának konfigurálásával kapcsolatos további információkért lásd: `~/.ssh/config` [SSH-kulcsok létrehozása.](mac-create-ssh-keys.md)

## <a name="scp-a-file-to-a-linux-vm"></a>Fájl SCP-vel való létrehozása Linux rendszerű virtuális gépre

Az első példában egy Azure-beli konfigurációs fájlt másolunk egy Linux rendszerű virtuális gépre, amely az automatizálás üzembe helyezésére szolgál. Mivel ez a fájl tartalmazza az Azure API hitelesítő adatait, amelyek titkos kulcsokat is tartalmaznak, a biztonság fontos. Az SSH által biztosított titkosított alagút védi a fájl tartalmát.

A következő parancs átmásolja a helyi *.azure/config* fájlt egy Azure-beli virtuális gépre teljes *tartománynévvel myserver.eastus.cloudapp.azure.com.* Ha nincs beállítva [FQDN,](../create-fqdn.md)használhatja a virtuális gép IP-címét is. Az Azure-beli virtuális gépen a rendszergazda felhasználóneve *azureuser.* A fájl célja a */home/azureuser/* könyvtár. A parancsban helyettesítse be a saját értékeit.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Címtár SCP-hez Linux rendszerű virtuális gépről

Ebben a példában a naplófájlok könyvtárát másoljuk le a Linux rendszerű virtuális gépről a munkaállomásra. A naplófájlok tartalmazhatnak bizalmas vagy titkos adatokat. Az SCP használata azonban biztosítja a naplófájlok tartalmának titkosítását. A fájlok SCP-vel történő átvitele a legegyszerűbb módszer a naplókönyvtár és a fájlok munkaállomásra való lehozása közben a biztonság érdekében.

A következő parancs az Azure-beli virtuális gép */home/azureuser/logs/* könyvtárában lévő fájlokat a helyi /tmp könyvtárba másolja:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

A jelző arra utasítja az SCP-t, hogy rekurzívan másolja a fájlokat és könyvtárakat a parancsban felsorolt `-r` könyvtárból.  Azt is figyelje meg, hogy a parancssori szintaxis hasonlít a `cp` másolási parancshoz.

## <a name="next-steps"></a>Következő lépések

* [Felhasználók és SSH kezelése, valamint lemezek ellenőrzése vagy javítása Azure-beli Linux rendszerű virtuális gépeken a VMAccess bővítvítmény használatával](../extensions/vmaccess.md?toc=/azure/virtual-machines/linux/toc.json)
