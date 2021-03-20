---
title: Adatelemzési projektek gyors fejlesztése – csoportos adatelemzési folyamat
description: Az adatelemzési projektet a csoportos adatelemzési folyamat segítségével szisztematikus, vezérelt, és együttműködési módon hajthatja végre a projektcsapat keretében.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 113c11b749b42bbaf9e5e40cdf63623288997a92
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89440286"
---
# <a name="agile-development-of-data-science-projects"></a>Az adatelemzési projektek gyors fejlesztése

Ez a dokumentum azt ismerteti, hogyan futtathatják a fejlesztők az adatelemzési projekteket a [csoportos adatelemzési folyamat](overview.md) (TDSP) használatával, a projekt-csoportokon belüli, szisztematikus, vezérelt és együttműködési módon. A TDSP a Microsoft által kifejlesztett keretrendszer, amely strukturált tevékenységeket biztosít a felhőalapú, prediktív elemzési megoldások hatékony végrehajtásához. Az adatelemzési csapat által a TDSP szabványosított szerepkörök és feladatok áttekintését lásd: [csoportos adatelemzési folyamat szerepkörei és feladatai](roles-tasks.md). 

Ez a cikk útmutatást nyújt a következőkhöz: 

- A *Sprint* a projektben résztvevő munkaelemeket tervezi.
- *Munkaelemek* hozzáadása a sprinthez.
- Hozzon létre és használjon egy *agilisan származtatott munkaelem-sablont* , amely kifejezetten igazodik a TDSP életciklusának szakaszaihoz.

Az alábbi útmutató ismerteti a TDSP-munkakörnyezetek Azure-táblákkal és Azure-beli DevOps történő beállításához szükséges lépéseket. Az utasítások az Azure DevOps-t használják, mivel ez az útmutató a TDSP megvalósításához a Microsoftnál. Ha a csoport egy másik kód-üzemeltetési platformot használ, a csapat vezető feladatai általában nem változnak, de a feladatok elvégzésének módja eltér. Előfordulhat például, hogy a git-elágazáshoz tartozó munkaelemek nem egyeznek meg a GitHubtal, mert az az Azure Repos.

Az alábbi ábra egy adatelemzési projekt tipikus Sprint-tervezését, kódolását és forrás-ellenőrzési munkafolyamatát szemlélteti:

![Csoportos adatelemzési folyamat](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Munkaelemek típusai

A TDSP Sprint tervezési keretrendszere négy gyakran használt *munkaelem* -típust *tartalmaz: funkciók*, *felhasználói történetek*, *feladatok* és *hibák*. Az összes munkaelem várakozó értéke a projekt szintjén van, nem a git-tárház szintjén. 

Itt láthatók a munkaelem-típusok definíciói:

- **Funkció**: a szolgáltatás a projekt bevonásának felel meg. Az ügyféllel különböző funkciók különböznek egymástól, és érdemes a projekt különböző fázisait különböző Funkciókként figyelembe venni. Ha olyan sémát választ, mint például a *\<ClientName>-\<EngagementName>* szolgáltatások elnevezése, egyszerűen felismerheti a projekt kontextusát, és maguktól a nevekből is elvégezheti a kapcsolódást.
  
- **Felhasználói történet**: a felhasználói történetek olyan munkaelemek, amelyek a funkciók végpontok közötti befejezéséhez szükségesek. A felhasználói történetek például a következők:
  - Adatok lekérése 
  - Adatok feltárása 
  - Szolgáltatások előállítása
  - Modellek létrehozása
  - Modellek üzembe helyezése 
  - Modellek újratanítása
  
- **Feladat**: a feladatok olyan hozzárendelhető munkaelemek, amelyeket egy adott felhasználói történet végrehajtásához kell elvégezni. Például a felhasználói történetnek az *adatok lekérése* :
  - SQL Server hitelesítő adatok beolvasása
  - Adatok feltöltése az Azure szinapszis Analyticsbe
  
- **Hiba**: a hibák olyan meglévő kódok vagy dokumentumok hibái, amelyeket a feladatok elvégzéséhez javítani kell. Ha a hibákat a hiányzó munkaelemek okozzák, a felhasználók megadhatják, hogy felhasználói történetek vagy feladatok legyenek. 

Az adatszakértők sokkal kényelmesebben érezhetik magukat egy agilis sablonnal, amely felváltja a funkciók, a felhasználói történetek és a TDSP életciklus-szakaszait és alszakaszait használó feladatokat. A TDSP életciklus szakaszait kifejezetten összehangoló, agilisan származtatott sablon létrehozásával kapcsolatban lásd: [agilis TDSP munkahelyi sablon használata](#set-up-agile-dsp-6).

> [!NOTE]
> A TDSP a funkciók, a felhasználói történetek, a feladatok és a hibák fogalmait a szoftverhasználat-kezelés (SCM) alapján kölcsönözi. A TDSP fogalmak kis mértékben eltérhetnek a hagyományos SCM-definíciók.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>A Sprint tervezése

Számos adattudós több projekthez is felhasználható, amely hónapokig eltarthat, és különböző ütemekben végezhető el. A Sprint tervezése hasznos a projektek rangsorolásához, valamint az erőforrások tervezéséhez és kiosztásához. Az Azure-táblákban egyszerűen létrehozhatja, kezelheti és nyomon követheti a projektek munkaelemeit, és a Sprint tervezésével biztosíthatja, hogy a projektek a várt módon haladnak előre.

A Sprint tervezésével kapcsolatos további információkért lásd: [dulakodás Sprint](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint). 

Az Azure-táblák Sprint-tervezésével kapcsolatos további információkért lásd: [várakozó elemek társítása egy sprinthez](/azure/devops/boards/sprints/assign-work-sprint). 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Szolgáltatás hozzáadása a várakozó fájlok számára 

A projekt és a projekt kódjának tárházának létrehozása után hozzáadhat egy funkciót a várakozó fájlok számára a projekt munkájának ábrázolásához.

1. A projekt lapon válassza   >   a bal oldali navigációs sávon a táblákat. 
   
1. A **várakozó** fájlok lapon, ha a felső sávban a munkaelem típusa a **történetek**, a legördülő menüben válassza a **funkciók** lehetőséget. Ezután válassza az **új munkaelem lehetőséget.**
   
   ![Új munkaelem kiválasztása](./media/agile-development/2-sprint-team-overview.png)
   
1. Adja meg a szolgáltatás címét, általában a projekt nevét, majd válassza a **Hozzáadás a tetejére** lehetőséget. 
   
   ![Adjon meg egy címet, és válassza a Hozzáadás a tetejére lehetőséget](./media/agile-development/3-sprint-team-add-work.png)
   
1. A **várakozó** fájlok listából válassza ki és nyissa meg az új szolgáltatást. Adja meg a leírást, rendeljen hozzá egy csapattagot, és adja meg a tervezési paramétereket. 
   
   A funkció a projekt Azure Repos-kódjának tárházához is összekapcsolható a **fejlesztési** szakasz **hivatkozás hozzáadása** lehetőségével. 
   
   A szolgáltatás szerkesztése után válassza a **mentés & Bezárás** lehetőséget.
   
   ![Szerkessze a funkciót, és válassza a Mentés & Bezárás lehetőséget.](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Felhasználói történet hozzáadása a szolgáltatáshoz 

A szolgáltatás alatt felhasználói történeteket adhat hozzá a projekt végrehajtásához szükséges fő lépések leírásához. 

Új felhasználói történet hozzáadása egy szolgáltatáshoz:

1. A **várakozó** fájlok lapon válassza a **+** szolgáltatástól balra található elemet. 
   
   ![Új felhasználói történet hozzáadása a funkció alatt](./media/agile-development/4-sprint-add-story.png)
   
1. Adja meg a felhasználó számára a címet, és szerkessze a részleteket, például a hozzárendelést, az állapotot, a leírást, a megjegyzéseket, a tervezést és a prioritást. 
   
   A felhasználói történetet a projekt Azure Repos Code repositoryjának egy ágában is összekapcsolhatja, ha a **fejlesztés** szakaszban a **hivatkozás hozzáadása** lehetőségre kattint. Válassza ki azt a tárházat és ágat, amelyhez a munkaelemet csatolni szeretné, majd kattintson **az OK gombra**.
   
   ![Hivatkozás hozzáadása](./media/agile-development/5-sprint-edit-story.png)
   
1. Ha végzett a felhasználói történet szerkesztésével, válassza a **mentés & Bezárás** lehetőséget. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Feladat hozzáadása egy felhasználói történethez 

A feladatok az egyes felhasználói történetek végrehajtásához szükséges részletes lépések. Egy felhasználói történet összes feladatának befejezése után a felhasználói történetet is el kell végezni. 

Ha feladatot szeretne felvenni egy felhasználói szövegegységbe, jelölje be a **+** felhasználói szövegegység elem melletti gombot, és válassza a **feladat** elemet. Adja meg a címet és az egyéb információkat a feladatban.

![Feladat hozzáadása egy felhasználói történethez](./media/agile-development/7-sprint-add-task.png)

A funkciók, a felhasználói történetek és a feladatok létrehozása után megtekintheti őket a **várakozó** fájlok vagy a **táblák** nézeteiben az állapotuk nyomon követéséhez.

![Várakozó fájlok nézete](./media/agile-development/8-sprint-backlog-view.png)

![Táblák nézet](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Agilis TDSP munkahelyi sablon használata

Az adatszakértők sokkal kényelmesebben érezhetik magukat egy agilis sablonnal, amely felváltja a funkciók, a felhasználói történetek és a TDSP életciklus-szakaszait és alszakaszait használó feladatokat. Az Azure-táblákban létrehozhat egy agilis származtatott sablont, amely TDSP életciklus-fázisokat használ a munkaelemek létrehozásához és nyomon követéséhez. Az alábbi lépések végigvezetik az adatelemzési specifikus agilis folyamat sablonjának beállításán, valamint az adatelemzési munkaelemeknek a sablon alapján történő létrehozásán.

### <a name="set-up-an-agile-data-science-process-template"></a>Agilis adatelemzési folyamat sablonjának beállítása

1. Az Azure DevOps-szervezet főoldalán válassza a **szervezeti beállítások** lehetőséget a bal oldali navigációs sávon. 
   
1. A **szervezeti beállítások** bal oldali navigációs sávján **válassza a** **folyamat** elemet. 
   
1. A **minden folyamat** ablaktáblán válassza a **...** elemet az **agilis** elem mellett, majd válassza az **örökölt folyamat létrehozása** lehetőséget.
   
   ![Örökölt folyamat létrehozása az agilis rendszerből](./media/agile-development/10-settings.png) 
   
1. Az **örökölt folyamat létrehozása az agilis** párbeszédpanelen írja be a *AgileDataScienceProcess* nevet, majd válassza a **folyamat létrehozása** lehetőséget.
   
   ![AgileDataScienceProcess folyamat létrehozása](./media/agile-development/11-agileds.png)
   
1. Az **összes folyamat** területen válassza ki az új **AgileDataScienceProcess**. 
   
1. A **munkaelem-típusok** lapon tiltsa le az **Epic**, a **Feature**, a **User Story** és a **Task** elemet. ehhez kattintson a **...** elemre az egyes elemek mellett, majd válassza a **Letiltás** lehetőséget. 
   
   ![Munkaelem-típusok letiltása](./media/agile-development/12-disable.png)
   
1. A **minden folyamat** területen válassza a **várakozó szintek** lapot. A **portfóliók** csomópontok alatt válassza a **...** lehetőséget az **Epic (letiltva)** elem mellett, majd válassza a **Szerkesztés/Átnevezés** lehetőséget. 
   
1. A **várakozó fájlok szintjének szerkesztése** párbeszédpanelen:
   1. A **név** alatt cserélje le az **EPICT** *TDSP-projektekkel*. 
   1. A **várakozó munkaelemek** területen válassza az **új munkaelem típusa** lehetőséget, írja be a *TDSP projektet*, és válassza a **Hozzáadás** lehetőséget. 
   1. Az **alapértelmezett munkaelem típusa** területen válassza a legördülő listát, majd a **TDSP projektet**. 
   1. Kattintson a **Mentés** gombra.
   
   ![Portfólió-várakozó fájlok szintjének beállítása](./media/agile-development/13-rename.png)  
   
1. Kövesse ugyanezen lépéseket a **szolgáltatások** *TDSP szakaszokra* való átnevezéséhez, és adja hozzá a következő új munkaelem-típusokat:
   
   - *Üzleti ismeretek*
   - *Adatgyűjtés*
   - *Modellezés*
   - *Üzembe helyezés*
   
1. A **követelmény** maradása területen **nevezze át a** *TDSP alszakaszokat*, adja hozzá az új munkaelem típusú *TDSP alfázist*, és állítsa be az alapértelmezett munkaelemet a **TDSP alszakaszra**.
   
1. Az **Ismétlési** várakozási sor területen adjon hozzá egy új munkaelem típusú *TDSP-feladatot*, és állítsa be úgy, hogy az alapértelmezett munkaelem típusú legyen. 
   
A lépések elvégzése után a várakozó fájlok szintjének a következőképpen kell kinéznie:
   
 ![TDSP-sablon várakozó szintjei](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Gyors adatelemzési folyamat munkaelemeinek létrehozása

Az adatelemzési folyamat sablonja segítségével TDSP-projekteket hozhat létre, és nyomon követheti a TDSP életciklusának szakaszaihoz tartozó munkaelemeket.

1. Az Azure DevOps-szervezet főoldalán válassza az **új projekt** lehetőséget. 
   
1. Az **új projekt létrehozása** párbeszédpanelen adja meg a projekt nevét, majd válassza a **speciális** lehetőséget. 
   
1. A **munkaelem folyamata** alatt válassza a legördülő menü **AgileDataScienceProcess** elemét, majd válassza a **Létrehozás** lehetőséget.
   
   ![TDSP-projekt létrehozása](./media/agile-development/15-newproject.png)
   
1. Az újonnan létrehozott projektben válassza   >   a bal oldali navigációs sávon a táblákat.
   
1. A TDSP-projektek láthatóvá tételéhez válassza a **csoport beállításainak konfigurálása** ikont. A **Beállítások** képernyőn jelölje be a **TDSP-projektek** jelölőnégyzetet, majd kattintson a **Mentés és bezárás** gombra.
   
   ![TDSP-projektek kijelölése jelölőnégyzet](./media/agile-development/16-enabledsprojects1.png)
   
1. Adatelemzésre vonatkozó TDSP projekt létrehozásához válassza a felső sávban a **TDSP projektek** elemet, majd válassza az **új munkaelem** lehetőséget. 
   
1. A felugró ablakban adja meg a TDSP projekt munkaelemének nevét, majd válassza a **Hozzáadás a tetejére** lehetőséget.
   
   ![Adatelemzési projekt munkaelemének létrehozása](./media/agile-development/17-dsworkitems0.png)
   
1. Ha munkaelemet szeretne felvenni a TDSP projekt alá, válassza ki a **+** projekt melletti elemet, majd válassza ki a létrehozandó munkaelem típusát. 
   
   ![Adatelemzési munkaelem típusának kiválasztása](./media/agile-development/17-dsworkitems1.png)
   
1. Adja meg a részleteket az új munkaelemben, majd válassza a **mentés & Bezárás** lehetőséget.
   
1. Folytassa a **+** munkaelemek melletti szimbólumok kijelölésével új TDSP szakaszok, alszakaszok és feladatok hozzáadásához. 
   
Íme egy példa arra, hogy az adatelemzési projekt munkaelemei megjelenjenek a **várakozó** fájlok nézetben:

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Következő lépések

A [git együttműködésen alapuló kódolása](collaborative-coding-with-git.md) leírja, hogyan lehet az adatelemzési projektek együttműködési programkódjának fejlesztését használni a git-t a közös programkód fejlesztési keretrendszereként, és hogyan kapcsolhatja össze ezeket a kódolási tevékenységeket az agilis folyamattal tervezett munkavégzéshez.

A [forgatókönyvek példái](walkthroughs.md) a hivatkozásokat és a miniatűr leírásait felsoroló forgatókönyvek. A hivatkozott forgatókönyvek bemutatják, hogyan kombinálhatja a Felhőbeli és a helyszíni eszközöket és szolgáltatásokat munkafolyamatokban vagy folyamatokban intelligens alkalmazások létrehozásához.
  
További források az agilis folyamatokról:

- [Agilis folyamat](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Agilis folyamat munkaelemek típusai és munkafolyamata](/azure/devops/boards/work-items/guidance/agile-process-workflow)

