---
title: Signal Gate beállítása eseményvezérelt videofelvételhez – Azure
description: Ez a cikk útmutatást nyújt ahhoz, hogyan konfigurálható a Signal Gate egy Media Graph-ban.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: afcec7c03f1353f08b58311278f5a533e0c911bc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94410793"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Signal Gate beállítása eseményvezérelt videofelvételhez

A Media Graph-ban a [Signal Gate processzor csomópont](media-graph-concept.md#signal-gate-processor) lehetővé teszi az adathordozó továbbítását az egyik csomópontról a másikra, ha a kaput egy esemény indítja el. Az aktiváláskor a kapu megnyílik, és lehetővé teszi a média áramlását a megadott időtartamra. A kapu kiváltásához szükséges események hiányában a kapu bezárul, és a média leáll. Az Event-alapú videofelvételekhez használhatja a Signal Gate-feldolgozót.

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy Signal Gate-processzort.

## <a name="suggested-prereading"></a>Javasolt előolvasás
-   [Médiagrafikon](media-graph-concept.md)
-   [Eseményalapú videófelvétel](event-based-video-recording-concept.md)


## <a name="problem"></a>Probléma
Előfordulhat, hogy egy felhasználó egy adott időpontban szeretné elindítani a rögzítést, mielőtt a kaput egy esemény aktiválja. A felhasználó ismeri a rendszeren belüli elfogadható késést. Ezért szeretnék megadni a Signal Gate-processzor késését. A rögzítésük minimális és maximális időtartamát is meg szeretné adni, függetlenül attól, hogy hány új esemény érkezik.
 
### <a name="use-case-scenario"></a>Használatieset-forgatókönyvek
Tegyük fel, hogy minden alkalommal rögzíteni szeretné a videót, amikor megnyílik az épület ajtaja. A következőre szeretne rögzíteni: 

- Az ajtó megnyitása előtt adja meg az *X* másodpercet. 
- Az utolsó legalább *Y* másodpercben, ha az ajtó nincs újra megnyitva. 
- Ha az ajtót többször is megnyitják, az utolsó legfeljebb *z* másodperc. 
 
Tudnia kell, hogy az ajtó érzékelője *másodperces* késéssel rendelkezik. Ha csökkenteni szeretné, hogy az események ne legyenek késői beérkezések, az események megérkezéséhez legalább *K* másodpercet kell engedélyezni.


## <a name="solution"></a>Megoldás

A probléma megoldásához módosítsa a Signal Gate-processzor paramétereit.

A Signal Gate-processzor konfigurálásához használja a következő négy paramétert:
- Aktiválás-értékelési ablak
- Aktiválási jel eltolása
- Minimális aktiválási ablak
- Aktiválás maximális száma ablak

A Signal Gate-processzor elindítása után nyitva marad a minimális aktiválási idő alatt. Az aktiválási esemény a legkorábbi esemény időbélyegzője és az aktiválási jel eltolása szerint kezdődik. 

Ha a Signal Gate-processzor újra aktiválódik, amíg meg van nyitva, az időzítő alaphelyzetbe áll, és a kapu nyitva marad legalább a minimális aktiválási időpontig. A Signal Gate-feldolgozó soha nem marad nyitva a maximális aktiválási időpontnál. 

Egy esemény (1. esemény), amely a média időbélyegzői alapján egy másik esemény (2. esemény) előtt fordulhat elő, figyelmen kívül hagyható, ha a rendszer lemarad, és az 1. esemény a Signal Gate-feldolgozón érkezik a 2. eseményt követően. Ha az 1. esemény nem érkezik meg a 2. esemény és az aktiválási kiértékelési időszak között, az 1. esemény figyelmen kívül lesz hagyva. Nem halad át a Signal Gate processzoron. 

A korrelációs azonosítók minden eseményhez be vannak állítva. Ezek az azonosítók a kezdeti esemény alapján vannak beállítva. A következő eseményekhez tartozó szekvenciálisak.

> [!IMPORTANT]
> A média ideje a média időbélyegzője alapján jelenik meg, amikor egy esemény történik az adathordozón. Előfordulhat, hogy a Signal kapun megjelenő események sora nem tükrözi a médiában megjelenő események sorát.


### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>Paraméterek azon fizikai idő alapján, amelyet az események a Signal kapun érkeznek

* **minimumActivationTime (a lehető legrövidebb időtartamú rögzítés)**: a Signal Gate-feldolgozó által az új események fogadását követően nyitva lévő másodpercek száma, ha a maximumActivationTime nem szakítja meg.
* **maximumActivationTime (egy felvétel leghosszabb lehetséges időtartama)**: a kezdeti eseményből érkező másodpercek maximális száma, amely után a Signal Gate-processzor nyitva marad, miután új események fogadását kezdeményezték, függetlenül attól, hogy milyen események érkeznek.
* **activationSignalOffset**: a Signal Gate-feldolgozó aktiválása és a videó rögzítésének kezdete közötti másodpercek száma. Ez az érték általában negatív, mert elindítja a rögzítést az eseményindító esemény előtt.
* **activationEvaluationWindow**: a kezdeti kiváltó eseménytől kezdődően azon másodpercek száma, amelyekben a kezdeti esemény előtt bekövetkezett esemény, a médiában pedig a Signal Gate-feldolgozónak kell érkeznie, mielőtt figyelmen kívül lenne hagyva.

> [!NOTE]
> A *késői beérkezés* minden olyan esemény, amely az aktiválási értékelési időszak után érkezik, de a kezdeti esemény előtt érkezik a médiában.

### <a name="limits-of-parameters"></a>Paraméterek korlátai

* **activationEvaluationWindow**: 0 másodperc – 10 másodperc
* **activationSignalOffset**: – 1 perc – 1 perc
* **minimumActivationTime**: 1 másodperc – 1 óra
* **maximumActivationTime**: 1 másodperc – 1 óra


A használati esetben a paramétereket a következőképpen állíthatja be:

* **activationEvaluationWindow**: *K* másodperc
* **activationSignalOffset**: *-X* másodperc
* **minimumActivationWindow**: *Y* másodperc
* **maximumActivationWindow**: *Z* másodperc


Íme egy példa arra, hogyan fog megjelenni a **Signal Gate processzor** -csomópont szakasz a következő paraméterek értékeihez:
* **activationEvaluationWindow**: 1 másodperc
* **activationSignalOffset**:-5 másodperc
* **minimumActivationTime**: 20 másodperc
* **maximumActivationTime**: 40 másodperc

> [!IMPORTANT]
> Az egyes paraméterek értékének az [ISO 8601 időtartam formátumát](https://en.wikipedia.org/wiki/ISO_8601#Durations
) kell megvárnia. Például: PT1S = 1 másodperc.


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


Most gondolja át, hogyan fog működni a Signal Gate-processzor konfigurációja különböző rögzítési helyzetekben.

### <a name="recording-scenarios"></a>Forgatókönyvek rögzítése

**Egy esemény egy forrásból (*normál aktiválás*)**

A Signal Gate-processzor, amely egy eseményt kap, egy olyan rögzítést eredményez, amely 5 másodpercet (aktiválási jel = 5 másodperc) indít el, mielőtt az esemény beérkezik a kapuba. A rögzítés többi része 20 másodperc (minimális aktiválási idő = 20 másodperc), mert más események nem érkeznek meg a kapu újraindításához szükséges minimális aktiválási idő végéig.

Példa diagramra:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Egy esemény normál aktiválását ábrázoló diagram egy forrásból.":::

* Rögzítés időtartama =-eltolás + minimumActivationTime = [E1 + eltolás, E1 + minimumActivationTime]


**Két esemény az egyik forrásból (*újraindított aktiválás*)**

A két eseményt fogadó Signal Gate-processzorok egy 5 másodpercet megkezdő rögzítést eredményeznek (az aktiválási jel eltolása = 5 másodperc), mielőtt az esemény beérkezik a kapuba. A 2. esemény az 1. esemény után 5 másodpercig is megérkezik. Mivel a 2. esemény az 1. esemény minimális aktiválási idejének (20 másodperc) vége előtt érkezik, a rendszer újraaktiválja a kaput. A rögzítés többi része 20 másodperc (a minimális aktiválási idő = 20 másodperc), mert más események nem érkeznek meg a 2. esemény minimális aktiválási ideje végéig a kapu újraindításához.

Példa diagramra:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Az egyik forrásból származó két esemény újraaktivált aktiválását ábrázoló diagram.":::

* Rögzítés időtartama =-eltolás + (2. esemény érkezése – 1. esemény érkezése) + minimumActivationTime


***N* esemény az egyik forrásból (*maximális aktiválás*)**

Az *N* eseményt fogadó Signal Gate-processzorok egy olyan rögzítést eredményeznek, amely 5 másodpercet indít el (az aktiválási jel eltolása = 5 másodperc), mielőtt az első esemény beérkezik a kapuba. Mivel minden esemény az előző eseménytől számított 20 másodperces idő lejárta előtt érkezik, a kaput folyamatosan újra kell indítani. Nyitva marad, amíg a maximális aktiválási idő 40 másodperc az első esemény után. Ezután a kapu bezárul, és már nem fogad új eseményeket.

Példa diagramra:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Az egyik forrásból származó N esemény maximális aktiválását ábrázoló diagram.":::
 
* Rögzítés időtartama =-eltolás + maximumActivationTime

> [!IMPORTANT]
> Az előző ábrák azt feltételezik, hogy minden esemény a fizikai idő és a média időpontjában azonos pillanatban érkezik. Ez azt feltételezi, hogy nincsenek késői beérkezések.

## <a name="next-steps"></a>Következő lépések

Próbálja ki az [Event-based video Recording oktatóanyagot](event-based-video-recording-tutorial.md). Kezdje a [topology.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)szerkesztésével. Módosítsa a signalgateProcessor csomópont paramétereit, majd kövesse az oktatóanyag további részeit. Tekintse át a videofelvételeket a paraméterek hatásának elemzéséhez.



