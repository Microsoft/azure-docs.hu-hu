---
title: Service Bus névtér áthelyezése egy másik régióba | Microsoft Docs
description: Ez a cikk bemutatja, hogyan helyezhet át egy Azure Service Bus névteret az aktuális régióból egy másik régióba.
ms.topic: how-to
ms.date: 06/23/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 573ea96437cf6cb76854ffa417fd3ad3fb86138b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88861067"
---
# <a name="move-an-azure-service-bus-namespace-to-another-region"></a>Azure Service Bus névtér áthelyezése egy másik régióba
Különböző helyzetekben érdemes áthelyezni a meglévő Service Bus névteret az egyik régióból a másikba. Előfordulhat például, hogy létre szeretne hozni egy névteret ugyanazzal a konfigurációval a teszteléshez. A vész- [helyreállítási tervezés](service-bus-geo-dr.md)részeként másodlagos névteret is létre kell hoznia egy másik régióban.

A magas szintű lépések:

1. Exportálja az aktuális régió Service Bus névterét egy Azure Resource Manager sablonba. 
1. Frissítési hely a sablon erőforrásaihoz. Emellett távolítsa el az alapértelmezett előfizetési szűrőt a sablonból, mert nem hozhat létre alapértelmezett szabályt, mert az automatikusan létrejön. 
1. A sablon segítségével telepítse a Service Bus névteret a célként megadott régióba. 
1. Győződjön meg arról, hogy a központi telepítés biztosítja, hogy a témakörökben a névtér, a várólisták, a témakörök és az előfizetések mind a cél régióban jöjjenek létre. 
1. A művelet befejezéséhez törölje a névteret a forrás régiójából az összes üzenet feldolgozását követően. 

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a fiók által használt Azure Service Bus és szolgáltatások támogatottak a célként megadott régióban.
 
## <a name="prepare"></a>Előkészítés
Első lépésként exportáljon egy Resource Manager-sablont. Ez a sablon a Service Bus névteret leíró beállításokat tartalmaz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden erőforrás** lehetőséget, majd válassza ki a Service Bus névteret.
3. Válassza > **Beállítások**  >  **Exportálás sablon** lehetőséget.
4. A **sablon exportálása** lapon kattintson a **Letöltés** elemre.

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Resource Manager-sablon letöltése":::
5. Keresse meg a portálról letöltött. zip fájlt, és bontsa ki a fájlt egy tetszőleges mappába. Ez a zip-fájl tartalmazza a sablon és a paraméterek JSON-fájlját. 
1. Nyissa meg a template.jsfájlt a kibontott mappában. 
1. Keresse meg a kifejezést `location` , és cserélje le a tulajdonság értékét a régió vagy hely új nevére. A helykód beszerzéséhez tekintse meg az [Azure-helyek](https://azure.microsoft.com/global-infrastructure/locations/)című témakört. A régió kódja a régió neve, szóközök nélkül, például `West US` egyenlő `westus` .
1. A következő típusú erőforrások definícióinak eltávolítása: `Microsoft.ServiceBus/namespaces/topics/subscriptions/rules` . Ne felejtse el eltávolítani a `,` szakasz előtti vessző () karaktert, hogy a JSON érvényes legyen.  

    > [!NOTE]
    > Egy Resource Manager-sablon használatával nem hozhat létre alapértelmezett szabályt az előfizetésekhez. Az alapértelmezett szabály automatikusan létrejön, amikor létrejön az előfizetés a célként megadott régióban. 

## <a name="move"></a>Áthelyezés
A sablon üzembe helyezésével hozzon létre egy Service Bus névteret a célként megadott régióban. 

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.
2. A **Keresés a piactéren** mezőbe írja be a keresett szöveghez tartozó **sablon központi telepítését** , válassza a **template Deployment (az egyéni sablonok használatával történő üzembe helyezés)** lehetőséget, majd nyomja le az **ENTER** billentyűt.

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="Új sablon központi telepítése":::    
1. A **template Deployment** lapon válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="Új sablon központi telepítése – létrehozás gomb":::        
1. Az **Egyéni telepítés** lapon válassza a **saját sablon létrehozása lehetőséget a szerkesztőben**.

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="Saját sablon létrehozása a szerkesztőben – hivatkozás":::            
1. A **Sablon szerkesztése** lapon válassza a **fájl betöltése** az eszköztáron lehetőséget, majd kövesse az utasításokat az utolsó szakaszban letöltött fájl **template.js** betöltéséhez.

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="Sablon kiválasztása":::                
1. A sablon mentéséhez válassza a **Mentés** lehetőséget. 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="Sablon mentése":::                    
1. Az **Egyéni telepítés** lapon kövesse az alábbi lépéseket: 
    1. Válasszon ki egy Azure- **előfizetést**. 
    2. Válasszon ki egy meglévő **erőforráscsoportot** , vagy hozzon létre egyet. 
    3. Válassza ki a **célhelyet vagy** régiót. Ha kiválasztott egy meglévő erőforráscsoportot, ez a beállítás csak olvasható. 
    4. Adja meg **a névtér új nevét**.
    1. Válassza az **Áttekintés + létrehozás** lehetőséget. 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="Resource Manager-sablon üzembe helyezése":::
    1. A **felülvizsgálat + létrehozás** oldalon válassza a **Létrehozás** elemet az oldal alján. 
    
## <a name="verify"></a>Ellenőrzés
1. Miután az üzembe helyezés sikeres volt, válassza az **Ugrás az erőforrás-csoportba** lehetőséget.

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="Ugrás az erőforráscsoport hivatkozására":::    
1. Az **erőforráscsoport** lapon válassza ki a Service Bus névteret. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Service Bus névtér kiválasztása":::    
1. A **Service Bus névtér** lapon ellenőrizze, hogy látható-e a forrás régiójában lévő várólisták, témakörök és előfizetések. 
    1. A **várólistákat** a jobb oldali ablaktábla alján található névtérben láthatja.         
    
        :::image type="content" source="./media/move-across-regions/queue-namespace.png" alt-text="A névtérben lévő várólisták":::
    2. Váltson a **témakörök** lapra a névtérben található témakörök megjelenítéséhez
    
        :::image type="content" source="./media/move-across-regions/topics-namespace.png" alt-text="A névtér témakörei":::
    3. Válassza ki a témakört az előfizetések ellenőrzéséhez. 

        :::image type="content" source="./media/move-across-regions/topic-subscriptions.png" alt-text="Témakör-előfizetések":::      
    
    

## <a name="discard-or-clean-up"></a>Elvetés vagy felesleges elemek eltávolítása
Ha az üzembe helyezést követően el szeretné indítani a műveletet, törölheti a **cél Service Bus névteret**, és megismételheti a cikk [előkészítés](#prepare) és [Áthelyezés](#move) szakaszában ismertetett lépéseket.

A módosítások elvégzéséhez és egy Service Bus névtér áthelyezésének befejezéséhez törölje a **forrás Service Bus névteret**. Győződjön meg arról, hogy az összes üzenetet feldolgozza a névtér törlése előtt. 

Service Bus névtér (forrás vagy cél) törlése a Azure Portal használatával:

1. A Azure Portal tetején található Keresés ablakban írja be a **Service Bus** kifejezést, és válassza a **Service Bus** lehetőséget a keresési eredmények közül. A listában megjelenik a Service Bus névterek listája.
2. Válassza ki a törölni kívánt cél névteret, és válassza a **Törlés** lehetőséget az eszköztárból. 

    ![Névtér törlése – gomb](./media/move-across-regions/delete-namespace-button.png)
3. Az **erőforrások törlése** lapon ellenőrizze a kiválasztott erőforrásokat, és erősítse meg a törlést az **Igen** érték beírásával, majd válassza a **Törlés** lehetőséget. 

    A másik lehetőség az Service Bus névteret tartalmazó erőforráscsoport törlése. Az **erőforráscsoport** lapon válassza az **erőforráscsoport törlése** elemet az eszköztáron, majd erősítse meg a törlést. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure Service Bus névteret helyezett át egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:

- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
