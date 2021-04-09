---
title: Adatok kinyerése az Excelben a Text Analytics és a Power automatizálás használatával
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan kinyerheti az Excel szövegét anélkül, hogy kódot kellene írnia a Text Analytics és a Power automatizálás használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: 197d28b2ac3d94b6639a6611b2919bdeb2b182e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93359901"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Adatok kinyerése az Excelben a Text Analytics és a Power automatizálás használatával 

Ebben az oktatóanyagban létre fog hozni egy automatizálási folyamatot egy Excel-számolótáblában található szöveg kinyeréséhez anélkül, hogy kódot kellene írnia. 

Ez a folyamat egy adott apartmankomplexum által jelentett problémák táblázatát veszi figyelembe, és két kategóriába sorolja azokat: a víz-és egyéb csoportokba. Emellett Kinyeri a nekik elküldett bérlők nevét és telefonszámát. Végül a folyamat hozzáfűzi ezt az információt az Excel-laphoz. 

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * Folyamat létrehozása a Power automatizálás használatával
> * Excel-adatok feltöltése a OneDrive for Businessből
> * Szöveg kinyerése az Excelből, és elküldése a Text Analytics APIba 
> * Az API-ból származó információk használatával frissítheti az Excel-lapokat.

## <a name="prerequisites"></a>Előfeltételek

- Egy Microsoft Azure-fiók. [Hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/) , vagy [Jelentkezzen be](https://portal.azure.com/).
- Egy Text Analytics erőforrás. Ha még nem rendelkezik ilyennel, [létrehozhat egyet a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) , és az oktatóanyag elvégzéséhez használhatja az ingyenes szintet is.
- A regisztráció során létrehozott [kulcs és végpont](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) .
- Bérlői problémákat tartalmazó táblázat. Példa a GitHubon tárolt információkra
- Microsoft 365 a OneDrive for Business szolgáltatással.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Az Excel-fájl hozzáadása a OneDrive for Business szolgáltatáshoz

Töltse le a példában szereplő Excel-fájlt a [githubról](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Ezt a fájlt a OneDrive for Business-fiókban kell tárolni.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Példák az Excel-fájlból.":::

A problémák nyers szövegben jelennek meg. A személy nevének és telefonszámának kinyeréséhez a Text Analytics API elnevezett entitás-felismerést fogjuk használni. Ezután a folyamat a leírásban a "plumbing" szót fogja keresni a hibák kategorizálása érdekében. 

## <a name="create-a-new-power-automate-workflow"></a>Új automatizálási munkafolyamat létrehozása

Lépjen a [Power automatizáló webhelyre](https://preview.flow.microsoft.com/), és jelentkezzen be. Ezután kattintson a **Létrehozás** és **ütemezett folyamat** elemre.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="A folyamat-létrehozási képernyő.":::


Az **ütemezett folyamat összeállítása** lapon inicializálja a folyamatot a következő mezőkkel:

|Mező |Érték  |
|---------|---------|
|**Folyamat neve**     | **Ütemezett felülvizsgálat** vagy más név.         |
|**Indítás**     |  Adja meg az aktuális dátumot és időpontot.       |
|**Ismétlés**     | **1 óra**        |

## <a name="add-variables-to-the-flow"></a>Változók hozzáadása a folyamathoz

> [!NOTE]
> Ha meg szeretné tekinteni a befejezett folyamat képét, letöltheti azt a [githubról](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams). 

Az Excel-fájlba felvenni kívánt adatokat jelölő változók létrehozása. Kattintson az **új lépés** gombra, és keresse meg az **inicializálás változót**. Négy változó létrehozásához hajtsa végre ezt a négy alkalommal.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Változók inicializálása.":::

Adja hozzá a következő adatokat a létrehozott változókhoz. Ők képviselik az Excel-fájl oszlopait. Ha a változók összecsukva vannak, a rájuk kattintva kibonthatja őket.

| Művelet |Név   | Típus | Érték |
|---------|---------|---|---|
| Változó inicializálása | var_person | Sztring | Személy |
| 2. változó inicializálása | var_phone | Sztring | Phone_Number |
| 3. változó inicializálása | var_plumbing | Sztring | bekötések nagy részével |
| 4. változó inicializálása | var_other | Sztring | egyéb | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="a folyamat változói között található információk":::

## <a name="read-the-excel-file"></a>Az Excel-fájl olvasása

Kattintson az **új lépés** gombra, és írja be az **Excel** elemet, majd válassza ki a **táblázatban szereplő sorok listáját** a műveletek listájából.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="Excel-sorok hozzáadása":::

Adja hozzá az Excel-fájlt a folyamathoz a művelet mezőinek kitöltésével. Ez az oktatóanyag megköveteli, hogy a fájl fel legyen töltve a OneDrive for Business szolgáltatásba.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="Excel-sorok kitöltése":::

Kattintson az **új lépés** gombra, és adjon hozzá egy **érvényes műveletet az egyes** műveletekhez.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="Adjon hozzá egy Apply parancsot.":::

Kattintson az **előző lépés kimenetének kiválasztása** elemre. A megjelenő dinamikus tartalom mezőben válassza az **érték** lehetőséget.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Válassza ki az Excel-fájl kimenetét.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Kérelem küldése a Text Analytics APInak

Ha még nem tette meg, létre kell hoznia egy [text Analytics erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) a Azure Portalban.

### <a name="create-a-text-analytics-connection"></a>Text Analytics-kapcsolatok létrehozása

A **minden alkalmazásban** kattintson a **művelet hozzáadása** lehetőségre. Nyissa meg a Text Analytics erőforrás **kulcs és végpont** lapját a Azure Portal, és szerezze be a Text Analytics erőforrás kulcsát és végpontját.

A folyamat során adja meg az alábbi adatokat egy új Text Analytics-kapcsolatok létrehozásához.

> [!NOTE]
> Ha már létrehozott egy Text Analytics-kapcsolatot, és módosítani szeretné a kapcsolat adatait, kattintson a jobb felső sarokban található három pontra, majd kattintson az **+ új kapcsolat hozzáadása** lehetőségre.

| Mező           | Érték                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Kapcsolat neve | A Text Analytics erőforráshoz való kapcsolódás neve. Például: `TAforPowerAutomate`. |
| Fiókkulcs     | A Text Analytics erőforrás kulcsa.                                                                                   |
| Webhely URL-címe        | A Text Analytics erőforrás végpontja.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Text Analytics hitelesítő adatok hozzáadása a folyamathoz.":::

## <a name="extract-the-excel-content"></a>Excel-tartalom kibontása 

A létrehozást követően keresse meg **text Analytics** és válassza az **entitások** lehetőséget. Ezzel kinyeri az adatokat a probléma Leírás oszlopában.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Text Analytics entitások hozzáadása.":::

Kattintson a **szövegmezőbe** , és válassza ki a megjelenő dinamikus tartalom ablak **Leírás** elemét. Adja meg `en` a nyelvet. (Kattintson a speciális beállítások megjelenítése elemre, ha nem látja a nyelvet)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Text Analytics beállítások hozzáadása.":::


## <a name="extract-the-person-name"></a>A személy nevének kinyerése

Ezután megtaláljuk a személy entitás típusát a Text Analytics kimenetében. A **hatókörben** kattintson a **művelet hozzáadása** lehetőségre, és hozzon létre egy másikat az **egyes** műveletekhez. Kattintson a szövegmezőbe, majd a megjelenő dinamikus tartalom ablakban válassza az **entitások** lehetőséget.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Text Analytics hitelesítő adatok hozzáadása a folyamathoz. 2":::

Az újonnan létrehozott **alkalmazás minden 2** művelethez kattintson a **művelet hozzáadása** lehetőségre, és adjon hozzá egy **feltétel** vezérlőelemet.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Text Analytics hitelesítő adatok hozzáadása a folyamathoz. 3":::

A feltétel ablakban kattintson az első szövegmezőre. A dinamikus tartalom ablakban keresse meg az **entitások típust** , és jelölje ki.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Text Analytics hitelesítő adatok hozzáadása a folyamathoz. 4":::

Győződjön meg arról, hogy a második mező értéke **egyenlő**. Ezután válassza ki a harmadik mezőt, és keresse meg a `var_person` dinamikus tartalom ablakban. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Text Analytics hitelesítő adatok hozzáadása a folyamathoz. 5":::

A **Ha igen** feltétel mezőben írja be az Excelben a **sor frissítése** lehetőséget.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Text Analytics hitelesítő adatok hozzáadása a folyamathoz. 6":::

Adja meg az Excel-adatokat, és frissítse a **kulcs oszlopot**, a **kulcs értékét** és a **PersonName** mezőt. Ez a művelet hozzáfűzi az API által az Excel-táblázathoz észlelt nevet. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Text Analytics hitelesítő adatok hozzáadása a folyamathoz. 7":::

## <a name="get-the-phone-number"></a>Telefonszám beolvasása

A név gombra kattintva csökkentse a **két művelet hatókörét** . Ezután adjon hozzá egy másikat **az egyes** műveletekhez, mint korábban. a nevet a rendszer **a 3 értékre alkalmazza**. Jelölje ki a szövegmezőt, és adja hozzá az **entitásokat** a művelet kimenetéhez. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Text Analytics hitelesítő adatok hozzáadása a folyamathoz. 8":::

A **3. hatókörön** belül adjon hozzá egy **feltétel** vezérlőelemet. Neve **2. feltételnek** számít. Az első szövegmezőben keresse meg és adja hozzá az **entitások típusát** a dinamikus tartalom ablakból. Győződjön meg arról, hogy a középső mező értéke **egyenlő**. Ezután a jobb oldali szövegmezőbe írja be a szöveget `var_phone` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Text Analytics hitelesítő adatok hozzáadása a folyamathoz. 9":::

A **Ha igen** állapotban adja hozzá a **sor frissítése** műveletet. Ezután adja meg a fentiekhez hasonló adatokat az Excel-táblázat telefonszámok oszlopában. Ezzel hozzáfűzi az API által észlelt telefonszámot az Excel-táblázathoz. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Text Analytics hitelesítő adatok hozzáadása a folyamathoz. 10":::


## <a name="get-the-plumbing-issues"></a>A vízvezetés problémáinak beolvasása

A név elemre kattintva csökkentse a **3** . Ezután hozzon **létre egy másikat a fölérendelt** műveletben. Jelölje ki a szövegmezőt, és adja hozzá az **entitásokat** a művelet kimenetként a dinamikus tartalom ablakból. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Text Analytics hitelesítő adatok hozzáadása a folyamathoz. 11":::


Ezután a folyamat azt vizsgálja, hogy az Excel-táblázatból származó probléma leírása tartalmazza-e a "plumbing" szót. Ha igen, a IssueType oszlopban a "plumbing" értéket fogja hozzáadni. Ha nem, akkor a "másik" értéket adjuk meg.

Az **alkalmazás minden 4** műveleten belül adjon hozzá egy **feltétel** vezérlőelemet. Neve **3. feltétel**. Az első szövegmezőben keresse meg és adja hozzá a **leírást** az Excel-fájlból a dinamikus tartalom ablak használatával. Győződjön meg arról, hogy a Center Box azt **tartalmazza**. Ezután a jobb oldali szövegmezőben keresse meg és válassza ki a elemet `var_plumbing` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Text Analytics hitelesítő adatok hozzáadása a folyamathoz. 12":::


A **Ha igen** állapotban kattintson a **művelet hozzáadása** lehetőségre, majd válassza **a sor frissítése** lehetőséget. Ezután adja meg a korábban megjelenő adatokat. A IssueType oszlopban válassza a elemet `var_plumbing` . Ez egy "plumbing" címkét fog alkalmazni a sorban.

A **Ha nincs** feltételben kattintson a **művelet hozzáadása** lehetőségre, majd válassza **a sor frissítése** lehetőséget. Ezután adja meg a korábban megjelenő adatokat. A IssueType oszlopban válassza a elemet `var_other` . Ez egy "másik" címkét fog alkalmazni a sorra.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Text Analytics hitelesítő adatok hozzáadása a folyamathoz. 13":::

## <a name="test-the-workflow"></a>A munkafolyamat tesztelése

A képernyő jobb felső sarkában kattintson a **Mentés**, majd a **teszt** elemre. Jelölje be  **az trigger művelet végrehajtása műveletet**. Kattintson a **mentés & teszt**, **folyamat futtatása**, majd a **kész** lehetőségre.

Az Excel-fájl frissülni fog a OneDrive-fiókjában. A következőhöz hasonlóan fog kinézni.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="A frissített Excel-táblázat.":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További megoldások megismerése](../text-analytics-user-scenarios.md)
