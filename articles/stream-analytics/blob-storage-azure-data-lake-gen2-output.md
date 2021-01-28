---
title: BLOB Storage és Azure Data Lake Gen2 kimenete Azure Stream Analytics
description: Ez a cikk a blob Storage és a 2. generációs Azure Data Lake a Azure Stream Analytics kimenetének ismertetése.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 3ce22837da2ca30249b399a297b6188d950b1ea4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935142"
---
# <a name="blob-storage-and-azure-data-lake-gen2-output-from-azure-stream-analytics"></a>BLOB Storage és Azure Data Lake Gen2 kimenete Azure Stream Analytics

A Data Lake Storage Gen2 lehetővé teszi az Azure Storage számára, hogy a vállalati adattavakat az Azure-ban hozza létre. Több petabájt-szolgáltatás megkezdése és kiszolgálása mellett a Data Lake Storage Gen2 segítségével könnyedén kezelhet nagy mennyiségű adatot. Data Lake Storage Gen2 alapvető része egy hierarchikus névtér hozzáadása a blob Storage-hoz.

Az Azure Blob Storage költséghatékony és méretezhető megoldást kínál nagy mennyiségű strukturálatlan adat tárolására a felhőben. A blob Storage és annak használatának bemutatása: Blobok [feltöltése, letöltése és listázása a Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="output-configuration"></a>Kimeneti konfiguráció

A következő táblázat felsorolja a tulajdonságok nevét, valamint a Blobok vagy ADLS Gen2 kimenetek létrehozásához szükséges leírásokat.

| Tulajdonság neve       | Description                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Kimeneti alias        | Egy rövid név, amely lekérdezésekben a lekérdezés kimenetének a blob-tárolóba történő irányítására szolgál. |
| A(z)     | Annak a Storage-fióknak a neve, ahová a kimenetet küldi.               |
| Storage-fiók kulcsa | A Storage-fiókhoz társított titkos kulcs.                              |
| Storage-tároló   | Az Azure Blob serviceban tárolt Blobok logikai csoportosítása. Amikor feltölt egy blobot a Blob serviceba, meg kell adnia egy tárolót a blobhoz. |
| Elérésiút-minta | Választható. A fájl elérési útjának mintája, amely a Blobok megadott tárolón belüli írásához használatos. <br /><br /> Az elérési út mintájában dönthet úgy, hogy a dátum és idő változók egy vagy több példányát használja a Blobok írásának megadásához: <br /> {Date}, {Time} <br /><br />Egyéni blob-particionálással megadhat egy egyéni {Field} nevet az esemény adataiból a Blobok particionálásához. A mező neve alfanumerikus, és tartalmazhat szóközt, kötőjelet és aláhúzást. Az egyéni mezőkre vonatkozó korlátozások a következők: <ul><li>A mezőnevek nem megkülönböztetik a kis-és nagybetűket. A szolgáltatás például nem tud különbséget tenni az "ID" oszlop és az "id" oszlop között.</li><li>Beágyazott mezők használata nem engedélyezett. Ehelyett használjon egy aliast a feladatokhoz a lekérdezésben a mező kisimításához.</li><li>A kifejezések nem használhatók mező neveként.</li></ul> <br />Ez a szolgáltatás lehetővé teszi az egyéni Dátum-/időformátumok megadott beállításainak használatát az elérési úton. Az egyéni dátum-és időformátumokat egyszerre kell megadni, a (z) {datetime: \<specifier> } kulcsszóval elkerítve. Az engedélyezett bemenetek a \<specifier> következők: ÉÉÉÉ, hh, M, DD, d, hh, H, mm, M, SS vagy s. A (z) {datetime: \<specifier> } kulcsszó többször is használható az elérési útban az egyéni dátum/idő konfigurációkhoz. <br /><br />Angol nyelvű Példák: <ul><li>1. példa: cluster1/naplók/{Date}/{Time}</li><li>2. példa: cluster1/naplók/{Date}</li><li>3. példa: cluster1/{client_id}/{Date}/{Time}</li><li>4. példa: cluster1/{datetime: SS}/{myField}, ahol a lekérdezés: válassza az adatok. myField lehetőséget myField a bemenettől;</li><li>5. példa: cluster1/év = {datetime: ÉÉÉÉ}/hónap = {datetime: PP}/nap = {datetime: DD}</ul><br />A létrehozott mappa struktúrájának időbélyegzője az UTC és a helyi idő szerint történik. A [System. timestamp](./stream-analytics-time-handling.md#choose-the-best-starting-time) az időalapú particionáláshoz használt idő.<br /><br />A fájlok elnevezése a következő konvenciót használja: <br /><br />{Path előtag-minta}/schemaHashcode_Guid_Number. bővítmény<br /><br /> Itt a GUID a blob-fájlba íráshoz létrehozott belső író egyedi azonosítóját jelöli. A szám a blob-blokk indexét jelöli. <br /><br /> Példa kimeneti fájlokra:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />További információ erről a szolgáltatásról: [Azure stream Analytics egyéni blob kimeneti particionálás](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Dátumformátum | Választható. Ha a Date tokent az előtag elérési útján használja, kiválaszthatja a fájlok rendszerezésének dátumát. Példa: éééé/hh/nn |
| Időformátum | Választható. Ha az időtokent az előtag elérési útja használja, adja meg a fájlok rendszerezésének időformátumát. Jelenleg az egyetlen támogatott érték a HH. |
| Eseményszerializációs formátum | A kimeneti adatokat a szerializálási formátum. A JSON, a CSV, a Avro és a Parquet támogatott. |
|Minimális sorok |A kötegek minimális sorainak száma. A parketta esetében minden köteg létrehoz egy új fájlt. Az aktuális alapértelmezett érték 2 000 sor, és az engedélyezett maximális érték 10 000 sor.|
|Maximális idő |A maximális várakozási idő tételenként. Ezt követően a köteg a kimenetbe kerül, még akkor is, ha a minimális sorokra vonatkozó követelmény nem teljesül. Az aktuális alapértelmezett érték 1 perc, az engedélyezett maximum pedig 2 óra. Ha a blob kimenetének elérésiút-mintázati gyakorisága van, a várakozási idő nem lehet nagyobb, mint a partíció időtartománya.|
| Encoding    | Ha CSV-vagy JSON-formátumot használ, meg kell adni egy kódolást. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| Elválasztó   | Csak a CSV-szerializáláshoz alkalmazható. Stream Analytics számos gyakori elhatárolót támogat a CSV-adattárolás szerializálásához. A támogatott értékek a következők: vessző, pontosvessző, szóköz, TAB és függőleges sáv. |
| Formátum      | Csak a JSON-szerializáláshoz alkalmazható. A **sor elválasztva** érték azt jelenti, hogy a kimenet formázása úgy történik, hogy minden JSON-objektum új sorral van elválasztva. Ha a **sor elválasztva** lehetőséget választja, a JSON egyszerre csak egy objektumot olvas be. A teljes tartalom önmagában nem érvényes JSON. A **Array** beállítás azt adja meg, hogy a kimenet a JSON-objektumok tömbje legyen formázva. Ezt a tömböt csak akkor zárja be a rendszer, ha a feladatot leállítja vagy Stream Analytics áthelyezte a következő időszakra. Általánosságban elmondható, hogy a rendszer inkább a sortöréses JSON-t használja, mert nem igényel semmilyen speciális kezelést, amíg a kimeneti fájl továbbra is írásra kerül. |

## <a name="blob-output-files"></a>BLOB kimeneti fájljai

Ha a blob Storage-ot kimenetként használja, a következő esetekben létrejön egy új fájl a blobban:

* Ha a fájl túllépi az engedélyezett blokkok maximális számát (jelenleg 50 000). Elérheti a maximálisan megengedett számú blokkot anélkül, hogy elérte a Blobok maximálisan megengedett méretét. Ha például a kimeneti sebesség magas, több bájt/blokk látható, és a fájlméret nagyobb. Ha a kimeneti sebesség alacsony, minden blokk kevesebb adattal rendelkezik, és a fájlméret kisebb.
* Ha a kimenetben séma változik, és a kimeneti formátumhoz rögzített séma szükséges (CSV, Avro, Parque).
* Ha egy feladat újra lett indítva, vagy külsőleg egy felhasználó leállítja és elindítja, vagy belsőleg a rendszerkarbantartáshoz vagy a hiba helyreállításához.
* Ha a lekérdezés teljesen particionálva van, és minden kimeneti partícióhoz létrejön egy új fájl.
* Ha a felhasználó töröl egy fájlt vagy egy tárolót a Storage-fiókból.
* Ha a kimenet időkorlátja az elérési út előtagja, és a rendszer egy új blobot használ, amikor a lekérdezés a következő órára kerül.
* Ha a kimenetet egy egyéni mező particionálja, és egy partíciós kulcsban létrehoz egy új blobot, ha az nem létezik.
* Ha a kimenetet egy olyan egyéni mező particionálja, amelyben a partíciós kulcs kardinális mérete meghaladja az 8 000-ot, és a partíciós kulcs egy új blobot hoz létre.

## <a name="partitioning"></a>Particionálás

A partíciós kulcshoz használja a {Date} és az {Time} tokent az esemény mezőiből az elérésiút-mintában. Válassza ki a dátumformátum (például éééé/hh/nn, nn/hh/éééé vagy hh-nn-éééé). A HH az időformátumot használja. A blob kimenete a (z) {mezőnév} vagy {datetime:} egyedi Event attribútummal particionálható \<specifier> . A kimeneti írók száma követi a [teljes párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md)bemeneti particionálását.

## <a name="output-batch-size"></a>Kimeneti köteg mérete

Az üzenetek maximális méretével kapcsolatban lásd: az [Azure Storage korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). A blob-blokk maximális mérete 4 MB, a Blobok maximális száma pedig 50 000. |

## <a name="next-steps"></a>Következő lépések

* [A felügyelt identitás (előzetes verzió) használatával hitelesítheti a Azure Stream Analytics feladatot az Azure-ban Blob Storage](blob-output-managed-identity.md)
* [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)
