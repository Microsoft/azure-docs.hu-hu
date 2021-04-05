---
title: Csoportos adatelemzési folyamat csoport-kezelő feladatai
description: Kövesse ezt a részletes útmutatót a Group Manager egy adatelemzési csapat projekten végzett feladatairól.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7ab6d6511d1e2cec82b321003c9d663249ddcf49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94740226"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Csoportos adatelemzési folyamat csoport-kezelő feladatai

Ez a cikk azokat a feladatokat ismerteti, amelyekkel egy *csoport-kezelő* befejeződik egy adatelemző szervezet számára. A Group Manager a teljes adatelemzési egységet egy vállalaton belül kezeli. Az adatelemzési egységek több csapattal is rendelkezhetnek, amelyek mindegyike különböző üzleti vertikális adatelemzési projekteken dolgozik. A Group Manager célja egy együttműködésen alapuló csoportos környezet létrehozása, amely a csoportos [adatelemzési folyamat](overview.md) (TDSP) szabványosítására szolgál. Az adatelemzési csapat által a TDSP szabványosított, az összes személyzeti szerepkör és kapcsolódó feladat áttekintését lásd: [csoportos adatelemzési folyamat szerepkörei és feladatai](roles-tasks.md).

Az alábbi ábrán a hat fő Group Manager telepítési feladat látható. A csoport kezelői a feladataikat helyetteseknek delegálják, de a szerepkörhöz kapcsolódó feladatok nem változnak.

![A Group Manager feladatai](./media/group-manager-tasks/tdsp-group-manager.png)

1. Hozzon létre egy **Azure DevOps-szervezetet** a csoport számára.
2. Hozza létre az alapértelmezett **GroupCommon-projektet** az Azure DevOps-szervezetben.
3. Hozza létre a **GroupProjectTemplate** -tárházat az Azure reposban.
4. Hozza létre a **GroupUtilities** -tárházat az Azure reposban.
5. Importálja a Microsoft TDSP csapata **ProjectTemplate** és **segédprogramjainak** tartalmát a közös Tárházak csoportjába.
6. **Tagság** és **engedélyek** beállítása a csapattagok számára a csoport eléréséhez.

Az alábbi oktatóanyag részletesen ismerteti a lépéseket. 

> [!NOTE] 
> Ez a cikk az Azure DevOps használatával állítja be a TDSP-környezeteket, mivel ez a TDSP megvalósításának módja a Microsoftnál. Ha a csoport más kódot üzemeltető vagy fejlesztői platformot használ, a csoport-kezelő feladatai megegyeznek, de a végrehajtásuk módja eltérő lehet.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Szervezet és projekt létrehozása az Azure DevOps

1. Nyissa meg a [VisualStudio.microsoft.com](https://visualstudio.microsoft.com), válassza a **Bejelentkezés** a jobb felső sarokban lehetőséget, és jelentkezzen be a Microsoft-fiókba. 
   
   ![Jelentkezzen be a Microsoft-fiókba](./media/group-manager-tasks/signinvs.png)
   
   Ha nincs Microsoft-fiók, válassza a **regisztráció most** lehetőséget, hozzon létre egy Microsoft-fiók, és jelentkezzen be ezzel a fiókkal. Ha a szervezet rendelkezik Visual Studio-előfizetéssel, jelentkezzen be az előfizetéshez tartozó hitelesítő adatokkal.
   
1. Miután bejelentkezett, a jobb felső sarokban az Azure DevOps lapon válassza az **új szervezet létrehozása** lehetőséget.
   
   ![Új szervezet létrehozása](./media/group-manager-tasks/create-organization.png)
   
1. Ha a rendszer kéri, hogy fogadja el a szolgáltatási feltételeket, az adatvédelmi nyilatkozatot és a viselkedési szabályzatot, válassza a **Folytatás** lehetőséget.
   
1. A feliratkozás párbeszédpanelen nevezze el az Azure DevOps-szervezetet, és fogadja el a gazdagép régiójának hozzárendelését, vagy válasszon egy másik régiót. Ezután válassza a **Folytatás** elemet. 

1. A kezdéshez **a projekt létrehozása** szakaszban adja meg a *GroupCommon*, majd a **projekt létrehozása** elemet. 
   
   ![Projekt létrehozása](./media/group-manager-tasks/create-project.png)

Megnyílik az **GroupCommon** projekt **Összefoglaló** lapja. A lap URL-címe *https: \/ / \<servername> / \<organization-name> /GroupCommon*.

![Projekt összegzése lap](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>A csoport közös tárházának beállítása

Az Azure Repos a következő típusú tárházat üzemelteti a csoport számára:

- **Közös adattárak**: általános célú adattárak, amelyekben az adatelemzési egységek több csapata is elfogadhat számos adatelemzési projektet. 
- **Csapat adattárai**: adatelemző egységen belüli adott csapatokhoz tartozó adattárak. Ezek a Tárházak egyediek a csapat igényeihez, és az adott csapaton belüli több projekthez is használhatók, de nem elég általánosak ahhoz, hogy egy adattudományi egységen belül több csapaton lehessen használni.
- **Projekt-Tárházak**: adott projektekhez tartozó Tárházak. Előfordulhat, hogy az ilyen adattárak nem elég általánosak a csapaton belüli több projekthez, illetve az adatelemzési egységben lévő más csapatokhoz.

A csoport közös tárházának beállításához a következőket kell tennie: 
- Az alapértelmezett **GroupCommon** -tárház átnevezése **GroupProjectTemplate**
- Új **GroupUtilities** -Tárház létrehozása

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Az alapértelmezett Project-tárház átnevezése GroupProjectTemplate

Az alapértelmezett **GroupCommon** -projekt tárházának átnevezése az **GroupProjectTemplate**-re:

1. A **GroupCommon** -projekt **összegzése** lapon válassza a **repók** lehetőséget. Ez a művelet a GroupCommon projekt alapértelmezett **GroupCommon** adattárát veszi igénybe, amely jelenleg üres.
   
1. A lap tetején húzza le a **GroupCommon** melletti nyilat, majd válassza a **tárolók kezelése** lehetőséget.
   
   ![Adattárak kezelése](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. A **Project Settings (projekt beállításai** ) lapon válassza a **...** elemet a **GroupCommon** mellett, majd válassza a **tárház átnevezése** elemet. 
   
   ![Válassza a... lehetőséget. majd válassza a tárház átnevezése elemet.](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. Az **GroupCommon-tárház átnevezése** felugró ablakban adja meg a *GroupProjectTemplate*, majd válassza az **Átnevezés** lehetőséget. 
   
   ![Adattár átnevezése](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>A GroupUtilities adattár létrehozása

A **GroupUtilities** adattár létrehozása:

1. A **GroupCommon** -projekt **összegzése** lapon válassza a **repók** lehetőséget. 
   
1. A lap tetején húzza le a **GroupProjectTemplate** melletti nyilat, és válassza az **új tárház** elemet.
   
   ![Új tárház kiválasztása](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. Az **új Tárház létrehozása** párbeszédpanelen válassza a **git** **lehetőséget, írja be a** *GroupUtilities* **nevet az adattár neveként**, majd válassza a **Létrehozás** lehetőséget.
   
   ![GroupUtilities-Tárház létrehozása](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. A **Project Settings (projekt beállításai** ) lapon válassza a bal oldali navigációs sáv alatt található **adattárak** **lehetőséget, és** tekintse meg a két csoport adattárait: **GroupProjectTemplate** és **GroupUtilities**.
   
   ![Két csoport adattára](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>A Microsoft TDSP-csoport adattárainak importálása

Az oktatóanyag ezen részében a Microsoft TDSP csapata által kezelt **ProjectTemplate** -és **segédprogram** -Tárházak tartalmát importálja a **GroupProjectTemplate** -és **GroupUtilities** -tárházba. 

A TDSP-csoport adattárainak importálása:

1. A **GroupCommon** projekt kezdőlapján kattintson a bal oldali navigációs sávon a **repók** elemre. Megnyílik az alapértelmezett **GroupProjectTemplate** -tárház. 
   
1. A **GroupProjectTemplate üres** lapon válassza az **Importálás** lehetőséget. 
   
   ![Importálás kiválasztása](./media/group-manager-tasks/import-repo.png)
   
1. A **git-tárház importálása** párbeszédpanelen válassza a **git** lehetőséget a **forrás típusaként**, majd a **klónozási URL-cím** mezőbe írja be a *https: \/ /GitHub.com/Azure/Azure-TDSP-ProjectTemplate.git* értéket. Ezután válassza az **Importálás** lehetőséget. A Microsoft TDSP Team ProjectTemplate adattárának tartalmát a rendszer a GroupProjectTemplate adattárba importálja. 
   
   ![Microsoft TDSP-csoport adattárának importálása](./media/group-manager-tasks/import-repo-2.png)
   
1. A **reposs** oldal tetején válassza a legördülő listát, és válassza ki a **GroupUtilities** -tárházat.
   
A két csoport összes tárháza már tartalmazza az összes fájlt, kivéve a *. git* könyvtárában lévő fájlokat a Microsoft TDSP csapatának megfelelő tárházában. 

## <a name="customize-the-contents-of-the-group-repositories"></a>A csoport adattárainak tartalmának testreszabása

Ha testre szeretné szabni a csoport adattárainak tartalmát, hogy azok megfeleljenek a csoport konkrét igényeinek, most megteheti. Módosíthatja a fájlokat, módosíthatja a címtár szerkezetét, vagy hozzáadhat olyan fájlokat, amelyeket a csoport fejlesztett ki, vagy amelyek hasznosak lehetnek a csoport számára.

### <a name="make-changes-in-azure-repos"></a>Változtatások az Azure-ban

A tárház tartalmának testreszabása:

1. A **GroupCommon** -projekt **összegzése** lapon válassza a **repók** lehetőséget. 
   
1. A lap tetején válassza ki a testreszabni kívánt tárházat.

1. A tárház-címtár struktúrájában navigáljon a módosítani kívánt mappához vagy fájlhoz. 
   
   - Új mappák vagy fájlok létrehozásához kattintson az **új** elem melletti nyílra. 
     
     ![Új fájl létrehozása](./media/group-manager-tasks/new-file.png)
     
   - Fájlok feltöltéséhez válassza a **fájl (ok) feltöltése** lehetőséget. 
     
     ![Fájlok feltöltése](./media/group-manager-tasks/upload-files.png)
     
   - Meglévő fájlok szerkesztéséhez navigáljon a fájlhoz, majd válassza a **Szerkesztés** lehetőséget. 
     
     ![Fájl szerkesztése](./media/group-manager-tasks/edit-file.png)
     
1. Fájlok hozzáadása vagy szerkesztése után válassza a **véglegesítés** lehetőséget.
   
   ![Módosítások véglegesítve](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Módosítások végrehajtása a helyi számítógép vagy DSVM használatával

Ha a helyi géppel vagy DSVM szeretné végrehajtani a módosításokat, és leküldi a módosításokat a csoport adattárakba, ellenőrizze, hogy rendelkezik-e a git és a Dsvm használatához szükséges előfeltételekkel:

- Azure-előfizetés, ha DSVM szeretne létrehozni.
- A git telepítve van a gépen. Ha DSVM használ, a git előre telepítve van. Egyéb esetben lásd: [platformok és eszközök függelék](platforms-and-tools.md#appendix).
- Ha DSVM szeretne használni, az Azure-ban létrehozott és konfigurált Windows-vagy Linux-DSVM. További információt és útmutatást a [Data Science Virtual Machine dokumentációjában](../data-science-virtual-machine/index.yml)talál.
- Windows DSVM esetén a [git Hitelesítőadat-kezelő (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) telepítve van a gépen. A *readme.MD* fájlban görgessen le a **letöltés és telepítés** szakaszban, és válassza ki a **legújabb telepítőt**. Töltse le az *. exe* -telepítőt a telepítő lapról, és futtassa. 
- Linux DSVM esetében egy nyilvános SSH-kulcs, amely be van állítva a DSVM, és hozzá lett adva az Azure DevOps. További információt és útmutatást az **SSH nyilvános kulcs létrehozása** című szakaszban talál a [platformok és eszközök függelékben](platforms-and-tools.md#appendix). 

Először másolja vagy *klónozása* a tárházat a helyi gépre. 
   
1. A **GroupCommon** -projekt **összegzése** lapon válassza a **repók** lehetőséget, majd a lap tetején válassza ki a klónozott tárházat.
   
1. A tárház lapon kattintson a jobb felső sarokban található **klónozás** elemre.
   
1. A **klónozási tárház** párbeszédpanelen válassza a **https** **http-kapcsolathoz vagy SSH** -kapcsolathoz lehetőséget, majd másolja a klónozási URL-címet a vágólapra a **parancssorban** .
   
   ![Klónozási tárház](./media/group-manager-tasks/clone.png)
   
1. A helyi gépen hozza létre a következő könyvtárakat:
   
   - Windows esetén: **C:\GitRepos\GroupCommon**
   - Linux, **$/GitRepos/GroupCommon** a saját kezdőkönyvtár 
   
1. Váltson a létrehozott könyvtárba.
   
1. A git Bashben futtassa a parancsot. `git clone <clone URL>.`
   
   Például az alábbi parancsok egyike klónozott a **GroupUtilities** -tárházat a helyi számítógép *GroupCommon* könyvtárába. 
   
   **HTTPS-kapcsolat:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH-kapcsolatok:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Miután bármilyen módosítást végzett a tárház helyi klónjában, leküldheti a módosításokat a közös csoport általános adattárakba. 

Futtassa a következő git bash-parancsokat a helyi **GroupProjectTemplate** vagy a **GroupUtilities** könyvtárból.

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

## <a name="add-group-members-and-configure-permissions"></a>Csoporttagok hozzáadása és engedélyek konfigurálása

Tagok hozzáadása a csoporthoz:

1. Az Azure DevOps a **GroupCommon** projekt kezdőlapján válassza a **projekt beállításai** lehetőséget a bal oldali navigációs sávon. 
   
1. A **projekt beállításai** bal oldali navigációs sávon válassza **a csapatok** lehetőséget, majd a **csapatok** lapon válassza ki a **GroupCommon csapatot**. 
   
   ![Csapatok konfigurálása](./media/group-manager-tasks/teams.png)
   
1. A **csapat profilja** lapon válassza a **Hozzáadás** lehetőséget.
   
   ![Hozzáadás a GroupCommon csapathoz](./media/group-manager-tasks/add-to-team.png)
   
1. A **felhasználók és csoportok hozzáadása** párbeszédpanelen keresse meg és válassza ki a csoporthoz hozzáadni kívánt tagokat, majd kattintson a **módosítások mentése** gombra. 
   
   ![Felhasználók és csoportok hozzáadása](./media/group-manager-tasks/add-users.png)
   

A tagok engedélyeinek konfigurálása:

1. A **projekt beállításai** bal oldali navigációs sávon válassza az **engedélyek** lehetőséget. 
   
1. Az **engedélyek** lapon válassza ki azt a csoportot, amelyhez hozzá kívánja adni a tagokat. 
   
1. Az adott csoport lapján válassza a **tagok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget. 
   
1. A **tagok meghívása** felugró ablakban keresse meg és válassza ki a csoportba felvenni kívánt tagokat, majd kattintson a **Mentés** gombra. 
   
   ![Engedélyek megadása a tagoknak](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Következő lépések

Az alábbi hivatkozások a csoportos adatelemzési folyamat egyéb szerepköreinek és feladatainak részletes leírására mutatnak:

- [A csapat vezető feladatai egy adattudományi csapat számára](team-lead-tasks.md)
- [Az adatelemzési csapat projekt-vezető feladatai](project-lead-tasks.md)
- [Az adatelemzési csapat egyedi közreműködő feladatainak projektje](project-ic-tasks.md)
