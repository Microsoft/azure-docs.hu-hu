---
title: Az érzékelőeszköz-térkép használata
description: Az eszköz Térkép az észlelt hálózati eszközök grafikus megjelenítését teszi lehetővé. A Térkép használatával elemezheti és kezelheti az eszköz adatait, a hálózati szeleteket és jelentéseket készíthet.
ms.date: 1/7/2021
ms.topic: how-to
ms.openlocfilehash: f7579cbca618baef404236556993c9831dd84bdf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784594"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>Az érzékelő észlelésének vizsgálata az eszköz térképén

Az eszköz Térkép az észlelt hálózati eszközök grafikus megjelenítését teszi lehetővé. A térképen a következőt használhatja:

  - Az eszköz adatainak beolvasása, elemzése és kezelése.

  - Hálózati szeletek elemzése, például az érdekes csoportok vagy a Purdue-rétegek.

  - Jelentések készítése, például az eszköz adatainak és összegzésének exportálása.

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Képernyőfelvétel az eszköz térképéről.":::

A Térkép elérése:

  - Válassza ki az **eszköz leképezése** lehetőséget a konzol főképernyőjén.

## <a name="map-search-and-layout-tools"></a>Keresési és elrendezési eszközök leképezése

A térképen a következő eszközök használhatók.

A felhasználói szerepkör határozza meg, hogy mely eszközök érhetők el az eszköz Térkép ablakban. A felhasználói szerepkörökkel kapcsolatos részletekért tekintse meg a [felhasználók létrehozása és kezelése](how-to-create-and-manage-users.md) című témakört.

| Szimbólum | Leírás |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| Keresés IP-cím vagy MAC-cím alapján egy adott eszközhöz. Adja meg az IP-címet vagy a MAC-címet a szövegmezőben. A Térkép megjeleníti azt az eszközt, amelyet az eszközhöz csatlakoztatott eszközökkel keres. |
| Csoport kiemelése és szűrők <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="Képernyőkép a csoport kiemeléséről és szűrőkről."::: | Az alapértelmezett és az egyéni eszközcsoport alapján szűrheti vagy kiemelheti a leképezést. |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | Összecsukja a nézetet, hogy lehetővé tegye a célzott nézetet az OT-eszközökön, és csoportosítsa az informatikai eszközöket.  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | Az eszköz aktuális elrendezésének karbantartása a térképen. Ha például az eszközöket a Térkép új helyeire húzza, akkor az eszközök ezen a helyen maradnak, amikor kilép a térképről. |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | Szélességhez igazítás |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | – Az eszközhöz azonosított Purdue-réteg megtekintése, beleértve az automatikus, a folyamat-vezérlést, a felügyeletet és a vállalatot <br /> – Az eszközök közötti kapcsolatok megtekintése.|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | Megjelenítés vagy elrejtés a szórás és a csoportos küldés között. |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | Az eszközök a térképen való szűrése a többi eszközzel folytatott legutóbbi kommunikáció időpontja alapján. |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="értesítések" border="false"::: | Megtekintheti az eszközre vonatkozó értesítéseket. Ha például egy meglévő MAC-cím használatával új IP-címet észlelt egy eszközhöz |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="Exportálás" border="false"::: | Eszköz adatainak exportálása/importálása. |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="Tulajdonságok" border="false"::: | A kiválasztott eszközök alapszintű eszközének tulajdonságainak megtekintése. |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="Nagyítás" border="false"::: vagy :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="kicsinyítés" border="false"::: | Az eszközök nagyítása vagy kicsinyítése a térképen. |

## <a name="view-ot-elements-only"></a>Csak az elemek megtekintése

Alapértelmezés szerint az alhálózat automatikusan összesíti az IT-eszközöket, így a Térkép nézet az OT és az INTERNETKAPCSOLATot használó hálózatokra koncentrál. Az IT hálózati elemek bemutatása a minimumra van összecsukva, ami csökkenti a térképen megjelenített eszközök teljes számát, és tiszta képet nyújt az OT és az INTERNETKAPCSOLATtal rendelkező hálózati elemekről.

Az egyes alhálózatok egyetlen entitásként jelennek meg az eszköz térképén, beleértve az interaktív összecsukást és bővítést is, hogy megnézzék az IT-alhálózatok és a háttér részleteit.

Az alábbi ábra egy összecsukott IT-alhálózatot mutat be 27 informatikai hálózati elemmel.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="a 27 IT-hálózati elemmel összecsukva IT-alhálózat":::

Az informatikai hálózatok összeomlási képességének engedélyezése:

- A **Rendszerbeállítások** ablakban ellenőrizze, hogy engedélyezve van-e az informatikai hálózatok bekapcsolása csoportosítási funkció.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="Rendszerbeállítási ablak":::

Az IT-alhálózat kibontása:

1. Az IT és az OT hálózatok közötti különbségtételhez a rendszerbeállítások képernyőn válassza az **alhálózatok** lehetőséget.

   > [!NOTE]
   > Javasoljuk, hogy minden olyan alhálózatot, amely a felhasználó által értelmezhető neveket használ, könnyen azonosítható legyen az IT és az OT hálózatok közötti különbségtétel érdekében.

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="Alhálózatok konfigurálása":::

2. Az **alhálózatok konfigurálása konfigurációs** ablakban törölje az összes olyan alhálózat **IC-alhálózata** jelölőnégyzetét, amelyet it-alhálózatként kíván megadni. Az IT-alhálózatok az eszközök térképén összecsukva jelennek meg az INTERNETKAPCSOLATtal rendelkező eszközök, például egy vezérlő vagy PLC az informatikai hálózatokban.

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="Alhálózatok konfigurációjának szerkesztése":::

3. Az IT-hálózat a térképen való kibontásához az eszközök ablakban kattintson rá a jobb gombbal, és válassza a **hálózat kibontása** lehetőséget.

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="Bontsa ki a hálózat nézetét.":::

4. Megjelenik egy megerősítési mező, amely értesíti, hogy az elrendezés módosítása nem állítható vissza.

5. Válassza az **OK** lehetőséget. Az IT-alhálózati elemek megjelennek a térképen.

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="OK":::

Az IT-alhálózat összecsukása:

1.  A bal oldali panelen válassza az **eszközök** elemet.

2. Az eszközök ablakban válassza az összecsukás ikont. A piros érték azt jelzi, hogy a térképen hány kibontott alhálózat látható.

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="Eszköz ablak":::

3. Válassza ki azokat az alhálózatokat, amelyeket össze kívánja csukni, vagy válassza az **összes összecsukása** elemet. A kiválasztott alhálózat összecsukva jelenik meg a térképen.

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="Az összes összecsukása":::

Az összecsukás ikon a bővített IT-alhálózatok frissített számával frissül.

## <a name="view-or-highlight-device-groups"></a>Erőforráscsoportok megtekintése vagy kiemelése

A Térkép megjelenítését eszközcsoport alapján is testreszabhatja. Például egy adott OT-protokollhoz, VLAN-hoz vagy alhálózathoz társított eszközök csoportjai. Az előre definiált csoportok elérhetők és egyéni csoportok hozhatók létre.

Csoportok megtekintése:

  - **Kiemelés:** Jelölje ki a kék színnel megadott csoporthoz tartozó eszközöket.

  - **Szűrés:** Csak egy adott csoporthoz tartozó eszközök megjelenítése.

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="A port szabványos nézete":::

A következő előre meghatározott csoportok érhetők el:

| Csoport neve | Leírás |
|--|--|
| **Ismert alkalmazások** | Fenntartott portokat (például TCP) használó eszközök.  |
| **nem szabványos portok (alapértelmezett)** | Nem szabványos portokat vagy portokat használó eszközök, amelyek nem rendelkeznek aliassal. |
| **OT protokollok (alapértelmezett)** | Az ismert OT-forgalmat kezelő eszközök. |
| **Engedélyezés (alapértelmezett)** | Azok az eszközök, amelyeket a rendszer a tanulási folyamat során észlelt a hálózaton, vagy amelyek hivatalosan engedélyezve voltak a hálózaton. |
| **Eszközök leltározási szűrői** | Az eszközök az eszköz leltározási táblázatában mentett szűrők szerint vannak csoportosítva. |
| **Lekérdezési időközök** | Az eszközök lekérdezési időközök szerint csoportosítva. A lekérdezési időközök automatikusan jönnek létre ciklikus csatornák vagy időszakok alapján. Például 15,0 másodperc, 3,0 másodperc, 1,5 másodperc vagy bármilyen intervallum. Az információk áttekintésével megtudhatja, hogy a rendszerek túl gyorsan vagy lassan kérdeznek-e le. |
| **Programozási** | Mérnöki állomások és programozási gépek. |
| **Alhálózatok** | Egy adott alhálózathoz tartozó eszközök. |
| **VLAN** | Egy adott VLAN-AZONOSÍTÓhoz társított eszközök. |
| **Több alhálózati kapcsolat** | Az egyik alhálózatról egy másik alhálózatra kommunikáló eszközök. |
| **Rögzített riasztások** | Azok az eszközök, amelyekhez a felhasználó riasztást rögzített. |
| **Támadási vektor szimulálása** | Sebezhető eszközök észlelhetők a támadási vektor jelentéseiben. Ha szeretné megtekinteni ezeket az eszközöket a térképen, jelölje be az **eszköz megjelenítése** jelölőnégyzetet a támadási vektor létrehozásakor. :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="Támadási vektor szimulációk hozzáadása":::. |
| **Legutóbbi észlelés** | Az eszközök az utolsó látott időkeret szerint csoportosítva, például: egy óra, hat óra, egy nap, hét nap. |
| **Nem Active Directory** | Minden olyan nem PLC-eszköz, amely nem kommunikál a Active Directory. |

Eszközök kiemelése vagy szűrése:

1. Válassza ki az **eszköz térképét** az oldalsó menüben.

2. Kattintson a szűrő ikonra. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Menü":::

3. A csoportok ablaktáblán válassza ki azt a csoportot, amelynek ki szeretné emelni vagy szűrni kívánja az eszközöket.

4. Válassza ki a **kiemelés** vagy a **szűrés** elemet. A kiemelés vagy a szűrő eltávolításához kapcsolja ki ugyanazt a kijelölést.

## <a name="define-custom-groups"></a>Egyéni csoportok definiálása

Az előre definiált csoportok megtekintése mellett egyéni csoportokat is meghatározhat. A csoportok megjelennek az eszközök térképén, az eszközök leltárában és az adatbányászati jelentésekben.

> [!NOTE]
> Létrehozhat csoportokat is az eszközök leltárában.

Csoport létrehozása:

1. Válassza az **eszközök** lehetőséget az oldalsó menüben. Megjelenik az eszköz térképe.

1. A csoportok beállításainak megjelenítéséhez válassza a :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="csoport beállítása"::: lehetőséget.

1. Új egyéni csoport létrehozásához válassza a :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="csoportok"::: lehetőséget.

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="Egyéni csoport létrehozása képernyő":::

1. Adja hozzá a csoport nevét, és használjon akár 30 karaktert.

1. Válassza ki a megfelelő eszközöket az alábbiak szerint:

   - Adja hozzá az eszközöket ehhez a menühöz, ha kiválasztja őket a listából (válassza a nyíl gombot),<br /> Vagy <br /> 
   - Adja hozzá az eszközöket ebből a menüből egy kiválasztott csoportból történő másolással (válassza a nyíl gombra)

1. Válassza a **Csoport hozzáadása** lehetőséget meglévő csoportok egyéni csoportokba való felvételéhez.

### <a name="add-devices-to-a-custom-group"></a>Eszközök hozzáadása egyéni csoporthoz

Hozzáadhat eszközöket egyéni csoporthoz, vagy létrehozhat egy új egyéni csoportot és az eszközt is.

1. Kattintson a jobb gombbal egy eszközre a térképen.

1. Válassza **a Hozzáadás a csoporthoz** lehetőséget.

1. Adja meg a csoport nevét a Group (csoport) mezőben, és válassza a + lehetőséget. Megjelenik az új csoport. Ha a csoport már létezik, a rendszer hozzáadja a meglévő egyéni csoporthoz.

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="Csoport neve":::

1. Adjon hozzá eszközöket egy csoporthoz az 1-3-es lépések megismétlésével.

## <a name="map-zoom-views"></a>Térkép nagyítási nézetei

A térképes nézetek használata segít a kriminalisztika meggyorsításában a nagyméretű hálózatok elemzésekor.

Három eszköz részletes nézete is megjeleníthető:

  - [Madár-szem nézet](#birds-eye-view)

  - [Eszköz típusa és a kapcsolatok nézete](#device-type-and-connection-view)

  - [Részletes nézet](#detailed-view)

### <a name="birds-eye-view"></a>Madár-szem nézet

Ez a nézet a következőképpen jeleníti meg az eszközök áttekintését:

  - A piros pontok jelzik a riasztás (ok) t használó eszközöket

  - Csillaggal jelölt pontok a fontosként megjelölt eszközöket jelölik.

  - A fekete pontok a riasztás nélküli eszközöket jelölik.

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="Bird Eye nézet":::

### <a name="device-type-and-connection-view"></a>Eszköz típusa és a kapcsolatok nézete 

Ez a nézet megjeleníti a térképen ikonként jelölt eszközöket, hogy kiemelje az eszközöket a riasztásokkal, az eszközök típusaival és a csatlakoztatott eszközökkel.

  - A riasztásokkal rendelkező eszközök piros gyűrűvel jelennek meg

  - A riasztás nélküli eszközök szürke gyűrűvel jelennek meg

  - A csillagként megjelenő eszközök fontosként vannak megjelölve

Az eszköz típusa ikon csatlakoztatott eszközökkel jelenik meg.

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="kapcsolatok nézet":::

### <a name="detailed-view"></a>Részletes nézet 

A részletes nézet az alábbi információk segítségével jeleníti meg az eszközöket és az eszközök címkéit és kijelzőit:

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Részletes nézet":::

### <a name="control-the-zoom-view"></a>A nagyítás nézet szabályozása

A Térkép nézet a Térkép nagyítási szintjétől függ. A térképi nézetek közötti váltás a nagyítási szintek módosításával végezhető el.

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="A nagyítás nézet szabályozása":::

### <a name="enable-simplified-zoom-views"></a>Egyszerűsített nagyítási nézetek engedélyezése

Azok a rendszergazdák, akik a biztonsági elemzőknek és a RO-felhasználóknak szeretnének hozzáférni a madártávlatból és az eszközhöz, és begépelik a kapcsolódási nézeteket, engedélyezni kell az egyszerűsített nézet lehetőséget.

Az egyszerűsített leképezési nézetek engedélyezése:

  - Válassza a **Rendszerbeállítások** lehetőséget, majd az **egyszerűsített Térkép nézet** lehetőséget.

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="Leegyszerűsíti a Térkép nézetét":::

## <a name="learn-more-about-devices"></a>További tudnivalók az eszközökről

Számos eszköz érhető el, amelyekkel további információkat tudhat meg az eszközökről:

- [Az eszközök címkéi és kijelzői](#device-labels-and-indicators)

- [Eszközök gyors nézetei](#device-quick-views)

- [Eszköz tulajdonságainak megtekintése és kezelése](#view-and-manage-device-properties)

- [Eszközök típusának megtekintése](#view-device-types)

- [Hátlap](#backplane-properties)

- [Az eszközhöz tartozó események idővonalának megtekintése](#view-a-timeline-of-events-for-the-device)

- [Programozási adatok és változások elemzése](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>Az eszközök címkéi és kijelzői

A térképen a következő címkék és mutatók jelenhetnek meg az eszközökön:

| Eszköz címkéje | Leírás |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="IP-állomásnév"::: | IP-cím állomásneve és IP-címe, vagy alhálózat címe |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="Riasztások száma"::: | Az eszközhöz társított riasztások száma |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | Eszköz típusa ikon, például Storage, PLC vagy történész. |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="eszközök csoportosítva"::: | Egy IT-hálózatban lévő alhálózatban csoportosított eszközök száma. Ebben a példában a 8. |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="eszköz tanulási időszaka"::: | Olyan eszköz, amelyet a rendszer a tanulási időszak után észlelt, és nem volt engedélyezve hálózati eszközként. |
| Folytonos vonal | Logikai kapcsolat az eszközök között |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="Új eszköz"::: | A tanulás befejezése után felderített új eszköz. |

### <a name="device-quick-views"></a>Eszközök gyors nézetei

Az eszköz tulajdonságainak és kapcsolatainak elérése a térképről.

A gyors Tulajdonságok menü megnyitása:

  - Válassza a gyors Tulajdonságok menü :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="gyors tulajdonságok menüpontját":::.

#### <a name="quick-device-properties"></a>Gyors eszköz tulajdonságai

Válasszon ki egy eszközt vagy több eszközt, miközben a gyors tulajdonságok képernyő nyitva van az eszközök kiemelésének megtekintéséhez:

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="Gyors eszköz tulajdonságai":::

#### <a name="quick-connection-properties"></a>Gyors kapcsolatok tulajdonságai

Válassza ki a kapcsolódást, miközben a gyors tulajdonságok képernyő nyitva van, hogy megtekintse az ebben a kapcsolatban használt protokollokat, és amikor utoljára látták:

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="Gyors kapcsolatok tulajdonságai":::

## <a name="view-and-manage-device-properties"></a>Eszköz tulajdonságainak megtekintése és kezelése

Megtekintheti a térképen megjelenő összes eszköz tulajdonságainak. Például az eszköz neve, típusa vagy operációs rendszere, vagy a belső vezérlőprogram vagy a szállító.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Eszköz tulajdonságainak megtekintése és kezelése":::

A következő információk manuálisan is frissíthetők. A manuálisan megadott adatok felülbírálják a Defender által a IoT számára felderített információkat.

  - Név

  - Típus

  - Operációs rendszer

  - Purdue-réteg

  - Leírás

| Elem | Leírás |
|--|--|
| Alapvető adatok | Az alapvető információk szükségesek. |
| Name | Az eszköz neve. <br /> Alapértelmezés szerint az érzékelő felfedi a hálózatban definiált eszköznév nevét. Például a DNS-kiszolgálón definiált név. <br /> Ha nincs ilyen név meghatározva, az eszköz IP-címe ebben a mezőben jelenik meg. <br /> Az eszköz nevét manuálisan is módosíthatja. Adja meg az eszközöknek a funkcionalitását tükröző, értelmezhető neveket. |
| Típus | Az érzékelő által észlelt eszköz típusa. <br /> További információ: [eszközök típusának megtekintése](#view-device-types). |
| Szállító | Az eszköz gyártója. Ezt az eszköz MAC-címe vezető karakterei határozzák meg. Ez a mező csak olvasható. |
| Operációs rendszer | Az érzékelő által észlelt eszköz operációs rendszer. |
| Purdue-réteg | Az eszköz szenzora által azonosított Purdue-réteg, beleértve a következőket: <br /> – Automatikus <br /> – Folyamat vezérlése <br /> – Felügyelet <br /> – Vállalati |
| Leírás | Egy ingyenes szövegmező. <br /> További információk hozzáadása az eszközről. |
| Attribútumok | Az eszközön a tanulási időszakban felderített további információk, amelyek nem tartoznak más kategóriákhoz, az attribútumok szakaszban jelennek meg. <br /> Az információ a RO. |
| Beállítások | Az eszközbeállítások manuális módosításával megakadályozhatja a hamis pozitív értéket: <br /> - **Jogosult eszköz**: a tanulási időszak alatt a hálózatban felderített összes eszköz meghatalmazott eszközként van azonosítva. Ha az eszközt a tanulási időszak után észlelik, a rendszer alapértelmezés szerint jogosulatlan eszközként jelenik meg. Ezt a definíciót manuálisan is módosíthatja. <br /> - **Más néven képolvasó**: engedélyezze ezt a beállítást, ha tudja, hogy az eszköz képolvasóként ismert, és nincs szükség riasztásra. <br /> - **Programozási eszköz**: engedélyezze ezt a beállítást, ha tudja, hogy ez az eszköz programozási eszközként ismert, és a programozási módosítások végrehajtásához használatos. A programozási eszközként való azonosítás megakadályozza az adategységből származó programozási változások riasztásait. |
| Egyéni csoportok | Az eszköz azon egyéni csoportjai, amelyekben ez az eszköz részt vesz. |
| Állapot | Az eszköz biztonsága és engedélyezési állapota: <br /> – Az állapot akkor jelenik meg, `Secured` Ha nincsenek riasztások <br /> – Ha riasztás van az eszközről, a riasztások száma megjelenik. <br /> – Az állapot `Unauthorized` megjelenik azon eszközök esetében, amelyek a tanulási időszak után a hálózathoz lettek adva. Az eszközt manuálisan is megadhatja `Authorized Device` a beállításokban <br /> – Ha az eszköz címe dinamikus címként van definiálva, az `DHCP` állapothoz kerül. |


| Network (Hálózat) | Leírás |
|--|--|
| Interfészek | Az eszköz csatolói. Egy RO mező. |
| Protokollok | Az eszköz által használt protokollok. Egy RO mező. |
| Belső vezérlőprogram | Ha rendelkezésre áll a hátlap adatai, a belső vezérlőprogram adatai nem jelennek meg. |
| Cím | Az eszköz IP-címe. |
| Sorozatszám | Az eszköz sorozatszáma. |
| Modul címe | Az eszköz modellje és a tárolóhely száma vagy azonosítója. |
| Modellezés | Az eszköz modelljének száma. |
| Belső vezérlőprogram verziója | A belső vezérlőprogram verziószáma. |

Az eszköz adatainak megtekintése:

1. Válassza az **eszközök** lehetőséget az oldalsó menüben.

2. Kattintson a jobb gombbal egy eszközre, és válassza a **tulajdonságok megtekintése** lehetőséget. Megjelenik az eszköz Tulajdonságok ablak.

3. Az ablak alján lévő szükséges riasztásra kattintva részletes információkat jeleníthet meg az eszközre vonatkozó riasztásokról.

### <a name="view-device-types"></a>Eszközök típusának megtekintése

Az eszköz típusát az érzékelő automatikusan azonosítja az eszköz felderítési folyamata során. A típust manuálisan is módosíthatja.

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="Eszközök típusának megtekintése":::

A következő táblázat a rendszeren található összes típust ismerteti:

| Kategória | Eszköz típusa |
|--|--|
| ICS | Mérnöki állomás <br /> PLC <br />Történész <br />HMI <br />IED <br />DCS-vezérlő <br />RTU <br />Ipari csomagolási rendszerek <br />Ipari méret <br />Ipari robot <br />Üzembe helyezési pont <br />Mérő <br />Változó gyakoriságú meghajtó  <br />Robot vezérlő <br />Szervo-meghajtó <br />Pneumatikus eszköz <br />Marquee |
| IT | Tartományvezérlő <br />ADATBÁZIS-kiszolgáló <br />Munkaállomás <br />Kiszolgáló <br />Terminál állomás <br />Tárolás <br />Intelligens telefon <br />Táblagép <br />Biztonsági mentési kiszolgáló |
| IoT | IP-kamera <br />Nyomtató  <br />Lyukasztó óra <br />ATM <br />Intelligens TV <br />Játékkonzol <br />DVR <br />Ajtó Vezérlőpultján <br />HVAC <br />Okostelefonok <br />Tűzjelző <br />Intelligens fény <br />Intelligens kapcsoló <br />Tűzvédelmi detektor <br />IP-telefon <br />Riasztórendszer <br />Riasztási sziréna <br />Mozgásérzékelő <br />Lift <br />Páratartalom-érzékelő <br />Vonalkódolvasó <br />Szünetmentes áramforrás <br />Személyek számláló rendszere <br />Intercom <br />Forgóajtón |
| Network (Hálózat) | Vezeték nélküli hozzáférési pont <br />Útválasztó <br />Kapcsoló (switch) <br />Firewall <br />VPN Gateway <br />NTP-kiszolgáló <br />Wi-Fi ananász <br />Fizikai hely <br />I/O-adapter <br /> Protokoll-átalakító |

Az eszköz adatainak megtekintése:

1.  Válassza az **eszközök** lehetőséget az oldalsó menüben.

2. Kattintson a jobb gombbal egy eszközre, és válassza a **tulajdonságok megtekintése** lehetőséget. Megjelenik az eszköz Tulajdonságok ablak.

3. Válassza ki a kívánt riasztást az eszközre vonatkozó riasztások részletes adatainak megtekintéséhez.

### <a name="backplane-properties"></a>Hátlap tulajdonságai

Ha egy PLC több modult tartalmaz állványokra és tárolóhelyekre, a jellemzők változhatnak a modul lapjain. Ha például az IP-cím és a MAC-cím azonos, a belső vezérlőprogram eltérő lehet.

A hátlap lehetőség használatával több vezérlőt/kártyát és azok beágyazott eszközeit is áttekintheti egy entitásként, különböző definíciókkal. A hátlap nézet minden egyes tárolóhelye a mögöttes eszközöket jelenti – a mögötte észlelt eszközöket.

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="Hátlap tulajdonságai":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="Hátlap eszköz tulajdonságai":::

A hátlap legfeljebb 30 vezérlő kártyát és akár 30 rack egységet is tartalmazhat. A több szinten található eszközök teljes száma legfeljebb 200 eszköz lehet.

A hátlap panel az eszköz Tulajdonságok ablak jelenik meg, amikor a rendszer a hátlap adatait észleli.

Az egyes tárolóhelyek a mögöttes eszközök számát és a modul típusát megjelenítő ikont jelenítik meg.

| Ikon | Modultípus |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="Tápegység"::: | Tápegység |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="Analóg I/O"::: | Analóg I/O |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="Kommunikációs adapter"::: | Kommunikációs adapter |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="Digitális I/O"::: | Digitális I/O |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="CPU"::: | CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="Általános"::: | Általános |

Amikor kiválaszt egy tárolóhelyet, megjelenik a tárolóhely részletei:

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="Válasszon egy tárolóhelyet":::

Ha a mögöttes eszközöket szeretné megtekinteni a tárolóhely mögött, válassza a **MEGTEKINTÉS a térképen** lehetőséget. A tárolóhely az eszköz térképén található, amely az összes mögöttes modult és eszközt csatlakoztatja.

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="MEGTEKINTÉS A TÉRKÉPEN":::

## <a name="view-a-timeline-of-events-for-the-device"></a>Az eszközhöz tartozó események idővonalának megtekintése

Egy eszközhöz társított események idővonalának megtekintése.

Az idősor megtekintése:

1. Kattintson a jobb gombbal egy eszközre a térképen.

2. Válassza az **események megjelenítése** lehetőséget. Megnyílik az esemény idővonala ablak, amely a kiválasztott eszközre vonatkozóan észlelt eseményekkel kapcsolatos információkat ismerteti.

Részletekért lásd az [esemény idővonalát](#event-timeline) .

## <a name="analyze-programming-details-and-changes"></a>Programozási adatok és változások elemzése

A kriminalisztika fejlesztése a hálózati eszközökön végrehajtott programozási események megjelenítésével és a kód módosításainak elemzésével. Ez az információ segít felderíteni a gyanús programozási tevékenységeket, például:

  - Emberi hiba: a mérnök nem megfelelő eszközt dolgoz fel.

  - Sérült programozási automatizálás: az automatizálási hiba miatt a program hibásan lett végrehajtva.

  - Feltört rendszerek: egy programozási eszközbe bejelentkezett jogosulatlan felhasználók.

Megjeleníthet egy programozott eszközt, és görgetheti a más eszközökön végrehajtott különböző programozási változtatásokat.

Megtekintheti a programozási eszköz által hozzáadott, módosított, eltávolított vagy újratöltve kódot. Megkeresheti a programozási módosításokat a fájltípusok, dátumok vagy érdekes időpontok alapján.

### <a name="when-to-review-programming-activity"></a>Mikor érdemes áttekinteni a programozási tevékenységet 

Előfordulhat, hogy át kell tekintenie a programozási tevékenységet:

  - A jogosulatlan programozással kapcsolatos riasztás megtekintése után

  - A vezérlők tervezett frissítését követően

  - Ha egy folyamat vagy gép nem működik megfelelően (hogy megtudja, ki hajtotta végre a legutóbbi frissítést, és mikor)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="Programozási változási napló":::

A többi lehetőség a következőket teszi lehetővé:

  - Egy csillaggal kapcsolatos érdekes események megjelölése.

  - Töltsön le egy *. txt fájlt az aktuális kóddal.

### <a name="about-authorized-vs-unauthorized-programming-events"></a>Az engedélyezett vs jogosulatlan programozási események 

A jogosulatlan programozási eseményeket olyan eszközök hajtják végre, amelyek nem tanultak meg vagy manuálisan határozták meg őket programozási eszközként. Az erre jogosult programozási eseményeket olyan eszközök hajtják végre, amelyek feloldottak vagy manuálisan definiáltak programozási eszközként.

A programozási elemzés ablak a hitelesített és a jogosulatlan programozási eseményeket is megjeleníti.

### <a name="accessing-programming-details-and-changes"></a>A programozási adatok és a módosítások elérése

A programozási elemzési ablak elérése a következő helyről:

- [Esemény idővonala](#event-timeline)

- [Jogosulatlan programozási riasztások](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>Esemény idővonala

Az esemény idővonala segítségével megjelenítheti a programozási változásokat észlelő események idővonalát.

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="Az esemény idővonalának nézete.":::

### <a name="unauthorized-programming-alerts"></a>Jogosulatlan programozási riasztások

A riasztások akkor aktiválódnak, ha a jogosulatlan programozási eszközök végzik a programozási tevékenységeket.

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="Jogosulatlan programozási riasztások":::

> [!NOTE]
> Az alapszintű programozási információkat az eszköz Tulajdonságok ablak és az eszközök leltárában is megtekintheti.

### <a name="working-in-the-programming-timeline-window"></a>Munka a programozási idővonal ablakban

Ez a szakasz a programozási fájlok megtekintését és a verziók összehasonlítását ismerteti. Megkeresheti a programozott eszközre eljuttatott adott fájlokat. Fájlok keresése a következő alapján:

  - Date

  - Fájl típusa

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="ütemezés ütemezése ablak":::

|Programozási idővonal típusa | Leírás |
|--|--|
| Programozott eszköz | A programozott eszköz részletes adatait tartalmazza, beleértve az állomásnevet és a fájlt. |
| Legutóbbi események | Az érzékelő által észlelt 50 legutóbbi eseményeket jeleníti meg. <br />Egy esemény kiemeléséhez vigye az egérmutatót a fölé, és kattintson a csillagra. :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> Az utolsó 50 esemény megtekinthető. |
| Fájlok | Megjeleníti a kiválasztott dátumhoz és a programozott eszköz fájlméretéhez észlelt fájlokat. <br /> Alapértelmezés szerint az eszközönkénti megjelenítéshez elérhető fájlok maximális száma 300. <br /> Alapértelmezés szerint a fájlok maximális mérete 15 MB. |
| Fájl állapota :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | A fájlok címkéi a fájl állapotát jelzik az eszközön, beleértve a következőket: <br /> **Hozzáadva**: a fájl hozzá lett adva a végponthoz a kiválasztott dátumon vagy időpontban. <br /> **Frissítve**: a fájl a kiválasztott dátumon vagy időpontban frissült. <br /> **Törölve**: a fájl el lett távolítva. <br /> **Nincs címke**: a fájl nem módosult.   |
| Programozási eszköz | A programozási változást használó eszköz. Előfordulhat, hogy több eszköz programozási változtatásokat hajtott végre egy programozott eszközön. Megjelenik az állomásnév, a dátum vagy a módosítás időpontja és a bejelentkezett felhasználó. |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | Megjeleníti a programozott eszközre telepített aktuális fájlt. |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | Töltse le a megjelenő kód szöveges fájlját. |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | Hasonlítsa össze az aktuális fájlt a kijelölt időpontban észlelt fájllal. |

### <a name="choose-a-file-to-review"></a>Válassza ki az áttekinteni kívánt fájlt

Ez a szakasz azt ismerteti, hogyan választható ki az áttekinthető fájl.

Válassza ki az áttekinteni kívánt fájlt:

1. Válasszon ki egy eseményt a **legutóbbi események** ablaktáblából.

2. Válasszon ki egy fájlt a fájl ablaktáblán. A fájl megjelenik az aktuális ablaktáblán.

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="Válassza ki a fájlt, amellyel dolgozni szeretne.":::

### <a name="compare-files"></a>Fájlok összehasonlítása

Ez a szakasz a programozási fájlok összehasonlítását ismerteti.

Összehasonlítás:

1. Válasszon ki egy eseményt a legutóbbi események ablaktáblából.

2. Válasszon ki egy fájlt a fájl ablaktáblán. A fájl megjelenik az aktuális ablaktáblán. Ezt a fájlt más fájlokhoz is összehasonlíthatja.

3. Válassza az összehasonlítás mutatót.

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="Kijelző összehasonlítása":::

   Az ablakban az összes olyan dátum látható, amelyet a kiválasztott fájl észlelt a programozott eszközön. Lehetséges, hogy a fájlt több programozási eszköz is frissítette a programozott eszközön.

   Az észlelt különbségek száma az ablak jobb felső sarkában jelenik meg. Előfordulhat, hogy lefelé kell görgetni a különbségek megjelenítéséhez.

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="Görgessen le a kijelöléshez":::

   A számot a módosított szöveg szomszédos sorai alapján számítja ki a rendszer. Ha például a kód nyolc egymást követő sora módosult (törölve, frissítve vagy hozzáadva), akkor ez egy különbségnek számít.

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="A programozás idővonalának nézete.":::

4. Válasszon dátumot. A kiválasztott időpontban észlelt fájl megjelenik az ablakban.

5. A legutóbbi események/fájlok ablaktáblán kiválasztott fájl mindig a jobb oldalon jelenik meg.

### <a name="device-programming-information-other-locations"></a>Eszköz programozási adatai: egyéb helyszínek

A programozási idővonalban a részletek áttekintése mellett a programozási információk is elérhetők az eszközön Tulajdonságok ablak és az eszközök leltárában.

| Eszköz típusa | Leírás |
|--|--|
| Eszköztulajdonságok | Az eszköz tulajdonságai ablak a device\. észlelt utolsó programozási eseményről nyújt információt. :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="Az eszköz tulajdonságai"::: |
| Az eszköz leltára | Az eszköz leltára azt jelzi, hogy az eszköz egy programozási device\. :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="Az eszközök leltára"::: |

## <a name="manage-device-information-from-the-map"></a>Az eszköz adatainak kezelése a térképről

Az érzékelő nem frissíti és nem befolyásolja az eszközöket közvetlenül a hálózaton. Az itt végrehajtott módosítások csak az eszköz elemzését befolyásolják.

### <a name="delete-devices"></a>Eszközök törlése

Előfordulhat, hogy törölni szeretne egy eszközt, ha a megismert információk nem relevánsak. Példa:

  - A mérnöki munkaállomáson lévő partneri kivitelező átmenetileg csatlakozik a konfigurációs frissítések végrehajtásához. A feladat befejezése után az eszköz el lesz távolítva.

  - A hálózat változásai miatt egyes eszközök már nem csatlakoznak.

Ha nem törli az eszközt, az érzékelő továbbra is figyeli. 60 nap után megjelenik egy értesítés, amely javaslatot tesz a törlésre.

Riasztás jelenhet meg, amely jelzi, hogy az eszköz nem válaszol, ha egy másik eszköz megpróbálja elérni. Ebben az esetben előfordulhat, hogy a hálózat hibásan van konfigurálva.

Az eszköz el lesz távolítva az eszköz térképéről, az eszközök Leltáráról és az adatbányászati jelentésekről. Egyéb információk, például: a widgetekben tárolt információk továbbra is érvényben maradnak.

Az eszköznek legalább 10 percig inaktívnak kell lennie a törléshez.

Eszköz törlése az eszköz térképéről:

1. Válassza az **eszközök** lehetőséget az oldalsó menüben.

2. Kattintson a jobb gombbal egy eszközre, és válassza a **Törlés** lehetőséget.

### <a name="merge-devices"></a>Eszközök egyesítése

Bizonyos esetekben előfordulhat, hogy egyesíteni kell az eszközöket. Erre akkor lehet szükség, ha az érzékelő külön hálózati entitásokat észlelt, amelyek egy egyedi eszközhöz vannak társítva. Példa:

  - Egy PLC négy hálózati kártyával.

  - Egy laptop WIFI és fizikai kártyával.
  
  - Két vagy több hálózati kártyával rendelkező munkaállomás.

Egyesítéskor arra utasítja az érzékelőt, hogy egyesítse két eszköz eszközének tulajdonságait. Ha ezt teszi, a rendszer az eszköz Tulajdonságok ablak és az érzékelő jelentéseit az új eszköz tulajdonság részleteivel frissíti.

Ha például két eszközt egyesít, amelyek mindegyike IP-címmel rendelkezik, akkor mindkét IP-cím külön illesztőfelületként jelenik meg az eszköz Tulajdonságok ablak. Csak a jóváhagyott eszközöket lehet egyesíteni.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Eszköz Tulajdonságok ablak":::

Az esemény idővonala az egyesítési eseményt mutatja be.

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="Az esemény idővonala egyesített eseményekkel.":::

Az eszközök egyesítése nem vonható vissza. Ha véletlenül két eszközt egyesít, törölje az eszközt, és várjon, amíg az érzékelő újra fel nem deríti a kettőt.

Eszközök egyesítése:

1. Válasszon ki két eszközt (Shift + kattintás), majd kattintson a jobb gombbal az egyikre.

2. Válassza az **Egyesítés** lehetőséget az eszközök egyesítéséhez. Az egyesítés akár 2 percet is igénybe vehet.

3. Az eszközök egyesítése attribútumok megadása párbeszédpanelen válassza ki az eszköz nevét.

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="attribútumok párbeszédpanel":::

4. Kattintson a **Mentés** gombra.

### <a name="authorize-and-unauthorize-devices"></a>Eszközök engedélyezésének és engedélyezésének engedélyezése

A tanulási időszak alatt a hálózatban felderített összes eszköz meghatalmazott eszközként van azonosítva. A **hitelesítő** címke nem jelenik meg ezeken az eszközökön az eszköz térképen.

Ha a tanulási időszak után észlelnek egy eszközt, az nem engedélyezett eszközként jelenik meg. Azon kívül, hogy a térképen megjelenjenek a jogosulatlan eszközök, az eszközök leltárában is megtekintheti őket.

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="Eszköz leltározása":::

**Új eszköz vs jogosulatlan**

Az új eszközök észlelését követően a tanulási időszak a `New` és a címkével fog megjelenni `Unauthorized` .

Ha áthelyez egy eszközt a térképen, vagy manuálisan módosítja az eszköz tulajdonságait, a `New` címke el lesz távolítva az eszköz ikonból.

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>Jogosulatlan eszközök – támadási vektorok és kockázatértékelési jelentések

A kockázatfelmérési jelentések és a támadási vektorok jelentéseiben nem engedélyezett eszközök szerepelnek.

- **Támadási vektoros jelentések:** A jogosulatlanként megjelölt eszközöket a rendszer a támadási vektorban oldja fel, mivel ez fenyegetést jelenthet a hálózatra.

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="Eseménynézetet állapotnézetet a támadási vektoros jelentéseket":::

- **Kockázatértékelési jelentések:** A nem engedélyezettként megjelölt eszközök:

  - Kockázatértékelési jelentésekben azonosítva

Az eszközök hitelesítésének engedélyezése vagy letiltása manuálisan:

1. Kattintson a jobb gombbal az eszközre a térképen, és válassza az **Engedélyezés engedélyezése** lehetőséget.

### <a name="mark-devices-as-important"></a>Eszközök megjelölése fontosként

Jelentős hálózati eszközöket is megjelölheti fontosként, például az üzleti szempontból kritikus fontosságú kiszolgálókat. Ezek az eszközök a térképen csillaggal vannak megjelölve. A csillag a Térkép nagyítási szintjétől függően változik.

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>Fontos eszközök – támadási vektorok és kockázatértékelési jelentések

A fontos eszközöket a kockázatértékelési jelentések és a támadási vektorok jelentéseinek létrehozásakor számítjuk ki.

  - A támadási vektor a fontosként megjelölt eszközöket a támadási vektorban támadási célokként oldja meg. 

  - Kockázatfelmérési jelentések: a fontosként megjelölt eszközök kiszámítása a kockázatértékelési jelentés biztonsági pontszámának megadásakor történik.

## <a name="generate-activity-reports-from-the-map"></a>Tevékenység-jelentések készítése a térképről

Tevékenység-jelentés létrehozása egy kiválasztott eszközhöz az 1, 6, 12 vagy 24 óra alatt. A következő információk érhetők el:

  - Kategória: az alapvető észlelési információk a forgalmi forgatókönyvek alapján.

  - Forrás és cél eszközök

  - Adatok: a további információk hibásak.

  - A legutóbb látott idő és dátum.

A jelentést Microsoft Excel-vagy Word-fájlként is mentheti.

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="Közelmúltbeli tevékenység":::

Tevékenység jelentés létrehozása egy eszközhöz:

1. Kattintson a jobb gombbal egy eszközre a térképen.

2. Válassza ki a tevékenység jelentését.

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="Megtekintheti a tevékenység jelentését.":::

## <a name="generate-attack-vector-reports-from-the-map"></a>Támadási vektoros jelentések készítése a térképről

Szimulálja a támadási vektor jelentését, hogy megtudja, van-e a kiválasztott térképen egy eszköz, amely sebezhető támadási célpont.

A támadási vektor jelentései a kihasználható eszközök sebezhetőségi láncának grafikus megjelenítését teszik lehetővé. Ezek a biztonsági rések a támadók számára hozzáférést biztosíthatnak a legfontosabb hálózati eszközökhöz. A támadási vektor szimulátor valós időben kiszámítja a támadási vektorokat, és egy adott célponton elemzi az összes támadási vektort.

Eszköz megtekintése támadási vektoros jelentésekben:

1. Kattintson a jobb gombbal egy eszközre a térképen.

2. Válassza ki a **támadási vektorok szimulálása** elemet. A támadási vektor párbeszédpanel megnyílik a támadási célként kiválasztott eszközzel.

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="Támadási vektor szimulációjának hozzáadása":::

3. Adja hozzá a többi paramétert a párbeszédpanelhez, és válassza a **szimulációs Hozzáadás** lehetőséget.

## <a name="export-device-information-from-the-map"></a>Eszköz adatainak exportálása a térképről

Exportálja a következő eszköz adatait a térképről.

  - Eszköz részletei (Microsoft Excel)

  - Eszköz összefoglalása (Microsoft Excel)

  - Egy Word-fájl csoportokkal (Microsoft Word)

Exportálás:

1. Válassza az Exportálás ikont a térképről.

1. Válassza ki az exportálási lehetőséget.

## <a name="see-also"></a>Lásd még

[Érzékelő észlelésének vizsgálata egy eszköz leltározásakor](how-to-investigate-sensor-detections-in-a-device-inventory.md)
