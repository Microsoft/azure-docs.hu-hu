---
title: A Microsoft Threat Modeling Tool funkcióinak áttekintése – Azure
description: Ismerje meg a Threat Modeling Toolban elérhető összes funkciót, például az elemzési nézetet és a jelentéseket.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 08/17/2017
ms.openlocfilehash: 65a88f309602462e77336181316c0d5bf19a8a1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90980683"
---
# <a name="threat-modeling-tool-feature-overview"></a>A Threat Modeling Tool funkcióinak áttekintése

A Threat Modeling Tool segíthet a veszélyforrások modellezési igényeiben. Az eszköz alapszintű bevezetését itt tekintheti meg: [az Threat Modeling Tool használatának első lépései](threat-modeling-tool-getting-started.md).

> [!NOTE]
>A Threat Modeling Tool gyakran frissülnek, ezért ebben az útmutatóban gyakran tekintheti meg a legújabb funkciókat és a továbbfejlesztett szolgáltatásokat.

Üres lap megnyitásához válassza **a modell létrehozása** lehetőséget.

![Üres lap](./media/threat-modeling-tool-feature-overview/tmtstart.png)

Az eszközben jelenleg elérhető funkciók megtekintéséhez használja az [első lépések](threat-modeling-tool-getting-started.md) példájában a csapat által létrehozott veszélyforrás modellt.

![Alapszintű veszélyforrások modellje](./media/threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigáció

Mielőtt megbeszéljük a beépített funkciókat, tekintsük át az eszköz fő összetevőit.

### <a name="menu-items"></a>Menüelemek

A felhasználói élmény hasonló más Microsoft-termékekhez. Tekintsük át a legfelső szintű menüelemeket.

![Menüelemek](./media/threat-modeling-tool-feature-overview/menuitems.png)

| Címke                               | Részletek      |
| --------------------------------------- | ------------ |
| **Fájl** | <ul><li>Fájlok megnyitása, mentése és lezárása</li><li>Jelentkezzen be, és jelentkezzen ki a OneDrive-fiókok közül.</li><li>Hivatkozások megosztása (megtekintés és szerkesztés).</li><li>A fájl adatainak megtekintése.</li><li>Új sablon alkalmazása meglévő modellekre.</li></ul> |
| **Szerkesztés** | Visszavonhatja és megismételheti a műveleteket, valamint a másolás, beillesztés és törlés műveletet. |
| **Megtekintés** | <ul><li>Váltás az **elemzési** és a **tervezési** nézetek között.</li><li>Nyissa meg a bezárt Windows (például a rajzsablonok, az elemek tulajdonságai és az üzenetek) elemet.</li><li>Az elrendezés alaphelyzetbe állítása az alapértelmezett beállításokra.</li></ul> |
| **Ábra** | Diagramokat adhat hozzá és törölhet, valamint diagramokat helyezhet át lapokon. |
| **Jelentések** | HTML-jelentések létrehozása másokkal való megosztáshoz. |
| **Súgó** | Útmutatást talál az eszköz használatának megkönnyítéséhez. |

A szimbólumok a legfelső szintű menük parancsikonjai:

| Szimbólum                               | Részletek      |
| --------------------------------------- | ------------ |
| **Megnyitás** | Új fájl megnyitása. |
| **Mentés** | Az aktuális fájl mentése. |
| **Tervezés** | Megnyitja a **tervezési** nézetet, ahol létrehozhat modelleket. |
| **Elemzés** | Megjeleníti a generált fenyegetéseket és azok tulajdonságait. |
| **Diagram hozzáadása** | Új diagramot szúr be (az Excel új lapjaihoz hasonlóan). |
| **Diagram törlése** | Törli az aktuális diagramot. |
| **Másolás/kivágás/beillesztés** | Elemek másolása, darabolása és beillesztése. |
| **Visszavonás/ismétlés** | Visszafejti és végrehajtja a műveleteket. |
| **Nagyítás/kicsinyítés** | A diagram nagyítása és kicsinyítése jobb nézetben. |
| **Visszajelzés** | Az MSDN-fórum megnyitása. |

### <a name="canvas"></a>Vászon

A vászon az elemek húzásának helye. A modellek kiépítésének leggyorsabb és leghatékonyabb módja a drag and drop. A menüben kattintson a jobb gombbal az elemek elemre, és válassza ki az elemek általános verzióit, ahogy az a következő képen is látható:

#### <a name="drop-the-stencil-on-the-canvas"></a>A rajzsablon eldobása a vásznon

![Vászon eldobása](./media/threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>A rajzsablon kijelölése

![Elem tulajdonságai](./media/threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Rajzsablonok

A kiválasztott sablon alapján megkeresheti az összes használható rajzsablont. Ha nem találja a megfelelő elemeket, használjon másik sablont. Vagy módosíthatja az igényeinek megfelelő sablont is. Általában a következő kategóriákhoz hasonló kategóriákat talál:

| Sablon neve                               | Részletek      |
| --------------------------------------- | ------------ |
| **Folyamat** | Alkalmazások, böngésző beépülő modulok, szálak, virtuális gépek |
| **Külső reaktor** | Hitelesítésszolgáltatók, böngészők, felhasználók, webalkalmazások |
| **Adattár** | Gyorsítótár, tárolás, konfigurációs fájlok, adatbázisok, beállításjegyzék |
| **Adatfolyam** | Bináris, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, nevesített cső, RPC/DCOM, SMB, UDP |
| **Megbízhatósági vonal/határ határa** | Vállalati hálózatok, Internet, gép, homokozó, felhasználói/kernel mód |

### <a name="notesmessages"></a>Megjegyzések/üzenetek

| Összetevő                               | Részletek      |
| --------------------------------------- | ------------ |
| **Üzenetek** | Belső eszköz logikája, amely riasztást küld a felhasználóknak a hibákról, például az elemek közötti adatforgalomról. |
| **Megjegyzések** | A manuális megjegyzések a tervezési és felülvizsgálati folyamat során az Engineering Teams használatával adhatók hozzá a fájlhoz. |

### <a name="element-properties"></a>Elem tulajdonságai

Az elem tulajdonságai a kiválasztott elemektől függően változnak. A megbízhatóság határain kívül minden más elem három általános beállítást tartalmaz:

| Elem tulajdonsága                               | Részletek      |
| --------------------------------------- | ------------ |
| **Név** | Hasznos lehet a folyamatok, a tárolók, a reaktorok és a folyamatok elnevezéséhez, hogy azok könnyen felismerhetők legyenek. |
| **Hatókörön kívül** | Ha be van jelölve, a rendszer kiveszi az elemet a fenyegetést generáló mátrixból (nem ajánlott). |
| **Hatókörön kívüli ok** | Indoklási mező, amely lehetővé teszi a felhasználóknak, hogy a hatókör kiválasztásának miért van kiválasztva. |

A tulajdonságok az egyes elemek kategóriái alatt változnak. Válassza ki az egyes elemeket az elérhető beállítások vizsgálatához. A sablont megnyitva további információkat is megtudhat. Nézzük át a funkciókat.

## <a name="welcome-screen"></a>Üdvözlőképernyő

Az alkalmazás megnyitásakor megjelenik az **üdvözlőképernyő** .

### <a name="open-a-model"></a>Modell megnyitása

Két lehetőség megjelenítéséhez vigye a kurzort a **nyílt modell** fölé, és nyissa meg **ezt a számítógépet** , és **nyissa** meg a OneDrive. Az első lehetőség megnyitja a **fájl megnyitása** képernyőt. A második lehetőség végigvezeti a bejelentkezési folyamaton a OneDrive. A sikeres hitelesítés után kiválaszthatja a mappákat és a fájlokat.

![Modell megnyitása](./media/threat-modeling-tool-feature-overview/openmodel.png)

![Megnyitás számítógépről vagy OneDrive](./media/threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Visszajelzések, javaslatok és problémák

Ha a **visszajelzések, javaslatok és problémák** lehetőségre kattint, az MSDN-fórumba LÉPHET az SDL-eszközökhöz. Elolvashatja, hogy mások mit mondanak az eszközről, beleértve a megkerülő megoldásokat és az új ötleteket is.

![A képernyőképen egy, a szöveges visszajelzéseket, a javaslatokat és a problémákat tartalmazó gomb látható.](./media/threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Tervező nézet

Új modell megnyitásakor vagy létrehozásakor megnyílik a **tervezési** nézet.

### <a name="add-elements"></a>Elemek hozzáadása

A rácson a következő két módon adhat hozzá elemeket:

- **Drag and drop**: húzza a kívánt elemet a rácsra. Ezután használja az elem tulajdonságait további információk megadásához.
- **Kattintson a jobb** gombbal: kattintson a jobb gombbal a rács tetszőleges pontjára, és válassza az elemek elemet a legördülő menüből. A képernyőn megjelenik a kiválasztott elem általános ábrázolása.

### <a name="connect-elements"></a>Elemek összekapcsolása

Az elemek kétféleképpen is csatlakoztathatók:

- **Drag and drop**: húzza a kívánt adatfolyam a rácsra, és mindkét végpontot a megfelelő elemekhez kapcsolja.
- **Kattintson a + SHIFT** gombra: kattintson az első elemre (adatok küldése), nyomja le és tartsa lenyomva a SHIFT billentyűt, majd válassza ki a második elemet (adatok fogadása). Kattintson a jobb gombbal, majd válassza a **kapcsolat** lehetőséget. Ha kétirányú adatfolyamot használ, a sorrend nem annyira fontos.

### <a name="properties"></a>Tulajdonságok

 A rajzsablonon módosítható tulajdonságok megtekintéséhez válassza ki a rajzsablont, és az adatok ennek megfelelően töltődnek fel. Az alábbi példa azt mutatja, hogy az **adatbázis** -rajzsablon előtt és után a diagramra van húzva:

#### <a name="before"></a>Előtte

![Előtte](./media/threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Utána

![Utána](./media/threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Üzenetek

Ha létrehoz egy fenyegetési modellt, és elfelejti az adatfolyamatok elemekhez való összekapcsolását, értesítést kap. Figyelmen kívül hagyhatja az üzenetet, vagy a probléma megoldásához kövesse az utasításokat. 

![Képernyőfelvétel: a veszélyforrások modellje összekötő, amely nem kapcsolódik az elemekhez, a probléma okozta üzenettel.](./media/threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Jegyzetek

Ha megjegyzéseket szeretne adni a diagramhoz, váltson az **üzenetek** lapra a **jegyzetek** lapra.

## <a name="analysis-view"></a>Elemzési nézet

A diagram összeállítását követően válassza ki az **elemzési** szimbólumot (a Nagyítót) a parancsikonok eszköztáron az **elemzési** nézetre való váltáshoz.

![Elemzési nézet](./media/threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Generált fenyegetés kiválasztása

Amikor kijelöl egy fenyegetést, három különböző függvényt használhat:

| Szolgáltatás                               | Tájékoztatás      |
| --------------------------------------- | ------------ |
| **Olvasási kijelző** | <p>A fenyegetés olvasottként van megjelölve, így nyomon követheti az áttekintett elemeket.</p><p>![Olvasási/olvasatlan kijelző](./media/threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Interakciós fókusz** | <p>A fenyegetéshez tartozó diagramon lévő interakció ki van emelve.</p><p>![Interakciós fókusz](./media/threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Fenyegetéstulajdonságok** | <p>A fenyegetéssel kapcsolatos további információk a **fenyegetés tulajdonságai** ablakban láthatók.</p><p>![Fenyegetéstulajdonságok](./media/threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Prioritás módosítása

Megváltoztathatja az egyes generált fenyegetések prioritási szintjét. A különböző színek megkönnyítik a magas, közepes és alacsony prioritású fenyegetések azonosítását.

![Prioritás módosítása](./media/threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Fenyegetés tulajdonságai szerkeszthető mezők

Ahogy az előző képen is látható, módosíthatja az eszköz által létrehozott adatokat. Bizonyos mezőkhöz, például az indokláshoz is hozzáadhat adatokat. Ezeket a mezőket a sablon hozza létre. Ha további információra van szüksége az egyes fenyegetésekről, módosításokat végezhet.

![Fenyegetéstulajdonságok](./media/threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Jelentések

Miután befejezte a prioritások módosítását és az egyes generált fenyegetések állapotának frissítését, mentheti a fájlt, és/vagy kinyomtathatja a jelentést. Nyissa meg a **jelentés**  >  **létrehozása teljes jelentést**. Nevezze el a jelentést, és az alábbi képhez hasonlóan kell megjelennie:

![Képernyőfelvétel: példa a veszélyforrások modellezésére szolgáló jelentésre, beleértve az összegzést, a diagramokat és az egyéb információkat.](./media/threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Következő lépések

- Küldje el kérdéseit, megjegyzéseit és problémáit tmtextsupport@microsoft.com . A kezdéshez **[töltse le](https://aka.ms/threatmodelingtool)** a Threat Modeling Tool.
- A sablon a Közösséghez való hozzájárulásához lépjen a [GitHub](https://github.com/Microsoft/threat-modeling-templates) oldalra.