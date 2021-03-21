---
title: Erőforráskészletek megismerése
description: Ez a cikk ismerteti, hogy milyen erőforrás-készleteket és hogyan hozza létre az Azure hatáskörébe.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: cbf070dce056795ad8e4a5f3e4d609e7d36d631e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200814"
---
# <a name="understanding-resource-sets"></a>Erőforráskészletek megismerése

Ebből a cikkből megtudhatja, hogyan használja az Azure hatáskörébe az adategységek logikai erőforrásokhoz való leképezését az erőforrás-készletek használatával.
## <a name="background-info"></a>Háttér-információk

A méretezés alatt álló adatfeldolgozási rendszerek általában egyetlen táblát tárolnak egy lemezen több fájlként. Ez a koncepció az Azure hatáskörébe tartozik az erőforrás-készletek használatával. Az erőforrás-készlet a katalógus egyetlen objektuma, amely nagy mennyiségű eszközt képvisel a tárolóban.

Tegyük fel például, hogy a Spark-fürt megőrzött egy DataFrame egy Azure Data Lake Storage (ADLS) Gen2-adatforrásban. Bár a Sparkban a tábla egyetlen logikai erőforráshoz hasonlít, a lemezen valószínűleg több ezer parketta-fájl található, amelyek mindegyike a teljes DataFrame-tartalom partícióját jelöli. A IoT és a webnaplók adatkezelési feladatának ugyanaz a kihívása. Képzelje el, hogy van egy érzékelője, amely másodpercenként többször is kiírja a naplófájlokat. Addig nem fog sokáig tartani, amíg az adott érzékelőből több százezer naplófájl van.

Ahhoz, hogy a nagy számú adategység egyetlen logikai erőforráshoz való leképezésének kihívásával foglalkozzon, az Azure hatáskörébe erőforrás-készleteket használ.

## <a name="how-azure-purview-detects-resource-sets"></a>Hogyan észleli az Azure hatáskörébe az erőforrás-készleteket?

Az Azure-beli hatáskörébe az Azure-Blob Storage, a ADLS Gen1 és a ADLS Gen2ben lévő erőforrás-készletek észlelése támogatott.

Az Azure-beli hatáskörébe a vizsgálat során automatikusan észleli az erőforrás-készleteket. Ez a funkció a vizsgálat során betöltött összes adatot megvizsgálja, és összehasonlítja a meghatározott minták egy halmazával.

Tegyük fel például, hogy egy olyan adatforrást keres, amelynek URL-címe `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Az Azure-beli hatáskörébe az elérésiút-szegmensek láthatók, és meghatározza, hogy a beépített mintázatoknak megfelelőek-e. Beépített mintákat tartalmaz a GUID-azonosítók, számok, dátumformátum, honosítási kódok (például en-US) stb. Ebben az esetben a szám minta *23*-ra illeszkedik. Az Azure-beli hatáskörébe feltételezi, hogy ez a fájl egy nevű erőforrás-készlet részét képezi `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Az URL-címekhez hasonlóan az Azure-beli `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` hatáskörébe is illeszkedik a honosítási minta és a szám minta, amely egy nevű erőforrás-készletet hoz létre `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Ennek a stratégiának a használatával az Azure hatáskörébe a következő erőforrások képezhetők le ugyanahhoz az erőforrás-készlethez `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Az Azure hatáskörébe tartozó fájltípusok nem észlelhetők erőforrás-készletként

A hatáskörébe szándékosan nincs lehetőség a legtöbb dokumentum-fájltípus (például Word, Excel vagy PDF) erőforrás-készletként való besorolására. A kivétel CSV-formátum, mivel ez egy közös particionált fájlformátum.

## <a name="how-azure-purview-scans-resource-sets"></a>Hogyan vizsgálja az Azure-beli hatáskörébe az erőforrás-készleteket?

Ha az Azure hatáskörébe olyan erőforrásokat észlel, amely úgy gondolja, hogy egy erőforrás-készlet részét képezi, a teljes vizsgálatból egy minta-vizsgálatra vált. Egy minta vizsgálatban csak a fájlok egy részhalmazát nyitja meg az erőforrás-készletben. Minden megnyitott fájl esetében a sémáját használja, és futtatja az osztályozó elemeit. Az Azure-beli hatáskörébe ezután megkeresi a legújabb erőforrást a megnyitott erőforrások között, és az adott erőforrás sémáját és besorolásait a katalógusban lévő teljes erőforráshoz tartozó bejegyzésben használja.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Milyen Azure-beli feladatok érhetők el az erőforrás-készletekről

Az egyes sémák és besorolások mellett az Azure hatáskörébe a következő információkat tárolja az erőforrás-készletekről:

- A legutóbb beolvasott partíciós erőforrásból származó adatok.
- Az erőforrás-készletet alkotó partíciós erőforrások összesített adatai.
- Partíciók száma, amely megmutatja, hogy hány partíciós erőforrást talált.
- Egy séma száma, amely azt mutatja, hogy hány egyedi séma található a mintában. Ez az érték egy 1 – 5 közötti szám, vagy az 5, 5 + értéknél nagyobb értékek esetén.
- A partíciós típusok listája, ha az erőforrás-készletben egynél több partíció szerepel. Előfordulhat például, hogy egy IoT-érzékelő XML-és JSON-fájlokat is kiállít, bár mindkettő logikailag ugyanahhoz az erőforráshoz tartozik.

## <a name="built-in-resource-set-patterns"></a>Beépített erőforrás-készlet mintázatai

Az Azure-beli hatáskörébe a következő erőforrás-set mintázatok támogatottak. Ezek a minták egy könyvtárban vagy egy fájlnév részeként is megjelenhetnek.
### <a name="regex-based-patterns"></a>Regex-alapú minták

| Minta neve | Megjelenítendő név | Leírás |
|--------------|--------------|-------------|
| Guid         | GUID       | Az [RFC 4122](https://tools.ietf.org/html/rfc4122) -ben meghatározott globálisan egyedi azonosító |
| Szám       | N          | Egy vagy több számjegy |
| Dátum-és időformátumok | Év Hónap Nap N     | Különböző dátum-/időformátumokat támogatunk, de mindegyiket {Year} [elválasztó] {month} [elválasztó] {Day} vagy {N} s sorozat jelöli. |
| 4ByteHex     | Hex        | Egy 4 számjegyű HEXADECIMÁLIS szám. |
| Honosítás | Loc        | A [BCP 47](https://tools.ietf.org/html/bcp47)-ben definiált nyelvi címke (például: en_CA és en-CA) támogatott. |

### <a name="complex-patterns"></a>Összetett minták

| Minta neve | Megjelenítendő név | Leírás |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Spark partíciós fájl azonosítója |
| Dátum (éééé/hh/nn) – elérési út  | {Year}/{Month}/{Day} | Több mappára kiterjedő év/hónap/nap minta |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Az erőforrás-készletek megjelenítése az Azure hatáskörébe katalógusában

Ha az Azure-beli hatáskörébe illeszkedik egy erőforrás-csoportba, az megpróbálja kinyerni a leghasznosabb információkat, amelyeket a rendszer a katalógusban megjelenítendő névként használ. Néhány példa az alapértelmezett elnevezési konvencióra: 

### <a name="example-1"></a>1\. példa

Minősített név: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Megjelenítendő név: "Spark-kimenet neve"

### <a name="example-2"></a>2\. példa

Minősített név: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Megjelenítendő név: "saját particionált adatai"

### <a name="example-3"></a>3\. példa

Minősített név: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Megjelenítendő név: "érték"

## <a name="known-issues-with-resource-sets"></a>Erőforrás-készletekkel kapcsolatos ismert problémák

Bár az erőforrás-készletek a legtöbb esetben jól működnek, a következő problémák merülhetnek fel, amelyekben az Azure hatáskörébe tartoznak:

- Az eszköz helytelen jelölése erőforrás-készletként
- Nem megfelelő erőforrás-készletbe helyezi az eszközt.
- Nem megfelelően jelöl meg egy eszközt, mert nem erőforrás-készlet

## <a name="next-steps"></a>Következő lépések

Az Azure-beli hatáskörébe való ismerkedéshez tekintse meg a rövid útmutató [: Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című témakört.
