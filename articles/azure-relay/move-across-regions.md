---
title: Azure Relay névtér áthelyezése egy másik régióba
description: Ez a cikk bemutatja, hogyan helyezhet át egy Azure Relay névteret az aktuális régióból egy másik régióba.
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89463606"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>Azure Relay névtér áthelyezése egy másik régióba
Ez a cikk bemutatja, hogyan helyezhet át egy Azure Relay névteret egyik régióból egy másik régióba. A magas szintű lépések:

1. **Exportálja** a Relay-névteret egy Azure Resource Manager sablonba.
1. **Frissítési hely (régió)** a sablon erőforrásaihoz. Emellett törölje a **dinamikus** WCF-továbbítókat a sablonból. 

    A WCF-továbbítóknak két módja van. Az első módban a WCF-továbbító explicit módon jön létre a Azure Portal vagy Azure Resource Manager sablonnal. A Azure Portal **WCF-továbbítók** lapján a **isDynamic** tulajdonság értéke **false (hamis** ) értékre van állítva egy továbbító esetében ebben a módban. 

    Második módban a WCF-továbbító automatikusan létrejön, ha egy figyelő (kiszolgáló) csatlakozik egy adott végponti címnek. Ha a figyelő csatlakoztatva van a továbbítóhoz, a továbbító megjelenik a Azure Portal WCF-továbbítások listájában. Ebben a módban lévő továbbító esetén a **isDynamic** tulajdonság **true (igaz** ) értékre van állítva, mert dinamikusan jön létre. A dinamikus WCF-továbbító eltűnik, ha a figyelő bontja a kapcsolatot. 
1. Erőforrások **üzembe helyezése** a sablon használatával a célként megadott régióban.

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a Azure Relay szolgáltatás elérhető a céltartományban. Tekintse meg [a régiókban elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all). 
 
## <a name="prepare"></a>Előkészítés
Első lépésként exportáljon egy Resource Manager-sablont. Ez a sablon a Azure Relay névteret leíró beállításokat tartalmaz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden erőforrás** lehetőséget, majd válassza ki a Azure Relay névteret.
3. Válassza a bal oldali menüben a **sablon exportálása** **menüpontot** .
4. Válassza a **Letöltés** elemet a **sablon exportálása** oldalon.

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Resource Manager-sablon letöltése":::
5. Keresse meg a portálról letöltött. zip fájlt, és bontsa ki a fájlt egy tetszőleges mappába. Ez a zip-fájl tartalmazza a sablon és a paraméterek JSON-fájlját. 
1. Nyissa meg a **template.js** fájlt a kibontott mappából egy tetszőleges szerkesztőben.
1. Keresse meg a kifejezést `location` , és cserélje le a tulajdonság értékét a régió új nevére. A helykód beszerzéséhez tekintse meg az [Azure-helyek](https://azure.microsoft.com/global-infrastructure/locations/)című témakört. A régió kódja a régió neve, szóközök nélkül, például `West US` egyenlő `westus` .
1. A **dinamikus WCF-továbbító** erőforrások definícióinak eltávolítása (típus: `Microsoft.Relay/namespaces/WcfRelays` ). A dinamikus WCF-továbbítók olyanok, amelyek **isDynamic** tulajdonsága **true** értékre van állítva a továbbítások **lapon.** A következő példában a **echoservice** egy dinamikus WCF-továbbító, és annak definícióját el kell távolítani a sablonból. 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="Dinamikus továbbítók":::

## <a name="move"></a>Áthelyezés
A sablon üzembe helyezésével hozzon létre egy Relay-névteret a célként megadott régióban. 

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
1. Az **erőforráscsoport** lapon válassza ki a Azure Relay névteret. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Azure Relay névtér kiválasztása":::    
1. A **Azure Relay névtér** lapon válassza a bal oldali menüben a **hibrid kapcsolatok** vagy a **WCF** -továbbító lehetőséget annak ellenőrzéséhez, hogy a hibrid kapcsolatok és a WCF-továbbítók létrejöttek-e. Ha a sablon importálása előtt elfelejtette a dinamikus WCF-továbbítók definícióinak törlését, törölje őket a **WCF** -Továbbítók lapon. A dinamikus WCF-továbbítókat a rendszer automatikusan hozza létre, amikor az ügyfelek csatlakoznak a továbbítási névtérhez. 

## <a name="discard-or-clean-up"></a>Elvetés vagy felesleges elemek eltávolítása
Ha az üzembe helyezést követően el szeretné indítani a műveletet, törölheti a **cél Azure Relay névteret**, és megismételheti a cikk [előkészítés](#prepare) és [Áthelyezés](#move) szakaszában ismertetett lépéseket.

A módosítások elvégzéséhez és a névtér áthelyezésének befejezéséhez törölje a **Azure Relay névteret** a forrás régióban. 

Azure Relay névtér (forrás vagy cél) törlése a Azure Portal használatával:

1. A Azure Portal tetején található Keresés ablakban **írja be a** **Relays** kifejezést, majd a keresési eredmények között válassza a **szolgáltatások** lehetőséget. Egy listában megjelenik az összes Azure Relay névtér.
2. Válassza ki a törölni kívánt cél névteret a **továbbítási** oldal megnyitásához. 
1. A **Relay** lapon válassza a **Törlés** lehetőséget az eszköztárból. 

    ![Névtér törlése – gomb](./media/move-across-regions/delete-namespace-button.png)
3. A **névtér törlése** lapon írja be a Azure Relay névtér nevét a törlés megerősítéséhez, majd válassza a **Törlés** lehetőséget. 

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy Azure Relay névteret helyezett át egyik régióból egy másik régióba. Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:

- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
