---
title: Az Azure Log Analytics munkaterület törlése és helyreállítása | Microsoft Docs
description: Megtudhatja, hogyan törölheti Log Analytics munkaterületét, ha létrehozott egy személyes előfizetést, vagy átalakította a munkaterület modelljét.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/20/2020
ms.openlocfilehash: 83a64e3348d4af768c56609df3df5c9194ec5af1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030868"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Az Azure Log Analytics munkaterület törlése és helyreállítása

Ez a cikk ismerteti az Azure Log Analytics Workspace Soft-delete fogalmát, valamint a törölt munkaterület helyreállításának módját.

## <a name="considerations-when-deleting-a-workspace"></a>Munkaterületek törlésekor megfontolandó szempontok

Ha töröl egy Log Analytics munkaterületet, a rendszer egy törlési műveletet hajt végre, amely lehetővé teszi a munkaterület helyreállítását 14 napon belül, beleértve az adatmennyiséget és a csatlakoztatott ügynököket is, függetlenül attól, hogy a törlés véletlen vagy szándékos volt-e. A Soft-delete időszak után a munkaterület-erőforrás és a hozzá tartozó adatfeldolgozás nem állítható vissza, és 30 napon belül teljes törlésre várólistára kerül. A munkaterület neve "Released", és használható egy új munkaterület létrehozásához.

> [!NOTE]
> Ha szeretné felülbírálni a törlési viselkedést és véglegesen törölni a munkaterületet, kövesse az [állandó munkaterület törlésének](#permanent-workspace-delete)lépéseit.

Fontos, hogy körültekintően járjon el, amikor töröl egy munkaterületet, mert olyan fontos adat és konfiguráció lehet, amely negatív hatással lehet a szolgáltatási műveletre. Tekintse át, hogy az ügynökök, a megoldások és az egyéb Azure-szolgáltatások hogyan tárolják az adataikat Log Analyticsban, például:

* Felügyeleti megoldások
* Azure Automation
* Windows-és Linux-alapú virtuális gépeken futó ügynökök
* A környezetben található Windows és Linux rendszerű számítógépeken futó ügynökök
* System Center Operations Manager

A törlési művelet törli a munkaterület-erőforrást, és a társított felhasználók engedélyei megszakadnak. Ha a felhasználók más munkaterületekhez vannak társítva, akkor továbbra is használhatják a Log Analyticst ezekkel a munkaterületekkel.

## <a name="soft-delete-behavior"></a>Helyreállítható törlés működése

A munkaterület-törlési művelet eltávolítja a munkaterület Resource Manager-erőforrását, de a konfigurációját és az adatait 14 napig őrzi meg a rendszer, miközben megadja a munkaterület törlésének a megjelenését. A munkaterületre való jelentésre konfigurált ügynökök és System Center Operations Manager-felügyeleti csoportok árva állapotban maradnak a törlési időszak alatt. A szolgáltatás továbbra is lehetővé teszi a törölt munkaterület helyreállítását, beleértve az adatforrásokat és a csatlakoztatott erőforrásokat, lényegében a törlést.

> [!NOTE] 
> A telepített megoldásokat és a társított szolgáltatásokat, például a Azure Automation fiókját véglegesen eltávolítja a munkaterületről a törlés időpontjában, és nem állítható helyre. Ezeket úgy kell konfigurálni, hogy a helyreállítási művelet után a munkaterület a korábban konfigurált állapotba kerüljön.

A munkaterületeket a [PowerShell](/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace), a [REST API](/rest/api/loganalytics/workspaces/delete)vagy a [Azure Portal](https://portal.azure.com)használatával törölheti.

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek** lehetőséget.
3. A Log Analytics munkaterületek listájában válasszon ki egy munkaterületet, majd kattintson a középső ablaktábla tetején található **Törlés**  elemre.
4. Megjelenik egy megerősítő lap, amely megjeleníti az adatfeldolgozást a munkaterületre az elmúlt héten. 
5. Ha véglegesen törölni szeretné a munkaterületet, hogy a későbbiekben helyreállítsa a beállítást, jelölje be a **munkaterület végleges törlése** jelölőnégyzetet.
6. A megerősítéshez írja be a munkaterület nevét, majd kattintson a **Törlés** gombra.

   ![Munkaterület törlésének megerősítése](media/delete-workspace/workspace-delete.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Állandó munkaterület törlése
Előfordulhat, hogy a Soft-Delete metódus nem fér el bizonyos helyzetekben, például a fejlesztéshez és a teszteléshez, ahol meg kell ismételnie egy központi telepítést ugyanazzal a beállításokkal és a munkaterület nevével. Ilyen esetekben véglegesen törölheti a munkaterületet, és "felülbírálhatja" a Soft-delete időszakot. Az állandó munkaterület-törlési művelet kiadja a munkaterület nevét, és létrehoz egy új munkaterületet ugyanazzal a névvel.

> [!IMPORTANT]
> Az állandó munkaterület törlési műveletét körültekintően, a visszafordíthatatlan óta használhatja, és nem fogja tudni helyreállítani a munkaterületet és annak adatait.

Ha a Azure Portal segítségével véglegesen törölni szeretne egy munkaterületet, jelölje be a **munkaterület végleges törlése** jelölőnégyzetet a **Törlés** gombra való kattintás előtt.

Ha véglegesen törölni szeretne egy munkaterületet a PowerShell használatával, adja hozzá a "-ForceDelete" címkét a munkaterület végleges törléséhez. A "-ForceDelete" lehetőség jelenleg az az. OperationalInsights 2.3.0 vagy újabb verzióban érhető el. 

```powershell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name" -ForceDelete
```

## <a name="recover-workspace"></a>Munkaterület helyreállítása
Ha véletlenül vagy szándékosan töröl egy Log Analytics munkaterületet, a szolgáltatás a munkaterületet egy olyan törlési állapotba helyezi, amely elérhetetlenné teszi a műveletet. A törölt munkaterület neve a törlési időszakban megmarad, és nem használható új munkaterület létrehozásához. A törlést követően a munkaterület nem állítható vissza, ezért a rendszer végleges törlésre és a hozzá tartozó névre ütemezi, és felhasználható egy új munkaterület létrehozásához.

A munkaterületet helyreállíthatja a Soft-delete időszakban, beleértve az adatait, a konfigurációját és a csatlakoztatott ügynököket is. Az előfizetés és az erőforráscsoport közreműködői engedélyekkel kell rendelkeznie, ahol a munkaterület a törlési művelet előtt található. A munkaterület-helyreállítást a Log Analytics munkaterület újbóli létrehozásával hajtja végre a törölt munkaterület részleteivel együtt, beleértve a következőket:

- Előfizetés azonosítója
- Erőforráscsoport neve
- Munkaterület neve
- Region

> [!IMPORTANT]
> Ha a munkaterületet az erőforráscsoport-törlési művelet részeként törölte, először újra létre kell hoznia az erőforráscsoportot.

### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek** lehetőséget. Megjelenik a kiválasztott hatókörben lévő munkaterületek listája.
3. Kattintson a bal felső menü **helyreállítás** elemére egy olyan oldal megnyitásához, amelyben munkaterületek állíthatók helyre a helyreállítható törlési állapotban.

   ![Képernyőfelvétel a Log Analytics munkaterületek képernyőről Azure Portal a menüsávon Kiemelt kiemeléssel.](media/delete-workspace/recover-menu.png)

4. Válassza ki a munkaterületet, majd kattintson a **helyreállítás** elemre a munkaterület helyreállításához.

   ![Képernyőfelvétel a törölt Log Analytics-munkaterületek visszaállítása párbeszédpanelről Azure Portal és a kiválasztott munkaterület lehetőségre, valamint a helyreállítás gombra.](media/delete-workspace/recover-workspace.png)


### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

A rendszer a helyreállítási művelet után visszaküldi a munkaterületet és az összes adatforrást. A megoldások és a társított szolgáltatások véglegesen el lettek távolítva a munkaterületről a törlés után, és ezeket újra kell konfigurálni, hogy a munkaterület a korábban konfigurált állapotba kerüljön. Előfordulhat, hogy egyes adatmennyiségek nem állnak rendelkezésre a lekérdezéshez a munkaterület helyreállítása után, amíg a társított megoldások újra nem települnek, és a sémáik hozzá lettek adva a munkaterülethez.

## <a name="troubleshooting"></a>Hibaelhárítás

A munkaterület törléséhez legalább *log Analytics közreműködői* engedélyekkel kell rendelkeznie.

* Ha nem biztos abban, hogy a törölt munkaterület helyreállítható törlési állapotban van-e, és visszaállítható, kattintson a Lomtár [megnyitása](#recover-workspace) *log Analytics munkaterületek* lapon a nem kötelezően törölt munkaterületek listájának megtekintéséhez. A véglegesen törölt munkaterületek nem szerepelnek a listában.
* Ha hibaüzenet jelenik meg, akkor *a munkaterület neve már használatban van* , vagy *ütközést* okoz a munkaterület létrehozásakor, az a következő lehet:
  * A munkaterület neve nem érhető el, és a szervezete vagy más ügyfél által használt személy használja.
  * A munkaterületet az elmúlt 14 napban törölték, és a neve a törlési időszak számára fenntartott marad. Ha felül szeretné bírálni a munkaterületet, hogy az azonos nevű új munkaterületet hozzon létre, hajtsa végre az alábbi lépéseket, hogy először helyreállítsa a munkaterületet, majd véglegesen törölje a következőket:<br>
    1. [Állítsa helyre](#recover-workspace) a munkaterületet.
    2. A munkaterület [végleges törlése](#permanent-workspace-delete) .
    3. Hozzon létre egy új munkaterületet ugyanazzal a munkaterület-névvel.
 
      Miután a törlési hívást sikeresen befejezte a háttérben, visszaállíthatja a munkaterületet, és elvégezheti az állandó törlési műveletet a korábban javasolt metódusok egyikében.

* Ha 204-as hibakódot kap, és az *erőforrás nem található* a munkaterület törlésekor, egy egymást követő újrapróbálkozási művelet fordulhat elő. a 204 egy üres válasz, amely általában azt jelenti, hogy az erőforrás nem létezik, így a törlés semmit nem végez.
* Ha törli az erőforráscsoportot és a benne foglalt munkaterületet, a törölt munkaterület megjelenik a [megnyitott](#recover-workspace) Lomtár lapon, azonban a helyreállítási művelet sikertelen lesz a 404 hibakód miatt, mert az erőforráscsoport nem létezik – hozza létre újra az erőforráscsoportot, és próbálkozzon újra a helyreállítással.
