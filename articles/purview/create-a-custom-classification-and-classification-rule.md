---
title: Egyéni besorolási és besorolási szabály létrehozása (előzetes verzió)
description: Ismerje meg, hogyan hozhat létre egyéni besorolásokat az adattípusok definiálásához az adatkészletben, amelyek egyediek a szervezet számára az Azure hatáskörébe.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 3/24/2021
ms.openlocfilehash: 7d6baee49250509e50cdeeea8cf8ca6cec5b362d
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222156"
---
# <a name="custom-classifications-in-azure-purview"></a>Egyéni besorolások az Azure hatáskörébe

Ez a cikk azt ismerteti, hogyan hozhat létre egyéni besorolásokat az adattípusok definiálásához az adatkészletben, amelyek egyediek a szervezet számára. Emellett leírja az egyéni besorolási szabályok létrehozását is, amelyek lehetővé teszik, hogy a megadott adatait az adatközpontban találja.

## <a name="default-classifications"></a>Alapértelmezett besorolások

Az Azure hatáskörébe Data Catalog az alapértelmezett besorolások nagy készletét biztosítja, amelyek az adatközpontban esetlegesen használt személyes adattípusokat jelölik.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="besorolás kiválasztása" border="true":::

Lehetősége van egyéni besorolások létrehozására is, ha az alapértelmezett besorolások bármelyike nem felel meg az igényeinek.

## <a name="steps-to-create-a-custom-classification"></a>Egyéni besorolás létrehozásának lépései

Egyéni besorolás létrehozásához kövesse az alábbi lépéseket:

1. A katalógusból válassza a bal oldali menü **felügyeleti központ** elemét.

2. A **metaadatok kezelése** területen válassza a **besorolások** lehetőséget.

3. Válassza az **+ új** lehetőséget

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="Új besorolás" border="true":::

Megnyílik az **új besorolás hozzáadása** panel, ahol megadhatja a besorolás nevét és leírását. Érdemes egy név-térköz konvenciót használni, például: `your company name.classification name` .

A Microsoft rendszerbesorolások a fenntartott névtér alatt vannak csoportosítva `MICROSOFT.` . Ilyen például a **Microsoft. Kormány. USA. TÁRSADALOMBIZTOSÍTÁSI \_ \_ szám**.

A besorolás nevének betűvel kell kezdődnie, amelyet betűk, számok és pont (.) vagy aláhúzás karakteres karaktersorozat követ. Nem engedélyezett szóközök. A gépelés során az UX automatikusan létrehoz egy rövid nevet. Ez a felhasználóbarát név az, amit a felhasználók látnak, amikor alkalmazza azt egy eszközre a katalógusban.

A név rövid megtartásához a rendszer a következő logika alapján hozza létre a felhasználóbarát nevet:

- A rendszer a névtér utolsó két szegmensét is kivágja.

- A burkolat úgy van beállítva, hogy az egyes szavak első betűje tőkésítve legyen. Az összes többi betűt kisbetűvé alakítja.

- Az összes aláhúzás ( \_ ) szóközre van cserélve.

Ha például a besorolási CONTOSO.HR nevezték el **. Az alkalmazott \_ azonosítója**, a felhasználóbarát név a rendszer **HR. Employee ID** néven tárolódik.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

Kattintson **az OK gombra**, és az új besorolás hozzá lesz adva a besorolási listához.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="Egyéni besorolás" border="true":::

Ha kiválasztja a besorolást a listában, megnyílik a besorolás részletei lap. Itt megtalálja a besorolás részleteit.

Ezek az adatok tartalmazzák a példányok számát, a formális nevet, a társított besorolási szabályokat (ha vannak ilyenek) és a tulajdonos nevét.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="Besorolás kiválasztása" border="true":::

## <a name="custom-classification-rules"></a>Egyéni besorolási szabályok

A Catalog szolgáltatás az alapértelmezett besorolási szabályok készletét biztosítja, amelyet a képolvasó az egyes adattípusok automatikus észlelésére használ. A saját egyéni besorolási szabályait is hozzáadhatja más típusú adattípusok észleléséhez, amelyeket érdemes lehet megkeresni az adatközpontban. Ez a képesség akkor lehet hatékony, ha az adatbirtokon belül próbál meg megkeresni egy adatmennyiséget.

Tegyük fel például, \' hogy egy contoso nevű vállalat olyan alkalmazotti azonosítókkal rendelkezik, amelyek a vállalaton belül szabványosítva vannak a szó \" alkalmazottal, \" majd egy GUID-t a (z) {GUID} alkalmazott létrehozásához. Például az alkalmazotti azonosító egy példánya úgy néz ki, mint `EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55` .

A contoso úgy konfigurálhatja a vizsgálati rendszert, hogy megkeresse az azonosítók példányait egy egyéni besorolási szabály létrehozásával. Olyan reguláris kifejezést is megadhatnak, amely megfelel az adatmintának, ebben az esetben: `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$` . Ha az adatmennyiség általában egy olyan oszlopban található, amelyik ismeri a nevét, például az alkalmazottak \_ azonosítóját vagy az Alkalmazottkód-t, hozzáadhatnak egy oszlop minta reguláris kifejezést, hogy a vizsgálat még pontosabb legyen. A regex például a Employee \_ ID \| AlkalmazottKód.

A vizsgálati rendszer ezt a szabályt használva megvizsgálhatja a tényleges adatokat az oszlopban, és az oszlopnév alapján azonosíthatja az összes olyan példányt, ahol az alkalmazotti azonosító minta található.

## <a name="steps-to-create-a-custom-classification-rule"></a>Egyéni besorolási szabály létrehozásának lépései

Egyéni besorolási szabály létrehozása:

1. Hozzon létre egy egyéni besorolást az előző szakasz utasításait követve. Ezt az egyéni besorolást a besorolási szabály konfigurációjában fogja hozzáadni, hogy a rendszer akkor alkalmazza, ha az oszlop egyezést talál.

2. Válassza ki a **felügyeleti központ** ikonját.

3. Válassza ki a **besorolási szabályok** szakaszt.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classificationrules.png" alt-text="Besorolási szabályok csempe" border="true":::

4. Válassza az **Új** lehetőséget.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/newclassificationrule.png" alt-text="Új besorolási szabály hozzáadása" border="true":::

5. Megnyílik az **új besorolási szabály** párbeszédpanel. Töltse ki a mezőket, és döntse el, hogy **reguláris kifejezési szabályt** vagy **szótári szabályt** szeretne-e létrehozni.

   |Mező     |Leírás  |
   |---------|---------|
   |Név   |    Kötelező. A maximális érték 100 karakter.    |
   |Leírás      |Választható. A maximális érték 256 karakter.    |
   |Besorolás neve    | Kötelező. A legördülő listából válassza ki a besorolás nevét, hogy a rendszer a képolvasót alkalmazza, ha egyezést talál.        |
   |Állapot   |  Kötelező. A beállítások engedélyezve vagy le vannak tiltva. Az alapértelmezés szerint engedélyezve van.    |

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-classification-rule.png" alt-text="Új besorolási szabály létrehozása" border="true":::

### <a name="creating-a-regular-expression-rule"></a>Reguláris kifejezési szabály létrehozása

1. Reguláris kifejezési szabály létrehozásakor a következő képernyő jelenik meg. Feltölthet egy fájlt, amelyet a rendszer a szabályhoz **javasolt regex-minták létrehozásához** fog használni.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-regex-rule.png" alt-text="Új regex-szabály létrehozása" border="true":::

1. Ha úgy dönt, hogy létrehoz egy javasolt regex-mintát, a fájl feltöltése után válasszon ki egy javasolt mintát, és válassza a **Hozzáadás a mintákhoz** lehetőséget a javasolt adattípusok és az oszlopok mintázatának használatához. Megadhatja a javasolt mintákat, vagy a fájl feltöltése nélkül is beírhatja saját mintáit.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/suggested-regex.png" alt-text="Javasolt regex készítése" border="true":::

   |Mező     |Leírás  |
   |---------|---------|
   |Adatminta    |Választható. Egy reguláris kifejezés, amely az adatmezőben tárolt adatértéket jelöli. A korlát nagyon nagy. Az előző példában az adatmintázatok a szó szerint egy alkalmazotti AZONOSÍTÓhoz vannak tesztelve `Employee{GUID}` .  |
   |Oszlop mintája    |Választható. Egy reguláris kifejezés, amely az egyező oszlop nevét jelöli. A korlát nagyon nagy. |

1. Az **adatminta** alatt két küszöbérték állítható be:

   - **Eltérő egyezési küszöbérték**: az oszlopokban a képolvasó által az adatmintázat futtatása előtt megtalált különböző adatértékek teljes száma. A javasolt érték 8. Ez az érték 2 és 32 közötti tartományba állítható be manuálisan. A rendszernek ezt az értéket kell megadnia ahhoz, hogy az oszlop elegendő adatmennyiséget tartalmazzon a képolvasó számára a pontos besoroláshoz. Például egy olyan oszlop, amely az 1 értéket tartalmazó több sort tartalmaz, nem lesz besorolva. Azokat az oszlopokat, amelyek egy értéket tartalmazó sort tartalmaznak, és a sorok többi része null értékű, nem lesz besorolva. Ha több mintázatot ad meg, ez az érték mindenre vonatkozik.

   - **Minimális egyezési küszöb**: ezzel a beállítással megadhatja, hogy a különböző adatértékek milyen minimális százalékos arányban legyenek meghatározva egy olyan oszlopban, amelyet a lapolvasónak kell megkeresnie az alkalmazandó besoroláshoz. A javasolt érték 60%. Ennek a beállításnak Körültekintőnek kell lennie. Ha csökkenti a 60% alatti szintet, akkor előfordulhat, hogy hamis pozitív besorolásokat vezet be a katalógusba. Ha több Adatmintázatot is megad, ez a beállítás le van tiltva, és az érték 60%-on lesz javítva.

1. Most ellenőrizheti a szabályt, és **létrehozhatja** azt.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/verify-rule.png" alt-text="Szabály ellenőrzése a létrehozás előtt" border="true":::

1. A létrehozási folyamat befejezése előtt tesztelje a besorolási szabályt annak ellenőrzéséhez, hogy az alkalmazás címkéket fog-e alkalmazni az eszközeire. A szabályban szereplő besorolásokat a rendszer a vizsgálat során éppen feltöltött mintaadatok alapján alkalmazza. Ez azt jelenti, hogy a rendszer az összes rendszerbesorolást és az egyéni besorolást a fájl adataihoz fogja egyeztetni.

   A bemeneti fájlok tartalmazhatnak tagolt fájlokat (CSV, PSV, SSV, TSV), JSON vagy XML-tartalmat. A tartalom a bemeneti fájl fájlkiterjesztés alapján lesz értelmezve. A tagolt adatmennyiséghez tartozhat egy fájlkiterjesztés, amely megfelel a felsorolt típusok bármelyikének. A TSV-beli adatfájlok például egy MySampleData.csv nevű fájlban létezhetnek. A tagolt tartalomnak is legalább 3 oszlopból kell állnia.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/test-rule-screen.png" alt-text="Szabály tesztelése a létrehozás előtt" border="true":::

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/test-rule-uploaded-file-result-screen.png" alt-text="Alkalmazott besorolások megtekintése egy tesztüzenet feltöltése után" border="true":::

### <a name="creating-a-dictionary-rule"></a>Szótári szabály létrehozása

1. Ha szótári szabályt hoz létre, a következő képernyő jelenik meg. Töltsön fel egy olyan fájlt, amely az egyetlen oszlopban létrehozott besorolás összes lehetséges értékét tartalmazza.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-rule.png" alt-text="Szótári szabály létrehozása" border="true":::

1. A szótár létrehozása után módosíthatja a különböző egyeztetési és a minimális egyezési küszöbértékeket, és elküldheti a szabályt.

- **Eltérő egyezési küszöbérték**: az oszlopokban a képolvasó által az adatmintázat futtatása előtt megtalált különböző adatértékek teljes száma. A különböző egyeztetési küszöbértéknek semmi köze a mintázat megfeleltetéséhez, de ez a minta egyeztetésének előfeltétele. A javasolt érték 8. Ez az érték 2 és 32 közötti tartományba állítható be manuálisan. A rendszernek ezt az értéket kell megadnia ahhoz, hogy az oszlop elegendő adatmennyiséget tartalmazzon a képolvasó számára a pontos besoroláshoz. Például egy olyan oszlop, amely az 1 értéket tartalmazó több sort tartalmaz, nem lesz besorolva. Azokat az oszlopokat, amelyek egy értéket tartalmazó sort tartalmaznak, és a sorok többi része null értékű, nem lesz besorolva. Ha több mintázatot ad meg, ez az érték mindenre vonatkozik.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="Szótár szabály létrehozása Dictionary-Generated pipa jellel." border="true":::

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozta a besorolási szabályt, készen áll a beolvasási szabálykészlet hozzáadására, hogy a vizsgálat a szabályt használja a vizsgálat során. További információkért lásd: [ellenőrzési szabálykészlet létrehozása](create-a-scan-rule-set.md).
