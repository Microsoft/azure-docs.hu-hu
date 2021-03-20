---
title: A projekthez vezető feladatok a csoportos adatelemzési folyamat során
description: Egy projekt feladatainak részletes bemutatása egy csoportos adatelemzési folyamat munkacsoportján
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9d9b2546553e03d1555cf4c587d699d9a4ea7e51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000980"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Projekt-érdeklődői feladatok a csoportos adatelemzési folyamatban

Ez a cikk azokat a feladatokat ismerteti, amelyekkel a *projekt* elvégezhető, hogy a [csoportos adatelemzési folyamat](overview.md) (TDSP) adattárat állítson be a Project csapatának. A TDSP a Microsoft által kifejlesztett keretrendszer, amely strukturált tevékenységeket biztosít a felhőalapú, prediktív elemzési megoldások hatékony végrehajtásához. A TDSP célja az együttműködés és a csapatmunka fejlesztése. A munkatársak szerepköreinek és a TDSP szabványosított adatelemzési csapathoz kapcsolódó feladatoknak a körvonalazása: a [csoportos adatelemzési folyamat szerepkörei és feladatai](roles-tasks.md).

A projekt a TDSP egy adott adatelemzési projekten belül az egyes adatszakértők napi tevékenységeit kezeli. A következő ábrán a Project Lead-feladatok munkafolyamata látható:

![Projekt-érdeklődői feladat munkafolyamata](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Ez az oktatóanyag az 1. lépés: a Project-Tárház létrehozása és a 2. lépés: a csapat ProjectTemplate adattárból származó projekt-adattár bevezetését ismerteti. 

A 3. lépés: a szolgáltatás munkaelemének létrehozása a projekthez és a 4. lépés: a projekt szakaszaihoz tartozó történetek hozzáadása című témakörben talál további információt az [adatelemzési projektek agilis fejlesztéséről](agile-development.md).

5. lépés: a tárolási/elemzési eszközök és megosztások létrehozása és testreszabása, ha szükséges, tekintse meg a [Team-adatok és-elemzési erőforrások létrehozása](team-lead-tasks.md#create-team-data-and-analytics-resources)című témakört.

6. lépés: a Project repository biztonsági ellenőrzésének beállítása című témakörben talál további információt a [csapattagok hozzáadása és az engedélyek konfigurálása](team-lead-tasks.md#add-team-members-and-configure-permissions)című részben.

> [!NOTE] 
> Ez a cikk az Azure Repos használatával állít be egy TDSP-projektet, mert ez a Microsoft TDSP megvalósításának módja. Ha a csapata egy másik kód-üzemeltetési platformot használ, a projekt vezető feladatai ugyanazok, de a végrehajtás módja eltérő lehet.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy a csoportvezető és a [Team](team-lead-tasks.md) [Manager](group-manager-tasks.md) a következő erőforrásokat és engedélyeket állította be:

- Az adategységhez tartozó Azure DevOps- **szervezet**
- Az adatelemzési csapathoz tartozó csoportos **projekt**
- A Team template és a Utilities **tárházai**
- A szervezeti fiók **engedélyei** a projekt adattárainak létrehozásához és szerkesztéséhez

A Tárházak klónozásához és a tartalmak módosításához a helyi gépen vagy Data Science Virtual Machineon (DSVM), illetve az Azure file Storage beállítása és csatlakoztatása a DSVM-hez az alábbi ellenőrzőlista is szükséges:

- Azure-előfizetés.
- A git telepítve van a gépen. Ha DSVM használ, a git előre telepítve van. Egyéb esetben lásd: [platformok és eszközök függelék](platforms-and-tools.md#appendix).
- Ha DSVM szeretne használni, az Azure-ban létrehozott és konfigurált Windows-vagy Linux-DSVM. További információt és útmutatást a [Data Science Virtual Machine dokumentációjában](../data-science-virtual-machine/index.yml)talál.
- Windows DSVM esetén a [git Hitelesítőadat-kezelő (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) telepítve van a gépen. A *readme.MD* fájlban görgessen le a **letöltés és telepítés** szakaszban, és válassza ki a **legújabb telepítőt**. Töltse le az *. exe* -telepítőt a telepítő lapról, és futtassa. 
- Linux DSVM esetében egy nyilvános SSH-kulcs, amely be van állítva a DSVM, és hozzá lett adva az Azure DevOps. További információt és útmutatást az **SSH nyilvános kulcs létrehozása** című szakaszban talál a [platformok és eszközök függelékben](platforms-and-tools.md#appendix). 

## <a name="create-a-project-repository-in-your-team-project"></a>Projekt Tárház létrehozása a csapat projektjeiben

Projekt Tárház létrehozása a csapat **MyTeam** -projektben:

1. Nyissa meg a csapat projekt- **Összefoglalás** lapját *https \/ / \<server name> / \<organization name> / \<team name> :*, például **https: \/ /dev.Azure.com/DataScienceUnit/MyTeam**, és válassza a bal oldali navigációs menüben a **repók** lehetőséget. 
   
1. Válassza ki az adattár nevét az oldal tetején, majd válassza az **új tárház** lehetőséget a legördülő menüből.
   
   ![Új tárház kiválasztása](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. Az **új Tárház létrehozása** párbeszédpanelen győződjön meg róla, hogy a **git** elem van kiválasztva a **típus** területen. Adja meg a *DSProject1* a **tárház neve** területen, majd válassza a **Létrehozás** lehetőséget.
   
   ![Tárház létrehozása](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Győződjön meg arról, hogy az új **DSProject1** -tárház megjelenik a projekt beállításai lapon. 
   
   ![Projekt adattár a projekt beállításaiban](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>A csoport sablonjának importálása a projekt adattárba

A Project-tárház feltöltése a Team template-tárház tartalmával:

1. A csapat projekt- **Összefoglalás** lapján válassza a **repók** lehetőséget a bal oldali navigációs sávon. 
   
1. Válassza ki a tárház nevét az oldal tetején, majd válassza a **DSProject1** lehetőséget a legördülő listából.
   
1. A **DSProject1 üres** lapon válassza az **Importálás** lehetőséget. 
   
   ![Importálás kiválasztása](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. A **git-tárház importálása** párbeszédpanelen válassza a **git** lehetőséget a **forrás típusaként**, majd adja meg a **TeamTemplate** -tárház URL-címét a **klónozási URL-cím** alatt. Az URL-cím *https: \/ / \<server name> / \<organization name> / \<team name> /_git \<team template repository name> /*. Például: **https: \/ /dev.Azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**. 
   
1. Válassza az **Importálás** lehetőséget. A Team template-tárház tartalmát a rendszer importálja a projekt adattárba. 
   
   ![Csoport sablonjának importálása adattár](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Ha testre kell szabnia a projekt-tárház tartalmát, hogy az megfeleljen a projekt konkrét igényeinek, hozzáadhat, törölhet vagy módosíthat adattár-fájlokat és mappákat. Közvetlenül dolgozhat az Azure-adattárakban, vagy a tárházat a helyi gépre vagy DSVM, módosíthatja és véglegesítheti, és leküldheti a frissítéseket a megosztott projekt adattárba. Kövesse a Team- [Tárházak tartalmának testreszabása](team-lead-tasks.md#customize-the-contents-of-the-team-repositories)című témakör utasításait.

## <a name="next-steps"></a>Következő lépések

Az alábbiakban a csoportos adatelemzési folyamat által meghatározott egyéb szerepkörök és feladatok részletes ismertetésére talál hivatkozásokat:

- [Az adatelemzési csapat Group Manager-feladatai](group-manager-tasks.md)
- [A csapat vezető feladatai egy adattudományi csapat számára](team-lead-tasks.md)
- [Adatelemzési csapat Egyéni közreműködő feladatai](project-ic-tasks.md)