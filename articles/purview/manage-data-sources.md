---
title: Adatforrások kezelése az Azure hatáskörébe (előzetes verzió)
description: Ismerje meg, hogyan regisztrálhat új adatforrásokat, hogyan kezelheti az adatforrások gyűjteményeit, és hogyan tekintheti meg a forrásokat az Azure hatáskörébe (előzetes verzió).
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 90a873b7de6ccc1ba21a05bf4c0e288ed668cac0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694461"
---
# <a name="manage-data-sources-in-azure-purview-preview"></a>Adatforrások kezelése az Azure hatáskörébe (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan regisztrálhat új adatforrásokat, hogyan kezelheti az adatforrások gyűjteményeit, és hogyan tekintheti meg a forrásokat az Azure hatáskörébe (előzetes verzió)

## <a name="register-a-new-source"></a>Új forrás regisztrálása

Új forrás regisztrálásához kövesse az alábbi lépéseket.

1. Nyissa meg a hatáskörébe Studio alkalmazást, és válassza a **források regisztrálása** csempét.

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Az Azure hatáskörébe Studio":::

1. Válassza a **regisztráció** lehetőséget, majd válassza ki a forrás típusát. Ez a példa az Azure Blob Storage-t használja. Válassza a **Folytatás** lehetőséget.

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="Válasszon egy adatforrás-típust a források regisztrálása lapon.":::

1. Töltse ki az űrlapot a **források regisztrálása** lapon. Válassza ki a forrás nevét, és adja meg a megfelelő adatokat. Ha kiválasztotta az **Azure-előfizetést** a fiók kiválasztása módszerként, akkor az előfizetésében lévő források egy legördülő listában jelennek meg. Azt is megteheti, hogy manuálisan adja meg a forrás adatait.

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="Adatforrás-információ űrlapja":::

1. Válassza a **Befejezés** gombot.

## <a name="view-sources"></a>Források megtekintése

Az Azure hatáskörébe Studio **források** lapján megtekintheti az összes regisztrált forrást. Kétféle nézettípus létezik: Térkép nézet és listanézet.

### <a name="map-view"></a>Térképnézet

A Térkép nézetben az összes forrását és gyűjteményét láthatja. Az alábbi képen egy Azure Blob Storage-forrás található. Minden forrás csempén szerkesztheti a forrást, új vizsgálatot indíthat, vagy megtekintheti a forrás részleteit.

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Az Azure-beli-adatforrások térképének nézete":::

### <a name="list-view"></a>Listanézet

A lista nézetben megtekintheti a források rendezhető listáját. Vigye a kurzort a forráshoz a szerkesztési lehetőségek, új vizsgálat megkezdése vagy Törlés lehetőségre.

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Az Azure-beli hatáskörébe adatforrások listájának nézete":::

## <a name="manage-collections"></a>Gyűjtemények kezelése

Az adatforrásokat gyűjteményekbe is csoportosíthatja. Új gyűjtemény létrehozásához válassza az **+ új gyűjtemény** lehetőséget az Azure hatáskörébe Studio *források* lapján. Adja meg a gyűjtemény nevét, és válassza a *none* értéket szülőként. Az új gyűjtemény megjelenik a Térkép nézetben.

Ha forrásokat szeretne hozzáadni egy gyűjteményhez, válassza a forrás **szerkesztése** lehetőséget, és válasszon egy gyűjteményt a **gyűjtemény kiválasztása** legördülő menüből.

Gyűjtemények hierarchiájának létrehozásához rendeljen magasabb szintű gyűjteményeket szülőként az alacsonyabb szintű gyűjtemények számára. Az alábbi ábrán a *Fabrikam* a *pénzügyi* gyűjtemény szülője, amely egy Azure Blob Storage-adatforrást tartalmaz. Összecsukhatja vagy kibonthatja a gyűjteményeket a szintek közötti nyílhoz csatolt kör elemre kattintva.

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Gyűjtemények hierarchiája az Azure hatáskörébe Studióban":::

A hierarchiában lévő források eltávolításához válassza a *nincs lehetőséget* a szülő számára. A nem fölérendelt források a Térkép nézet pontozott mezőjébe vannak csoportosítva, és a nyilak nem kapcsolódnak a szülőhöz.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan regisztrálhat és vizsgálhat különféle adatforrásokat:

* [Azure Data Lake Storage Gen 2](register-scan-adls-gen2.md)
* [Power BI bérlő](register-scan-power-bi-tenant.md)
* [Azure SQL Database](register-scan-azure-sql-database.md)
