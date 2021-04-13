---
title: Predicative-karbantartási architektúra a rendellenesség-Kiderítő többváltozós API használatához
titleSuffix: Azure Cognitive Services
description: A rendellenesség-Kiderítő többváltozós API-k használatára vonatkozó hivatkozási architektúra, amely a prediktív karbantartás érdekében anomáliák észlelését alkalmazza az idősoros adataira.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: anomáliák észlelése, gépi tanulás, algoritmusok
ms.openlocfilehash: 4d379cfe01dcbd88531b98a32b45e0b30f6adeef
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315594"
---
# <a name="predictive-maintenance-solution-with-anomaly-detector-multivariate"></a>Prediktív karbantartási megoldás a anomália-detektor többváltozós szolgáltatásával

Számos különböző iparágnak prediktív karbantartási megoldásokra van szüksége, hogy csökkentse a kockázatokat, és gyakorlatban hasznosítható elemzéseket nyerjen a berendezések adatainak feldolgozásával. A prediktív karbantartás kiértékeli a berendezések állapotát az online figyelés végrehajtásával. A cél az, hogy karbantartást végezzen, mielőtt a berendezés rontja vagy lebomlik.

A berendezések állapotának monitorozása kihívást jelenthet, mivel a berendezésen belüli egyes összetevők több tucat jelet is létrehozhatnak, például a vibrációt, a tájolást és a rotációt.  Ez még összetettebb lehet, ha ezek a jelek implicit kapcsolattal rendelkeznek, és együtt kell figyelni és elemezni. A jelek különböző szabályainak definiálása és egymással való korrelációja költséges lehet. A rendellenesség-érzékelő többváltozós funkciója lehetővé teszi a következőket:

* Egyszerre több korrelációs jelet kell figyelni a modellben, és az egymás közötti korrelációk is megjelennek a modellben.
* Az egyes rögzített anomáliák esetében a különböző jelek hozzájárulási rangja segíthet a anomáliák magyarázatában és az incidensek kiváltó okának elemzésében.
* A többváltozós anomáliák észlelési modellje nem felügyelt módon van felépítve. A modelleket kifejezetten különböző típusú eszközökhöz lehet tanítani.

Itt a prediktív karbantartási megoldások hivatkozási architektúráját biztosítjuk a többváltozós anomália alapján.

## <a name="reference-architecture"></a>Referenciaarchitektúra

[Az ![ architektúra ábrája, amely az érzékelő adatain kezdődik, és egy darab ipari berendezéssel kerül begyűjtésre, és nyomon követi a feldolgozási/elemzési folyamatot az incidensek észlelése után generált incidens-riasztás végéig. ](../media/multivariate-architecture/multivariate-architecture.png)](../media/multivariate-architecture/multivariate-architecture.png#lightbox)

A fenti architektúrában az érzékelői adatokból érkező folyamatos átviteli eseményeket Azure Data Lake fogja tárolni, majd egy Adatátalakítási modul dolgozza fel, amelyet egy idősoros formátumba kell alakítani. Az adatfolyam-továbbítási esemény a valós idejű észlelést is elindítja a betanított modellel. Általánosságban elmondható, hogy egy modul fogja kezelni a többváltozós modell életciklusát, például a *Bridge szolgáltatást* ebben az architektúrában.

**Modell betanítása**: mielőtt az anomália-detektor többváltozós használatával észlelje az összetevők vagy a berendezések rendellenességeit. Be kell tanítani egy modellt az entitás által generált adott jelekre (idősorozatra). A *Bridge szolgáltatás* beolvassa az előzményeket, és beküld egy betanítási feladatot az anomália-detektorba, majd megőrzi a modell azonosítóját a *modell meta* Storage-ban.

**Modell ellenőrzése**: egy bizonyos modell betanítási ideje a betanítási adatmennyiség alapján változhat. A *Bridge szolgáltatás* rendszeres időközönként lekérdezheti a modell állapotát és a diagnosztikai adatokat. A modell minőségének érvényesítése szükséges lehet, mielőtt online állapotba helyezné. Ha vannak feliratok a forgatókönyvben, ezek a címkék használhatók a modell minőségének ellenőrzéséhez. Ellenkező esetben a diagnosztikai adatok segítségével kiértékelheti a modell minőségét, és a betanított modellel is elvégezheti a korábbi adatok észlelését, és kiértékelheti az eredményt, hogy Backtest a modell érvényességét.

A **modell következtetése**: az online észlelés az érvényes modellel lesz elvégezve, és az eredmény-azonosító a *következtetési táblában* is tárolható. A betanítási folyamat és a következtetési folyamat is aszinkron módon történik. Általánosságban elmondható, hogy egy észlelési feladat másodperceken belül elvégezhető. Az észleléshez használt jeleknek azonosnak kell lenniük a betanításhoz. Ha például rezgést, tájolást és rotációt használ a képzéshez, az észlelés során a három jelet bemenetként kell szerepeltetni.

**Incidensek riasztása** Az észlelés eredményét az eredmény-azonosítókkal kérdezheti le. Minden eredmény az egyes anomáliák súlyosságát, és a hozzájárulási rangsort tartalmazza. A hozzájárulási rangsorban megtudhatja, miért történt ez a rendellenesség, és melyik jel okozta ezt az eseményt. A súlyosság beállításával különböző küszöbértékeket állíthat be, hogy riasztásokat és értesítéseket hozzon létre a helyszíni mérnököknek a karbantartási munkálatok elvégzéséhez.

## <a name="next-steps"></a>Következő lépések

- Gyors [útmutató.](../quickstarts/client-libraries-multivariate.md)
- Ajánlott [eljárások](../concepts/best-practices-multivariate.md): Ez a cikk a többváltozós API-kkal való használatra javasolt mintákat ismerteti.