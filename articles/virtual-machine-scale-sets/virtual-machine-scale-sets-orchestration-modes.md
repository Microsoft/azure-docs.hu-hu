---
title: Az Azure-beli virtuálisgép-méretezési csoportok előkészítési módjai
description: Ismerje meg, hogyan használhatók a rugalmas és egységes előkészítési módok a virtuálisgép-méretezési csoportokhoz az Azure-ban.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 3d9d9449e2a971a4247e507e0c022c8c5fb9956c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075406"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Előzetes verzió: az Azure-beli virtuálisgép-méretezési csoportok előkészítési módjai 

Virtual Machines méretezési csoportok biztosítják a platform által felügyelt virtuális gépek logikai csoportosítását. A méretezési csoportokkal létrehozhat egy virtuálisgép-konfigurációs modellt, automatikusan hozzáadhat vagy eltávolíthat további példányokat a CPU vagy a memória terhelése alapján, és automatikusan frissítheti a legújabb operációsrendszer-verzióra. A méretezési csoportok hagyományosan lehetővé teszik, hogy virtuális gépeket hozzon létre a méretezési csoport létrehozásakor megadott virtuálisgép-konfigurációs modellel, és a méretezési csoport csak a konfigurációs modell alapján implicit módon létrehozott virtuális gépeket tudja kezelni. 

A méretezési csoport előkészítési módjai lehetővé teszik, hogy jobban szabályozható legyen a méretezési csoport által kezelt virtuálisgép-példányok kezelése. 

> [!IMPORTANT]
> A skálázási mód a méretezési csoport létrehozásakor van meghatározva, és később nem módosítható és nem frissíthető.


## <a name="scale-sets-with-uniform-orchestration"></a>Méretezési csoportok egységes előkészítéssel
Nagy mennyiségű állapot nélküli számítási feladatokhoz optimalizált, azonos példányokkal.

Az egységes előkészítést biztosító virtuálisgép-méretezési csoportok virtuálisgép-profil vagy-sablon használatával méretezhetők fel a kívánt kapacitásra. Az egyes virtuálisgép-példányok felügyeletére és testre szabására is van lehetőség, így az egységes virtuálisgép-példányok használata is azonos. A virtuálisgép-méretezési csoport VM API-parancsain keresztül egyedi, egységes VM-példányok érhetők el. Az egyes példányok nem kompatibilisek a standard Azure IaaS VM API-parancsokkal, az Azure felügyeleti szolgáltatásaival, például a Azure Resource Manager erőforrás-címkézés RBAC engedélyeivel, Azure Backup vagy Azure Site Recovery. Az egységes előkészítés biztosítja a tartalék tartomány magas rendelkezésre állású garanciáit, ha az 100-nál kevesebb példánynál van konfigurálva. Az egységes előkészítés általánosan elérhető, és támogatja a méretezési csoport felügyeletének és előkészítésének teljes körét, beleértve a metrikák alapú automatikus skálázást, a példányok védelmét és az automatikus operációs rendszer frissítéseit. 


## <a name="scale-sets-with-flexible-orchestration"></a>Rugalmas előkészítést biztosító méretezési készletek 
Az azonos vagy több virtuálisgép-típussal rendelkező, magas rendelkezésre állást érhet el.

Rugalmas előkészítéssel az Azure egységes felhasználói élményt nyújt az Azure-beli virtuális gépek ökoszisztémáján belül. A rugalmas összehangolás magas rendelkezésre állású garanciákat biztosít (akár 1000 virtuális gép) azáltal, hogy a virtuális gépeket egy adott régióban vagy egy rendelkezésre állási zónán belül terjeszti. Ez lehetővé teszi, hogy kibővítse az alkalmazást, miközben a tartalék tartomány elkülönítésének fenntartása elengedhetetlen a kvórum-vagy állapot-nyilvántartó számítási feladatok futtatásához, beleértve a következőket:
- Kvórum-alapú számítási feladatok
- Adatbázisok Open-Source
- Állapot-nyilvántartó alkalmazások
- Magas rendelkezésre állást és nagy léptéket igénylő szolgáltatások
- A virtuálisgép-típusokat összekeverő szolgáltatások, illetve a helyszíni és az igény szerinti virtuális gépek együttes kihasználása
- Meglévő rendelkezésre állási csoport alkalmazásai  

> [!IMPORTANT]
> A rugalmas előkészítési módban lévő virtuálisgép-méretezési csoportok jelenleg nyilvános előzetes verzióban érhetők el. Az alábbiakban ismertetett nyilvános előzetes funkciók használatához egy opt-in eljárás szükséges.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, ezért éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Mi változott a rugalmas előkészítési móddal?
A rugalmas előkészítés egyik fő előnye, hogy a méretezési csoportba tartozó virtuális gépek helyett a standard szintű Azure IaaS-alapú virtuális gépeken is biztosítanak előkészítési funkciókat. Ez azt jelenti, hogy a szabványos virtuálisgép-API-kat a rugalmas előkészítési példányok kezeléséhez használhatja, a virtuálisgép-méretezési csoportba tartozó VM API-k egységes előkészítéssel való használata helyett. Az előzetes verzió ideje alatt a példányok kezelése a rugalmas előkészítés és az egységes előkészítés között több különbséget is mutat. Általánosságban azt javasoljuk, hogy ha lehetséges, használja a standard Azure IaaS VM API-kat. Ebben a szakaszban a virtuálisgép-példányok rugalmas előkészítéssel történő kezelésével kapcsolatos ajánlott eljárásokat mutatjuk be.  

### <a name="assign-fault-domain-during-vm-creation"></a>Tartalék tartomány kiosztása a virtuális gépek létrehozása során
Megadhatja a tartalék tartományok számát a rugalmas előkészítési méretezési csoport számára. Alapértelmezés szerint, ha egy virtuális gépet egy rugalmas méretezési csoportba ad hozzá, az Azure egyenletesen osztja el a példányokat a tartalék tartományok között. Habár azt javasoljuk, hogy az Azure-t a tartalék tartományhoz rendelje, speciális vagy hibaelhárítási helyzetekben felülbírálhatja ezt az alapértelmezett viselkedést, és megadhatja azt a tartalék tartományt, ahol a példány el fog szállni.

```azurecli-interactive 
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Példányok elnevezése 
Amikor létrehoz egy virtuális gépet, és hozzáadja azt egy rugalmas méretezési csoporthoz, a példányok neveinek teljes hozzáférése az Azure elnevezési konvenció szabályain belül történik. Ha a rendszer automatikusan hozzáadja a virtuális gépeket a méretezési csoporthoz az automatikus skálázás használatával, egy előtagot ad meg, és az Azure egy egyedi számot fűz hozzá a név végéhez.

### <a name="query-instances-for-power-state"></a>A Power State lekérdezési példányai
Az előnyben részesített módszer az Azure Resource Graph használata egy virtuálisgép-méretezési csoport összes virtuális gépe lekérdezéséhez. Az Azure Resource Graph hatékony lekérdezési képességeket biztosít az Azure-erőforrások számára a nagy léptékű előfizetések között. 

``` 
| where type =~ 'Microsoft.Compute/virtualMachines' 
| where properties.virtualMachineScaleSet contains "demo" 
| extend powerState = properties.extended.instanceView.powerState.code 
| project name, resourceGroup, location, powerState 
| order by resourceGroup desc, name desc 
```

Az erőforrások [Azure Resource Graph](../governance/resource-graph/overview.md) használatával történő lekérdezése kényelmes és hatékony módja az Azure-erőforrások lekérdezésének, valamint az erőforrás-szolgáltatóhoz való API-hívások minimalizálásának. Az Azure Resource Graph egy végül konzisztens gyorsítótár, amelyben az új vagy frissített erőforrások nem jelenhetnek meg legfeljebb 60 másodpercig. A következőket teheti:
- Egy erőforráscsoport vagy előfizetés virtuális gépei listázása.
- A Kibontás lehetőséggel lekérheti a példány nézetet (tartalék tartomány-hozzárendelés, energiaellátás és kiépítési állapotok) az előfizetésben lévő összes virtuális géphez.
- A virtuális gépek beolvasása API-val és parancsokkal egyetlen példány modelljének és példányának nézetét szerezheti be.

### <a name="scale-sets-vm-batch-operations"></a>Méretezési csoportok VM batch-műveletei
A virtuálisgép-méretezési csoport VM API-jai helyett a standard VM-parancsok használatával elindíthatja, leállíthatja, újraindíthatja és törölheti a példányokat. A virtuálisgép-méretezési csoport VM batch-műveletei (az összes elindítása, az összes leállítása, az összes alaphelyzetbe állítás stb.) nem használhatók rugalmas előkészítési módban. 

### <a name="monitor-application-health"></a>Alkalmazásállapot monitorozása 
Az alkalmazás állapotának figyelése lehetővé teszi, hogy az alkalmazás egy szívveréssel biztosítsa az Azure-nak, hogy az alkalmazás kifogástalan vagy sérült legyen. Az Azure képes automatikusan helyettesíteni a nem kifogástalan virtuálisgép-példányokat. A rugalmas méretezési csoport példányai esetében telepítenie és konfigurálnia kell az alkalmazás állapota bővítményt a virtuális gépen. Az egységes méretezési csoport példányai esetében használhatja az alkalmazás állapotát vagy az állapot mérését Azure Load Balancer egyéni állapot-mintavételsel. 

### <a name="list-scale-sets-vm-api-changes"></a>Méretezési csoportok VM API-változásainak listázása 
Virtual Machine Scale Sets lehetővé teszi a méretezési csoporthoz tartozó példányok listázását. A rugalmas összehangolás Virtual Machine Scale Sets a virtuálisgép-parancs lista a méretezési csoportok virtuálisgép-azonosítóinak listáját tartalmazza. Ezután meghívhatja a GET Virtual Machine Scale Sets VM-parancsokat, hogy további részleteket kapjon arról, hogyan működik a méretezési csoport a virtuálisgép-példánnyal. A virtuális gép összes adatának beszerzéséhez használja a standard GET VM-parancsokat vagy az [Azure Resource Graphot](../governance/resource-graph/overview.md). 

### <a name="retrieve-boot-diagnostics-data"></a>Rendszerindítási diagnosztikai adatlekérdezés 
A standard VM API-k és parancsok használatával lekérheti a példány rendszerindítási diagnosztikai adatait és a képernyőképeket. A virtuálisgép-rendszerindítási diagnosztikai API-k és parancsok Virtual Machine Scale Sets nem használhatók rugalmas előkészítési módú példányokkal.

### <a name="vm-extensions"></a>Virtuálisgép-bővítmények 
A standard szintű virtuális gépekhez célként megadott bővítmények használata az egységes előkészítési üzemmódú példányok helyett.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Rugalmas, egységes és rendelkezésre állási készletek összehasonlítása 
A következő táblázat összehasonlítja a rugalmas előkészítési módot, az egységes előkészítési módot és a rendelkezésre állási csoportokat a funkcióik alapján.

| Szolgáltatás | Rugalmas előkészítéssel támogatott (előzetes verzió) | Egységes összehangolás (általános rendelkezésre állás) által támogatott | A AvSets által támogatott (általánosan elérhető) |
|-|-|-|-|
|         Virtuális gép típusa  | Standard szintű Azure IaaS VM (Microsoft. számítási/virtualmachines)  | Meghatározott virtuális gépek méretezése (Microsoft. számítási/virtualmachinescalesets/virtualmachines)  | Standard szintű Azure IaaS VM (Microsoft. számítási/virtualmachines)  |
|         Támogatott SKU-i  |            D sorozat, E sorozat, F sorozat, sorozat, B sorozat, Intel, AMD  |            Összes SKU  |            Összes SKU  |
|         Rendelkezésre állási zónák  |            Egyetlen rendelkezésre állási zónában lévő összes példány megadása opcionálisan |            Példányok meghatározása 1, 2 vagy 3 rendelkezésre állási zónán belül  |            Nem támogatott  |
|         Teljes hozzáférés a virtuális gépekhez, hálózati adapterekhez, lemezekhez  |            Yes  |            Korlátozott vezérlés a virtuálisgép-méretezési csoportokkal rendelkező VM API-val  |            Yes  |
|         Automatikus skálázás  |            Nem  |            Igen  |            Nem  |
|         Virtuális gép kiosztása egy adott tartalék tartományhoz  |            Igen  |             Nem   |            Nem  |
|         Hálózati adapterek és lemezek eltávolítása virtuálisgép-példányok törlésekor  |            Nem  |            Igen  |            Nem  |
|         Frissítési szabályzat (VM-méretezési csoportok) |            No  |            Automatikus, működés közbeni manuális  |            N/A  |
|         Operációs rendszer automatikus frissítései (VM-méretezési csoportok) |            Nem  |            Igen  |            N/A  |
|         A vendég biztonsági javításában  |            Igen  |            Nem  |            Igen  |
|         Értesítések leállítása (virtuálisgép-méretezési csoportok) |            Nem  |            Igen  |            N/A  |
|         Példány javítása (virtuálisgép-méretezési csoportok) |            Nem  |            Igen   |            N/A  |
|         Gyorsított hálózatkezelés  |            Igen  |            Igen  |            Yes  |
|         Helyszíni példányok és díjszabás   |            Igen, mind a helyszíni, mind a Normál prioritású példányok használhatók  |            Igen, a példányoknak mind a helyszínen, mind pedig a szokásosnak kell lenniük  |            Nem, csak normál prioritású példányok  |
|         Vegyes operációs rendszerek  |            Igen, a Linux és a Windows ugyanazon a rugalmas méretezési csoporton belül lehet |            Nem, a példányok azonos operációs rendszer  |               Igen, a Linux és a Windows ugyanazon a rugalmas méretezési csoporton belül lehet |
|         Alkalmazás állapotának figyelése  |            Alkalmazás állapota bővítmény  |            Az alkalmazás állapota vagy az Azure Load Balancer mintavétele  |            Alkalmazás állapota bővítmény  |
|         UltraSSD-lemezek   |            Yes  |            Igen, csak a zónákon üzemelő példányok esetében  |            No  |
|         InfiniBand   |            No  |            Igen, csak egy elhelyezési csoport  |            Yes  |
|         írásgyorsító   |            Nem  |            Igen  |            Yes  |
|         Proximity elhelyezési csoportok   |            Igen  |            Igen  |            Yes  |
|         Dedikált Azure-gazdagépek   |            Nem  |            Igen  |            Yes  |
|         Alapszintű SLB   |            Nem  |            Igen  |            Yes  |
|         Standard SKU Azure Load Balancer |            Igen  |            Igen  |            Yes  |
|         Application Gateway  |            Nem  |            Igen  |            Yes  |
|         Karbantartási ellenőrzés   |            Nem  |            Igen  |            Yes  |
|         Készletben lévő virtuális gépek listázása  |            Igen  |            Yes  |            Igen, virtuális gépek listázása a AvSet-ben  |
|         Azure-riasztások  |            Nem  |            Igen  |            Yes  |
|         VM-ismeretek  |            Nem  |            Igen  |            Yes  |
|         Azure Backup  |            Igen  |            Igen  |            Yes  |
|         Azure Site Recovery  |     Nem  |            Nem  |            Igen  |
|         Meglévő virtuális gép hozzáadása vagy eltávolítása a csoportba  |            Nem  |            Nem  |            Nem  | 


## <a name="register-for-flexible-orchestration-mode"></a>Regisztráció rugalmas előkészítési módra
A virtuálisgép-méretezési csoportok rugalmas előkészítési módban való üzembe helyezéséhez először regisztrálnia kell az előfizetését az előzetes verzió szolgáltatáshoz. A regisztráció elvégzése több percet is igénybe vehet. A regisztráláshoz a következő Azure PowerShell vagy Azure CLI-parancsok használhatók.

### <a name="azure-portal"></a>Azure Portal
Navigáljon a Részletek lapra ahhoz az előfizetéshez, amelyhez létre szeretné hozni a méretezési csoport rugalmas előkészítési módot, majd válassza a menü előnézet funkciók elemét. Válassza ki az engedélyezni kívánt két Orchestrator-funkciót: _VMOrchestratorSingleFD_ és _VMOrchestratorMultiFD_, majd kattintson a regisztráció gombra. A szolgáltatás regisztrálása akár 15 percet is igénybe vehet.

![Funkció regisztrálása.](https://user-images.githubusercontent.com/157768/110361543-04d95880-7ff5-11eb-91a7-2e98f4112ae0.png)

Ha a szolgáltatások regisztrálva lettek az előfizetéséhez, fejezze be a beléptetési folyamatot a számítási erőforrás-szolgáltatóra való váltás propagálásával. Navigáljon az előfizetés erőforrás-szolgáltatók lapjára, válassza a Microsoft. számítás lehetőséget, majd kattintson az újbóli regisztrálás gombra.

![Regisztráció újra](https://user-images.githubusercontent.com/157768/110362176-cd1ee080-7ff5-11eb-8cc8-36aa967e267a.png)


### <a name="azure-powershell"></a>Azure PowerShell 
Az előfizetés előnézetének engedélyezéséhez használja a [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancsmagot. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

A szolgáltatás regisztrálása akár 15 percet is igénybe vehet. A regisztráció állapotának ellenõrzése: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

Miután regisztrálta a szolgáltatást az előfizetéséhez, fejezze be a beléptetési folyamatot a számítási erőforrás-szolgáltatóra történő váltás propagálásával. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>Azure CLI 2.0 
Az az [Feature Register](/cli/azure/feature#az-feature-register) paranccsal engedélyezheti az előfizetésének előnézetét. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

A szolgáltatás regisztrálása akár 15 percet is igénybe vehet. A regisztráció állapotának ellenõrzése: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

Miután regisztrálta a szolgáltatást az előfizetéséhez, fejezze be a beléptetési folyamatot a számítási erőforrás-szolgáltatóra történő váltás propagálásával. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Ismerkedés a rugalmas előkészítési móddal

A méretezési csoportokhoz a Azure Portal, az Azure CLI, a Terraform vagy a REST API segítségével rugalmasan összehangoló üzemmódot érhet el.

### <a name="azure-portal"></a>Azure Portal

Hozzon létre egy virtuálisgép-méretezési készletet rugalmas hangolási módban a Azure Portalon keresztül.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).
1. A keresési sávban keresse meg és válassza ki a **virtuálisgép-méretezési** csoportok elemet. 
1. A **virtuálisgép-méretezési** csoportok lapon válassza a **Létrehozás** lehetőséget.
1. A **virtuálisgép-méretezési csoport létrehozása** lapon tekintse meg az **előkészítési szakaszt.**
1. A előkészítési **mód** beállításnál válassza a **rugalmas** lehetőséget.
1. Állítsa be a tartalék **tartományok darabszámát**.
1. Állítsa be a méretezési csoport létrehozását. A méretezési csoport létrehozásával kapcsolatos további információkért lásd: [méretezési csoport létrehozása a Azure Portalban](quick-create-portal.md#create-virtual-machine-scale-set) .

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Skálázási mód a portálon méretezési csoport létrehozásakor":::

Ezután adjon hozzá egy virtuális gépet a méretezési csoporthoz rugalmas előkészítési módban.

1. A keresési sávban keresse meg és válassza ki a **virtuális gépek** elemet.
1. A **virtuális gépek** lapon válassza a **Hozzáadás** lehetőséget.
1. Az **alapvető beállítások** lapon tekintse meg a **példány részletei** szakaszt.
1. Adja hozzá a virtuális gépet a méretezési csoporthoz rugalmas előkészítési módban a méretezési csoportnak a **rendelkezésre állási beállításokban** való kiválasztásával. A virtuális gépet egy adott régióban, zónában és erőforráscsoporthoz is hozzáadhatja egy méretezési csoporthoz.
1. Fejezze be a virtuális gép létrehozását. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="Virtuális gép felvétele a rugalmas előkészítési mód méretezési csoportba":::


### <a name="azure-cli-20"></a>Azure CLI 2.0
Rugalmas virtuálisgép-méretezési csoport létrehozása az Azure CLI-vel. Az alábbi példa egy olyan rugalmas méretezési csoport létrehozását mutatja be, amelyben a tartalék tartományok száma 3, a virtuális gép létrejön, majd a rugalmas méretezési csoportba kerül. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Hozzon létre egy rugalmas virtuálisgép-méretezési készletet a Terraform. Ehhez a folyamathoz a **Terraform Azurerm Provider v 2.15.0** vagy újabb verzió szükséges. Vegye figyelembe a következő paramétereket:
- Ha nincs megadva zóna, `platform_fault_domain_count` a régiótól függően 1, 2 vagy 3 lehet.
- Ha meg van adva egy zóna, `the fault domain count` 1 lehet.
- `single_placement_group` a paraméternek `false` rugalmas virtuálisgép-méretezési csoportokhoz kell tartoznia.
- Ha regionális telepítést hajt végre, nem kell megadnia a következőt: `zones` .

```terraform
resource "azurerm orchestrated_virtual_machine_scale_set" "tf_vmssflex" {
name = "tf_vmssflex"
location = azurerm_resource_group.myterraformgroup.location
resource_group_name = azurerm_resource_group.myterraformgroup.name
platform_fault_domain_count = 1
single_placement_group = false
zones = ["1"]
}
```


### <a name="rest-api"></a>REST API

1. Hozzon létre egy üres méretezési készletet. A következő paraméterek szükségesek:
    - API-verzió 2019-12-01 (vagy újabb) 
    - Egy elhelyezési csoportnak `false` rugalmas méretezési csoport létrehozásakor kell lennie

    ```json
    {
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('virtualMachineScaleSetName')]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "properties": {
        "singlePlacementGroup": false,
        "platformFaultDomainCount": "[parameters('virtualMachineScaleSetPlatformFaultDomainCount')]"
        },
    "zones": "[variables('selectedZone')]"
    }
    ```

2. Adja hozzá a virtuális gépeket a méretezési csoporthoz.
    1. Rendelje hozzá a `virtualMachineScaleSet` tulajdonságot a korábban létrehozott méretezési készlethez. A `virtualMachineScaleSet` virtuális gép létrehozásakor meg kell adnia a tulajdonságot. 
    1. Több virtuális gép egyidejű létrehozásához használhatja a **copy ()** Azure Resource Manager template függvényt. Lásd: [erőforrás-iteráció](../azure-resource-manager/templates/copy-resources.md#iteration-for-a-child-resource) Azure Resource Manager-sablonokban. 

    ```json
    {
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(parameters('virtualMachineNamePrefix'), copyIndex(1))]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "copy": {
        "name": "VMcopy",
        "count": "[parameters('virtualMachineCount')]"
        },
    "dependsOn": [
        "
        [resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]",
        "
        [resourceID('Microsoft.Storage/storageAccounts', variables('diagnosticsStorageAccountName'))]",
        "
        [resourceID('Microsoft.Network/networkInterfaces', concat(parameters('virtualMachineNamePrefix'), copyIndex(1), '-NIC1'))]"
        ],
    "properties": {
        "virtualMachineScaleSet": {
            "id": "[resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]"
        }
    }
    ```

Tekintse meg az [Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) gyors üzembe helyezésének teljes példáját.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Milyen mértékben támogatja a rugalmas előkészítést?**

A méretezési csoportokhoz akár 1000 virtuális gépet is hozzáadhat rugalmas előkészítési módban.

**Hogyan hasonlítja össze a rendelkezésre állást rugalmas előkészítéssel a rendelkezésre állási csoportokkal vagy az egységes előkészítéssel?**

| Rendelkezésre állási attribútum  | Rugalmas előkészítés  | Egységes előkészítés  | Rendelkezésre állási csoportok  |
|-|-|-|-|
| Üzembe helyezés rendelkezésre állási zónák között  | Nem  | Igen  | Nem  |
| Tartalék tartomány rendelkezésre állási garanciái a régión belül  | Igen, akár 1000 példány is elosztható akár 3 tartalék tartomány között a régióban. A tartalék tartományok maximális száma régiónként eltérő  | Igen, legfeljebb 100 példány  | Igen, legfeljebb 200 példány  |
| Elhelyezési csoportok  | A rugalmas mód mindig több elhelyezési csoportot használ (singlePlacementGroup = false)  | Egyetlen elhelyezési csoportot vagy több elhelyezési csoportot is választhat | N/A  |
| Frissítési tartományok  | Nincs, a karbantartási vagy a gazdagép frissítései a tartalék tartomány szerint vannak végrehajtva.  | Legfeljebb 5 frissítési tartomány  | Legfeljebb 20 frissítési tartomány  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Rugalmas előkészítéssel rendelkező méretezési csoportok – problémamegoldás
A hibaelhárítási forgatókönyvnek megfelelő megoldás megkeresése. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**OK:** Az előfizetés nincs regisztrálva a rugalmas előkészítési mód nyilvános előzetes verziójához. 

**Megoldás:** A következő útmutatást követve regisztrálhat a rugalmas előkészítési mód nyilvános előzetes verziójára. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**OK:** A `platformFaultDomainCount` paraméter érvénytelen a kiválasztott régióhoz vagy zónához. 

**Megoldás:** Érvényes értéket kell választania `platformFaultDomainCount` . A zónákon alapuló központi telepítések esetében a maximális `platformFaultDomainCount` érték 1. Olyan regionális központi telepítések esetén, ahol nincs megadva zóna, a maximális eltérés a `platformFaultDomainCount` régiótól függően változhat. Tekintse meg a [virtuális gépek rendelkezésre állásának kezelése a parancsfájlok](../virtual-machines/availability.md) számára című témakört, amely meghatározza a tartalék tartományok maximális számát régiónként. 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**OK:** Egy vagy több virtuális géphez társított rugalmas előkészítési módban lévő méretezési csoport törlésére tett kísérlet. 

**Megoldás:** Törölje a méretezési csoporthoz társított összes virtuális gépet rugalmas előkészítési módban, majd törölje a méretezési készletet.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**OK:** Az előfizetés regisztrálva van a rugalmas előkészítési mód előzetes verziójában; a paraméter azonban `singlePlacementGroup` *igaz* értékre van állítva. 

**Megoldás:** A `singlePlacementGroup` *értéket hamis* értékre kell állítani. 


## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Megtudhatja, hogyan helyezheti üzembe az alkalmazást a méretezési csoporton.](virtual-machine-scale-sets-deploy-app.md)
