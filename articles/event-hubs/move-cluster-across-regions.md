---
title: Azure Event Hubs dedikált fürt áthelyezése egy másik régióba | Microsoft Docs
description: Ez a cikk bemutatja, hogyan helyezhet át egy Azure Event Hubs dedikált fürtöt az aktuális régióból egy másik régióba.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 94be44ee8f9442a3a65e899d7a58524b2570f194
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89380832"
---
# <a name="move-an-azure-event-hubs-dedicated-cluster-to-another-region"></a>Azure Event Hubs dedikált fürt áthelyezése egy másik régióba
Ez a cikk bemutatja, hogyan exportálhat egy Azure Resource Manager sablont egy meglévő Event Hubs dedikált fürthöz, majd a sablonnal létrehozhat egy másik régióban azonos konfigurációs beállításokkal rendelkező fürtöt. 

Ha olyan erőforrásokkal rendelkezik, mint például a névterek és az Event hubok az Event Hubs fürtöt tartalmazó Azure-erőforráscsoport esetében, érdemes exportálni a sablont az erőforráscsoport szintjén, hogy az összes kapcsolódó erőforrás áthelyezhető az új régióba egy lépésben. A cikkben ismertetett lépések bemutatják, hogyan exportálhat egy **Event Hubs fürtöt** a sablonba. Az **erőforráscsoportok** sablonba való exportálásának lépései hasonlóak. 

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a dedikált fürt hozható létre a célként megadott régióban. A legkönnyebben kideríthető, hogy a Azure Portal használatával próbáljon meg [egy Event Hubs dedikált fürtöt létrehozni](event-hubs-dedicated-cluster-create-portal.md). Itt láthatja a fürt létrehozásához adott időpontban támogatott régiók listáját. 

## <a name="prepare"></a>Előkészítés
Első lépésként exportáljon egy Resource Manager-sablont. Ez a sablon a Event Hubs dedikált fürtöt leíró beállításokat tartalmaz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden erőforrás** lehetőséget, majd válassza ki a Event Hubs dedikált fürtöt.
3. Válassza > **Beállítások**  >  **Exportálás sablon** lehetőséget.
4. A **sablon exportálása** lapon kattintson a **Letöltés** elemre.

    :::image type="content" source="./media/move-cluster-across-regions/download-template.png" alt-text="Resource Manager-sablon letöltése" lightbox="./media/move-cluster-across-regions/download-template.png":::
5. Keresse meg a portálról letöltött. zip fájlt, és bontsa ki a fájlt egy tetszőleges mappába.

   Ez a zip-fájl tartalmazza azokat a. JSON fájlokat, amelyek tartalmazzák a sablont és a parancsfájlokat a sablon telepítéséhez.


## <a name="move"></a>Áthelyezés

A sablon üzembe helyezésével hozzon létre egy Event Hubs dedikált fürtöt a célként megadott régióban. 


1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.
2. A **Keresés a piactéren** mezőbe írja be a **sablon központi telepítése** kifejezést, és válassza a **template Deployment (üzembe helyezés egyéni sablonok használatával)** lehetőséget.
5. Válassza **a saját sablon létrehozása lehetőséget a szerkesztőben**.
6. Válassza a **fájl betöltése** lehetőséget, majd kövesse az utasításokat az utolsó szakaszban letöltött fájl **template.js** betöltéséhez.
1. Frissítse a `location` tulajdonság értékét úgy, hogy az az új régióra mutasson. A helykód beszerzéséhez tekintse meg az [Azure-helyek](https://azure.microsoft.com/global-infrastructure/locations/)című témakört. A régió kódja a régió neve, szóközök nélkül, például `West US` egyenlő `westus` .
1. A sablon mentéséhez válassza a **Mentés** lehetőséget. 
1. Az **Egyéni telepítés** lapon kövesse az alábbi lépéseket: 
    1. Válasszon ki egy Azure- **előfizetést**. 
    2. Válasszon ki egy meglévő **erőforráscsoportot** , vagy hozzon létre egyet. 
    3. Válassza ki a **célhelyet vagy** régiót. Ha kiválasztott egy meglévő erőforráscsoportot, ez a beállítás csak olvasható. 
    4. A **Beállítások** szakaszban hajtsa végre a következő lépéseket:    
        1. Adja meg az új **fürt nevét**. 

            :::image type="content" source="./media/move-cluster-across-regions/deploy-template.png" alt-text="Resource Manager-sablon üzembe helyezése":::
    5. Kattintson a lap alján található **felülvizsgálat + létrehozás** lehetőségre. 
    1. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.  

## <a name="discard-or-clean-up"></a>Elvetés vagy felesleges elemek eltávolítása
Ha az üzembe helyezést követően el szeretné indítani a műveletet, törölheti a **cél Event Hubs dedikált fürtöt**, és megismételheti a cikk [előkészítés](#prepare) és [Áthelyezés](#move) szakaszában ismertetett lépéseket.

A módosítások elvégzéséhez és egy Event Hubs-fürt áthelyezésének befejezéséhez törölje a **Event Hubs-fürtöt** az eredeti régióban. 

Event Hubs-fürt (forrás vagy cél) törlése a Azure Portal használatával:

1. A Azure Portal tetején található Keresés ablakban írja be a **Event Hubs fürtök** kifejezést, és válassza ki **Event Hubs-fürtöket** a keresési eredmények közül. A listában megjelenik a Event Hubs-fürt.
2. Válassza ki a törölni kívánt fürtöt, és válassza a **Törlés** lehetőséget az eszköztárból. 
3. A **fürt törlése** lapon erősítse meg a törlést a **fürt nevének** beírásával, majd válassza a **Törlés** lehetőséget. 

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtanulta, hogyan helyezhet át egy Event Hubs dedikált fürtöt az egyik régióból a másikba. 

A névtér egyik régióból egy másik régióba való áthelyezésével kapcsolatos utasításokért tekintse meg [Event Hubs névterek áthelyezése a régiók között](move-across-regions.md) című cikket. 

Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:

- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](../site-recovery/azure-to-azure-tutorial-migrate.md)
