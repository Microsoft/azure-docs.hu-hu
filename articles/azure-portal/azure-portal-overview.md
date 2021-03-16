---
title: Az Azure Portal áttekintése
description: Az Azure Portal egy grafikus felhasználói felület, amelyet az Azure-szolgáltatások kezeléséhez használhat. Megtudhatja, hogyan navigálhat és kereshet erőforrásokat a Azure Portalban.
keywords: portál
ms.date: 03/12/2021
ms.topic: overview
ms.openlocfilehash: 8f61934e6af150c18d1331c2b28e2686641967d1
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563994"
---
# <a name="azure-portal-overview"></a>Az Azure Portal áttekintése

Ez a cikk bemutatja a Azure Portal, azonosítja a portál oldal elemeit, és segítséget nyújt a Azure Portal felügyeleti felületének megismeréséhez.

## <a name="what-is-the-azure-portal"></a>Mi az az Azure Portal?

Az Azure Portal egy egységesített felületet biztosító webes konzol, amely alternatívát biztosít a parancssori eszközökkel szemben. A Azure Portalon egy grafikus felhasználói felületen kezelheti Azure-előfizetését. Az egyszerű webalkalmazásokból az összetett felhőben üzemelő példányokig bármit létrehozhat, kezelhet és figyelhet. Egyéni irányítópultokat hozhat létre az erőforrások rendezett megtekintéséhez. Kisegítő lehetőségeket állíthat be az optimális felhasználói élmény érdekében.

Az Azure Portalt úgy tervezték, hogy rugalmas legyen és folyamatosan rendelkezésre álljon. Minden Azure-adatközpontban jelen van. Ez a konfiguráció megkönnyíti az egyes adatközpontok hibáinak Azure Portalét, és elkerüli a hálózat lelassulását azáltal, hogy a felhasználókhoz közeledik. Az Azure Portal folyamatosan frissül, és nem igényel állásidőt a karbantartási tevékenységekhez.

## <a name="azure-portal-menu"></a>Azure Portal menü

Kiválaszthatja a portál menü alapértelmezett módját. Dokkolható, vagy beállítható a kilépési panelként.

Ha a portál menüje a kilépési módban van, akkor a rendszer rejtve marad, amíg nincs rá szüksége. Válassza a menü ikont a menü megnyitásához vagy bezárásához.

![Azure Portal menü a kinézeti módban](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

Ha a portál menü dokkolt mód elemét választja, mindig látható lesz. A menü összecsukásával több munkaterületet is megadhat.

![Azure Portal menü dokkolt módban](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Azure Kezdőlap

Az Azure-szolgáltatások új előfizetői a [portálra való bejelentkezés](https://portal.azure.com) után elsőként láthatják az **Azure kezdőlapját**. Ez az oldal olyan erőforrásokat fordít le, amelyek segítenek az Azure-előfizetések legtöbbje számára. Az ingyenes online tanfolyamokra, dokumentációra, alapszolgáltatásokra és hasznos webhelyekre mutató hivatkozásokat tartalmaz, amelyekkel naprakészen maradhat, és kezelheti a szervezete változásait. A munkához való gyors és könnyű hozzáférés érdekében a legutóbb felkeresett erőforrások listáját is megjeleníti. Ezt a lapot nem szabhatja testre, de kiválaszthatja, hogy az **Azure Home** vagy az **Azure irányítópultot** szeretné-e látni alapértelmezett nézetként. Amikor először jelentkezik be, az oldal tetején megjelenik egy üzenet, amelyen mentheti a beállításokat.

![Képernyőfelvétel: a beállítások mentésének helye.](./media/azure-portal-overview/azure-portal-default-view.png)

A **portál beállításaiban** a Azure Portal menü és az Azure alapértelmezett nézet is módosítható. Ha megváltoztatja a kijelölést, a rendszer azonnal alkalmazza a módosítást.

![Az alapértelmezett nézet-választót ábrázoló képernyőfelvétel](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Azure-irányítópult

Az irányítópultok célzott áttekintést nyújtanak az előfizetésben található azon erőforrásokról, amelyek a leginkább az Ön számára fontosak. A kezdéshez egy alapértelmezett irányítópultot kaptunk. Ezt az irányítópultot testreszabhatja úgy, hogy a gyakran használt erőforrásokat egyetlen nézetben hozza. Az alapértelmezett nézetben végrehajtott módosítások csak a felhasználói élményt érintik. Létrehozhat azonban további irányítópultokat saját használatra, vagy közzéteheti a testreszabott irányítópultokat, és megoszthatja azokat a szervezet más felhasználóival. További információ: [irányítópultok létrehozása és megosztása a Azure Portalban](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Ismerkedés a portál körül

Hasznos megérteni az alapszintű portál elrendezését és a velük való interakciót. Itt bemutatjuk a felhasználói felület összetevőit és az utasításokhoz használt terminológiát. A portál részletes ismertetését [lásd a tanfolyamot bemutató leckében](/learn/modules/tour-azure-portal/3-navigate-the-portal).

A Azure Portal menü és az oldal fejléce olyan globális elemek, amelyek mindig jelen vannak. Ezek az állandó funkciók az egyes szolgáltatásokhoz vagy szolgáltatásokhoz társított felhasználói felület "rendszerhéja", a fejléc pedig hozzáférést biztosít a globális vezérlőkhöz. Az erőforrásokhoz tartozó konfigurációs lap (más néven "panel") is rendelkezhet erőforrás-menüvel, amely segítséget nyújt a szolgáltatások közötti váltáshoz.

Az alábbi ábra a Azure Portal alapvető elemeit címkézi, amelyek mindegyike az alábbi táblázatban látható.

![A teljes képernyős portál nézetét és a felhasználói felület elemeinek kulcsát bemutató képernyőfelvétel](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![A bővített portál menüjét bemutató képernyőkép](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Kulcs|Leírás
|:---:|---|
|1|Oldal fejléce Megjelenik az egyes portálok oldal tetején, és globális elemeket tart fenn.|
|2| Globális keresés. A keresősáv használatával gyorsan megtalálhatja az adott erőforrást, szolgáltatást vagy dokumentációt.|
|3|Globális vezérlők. Az összes globális elemhez hasonlóan ezek a funkciók is megmaradnak a portálon, így többek között a következők: Cloud Shell, előfizetési szűrő, értesítések, portál beállításai, Súgó és támogatás, valamint visszajelzés küldése.|
|4|Az Ön fiókja. Megtekintheti a fiókjával kapcsolatos információkat, átválthatja a címtárakat, kijelentkezhet vagy bejelentkezhet egy másik fiókkal.|
|5|Portál menü. A portál menü egy globális elem, amely segít a szolgáltatások közötti váltásban. Más néven az oldalsáv, a portál menü mód módosítható a **portál beállításaiban**.|
|6|Erőforrás menü. Számos szolgáltatás tartalmaz egy erőforrás menüt, amely segít a szolgáltatás kezelésében. Ezt az elemet a bal oldali ablaktábla néven láthatja.|
|7|Parancssáv. A parancssáv vezérlői az aktuális fókusz kontextusában jelennek meg.|
|8|Munkaablakban.  A jelenleg fókuszban lévő erőforrás részleteit jeleníti meg.|
|9|Morzsa. A navigációs hivatkozások segítségével egy szint visszahelyezhető a munkafolyamatba.|
|10|A fő vezérlővel új erőforrás hozható létre a jelenlegi előfizetésben. Bontsa ki vagy nyissa meg a portál menüt a **+ erőforrás létrehozásához**. Keresse meg vagy tallózással keresse meg az Azure piactéren a létrehozni kívánt erőforrás típusát.|
|11|A Kedvencek listája. Tekintse meg a [Kedvencek hozzáadásával, eltávolításával és rendezésével](../azure-portal/azure-portal-add-remove-sort-favorites.md) kapcsolatos információkat a lista testreszabásával kapcsolatban.|

## <a name="get-started-with-services"></a>Ismerkedés a szolgáltatásokkal

Ha Ön új előfizető, létre kell hoznia egy erőforrást, mielőtt bármit kezelnie kellene. Válassza az **+ erőforrás létrehozása** lehetőséget az Azure piactéren elérhető szolgáltatások megtekintéséhez. Itt több száz alkalmazás és szolgáltatás közül választhat, amelyek mindegyike az Azure-ban fut.

A gyakran használt szolgáltatásokra mutató hivatkozásokkal előre kitöltöttük kedvenceit az oldalsávon.  Az összes elérhető szolgáltatás megtekintéséhez válassza a **minden szolgáltatás** lehetőséget az oldalsávon.

> [!TIP]
> Az erőforrás, szolgáltatás vagy dokumentáció keresésének leggyorsabb módja a *Keresés* használata a globális fejlécben. A navigációs hivatkozások használatával visszatérhet az előző lapokra.
>
Tekintse meg ezt a videót, amely bemutatja, hogyan használható a globális keresés a Azure Portalban.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[A globális keresés használata a Azure Portalban](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Következő lépések

* További információ a Azure Portal futtatásáról a [támogatott böngészőkben és eszközökön](../azure-portal/azure-portal-supported-browsers-devices.md)
* Útközben is kapcsolódhat [Azure Mobile App](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* Felhőalapú környezet beléptetése és beállítása az [Azure Gyorsindítás központtal](../azure-portal/azure-portal-quickstart-center.md)