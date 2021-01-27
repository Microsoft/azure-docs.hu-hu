---
title: Áttelepíti a Microsoft Translator hub-munkaterületet és-projekteket? -Egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ez a cikk azt ismerteti, hogyan telepítheti át a hub-munkaterületet és-projekteket az Azure Cognitive Services Custom Translator szolgáltatásba.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 89658ce88b4f1ac9d5bacac7bd45511b4aa0a1be
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895711"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Központi munkaterület és projektek áttelepíthetők az egyéni Fordítóba

A [Microsoft Translator hub](https://hub.microsofttranslator.com/) -munkaterületét és-projektjeit egyszerűen áttelepítheti egyéni fordítóba. Az áttelepítés a Microsoft hub-ból kezdeményezhető egy munkaterület vagy projekt kiválasztásával, majd a munkaterületek egyéni fordítóban való kiválasztásával, majd az átvinni kívánt képzések kiválasztásával. Az áttelepítés megkezdése után a kiválasztott képzési beállítások az összes vonatkozó dokumentummal lesznek átadva. A központilag telepített modellek betanítva vannak, és a befejezést követően is üzembe helyezhetők.

Ezeket a műveleteket az áttelepítés során hajtja végre a rendszer:
* Az összes dokumentum és projekt-definíció neve a "hub_" hozzáadásával lesz átadva a névnek. Az automatikusan létrehozott teszt-és hangolási adathub_systemtune_ \<modelid> vagy hub_systemtest_ neve lesz \<modelid> .
* A központilag telepített állapotban lévő, a Migrálás során bekövetkezett képzések automatikusan betanítva lesznek a hub betanításának dokumentumaival. Ez a képzés nem lesz felszámítva az előfizetésre. Ha az automatikus központi telepítés be van jelölve az áttelepítéshez, a rendszer a betanított modellt a befejezés után telepíti. A rendszer a rendszeres üzemeltetési díjakat alkalmazza.
* Az üzembe helyezett állapotban nem lévő áttelepített Betanítások az áttelepített vázlat állapotba kerülnek. Ebben az állapotban lehetősége lesz az áttelepített definícióval rendelkező modellek betanítására, de a normál betanítási díjak is érvényesek lesznek.
* A központi képzésből áttelepített BLEU-pontszám a modell TrainingDetails oldalán található a "Bleu pontszám az MT hub-ban" című szakaszban.

> [!Note] 
> A sikeres képzés érdekében a Custom Translator legalább 10 000 egyedi kinyert mondatot igényel. Az egyéni fordító nem végezheti el a [javasolt minimálisnál](./sentence-alignment.md#suggested-minimum-number-of-sentences)kevesebb tanítást.

## <a name="find-custom-translator-workspace-id"></a>Egyéni Translator-munkaterület AZONOSÍTÓjának keresése

A [Microsoft Translator hub](https://hub.microsofttranslator.com/) -munkaterület átmigrálása érdekében egyéni fordítóban kell megadnia a cél munkaterület azonosítóját. Az egyéni Translator cél munkaterülete az a hely, ahol az összes központ-munkaterületet és projektet át kell telepíteni.

A cél munkaterület-azonosítót a Custom Translator Settings (egyéni fordítói beállítások) lapon találja:

1. Nyissa meg a "beállítás" lapot az egyéni Translator portálon.

2. A munkaterület-azonosítót az alapszintű információk szakaszban találja.

    ![A cél-munkaterület AZONOSÍTÓjának megkeresése](media/how-to/how-to-find-destination-ws-id.png)

3. Tartsa meg a cél-munkaterület AZONOSÍTÓját, hogy az áttelepítési folyamat során hivatkozzon.

## <a name="migrate-a-project"></a>Projekt migrálása

Ha szelektíven szeretné áttelepíteni a projekteket, a Microsoft Translator hub ezt lehetővé teszi.

Projekt átmigrálása:

1. Jelentkezzen be a Microsoft Translator hub-ba.

2. Nyissa meg a "projektek" lapot.

3. Kattintson az áttelepítés hivatkozásra a megfelelő projekthez.

    ![Képernyőfelvétel: a kiválasztott projekt áttelepíteni kívánt gombjának kiemelése.](media/how-to/how-to-migrate-from-hub.png)

4. Az áttelepítés hivatkozás megnyomásakor a következő űrlapot fogja megjeleníteni:
   * Adja meg azt a munkaterületet, amelyet át szeretne vinni az egyéni Fordítóba
   * Jelezze, hogy szeretné-e átvinni az összes betanítást a sikeres képzésekkel vagy csak az üzembe helyezett képzésekkel. Alapértelmezés szerint minden sikeres tanítás átkerül.
   * Jelezze, hogy szeretné-e automatikusan üzembe helyezni a képzést, ha befejeződik a képzés. Alapértelmezés szerint a képzés nem lesz automatikusan telepítve a befejezés után.

5. Kattintson a kérelem elküldése elemre.

## <a name="migrate-a-workspace"></a>Munkaterület átmigrálása

Egyetlen projekt áttelepítése mellett az összes projektet áttelepítheti egy munkaterületen a sikeres képzések használatával is. Ez azt eredményezi, hogy a munkaterületen minden projekt kiértékelése megtörtént, mintha az áttelepítési hivatkozás le lett nyomva. Ez a szolgáltatás olyan felhasználók számára megfelelő, akik sok projekttel szeretnék áttelepíteni az összeset az egyéni Fordítóba ugyanazokkal a beállításokkal. A munkaterületek áttelepítése a Translator hub Settings (beállítások) lapjáról indítható el.

Munkaterület átmigrálása:

1. Jelentkezzen be a Microsoft Translator hub-ba.

2. Nyissa meg a "beállítások" lapot.

3. A "beállítások" lapon kattintson a munkaterület-adatáttelepítés egyéni Fordítóba elemre.

    ![Képernyőfelvétel: a munkaterület-adatáttelepítés egyéni fordítói lehetőségre való kiemelése.](media/how-to/how-to-migrate-workspace-from-hub.png)

4. A következő lapon válassza ki a két lehetőség egyikét:

    a. Csak központilag telepített képzések: Ha ezt a lehetőséget választja, a rendszer csak a telepített rendszereket és a kapcsolódó dokumentumokat telepíti át.

    b. Minden sikeres tanítás: Ha ezt a lehetőséget választja, a rendszer áttelepíti az összes sikeres betanítást és kapcsolódó dokumentumot.

    c. Adja meg a cél munkaterület-azonosítót az egyéni fordítóban.

    ![Áttelepítés az elosztóról](media/how-to/how-to-migrate-from-hub-screen.png)

5. Kattintson a kérelem küldése gombra.

## <a name="migration-history"></a>Áttelepítési előzmények

Ha a munkaterületről vagy a projektről való áttelepítést kérte a központból, az áttelepítési előzményeket az egyéni fordítói beállítások lapon találja.

Az áttelepítési előzmények megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a "beállítás" lapot az egyéni Translator portálon.

2. A beállítások lap áttelepítési előzmények szakaszában kattintson az áttelepítési előzmények elemre.

    ![Áttelepítési előzmények](media/how-to/how-to-migration-history.png)

Az áttelepítési Előzmények lap az összes kért áttelepítésre vonatkozó Összegzésként jeleníti meg a következő információkat.

1. Áttelepítette: az áttelepítési kérelmet elküldő felhasználó neve és e-mail-címe

2. Migrálva: az áttelepítés dátuma és időbélyegzője

3. Projektek: azoknak a projekteknek a száma, amelyeket az áttelepítéshez a sikeres Migrálás idején igényeltek.

4. Képzések: az áttelepítéshez szükséges Betanítások száma

5. Dokumentumok: azoknak a dokumentumoknak a száma, amelyeket a rendszer az áttelepítéshez a sikeresen áttelepített dokumentumok száma miatt kért.

    ![Áttelepítési előzmények részletei](media/how-to/how-to-migration-history-details.png)

Ha részletesebb áttelepítési jelentést szeretne készíteni a projektekről, a képzésekről és a dokumentumokról, akkor a részletek CSV-ként való exportálását választja.

## <a name="implementation-notes"></a>Implementációs megjegyzések
* Az egyéni fordítóban még nem elérhető nyelvi párokkal rendelkező rendszerek csak az adathozzáféréshez és az egyéni Translator használatával történő telepítéshez lesznek elérhetők. Ezek a projektek "nem érhető el" jelöléssel jelennek meg a projektek lapon. Mivel az egyéni fordítókkal új nyelvi párokat engedélyezünk, a projektek betanításra és üzembe helyezésre lesznek aktívak. 
* Egy projekt a központból az egyéni Fordítóba való áttelepítése nem befolyásolja a hub betanításait vagy projektjeit. A Migrálás során nem törölünk projekteket vagy dokumentumokat, és nem távolítjuk el a modelleket.
* Projektenként csak egyszer lehet áttelepíteni. Ha egy projekt áttelepítését kell megismételni, vegye fel velünk a kapcsolatot.
* Az egyéni fordító támogatja a NMT nyelvét az angoltól és angolra. [Tekintse meg a támogatott nyelvek teljes listáját](../language-support.md#customization). A hub nem igényel alapmodelleket, ezért több ezer nyelvet támogat. Áttelepítheti a nem támogatott nyelvi párokat, de csak a dokumentumok és a projekt-definíciók áttelepítését fogjuk elvégezni. Az új modellt nem lehet majd betanítani. Emellett ezek a dokumentumok és projektek inaktívként jelennek meg, így jelezve, hogy jelenleg nem használhatók. Ha támogatást adnak a projektekhez és/vagy dokumentumokhoz, az aktív és a betanítható lesz.
* Az egyéni Translator jelenleg nem támogatja az egynyelvű betanítási adatgyűjtést. A nem támogatott nyelvi párokhoz hasonlóan áttelepítheti az egynyelvű dokumentumokat, de inaktívként jeleníti meg őket, amíg az egynyelvű adatbevitel nem támogatott.
* Az egyéni fordítónak 10k Parallel mondatokra van szüksége a betanításhoz. A Microsoft hub kisebb adathalmazon is betanítható. Ha olyan betanítást telepít át, amely nem felel meg ennek a követelménynek, nem lesz betanítva.

## <a name="custom-translator-versus-hub"></a>Egyéni fordító és hub

Ez a táblázat a Microsoft Translator hub és az egyéni fordító funkcióit hasonlítja össze.

| Szolgáltatás | Hub | Custom Translator |
| ------- | :-: | :---------------: |
| Testreszabási funkció állapota    | Általános elérhetőség    | Általános elérhetőség |
| Szöveges API-verzió    | 2. verzió     | V3  |
| SMT testreszabása    | Igen    | Nem |
| NMT testreszabása    | Nem    | Igen |
| Új Unified Speech Services testreszabása    | Nem    | Igen |
| Nincs nyomkövetés | Igen | Yes |

## <a name="new-languages"></a>Új nyelvek

Ha Ön olyan Közösség vagy szervezet, amely a fordító új nyelvi rendszerének létrehozására dolgozik, további információkért tekintse meg a [custommt@microsoft.com](mailto:custommt@microsoft.com) következőt:.

## <a name="next-steps"></a>Következő lépések

- [Modell betanítása](how-to-train-model.md).
- Az üzembe helyezett egyéni fordítási modell használatának megkezdése [Translator v3](../reference/v3-0-translate.md?tabs=curl)-n keresztül.