---
title: A virtuálisgép-méretezési csoportokra vonatkozó karbantartási értesítések az Azure-ban
description: Megtekintheti a karbantartási értesítéseket, és megkezdheti az Azure-beli virtuálisgép-méretezési csoportokhoz tartozó önkiszolgáló karbantartást.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 11/12/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 85e4b6a4d0ff1c3bd7e634311a36396a74408419
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594437"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok – tervezett karbantartás értesítései


Az Azure rendszeres időközönként frissítéseket végez a virtuális gépek (VM-EK) számára a gazdagép-infrastruktúra megbízhatóságának, teljesítményének és biztonságának javítása érdekében. A frissítések tartalmazhatják az üzemeltetési környezet javítását, illetve a hardverek frissítését és leszerelését. A legtöbb frissítés nem befolyásolja az üzemeltetett virtuális gépeket. A frissítések azonban a következő helyzetekben érintik a virtuális gépeket:

- Ha a karbantartás nem igényel újraindítást, az Azure néhány másodpercig szünetelteti a virtuális gépet, amíg a rendszer frissíti a gazdagépet. Az ilyen típusú karbantartási műveletek tartalék tartományra vonatkoznak a tartalék tartomány alapján. A rendszer leállítja az előrehaladást, ha a rendszer figyelmeztetési állapottal kapcsolatos riasztásokat fogad.

- Ha a karbantartás újraindítást igényel, a karbantartás megtervezése után értesítést kap. Ezekben az esetekben egy olyan időablakot kap, amely általában 35 nap, ahol elindíthatja a karbantartást, ha Ön is működik.


Az újraindítást igénylő tervezett karbantartás hullámokban van ütemezve. Minden hullám különböző hatókörrel rendelkezik (régiók):

- A Wave az ügyfeleknek küldött értesítésekkel kezdődik. Alapértelmezés szerint a rendszer értesítést küld az előfizetés tulajdonosának és a közös tulajdonosoknak. Az értesítésekhez hozzáadhat címzetteket és üzenetküldési beállításokat, például e-maileket, SMS-t és webhookokat az Azure- [Tevékenységnaplók riasztásai](../azure-monitor/essentials/platform-logs-overview.md)használatával.  
- A Notification *szolgáltatással egy önkiszolgáló ablak* is elérhetővé válik. Ez az ablak általában 35 nap alatt megkeresheti, hogy a virtuális gépek mely része szerepeljen a Wave-ben. Proaktív módon megkezdheti a karbantartást a saját ütemezési igényei szerint.
- Az önkiszolgáló ablak után megkezdődik az *ütemezett karbantartási* időszak. Ezen időszak alatt az Azure a szükséges karbantartást a virtuális gépre irányítja és alkalmazza. 

Ahhoz, hogy az Azure automatikusan megkezdje a karbantartást, a két Windows rendszernek megfelelő időt kell biztosítania a karbantartás elindítására és a virtuális gép újraindítására.

A Azure Portal, a PowerShell, a REST API és az Azure CLI segítségével lekérdezheti a karbantartási időszakokat a virtuálisgép-méretezési csoport virtuális gépei számára, és elindíthatja az önkiszolgáló karbantartást.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>A karbantartást az önkiszolgáló ablakon belül kell elkezdeni?  

Az alábbi irányelvek segítségével eldöntheti, hogy a kiválasztott időpontban szeretné-e elindítani a karbantartást.

> [!NOTE] 
> Előfordulhat, hogy az önkiszolgáló karbantartás nem érhető el az összes virtuális géphez. Annak megállapításához, hogy elérhető-e az előjelzéses újratelepítés a virtuális géphez, keresse meg a **Start Now** (Karbantartás) állapotot. Jelenleg az önkiszolgáló karbantartás nem érhető el az Azure Cloud Services (webes és feldolgozói szerepkör) és az Azure Service Fabric számára.


Az önkiszolgáló karbantartás nem ajánlott a *rendelkezésre állási csoportokat* használó központi telepítések esetén. A rendelkezésre állási csoportok olyan nagy rendelkezésre állású telepítések, amelyekben egyszerre csak egy frissítési tartományt érint. Rendelkezésre állási csoportokhoz:

- Hagyja, hogy az Azure aktiválja a karbantartást. Az újraindítást igénylő karbantartás esetén a frissítés a tartomány frissítési tartománya alapján történik. A frissítési tartományok nem feltétlenül kapják meg a karbantartást egymás után. A frissítési tartományok között 30 perces szünet van.
- Ha az egyes kapacitások (1/frissítési tartományok száma) ideiglenes elvesztése aggodalomra ad okot, egyszerűen kompenzálhatja a veszteséget, ha a karbantartási időszakban további példányokat foglal le.
- Az újraindítást nem igénylő karbantartás esetén a frissítések a tartalék tartomány szintjén lesznek alkalmazva. 
    
**Ne** használja az önkiszolgáló karbantartást az alábbi helyzetekben: 

- Ha a virtuális gépeket gyakran vagy manuálisan állítja le a DevTest Labs használatával, az automatikus leállítás vagy az ütemterv követése révén. Ezekben a forgatókönyvekben az önkiszolgáló karbantartás visszaállíthatja a karbantartási állapotot, és további állásidőt eredményezhet.
- A rövid élettartamú virtuális gépeken, amelyeket tudni fog, a karbantartási hullám vége előtt törölni fogjuk. 
- A frissítés után megőrizni kívánt helyi (ideiglenes) lemezen található nagy állapotú munkaterhelések esetén. 
- Ha gyakran lakik a virtuális gép. Ez a forgatókönyv a karbantartási állapotot is visszaállíthatja. 
- Ha olyan ütemezett eseményeket fogadott el, amelyek a karbantartási leállítás megkezdése előtt 15 perccel lehetővé teszik az előjelzéses feladatátvételt vagy a számítási feladatok zökkenőmentes leállítását.

**Használja az** önkiszolgáló karbantartást, ha azt tervezi, hogy az ütemezett karbantartási fázisban megszakítás nélkül futtatja a virtuális gépet, és a fenti counterindications egyikét sem alkalmazza. 

Az önkiszolgáló karbantartást a következő esetekben érdemes használni:

- Egy pontos karbantartási időszakot kell kommunikálnia a felügyelethez vagy az ügyfélhez. 
- A karbantartást egy adott dátummal kell végrehajtania. 
- A biztonságos helyreállítás biztosításához meg kell határoznia a karbantartási sorozatot, például egy többrétegű alkalmazásban.
- Két frissítési tartomány között több mint 30 percet kell használnia a virtuális gépek helyreállítási idejét. A frissítési tartományok közötti idő szabályozásához egyszerre kell elindítania a virtuális gépek karbantartását egy frissítési tartományon.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>A portál karbantartás által érintett virtuálisgép-méretezési csoportjainak megtekintése

Tervezett karbantartási hullám ütemezésekor megtekintheti azoknak a virtuálisgép-méretezési csoportoknak a listáját, amelyeket a következő karbantartási hullám érint a Azure Portal használatával. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza a **minden szolgáltatás** lehetőséget, majd válassza a **virtuálisgép-méretezési** csoportok lehetőséget.
3. A **virtuálisgép-méretezési** csoportok területen válassza az **Oszlopok szerkesztése** lehetőséget az elérhető oszlopok listájának megnyitásához.
4. Az **elérhető oszlopok** szakaszban válassza az **önkiszolgáló karbantartás** lehetőséget, majd helyezze át a **kijelölt oszlopok** listájára. Kattintson az **Alkalmaz** gombra.  

    Ahhoz, hogy az **önkiszolgáló karbantartási** elem könnyebben megkereshető legyen, az **összesből** a **Tulajdonságok** lehetőségre kattintva megváltoztathatja a legördülő lehetőséget az **elérhető oszlopok** szakaszban.

Az **önkiszolgáló karbantartási** oszlop most megjelenik a virtuálisgép-méretezési csoportok listájában. A virtuálisgép-méretezési csoportokhoz a következő értékek egyike tartozhat az önkiszolgáló karbantartási oszlophoz:

| Érték | Leírás |
|-------|-------------|
| Igen | A virtuálisgép-méretezési csoport legalább egy virtuális gépe önkiszolgáló ablakban található. Az önkiszolgáló ablakon bármikor elindíthatja a karbantartást. | 
| No | Az érintett virtuálisgép-méretezési csoport önkiszolgáló ablakában egyetlen virtuális gép sem található. | 
| - | A virtuális gépek méretezési csoportjai nem részei a tervezett karbantartási hullámnak.| 

## <a name="notification-and-alerts-in-the-portal"></a>Értesítés és riasztások a portálon

Az Azure a tervezett karbantartásra vonatkozó ütemtervet küld az előfizetés tulajdonosának és a közös tulajdonosok csoportjának e-mail-címének elküldésével. A kommunikációhoz címzetteket és csatornákat is hozzáadhat a tevékenység naplójának riasztásai létrehozásával. További információ: [előfizetés figyelése tevékenység az Azure-tevékenység naplójában](../azure-monitor/essentials/platform-logs-overview.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza a **figyelő** elemet. 
3. A **figyelő-riasztások (klasszikus)** ablaktáblán válassza a **+ tevékenység naplójának hozzáadása riasztás** elemet.
4. A **tevékenység naplójának hozzáadása riasztás** lapon válassza ki vagy adja meg a kért adatokat. A **feltételek** területen győződjön meg arról, hogy a következő értékeket állítja be:
   - **Esemény kategóriája**: válassza a **Service Health** lehetőséget.
   - **Szolgáltatások**: válassza **a Virtual Machine Scale sets és Virtual Machines** lehetőséget.
   - **Típus**: válassza a **tervezett karbantartás** lehetőséget. 
    
A műveletnapló-riasztások konfigurálásával kapcsolatos további tudnivalókért tekintse meg a [műveletnapló riasztások létrehozása](../azure-monitor/alerts/activity-log-alerts.md) című témakört.
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>A virtuális gépi méretezési csoport karbantartásának elindítása a portálról

A karbantartással kapcsolatos részleteket a virtuálisgép-méretezési csoportok áttekintésében tekintheti meg. Ha a tervezett karbantartási hullám tartalmaz legalább egy virtuális gépet a virtuálisgép-méretezési csoportba, egy új értesítési menüszalag kerül hozzáadásra az oldal tetején. Válassza ki az értesítési menüszalagot a **karbantartás** lapra ugráshoz. 

A **karbantartás** lapon láthatja, hogy a tervezett karbantartás milyen virtuálisgép-példányt érint. A karbantartás indításához jelölje be az érintett virtuális géphez tartozó jelölőnégyzetet. Ezután válassza a  **karbantartás indítása** lehetőséget.

A karbantartás megkezdése után a virtuálisgép-méretezési csoportba tartozó érintett virtuális gépek karbantartás alatt állnak, és átmenetileg nem érhetők el. Ha kihagyta az önkiszolgáló ablakot, akkor továbbra is megtekintheti az időablakot, amikor a virtuálisgép-méretezési készletet az Azure fogja karbantartani.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Karbantartási állapot ellenőrzéséhez a PowerShell használatával

A Azure PowerShell használatával megtekintheti, hogy a virtuálisgép-méretezési csoportokban lévő virtuális gépek karbantartásra vannak-e ütemezve. A tervezett karbantartási információk a [Get-AzVmss](/powershell/module/az.compute/get-azvmss) parancsmag használatával érhetők el, ha a `-InstanceView` paramétert használja.
 
A karbantartási adatokat csak akkor adja vissza a rendszer, ha a karbantartást tervezték. Ha nincs ütemezve olyan karbantartás, amely hatással van a virtuálisgép-példányra, a parancsmag nem ad vissza karbantartási információt. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

A **MaintenanceRedeployStatus** alatt a következő tulajdonságokat adja vissza: 

| Érték | Leírás   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy a virtuális gép karbantartását jelenleg lehet-e elindítani. |
| PreMaintenanceWindowStartTime         | A karbantartás önkiszolgáló ablakának kezdete, ha a virtuális gép karbantartását kezdeményezi. |
| PreMaintenanceWindowEndTime           | A karbantartási önkiszolgáló ablak befejezése, ha a virtuális gép karbantartását kezdeményezheti. |
| MaintenanceWindowStartTime            | Azon karbantartási időszak kezdete, amelyben az Azure a virtuális gépen kezdeményezi a karbantartást. |
| MaintenanceWindowEndTime              | A karbantartás ütemezett ablakának vége, amelyben az Azure a virtuális gépen kezdeményezi a karbantartást. |
| LastOperationResultCode               | A virtuális gép karbantartásának kezdeményezésére tett legutóbbi kísérlet eredménye. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>A virtuálisgép-példány karbantartásának elindítása a PowerShell használatával

A karbantartást elindíthatja egy virtuális gépen, ha a **IsCustomerInitiatedMaintenanceAllowed** értéke **true (igaz**). Használja a [set-AzVmss](/powershell/module/az.compute/set-azvmss) parancsmagot a `-PerformMaintenance` paraméterrel.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>A karbantartási állapot ellenőrzéséhez használja a CLI-t

A tervezett karbantartási információkat az [az vmss List-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances)paranccsal tekintheti meg.
 
A karbantartási adatokat csak akkor adja vissza a rendszer, ha a karbantartást tervezték. Ha a virtuálisgép-példányt érintő karbantartás nincs ütemezve, a parancs nem ad vissza karbantartási információt. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Az egyes virtuálisgép-példányok **MaintenanceRedeployStatus** a következő tulajdonságokat adja vissza: 

| Érték | Leírás   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy a virtuális gép karbantartását jelenleg lehet-e elindítani. |
| PreMaintenanceWindowStartTime         | A karbantartás önkiszolgáló ablakának kezdete, ha a virtuális gép karbantartását kezdeményezi. |
| PreMaintenanceWindowEndTime           | A karbantartási önkiszolgáló ablak befejezése, ha a virtuális gép karbantartását kezdeményezheti. |
| MaintenanceWindowStartTime            | Azon karbantartási időszak kezdete, amelyben az Azure a virtuális gépen kezdeményezi a karbantartást. |
| MaintenanceWindowEndTime              | A karbantartás ütemezett ablakának vége, amelyben az Azure a virtuális gépen kezdeményezi a karbantartást. |
| LastOperationResultCode               | A virtuális gép karbantartásának kezdeményezésére tett legutóbbi kísérlet eredménye. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>A virtuálisgép-példány karbantartásának elindítása a parancssori felület használatával

A következő hívás egy virtuálisgép-példány karbantartását kezdeményezi `IsCustomerInitiatedMaintenanceAllowed` , ha a értéke **true (igaz**):

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>GYIK

**K: Miért van szükség a virtuális gépek újraindítására?**

**A:** Habár az Azure-platform frissítései és frissítései nem érintik a virtuális gépek rendelkezésre állását, bizonyos esetekben nem tudjuk elkerülni az Azure-ban üzemeltetett virtuális gépek újraindítását. Több olyan módosítást is összegyűjtöttünk, amelyek a virtuális gépek újraindítását eredményező kiszolgálók újraindítását igénylik.

**K: Ha a magas rendelkezésre állásra vonatkozó javaslatokat a rendelkezésre állási csoport használatával követem, akkor biztonságban vagyok?**

**A:** A rendelkezésre állási csoportokban vagy a virtuálisgép-méretezési csoportokban üzembe helyezett virtuális gépek frissítési tartományokat használnak. A karbantartás során az Azure betartja a frissítési tartomány korlátozását, és nem indít újra virtuális gépeket egy másik frissítési tartományból (ugyanazon rendelkezésre állási csoporton belül). Az Azure a virtuális gépek következő csoportjára való áttérés előtt legalább 30 percet vár. 

A magas rendelkezésre állással kapcsolatos további információkért lásd: [régiók és rendelkezésre állás az Azure-beli virtuális gépek](../virtual-machines/availability.md)számára.

**K: Hogyan kaphatok értesítést a tervezett karbantartásról?**

**A:** Egy tervezett karbantartási hullám egy vagy több Azure-régióra vonatkozó ütemterv beállításával kezdődik. Az előfizetés-adminisztrátorok, a közös adminisztrátorok, a tulajdonosok és a közreműködők e-mailben értesítést kapnak (egy e-mail-cím/előfizetés). Az értesítéshez tartozó további csatornákat és címzetteket a műveletnapló riasztásai használatával lehet konfigurálni. Ha olyan régióba helyez üzembe egy virtuális gépet, amelyben a tervezett karbantartás már be van ütemezve, akkor nem kapja meg az értesítést. Ehelyett vizsgálja meg a virtuális gép karbantartási állapotát.

**K: nem látom a tervezett karbantartást a portálon, a PowerShellen vagy a parancssori felületen. mi a baj?**

**A:** A tervezett karbantartással kapcsolatos információk a tervezett karbantartási hullámokban csak a tervezett karbantartás által érintett virtuális gépek esetében érhetők el. Ha nem látja az adatait, előfordulhat, hogy a karbantartási hullám már befejeződött (vagy nem indult el), vagy a virtuális gép már egy frissített kiszolgálón található.

**K: van mód arra, hogy pontosan tudja, mikor kell a virtuális gépet érinteni?**

**A:** Az ütemterv beállításakor több napos időablakot határozunk meg. Ezen időszakon belül a kiszolgálók (és a virtuális gépek) pontos karbantartási ütemezése nem ismert. Ha tudni szeretné, hogy a virtuális gépek milyen pontos időt fognak frissíteni, használhatja az [ütemezett eseményeket](../virtual-machines/windows/scheduled-events.md). Amikor ütemezett eseményeket használ, lekérdezheti a virtuális gépről, és 15 perces értesítést kaphat a virtuális gép újraindítása előtt.

**K: mennyi időt vesz igénybe a virtuális gép újraindítása?**

**A:**  A virtuális gép méretétől függően az újraindítás akár több percet is igénybe vehet az önkiszolgáló karbantartási időszak során. Az ütemezett karbantartási időszakban az Azure által kezdeményezett újraindítások során az újraindítás általában körülbelül 25 percet vesz igénybe. Ha Cloud Services (webes/feldolgozói szerepkör), virtuálisgép-méretezési csoportokat vagy rendelkezésre állási csoportokat használ, a rendszer 30 percet kap a virtuális gépek egyes csoportjai között az ütemezett karbantartási időszak alatt. 

**K: nem látok semmilyen karbantartási információt a virtuális gépeken. Mi volt a baj?**

**A:** Több oka is van annak, hogy miért nem láthatók karbantartási információk a virtuális gépeken:
   - *Microsoft belsőként* jelölt előfizetést használ.
   - A virtuális gépek nincsenek karbantartásra ütemezve. Előfordulhat, hogy a karbantartási hullám véget ért, megszakították, vagy módosították, hogy a virtuális gépeket már nem érinti.
   - Nem rendelkezik a virtuális gépek listájának nézetéhez hozzáadott **karbantartási** oszloppal. Bár ezt az oszlopot az alapértelmezett nézethez adta hozzá, ha úgy konfigurálja a nézetet, hogy a nem alapértelmezett oszlopok megjelenjenek, manuálisan kell hozzáadnia a **karbantartási** oszlopot a virtuálisgép-lista nézetéhez.

**K: a virtuális gép másodszor is karbantartásra van ütemezve. miért?**

**A:** Számos felhasználási esetben a virtuális gép karbantartásra van ütemezve, miután befejezte a karbantartást és az újraüzembe helyezést:
   - Megszakítottuk a karbantartási hullámot, és egy másik hasznos adattartalommal újraindítottuk. Előfordulhat, hogy hibás adattartalmat észlelt, ezért egyszerűen üzembe kell helyezni egy további hasznos adatot.
   - A virtuális gép egy hardverhiba miatt *meggyógyult* egy másik csomópontra.
   - Azt választotta, hogy leállítja (felszabadítja), majd újraindítja a virtuális gépet.
   - A virtuális gép **automatikus leállítása** be van kapcsolva.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan regisztrálhat karbantartási eseményekre a virtuális gépen a [ütemezett események](../virtual-machines/windows/scheduled-events.md)használatával.
