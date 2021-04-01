---
title: Azure Event Hubs-névtér áthelyezése másik régióba | Microsoft Docs
description: Ez a cikk bemutatja, hogyan helyezhet át egy Azure Event Hubs-névteret az aktuális régióból egy másik régióba.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: b177c3916919e3d97325f9d8c6b6027c00cb476f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019922"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Azure Event Hubs-névtér áthelyezése másik régióba
Ez a cikk bemutatja, hogyan exportálhat egy Azure Resource Manager sablont egy meglévő Event Hubs névtérbe, majd a sablonnal létrehozhat egy másik régióban azonos konfigurációs beállításokkal rendelkező névteret. Azonban ez a folyamat nem helyezi át a még nem feldolgozott eseményeket. A törlés előtt fel kell dolgoznia az eseményeket az eredeti névtérből.
 
Ha a Event Hubs névteret tartalmazó Azure-erőforráscsoport egyéb erőforrásai is vannak, érdemes lehet exportálni a sablont az erőforráscsoport szintjén, hogy az összes kapcsolódó erőforrás áthelyezhető az új régióba egy lépésben. A cikkben ismertetett lépések bemutatják, hogyan exportálhat egy **névteret** a sablonba. Az **erőforráscsoportok** sablonba való exportálásának lépései hasonlóak. 

## <a name="prerequisites"></a>Előfeltételek

- Ellenőrizze, hogy a tárfiók által használt szolgáltatások és funkciók támogatottak-e a célként megadott régióban.
- Ha az Event hubok esetében engedélyezve van a **rögzítési funkció** a névtérben, helyezze át az [Azure Storage-t vagy Azure Data Lake Store gen 2](../storage/common/storage-account-move.md) vagy [Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) fiókot az Event Hubs névtér áthelyezése előtt. A tárolót és a Event Hubs névtereket is tartalmazó erőforráscsoportot áthelyezheti a másik régióba a jelen cikkben ismertetett lépésekhez hasonló módon. 
- Ha a Event Hubs névtér egy **Event Hubs fürtben** található, [Helyezze át a dedikált fürtöt](move-cluster-across-regions.md) a **célként megadott régióba** , mielőtt átugorja a jelen cikkben szereplő lépéseket. Event Hubs-fürt létrehozásához használhatja a [githubon a Gyorsindítás sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-cluster-namespace-eventhub/) is. A sablonban távolítsa el a JSON névtér részét, hogy csak a fürtöt hozza létre. 

## <a name="prepare"></a>Előkészítés
Első lépésként exportáljon egy Resource Manager-sablont. Ez a sablon a Event Hubs névteret leíró beállításokat tartalmaz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden erőforrás** lehetőséget, majd válassza ki a Event Hubs névteret.
3. Válassza > **Beállítások**  >  **Exportálás sablon** lehetőséget.
4. A **sablon exportálása** lapon kattintson a **Letöltés** elemre.

    ![Resource Manager-sablon letöltése](./media/move-across-regions/download-template.png)
5. Keresse meg a portálról letöltött. zip fájlt, és bontsa ki a fájlt egy tetszőleges mappába.

   Ez a zip-fájl tartalmazza azokat a. JSON fájlokat, amelyek tartalmazzák a sablont és a parancsfájlokat a sablon telepítéséhez.


## <a name="move"></a>Áthelyezés

A sablon üzembe helyezésével hozzon létre egy Event Hubs névteret a célként megadott régióban. 


1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.
2. A **Keresés a piactéren** mezőbe írja be a **sablon központi telepítése** kifejezést, és válassza a **template Deployment (üzembe helyezés egyéni sablonok használatával)** lehetőséget.
5. Válassza **a saját sablon létrehozása lehetőséget a szerkesztőben**.
6. Válassza a **fájl betöltése** lehetőséget, majd kövesse az utasításokat az utolsó szakaszban letöltött fájl **template.js** betöltéséhez.
1. Frissítse a `location` tulajdonság értékét úgy, hogy az az új régióra mutasson. A helykód beszerzéséhez tekintse meg az [Azure-helyek](https://azure.microsoft.com/global-infrastructure/locations/)című témakört. A régió kódja a régió neve, szóközök nélkül, például `West US` egyenlő `westus` .
1. A sablon mentéséhez válassza a **Mentés** lehetőséget. 
1. Az **Egyéni telepítés** lapon kövesse az alábbi lépéseket: 
    1. Válasszon ki egy Azure- **előfizetést**. 
    2. Válasszon ki egy meglévő **erőforráscsoportot** , vagy hozzon létre egyet. Ha a forrás névtere egy Event Hubs fürtben volt, válassza ki azt az erőforráscsoportot, amely a célként megadott régióban található fürtöt tartalmazza. 
    3. Válassza ki a **célhelyet vagy** régiót. Ha kiválasztott egy meglévő erőforráscsoportot, ez a beállítás csak olvasható. 
    4. A **Beállítások** szakaszban hajtsa végre a következő lépéseket:    
        1. Adja meg az új **névtér nevét**. 

            ![Resource Manager-sablon üzembe helyezése](./media/move-across-regions/deploy-template.png)
        2. Ha a forrás névtere egy **Event Hubs fürtben** volt, írja be az **erőforráscsoport** nevét és a **Event Hubs-fürtöt** a **külső azonosító** részeként. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Ha a névtérben az Event hub Storage-fiókot használ az események rögzítéséhez, adja meg az erőforráscsoport nevét és a Storage-fiókot a (z `StorageAccounts_<original storage account name>_external` ) mezőben. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Kattintson a lap alján található **felülvizsgálat + létrehozás** lehetőségre. 
    1. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.   

## <a name="discard-or-clean-up"></a>Elvetés vagy felesleges elemek eltávolítása
Ha az üzembe helyezést követően el szeretné indítani a műveletet, törölheti a **cél Event Hubs névteret**, és megismételheti a cikk [előkészítés](#prepare) és [Áthelyezés](#move) szakaszában ismertetett lépéseket.

A módosítások végrehajtásához és egy Event Hubs névtér áthelyezésének befejezéséhez törölje a **Event Hubs névteret** az eredeti régióban. A névtér törlése előtt győződjön meg arról, hogy a névtérben lévő összes eseményt feldolgozta. 

Event Hubs névtér (forrás vagy cél) törlése a Azure Portal használatával:

1. A Azure Portal tetején található Keresés ablakban írja be a **Event Hubs** kifejezést, és válassza a **Event Hubs** lehetőséget a keresési eredmények közül. A listában megjelenik a Event Hubs névterek listája.
2. Válassza ki a törölni kívánt cél névteret, és válassza a **Törlés** lehetőséget az eszköztárból. 

    ![Névtér törlése – gomb](./media/move-across-regions/delete-namespace-button.png)
3. A **névtér törlése** lapon erősítse meg a törlést a **névtér nevének** beírásával, majd válassza a **Törlés** lehetőséget. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure Event Hubs névteret helyezett át egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](../site-recovery/azure-to-azure-tutorial-migrate.md)
