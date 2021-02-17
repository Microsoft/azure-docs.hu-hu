---
title: Adatnormalizálás az Azure Sentinelben | Microsoft Docs
description: Ez a cikk azt ismerteti, hogy az Azure Sentinel hogyan normalizálja az adatokat számos különböző forrásból, és részletezi a normalizálás sémát.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 35b3f4fe5703e9ce902553f992dfa44cea7db78f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570456"
---
# <a name="normalization-in-azure-sentinel"></a>Normalizálás az Azure Sentinelben

Ez a cikk ismerteti az Adatséma normalizálása az Azure Sentinelben, valamint a hálózati kapcsolatok és munkamenetek sémáját, amelyhez hivatkozást tartalmaz.

## <a name="what-is-normalization"></a>Mi az a normalizálás?

A különböző adattípusok és táblázatok együttes használata kihívásokat jelent. Ismernie kell számos különböző adattípust és sémát, az egyes elemzési szabályok, munkafüzetek és vadászati lekérdezések egyedi készletét kell írnia és használnia, még azok esetében is, amelyek osztoznak a közös adatmegosztásokon (például a tűzfal eszközeire vonatkozik). A különböző adattípusok, a nyomozáshoz és a vadászathoz szükséges korreláció is nehézkes. Az Azure Sentinel zökkenőmentes felhasználói élményt nyújt a különböző forrásokból származó adatok egységes, normalizált nézetekben való kezeléséhez.

Az Azure Sentinel **Common Information Model** három szempontot tartalmaz:

- A **normalizált sémák** a könnyen használható előre jelezhető eseménytípus (táblák) közös készleteit fedik le, és egységesített képességeket építhetnek be (például hálózati tábla). A séma tartalmaz egy normalizált oszlopokra vonatkozó konvenciót, valamint az értékek és a formázási szabványosítás (például az IP-címek szabványos konzisztens ábrázolása) definícióit is.

- Az **elemzők** a különböző típusú meglévő adattípusokat a normalizált sémához rendelik. A modell alapján az adatok a normalizált sémába elemezhetők a lekérdezési időszakban (függvények használatával) vagy a betöltési idővel. Jelenleg csak a lekérdezési idejű elemzés támogatott.

- **Az egyes normalizált sémák tartalma az** elemzési szabályok, az interaktív munkafüzetek, a vadászati lekérdezések és a további tartalmak.

### <a name="current-release"></a>Aktuális kiadás

Ebben a kiadásban a [normalizált hálózati kapcsolatok és munkamenetek sémája](./normalization-schema.md) (v 1.0.0) elérhető nyilvános előzetes verzióra. A séma olyan hálózati kapcsolatokat vagy munkameneteket ír le, mint például a tűzfalak, a vezetékes adatforgalom, a NSG, a Netflow, a proxy rendszerek és a webes biztonsági átjárók által naplózott.  A kiválasztott lekérdezési idejű elemző és elemzési szabályok a sémával együtt érhetők el, és igénybe vehetik azt.

A séma jelenleg csak lekérdezési idejű elemzők használatával érhető el (lásd: elemzők szakasz).

Az adatelemzés további ábrázolásokra is használható, és a [OSSEM entitások elnevezési modelljével](https://ossemproject.com/cdm/entities/intro.html#) olyan oszlopokat hozhat létre, amelyek konzisztensek lesznek a meglévő és a jövőbeli normalizált táblákkal.

## <a name="normalized-schema-and-parsing"></a>Normalizált séma és elemzés

### <a name="how-our-normalized-schemas-are-defined"></a>A normalizált sémák meghatározása

Az Azure Sentinel a [nyílt forráskódú biztonsági események metaadatainak (OSSEM)](https://ossemproject.com/intro.html) közös információs modelljével van összhangban, ami lehetővé teszi a kiszámítható entitások korrelációját a normalizált táblák között. A OSSEM egy Közösség által vezetett projekt, amely elsősorban a különböző adatforrásokból és operációs rendszerekből származó biztonsági eseménynaplók dokumentációját és egységesítését összpontosítja. Emellett a projekt egy közös adatmodellt (CIM) is biztosít, amely adatmérnökök számára használható az adatnormalizálás eljárásaiban, hogy a biztonsági elemzők a különböző adatforrásokból származó adatok lekérdezésére és elemzésére is alkalmasak legyenek.

A [CIM OSSEM](https://ossemproject.com/cdm/intro.html) definiál egy entitást (például: fájl, GAZDAGÉP, IP, folyamat), és definiálja az egyes entitásokhoz tartozó attribútumok készletét. Emellett a CIM olyan táblákat (például [hálózati munkamenet](https://ossemproject.com/cdm/tables/network_session.html) -táblázatot) határoz meg, amelyek ezen entitások megfelelő attribútumait használják, ami lehetővé teszi a zökkenőmentes és kiszámítható korrelációt. Vegye figyelembe, hogy az entitások egymásba ágyazhatók (például a forrásoldali entitás tartalmazhatja a name attribútummal rendelkező fájl entitást is).

Ha többet szeretne megtudni az OSSEM-entitás struktúrájáról, látogasson el a [hivatalos OSSEM-referenciára](https://ossemproject.com/cdm/guidelines/entity_structure.html).

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>A normalizált sémák megvalósítása az Azure Sentinelben

A OSSEM CIM Azure Sentinel-ben történő megvalósítása során a OSSEM-Log Analytics ábrázolást a táblázatos ábrázolásra tervezjük, függetlenül attól, hogy ez egy beépített tábla-e, vagy olyan lekérdezési idejű elemzők vagy függvények használatával hozták létre, amelyek a jelen ábrázoláshoz meglévő adatokat képeznek le. A táblázatos ábrázoláshoz összefűzjük a OSSEM és az attribútumok nevét, és azokat egymáshoz rendeljük egyetlen oszlop neveként. Egy MD5 attribútumot tartalmazó kivonatoló entitást tartalmazó forrásoldali entitás például a következő Log Analytics oszlopként lesz implementálva: SrcFileHashMd5. (A OSSEM alapértelmezés szerint *snake_case* használ, míg az Azure Sentinel és a log Analytics *PascalCase* használ. A OSSEM ilyen oszlop src_file_hash_md5.)

Az Azure Sentinel implementációjában további egyéni mezők is előfordulhatnak, mivel a platformra vonatkozó követelmények és az Azure Sentinel-ügyfelekre jellemző használati esetek Log Analytics.

### <a name="schema-reference"></a>Séma-hivatkozás

További információért tekintse meg a [séma](./normalization-schema.md)- [dokumentációt](https://ossemproject.com/cdm/intro.html), valamint a hivatalos OSSEM.

A séma-hivatkozás az érték és a formátum szabványosítását is tartalmazza. Előfordulhat, hogy az eredeti vagy az elemzett forrás mező nem szabványos formátumú, vagy nem használja az értékek sémájának szabványos listáját, ezért a teljes normalizálás érdekében át kell alakítani a séma szabványos ábrázolására.

## <a name="parsers"></a>Elemzőit

- [Mi az elemzés?](#what-is-parsing)
- [Lekérdezési idő elemzők használata](#using-query-time-parsers)

### <a name="what-is-parsing"></a>Mi az elemzés?

A definiált normalizált táblázatok alapkészlete elérhető, és ezeket a táblákat át kell alakítania (elemezni/leképezni). Ez azt eredményezi, hogy a nyers űrlapról származó konkrét adatok a normalizált sémában jól ismert oszlopokban lesznek kibontva. Az Azure Sentinel szolgáltatásban végzett elemzés a **lekérdezési idő** -elemzők log Analytics felhasználói függvények (Kusto lekérdezési nyelv – KQL) használatával történik, amely a meglévő táblákban lévő adatok (például CommonSecurityLog, egyéni naplók táblái, syslog) segítségével alakítja át a normalizált táblák sémába.

Az Azure Sentinel szolgáltatásban még nem támogatott egyéb elemzési módszer a **betöltési idő** , amely lehetővé teszi az adatok közvetlenül a normalizált táblázat (ok) ba való begyűjtését, mivel az adatforrásokból való betöltése folyamatban van. A betöltési idő elemzése nagyobb teljesítményt biztosít, mivel az adatmodellt közvetlenül a függvények használata nélkül kérdezik le.

### <a name="using-query-time-parsers"></a>Lekérdezési idő elemzők használata

- [Elemző telepítése](#installing-a-parser)
- [Az elemzők használata](#using-the-parsers)
- [Elemzők testreszabása](#customizing-parsers)

#### <a name="installing-a-parser"></a>Elemző telepítése

Az elérhető lekérdezési idő elemzői az Azure Sentinel [hivatalos GitHub-tárházában](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0))érhetők el. Minden elemző verziója lehetővé teszi, hogy az ügyfelek könnyedén használhassák és felügyeljék a jövőbeli frissítéseket. Elemző telepítése:

1. Másolja a megfelelő elemzői tartalmat a fenti GitHub-hivatkozáson található megfelelő KQL-fájlból a vágólapra.

1. Az Azure Sentinel-portálon nyissa meg a naplók lapot, és illessze be a KQL fájl tartalmát a naplók képernyőre, majd kattintson a **Mentés** gombra.

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="Új elemző telepítése":::

1. A Save (Mentés) párbeszédpanelen töltse fel a mezőket a következőképpen:
    1. **Név**: ajánlott ugyanazt az értéket használni a **függvény alias** mezőjében (a fenti példában *CheckPoint_Network_NormalizedParser*).
    
    1. **Mentés másként**: Select **függvény**

    1. **Function alias**: a nevet a következő elnevezési konvencióban kell megnevezni – *PRODUCT_Network_NormalizedParser* (a fenti példában *CheckPoint_Network_NormalizedParser*).

    1. **Kategória**: választhat egy meglévő kategóriát, vagy létrehozhat új kategóriát (például *NormalizedNetworkSessionsParsers*).
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="Az elemző mentése":::

Az elemzők megfelelő használatához telepítenie kell az üres hálózati séma-elemzőt is (amely üres táblázatos nézetet hoz létre az összes hálózati munkamenet-séma mezőiről) és a hálózati meta-elemzőt (amely összekapcsolja az összes engedélyezett elemzőt, hogy egyetlen nézetet hozzon létre a hálózati séma különböző forrásaiból származó adatokból). A két elemző telepítése hasonló módon történik a fenti lépésekben.

A lekérdezési függvények mentésekor szükség lehet a lekérdezési tallózó bezárására és újbóli megnyitására az új függvény megjelenítéséhez.

#### <a name="using-the-parsers"></a>Az elemzők használata

Ha engedélyezve van, a meta-elemző segítségével lekérdezheti az összes jelenleg engedélyezett elemző egyesített nézetét. Ehhez nyissa meg az Azure Sentinel-naplók lapot, és kérdezze le a meta-elemzőt:

:::image type="content" source="./media/normalization/query-parser.png" alt-text="Az elemző lekérdezése":::
 
A "Query Explorer" gombra kattintva a metaadatokat vagy az egyes elemzőket a lekérdezés-kezelővel is elérheti a naplók lapon:

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="Lekérdezési tallózó":::

A jobb oldali ablaktáblán bontsa ki a "mentett lekérdezések" szakaszt, és keresse meg a "NormalizedNetworkParsers" mappát (vagy az elemzők létrehozásakor kiválasztott kategória nevét):

:::image type="content" source="./media/normalization/find-parser.png" alt-text="Elemző keresése":::

Az egyes elemzők lehetőségre kattintva megtekintheti az általa használt mögöttes függvényt, és futtathatja (vagy közvetlenül az aliasával érheti el). Vegye figyelembe, hogy egyes elemzők a normalizált mezők számára is megőrzik az eredeti mezőket a kényelem érdekében. Ezt egyszerűen szerkesztheti az elemző lekérdezésében.

> [!TIP]
> A mentett függvények az Azure Sentinel-táblázatok helyett bármilyen lekérdezésben használhatók, beleértve a vadászati és észlelési lekérdezéseket is. További információkért lásd:
>
> - [Adatnormalizálás az Azure Sentinel szolgáltatásban](normalization.md#parsers)
> - [Szöveg elemzése Azure Monitor naplókban](/azure/azure-monitor/log-query/parse-text)
>
#### <a name="customizing-parsers"></a>Elemzők testreszabása

Ismételje meg a fenti lépéseket (az elemző megkeresése a Query Explorerben), kattintson a megfelelő elemzőre, és tekintse meg a funkció megvalósítását.
Dönthet például úgy, hogy szerkeszti a meta-elemzőt az egyes elemzők hozzáadásához vagy eltávolításához.

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="Az elemző testreszabása":::
 
Ha a függvény módosult, kattintson ismét a Save (Mentés) gombra, és használja ugyanazt a nevet, aliast és kategóriát. A rendszer megnyit egy felülbírálási párbeszédpanelt – nyomja meg az "OK" gombot:

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="biztos vagy benne":::

#### <a name="additional-information"></a>További információ

A JSON, az XML és a CSV különösen a lekérdezési idő elemzésekor használható. Az Azure Sentinel beépített elemzési funkciókat tartalmaz a JSON, az XML és a CSV számára, valamint egy JSON-elemzési eszközt is.  További információ: [JSON-mezők használata az Azure sentinelben](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747) (blog). 

További információ a [mentett lekérdezésekről](../azure-monitor/logs/example-queries.md) (a lekérdezési idő elemzői megvalósításáról) log Analytics.


## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megismerheti az Azure Sentinel normalizáló sémáját. Saját maga a hivatkozási séma esetében tekintse meg az [Azure Sentinel-adatnormalizálás sémájának referenciáját](./normalization-schema.md).

* [Azure Sentinel blog](https://aka.ms/azuresentinelblog). Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.