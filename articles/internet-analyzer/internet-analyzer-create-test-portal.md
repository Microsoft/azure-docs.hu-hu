---
title: Internet Analyzer-teszt létrehozása a portál használatával | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhatja létre az első Internet Analyzer-tesztet.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "73501770"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Internet Analyzer-teszt létrehozása a Portal használatával (előzetes verzió)

Az Internet Analyzer erőforrásait kétféleképpen lehet létrehozni – a Azure Portal vagy a [parancssori](internet-analyzer-cli.md)felület használatával. Ez a szakasz segítséget nyújt egy új Azure Internet Analyzer-erőforrás létrehozásához a portál felhasználói felületének használatával.

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

A nyilvános előzetes verzió globálisan használható; az adattárolás azonban az *USA 2. nyugati* régiójára korlátozódik az előzetes verzióban.

## <a name="basics"></a>Alapvető beállítások

1. Az Internet Analyzer előnézeti hozzáférésének beszerzése a **hogyan az előzetes** verzióban való részvétel után? az [Azure Internet Analyzer – gyakori kérdések](internet-analyzer-faq.md)című témakörben olvashat.
2. A [Azure Portal](https://preview.portal.azure.com)kezdőlapján kattintson az **+ erőforrás létrehozása** elemre. Az Internet Analyzer jelenleg csak a Azure Portal előzetes verziójában érhető el.
3. Az **új** lapon keressen rá az "Internet Analyzer" kifejezésre a *Keresés a piactéren* mezőben.
4. Kattintson az **Internet Analyzer (előzetes verzió)** elemre. Győződjön meg arról, hogy a közzétevő a *Microsoft* , a kategória pedig *hálózatkezelés*.
5. Az **Internet Analyzer (előzetes verzió)** lapon kattintson a **Létrehozás** gombra az **Internet Analyzer létrehozása** lap megnyitásához.
6. Adja meg az alábbi konfigurációs beállításokat az Internet Analyzer-erőforráshoz:

    * **Előfizetés:** Az Azure-előfizetés, amely az új Internet Analyzer-erőforrást üzemelteti. **_Használja ugyanazt az előfizetés-azonosítót, amelyet az előzetes verzióhoz való hozzáférés kérelmezéséhez használ._**
    * **Erőforráscsoport:** Az az Azure-erőforráscsoport, amelyet az új Internet Analyzer-erőforrás fog létrehozni a alkalmazásban. Ha nem rendelkezik meglévő erőforráscsoporthoz, létrehozhat egy újat.
    * **Név:** Az új Internet Analyzer erőforrás-profil neve.
    * **Régió:** Az Azure nyilvános régiója, amelyben az erőforrás létrejön. Az előzetes verzióban csak az *USA 2. nyugati* régiója érhető el.

7. Ha végzett a Profilbeállítások megadásával, kattintson a **felülvizsgálat + létrehozás** gombra.

## <a name="configuration"></a>Konfiguráció

Az alapszintű lépések végrehajtásának előfeltétele egy teszt konfigurálása és a JavaScript-ügyfél beágyazása. Miután létrehozott egy profilt, lépjen a **beállítások > konfiguráció** elemre az első teszt konfigurálásához.

1. Adja meg a teszt nevét a **test Name (teszt neve** ) mezőben.
2. Adja meg a teszt leírását a **Leírás** mezőben.
3. Kattintson a **végpont konfigurálása** – egy lap jelenik meg a jobb oldalon. Válassza ki a konfigurálni kívánt végpont típusát – egyetlen Azure-régiót, több Azure-régiót vagy egyéni végpontot.

    >
    ***Előre konfigurált végpontok: egyetlen és több Azure-régió kombinációja***
    * Válasszon ki egy régiót vagy régiót az [előre konfigurált Azure-végpontok listájáról](internet-analyzer-faq.md).
    * Ezután válassza ki az alkalmazás vagy a Content Delivery architektúra kiértékelésének típusát.
        * Egyetlen Azure-régió: site Acceleration ([Azure bejárati ajtó](https://azure.microsoft.com/services/frontdoor/)), statikus tartalom gyorsítótárazása ([Azure CDN a Microsoft számára](https://azure.microsoft.com/services/cdn/)), vagy egyik sem.
        * Több Azure-régió: site Acceleration ([Azure bejárati ajtó](https://azure.microsoft.com/services/frontdoor/)), DNS-irányítás ([Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/))  

    ***Egyéni végpontok***
    * Kövesse az [Egyéni végpont létrehozása](internet-analyzer-custom-endpoint.md) oldalon található utasításokat.
    * Illessze be az egy képpontos rendszerkép HTTPS URL-címét a portálon.
    >

4. A **Hozzáadás** gombra kattintva vegye fel a végpontot a tesztbe.
5. Ismételje meg a 1-4 lépést a második végpont konfigurálásához. A B végpontot mindig az A végponthoz viszonyítva mérjük – A végpontok konfigurálásakor gondolja át, melyik végpont legyen a tesztelési vezérlő.
6. A teszt mentéséhez kattintson a **Save (Mentés** ) gombra. A teszt mentése után már nem szerkesztheti egy adott teszt végpontjait.
7. Válassza ki az elindítani kívánt teszt (eke) t, majd kattintson a **teszt indítása** lehetőségre. Ez a ***teszt (ek) és a (z) _*_futtatásának_ állapotát** fogja megváltoztatni. A tesztek bármikor elindíthatók, de a JavaScript-ügyfelet be kell ágyazni a tesztbe a mérések gyűjtésének megkezdéséhez.
8. Bármikor hozzáadhat további teszteket. Vegye figyelembe, hogy az egyedi JavaScript-ügyfél nem jön létre, amíg egy teszt létre nem jön.

## <a name="embed-client"></a>Ügyfél beágyazása

A tesztek elindításához a JavaScript-ügyfelet be kell ágyazni a webalkalmazásba. Legalább egy teszt konfigurálását követően kattintson a **felülvizsgálat + létrehozás** gombra, lépjen a **Beállítások > konfiguráció** elemre, és másolja a JavaScript-ügyfelet. A konkrét utasítások az [Internet Analyzer-ügyfél beágyazása](internet-analyzer-embed-client.md) oldalon találhatók.  

## <a name="next-steps"></a>Következő lépések

* Az [Internet Analyzer gyakori kérdéseinek](internet-analyzer-faq.md) beolvasása
* További információ az [Internet Analyzer-ügyfél](internet-analyzer-embed-client.md) beágyazásáról és az [Egyéni végpontok](internet-analyzer-custom-endpoint.md)létrehozásáról.
