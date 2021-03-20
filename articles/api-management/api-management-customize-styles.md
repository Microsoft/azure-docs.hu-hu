---
title: Az oldal stílusainak testreszabása API Management örökölt fejlesztői portálon
titleSuffix: Azure API Management
description: Ennek a rövid útmutatónak a lépéseit követve szabhatja testre az elemek stílusát az Azure API Management fejlesztői portálon.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 0cdad245981ad02d3cdaefba447d131c775e242f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93145769"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>A fejlesztői portál oldalai stílusának testreszabása

A fejlesztői portál testreszabásának három leggyakoribb módja van az Azure API Managementban:
 
* [A statikus oldalak tartalmának és oldalelrendezési elemeinek szerkesztése](api-management-modify-content-layout.md)
* A teljes fejlesztői portál oldalelemeihez használt stílusok frissítése (részletesen ebben az útmutatóban)
* [A portál által létrehozott oldalakhoz használt sablonok módosítása](api-management-developer-portal-templates.md) (például API-dokumentáció, termékek vagy felhasználói hitelesítés)

Ebből a cikkből megtudhatja, hogyan szabhatja testre az elemek stílusát az örökölt **fejlesztői** portál oldalain, és megtekintheti a módosításokat.

![Képernyőfelvétel: a régi fejlesztői portálon a beállítások módosításának helyét mutatja.](./media/modify-developer-portal-style/developer_portal.png)

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>A fejlesztői portál testreszabása

1. Válassza az **Áttekintés** lehetőséget.
2. Kattintson a **fejlesztői portál (örökölt)** gombra az **Áttekintés** ablak tetején.
3. A képernyő bal felső részén egy két ecsetet ábrázoló ikon látható. Vigye a mutatót az ikon fölé a portál testreszabási menüjének megnyitásához.

    ![Képernyőkép, amely kiemeli az ikont két ecsettel.](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Válassza a menü **Stílusok** elemét a stílus testreszabási paneljének megnyitásához.

    A **Stílusok** panellel testre szabható összes elem megjelenik az oldalon.
5. Írja be a „headings-color” (fejléc színe) kifejezést a **Változó értékeinek módosítása a fejlesztői portál megjelenésének testreszabásához:** mezőbe.

    A **\@ fejlécek – szín** elem jelenik meg az oldalon. Ez a változó szabályozza a szöveg színét.

    ![stílus testreszabása](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Kattintson a **\@ fejlécek-Color** változó mezőre. 
    
    Megjelenik a színválasztó legördülő menü.
7. A színválasztó legördülő menüben válassza ki az új színt.

    > [!TIP]
    > Minden módosítás előnézete megtekinthető valós időben. Egy folyamatjelző jelenik meg a testreszabási panel tetején. Néhány másodperc elteltével a fejléc szövegének színe átvált az újonnan kijelölt színre.

8. Válassza a **Közzététel** lehetőséget a testreszabási panel menüjének bal alsó részén.
9. Válassza a **Testreszabások közzététele** lehetőséget a módosítások nyilvánosan elérhetővé tételéhez.

## <a name="view-your-change"></a>A módosítások megtekintése

1. Navigáljon a fejlesztői portálra.
2. Itt megtekintheti a végrehajtott módosításokat.

## <a name="next-steps"></a>Következő lépések

További érdekes tudnivalók [az Azure API Management fejlesztői portál sablonok használatával történő testreszabásáról](api-management-developer-portal-templates.md).