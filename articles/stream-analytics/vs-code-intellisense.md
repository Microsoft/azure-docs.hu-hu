---
title: IntelliSense Azure Stream Analytics Visual Studio Code-eszközökön
description: Ez a cikk azt ismerteti, hogyan használható az IntelliSense funkció a Azure Stream Analytics Tools for Visual Studio Code-ban.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: how-to
ms.openlocfilehash: 988b9d364f09a4c88bcaab1fe81ae80c8de5d4d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96491570"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense Azure Stream Analytics Visual Studio Code-eszközökön

Az IntelliSense [stream Analytics lekérdezési nyelvhez](/stream-analytics-query/stream-analytics-query-language-reference?bc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fbread%2ftoc.json&toc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fstream-analytics%2ftoc.json) érhető el a [vs Code Azure stream Analytics eszközökben](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview). Az IntelliSense egy kód-kiegészítési támogatás, amely számos funkciót tartalmaz: tagok listázása, a paraméterek adatai, a gyors információ és a teljes szó. Az IntelliSense szolgáltatásait más nevek is meghívják, például a "kód befejezése", a "Content Assist" és a "Code Hinting".

![IntelliSense-bemutató](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense-funkciók

A VS Code Stream Analytics eszközeinek IntelliSense-funkcióit egy nyelvi szolgáltatás látja el. A nyelvi szolgáltatás elemzi a forráskódot, és intelligens kódokat biztosít a nyelvi szemantika alapján. Ha egy nyelvi szolgáltatás képes a lehetséges befejezésre, az IntelliSense-javaslatok beírásakor. Ha folytatja a beírást, a rendszer szűri a tagok, például a változók és a metódusok listáját úgy, hogy csak olyan tagokat tartalmazzon, amelyek tartalmazzák a beírt karaktereket. Ha megnyomja a `Tab` vagy a `Enter` billentyűt, az IntelliSense beszúrja a kiválasztott tagot.

Az IntelliSense bármely szerkesztő ablakban aktiválható egy trigger karakter, például a pont karakter beírásával `.` .

![IntelliSense-Újratöltés](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> A javaslatok widget támogatja a CamelCase szűrést. Megadhatja, hogy a rendszer milyen betűket írjon be a metódusok nevében a javaslatok korlátozása érdekében. A "CRA" például gyorsan felveszi a "létrehozás".

### <a name="types-of-completions"></a>A Befejezés típusai

A VS Code IntelliSense Stream Analytics eszközei különböző típusú kiegészítéseket biztosítanak, beleértve a nyelvi kiszolgáló javaslatait, a kódrészleteket és az egyszerű, Word-alapú szöveges kiegészítéseket.

|Befejezés     |  Típus       |
| ----- | ------- |
| Kulcsszavak | `keyword`
| Functions | `build-in function`, `user defined function`  |
| Adathalmaz neve| `input`, `output`, `intermediate result set`|
| Adathalmaz oszlopának neve|`input`, `intermediate result set`|

#### <a name="name-completion"></a>Név befejezése

A kulcsszó automatikus kiegészítése mellett a VS Code-hoz készült Stream Analytics Tools beolvassa a feladatok bemeneti és kimeneti neveinek listáját, valamint az adatforrásokban lévő oszlopok nevét, amikor azok konfigurálva vannak. A bővítmény ezeket az információkat olyan név-kiegészítési képességek biztosítására írja elő, amelyek néhány billentyűleütéssel rendelkező utasítások megadásához hasznosak:

A kódolás során nem kell elhagynia a szerkesztőt, hogy végezzen keresést a feladatok bemeneti nevein, a kimeneti névben és az oszlopnevek. Megtarthatja a környezetét, megkeresheti a szükséges információkat, elemeket szúrhat be közvetlenül a kódjába, és az IntelliSense befejezi a gépelést.

Vegye figyelembe, hogy helyi bevitelt vagy élő bemenetet kell konfigurálnia, és mentenie kell a konfigurációs fájlt, hogy használni tudja a név befejezését.

![név befejezése](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Paraméter adatai

Az IntelliSense- **paraméter adatai** lehetőség megnyit egy paraméter-listát, amely információkat biztosít a függvények által igényelt paraméterek számáról, nevéről és típusairól. A félkövérrel szedett paraméter a függvény beírásakor szükséges következő paramétert jelzi.

A beágyazott függvények esetében a paraméterek listája is megjelenik. Ha a függvényt paraméterként adja meg egy másik függvénynek, a paraméterek listája a belső függvény paramétereit jeleníti meg. Ezután, amikor a belső függvény paraméterének listája elkészült, a paraméterek listája visszaállítja a külső függvény paramétereinek megjelenítéséhez.

![paraméter adatai](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Gyors információ

A nyelvi szolgáltatás által biztosítottak szerint az egyes azonosítók **gyors információi** láthatók a kódban. Néhány példa az azonosítók bemenetre, kimenetre, köztes eredményhalmaz vagy függvényre. Amikor az egérmutatót egy azonosító fölé helyezi, a deklaráció megjelenik egy előugró ablakban. A bemenetek tulajdonságai és adatsémái, ha be van állítva, és a köztes adatkészlet látható.

![gyors információ](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>IntelliSense-hibák

Ezt a problémát a hiányzó bemeneti konfiguráció okozta, amely adatokat biztosít. Megtekintheti, hogy a [helyi bemenet](visual-studio-code-local-run.md#define-a-local-input) vagy az [élő bemenet](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) megfelelően van-e konfigurálva.

## <a name="next-steps"></a>Következő lépések

* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio Code-ban](quick-create-visual-studio-code.md)
* [A Visual Studio Code segítségével helyileg tesztelheti Stream Analytics lekérdezéseket a mintaadatok használatával](visual-studio-code-local-run.md)
* [A Visual Studio Code használatával helyileg tesztelheti Stream Analytics lekérdezéseket élő stream-bevitelsel](visual-studio-code-local-run-live-input.md)