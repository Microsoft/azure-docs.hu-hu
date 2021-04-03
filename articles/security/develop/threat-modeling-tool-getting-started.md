---
title: Első lépések – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Megtudhatja, hogyan kezdheti el a Threat Modeling Tool használatát. Hozzon létre egy diagramot, azonosítsa a fenyegetéseket, enyhítse a fenyegetéseket, és ellenőrizze az egyes csökkentéseket.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 322f5f6a79bdce23706b2211ccc04ef2451675d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94515727"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>A Threat Modeling Tool első lépései

A Microsoft Threat Modeling Tool 2018-2018 as verziójának kiadása ingyenes **[kattintással letölthető](https://aka.ms/threatmodelingtool)**. A kézbesítési mechanizmus változása lehetővé teszi számunkra, hogy a legújabb javításokat és hibajavításokat minden alkalommal leküldje az ügyfeleknek, amikor megnyitják az eszközt, így egyszerűbbé válik a karbantartás és a használat.
Ebből a cikkből megtudhatja, hogyan hozhatja létre a Microsoft SDL Threat modellezési módszerét, és bemutatja, hogyan fejlesztheti az eszközt a biztonsági folyamat gerincét képező nagy veszélyforrású modellek fejlesztéséhez.

Ez a cikk az SDL-veszélyforrások modellezési megközelítésének meglévő ismeretét építi fel. A gyors áttekintéshez tekintse meg a **[veszélyforrások modellezése webalkalmazások](/previous-versions/msp-n-p/ff648006(v=pandp.10))** és a **[biztonsági hiányosságok](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** archivált változatát az 2006-ben közzétett lépéseket ismertető MSDN-cikkben.

A gyors összefoglalás érdekében a megközelítés magában foglalja egy diagram létrehozását, a fenyegetések azonosítását, a mérséklést és az egyes enyhítések érvényesítését. Itt látható egy diagram, amely kiemeli ezt a folyamatot:

![SDL-folyamat](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>A veszélyforrások modellezési folyamatának elindítása

A Threat Modeling Tool indításakor néhány dolgot láthat, ahogy az ábrán látható:

![Üres Kezdőlap](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Veszélyforrások modelljének szakasza

| Összetevő                                   | Részletek                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Visszajelzés, javaslatok és problémák gomb** | Az MSDN- **[fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** minden, az SDL-hez készült dologra felhasználhatja. Lehetőséget nyújt arra, hogy áttekintse a többi felhasználót, a megkerülő megoldásokkal és javaslatokkal együtt. Ha továbbra sem találja, amit keres, a támogatási csapatának e-mail-címe tmtextsupport@microsoft.com segítséget nyújt Önnek                                                                                                                            |
| **Modell létrehozása**                          | Egy üres vászon megnyitása a diagram rajzolásához. Győződjön meg arról, hogy ki szeretné választani a modellhez használni kívánt sablont                                                                                                                                                                                                                                                                                                                                                                       |
| **Sablon az új modellekhez**                 | A modell létrehozása előtt ki kell választania a használni kívánt sablont. A fő sablon az Azure Threat Model-sablon, amely az Azure-specifikus rajzsablonokat, fenyegetéseket és enyhítéseket tartalmazza. Általános modellek esetében válassza ki az SDL TM tudásbázist a legördülő menüből. Szeretne saját sablont létrehozni, vagy újat beküldeni az összes felhasználó számára? További információért tekintse meg a **[template adattár](https://github.com/Microsoft/threat-modeling-templates)** GitHub-lapját                              |
| **Modell megnyitása**                            | <p>A korábban mentett fenyegetések modelljeinek megnyitása. A legutóbb megnyitott modellek szolgáltatás nagyszerű megoldás, ha meg kell nyitnia a legújabb fájlokat. Ha a kijelölés fölé viszi a mutatót, két módszert talál a modellek megnyitására:</p><p><ul><li>Megnyitás a számítógépről – klasszikus mód egy fájl megnyitására helyi tároló használatával</li><li>Megnyitás a OneDrive – a csapatok a OneDrive lévő mappák használatával menthetik és megoszthatják az összes veszélyforrást tartalmazó modelljeiket egyetlen helyen a hatékonyság és az együttműködés növelése érdekében</li></ul></p> |
| **Első lépések útmutató**                   | A **[Microsoft Threat Modeling Tool](threat-modeling-tool.md)** főoldalának megnyitása                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Sablon szakasz

| Összetevő               | Részletek                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Új sablon létrehozása** | Egy üres sablont nyit meg, amelybe felépíthető. Ha nem rendelkezik széles körű ismeretekkel a sablonok előzmények nélküli létrehozásához, azt javasoljuk, hogy hozzon létre meglévőket |
| **Sablon megnyitása**       | Meglévő sablonokat nyit meg, amelyekkel módosításokat végezhet                                                                                                              |

Az Threat Modeling Tool csapat folyamatosan dolgozik az eszközök funkcióinak és működésének javításán. Előfordulhat, hogy az év folyamán néhány kisebb módosítás is megtörténik, de az összes jelentős módosításhoz szükség van az útmutatóban szereplő újraírásokra. A legújabb bejelentések beszerzésének biztosításához gyakran tekintse át.

## <a name="building-a-model"></a>Modell létrehozása

Ebben a szakaszban a következőket követjük:

- Cristina (a fejlesztő)
- Ricardo (a program vezetője) és
- Makadi (a Tester)

Folyamatban van az első veszélyforrások modelljének fejlesztése.

> Ricardo: Szia Cristina, dolgoztam a Threat Model diagramon, és azt is szeretném, hogy megbizonyosodjon róla, hogy megkapta a részleteket. Hasznosnak tekintem?
> Cristina: abszolút. Vizsgáljuk ezt meg.
> Ricardo megnyitja az eszközt, és megosztja a képernyőt Cristina-val.

![Alapszintű veszélyforrások modellje](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: ok, egyszerűnek tűnik, de megmutatom?
> Ricardo: biztos! Itt látható a részletezés:
> - Az emberi felhasználó külső entitásként van kirajzolva – egy négyzet
> - Parancsokat küldenek a webkiszolgálónak – a kört
> - A webkiszolgáló egy adatbázishoz nyújt tanácsadást (két párhuzamos vonal)

Azt mutatta, hogy Ricardo egy DFD, röviden az **[adatáramlási diagramhoz](https://en.wikipedia.org/wiki/Data_flow_diagram)**. A Threat Modeling Tool lehetővé teszi a felhasználók számára, hogy megadják a megbízhatósági határokat, amelyeket a piros pontozott vonalak jeleznek, hogy a különböző entitások hol vannak a vezérlésben A rendszergazdák például Active Directory rendszert igényelnek hitelesítési célokra, így a Active Directory kívül esnek a vezérlőn.

> Cristina: megkeresi a jogot. Mi a helyzet a fenyegetésekkel?
> Ricardo: megmutatom Önnek.

## <a name="analyzing-threats"></a>Fenyegetések elemzése

Miután rákattintott az elemzés nézetre az ikon kiválasztásakor (a nagyítóval rendelkező fájl), Threat Modeling Tool a rendszer a generált fenyegetések listáját használja az alapértelmezett sablon alapján, amely a lépéshossz (a hamisítás, a hamisítás, **[az adatok nyilvánosságra hozatala, a Megtagadás, a szolgáltatásmegtagadás és a jogosultsági szint emelése)](https://en.wikipedia.org/wiki/STRIDE_(security))** nevű SDL-módszert használja. Az elképzelés az, hogy a szoftver a fenyegetések előre jelezhető halmaza alá tartozik, amely a következő 6 kategóriával érhető el.

Ez a megközelítés olyan, mint a ház biztonságossá tétele, amely biztosítja, hogy az egyes ajtók és ablakok a riasztási rendszer hozzáadása előtt vagy a tolvaj után üldözve legyenek.

![Alapszintű fenyegetések](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo a lista első elemének kiválasztásával kezdődik. A következők történnek:

Először is a két rajzsablon közötti interakció van kiemelve

![A képernyőfelvételen két rajzsablon látható, és a görbe nyíl a sorban lévő nagyobb tömeghez csatlakozik.](./media/threat-modeling-tool-getting-started/interaction.png)

Másodszor, a fenyegetéssel kapcsolatos további információk jelennek meg a fenyegetés Tulajdonságok ablak

![A képernyőképen látható a fenyegetés Tulajdonságok ablak, amely tartalmazza a címet, a kategóriát, a leírást, az interakciót és a prioritást.](./media/threat-modeling-tool-getting-started/interactioninfo.png)

A generált fenyegetés segít megérteni a lehetséges tervezési hibákat. A LÉPÉSHOSSZ kategorizálása a potenciális támadási vektorokra mutat, míg a további leírások pontosan azt jelzik, hogy mi a probléma, valamint a lehetséges megoldási módszerekkel. A szerkeszthető mezők használatával megjegyzéseket írhat az indoklás részleteiben, vagy megváltoztathatja a prioritási minősítéseket a szervezete programhiba-sávjától függően.

Az Azure-sablonok további információkkal szolgálnak, amelyek segítenek a felhasználóknak a hibák megismerésében, de azt is, hogyan javíthatja a leírások, példák és az Azure-specifikus dokumentációra mutató hivatkozások hozzáadásával.

A Leírás rámutatott a hitelesítési mechanizmus hozzáadásának fontosságára, hogy megakadályozza a felhasználók hamisítását, feltárva az első fenyegetést. Az Cristina-val folytatott megbeszélések néhány perc alatt megértették a hozzáférés-vezérlés és a szerepkörök megvalósításának fontosságát. Ricardo kitöltött néhány gyors megjegyzést, hogy megbizonyosodjon róla, hogy implementálták.

Ahogy Ricardo bement a fenyegetésekbe az információ-közzététel alatt, felismerte, hogy a hozzáférés-vezérlési terv néhány írásvédett fiókot igényelt a naplózáshoz és a jelentéskészítéshez. Megértette, hogy ez egy új fenyegetés-e, de a mérséklések ugyanazok voltak, ezért ennek megfelelően felismerte a fenyegetést.
Azt is gondolta, hogy az információk közzététele valamivel több, és rájött, hogy a biztonsági mentési szalagoknak titkosításra, az operatív csapatra vonatkozó feladatra lesz szükségük.

A meglévő enyhítések vagy biztonsági garanciák miatt a tervezésre nem alkalmazható fenyegetések az állapot legördülő menüből "nem alkalmazható" értékre módosíthatók. Három további lehetőség közül választhat: nincs elindítva – az alapértelmezett beállítás, a vizsgálathoz szükséges – az elemek nyomon követéséhez és a megoldáshoz, ha teljes mértékben működött.

## <a name="reports--sharing"></a>Jelentések & megosztás

Miután Ricardo megkezdi a listát Cristina-val, fontos megjegyzéseket, enyhítő és indoklást, prioritást és állapot-változásokat tesz elérhetővé, kiválasztja a jelentések-> teljes jelentés készítése – > mentési jelentést, amely kinyomtat egy szép jelentést, amely lehetővé teszi a munkatársainak a megfelelő biztonsági feladatok megvalósítását.

![A képernyőképen egy reprezentatív veszélyforrás modellezési jelentés látható.](./media/threat-modeling-tool-feature-overview/report.png)

Ha Ricardo Ehelyett a fájlt szeretné megosztani, egyszerűen megteheti a szervezete OneDrive-fiókjában való mentéssel. Ha ezt elvégezte, átmásolhatja a dokumentum hivatkozását, és megoszthatja azt a kollégáival. 

## <a name="threat-modeling-meetings"></a>Veszélyforrások modellezési ülései

Amikor Ricardo elutasította a fenyegetés modelljét a munkatársának a OneDrive, a underwhelmed-t használva, a Tester volt. Úgy tűnik, hogy Ricardo és Cristina is kihagyott néhány fontos sarki esetet, ami könnyen feltört. A szkepticizmus a fenyegetések modelljeinek kiegészítéseként szolgál.

Ebben a forgatókönyvben, miután a (z) a (z) a Threat modelt vette át, a következő két veszélyforrás-modellezési értekezletet hívta el: az egyik értekezlet szinkronizálja a folyamatot, és végigvezeti a diagramokon, majd egy második értekezletet a fenyegetések áttekintéséhez

Az első találkozón az SDL fenyegetések modellezési folyamata 10 percet töltött. Ezután felhúzta a Threat Model diagramot, és részletesen elmagyarázza a magyarázatot. Öt percen belül egy fontos hiányzó összetevőt azonosítottak.

Néhány perccel később, és a Ricardo és Ricardo a webkiszolgáló létrehozásával kapcsolatos kiterjesztett vitára került. Nem volt az ideális módszer a megbeszélés folytatására, de végül mindenki megállapodott abban, hogy a korai eltérés felfedése a jövőben időt takarít meg.

A második ülésen a csapat végigvezeti a fenyegetéseken, megvitatta a velük kapcsolatos néhány módszert, és kijelentkezett a veszélyforrások modelljére. Megvizsgálták a dokumentumot a forrás vezérlőelemben, és folytatták a fejlesztést.

## <a name="thinking-about-assets"></a>Az eszközök gondolkodása

Előfordulhat, hogy egyes, a fenyegetést modellező olvasók láthatják, hogy egyáltalán nem beszéltünk az eszközökről. Felderítettük, hogy számos szoftverfejlesztő mérnöke jobban érti a szoftvereket, mint az eszközök fogalmát, és azt, hogy milyen eszközöket érdekli a támadó.

Ha egy házat fog megfenyegetni, érdemes lehet a családja, a pótolhatatlan fényképek vagy az értékes alkotások alapján kezdenie. Lehet, hogy először is érdemes meggondolni, hogy ki és mikor lehet megszakítani a jelenlegi biztonsági rendszereket. Vagy megkezdheti a fizikai funkciók, például a készlet vagy a tornác bevezetését. Ezek hasonlóak az eszközök, a támadók és a szoftverek tervezéséhez. Ezen három megközelítés bármelyike működik.

Az itt bemutatott veszélyforrások modellezésének megközelítése lényegesen egyszerűbb, mint amit a Microsoft korábban végzett. Azt találtuk, hogy a szoftver kialakításának megközelítése sok csapat számára jól működik. Reméljük, hogy a tiéd is.

## <a name="next-steps"></a>Következő lépések

Küldje el kérdéseit, megjegyzéseit és problémáit tmtextsupport@microsoft.com . A kezdéshez **[töltse le](https://aka.ms/threatmodelingtool)** a Threat Modeling Tool.