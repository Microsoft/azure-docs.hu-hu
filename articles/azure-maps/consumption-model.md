---
title: Gépjármű-felhasználási modellek az útválasztáshoz | Microsoft Azure térképek
description: 'Ismerje meg a Azure Maps által támogatott fogyasztási modelleket: égés és elektromos. Megtekintheti az egyes modellek által használt paramétereket, és megtekintheti a paraméterek megkötéseit.'
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: b44186d783a249192a8c13ee97063034ee319df7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013139"
---
# <a name="consumption-model"></a>Használatalapú modell

Az útválasztási szolgáltatás a járműre jellemző használati modell részletes leírására szolgáló paramétereket tartalmaz.
A **vehicleEngineType** értékétől függően két fő felhasználási modell támogatott: az _égés_ és az _elektromos_. Nem megfelelő a különböző modellekhez tartozó paraméterek megadása ugyanabban a kérésben. Emellett a használati modell paraméterei nem használhatók a következő **travelMode** -értékekkel: _kerékpár_ és _gyalogos_.

## <a name="parameter-constraints-for-consumption-model"></a>A használati modell paramétereinek megkötései

Mindkét felhasználási modellben vannak függőségek a paraméterek megadásakor. Ami azt jelenti, hogy az egyes paraméterek explicit módon történő megadása más paraméterek megadását is szükségessé teheti. A következő függőségek ismerete:

* Minden paraméterhez a felhasználónak meg kell adni a **constantSpeedConsumption** . Hiba a más felhasználási modell paraméterének megadásakor, ha a **constantSpeedConsumption** nincs megadva. A **vehicleWeight** paraméter kivételt jelent ennek a követelménynek.
* a **accelerationEfficiency** és a **decelerationEfficiency** mindig párosként kell megadni (azaz mindkettőt vagy egyiket sem).
* Ha a **accelerationEfficiency** és a **decelerationEfficiency** meg van adva, az értékük szorzata nem lehet nagyobb, mint 1 (az örökös mozgás megakadályozása érdekében).
* a **uphillEfficiency** és a **downhillEfficiency** mindig párosként kell megadni (azaz mindkettőt vagy egyet).
* Ha a **uphillEfficiency** és a **downhillEfficiency** meg van adva, az értékük szorzata nem lehet nagyobb, mint 1 (az örökös mozgás megakadályozása érdekében).
* Ha a \* __hatékonysági__ paramétereket a felhasználó adta meg, akkor a **vehicleWeight** is meg kell adni. A **vehicleEngineType** _égés_ esetén a **fuelEnergyDensityInMJoulesPerLiter** is meg kell adni.
* a **maxChargeInkWh** és a **currentChargeInkWh** mindig párosként kell megadni (azaz mindkettőt vagy egyiket sem).

> [!NOTE]
> Ha csak a **constantSpeedConsumption** van megadva, a használati számítások során nem számítunk fel más felhasználási szempontokat, például a lejtőket és a járművek gyorsítását.

## <a name="combustion-consumption-model"></a>Égési fogyasztás modellje

Az égési használat modelljét akkor kell használni, ha a **vehicleEngineType** _égési_ értékre van állítva.
A modellhez tartozó paraméterek listája alább látható. A Részletes leírásért tekintse meg a parameters (paraméterek) szakaszt.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Elektromos fogyasztási modell

Az elektromos fogyasztási modellt akkor kell használni, ha a **vehicleEngineType** az _Electric_ értékre van állítva.
A modellhez tartozó paraméterek listája alább látható. A Részletes leírásért tekintse meg a parameters (paraméterek) szakaszt.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>A felhasználási paraméterek ésszerű értékei

A használati paraméterek egy adott készlete elutasítható, még akkor is, ha a készlet megfelel az összes kifejezett követelménynek. Akkor következik be, amikor egy adott paraméter értéke vagy a különböző paraméterek értékeinek kombinációja a használati értékek nem ésszerű mértékű nagyságrendjét eredményezi. Ha ez történik, valószínűleg bemeneti hibát jelez, mivel a használati paraméterek minden ésszerű értékének megfelelő gondossággal kell eljárni. Ha a rendszer elutasítja a használati paraméterek egy adott készletét, a mellékelt hibaüzenet az OK (ok) szöveges magyarázatát fogja tartalmazni.
A paraméterek részletes leírása példákat mutat be mindkét modell ésszerű értékeire.
