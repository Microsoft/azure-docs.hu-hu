---
title: Az eszköz adatai elemzése az Azure IoT Central alkalmazásban | Microsoft Docs
description: Elemezze az eszköz adatait az Azure IoT Central alkalmazásban.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f329b6377915a3df6e3f3e212cbd8d41f9ed80d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90017660"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Az eszköz-adatelemzés használata az eszközökön

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Az Azure IoT Central gazdag elemzési funkciókat kínál a korábbi trendek elemzéséhez és az eszközök különböző telemetriáiról összekapcsolásához. Első lépésként látogasson el az **elemzésre** a bal oldali ablaktáblán.

## <a name="understanding-the-analytics-ui"></a>Az elemzési felhasználói felület ismertetése
Az elemzési felhasználói felület három fő összetevőből áll:
- **Adatkonfiguráció panel:** A konfiguráció panelen válassza ki azt az erőforráscsoportot, amelynek az adatelemzését el szeretné elemezni. Ezután válassza ki az elemezni kívánt telemetria, és válassza ki az összesítési módszert az egyes telemetria. A **felosztás** a vezérléssel segít az adatcsoportosításban az eszköz tulajdonságait dimenzióként használva.

- **Idő vezérlőelem:** Az idővezérlő használatával kiválaszthatja az időtartamot, amelynek az adatelemzését el szeretné elemezni. Az időtartomány kijelöléséhez húzza az idő csúszkát az idő végére. Az idővezérlő is tartalmaz egy **intervallum-méretezési** csúszkát, amely szabályozza a gyűjtőt vagy az adatösszesítéshez használt intervallum méretét. 

- **Diagram vezérlőelem:** A diagram vezérlőelem soronként jeleníti meg az adatdiagramot. Az adott vonalak láthatóságát a diagram jelmagyarázatával is átválthatja. 


  ![Az elemzési felhasználói felület áttekintése](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Az adatai lekérdezése

Először ki kell választania egy telemetria, és az elemezni kívánt **eszközöket**. Ha elkészült, válassza az **elemzés** lehetőséget az adatai megjelenítésének megkezdéséhez.

- **Eszközcsoport:** Az [eszközcsoport](tutorial-use-device-groups.md) egy felhasználó által definiált eszköz. Például az összes hűtőszekrény a Oaklandben, vagy az összes 2,0-es szélturbinák.

- **Telemetria:** Válassza ki az elemezni és felderíteni kívánt telemetria. Egyszerre több telemetriáiról is kiválaszthat. Az alapértelmezett összesítési módszer az átlag értékre van állítva, amely numerikus és darabszám típusú. A numerikus adattípusok támogatott összesítési módszerei az átlag, a maximum, a minimum, a darabszám és a összeg.  A karakterlánc adattípusának támogatott összesítési módszerei a darabszám.

- **Felosztás:** A "Split by" vezérlő segít az adatcsoportosításban az eszköz tulajdonságait dimenzióként használva. Az eszköz és a felhő tulajdonságainak értékei a telemetria és az eszköz által történő küldéskor is összekapcsolhatók. Ha a felhő vagy az eszköz tulajdonság frissítve lett, a telemetria a diagramon különböző értékek szerint csoportosítva jelenik meg.

    > [!TIP]
    > Ha külön szeretné megtekinteni az egyes eszközökre vonatkozó adatmegjelenítést, válassza az eszköz azonosítója elemet a "felosztás" vezérlőelemben.

## <a name="interacting-with-your-data"></a>Interakció az adataival

Miután lekérdezte az adatait, megkezdheti a megjelenítését a vonalas diagramon. Megjelenítheti/elrejtheti a telemetria, módosíthatja az időtartamot, megtekintheti a telemetria egy adatrácsban.

- **Időszerkesztő panel:** Alapértelmezés szerint az elmúlt egy nap adatait fogjuk beolvasni. Az idő hosszának megváltoztatásához húzza az időcsúszka végét. A naptár vezérlővel is kiválaszthatja az előre meghatározott időgyűjtők egyikét, vagy kiválaszthat egy egyéni időtartományt. Az idővezérlő is tartalmaz egy **intervallum-méretezési** csúszkát, amely szabályozza a gyűjtőt vagy az adatösszesítéshez használt intervallum méretét.

    ![Időszerkesztő](media/howto-create-analytics/timeeditorpanel.png)

    - **Belső dátumtartomány csúszka eszköz**: a két végpont vezérlőelem használatával húzza őket a kívánt időtartományra. Ezt a belső dátumtartományt a külső dátumtartomány csúszka vezérlőeleme korlátozza.
    
   
    - **Külső dátumtartomány csúszka vezérlő**: a végpont vezérlőelemekkel kiválaszthatja a külső dátumtartományt, amely a belső dátumtartomány vezérlőelemhez lesz elérhető.

    - **Dátumtartomány gombjának növelése és csökkentése**: növelje vagy csökkentse az időtartományt úgy, hogy kijelöli a kívánt időszakra vonatkozó gombot.

    - **Intervallum-méret csúszka**: ezzel a beállítással az adott időkereten belül és kívül is nagyíthatja az intervallumokat. Ez a művelet a nagy szeletek közötti mozgás pontosabb szabályozását teszi lehetővé. Használhatja az adatai részletes, nagy felbontású nézeteinek megtekintésére is, akár ezredmásodpercig is. A csúszka alapértelmezett kiindulási pontja a kiválasztott adatok legoptimálisabb nézete, amely egyensúlyt teremt a felbontás, a lekérdezés sebessége és a részletesség szempontjából.
    
    - **Dátumtartomány-választó**: ezzel a webes vezérlővel egyszerűen kiválaszthatja a kívánt dátum-és időtartományokat. A vezérlőelemet a különböző időzónák közötti váltásra is használhatja. Miután elvégezte a módosítások alkalmazását az aktuális munkaterületre, válassza a mentés lehetőséget.

    > [!TIP]
    > Az intervallum méretét a rendszer dinamikusan határozza meg a kijelölt időtartomány alapján. A kisebb időkeretek lehetővé teszik, hogy az adatokat nagyon részletes időközönként, néhány másodpercen belül összesítse.


- **Diagram jelmagyarázata:** A diagram jelmagyarázata a diagram kiválasztott telemetria jeleníti meg. Az egérmutatót a jelmagyarázat minden eleme fölé helyezheti, hogy az a diagramon fókuszba kerüljön. A "Split by" használatakor a telemetria a kiválasztott dimenzió megfelelő értékei szerint csoportosítja. A csoport nevére kattintva válthat az egyes telemetria vagy a teljes csoport láthatósága is.  


- **Y tengely formátumának vezérlője:** az y tengely üzemmódja az elérhető y tengelyes nézet beállításain keresztül vált. Ez a vezérlő csak akkor érhető el, ha a különböző telemetriáiról láthatóvá válnak. Az y tengelyt úgy állíthatja be, hogy a következő három mód közül választhat:

    - **Halmozott:** Minden telemetria gráfja halmozott, és mindegyik gráf saját y tengellyel rendelkezik. Ez a mód alapértelmezettként van beállítva.
    - **Megosztott:** Minden telemetria gráfja ugyanazon y tengelyen van ábrázolva.
    - **Átfedés:** Ezzel a beállítással több sort is halmozhat ugyanazon y tengelyen, és az y tengely adatváltozása a kijelölt sor alapján változik.

  ![Adatelrendezés az y tengelyen különböző vizualizációs módokkal](media/howto-create-analytics/yaxiscontrol.png)

- **Nagyítási vezérlő:** A nagyítással tovább részletezheti az adatait. Ha olyan időszakot talál, amelyet szeretne összpontosítani az eredményhalmaz alapján, az egérmutatóval fogja megragadni a területét, majd húzza azt a választott végpontra. Ezután kattintson a jobb gombbal a kijelölt régióra, majd kattintson a nagyítás lehetőségre.

  ![Nagyítás az adattartalomba](media/howto-create-analytics/zoom.png)

A három pont alatt több diagram vezérlőelemet is használhat az adatkezeléshez:

- **Rács megjelenítése:** Az eredmények táblázatos formában érhetők el, így megtekintheti az egyes adatpontok adott értékét.

- **Letöltés CSV-fájlként:** Az eredmények vesszővel tagolt (CSV) fájlként exportálhatók. A CSV-fájl minden eszközhöz tartalmaz adatkészletet. Az eredményeket a megadott intervallum és időkeret alapján exportálja a rendszer. 

- **Jelölő eldobása:** A "drop marker" vezérlővel bizonyos adatpontokat lehet rögzíteni a diagramon. Akkor hasznos, ha több sor esetében próbál meg összehasonlítani különböző időszakokban lévő információkat.

  ![Az elemzések rács nézetének megjelenítése](media/howto-create-analytics/additionalchartcontrols.png)
