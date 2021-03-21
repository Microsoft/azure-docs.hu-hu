---
title: Azure Stream Analytics Power BI kimenete
description: Ez a cikk azt ismerteti, hogyan lehet adatokat kiadni a Azure Stream Analyticsból a Power BIba.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: a94389a075fd62d80345a21e32f1bc977dfdee87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020060"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>Azure Stream Analytics Power BI kimenete

A Stream Analytics feladatokhoz [Power bi](https://powerbi.microsoft.com/) kimenetként használhatja az elemzési eredmények gazdag vizualizációs élményének biztosításához. Ezt a funkciót használhatja az operatív irányítópultokhoz, a jelentéskészítéshez és a metrikus alapú jelentéskészítéshez.

Stream Analytics Power BI kimenete jelenleg nem érhető el az Azure China 21Vianet és az Azure Germany (T-Systems International) régióiban.

## <a name="output-configuration"></a>Kimeneti konfiguráció

A következő táblázat felsorolja a tulajdonságok nevét és leírásait a Power BI kimenetének konfigurálásához.

| Tulajdonság neve | Description |
| --- | --- |
| Kimeneti alias |Adjon meg egy rövid nevet, amelyet a lekérdezésekben használ a lekérdezés kimenetének a Power BI kimenetre való irányításához. |
| Csoport munkaterülete |Ha az adatmegosztást más Power BI felhasználókkal szeretné engedélyezni, válassza ki a csoportokat a Power BI-fiókban, vagy válassza **a saját munkaterület** lehetőséget, ha nem szeretne írni egy csoportba. Egy meglévő csoport frissítéséhez a Power BI hitelesítés megújítása szükséges. |
| Adatkészlet neve |Adja meg azt az adatkészlet-nevet, amelyet a Power BI kimenetének használni szeretne. |
| Table name (Táblázat neve) |Adja meg a tábla nevét a Power BI kimenetének adatkészletében. Jelenleg Power BI Stream Analytics feladatokból származó kimenetnek csak egy táblája lehet egy adatkészletben. |
| Kapcsolat engedélyezése | A kimeneti beállítások konfigurálásához engedélyeznie kell a Power BI. Miután engedélyezte ezt a kimenetet a Power BI-irányítópulthoz, visszavonhatja a hozzáférést a felhasználói fiók jelszavának módosításával, a feladatok kimenetének törlésével vagy a Stream Analytics feladatok törlésével. | 

A Power BI kimenetének és irányítópultjának konfigurálásához lásd: [Azure stream Analytics és Power bi](stream-analytics-power-bi-dashboard.md) oktatóanyag.

> [!NOTE]
> Ne explicit módon hozza létre az adatkészletet és a táblát a Power BI irányítópulton. Az adatkészlet és a tábla automatikusan fel lesz töltve a feladatok elindításakor, és a feladattal Power BIba kerül a bepumpálás. Ha a feladatütemezés nem hoz létre eredményeket, az adatkészlet és a tábla nem jön létre. Ha Power BI már van olyan adatkészlet és tábla, amelynek a neve megegyezik az ebben a Stream Analytics feladatban megadott névvel, a rendszer felülírja a meglévő adatokat.
>

### <a name="create-a-schema"></a>Séma létrehozása

Azure Stream Analytics létrehoz egy Power BI adatkészletet és egy tábla sémát a felhasználó számára, ha még nem léteznek. Minden más esetben a tábla új értékekkel frissül. Jelenleg csak egy tábla létezhet egy adatkészleten belül. 

A Power BI az első, az első kimenő (FIFO) adatmegőrzési szabályt használja. Az adatok egy táblában lesznek gyűjtve, amíg 200 000 sort nem üt.

> [!NOTE]
> Nem ajánlott több kimenetet ugyanarra az adatkészletbe írni, mert több probléma merülhet fel. Az egyes kimenetek egymástól függetlenül megpróbálják létrehozni az Power BI adatkészletet, ami több azonos nevű adatkészletet eredményezhet. Továbbá, ha a kimenetek nem konzisztens sémákkal rendelkeznek, az adatkészlet módosítja a sémát az egyes írásokra, ami túl sok séma-módosítási kérést eredményez. Még ha ezek a problémák elkerülhetők, a több kimenet kevesebb lesz, mint egyetlen egyesített kimenet.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Adattípus átalakítása Stream Analyticsból Power BI

Azure Stream Analytics az adatmodellt dinamikusan frissíti futásidőben, ha a kimeneti séma megváltozik. Az oszlop neve megváltozik, az oszlop típusa megváltozik, az oszlopok hozzáadása vagy eltávolítása pedig nyomon követhető.

Ez a táblázat a [stream Analytics](/stream-analytics-query/data-types-azure-stream-analytics) adattípusok adattípusra való átalakítását ismerteti Power bi [Entity EDM-típusokra](/dotnet/framework/data/adonet/entity-data-model), ha Power bi adatkészlet és tábla nem létezik.

Stream Analytics | Power BI
-----|-----
bigint | Int64
nvarchar (max.) | Sztring
dátum/idő | Datetime
float | Dupla
Tömb rögzítése | Karakterlánc típusa, konstans érték: "IRecord" vagy "IArray"

### <a name="update-the-schema"></a>A séma frissítése

Stream Analytics kikövetkezteti az adatmodell-sémát a kimenetben szereplő első események alapján. Később, ha szükséges, az adatmodell-séma úgy frissül, hogy befogadja a bejövő eseményeket, amelyek esetleg nem férnek hozzá az eredeti sémához.

Kerülje a `SELECT *` lekérdezésben, hogy megakadályozza a dinamikus séma frissítését a sorok között. A lehetséges teljesítmény-kihatások mellett előfordulhat, hogy az eredményekhez szükséges idő bizonytalan. Válassza ki azokat a pontos mezőket, amelyeket meg kell jeleníteni a Power BI irányítópulton. Emellett az adatértékeknek meg kell felelniük a választott adattípusnak.

Előző/aktuális | Int64 | Sztring | Datetime | Dupla
-----------------|-------|--------|----------|-------
Int64 | Int64 | Sztring | Sztring | Dupla
Dupla | Dupla | Sztring | Sztring | Dupla
Sztring | Sztring | Sztring | Sztring | Sztring 
Datetime | Sztring | Sztring |  Datetime | Sztring

## <a name="output-batch-size"></a>Kimeneti köteg mérete

A kimeneti köteg mérete: [Power bi REST API-korlátok](/power-bi/developer/automation/api-rest-api-limitations).

## <a name="next-steps"></a>Következő lépések

* [Felügyelt identitás használata a Azure Stream Analytics-feladatok hitelesítéséhez Power BI (előzetes verzió)](powerbi-output-managed-identity.md)
* [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)