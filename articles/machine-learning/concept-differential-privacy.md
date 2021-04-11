---
title: Differenciált adatvédelem a gépi tanulásban (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy mi a különbségi adatvédelem, és hogyan valósítható meg az adatvédelmet megőriző differentially-rendszerek megvalósítása.
author: luisquintanilla
ms.author: luquinta
ms.date: 01/21/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: responsible-ml
ms.openlocfilehash: de85c8184e05c9d662a2854e6138f3a61bfba8de
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062454"
---
# <a name="what-is-differential-privacy-in-machine-learning-preview"></a>Mi a különbségi adatvédelem a Machine learningben (előzetes verzió)

Ismerje meg a gépi tanulásban való differenciált adatvédelmet és működését.

Mivel a szervezet által gyűjtött és az elemzésekhez felhasznált adatok mennyisége növekszik, az adatvédelem és a biztonság terén is felmerülnek. Elemzések szükségesek az adatkezeléshez. Általában a modellek betanításához használt több adattal, annál pontosabban vannak. Ha a rendszer személyes adatokat használ ezekhez az elemzésekhez, különösen fontos, hogy az adatok a használat során is magánjellegűek maradjanak.

## <a name="how-differential-privacy-works"></a>A különbözeti adatvédelem működése

A differenciált adatvédelem olyan rendszerek és eljárások összessége, amelyek segítenek megőrizni a magánszemélyek adatait.

> [!div class="mx-imgBorder"]
> ![Differenciált adatvédelmi gépi tanulási folyamat](./media/concept-differential-privacy/differential-privacy-machine-learning.jpg)

A hagyományos helyzetekben a nyers adatok tárolása a fájlokban és az adatbázisokban történik. Amikor a felhasználók elemeznek az adatelemzést, általában a nyers adattípust használják. Ez aggodalomra ad okot, mert sértheti az egyén adatvédelmét. A differenciált adatvédelem a "zaj" vagy az adatok véletlenszerű törlésével próbálkozik a probléma megoldásával, így a felhasználók nem azonosíthatják az egyes adatpontokat. Legalább egy ilyen rendszer kézenfekvő megtagadást biztosít. Ezért az egyének személyes adatainak megőrzése korlátozott hatással van az adatok pontosságára.

A differentially privát rendszerekben a **lekérdezéseknek** nevezett kérelmeken keresztül osztják meg az adatmegosztást. Amikor egy felhasználó adatlekérdezést küld, az **adatvédelmi mechanizmusként** ismert műveletek a kért adatokat is felvehetik. Az adatvédelmi mechanizmusok a nyers adatok helyett *az adatok közelítését* adják vissza. Ez az adatvédelmi megőrzési eredmény megjelenik egy **jelentésben**. A jelentések két részből állnak, a tényleges adatok kiszámításával és az adatok létrehozásának leírásával.

## <a name="differential-privacy-metrics"></a>Különbözeti adatvédelmi mérőszámok

A differenciált adatvédelem olyan lehetőségekkel igyekszik védeni a felhasználókat, hogy a felhasználók határozatlan számú jelentést hozzanak létre, hogy végül fel tudják fedni a bizalmas adatokat. A **epszilon** néven ismert érték a jelentések zajos vagy magánjellegű jelentését méri. A epszilon fordított kapcsolattal rendelkezik a zajjal vagy az adatvédelemmel kapcsolatban. Minél kisebb a epszilon, annál több a zajos (és magán) az adathalmaz.

A epszilon értékei nem negatívak. Az 1. értékkel rendelkező értékek teljes valószerűség-megtagadást biztosítanak. A fenti 1. számú adat nagyobb kockázatot jelent a tényleges adatok kihatásával szemben. A differentially privát rendszereinek megvalósítása során 0 és 1 közötti epszilon-értékkel rendelkező jelentéseket szeretne készíteni.

Egy másik érték, amely közvetlenül összefügg a epszilon- **különbözettel**. A delta érték azt a valószínűséget jelenti, hogy egy jelentés nem teljesen privát. Minél nagyobb a Delta, annál nagyobb a epszilon. Mivel ezek az értékek korrelálnak, a epszilon gyakrabban van használatban.

## <a name="limit-queries-with-a-privacy-budget"></a>Lekérdezések korlátozása adatvédelmi költségvetéssel

Annak érdekében, hogy az adatvédelem olyan rendszereken történjen, ahol több lekérdezés is engedélyezett, a különbözeti adatvédelem meghatározza a díjszabási korlátot. Ezt a korlátot **adatvédelmi költségvetésnek** nevezzük. Az adatvédelmi költségvetés megakadályozza, hogy az adatok több lekérdezésen keresztül ne legyenek újra létrehozva. Az adatvédelemmel kapcsolatos költségvetések epszilon, jellemzően 1 és 3 között vannak lefoglalva az újraazonosítás kockázatának csökkentése érdekében. A jelentések létrehozásakor az adatvédelmi költségvetés nyomon követheti az egyes jelentések epszilon értékét, valamint az összes jelentés összesítését. Az adatvédelem költségvetésének elköltése vagy kimerülése után a felhasználók már nem férhetnek hozzá az adatokhoz. 

## <a name="reliability-of-data"></a>Az adatbiztonság

Bár az adatvédelem megőrzése a cél, az adatok használhatósága és megbízhatósága terén kompromisszumot kell biztosítani. Az adatelemzés során a pontosság a mintavételezési hibák által bevezetett bizonytalanság mértékét is meggondolhatja. Ez a bizonytalanság általában bizonyos határokon belül esik. A differenciált adatvédelem szempontjából a **pontosság** az adatok megbízhatóságát méri, amelyet az adatvédelmi mechanizmusok által bevezetett bizonytalanság érint. Röviden, a magasabb szintű zaj vagy adatvédelem olyan adatokat fordít, amelyek alacsonyabb epszilon, pontossággal és megbízhatósággal rendelkeznek. 

## <a name="open-source-differential-privacy-libraries"></a>Nyílt forráskódú differenciált adatvédelmi könyvtárak

A SmartNoise egy nyílt forráskódú projekt, amely különböző összetevőket tartalmaz a globális differentially-alapú privát rendszerek létrehozásához. A SmartNoise a következő legfelső szintű összetevőkből áll:

- SmartNoise Core könyvtár
- SmartNoise SDK-könyvtár

### <a name="smartnoise-core"></a>SmartNoise mag

Az alapszintű függvénytár a következő adatvédelmi mechanizmusokat tartalmazza a differentially privát rendszerének megvalósításához:

|Összetevő  |Leírás  |
|---------|---------|
|Elemzés     | Tetszőleges számítások gráf-leírása. |
|Validator     | Egy olyan, a rozsda-függvénytár, amely eszközöket tartalmaz az elemzéshez szükséges feltételek ellenőrzéséhez és lefoglalásához a differentially.          |
|Futtatókörnyezet     | Az elemzés végrehajtásához szükséges adathordozó. A hivatkozási futtatókörnyezet rozsda, de a futtatókörnyezetek az adatigénytől függően bármilyen számítási keretrendszer, például az SQL és a Spark használatával írhatók.        |
|Kötések     | Nyelvi kötések és segítő kódtárak elemzések készítéséhez. A SmartNoise jelenleg Python-kötéseket biztosít. |

### <a name="smartnoise-sdk"></a>SmartNoise SDK

A rendszerkönyvtár a következő eszközöket és szolgáltatásokat biztosítja a táblázatos és a kapcsolati adatokat kezelő szolgáltatásokhoz:

|Összetevő  |Leírás  |
|---------|---------|
|Data Accesss     | Az SQL-lekérdezések elfogására és feldolgozására szolgáló függvénytár, amely jelentéseket készít. Ez a kódtár a Pythonban van megvalósítva, és a következő ODBC-és DBAPI-adatforrásokat támogatja:<ul><li>PostgreSQL</li><li>SQL Server</li><li>Spark</li><li>Preston</li><li>Pandák</li></ul>|
|Szolgáltatás     | A végrehajtási szolgáltatás, amely REST-végpontot biztosít a kérelmek és a megosztott adatforrások közötti lekérdezések kiszolgálásához. A szolgáltatás úgy lett kialakítva, hogy lehetővé tegye a differenciált adatvédelmi modulok összeállítását, amelyek különböző Delta és epszilon értékeket, más néven heterogén kérelmeket tartalmaznak. Ez a hivatkozási implementáció a korrelált adatok lekérdezéseit érintő további hatásokra utal. |
|Értékelő     | A sztochasztikus értékelő, amely az adatvédelem megsértését, a pontosságot és a torzítást ellenőrzi. Az értékelő a következő teszteket támogatja: <ul><li>Adatvédelmi teszt – meghatározza, hogy a jelentés megfelel-e a különbözeti adatvédelem feltételeinek.</li><li>Pontossági teszt – meghatározza, hogy a jelentések megbízhatósága a 95%-os megbízhatósági szint miatt a felső és az alsó határokon belülre esik-e.</li><li>Segédprogram tesztelése – meghatározza, hogy a jelentések megbízhatósági határai elég lezárultak-e az adatokhoz, miközben továbbra is maximalizálják az adatvédelmet.</li><li>Torzítási teszt – a jelentések ismétlődő lekérdezésekre való terjesztését méri, így biztosítva, hogy ne legyenek kiegyensúlyozva</li></ul> |

## <a name="next-steps"></a>Következő lépések

[Differentially privát rendszer](how-to-differential-privacy.md) létrehozása Azure Machine Learningban.

Ha többet szeretne megtudni a SmartNoise összetevőiről, tekintse meg a [SmartNoise Core](https://github.com/opendifferentialprivacy/smartnoise-core), a [SmartNoise SDK](https://github.com/opendifferentialprivacy/smartnoise-sdk)és a SmartNoise- [minták](https://github.com/opendifferentialprivacy/smartnoise-samples)GitHub-tárházait.