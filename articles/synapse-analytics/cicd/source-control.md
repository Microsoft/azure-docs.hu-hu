---
title: Forrásvezérlés a Synapse Studio
description: Megtudhatja, hogyan konfigurálhatja a forrásvezérlést a Azure Synapse Studióban
author: liud
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 8f1b459c2644472463004c231f5827ff653d2da1
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567842"
---
# <a name="source-control-in-azure-synapse-studio"></a>Forrásvezérlés a Azure Synapse Studióban

Alapértelmezés szerint a Azure Synapse a Studio szerzői közvetlenül a Synapse szolgáltatásban. Ha együttműködésre van szüksége a Git használatával a verzióvezérléshez, a Synapse Studio lehetővé teszi a munkaterület társítását egy Git-adattárakhoz, az Azure DevOpshoz vagy a GitHubhoz. 

Ez a cikk bemutatja, hogyan konfigurálhatja és munkához egy Synapse-munkaterületet engedélyezett Git-adattárak használatával. Emellett kiemelünk néhány ajánlott gyakorlatot és egy hibaelhárítási útmutatót is.

> [!NOTE]
> Azure Synapse Studio Git-integrációja nem érhető el a Azure Government Cloudban.

## <a name="configure-git-repository-in-synapse-studio"></a>Git-adattár konfigurálása a Synapse Studio 

A Synapse Studio után konfigurálhat egy Git-adattárat a munkaterületen. Egy Synapse Studio munkaterület egyszerre csak egy Git-adattárhoz társítható. 

### <a name="configuration-method-1-global-bar"></a>1. konfigurációs módszer: globális sáv

A Synapse Studio sávon válassza a **Synapse Live** legördülő menüt, majd válassza a **Kódtár beállítása lehetőséget.**

![A kódtár beállításainak konfigurálása szerzői beállításokból](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>2. konfigurációs módszer: Központ kezelése

A Manage (Kezelés) Synapse Studio. A **Verzióvezérlés szakaszban** válassza a **Git-konfiguráció** lehetőséget. Ha nincs csatlakoztatva adattár, kattintson a Configure (Konfigurálás) **gombra.**

![A kódtár beállításainak konfigurálása a felügyeleti központban](media/configure-repo-2.png)

> [!NOTE]
> A munkaterület közreműködőjeként, tulajdonosaként vagy magasabb szintű szerepköreként megadott felhasználók konfigurálhatja, szerkeszthetik a beállítást, és leválaszthatja a Git-adattárat a Azure Synapse Studióban 

A munkaterületen csatlakoztathatja az Azure DevOpsot vagy a GitHub Git-adattárat.

## <a name="connect-with-azure-devops-git"></a>Kapcsolódás az Azure DevOps Git használatával 

Synapse-munkaterületet társíthat egy Azure DevOps-adattárhoz a verziókezelés, az együttműködés, a verziókezelés stb. érdekében. Ha nem rendelkezik Azure DevOps-adattárral, [](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) kövesse ezeket az utasításokat az adattár erőforrásainak létrehozásához.

### <a name="azure-devops-git-repository-settings"></a>Az Azure DevOps Git-adattár beállításai

A Git-adattárhoz való csatlakozáskor először válassza ki az adattár típusát Azure DevOps gitként, majd válasszon ki egy Azure AD-bérlőt a legördülő listából, majd kattintson a **Folytatás gombra.**

![A kódtár beállításainak konfigurálása](media/connect-with-azuredevops-repo-selected.png)

A konfigurációs panelen a következő Azure DevOps git-beállítások érhetők el:

| Beállítás | Leírás | Érték |
|:--- |:--- |:--- |
| **Adattár típusa** | Az Azure Repos kódtár típusa.<br/> | Azure DevOps Git vagy GitHub |
| **Azure Active Directory** | Az Azure AD-bérlő neve. | `<your tenant name>` |
| **Azure DevOps-fiók** | Az Azure Repos-szervezet neve. Az Azure Repos-szervezet nevét itt találja: `https://{organization name}.visualstudio.com` . Az Azure [Repos-szervezetbe](https://www.visualstudio.com/team-services/git/) bejelentkezve hozzáférhet Visual Studio profilhoz, és láthatja az adattárakat és a projekteket. | `<your organization name>` |
| **ProjectName (Projekt neve)** | Az Azure Repos-projekt neve. Az Azure Repos projekt nevét itt találja: `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **RepositoryName (Adattár neve)** | Az Azure Repos-kódtár neve. Az Azure Repos-projektek Git-adattárakat tartalmaznak a forráskód a projekt növekedésével való kezeléséhez. Létrehozhat egy új adattárat, vagy használhat egy meglévő adattárat, amely már a projektben van. | `<your Azure Repos code repository name>` |
| **Együttműködési ág** | A közzétételhez használt Azure Repos együttműködési ág. Alapértelmezés szerint a következő: `master` . Módosítsa ezt a beállítást arra az esetre, ha egy másik ágból szeretne erőforrásokat közzétenni. Kiválaszthat meglévő ágakat, vagy létrehozhat újat | `<your collaboration branch name>` |
| **Gyökérmappa** | A gyökérmappa az Azure Repos együttműködési ágban. | `<your root folder name>` |
| **Meglévő erőforrások importálása az adattárba** | Megadja, hogy importálja-e a meglévő Synapse Studio egy Azure Repos Git-adattárba. Jelölje be a jelölőnégyzetet a munkaterület erőforrásainak JSON formátumban történő importáláshoz (a készletek kivételével) a társított Git-adattárba. Ez a művelet egyenként exportálja az egyes erőforrásokat. Ha ez a jelölőnégyzet nincs bejelölve, a rendszer nem importálja a meglévő erőforrásokat. | Bejelölve (alapértelmezett) |
| **Erőforrás importálása ebbe az ágba** | Válassza ki, hogy az erőforrások (sql-szkript, jegyzetfüzet, Spark-feladatdefiníció, adatkészlet, adatfolyam stb.) melyik ágba importálhatók. 

Az adattár hivatkozásával is gyorsan rámutathat arra a Git-adattárra, amelyhez csatlakozni szeretne. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Másik bérlő Azure Active Directory használata

Az Azure Repos Git-adattár egy másik bérlőben Azure Active Directory lehet. Másik Azure Active Directory-bérlő megadásához rendszergazdai engedélyekkel kell rendelkeznie az Ön által használt Azure-előfizetésben. További információ: [Előfizetés-rendszergazda módosítása](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)

> [!IMPORTANT]
> Ha egy másik Azure Active Directory szeretne csatlakozni, a bejelentkezett felhasználónak az adott Active Directory részének kell lennie. 

### <a name="use-your-personal-microsoft-account"></a>Személyes Microsoft-fiók

Ha személyes adattárat Microsoft-fiók Git-integrációhoz, a személyes Azure-adattárat a szervezet saját Active Directory.

1. Adja hozzá Microsoft-fiók személyes adatait a Active Directory-fiókhoz vendégként. További információ: [B2B Azure Active Directory felhasználók hozzáadása a Azure Portal.](../../active-directory/external-identities/add-users-administrator.md)

2. Jelentkezzen be a Azure Portal a személyes Microsoft-fiók. Ezután váltson a szervezet Active Directory.

3. Ugrás az Azure DevOps szakaszra, ahol láthatja a személyes adattát. Válassza ki a repo-t, és csatlakozzon Active Directory.

A konfigurációs lépések után a személyes adattár elérhetővé válik, amikor beállít egy Git-integrációt a Synapse Studio.

További információ az Azure-adattáraknak a szervezet Active Directory való csatlakoztatásával kapcsolatban: [A](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)szervezet csatlakoztatása Azure Active Directory.

## <a name="connect-with-github"></a>Kapcsolódás a GitHubbal 

 A munkaterületeket GitHub-adattárakhoz társíthatja a verziók vezérlése, együttműködése és verziószámozása érdekében. Ha nem rendelkezik GitHub-fiókkal vagy -adattárakkal, kövesse ezeket az [utasításokat](https://github.com/join) az erőforrások létrehozásához.

A GitHub és a Synapse Studio a nyilvános GitHubot (azaz ) és [https://github.com](https://github.com) a GitHub Enterprise-t is támogatja. Nyilvános és privát GitHub-adattárakat is használhat, ha rendelkezik olvasási és írási engedéllyel a GitHub-adattárhoz.

### <a name="github-settings"></a>GitHub-beállítások

A Git-adattárhoz való csatlakozáskor először válassza ki az adattár típusát GitHubként, majd adja meg a GitHub-fiókját vagy a GitHub Enterprise Server URL-címét, ha a GitHub Enterprise Servert használja, majd kattintson a Folytatás gombra.

![GitHub-adattár beállításai](media/connect-with-github-repo-1.png)

A konfigurációs panelen az alábbi GitHub-adattárbeállítások érhetők el:

| **Beállítás** | **Leírás**  | **Érték**  |
|:--- |:--- |:--- |
| **Adattár típusa** | Az Azure Repos kódtár típusa. | GitHub |
| **A GitHub Enterprise használata** | Jelölőnégyzet a GitHub Enterprise kiválasztásához | nincs kijelölve (alapértelmezett) |
| **GitHub Enterprise URL-címe** | A GitHub Enterprise gyökér URL-címe (HTTPS-nek kell lennie a helyi GitHub Enterprise-kiszolgálóhoz). Példa: `https://github.mydomain.com`. Csak akkor szükséges, ha **a GitHub Enterprise** használata van kiválasztva | `<your GitHub enterprise url>` |                                                           
| **GitHub-fiók** | A GitHub-fiók neve. Ez a név a következő helyen található: \/ /github.com/{fióknév}/{adattár neve}. Ha erre az oldalra lép, meg kell adnia a GitHub OAuth hitelesítő adatait a GitHub-fiókjában. | `<your GitHub account name>` |
| **Adattár neve**  | A GitHub-kódtár neve. A GitHub-fiókok Git-adattárakat tartalmaznak a forráskód kezeléséhez. Létrehozhat egy új adattárat, vagy használhat egy meglévő adattárat, amely már a fiókjában van. | `<your repository name>` |
| **Együttműködési ág** | A Közzétételhez használt GitHub-együttműködési ág. Alapértelmezés szerint a főkiszolgálója. Módosítsa ezt a beállítást arra az esetre, ha egy másik ágból szeretne erőforrásokat közzétenni. | `<your collaboration branch>` |
| **Gyökérmappa** | A gyökérmappa a GitHub-együttműködési ágban. |`<your root folder name>` |
| **Meglévő erőforrások importálása az adattárba** | Megadja, hogy importálni kell-e a meglévő Synapse Studio egy Git-adattárba. Jelölje be a jelölőnégyzetet a munkaterület erőforrásainak JSON formátumban történő importáláshoz (a készletek kivételével) a társított Git-adattárba. Ez a művelet egyenként exportálja az egyes erőforrásokat. Ha ez a jelölőnégyzet nincs bejelölve, a rendszer nem importálja a meglévő erőforrásokat. | Kiválasztva (alapértelmezett) |
| **Erőforrás importálása ebbe az ágba** | Válassza ki, hogy az erőforrások (sql-szkript, jegyzetfüzet, Spark-feladat definíciója, adatkészlet, adatfolyam stb.) melyik ága lesz importálva. 

### <a name="github-organizations"></a>GitHub-szervezetek

A GitHub-szervezethez való csatlakozáshoz a szervezetnek engedélyt kell adni a Synapse Studio. A szervezeten rendszergazdai engedélyekkel rendelkező felhasználónak el kell végeznie az alábbi lépéseket.

#### <a name="connecting-to-github-for-the-first-time"></a>Csatlakozás a GitHubhoz első alkalommal

Ha először csatlakozik a GitHubhoz a Synapse Studio, kövesse az alábbi lépéseket egy GitHub-szervezethez való csatlakozáshoz.

1. A Git konfigurációs panelen írja be a szervezet nevét a *GitHub-fiók mezőbe.* Megjelenik egy üzenet, amely a GitHubra való bejelentkezésre kéri. 

1. Jelentkezzen be a felhasználói hitelesítő adataival.

1. A rendszer megkéri, hogy engedélyezze a Synapse-t a következő *alkalmazásként: Azure Synapse.* Ezen a képernyőn megjelenik egy lehetőség, amely engedélyt ad a Synapse számára a szervezet eléréséhez. Ha nem látja az engedélyek megadásának beállítását, kérjen meg egy rendszergazdát, hogy manuálisan adja meg az engedélyt a GitHubon keresztül.

Ha követi ezeket a lépéseket, a munkaterület nyilvános és privát adattárakhoz is képes lesz csatlakozni a szervezeten belül. Ha nem tud csatlakozni, próbálja meg törölni a böngésző gyorsítótárát, és próbálkozzon újra.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Már csatlakoztatva van a GitHubhoz egy személyes fiókkal

Ha már csatlakozott a GitHubhoz, és csak engedélyt adott egy személyes fiók eléréséhez, kövesse az alábbi lépéseket az engedélyek szervezet számára való megadásához.

1. Nyissa meg a GitHubot, és nyissa meg a **Beállításokat.**

    ![GitHub-beállítások megnyitása](media/github-settings.png)

1. Válassza az **Alkalmazások lehetőséget.** Az **Authorized OAuth apps (Engedélyezett OAuth-alkalmazások)** lapon a *következőnek* kell Azure Synapse.

    ![OAuth-alkalmazások hitelesítése](media/authorize-app.png)

1. Válassza ki *Azure Synapse,* és adjon hozzáférést a szervezetnek.

    ![Szervezeti engedély megadása](media/grant-organization-permission.png)

A lépések befejezése után a munkaterület nyilvános és privát adattárakhoz is képes lesz csatlakozni a szervezeten belül.

## <a name="version-control"></a>Verziókövetés

A verzióvezérlő rendszerek (más néven _verzióvezérlés)_ lehetővé teszik a fejlesztők számára a kódon való együttműködést és a változások nyomon követését. A forrásvezérlés elengedhetetlen eszköz a többfejlesztős projektekhez.

### <a name="creating-feature-branches"></a>Szolgáltatáságak létrehozása

Minden git-adattár, amely egy Synapse Studio rendelkezik egy együttműködési ággal. ( `main` vagy az alapértelmezett együttműködési `master` ág). A felhasználók a legördülő menü **+ Új** ág lehetőségére kattintva is létrehozhatnak funkcióágakat. Miután megjelenik az új ág panel, adja meg a funkcióág nevét.

![Új ág létrehozása](media/create-new-branch.png)

Ha készen áll arra, hogy egyesítse a funkcióág módosításait az együttműködési ággal, kattintson az ág legördülő menüre, és válassza a **Create pull request (Lekéréses kérelem létrehozása) lehetőséget.** Ezzel a művelettel a Git-szolgáltatóhoz kerül, ahol lekéréses kérelmeket emelhet ki, kódokat kérhet le, és egyesítheti a módosításokat az együttműködési ággal. Csak az együttműködési ágból tehet közzé a Synapse szolgáltatásban. 

![Új lekéréses kérelem létrehozása](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Közzétételi beállítások konfigurálása

Alapértelmezés szerint a Synapse Studio létrehozza a munkaterület-sablonokat, és menti őket egy nevű `workspace_publish` ágba. Egyéni közzétételi ág konfiguráláskor adjon hozzá egy fájlt az együttműködési ág `publish_config.json` gyökérmappába. A közzétételkor a Synapse Studio beolvassa ezt a fájlt, a mezőt keres, és a munkaterület sablonfájlját a megadott `publishBranch` helyre menti. Ha az ág nem létezik, a Synapse Studio automatikusan létrehozza. A fájl például az alábbi módon néz ki:

```json
{
    "publishBranch": "workspace_publish"
}
```

Azure Synapse Studio egyszerre csak egy közzétételi ággal lehet. Új közzétételi ág megadásakor az előző közzétételi ág nem törlődik. Ha el szeretné távolítani az előző közzétételi ágat, törölje manuálisan.


### <a name="publish-code-changes"></a>Kód módosításainak közzététele

Az együttműködési ág módosításainak egyesítése után kattintson a **Közzététel** gombra a kód módosításainak manuális közzétételéhez az együttműködési ágban a Synapse szolgáltatásban.

![Változások közzétevése](media/gitmode-publish.png)

Megnyílik egy oldalpanel, ahol megerősítheti, hogy a közzétételi ág és a függőben lévő módosítások helyesek. A módosítások ellenőrzése után kattintson az **OK gombra** a közzététel megerősítéséhez.

![A megfelelő közzétételi ág ellenőrzése](media/publish-change.png)

> [!IMPORTANT]
> Az együttműködési ág nem reprezentációja a szolgáltatásban üzembe helyezett erőforrásoknak. Az együttműködési ág módosításait *manuálisan kell* közzétenni szolgáltatásként.

## <a name="switch-to-a-different-git-repository"></a>Váltás másik Git-adattárra

Másik Git-adattárra úgy válthat át, hogy a Verziókezelés alatti felügyeleti központ Git-konfigurációs **lapjára vált.** Válassza a **Leválasztás lehetőséget.** 

![Git ikon](media/remove-repository.png)

Adja meg a munkaterület nevét, majd kattintson a **Leválasztás** gombra a munkaterülethez társított Git-adattár eltávolításához.

Miután eltávolítja az aktuális adattárhoz való társítást, konfigurálhatja a Git-beállításokat egy másik adattár használatára, majd importálhatja a meglévő erőforrásokat az új adattárba.

> [!IMPORTANT]
> A Git-konfiguráció munkaterületről való eltávolítása nem töröl semmit az adattárból. A Synapse-munkaterület az összes közzétett erőforrást tartalmazza. A munkaterületet továbbra is közvetlenül a szolgáltatásban szerkesztheti.

## <a name="best-practices-for-git-integration"></a>Ajánlott eljárások a Git-integrációhoz

-   **Engedélyek.** Miután csatlakoztatta a munkaterülethez a Git-adattárat, bárki, aki a munkaterület bármely szerepkörével hozzáférhet a Git-adattárhoz, frissítheti az olyan összetevőket, mint az sql-szkript, a notebook, a spark-feladatdefiníció, az adatkészlet, az adatfolyam és a folyamat Git módban. Általában nem szeretné, hogy a csapat minden tagja jogosultsággal rendelkezik a munkaterület frissítéséhez. Csak a Synapse-munkaterület összetevő-készítőinek adjon engedélyt a Git-adattár számára. 
-   **Együttműködés**. Javasoljuk, hogy ne engedélyezze a közvetlen bejelentkezést az együttműködési ágba. Ez a korlátozás segíthet megelőzni a hibákat, mivel minden bejelentkezés egy lekéréses kérelem felülvizsgálati folyamatán megy keresztül, amely a [Funkcióágak létrehozása alatt áll.](source-control.md#creating-feature-branches)
-   **Synapse élő mód.** A Git módban való közzététel után az összes módosítás megjelenik az élő Synapse módban. Az élő Synapse módban a közzététel le van tiltva. Az összetevők akkor is megtekinthetők és futtathatók élő módban, ha a megfelelő engedélyekkel rendelkezik. 
-   **Összetevők szerkesztése a Studióban.** A Synapse Studio az egyetlen hely, ahol engedélyezheti a munkaterület verziókezelését, és automatikusan szinkronizálhatja a git módosításait. Az SDK-n, a PowerShellen keresztüli módosítás nem lesz szinkronizálva a Gitbe. Javasoljuk, hogy mindig szerkessze az összetevőt a Studióban, ha a git engedélyezve van.

## <a name="troubleshooting-git-integration"></a>Git-integráció hibaelhárítása

### <a name="access-to-git-mode"></a>Hozzáférés a Git módhoz 

Ha engedélyt kapott a munkaterülethez csatolt GitHub Git-adattárhoz, de nem fér hozzá a Git módhoz: 

1. Ürítse ki a gyorsítótárat, és frissítse az oldalt. 

1. Jelentkezzen be a GitHub-fiókjába.

### <a name="stale-publish-branch"></a>Elavult közzétételi ág

Ha a közzétételi ág nincs szinkronban az együttműködési ággal, és a legutóbbi közzététel ellenére elavult erőforrásokat tartalmaz, próbálkozzon az alábbi lépésekkel:

1. Az aktuális Git-adattár eltávolítása

1. Konfigurálja újra a Gitet ugyanazokkal a beállításokkal, de győződjön meg arról, hogy a **Meglévő** erőforrások importálása az adattárba beállítás be van jelölve, és válassza ki ugyanazt az ágat.  

1. Lekéréses kérelem létrehozása a változások együttműködési ágba való egyesítéséhez 

## <a name="unsupported-features"></a>Nem támogatott funkciók

- Synapse Studio nem engedélyezi a véglegesítések vagy az erőforrások szelektív közzétételét. 
- Synapse Studio nem támogatja a véglegesítési üzenetek testreszabását.
- A studio törlési művelete terv szerint közvetlenül a gitben lesz véglegesítve

## <a name="next-steps"></a>Következő lépések

* A folyamatos integráció és üzembe helyezés implementációja: Folyamatos integráció és [teljesítés (CI/CD).](continuous-integration-deployment.md)