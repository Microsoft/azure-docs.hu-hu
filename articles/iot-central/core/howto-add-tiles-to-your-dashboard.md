---
title: Konfigurálás a Azure IoT Central irányítópultra | Microsoft Docs
description: Szerkesztőként megtudhatja, hogyan konfigurálhatja az alkalmazás irányítópultjának Azure IoT Central alapértelmezett csempéket.
author: philmea
ms.author: philmea
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 8a8ba765a966409c06dbba636932f7777624f6d4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864252"
---
# <a name="configure-the-application-dashboard"></a>Az alkalmazás irányítópultjának konfigurálása

Az **Irányítópult** az első oldal, amely akkor látható, amikor csatlakozik egy IoT Central alkalmazáshoz. Ha az alkalmazást az egyik iparág-központú alkalmazássablonból hozza [létre,](./concepts-app-templates.md)az alkalmazásnak van egy előre definiált irányítópultja. Ha egyéni alkalmazássablonból hozza létre az [alkalmazást,](./concepts-app-templates.md)az irányítópult néhány tippet tartalmaz az első lépésekhez.

> [!TIP]
> A felhasználók [az alapértelmezett alkalmazás-irányítópult](howto-create-personal-dashboards.md) mellett több irányítópultot is létrehozhatnak. Ezek az irányítópultok csak a felhasználó személyes adatai, vagy megoszthatók az alkalmazás összes felhasználója között.  

## <a name="add-tiles"></a>Csempék hozzáadása

Az alábbi képernyőképen az egyéni alkalmazássablonból létrehozott alkalmazás **irányítópultja** látható. Az aktuális irányítópult testreszabásához válassza a **Szerkesztés** lehetőséget egyéni személyes vagy megosztott irányítópult hozzáadásához, majd válassza az Új **lehetőséget:**

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Irányítópult az egyéni alkalmazássablonon alapuló alkalmazásokhoz":::

A Szerkesztés **vagy** az Új lehetőséget **választva** az irányítópult *szerkesztési módban* van. Az Irányítópult szerkesztése panel  eszközeivel csempéket adhat hozzá az irányítópulthoz, és testreszabhatja és eltávolíthatja a csempéket az irányítópulton. Ha például egy **Telemetria csempét** szeretne hozzáadni, amely egy vagy több eszköz által jelentett aktuális hőmérsékletet mutatja:

1. Válasszon ki **egy eszközcsoportot,**  majd válassza ki az eszközöket a csempén az Eszközök legördülő menüből. Most már láthatja az eszközökről elérhető telemetriai adatokat, tulajdonságokat és parancsokat.

1. Szükség esetén a legördülő menüben kiválaszthatja a csempén megjelenni kívánt telemetriaértéket. A csempéhez további elemeket is hozzáadhat, ha kiválasztja a **+ Telemetria,** **+ Tulajdonság**, vagy **+ Felhőtulajdonság elemet.**

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Hőmérsékleti telemetriacsempe hozzáadása az irányítópulthoz":::

Miután kiválasztotta a csempén megmutatja az összes értéket, kattintson a **Csempe hozzáadása lehetőségre.** A csempe most már megjelenik az irányítópulton, ahol módosíthatja, átméretezheti, áthelyezheti és konfigurálhatja.

Miután befejezte a csempék hozzáadását és testreszabását az irányítópulton, a Mentés lehetőség kiválasztásával mentheti a módosításokat az irányítópultra, így kiveheti a szerkesztési módot. 

## <a name="customize-tiles"></a>Csempék testreszabása

A csempék szerkesztéséhez szerkesztési módban kell lennie.  Az elérhető testreszabási lehetőségek a csempe [típusától függnek:](#tile-types)

* A csempe vonalzó ikonjával módosíthatja a vizualizációt. A vizualizációk közé tartoznak a vonaldiagramok, a sávdiagramok, a tortadiagramok, az utolsó ismert értékek, a fő teljesítménymutatók (vagy KPI-k), az hőtérképek és a térképek.

* A négyzet ikonnal átméretezheti a csempét.

* A fogaskerék ikonnal konfigurálhatja a vizualizációt. Vonaldiagram-vizualizációkhoz például kiválaszthatja, hogy a jelmagyarázatot és a tengelyeket is megmutatja, és kiválaszthatja a ábrázolnini való időtartományt.


## <a name="tile-types"></a>Csempetípusok

Az alábbi táblázat az irányítópultokhoz hozzáadhatja a csempék különböző típusait ismerteti:

| Csempe             | Description |
| ---------------- | ----------- |
| Markdown         | A Markdown-csempék olyan kattintható csempék, amelyek markdown használatával formázott címsort és leírást jelenítnek meg. Az URL-cím lehet az alkalmazás egy másik oldalára mutató relatív hivatkozás, vagy egy külső webhelyre mutató abszolút hivatkozás.|
| Kép            | A képcsempék egy egyéni képet jelenít meg, és kattinthatóak. Az URL-cím lehet az alkalmazás egy másik oldalára mutató relatív hivatkozás, vagy egy külső webhelyre mutató abszolút hivatkozás.|
| Címke            | A címkecsempék egyéni szöveget jelenítnek meg az irányítópulton. Kiválaszthatja a szöveg méretét. Címkecsempék használatával releváns információkat adhat az irányítópulthoz, például leírásokat, kapcsolattartási adatokat vagy súgót.|
| Darabszám            | A számlálási csempék az eszközcsoportban található eszközök számát jelenítik meg.|
| Térkép              | A térképcsempék egy vagy több eszköz helyét jelenítik meg a térképen. Egy eszköz helyelőzményének legfeljebb 100 pontja is megjeleníthető. Megjeleníthet például egy mintaútvonalat, ahol az eszköz volt az elmúlt héten.|
| KPI              |  A KPI-csempék egy vagy több eszköz összesített telemetriai értékeit jelenítik meg egy adott időszakban. A használatával például egy vagy több eszköz elmúlt órában elért maximális hőmérsékletét és nyomását mutathatja.|
| Vonaldiagram       | A vonaldiagram csempéi egy vagy több összesített telemetriaértéket ábrázolnak egy vagy több eszköz számára egy adott időszakra vonatkozóan. Például megjeleníthet egy vonaldiagramot, amely egy vagy több eszköz átlagos hőmérsékletét és nyomását ábrázolja az elmúlt órában.|
| Sávdiagram        | A sávdiagram csempéi egy vagy több összesített telemetriaértéket ábrázolnak egy vagy több eszköz számára egy adott időszakra vonatkozóan. Megjeleníthet például egy sávdiagramot, amely egy vagy több eszköz átlagos hőmérsékletét és nyomását mutatja az elmúlt órában.|
| Tortadiagram        | A tortadiagram csempéi egy vagy több összesített telemetriaértéket jelenítnek meg egy vagy több eszközre vonatkozóan egy adott időszakra vonatkozóan.|
| Intenzitástérkép         | Az hőtérkép-csempék egy vagy több eszköz adatait jelenítik meg színekként ábrázolva.|
| Utolsó ismert érték | Az utolsó ismert értékcsempék egy vagy több eszköz legfrissebb telemetriai értékeit jelenítik meg. Ezzel a csempével például egy vagy több eszköz legfrissebb hőmérsékleti, nyomási és páratartalom-értékei jeleníthetőek meg. |
| Eseményelőzmények    | Az Eseményelőzmények csempék egy eszköz adott időszakra vonatkozó eseményeit jelenítik meg. A használatával például egy vagy több eszköz összes nyitott és lezáró eseménye használhatja az elmúlt egy órában.|
| Tulajdonság         |  A tulajdonságcsempék egy vagy több eszköz tulajdonságainak és felhőtulajdonságának aktuális értékét jelenítik meg. Ezzel a csempével például olyan eszköztulajdonságokat jeleníthet meg, mint az eszköz gyártója vagy belső vezérlőprogramjának verziója. |

Jelenleg legfeljebb 10 eszközt adhat hozzá több eszközt támogató csempékhez.

### <a name="customizing-visualizations"></a>Vizualizációk testreszabása

A vonaldiagramok alapértelmezés szerint egy időtartomány adatait mutatják. A kiválasztott időtartomány 50 egyenlő méretű gyűjtőre van osztva, az eszközadatok pedig gyűjtőnként összesítve 50 adatpontot adnak a kiválasztott időtartományon. Ha nyers adatokat szeretne megtekinteni, módosíthatja a kijelölést az utolsó 100 érték megtekintéséhez. Az időtartományt úgy módosíthatja, hogy nyers adatvizualizációt választ ki, használja a Megjelenítési tartomány legördülő menüt a **Diagram konfigurálása panelen.**

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/display-range.png" alt-text="Vonaldiagram megjelenítési tartományának módosítása":::

Az összesített értékeket megjelenítő csempék esetén válassza a telemetria típusa melletti fogaskerék ikont a Diagram konfigurálása **panelen** az aggregáció kiválasztásához. Az átlag, az összeg, a maximum, a minimum és a darabszám közül választhat.

Vonaldiagramok, sávdiagramok és tortadiagramok esetén testre szabhatja a különböző telemetriai értékek színét. Válassza a testreszabni kívánt telemetria melletti paletta ikont:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Telemetriaérték színének módosítása":::

A sztringtulajdonságokat vagy telemetriai értékeket megjelenítő csempék esetén kiválaszthatja, hogyan jelenítse meg a szöveget. Ha például az eszköz egy URL-címet tárol egy sztringtulajdonságban, akkor kattintható hivatkozásként is megjeleníthető. Ha az URL-cím egy képre hivatkozik, a képet egy utolsó ismert érték- vagy tulajdonságcsempében renderelheti. A sztringek megjelenítése a csempe konfigurációjában a telemetria típusa vagy tulajdonsága melletti fogaskerék ikonra kattintva változtatva:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Sztring csempén való megjelenítésének módosítása":::

Numerikus **KPI,** **Utolsó** ismert  érték és Tulajdonság csempék esetén feltételes formázással szabhatja testre a csempe színét az aktuális értéke alapján. Feltételes formázás hozzáadásához válassza a **konfigurálás** lehetőséget  a csempén, majd válassza a testre szabni kívánt érték melletti Feltételes formázás ikont:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="Képernyőkép egy csempe konfigurálás lehetőségének, majd a feltételes formázás ikonjának megkeresésről":::

Adja hozzá a feltételes formázási szabályokat:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="Képernyőkép az átlagos folyamat feltételes formázási szabályairól. Három szabály van : a 20-asnál kevesebb zöld, az 50-esnél kevesebb sárga, az 50-esnél nagyobb pedig piros":::
   
Az alábbi képernyőképen a feltételes formázási szabály hatása látható:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="Képernyőkép az Átlagos vízáramlás csempén látható piros háttérszínről. A csempén 50,54 a szám":::

### <a name="tile-formatting"></a>"csempe" formázása
Ez a KPI-, LKV- és Tulajdonságcsempékben elérhető funkció lehetővé teszi a felhasználók számára a betűméret beállítását, a tizedes pontosság beállítását, a numerikus értékek rövidítését (például 1700 1,7 ezer formátum) vagy a sztringértékek burkoltatását a csempéken.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/tile-format.png" alt-text="Csempeformátum":::

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan konfigurálhatja Azure IoT Central alapértelmezett alkalmazás-irányítópultját, megtudhatja, hogyan hozhat létre [személyes irányítópultot.](howto-create-personal-dashboards.md)
