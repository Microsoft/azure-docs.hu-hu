---
title: QnA Maker szolgáltatás beállítása – QnA Maker
description: Mielőtt bármilyen QnA Maker tudásbázist létrehozni, először be kell állítania egy QnA Maker szolgáltatást az Azure-ban. Az előfizetésben lévő új erőforrások létrehozásához szükséges engedélyekkel rendelkező bárki beállíthat QnA Maker szolgáltatást.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: b6ab131c0fa81609b956de53f2b15d445e8979dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102219267"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker erőforrások kezelése

Mielőtt bármilyen QnA Maker tudásbázist létrehozni, először be kell állítania egy QnA Maker szolgáltatást az Azure-ban. Az előfizetésben lévő új erőforrások létrehozásához szükséges engedélyekkel rendelkező bárki beállíthat QnA Maker szolgáltatást.

Az erőforrás létrehozása előtt a következő fogalmak alapos ismerete hasznos:

* [Erőforrások QnA Maker](../Concepts/azure-resources.md)
* [Kulcsok létrehozása és közzététele](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Új QnA Maker szolgáltatás létrehozása

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

Ez az eljárás létrehozza a Tudásbázis tartalmának kezeléséhez szükséges Azure-erőforrásokat. A lépések elvégzése után megtalálhatja az _előfizetési_ kulcsokat a Azure Portal erőforrás **kulcsok** lapján.

1. Jelentkezzen be a Azure Portalba, és [hozzon létre egy QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) -erőforrást.

1. Válassza a **Létrehozás** lehetőséget a használati feltételek elolvasása után:

    ![Új QnA Maker szolgáltatás létrehozása](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. A **QnA Maker** területen válassza ki a megfelelő szintet és régiókat:

    ![Új QnA Maker szolgáltatás létrehozása – árképzési réteg és régiók](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * A **név** mezőben adjon meg egy egyedi nevet a QnA Maker szolgáltatás azonosításához. Ez a név azonosítja azt a QnA Maker végpontot is, amelyhez a Tudásbázis társítva lesz.
    * Válassza ki azt az **előfizetést** , amely alatt a QnA Maker erőforrást telepíteni fogja.
    * Válassza ki a QnA Maker felügyeleti szolgáltatások (portál és felügyeleti API-k) **díjszabási szintjét** . [További részletek a SKU díjszabásáról](https://aka.ms/qnamaker-pricing).
    * Hozzon létre egy új **erőforráscsoportot** (ajánlott), vagy használjon egy meglévőt, amelyben a QnA Maker-erőforrást szeretné telepíteni. QnA Maker több Azure-erőforrást hoz létre. Amikor létrehoz egy erőforráscsoportot az erőforrások tárolásához, egyszerűen megkeresheti, kezelheti és törölheti ezeket az erőforrásokat az erőforráscsoport neve alapján.
    * Válassza ki az **erőforráscsoport helyét**.
    * Válassza ki az Azure Cognitive Search szolgáltatás **keresési díjszabási szintjét** . Ha az ingyenes csomag lehetőség nem érhető el (szürkén jelenik meg), az azt jelenti, hogy már rendelkezik az előfizetésen keresztül üzembe helyezett ingyenes szolgáltatással. Ebben az esetben az alapszintű csomaggal kell kezdődnie. Tekintse meg az [Azure Cognitive Search díjszabását](https://azure.microsoft.com/pricing/details/search/).
    * Válassza ki azt a **keresési helyet** , ahová telepíteni szeretné az Azure Cognitive Search indexeit. Az ügyféladatok tárolási helyétől való korlátozásával meghatározhatja az Azure Cognitive Search számára kiválasztott helyet.
    * Az **alkalmazás neve** mezőben adja meg Azure app Service példányának nevét.
    * Alapértelmezés szerint App Service alapértelmezett érték a standard (S1) szint. A tervet a létrehozás után is módosíthatja. További információ a [app Service díjszabásáról](https://azure.microsoft.com/pricing/details/app-service/).
    * Válassza ki a **webhely helyét** , ahol a app Service telepíteni fogja.

        > [!NOTE]
        > A **Keresés helye** eltérő lehet a **webhely helyétől**.

    * Adja meg, hogy szeretné-e engedélyezni **Application Insights**. Ha a **Application Insights** engedélyezve van, QnA Maker gyűjti a telemetria a forgalom, a csevegési naplók és a hibák alapján.
    * Válassza ki azt az **alkalmazás-bepillantást** , ahol a Application Insights erőforrást telepíteni fogja.
    * A költségmegtakarítási mértékek esetében [megoszthat](configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) néhányat, de nem minden QnA Makerhoz létrehozott Azure-erőforrást.

1. Miután az összes mezőt érvényesíti, válassza a **Létrehozás** lehetőséget. A folyamat eltarthat néhány percig.

1. Az üzembe helyezés befejezése után az előfizetésében létrehozott alábbi erőforrások jelennek meg:

   ![Az erőforrás új QnA Maker szolgáltatást hozott létre](../media/qnamaker-how-to-setup-service/resources-created.png)

    Az _Cognitive Services_ típusú erőforrás rendelkezik az _előfizetési_ kulcsokkal.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

Ez az eljárás létrehozza a Tudásbázis tartalmának kezeléséhez szükséges Azure-erőforrásokat. A lépések elvégzése után megtalálhatja az *előfizetési* kulcsokat a Azure Portal erőforrás **kulcsok** lapján.

1. Jelentkezzen be a Azure Portalba, és [hozzon létre egy QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) -erőforrást.

1. Válassza a **Létrehozás** lehetőséget a használati feltételek elolvasása után:

    ![Új QnA Maker szolgáltatás létrehozása](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. A **QnA Makerban** jelölje be a felügyelt (előzetes verzió) jelölőnégyzetet, és válassza ki a megfelelő csomagokat és régiókat:

    ![Új QnA Maker felügyelt szolgáltatás – díjszabási réteg és régiók létrehozása](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png)

    * Válassza ki azt az **előfizetést** , amely alatt a QnA Maker erőforrást telepíteni fogja.
    * Hozzon létre egy új **erőforráscsoportot** (ajánlott), vagy használjon egy meglévőt, amelyben telepíteni szeretné ezt a QnA Maker felügyelt (előzetes verzió) erőforrást. QnA Maker felügyelt (előzetes verzió) néhány Azure-erőforrást hoz létre. Amikor létrehoz egy erőforráscsoportot az erőforrások tárolásához, egyszerűen megkeresheti, kezelheti és törölheti ezeket az erőforrásokat az erőforráscsoport neve alapján.
    * A **név** mezőben adjon meg egy egyedi nevet a QnA Maker felügyelt (előzetes verzió) szolgáltatás azonosításához. 
    * Válassza ki azt a **helyet** , ahol a QnA Maker felügyelt (előzetes verzió) szolgáltatást telepíteni szeretné. A felügyeleti API-k és a szolgáltatási végpont ezen a helyen lesz üzemeltetve. 
    * Válassza ki a QnA Maker felügyelt (előzetes verzió) szolgáltatás **díjszabási szintjét** (előzetes verzióként ingyenes). [További részletek a SKU díjszabásáról](https://aka.ms/qnamaker-pricing).
    * Válassza ki azt a **keresési helyet** , ahová telepíteni szeretné az Azure Cognitive Search indexeit. Az ügyféladatok tárolási helyétől való korlátozásával meghatározhatja az Azure Cognitive Search számára kiválasztott helyet.
    * Válassza ki az Azure Cognitive Search szolgáltatás **keresési díjszabási szintjét** . Ha az ingyenes csomag lehetőség nem érhető el (szürkén jelenik meg), az azt jelenti, hogy már rendelkezik az előfizetésen keresztül üzembe helyezett ingyenes szolgáltatással. Ebben az esetben az alapszintű csomaggal kell kezdődnie. Tekintse meg az [Azure Cognitive Search díjszabását](https://azure.microsoft.com/pricing/details/search/).

1. Az összes mező ellenőrzése után válassza a **felülvizsgálat + létrehozás** elemet. A folyamat eltarthat néhány percig.

1. Az üzembe helyezés befejezése után az előfizetésében létrehozott alábbi erőforrások jelennek meg:

    ![Az erőforrás új QnA Maker felügyelt (előzetes verzió) szolgáltatást hozott létre](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

    Az _Cognitive Services_ típusú erőforrás rendelkezik az _előfizetési_ kulcsokkal.

---

## <a name="upgrade-azure-resources"></a>Azure-erőforrások frissítése

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

### <a name="upgrade-qna-maker-sku"></a>QnA Maker SKU frissítése

Ha további kérdésekkel és válaszokkal szeretne rendelkezni a Tudásbázisban, az aktuális szinten túlmutatva frissítse a QnA Maker Service díjszabási szintjét.

A QnA Maker felügyeleti SKU frissítése:

1. Nyissa meg a QnA Maker erőforrást a Azure Portal, és válassza az **árképzési szintet**.

    ![Erőforrás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Válassza ki a megfelelő SKU-t, majd kattintson a **kiválasztás** gombra.

    ![Díjszabás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)
    
### <a name="upgrade-app-service"></a>App Service frissítése

Ha a Tudásbázisnak több kérést kell kiszolgálnia az ügyfélalkalmazás számára, frissítse a App Service díjszabási szintjét.

A App Service vertikális [fel](../../../app-service/manage-scale-up.md) -vagy felskálázása.

Nyissa **meg a Azure Portal** app Service erőforrását, és szükség szerint válassza ki a vertikális felskálázás vagy a **kibővítés** lehetőséget.

![QnA Maker App Service skálázás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Az Azure Cognitive Search szolgáltatás frissítése

Ha sok tudásbázis használatát tervezi, váltson magasabb szintű tarifacsomagra az Azure Cognitive Search szolgáltatás esetében.

Jelenleg nem végezhető el az Azure Search SKU helyben történő frissítése. Létrehozhat azonban egy új Azure Search-erőforrást a kívánt SKU-val, visszaállíthatja az új erőforrást, majd csatolhatja azt a QnA Maker veremhez. Ehhez kövesse az alábbi lépéseket:

1. Hozzon létre egy új Azure Search-erőforrást a Azure Portalban, és válassza ki a kívánt SKU-t.

    ![Azure Search-erőforrás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Állítsa vissza az indexeket az eredeti Azure Search-erőforrásból az újat. Tekintse meg a [biztonsági mentésre és visszaállításra vonatkozó mintakódot](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Az adatvisszaállítást követően nyissa meg az új Azure Search-erőforrást, válassza a **kulcsok** lehetőséget, és jegyezze fel a **nevet** és a **rendszergazdai kulcsot**:

    ![Azure Search-kulcsok QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Ha az új Azure Search-erőforrást az QnA Maker veremhöz szeretné kapcsolni, lépjen a QnA Maker App Service példányra.

    ![QnA Maker App Service példány](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Válassza az **Alkalmazásbeállítások** elemet, és módosítsa a beállításokat az **AzureSearchName** és az **AzureSearchAdminKey** mezőben a 3. lépésben feljegyzett értékekre.

    ![QnA Maker App Service beállítás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Indítsa újra az App Service-példányt.

    ![A QnA Maker App Service példány újraindítása](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)
    
### <a name="inactivity-policy-for-free-search-resources"></a>Inaktivitási szabályzat ingyenes keresési erőforrásokhoz

Ha nem használ QnA Maker-erőforrást, távolítsa el az összes erőforrást. Ha nem távolítja el a nem használt erőforrásokat, a Tudásbázis nem fog működni, ha létrehozott egy ingyenes keresési erőforrást.

Az ingyenes keresési erőforrások 90 nap után törlődnek, és nem kapnak API-hívást.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Az Azure Cognitive Search szolgáltatás frissítése

Ha sok tudásbázis használatát tervezi, váltson magasabb szintű tarifacsomagra az Azure Cognitive Search szolgáltatás esetében.

Jelenleg nem végezhető el az Azure Search SKU helyben történő frissítése. Létrehozhat azonban egy új Azure Search-erőforrást a kívánt SKU-val, visszaállíthatja az új erőforrást, majd csatolhatja azt a QnA Maker veremhez. Ehhez kövesse az alábbi lépéseket:

1. Hozzon létre egy új Azure Search-erőforrást a Azure Portalban, és válassza ki a kívánt SKU-t.

    ![Azure Search-erőforrás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Állítsa vissza az indexeket az eredeti Azure Search-erőforrásból az újat. Tekintse meg a [biztonsági mentésre és visszaállításra vonatkozó mintakódot](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Ha az új Azure Search-erőforrást a QnA Maker felügyelt (előzetes verzió) szolgáltatáshoz szeretné kapcsolni, tekintse meg az alábbi témakört.

### <a name="inactivity-policy-for-free-search-resources"></a>Inaktivitási szabályzat ingyenes keresési erőforrásokhoz

Ha nem használ QnA Maker-erőforrást, távolítsa el az összes erőforrást. Ha nem távolítja el a nem használt erőforrásokat, a Tudásbázis nem fog működni, ha létrehozott egy ingyenes keresési erőforrást.

Az ingyenes keresési erőforrások 90 nap után törlődnek, és nem kapnak API-hívást.

---

## <a name="delete-azure-resources"></a>Azure-erőforrások törlése

Ha törli a QnA Maker tudásbázishoz használt Azure-erőforrásokat, a tudásbázisok nem fognak működni. Az erőforrások törlése előtt győződjön meg arról, hogy a tudásbázisokat a **Beállítások** lapról exportálja.

## <a name="next-steps"></a>Következő lépések

További információ az [app Service](../../../app-service/index.yml) és a [Search szolgáltatásról](../../../search/index.yml).

> [!div class="nextstepaction"]
> [További információ a másokkal való létrehozásról](../index.yml)
