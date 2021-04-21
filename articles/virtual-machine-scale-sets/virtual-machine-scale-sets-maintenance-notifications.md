---
title: Karbantartási értesítések az Azure-beli virtuálisgép-méretezési készletekről
description: Megtekintheti a karbantartási értesítéseket, és önkiszolgáló karbantartást kezdhet az Azure-beli virtuálisgép-méretezési készletekhez.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: maintenance-control
ms.date: 11/12/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: ec8d211bd25eb04f9e000af950cea9a28a0d1874
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762838"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok – tervezett karbantartás értesítései


Az Azure rendszeres időközönként frissítéseket hajt végre a virtuális gépek gazdagép-infrastruktúrájának megbízhatóságának, teljesítményének és biztonságának javítása érdekében. A frissítések közé tartozhat az üzemeltetési környezet javítása vagy a hardver frissítése és leszerelése. A legtöbb frissítés nincs hatással az üzemeltetett virtuális gépekre. A frissítések azonban az alábbi esetekben érintik a virtuális gépeket:

- Ha a karbantartáshoz nincs szükség újraindításra, az Azure néhány másodpercre felfüggeszti a virtuális gépet a gazdagép frissítése közben. Az ilyen típusú karbantartási műveletek tartalék tartományt alkalmaznak a tartalék tartományra. A folyamat leáll, ha bármilyen figyelmeztető állapotjelző jelet kap.

- Ha a karbantartás újraindítást igényel, értesítést kap a karbantartás tervezett befejezéséről. Ezekben az esetekben olyan időkeretet kap, amely általában 35 nap, amikor ön is elkezdheti a karbantartást, amikor az Önnek megfelelő.


Az újraindítást igénylő tervezett karbantartás hullámokban van ütemezve. Mindegyik hullám különböző hatókörrel rendelkezik (régiók):

- A hullám az ügyfeleknek szóló értesítéssel kezdődik. Alapértelmezés szerint a rendszer értesítést küld az előfizetés tulajdonosának és társtulajdonosának. Az Azure-tevékenységnapló riasztásai segítségével címzetteket és üzenetkezelési beállításokat adhat hozzá az [értesítésekhez,](../azure-monitor/essentials/platform-logs-overview.md)például e-maileket, SMS-eket és webhookokat.  
- Az értesítéssel *elérhetővé válik* egy önkiszolgáló ablak. Ebben az általában 35 napos időszakban kiderítheti, hogy melyik virtuális gép szerepel a hullámban. A karbantartást proaktívan is elindíthatja a saját ütemezési igényeinek megfelelően.
- Az önkiszolgáló időszak után megkezdődik az *ütemezett karbantartási időszak.* Az Azure az időszak egy pontján ütemezi és alkalmazza a szükséges karbantartást a virtuális gépre. 

A két ablak célja, hogy elegendő időt adjon a karbantartás elkezdését és a virtuális gép újraindítását, miközben tudhatja, hogy az Azure mikor indítja el automatikusan a karbantartást.

A virtuálisgép-méretezési Azure Portal, a PowerShell, a REST API és az Azure CLI használatával lekérdezheti a virtuálisgép-méretezési készlet virtuális gépei karbantartási időszakát, és elindíthatja az önkiszolgáló karbantartást.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Érdemes elkezdeni a karbantartást az önkiszolgáló időszak alatt?  

Az alábbi irányelvek segíthetnek eldönteni, hogy a karbantartást egy ön által választott időpontban indítja-e el.

> [!NOTE] 
> Előfordulhat, hogy az önkiszolgáló karbantartás nem érhető el az összes virtuális géphez. Annak megállapításához, hogy a virtuális géphez elérhető-e a proaktív ismételt üzembe állás, keresse meg az **Indítás most** karbantartási állapotát. Az önkiszolgáló karbantartás jelenleg nem érhető el a Azure Cloud Services (webes/feldolgozói szerepkör) és az Azure Service Fabric.


Az önkiszolgáló karbantartás nem ajánlott olyan üzemelő példányok esetén, amelyek rendelkezésre *állási készleteket használnak.* A rendelkezésre állási csoport olyan magas rendelkezésre állású beállítás, amelyben egyszerre csak egy frissítési tartomány van hatással. Rendelkezésre állási készletekhez:

- Hagyja, hogy az Azure aktiválja a karbantartást. Újraindítást igénylő karbantartás esetén a karbantartás frissítési tartomány szerint történik. A frissítési tartományok nem feltétlenül kapják meg egymás után a karbantartást. A frissítési tartományok között 30 perces szünet van.
- Ha a kapacitás egy része ideiglenes elvesztése (1/frissítési tartományszám) problémát jelent, könnyedén kompenzálhatja a elvesztését, ha további példányokat foglal le a karbantartási időszakban.
- Újraindítást nem igénylő karbantartás esetén a frissítések a tartalék tartomány szintjén vannak alkalmazva. 
    
**A következő esetekben** ne használjon önkiszolgáló karbantartást: 

- Ha gyakran, manuálisan, a DevTest Labs használatával, automatikus leállítás használatával vagy ütemezés szerint leállítja a virtuális gépeket. Ezekben az esetekben az önkiszolgáló karbantartás visszaállíthatja a karbantartási állapotot, és további állásidőt okozhat.
- A rövid életű virtuális gépek, amelyekről tudja, a karbantartási időszak vége előtt törlődnek. 
- Nagy méretű, a frissítés után karbantartani kívánt helyi (rövidtű) lemezen tárolt számítási feladatok esetén. 
- Ha gyakran méretezi át a virtuális gépet. Ez a forgatókönyv visszaállíthatja a karbantartási állapotot. 
- Ha olyan ütemezett eseményeket fogadott, amelyek lehetővé teszik a számítási feladat proaktív feladatátvételét vagy türelmi leállítását 15 perccel a karbantartás megkezdése előtt.

**Akkor** használjon önkiszolgáló karbantartást, ha azt tervezi, hogy a virtuális gépet zavartalanul futtatja az ütemezett karbantartási fázisban, és a fenti ellenintézkedések egyike sem érvényes. 

Az önkiszolgáló karbantartást a következő esetekben ajánlott használni:

- Pontosan meg kell kommunikálnia a karbantartási időszakokat a kezelővel vagy az ügyféllel. 
- A karbantartást egy adott dátum szerint kell befejeznie. 
- A biztonságos helyreállítás biztosítása érdekében szabályozni kell a karbantartás sorrendjét, például egy többrétegű alkalmazásban.
- Két frissítési tartomány között több mint 30 percnyi virtuálisgép-helyreállítási időre van szükség. A frissítési tartományok közötti idő szabályozása érdekében egyszerre egy frissítési tartományon kell karbantartást aktiválni a virtuális gépeken.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>A karbantartás által érintett virtuálisgép-méretezési készletek megtekintése a portálon

Ha tervezett karbantartási hullám van ütemezve, a következő karbantartási hullám által érintett virtuálisgép-méretezési Azure Portal. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza a **Minden** szolgáltatás, majd a **Virtuálisgép-méretezési készletek lehetőséget.**
3. A **Virtuálisgép-méretezési készletek alatt** válassza az Oszlopok szerkesztése **lehetőséget** az elérhető oszlopok listájának megnyitásához.
4. Az Elérhető **oszlopok szakaszban** válassza az **Önkiszolgáló** karbantartás lehetőséget, majd helyezze át a Kijelölt **oszlopok listára.** Kattintson az **Alkalmaz** gombra.  

    Az Önkiszolgáló **karbantartási** elem könnyebb megkeresése érdekében az Elérhető oszlopok  szakaszban található legördülő menüt Az összes lehetőségről **Tulajdonságokra** **módosíthatja.**

Az **Önkiszolgáló karbantartás oszlop** megjelenik a virtuálisgép-méretezési készletek listájában. Minden virtuálisgép-méretezési készlet az alábbi értékek egyikét használhatja az önkiszolgáló karbantartási oszlophoz:

| Érték | Leírás |
|-------|-------------|
| Igen | A virtuálisgép-méretezési készletben legalább egy virtuális gép egy önkiszolgáló ablakban található. Ebben az önkiszolgáló időszakban bármikor elkezdheti a karbantartást. | 
| No | Nincsenek virtuális gépek az érintett virtuálisgép-méretezési készlet önkiszolgáló ablakában. | 
| - | A virtuálisgép-méretezési készletek nem részei tervezett karbantartási hullámnak.| 

## <a name="notification-and-alerts-in-the-portal"></a>Értesítések és riasztások a portálon

Az Azure úgy kommunikálja a tervezett karbantartás ütemezését, hogy e-mailt küld az előfizetés tulajdonosának és társtulajdonosok csoportjának. Ehhez a kommunikációhoz tevékenységnapló-riasztások létrehozásával adhat hozzá címzetteket és csatornákat. További információ: [Előfizetési tevékenység figyelése az Azure-tevékenységnaplóval.](../azure-monitor/essentials/platform-logs-overview.md)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza a **Monitor lehetőséget.** 
3. A **Figyelés – Riasztások (klasszikus) panelen** válassza a **+Tevékenységnapló-riasztás hozzáadása lehetőséget.**
4. A **Tevékenységnapló-riasztás hozzáadása lapon** válassza ki vagy adja meg a kért adatokat. A **Feltételek mezőben** adja meg a következő értékeket:
   - **Eseménykategória:** Válassza a **Service Health** lehetőséget.
   - **Szolgáltatások:** Válassza a **Virtual Machine Scale Sets, majd Virtual Machines** lehetőséget.
   - **Típus:** Válassza a **Tervezett karbantartás lehetőséget.** 
    
További információ a tevékenységnapló-riasztások konfigurálásról: [Tevékenységnapló-riasztások létrehozása](../azure-monitor/alerts/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Indítsa el a virtuálisgép-méretezési készlet karbantartását a portálról

A karbantartással kapcsolatos további részleteket a virtuálisgép-méretezési készletek áttekintésében talál. Ha a tervezett karbantartási időszakban a virtuálisgép-méretezési csoportban legalább egy virtuális gép szerepel, egy új értesítési menüszalag lesz hozzáadva az oldal tetején. Válassza az értesítési menüszalagot a Karbantartás **lapra való ugráshoz.** 

A Karbantartás **lapon** láthatja, hogy a tervezett karbantartás melyik virtuálisgép-példányt érinti. A karbantartás elkezdődjön, jelölje be az érintett virtuális gépnek megfelelő jelölőnégyzetet. Ezután válassza a **Start maintenance (Karbantartás kezdete) lehetőséget.**

A karbantartást követően a virtuálisgép-méretezési készlet érintett virtuális gépei karbantartáson esnek át, és átmenetileg nem érhetők el. Ha kihagyta az önkiszolgáló ablakot, továbbra is megjelenik az az időablak, amikor az Azure fenntartja a virtuálisgép-méretezési készletet.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Karbantartási állapot ellenőrzése a PowerShell használatával

A virtuálisgép Azure PowerShell segítségével láthatja, hogy mikor vannak ütemezve a virtuálisgép-méretezési készletekben lévő virtuális gépek karbantartásra. A tervezett karbantartási információk a [Get-AzVmss](/powershell/module/az.compute/get-azvmss) parancsmag használatával érhetők el a paraméter `-InstanceView` használata esetén.
 
A rendszer csak akkor ad vissza karbantartási információkat, ha karbantartást terveznek. Ha nincs olyan karbantartás ütemezve, amely hatással lenne a virtuálisgép-példányra, a parancsmag nem ad vissza karbantartási információkat. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

A **MaintenanceRedeployStatus a következő tulajdonságokat tartalmazza:** 

| Érték | Leírás   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy jelenleg elindíthatja-e a virtuális gép karbantartását. |
| PreMaintenanceWindowStartTime         | A karbantartási önkiszolgáló időszak kezdete, amikor karbantartást kezdeményezhet a virtuális gépen. |
| PreMaintenanceWindowEndTime           | A karbantartási önkiszolgáló időszak vége, amikor karbantartást kezdeményezhet a virtuális gépen. |
| KarbantartásWindowStartTime            | Annak az ütemezett karbantartásnak a kezdete, amelyben az Azure karbantartást kezdeményez a virtuális gépen. |
| MaintenanceWindowEndTime (KarbantartáswindowEndTime)              | A karbantartási ütemezett időszak vége, amelyben az Azure karbantartást kezdeményez a virtuális gépen. |
| LastOperationResultCode               | A virtuális gép karbantartásának kezdeményezésére tett legutóbbi kísérlet eredménye. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>A virtuálisgép-példány karbantartásának elkezdenie a PowerShell használatával

Ha az **IsCustomerInitiatedMaintenanceAllowed** true (igaz) érték van beállítva, elindíthatja a virtuális gép **karbantartását.** Használja a [Set-AzVmss](/powershell/module/az.compute/set-azvmss) parancsmagot a `-PerformMaintenance` paraméterrel.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Karbantartási állapot ellenőrzése a CLI használatával

A tervezett karbantartási információkat az [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances)használatával tudja megtekinteni.
 
A rendszer csak akkor ad vissza karbantartási információkat, ha karbantartást terveznek. Ha nincs ütemezve a virtuálisgép-példányt érintő karbantartás, a parancs nem ad vissza karbantartási információkat. 

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
| LastOperationResultCode               | A karbantartás kezdeményezésére tett legutóbbi kísérlet eredménye a virtuális gépen. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Indítsa el a karbantartást a virtuálisgép-példányon a CLI használatával

A következő hívás karbantartást kezdeményez egy virtuálisgép-példányon, ha true `IsCustomerInitiatedMaintenanceAllowed` (igaz) érték van **beállítva:**

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>GYIK

**K: Miért kell most újraindítani a virtuális gépeket?**

**A:** Bár az Azure platform legtöbb frissítése és frissítése nincs hatással a virtuális gépek rendelkezésre állásra, bizonyos esetekben nem tudjuk elkerülni az Azure-ban üzemeltetett virtuális gépek újraindítását. Több olyan módosítást is összegyűjtöttünk, amelyek miatt újra kell indítani a kiszolgálókat, ami a virtuális gép újraindítását eredményezi.

**K: Ha egy rendelkezésre állási készlet használatával követem a magas rendelkezésre állásra vonatkozó javaslatait, akkor biztonságban vagyok?**

**A:** A rendelkezésre állási vagy virtuálisgép-méretezési készletekben üzembe helyezett virtuális gépek frissítési tartományokat használnak. Karbantartás végrehajtásakor az Azure tiszteletben tart egy frissítési tartományra vonatkozó korlátozást, és nem indítja újra a virtuális gépeket egy másik frissítési tartományból (ugyanabban a rendelkezésre állási csoportban). Az Azure emellett legalább 30 percet vár, mielőtt továbblép a következő virtuális gépcsoportra. 

További információ a magas rendelkezésre állásról: Az Azure-beli virtuális gépek régiói [és rendelkezésre állása.](../virtual-machines/availability.md)

**K: Hogyan kaphatok értesítést a tervezett karbantartásról?**

**A:** A tervezett karbantartási hullám azzal kezdődik, hogy be kell ütemezni egy vagy több Azure-régiót. Nem sokkal később a rendszer e-mail-értesítést küld az előfizetés rendszergazdáinak, társadminisztrának, tulajdonosának és közreműködőinek (előfizetésenként egy e-mailt). Az értesítéshez további csatornákat és címzetteket is konfigurálhat a Tevékenységnapló-riasztások használatával. Ha olyan régióban helyez üzembe virtuális gépet, ahol a tervezett karbantartás már ütemezve van, nem kapja meg az értesítést. Ehelyett ellenőrizze a virtuális gép karbantartási állapotát.

**K: Nem látható semmilyen tervezett karbantartásra utaló jel a portálon, a PowerShellben vagy a parancssori felületen. mi a baj?**

**A:** A tervezett karbantartással kapcsolatos információk csak a tervezett karbantartás által érintett virtuális gépek számára érhetők el a tervezett karbantartás során. Ha nem lát adatokat, előfordulhat, hogy a karbantartási hullám már befejeződött (vagy nem indult el), vagy a virtuális gép már egy frissített kiszolgálón van üzemeltetve.

**K: Meg lehet tudni, hogy pontosan mikor érinti a rendszer a virtuális gépemet?**

**A:** Az ütemezés beállításakor egy többnapos időkeretet határozunk meg. Ezen időszakon belül a kiszolgálók (és a virtuális gépek) pontos karbantartási ütemezése nem ismert. Ha szeretné pontosan tudni a virtuális gépek frissítésének pontos idejét, ütemezett [eseményeket használhat.](../virtual-machines/windows/scheduled-events.md) Ha ütemezett eseményeket használ, a virtuális gépen belülről is lekérdezhet, és 15 perces értesítést kaphat a virtuális gép újraindítása előtt.

**K: Mennyi ideig tart a virtuális gép újraindítása?**

**A:**  A virtuális gép méretétől függően az újraindítás akár több percig is eltarthat az önkiszolgáló karbantartási időszak alatt. Az Azure által kezdeményezett újraindítások során az ütemezett karbantartási időszakban az újraindítás általában körülbelül 25 percet vesz igénybe. Ha Cloud Services (webes/feldolgozói szerepkör), virtuálisgép-méretezési csoportokat vagy rendelkezésre állási csoportokat használ, 30 percet kap a virtuális gépek (frissítési tartomány) egyes csoportja között az ütemezett karbantartási időszakban. 

**K: Nem látok karbantartási információkat a virtuális gépeimről. Mi volt a hiba?**

**A:** Több oka is lehet annak, hogy nem lát karbantartási információkat a virtuális gépeken:
   - Microsoft Internalként megjelölt *előfizetést használ.*
   - A virtuális gépek nincsenek karbantartásra ütemezve. Előfordulhat, hogy a karbantartási hullám véget ért, törölték vagy úgy módosították, hogy a továbbiakban ne legyen hatással a virtuális gépekre.
   - Nem megjelenik a **Karbantartás** oszlop a virtuális gép listanézetében. Bár ezt az oszlopot hozzáadjuk az alapértelmezett nézethez, ha a nézetet nem alapértelmezett oszlopok megtekintésére konfigurálja, manuálisan kell hozzáadnia a **Karbantartás** oszlopot a virtuálisgép-listanézethez.

**K: A virtuális gépem második karbantartásra van ütemezve. miért?**

**A:** Számos esetben a virtuális gép karbantartásra van ütemezve, miután már befejezte a karbantartást, és újra üzembe lett használhatja:
   - Megszüntetjük a karbantartási hullámot, és más hasznos okkal újraindítjuk. Lehetséges, hogy hibát észleltünk a hasznos adatokban, és egyszerűen egy további hasznos információt kell üzembe helyeznünk.
   - A virtuális gépet *egy hardverhiba* miatt egy másik csomóponton szervizelték.
   - Kiválasztotta a virtuális gép leállítását (felszabadítása) és újraindítását.
   - A virtuális **gép automatikus leállítása** be van kapcsolva.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan regisztrálhat karbantartási eseményekre a virtuális gépen belül ütemezett [események használatával.](../virtual-machines/windows/scheduled-events.md)
