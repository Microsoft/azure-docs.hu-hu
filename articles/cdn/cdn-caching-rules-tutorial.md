---
title: Oktatóanyag – Azure CDN-gyorsítótárazási szabályok beállítása | Microsoft Docs
description: Ez az oktatóanyag egy globális Azure CDN-gyorsítótárszabály és egy egyéni gyorsítótárszabály beállítását ismerteti.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: ae217352b83617600fd983a747d578f8f28e7ddd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779237"
---
# <a name="tutorial-set-azure-cdn-caching-rules"></a>Oktatóanyag: Azure CDN-gyorsítótárazási szabályok beállítása

> [!NOTE] 
> A gyorsítótárazási szabályok csak a **Verizon Azure CDN standard** csomagból és a Akamai-profilokból **Azure CDN standard** csomagból érhetők el. A **Microsoft** -profilokból Azure CDN a [standard szintű szabályok motorját](cdn-standard-rules-engine-reference.md) kell használnia a Verizon-profilokkal való **Azure CDN premiumhoz** , a **felügyeleti portálon** a hasonló funkciókhoz a [Verizon Premium Rules motort](./cdn-verizon-premium-rules-engine.md) kell használnia.
 

Ez az oktatóanyag leírja, hogyan használhatók az Azure Content Delivery Network (CDN) gyorsítótárazási szabályai az alapértelmezett gyorsítótár-lejárati viselkedés globális vagy egyéni feltételek megadásával, például URL-elérési úttal vagy fájlkiterjesztéssel történő módosítására. Az Azure CDN két gyorsítótárazási szabálytípust biztosít:
- Globális gyorsítótárazási szabályok: Beállíthat egy globális gyorsítótárazási szabályt mindegyik végponthoz a profiljában, amely a végpontra küldött összes kérelmet érinti. A globális gyorsítótárazási szabály felülbírálja az összes HTTP-gyorsítótárazási irányelv fejlécét, ha be van állítva.

- Egyéni gyorsítótárazási szabályok: Beállíthat egy vagy több globális gyorsítótárazási szabályt minden egyes végponthoz a profiljában. Az egyéni gyorsítótárazási szabályok meghatározott elérési utaknak és fájlkiterjesztéseknek felelnek meg, a feldolgozásuk sorrendben történik, és felülbírálják a globális gyorsítótárazási szabályt, ha az be van állítva. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - A gyorsítótárazási szabályok lap megnyitása.
> - Globális gyorsítótárazási szabály létrehozása.
> - Egyéni gyorsítótárazási szabály létrehozása.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először létre kell hoznia egy CDN-profilt, és legalább egy CDN-végpontot. További információk: [Gyors útmutató: Azure CDN-profil és -végpont létrehozása](cdn-create-new-endpoint.md)

## <a name="open-the-azure-cdn-caching-rules-page"></a>Az Azure CDN-gyorsítótárazási szabályok lap megnyitása

1. Az [Azure Portalon](https://portal.azure.com) válasszon egy CDN-profilt, majd egy végpontot.

2. A bal oldali ablaktáblán, a Beállítások alatt válassza a **Gyorsítótárszabályok** lehetőséget.

   ![CDN-gyorsítótárszabályok gomb](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Megjelenik a **Gyorsítótárszabályok** lap.

   ![CDN-gyorsítótárazási szabályok lap](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="set-global-caching-rules"></a>Globális gyorsítótárszabályok beállítása

Hozzon létre egy globális gyorsítótárszabályt a következők szerint:

1. A **Globális gyorsítótárszabályok** alatt a **Lekérdezési sztringek gyorsítótárazása** beállításaként adja meg a **Lekérdezési sztringek kihagyása** értéket.

2. Adja meg a **Gyorsítótárazás működése** beállításaként a **Beállítás hiányzó érték esetén** lehetőséget.
       
3. A **Gyorsítótár elévülési ideje** értékeként adja meg a 10 értéket a **Napok** mezőben.

    A globális gyorsítótárszabály érinti a végpontra küldött összes kérelmet. Ez a szabály eleget tesz az forrás szerinti gyorsítótárirányelv-fejléceknek, ha vannak (`Cache-Control` vagy `Expires`); egyébként, ha nincsenek meghatározva, a gyorsítótárhoz 10 napot állít be. 

    ![Globális gyorsítótárszabályok](./media/cdn-caching-rules/cdn-global-caching-rules.png)

## <a name="set-custom-caching-rules"></a>Egyéni gyorsítótárszabályok beállítása

Hozzon létre egy egyéni gyorsítótárszabályt a következők szerint:

1. Az **Egyéni gyorsítótárszabályok** területen adja meg az **Egyeztetési feltétel** beállításaként az **Elérési út** az **Egyeztetési érték** beállításaként pedig a `/images/*.jpg` lehetőséget.
    
2. Adja meg a **Gyorsítótárazás működése** beállításaként a **Felülbírálás** lehetőséget, és írja be, hogy 30 a **Napok** mezőbe.
       
    Ezzel az egyéni szabállyal 30 napos gyorsítótár-élettartamot állít be a végpont `/images` mappájának bármely `.jpg` képfájljához. Ezzel felülbírálja a forráskiszolgáló által küldött bármely `Cache-Control` vagy `Expires` HTTP-fejlécet.

    ![Egyéni gyorsítótárszabályok](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

    
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben gyorsítótárazási szabályokat hozott létre. Ha már nem szeretné használni ezeket a gyorsítótárazási szabályokat, a következő lépések követésével távolíthatja el őket:
 
1. Válassza ki a CDN-profilt, majd azt a végpontot, amely tartalmazza az eltávolítani kívánt gyorsítótárszabályokat.

2. A bal oldali ablaktáblán, a Beállítások alatt válassza a **Gyorsítótárszabályok** lehetőséget.

3. A **Globális gyorsítótárszabályok** területen adja meg a **Gyorsítótárazás működése** beállításaként a **Nincs beállítva** lehetőséget.
 
4. Az **Egyéni gyorsítótárszabályok** alatt jelölje be a törölni kívánt szabály melletti jelölőnégyzetet.

5. Válassza a **Törlés** elemet.

6. Válassza a lap tetején a **Mentés** lehetőséget.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - A gyorsítótárazási szabályok lap megnyitása.
> - Globális gyorsítótárazási szabály létrehozása.
> - Egyéni gyorsítótárazási szabály létrehozása.

Lépjen tovább a következő cikkre, ahol további gyorsítótárszabályok beállítását sajátíthatja el.

> [!div class="nextstepaction"]
> [Az Azure CDN gyorsítótárazási viselkedésének vezérlése gyorsítótárszabályokkal](cdn-caching-rules.md)