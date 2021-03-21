---
title: Térképek létrehozása
description: Ez a cikk bemutatja, hogyan hozhatja ki a térképeket az Azure FarmBeats-ben.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: d0176fd337abd37f97a6dbe5cf7c68ccf94114d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173864"
---
# <a name="generate-maps"></a>Térképek létrehozása

Az Azure FarmBeats használatával a következő térképeket hozhatja ki a műholdképek és az érzékelő adatbemenetek használatával. A Maps segítségével megtekintheti a farm földrajzi helyét, és azonosíthatja az eszközök megfelelő helyét.

  - **Szenzor-elhelyezési Térkép**: ajánlásokat biztosít a használatban lévő érzékelők számának és a farmon való elhelyezésének módjáról.
  - **Satellite** indexek térképe: a farm vegetációs indexét és a víz indexét jeleníti meg.
  - A **talaj nedvességtartalmának hő**: a talaj nedvességtartalmának eloszlását mutatja be a műholdon tárolt adatforgalom és az érzékelői adatforgalom

## <a name="sensor-placement-map"></a>Érzékelő elhelyezési leképezése

A FarmBeats-érzékelő elhelyezési térképe segíti a talaj nedvességtartalmának érzékelők elhelyezését. A Térkép kimenete az érzékelők központi telepítésének koordinátáit tartalmazza. Ezekből az érzékelőkből származó bemeneteket a rendszer a műholdas képekkel együtt használja a talaj nedvességtartalmának hő létrehozásához.

Ez a Térkép az év során több dátummal ellátott lombkorona szegmentálásával származtatható. A lombkorona része még a puszta talaj és az épületek is. A helyen nem szükséges érzékelők eltávolíthatók. Ez a Térkép útmutatásként szolgál, és a pozíciót és a számokat a saját tudása alapján kis mértékben módosíthatja. Az érzékelők hozzáadása nem visszafejlődés a hő eredményeit, de a hő pontossága is csökkenhet, ha az érzékelő száma csökken.

## <a name="before-you-begin"></a>Előkészületek

A következő előfeltételeknek kell megfelelnie az érzékelő elhelyezési térképének létrehozása előtt:

- A farm méretének egynél több hektárnak kell lennie.
- A felhő nélküli Sentinel-jelenetek számának a kiválasztott dátumtartomány esetében hatnál nagyobbnak kell lennie.
- Legalább hat felhőalapú Sentinel-jelenetnek a 0,3-nál nagyobb vagy azzal egyenlő, normalizált különbségi vegetációs indexszel (NDVI) kell rendelkeznie.

    > [!NOTE]
    > A Sentinel csak két egyidejű szálat engedélyez felhasználónként. Ennek eredményeképpen a feladatok várólistára kerülnek, és hosszabb időt is igénybe vehetnek.

### <a name="dependencies-on-sentinel"></a>A Sentinel függőségei

A Sentinel a következő függőségekre vonatkozik:

- A műhold-lemezképek letöltésére a Sentinel-teljesítmény függ. A Sentinel teljesítmény állapotának és karbantartási [tevékenységeinek](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)ellenőrzését.
- A Sentinel csak két egyidejű [letöltési szálat](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) engedélyez felhasználónként.
- A precíziós leképezések generációját a [Sentinel-lefedettség és az újra-látogatás gyakorisága]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)befolyásolja.

## <a name="create-a-sensor-placement-map"></a>Érzékelő elhelyezési Térkép létrehozása
Ez a szakasz az érzékelő elhelyezési leképezések létrehozási eljárásait részletezi.

> [!NOTE]
> Az érzékelő elhelyezési leképezését kezdeményezheti a **térképek** oldalról vagy a **Farm részletek** lapján található **precíziós térképek létrehozása** legördülő menüből.

Hajtsa végre az alábbi lépéseket.

1. A kezdőlapon válassza a **Maps** elemet a bal oldali navigációs menüből.
2. Válassza a **térképek létrehozása** lehetőséget, majd a legördülő menüben válassza az **érzékelő elhelyezése** lehetőséget.

    ![Az érzékelő elhelyezésének kiválasztása](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Az **érzékelő elhelyezésének** kiválasztása után megjelenik az **érzékelő elhelyezése** ablak.

    ![Érzékelő elhelyezése ablak](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Válasszon ki egy farmot a **Farm** legördülő menüből.
   A farm kereséséhez és kiválasztásához görgessen a legördülő listából, vagy írja be a farm nevét a szövegmezőbe.
5. Az előző évhez tartozó Térkép létrehozásához válassza az **ajánlott** lehetőséget.
6. Ha térképet szeretne készíteni egy egyéni dátumtartomány számára, válassza a **Dátumtartomány kiválasztása** lehetőséget. Adja meg azt a kezdő és záró dátumot, amelyhez az érzékelő elhelyezési leképezését elő szeretné állítani.
7. Válassza a **leképezések előállítása** lehetőséget.
 Megjelenik egy megerősítő üzenet a feladatok részleteivel.

  A feladat állapotáról további információt a **feladatok megtekintése** című szakaszban talál. Ha a feladatok állapota *nem sikerül*, a rendszer részletes hibaüzenetet jelenít meg a *sikertelen* állapot elemleírásában. Ebben az esetben ismételje meg az előző lépéseket, és próbálkozzon újra.

  Ha a probléma továbbra is fennáll, tekintse meg a [hibakeresés](troubleshoot-azure-farmbeats.md) szakaszt, vagy lépjen kapcsolatba az [Azure FarmBeats Fórumával a kapcsolódó naplók támogatásához](https://aka.ms/FarmBeatsMSDN) .

### <a name="view-and-download-a-sensor-placement-map"></a>Érzékelő elhelyezési térképének megtekintése és letöltése

Hajtsa végre az alábbi lépéseket.

1. A kezdőlapon válassza a **Maps** elemet a bal oldali navigációs menüből.

    ![Válasszon térképeket a bal oldali navigációs menüből](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Válassza a **szűrő** lehetőséget az ablak bal oldali navigációs sávján.
  A **szűrő** ablak a keresési feltételeket jeleníti meg.

    ![Szűrő ablak](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Válassza ki a **típus**, a **dátum** és a **név** értékeket a legördülő menükből. Ezután az **alkalmaz** gombra kattintva megkeresheti a megtekinteni kívánt térképet.
  A feladatok létrehozásának dátuma type_farmname_YYYY-hh-nn formátumban jelenik meg.
4. Görgessen végig az elérhető térképek listáján az oldal végén lévő navigációs sávok használatával.
5. Válassza ki a megtekinteni kívánt térképet. Megjelenik egy előugró ablak, amely a kiválasztott Térkép előnézetét jeleníti meg.
6. Válassza a **Letöltés** lehetőséget, majd töltse le az érzékelő koordinátáinak GeoJSON-fájlját.

    ![Érzékelő elhelyezési térképének előnézete](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Szatellit indexek térképe

A következő szakaszokban ismertetjük a műholdas indexek térképének létrehozásával és megtekintésével kapcsolatos eljárásokat.

> [!NOTE]
> A **Térkép** lapról vagy a **pontos térképek létrehozása** legördülő menüből a **Farm részletek** lapján kezdeményezheti a szatellit indexek leképezését.

A FarmBeats lehetővé teszi a NDVI, a továbbfejlesztett vegetációs index (EVI) és a normalizált különbözeti (NDWI) leképezések létrehozását a farmok számára. Ezek az indexek segítenek meghatározni, hogyan növekszik a termés, vagy a múltban, valamint a reprezentatív vízszinteket a helyszínen.


> [!NOTE]
> A Térkép létrehozási napjaihoz Sentinel-rendszerkép szükséges.


## <a name="create-a-satellite-indices-map"></a>Műholdas indexek létrehozása – Térkép

Hajtsa végre az alábbi lépéseket.

1. A kezdőlapon válassza a **Maps** elemet a bal oldali navigációs menüből.
2. Válassza a **térképek létrehozása** lehetőséget, majd a legördülő menüben válassza a **Satellite indexek** lehetőséget.

    ![Válassza ki a Satellite indexeket a legördülő menüből](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. A **szatellit** indexek kiválasztása után megjelenik a **Satellite indexek** ablak.

    ![Satellite indexek ablak](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Válasszon ki egy farmot a legördülő menüből.
   A farm kereséséhez vagy kiválasztásához görgessen a legördülő listából, vagy adja meg a farm nevét.   
5. Az elmúlt hét térképének létrehozásához válassza ki **ezt a hetet**.
6. Ha térképet szeretne készíteni egy egyéni dátumtartomány számára, válassza a **Dátumtartomány kiválasztása** lehetőséget. Adja meg azt a kezdő és záró dátumot, amelynek a műhold-indexek leképezését elő szeretné állítani.
7. Válassza a **leképezések előállítása** lehetőséget.
    Megjelenik egy megerősítő üzenet a feladatok részleteivel.

    ![Satellite indexek Térkép megerősítő üzenet](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    A feladat állapotáról további információt a **feladatok megtekintése** című szakaszban talál. Ha a feladatok állapota *nem sikerül*, a rendszer részletes hibaüzenetet jelenít meg a *sikertelen* állapot elemleírásában. Ebben az esetben ismételje meg az előző lépéseket, és próbálkozzon újra.

    Ha a probléma továbbra is fennáll, tekintse meg a [hibakeresés](troubleshoot-azure-farmbeats.md) szakaszt, vagy lépjen kapcsolatba az [Azure FarmBeats Fórumával a kapcsolódó naplók támogatásához](https://aka.ms/FarmBeatsMSDN) .

### <a name="view-and-download-a-map"></a>Térkép megtekintése és letöltése

Hajtsa végre az alábbi lépéseket.

1. A kezdőlapon válassza a **Maps** elemet a bal oldali navigációs menüből.

    ![Térképek kiválasztása](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Válassza a **szűrő** lehetőséget az ablak bal oldali navigációs sávján. A **szűrő** ablak a keresési feltételeket jeleníti meg.

    ![A szűrő ablak a keresési feltételeket jeleníti meg](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Válassza ki a **típus**, a **dátum** és a **név** értékeket a legördülő menükből. Ezután az **alkalmaz** gombra kattintva megkeresheti a megtekinteni kívánt térképet.
  A feladatok létrehozásának dátuma type_farmname_YYYY-hh-nn formátumban jelenik meg.

4. Görgessen végig az elérhető térképek listáján az oldal végén lévő navigációs sávok használatával.
5. A **farm nevének** és **dátumának** minden kombinációja esetében a következő három Térkép érhető el:
    - NDVI
    - EVI
    - NDWI
6. Válassza ki a megtekinteni kívánt térképet. Megjelenik egy előugró ablak, amely a kiválasztott Térkép előnézetét jeleníti meg.
7. A letöltési formátum kiválasztásához válassza a **Letöltés** lehetőséget a legördülő menüből. A Térkép le van töltve, és a számítógép helyi mappájában tárolódik.

    ![Kiválasztott Satellite indexek – előzetes verzió](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Talaj nedvességtartalmának hő

A talaj nedvességtartalma a talaj részecskék közötti térközben tárolt víz.A talaj nedvesség-hő segít megérteni a talaj nedvességtartalmának adatait bármilyen mélységben, a farmon belül nagy felbontásban. Pontos és felhasználható nedvesség-hő létrehozásához az érzékelők egységes üzembe helyezésére van szükség. Az érzékelőknek ugyanabból a szolgáltatóból kell származnia. A különböző szolgáltatók eltérő módon határozzák meg a talaj nedvességtartalmának mértékét, a kalibrálási különbségekkel együtt. A hő az adott mélységben üzembe helyezett érzékelők használatával jön létre.

### <a name="before-you-begin"></a>Előkészületek

A következő előfeltételeknek kell megfelelnie, mielőtt megkísérli a talaj nedvességtartalmának hő:

- Legalább három nedvesség-érzékelőt kell üzembe helyezni. Ne próbálja meg létrehozni a talaj nedvességtartalmát az érzékelők üzembe helyezése és a farmhoz való hő előtt.
- A talaj nedvességtartalmának hő a Sentinel Path lefedettsége, a Cloud Cover és a Cloud Shadow is befolyásolja. Legalább egy felhőalapú Sentinel-színtérnek elérhetőnek kell lennie az elmúlt 120 napban, azon a napon, ameddig a hő kérték.
- A farmon üzembe helyezett érzékelők legalább felének online állapotban kell lennie, és adatfolyamot kell biztosítania a datahub.
- A hő ugyanabból a szolgáltatóból származó Sensor-mértékek használatával kell létrehozni.


## <a name="create-a-soil-moisture-heatmap"></a>Talaj nedvességtartalmának hő létrehozása

Hajtsa végre az alábbi lépéseket.

1. A kezdőlapon kattintson a Maps ( **térképek** ) elemre a bal oldali navigációs menüben a **Maps** oldal megtekintéséhez.
2. Válassza a **térképek létrehozása** lehetőséget, majd a legördülő menüből válassza a **talaj nedvesség** lehetőséget.

    ![Válassza ki a talaj nedvességtartalmát a legördülő menüből](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. A **talaj nedvességének** kiválasztása után megjelenik a **talaj nedvességtartalma** ablak.

    ![A talaj nedvességtartalmának ablaka](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Válasszon ki egy farmot a **Farm** legördülő menüből.
   A farm kereséséhez és kiválasztásához görgessen a legördülő listából, vagy adja meg a farm nevét a **Farm kiválasztása** legördülő menüben.
5. A **felszín nedvesség-érzékelői mértékének kiválasztása** legördülő menüben válassza ki a talaj nedvességtartalmának érzékelő mértékét (mélységét), amelyhez a térképet elő szeretné állítani.
Az érzékelő mértékének megkereséséhez nyissa meg az **érzékelők** elemet, és válassza ki a talaj nedvességtartalmának érzékelőjét. Ezután az **érzékelő tulajdonságai** szakaszban használja a **mérték neve** értéket.
6. Ha **ma** vagy **ezen a héten** szeretne térképet készíteni, válassza ki az egyik lehetőséget.
7. Ha térképet szeretne készíteni egy egyéni dátumtartomány számára, válassza a **Dátumtartomány kiválasztása** lehetőséget. Adja meg azt a kezdő és záró dátumot, amelynek a talaj-nedvesség hő elő kívánja állítani.
8. Válassza a **leképezések előállítása** lehetőséget.
 Megjelenik egy megerősítő üzenet a feladatok részleteivel.

   ![A talaj nedvességtartalmának leképezését kérő üzenet](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    A feladat állapotáról további információt a **feladatok megtekintése** című szakaszban talál. Ha a feladatok állapota *nem sikerül*, a rendszer részletes hibaüzenetet jelenít meg a *sikertelen* állapot elemleírásában. Ebben az esetben ismételje meg az előző lépéseket, és próbálkozzon újra.

    Ha a probléma továbbra is fennáll, tekintse meg a [hibakeresés](troubleshoot-azure-farmbeats.md) szakaszt, vagy lépjen kapcsolatba az [Azure FarmBeats Fórumával a kapcsolódó naplók támogatásához](https://aka.ms/FarmBeatsMSDN) .

### <a name="view-and-download-a-map"></a>Térkép megtekintése és letöltése

Hajtsa végre az alábbi lépéseket.

1. A kezdőlapon válassza a **Maps** elemet a bal oldali navigációs menüből.

    ![Ugrás a Maps-re](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Válassza a **szűrő** lehetőséget az ablak bal oldali navigációs sávján. Megjelenik a **szűrő** ablak, ahol kereshet térképeket.

    ![Szűrő kiválasztása a bal oldali navigációs sávon](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Válassza ki a **típus**, a **dátum** és a **név** értékeket a legördülő menükből. Ezután az **alkalmaz** gombra kattintva megkeresheti a megtekinteni kívánt térképet. A feladatok létrehozásának dátuma type_farmname_YYYY-hh-nn formátumban jelenik meg.
4. Válassza a tábla fejlécek melletti **Rendezés** ikont a **Farm**, a **dátum**, **a létrehozás, a** feladat **azonosítója** és a **feladattípus** alapján történő rendezéshez.
5. Görgessen végig az elérhető térképek listáján az oldal végén található navigációs gombok használatával.
6. Válassza ki a megtekinteni kívánt térképet. Megjelenik egy előugró ablak, amely a kiválasztott Térkép előnézetét jeleníti meg.
7. A letöltési formátum kiválasztásához válassza a **Letöltés** lehetőséget a legördülő menüből. A rendszer letölti és tárolja a térképet a megadott mappában.

    ![A talaj nedvességtartalmának hő előzetes verziója](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
