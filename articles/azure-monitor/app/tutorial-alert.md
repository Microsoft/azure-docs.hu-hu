---
title: Riasztások küldése az Azure Application Insightsból | Microsoft Docs
description: 'Oktatóanyag: riasztások küldése az alkalmazásban előforduló hibákra válaszul az Azure Application Insights használatával.'
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 6b8cc951301ec315f758478eda1c0736882e66f2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100627679"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Az alkalmazás állapotának figyelése és riasztása az Azure Application Insights

Az Azure Application Insights lehetővé teszi az alkalmazás figyelését, és riasztások küldését, ha nem érhető el, hiba történt, vagy a teljesítménnyel kapcsolatos problémák okozzák.  Ez az oktatóanyag végigvezeti az alkalmazás rendelkezésre állásának folyamatos ellenőrzéséhez szükséges tesztek létrehozásának folyamatán.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Rendelkezésre állási teszt létrehozása az alkalmazás válaszának folyamatos ellenőrzéséhez
> * Levelezés küldése a rendszergazdáknak probléma esetén

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

Hozzon létre egy [Application Insights erőforrást](../app/create-new-resource.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-availability-test"></a>Rendelkezésre állási teszt létrehozása

A Application Insights rendelkezésre állási tesztek lehetővé teszik az alkalmazások automatikus tesztelését a világ különböző helyeiről.   Ebben az oktatóanyagban egy URL-tesztet fog végrehajtani, amely biztosítja, hogy a webalkalmazás elérhető legyen.  Létrehozhat egy teljes bemutatót is a részletes művelet teszteléséhez. 

1. Válassza ki az **Application Insights** elemet, majd az előfizetését.  

2. A vizsgálat menüben válassza a **rendelkezésre állás** **lehetőséget,** majd kattintson a **teszt létrehozása** lehetőségre.

    ![Rendelkezésre állási teszt hozzáadása](media/tutorial-alert/add-test-001.png)

3. Írja be a teszt nevét, és hagyja meg a többi alapértelmezett értéket.  A kijelölés 5 percenként elindítja az alkalmazás URL-címére irányuló kérelmeket öt különböző földrajzi helyről.

4. Válassza a **riasztások** lehetőséget a **riasztások** legördülő listájának megnyitásához, ahol megadhatja, hogyan válaszoljon a tesztek, ha a teszt sikertelen. Válassza a **közel valós idejű** lehetőséget, és állítsa be az állapotot engedélyezve értékre **.**

    Írjon be egy e-mail-címet, amelyet a rendszer a riasztási feltételek teljesülése esetén küld.  A riasztás feltételeinek teljesülése esetén megadható, hogy a webhook milyen címen hívható meg.

    ![Teszt létrehozása](media/tutorial-alert/create-test-001.png)

5. Térjen vissza a teszt panelre, kattintson a három pontra, majd a riasztás szerkesztése elemre a közel valós idejű riasztás konfigurációjának megadásához.

    ![Riasztás szerkesztése](media/tutorial-alert/edit-alert-001.png)

6. A sikertelen tárolóhelyek beállítása 3 értéknél nagyobb vagy azzal egyenlő. Hozzon létre egy [műveleti csoportot](../alerts/action-groups.md) , amely azt konfigurálja, hogy ki kapja meg a riasztási küszöbérték megszegése esetén a rendszer értesítést kapjon.

    ![Riasztás felhasználói felületének mentése](media/tutorial-alert/save-alert-001.png)

7. Miután konfigurálta a riasztást, a teszt nevére kattintva megtekintheti az egyes helyek részleteit. A tesztek a grafikonon és a pontdiagram formátumában is megtekinthetők az adott időtartomány sikerességének és hibáinak megjelenítéséhez.

    ![Teszt részletei](media/tutorial-alert/test-details-001.png)

8. Az összes teszt részleteinek részletezéséhez kattintson a pontra a scatter diagramon. Ez elindítja a végpontok közötti tranzakció részletes nézetét. Az alábbi példa egy sikertelen kérelem részleteit mutatja be.

    ![Teszt eredménye](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a problémákkal kapcsolatos riasztásokat, folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan használhatja a felhasználókat az alkalmazással való kommunikációra.

> [!div class="nextstepaction"]
> [A felhasználók megismerése](./tutorial-users.md)

