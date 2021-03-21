---
title: Tudásbázisok kezelése – QnA Maker
description: A QnA Maker lehetővé teszi a Tudásbázis-beállítások és-tartalmak hozzáférésének biztosítását.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: c7bb79cbd67ba2fb151641ddeeb31b54e399b906
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96938256"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Tudásbázis létrehozása és beállítások kezelése

A QnA Maker a Tudásbázis-beállításokhoz és az adatforrásokhoz való hozzáférés biztosításával lehetővé teszi a tudásbázisok kezelését.

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/) a feladatok megkezdése előtt.
> * A Azure Portalban létrehozott [QnA Maker erőforrás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) . Jegyezze fel Azure Active Directory AZONOSÍTÓját, előfizetését, az erőforrás létrehozásakor kiválasztott QnA-erőforrás nevét.

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

1. Jelentkezzen be az [QnAMaker.ai](https://QnAMaker.ai) -portálra az Azure-beli hitelesítő adataival.

1. A QnA Maker portálon válassza a **Tudásbázis létrehozása** elemet.

1. A **Létrehozás** lapon hagyja ki az **1. lépést** , ha már rendelkezik a QnA Maker erőforrással.

    Ha még nem hozta létre az erőforrást, válassza **a QnA-szolgáltatás létrehozása** lehetőséget. A rendszer átirányítja az [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), ahol be kell állítania az előfizetésében egy QnA Maker-szolgáltatást. Jegyezze fel Azure Active Directory AZONOSÍTÓját, előfizetését, az erőforrás létrehozásakor kiválasztott QnA-erőforrás nevét.

    Amikor elkészült az erőforrás létrehozásával a Azure Portalban, térjen vissza a QnA Maker portálra, frissítse a böngésző oldalát, és folytassa a **2. lépéssel**.

1. A **3. lépésben** válassza ki az Active Directoryt, előfizetést, szolgáltatást (erőforrás) és a szolgáltatásban létrehozott összes Tudásbázis nyelvét.

   ![Képernyőkép a QnA Maker szolgáltatás tudásbázisának kiválasztásáról](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. A **3. lépésben** adja meg a Tudásbázis nevét `My Sample QnA KB` .

1. A **4. lépésben** konfigurálja a beállításokat a következő táblázattal:

    |Beállítás|Érték|
    |--|--|
    |**Az URL-címek, a. PDF vagy a. docx fájlok többszörös kinyerésének engedélyezése.**|Jelölje be|
    |**Alapértelmezett válasz szövege**| `Quickstart - default answer not found.`|
    |**+ URL-cím hozzáadása**|`https://azure.microsoft.com/en-us/support/faq/`|
    |**Csevegés**|**Professional** kiválasztása|

1. Az **5. lépésben** válassza **a saját kb létrehozása** lehetőséget.

    A kinyerési folyamat néhány percet vesz igénybe, hogy beolvassa a dokumentumot, és azonosítsa a kérdéseket és a válaszokat.

    Miután QnA Maker sikeresen létrehozta a tudásbázist, megnyílik a **Tudásbázis** lap. Ezen a lapon szerkesztheti a Tudásbázis tartalmát.

## <a name="edit-knowledge-base"></a>Tudásbázis szerkesztése

1.  Válassza a **saját tudásbázisok** lehetőséget a felső navigációs sávon.

       Az összes Ön által létrehozott vagy megosztott szolgáltatást megtekintheti az **utolsó módosítás** dátuma szerinti csökkenő sorrendben.

       ![Saját tudásbázisok](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Válasszon ki egy adott tudásbázist a szerkesztéshez.

1.  Válassza a **Beállítások** lehetőséget. A következő lista a módosítható mezőket tartalmazza.

       |Cél|Művelet|
       |--|--|
       |URL-cím hozzáadása|Új URL-címek hozzáadásával új GYIK-tartalmakat adhat hozzá a tudásbázishoz, ha a Tudásbázis kezelése elemre kattint **– > "+ URL-cím hozzáadása"** hivatkozást.|
       |URL-cím törlése|A Törlés ikonra kattintva törölheti a meglévő URL-címeket, a kukába is.|
       |Tartalom frissítése|Ha azt szeretné, hogy a tudásbázisa a meglévő URL-címek legújabb tartalmát bejárja, jelölje be a **refresh (frissítés** ) jelölőnégyzetet. Ekkor a rendszer egyszer frissíti a tudásbázist a legújabb URL-tartalommal. Ez nem állítja be a frissítések rendszeres ütemtervét.|
       |Fájl hozzáadása|Egy Tudásbázis részét képező támogatott dokumentum hozzáadásához válassza a **Tudásbázis kezelése** lehetőséget, majd a **+ fájl hozzáadása** elemet.|
    |Importálás|A meglévő tudásbázist a **Tudásbázis importálása** gomb kiválasztásával is importálhatja. |
    |Frissítés|A Tudásbázis frissítése a tudásbázishoz kapcsolódó QnA Maker szolgáltatás létrehozásakor használt **felügyeleti díjszabási szinttől** függ. Ha szükséges, a felügyeleti szintet Azure Portal is frissítheti.

  1. Miután végzett a Tudásbázis módosításával, a módosítások megőrzése érdekében válassza a **Mentés és a betanítás** lehetőséget a lap jobb felső sarkában.

       ![Mentés és betanítás](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Ha a **Mentés és a betanítás** kiválasztása előtt elhagyja a lapot, az összes módosítás elvész.



## <a name="manage-large-knowledge-bases"></a>Nagyméretű tudásbázisok kezelése

* **Adatforrás-csoportok**: a QnAs azon adatforrás szerint vannak csoportosítva, amelyből kibontották őket. Kibonthatja vagy összecsukhatja az adatforrást.

    ![Az adatforrással kapcsolatos kérdések és válaszok összecsukása és kibontása a QnA Maker adatforrás-sávjának használatával](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Keresés a Tudásbázisban**: a Tudásbázis táblázatának tetején található szövegmező beírásával keresheti meg a tudásbázist. Kattintson az ENTER gombra a kérdés, válasz vagy metaadatok tartalmának megkereséséhez. Kattintson az X ikonra a keresési szűrő eltávolításához.

    ![A kérdések és válaszok fölötti QnA Maker keresőmező használatával csökkentse a nézetet csak a szűrővel egyező elemek](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Tördelés**: az adatforrások gyors áthelyezése a nagyméretű tudásbázisok kezelésére

    ![A kérdések és válaszok lapokon való áthelyezéséhez használja a QnA Maker tördelési funkcióit.](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Tudásbázisok törlése

Egy Tudásbázis törlése (KB) állandó művelet. Nem vonható vissza. A Tudásbázis törlése előtt exportálja a tudásbázist a QnA Maker portál **Beállítások** lapjáról.

Ha a tudásbázist a közreműködők, a] (együttműködés – Knowledge-base.md) használatával osztja meg, akkor törölje, mindenki elveszti a KB-hoz való hozzáférést.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [kezelheti az](../index.yml) erőforrások összes tudásbázisának nyelvét.

* QnA-párok szerkesztése
* Az QnA Maker által használt Azure-erőforrások kezelése