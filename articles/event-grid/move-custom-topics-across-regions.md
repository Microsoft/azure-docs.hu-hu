---
title: Egyéni témakörök áthelyezése másik régióba Azure Event Grid
description: Ez a cikk bemutatja, hogyan helyezheti át Azure Event Grid egyéni témaköröket az egyik régióból egy másik régióba.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: d0656a4f6ec1c7431cf7111f786b0f1d779166e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89145339"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>Egyéni témakörök áthelyezése másik régióba Azure Event Grid
Előfordulhat, hogy több okból is át szeretné helyezni az erőforrásokat egy másik régióba. Például egy új Azure-régió kihasználása érdekében a belső házirend-és irányítási követelmények teljesítéséhez, vagy a kapacitás megtervezésének követelményeire reagálva. 

A jelen cikkben szereplő magas szintű lépések: 

- **Exportálja az egyéni témakör** -erőforrást egy Azure Resource Manager sablonba. 

    > [!IMPORTANT]
    > Csak az egyéni témakör lett exportálva a sablonba. A témakörhöz tartozó előfizetések nem lesznek exportálva.
- A **sablon használatával telepítse az egyéni témakört** a célként megadott régióba. 
- Az **előfizetéseket manuálisan hozza létre** a céltartományban. Ha az egyéni témakört az aktuális régióban lévő sablonba exportálta, a rendszer csak a témakört exportálja. Az előfizetések nem szerepelnek a sablonban, ezért manuálisan hozza létre őket, miután létrehozta az egyéni témakört a célként megadott régióban. 
- **Ellenőrizze az** üzemelő példányt. Győződjön meg arról, hogy az egyéni témakör a célként megadott régióban lett létrehozva. 
- **Az áthelyezés befejezéséhez** törölje az egyéni témakört a forrás régióból. 

## <a name="prerequisites"></a>Előfeltételek
- Fejezze be a gyors üzembe helyezési útmutatót [: egyéni események átirányítása webes végpontra](custom-event-quickstart-portal.md) a forrás régióban. Végezze el ezt a lépést, hogy tesztelje a cikkben szereplő lépéseket. 
- Győződjön meg arról, hogy a Event Grid szolgáltatás elérhető a célként megadott régióban. Tekintse meg [a régiókban elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Előkészítés
Első lépésként exportáljon egy Resource Manager-sablont az egyéni témakörbe. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A keresősáv mezőbe írja be **Event Grid témaköröket**, és válassza ki **Event Grid témaköröket** az eredmények listából. 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="Event Grid témakörök keresése és kiválasztása":::
3. Válassza ki azt a **témakört** , amelyet egy Resource Manager-sablonba szeretne exportálni. 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="Egyéni témakör kiválasztása":::   
4. A **Event Grid témakör** lapon válassza a bal oldali **menüben a** **sablon exportálása** lehetőséget, majd válassza a **Letöltés** lehetőséget az eszköztáron. 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="Sablon exportálása – > Letöltés":::   

    > [!IMPORTANT]
    > A rendszer csak a témakört exportálja a sablonba. A témakör előfizetései nem lesznek exportálva. Ezért elő kell készítenie a témakör előfizetéseit, miután áthelyezte a témakört a célhelyre. 
5. Keresse meg a portálról letöltött **. zip** fájlt, és bontsa ki a fájlt egy tetszőleges mappába. Ez a zip-fájl sablon-és Parameters JSON-fájlokat tartalmaz. 
1. Nyissa **meg atemplate.jst** egy tetszőleges szerkesztőben. 
8. `location`A **témakör** erőforrásának frissítése a célként megadott régióba vagy helyre. A helykód beszerzéséhez tekintse meg az [Azure-helyek](https://azure.microsoft.com/global-infrastructure/locations/)című témakört. A régió kódja a régió neve, szóközök nélkül, például `West US` egyenlő `westus` .

    ```json
    "type": "Microsoft.EventGrid/topics",
    "apiVersion": "2020-06-01",
    "name": "[parameters('topics_mytopic0130_name')]",
    "location": "westus"
    ```
1. **Mentse** a sablont. 

## <a name="recreate"></a>Hozza létre újra 
A sablon üzembe helyezésével egyéni témakört hozhat létre a célhelyen. 

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
    1. A **témakör neve** mezőben adjon meg egy új nevet a témakörhöz. 
    1. Kattintson a lap alján található **felülvizsgálat + létrehozás** lehetőségre. 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="Egyéni telepítés":::
    1. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, majd válassza a **Létrehozás** lehetőséget. 

## <a name="verify"></a>Ellenőrzés

1. Miután az üzembe helyezés sikeres volt, válassza az **Ugrás erőforráshoz** lehetőséget. 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="Erőforrás megnyitása":::
1. Ellenőrizze, hogy megjelenik-e az egyéni témakör **Event Grid témakör** lapja.   
1. Az [Egyéni események átirányítása webes végpontra](custom-event-quickstart-portal.md#send-an-event-to-your-topic) című szakasz lépéseit követve küldhet eseményeket a témakörbe. Ellenőrizze, hogy a webhook-eseménykezelő meghívása megtörtént-e. 

## <a name="discard-or-clean-up"></a>Elvetés vagy felesleges elemek eltávolítása
Az áthelyezés befejezéséhez törölje az egyéni témakört a forrás régióban.  

Ha el szeretné kezdeni a műveletet, törölje a témakört a célcsoportban, és ismételje meg a cikk [előkészítés](#prepare) és [újbóli létrehozás](#recreate) szakaszának lépéseit.

Egyéni témakör törlése a Azure Portal használatával:

1. A Azure Portal tetején található Keresés ablakban írja be **Event Grid témaköröket**, és válassza ki **Event Grid témaköröket** a keresési eredmények közül. 
2. Válassza ki a törölni kívánt témakört, és válassza a **Törlés** lehetőséget az eszköztárból. 
3. A jóváhagyás lapon adja meg az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.  

Az egyéni témakört tartalmazó erőforráscsoport törlése a Azure Portal használatával:

1. A Azure Portal tetején található Keresés ablakban írja be az **erőforráscsoportok** kifejezést, majd válassza az **erőforráscsoportok** lehetőséget a keresési eredmények közül. 
2. Válassza ki a törölni kívánt erőforráscsoportot, és válassza a **Törlés** lehetőséget az eszköztárból. 
3. A jóváhagyás lapon adja meg az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.  

## <a name="next-steps"></a>Következő lépések
Megtanulta, hogyan helyezhet át egy Event Grid egyéni témakört az egyik régióból egy másik régióba. Tekintse át a következő cikkeket a rendszertémakörök, tartományok és partneri névterek régiók közötti áthelyezéséhez.

- [Rendszertémakörök áthelyezése a régiók között](move-system-topics-across-regions.md). 
- [Tartományok áthelyezése a régiók között](move-domains-across-regions.md). 
- A [partnerek névtereit áthelyezheti a régiók között](move-partner-namespaces-across-regions.md).

Ha többet szeretne megtudni a régiók és a vész-helyreállítási erőforrások közötti áthelyezésről az Azure-ban, tekintse meg a következő cikket: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).