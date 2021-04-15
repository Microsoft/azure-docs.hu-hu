---
title: Karbantartási értesítések az Azure-beli virtuálisgép-méretezési készletekhez
description: Megtekintheti a karbantartási értesítéseket, és önkiszolgáló karbantartást kezdhet az Azure-beli virtuálisgép-méretezési készletekhez.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: maintenance-control
ms.date: 11/12/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 355e29cf062b731ed26670497a8de75fef266b99
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375712"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok – tervezett karbantartás értesítései


Az Azure rendszeres időközönként frissítéseket hajt végre a virtuális gépek gazdagép-infrastruktúrájának megbízhatóságának, teljesítményének és biztonságának javítása érdekében. A frissítések közé tartozhat az üzemeltetési környezet javítása vagy a hardver frissítése és leszerelése. A legtöbb frissítés nincs hatással az üzemeltetett virtuális gépekre. A frissítések azonban a következő esetekben érintik a virtuális gépeket:

- Ha a karbantartáshoz nincs szükség újraindításra, az Azure néhány másodpercre felfüggeszti a virtuális gépet a gazdagép frissítése közben. Az ilyen típusú karbantartási műveletek tartalék tartományt alkalmaznak a tartalék tartományra. A folyamat leáll, ha figyelmeztetési állapotjelző jelek fogadnak.

- Ha a karbantartás újraindítást igényel, értesítést kap a karbantartás tervezett befejezéséről. Ezekben az esetekben olyan időkeretet kap, amely általában 35 nap, amikor ön is elkezdheti a karbantartást, amikor az Önnek megfelelő.


Az újraindítást igénylő tervezett karbantartás hullámokban van ütemezve. Minden egyes hullám különböző hatókörrel rendelkezik (régiók):

- A hullám az ügyfeleknek szóló értesítéssel kezdődik. Alapértelmezés szerint a rendszer értesítést küld az előfizetés tulajdonosának és társtulajdonosának. Az Azure-tevékenységnapló riasztásai segítségével címzetteket és üzenetkezelési beállításokat adhat hozzá az [értesítésekhez,](../azure-monitor/essentials/platform-logs-overview.md)például e-maileket, SMS-eket és webhookokat.  
- Értesítéssel *elérhetővé válik* egy önkiszolgáló ablak. Ebben az általában 35 napos időszakban kiderítheti, hogy melyik virtuális gép szerepel a hullámban. A karbantartást proaktív módon kezdheti meg a saját ütemezési igényei szerint.
- Az önkiszolgáló időszak után megkezdődik az *ütemezett karbantartási időszak.* Az ablak egy pontján az Azure ütemezi és alkalmazza a szükséges karbantartást a virtuális gépre. 

A két időablak célja, hogy elegendő időt adjon a karbantartás elkezdését és a virtuális gép újraindítását, miközben tudni fogja, hogy az Azure mikor indítja el automatikusan a karbantartást.

A virtuálisgép-méretezési Azure Portal, a PowerShell, a REST API és az Azure CLI használatával lekérdezheti a virtuálisgép-méretezési készlet virtuális gépei karbantartási időszakát, és elindíthatja az önkiszolgáló karbantartást.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Érdemes elkezdeni a karbantartást az önkiszolgáló időszakban?  

Az alábbi irányelvek segíthetnek eldönteni, hogy a karbantartást egy ön által választott időpontban indítja-e el.

> [!NOTE] 
> Előfordulhat, hogy az önkiszolgáló karbantartás nem érhető el az összes virtuális géphez. Annak megállapításához, hogy a virtuális géphez elérhető-e a proaktív ismételt üzembe állás, keresse meg az **Indítás** most karbantartási állapotot. Az önkiszolgáló karbantartás jelenleg nem érhető el a Azure Cloud Services (webes/feldolgozói szerepkör) és az Azure Service Fabric.


Az önkiszolgáló karbantartás nem ajánlott a rendelkezésre állási készleteket *() használó üzemelő példányok esetén.* A rendelkezésre állási csoport olyan magas rendelkezésre állású beállítás, amelyben egyszerre csak egy frissítési tartományra van hatással. Rendelkezésre állási készletek:

- Hagyja, hogy az Azure elindítsa a karbantartást. Újraindítást igénylő karbantartás esetén a karbantartás a frissítési tartomány szerint történik. A frissítési tartományok nem feltétlenül kapják meg egymás után a karbantartást. A frissítési tartományok között 30 perces szünet van.
- Ha a kapacitás egy része (1/frissítési tartományszám) ideiglenes elvesztése problémát jelent, a karbantartási időszak alatt további példányok allokálására való rendszerezésével könnyedén kompenzálhatja a elvesztését.
- Újraindítást nem igénylő karbantartás esetén a frissítések a tartalék tartomány szintjén vannak alkalmazva. 
    
**A következő esetekben** ne használjon önkiszolgáló karbantartást: 

- Ha gyakran, manuálisan, a DevTest Labs használatával, automatikus leállítás használatával vagy ütemezés szerint leállítja a virtuális gépeket. Ezekben az esetekben az önkiszolgáló karbantartás visszaállíthatja a karbantartási állapotot, és további állásidőt okozhat.
- A rövid életű virtuális gépek, amelyekről tudja, a karbantartási időszak vége előtt törlődnek. 
- Nagy méretű, a frissítés után karbantartani kívánt helyi (rövidtű) lemezen tárolt számítási feladatok esetén. 
- Ha gyakran méretezi át a virtuális gépet. Ez a forgatókönyv visszaállíthatja a karbantartási állapotot. 
- Ha olyan ütemezett eseményeket fogadott el, amelyek lehetővé teszik a számítási feladat proaktív feladatátvételét vagy leállását 15 perccel a karbantartás megkezdése előtt.

**Akkor** használjon önkiszolgáló karbantartást, ha a virtuális gépet zavartalanul tervezi futtatni az ütemezett karbantartási fázisban, és a fenti ellenintézkedések egyike sem érvényes. 

Az önkiszolgáló karbantartást a következő esetekben ajánlott használni:

- Pontosan meg kell kommunikálnia a karbantartási időszakokat a kezelővel vagy az ügyféllel. 
- A karbantartást egy adott dátum szerint kell befejeznie. 
- A biztonságos helyreállítás biztosítása érdekében szabályozni kell a karbantartás sorrendjét, például egy többrétegű alkalmazásban.
- Több mint 30 percnyi virtuálisgép-helyreállítási időre van szüksége két frissítési tartomány között. A frissítési tartományok közötti idő szabályozása érdekében egyszerre egy frissítési tartományt kell aktiválni a virtuális gépeken.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>A karbantartás által érintett virtuálisgép-méretezési készletek megtekintése a portálon

Ha tervezett karbantartási hullám van ütemezve, a következő karbantartási hullám által érintett virtuálisgép-méretezési Azure Portal. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza a **Minden** szolgáltatás, majd a **Virtuálisgép-méretezési készletek lehetőséget.**
3. A **Virtuálisgép-méretezési készletek alatt** válassza az Oszlopok szerkesztése **lehetőséget** az elérhető oszlopok listájának megnyitásához.
4. Az Elérhető **oszlopok szakaszban** válassza az **Önkiszolgáló** karbantartás lehetőséget, majd helyezze át a Kijelölt oszlopok **listára.** Kattintson az **Alkalmaz** gombra.  

    Annak érdekében, hogy **könnyebben** megtalálja az önkiszolgáló karbantartási elemet,  az Elérhető oszlopok szakaszban található legördülő menüt **az Összes** lehetőségről a Tulajdonságok lehetőségre **módosíthatja.**

Az **Önkiszolgáló karbantartás oszlop** megjelenik a virtuálisgép-méretezési készletek listájában. Minden virtuálisgép-méretezési készlet az alábbi értékekkel lehet az önkiszolgáló karbantartási oszlopban:

| Érték | Leírás |
|-------|-------------|
| Igen | A virtuálisgép-méretezési készletben legalább egy virtuális gép egy önkiszolgáló ablakban található. Ebben az önkiszolgáló időszakban bármikor elkezdheti a karbantartást. | 
| Nem | Az érintett virtuálisgép-méretezési készlet egyik virtuális gépe sem található önkiszolgáló ablakban. | 
| - | A virtuálisgép-méretezési készletek nem részei egy tervezett karbantartási hullámnak.| 

## <a name="notification-and-alerts-in-the-portal"></a>Értesítések és riasztások a portálon

Az Azure e-mailben elküldi az előfizetés tulajdonosának és a társtulajdonosok csoportjának a tervezett karbantartás ütemezését. Ehhez a kommunikációhoz tevékenységnapló-riasztások létrehozásával adhat hozzá címzetteket és csatornákat. További információ: [Előfizetési tevékenység figyelése az Azure-tevékenységnaplóval.](../azure-monitor/essentials/platform-logs-overview.md)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza a **Figyelás lehetőséget.** 
3. A **Figyelés – Riasztások (klasszikus) panelen** válassza a **+Tevékenységnapló-riasztás hozzáadása lehetőséget.**
4. A **Tevékenységnapló-riasztás hozzáadása lapon** válassza ki vagy adja meg a kért adatokat. Győződjön **meg arról,** hogy a Feltételek mezőben a következő értékeket adja meg:
   - **Eseménykategória:** Válassza a **Service Health** lehetőséget.
   - **Szolgáltatások:** Válassza a **Virtual Machine Scale Sets, majd Virtual Machines** lehetőséget.
   - **Típus:** Válassza **a Tervezett karbantartás lehetőséget.** 
    
További információ a tevékenységnapló-riasztások konfigurálásról: [Tevékenységnapló-riasztások létrehozása](../azure-monitor/alerts/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Indítsa el a karbantartást a virtuálisgép-méretezési készleten a portálról

A karbantartással kapcsolatos további részleteket a virtuálisgép-méretezési készletek áttekintésében talál. Ha a virtuálisgép-méretezési csoportban legalább egy virtuális gép szerepel a tervezett karbantartási hullámban, egy új értesítési menüszalag lesz hozzáadva az oldal tetején. Válassza az értesítési menüszalagot a Karbantartás **lapra való ugráshoz.** 

A Karbantartás **lapon** láthatja, hogy a tervezett karbantartás melyik virtuálisgép-példányt érinti. A karbantartás elkezdenihez jelölje be az érintett virtuális gépnek megfelelő jelölőnégyzetet. Ezután válassza a **Start maintenance (Karbantartás kezdete) lehetőséget.**

A karbantartás elkezdésével a virtuálisgép-méretezési készlet érintett virtuális gépei karbantartáson esnek át, és átmenetileg nem érhetők el. Ha kihagyta az önkiszolgáló ablakot, továbbra is megjelenik az az időablak, amikor az Azure fenntartja a virtuálisgép-méretezési készletet.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Karbantartási állapot ellenőrzése a PowerShell használatával

A virtuálisgép Azure PowerShell azt is láthatja, hogy mikor vannak ütemezve a virtuálisgép-méretezési készletekben lévő virtuális gépek karbantartásra. A tervezett karbantartási információk a [Get-AzVmss](/powershell/module/az.compute/get-azvmss) parancsmag használatával érhetők el a paraméter `-InstanceView` használata esetén.
 
A rendszer csak akkor ad vissza karbantartási információkat, ha karbantartást terveznek. Ha nincs ütemezve olyan karbantartás, amely hatással lenne a virtuálisgép-példányra, a parancsmag nem ad vissza karbantartási információkat. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

A **MaintenanceRedeployStatus** a következő tulajdonságokat tartalmazza: 

| Érték | Leírás   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy jelenleg elindíthatja-e a virtuális gép karbantartását. |
| PreMaintenanceWindowStartTime         | A karbantartási önkiszolgáló időszak kezdete, amikor karbantartást kezdeményezhet a virtuális gépen. |
| PreMaintenanceWindowEndTime           | A karbantartási önkiszolgáló időszak vége, amikor karbantartást kezdeményezhet a virtuális gépen. |
| KarbantartásWindowStartTime            | Annak az ütemezett karbantartásnak a kezdete, amelyben az Azure karbantartást kezdeményez a virtuális gépen. |
| MaintenanceWindowEndTime (KarbantartáswindowEndTime)              | A karbantartási ütemezett időszak vége, amelyben az Azure karbantartást kezdeményez a virtuális gépen. |
| LastOperationResultCode               | A karbantartás kezdeményezésére tett legutóbbi kísérlet eredménye a virtuális gépen. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Indítsa el a karbantartást a virtuálisgép-példányon a PowerShell használatával

Ha az **IsCustomerInitiatedMaintenanceAllowed** true (igaz) érték van beállítva, elindíthatja a **karbantartást** egy virtuális gépen. Használja a [Set-AzVmss](/powershell/module/az.compute/set-azvmss) parancsmagot a `-PerformMaintenance` paraméterrel.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Karbantartási állapot ellenőrzése a CLI használatával

A tervezett karbantartási információkat az [az vmss list-instances](/cli/azure/vmss#az-vmss-list-instances)használatával lehet megtekinteni.
 
A rendszer csak akkor ad vissza karbantartási információkat, ha karbantartást terveznek. Ha a virtuálisgép-példányt érintő karbantartás nincs ütemezve, a parancs nem ad vissza karbantartási információkat. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Az egyes virtuálisgép-példányok **MaintenanceRedeployStatus** tulajdonsága a következő tulajdonságokat tartalmazza: 

| Érték | Leírás   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy jelenleg elindíthatja-e a virtuális gép karbantartását. |
| PreMaintenanceWindowStartTime         | A karbantartási önkiszolgáló időszak kezdete, amikor karbantartást kezdeményezhet a virtuális gépen. |
| PreMaintenanceWindowEndTime           | A karbantartási önkiszolgáló időszak vége, amikor karbantartást kezdeményezhet a virtuális gépen. |
| KarbantartásWindowStartTime            | Annak az ütemezett karbantartásnak a kezdete, amelyben az Azure karbantartást kezdeményez a virtuális gépen. |
| MaintenanceWindowEndTime (KarbantartáswindowEndTime)              | A karbantartási ütemezett időszak vége, amelyben az Azure karbantartást kezdeményez a virtuális gépen. |
| LastOperationResultCode               | A virtuális gép karbantartásának kezdeményezésére tett legutóbbi kísérlet eredménye. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>A virtuálisgép-példány karbantartásának elkezdenie a CLI-t

A következő hívás karbantartást kezdeményez egy virtuálisgép-példányon, ha true `IsCustomerInitiatedMaintenanceAllowed` (igaz) érték van **beállítva:**

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>GYIK

**K: Miért kell most újraindítani a virtuális gépeket?**

**A:** Bár az Azure platform frissítései és frissítései nem befolyásolják a virtuális gépek rendelkezésre állását, bizonyos esetekben nem tudjuk elkerülni az Azure-ban üzemeltetett virtuális gépek újraindítását. Több olyan módosítást is összegyűjtöttünk, amelyek miatt újra kell indítani a kiszolgálókat, ami a virtuális gép újraindítását eredményezi.

**K: Ha a magas rendelkezésre állásra vonatkozó javaslatait egy rendelkezésre állási készlet használatával követem, akkor biztonságban vagyok?**

**A:** A rendelkezésre állási vagy virtuálisgép-méretezési készletekben üzembe helyezett virtuális gépek frissítési tartományokat használnak. Karbantartás végrehajtásakor az Azure tiszteletben tart egy frissítési tartományra vonatkozó korlátozást, és nem indítja újra a virtuális gépeket egy másik frissítési tartományból (ugyanabban a rendelkezésre állási csoportban). Az Azure emellett legalább 30 percet vár, mielőtt továbblép a következő virtuális gépcsoportra. 

További információ a magas rendelkezésre állásról: Az Azure-beli virtuális gépek [régiói és rendelkezésre állása.](../virtual-machines/availability.md)

**K: Hogyan kaphatok értesítést a tervezett karbantartásról?**

**A:** A tervezett karbantartási hullám egy ütemezés egy vagy több Azure-régióra való beállításával kezdődik. Nem sokkal később a rendszer e-mailes értesítést küld az előfizetés rendszergazdáinak, társadminisztr rendszergazdáinak, tulajdonosainak és közreműködőinek (előfizetésenként egy e-mailt). Az értesítéshez további csatornák és címzettek is konfigurálhatók a Tevékenységnapló-riasztások használatával. Ha olyan régióban helyez üzembe egy virtuális gépet, ahol már be van ütemezve a tervezett karbantartás, nem kapja meg az értesítést. Ehelyett ellenőrizze a virtuális gép karbantartási állapotát.

**K: Nem látható semmilyen tervezett karbantartásra utaló jel a portálon, a PowerShellben vagy a parancssori felületen. mi a baj?**

**A:** A tervezett karbantartással kapcsolatos információk csak a tervezett karbantartás által érintett virtuális gépek számára érhetők el a tervezett karbantartás során. Ha nem lát adatokat, előfordulhat, hogy a karbantartási időszak már befejeződött (vagy nem indult el), vagy a virtuális gép már egy frissített kiszolgálón üzemel.

**K: Meg lehet tudni, hogy pontosan mikor lesz hatással a virtuális gépem?**

**A:** Az ütemezés beállításakor több napos időkeretet határozunk meg. Ezen időszakon belül a kiszolgálók (és a virtuális gépek) pontos karbantartási ütemezése nem ismert. Ha tudni szeretné a virtuális gépek frissítésének pontos idejét, ütemezett [eseményeket használhat.](../virtual-machines/windows/scheduled-events.md) Ütemezett események használata során a virtuális gépen belülről is lekérdezhet, és 15 perces értesítést kaphat a virtuális gép újraindítása előtt.

**K: Mennyi ideig tart a virtuális gép újraindítása?**

**A:**  A virtuális gép méretétől függően az újraindítás akár több percig is eltarthat az önkiszolgáló karbantartási időszak alatt. Az Azure által kezdeményezett újraindítások során az ütemezett karbantartási időszakban az újraindítás általában körülbelül 25 percet vesz igénybe. Ha Cloud Services (webes/feldolgozói szerepkör), virtuálisgép-méretezési csoportokat vagy rendelkezésre állási csoportokat használ, az ütemezett karbantartási időszakban 30 perc lesz a virtuális gépek egyes csoportja (frissítési tartomány) között. 

**K: Nem látom a virtuális gépekkel kapcsolatos karbantartási információkat. Mi volt a hiba?**

**A:** Több oka is lehet annak, hogy nem lát karbantartási információkat a virtuális gépeken:
   - Microsoft Belsőként megjelölt *előfizetést használ.*
   - A virtuális gépek nincsenek karbantartásra ütemezve. Lehetséges, hogy a karbantartási hullám véget ért, törölve lett vagy módosítva, hogy a továbbiakban ne legyen hatással a virtuális gépekre.
   - Nem megjelenik a **Karbantartás** oszlop a virtuális gépek listanézetében. Bár ezt az oszlopot hozzáadta az alapértelmezett nézethez, ha a nézetet nem alapértelmezett oszlopok megtekintésére konfigurálja, manuálisan kell hozzáadnia a **Karbantartás** oszlopot a virtuálisgép-listanézethez.

**K: A virtuális gépem második karbantartásra van ütemezve. miért?**

**A:** Számos esetben a virtuális gép karbantartásra van ütemezve, miután már befejezte a karbantartást, és újra üzembe elte:
   - Megszüntetjük a karbantartási hullámot, és más hasznos okkal újraindítjuk. Lehetséges, hogy hibát észleltünk a hasznos adatokban, és egyszerűen csak egy további hasznos információt kell üzembe helyeznünk.
   - A virtuális gép *egy hardverhiba* miatt egy másik csomóponton lett szervizelve.
   - Kiválasztotta a virtuális gép leállítását (felszabadítása) és újraindítását.
   - A virtuális **gép automatikus leállítása** be van kapcsolva.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan regisztrálhat karbantartási eseményekre a virtuális gépen belül ütemezett [események használatával.](../virtual-machines/windows/scheduled-events.md)
