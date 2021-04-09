---
title: Adatbányászat az Azure hatáskörébe (előzetes verzió)
description: Az adatbányászati fogalmakat ismerteti.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: 476355f41de5e0e6aaffdedea8947cab5221767a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200731"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Adatbányászat az Azure hatáskörébe Data Catalog ügyfél

Ez a cikk áttekintést nyújt az adatbányászatról az Azure hatáskörébe Data Catalog. Azt is részletezi, hogy az adatrendszerek hogyan integrálhatók a katalógusba az adatbányászati adatok rögzítéséhez. A hatáskörébe rögzítheti a szervezeti adatközpont különböző részeiben lévő adatbányászati értékeket, valamint az előkészítés különböző szintjein, beleértve a következőket:

- Teljesen nyers adatok, amelyek különböző platformokról lettek átrendezve
- Átalakított és előkészített adatértékek
- Vizualizációs platformok által használt adathalmazok.

## <a name="use-cases"></a>Használati esetek

Az adatbányászat széles körben megértette az adatforrást átölelő életciklust, és az adatközpontban az idő múlásával halad át. Különböző típusú visszamenőleges megjelenésű forgatókönyvek, például hibaelhárítás, az adatfolyamatok és a hibakeresés alapvető okának nyomon követésére szolgál. A Lineage az adatminőségi elemzéshez, a megfelelőséghez és a "mit if" forgatókönyvekhez is használatos. A Lineage vizuálisan ábrázolja a forrásról a célhelyre irányuló adatok megjelenítését, beleértve az adatok átalakításának módját. A legtöbb vállalati adatkörnyezet összetettsége miatt ezeket a nézeteket nehéz megérteni a perifériás adatpontok összevonása vagy maszkolása nélkül.

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Lineage-élmény az Azure hatáskörébe Data Catalog

A hatáskörébe Data Catalog kapcsolódni fog más adatfeldolgozási, tárolási és elemzési rendszerekhez, hogy kinyerje a származási adatokat. Az adatok összevonása a katalógus általános, forgatókönyv-specifikus kifejlődési felületének felel meg.

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="a blob Store-ból átmásolt adatok teljes körűen Power BI irányítópulton":::

Az adatkészletbe beletartozhatnak az kinyerési, átalakítási (ETL/elt-rendszerek), elemzési és vizualizációs rendszerek. Az egyes rendszerek olyan gazdag statikus és működési metaadatokat rögzítenek, amelyek a rendszerek határain belüli adatok állapotát és minőségét ismertetik. Az adatkatalógusban a Lineage célja, hogy kinyerje a mozgást, az átalakítást és az operatív metaadatokat az egyes adatrendszerekről a lehető legalacsonyabb gabona alapján.

Az alábbi példa a több rendszer között áthaladó adatátviteli esetek tipikus használati esete, ahol a Data Catalog a lebontási rendszerek mindegyikéhez csatlakozni fog.

- Data Factory adatok másolása a helyszíni/nyers zónából a felhőben lévő kirakodási zónába. 
- Az adatfeldolgozási rendszerek, például a szinapszis, a Databricks feldolgozzák és átalakítják az adatok a kirakodási zónából a kurátori zónába a notebook
- Az adatelemzési modellek további feldolgozása az optimális lekérdezési teljesítmény és összesítés érdekében. 
- Az adatvizualizációs rendszerek felhasználják az adathalmazokat és dolgozzák fel a meta-modellen keresztül egy BI-irányítópultot, ML-kísérleteket és így tovább.

## <a name="lineage-granularity"></a>A Lineage részletessége

Ez a szakasz részletesen ismerteti, hogy az adatkatalógus milyen részletességgel gyűjti össze a leszármazási adatokat. Ez a részletesség a folyamatban lévő adatrendszerektől függően változhat.

### <a name="entity-level-lineage-sources--process--targets"></a>Entitás szintje Lineage: forrás (ok) > folyamat > cél (ok) 

- A Lineage diagramként van ábrázolva, amely általában olyan forrás-és célcsoportokat tartalmaz az adattároló rendszerekben, amelyeket egy számítási rendszer által meghívott folyamat csatlakoztat. 
- Az adatrendszerek az adatkatalógushoz csatlakozva olyan egyedi objektum létrehozásához és jelentéséhez kapcsolódnak, amely az alapul szolgáló adatrendszer fizikai objektumára hivatkozik, például: SQL tárolt eljárás, jegyzetfüzetek stb.
- A magas szintű hűség a további metaadatokat, például a tulajdonjogot rögzíti, hogy a forrás & a célként szolgáló entitások számára olvasható formában jelenítse meg a törzset. például: a struktúra vagy a fájl szintjének helyett a kaptár tábla szintjén.

### <a name="column-or-attribute-level-lineage"></a>Oszlop vagy attribútum szintjének Lineage

Azonosítsa egy olyan forrásoldali entitás attribútumát, amely attribútum (ok) létrehozásához vagy származtatása céljából van használatban a célként megadott entitásban. A forrás attribútum nevét megtarthatja vagy átnevezheti a célhelyen. Az ADF-hez hasonló rendszerek a helyszíni környezetből a felhőbe is elvégezhetnek egy másolatot. Példa: `Table1/ColumnA -> Table2/ColumnA`.

### <a name="process-execution-status"></a>Folyamat végrehajtási állapota

Az alapvető okok elemzésének és az adatminőségi forgatókönyvek támogatásához a feladatok végrehajtási állapotát az adatfeldolgozó rendszerekben rögzítjük. Ennek a követelménynek semmi köze nincs a más adatfeldolgozási rendszerek figyelési képességeinek lecseréléséhez, sem a cél az, hogy lecserélje őket. 

## <a name="summary"></a>Összefoglalás

A Lineage a hatáskörébe Data Catalog kritikus funkciója a minőségi, megbízhatósági és auditálási forgatókönyvek támogatásához. A adatkatalógus célja, hogy egy robusztus keretrendszert hozzon létre, ahol a környezetében lévő összes adatrendszer természetes módon csatlakoztatható és jelentheti a leszármazást. A metaadatok elérhetővé tétele után az adatkatalógus összekapcsolhatja az adatrendszerek által biztosított metaadatokat az adatkezelési használati esetekben.

## <a name="next-steps"></a>Következő lépések

* [Gyors útmutató: Azure-beli hatáskörébe tartozó fiók létrehozása a Azure Portal](create-catalog-portal.md)
* [Gyors útmutató: Azure-beli hatáskörébe tartozó fiók létrehozása Azure PowerShell/Azure CLI használatával](create-catalog-powershell.md)
* [Rövid útmutató: a hatáskörébe Studio használata](use-purview-studio.md)
