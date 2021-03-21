---
title: Mik az Azure IoT Central kiskereskedelmi megoldásai | Microsoft Docs
description: Ismerje meg, hogyan használhatók az Azure IoT Central alkalmazás-sablonok a csatlakoztatott logisztika, a digitális terjesztési központ, az áruházbeli elemzés, a feltételek figyelése, a pénztár, az intelligens leltár-kezelés és a kiskereskedelmi megoldások létrehozásához.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 56198cf661001bcfcec45d483d0f691234851894
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832283"
---
# <a name="what-are-the-iot-central-retail-solutions"></a>Mik a IoT Central kiskereskedelmi megoldások?

Az Azure IoT Central egy IoT, amely csökkenti a nagyvállalati szintű IoT-megoldások fejlesztésével, kezelésével és karbantartásával járó terheket és költségeket. Az Azure IoT Central kiválasztásával lehetősége van arra, hogy az Ön üzleti adatait IoT-adataival alakítsa át, és nem csupán egy összetett és folyamatosan fejlődő IoT-infrastruktúra karbantartására és frissítésére koncentrál.

Ez a cikk több, a kiskereskedelmi IoT Central alkalmazás sablonjait ismerteti. Megoldás-szerkesztőként használhatja ezeket a sablonokat olyan IoT-megoldások készítéséhez, amelyek optimalizálják az ellátási láncokat, javítják az ügyfelek számára az áruházbeli tapasztalatokat, és hatékonyabban nyomon követhetik a leltárt.

:::image type="content" source="media/overview-iot-central-retail/retail-app-templates.png" alt-text="Az Azure IoT kiskereskedelmi áttekintése":::

Az alábbi szakaszok ismertetik az alkalmazás-sablonok képességeit:

## <a name="connected-logistics"></a>Összekapcsolt logisztika

A globális logisztikai költségek várhatóan elérik a $10 600 000 000 000-es kiadást 2020-ben. A kiadások és a hajózási szolgáltatók nagy részének szállítása jelentős versenynyomást és kényszereket biztosít.

A IoT érzékelőkkel olyan környezeti körülményeket gyűjthet és figyelheti, mint például a hőmérséklet, a páratartalom, a dőlés, a sokk, a fény és a szállítás helye. A IoT érzékelőkből és eszközökből gyűjtött telemetria kombinálhatja más adatforrásokkal, például időjárási és forgalmi információkkal a felhőalapú üzleti intelligencia rendszerekben.

A csatlakoztatott logisztikai megoldás előnyei a következők:

* A szállítmány monitorozása valós idejű nyomon követéssel. 
* A szállítmány integritásának biztosítása valós idejű környezetiadat-monitorozással.
* A szállítmány ellopása, elvesztése vagy sérülése elleni védelem.
* Geokerítés alkalmazása, útvonal-optimalizálás, flottakezelés és járműelemzés.
* A szállítmányok becsülhető indulási és érkezési időpontjának előrejelzése.

A következő képernyőképek az alkalmazás sablonjában lévő beépített irányítópultot mutatják be. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldás követelményeinek:

:::image type="content" source="media/overview-iot-central-retail/connected-logistics-dashboard1.png" alt-text="A kapcsolódó logisztikai műveletek irányítópultjának felső felét bemutató képernyőkép.":::

:::image type="content" source="media/overview-iot-central-retail/connected-logistics-dashboard2.png" alt-text="Képernyőkép, amely a csatlakoztatott logisztikai műveletek irányítópultjának alsó felét mutatja.":::

További információért lásd: az [üzembe helyezés és az útmutató egy csatlakoztatott logisztikai alkalmazás sablonjának](./tutorial-iot-central-connected-logistics.md) létrehozásához.

## <a name="digital-distribution-center"></a>Digitális terjesztési központ

Mivel a gyártók és a kiskereskedők globális jelenlétet alkotnak, az ellátási láncok egyre összetettebbek. A fogyasztók mostantól nagy számú termék elérhetővé tételére számíthatnak, és ezek a termékek egy vagy két napon belül érkeznek. A terjesztési központoknak alkalmazkodnia kell ezekhez a trendekhez, és a meglévő eredménytelenség leküzdéséhez.

Napjainkban a manuális munkaterhelésnek köszönhetően a kitárolási és csomagolási fiókok a Distribution Center költségeinek 55-65%-át használják. A manuális kivételezés és a csomagolás is általában lassabb, mint az automatizált rendszerek, és a munkaerő gyors ingadozása még nehezebbé teszi a szállítási kötetek teljesítését. Ez az idényjellegű ingadozás a személyzet magas szintű forgalmát eredményezi, és növelheti a költséges hibák valószínűségét.

A IoT-kompatibilis kamerákon alapuló megoldások a digitális visszajelzési hurok engedélyezésével képesek átalakítási előnyökre. A terjesztési központból származó adatok a gyakorlatban hasznosítható elemzésekhez vezetnek, amelyek viszont jobb adatmennyiséget eredményeznek.

A digitális terjesztési központ előnyei a következők:

* A kamerák megfigyelik az árukat a megérkezésük után, és áthelyezhetik azokat a továbbító rendszeren.
* Hibás termékek automatikus azonosítása.
* Hatékony megrendelés nyomon követése.
* Csökkentett költségek, hatékonyabb termelékenység és optimalizált használat.

Az alábbi képernyőfelvételen a beépített irányítópult látható az alkalmazás sablonjában. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldás követelményeinek: 

:::image type="content" source="media/overview-iot-central-retail/digital-distribution-center-dashboard.png" alt-text="Digitális terjesztési központ irányítópultja":::

További információért lásd: a [Digital Distribution Center alkalmazás-sablon üzembe helyezése és](./tutorial-iot-central-digital-distribution-center.md) áttekintő oktatóanyaga.

## <a name="in-store-analytics---condition-monitoring"></a>Tárolt elemzési állapot figyelése

Számos kiskereskedő esetében az áruházban lévő környezeti feltételek kulcsfontosságú differenciált versenytársaik számára. A kereskedők kellemes feltételeket szeretnének fenntartani az üzleteik számára az ügyfelek számára.  

Megoldás-szerkesztőként használhatja a IoT Central in-store Analytics feltételeit figyelő alkalmazás sablonját, amellyel teljes körű megoldást hozhat létre. Az alkalmazás sablonja lehetővé teszi, hogy digitálisan kapcsolódjon egy kiskereskedelmi tárolási környezethez, és figyelje a különböző típusú érzékelő eszközök használatával. Ezek az érzékelők-eszközök olyan telemetria hoznak létre, amelyeket üzleti elemzési eredményekre válthatnak, így csökkenthetik az üzemeltetési költségeket, és nagyszerű élményt nyújtanak ügyfeleiknek.

Az alkalmazás sablonjának használata a következőhöz:

* Különböző típusú IoT-érzékelők összekapcsolhatók egy IoT Central alkalmazás-példánnyal.
* Az érzékelő hálózat és a környezetben található átjárók állapotának monitorozása és felügyelete.
* Hozzon létre egyéni szabályokat az áruházban lévő környezeti feltételek köré, hogy riasztásokat indítson az áruház-kezelők számára.
* Alakítsa át az áruházban lévő környezeti körülményeket, és Ismerje meg, hogy a kiskereskedelmi áruház csapata az ügyfelek felhasználói élményének javítására használja fel.
* A kiskereskedelmi munkatársak számára hasznos és kellő időben elérhetővé teszi az összesített információk meglévő vagy új üzleti alkalmazásokba való exportálását.

Az alkalmazás sablonja tartalmazza az eszközök sablonjait, és szimulált eszközök használatával tölti fel az irányítópultot. 

Az alábbi képernyőfelvételen a beépített irányítópult látható az alkalmazás sablonjában. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldás követelményeinek: 

:::image type="content" source="media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png" alt-text="Az áruházbeli elemzési feltétel figyelése":::

További információért lásd az [Azure-ban tárolt elemzési alkalmazás létrehozása az Azure IoT Central](./tutorial-in-store-analytics-create-app.md) oktatóanyagban című témakört.

## <a name="in-store-analytics---checkout"></a>Tárolt elemzések – pénztár

Egyes kiskereskedőknél az áruházban lévő fizetési élmény a versenytársak egyik kulcsfontosságú megkülönböztetője. A kiskereskedők zökkenőmentes fizetési lehetőséget szeretnének biztosítani az áruházban, hogy az ügyfelek visszatérjenek.  

Megoldás-szerkesztőként használhatja a IoT Central in-store Analytics Checkout-alkalmazás sablonját olyan megoldás létrehozásához, amely az áruház pénztári zónáján belüli elemzéseket biztosít a kiskereskedelmi munkatársaknak. Az érzékelők például adatokat biztosíthatnak a várólista hosszáról, valamint az egyes pénztárak átlagos várakozási idejéről.

Az alkalmazás sablonjának használata a következőhöz:

* Különböző típusú IoT-érzékelők összekapcsolhatók egy IoT Central alkalmazás-példánnyal.
* Az érzékelő hálózat és a környezetben található átjárók állapotának monitorozása és felügyelete.
* Hozzon létre egyéni szabályokat a pénztári feltétel köré a tárolón belül a kereskedelmi személyzet riasztásának elindításához.
* Alakítsa át a pénztári feltételeket az áruházban, és tekintse át, hogy a kiskereskedelmi áruház csapata hogyan növelheti a felhasználói élményt.
* A kiskereskedelmi munkatársak számára hasznos és kellő időben elérhetővé teszi az összesített információk meglévő vagy új üzleti alkalmazásokba való exportálását.

Az alkalmazás sablonja tartalmazza az eszközök sablonjait, és szimulált eszközök használatával tölti fel az irányítópultot a sáv kihasználtsági adataival. 

Az alábbi képernyőfelvételen a beépített irányítópult látható az alkalmazás sablonjában. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldás követelményeinek: 

:::image type="content" source="media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png" alt-text="Tárolt elemzési pénztár":::

További információért lásd az [Azure-ban tárolt elemzési alkalmazás létrehozása az Azure IoT Central](./tutorial-in-store-analytics-create-app.md) oktatóanyagban című témakört.

## <a name="smart-inventory-management"></a>Intelligens készletkezelés

A leltár az a kiskereskedelmi tulajdonú árucikkek készlete. A Készletkezelés kritikus fontosságú annak biztosítása érdekében, hogy a megfelelő termék megfelelő helyen legyen a megfelelő időben. A kereskedőnek el kell osztania a túl sok leltár tárolásával járó költségeket azzal a költségekkel, hogy az igények kielégítése érdekében ne legyen elegendő tétel a készletben.

A rádiófrekvenciás azonosító (RFID) címkékből, figyelőkből és kamerákból generált IoT-adatok lehetővé teszik a készletkezelési folyamatok fejlesztését. A IoT érzékelőkből és eszközökből gyűjtött telemetria kombinálhatja más adatforrásokkal, például időjárási és forgalmi információkkal a felhőalapú üzleti intelligencia rendszerekben.

Az intelligens leltár kezelésének előnyei a következők:

* Csökkentheti az elemek kialakulásának kockázatát, és biztosíthatja a kívánt ügyfél-szolgáltatási szintet. 
* Részletes elemzés és betekintés a leltár pontosságára közel valós időben.
* Eszközök, amelyekkel eldöntheti, hogy megfelelő mennyiségű leltárt szeretne-e tárolni az ügyfelek rendeléseinek kielégítése érdekében.

Ez az alkalmazási sablon az eszközök kapcsolatára koncentrál, valamint az RFID-és a Bluetooth-alapú, alacsony energiafogyasztású olvasó eszközök konfigurálására és felügyeletére.

Az alábbi képernyőfelvételen a beépített irányítópult látható az alkalmazás sablonjában. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldás követelményeinek:

:::image type="content" source="media/overview-iot-central-retail/smart-inventory-management-dashboard.png" alt-text="Intelligens leltár-kezelési irányítópult":::

További információ: [üzembe helyezés és útmutató az intelligens leltár kezelési alkalmazás sablonjának](./tutorial-iot-central-smart-inventory-management.md) oktatóanyaga.

## <a name="micro-fulfillment-center"></a>Mikro-teljesítési központ

Az egyre versenyképesebb kiskereskedelmi környezetben a kiskereskedők folyamatosan nyomást gyakorolnak a kereslet és a teljesítés közötti szakadék lezárására. Az egyre növekvő fogyasztói kereslettel foglalkozó új tendencia az, hogy a leltárt a végfelhasználók és a meglátogatott üzletek közelében tárolják.

A IoT Central Micro-beteljesülés Center alkalmazás sablon lehetővé teszi a megoldás-építők számára a teljes mértékben automatizált teljesítési központok minden aspektusának figyelését és kezelését. A sablon szimulált feltételt figyelő érzékelőkkel és robot-szolgáltatókkal is rendelkezik a megoldás fejlesztési folyamatának felgyorsításához. Ezek az érzékelő eszközök olyan értelmes jeleket rögzítenek, amelyek az üzleti eredményekre alakíthatók, így a kiskereskedők csökkenthetik működési költségeiket, és tapasztalatokat hozhatnak létre ügyfeleiknek.

Az alkalmazás sablonja a következőket teszi lehetővé: 

- Zökkenőmentesen csatlakoztathatók különböző típusú IoT-érzékelők, például robotok vagy feltételt figyelő érzékelők egy IoT Central alkalmazás-példányhoz.
- Figyelheti és kezelheti az érzékelő hálózat állapotát, valamint a környezetben található bármely átjáró eszközét.
- A megfelelő riasztások elindításához hozzon létre egyéni szabályokat a környezeti feltételek köré a teljesítési központban.
- Alakítsa át a környezeti feltételeket a teljesítési központban a kiskereskedelmi tárház csapata által kihasználható elemzésekre.
- Exportálja az összesített betekintést meglévő vagy új üzleti alkalmazásokba a kiskereskedelmi személyzet tagjai számára.

Az alábbi képernyőfelvételen a beépített irányítópult látható az alkalmazás sablonjában. Az irányítópult teljes mértékben testreszabható, hogy megfeleljen az adott megoldás követelményeinek:

:::image type="content" source="media/overview-iot-central-retail/MFC-Dashboard.png" alt-text="Micro-beteljesülés központja":::

További információért lásd: [üzembe helyezés és útmutató a Micro-teljesítési központ alkalmazás sablonjának](./tutorial-micro-fulfillment-center.md) oktatóanyaga.

## <a name="video-analytics---object-and-motion-detection"></a>Videóelemzés – objektum- és mozgásérzékelés

A *IoT Central video Analytics – Object és Motion Detection* alkalmazás sablon segítségével gyorsan megtudhatja, hogyan helyezheti üzembe, kezelheti és figyelheti az intelligens peremhálózati kamerákat használó megoldásokat az objektumok és a mozgások észlelésére.

A video Analytics-alkalmazás a IoT Edge futó [Live Video Analytics (LVA)](#live-video-analytics) modult használja. A LVA modul platformot biztosít a peremhálózat és a felhőre kiterjedő intelligens video-alkalmazások létrehozásához. A platform segítségével javíthatja a IoT-megoldásokat, például a video Analytics-alkalmazást az objektumok és a mozgásészlelés segítségével.

Az alkalmazás sablonja négy alkalmazás-irányítópultot tartalmaz:

* **Első lépések** az erőforrásokra mutató hivatkozásokat tartalmaz, amelyek segítenek az alkalmazás-sablon használatának megkezdésében.
* A **bemutató irányítópulton** látható, hogy milyen típusú információkat lehet megjeleníteni a csatlakoztatott kamerákból.
* **(Minta) a Real cames Management** szimulált kamerák használatával mutatja be, hogyan kezelheti a kamerákat az alkalmazásból.
* **(Minta) a valódi kamera-figyelő** szimulált kamerákat használ annak bemutatására, hogyan figyelheti meg a kamerákat az alkalmazásból.

:::image type="content" source="media/overview-iot-central-retail/live-video-analytics.png" alt-text="Videóelemzés – objektum- és mozgásérzékelés":::

A megoldás architektúrájának megismeréséhez tekintse meg a [video Analytics alkalmazás architektúráját](architecture-video-analytics.md)ismertető témakört.

A megoldás üzembe helyezésének megismeréséhez tekintse meg a [video Analytics-alkalmazás létrehozása az Azure-ban IoT Central](tutorial-video-analytics-deploy.md) oktatóanyagot.

### <a name="live-video-analytics"></a>Élő videó-elemzés

Az [élő videó Analytics](https://github.com/Azure/live-video-analytics) platformot biztosít a peremhálózat és a felhőre kiterjedő intelligens video-alkalmazások létrehozásához. A platform lehetővé teszi az élő videók rögzítését, rögzítését, elemzését, valamint az eredmények közzétételét az Azure-szolgáltatások számára videó-vagy videó-elemzéssel. Az Azure-szolgáltatások a felhőben vagy az Edge-ben is futhatnak. A platform segítségével növelheti a IoT-megoldásokat a video Analytics segítségével.

## <a name="next-steps"></a>Következő lépések

A kereskedelmi megoldások létrehozásának megkezdéséhez:

* Ismerkedjen meg az [Azure IoT Central in-store Analytics-alkalmazás létrehozása](./tutorial-in-store-analytics-create-app.md) című oktatóanyaggal, amely végigvezeti a megoldásnak az áruházbeli elemzési alkalmazás-sablonokkal való kiépítésének lépésein.
* [A video Analytics-alkalmazás sablonjának üzembe helyezése és átjárása](./tutorial-video-analytics-deploy.md).
* [Üzembe helyezés és végigvezeti a csatlakoztatott logisztikai alkalmazás sablonján](./tutorial-iot-central-connected-logistics.md).
