---
title: Átalakítás rendezése leképezési adatfolyamban
description: Azure Data Factory adatrendezési átalakítás leképezése
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 4a6567f8576e2507704956233bc593b203b48239
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588734"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Átalakítás rendezése leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A rendezési átalakítással rendezheti az aktuális adatfolyam bejövő sorait. Kiválaszthat egyes oszlopokat, és növekvő vagy csökkenő sorrendbe rendezheti őket.

> [!NOTE]
> A leképezési adatfolyamok spark-fürtökön vannak végrehajtva, amelyek több csomópont és partíció között osztják el az adatokat. Ha egy későbbi átalakítás során újraparticionálja az adatokat, az adatok újratitkozása miatt elveszítheti a rendezést. A rendezési sorrend fenntartásának legjobb módja az adatfolyamban, ha egyetlen partíciót ad meg az átalakítás Optimalizálás lapján, és a Rendezés átalakítást a fogadóhoz a lehető közelebb tartja.

## <a name="configuration"></a>Konfiguráció

![Beállítások rendezése](media/data-flow/sort.png "Rendezés")

**A kis- és a kis- és a nagy- és a kis** Azt határozza meg, hogy szeretné-e figyelmen kívül hagyni a kis- és a nagy- és a kis- és a nagy- és a nagy- és szövegmezőket a sztringek és a szövegmezők rendezése során

**Rendezés csak a partíciókban:** Mivel az adatfolyamok Sparkon futnak, minden adatfolyam partíciókra van osztva. Ez a beállítás csak a bejövő partíciókban rendezi az adatokat ahelyett, hogy a teljes adatfolyamot rendezi. 

**Rendezési feltételek:** Válassza ki, hogy mely oszlopok szerint rendez, és milyen sorrendben történjen a rendezés. A sorrend határozza meg a rendezési prioritást. Adja meg, hogy a nullértékek megjelenjenek-e az adatfolyam elején vagy végén.

### <a name="computed-columns"></a>Számított oszlopok

Ha módosítani vagy ki szeretnényerni egy oszlop értékét a rendezés alkalmazása előtt, vigye a kurzort az oszlop fölé, és válassza a "számított oszlop" lehetőséget. Ezzel megnyitja a kifejezésszerkesztőt, és oszlopérték használata helyett egy kifejezést hoz létre a rendezési művelethez.

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

![Beállítások rendezése](media/data-flow/sort.png "Rendezés")

A fenti rendezési konfiguráció adatfolyam-szkripte az alábbi kódrészletben található.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Következő lépések

A rendezés után használhatja az összesítési [átalakítást](data-flow-aggregate.md)
