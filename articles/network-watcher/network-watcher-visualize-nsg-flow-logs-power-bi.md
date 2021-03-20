---
title: Azure NSG flow-naplók megjelenítése – Power BI
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogyan jelenítheti meg Power BI a hálózati biztonsági csoport folyamatábráinak megjelenítéséhez, hogy megtekintse az Azure Network Watcher IP-forgalmával kapcsolatos információkat.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 50f08ffc304962cc66f6eed07adcb5b7e4aa6692
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98011152"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Hálózati biztonsági csoport folyamatábráinak megjelenítése Power BI

A hálózati biztonsági csoport folyamatábrái lehetővé teszik a hálózati biztonsági csoportokon található bejövő és kimenő IP-forgalomra vonatkozó információk megtekintését. Ezek a flow-naplók a kimenő és bejövő folyamatokat jelenítik meg egy szabály alapján, a flow a folyamatra vonatkozik, 5 rekordos információ a folyamatról (forrás/cél IP-címe, forrás/célport, protokoll), és ha a forgalmat engedélyezték vagy megtagadták.

A naplófájlok manuális keresésével nehéz lehet betekintést nyerni a flow naplózási adataiba. Ebben a cikkben egy megoldást biztosítunk a legutóbbi flow-naplók megjelenítésére és a hálózat forgalmának megismerésére.

> [!Warning]  
> Az alábbi lépések a flow-naplók 1-es verziójával működnek. Részletekért lásd: a [hálózati biztonsági csoportok flow-naplózásának bemutatása](network-watcher-nsg-flow-logging-overview.md). A következő utasítások nem fognak működni a naplófájlok 2-es verziójával, módosítás nélkül.

## <a name="scenario"></a>Eset

Az alábbi forgatókönyvben a NSG-flow naplózási adataihoz tartozó fogadóként konfigurált Storage-fiókhoz csatlakozik Power BI asztal. A Storage-fiókhoz való kapcsolódás után Power BI letölti és elemzi a naplókat a hálózati biztonsági csoportok által naplózott forgalom vizuális megjelenítéséhez.

A sablonban megadott vizualizációk használatával ellenőrizheti a következőket:

* Leggyakoribb beszélők
* Az idősorozat folyamatábrája irány és szabály alapján
* Folyamatok hálózati adapter MAC-címe szerint
* Folyamatok NSG és szabály szerint
* Folyamatok célport szerint

A megadott sablon szerkeszthető, így az igényeinek megfelelően módosíthatja az új adatelemek, vizualizációk vagy szerkesztési lekérdezések hozzáadását.

## <a name="setup"></a>Beállítás

Mielőtt elkezdené, a fiókjában egy vagy több hálózati biztonsági csoporton engedélyezni kell a hálózati biztonsági csoport folyamatának naplózását. A hálózati biztonsági folyamatok naplófájljainak engedélyezésével kapcsolatos utasításokért tekintse meg a következő cikket: a [hálózati biztonsági csoportok flow-naplózásának bemutatása](network-watcher-nsg-flow-logging-overview.md).

Az Power BI Desktop-ügyfélnek telepítve kell lennie a gépen, és elegendő szabad területtel kell rendelkeznie a gépen a Storage-fiókban található naplófájlok letöltéséhez és betöltéséhez.

![Visio-diagram][1]

### <a name="steps"></a>Lépések

1. Töltse le és nyissa meg a következő Power BI sablont a Power BI Desktop alkalmazásban [Network Watcher PowerBI flow-naplók sablonja](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Adja meg a szükséges lekérdezési paramétereket
   1. **StorageAccountName** – annak a Storage-fióknak a nevét adja meg, amely tartalmazza a betölteni és megjeleníteni kívánt NSG flow-naplókat.
   1. **NumberOfLogFiles** – megadja, hogy hány naplófájlt szeretne letölteni és megjeleníteni Power BIban. Ha például 50 van megadva, a 50 legújabb naplófájlok. Ha 2 NSG engedélyezve van, és úgy van konfigurálva, hogy NSG-flow-naplókat küldjön erre a fiókra, akkor az elmúlt 25 órányi napló megtekinthető.

      ![Power BI – fő][2]

1. Adja meg a Storage-fiók elérési kulcsát. Az érvényes hozzáférési kulcsok megkereséséhez navigáljon a Storage-fiókjához a Azure Portal, és válassza a **hozzáférési kulcsok** lehetőséget a beállítások menüből. Kattintson a **kapcsolat** , majd a módosítások alkalmazása lehetőségre.

    ![hozzáférési kulcsok][3]

    ![2. hozzáférési kulcs][4]

4. A rendszer letölti és elemzi a naplókat, és mostantól használhatja az előre létrehozott vizualizációkat.

## <a name="understanding-the-visuals"></a>A vizualizációk megismerése

A sablonban megadott elemek olyan vizualizációk összessége, amelyek segítenek megérteni a NSG folyamat naplójának információit. Az alábbi képek egy mintát mutatnak arról, hogy az irányítópult hogyan néz ki az adatokkal való feltöltésekor. Alább részletesebben vizsgáljuk meg az egyes vizualizációkat 

![powerbi][5]
 
A legfontosabb beszélők vizualizációja azokat az IP-címeket mutatja be, amelyek a legtöbb kapcsolatot kezdeményezték a megadott időszakban. A mezők mérete megfelel a kapcsolatok relatív számának. 

![toptalkers][6]

A következő idősorozat-diagramok az időszakon belül a folyamatok számát mutatják. A felső diagramot a folyamat iránya szegmentálja, és az alacsonyabb érték a döntés által meghozott (Engedélyezés vagy megtagadás). Ezzel a vizualizációval az idő múlásával megvizsgálhatja a forgalmi trendeket, és megfigyelheti a forgalom vagy a forgalom szegmentálását.

![flowsoverperiod][7]

A következő diagramok a folyamatokat a hálózati felületen mutatják be, a felső szegmenst a flow irányával és a meghozott döntés alapján. Ezekkel az információkkal betekintést nyerhet, hogy a virtuális gépek közül melyik a legtöbbet, másokhoz képest, és ha egy adott virtuális gépre irányuló forgalom engedélyezése vagy elutasítása folyamatban van.

![flowspernic][8]

A következő fánk-kerék diagram a folyamatok lebontását mutatja meg a célport alapján. Ezekkel az információkkal megtekintheti a megadott időszakon belül leggyakrabban használt rendeltetési portokat.

![gyűrű][9]

A következő sávdiagram a folyamatot a NSG és a Rule alapján mutatja. Ezen információk alapján megtekintheti a legtöbb forgalomért felelős NSG, valamint a NSG szabály szerinti bontását.

![barchart][10]
 
A következő tájékoztató diagramok információkat jelenítenek meg a naplókban található NSG, az adott időszakban rögzített folyamatok számáról és a legkorábbi napló rögzítésének dátumáról. Ez az információ a naplózott NSG és a folyamatok időtartományát ismerteti.

![infochart1][11]

![infochart2][12]

Ez a sablon a következő szeletelőket tartalmazza, hogy csak azokat az adattartalmakat tekintse meg, amelyek érdeklik. Az erőforráscsoportok, a NSG és a szabályok szűrésére is lehetőség van. A szűrés 5 rekordos információ, döntés és a napló írásának időpontja alapján is végezhető.

![Szeletelők][13]

## <a name="conclusion"></a>Összegzés

Ebben a forgatókönyvben azt mutatták, hogy Network Watcher és Power BI által biztosított hálózati biztonsági csoport Folyamatábráinak használatával képesek vagyunk megjeleníteni és értelmezni a forgalmat. A megadott sablon használatával Power BI letölti a naplókat közvetlenül a tárolóból, és helyileg dolgozza fel őket. A sablon betöltéséhez szükséges idő a kért fájlok számától és a letöltött fájlok teljes méretétől függően változhat.

Nyugodtan testreszabhatja ezt a sablont az igényeinek megfelelően. Számos módon használhatja a Power BIt a hálózati biztonsági csoport adatfolyam-naplóival. 

## <a name="notes"></a>Jegyzetek

* A rendszer alapértelmezés szerint a naplókat tárolja `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Ha más címtárban található egyéb adatkönyvtár is, a lekérdezéseket a lekéréses és az adatfeldolgozási folyamatnak módosítania kell.

* A megadott sablon több mint 1 GB-nál több napló használata esetén nem ajánlott.

* Ha nagy mennyiségű naplót használ, javasoljuk, hogy egy másik adattárral (például Data Lake vagy SQL Server) vizsgálja meg a megoldást.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan jelenítheti meg a NSG-flow-naplókat a rugalmas Veremtel az [Azure Network WATCHER NSG flow-naplók vizualizációs megnyitása nyílt forráskódú eszközök használatával](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
