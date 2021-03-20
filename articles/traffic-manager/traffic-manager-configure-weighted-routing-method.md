---
title: 'Oktatóanyag: súlyozott Round-Robin forgalom útválasztásának konfigurálása az Azure Traffic Manager'
description: Ez az oktatóanyag azt ismerteti, hogyan lehet terheléselosztást használni a Traffic Manager egy ciklikus multiplexelés használatával.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: abcfce43b90c7371d5b38aa5b7a6d478e9d6a0dd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92207836"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Oktatóanyag: a súlyozott forgalom útválasztási módszerének konfigurálása Traffic Manager

A közös forgalom-útválasztási módszer minta az azonos végpontok készletének biztosítása, amely magában foglalja a Cloud Servicest és a webhelyeket, valamint a forgalmat egyaránt továbbítja. Az alábbi lépések azt ismertetik, hogyan konfigurálható az ilyen típusú forgalom-útválasztási módszer.

> [!NOTE]
> Az Azure-webalkalmazás már az Azure-régión belüli (több adatközpontot is tartalmazó) webhelyekhez biztosít ciklikusan részletes terheléselosztási funkciókat. Traffic Manager lehetővé teszi a forgalom terjesztését különböző adatközpontokban lévő webhelyek között.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - Hozzon létre egy Traffic Manager-profilt súlyozott útválasztással.
> - Használja a Traffic Manager profilt.
> - Traffic Manager profil törlése.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).

## <a name="configure-the-weighted-traffic-routing-method"></a>A súlyozott forgalom-útválasztási módszer konfigurálása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A portálon keresse meg az előző szakaszban létrehozott **Traffic Manager profil** nevét, és válassza a Traffic Manager-profil lehetőséget a megjelenített eredmények között.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/search-traffic-manager-weighted-profile.png" alt-text="Traffic Manager profil keresése":::

1. Válassza a **konfiguráció** lehetőséget, majd válassza ki vagy adja meg a következő beállításokat:

    | Beállítás         | Érték                                              |
    | ---             | ---                                                |
    | Útválasztási metódus            | Válassza a **súlyozott** elemet. |    
    | DNS élettartama (TTL) | Ez az érték azt határozza meg, hogy az ügyfél helyi gyorsítótárazási névkiszolgáló milyen gyakran kérdezi le a Traffic Manager rendszerét a frissített DNS-bejegyzésekhez. A Traffic Managertel kapcsolatos bármilyen változás, például a forgalom-útválasztási módszer változása vagy a hozzáadott végpontok rendelkezésre állása módosul, a DNS-kiszolgálók globális rendszerében ezt az időszakot fogja frissíteni. |
    | Protokoll    | Válasszon protokollt a végpontok figyeléséhez. *Beállítások: HTTP, HTTPS és TCP* |
    | Port | Itt adhatja meg a portszámot. |
    | Elérési út | A végpontok figyeléséhez meg kell adnia egy elérési utat és egy fájlnevet. A "/" perjel a relatív elérési útra érvényes bejegyzés, amely azt jelenti, hogy a fájl a gyökérkönyvtárban van (alapértelmezés). |
    | Egyéni fejléc beállításai | Konfigurálja az egyéni fejléceket a Format Host:contoso. com, newheader: NewValue. A támogatott párok maximális száma 8. A http és a HTTPS protokoll esetében alkalmazható. A profil összes végpontján érvényes |
    | Várt állapotkód-tartományok (alapértelmezett: 200) | Állítsa be az állapotkód tartományát a 200-299301-301 formátumban. A támogatott tartomány maximális értéke 8. A http és a HTTPS protokoll esetében alkalmazható. A profil összes végpontján érvényes |
    | Mintavételi időköz | Adja meg a végponti állapotú mintavételek közötti időintervallumot. 10 vagy 30 másodpercet is választhat. |
    | Hibák számának tolerálása | Beállíthatja, hogy a rendszer a végponti hiba elindítását megelőzően tolerálja az állapot-mintavételi hibák számát. 0 és 9 közötti számot is megadhat. | 
    | Mintavételi időkorlát | Állítsa be azt az időtartamot, ameddig a végponti állapot mintavétele időtúllépést nem igényel. Ennek az értéknek legalább 5-nek kell lennie, és kisebbnek kell lennie, mint a szondázás intervallum értéke. |

1. A konfiguráció befejezéséhez válassza a **Mentés** lehetőséget.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-configuration.png" alt-text="Traffic Manager súlyozott konfiguráció"::: 

1. Válassza ki a **végpontot** , és konfigurálja az egyes végpontok súlyozását. A súlyozás 1-1000 között lehet. Minél nagyobb a súly, annál magasabb a prioritás.  

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-configure-endpoints-weighted.png" alt-text="Traffic Manager súlyozott végpontok konfigurálása"::: 

## <a name="use-the-traffic-manager-profile"></a>A Traffic Manager profil használata

A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve. A nevet bármely ügyfél használhatja (például a böngésző használatával történő navigálással), hogy a jobb oldali végpontra irányítsa át az útválasztási típus alapján. Ebben az esetben az összes kérelem minden végpontot ciklikus multiplexelés módon irányít.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-overview.png" alt-text="Traffic Manager súlyozott áttekintés"::: 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a Traffic Manager profilra, keresse meg a profilt, és válassza a **Profil törlése** lehetőséget.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/delete-traffic-manager-weighted-profile.png" alt-text="Traffic Manager súlyozott profil törlése":::

## <a name="next-steps"></a>Következő lépések

A súlyozott útválasztási módszerekkel kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Súlyozott forgalom útválasztási módszere](traffic-manager-routing-methods.md#weighted)