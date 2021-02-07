---
title: Számítási költségek mentése fenntartott kapacitással
description: Megtudhatja, hogyan vásárolhat Azure Data Factory adatfolyamok számára fenntartott kapacitást a számítási költségek mentéséhez.
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 02/05/2021
ms.openlocfilehash: 86ebbb46e8f3b5bb34dfe5789a17a2d63526f65c
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808320"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>A fenntartott kapacitással rendelkező erőforrások költségeinek megtakarítása – Azure Data Factory adatforgalom

Az utólagos elszámolású díjszabáshoz képest a számítási erőforrások foglalásával megtakaríthat pénzt Azure Data Factory adatáramlási költségekkel. A fenntartott kapacitással egy-egy vagy három éves időszakra vonatkozó, a számítási költségek jelentős kedvezményét biztosító, az ADF-adatforgalom használatára vonatkozó kötelezettségvállalást tesz. A fenntartott kapacitás megvásárlásához meg kell adnia az Azure-régiót, a számítási típust, a mag darabszámának mennyiségét és a kifejezést.

A foglalást nem kell hozzárendelni egy adott gyári vagy integrációs futtatókörnyezethez. A meglévő gyárak vagy újonnan üzembe helyezett üzemek automatikusan megkapják a kedvezményt. A foglalás megvásárlásával véglegesíti az adatáramlás számítási költségeinek egy vagy három évre történő felhasználását. A foglalás megvásárlása után a foglalási attribútumoknak megfelelő számítási díjakra már nem számítunk fel díjat az utólagos elszámolású díjszabásban. 

A [fenntartott kapacitást](https://portal.azure.com) a foglalások [elöl vagy havi fizetéssel](https://docs.microsoft.com/azure/cost-management-billing/reservations/prepare-buy-reservation.md)is megvásárolhatja. Fenntartott kapacitás vásárlása:

- Legalább egy Nagyvállalati vagy egyéni előfizetéshez tulajdonosi szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.
- Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com). Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie. Fenntartott kapacitás.

Ha többet szeretne megtudni arról, hogy a vállalati ügyfelek és az utólagos elszámolású ügyfelek milyen díjat számítanak fel a foglalások beszerzésére, tekintse meg az [Azure foglalás használatának ismertetése a nagyvállalati beléptetéssel](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) és [Az Azure foglalás használatának](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage)ismertetése az utólagos elszámolású előfizetéshez.

> [!NOTE]
> A fenntartott kapacitás megvásárlása nem foglalja le előre a használatra vonatkozó infrastruktúra-erőforrásokat (virtuális gépeket vagy fürtöket).

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>A vásárlás előtt szükséges helyes Azure IR-méretek meghatározása

A foglalás méretének a meglévő vagy hamarosan üzembe helyezett Adatáramlások által a számítási réteg használatával felhasznált teljes számítási mennyiség alapján kell alapulnia.

Tegyük fel például, hogy a folyamatot óránként, a 32 maggal optimalizált memóriával hajtja végre. Továbbá Tételezzük fel, hogy a következő hónapban szeretne üzembe helyezni egy olyan folyamatot, amely általános célú 64 magot használ. Azt is tegyük fel, hogy tudnia kell, hogy legalább 1 évig szüksége lesz ezekre az erőforrásokra. Ebben az esetben adja meg az egyes számítási típusokhoz szükséges magok számát 1 órára. Az Azure Portalon keresse meg a foglalások kifejezést. Válassza a Data Factory > adatfolyamatok lehetőséget, majd a 32 értéket adja meg az optimalizált memória és a 64 általános célú tárolásához.

## <a name="buy-reserved-capacity"></a>Fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget, majd a **foglalások** ablaktáblán válassza az **ADF-adatforgalom** lehetőséget, ha új foglalást szeretne vásárolni az ADF-adatforgalomhoz.
4. Adja meg a kötelező mezőket és attribútumokat, amelyeknek a minősítését a fenntartott kapacitás kedvezményének beszerzéséhez. A kedvezményt megkapó adatfolyamatok tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.
5. Tekintse át a kapacitás foglalásának költségeit a costs ( **költségek** ) szakaszban.
6. Válassza a **Beszerzés** lehetőséget.
7. Válassza a **foglalás megtekintése** lehetőséget a vásárlás állapotának megtekintéséhez.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Az Azure Reservations-kedvezmény ismertetése](data-flow-understand-reservation-charges.md)
