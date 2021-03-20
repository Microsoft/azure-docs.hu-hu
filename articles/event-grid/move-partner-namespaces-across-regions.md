---
title: Azure Event Grid partneri névterek áthelyezése másik régióba
description: Ez a cikk bemutatja, hogyan helyezhet át Azure Event Grid partneri névtereket az egyik régióból a másikba.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: 6783db6b9bb1c7d48b308234a179925d6f30e281
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89084544"
---
# <a name="move-azure-event-grid-partner-namespaces-to-another-region"></a>Azure Event Grid partneri névterek áthelyezése másik régióba
Előfordulhat, hogy több okból is át szeretné helyezni az erőforrásokat egy másik régióba. Például egy új Azure-régió kihasználása érdekében a belső házirend-és irányítási követelmények teljesítéséhez, vagy a kapacitás megtervezésének követelményeire reagálva. 

A jelen cikkben szereplő magas szintű lépések: 

- **Exportálja a partneri névtér** erőforrását egy Azure Resource Manager sablonba. A sablonban lévő Event Channel-erőforrások definícióinak törlése. Előfordulhat, hogy az esemény-csatorna a partner témakör Azure Resource Manager AZONOSÍTÓJÁRA hivatkozik, amely egy ügyfél tulajdonában van. Ezért nem hozhatók létre a célként megadott régióban található sablon használatával.  
- A **sablon használatával telepítse a partneri névteret** a célként megadott régióba. Ezután hozzon létre egy Event channels-et az új partneri névtérben a célként megadott régióban. 
- **Az áthelyezés befejezéséhez** törölje a partneri névteret a forrás régiójából. 

    > [!NOTE]
    > - A **partnerek témaköreinek** Azure Resource Manager sablonba való exportálása nem támogatott, mivel az ügyfelek nem tudnak közvetlenül létrehozni partneri témakört. 
    > - A **partnerek regisztrációja** globális erőforrás (nem egy adott régióhoz kötődik), ezért az egyik régióból egy másik régióba való áthelyezés nem alkalmazható. 

## <a name="prerequisites"></a>Előfeltételek
- Győződjön meg arról, hogy a Event Grid szolgáltatás elérhető a célként megadott régióban. Tekintse meg [a régiókban elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Előkészítés
Első lépésként exportáljon egy Resource Manager-sablont a partneri névtérhez. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső keresési sávon írja be **Event Grid partner névterek** kifejezést, majd válassza ki **Event Grid partneri névtereket** az eredmények listából. 
3. Válassza ki azt a **partneri névteret** , amelyet Resource Manager-sablonba szeretne exportálni. 
4. Az **Event Grid partneri névtér** lapon válassza a bal oldali menüben **a** **sablon exportálása** lehetőséget, majd válassza a **Letöltés** lehetőséget az eszköztáron. 

    :::image type="content" source="./media/move-partner-namespaces-across-regions/download-template.png" alt-text="Sablon exportálása – > Letöltés" lightbox="./media/move-partner-namespaces-across-regions/download-template.png":::   
5. Keresse meg a portálról letöltött **. zip** fájlt, és bontsa ki a fájlt egy tetszőleges mappába. Ez a zip-fájl sablon-és Parameters JSON-fájlokat tartalmaz. 
1. Nyissa **meg atemplate.jst** egy tetszőleges szerkesztőben. 
8. `location`A **témakör** erőforrásának frissítése a célként megadott régióba vagy helyre. A helykód beszerzéséhez tekintse meg az [Azure-helyek](https://azure.microsoft.com/global-infrastructure/locations/)című témakört. A régió kódja a régió neve, szóközök nélkül, például `West US` egyenlő `westus` .

    ```json
    {
        "type": "Microsoft.EventGrid/partnerNamespaces",
        "apiVersion": "2020-04-01-preview",
        "name": "[parameters('partnerNamespace_name')]",
        "location": "westus",
        "properties": {
            "partnerRegistrationFullyQualifiedId": "[parameters('partnerRegistrations_ContosoCorpAccount1_externalid')]"
        }
    }
    ``` 
1. **Mentse** a sablont. 

## <a name="recreate"></a>Hozza létre újra 
A sablon üzembe helyezésével hozzon létre egy partneri névteret a célként megadott régióban. 

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
    1. A **hely** mezőben válassza ki a kívánt régiót. Ha kiválasztott egy meglévő erőforráscsoportot, ez a beállítás csak olvasható. 
    1. A **partner névterének neve** mezőbe írja be az új partneri névtér nevét. 
    1. A partner regisztrációjának külső AZONOSÍTÓjának megadásához adja meg a partner regisztrációjának erőforrás-AZONOSÍTÓját a következő formátumban: `/subscriptions/<Azure subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.EventGrid/partnerRegistrations/<Partner registration name>` .
    1. Jelölje be az **Elfogadom a fenti feltételeket és kikötéseket** jelölőnégyzetet.     
    1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a telepítési folyamat elindításához. 
    1. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, majd válassza a **Létrehozás** lehetőséget. 

## <a name="discard-or-clean-up"></a>Elvetés vagy felesleges elemek eltávolítása
Az áthelyezés befejezéséhez törölje a partneri névteret a forrás régióban.  

Ha újra szeretné kezdeni, törölje a partneri névteret a célként megadott régióban, és ismételje meg a cikk [előkészítés](#prepare) és [újbóli létrehozás](#recreate) szakaszában szereplő lépéseket.

Partneri névtér törlése a Azure Portal használatával:

1. A Azure Portal tetején található Keresés ablakban írja be **Event Grid partner névterek** kifejezést, majd válassza ki **Event Grid partneri névtereket** a keresési eredmények közül. 
2. Válassza ki a törölni kívánt partneri névteret, és válassza a **Törlés** lehetőséget az eszköztárból. 
3. **Erősítse meg** a törlést a partneri névtér törléséhez. 

## <a name="next-steps"></a>Következő lépések
Megtanulta, hogyan helyezhet át egy Event Grid partneri névteret az egyik régióból egy másik régióba. Tekintse át a következő cikkeket a rendszertémakörök, az egyéni témakörök és a tartományok régiók közötti áthelyezéséhez.

- [Rendszertémakörök áthelyezése a régiók között](move-system-topics-across-regions.md). 
- [Egyéni témakörök áthelyezése a régiók között](move-custom-topics-across-regions.md). 
- [Tartományok áthelyezése a régiók között](move-domains-across-regions.md).

Ha többet szeretne megtudni a régiók és a vész-helyreállítási erőforrások közötti áthelyezésről az Azure-ban, tekintse meg a következő cikket: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).