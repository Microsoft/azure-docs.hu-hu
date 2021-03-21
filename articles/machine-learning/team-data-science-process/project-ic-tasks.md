---
title: A csoportos adatelemzési folyamat egyes közreműködői által feldolgozható feladatok
description: Az adatelemzési csapat projektjein az egyes közreműködők feladatainak részletes áttekintése.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ecb5fef9c9b14bde72de29a45e29d7e16131bd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000997"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>A csoportos adatelemzési folyamat egyes közreműködői által feldolgozható feladatok

Ez a témakör azokat a feladatokat ismerteti, amelyeket az *egyes közreműködők* a [csoportos adatelemzési folyamat](overview.md) (TDSP) projekt beállításához végeznek. A cél a TDSP szabványosított együttműködési csoportmunka-környezet használata. A TDSP célja az együttműködés és a csapatmunka fejlesztése. A munkatársak szerepköreinek és az adatelemzési csapat által a TDSP szabványosított feladatainak a körvonalazása: a [csoportos adatelemzési folyamat szerepkörei és feladatai](roles-tasks.md).

A következő ábra azokat a feladatokat mutatja be, amelyekkel a projektben az egyes közreműködők (adatszakértők) készen állnak a csapat környezetének beállítására. Az adatelemzési projektek TDSP való végrehajtásával kapcsolatos útmutatásért tekintse meg az [adatelemzési projektek végrehajtását](./agile-development.md)ismertető témakört. 

![Egyéni közreműködők feladatai](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- A **ProjectRepository** az a tárház, amelyet a projektcsapat tart fenn a projekt sablonjainak és eszközeinek megosztásához.
- A **TeamUtilities** az a közmű-tárház, amelyet a csapata kifejezetten a csapatának tart fenn. 
- A **GroupUtilities** az a tárház, amelyet a csoport fenntart a hasznos segédprogramok teljes csoporton belüli megosztásához. 

> [!NOTE] 
> Ez a cikk az Azure Repos és egy Data Science Virtual Machine (DSVM) használatával állítja be a TDSP-környezetet, mivel ez a TDSP implementálása a Microsoftnál. Ha a csapata más kódot üzemeltető vagy fejlesztői platformot használ, az egyes közreműködő tevékenységek ugyanazok, de a végrehajtás módja eltérő lehet.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag azt feltételezi, hogy a [Group Manager](group-manager-tasks.md), a [Team Lead](team-lead-tasks.md)és a [Project Lead](project-lead-tasks.md)a következő erőforrásokat és engedélyeket állította be:

- Az adatelemzési egység Azure DevOps- **szervezete**
- Projekt- **adattár** , amelyet a projekt a projekt sablonjainak és eszközeinek megosztására állított be
- A Group Manager és a Team Lead által beállított **GroupUtilities** -és **TeamUtilities** -Tárházak, ha vannak ilyenek
- Az Azure **file Storage** a csoporthoz vagy projekthez tartozó megosztott eszközökhöz van beállítva, ha van ilyen
- **Engedélyek** a projekt-adattárba való klónozáshoz és azok visszaküldéséhez 

A Tárházak klónozása és a tartalom módosítása a helyi gépen vagy DSVM, vagy az Azure file Storage csatlakoztatása a DSVM a következő ellenőrzőlista szükséges:

- Azure-előfizetés.
- A git telepítve van a gépen. Ha DSVM használ, a git előre telepítve van. Egyéb esetben lásd: [platformok és eszközök függelék](platforms-and-tools.md#appendix).
- Ha DSVM szeretne használni, az Azure-ban létrehozott és konfigurált Windows-vagy Linux-DSVM. További információt és útmutatást a [Data Science Virtual Machine dokumentációjában](../data-science-virtual-machine/index.yml)talál.
- Windows DSVM esetén a [git Hitelesítőadat-kezelő (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) telepítve van a gépen. A *readme.MD* fájlban görgessen le a **letöltés és telepítés** szakaszban, és válassza ki a **legújabb telepítőt**. Töltse le az *. exe* -telepítőt a telepítő lapról, és futtassa. 
- Linux DSVM esetében egy nyilvános SSH-kulcs, amely be van állítva a DSVM, és hozzá lett adva az Azure DevOps. További információt és útmutatást az **SSH nyilvános kulcs létrehozása** című szakaszban talál a [platformok és eszközök függelékben](platforms-and-tools.md#appendix). 
- A DSVM csatlakoztatni kívánt Azure file Storage-adatok az Azure file Storage-hoz. 

## <a name="clone-repositories"></a>Klónozási tárak

Ha helyileg kívánja dolgozni a tárházait, és a változtatásokat a megosztott csapat és a projekt adattárházában küldi el, először másolja vagy *klónozottan* másolja a tárházat a helyi gépre. 

1. Az Azure DevOps nyissa meg a csapat projekt-összefoglalás oldalát *https: \/ / \<server name> / \<organization name> / \<team name>*, például **https: \/ /dev.Azure.com/DataScienceUnit/MyTeam**.
   
1. Válassza a **repók** lehetőséget a bal oldali navigációs menüben, majd az oldal tetején válassza ki a klónozott tárházat.
   
1. A tárház lapon kattintson a jobb felső sarokban található **klónozás** elemre.
   
1. A **klónozási tárház** párbeszédpanelen válassza a **https** **http-kapcsolathoz vagy SSH** -kapcsolathoz lehetőséget, majd másolja a klónozási URL-címet a vágólapra a **parancssorban** .
   
   ![Klónozási tárház](./media/project-ic-tasks/clone.png)
   
1. A helyi gépen vagy DSVM hozza létre a következő könyvtárakat:
   
   - Windows esetén: **C:\GitRepos**
   - Linux esetén: **$Home/gitrepos**
   
1. Váltson a létrehozott könyvtárba.
   
1. A git Bashben futtassa az parancsot `git clone <clone URL>` minden olyan tárházhoz, amelyet klónozottként szeretne használni. 
   
   A következő parancs például a **TeamUtilities** tárházat a helyi számítógép *MyTeam* könyvtárába klónozott. 
   
   **HTTPS-kapcsolat:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH-kapcsolatok:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Ellenőrizze, hogy látható-e a klónozott Tárházak mappái a helyi projekt címtárában.
   
   ![Három helyi tárház mappája](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Az Azure file Storage csatlakoztatása a DSVM

Ha a csapata vagy projektje megosztott eszközöket tartalmaz az Azure file Storage-ban, csatlakoztassa a file Storage-t a helyi géphez vagy DSVM. Kövesse az [Azure file Storage csatlakoztatása a helyi gépen vagy DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm)című témakör utasításait.

## <a name="next-steps"></a>Következő lépések

Az alábbiakban a csoportos adatelemzési folyamat által meghatározott egyéb szerepkörök és feladatok részletes ismertetésére talál hivatkozásokat:

- [Az adatelemzési csapat Group Manager-feladatai](group-manager-tasks.md)
- [A csapat vezető feladatai egy adattudományi csapat számára](team-lead-tasks.md)
- [Az adatelemzési csapat projekt-vezető feladatai](project-lead-tasks.md)