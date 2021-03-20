---
title: 1. generációs kimenet Azure Data Lake Storage Azure Stream Analytics
description: Ez a cikk az 1. generációs Azure Data Lake Storage Azure Stream Analytics kimeneti beállítását ismerteti.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 629a154c89ad301a3e200b1d6cd04c62057d9959
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016541"
---
# <a name="azure-data-lake-storage-gen-1-output-from-azure-stream-analytics"></a>1. generációs kimenet Azure Data Lake Storage Azure Stream Analytics

A Stream Analytics támogatja a [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md) kimeneteket. A Azure Data Lake Storage egy nagyvállalati szintű, nagy kapacitású adattár big data analitikus számítási feladatokhoz. A Data Lake Storage használatával bármilyen méretű, típusú és feldolgozási sebességű adatot tárolhat az operatív és a felderítő elemzésekhez. Stream Analytics jogosultsággal kell rendelkeznie a Data Lake Storage eléréséhez.

Stream Analytics Azure Data Lake Storage kimenete nem érhető el az Azure China 21Vianet és az Azure Germany (T-Systems International) régióiban.

## <a name="output-configuration"></a>Kimeneti konfiguráció

A következő táblázat felsorolja a tulajdonságok nevét és a hozzájuk tartozó leírásokat a Data Lake Storage 1. generációs kimenetének konfigurálásához.

| Tulajdonság neve | Description |
| --- | --- |
| Kimeneti alias | Egy rövid név, amely lekérdezésekben a lekérdezés kimenetének Data Lake Storeba való irányítására szolgál. |
| Előfizetés | Az Azure Data Lake Storage-fiókot tartalmazó előfizetés. |
| Fióknév | Annak a Data Lake Store-fióknak a neve, ahová a kimenetet küldi. Az előfizetésében elérhető Data Lake Store fiókok legördülő listája jelenik meg. |
| Elérésiút-előtag mintája | A fájlnak a megadott Data Lake Store fiókban való írásához használt elérési út. Megadhatja a (z) {date} és a {Time} változók egy vagy több példányát:<br /><ul><li>1. példa: Mappa1/naplók/{Date}/{Time}</li><li>2. példa: Mappa1/naplók/{Date}</li></ul><br />A létrehozott mappa struktúrájának időbélyegzője az UTC és a helyi idő szerint történik.<br /><br />Ha a fájl elérési útjának mintája nem tartalmaz záró perjelet (/), a fájl elérési útjának utolsó mintázata fájlnév-előtagként lesz kezelve. <br /><br />Az új fájlok a következő esetekben jönnek létre:<ul><li>Változás a kimeneti sémában</li><li>A feladatok külső vagy belső újraindítása</li></ul> |
| Dátumformátum | Választható. Ha a Date tokent az előtag elérési útján használja, kiválaszthatja a fájlok rendszerezésének dátumát. Példa: éééé/hh/nn |
|Időformátum | Választható. Ha az időtokent az előtag elérési útja használja, adja meg a fájlok rendszerezésének időformátumát. Jelenleg az egyetlen támogatott érték a HH. |
| Eseményszerializációs formátum | A kimeneti adatmennyiség szerializálási formátuma. A JSON, a CSV és a Avro támogatottak.|
| Encoding | Ha CSV-vagy JSON-formátumot használ, meg kell adni egy kódolást. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum.|
| Elválasztó | Csak a CSV-szerializáláshoz alkalmazható. Stream Analytics számos gyakori elhatárolót támogat a CSV-adattárolás szerializálásához. A támogatott értékek a következők: vessző, pontosvessző, szóköz, TAB és függőleges sáv.|
| Formátum | Csak a JSON-szerializáláshoz alkalmazható. A **sor elválasztva** érték azt jelenti, hogy a kimenet formázása úgy történik, hogy minden JSON-objektum új sorral van elválasztva. Ha a **sor elválasztva** lehetőséget választja, a JSON egyszerre csak egy objektumot olvas be. A teljes tartalom önmagában nem érvényes JSON.  A **Array** beállítás azt adja meg, hogy a kimenet a JSON-objektumok tömbje legyen formázva. Ezt a tömböt csak akkor zárja be a rendszer, ha a feladatot leállítja vagy Stream Analytics áthelyezte a következő időszakra. Általánosságban elmondható, hogy a rendszer inkább a sortöréses JSON-t használja, mert nem igényel semmilyen speciális kezelést, amíg a kimeneti fájl továbbra is írásra kerül.|
| Hitelesítési módszer | A Data Lake Storage-fiók hozzáférését [felügyelt identitás](stream-analytics-managed-identities-adls.md) (előzetes verzió) vagy felhasználói jogkivonat használatával engedélyezheti. A hozzáférés engedélyezése után visszavonhatja a hozzáférést a felhasználói fiók jelszavának módosításával, a feladathoz tartozó Data Lake Storage kimenet törlésével vagy a Stream Analyticsi feladatok törlésével. |

## <a name="partitioning"></a>Particionálás

A partíciós kulcshoz használja a {Date} és az {Time} tokent az elérési út előtagja mintában. Válasszon dátumformátum, például éééé/hh/nn, nn/hh/éééé vagy HH-NN-ÉÉÉÉ. A HH formátumot használja az időformátumhoz. A kimeneti írók száma követi a [teljes párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md)bemeneti particionálását.

## <a name="output-batch-size"></a>Kimeneti köteg mérete

Az üzenetek maximális méretéhez lásd: [Data Lake Storage korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits). A köteg méretének optimalizálása érdekében írási művelet esetén akár 4 MB-ot is használhat.

## <a name="next-steps"></a>Következő lépések

* [Stream Analytics hitelesítése Azure Data Lake Storage Gen1 felügyelt identitások használatával (előzetes verzió)](stream-analytics-managed-identities-adls.md)
* [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)