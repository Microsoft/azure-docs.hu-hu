---
title: Azure Event Grid rendszertémakörök áthelyezése másik régióba
description: Ez a cikk bemutatja, hogyan helyezhet át Azure Event Grid rendszertémaköröket az egyik régióból egy másikba.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: eb6029b206e7d47789371ee81e75c4e05c69ee65
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89084531"
---
# <a name="move-azure-event-grid-system-topics-to-another-region"></a>Azure Event Grid rendszertémakörök áthelyezése másik régióba
Előfordulhat, hogy több okból is át szeretné helyezni az erőforrásokat egy másik régióba. Például egy új Azure-régió kihasználása érdekében a belső házirend-és irányítási követelmények teljesítéséhez, vagy a kapacitás megtervezésének követelményeire reagálva. 

A jelen cikkben szereplő magas szintű lépések: 

- **Exportálja az** Azure Storage-fiókot és a hozzá tartozó System témakört tartalmazó erőforráscsoportot egy Resource Manager-sablonba. A sablont csak a rendszertémakörhöz lehet exportálni. Ha ezt az útvonalat választja, ne felejtse el áthelyezni az Azure-esemény forrását (ebben a példában egy Azure Storage-fiókot) a másik régióba, mielőtt áthelyezi a rendszertémakört. Ezt követően a rendszer témakör exportált sablonjában frissítse a Storage-fiók külső AZONOSÍTÓját a célként megadott régióban. 
- **Módosítsa a sablont** úgy, hogy hozzáadja a `endpointUrl` tulajdonságot úgy, hogy az a rendszertémakörre előfizetett webhookra mutasson. A rendszertémakör exportálásakor a rendszer az előfizetését (ebben az esetben a webhookot) is exportálja a sablonba, de a `endpointUrl` tulajdonság nem szerepel benne. Ezért frissítenie kell, hogy a témakörre előfizetett végpontra mutasson. Frissítse a `location` tulajdonság értékét az új helyre vagy régióra is. Más típusú eseménykezelők esetén csak a hely frissítése szükséges. 
- **A sablon használatával erőforrásokat telepíthet** a célként megadott régióba. Megadhatja a Storage-fiók nevét és a cél régióban létrehozandó rendszertémakört. 
- **Ellenőrizze az** üzemelő példányt. Győződjön meg arról, hogy a webhook meghívásra kerül, amikor feltölt egy fájlt a blob Storage-ba a célként megadott régióban. 
- **Az áthelyezés befejezéséhez** törölje az erőforrásokat (eseményforrás és rendszer témakör) a forrás régióból. 

## <a name="prerequisites"></a>Előfeltételek
- Fejezze be a rövid útmutató [: blob Storage-események átirányítása webes végpontra](blob-event-quickstart-portal.md) a forrás régióban lévő Azure Portal. Ez a lépés **nem kötelező**. Végezze el a jelen cikkben szereplő lépések tesztelését. A Storage-fiókot külön erőforráscsoport tárolja a App Service és a App Service csomagból. 
- Győződjön meg arról, hogy a Event Grid szolgáltatás elérhető a célként megadott régióban. Tekintse meg [a régiókban elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Előkészítés
Első lépésként exportáljon egy Resource Manager-sablont az erőforráscsoporthoz, amely tartalmazza a rendszer-eseményforrás (Azure Storage-fiók) és a hozzá tartozó rendszer témakört. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza az **erőforráscsoportok** lehetőséget a bal oldali menüben. Ezután válassza ki azt az erőforráscsoportot, amely azt az eseményforrás tartalmazza, amelyhez a rendszertémakört létrehozták. A következő példában ez az **Azure Storage** -fiók. Az erőforráscsoport tartalmazza a Storage-fiókot és a hozzá tartozó System témakört. 

    :::image type="content" source="./media/move-system-topics-across-regions/resource-group-page.png" alt-text="Erőforráscsoport lapja":::        
3. A bal oldali menüben válassza a **sablon exportálása** a **Beállítások** alatt lehetőséget, majd válassza a **Letöltés** lehetőséget az eszköztáron. 

    :::image type="content" source="./media/move-system-topics-across-regions/export-template-menu.png" alt-text="További fiók – sablon exportálása lap":::        
5. Keresse meg a portálról letöltött **. zip** fájlt, és bontsa ki a fájlt egy tetszőleges mappába. Ez a zip-fájl sablon-és Parameters JSON-fájlokat tartalmaz. 
1. Nyissa **meg atemplate.jst** egy tetszőleges szerkesztőben. 
1. A webhook URL-címét a rendszer nem exportálja a sablonba. Végezze el a következő lépéseket:
    1. A sablon fájlban keresse meg a **webhook** kifejezést. 
    1. A **Properties (Tulajdonságok** ) szakaszban adjon hozzá egy vessző ( `,` ) karaktert az utolsó sor végén. Ebben a példában ez a következő: `"preferredBatchSizeInKilobytes": 64` . 
    1. Adja hozzá a `endpointUrl` tulajdonságot a webhook URL-címéhez beállított értékkel az alábbi példában látható módon. 

        ```json
        "destination": {
            "properties": {
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64,
                "endpointUrl": "https://mysite.azurewebsites.net/api/updates"
            },
            "endpointType": "WebHook"
        }
        ```

        > [!NOTE]
        > Más típusú eseménykezelők esetében az összes tulajdonságot a rendszer a sablonba exportálja. Csak a `location` következő lépésben látható módon kell frissítenie a tulajdonságot a célként megadott régióba. 
7. Frissítse a `location` **Storage-fiók** erőforrását a célként megadott régióba vagy helyre. A helykód beszerzéséhez tekintse meg az [Azure-helyek](https://azure.microsoft.com/global-infrastructure/locations/)című témakört. A régió kódja a régió neve, szóközök nélkül, például `West US` egyenlő `westus` .

    ```json
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccounts_spegridstorage080420_name')]",
    "location": "westus",
    ```
8. Ismételje meg a lépést a `location` sablonban található **rendszertémakör** -erőforrás frissítéséhez. 

    ```json
    "type": "Microsoft.EventGrid/systemTopics",
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('systemTopics_spegridsystopic080420_name')]",
    "location": "westus",
    ```
1. **Mentse** a sablont. 

## <a name="recreate"></a>Hozza létre újra 
A sablon üzembe helyezésével hozzon létre egy Storage-fiókot és egy rendszertémakört a cél régióban lévő Storage-fiókhoz. 

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.
2. A **Keresés a piactéren** mezőbe írja be a **sablon központi telepítése** kifejezést, majd nyomja le az **ENTER** billentyűt.
3. Válassza a **template Deployment** lehetőséget.
4. Válassza a **Létrehozás** lehetőséget.
5. Válassza **a saját sablon létrehozása lehetőséget a szerkesztőben**.
6. Válassza a **fájl betöltése** lehetőséget, majd kövesse az utasításokat az utolsó szakaszban letöltött fájl **template.js** betöltéséhez.
7. A sablon mentéséhez válassza a **Mentés** lehetőséget. 
8. Az **Egyéni telepítés** lapon kövesse az alábbi lépéseket. 
    1. Válasszon ki egy Azure- **előfizetést**. 
    1. Válasszon ki egy meglévő **erőforráscsoportot** a célként megadott régióban, vagy hozzon létre egyet. 
    1. A **régió** mezőben válassza ki a kívánt régiót. Ha kiválasztott egy meglévő erőforráscsoportot, ez a beállítás csak olvasható.
    1. A **rendszertéma neve** mezőbe írja be annak a rendszertémakörnek a nevét, amely a Storage-fiókhoz lesz társítva.  
    1. A **Storage-fiók neve** mezőben adja meg a cél régióban létrehozandó Storage-fiók nevét. 

        :::image type="content" source="./media/move-system-topics-across-regions/deploy-template.png" alt-text="Resource Manager-sablon üzembe helyezése":::
    5. Kattintson a lap alján található **felülvizsgálat + létrehozás** lehetőségre. 
    1. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, majd válassza a **Létrehozás** lehetőséget. 

## <a name="verify"></a>Ellenőrzés
1. Az üzembe helyezés sikerességét követően válassza a **goto erőforráscsoport** elemet. 
1. Az **erőforráscsoport** lapon ellenőrizze, hogy az eseményforrás (ebben a példában az Azure Storage-fiók) és a rendszertémakör létrejött-e. 
1. Töltsön fel egy fájlt egy tárolóba az Azure Blob Storage-ban, és ellenőrizze, hogy a webhook fogadta-e az eseményt. További információ: [esemény küldése a végpontnak](blob-event-quickstart-portal.md#send-an-event-to-your-endpoint).

## <a name="discard-or-clean-up"></a>Elvetés vagy felesleges elemek eltávolítása
Az áthelyezés befejezéséhez törölje a Storage-fiókot és a hozzá tartozó System témakört tartalmazó erőforráscsoportot a forrás régióban.  

Ha újra szeretné kezdeni, törölje az erőforráscsoportot a célként megadott régióban, és ismételje meg a cikk [előkészítés](#prepare) és [újbóli létrehozás](#recreate) szakaszában szereplő lépéseket.

Erőforráscsoport (forrás vagy cél) törlése a Azure Portal használatával:

1. A Azure Portal tetején található Keresés ablakban írja be az **erőforráscsoportok** kifejezést, majd válassza az **erőforráscsoportok** lehetőséget a keresési eredmények közül. 
2. Válassza ki a törölni kívánt erőforráscsoportot, és válassza a **Törlés** lehetőséget az eszköztárból. 

    :::image type="content" source="./media/move-system-topics-across-regions/delete-resource-group-button.png" alt-text="Erőforráscsoport törlése":::
3. A jóváhagyás lapon adja meg az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.  

## <a name="next-steps"></a>Következő lépések
Megtanulta, hogyan helyezhet át egy Azure-beli eseményforrás és a hozzá tartozó rendszer témakört az egyik régióból egy másik régióba. Tekintse meg az alábbi cikkeket az egyéni témakörök, tartományok és partneri névterek régiók közötti áthelyezéséhez.

- [Egyéni témakörök áthelyezése a régiók között](move-custom-topics-across-regions.md). 
- [Tartományok áthelyezése a régiók között](move-domains-across-regions.md). 
- A [partnerek névtereit áthelyezheti a régiók között](move-partner-namespaces-across-regions.md). 

Ha többet szeretne megtudni a régiók és a vész-helyreállítási erőforrások közötti áthelyezésről az Azure-ban, tekintse meg a következő cikket: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).
