---
title: Válassza ki a megfelelő díjszabási szintet Microsoft Azure térképekhez
description: További információ a Azure Maps díjszabási szintjeiről. Itt megtekintheti, hogy mely funkciók érhetők el, és hogyan tekintheti meg az árképzési szintek kiválasztásának legfontosabb szempontjait.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b5157716058833e2cd9ae8c9ce7b1eb36bb0f82f
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256054"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Válassza ki a megfelelő díjszabási szintet Azure Maps

A Azure Maps két díjszabási szintet kínál: S0 és S1. Ennek a cikknek a célja, hogy segítséget nyújtson az igényeinek megfelelő díjszabási csomag kiválasztásában. A megfelelő árképzési szint kiválasztásához Kérdezzen rá a következő két kérdésre.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Hány egyidejű felhasználót tervezek támogatni?

A S0 és S1 díjszabási szintjei különböző mennyiségű adatátviteli sebességet kezelnek. A S0 díjszabási szintje másodpercenként legfeljebb **50 lekérdezést** kezel. Míg az S1-es **szinten másodpercenként több mint 50 lekérdezés** kezelhető.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Milyen térinformatikai funkciókat kívánok használni?

Ha az alapszintű Térinformatikai API-k megfelelnek a szolgáltatás követelményeinek, válassza a S0 díjszabási szintjét. Ha fejlettebb funkciókat szeretne használni az alkalmazáshoz, érdemes lehet az S1 árképzési szintet választania. A speciális képességek többek között a következők: antenna és hibrid képek, az útválasztási tartomány és a Batch-helymeghatározáshoz. Az alkalmazáshoz leginkább megfelelő árképzési csomag kiválasztásához tekintse át az alábbi **díjszabási szintű képességek** táblázatát:

### <a name="pricing-tier-capabilities"></a>Díjszabási csomag képességei

| Képesség                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Leképezés megjelenítése                              | ✓                   | ✓       |
| Szatellit képek                       |                     | ✓        |
| Keresés                                  | ✓                    | ✓        |
| Batch-keresés                            |                     | ✓        |
| Útvonal                                   | ✓                    |✓        |
| Kötegelt Útválasztás                            |                    | ✓        |
| Mátrix-Útválasztás                          |                     | ✓        |
| Útvonal tartománya (izokrón)                |                     | ✓        |
| Adatforgalom                                |✓                    |✓        |
| Időzóna                               |✓                    |✓        |
| Földrajzi hely (előzetes verzió)                    |✓                   |✓        |
| Térbeli műveletek                        |                    |✓        |
| Geokerítések kezelése                                |                    |✓        |
| AdatAzure Maps (előzetes verzió)                |                     | ✓        |
| Mobilitás (előzetes verzió)                       |                     | ✓        |
| Időjárás                       |✓                    |✓        |
|  Létrehozó (előzetes verzió)                         |                   |✓        |
|  Jogosultságszint-emelés (előzetes verzió)                        |                   |✓        |

Vegye figyelembe ezeket a további pontokat:

* Milyen típusú vállalattal rendelkezik?
* Mennyire kritikus az alkalmazása?

### <a name="pricing-tier-targeted-customers"></a>Árképzési szintek megcélzó ügyfelei

Tekintse meg a **díjszabási szinten megcélozott ügyfelek** táblázatát, hogy jobban érthető legyen a S0 és az S1 díjszabása. További információ: [Azure Maps díjszabása](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Tarifacsomag  |     Megcélzó ügyfelek                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    A S0 díjszabási szintje az éles környezetben futó alkalmazások számára is működik: a megvalósíthatósági fejlesztéstől és a korai teszteléstől az alkalmazások gyártásához és üzembe helyezéséhez. Ez a réteg azonban kisméretű fejlesztésre, vagy alacsony egyidejű felhasználókkal rendelkező ügyfelekre, vagy mindkettőre lett tervezve. 
| S1            |    Az S1 díjszabási szintje olyan ügyfelek számára készült, akik nagyméretű nagyvállalati alkalmazásokat, kritikus fontosságú alkalmazásokat vagy nagy mennyiségű egyidejű felhasználót foglalnak magukban. Azok az ügyfelek is, akik speciális térinformatikai szolgáltatásokat igényelnek.

## <a name="next-steps"></a>Következő lépések

További információ a díjszabási szintek megtekintéséről és módosításáról:

> [!div class="nextstepaction"]
> [Árképzési szintek kezelése](how-to-manage-pricing-tier.md)
