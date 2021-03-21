---
title: A standard Azure CDNban a HTTPS kikényszeríthető szabályokkal rendelkező motor használatával | Microsoft Docs
description: A Microsoft standard Azure Content Delivery Network (Azure CDN) szabályainak motorja segítségével testre Azure CDN szabhatja a HTTP-kérések kezelését, például bizonyos tartalomtípusok kézbesítésének blokkolását, a gyorsítótárazási házirend meghatározását és a HTTP-fejlécek módosítását. Ebből a cikkből megtudhatja, hogyan hozhat létre egy szabályt a felhasználók HTTPS-re való átirányításához.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b94798580b2d4ba746c152486c0de753cf64d2c9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564900"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>A Standard Rules Engine beállítása Azure CDN

Ez a cikk bemutatja, hogyan állíthatja be és használhatja az Azure Content Delivery Network (Azure CDN) szabványos szabályok motorját.

## <a name="standard-rules-engine"></a>Standard szintű szabályok motorja

A Standard Rules Engine for Azure CDN a HTTP-kérések kezelésének testreszabására használható. Például a szabályok motor használatával kényszerítheti ki a tartalmak kézbesítését adott protokollok használatára, a gyorsítótárazási házirend meghatározására, vagy egy HTTP-fejléc módosítására. Ez a cikk bemutatja, hogyan hozhat létre olyan szabályt, amely automatikusan átirányítja a felhasználókat a HTTPS-re. 

> [!NOTE]
> A jelen cikkben ismertetett szabályok motorja csak a Microsoft szabványos Azure CDN számára érhető el. 

## <a name="redirect-users-to-https"></a>Felhasználók átirányítása HTTPS-re

1. A Microsoft-profilokban lépjen az Azure Content Delivery Networkra.

1. A **CDN-profil** lapon válassza ki azt a végpontot, amelyhez szabályokat kíván létrehozni.
  
1. Válassza a **szabályok motor** fület.
   
    Megnyílik a **szabályok motor** panel, és megjeleníti az elérhető globális szabályok listáját. 
   
    [![Azure CDN új szabályok lap](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > A különböző szabályok listájának sorrendje befolyásolja a szabályok kezelését. Előfordulhat, hogy egy szabályban megadott műveleteket egy későbbi szabály felülírja.
   >

1. Válassza a **szabály hozzáadása** lehetőséget, és írja be a szabály nevét. A szabályok nevének betűvel kell kezdődnie, és csak számokból és betűkből állhat.

1. A szabály által érintett kérelmek típusának megállapításához hozzon létre egy egyeztetési feltételt:
    1. Válassza a **feltétel hozzáadása** lehetőséget, majd válassza a **kérelem protokolljának** egyeztetése feltételt.
    1. A **Operátor** területen válassza az **Egyenlő** lehetőséget.
    1. Az **érték** mezőben válassza a **http** lehetőséget.
   
   [![Azure CDN szabály egyeztetési feltétele](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > A **feltétel hozzáadása** legördülő listában több egyezési feltétel közül választhat. Az egyeztetési feltételek részletes listáját lásd: [feltételek egyeztetése a standard szabályok motorban](cdn-standard-rules-engine-match-conditions.md).
   
1. Válassza ki az egyeztetési feltételnek megfelelő kérelmekre alkalmazni kívánt műveletet:
   1. Válassza a **művelet hozzáadása** lehetőséget, majd az **URL-átirányítás** elemet.
   1. A **Típus mezőben** válassza a **Found (302)** elemet.
   1. A **protokoll** területen válassza a **https** lehetőséget.
   1. A bejövő értékek használatához hagyja üresen az összes többi mezőt.
   
   [![Azure CDN szabály művelete](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > A **művelet hozzáadása** legördülő listában több művelet közül választhat. A műveletek részletes listáját lásd: [műveletek a standard szabályok motorban](cdn-standard-rules-engine-actions.md).

6. Az új szabály mentéséhez válassza a **Mentés** lehetőséget. A szabály most már használható.
   
   > [!IMPORTANT]
   > A szabály módosításai akár 15 percet is igénybe vehetnek Azure CDN.
   >
   

## <a name="next-steps"></a>Következő lépések

- [Azure CDN áttekintése](cdn-overview.md)
- [A Standard szabálymotor referenciája](cdn-standard-rules-engine-reference.md)
- [Egyeztetési feltételek a standard szabályok motorban](cdn-standard-rules-engine-match-conditions.md)
- [Műveletek a standard szabályok motorban](cdn-standard-rules-engine-actions.md)
