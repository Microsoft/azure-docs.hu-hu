---
title: Data Catalog Lineage felhasználói útmutatója (előzetes verzió)
description: Ez a cikk áttekintést nyújt az Azure hatáskörébe tartozó Catalog Lineage szolgáltatásról.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/29/2020
ms.openlocfilehash: 6af183c16238c6630b194b112f0c09fd4399d443
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694070"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Az Azure hatáskörébe Data Catalog Lineage felhasználói útmutatója

Ez a cikk áttekintést nyújt az Azure hatáskörébe Data Catalog adatbányászati funkcióiról.

## <a name="background"></a>Háttér

Az Azure hatáskörébe tartozó platform egyik funkciója az adatfolyamatok által létrehozott adathalmazok közötti vonal megjelenítésének lehetősége. Az olyan rendszerek, mint az Data Factory, az adatmegosztás és a Power BI az adatvonalat rögzítik a mozgatások során. Az Atlas hookok és REST API használatával az egyéni Lineage-jelentéskészítés is támogatott.

## <a name="lineage-collection"></a>Lineage-gyűjtemény 
 Az Azure-beli hatáskörébe tartozó, nagyvállalati adatrendszerek által gyűjtött metaadatok összefűzése egy végpontok közötti adatvonalra mutat. A kifejlődést a hatáskörébe begyűjtő adatrendszerek széles körben kategorizálva lesznek a következő három típusra.

### <a name="data-processing-system"></a>Adatfeldolgozási rendszerek
Az adatintegrációs és az ETL-eszközök végrehajtási idő alatt leküldhetik az Azure hatáskörébe. Az olyan eszközök, mint a Data Factory, az adatmegosztás, a szinapszis, a Azure Databricks stb.) az adatrendszerek ezen kategóriájára tartoznak. Az adatfeldolgozási rendszerek az adatkészleteket különböző adatbázisokból és tárolási megoldásokból származó forrásként hivatkoznak a cél adatkészletek létrehozásához. Az alábbi táblázatban látható az adatfeldolgozási rendszerek aktuálisan integrált adatfeldolgozó rendszereinek listája.


| Adatfeldolgozási rendszerek | Támogatott hatókör |
| ---------------------- | ------------|
| Azure Data Factory | [Másolási tevékenység](how-to-link-azure-data-factory.md#data-factory-copy-data-support) <br> [Adatfolyam-tevékenység](how-to-link-azure-data-factory.md#data-factory-data-flow-support) <br> [SSIS-csomag végrehajtása tevékenység](how-to-link-azure-data-factory.md#data-factory-execute-ssis-package-support) |
| Azure Data Share | [Pillanatkép megosztása](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>Adattároló rendszerek
Az adatbázisok & a tárolási megoldások, például a SQL Server, a Teradata és az SAP, hogy a lekérdezési motorokkal alakítsuk át az adatátalakítást programozási nyelv használatával. A tárolt eljárásokból származó adatok összegyűjtését a rendszer begyűjti a hatáskörébe, és összevarrja azokat más rendszerekből származó vonalakkal.

| Adattárolási rendszerek | Támogatott hatókör |
| ---------------------- | ------------|
| Teradata | Tárolt eljárások

### <a name="data-analytics--reporting-systems"></a>Adatelemzési & jelentési rendszerek
Adatrendszerek, például az Azure ML és a Power BI jelentés-lefordítás az Azure hatáskörébe. Ezek a rendszerek a tárolási rendszerekből származó adatkészleteket és folyamatokat használják a saját meta-modelljük alapján BI-irányítópult, ML kísérletek és így tovább.

| Adatelemzési & jelentési rendszer | Támogatott hatókör |
| ---------------------- | ------------|
| Power BI | [Adatkészletek, adatfolyamok, jelentések & irányítópultok](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>Ismerkedés a lineagevel

A hatáskörébe tartozó vonal adatkészleteket és folyamatokat tartalmaz. Az adatkészleteket csomópontoknak is nevezik, a folyamatok pedig élek:

* **Adatkészlet (csomópont)**: egy folyamatba bemenetként megadott adatkészlet (strukturált vagy strukturálatlan). Például egy SQL-tábla, egy Azure-Blob és egy fájl (például. csv és. xml) minden adathalmaznak minősül. A hatáskörébe tartozó vonalakban az adathalmazok téglalap alakú mezőkben jelennek meg.

* **Folyamat (Edge)**: egy adatkészleten végrehajtott tevékenységet vagy átalakítást folyamatnak nevezzük. Például az ADF másolási tevékenység, az adatmegosztási pillanatkép és így tovább. A hatáskörébe tartozó folyamatok esetében a folyamatokat a kör alakú mezők jelölik.

A következő lépések végrehajtásával érheti el egy adott eszköz rendszerállapot-információit:

1. A Azure Portal nyissa meg az [Azure hatáskörébe tartozó fiókok lapot](https://aka.ms/purviewportal).

1. Válassza ki az Azure-beli hatáskörébe tartozó fiókot a listából, majd válassza a **hatáskörébe tartozó fiók elindítása** lehetőséget az **Áttekintés** lapon.

1. Az Azure-beli hatáskörébe tartozó **kezdőlapon** keresse meg az adatkészlet nevét vagy a folyamat nevét, például az ADF-másolást vagy az adatfolyam-tevékenységet. Majd nyomja le az ENTER billentyűt.

1. A keresési eredmények közül válassza ki az eszközt, és válassza ki annak **Lineage** (kiindulási) lapját.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="A Lineage lap kijelölését bemutató képernyőkép." border="true":::

## <a name="asset-level-lineage"></a>Objektum szintű Lineage

Az Azure hatáskörébe tartozik az adathalmazok és a folyamatok adatforrásának szintje. Az adategység szintjének megjelenítéséhez nyissa meg a katalógus aktuális eszközének **Lineage** lapját. Válassza ki az aktuális adatkészlet-eszköz csomópontot. Alapértelmezés szerint az adataihoz tartozó oszlopok listája megjelenik a bal oldali ablaktáblán.

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="Képernyőfelvétel: oszlopok megtekintésének kiválasztása a Lineage lapon" border="true":::

## <a name="dataset-column-lineage"></a>Adatkészlet oszlopának Lineage

Az adatkészlet oszlop szintű vonalának megjelenítéséhez nyissa meg a katalógus aktuális eszközének **Lineage (vonal** ) lapját, és kövesse az alábbi lépéseket:

1. Miután a Lineage (leválasztás) lap bal oldali ablaktábláján bejelöli az adatvonalban megjeleníteni kívánt oszlopok melletti jelölőnégyzetet, jelölje be a jelölőnégyzetet.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="A Lineage lapon megjelenítendő oszlopok kijelölését bemutató képernyőkép." lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

2. Vigye az egérmutatót egy kijelölt oszlopra a bal oldali ablaktáblán, vagy a Lineage vászon adatkészletében az oszlop leképezésének megtekintéséhez. A rendszer minden oszlop-példányt kiemel.

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="Képernyőfelvétel: az oszlop neve fölé mutató egérmutató kiemelése az adatbányászati útvonalon lévő oszlop folyamatában." lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

3. Ha az oszlopok száma nagyobb, mint ami a bal oldali ablaktáblán látható, akkor a szűrő beállítással kiválaszthatja a név alapján egy adott oszlopot. Azt is megteheti, hogy az egér segítségével görgeti a listát.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="Képernyőfelvétel: oszlopok szűrése oszlop neve alapján a Lineage lapon." lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

4. Ha a leválasztási vászon több csomópontot és éleket tartalmaz, a szűrő használatával válassza ki az adategységet, vagy dolgozza fel a csomópontokat név szerint. Azt is megteheti, hogy az egérmutatót a Lineage ablak körére helyezi.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="Képernyőfelvétel: az adategység csomópontjainak neve a Lineage lapon." lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

5. A bal oldali ablaktáblán látható váltógomb használatával kiemelheti az adatkészletek listáját a Lineage vásznon. Ha kikapcsolja a kapcsolót, a rendszer minden olyan eszközt megjelenít, amely legalább egy kiválasztott oszlopot tartalmaz. Ha bekapcsolja a kapcsolót, csak az összes oszlopot tartalmazó adatkészletek jelennek meg.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="Képernyőfelvétel: a váltás használatával szűrheti a csomópontok listáját a Lineage lapon." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

## <a name="process-column-lineage"></a>Folyamat oszlopának Lineage
Az adatfolyamatok egy vagy több bemeneti adatkészletet hozhatnak létre egy vagy több kimenet létrehozásához. A hatáskörébe az oszlop szintű Lineage a folyamat csomópontjai számára érhető el. 
1. Váltás a bemeneti és a kimeneti adatkészletek között egy legördülő listából az oszlopok panelen.
2. Jelöljön ki oszlopokat egy vagy több táblából a bemeneti adatkészletből a megfelelő kimeneti adatkészletbe áramló vonal megjelenítéséhez.

   :::image type="content" source="./media/catalog-lineage-user-guide/process-column-lineage.png" alt-text="A folyamat csomópontjának oszlopait ábrázoló képernyőkép." lightbox="./media/catalog-lineage-user-guide/process-column-lineage.png":::

## <a name="browse-assets-in-lineage"></a>Eszközök tallózása a lineageben
1. Válassza az **eszközre váltás** bármely eszközön lehetőséget, hogy a megfelelő metaadatokat a leszármazási nézetből megtekinthesse. Ennek hatására a rendszer hatékony módot keres a katalógusban lévő másik objektumra a leszármazási nézetből.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="Képernyőfelvétel: Váltás az eszközre az adatbányászati adategységben." lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

2. A leszármazási vászon összetett lehet a népszerű adatkészletek számára. A zűrzavar elkerülése érdekében az alapértelmezett nézet csak a fókuszban lévő objektum öt szintjének megjelenítését fogja megjeleníteni. A többi vonal kibontható úgy, hogy a kibontási vászonon a buborékok elemre kattint. Az adatfelhasználók a vásznon található eszközöket is el tudják rejteni, amelyek nem érdeklik. A zűrzavar további csökkentése érdekében kapcsolja ki a bekapcsolás **nagyobb vonalakat** a Lineage vászon tetején. Ez a művelet elrejti az összes buborékot a Lineage vásznon.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="Képernyőfelvétel: több vonal váltásának módja." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

3. A Lineage vászon intelligens gombjaival optimális képet kaphat a leszármazásról. Az automatikus elrendezés, a nagyítás a mérethez, a nagyítás és kicsinyítés, a teljes képernyő és a navigációs Térkép egyaránt elérhető a katalógusban.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="A Lineage intelligens gombjainak kijelölését bemutató képernyőkép." lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>Következő lépések

* [Hivatkozás Azure Data Factoryra a Lineage](how-to-link-azure-data-factory.md)
* [Hivatkozás az Azure-beli adatmegosztásra a Lineage számára](how-to-link-azure-data-share.md)