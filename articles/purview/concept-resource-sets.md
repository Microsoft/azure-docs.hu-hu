---
title: Erőforráskészletek megismerése
description: Ez a cikk ismerteti, mik azok az erőforráskészletek, és hogyan hozza létre őket az Azure Purview.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 330a6e54ee88781f71c4a861051aab94f8eef81f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587901"
---
# <a name="understanding-resource-sets"></a>Erőforráskészletek megismerése

Ez a cikk segít megérteni, hogy az Azure Purview hogyan használja az erőforráskészleteket az adateszközök logikai erőforrásokhoz való leképezéséhez.
## <a name="background-info"></a>Háttérinformációk

A nagy léptékű adatfeldolgozási rendszerek általában egyetlen táblát tárolnak egy lemezen több fájlként. Ezt a fogalmat az Azure Purview az erőforráskészletek használatával képviseli. Az erőforráskészlet egyetlen objektum a katalógusban, amely nagy számú objektumot képvisel a tárolóban.

Tegyük fel például, hogy a Spark-fürt egy DataFrame-et egy 2. generációs Azure Data Lake Storage (ADLS) adatforrásban őrzött meg. Bár a Sparkban a tábla egyetlen logikai erőforrásnak tűnik, a lemezen valószínűleg több ezer Parquet-fájl található, amelyek a teljes DataFrame tartalmának partícióját jelölik. Az IoT-adatok és a webes naplóadatok ugyanazokkal a kihívásokkal néznek szembe. Tegyük fel, hogy van egy érzékelője, amely másodpercenként többször is naplófájlokat ad ki. Nem fog sokáig tartani, amíg több százezer naplófájlt nem fog tartalmazni az érzékelőről.

A nagy számú adateszköz egyetlen logikai erőforráshoz való leképezésének feladatának kezelése érdekében az Azure Purview erőforráskészleteket használ.

## <a name="how-azure-purview-detects-resource-sets"></a>Hogyan észleli az Azure Purview az erőforráskészleteket?

Az Azure Purview támogatja az erőforráskészletek észlelését a Azure Blob Storage, ADLS Gen1 és ADLS Gen2.

Az Azure Purview automatikusan észleli az erőforráskészleteket a vizsgálat során. Ez a funkció a vizsgálaton keresztül betöltött összes adatot összehasonlítja egy meghatározott mintával.

Tegyük fel például, hogy beolvas egy olyan adatforrást, amelynek URL-címe `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Az Azure Purview az útvonalszegmenseket nézi, és megállapítja, hogy azok megfelelnek-e a beépített mintáknak. Beépített mintákkal rendelkezik a GUID azonosítókhoz, számokhoz, dátumformátumokhoz, honosítási kódokhoz (például en-us) és így tovább. Ebben az esetben a számminta megfelel a *23-nak.* Az Azure Purview feltételezi, hogy ez a fájl egy nevű erőforráskészlet `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` része.

Vagy egy URL-cím ( például ) számára az Azure Purview a honosítási mintával és a számmintával is egyezik, így egy nevű `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` erőforráskészletet hoz `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` létre.

Ezzel a stratégiával az Azure Purview a következő erőforrásokat leképezi ugyanoda az `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` erőforráskészletre:

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Az Azure Purview által erőforráskészletként nem észlelt fájltípusok

A nézet szándékosan nem próbálja erőforráskészletként besorolni a legtöbb dokumentumfájltípust, például a Wordet, az Excelt vagy a PDF-et. Ez alól kivétel a CSV formátum, mivel ez egy általános particionált fájlformátum.

## <a name="how-azure-purview-scans-resource-sets"></a>Hogyan vizsgálja az Azure Purview az erőforráskészleteket?

Amikor az Azure Purview észleli azokat az erőforrásokat, amelyekről úgy gondolja, hogy egy erőforráskészlet részei, teljes vizsgálatról mintavizsgálatra vált. Egy mintavizsgálat csak az erőforráskészletben lévőnek csak egy részhalmazát nyitja meg. Minden megnyitott fájlhoz a sémáját használja, és futtatja az osztályozóit. Az Azure Purview ezután megkeresi a megnyitott erőforrások közül a legújabb erőforrást, és az erőforrás sémáját és besorolását használja a katalógusban található teljes erőforráskészlet bejegyzésében.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Mit tárol az Azure Purview az erőforráskészletekkel kapcsolatban?

Az egyetlen séma és besorolás mellett az Azure Purview a következő információkat tárolja az erőforráskészletekkel kapcsolatban:

- A legújabb partícióerőforrás adatait olvasott be mélyebben.
- Összesíti az erőforráskészletet felállító partíció-erőforrásokra vonatkozó információkat.
- Partíciószám, amely megmutatja, hány partícióerőforrást talált.
- Sémaszám, amely azt mutatja, hogy a mintakészletben talált egyedi sémák közül hányon vett át mély vizsgálatot. Ez az érték lehet egy 1 és 5 közötti szám, vagy az 5,5+-nál nagyobb értékek.
- Partíciótípusok listája, ha egynél több partíciótípus szerepel az erőforráskészletben. Előfordulhat például, hogy egy IoT-érzékelő XML- és JSON-fájlokat is kiállít, bár mindkettő logikailag ugyanannak az erőforráskészletnek a része.

## <a name="built-in-resource-set-patterns"></a>Beépített erőforráskészlet-minták

Az Azure Purview a következő erőforráskészlet-mintákat támogatja. Ezek a minták névként is megjelenhetnek egy könyvtárban vagy egy fájlnév részeként.
### <a name="regex-based-patterns"></a>Regex-alapú minták

| Minta neve | Megjelenítendő név | Leírás |
|--------------|--------------|-------------|
| Guid         | {GUID}       | Az [RFC 4122 szabványban meghatározott globálisan egyedi azonosító](https://tools.ietf.org/html/rfc4122) |
| Szám       | {N}          | Egy vagy több számjegy |
| Dátum-/időformátumok | {Year} {Month} {Day} {N}     | Különböző dátum-/időformátumokat támogatunk, de mindegyiket {Year}[elválasztó]{Month}[elválasztó]{Day} vagy {N}s sorozat képviseli. |
| 4ByteHex     | {HEX}        | Egy 4 jegyű HEXX-szám. |
| Honosítás | {LOC}        | A [BCP 47-ben](https://tools.ietf.org/html/bcp47)meghatározott nyelvcímkék – és _ nevek is támogatottak (például en_ca en-ca) |

### <a name="complex-patterns"></a>Összetett minták

| Minta neve | Megjelenítendő név | Leírás |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Spark-partíció fájlazonosítója |
| Date(yyyy/mm/dd)InPath  | {Év}/{Hónap}/{Nap} | Év/hónap/nap minta több mappára kiterjedően |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Az erőforráskészletek megjelenítése az Azure Purview-katalógusban

Amikor az Azure Purview egy erőforráskészletbe egy eszközcsoportot illeszkedik, megkísérli kinyerni a katalógusban megjelenített névként használható leghasznosabb információkat. Néhány példa az alkalmazott alapértelmezett elnevezési konvencióra: 

### <a name="example-1"></a>1\. példa

Minősített név: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Megjelenített név: "a spark-kimenet neve"

### <a name="example-2"></a>2\. példa

Minősített név: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Megjelenített név: "saját particionált adatok"

### <a name="example-3"></a>3\. példa

Minősített név: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Megjelenített név: "data"

## <a name="customizing-resource-set-grouping-using-pattern-rules"></a>Erőforráskészlet-csoportosítás testreszabása mintaszabályokkal

hen vizsgálat egy tárfiókot, az Azure Purview meghatározott minták halmazát használja annak megállapításához, hogy az eszközcsoportok erőforráskészletek-e. Bizonyos esetekben előfordulhat, hogy az Azure Purview erőforráskészlet-csoportosítása nem tükrözi pontosan az adat tulajdonát. Ezek a problémák a következők lehetnek:

- Eszköz helytelen megjelölése erőforráskészletként
- Egy eszköz rossz erőforráskészletbe való beállítása
- Az eszköz helytelen megjelölése nem erőforráskészletként

Ha testre szeretné szabni vagy felülbírálni, hogy az Azure Purview hogyan észleli, hogy mely eszközök vannak erőforráskészletekként csoportosítva, és hogyan jelennek meg a katalógusban, mintaszabályokat határozhat meg a felügyeleti központban. Részletes útmutatásért és szintaxisért tekintse meg az [erőforráskészlet-mintaszabályokat.](how-to-resource-set-pattern-rules.md)
## <a name="next-steps"></a>Következő lépések

Az Azure Purview használatának első lépésekért lásd: [Rövid útmutató: Azure Purview-fiók létrehozása.](create-catalog-portal.md)
