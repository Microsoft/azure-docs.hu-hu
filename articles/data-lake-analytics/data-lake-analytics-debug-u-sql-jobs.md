---
title: A C#-kód hibakeresése Azure Data Lake U-SQL-feladatokhoz
description: Ez a cikk azt ismerteti, hogyan lehet hibakeresést végezni a U-SQL-ben a Azure Data Lake Tools for Visual Studio használatával.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/30/2017
ms.openlocfilehash: db1d57e3904087bc5cb3711b23cfe6bcf18c3455
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218017"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Felhasználó által definiált C# kód hibakeresése a sikertelen U-SQL-feladatokhoz

Az U-SQL bővíthető modellt biztosít a C# használatával. A U-SQL-szkriptekben könnyen hívhat C#-függvényeket, és olyan analitikai funkciókat hajthat végre, amelyek az SQL-hez hasonló deklaratív nyelv nem támogatott. Ha többet szeretne megtudni az U-SQL bővíthetőségéről, tekintse meg a [u-SQL programozható útmutatóját](./data-lake-analytics-u-sql-programmability-guide.md#use-user-defined-functions-udf). 

A gyakorlatban előfordulhat, hogy bármely kód hibakeresést igényel, de a felhőben lévő, korlátozott naplófájlokkal rendelkező elosztott feladatok nem végezhetők el egyéni kóddal. A [Visual studióhoz készült Azure Data Lake Tools](https://aka.ms/adltoolsvs) egy **sikertelen csúcspont-hibakereső** nevű szolgáltatást biztosít, amely megkönnyíti az Egyéni kódban előforduló hibák hibakeresését. Ha a U-SQL-feladatok sikertelenek, a szolgáltatás megtartja a meghibásodási állapotot, és az eszköz segítségével letöltheti a Felhőbeli meghibásodási környezetet a helyi gépre a hibakereséshez. A helyi Letöltés rögzíti a teljes felhőalapú környezetet, beleértve a bemeneti adatokat és a felhasználói kódokat is.

Az alábbi videó azt mutatja be, hogy a Visual studióhoz készült Azure Data Lake-eszközök nem felelnek meg a vertex hibakeresésének.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> A Visual Studio a következő két frissítést igényli a szolgáltatás használatához: [Microsoft Visual C++ 2015 újraterjeszthető frissítés 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) és a [Windows rendszerhez készült univerzális C futtatókörnyezet](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>A sikertelen csúcspont letöltése a helyi gépre

Ha egy sikertelen feladatot nyit meg Azure Data Lake Tools for Visual Studio alkalmazásban, a hiba lapon egy sárga riasztási sáv jelenik meg, amely részletes hibaüzeneteket jelenít meg.

1. Kattintson a **Letöltés** gombra az összes szükséges erőforrás és bemeneti adatfolyam letöltéséhez. Ha a letöltés nem fejeződött be, kattintson az **újra** gombra.

2. A letöltés befejezése után kattintson az **Open (Megnyitás** ) gombra a helyi hibakeresési környezet létrehozásához. Új hibakeresési megoldás lesz megnyitva, és ha meglévő megoldás van megnyitva a Visual Studióban, győződjön meg róla, hogy a hibakeresés előtt menti és zárja be azt.

![A Visual Studio letöltési csúcspontjának Azure Data Lake Analytics U-SQL hibakeresése](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>A hibakeresési környezet konfigurálása

> [!NOTE]
> A hibakeresés előtt győződjön meg arról, hogy a kivétel-beállítások ablakban a **gyakori nyelvi futtatókörnyezeti kivételeket** szeretné ellenőrizni (**CTRL + ALT + E**).

![Azure Data Lake Analytics U-SQL hibakeresési Visual Studio-beállítás](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

Az új Visual Studio-példányban előfordulhat, hogy nem találja a felhasználó által definiált C#-forráskódot:

1. [A forráskódom megtalálható a megoldásban](#source-code-is-included-in-debugging-solution)

2. [Nem találom a forráskódot a megoldásban](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>A forráskód a hibakeresési megoldás része.

A C# forráskód két esetben van rögzítve:

1. A felhasználói kód a kód mögötti fájlban van definiálva (általában `Script.usql.cs` U-SQL-projektben szerepel).

2. A felhasználói kód az U-SQL-alkalmazáshoz tartozó C# Class Library-projektben van definiálva, és **hibakeresési információkkal** együtt regisztrálva van.

Ha a forráskódot importálja a megoldásba, a probléma elhárításához használhatja a Visual Studio hibakereső eszközeit (megtekintés, változók stb.):

1. Nyomja le az **F5** billentyűt a hibakeresés megkezdéséhez. A kód addig fut, amíg a kivétel nem szűnik meg.

2. Nyissa meg a forráskód-fájlt, és állítsa be a töréspontokat, majd nyomja le az **F5** billentyűt a kód lépésről lépésre történő hibakereséséhez.

    ![Azure Data Lake Analytics U-SQL hibakeresési kivétel](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>A forráskód nem szerepel a hibakeresési megoldásban

Ha a felhasználói kód nem szerepel a kód mögötti fájlban, vagy nem regisztrálta a szerelvényt a **hibakeresési adatokkal**, akkor a rendszer nem tartalmazza automatikusan a forráskódot a hibakeresési megoldásban. Ebben az esetben további lépések szükségesek a forráskód hozzáadásához:

1. Kattintson a jobb gombbal a **"VertexDebug" megoldásra, > adjon hozzá > meglévő projektet...** a szerelvény forráskódjának megkereséséhez és a projekt hozzáadásához a hibakeresési megoldáshoz.

    ![Azure Data Lake Analytics U-SQL-hibakeresési projekt hozzáadása](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. A **FailedVertexDebugHost** projekthez tartozó Project mappa elérési útjának beolvasása. 

3. Right-Click **a hozzáadott Assembly forráskód-projekt > tulajdonságai** elemre, válassza ki a **Build** fület a bal oldalon, majd illessze be a \Bin\Debug végződésű másolt elérési utat **kimeneti > kimeneti útvonalként**. A végső kimeneti útvonal hasonló `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\` .

    ![Az U-SQL hibakeresési készletének Azure Data Lake Analytics az PDB elérési útja](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Ezeket a beállításokat az **F5** és a töréspontok hibakeresésével indíthatja el. A probléma elhárításához használhatja a Visual Studio hibakereső eszközeit (megtekintés, változók stb.) is.

> [!NOTE]
> Minden alkalommal újra létre kell hoznia a szerelvény forráskódjának projektjét, miután módosította a kódot a frissített. pdb fájlok létrehozásához.

## <a name="resubmit-the-job"></a>A feladatoknak a újraküldése

A hibakeresést követően, ha a projekt sikeresen befejeződik, a kimeneti ablak a következő üzenetet jeleníti meg:

`The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).`

![Azure Data Lake Analytics U-SQL-hibakeresés sikeres](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

A sikertelen feladatok újraküldése:

1. A kód mögötti megoldásokkal rendelkező feladatok esetében másolja a C#-kódot a forráskód mögötti fájlba (jellemzően `Script.usql.cs` ).

2. A szerelvényekkel rendelkező feladatok esetében kattintson a jobb gombbal a szerelvény forráskódjának projektre a hibakeresési megoldásban, és regisztrálja a frissített. dll szerelvényeket a Azure Data Lake-katalógusban.

3. Küldje el újra az U-SQL-feladatot.

## <a name="next-steps"></a>Következő lépések

- [Az U-SQL programozhatósági útmutatója](data-lake-analytics-u-sql-programmability-guide.md)
- [A felhasználó által definiált U-SQL-operátorok fejlesztése Azure Data Lake Analytics feladatokhoz](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [U-SQL feladatok tesztelése és hibakeresése helyi futtatással és az Azure Data Lake U-SQL SDK használatával](data-lake-analytics-data-lake-tools-local-run.md)
- [Rendellenes ismétlődő feladatok hibáinak megoldása](data-lake-analytics-data-lake-tools-debug-recurring-job.md)