---
title: Erőforrás-naplók összegyűjtése az Azure-erőforrásokból és a Azure Monitor elemzése
description: Oktatóanyag a diagnosztikai beállítások konfigurálásához az Azure-erőforrásokból származó erőforrás-naplók összegyűjtéséhez egy Log Analytics munkaterületre, ahol egy napló lekérdezéssel elemezhetők.
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: d3c3eff32120438fd891cb32fe98d73424f82b7f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734145"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Oktatóanyag: erőforrás-naplók összegyűjtése és elemzése Azure-erőforrásból

Az erőforrás-naplók betekintést nyújtanak az Azure-erőforrások részletes műveleteibe, és hasznosak az állapotuk és a rendelkezésre állásuk monitorozásához. Az Azure-erőforrások automatikusan létrehoznak erőforrás-naplókat, de be kell állítania, hogy hol legyenek összegyűjtve. Ez az oktatóanyag végigvezeti a diagnosztikai beállítás létrehozásának folyamatán, amely az Azure-előfizetésben lévő erőforrásokhoz tartozó erőforrás-naplók összegyűjtését és a napló lekérdezésével történő elemzését ismerteti.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Log Analytics munkaterület létrehozása Azure Monitor
> * Diagnosztikai beállítás létrehozása az erőforrás-naplók összegyűjtéséhez 
> * Egyszerű naplózási lekérdezés létrehozása a naplók elemzéséhez


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-erőforrásra a figyeléshez. Az Azure-előfizetésében bármilyen erőforrást használhat, amely támogatja a diagnosztikai beállításokat. Annak megállapításához, hogy egy erőforrás támogatja-e a diagnosztikai beállításokat, lépjen a Azure Portal menüjére, és ellenőrizze, hogy van-e **diagnosztikai beállítások** lehetőség a menü **figyelés** szakaszában.


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be a Azure Portalba a következő címen: [https://portal.azure.com](https://portal.azure.com) .


## <a name="create-a-workspace"></a>Munkaterület létrehozása
A Azure Monitor Log Analytics munkaterülete különböző forrásokból gyűjti és indexeli a naplózási adatokat, és lehetővé teszi a speciális elemzést egy hatékony lekérdezési nyelv használatával. A Log Analytics munkaterületnek léteznie kell ahhoz, hogy diagnosztikai beállítást hozzon létre az adatküldéshez. Használhat egy meglévő munkaterületet az Azure-előfizetésében, vagy létrehozhat egyet az alábbi eljárással. 

> [!NOTE]
> A **Azure monitor** menüben log Analytics munkaterületeken található adatkezelési munkaterületek is használhatók, a **log Analytics** munkaterületek menüben pedig munkaterületeket hozhat létre és kezelhet.

1. Az **összes szolgáltatás** területen válassza **log Analytics munkaterületek** lehetőséget.
2. A képernyő felső részén kattintson a **Hozzáadás** gombra, és adja meg a következő adatokat a munkaterülethez:
   - **Log Analytics munkaterület**: az új munkaterület neve. Ennek a névnek globálisan egyedinek kell lennie az összes Azure Monitor-előfizetésen belül.
   - **Előfizetés**: válassza ki a munkaterület tárolására szolgáló előfizetést. Ennek nem kell ugyanaz az előfizetése, mint a figyelt erőforrásnak.
   - **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy kattintson az **új létrehozása** lehetőségre egy új létrehozásához. Ennek nem kell ugyanaz az erőforráscsoport, mint a figyelt erőforrásnak.
   - **Hely**: válasszon ki egy Azure-régiót, vagy hozzon létre újat. Ennek a helynek nem kell megegyeznie, mint a figyelt erőforrás.
   - **Díjszabási** csomag: *válassza az utólagos* elszámolású lehetőséget a díjszabási szinten. Ezt az árképzési szintet később is megváltoztathatja. A különböző díjszabási csomagokkal kapcsolatos további információért kattintson a **log Analytics díjszabási** hivatkozásra.

    ![Új munkaterület](media/tutorial-resource-logs/new-workspace.png)

3. A munkaterület létrehozásához kattintson **az OK** gombra.

## <a name="create-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása
A [diagnosztikai beállítások](../essentials/diagnostic-settings.md) határozzák meg, hogy az erőforrás-naplókat el kell-e juttatni egy adott erőforráshoz. Egyetlen diagnosztikai beállításnak több [célhelye](../essentials/diagnostic-settings.md#destinations)is lehet, de ebben az oktatóanyagban csak egy log Analytics munkaterületet fogunk használni.

1. Az erőforrás menüjének **figyelés** területén válassza a **diagnosztikai beállítások** elemet.
2. Rendelkeznie kell egy "nincsenek definiálva diagnosztikai beállítások" üzenettel. Kattintson a **diagnosztikai beállítás hozzáadása** elemre.

    ![Diagnosztikai beállítások](media/tutorial-resource-logs/diagnostic-settings.png)

3. Az egyes diagnosztikai beállítások három alapvető részből állnak:
 
   - **Név**: ez nem jelent jelentős hatást, és egyszerűen csak beszédes lehet.
   - **Célhelyek**: egy vagy több célhely a naplók elküldéséhez. Az összes Azure-szolgáltatás ugyanazt a három lehetséges célhelyet használja. Az egyes diagnosztikai beállítások egy vagy több célhelyet is meghatározhatnak, de az adott típusnak egynél több rendeltetési helye sem lehet. 
   - **Kategóriák**: az egyes célhelyekre küldendő naplók kategóriái. A kategóriák halmaza minden egyes Azure-szolgáltatás esetében eltérő lesz.

4. Válassza a **küldés log Analytics munkaterületre** lehetőséget, majd válassza ki a létrehozott munkaterületet.
5. Válassza ki a gyűjteni kívánt kategóriákat. Az egyes szolgáltatásokhoz tartozó dokumentációban megtekintheti a rendelkezésre álló kategóriák definícióját.

    ![Diagnosztikai beállítás](media/tutorial-resource-logs/diagnostic-setting.png)

6. A diagnosztikai beállítások mentéséhez kattintson a **Mentés** gombra.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Naplók beolvasása a log lekérdezéssel
Az adatok beolvasása egy Log Analytics munkaterületről a Kusto lekérdezési nyelvén (KQL) írt napló lekérdezés használatával történik. A Azure Monitorban bekövetkező elemzések és megoldások naplózási lekérdezéseket biztosítanak az adatok egy adott szolgáltatáshoz való lekéréséhez, de közvetlenül a naplózási lekérdezésekkel és azok eredményeivel is dolgozhatnak a Azure Portal a Log Analytics használatával. 

1. Az erőforrás menüjének **figyelés** területén válassza a **naplók** lehetőséget.
2. Log Analytics megnyílik egy üres lekérdezési ablak, amelynek hatóköre az erőforrásra van beállítva. Minden lekérdezés csak az adott erőforrás rekordjait fogja tartalmazni.

    > [!NOTE]
    > Ha a Azure Monitor menüből nyit meg naplókat, a hatókör a Log Analytics munkaterületre lesz beállítva. Ebben az esetben minden lekérdezés tartalmazni fogja a munkaterület összes rekordját.
   
    ![Képernyőfelvétel: egy logikai alkalmazás naplóit jeleníti meg, amelyek egy új lekérdezést jelenítenek meg a logikai alkalmazás neve kiemelve.](media/tutorial-resource-logs/logs.png)

4. A példában szereplő szolgáltatás erőforrás-naplókat ír a **AzureDiagnostics** táblába, de más szolgáltatások is írhatnak más táblákba. A különböző Azure-szolgáltatások által használt táblák esetében lásd: [támogatott szolgáltatások, sémák és kategóriák az Azure-erőforrás-naplókhoz](../essentials/resource-logs-schema.md) .

    > [!NOTE]
    > Több szolgáltatás erőforrás-naplókat ír a AzureDiagnostics táblába. Ha a Azure Monitor menüből indítja el a Log Analytics, akkor az `where` adott szolgáltatás megadásához hozzá kell adnia egy utasítást az `ResourceProvider` oszlophoz. Amikor elindít Log Analytics egy erőforrás menüjéből, akkor a hatókör csak az adott erőforrás rekordjait állítja be, ezért erre az oszlopra nincs szükség. Tekintse meg a szolgáltatás dokumentációját a példák lekérdezéséhez.


5. Írjon be egy lekérdezést, és kattintson a **Futtatás** elemre az eredmények vizsgálatához. 
6. Lásd: Ismerkedés [a naplók lekérdezésével a Azure monitorban](../logs/get-started-queries.md) a naplók írásához.

    ![Napló lekérdezése](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte, hogyan gyűjthet erőforrás-naplókat egy Log Analytics munkaterületre, hogyan elemezheti az adatokat a naplók írásához.

> [!div class="nextstepaction"]
> [Ismerkedés az Azure Monitor-naplólekérdezésekkel](../logs/get-started-queries.md)