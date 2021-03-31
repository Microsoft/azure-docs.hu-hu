---
title: A csapat adatelemzési folyamatának csapatának vezető feladatai
description: Egy csapat adatelemzési folyamatának csapatával kapcsolatos feladatok részletes áttekintése
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: df7d2278487c1b098615a14562c498b9187c56eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000028"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>A Team adatelemzési folyamatának csapatával kapcsolatos feladatok

Ez a cikk azokat a feladatokat ismerteti, amelyekkel a *csapat vezeti* az adatelemzési csapatot. A csapat vezető célja, hogy olyan együttműködő csapatmunka-környezetet hozzon létre, amely a [csoportos adatelemzési folyamat](overview.md) (TDSP) szabványosítására szolgál. A TDSP célja az együttműködés és a csapatmunka fejlesztése. 

A TDSP egy agilis, ismétlődő adatelemzési módszer a prediktív elemzési megoldások és intelligens alkalmazások hatékony megvalósítása érdekében. A folyamat a Microsoft és az iparág által ajánlott eljárásokat és struktúrákat is felhasználja.  A cél az adatelemzési kezdeményezések sikeres megvalósítása és az elemzési programok előnyeinek teljes körű megvalósítása. A munkatársak szerepköreinek és a TDSP szabványosított adatelemzési csapathoz kapcsolódó feladatoknak a körvonalazása: a [csoportos adatelemzési folyamat szerepkörei és feladatai](roles-tasks.md).

A csapat vezetője a vállalat adattudományi egységében számos adatszakértőből álló csapatot kezel. Az adattudományi egység méretétől és struktúrájától függően a [csoportvezető](group-manager-tasks.md) és a csapat vezetője is lehet ugyanaz a személy, vagy a feladataikat helyetteseknek delegálhatja. A feladatok azonban nem változnak. 

Az alábbi ábrán a csapat által vezetett feladatok munkafolyamata látható a Team-környezet beállításához:

![Csapat vezető tevékenységének munkafolyamata](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Hozzon létre egy **Team-projektet** a csoport szervezetében az Azure DevOps. 
  
1. Nevezze át az alapértelmezett **TeamUtilities** a csoportba.
  
1. Hozzon létre egy új **TeamTemplate** -tárházat a csapat projektben. 
  
1. Importálja a csoport **GroupUtilities** és **GroupProjectTemplate** -tárházának tartalmát a **TeamUtilities** és a **TeamTemplate** -tárházba. 
  
1. Állítsa be a **biztonsági vezérlést** a csapattagok hozzáadásával és az engedélyeik konfigurálásával.
  
1. Ha szükséges, hozzon létre a csoport és az elemzési erőforrások:
   - Adja hozzá a **TeamUtilities** -tárházhoz a csoportra jellemző segédprogramokat. 
   - Hozzon létre **Azure file Storage** -t az adategységek tárolására, amelyek hasznosak lehetnek a teljes csapat számára. 
   - Csatlakoztassa az Azure file Storage-t a Team Lead **Data Science Virtual Machine** (DSVM), és adja hozzá az adategységeket.

Az alábbi oktatóanyag részletesen ismerteti a lépéseket.

> [!NOTE] 
> Ez a cikk az Azure DevOps és a DSVM segítségével állítja be a TDSP-csapat környezetét, mert ez a Microsoft-TDSP implementálása. Ha a csapata más kódot üzemeltető vagy fejlesztői platformot használ, a csapat vezető feladatai megegyeznek, de a végrehajtásuk módja eltérő lehet.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag azt feltételezi, hogy a következő erőforrásokat és engedélyeket állította be a [Group Manager](group-manager-tasks.md):

- Az adategységhez tartozó Azure DevOps- **szervezet**
- **GroupProjectTemplate** -és **GroupUtilities** -Tárházak, amelyek a Microsoft TDSP-csapat **ProjectTemplate** -és **segédprogram** -tárházának tartalmával vannak feltöltve
- A céges fiókjához tartozó engedélyek, amelyek segítségével projekteket és adattárakat hozhat létre a csapat számára

A Tárházak klónozásához és a tartalmak helyi gépen vagy DSVM való módosításához, illetve az Azure file Storage beállításához és a DSVM való csatlakoztatásához a következőkre lesz szüksége:

- Azure-előfizetés.
- A git telepítve van a gépen. Ha DSVM használ, a git előre telepítve van. Egyéb esetben lásd: [platformok és eszközök függelék](platforms-and-tools.md#appendix).
- Ha DSVM szeretne használni, az Azure-ban létrehozott és konfigurált Windows-vagy Linux-DSVM. További információt és útmutatást a [Data Science Virtual Machine dokumentációjában](../data-science-virtual-machine/index.yml)talál.
- Windows DSVM esetén a [git Hitelesítőadat-kezelő (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) telepítve van a gépen. A *readme.MD* fájlban görgessen le a **letöltés és telepítés** szakaszban, és válassza ki a **legújabb telepítőt**. Töltse le az *. exe* -telepítőt a telepítő lapról, és futtassa. 
- Linux DSVM esetében egy nyilvános SSH-kulcs, amely be van állítva a DSVM, és hozzá lett adva az Azure DevOps. További információt és útmutatást az **SSH nyilvános kulcs létrehozása** című szakaszban talál a [platformok és eszközök függelékben](platforms-and-tools.md#appendix). 

## <a name="create-a-team-project-and-repositories"></a>Csapat projekt és adattárak létrehozása

Ebben a szakaszban a következő erőforrásokat hozza létre a csoport Azure DevOps-szervezetében:

- A **MyTeam** projekt az Azure DevOps
- A **TeamTemplate** adattár
- A **TeamUtilities** adattár

Az ebben az oktatóanyagban szereplő adattárakhoz és könyvtárakhoz megadott nevek feltételezik, hogy egy külön projektet kíván létrehozni a saját csapata számára a nagyobb adatelemzési szervezeten belül. A teljes csoport azonban úgy is dönthet, hogy a Group Manager vagy a szervezet rendszergazdája által létrehozott egyetlen projekt keretében dolgozik. Ezután az összes adatelemzési csapat létrehoz egy adattárat ebben az egyetlen projektben. Ez a forgatókönyv a következő esetekben lehet érvényes:
- Egy kis adattudományi csoport, amely nem rendelkezik több adatelemzési csapattal. 
- Egy nagyobb adatelemzési csoport több adatelemző csapattal, amelyek ennek ellenére szeretnének optimalizálni a csapaton belüli együttműködést olyan tevékenységekkel, mint a csoport szintű Sprint-tervezés. 

Ha a csapatok úgy döntenek, hogy a csoportra jellemző adattárak egyetlen csoportos projektben vannak, akkor a csapat által létrehozott tárolók a *\<TeamName> sablonhoz* és a *\<TeamName> segédprogramokhoz* hasonló névvel hozhatnak létre adattárakat. Például: *TeamATemplate* és *TeamAUtilities*. 

A csapatnak minden esetben meg kell adnia, hogy a csapat tagjai tudják, hogy mely sablon és segédprogramok tárházat állítanak be a beállításhoz és a klónozáshoz. A projekt-érdeklődőknek követniük kell az [adatelemzési csapat projekt-vezető feladatait](project-lead-tasks.md) a projekt-Tárházak létrehozásához, akár külön projektek, akár egyetlen projekt keretében. 

### <a name="create-the-myteam-project"></a>A MyTeam projekt létrehozása

Különálló projekt létrehozása a csapat számára:

1. A böngészőben nyissa meg a csoport Azure DevOps-szervezetének kezdőlapját a *https: \/ / \<server name> / \<organization name>* címen, és válassza az **új projekt** lehetőséget. 
   
   ![Új projekt kiválasztása](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. A **projekt létrehozása** párbeszédpanelen adja meg a csoport nevét (például *MyTeam*) a **projekt neve** alatt, majd válassza a **speciális** lehetőséget. 
   
1. A **verziókövetés** területen válassza a **git** lehetőséget, majd a **munkaelemek feldolgozása** területen válassza az **agilis** lehetőséget. Ezután kattintson a **Létrehozás** elemre. 
   
   ![Projekt létrehozása](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
Megnyílik a Team Project **Összefoglaló** lapja, a Page URL *https: \/ / \<server name> / \<organization name> / \<team name>*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>A MyTeam alapértelmezett tárházának átnevezése a TeamUtilities

1. A **MyTeam** -projekt **összegzése** lapon válassza ki, **hogy milyen szolgáltatást szeretne kezdeni?** lehetőségnél válassza a **repók** lehetőséget. 
   
   ![A repók kiválasztása](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. A **MyTeam** -adattár lapon válassza ki a **MyTeam** -tárházat az oldal tetején, majd válassza a legördülő listából a **tárolók kezelése** lehetőséget. 
   
   ![Válassza ki a tárak kezelése elemet](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. A **Project Settings (projekt beállításai** ) lapon válassza a **...** lehetőséget a **MyTeam** adattár mellett, majd válassza a **tárház átnevezése** elemet. 
   
   ![Adattár átnevezésének kiválasztása](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. Az **MyTeam-tárház átnevezése** felugró ablakban adja meg a *TeamUtilities*, majd válassza az **Átnevezés** lehetőséget. 

### <a name="create-the-teamtemplate-repository"></a>A TeamTemplate adattár létrehozása

1. A **Project Settings (projekt beállításai** ) lapon válassza az **új adattár lehetőséget.** 
   
   ![Új tárház kiválasztása](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Vagy válassza az **MyTeam** -projekt **összegzése** lap bal oldali navigációs menüjében található **repók** lehetőséget, válasszon ki egy adattárat a lap tetején, majd válassza ki az **új tárház** elemet a legördülő menüből.
   
1. Az **új Tárház létrehozása** párbeszédpanelen győződjön meg róla, hogy a **git** elem van kiválasztva a **típus** területen. Adja meg a *TeamTemplate* a **tárház neve** területen, majd válassza a **Létrehozás** lehetőséget.
   
   ![Tárház létrehozása](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Győződjön meg arról, hogy a **TeamUtilities** és a **TeamTemplate** két tárháza látható a projekt beállításai lapon. 
   
   ![Két csapat adattára](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>A csoport általános tárházai tartalmának importálása

A csapat adattárainak feltöltése a Group Manager által beállított közös adattárak tartalmával:

1. A **MyTeam** projekt kezdőlapján válassza a **repók** lehetőséget a bal oldali navigációs sávon. Ha egy üzenet jelenik meg arról, hogy a **MyTeam** -sablon nem található, akkor a másik lehetőségnél válassza ki a hivatkozást **, és navigáljon az alapértelmezett TeamTemplate-tárházhoz.** 
   
   Megnyílik az alapértelmezett **TeamTemplate** adattár. 
   
1. A **TeamTemplate üres** lapon válassza az **Importálás** lehetőséget. 
   
   ![Importálás kiválasztása](./media/team-lead-tasks/import-repo.png)
   
1. A **git-tárház importálása** párbeszédpanelen válassza a **git** lehetőséget a **forrás típusaként**, majd adja meg a csoport általános sablonjának URL-címét a **klónozott URL-cím** alatt. Az URL-cím *https: \/ / \<server name> / \<organization name> /_git \<repository name> /*. Például: *https: \/ /dev.Azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Válassza az **Importálás** lehetőséget. A Group template-tárház tartalmát a rendszer importálja a Team template adattárba. 
   
   ![Csoport általános sablonjának importálása adattár](./media/team-lead-tasks/import-repo-2.png)
   
1. A projekt **reposs** oldalának tetején válassza a legördülő listát, és válassza ki a **TeamUtilities** -tárházat.
   
1. Az importálási folyamat megismétlésével importálja a Group Common Utilities-tárház (például *GroupUtilities*) tartalmát a **TeamUtilities** -tárházba. 
   
A két csapat tárháza mostantól tartalmazza a megfelelő csoport közös tárházának fájljait. 

### <a name="customize-the-contents-of-the-team-repositories"></a>A csapat adattárainak tartalmának testreszabása

Ha testre szeretné szabni, hogy a csapata mely tárházait szeretné kielégíteni a csapat konkrét igényeinek megfelelően, ezt most megteheti. Módosíthatja a fájlokat, módosíthatja a címtár szerkezetét, vagy hozzáadhat fájlokat és mappákat.

Fájlok és mappák módosítása, feltöltése vagy létrehozása közvetlenül az Azure DevOps:

1. A **MyTeam** -projekt **összegzése** lapon válassza a **repók** lehetőséget. 
   
1. A lap tetején válassza ki a testreszabni kívánt tárházat.

1. A tárház-címtár struktúrájában navigáljon a módosítani kívánt mappához vagy fájlhoz. 
   
   - Új mappák vagy fájlok létrehozásához kattintson az **új** elem melletti nyílra. 
     
     ![Új fájl létrehozása](./media/team-lead-tasks/new-file.png)
     
   - Fájlok feltöltéséhez válassza a **fájl (ok) feltöltése** lehetőséget. 
     
     ![Fájlok feltöltése](./media/team-lead-tasks/upload-files.png)
     
   - Meglévő fájlok szerkesztéséhez navigáljon a fájlhoz, majd válassza a **Szerkesztés** lehetőséget. 
     
     ![Fájl szerkesztése](./media/team-lead-tasks/edit-file.png)
     
1. Fájlok hozzáadása vagy szerkesztése után válassza a **véglegesítés** lehetőséget.
   
   ![Módosítások véglegesítve](./media/team-lead-tasks/commit.png)

A helyi gépen vagy DSVM lévő adattárakkal való munkavégzéshez először másolja vagy *klónozottan* másolja a tárházat a helyi gépre, majd véglegesítse és küldje el a módosításokat a megosztott csapat adattárainak. 

Tárak klónozása:

1. A **MyTeam** -projekt **összegzése** lapon válassza a **repók** lehetőséget, majd a lap tetején válassza ki a klónozott tárházat.
   
1. A tárház lapon kattintson a jobb felső sarokban található **klónozás** elemre.
   
1. A **klónozási tárház** párbeszédpanel **parancssor** területén válassza a **https** lehetőséget egy http-kapcsolathoz vagy **SSH** -kapcsolathoz, és másolja a klónozási URL-címet a vágólapra.
   
   ![Klón URL-címének másolása](./media/team-lead-tasks/clone.png)
   
1. A helyi gépen hozza létre a következő könyvtárakat:
   
   - Windows esetén: **C:\GitRepos\MyTeam**
   - Linux esetén **$Home/gitrepos/myteam** 
   
1. Váltson a létrehozott könyvtárba.
   
1. A git Bashben futtassa a parancsot `git clone <clone URL>` , ahol a a \<clone URL> **klón** PÁRBESZÉDPANELRŐL másolt URL-cím.
   
   Ha például a következő parancsok egyikét használja a **TeamUtilities** -tárház klónozásához a helyi számítógép *MyTeam* könyvtárába. 
   
   **HTTPS-kapcsolat:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH-kapcsolatok:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Ha bármilyen módosítást szeretne végrehajtani a tárház helyi klónjában, véglegesítse és küldje el a megosztott csapat adattárainak módosításait. 

Futtassa a következő git bash-parancsokat a helyi **GitRepos\MyTeam\TeamTemplate** vagy a **GitRepos\MyTeam\TeamUtilities** könyvtárból.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Ha első alkalommal véglegesít egy git-tárházat, előfordulhat, hogy a parancs futtatása előtt konfigurálnia kell a *User.name* és a *User.email* globális paramétereket `git commit` . Futtassa a következő két parancsot:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Ha több git-tárházat követ el, ugyanazt a nevet és e-mail-címet használja mindegyikhez. Ugyanazt a nevet és e-mail-címet érdemes használni Power BI irányítópultok létrehozásakor, hogy több tárházban nyomon kövessék a git-tevékenységeket.

## <a name="add-team-members-and-configure-permissions"></a>Csoporttagok hozzáadása és engedélyek konfigurálása

Tagok hozzáadása a csapathoz:

1. Az Azure DevOps a **MyTeam** projekt kezdőlapján válassza a **projekt beállításai** lehetőséget a bal oldali navigációs sávon. 
   
1. A **projekt beállításai** bal oldali navigációs sávon válassza **a csapatok** lehetőséget, majd a **csapatok** lapon válassza ki a **MyTeam csapatot**. 
   
   ![Csapatok konfigurálása](./media/team-lead-tasks/teams.png)
   
1. A **csapat profilja** lapon válassza a **Hozzáadás** lehetőséget.
   
   ![Hozzáadás a MyTeam csapathoz](./media/team-lead-tasks/add-to-team.png)
   
1. A **felhasználók és csoportok hozzáadása** párbeszédpanelen keresse meg és válassza ki a csoporthoz hozzáadni kívánt tagokat, majd kattintson a **módosítások mentése** gombra. 
   
   ![Felhasználók és csoportok hozzáadása](./media/team-lead-tasks/add-users.png)
   

A csapattagok engedélyeinek konfigurálása:

1. A **projekt beállításai** bal oldali navigációs sávon válassza az **engedélyek** lehetőséget. 
   
1. Az **engedélyek** lapon válassza ki azt a csoportot, amelyhez hozzá kívánja adni a tagokat. 
   
1. Az adott csoport lapján válassza a **tagok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget. 
   
1. A **tagok meghívása** felugró ablakban keresse meg és válassza ki a csoportba felvenni kívánt tagokat, majd kattintson a **Mentés** gombra. 
   
   ![Engedélyek megadása a tagoknak](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Csoport-és elemzési erőforrások létrehozása

Ez a lépés nem kötelező, de az adat-és elemzési erőforrások teljes csapattal való megosztása teljesítménybeli és költséghatékonysági előnyökkel jár. A csapattagok végrehajtják projektjeiket a megosztott erőforrásokon, menthetik a költségvetést, és hatékonyabban dolgozhatnak. Létrehozhat Azure file Storage-t, és csatlakoztathatja azt a DSVM, és megoszthatja azokat a csoporttagokkal. 

További információ a csapattal (például Azure HDInsight Spark-fürtökkel) kapcsolatos egyéb erőforrások megosztásáról: [platformok és eszközök](platforms-and-tools.md). Ez a témakör egy adatelemzési nézőpontból nyújt útmutatást az igényeinek megfelelő erőforrások kiválasztásához, valamint a termékek oldalaira és egyéb releváns és hasznos oktatóanyagokra mutató hivatkozásokat tartalmaz.

>[!NOTE]
> Az adatközpontok közötti adatátvitel elkerüléséhez, amely lassú és költséges lehet, győződjön meg arról, hogy az Azure-erőforráscsoport, a Storage-fiók és a DSVM mind ugyanabban az Azure-régióban található. 

### <a name="create-azure-file-storage"></a>Azure file Storage létrehozása

1. Futtassa a következő szkriptet az Azure file Storage létrehozásához az adategységekhez, amelyek hasznosak a teljes csapat számára. A szkript felszólítja az Azure-előfizetések adatainak megadására, így azok készen állnak a bevitelre. 

   - Windows rendszerű gépen futtassa a parancsfájlt a PowerShell-parancssorból:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Linux rendszerű gépen futtassa a szkriptet a Linux-rendszerhéjból:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Ha a rendszer kéri, jelentkezzen be Microsoft Azure-fiókjába, és válassza ki a használni kívánt előfizetést.
   
1. Válassza ki a használni kívánt Storage-fiókot, vagy hozzon létre egy újat a kiválasztott előfizetéshez. Az Azure file Storage neveként kisbetűket, számokat és kötőjeleket is használhat.
   
1. A tárterület csatlakoztatásának és megosztásának megkönnyítéséhez nyomja le az ENTER billentyűt, vagy az *Y* érték megadásával mentse az Azure file Storage-adatokat egy szövegfájlba az aktuális könyvtárban. Ezt a szövegfájlt a **TeamTemplate** adattárba is bejelölheti, ideális esetben a **Docs\DataDictionaries** alatt, így a csapat összes projektje elérheti azt. Az Azure file Storage az Azure-DSVM való csatlakoztatásához a következő szakaszban is szükség van a fájl adataira. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Az Azure file Storage csatlakoztatása a helyi gépen vagy DSVM

1. Az Azure file Storage helyi gépre vagy DSVM való csatlakoztatásához használja az alábbi parancsfájlt.
   
   - Windows rendszerű gépen futtassa a parancsfájlt a PowerShell-parancssorból:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Linux rendszerű gépen futtassa a szkriptet a Linux-rendszerhéjból:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Nyomja le az ENTER billentyűt, vagy a folytatáshoz adja meg az *Y* értéket, ha az előző lépésben mentett egy Azure file Storage-információs fájlt. Adja meg a létrehozott fájl teljes elérési útját és nevét. 
   
   Ha nem rendelkezik Azure file Storage-információs fájllal, írja be az *n* értéket, és kövesse az utasításokat az előfizetés, az Azure Storage-fiók és az Azure file Storage-információk megadásához.
   
1. Adja meg annak a helyi vagy TDSP-meghajtónak a nevét, amelybe a fájlmegosztást csatlakoztatni szeretné. A képernyőn megjelenik a meglévő meghajtók neveinek listája. Olyan nevet adjon meg, amely még nem létezik.
   
1. Győződjön meg arról, hogy az új meghajtó és a tároló sikeresen csatlakoztatva van a gépen.

## <a name="next-steps"></a>Következő lépések

Az alábbiakban a csoportos adatelemzési folyamat által meghatározott egyéb szerepkörök és feladatok részletes ismertetésére talál hivatkozásokat:

- [Az adatelemzési csapat Group Manager-feladatai](group-manager-tasks.md)
- [Az adatelemzési csapat projekt-vezető feladatai](project-lead-tasks.md)
- [Az adatelemzési csapat egyedi közreműködő feladatainak projektje](project-ic-tasks.md)