---
title: Vezénylési módok virtuálisgép-méretezési készletekhez az Azure-ban
description: Megtudhatja, hogyan használhatja a rugalmas és egységes vezénylési módokat a virtuálisgép-méretezési készletekhez az Azure-ban.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 72e36a942eeaea00699f346db99a7ca3503495da
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481650"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Előzetes verzió: Vezénylési módok virtuálisgép-méretezési készletekhez az Azure-ban 

Virtual Machines méretezési csoportok a platform által felügyelt virtuális gépek logikai csoportosítását biztosítják. A méretezési csoportokkal létrehozhat egy virtuálisgép-konfigurációs modellt, automatikusan hozzáadhat vagy eltávolíthat további példányokat a processzor- vagy memóriaterhelés alapján, és automatikusan frissít a legújabb operációsrendszer-verzióra. Hagyományosan a méretezési csoportokkal virtuális gépeket hozhat létre a méretezési csoport létrehozásakor megadott virtuálisgép-konfigurációs modellel, és a méretezési csoport csak a konfigurációs modell alapján implicit módon létrehozott virtuális gépeket tudja kezelni. 

A méretezési készlet vezénylési módjai lehetővé teszik a virtuálisgép-példányok méretezési készlet által való felügyeletének nagyobb mértékű szabályozását. 

> [!IMPORTANT]
> A vezénylési mód a méretezési csoport létrehozásakor van definiálva, és később nem módosítható vagy frissíthető.


## <a name="scale-sets-with-uniform-orchestration"></a>Méretezési készletek egységes vezényléssel
Nagy méretű, állapot nélküli számítási feladatokhoz van optimalizálva, azonos példányokkal.

Az egységes vezénylésű virtuálisgép-méretezési készletek virtuálisgép-profillal vagy -sablonnal skálázhatóak fel a kívánt kapacitásra. Bár van lehetőség az egyes virtuálisgép-példányok kezelésére és testreszabásra, a Uniform azonos virtuálisgép-példányokat használ. Az egyes egységes virtuálisgép-példányok a virtuálisgép-méretezési készlet virtuálisgép-API-parancsai révén vannak elérhetővé téve. Az egyes példányok nem kompatibilisek a szabványos Azure IaaS VM API-parancsokkal, az Azure felügyeleti funkcióival, például az Azure Resource Manager erőforrás-címkézés RBAC-engedélyekkel, Azure Backup vagy Azure Site Recovery. Az egységes vezénylés garantálja a tartalék tartományok magas rendelkezésre állását, ha kevesebb mint 100 példányra van konfigurálva. Általánosan elérhető az egységes vezénylés, és támogatja a méretezésikészlet-kezelés és -vezénylés teljes körét, beleértve a metrikaalapú automatikus skálázást, a példányvédelmet és az operációs rendszer automatikus frissítését. 


## <a name="scale-sets-with-flexible-orchestration"></a>Méretezési készletek rugalmas vezényléssel 
Nagy léptékű magas rendelkezésre állást érhet el azonos vagy több virtuálisgép-típussal.

A rugalmas vezénylésnek megfelelően az Azure egységes felhasználói élményt nyújt az Azure-beli virtuális gépek ökoszisztémájában. A rugalmas vezénylés magas rendelkezésre állást garantál (legfeljebb 1000 virtuális géphez) azáltal, hogy a virtuális gépeket egy régió vagy egy rendelkezésre állási zóna tartalék tartományai között elterjed. Ez lehetővé teszi az alkalmazás horizontális felskálálását a tartalék tartományok elkülönítésének fenntartása mellett, ami elengedhetetlen a kvórumalapú vagy állapotalapú számítási feladatok futtatásához, beleértve a következőket:
- Kvórumalapú számítási feladatok
- Open-Source adatbázisok
- Állapot- szerinti alkalmazások
- Magas rendelkezésre állást és nagy méretű szolgáltatásokat igénylő szolgáltatások
- Szolgáltatások, amelyek vegyesen szeretnék használni a virtuális gépek típusait, vagy kihasználják a kihasznált és az igény szerinti virtuális gépeket
- Meglévő rendelkezésre állásikészlet-alkalmazások  

> [!IMPORTANT]
> A rugalmas vezénylési módban lévő virtuálisgép-méretezési készletek jelenleg nyilvános előzetes verzióban használatban vannak. Az alább ismertetett nyilvános előzetes verziójú funkciók használatához egy lemondási eljárásra van szükség.
> Ez az előzetes verzió szolgáltatói szerződés nélkül érhető el, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Mi változott a rugalmas vezénylési módban?
A rugalmas vezénylés egyik fő előnye, hogy a méretezési készlet gyermek virtuális gépei helyett vezénylési funkciókat biztosít a standard Azure IaaS virtuális gépekhez. Ez azt jelenti, hogy a rugalmas vezénylési példányok kezelésekor használhatja az összes standard VM API-t a virtuálisgép-méretezési készlet virtuálisgép-API-i helyett, ha egységes vezénylést használ. Az előzetes verzió időtartama alatt számos különbség van a példányok rugalmas vezénylésben és az egységes vezénylésben való kezelése között. Általában azt javasoljuk, hogy amikor csak lehetséges, használja a standard Azure IaaS virtuálisgép-API-kat. Ebben a szakaszban példákat mutatunk be a rugalmas vezénylésű virtuálisgép-példányok kezeléséhez ajánlott eljárásokra.  

### <a name="assign-fault-domain-during-vm-creation"></a>Tartalék tartomány hozzárendelése a virtuális gép létrehozása során
A rugalmas vezénylési méretezési készlethez kiválaszthatja a tartalék tartományok számát. Alapértelmezés szerint ha virtuális gépet ad hozzá egy rugalmas méretezési készlethez, az Azure egyenletesen eltárja a példányokat a tartalék tartományok között. Bár azt javasoljuk, hogy az Azure rendelje hozzá a tartalék tartományt, speciális vagy hibaelhárítási forgatókönyvek esetén felülbírálhatja ezt az alapértelmezett viselkedést, és megadhatja azt a tartalék tartományt, ahová a példány le fog ni.

```azurecli-interactive 
az vm create â€“vmss "myVMSS"  â€“-platform_fault_domain 1
```

### <a name="instance-naming"></a>Példányelnevezés 
Amikor létrehoz egy virtuális gépet, és hozzáadja egy rugalmas méretezési csoporthoz, teljes mértékben szabályozhatja a példányok nevét az Azure elnevezési konvenciós szabályaiban. Amikor a rendszer automatikusan hozzáadja a virtuális gépeket a méretezési készlethez az automatikus skálázáson keresztül, meg kell adnia egy előtagot, és az Azure egyedi számot fűz a név végéhez.

### <a name="query-instances-for-power-state"></a>Az energiaállapot lekérdezéspéldányai
Az előnyben részesített módszer az Azure Resource Graph virtuálisgép-méretezési készlet összes virtuális gépének lekérdezése. Azure Resource Graph hatékony lekérdezési képességeket biztosít az Azure-erőforrásokhoz nagy méretekben, több előfizetésben. 

``` 
|â€¯whereâ€¯typeâ€¯=~â€¯'Microsoft.Compute/virtualMachines' 
|â€¯whereâ€¯properties.virtualMachineScaleSetâ€¯containsâ€¯"demo" 
|â€¯extendâ€¯powerStateâ€¯=â€¯properties.extended.instanceView.powerState.code 
|â€¯projectâ€¯name,â€¯resourceGroup,â€¯location,â€¯powerState 
|â€¯orderâ€¯byâ€¯resourceGroupâ€¯desc,â€¯nameâ€¯desc 
```

Az erőforrások [lekérdezése](../governance/resource-graph/overview.md) Azure Resource Graph és hatékony módja az Azure-erőforrások lekérdezésének, és minimálisra csökkenti az erőforrás-szolgáltatóhoz való API-hívásokat. Azure Resource Graph egy végül konzisztens gyorsítótár, ahol az új vagy frissített erőforrások akár 60 másodpercig sem jelennek meg. A következőket teheti:
- Az erőforráscsoportban vagy előfizetésben található virtuális gépek felsorolása.
- A kibontás lehetőséggel lekérheti az előfizetésében található összes virtuális gép példánynézetét (tartalék tartomány hozzárendelése, energiaellátási és kiépítési államok).
- A Get VM API és parancsok használatával modell- és példánynézetet is lekért egyetlen példányhoz.

### <a name="scale-sets-vm-batch-operations"></a>Méretezési készletek virtuálisgép-kötegműveletei
A virtuálisgép-méretezési csoport virtuálisgép-API-i helyett használja a standard virtuálisgép-parancsokat a példányok indításához, leállításához, újraindításához és törléséhez. A Virtuálisgép-méretezési készlet virtuálisgép-kötegműveletei (az összes újraindítása, az összes visszaállítása, az összes rendszerimizálása stb.) nincsenek rugalmas vezénylési módban használva. 

### <a name="monitor-application-health"></a>Alkalmazásállapot monitorozása 
Az alkalmazás állapotfigyelése lehetővé teszi, hogy az alkalmazás szívveréssel megállapítsa, hogy az alkalmazás kifogástalan vagy nem megfelelő állapotú-e. Az Azure képes automatikusan lecserélni a nem megfelelő virtuálisgép-példányokat. Rugalmas méretezésikészlet-példányok esetén telepítenie és konfigurálnia kell az Application Health bővítményt a virtuális gépen. Egységes méretezési csoport példányai esetén használhatja az Application Health bővítményt, vagy mérheti az állapotát egy egyéni állapot Azure Load Balancer használatával. 

### <a name="list-scale-sets-vm-api-changes"></a>Méretezési készletek virtuálisgép-API-módosításainak felsorolása 
Virtual Machine Scale Sets lehetővé teszi a méretezési készlethez tartozó példányok felsorolását. Rugalmas vezényléssel a Virtual Machine Scale Sets virtuálisgép-parancs listájában megjelenik a méretezési készletek virtuálisgép-adatbázisának listája. Ezután a GET Virtual Machine Scale Sets virtuálisgép-parancsokat hívva további részleteket kaphat arról, hogyan működik együtt a méretezési készlet a virtuálisgép-példányokkal. A virtuális gép teljes részleteinek lekért információkért használja a standard GET VM-parancsokat, vagy [Azure Resource Graph.](../governance/resource-graph/overview.md) 

### <a name="retrieve-boot-diagnostics-data"></a>Rendszerindítási diagnosztikai adatok lekérése 
Használja a standard VM API-kat és parancsokat a példány rendszerindítási diagnosztikai adatainak és képernyőképének lekéréséhez. A Virtual Machine Scale Sets virtuális gép rendszerindítási diagnosztikai API-ját és parancsait a rendszer nem használja rugalmas vezénylési módú példányokkal.

### <a name="vm-extensions"></a>Virtuálisgép-bővítmények 
Használjon standard virtuális gépekhez célzott bővítményeket az egységes vezénylési módú példányokhoz megcélzott bővítmények helyett.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>A rugalmas, egységes és rendelkezésre állási készletek összehasonlítása 
Az alábbi táblázat a rugalmas vezénylési módot, az egységes vezénylési módot és a rendelkezésre állási készleteket hasonlítja össze azok funkciói alapján.

| Szolgáltatás | Rugalmas vezénylés által támogatott (előzetes verzió) | Egységes vezénylés által támogatott (általánosan elérhető) | Az AvSets által támogatott (általánosan elérhető) |
|-|-|-|-|
|         Virtuális gép típusa  | Standard Azure IaaS virtuális gép (Microsoft.compute /virtualmachines)  | Méretezésikészlet-specifikus virtuális gépek (Microsoft.compute /virtualmachinescalesets/virtualmachines)  | Standard Azure IaaS virtuális gép (Microsoft.compute /virtualmachines)  |
|         Támogatott SKUS-k  |            D sorozat, E sorozat, F sorozat, A sorozat, B sorozat, Intel, AMD  |            Minden SKUs  |            Minden SKUs  |
|         Rendelkezésre állási zónák  |            Megadhatja, hogy az összes példány egyetlen rendelkezésre állási zónába ássa le |            1, 2 vagy 3 rendelkezésre állási zónába leérkedő példányok megadása  |            Nem támogatott  |
|         A virtuális gépek, a NIC-k és a lemezek teljes körű vezérlése  |            Igen  |            Korlátozott hozzáférés a virtuálisgép-méretezési készletek VM API-jának használatával  |            Igen  |
|         Automatikus skálázás  |            Nem  |            Igen  |            Nem  |
|         Virtuális gép hozzárendelése egy adott tartalék tartományhoz  |            Igen  |             Nem   |            Nem  |
|         A virtuálisgép-példányok törlésekor távolítsa el a NIC-ket és a lemezeket  |            Nem  |            Igen  |            Nem  |
|         Frissítési szabályzat (virtuálisgép-méretezési készletek) |            Nem  |            Automatikus, működés közbeni, manuális  |            N/A  |
|         Automatikus operációsrendszer-frissítések (virtuálisgép-méretezési készletek) |            Nem  |            Igen  |            N/A  |
|         Vendég biztonsági javításában  |            Igen  |            Nem  |            Igen  |
|         Leállítja az értesítéseket (virtuálisgép-méretezési készletek) |            Nem  |            Igen  |            N/A  |
|         Példányjavítás (virtuálisgép-méretezési készletek) |            Nem  |            Igen   |            N/A  |
|         Gyorsított hálózatkezelés  |            Igen  |            Igen  |            Igen  |
|         Spot® âinstances and pricingà€ â  |            Igen, a Spot és a Regular prioritáspéldány is lehet  |            Igen, a példányok csak spot vagy csak normál példányok  |            Nem, csak a normál prioritású példányok  |
|         Operációs rendszerek vegyesen  |            Igen, a Linux és a Windows ugyanabban a rugalmas méretezési készletben is lehet |            Nem, a példányok ugyanaz az operációs rendszer  |               Igen, a Linux és a Windows ugyanabban a rugalmas méretezési készletben is lehet |
|         Alkalmazás állapotának figyelése  |            Alkalmazás állapotbővítménye  |            Alkalmazás állapotkiterjesztése vagy Azure Load Balancer-mintavétel  |            Alkalmazás állapotbővítménye  |
|         UltraSSDà€ àDisksâ€ à  |            Igen  |            Igen, csak zóna üzemelő példányok esetén  |            Nem  |
|         Infiniband® à  |            Nem  |            Igen, csak egy elhelyezési csoport  |            Igen  |
|         Writeâ€ àAccelerator® à  |            Nem  |            Igen  |            Igen  |
|         Közelségi hely€ àPlacement Groups® à  |            Igen  |            Igen  |            Igen  |
|         Azure-beli dedikált gazdagépek  |            Nem  |            Igen  |            Igen  |
|         Alapszintű SLBà€ à  |            Nem  |            Igen  |            Igen  |
|         Azure Load Balancer standard termékváltozat |            Igen  |            Igen  |            Igen  |
|         Application Gateway  |            Nem  |            Igen  |            Igen  |
|         Karbantartás-vezérlés  |            Nem  |            Igen  |            Igen  |
|         A készletbe sorolt virtuális gépek listája  |            Igen  |            Igen  |            Igen, list virtuális gépek az AvSet-ban  |
|         Azure-riasztások  |            Nem  |            Igen  |            Igen  |
|         VM Insights  |            Nem  |            Igen  |            Igen  |
|         Azure Backup  |            Igen  |            Igen  |            Igen  |
|         Azure Site Recovery  |     Nem  |            Nem  |            Igen  |
|         Meglévő virtuális gép hozzáadása/eltávolítása a csoporthoz  |            Nem  |            Nem  |            Nem  | 


## <a name="register-for-flexible-orchestration-mode"></a>Regisztrálás rugalmas vezénylési módra
Ahhoz, hogy rugalmas vezénylési módban helyez üzembe virtuálisgép-méretezési készleteket, először regisztrálnia kell az előfizetését az előzetes verziójú funkcióra. A regisztráció több percig is eltarthat. A regisztrációhoz használhatja Azure PowerShell Azure CLI-parancsokat.

### <a name="azure-portal"></a>Azure Portal
Lépjen annak az előfizetésnek a részletek oldalára, amelyről rugalmas vezénylési módban szeretne méretezési csoportokat létrehozni, majd válassza az Előzetes verziójú funkciók lehetőséget a menüből. Válassza ki az engedélyezni kívánt két vezénylési funkciót: _VMOrchestratorSingleFD_ és _VMOrchestratorMultiFD,_ majd kattintson a Regisztráció gombra. A szolgáltatásregisztráció akár 15 percet is igénybe vehet.

![Szolgáltatásregisztráció.](https://user-images.githubusercontent.com/157768/110361543-04d95880-7ff5-11eb-91a7-2e98f4112ae0.png)

Miután regisztrálta a funkciókat az előfizetésében, a módosításnak a Compute erőforrás-szolgáltatóba való propagálással teljes körűen be kell fejeződött a feliratkozási folyamat. Lépjen az előfizetéséhez az Erőforrás-szolgáltatók lapra, válassza a Microsoft.compute lehetőséget, majd kattintson az Újra regisztrálás elemre.

![Regisztráció újra](https://user-images.githubusercontent.com/157768/110362176-cd1ee080-7ff5-11eb-8cc8-36aa967e267a.png)


### <a name="azure-powershell"></a>Azure PowerShell 
A [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancsmag használatával engedélyezheti az előzetes verzió használatát az előfizetéséhez. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

A szolgáltatásregisztráció akár 15 percet is igénybe vehet. A regisztráció állapotának ellenőrzése: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

Miután regisztrálta a funkciót az előfizetésében, a módosításnak a Compute erőforrás-szolgáltatóba való propagálással kell befejeznie a feliratkozási folyamatot. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>Azure CLI 2.0 
Az [az feature register használatával](/cli/azure/feature#az-feature-register) engedélyezheti az előzetes verzió használatát az előfizetéséhez. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

A szolgáltatásregisztráció akár 15 percet is igénybe vehet. A regisztráció állapotának ellenőrzése: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

Miután regisztrálta a funkciót az előfizetésben, a módosításnak a Compute erőforrás-szolgáltatóba való propagálással teljes körűen be kell fejeződött a feliratkozási folyamat. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>A rugalmas vezénylési mód első lépések

Első lépések a méretezési készletek rugalmas vezénylési módjával a Azure Portal, az Azure CLI, a Terraform vagy a REST API.

### <a name="azure-portal"></a>Azure Portal

Hozzon létre egy virtuálisgép-méretezési csoport rugalmas vezénylési módban a Azure Portal.

1. Jelentkezzen be a [Azure Portal.](https://portal.azure.com)
1. A keresősávban keresse meg és válassza a **Virtuálisgép-méretezési készletek lehetőséget.** 
1. A **Virtuálisgép-méretezési** **csoport oldalon válassza a Létrehozás** lehetőséget.
1. A **Virtuálisgép-méretezési csoport létrehozása oldalon** tekintse meg a **Vezénylés szakaszt.**
1. A **Vezénylési mód beállításhoz** válassza a **Rugalmas** lehetőséget.
1. Állítsa be **a Tartalék tartományok számát.**
1. Fejezze be a méretezési készlet létrehozását. A [méretezési csoport létrehozásáról](quick-create-portal.md#create-virtual-machine-scale-set) a méretezési csoport Azure Portal a méretezési csoport létrehozásáról.

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Vezénylési mód a Portálon méretezési készlet létrehozásakor":::

Ezután adjon hozzá egy virtuális gépet a méretezési készlethez rugalmas vezénylési módban.

1. A keresősávban keresse meg és válassza a **Virtuális gépek lehetőséget.**
1. A **Virtuális** gépek lapon válassza **a Hozzáadás** lehetőséget.
1. Az Alapvető **beállítások lapon** tekintse meg a Példány **részletei szakaszt.**
1. Adja hozzá a virtuális gépet a méretezési készlethez rugalmas vezénylési módban a rendelkezésre állási lehetőségek között a méretezési **készlet kiválasztásával.** A virtuális gépet hozzáadhatja egy ugyanabban a régióban, zónában és erőforráscsoportban lévő méretezési csoporthoz.
1. Fejezze be a virtuális gép létrehozását. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="Virtuális gép hozzáadása a rugalmas vezénylési módú méretezési készlethez":::


### <a name="azure-cli-20"></a>Azure CLI 2.0
Rugalmas virtuálisgép-méretezési csoport létrehozása az Azure CLI használatával. Az alábbi példa egy rugalmas méretezési csoport létrehozását mutatja be, amelyben a tartalék tartományok száma 3, a rendszer létrehoz egy virtuális gépet, majd hozzáadja a rugalmas méretezési csoporthoz. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Rugalmas virtuálisgép-méretezési csoport létrehozása a Terraformmal. Ehhez a folyamathoz **a Terraform Azurerm-szolgáltató 2.15.0-s vagy** újabb szükséges. Figyelje meg a következő paramétereket:
- Ha nincs zóna megadva, a régiótól függően `platform_fault_domain_count` 1, 2 vagy 3 lehet.
- Zóna megszava esetén a `the fault domain count` lehet 1.
- `single_placement_group` A paraméternek rugalmas `false` virtuálisgép-méretezési készletekhez kell lennie.
- Regionális üzembe helyezés esetén nem kell megadnia a `zones` értéket.

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

1. Hozzon létre egy üres méretezési csoport. A következő paraméterek szükségesek:
    - API-verzió: 2019.12. 01. (vagy újabb) 
    - Rugalmas méretezési csoport létrehozásakor `false` egyetlen elhelyezési csoportnak kell lennie

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

2. Virtuális gépek hozzáadása a méretezési készlethez.
    1. Rendelje hozzá `virtualMachineScaleSet` a tulajdonságot a korábban létrehozott méretezési készlethez. A tulajdonságot a virtuális gép létrehozásakor kell `virtualMachineScaleSet` megadnia. 
    1. A **copy()** Azure Resource Manager több virtuális gép létrehozására is használhatja egyszerre. Lásd: [Erőforrás-iteráció](../azure-resource-manager/templates/copy-resources.md#iteration-for-a-child-resource) Azure Resource Manager sablonokban. 

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

Teljes [példát az Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) rövid útmutatóban láthat.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Mekkora méretet támogat a rugalmas vezénylés?**

Rugalmas vezénylési módban akár 1000 virtuális gép is hozzáadható egy méretezési készlethez.

**Hogyan viszonyul a rendelkezésre állás a rugalmas vezényléssel a rendelkezésre állási készletekhez vagy az egységes vezényléshez?**

| Rendelkezésre állási attribútum  | Rugalmas vezénylés  | Egységes vezénylés  | Rendelkezésre állási csoportok  |
|-|-|-|-|
| Üzembe helyezés rendelkezésre állási zónák között  | Nem  | Igen  | Nem  |
| Tartalék tartomány rendelkezésre állási garanciái egy régión belül  | Igen, legfeljebb 1000 példány használhatja a régió legfeljebb 3 tartalék tartományát. A tartalék tartományok maximális száma régiónként változik  | Igen, legfeljebb 100 példány  | Igen, legfeljebb 200 példány  |
| Elhelyezési csoportok  | A rugalmas mód mindig több elhelyezési csoportot használ (singlePlacementGroup = false)  | Választhat egy elhelyezési csoportot vagy több elhelyezési csoportot is. | N/A  |
| Frissítési tartományok  | Nincs, a karbantartási vagy gazdagép-frissítések tartalék tartományon vannak el végezve a tartalék tartomány szerint  | Legfeljebb 5 frissítési tartomány  | Legfeljebb 20 frissítési tartomány  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Rugalmas vezénylésű méretezési készletek hibaelhárítása
Keresse meg a megfelelő megoldást a hibaelhárítási forgatókönyvhöz. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Ok:** Az előfizetés nincs regisztrálva a rugalmas vezénylési mód nyilvános előzetes kiadásában. 

**Megoldás:** A fenti utasításokat követve regisztráljon a rugalmas vezénylési mód nyilvános előzetes kiadására. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Ok:** A `platformFaultDomainCount` paraméter érvénytelen a kiválasztott régióhoz vagy zónához. 

**Megoldás:** Érvényes értéket kell `platformFaultDomainCount` választania. Zónaális üzemelő példányok esetén a maximális `platformFaultDomainCount` érték 1. Regionális üzemelő példányok esetén, ahol nincs zóna megadva, a maximális érték `platformFaultDomainCount` a régiótól függően változik. A hibatartományok régiónkénti maximális számát a virtuális gépek rendelkezésre állásának kezelése szkriptek esetén lásd: Manage the [availability of VMs](../virtual-machines/availability.md) for scripts (Virtuális gépek rendelkezésre állásának kezelése szkriptek esetén). 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Ok:** Egy vagy több virtuális géphez társított rugalmas vezénylési módban próbálja törölni a méretezési csoportokat. 

**Megoldás:** A méretezési csoporthoz társított összes virtuális gép törlése rugalmas vezénylési módban, majd a méretezési csoport törlése.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Ok:** Az előfizetés regisztrálva van a rugalmas vezénylési mód előzetes kiadására; A paraméter azonban `singlePlacementGroup` True (Igaz) *értékre van állítva.* 

**Megoldás:** A `singlePlacementGroup` értéknek False (Hamis) értéket *kell beállítania.* 


## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Megtudhatja, hogyan helyezheti üzembe az alkalmazást a méretezési készleten.](virtual-machine-scale-sets-deploy-app.md)
