---
title: Azure Certified-eszköz program szószedete
description: Az Azure Certified eszköz programban használt általános kifejezések listája
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/03/2021
ms.custom: template-concept
ms.openlocfilehash: f58c29c6bd2c22f37d2285ac6e93c6f54e47c4e0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305089"
---
# <a name="azure-certified-device-program-glossary"></a>Azure Certified-eszköz program szószedete

Ez az útmutató az Azure Certified eszköz programjában és portálján gyakran használt kifejezések definícióit tartalmazza. A minősítési folyamat tisztázásához tekintse meg ezt a szószedetet. Az Ön kényelme érdekében ez a Szószedet olyan főbb minősítési fogalmak alapján van kategorizálva, amelyekkel kapcsolatban kérdése lehet.

## <a name="device-class"></a>Eszköz osztálya

A minősítési projekt létrehozásakor a rendszer megkéri, hogy adja meg az eszköz osztályát. Az eszköz osztály az eszközhöz legjobban illő Form Factor vagy besorolásra hivatkozik.

- **Átjáró**

    Egy eszköz, amely feldolgozza az IoT hálózaton keresztül elküldett adatfeldolgozást.

- **Érzékelő**

    Olyan eszköz, amely észleli és reagál a környezet változásaira, és az átjárókkal csatlakozik a módosítások feldolgozásához.

- **Egyéb**

    Ha az Other (egyéb) lehetőséget választja, a saját szavaiban adja hozzá az eszköz osztályának leírását. Az idő múlásával továbbra is hozzáadhatunk új értékeket a listához, különösen, ha a partnereink visszajelzéseit továbbra is figyelik.

## <a name="device-type"></a>Eszköz típusa

A minősítési folyamat során a rendszer a két eszköz típusának kiválasztását is kéri.

- **Végtermék**

    Olyan eszköz, amely készen áll, és készen áll az éles környezetben való üzembe helyezésre. A belső vezérlőprogram és az operációs rendszer esetében általában befejeződött az űrlap. Ezek lehetnek olyan általános célú eszközök, amelyek további testreszabási vagy specializált eszközöket igényelnek, amelyeknek nincs szükségük a használat módosítására.
- **Megoldás – kész fejlesztői csomag**

    A hardvereket és szoftvereket tartalmazó fejlesztői csomag megkönnyíti a prototípus-készítést, általában nem kész formában. Általában a gyors prototípusok engedélyezéséhez a mintakód és az oktatóanyagok szerepelnek.

## <a name="component-type"></a>Összetevő típusa

Az eszköz részletei szakaszban leírhatja az eszközt az összetevők típus szerinti listázásával. Az összetevőkről [itt](./how-to-using-the-components-feature.md)találhat további útmutatást.

- **Ügyfélre felkészített termék**

    A teljes vagy elsődleges eszköz összetevő-ábrázolása. Ez különbözik a **készterméktől**, amely az eszköznek az ügyfél általi használatra készen álló besorolása további fejlesztés nélkül. A késztermék egy ügyfél-Ready termék összetevőjét fogja tartalmazni.
- **Fejlesztési Tanács**

    Egy integrált vagy leválasztható, mikroprocesszoros tábla, amely megkönnyíti a testreszabást.
- **Perifériás**

    A termék integrált vagy leválasztható kiegészítője (például egy tartozék). Ezek általában a fő eszközhöz csatlakozó eszközök, de nem járulnak hozzá az eszköz elsődleges funkcióihoz. Ehelyett további funkciókat is biztosít. A memória, a RAM, a tárolás, a merevlemezek és a processzorok nem tekintendők perifériáknak (ezek helyett az ügyfélre vonatkozó termék-összetevő további specifikációi között kell szerepelniük).
- **Rendszer-modul**  

    Egy tábla szintű áramkör, amely egyetlen modulban integrálja a rendszerfüggvényeket.

## <a name="component-attachment-method"></a>Összetevő-melléklet metódusa

Az összetevő-csatolási módszer egy másik összetevő-részlet, amely tájékoztatja az ügyfelet arról, hogy az összetevő hogyan integrálódik a teljes termékbe.

- **Integrált**
 
    Arra utal, hogy egy eszköz összetevője a termék fő alvázának részét képezi. Ez leggyakrabban olyan perifériás összetevő-típusra hivatkozik, amely nem távolítható el az eszközről.  
    Példa: egy integrált hőmérséklet-érzékelő egy átjáró-alvázon belül.

- **Diszkrét**

    Arra utal, hogy ha egy összetevő **nem** része a termék fő alvázának.  
    Példa: külső hőmérséklet-érzékelő, amelyet csatolni kell az eszközhöz.


## <a name="next-steps"></a>Következő lépések

Ez a Szószedet végigvezeti a projektnek a portálon való tanúsításának lépésein. Most már készen áll a projekt megkezdésére!
- [Oktatóanyag: a projekt létrehozása](./tutorial-01-creating-your-project.md)
