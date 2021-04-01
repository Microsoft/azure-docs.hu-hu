---
title: A megszemélyesítő működése – személyre szabás
description: A személyre szabási _hurok_ gépi tanulással hozza létre a modellt, amely előre jelzi a tartalom legfelső szintű műveletét. A modell kizárólag a rangsorban és a jutalmazási hívásokkal eljuttatott adataira van kitanítva.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: cfbe5cf8c19bfafb38f6149391e09350785ebf9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91303607"
---
# <a name="how-personalizer-works"></a>A Personalizer működése

A személyre szabott erőforrás, a _tanulási ciklus_, a gépi tanulás segítségével hozza létre a modellt, amely előre megjósolja a tartalom legfelső szintű műveletét. A modell kizárólag a **rangsorban** és a **jutalmazási** hívásokkal eljuttatott adataira van kitanítva. Minden hurok teljesen független egymástól.

## <a name="rank-and-reward-apis-impact-the-model"></a>A Range és a jutalmazási API-k befolyásolják a modellt

A Range API- _val funkciókkal_ és _környezeti funkciókkal_ is elküldheti a műveleteket. A **Rank** API úgy dönt, hogy az alábbiakat használja:

* _Kihasználva_: a jelenlegi modell alapján döntheti el, hogy a lehető leghatékonyabban kell-e a múltban.
* _Megismerés_: válasszon másik műveletet a felső művelet helyett. [Ezt a százalékértéket](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) a Azure Portal személyre szabott erőforrásához konfigurálja.

Határozza meg a jutalom pontszámát, és küldje el a pontszámot a jutalmazási API-nak. A **jutalmazási** API:

* Összegyűjti az adatokat a modell betanításához az egyes rangsorolási hívások funkcióinak és jutalmazási pontjainak rögzítésével.
* Ezeket az adattípusokat használja a modell frissítéséhez a _tanulási szabályzatban_ megadott konfiguráció alapján.

## <a name="your-system-calling-personalizer"></a>A rendszerhívás személyre szabása

Az alábbi képen a rang és a jutalmazási hívások meghívásának építészeti folyamata látható:

![helyettesítő szöveg](./media/how-personalizer-works/personalization-how-it-works.png "A személyre szabás működése")

1. A Range API- _val funkciókkal_ és _környezeti funkciókkal_ is elküldheti a műveleteket.

    * Személyre szabhatja, hogy kihasználja-e az aktuális modellt, vagy a modell új választási lehetőségeit tárja fel.
    * A rangsor eredményét a rendszer elküldi a EventHub.
1. A legmagasabb rangot _jutalom műveleti azonosítóként_ adja vissza a rendszernek.
    A rendszer ezt a tartalmat mutatja be, és a saját üzleti szabályai alapján határozza meg a jutalom pontszámát.
1. A rendszere visszaadja a jutalmazási pontszámot a tanulási ciklusnak.
    * Ha a személy megkapja a jutalmat, a jutalmat a EventHub kapja meg.
    * A rangsor és a jutalom összefügg.
    * Az AI-modell a korrelációs eredmények alapján frissül.
    * A következtetési motor az új modellel frissül.

## <a name="personalizer-retrains-your-model"></a>Személyre szabott újraképzések a modellhez

A személyre szabás a modell **gyakoriságának frissítési** beállítása alapján újratanítja a modellt a Azure Portal személyre szabott erőforrásán.

A személyre szabott adatok az összes jelenleg megőrzött adat alapján a Azure Portal személyre szabott erőforrásának **adatmegőrzési** beállításán alapulnak.

## <a name="research-behind-personalizer"></a>Személyre szabott kutatás

A személyre szabott tudomány és kutatás területén a [megerősítő tanulás](concepts-reinforcement-learning.md) , többek között a dokumentumok, a kutatási tevékenységek, valamint a kutatás folyamatban van a Microsoft Research szolgáltatásban.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a személyre szabott [leggyakoribb forgatókönyvekkel](where-can-you-use-personalizer.md)