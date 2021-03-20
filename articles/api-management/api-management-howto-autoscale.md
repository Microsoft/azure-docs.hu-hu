---
title: Azure API Management-példányok méretezésének konfigurálása | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan állíthatja be az Azure API Management-példányok autoskálázási viselkedését.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 41a97c639d9148f2bff3e4f530363ea05a65a7b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018247"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Az Azure API Management-példány automatikus skálázása  

Az Azure API Management Service-példányok automatikusan méretezhetők egy adott szabálykészlet alapján. Ez a viselkedés engedélyezhető és konfigurálható Azure Monitoron keresztül, és csak az Azure API Management szolgáltatás **standard** és **prémium** szintjein támogatott.

A cikk végigvezeti az automatikus méretezés konfigurálásának folyamatán, és az automatikus skálázási szabályok optimális konfigurálását javasolja.

> [!NOTE]
> API Management **szolgáltatás a használati** szinten automatikusan a forgalomtól függ, és nincs szükség további konfigurálásra.

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek követéséhez a következőket kell tennie:

+ Aktív Azure-előfizetés
+ Azure API Management-példánnyal rendelkezik. További információ: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Ismerje meg az [Azure API Management-példány kapacitásának](api-management-capacity.md)koncepcióját.
+ [Az Azure API Management-példányok manuális skálázási folyamatának](upgrade-and-scale.md)megismerése, beleértve a költségeket.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Az Azure API Management az autoskálázás korlátozásai

Az automatikus skálázási viselkedés konfigurálása előtt figyelembe kell venni bizonyos korlátozásokat és a skálázási döntések következményeit.

+ Az autoscale csak az Azure API Management szolgáltatás **standard** és **prémium** szintjein engedélyezhető.
+ Az árképzési szintek meghatározzák a szolgáltatási példányok maximális számát is.
+ A skálázási folyamat legalább 20 percet vesz igénybe.
+ Ha a szolgáltatást egy másik művelet zárolja, a skálázási kérelem sikertelen lesz, és automatikusan újrapróbálkozik.
+ Többrégiós környezetekben üzemelő szolgáltatások esetében csak az **elsődleges helyen** lévő egységek méretezhetők. A más helyen lévő egységek nem méretezhetők.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Az Azure API Management szolgáltatáshoz tartozó autoskálázás engedélyezése és konfigurálása

Az alábbi lépéseket követve konfigurálhatja az Azure API Management szolgáltatáshoz tartozó autoskálázást:

1. Navigáljon a Azure Portal **figyelő** példányához.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. A bal oldali menüben válassza az **autoskálázás** lehetőséget.

    ![Erőforrás-méretezés Azure Monitor](media/api-management-howto-autoscale/02.png)

3. Keresse meg az Azure API Management szolgáltatást a legördülő menük szűrői alapján.
4. Válassza ki a kívánt Azure API Management Service-példányt.
5. Az újonnan megnyitott szakaszban kattintson az **autoskálázás engedélyezése** gombra.

    ![Azure Monitor az autoskálázás engedélyezése](media/api-management-howto-autoscale/03.png)

6. A **szabályok** szakaszban kattintson a **+ szabály hozzáadása** lehetőségre.

    ![Új szabály Azure Monitor](media/api-management-howto-autoscale/04.png)

7. Új kibővítő szabály definiálása.

   Egy Felskálázási szabály például kiválthatja egy Azure API Management egység hozzáadását, ha az elmúlt 30 percben az átlagos kapacitási metrika meghaladja a 80%-ot. Az alábbi táblázat egy ilyen szabály konfigurációját tartalmazza.

    | Paraméter             | Érték             | Jegyzetek                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Metrikaforrás         | Aktuális erőforrás  | Határozza meg a szabályt az aktuális Azure API Management erőforrás-metrikák alapján.                                                                                                                                                                                                     |
    | *Feltételek*            |                   |                                                                                                                                                                                                                                                                                 |
    | Idő összesítése      | Átlag           |                                                                                                                                                                                                                                                                                 |
    | Metrika neve           | Kapacitás          | A kapacitás mérőszáma egy Azure API Management metrika, amely az Azure API Management-példány erőforrásainak használatát tükrözi.                                                                                                                                                            |
    | Időfelbontási szint statisztikája  | Átlag           |                                                                                                                                                                                                                                                                                 |
    | Művelet              | Nagyobb, mint      |                                                                                                                                                                                                                                                                                 |
    | Küszöbérték             | 80%               | Az átlagos kapacitási metrika küszöbértéke.                                                                                                                                                                                                                                 |
    | Időtartam (perc) | 30                | A TimeSpan átlagosan a kapacitás mérőszáma a használati mintákra jellemző. Minél hosszabb az időszak, annál simább lesz a reakció – az időszakos tüskék kevesebb hatással lesznek a kibővíthető döntésre. Ugyanakkor a kibővíthető triggert is késleltetni fogja. |
    | *Művelet*              |                   |                                                                                                                                                                                                                                                                                 |
    | Művelet             | Szám növelése a következővel |                                                                                                                                                                                                                                                                                 |
    | Példányok száma        | 1                 | Az Azure API Management-példány felskálázása 1 egységgel.                                                                                                                                                                                                                          |
    | Lehűlés (perc)   | 60                | Az Azure API Management szolgáltatás méretezése legalább 20 percet vesz igénybe. A legtöbb esetben a 60 perces lassú elérési idő megakadályozza, hogy a Kibővítés számos felskálázást indítson el.                                                                                                  |

8. A szabály mentéséhez kattintson a **Hozzáadás** gombra.

    ![Felskálázási szabály Azure Monitor](media/api-management-howto-autoscale/05.png)

9. Kattintson ismét a **+ szabály hozzáadása** lehetőségre.

    Ezúttal meg kell határozni egy méretezési szabályt. Gondoskodik arról, hogy a rendszer ne pazarolja az erőforrásokat, amikor az API-k használata csökken.

10. Új skála definiálása a szabályban.

    A szabály skálázása például kiválthatja egy Azure API Management egység eltávolítását, ha az elmúlt 30 percben az átlagos kapacitási metrika 35%-nál kisebb. Az alábbi táblázat egy ilyen szabály konfigurációját tartalmazza.

    | Paraméter             | Érték             | Jegyzetek                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Metrikaforrás         | Aktuális erőforrás  | Határozza meg a szabályt az aktuális Azure API Management erőforrás-metrikák alapján.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Feltételek*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Idő összesítése      | Átlag           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Metrika neve           | Kapacitás          | A kibővítő szabályhoz használt metrika.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Időfelbontási szint statisztikája  | Átlag           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Művelet              | Kevesebb, mint         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Küszöbérték             | 35%               | A Felskálázási szabályhoz hasonlóan ez az érték nagy mértékben függ az Azure-API Management használati szokásaitól. |
    | Időtartam (perc) | 30                | A kibővítő szabályhoz használt érték megegyezik.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Művelet*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Művelet             | Szám csökkentése a következővel | Ezzel szemben a kibővítő szabályhoz használttal.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Példányok száma        | 1                 | A kibővítő szabályhoz használt érték megegyezik.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Lehűlés (perc)   | 90                | A skálázásnak szigorúbbnak kell lennie, mint a felskálázás, így a lassú elérési idő hosszabbnak kell lennie.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. A szabály mentéséhez kattintson a **Hozzáadás** gombra.

    ![Azure Monitor skálázás a szabályban](media/api-management-howto-autoscale/06.png)

12. Az Azure API Management-egységek **maximális** számának beállítása.

    > [!NOTE]
    > Az Azure API Management egy példányra vonatkozó korláttal rendelkezhet. A korlát a szolgáltatási szinttől függ.

    ![Képernyőkép, amely kiemeli, hogy hol állítható be az Azure API Management-egységek maximális száma.](media/api-management-howto-autoscale/07.png)

13. Kattintson a **Mentés** gombra. Az autoskálázás konfigurálva van.

## <a name="next-steps"></a>Következő lépések

- [Azure API Management-szolgáltatáspéldány üzembe helyezése több Azure-régióban](api-management-howto-deploy-multi-region.md)
- [Optimalizálja és mentse a Felhőbeli kiadásait](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)