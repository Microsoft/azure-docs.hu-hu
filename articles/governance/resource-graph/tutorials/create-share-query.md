---
title: 'Oktatóanyag: lekérdezések kezelése Azure Portalban'
description: Ebben az oktatóanyagban létrehoz egy Resource Graph-lekérdezést, és megosztja az új lekérdezést a Azure Portal többi részével.
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: 7240a843cfa041137b28284e396c8a4b62a81926
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98916679"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Oktatóanyag: Azure Resource Graph-lekérdezés létrehozása és megosztása a Azure Portal

Az Azure Resource Graph Explorer lehetővé teszi, hogy közvetlenül a Azure Portal mentse az erőforrás-gráf lekérdezéseit. Kétféle lekérdezés létezik: _magán_ -és _megosztott_. A rendszer a Azure Portal beállításokban menti a privát lekérdezést. Míg a megosztott lekérdezés olyan Azure Resource Manager erőforrás, amely az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) felügyelhető és erőforrás-zárolásokkal védett. Mindkét típusú lekérdezés titkosítva van a nyugalmi állapotban.

A lekérdezéseknek a Azure Portalban való mentésével megtakaríthatja a kedvenc vagy gyakran használt lekérdezések keresésének időpontját. A lekérdezések megosztásakor segít a csapatnak megvalósítani a konzisztencia és a hatékonyság célkitűzéseit az ismétlődéssel.

Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
> - Privát lekérdezés létrehozása és törlése
> - Megosztott lekérdezés létrehozása
> - Megosztott lekérdezések felderítése
> - Megosztott lekérdezés törlése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="create-and-delete-a-private-query"></a>Privát lekérdezés létrehozása és törlése

A privát lekérdezések csak az őket létrehozó fiók számára érhetők el és láthatók. Ahogy azokat a rendszer a fiók Azure Portal beállításaiban menti, azokat csak a Azure Portal belül lehet létrehozni, használni és törölni. Egy privát lekérdezés nem Resource Manager-erőforrás. Új privát lekérdezés létrehozásához kövesse az alábbi lépéseket:

1. A portál menüjében válassza a **minden szolgáltatás** lehetőséget, vagy használja az összes oldal tetején található Azure Search mezőt. Keresse meg és válassza ki az **Erőforrásgrafikon Explorert**.

1. Az Azure Resource Graph Explorer **lekérdezés 1** lapján adja meg a következő lekérdezést:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Válassza a **lekérdezés futtatása** lehetőséget a lekérdezés eredményeinek megtekintéséhez az alsó ablaktáblán.

   A lekérdezéssel kapcsolatos további információkért lásd: [minták – virtuális gépek száma operációs rendszer típusa szerint](../samples/starter.md#count-os).

1. Válassza a **Mentés** vagy a **Mentés másként** lehetőséget, írja be a **virtuális gépek száma operációs rendszer** szerint a nevet, hagyja a típust **privát lekérdezésként**, majd kattintson **a Mentés gombra** a **lekérdezés mentése** ablaktábla alján. A lap címe módosul az **1. lekérdezésből** a **virtuális gépek operációs rendszer szerinti megszámlálásához**.

1. Lépjen távolabb az Azure Resource Graph Explorerben a Azure Portal, majd térjen vissza. Figyelje meg, hogy a mentett lekérdezés már nem jelenik meg, és az **1. lekérdezés** lapot adta vissza.

1. Válassza **a lekérdezés megnyitása** lehetőséget. Győződjön meg arról, hogy a típus **privát lekérdezés**. Az operációs rendszer most már a **lekérdezés neve** listában megjelenik a mentett nevek **száma virtuális gépek** . Amikor kiválasztja a mentett lekérdezés cím hivatkozását, a rendszer betölti egy új lapra a lekérdezés nevével.

   > [!NOTE] 
   > Ha egy mentett lekérdezés meg van nyitva, és a TAB megjeleníti a nevét, a **Save (Mentés** ) gombra kattintva frissítheti az összes végrehajtott módosítást. Ha új mentett lekérdezést szeretne létrehozni ebből a nyílt lekérdezésből, válassza a **Mentés másként** lehetőséget, és folytassa, ha új lekérdezést ment.

1. A mentett lekérdezés törléséhez válassza ismét **a lekérdezés megnyitása** lehetőséget, és ellenőrizze, hogy a **Type (típus** ) mező **privát lekérdezésre** van-e beállítva. A mentett lekérdezés sorában `Count VMs by OS` kattintson a **Törlés** (Lomtár ikon) elemre. A megerősítő párbeszédpanelen válassza az **Igen** lehetőséget a lekérdezés törlésének befejezéséhez.
   Ezután zárjuk be a **lekérdezés megnyitása** ablaktáblát.

## <a name="create-a-shared-query"></a>Megosztott lekérdezés létrehozása

Egy privát lekérdezéstől eltérően a megosztott lekérdezés egy Resource Manager-erőforrás. Ez azt jelenti, hogy a lekérdezés egy erőforráscsoport számára lesz mentve, felügyelhető és szabályozható az Azure RBAC, és az erőforrás-zárolásokkal is védelmet biztosíthat. Erőforrásként bárki, aki rendelkezik a megfelelő engedélyekkel, láthatja és használhatja azt. Új megosztott lekérdezés létrehozásához kövesse az alábbi lépéseket:

1. A portál menüjében válassza a **minden szolgáltatás** lehetőséget, vagy használja az összes oldal tetején található Azure Search mezőt, és válassza ki az **Erőforrásgrafikon-kezelőt**.

1. Az Azure Resource Graph Explorer **lekérdezés 1** lapján adja meg a következő lekérdezést:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Válassza a **lekérdezés futtatása** lehetőséget a lekérdezés eredményeinek megtekintéséhez az alsó ablaktáblán.

   A lekérdezéssel kapcsolatos további információkért lásd: [minták – virtuális gépek száma operációs rendszer típusa szerint](../samples/starter.md#count-os).

1. Válassza a **Mentés** vagy **a Mentés másként** lehetőséget.

   :::image type="content" source="../media/create-share-query/save-shared-query-buttons.png" alt-text="Az új lekérdezés mentése a Save (Mentés) gomb használatával" border="false":::

1. A **lekérdezés mentése** ablaktáblán adja meg a **virtuális gépek száma operációs rendszer szerint** a nevet.

1. Módosítsa a típust **megosztott lekérdezésre**, állítsa a leírást a **virtuális gépek számának az operációs rendszer típusa** szerint, és állítsa be az **előfizetést** a lekérdezési erőforrás létrehozási helyének megadásához.

1. Hagyja bejelölve a **Közzététel az erőforráshoz – Graph – lekérdezések erőforráscsoport** jelölőnégyzetet, és az **erőforráscsoport helye** az USA **nyugati középső** régiója.

1. Kattintson a Save ( **Mentés** ) gombra a **lekérdezés mentése** ablaktábla alján. A lap címe módosul az **1. lekérdezésből** a **virtuális gépek operációs rendszer szerinti megszámlálásához**. Az erőforrás **-gráf-lekérdezések** erőforráscsoport első használatakor a Mentés a vártnál hosszabb időt vesz igénybe, mert az erőforráscsoport létrejön.
   
   :::image type="content" source="../media/create-share-query/save-shared-query-window.png" alt-text="Az új lekérdezés mentése megosztott lekérdezésként" border="false":::

   > [!NOTE] 
   > Ha meg szeretné adni egy meglévő erőforráscsoport nevét, akkor törölheti a **Közzététel az erőforrás-gráf-lekérdezések erőforráscsoporthoz** jelölőnégyzetet. A lekérdezések alapértelmezett megnevezett erőforráscsoport használatával könnyebben derítheti fel a megosztott lekérdezéseket. Emellett nyilvánvalóvá teszi az erőforráscsoport célját. A meglévő engedélyek alapján azonban dönthet úgy, hogy egy meglévő erőforráscsoportot választ ki biztonsági okokból.

1. Lépjen távolabb az Azure Resource Graph Explorerben a Azure Portal, majd térjen vissza. Figyelje meg, hogy a mentett lekérdezés már nem jelenik meg, és az **1. lekérdezés** lapot adta vissza.

1. Válassza **a lekérdezés megnyitása** lehetőséget. Ellenőrizze, hogy a típus a **megosztott lekérdezésre** van-e beállítva, és hogy az **előfizetés** és az **erőforráscsoport** kombinációja egyezik-e a lekérdezés mentésekor. A mentett **darabszámú virtuális gépek operációsrendszer** -elem alapján most megjelenik a **lekérdezés neve** listában. Válassza ki a mentett lekérdezés cím hivatkozását egy új lapra való betöltéshez a lekérdezés nevével. Megosztott lekérdezésként megjelenít egy ikont a cím melletti lapon, és megoszthatja azt.

   :::image type="content" source="../media/create-share-query/show-saved-shared-query.png" alt-text="A megosztott lekérdezés ikonjának megjelenítése" border="false":::

   > [!NOTE] 
   > Ha egy mentett lekérdezés meg van nyitva, és a TAB megjeleníti a nevét, a **Save (Mentés** ) gombra kattintva frissítheti az összes végrehajtott módosítást. Új mentett lekérdezés létrehozásához válassza a **Mentés másként** lehetőséget, és folytassa, ha új lekérdezést ment.

## <a name="discover-shared-queries"></a>Megosztott lekérdezések felderítése

Mivel a megosztott lekérdezés egy Resource Manager-erőforrás, többféleképpen is megtalálhatja a következőt:

- A Resource Graph Explorerben. Válassza a **lekérdezés megnyitása** lehetőséget, és állítsa be a típust **megosztott lekérdezésre**.
- Az erőforrás-gráf lekérdezési portál oldalán.
- Azt az erőforráscsoportot, amelyben a megosztott lekérdezés el lett mentve.
- Lekérdezésen keresztül az erőforrás-gráfon.

### <a name="view-resource-graph-queries"></a>Resource Graph-lekérdezések megtekintése

A Azure Portal az Erőforrásgrafikon-lekérdezések lap azokat a megosztott lekérdezéseket jeleníti meg, amelyekhez a bejelentkezett fiók hozzáfér. Ezen a lapon engedélyezheti a szűrést név, előfizetés, erőforráscsoport és az Erőforrásgrafikon-lekérdezés egyéb tulajdonságai alapján. Az erőforrás-gráf lekérdezéseit az interfész használatával is címkézheti, exportálhatja és törölheti.

A lekérdezések egyikének kiválasztásával megnyílik az erőforrás-gráf lekérdezési lapja. A többi Resource Manager-erőforráshoz hasonlóan ez az oldal interaktív áttekintést nyújt a műveletnapló, a hozzáférés-vezérlés és a címkék mellett. Ezen a lapon közvetlenül is alkalmazhat erőforrás-zárolást.

Az összes szolgáltatás vagy az összes oldal tetején található Azure Search (az összes **szolgáltatás** ) lehetőség kiválasztásával szerezze be az Erőforrásgrafikon-lekérdezések lapot a portál menüjében. Keresse meg és válassza ki a **Resource Graph Explorert**.

### <a name="list-resource-groups-resources"></a>Erőforráscsoportok erőforrásainak listázása

Az erőforrás-gráf lekérdezés az erőforráscsoport részét képező egyéb erőforrások mellett szerepel.
Ha kijelöli az Erőforrásgrafikon-lekérdezést, megnyílik a lekérdezés lapja. A három pont és a helyi menü beállításai (a jobb gombbal kattintva) ugyanúgy működnek, mint az erőforrás-gráf lekérdezése lapon.

### <a name="query-resource-graph"></a>Erőforrás-diagram lekérdezése

Az erőforrás-gráf lekérdezéseit egy lekérdezésen keresztül keresheti meg az erőforrás-diagramon. A következő erőforrás-gráf lekérdezési határértékek típus szerint `Microsoft.ResourceGraph/queries` , majd a használatával `project` csak a név, a módosítás időpontja és a lekérdezés szerepel:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="run-a-shared-query"></a>Megosztott lekérdezés futtatása

Az erőforrás-gráf megosztott lekérdezése a `{{shared-query-uri}}` szintaxissal (előzetes verzió) is futtatható. További információ: [megosztott lekérdezési szintaxis](../concepts/query-language.md#shared-query-syntax).

## <a name="delete-a-shared-query"></a>Megosztott lekérdezés törlése

Ha már nincs szükség megosztott lekérdezésre, törölje azt. Egy megosztott lekérdezés törlésével eltávolítja a megfelelő Resource Manager-erőforrást. Minden olyan irányítópult, amelyre az eredmények diagramja be lett rögzítve, hibaüzenet jelenik meg. Ha ez a hibaüzenet jelenik meg, a **Eltávolítás az irányítópultról** gombbal törölje az irányítópultot.

A megosztott lekérdezéseket az alábbi felületeken törölheti:
- Resource Graph-lekérdezések lap
- Erőforrás-diagram lekérdezési lapja
- A **lekérdezés megnyitása** lap a Resource Graph Explorerben
- Erőforráscsoportok lap

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült az Oktatóanyaggal, törölje a létrehozott privát és megosztott lekérdezéseket, ha már nem szeretné őket.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban privát és megosztott lekérdezéseket hozott létre. Ha többet szeretne megtudni az erőforrás-gráf nyelvéről, folytassa a lekérdezés nyelvének részletei lapon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](../concepts/query-language.md)