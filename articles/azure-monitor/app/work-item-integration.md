---
title: Munkaelem-integráció (előzetes verzió) – Application Insights
description: Megtudhatja, hogyan hozhat létre munkaelemeket a GitHubon vagy az Azure DevOps-ban Application Insights beágyazott adatokkal.
ms.topic: conceptual
ms.date: 02/9/2021
ms.openlocfilehash: ba0a67bad3ba47191414d6b406ab6cb4e6b7da78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731918"
---
# <a name="work-item-integration-preview"></a>Munkaelem-integráció (előzetes verzió)

A munkaelem-integráció funkció lehetővé teszi, hogy könnyedén hozzon létre olyan munkaelemeket a GitHubban vagy az Azure DevOps, amelyek releváns Application Insights adatokkal vannak beágyazva.

> [!IMPORTANT]
> A munkaelem-integráció jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-and-configure-a-work-item-template"></a>Munkaelem-sablon létrehozása és konfigurálása

1. Munkaelem-sablon létrehozásához nyissa meg a Application Insights erőforrást, és a bal oldalon *a válassza ki* a **munkaelemek** kiválasztása lehetőséget, majd a felső részen válassza az **új sablon létrehozása** elemet.

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" Képernyőfelvétel a munkaelemek lapról a kiválasztott új sablon létrehozása elemre." lightbox="./media/work-item-integration/create-work-item-template.png":::

    A végpontok közötti tranzakció részletei lapon létrehozhat egy munkaelem-sablont is, ha nem létezik sablon. Válasszon ki egy eseményt, és a jobb gombbal válassza a **munkaelem létrehozása elemet**, majd **kezdjen el egy munkafüzet-sablonnal**.

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" Képernyőkép a végpontok közötti tranzakció részletei lapról a munkaelem létrehozásával, Kezdés a kiválasztott munkafüzet-sablonnal." lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. Miután kiválasztotta az **új sablon létrehozása** lehetőséget, kiválaszthatja a nyomon követési rendszereket, megnevezheti a munkafüzetet, összekapcsolhatja a kiválasztott követési rendszert, és kiválaszthat egy régiót a sablon tárolásához (az alapértelmezett az a régió, amelyben a Application Insights erőforrás található). Az URL-paraméterek az adattár alapértelmezett URL-címe, például `https://github.com/myusername/reponame` vagy `https://mydevops.visualstudio.com/myproject` .

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" Képernyőfelvétel: új munkaelem-munkafüzet sablon létrehozása.":::

## <a name="create-a-work-item"></a>Munkaelem létrehozása

 Az új sablont bármely végpontok közötti tranzakció részleteivel elérheti, amelyeket elérheti a teljesítmény, a hibák, a rendelkezésre állás vagy más lapok.

1. Munkaelem létrehozásához nyissa meg a végpontok közötti tranzakció részleteit, válasszon ki egy eseményt, majd válassza a munkaelem **létrehozása elemet** , és válassza ki a munkaelem-sablont.

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" Képernyőfelvétel a végpontok közötti tranzakció részletei lapról a kiválasztott munkaelem kiválasztásával." lightbox="./media/work-item-integration/create-work-item.png":::

1. A böngészőben egy új lap nyílik meg a Select Tracking rendszerre. Az Azure DevOps létrehozhat egy hibát vagy feladatot, a GitHubban pedig létrehozhat egy új problémát a tárházban. A rendszer automatikusan létrehoz egy új munkaelemet a Application Insights által biztosított környezetfüggő információkkal.

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" Az automatikusan létrehozott GitHub-probléma képernyőképe" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Képernyőfelvétel: az Azure DevOps automatikusan létrehozza a hibát." lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>Sablon szerkesztése

A sablon szerkesztéséhez nyissa meg a **munkaelemek** lapot a *configure (Konfigurálás* ) területen, és válassza a frissíteni kívánt munkafüzet melletti ceruza ikont.

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" Képernyőfelvétel a munkaelem lapról a ceruza szerkesztése ikon kiválasztásával.":::

A ![ sablon szerkesztésének ](./media/work-item-integration/edit-icon.png) megkezdéséhez válassza a felül a szerkesztési ikon szerkesztése lehetőséget. A munkaelem-sablonok [Azure monitor munkafüzetek](../visualize/workbooks-overview.md)alapján működnek. A munkaelem adatai a kulcsszó lekérdezési nyelv használatával jönnek létre. A lekérdezéseket úgy módosíthatja, hogy a csapat számára még nagyobb kontextust adjanak hozzá. Ha végzett a szerkesztéssel, mentse a munkafüzetet a ![ felső eszköztár mentés ikon mentése ikonjára kattintva ](./media/work-item-integration/save-icon.png) .

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" Képernyőkép a munkaelem-sablon munkafüzetéről szerkesztési módban." lightbox="./media/work-item-integration/edit-workbook.png":::

Több munkaelem-konfigurációt is létrehozhat, és az egyes forgatókönyvek teljesítéséhez egyéni munkafüzetet kell létrehoznia. A munkafüzetek Azure Resource Manager is üzembe helyezhetők, így biztosítható a környezetek szokásos implementációja.