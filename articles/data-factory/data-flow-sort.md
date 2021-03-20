---
title: Átalakítás rendezése a leképezési adatfolyamban
description: Azure Data Factory leképezési adatrendezési átalakítás
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "81606324"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Átalakítás rendezése a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A rendezési transzformáció lehetővé teszi a bejövő sorok rendezését az aktuális adatfolyamon. Az egyes oszlopokat kiválaszthatja, és sorba rendezheti őket növekvő vagy csökkenő sorrendben.

> [!NOTE]
> Az adatforgalom leképezése olyan Spark-fürtökön történik, amelyek több csomóponton és partíción keresztül terjesztik az adatforgalmat. Ha úgy dönt, hogy egy későbbi átalakítás során újraparticionálja az adatait, a rendezést elveszítheti az adatkeverés miatt.

## <a name="configuration"></a>Konfiguráció

![Rendezési beállítások](media/data-flow/sort.png "Rendezés")

**Kis-és nagybetűk megkülönböztetése:** Azt határozza meg, hogy nem kívánja-e figyelmen kívül hagyni az esetet karakterlánc vagy szöveges mezők rendezése során

**Csak a partíción belüli rendezés:** Mivel az adatfolyamatok a sparkon futnak, minden adatfolyam partícióra van osztva. Ez a beállítás csak a bejövő partíción belül rendezi az adatátvitelt, és nem rendezi a teljes adatfolyamot. 

**Rendezési feltételek:** Válassza ki a rendezés alapjául szolgáló oszlopokat, és hogy milyen sorrendben történjen a rendezés. A sorrend meghatározza a rendezési prioritást. Adja meg, hogy az adatfolyam elején vagy végén megjelenjenek-e nullák.

### <a name="computed-columns"></a>Számított oszlopok

Egy oszlop értékének módosításához vagy kinyeréséhez a rendezés alkalmazása előtt vigye az egérmutatót az oszlop fölé, és válassza a "számított oszlop" lehetőséget. Ekkor megnyílik a Kifejezésszerkesztő, hogy a rendezési művelethez kifejezést hozzon létre az oszlop értékének használata helyett.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Példa

![Rendezési beállítások](media/data-flow/sort.png "Rendezés")

A fenti rendezési konfigurációhoz tartozó adatfolyam-szkript az alábbi kódrészletben található.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Következő lépések

A rendezést követően érdemes lehet az [összesített átalakítást](data-flow-aggregate.md) használni
