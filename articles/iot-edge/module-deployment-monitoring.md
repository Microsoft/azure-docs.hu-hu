---
title: Eszközkezelés automatikus központi telepítése – Azure IoT Edge | Microsoft Docs
description: A Azure IoT Edgeban található automatikus központi telepítések használata a megosztott címkéken alapuló eszközök csoportjai kezelésére
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3989ec4ca2b5c9d7385841604678791b20c1d102
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489982"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>IoT Edge automatikus központi telepítések ismertetése egyetlen eszközön vagy nagy méretekben

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Az automatikus telepítések és a rétegzett üzembe helyezés segít nagy számú IoT Edge eszközön lévő modulok felügyeletében és konfigurálásában.

Azure IoT Edge kétféle módon konfigurálhatja a modulokat IoT Edge eszközökön való futtatáshoz. Az első módszer a modulok eszközönkénti üzembe helyezése. Hozzon létre egy üzembe helyezési jegyzéket, majd alkalmazza azt egy adott eszközre név szerint. A második módszer a modulok automatikus telepítése bármely regisztrált eszközre, amely megfelel a megadott feltételeknek. Létre kell hoznia egy üzembe helyezési jegyzéket, majd meg kell határoznia, hogy mely eszközökre vonatkozik az eszköz Twin- [címkék](../iot-edge/how-to-deploy-at-scale.md#identify-devices-using-tags) alapján.

Ez a cikk az eszközök flottáinak konfigurálását és monitorozását ismerteti, amelyeket közösen a *IoT Edge automatikus központi telepítésnek* nevezünk. Az alapszintű üzembe helyezés lépései a következők:

1. Az operátorok olyan központi telepítést határoznak meg, amely modulokat és a megcélzott eszközöket ismerteti. Minden központi telepítéshez tartozik egy üzembe helyezési jegyzék, amely tükrözi ezt az információt.
2. A IoT Hub szolgáltatás az összes megadott eszközzel kommunikál a deklarált modulokkal.
3. A IoT Hub szolgáltatás lekérdezi az állapotot a IoT Edge eszközökről, és elérhetővé teszi őket a kezelő számára.  Egy operátor például akkor láthatja, ha egy peremhálózati eszköz nincs megfelelően konfigurálva, vagy ha egy modul meghibásodik a futtatókörnyezet során.
4. Az új IoT Edge-eszközök, amelyek megfelelnek a célzási feltételeknek, konfigurálva vannak a központi telepítéshez.

Ez a cikk a központi telepítés konfigurálásának és figyelésének minden összetevőjét ismerteti. A központi telepítések létrehozásának és frissítésének bemutatóját lásd: [IoT Edge modulok üzembe helyezése és figyelése nagy léptékben](how-to-deploy-at-scale.md).

## <a name="deployment"></a>Üzembe helyezés

Az IoT Edge automatikus központi telepítése IoT Edge modul rendszerképeit rendeli hozzá példányként a IoT Edge eszközök egy meghatározott készletén. Úgy működik, hogy egy IoT Edge telepítési jegyzéket konfigurál, hogy tartalmazza a megfelelő inicializálási paraméterekkel rendelkező modulok listáját. A központi telepítés egyetlen eszközhöz (az eszköz azonosítója alapján) vagy eszközök egy csoportjára (címkék alapján) is hozzárendelhető. Miután egy IoT Edge eszköz megkapja az üzembe helyezési jegyzéket, letölti és telepíti a tároló lemezképeit a megfelelő tároló-tárházból, és ennek megfelelően konfigurálja azokat. A központi telepítés létrehozása után az operátor figyelheti a központi telepítés állapotát, és ellenőrizheti, hogy megfelelően vannak-e konfigurálva a meglevő eszközök.

Csak IoT Edge eszköz konfigurálható központi telepítéssel. A telepítés megkezdése előtt a következő előfeltételeknek kell megfelelnie az eszközön:

* Az alap operációs rendszer
* Egy tároló-felügyeleti rendszer, például a Moby vagy a Docker
* Az IoT Edge futtatókörnyezet kiépítés

### <a name="deployment-manifest"></a>Üzembehelyezési jegyzék

Az üzembe helyezési jegyzék egy JSON-dokumentum, amely leírja a célként IoT Edge eszközökön konfigurálandó modulokat. Tartalmazza az összes modul konfigurációs metaadatait, beleértve a szükséges rendszermodulokat (különösen a IoT Edge ügynököt és IoT Edge hubot).  

Az egyes modulok konfigurációs metaadatai a következők:

* Verzió
* Típus
* Állapot (például fut vagy leállítva)
* Újraindítási szabályzat
* Rendszerkép és tároló beállításjegyzéke
* Az adatbevitelhez és a kimenethez tartozó útvonalak

Ha a modul képe egy privát tároló beállításjegyzékében van tárolva, a IoT Edge ügynök tárolja a beállításjegyzékbeli hitelesítő adatokat.

### <a name="target-condition"></a>Cél feltétel

A cél feltételét a rendszer folyamatosan kiértékeli az üzemelő példány teljes élettartama során. A rendszer minden olyan új eszközt tartalmaz, amely megfelel a követelményeknek, és minden olyan meglévő eszköz törlődik, amely már nem végezhető el. A rendszer újraaktiválja a központi telepítést, ha a szolgáltatás észleli a megcélzott feltételek változását.

Tegyük fel, hogy van egy központi telepítés, amelynek a célja feltétele. környezet = "Prod". Az üzembe helyezés elindítását követően 10 éles eszköz található. A modulok sikeresen telepítve vannak a 10 eszközön. A IoT Edge ügynök állapota 10 összes eszközt, 10 sikeres választ, 0 sikertelen választ és 0 függőben lévő választ jelenít meg. Most öt további eszközt ad hozzá címkékkel. environment = "Prod". A szolgáltatás észleli a változást, és a IoT Edge ügynök állapota 15 teljes eszköz, 10 sikeres válasz, 0 sikertelen válasz és 5 függőben lévő válasz lesz, miközben az öt új eszközre települ.

A cél eszközök kiválasztásához használjon bármilyen logikai feltételt az eszköz Twin-címkék, az eszköz Twin jelentett tulajdonságai vagy a deviceId használatával. Ha címkével szeretné feltételt használni, hozzá kell adnia a "címkék": {} szakaszt az eszköz twin (a Tulajdonságok) csomópontjának azonos szintjén. [További információ a címkékről az eszköz Twin-ben](../iot-hub/iot-hub-devguide-device-twins.md)

Példák a megcélzott feltételekre:

* deviceId = ' linuxprod1 '
* Címkék. környezet = "Prod"
* Tags. environment = ' Prod ' és Tags. location = ' westus '
* Tags. environment = ' Prod ' vagy Tags. location = ' westus '
* Tags. operator = ' John ' és Tags. environment = ' Prod ' és NOT deviceId = ' linuxprod1 '
* tulajdonságok. jelentett. devicemodel = ' 4000x '

A cél feltételének létrehozásakor vegye figyelembe ezeket a korlátozásokat:

* A Device Twin eszközben csak címkék, jelentett tulajdonságok vagy deviceId használatával hozhat létre célként feltételt.
* Idézőjelek nem engedélyezettek a megcélzott feltétel bármely részén. Használjon aposztrófokat.
* Az aposztrófok a célként megadott feltétel értékeit jelölik. Ezért el kell kerülnie az egyetlen idézőjelet egy másik aposztróftal, ha az eszköz neve része. Ha például egy nevű eszközt szeretne megcélozni, írja a következőt: `operator'sDevice` `deviceId='operator''sDevice'` .
* A megcélzott feltételi értékekben a számok, betűk és a következő karakterek engedélyezettek: `-:.+%_#*?!(),=@;$` .

### <a name="priority"></a>Prioritás

A prioritás határozza meg, hogy a központi telepítést a többi központi telepítéshez képest a célként megadott eszközre kell-e alkalmazni. A központi telepítés prioritása pozitív egész szám, amely nagyobb prioritást jelöl. Ha egy IoT Edge eszközt egynél több üzemelő példány céloz meg, akkor a legmagasabb prioritású üzemelő példány érvényes.  Az alacsonyabb prioritású központi telepítések nincsenek alkalmazva, és nincsenek egyesítve.  Ha egy eszközhöz két vagy több, azonos prioritású üzemelő példány van rendelve, a rendszer a legutóbb létrehozott központi telepítést (a létrehozási időbélyeg alapján határozza meg) alkalmazza.

### <a name="labels"></a>Címkék

A címkék olyan karakterlánc-kulcs/érték párok, amelyek segítségével szűrheti és csoportosíthatja a központi telepítéseket. Egy üzemelő példány több címkével is rendelkezhet. A címkék nem kötelezőek, és nem befolyásolják IoT Edge eszközök tényleges konfigurációját.

### <a name="metrics"></a>Mérőszámok

Alapértelmezés szerint az összes központi telepítés négy mérőszámon jelent meg:

* A **célként** megadott IoT Edge eszközök megfelelnek a központi telepítés célcsoportjának.
* **Alkalmazva** : azokat a célként IoT Edge eszközöket jeleníti meg, amelyeket nem a magasabb prioritású másik telepítés céloz meg.
* A **sikeres jelentéskészítés** megjeleníti azokat a IoT Edge-eszközöket, amelyek jelentette, hogy a modulok telepítése sikeresen megtörtént.
* A **jelentéskészítési hiba** megjeleníti azokat a IoT Edge eszközöket, amelyek azt jelentették, hogy egy vagy több modul telepítése nem sikerült. A hiba további kivizsgálásához kapcsolódjon távolról az eszközökhöz, és tekintse meg a naplófájlokat.

Emellett saját egyéni metrikákat is meghatározhat a telepítés figyeléséhez és kezeléséhez.

A metrikák összegző számlálást biztosítanak azon különböző állapotok számára, amelyeket az eszközök jelenthetnek a telepítési konfiguráció alkalmazásának eredményeképpen. A metrikák lekérhetik a [edgeHub-modul két jelentett tulajdonságát](module-edgeagent-edgehub.md#edgehub-reported-properties), például a *LastDesiredStatus* vagy a *lastConnectTime*. Például:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

A saját mérőszámok hozzáadása nem kötelező, és nem befolyásolja IoT Edge eszközök tényleges konfigurációját.

## <a name="layered-deployment"></a>Rétegzett üzembe helyezés

A többrétegű központi telepítések olyan automatikus központi telepítések, amelyek kombinálhatók a létrehozandó egyedi központi telepítések számának csökkentése érdekében. A többrétegű központi telepítések olyan helyzetekben hasznosak, amelyekben ugyanazokat a modulokat használják újra a különböző kombinációkban számos automatikus központi telepítésben.

A többrétegű központi telepítések azonos alapszintű összetevőkkel rendelkeznek, mint bármely automatikus központi telepítés. Az eszközök az ikreken alapuló címkék alapján célozzák meg az eszközöket, és ugyanazokat a funkciókat biztosítják, mint a címkék, a metrikák és az állapotjelentések. A többrétegű központi telepítések a hozzájuk rendelt prioritásokat is magukban foglalják, de ahelyett, hogy az adott eszközre alkalmazza a központi telepítést, a prioritás határozza meg, hogy a rendszer hogyan rangsorolja az adott eszközön a különböző központi telepítéseket. Ha például két rétegzett üzemelő példánynak van egy modulja vagy azonos nevű útvonala, akkor a magasabb prioritású rétegzett üzemelő példány lesz alkalmazva, miközben a rendszer felülírja az alacsonyabb prioritást.

A rendszer-futtatókörnyezeti modulok, a edgeAgent és a edgeHub nem egy rétegzett telepítés részeként vannak konfigurálva. A többrétegű üzemelő példány által megszabott IoT Edge-eszközökre először a szabványos automatikus telepítést kell alkalmazni. Az automatikus központi telepítés biztosítja azt az alapot, amelyre a rétegzett központi telepítések hozzáadhatók.

Egy IoT Edge eszköz csak egy szabványos automatikus központi telepítést alkalmazhat, de több rétegzett automatikus telepítést is alkalmazhat. Az eszközt célzó minden rétegzett központi telepítésnek magasabb prioritással kell rendelkeznie, mint az adott eszköz automatikus központi telepítése.

Vegyünk például egy olyan vállalat következő forgatókönyvét, amely az épületeket kezeli. IoT Edge modulokat fejlesztettek ki a biztonsági kamerákból, a mozgásérzékelők és a felvonók adatainak gyűjtésére. Azonban nem minden épület képes mindhárom modul használatára. A standard automatikus központi telepítések esetében a vállalatnak egyedi központi telepítéseket kell létrehoznia az összes olyan modul-kombinációhoz, amelyhez az épületeknek szüksége van.

![A standard szintű automatikus központi telepítéseknek minden modul-kombinációt el kell fogadniuk](./media/module-deployment-monitoring/standard-deployment.png)

Ha azonban a vállalat többrétegű automatikus központi telepítésre vált, úgy találják, hogy ugyanazon modul-kombinációkat hozhatnak létre a felügyelethez kevesebb központi telepítéssel rendelkező épületekhez. Mindegyik modul saját rétegzett üzemelő példányával rendelkezik, és az eszköz címkéi határozzák meg, hogy mely modulok legyenek hozzáadva az egyes felépítésekhez.

![A többrétegű automatikus központi telepítések egyszerűbbé teszik az olyan forgatókönyveket, amelyekben ugyanazok a modulok különböző módokon kombinálhatók](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Modul – különálló konfiguráció

Többrétegű központi telepítések esetén előfordulhat, hogy szándékosan vagy más módon két üzemelő példánya van ugyanazzal a modullal, amely egy eszközt céloz meg. Ezekben az esetekben eldöntheti, hogy a magasabb prioritású központi telepítés felülírja-e a modult (Twin), vagy hozzáfűzi azt. Előfordulhat például, hogy olyan központi telepítéssel rendelkezik, amely ugyanazt a modult alkalmazza 100 különböző eszközökre. Az eszközök közül 10 azonban biztonságos létesítményekben van, és további konfigurációra van szükség a proxykiszolgálók közötti kommunikációhoz. Többrétegű üzembe helyezéssel olyan modulokat adhat hozzá, amelyek lehetővé teszik, hogy a 10 eszköz biztonságosan kommunikáljon anélkül, hogy felülírja a meglévő modul különálló információit az alapszintű telepítésből.

Az üzembe helyezési jegyzékben hozzáfűzheti a modulhoz tartozó, a kívánt tulajdonságokat. A standard szintű üzembe helyezés során a tulajdonságok **.** a modul egy különálló, többrétegű központi telepítésben való hozzáadásával a tulajdonságokat a kívánt tulajdonságok új részhalmaza deklarálhatja.

Normál telepítés esetén például hozzáadhatja a szimulált hőmérséklet-érzékelő modult a következő kívánt tulajdonságokkal, amelyek azt jelzik, hogy az adatküldés 5 másodperces időközönként történik:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

Egy rétegzett központi telepítésben, amely egy vagy több azonos eszközt céloz meg, hozzáadhat egy olyan tulajdonságot, amely közli a szimulált érzékelővel, hogy 1000 üzenetet küldjön, majd leállíthatja. Nem kívánja felülírni a meglévő tulajdonságokat, ezért új szakaszt kell létrehoznia a kívánt tulajdonságok között `layeredProperties` , amely az új tulajdonságot tartalmazza:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Az olyan eszközök, amelyeken mindkét üzemelő példány van alkalmazva, a szimulált hőmérséklet-érzékelőhöz tartozó 2. modul következő tulajdonságait fogja tartalmazni:

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

Ha a `properties.desired` modul mezőjét egy többrétegű központi telepítésben állítja be, a modul minden alacsonyabb prioritású üzemelő példányban felül fogja írni a kívánt tulajdonságokat.

## <a name="phased-rollout"></a>Szakaszos bevezetés

A többfázisú bevezetés egy átfogó folyamat, amellyel a kezelők a IoT Edge-eszközök bővítési csoportjának módosításait helyezik üzembe. A cél az, hogy fokozatosan végezze el a módosításokat, hogy csökkentse a nagy léptékű törések változásának kockázatát. Az automatikus központi telepítések segítik a többfázisú bevezetések kezelését IoT Edge-eszközök flottáján keresztül.

A szakaszos bevezetést a következő fázisokban és lépésekben hajtja végre:

1. Hozzon létre egy tesztkörnyezet IoT Edge eszközök kiépítésével, és állítsa be az eszköz kettős címkéjét, például: `tag.environment='test'` . A tesztkörnyezetben tükröznie kell azt a éles környezetet, amelyet a központi telepítés végül megcéloz.
2. Hozzon létre egy központi telepítést, beleértve a kívánt modulokat és konfigurációkat. A célzási feltételnek meg kell céloznia a teszt IoT Edge eszköz környezetét.
3. Az új modul konfigurációjának ellenőrzése a tesztkörnyezetben.
4. Frissítse a központi telepítést, hogy az éles IoT Edge-eszközök egy részhalmazát adja hozzá egy új címke hozzáadásával a célzási feltételhez. Ügyeljen arra is, hogy a központi telepítés prioritása nagyobb legyen, mint a jelenleg az eszközökre irányuló egyéb központi telepítések
5. A központi telepítés állapotának megtekintésével ellenőrizze, hogy a központi telepítés sikeresen megtörtént-e a megcélozott IoT-eszközökön.
6. Frissítse a központi telepítést az összes fennmaradó éles IoT Edge eszköz megcélzásához.

## <a name="rollback"></a>Visszaállítás

A központi telepítések visszaállíthatók, ha hibákat vagy helytelen konfigurációkat kap. Mivel a központi telepítés egy IoT Edge eszköz abszolút moduljának konfigurációját definiálja, egy további központi telepítésnek is ugyanazt az eszközt kell céloznia, ha a cél az összes modul eltávolítása.  

A központi telepítés törlése nem távolítja el a modulokat a célként megadott eszközökről. Egy másik központi telepítésnek kell lennie, amely új konfigurációt határoz meg az eszközökhöz, még akkor is, ha ez egy üres telepítés.

Hajtsa végre a visszaállításokat a következő sorozatban:

1. Győződjön meg arról, hogy egy második központi telepítés is meg van célozva ugyanarra az eszközre. Ha a visszaállítás célja az összes modul eltávolítása, a második telepítés nem tartalmazhat modulokat.
2. Módosítsa vagy távolítsa el annak a központi telepítésnek a cél feltételét, amelyet vissza szeretne állítani, hogy az eszközök többé ne legyenek kielégítve a célzási feltételnek.
3. A telepítés állapotának megtekintésével ellenőrizze, hogy a visszaállítás sikeres volt-e.
   * A visszaállított központi telepítésnek többé nem kell megjelennie a visszaállított eszközök állapotának megjelenítéséhez.
   * A második központi telepítésnek ekkor tartalmaznia kell a visszaállított eszközök telepítési állapotát.

## <a name="next-steps"></a>Következő lépések

* Végigvezeti a központi telepítések létrehozásához, frissítéséhez vagy törléséhez szükséges lépéseket a [IoT Edge modulok nagy léptékű üzembe helyezéséhez és figyeléséhez](how-to-deploy-at-scale.md).
* További információ a [IoT Edge futtatókörnyezettel](iot-edge-runtime.md) és a [IoT Edge modulokkal](iot-edge-modules.md)kapcsolatos egyéb IoT Edge fogalmakról.
