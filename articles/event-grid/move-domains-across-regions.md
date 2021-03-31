---
title: Azure Event Grid tartományok áthelyezése másik régióba
description: Ebből a cikkből megtudhatja, hogyan helyezhet át Azure Event Grid-tartományokat az egyik régióból egy másik régióba.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: fff8638a819511f84f15c52ad0695cdd5759f971
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89084549"
---
# <a name="move-azure-event-grid-domains-to-another-region"></a>Azure Event Grid tartományok áthelyezése másik régióba
Előfordulhat, hogy több okból is át szeretné helyezni az erőforrásokat egy másik régióba. Például egy új Azure-régió kihasználása érdekében a belső házirend-és irányítási követelmények teljesítéséhez, vagy a kapacitás megtervezésének követelményeire reagálva. 

A jelen cikkben szereplő magas szintű lépések: 

- **Exportálja a tartományi** erőforrást egy Azure Resource Manager sablonba. 

    > [!IMPORTANT]
    > A tartományban lévő tartományi erőforrás és témakörök a sablonba lesznek exportálva. A tartományba tartozó témakörök előfizetései nem lesznek exportálva. 
- A **sablont használva telepítse a tartományt** a célként megadott régióba. 
- **Hozzon létre előfizetéseket a tartományi témakörökhöz** a célként megadott régióban. Ha a tartományt az aktuális régióban lévő sablonba exportálta, a tartományi témakörök előfizetései nem lesznek exportálva. Tehát hozza létre őket, miután a tartomány és a tartomány témakörök létre lettek hozva a célként megadott régióban. 
- **Ellenőrizze az** üzemelő példányt. Küldjön egy eseményt egy tartományi témakörbe a tartományban, és ellenőrizze, hogy az előfizetéshez társított eseménykezelőt meghívja-e a rendszer. 
- **Az áthelyezés befejezéséhez** törölje a tartományt a forrás régióból. 

## <a name="prerequisites"></a>Előfeltételek
- Győződjön meg arról, hogy a Event Grid szolgáltatás elérhető a célként megadott régióban. Tekintse meg [a régiókban elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Előkészítés
Első lépésként exportáljon egy Resource Manager-sablont a tartományhoz. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A keresősáv mezőbe írja be **Event Grid tartományokat**, és válassza ki **Event Grid tartományokat** az eredmények listából. 

    :::image type="content" source="./media/move-domains-across-regions/search-domains.png" alt-text="Event Grid tartományok keresése és kiválasztása":::
3. Válassza ki azt a **tartományt** , amelyet Resource Manager-sablonba szeretne exportálni. 

    :::image type="content" source="./media/move-domains-across-regions/select-domain.png" alt-text="Válassza ki a tartományt":::   
4. A **Event Grid tartomány** lapon a bal oldali menüben válassza a **sablon exportálása** **lehetőséget,** majd válassza a **Letöltés** lehetőséget az eszköztáron. 

    :::image type="content" source="./media/move-domains-across-regions/export-template-download.png" alt-text="Sablon exportálása – > Letöltés" lightbox="./media/move-domains-across-regions/export-template-download.png":::   

    > [!IMPORTANT]
    > A tartomány-és tartományi témakörök exportálása megtörtént. A tartományi témakörök előfizetései nem lesznek exportálva. Ezért létre kell hoznia az előfizetéseket a tartományi témakörökhöz, miután áthelyezte a tartományi témákat. 
5. Keresse meg a portálról letöltött **. zip** fájlt, és bontsa ki a fájlt egy tetszőleges mappába. Ez a zip-fájl sablon-és Parameters JSON-fájlokat tartalmaz. 
1. Nyissa **meg atemplate.jst** egy tetszőleges szerkesztőben. 
8. Frissítse `location` a **tartományi** erőforrást a célként megadott régióba vagy helyre. A helykód beszerzéséhez tekintse meg az [Azure-helyek](https://azure.microsoft.com/global-infrastructure/locations/)című témakört. A régió kódja a régió neve, szóközök nélkül, például `West US` egyenlő `westus` .

    ```json
    "type": "Microsoft.EventGrid/domains",
    "apiVersion": "2020-06-01",
    "name": "[parameters('domains_spegriddomain_name')]",
    "location": "westus",
    ```
1. **Mentse** a sablont. 

## <a name="recreate"></a>Hozza létre újra 
A sablon üzembe helyezésével hozza létre a tartomány és a tartomány témakört a célként megadott régióban. 

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.
2. A **Keresés a piactéren** mezőbe írja be a **sablon központi telepítése** kifejezést, majd nyomja le az **ENTER** billentyűt.
3. Válassza a **template Deployment** lehetőséget.
4. Válassza a **Létrehozás** lehetőséget.
5. Válassza **a saját sablon létrehozása lehetőséget a szerkesztőben**.
6. Válassza a **fájl betöltése** lehetőséget, majd kövesse az utasításokat az utolsó szakaszban letöltött fájl **template.js** betöltéséhez.
7. A sablon mentéséhez válassza a **Mentés** lehetőséget. 
8. Az **Egyéni telepítés** lapon kövesse az alábbi lépéseket:
    1. Válasszon ki egy Azure- **előfizetést**. 
    1. Válasszon ki egy meglévő **erőforráscsoportot** a célként megadott régióban, vagy hozzon létre egyet. 
    1. A **régió** mezőben válassza ki a kívánt régiót. Ha kiválasztott egy meglévő erőforráscsoportot, ez a beállítás csak olvasható. 
    1. A **tartománynév** mezőben adjon meg egy új nevet a tartomány számára. 
    1. Válassza az **Áttekintés + létrehozás** lehetőséget. 
    
        :::image type="content" source="./media/move-domains-across-regions/deploy-template.png" alt-text="Sablon üzembe helyezése":::        
    1. Miután a sablon érvényesítése sikeres volt, a lap alján található **Létrehozás** gombra kattintva telepítheti az erőforrást. 
    1. Az üzembe helyezés sikerességét követően válassza az **Ugrás az erőforráscsoporthoz** lehetőséget az erőforráscsoport oldalának megnyitásához. Győződjön meg arról, hogy az erőforráscsoport tartománya van. Válassza ki a tartományt. Ellenőrizze, hogy vannak-e tartományi témakörök a tartományban. 

## <a name="discard-or-clean-up"></a>Elvetés vagy felesleges elemek eltávolítása
Az áthelyezés befejezéséhez törölje a tartományt a forrás régióban.  

Ha újra szeretné kezdeni, törölje a tartományt a céltartományban, és ismételje meg a cikk [előkészítés](#prepare) és [újbóli létrehozás](#recreate) szakaszában található lépéseket.

Tartomány törlése a Azure Portal használatával:

1. A Azure Portal tetején található Keresés ablakban írja be **Event Grid tartományokat**, és válassza ki **Event Grid tartományokat** a keresési eredmények közül. 
2. Válassza ki a törölni kívánt tartományt, és válassza a **Törlés** lehetőséget az eszköztárból. 
3. A jóváhagyás lapon adja meg az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.  

A tartományt tartalmazó erőforráscsoport törlése a Azure Portal használatával:

1. A Azure Portal tetején található Keresés ablakban írja be az **erőforráscsoportok** kifejezést, majd válassza az **erőforráscsoportok** lehetőséget a keresési eredmények közül. 
2. Válassza ki a törölni kívánt erőforráscsoportot, és válassza a **Törlés** lehetőséget az eszköztárból. 
3. A jóváhagyás lapon adja meg az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.  

## <a name="next-steps"></a>Következő lépések
Megtanulta, hogyan helyezhet át egy Event Grid tartományt egyik régióból egy másik régióba. Tekintse át a következő cikkeket a rendszertémakörök, az egyéni témakörök és a partneri névterek régiók közötti áthelyezéséhez.

- [Rendszertémakörök áthelyezése a régiók között](move-system-topics-across-regions.md). 
- [Egyéni témakörök áthelyezése a régiók között](move-custom-topics-across-regions.md). 
- A [partnerek névtereit áthelyezheti a régiók között](move-partner-namespaces-across-regions.md).

Ha többet szeretne megtudni a régiók és a vész-helyreállítási erőforrások közötti áthelyezésről az Azure-ban, tekintse meg a következő cikket: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).