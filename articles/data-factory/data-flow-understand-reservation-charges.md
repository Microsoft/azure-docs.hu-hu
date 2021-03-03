---
title: Azure Data Factory adatforgalom foglalási kedvezményének ismertetése | Microsoft Docs
description: Megtudhatja, hogyan alkalmazza a foglalási kedvezményt az ADF-adatfolyamatok futtatására. A rendszer óránként alkalmazza a kedvezményt ezekre az adatforgalomra.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 12b640fd97f48e293320593b33ab2fdc54980c0f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716295"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Foglalási kedvezmény alkalmazása Azure Data Factory adatforgalomra

Miután megvásárolta az ADF-adatfolyam számára fenntartott kapacitást, a foglalási kedvezményt a rendszer automatikusan alkalmazza az adatforgalomra egy olyan Azure Integration Runtime használatával, amely megfelel a kiszámítási típusnak és a foglalás alapvető számának.

## <a name="how-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

A foglalási kedvezmény csak akkor érvényes, ha *folyamatosan igénybe veszi*. Ha tehát nem rendelkezik a megfelelő Azure-integrációs erőforrásokkal, akkor az adott órára vonatkozó foglalási mennyiség elvész. A lefoglalt, de fel nem használt órák nem vihetők tovább.

Ha az integrációs modult az adatforgalomhoz használja, a foglalási kedvezmény automatikusan a megadott hatókör egy másik megfelelő erőforrására vonatkozik. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák *elvesznek*.

## <a name="discount-applied-to-adf-data-flows"></a>Az ADF-adatforgalomra alkalmazott kedvezmény

Az ADF-adatfolyam számára fenntartott kapacitás-kedvezményt az integrációs modulok óránkénti végrehajtásához kell alkalmazni. A megvásárolt foglalást az Adatáramlások integrációs moduljában használt számítási típushoz kell egyeztetni. A teljes órát nem futtató adatforgalom esetén a foglalás automatikusan a foglalási attribútumoknak megfelelő más adatfolyamatokra lesz alkalmazva. A kedvezményt a párhuzamosan futó adatfolyamatokra is lehet alkalmazni. Ha nem rendelkezik olyan adatfolyamatokkal, amelyek a foglalási attribútumoknak megfelelő teljes órán futnak, a foglalási kedvezmény teljes előnyeit nem érheti el az adott órában.

Az alábbi példák azt mutatják be, hogy az ADF-adatfolyam fenntartott kapacitásának kedvezménye a megvásárolt magok számától és a futásuk helyétől függ.

- 1. eset: egy ADF-adatáramlási foglalást vásárol, amely 1 órára 80 magot igényel a memória optimalizált számítási feladatokhoz, ha a 80 értéket adja meg a memória típusú optimalizált számítási típus mennyiségének megfelelően. Az Azure Integration Runtime egy órára optimalizált, 144 maggal rendelkező adatfolyamot futtat. Az 64 mag adatáramlási díjait egy órára számítjuk fel. A foglalási kedvezményt egy órányi, 80-nél több memóriára optimalizált használattal érheti el.
- 2. forgatókönyv: az általános célú számításhoz a 32 értéket az általános célú számítási típushoz tartozó mennyiségű, 1 órás 32-es mag-os adatforgalom foglalásával vásárolhatja meg. Az adatfolyamatokat 1 órára, az általános számítási Azure Integration Runtime 32-es magjainak használatával hibakeresést végez. A foglalási kedvezményt a teljes órányi használatért veheti igénybe.

Az Azure Reservations számlázási használati jelentésekben történő alkalmazásának megismeréséhez és megtekintéséhez lásd [az Azure Reservations használatát ismertető](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) cikket.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Reservationsről, tekintse meg a következő cikket:

- [Mi az az Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)