---
title: Azure CDN Endpoint multi-Origin (előzetes verzió)
description: Ismerkedjen meg Azure CDN Endpoint több eredetével.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 9/06/2020
ms.author: allensu
ms.openlocfilehash: 6e433950c04c4494201b090063b17a10e54a4822
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685771"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN Endpoint multi-Origin

A többszörös eredetű támogatás kiküszöböli az állásidőt, és létrehozza a globális redundanciát. 

Ha több kezdőpontot választ egy Azure CDN végponton belül, a redundancia a kockázatot az egyes források állapotának kiszűrésével, és szükség esetén feladatátvételsel terjeszti ki.

Állítson be egy vagy több származási csoportot, és válasszon ki egy alapértelmezett Origó csoportot. Az egyes Origin csoportok egy vagy több olyan gyűjteményből állnak, amelyek hasonló számítási feladatokat végezhetnek.

> [!NOTE]
> Ez a funkció jelenleg csak a Microsoft Azure CDNból érhető el. 

## <a name="create-the-origin-group"></a>A Origin csoport létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

2. Válassza ki a Azure CDN profilt, majd válassza ki a többszörös forráshoz konfigurálni kívánt végpontot.

3. A végpont konfigurációjának **Beállítások** területén válassza a **forrás** lehetőséget:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="CDN-végpont" border="true":::

4. A többszörös származás engedélyezéséhez szükség van legalább egy származási csoportra. Válassza ki a **Forrás létrehozása csoportot**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Forrás beállításai" border="true":::

5. A **forrás csoport hozzáadása** konfiguráció területen adja meg vagy válassza ki a következő adatokat:

   | Beállítás           | Érték                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Forrás csoport neve | Adja meg a forrás csoport nevét.                                   |
   | Mintavételi állapot      | Válassza az **Engedélyezve** lehetőséget. </br> A Azure CDN a világ különböző pontjain található állapot-mintavételeket fogja futtatni a forrás állapotának meghatározásához. Ne engedélyezze, ha az aktuális Origó csoport nem aktív a további díjak elkerülése érdekében.
   | Mintavételi útvonal        | Az állapot meghatározásához használt forrás elérési útja. |
   | Mintavételi időköz    | Válasszon egy 1, 2 vagy 4 perces mintavételi időközt.                        |
   | Mintavételi protokoll    | Válassza a **http** vagy a **https** lehetőséget.                                         |
   | Mintavételi módszer      | Válassza a **fej** vagy a **beolvasás** lehetőséget.                                           |
   | Alapértelmezett Origó | Válassza ki az alapértelmezett origóként beállítani kívánt mezőt.
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Forrás csoport hozzáadása" border="true":::

6. Válassza a **Hozzáadás** lehetőséget.

## <a name="add-multiple-origins"></a>Több eredet hozzáadása

1. A végpont forrás beállításainál válassza a **+ Forrás létrehozása** elemet:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Forrás létrehozása" border="true":::

2. Adja meg vagy válassza ki az alábbi adatokat a **forrás hozzáadása** konfigurációban:

   | Beállítás           | Érték                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Név        | Adja meg a forrás nevét.        |
   | Forrás típusa | Válassza a **Storage**, a **Cloud Service**, a **Web App** vagy az **Egyéni forrás** lehetőséget.                                   |
   | Forrás gazdaneve        | Válassza ki vagy adja meg a forrás állomásnevét.  A legördülő lista felsorolja az előző beállításban megadott típusú összes rendelkezésre álló forrást. Ha az **Egyéni forrás** lehetőséget választotta a forrás típusaként, adja meg az ügyfél-forráskiszolgáló tartományát. |
   | Forrás állomásfejléce    | Adja meg az egyes kérésekhez küldendő Azure CDN, vagy hagyja meg az alapértelmezett értéket.                        |
   | HTTP-port   | Adja meg a HTTP-portot.                                         |
   | HTTPS-port     | Adja meg a HTTPS-portot.                                           |
   | Prioritás    | Adjon meg egy 1 és 5 közötti számot.       |
   | Tömeg      | 1 és 1000 közötti számot adjon meg.   |

    > [!NOTE]
    > Ha egy forrást egy Origin csoporton belül hoznak létre, azt prioritásnak és súlyozásnak kell megfelelnie. Ha egy származási csoportnak csak egy forrása van, akkor az alapértelmezett prioritás és a súlyozás értéke 1. Ha a forrás kifogástalan, a rendszer átirányítja a forgalmat a legmagasabb prioritási állapotba. Ha a forrás megállapítása nem kifogástalan állapotú, akkor a kapcsolatok a prioritás sorrendjében egy másik forráshoz lesznek kiválasztva. Ha két eredet azonos prioritással rendelkezik, akkor a forgalom elosztása a forráshoz megadott súlyozás szerint történik. 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="További forrás hozzáadása" border="true":::

3. Válassza a **Hozzáadás** lehetőséget.

4. Válassza a **forrás beállítása** lehetőséget a forrás elérési útjának az összes eredethez beállításához:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Forrás elérési útjának konfigurálása" border="true":::

5. Válassza az **OK** lehetőséget.

## <a name="configure-origins-and-origin-group-settings"></a>Az Origins és a származási csoport beállításainak konfigurálása

Ha több eredete és a származási csoport is van, hozzáadhat vagy eltávolíthat különböző csoportokba tartozó eredeteket. Az azonos csoportba tartozó származásoknak hasonló számítási feladatokat kell kiszolgálni. A forgalmat a rendszer a megfelelő állapot, a prioritás és a súlyozási érték alapján osztja szét ezekbe az eredetbe. 

1. Az Azure CDN végpont forrás beállításainál válassza ki a konfigurálni kívánt származási csoport nevét:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Az Origins és a származási csoport beállításainak konfigurálása" border="true":::

2. A **frissítés forrása csoportban** válassza a **+ forrás kiválasztása** lehetőséget:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Frissítési forrás csoport" border="true":::

4. Válassza ki a csoportba felvenni kívánt forrást a legördülő mezőben, majd kattintson az **OK gombra**.

5. Győződjön meg arról, hogy a forrás hozzá lett adva a csoporthoz, majd válassza a **Mentés** lehetőséget:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="A csoportba felvett további forrás ellenőrzése" border="true":::

## <a name="remove-origin-from-origin-group"></a>Forrás eltávolítása a forrás csoportjából

1. Az Azure CDN végpont forrás beállításainál válassza ki a forrás csoport nevét:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Forrás eltávolítása a csoportból" border="true":::

2. Ha el szeretne távolítani egy forrást a forrás csoportból, jelölje be a forrás melletti Kuka ikont, majd válassza a **Mentés** lehetőséget:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="A Origin csoport törlési forrásának frissítése" border="true":::

## <a name="override-origin-group-with-rules-engine"></a>A forrás csoport felülbírálása a szabályok motorral

A Standard Rules Engine használatával testreszabhatja, hogy a rendszer hogyan ossza el a forgalmat a különböző Origin csoportokba.

A forgalom elosztása egy másik csoportba a kérelem URL-címe alapján.

1. A CDN-végponton válassza a **szabályok motor** elemet a **Beállítások** területen:

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-12.png" alt-text="Szabályok motorja" border="true":::

2. Válassza a **+ szabály hozzáadása** elemet.

3. Adja meg a szabály nevét a **névben**.

4. Válassza a **+ feltétel**, majd az **URL-cím elérési útja** lehetőséget.

5. A **kezelő** legördülő menüben válassza a **tartalmaz** elemet.

6. Az **érték** mezőben adja meg a **/images**.

7. Válassza a **+ művelet hozzáadása**, majd a **Origó felülbírálás** lehetőséget.

8. A **forrás csoportban** válassza ki a forrás csoportot a legördülő listából.

:::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-13.png" alt-text="Szabályok motorjának feltételei" border="true":::

Az összes bejövő kérelem esetén, ha az URL-cím **/images** tartalmaz, akkor a kérés a művelet szakaszban **(myorigingroup)** lesz hozzárendelve a forrás csoportjához. 

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben engedélyezte Azure CDN Endpoint multi-Origin használatát.

A Azure CDNről és a jelen cikkben említett egyéb Azure-szolgáltatásokról további információt a következő témakörben talál:

* [Azure CDN](./cdn-overview.md)
* [Azure CDN termék funkcióinak összehasonlítása](./cdn-features.md)
