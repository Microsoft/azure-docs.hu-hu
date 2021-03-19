---
title: Egyéni és Azure-Virtual Machines
description: Az egyéni adatok és a Cloud-Init használatának részletei az Azure-on Virtual Machines
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 2924caaac5fb8c512100d9e897f7f153af9a3b3e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87284914"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Egyéni adatkezelés és Cloud-Init az Azure-on Virtual Machines

Előfordulhat, hogy egy parancsfájlt vagy más metaadatokat kell beszúrnia egy Microsoft Azure virtuális gépre a kiépítési időpontban.  Más felhőkben ezt a koncepciót gyakran felhasználói adatként is nevezik.  Microsoft Azure az egyéni adatszolgáltatáshoz hasonló funkciót tartalmaz. 

A rendszer csak az első rendszerindítás/kezdeti beállítás során bocsátja elérhetővé a virtuális gép számára, ezt "kiépítés" hívjuk. A kiépítés az a folyamat, amelyben a virtuális gép paramétereket hoz létre (például állomásnév, Felhasználónév, jelszó, tanúsítványok, egyéni adatok, kulcsok stb.) elérhetővé válnak a virtuális gép számára, és a kiépítési ügynök feldolgozza azokat, például a [Linux-ügynököt](./extensions/agent-linux.md) és a [Cloud-init-](./linux/using-cloud-init.md#troubleshooting-cloud-init)t. 


## <a name="passing-custom-data-to-the-vm"></a>Egyéni adattovábbítás a virtuális gépre
Az egyéni adatértékek használatához Base64 kódolást kell végrehajtania az API-ba való továbbítás előtt, kivéve, ha olyan CLI-eszközt használ, amely az Ön számára, például az az parancssori felület. A méret nem lehet nagyobb, mint 64 KB.

A CLI-ben átadhatja az egyéni adatait fájlként, és Base64-re lesz konvertálva.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

Azure Resource Manager (ARM)-ben egy Base64- [függvény](../azure-resource-manager/templates/template-functions-string.md#base64)van.

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>Egyéni adatfeldolgozás
A virtuális gépekre telepített kiépítési ügynökök a platformmal és a fájlrendszerre helyezve. 

### <a name="windows"></a>Windows
Az egyéni adatfájlok bináris fájlként kerülnek a *%SYSTEMDRIVE%\AzureData\CustomData.bin* , de a rendszer nem dolgozza fel őket. Ha szeretné feldolgozni ezt a fájlt, létre kell hoznia egy egyéni rendszerképet, és kódot kell írnia a CustomData. bin feldolgozásához.

### <a name="linux"></a>Linux  
A Linux operációs rendszereken a ovf-env.xml fájl segítségével átadja a virtuális gép egyéni adatfájljait, amelyet a rendszer a */var/lib/waagent* könyvtárba másol a kiépítés során.  A Microsoft Azure Linux-ügynök újabb verziói a Base64 kódolású */var/lib/waagent/CustomData* is átmásolják a kényelem érdekében.

Az Azure jelenleg két kiépítési ügynököt támogat:
* Linux-ügynök – alapértelmezés szerint az ügynök nem dolgozza fel az egyéni adatok feldolgozását, ezért az általa engedélyezett egyéni rendszerképet kell létrehoznia. A megfelelő beállítások a [dokumentációban](https://github.com/Azure/WALinuxAgent#configuration) a következők:
    * Kiépítés. DecodeCustomData
    * Provisioning.ExecuteCustomData

Ha engedélyezi az egyéni adatértékeket, és végrehajt egy parancsfájlt, akkor a rendszer késlelteti a virtuális gép jelentéskészítését, amely kész, vagy a kiépítés sikeres volt, amíg a parancsfájl be nem fejeződik. Ha a parancsfájl mérete meghaladja a virtuális gépek teljes kiépítési idejét 40 percnél, a virtuális gép létrehozása sikertelen lesz. Vegye figyelembe, hogy ha a parancsfájl végrehajtása nem sikerül, vagy a végrehajtása során hiba lép fel, a rendszer nem végzetes kiépítési hibát jelez, ezért létre kell hoznia egy értesítési útvonalat, amely riasztást küld a parancsfájl befejezési állapotáról.

Az egyéni adatvégrehajtás hibakereséséhez tekintse át a */var/log/waagent.log*

* Cloud-init – alapértelmezés szerint a Cloud-init alapértelmezés szerint dolgozza fel az egyéni értékeket, így [több formátumot](https://cloudinit.readthedocs.io/en/latest/topics/format.html) is elfogad, például a Cloud-init konfigurációt, a parancsfájlokat stb. Hasonlóan a Linux-ügynökhöz, amikor a Cloud-init dolgozza fel az egyéni adatértékeket. Ha hibák léptek fel a konfigurációs feldolgozás vagy a parancsfájlok végrehajtása során, nem minősül végzetes kiépítési hibanek, és létre kell hoznia egy értesítési útvonalat, amely riasztást küld a parancsfájl befejezési állapotáról. A Linux-ügynöktől eltérően azonban a Cloud-init nem várja meg a felhasználói egyéni adatkonfigurációk befejeződését, mielőtt a virtuális gép készen áll a platformra történő jelentéskészítésre. Az Azure-beli Cloud-init szolgáltatással kapcsolatos további információkért tekintse át a [dokumentációt](./linux/using-cloud-init.md).


Az egyéni adatvégrehajtás hibaelhárításához tekintse át a hibaelhárítási [dokumentációt](./linux/using-cloud-init.md#troubleshooting-cloud-init).


## <a name="faq"></a>GYIK
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>A virtuális gép létrehozása után frissíthetem az egyéni adatértékeket?
Egyetlen virtuális gép esetében a virtuálisgép-modellben lévő egyéni adat nem frissíthető, de a VMSS esetében a VMSS egyéni adatait [REST API](/rest/api/compute/virtualmachinescalesets/update) (PS vagy az CLI-ügyfelek esetében nem alkalmazható) használatával frissítheti. A VMSS modellben lévő egyéni adattípusok frissítésekor:
* A VMSS lévő meglévő példányok nem kapják meg az egyéni adatok frissítését, csak addig, amíg el nem távolítják őket.
* A frissített VMSS lévő meglévő példányok nem kapják meg a frissített egyéni adatértékeket.
* Az új példányok megkapják az új egyéni adatértékeket.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Bizalmas értékeket is elhelyezek az egyéni adatokban?
Javasoljuk, hogy a bizalmas adatokat **ne** tárolja egyéni adatokban. További információ: [Azure Security and encryption – ajánlott eljárások](../security/fundamentals/data-encryption-best-practices.md).


### <a name="is-custom-data-made-available-in-imds"></a>A IMDS-ben elérhetővé tett egyéni adathalmazok?
Nem, ez a szolgáltatás jelenleg nem érhető el.
