---
title: Egyéni méretezési szabályzatok használata Azure-beli virtuálisgép-méretezési csoportokkal
description: Ismerje meg, hogyan használhatja az egyéni méretezési szabályzatokat az Azure-beli virtuálisgép-méretezési csoportokkal, amelyek az automatikus skálázási konfiguráció használatával kezelik a példányszámot
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 9ca6310705d54d563aae746ab2dbfe6cb412e6a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92747793"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Egyéni méretezési szabályzatok használata Azure-beli virtuálisgép-méretezési csoportokkal

A virtuálisgép-méretezési csoport üzembe helyezése mérőszámok tömbje alapján méretezhető vagy méretezhető, beleértve a platformot és a felhasználó által definiált egyéni metrikákat is. Míg a méretezési csoport modellje alapján a kibővítő új virtuális gépeket hoz létre, a méretezési funkció hatással van a futó virtuális gépekre, amelyek különböző konfigurációkkal és/vagy funkciókkal rendelkeznek a méretezési csoport számítási feladatainak változásakor. 

A skálázási szabályzat funkció lehetővé teszi a felhasználók számára, hogy a virtuális gépek méretezési sorrendjét három méretezési konfigurációval konfigurálja: 

1. Alapértelmezett
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>Alapértelmezett méretezési házirend

Alapértelmezés szerint a virtuálisgép-méretezési csoport alkalmazza ezt a házirendet annak meghatározására, hogy mely példányok lesznek méretezve a-ben. Az *alapértelmezett* szabályzattal a virtuális gépek a következő sorrendben vannak kiválasztva a méretezéshez:

1. Virtuális gépek elosztása a rendelkezésre állási zónák között (ha a méretezési csoport a zóna-konfigurációban van telepítve)
2. Virtuális gépek elosztása a tartalék tartományok között (a legjobb megoldás)
3. A legmagasabb AZONOSÍTÓJÚ virtuális gép törlése

A felhasználóknak nem kell megadniuk a méretezési szabályzatot, ha csak az alapértelmezett sorrendet szeretnék követni.

Vegye figyelembe, hogy a rendelkezésre állási zónák vagy a tartalék tartományok közötti egyensúly nem helyezi át a példányokat a rendelkezésre állási zónák vagy a tartalék tartományok Az egyensúly a virtuális gépek nem kiegyensúlyozatlan rendelkezésre állási zónákból vagy tartalék tartományokból való törlésével érhető el, amíg a virtuális gépek eloszlása nem válik egyensúlyba.

### <a name="newestvm-scale-in-policy"></a>NewestVM skálázási szabályzat

Ez a szabályzat törli a legújabb létrehozott virtuális gépet a méretezési csoportból, a virtuális gépek elosztása a rendelkezésre állási zónák között (a zónák szerinti üzembe helyezések esetében). Ennek a szabályzatnak az engedélyezéséhez a virtuálisgép-méretezési csoport modelljében konfigurációs módosításra van szükség.

### <a name="oldestvm-scale-in-policy"></a>OldestVM skálázási szabályzat

Ez a szabályzat törli a legrégebben létrehozott virtuális gépet a méretezési csoportból, miután kiegyensúlyozta a virtuális gépeket a rendelkezésre állási zónák között (a zónák szerinti üzembe helyezések esetében). Ennek a szabályzatnak az engedélyezéséhez a virtuálisgép-méretezési csoport modelljében konfigurációs módosításra van szükség.

## <a name="enabling-scale-in-policy"></a>A skálázási szabályzat engedélyezése

A méretezési szabályzatok a virtuálisgép-méretezési csoport modelljében vannak meghatározva. Ahogy az a fenti szakaszban is látható, a "NewestVM" és a "OldestVM" szabályzat használatakor szükség van egy méretezési házirend-definícióra. A virtuálisgép-méretezési csoport automatikusan az "alapértelmezett" méretezési házirendet fogja használni, ha a méretezési csoport modelljében nem található skálázási házirend-definíció. 

A következő módokon lehet definiálni egy méretezési szabályzatot a virtuálisgép-méretezési csoport modelljében:

### <a name="azure-portal"></a>Azure Portal
 
Az alábbi lépések a méretezési szabályzatot határozzák meg új méretezési csoport létrehozásakor. 
 
1. Nyissa meg a **virtuálisgép-méretezési csoportokat**.
1. Válassza a **+ Hozzáadás** lehetőséget egy új méretezési csoport létrehozásához.
1. Nyissa meg a **skálázás** lapot. 
1. Keresse meg a **skálázási szabályzat** szakaszt.
1. Válasszon ki egy méretezési házirendet a legördülő menüből.
1. Ha elkészült az új méretezési csoport létrehozásával, válassza a **felülvizsgálat + létrehozás** gombot.

### <a name="using-api"></a>Az API használata

Hajtson végre egy PUT-t a virtuálisgép-méretezési csoporton a 2019-03-01-es API használatával:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Hozzon létre egy erőforráscsoportot, majd hozzon létre egy új méretezési csoportot a *OldestVM* beállítással.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Az alábbi példa egy méretezési szabályzatot hoz létre egy új méretezési csoport létrehozásakor. Először hozzon létre egy erőforráscsoportot, majd hozzon létre egy új méretezési csoportot *OldestVM*-ként. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Sablon használata

A sablon "tulajdonságok" területén adja hozzá a következőt:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

A fenti blokkok azt határozzák meg, hogy a virtuálisgép-méretezési csoport törli a legrégebbi virtuális gépet egy elosztott méretezési csoportból (Automatikus méretezéssel vagy manuális törléssel).

Ha egy virtuálisgép-méretezési csoport nem kiegyensúlyozott, a méretezési csoport először törli a virtuális gépeket a kiegyensúlyozatlan zóná (k) között. A kiegyensúlyozatlan zónákon belül a méretezési csoport a fent megadott méretezési házirend alapján határozza meg, hogy melyik virtuális gépet szeretné méretezni a alkalmazásban. Ebben az esetben a méretezési csoport a törölni kívánt zónában a legrégebbi virtuális gépet fogja kiválasztani.

A nem zónákra kiterjedő virtuálisgép-méretezési csoport esetében a házirend kiválasztja a legrégebbi virtuális gépet a törléshez a méretezési csoporton belül.

Ugyanez a folyamat érvényes a "NewestVM" használatakor a fenti méretezési házirendben.

## <a name="modifying-scale-in-policies"></a>Méretezési szabályzatok módosítása

A skálázási szabályzat módosítása a skálázási szabályzat alkalmazásával megegyező eljárást követi. Ha például a fenti példában a "OldestVM" és a "NewestVM" szabályzatot szeretné módosítani, ezt a következő módon teheti meg:

### <a name="azure-portal"></a>Azure Portal

Módosíthatja egy meglévő méretezési csoport skálázási szabályzatát a Azure Portal használatával. 
 
1. Egy meglévő virtuálisgép-méretezési csoportnál válassza a bal oldali menüben a **skálázás** elemet.
1. Válassza a **méretezési házirend** fület.
1. Válasszon ki egy méretezési házirendet a legördülő menüből.
1. Amikor elkészült, válassza a **Mentés** gombot. 

### <a name="using-api"></a>Az API használata

Hajtson végre egy PUT-t a virtuálisgép-méretezési csoporton a 2019-03-01-es API használatával:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Egy meglévő méretezési csoport skálázási házirendjének frissítése:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

A következő példa egy meglévő méretezési csoport skálázási házirendjének frissítését szemlélteti: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Sablon használata

A sablonban a "tulajdonságok" területen módosítsa a sablont az alábbiak szerint, és telepítse újra: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

Ugyanez a folyamat akkor is érvényes, ha úgy dönt, hogy a "NewestVM" értéket "default" vagy "OldestVM" értékre módosítja

## <a name="instance-protection-and-scale-in-policy"></a>A példányok védelme és a skálázási szabályzat

A virtuálisgép-méretezési csoportok két típusú [példány-védelmet](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)biztosítanak:

1. Védelem a méretezésből
2. Védelem a méretezési csoport műveleteiből

Egy védett virtuális gép nem törlődik egy méretezési művelettel, függetlenül az alkalmazott méretezési házirendtől. Ha például VM_0 (a méretezési csoport legrégebbi virtuális gépe) védett a méretezési szolgáltatásban, és a méretezési csoport "OldestVM" skálázási házirendje engedélyezve van, akkor a rendszer nem veszi figyelembe a méretezési csoportba VM_0, még akkor is, ha a méretezési csoport legrégebbi virtuális gépe. 

A felhasználó bármikor manuálisan törölheti a védett virtuális gépeket, függetlenül a méretezési csoporton engedélyezett skálázási házirendtől. 

## <a name="usage-examples"></a>Használati példák 

Az alábbi példák azt mutatják be, hogy egy virtuálisgép-méretezési csoport hogyan válassza ki a kibővíthető esemény bekövetkeztekor törölni kívánt virtuális gépeket. A legmagasabb példány-azonosítóval rendelkező virtuális gépeket a rendszer feltételezi, hogy a méretezési csoport legújabb virtuális gépei, a legkisebb példány-azonosítóval rendelkező virtuális gépek pedig a méretezési csoport legrégebbi virtuális gépei. 

### <a name="oldestvm-scale-in-policy"></a>OldestVM skálázási szabályzat

| Esemény                 | Példány-azonosítók a (1-ben  | Példány-azonosítók a Zone2-ben  | Példány-azonosítók a Zone3-ben  | Méretezés – kijelölés                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Kezdeti               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Skálázás              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | Válasszon a 1. zóna és a 2 között, még akkor is, ha 3. zóna a legrégebbi virtuális géppel rendelkezik. Törölje a 2. zóna VM2, mert ez az adott zónában a legrégebbi virtuális gép.   |
| Skálázás              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Válassza 1. zóna még akkor is, ha 3. zóna rendelkezik a legrégebbi virtuális géppel. Törölje a 1. zóna VM3, mert ez az adott zónában a legrégebbi virtuális gép.                  |
| Skálázás              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | A zónák egyensúlyban vannak. Törölje a VM1 3. zóna, mert a méretezési csoport legrégebbi virtuális gépe.                                               |
| Skálázás              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | 1. zóna és 2. zóna közül választhat. Törölje a VM4 1. zóna, mivel ez a legrégebbi virtuális gép a két zónában.                              |
| Skálázás              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | Válassza 2. zóna még akkor is, ha 1. zóna rendelkezik a legrégebbi virtuális géppel. Törölje a VM6 1. zóna, mert ez a zóna legrégebbi virtuális gépe.                    |
| Skálázás              | ***5***, 10            | 9, 11                  | 7, 8                   | A zónák egyensúlyban vannak. Törölje a VM5 1. zóna, mert a méretezési csoport legrégebbi virtuális gépe.                                                |

A nem zónákra kiterjedő virtuálisgép-méretezési csoportok esetében a szabályzat a legrégebben használt virtuális gépet választja a törléshez. A rendszer kihagyja a "védett" virtuális gépet törlésre.

### <a name="newestvm-scale-in-policy"></a>NewestVM skálázási szabályzat

| Esemény                 | Példány-azonosítók a (1-ben  | Példány-azonosítók a Zone2-ben  | Példány-azonosítók a Zone3-ben  | Méretezés – kijelölés                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Kezdeti               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Skálázás              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Válasszon a 1. zóna és a 2 között. Törölje a VM11 2. zóna, mert ez a legújabb virtuális gép a két zónán belül.                                |
| Skálázás              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Válassza a 1. zóna lehetőséget, mert több virtuális gépet tartalmaz, mint a többi két zónában. Törölje a VM10 a 1. zónaről, mert az adott zónában a legújabb virtuális gép.          |
| Skálázás              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | A zónák egyensúlyban vannak. 2. zóna VM9 törlése, mert ez a méretezési csoport legújabb virtuális gépe.                                                |
| Skálázás              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | 1. zóna és 3. zóna közül választhat. Törölje a VM8 3. zóna, mert ez az adott zónában lévő legújabb virtuális gép.                                      |
| Skálázás              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Válassza 1. zóna annak ellenére, hogy 3. zóna rendelkezik a legújabb virtuális géppel. Törölje a VM5 1. zóna, mert ez az adott zónában lévő legújabb virtuális gép.                    |
| Skálázás              | 3, 4                   | 2, 6                   | 1, ***7***             | A zónák egyensúlyban vannak. 3. zóna VM7 törlése, mert ez a méretezési csoport legújabb virtuális gépe.                                                |

A nem zónákra kiterjedő virtuálisgép-méretezési csoportok esetében a szabályzat a legújabb virtuális gépet választja a törléshez a méretezési csoporton belül. A rendszer kihagyja a "védett" virtuális gépet törlésre. 

## <a name="troubleshoot"></a>Hibaelhárítás

1. Nem sikerült engedélyezni a scaleInPolicy-t, ha "BadRequest" hibaüzenetet kap, amely azt jelzi, hogy "a" tulajdonság "típusú objektum nem találta a" scaleInPolicy "tagot, majd ellenőrizze a virtuálisgép-méretezési csoporthoz használt API-verziót. Ehhez a szolgáltatáshoz a 2019-03-01-es vagy újabb API-verzió szükséges.

2. A virtuális gépeknek a méretezéshez való helytelen kiválasztása a fenti példákra vonatkozik. Ha a virtuálisgép-méretezési csoport egy zónákra épülő telepítés, a skálázási szabályzatot először a kiegyensúlyozatlan zónákra alkalmazza a rendszer, majd a méretezési csoporton keresztül, ha a zóna kiegyensúlyozott. Ha a skálázási sorrend nem konzisztens a fenti példákkal, a hibaelhárításhoz hozzon létre egy lekérdezést a virtuálisgép-méretezési csoport csapatával.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [helyezheti üzembe az alkalmazást](virtual-machine-scale-sets-deploy-app.md) a virtuálisgép-méretezési csoportokban.
