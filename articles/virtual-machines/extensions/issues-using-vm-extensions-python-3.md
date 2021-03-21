---
title: A Python 3-kompatibilis Linux Azure Virtual Machines rendszerek virtuálisgép-bővítményeit használó problémák
description: Tudnivalók a VM-bővítmények használatáról a Python 3-kompatibilis Linux rendszerekben
services: virtual-machines
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: d38b41bc47c01b01fcbb1b05238396dca6d8eb4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558644"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>A Python 3-kompatibilis Linux Azure Virtual Machines rendszerek virtuálisgép-bővítményeit használó problémák

> [!NOTE]
> A Microsoft arra biztatja a felhasználókat, hogy csak akkor fogadjanak el **Python 3. x** rendszert, ha a számítási feladathoz a **Python 2. x** támogatása szükséges. Ilyen követelmény például az örökölt felügyeleti parancsfájlok, illetve a **Azure Disk Encryption** és a **Azure monitor** bővítmények lehetnek.
>
> A **Python 2. x** éles környezetben való telepítése előtt vegye figyelembe a Python 2. x hosszú távú támogatásának kérdését, különösen a biztonsági frissítések fogadásának lehetőségét. Termékként, beleértve az említett bővítmények némelyikét, a **python 3,8** -támogatással való frissítéssel a Python 2. x verziójának használatát kell megszüntetnie.

Egyes Linux-disztribúciók áttértek a Python 3,8-re, és eltávolította a `/usr/bin/python` teljes Python örökölt BelépésiPont. Ez az áttérés hatással van az egyes virtuálisgép-bővítmények beépített, automatikus üzembe helyezésére a következő két feltétellel:

- A Python 3. x-támogatással továbbra is elérhető bővítmények
- A régi BelépésiPont használó bővítmények `/usr/bin/python`

A **Python 3. x verzióra** áttérő Linux-disztribúciós felhasználóknak biztosítaniuk kell a régi `/usr/bin/python` BelépésiPont, mielőtt a bővítményeket a virtuális gépekre telepítené. Ellenkező esetben előfordulhat, hogy a bővítmény telepítése sikertelen lesz. 

- A támogatott Linux-disztribúciók közé tartozik az **Ubuntu Server 20,04 LTS** és az **Ubuntu Pro 20,04 LTS**.

- Az érintett virtuálisgép-bővítmények közé tartozik a **Azure Disk Encryption**, a **log Analytics**, a virtuális gépekhez **való hozzáférés** (a jelszó-visszaállításhoz használt) és a **vendég diagnosztika** (a további teljesítményszámlálók esetében használatos).

A helyben történő verziófrissítések, például az **ubuntu 18,04 LTS** -ről **Ubuntu 20,04 LTS**-re való frissítés, meg kell őrizni a `/usr/bin/python` symlinket, és nem kell tovább működniük.

## <a name="resolution"></a>Feloldás

A bővítmények telepítése előtt vegye figyelembe ezeket az általános ajánlásokat az összegzésben korábban ismertetett ismert forgatókönyvekben:

1. A bővítmény telepítése előtt állítsa vissza a `/usr/bin/python` symlink-t a Linux Distribution gyártó által biztosított módszer használatával.

   - Például a **Python 2,7** esetén használja a következőt: `sudo apt update && sudo apt install python-is-python2`

1. Ez az ajánlat az Azure-ügyfelekre vonatkozik, és nem támogatott a Azure Stackban:

   - Ha már telepített egy ezt a problémát bemutató példányt, használja a virtuális gép panelen a futtatási parancs funkciót a fent említett parancsok futtatásához. A futtatási parancs kiterjesztését nem érinti a Python 3,8-re való áttérés.

1. Ha új példányt telepít, és a kiépítési időpontban be kell állítania egy bővítményt, a fent említett csomagok telepítéséhez a **Cloud-init** felhasználói adatszolgáltatásokat kell használnia.

   Például a Python 2,7 esetén:

   ```python
   # create cloud-init config
   cat > cloudinitConfig.json <<EOF
   #cloud-config
   package_update: true
    
   runcmd:
   - sudo apt update
   - sudo apt install python-is-python2 
   EOF

   # create VM
   az vm create \
       --resource-group <resourceGroupName> \
       --name <vmName> \
       --image <Ubuntu 20.04 Image URN> \
       --admin-username azadmin \
       --ssh-key-value "<sshPubKey>" \
       --custom-data ./cloudinitConfig.json
   ```

1. Ha a szervezete házirend-rendszergazdája megállapítja, hogy a bővítmények nem telepíthetők virtuális gépekre, akkor letilthatja a bővítmények támogatását a kiépítési időszakban:

   - REST API

     A bővítmények letiltásához és engedélyezéséhez, ha egy virtuális gépet telepít a következő tulajdonsággal:

     ```python
       "osProfile": {
         "allowExtensionOperations": false
       },
     ```

## <a name="next-steps"></a>Következő lépések

További információért tekintse meg a további [alaprendszer-változásokat a 18,04 LTS – Python 3 óta](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default) .
