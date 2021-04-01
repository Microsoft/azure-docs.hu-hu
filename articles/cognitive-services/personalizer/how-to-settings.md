---
title: A Personalizer konfigurálása
description: A szolgáltatás konfigurációja magában foglalja, hogy a szolgáltatás hogyan kezeli a jutalmakat, milyen gyakran vizsgálja a szolgáltatás, milyen gyakran történik a modell újratanítása és mennyi az adattárolás.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 9b7347cb98bcbf2e1d92f115d404197083acef4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91292608"
---
# <a name="configure-personalizer-learning-loop"></a>Személyre szabott tanulási hurok konfigurálása

A szolgáltatás konfigurációja magában foglalja, hogy a szolgáltatás hogyan kezeli a jutalmakat, milyen gyakran vizsgálja a szolgáltatás, milyen gyakran történik a modell újratanítása és mennyi az adattárolás.

Konfigurálja a tanulási ciklust a **konfiguráció** lapon a személyre szabott erőforráshoz tartozó Azure Portalban.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="planning-configuration-changes"></a>Konfiguráció módosításainak megtervezése

Mivel egyes konfigurációs változások [alaphelyzetbe állítják a modellt](#settings-that-include-resetting-the-model), meg kell terveznie a konfiguráció módosításait.

Ha [gyakornoki módot](concept-apprentice-mode.md)kíván használni, mindenképpen tekintse át a személyre szabott konfigurációt, mielőtt a Apprentice módra vált.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>A modell alaphelyzetbe állítását tartalmazó beállítások

Az alábbi műveletek elindítják a modell átképzését az elmúlt két nap során rendelkezésre álló adatmennyiség használatával.

* Jutalom
* Feltárás

Az összes adatai [törléséhez](how-to-manage-model.md) használja a **modell és tanulási beállítások** lapot.

## <a name="configure-rewards-for-the-feedback-loop"></a>Jutalmak konfigurálása a visszajelzési hurokhoz

Konfigurálja a szolgáltatást a tanulási ciklusban a jutalmak használatára. A következő értékek módosításai visszaállítják a jelenlegi személyre szabott modellt, majd az utolsó 2 nap adatait áttanítják.

> [!div class="mx-imgBorder"]
> ![Adja meg a visszajelzési hurok jutalmazási értékeit](media/settings/configure-model-reward-settings.png)

|Érték|Cél|
|--|--|
|Jutalom várakozási ideje|Meghatározza azt az időtartamot, ameddig a személynek a rangsorolási híváshoz tartozó jutalmazási értékeket kell gyűjtenie a rangsor meghívásának pillanatától kezdve. Ezt az értéket úgy állítja be a rendszer, hogy megkérdezi, hogy mennyi ideig kell megvárnia a személyre szabási hívásokat? Az ablak beérkezését követően bekövetkező minden jutalom bekerül, de nem használható a tanuláshoz.|
|Alapértelmezett jutalom|Ha nem fogadja el a személyre szabott jutalmat a rangsorolási várakozási idő időszaka alatt, a személyre szabási híváshoz rendelt jutalmazási időablakban az alapértelmezett jutalom lesz hozzárendelve. Alapértelmezés szerint a legtöbb esetben az alapértelmezett jutalom nulla (0).|
|Jutalom összesítése|Ha ugyanahhoz a Range API-híváshoz több jutalom érkezik, a rendszer ezt az összesítési módszert használja: **Sum** vagy **legkorábbi**. A legkorábbi pontszámot kapott, és elveti a többit. Ez akkor hasznos, ha egyedi jutalmat szeretne használni a lehetséges duplikált hívások között. |

Az értékek módosítása után válassza a **Mentés** lehetőséget.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>A felderítés konfigurálása, amely lehetővé teszi, hogy a tanulási hurok alkalmazkodjon

A személyre szabás lehetővé tenné az új mintázatok felderítését és a felhasználói viselkedés változásainak időbeli változását azáltal, hogy a betanított modell előrejelzése helyett alternatívákat keres. A **feltárási** érték határozza meg, hogy a rangsorolási hívások hány százalékát kell megválaszolni a feltárással.

Az érték módosításakor a rendszer alaphelyzetbe állítja az aktuális személyre szabott modellt, majd az utolsó 2 nap adatainak megfelelően áttanítja azt.

![A feltárási érték határozza meg, hogy a rangsorolási hívások hány százalékát kell megválaszolni a feltárással](media/settings/configure-exploration-setting.png)

Az érték módosítása után válassza a **Mentés** lehetőséget.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Modell-frissítési gyakoriság konfigurálása a modell betanításához

A **modell frissítési gyakorisága** határozza meg, hogy a modell milyen gyakran legyen betanítva.

|Gyakorisági beállítás|Cél|
|--|--|
|1 perc|Az egyperces frissítési gyakoriságok akkor hasznosak, ha az alkalmazás kódját a személyre szabás, a demók **vagy a gépi** tanulási szempontok interaktív tesztelése során végzi.|
|15 perc|A magas modell-frissítési gyakoriságok olyan helyzetekben hasznosak, amikor **szorosan nyomon szeretné követni** a felhasználói viselkedések változásait. Ilyenek például az élő Hírek, a vírusos tartalmak vagy az élő termékek ajánlattételi helyei. Ezeket a forgatókönyveket 15 perces gyakorisággal használhatja. |
|1 óra|A legtöbb felhasználási eset esetében az alacsonyabb frissítési gyakoriság érvényes.|

![A modell frissítési gyakorisága határozza meg, hogy milyen gyakran van új személyre szabott modell újratanítása.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Az érték módosítása után válassza a **Mentés** lehetőséget.

## <a name="data-retention"></a>Adatmegőrzés

**Az adatmegőrzési időszak** azt állítja be, hogy hány nap személy tartja az adatnaplókat. Az [Offline értékelések](concepts-offline-evaluation.md)végrehajtásához múltbeli adatnaplókra van szükség, amelyek a személyre szabott és a tanulási szabályzatok optimalizálására szolgálnak.

Az érték módosítása után válassza a **Mentés** lehetőséget.



## <a name="next-steps"></a>Következő lépések

[Ismerje meg, hogyan kezelheti a modellt](how-to-manage-model.md)
