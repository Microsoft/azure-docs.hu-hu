---
title: Megosztott készletek
titleSuffix: Azure Data Science Virtual Machine
description: Megtudhatja, hogyan hozhat létre & az adatelemzési Virtual Machines (Dsvm) megosztott készletét megosztott erőforrásként üzembe helyezni egy csapat számára.
keywords: Deep learning, AI, adatelemzési eszközök, adatelemzési virtuális gép, térinformatikai elemzés, csoportos adatelemzési folyamat
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: a118d5a3e716a80bda21ffe82a8cefd1da1202f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519711"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Adatelemzési Virtual Machines megosztott készletének létrehozása

Ebből a cikkből megtudhatja, hogyan hozhat létre az adatelemzési Virtual Machines (Dsvm) megosztott készletét egy csapat számára. A megosztott készlet használatának előnyei közé tartozik a jobb erőforrás-használat, a könnyebb megosztás és együttműködés, valamint a DSVM erőforrások hatékonyabb kezelése.

Számos módszert és technológiát használhat a Dsvm készletének létrehozásához. Ez a cikk az interaktív virtuális gépek (VM-EK) készleteit tárgyalja. Egy alternatív felügyelt számítási infrastruktúra Azure Machine Learning számítás. További információt a [számítási fürt létrehozása](../how-to-create-attach-compute-cluster.md)című témakörben talál.

## <a name="interactive-vm-pool"></a>Interaktív virtuálisgép-készlet

A teljes AI/adatelemzési csapat által megosztott interaktív virtuális gépek készlete lehetővé teszi a felhasználók számára, hogy a DSVM elérhető példányára jelentkezzenek be ahelyett, hogy dedikált példányt kellene létrehoznia az egyes felhasználók számára. Ez a beállítás lehetővé teszi az erőforrások jobb rendelkezésre állását és hatékonyabb kihasználását.

Az [Azure virtuálisgép-méretezési](../../virtual-machine-scale-sets/index.yml) csoportok technológiájának használatával interaktív virtuálisgép-készletet hozhat létre. A méretezési csoportok segítségével azonos, elosztott terhelésű és automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti.

A felhasználó bejelentkezik a fő készlet IP-címére vagy DNS-címére. A méretezési csoport automatikusan átirányítja a munkamenetet a méretezési csoport egy elérhető DSVM. Mivel a felhasználók egységes és ismerős környezetet kívánnak, függetlenül attól, hogy milyen virtuális gépről jelentkeznek be, a méretezési csoport minden példánya megosztott hálózati meghajtót csatlakoztat, például egy Azure Files megosztást vagy egy hálózati fájlrendszert (NFS-megosztást). A felhasználó megosztott munkaterülete általában az egyes példányokra csatlakoztatott megosztott fájlon marad.

Megtalálhatja a [githubon](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json)található Ubuntu DSVM-példányokkal rendelkező méretezési csoport létrehozására szolgáló minta Azure Resource Manager sablont. Ugyanazon a helyen talál egy [mintát a Azure Resource Manager](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) sablonhoz.

A méretezési csoport a Azure Resource Manager sablon alapján hozható létre az Azure CLI-ben található paraméterérték értékének megadásával:

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az deployment group create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

Az előző parancsok feltételezik, hogy rendelkezik az alábbiakkal:

* A paraméter fájljának másolata a méretezési csoport példányához megadott értékekkel.
* A virtuálisgép-példányok száma.
* A Azure Files megosztásra mutató mutatók.
* A Storage-fiók hitelesítő adatai, amelyek az egyes virtuális gépekre lesznek csatlakoztatva.

A paraméter fájlját helyileg kell hivatkozni a parancsokban. A paramétereket beágyazottként is átadhatja, vagy rákérdezheti őket a parancsfájlban.  

Az előző sablon lehetővé teszi, hogy az SSH és a JupyterHub port az előtér-méretezési csoportból az Ubuntu-Dsvm háttér-készlete legyen. Felhasználóként a virtuális gépre egy Secure Shell (SSH) vagy a JupyterHub normál módon jelentkezhet be. Mivel a virtuálisgép-példányok dinamikusan méretezhetők vagy leállíthatók, minden állapotot a csatlakoztatott Azure Files-megosztásba kell menteni. Ugyanezt a megközelítést használhatja a Windows Dsvm készletének létrehozásához.

A [Azure Files megosztást csatlakoztató szkript](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) a GitHub Azure DataScienceVM adattárában is elérhető. A parancsfájl a paraméter fájljának megadott csatlakoztatási pontján csatlakoztatja a Azure Files megosztást. A parancsfájl emellett a kezdeti felhasználó kezdőkönyvtárának hivatkozásait is létrehozza a csatlakoztatott meghajtóra. A Azure Files-megosztásban lévő felhasználó-specifikus jegyzetfüzet-könyvtár nem kapcsolódik a `$HOME/notebooks/remote` címtárhoz, így a felhasználók hozzáférhetnek, futtathatnak és menthetik a Jupyter-jegyzetfüzeteket. Ugyanezt az egyezményt akkor használhatja, ha további felhasználókat hoz létre a virtuális gépen, hogy az egyes felhasználók Jupyter-munkaterületét az Azure Files-megosztásra irányítsa.

A virtuálisgép-méretezési csoportok támogatják az automatikus skálázást. Szabályokat állíthat be a további példányok létrehozásához és a példányok méretezési idejéhez. Ha például a virtuális gépeket egyáltalán nem használják, a Felhőbeli hardver használati költségeire csökkentheti a példányok méretét. A virtuálisgép-méretezési csoportok dokumentációs oldalain részletes útmutatást talál az automatikus [skálázáshoz](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

## <a name="next-steps"></a>Következő lépések

* [Közös identitás beállítása](dsvm-common-identity.md)
* [Hitelesítő adatok biztonságos tárolása a felhőalapú erőforrások eléréséhez](dsvm-secure-access-keys.md)