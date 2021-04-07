---
title: Trendek és statisztikai jelentések készítése
description: Ismerje meg a hálózati tevékenységet, a statisztikát és a trendeket a Defender használatával a IoT trendek és a statisztikai widgetek számára.
ms.date: 2/21/2021
ms.topic: how-to
ms.openlocfilehash: b4539e20ff485f1b6be69fee8e6849298540adcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779001"
---
# <a name="sensor-trends-and-statistics-reports"></a>Érzékelő trendek és statisztikai jelentések

Létrehozhat widget-diagramokat és kördiagramokat, és betekintést nyerhet a hálózat trendjeibe és statisztikába. A widgetek a felhasználó által definiált irányítópultok alatt csoportosíthatók.

> [!NOTE]
> A rendszergazdák és a biztonsági elemzők trendeket és statisztikai jelentéseket hozhatnak létre.

Az irányítópult olyan widgeteket tartalmaz, amelyek grafikusan leírják a következő típusú információkat:

- Forgalom port alapján
- Legfelső szintű forgalom port alapján
- Csatorna sávszélessége
- Teljes sávszélesség
- Aktív TCP-kapcsolatok
- Legfelső szintű sávszélesség VLAN-nal
- Eszközök
  - Új eszközök
  - Foglalt eszközök
  - Eszközök szállító szerint
  - Eszközök operációs rendszer szerint
  - Eszközök száma VLAN-onként
  - Leválasztott eszközök
- A kapcsolat órákig csökken
- Incidensek típusú riasztások típus szerint
- Adatbázistábla-hozzáférés
- Protokollok metszetének widgetje
- DELTAV
  - DeltaV Roc-műveletek eloszlása
  - DeltaV Roc-események név szerint
  - DeltaV események idő szerint
- AMS
  - AMS-forgalom kiszolgáló portja szerint
  - AMS-forgalom parancs alapján
- Ethernet és IP-cím:
  - Ethernet-és IP-címek forgalma a CIP szolgáltatással
  - Ethernet és IP-címek forgalma a CIP-osztály szerint
  - Az Ethernet és az IP-cím forgalmának parancs alapján
- OPC
  - OPC felső szintű felügyeleti műveletek
  - OPC felső I/O-műveletek
- Siemens S7:
  - S7-es forgalom vezérlési funkcióval
  - S7-es forgalom alfüggvény alapján
- VLAN
  - Eszközök száma VLAN-onként
  - Legfelső szintű sávszélesség VLAN-nal
- 60870-5-104
  - IEC-60870 forgalom ASDU szerint
- BACNET
  - BACnet szolgáltatások
- DNP3
  - DNP3-forgalom függvény szerint
- SRTP
  - SRTP-forgalom
  - SRTP hibák naponta
- SuiteLink:
  - SuiteLink Top lekérdezett Címkék
  - SuiteLink numerikus címke viselkedése
- IEC-60870 forgalom ASDU szerint
- DNP3-forgalom függvény szerint
- MMS-forgalom szolgáltatás szerint
- Modbus-forgalom függvény szerint
- OPC – UA forgalom szolgáltatás szerint

> [!NOTE]
>  A widgetek ideje az érzékelő ideje szerint van beállítva.

## <a name="create-reports"></a>Jelentések létrehozása

Az irányítópultok és a widgetek megtekintéséhez:

Az oldalsó menüben válassza a **trendek & statisztikát** .

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Képernyőkép egy vizsgálatról.":::

Alapértelmezés szerint az eredmények az elmúlt hét napban észlelhetők. A szűrési eszközök használatával módosíthatja ezt a tartományt. Például egy ingyenes szöveges keresés.

## <a name="create-a-dashboard"></a>Irányítópult létrehozása

Hozzon létre egy új irányítópultot az **irányítópult** legördülő menü kiválasztásával. Több widgetet is létrehozhat és hozzáadhat egy irányítópulthoz.

A következő beállításokkal hozhat létre testreszabott irányítópultokat:

- Widget hozzáadása az irányítópulthoz

- Widget törlése az irányítópultról

- Widget-szűrő módosítása

- Widget átméretezése

- Widget helyének módosítása

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/pin-a-dashboard.png" alt-text="Egy widget helyének módosítása":::

Testreszabott irányítópult létrehozása:

1. Válassza a **trendek és statisztika** lehetőséget a bal oldali panelen.

1. Jelölje be az **irányítópult kiválasztása** legördülő menüt, majd kattintson az **irányítópult létrehozása** gombra.

1. Írjon be egy értelmes nevet az új irányítópulthoz, és válassza a **Létrehozás** lehetőséget.

1. Kattintson a lap bal felső részén található **widget hozzáadása** lehetőségre.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Válassza ki a widget-áruházból a widgetet.":::

1. A **Biztonság** és az **operatív** widgetek az ablak jobb felső részén érhetők el. Különböző kategóriák és protokollok közül választhat. A miniatűr grafikával rendelkező rövid leírás minden widgettel együtt jelenik meg. Az összes elérhető widget megjelenítéséhez használja a görgetősávot.

1. Válasszon ki egy widgetet a **Hozzáadás gombra kattintva** . A widget azonnal megjelenik az irányítópulton.

Irányítópult törlése:

1. Válassza ki az irányítópult nevét a legördülő menüből.

1. Válassza a **Törlés** ikont, majd kattintson **az OK gombra**.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/garbage-icon.png" alt-text="Az irányítópult törléséhez kattintson a Törlés ikonra.":::

Irányítópult nevének szerkesztése:

1. Válassza ki az irányítópult nevét a legördülő menüből.

1. Kattintson a **Szerkesztés** ikonra.
  
  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/edit-name.png" alt-text="Az irányítópult nevének szerkesztéséhez kattintson a Szerkesztés ikonra.":::

1. Adja meg az irányítópult új nevét, majd kattintson a **Mentés** gombra.
 
Az alapértelmezett irányítópult beállítása:

1. Válassza ki az irányítópult nevét a legördülő menüből.

1. Válassza ki a **csillag** ikont, és válassza ki az alapértelmezett irányítópultként beállítani kívánt irányítópultot.

   :::image type="content" source="media/how-to-create-trends-and-statistics-reports/default-dashboard.png" alt-text="Válassza ki a csillag ikont az alapértelmezett irányítópult kiválasztásához."::: 

Egy widget szűrési beállításainak módosítása:

1. Kattintson a **szűrő** ikonra.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/filter-widget.png" alt-text="Válassza a szűrő ikont a widget paramétereinek megadásához.":::

1. Szerkessze a szükséges paramétereket.

1. Válassza az **OK** lehetőséget.

Widget törlése:

- Válassza ki az :::image type="icon" source="media/how-to-create-trends-and-statistics-reports/x-icon.png" border="false"::: ikont.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/delete-widget.png" alt-text="Válassza ki az X-et a widget törléséhez.":::

## <a name="creating-widgets"></a>Widgetek létrehozása 

A widget Store lehetővé teszi, hogy kategória és protokoll szerint válassza ki a widgeteket. Az elérhető **biztonsági** vagy **működési** widgeteket a kiválasztásával jelenítheti meg.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/widget-store.png" alt-text="Válassza ki a widget-áruházból a widgetet.":::

Mindegyik widget konkrét információt tartalmaz a rendszerforgalomról, a hálózati statisztikákról, a protokollok statisztikáról, az eszközről és a riasztási információról. Üzenet jelenik meg, ha a widgethez nem áll rendelkezésre adatelem.

A tortadiagram egy szakaszát eltávolíthatja egy tortadiagramon, hogy a hátralévő szeletek relatív jelentőségét világosabban lássuk. Ehhez a képernyő alján található jelmagyarázatban válassza ki a szelet nevét.

A következő részekben példákat talál az egyes widgetek használati eseteire.

### <a name="busy-devices-widget"></a>Foglalt eszközök widget

Ez a widget felsorolja az első öt legforgalmasabb eszközt. **Szerkesztési** módban szűrheti az ismert protokollok alapján.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/busy-device.png" alt-text="A foglalt eszköz widget nézete.":::

### <a name="total-bandwidth-widget"></a>Teljes sávszélesség widget

Ez az eszköz az MB/s (megabit/másodperc) sávszélességet követi nyomon. A sávszélesség az y tengelyen, az x tengelyen megjelenő dátummal van megadva. A **szerkesztési** mód lehetővé teszi az eredmények ügyfél, kiszolgáló, kiszolgáló port vagy alhálózat alapján történő szűrését. Egy elemleírás jelenik meg, amikor a diagram fölé viszi a kurzort.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/total-bandwidth.png" alt-text="A teljes sávszélesség widget nézete":::

### <a name="channels-bandwidth-widget"></a>Csatornák sávszélességének widgetje

Ez a widget az első öt forgalmi csatornát jeleníti meg. A szűrést elvégezheti a címek alapján, és beállíthatja a megjelenített eredmények számát. Kattintson a lefelé mutató nyílra a további csatornák megjelenítéséhez.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/channels-bandwidth.png" alt-text="A channels sávszélesség widget nézete":::

### <a name="traffic-by-port-widget"></a>Forgalom porton keresztüli widget

Ez a widget megjeleníti a forgalmat a port alapján, amelyet egy tortadiagram jelöl, és minden olyan portot, amelyet más színnel jelöltek ki. Az egyes portok forgalmának mennyisége arányos a torta egy részének méretével.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/traffic-by-port.png" alt-text="A portos widget által használt forgalom nézete.":::

### <a name="new-devices-widget"></a>Új eszközök widget

Ez a widget az új eszközök sáv diagramot jeleníti meg, amely azt jelzi, hogy egy adott dátum hány új eszközt észlelt.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/new-devices.png" alt-text="Az új eszközök widget nézete.":::

### <a name="protocol-dissection-widgets"></a>Protokollok metszetének widgetje

Ez a widget egy tortadiagramot jelenít meg, amely lehetővé teszi a protokollon keresztüli forgalom, a függvények kódjai és a szolgáltatások által elválasztott adatok áttekintését. A torta minden szeletének mérete arányos a többi szelethez viszonyított forgalom mennyiségével.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/protocol-dissection.png" alt-text="A protokoll metszeti widgetének nézete.":::

### <a name="active-tcp-connections-widget"></a>Aktív TCP-kapcsolatok widget

Ez a widget egy diagramot jelenít meg, amely megjeleníti a rendszer aktív TCP-kapcsolatainak számát.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/active-tcp.png" alt-text="Az aktív TCP-kapcsolatok widget nézete.":::

### <a name="incident-by-type-widget"></a>Incidens típus szerint widget

Ez a widget egy tortadiagramot jelenít meg, amely az incidensek típus szerinti számát jeleníti meg. Az egyes motorok által az előre meghatározott időszakban generált riasztások száma.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/incident-by-type.png" alt-text="Az incidens típusa widget típusú nézet.":::

## <a name="devices-by-vendor-widget"></a>Eszközök szállítói widget alapján

Ez a widget egy tortadiagramot jelenít meg, amely az eszközök gyártó által megadott számát jeleníti meg. Egy adott szállító eszközeinek száma a lemez szállítói részének a többi eszköz gyártójához viszonyított méretével arányos.

## <a name="number-of-devices-per-vlan-widget"></a>Eszközök száma VLAN-widgetként

Ez a widget egy tortadiagramot jelenít meg, amely a felderített eszközök számát mutatja VLAN-onként. A torta minden szeletének mérete arányos a felderített eszközök számával a többi szelethez képest.

Minden VLAN megjelenik az érzékelő vagy a manuálisan hozzáadott név által hozzárendelt VLAN-címkével.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/number-of-devices.png" alt-text="A Devices widget számának nézete.":::

### <a name="top-bandwidth-by-vlan-widget"></a>Legfelső szintű sávszélesség VLAN-widgettel

Ez a widget megjeleníti a VLAN sávszélesség-felhasználását. Alapértelmezés szerint a widget öt VLAN-t mutat be a legnagyobb sávszélesség-használattal.

Az adathalmazt a widgetben bemutatott időszak alapján szűrheti. Kattintson a lefelé mutató nyílra a további találatok megjelenítéséhez.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/top-bandwidth.png" alt-text="A legfelső szintű sávszélesség a VLAN Widget használatával.":::

## <a name="system-report"></a>Rendszerjelentés

A rendszerjelentés letöltése: 

1. Az oldalsó menüben válassza a **trendek & statisztikát** .

1. Válassza a **Rendszerjelentés** lehetőséget a jobb felső sarokban. A jelentés automatikusan le lesz letöltve.

  :::image type="content" source="media/how-to-create-trends-and-statistics-reports/system-report.png" alt-text="Válassza a rendszerjelentés gombot a rendszerjelentés másolatának letöltéséhez.":::

A rendszerjelentés egy PDF-fájl, amely a rendszer összes információját tartalmazza:

  - Eszközök

  - Riasztások

  - Hálózati házirend adatai

## <a name="devices-in-a-system-report"></a>Rendszerjelentés eszközei 

A rendszerjelentés az összes eszköz listáját és azok információit jeleníti meg. Például a használt típus, név és protokollok. A rendszerjelentés az eszközök szállítónként való listáját is megjeleníti.

## <a name="alerts-in-system-report"></a>Riasztások a rendszerjelentésben 

A rendszerjelentés megjeleníti az összes olyan riasztás listáját, amely tartalmazza az adatokat, például a dátumot és a súlyosságot.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/alerts-report.png" alt-text="A rendszerjelentéseken lévő riasztások nézete.":::

## <a name="network-information-in-system-report"></a>Hálózati információk a rendszerjelentésben 

A rendszerjelentés részletesen megjeleníti a hálózat alapkonfigurációját. Például a DNP3 és a portok megnyitása kapcsolatonként.

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/dnp3.png" alt-text="A DNP3 függvény nézete a rendszerjelentésből.":::

:::image type="content" source="media/how-to-create-trends-and-statistics-reports/open-port.png" alt-text="A nyitott porton keresztüli jelentés nézete.":::

## <a name="see-also"></a>Lásd még

[Kockázatértékelési jelentéskészítés](how-to-create-risk-assessment-reports.md) 
 [Érzékelő adatbányászati lekérdezések](how-to-create-data-mining-queries.md) 
 [Támadási vektorok jelentése](how-to-create-attack-vector-reports.md)
