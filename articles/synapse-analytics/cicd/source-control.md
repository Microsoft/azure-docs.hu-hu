---
title: Verziókövetés a szinapszis Studióban
description: Ismerje meg, hogyan konfigurálhatja a verziókövetés az Azure szinapszis Studióban
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 3564609d869bef090f0a3db5e6040ba0f5ad80b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98796961"
---
# <a name="source-control-in-azure-synapse-studio"></a>Verziókövetés az Azure szinapszis Studióban

Alapértelmezés szerint az Azure szinapszis Studio szerzője közvetlenül a szinapszis szolgáltatáson keresztül. Ha együttműködésre van szüksége a git használatával a verziókövetés számára, a szinapszis Studio lehetővé teszi, hogy a munkaterületet a git-adattárral, az Azure DevOps vagy a GitHubral társítsa. 

Ebből a cikkből megtudhatja, hogyan konfigurálhat és dolgozhat egy szinapszis-munkaterületen, ha engedélyezve van a git-tárház. Emellett kiemeljük az ajánlott eljárásokat és a hibaelhárítási útmutatót is.

> [!NOTE]
> Az Azure szinapszis Studio git-integrációja nem érhető el a Azure Government felhőben.

## <a name="configure-git-repository-in-synapse-studio"></a>A git-tárház konfigurálása a szinapszis Studióban 

A szinapszis Studio elindítása után beállíthatja a git-tárházat a munkaterületen. A szinapszis Studio-munkaterület egyszerre csak egy git-tárházhoz társítható. 

### <a name="configuration-method-1-global-bar"></a>1. konfigurációs módszer: globális sáv

A szinapszis Studio globális sávján kattintson a **szinapszis élő** legördülő menüre, majd válassza a **kódlap beállítása** lehetőséget.

![A kód-adattár beállításainak konfigurálása szerzői műveletekből](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>2. konfigurációs módszer: a hub kezelése

Nyissa meg a szinapszis Studio felügyelet központját. Válassza a **git-konfiguráció** elemet a **verziókövetés** szakaszban. Ha nincs csatlakoztatva adattár, kattintson a **Konfigurálás** elemre.

![A Code adattár beállításainak konfigurálása a felügyeleti központban](media/configure-repo-2.png)

> [!NOTE]
> A munkaterület-közreműködőként, tulajdonosként vagy magasabb szintű szerepkörökként megadott felhasználók konfigurálhatják, módosíthatják a git-tárházat az Azure szinapszis Studióban 

A munkaterületen az Azure DevOps vagy a GitHub git-tárházat is csatlakozhat.

## <a name="connect-with-azure-devops-git"></a>Kapcsolódjon az Azure DevOps git-vel 

A szinapszis-munkaterületet társíthatja egy Azure DevOps adattárral a verziókövetés, az együttműködés, a verziószámozás és így tovább. Ha nem rendelkezik Azure DevOps adattárral, a tárház erőforrásainak létrehozásához kövesse az [alábbi utasításokat](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) .

### <a name="azure-devops-git-repository-settings"></a>Az Azure DevOps git-tárházának beállításai

A git-tárházhoz való csatlakozáskor először válassza ki az adattár típusát Azure DevOps git néven, majd válasszon ki egy Azure AD-bérlőt a legördülő listából, és kattintson a **Folytatás** gombra.

![A kódlap beállításainak konfigurálása](media/connect-with-azuredevops-repo-selected.png)

A konfigurációs ablaktábla a következő Azure DevOps git-beállításokat jeleníti meg:

| Beállítás | Leírás | Érték |
|:--- |:--- |:--- |
| **Tárház típusa** | Az Azure Repos Code adattár típusa.<br/> | Azure DevOps git vagy GitHub |
| **Azure Active Directory** | Az Azure AD-bérlő neve. | `<your tenant name>` |
| **Azure DevOps-fiók** | Az Azure Repos-szervezet neve. Az Azure Repos-szervezet nevét itt találja: `https://{organization name}.visualstudio.com` . Bejelentkezhet az [Azure Repos-szervezetbe](https://www.visualstudio.com/team-services/git/) a Visual Studio-profil eléréséhez, és megtekintheti a tárházait és projektjeit. | `<your organization name>` |
| **Projektnév** | Azure Repos-projekt neve. Az Azure Repos-projekt nevét itt találja: `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **RepositoryName** | Az Azure Repos-kód tárházának neve. Az Azure Repos-projektek git-adattárakat tartalmaznak, amelyekkel a projekt növekedésével kezelhetők a forráskód. Létrehozhat egy új tárházat, vagy használhat olyan meglévő tárházat, amely már szerepel a projektben. | `<your Azure Repos code repository name>` |
| **Együttműködési ág** | A közzétételhez használt Azure Repos együttműködési ág. Alapértelmezés szerint a `master` . Módosítsa ezt a beállítást abban az esetben, ha egy másik ág erőforrásait közzé szeretné tenni. Kijelölhet meglévő ágakat, vagy létrehozhat új | `<your collaboration branch name>` |
| **Gyökérmappa** | Az Azure Repos együttműködési ág gyökérkönyvtára. | `<your root folder name>` |
| **Meglévő erőforrások importálása adattárba** | Megadja, hogy a rendszer importálja-e a meglévő erőforrásokat a szinapszis studióból egy Azure Repos git-tárházba. Jelölje be a jelölőnégyzetet a munkaterület-erőforrások (a készletek kivételével) importálásához a társított git-tárházba JSON formátumban. Ez a művelet egyenként exportálja az egyes erőforrásokat. Ha a jelölőnégyzet nincs bejelölve, a meglévő erőforrások nem kerülnek importálásra. | Bejelölve (alapértelmezett) |
| **Erőforrás importálása ebbe az elágazásba** | Válassza ki, hogy az erőforrásokat (SQL-parancsfájl, jegyzetfüzet, Spark-feladatdefiníció, adatkészlet, adatfolyam stb.) melyik ágat importálja a rendszer. 

A tárház hivatkozásával gyorsan rámutathat arra a git-tárházra, amelyhez csatlakozni szeretne. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Másik Azure Active Directory bérlő használata

Az Azure Repos git-tárháza lehet egy másik Azure Active Directory bérlő. Másik Azure Active Directory-bérlő megadásához rendszergazdai engedélyekkel kell rendelkeznie az Ön által használt Azure-előfizetésben. További információ: előfizetés- [rendszergazda módosítása](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)

> [!IMPORTANT]
> Egy másik Azure Active Directoryhoz való kapcsolódáshoz a bejelentkezett felhasználónak az Active Directory részét kell képeznie. 

### <a name="use-your-personal-microsoft-account"></a>Személyes Microsoft-fiók használata

Ha személyes Microsoft-fiók szeretne használni a git-integrációhoz, összekapcsolhatja a személyes Azure-tárházat a szervezete Active Directory.

1. Adja hozzá személyes Microsoft-fiókét a szervezet Active Directory vendégként. További információ: [Azure Active Directory B2B együttműködéssel rendelkező felhasználók hozzáadása a Azure Portal](../../active-directory/external-identities/add-users-administrator.md).

2. Jelentkezzen be a Azure Portalba személyes Microsoft-fiókával. Ezután váltson a szervezet Active Directoryra.

3. Nyissa meg az Azure DevOps szakaszt, ahol most megjelenik a személyes tárháza. Válassza ki a tárházat, és kapcsolódjon Active Directory.

Ezeket a konfigurációs lépéseket követően a saját tárháza akkor érhető el, ha git-integrációt állít be a szinapszis Studióban.

További információ az Azure Repos szervezet Active Directoryhoz való csatlakoztatásáról: [a szervezet csatlakoztatása Azure Active Directoryhoz](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="connect-with-github"></a>Kapcsolódjon a GitHubhoz 

 A munkaterületet társíthatja egy GitHub-adattárral a verziókövetés, az együttműködés, a verziószámozás érdekében. Ha nem rendelkezik GitHub-fiókkal vagy-adattárral, kövesse az [alábbi utasításokat](https://github.com/join) az erőforrások létrehozásához.

A szinapszis Studióval való integrációja támogatja a nyilvános GitHub (azaz a [https://github.com](https://github.com) ) és a GitHub Enterprise használatát is. Használhatja a nyilvános és a privát GitHub-adattárakat is, amíg a GitHubon olvasási és írási engedéllyel rendelkezik a tárházhoz.

### <a name="github-settings"></a>GitHub-beállítások

A git-tárházhoz való csatlakozáskor először válassza ki a tárház típusát GitHub néven, majd adja meg a GitHub-fiókját vagy a GitHub vállalati kiszolgáló URL-címét, ha a GitHub Enterprise Servert használja, és kattintson a **Folytatás** gombra.

![GitHub-adattár beállításai](media/connect-with-github-repo-1.png)

A konfigurációs ablaktábla a GitHub-tárház következő beállításait jeleníti meg:

| **Beállítás** | **Leírás**  | **Érték**  |
|:--- |:--- |:--- |
| **Tárház típusa** | Az Azure Repos Code adattár típusa. | GitHub |
| **A GitHub Enterprise használata** | Jelölőnégyzet a GitHub Enterprise kiválasztásához | nincs kiválasztva (alapértelmezett) |
| **GitHub Enterprise URL-cím** | A GitHub vállalati gyökér URL-címe (a helyi GitHub Enterprise Server esetében HTTPS-nek kell lennie). Példa: `https://github.mydomain.com`. Csak akkor szükséges, ha a **GitHub Enterprise használata** van kiválasztva | `<your GitHub enterprise url>` |                                                           
| **GitHub-fiók** | A GitHub-fiók neve. Ez a név a https: \/ /GitHub.com/{Account Name}/{repository neve} helyről érhető el. Ezen az oldalon navigálva megadhatja a GitHub-OAuth hitelesítő adatait a GitHub-fiókjában. | `<your GitHub account name>` |
| **Adattár neve**  | A GitHub-kód tárházának neve. A GitHub-fiókok git-tárházat tartalmaznak a forráskód kezeléséhez. Létrehozhat egy új tárházat, vagy használhat olyan meglévő tárházat, amely már szerepel a fiókjában. | `<your repository name>` |
| **Együttműködési ág** | A GitHub-együttműködési ág, amely a közzétételhez használatos. Alapértelmezés szerint a főkiszolgálója. Módosítsa ezt a beállítást abban az esetben, ha egy másik ág erőforrásait közzé szeretné tenni. | `<your collaboration branch>` |
| **Gyökérmappa** | A legfelső szintű mappa a GitHub-együttműködési ágban. |`<your root folder name>` |
| **Meglévő erőforrások importálása adattárba** | Megadja, hogy a rendszer importálja-e a meglévő erőforrásokat a szinapszis studióból egy git-tárházba. Jelölje be a jelölőnégyzetet a munkaterület-erőforrások (a készletek kivételével) importálásához a társított git-tárházba JSON formátumban. Ez a művelet egyenként exportálja az egyes erőforrásokat. Ha a jelölőnégyzet nincs bejelölve, a meglévő erőforrások nem kerülnek importálásra. | Kijelölt (alapértelmezett) |
| **Erőforrás importálása ebbe az elágazásba** | Válassza ki az erőforrásokat (SQL-szkript, jegyzetfüzet, Spark-feladatdefiníció, adatkészlet, adatfolyam stb.). 

### <a name="github-organizations"></a>GitHub-szervezetek

A GitHub-szervezethez való csatlakozáshoz a szervezetnek engedélyt kell adnia a szinapszis Studiónak. A szervezeten belül rendszergazdai engedélyekkel rendelkező felhasználónak el kell végeznie az alábbi lépéseket.

#### <a name="connecting-to-github-for-the-first-time"></a>Csatlakozás a GitHubhoz első alkalommal

Ha első alkalommal csatlakozik a GitHubhoz a szinapszis studióból, kövesse az alábbi lépéseket egy GitHub-szervezethez való csatlakozáshoz.

1. A git-konfiguráció ablaktáblán adja meg a szervezet nevét a *GitHub-fiók* mezőben. Megjelenik a GitHubba való bejelentkezésre vonatkozó kérés. 

1. Jelentkezzen be a felhasználói hitelesítő adataival.

1. A rendszer felkéri, hogy engedélyezze a szinapszis-t az *Azure szinapszis* nevű alkalmazásként. Ezen a képernyőn megjelenik egy lehetőség, hogy engedélyt adjon a szinapszisnak a szervezet elérésére. Ha nem látja az engedélyt, kérje meg a rendszergazdát, hogy manuálisan adja meg az engedélyt a GitHubon keresztül.

Ha követte ezeket a lépéseket, a munkaterület a szervezeten belüli nyilvános és privát adattárakhoz is csatlakozhat. Ha nem tud csatlakozni, próbálja meg törölni a böngésző gyorsítótárát, és próbálkozzon újra.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Már csatlakoztatva van a GitHubhoz egy személyes fiók használatával

Ha már csatlakozott a GitHubhoz, és csak egy személyes fiók elérésére vonatkozó engedélyt kapott, kövesse az alábbi lépéseket egy szervezet engedélyeinek megadásához.

1. Lépjen a GitHubra, és nyissa meg a **beállításokat**.

    ![GitHub-beállítások megnyitása](media/github-settings.png)

1. Válassza az **alkalmazások** lehetőséget. Az **OAuth-alkalmazások engedélyezése** lapon az *Azure szinapszisot* kell látnia.

    ![OAuth-alkalmazások engedélyezése](media/authorize-app.png)

1. Válassza ki az *Azure szinapszisot* , és adja meg a szervezethez való hozzáférést.

    ![Szervezeti engedély megadása](media/grant-organization-permission.png)

A lépések elvégzése után a munkaterület a szervezeten belüli nyilvános és privát adattárakhoz is csatlakozhat.

## <a name="version-control"></a>Verziókövetés

A verziókövetés-rendszerek (más néven a _verziókövetés_) lehetővé teszik a fejlesztők számára a kód és a változások követését. A verziókövetés elengedhetetlen eszköz a több fejlesztő projektjeihez.

### <a name="creating-feature-branches"></a>Szolgáltatási ágak létrehozása

A szinapszis studióhoz társított minden git-tárház együttműködési ágat tartalmaz. ( `main` vagy `master` az alapértelmezett együttműködési ág). A felhasználók létrehozhatnak szolgáltatási ágakat is, ha a ág legördülő menüben az **+ új ág** lehetőségre kattintanak. Miután megjelenik az új ág ablaktábla, adja meg a szolgáltatási ág nevét.

![Új ág létrehozása](media/create-new-branch.png)

Ha készen áll a szolgáltatással kapcsolatos változások egyesítésére az együttműködési ágra, kattintson az ág legördülő listára, és válassza a **pull-kérelem létrehozása** lehetőséget. Ezzel a művelettel megadhatja a git-szolgáltatót, ahol a pull-kérelmeket, a kódok felülvizsgálatát és az együttműködési ág módosításait egyesítheti. Csak a szinapszis szolgáltatásban lehet közzétenni az együttműködési ágban. 

![Új lekéréses kérelem létrehozása](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Közzétételi beállítások konfigurálása

Alapértelmezés szerint a szinapszis Studio létrehozza a munkaterület-sablonokat, és elmenti őket egy nevű ágra `workspace_publish` . Egyéni közzétételi ág konfigurálásához vegyen fel egy `publish_config.json` fájlt az együttműködési ág gyökérmappa mappájába. Közzétételkor a szinapszis Studio beolvassa ezt a fájlt, megkeresi a mezőt `publishBranch` , és menti a munkaterület-sablon fájljait a megadott helyre. Ha a ág nem létezik, a szinapszis Studio automatikusan létrehozza azt. És példa arra, hogy a fájl milyen módon néz ki:

```json
{
    "publishBranch": "workspace_publish"
}
```

Az Azure szinapszis Studio egyszerre csak egy közzétételi ágat tartalmazhat. Új közzétételi ág megadásakor az előző közzétételi ág nem lett törölve. Ha el szeretné távolítani az előző közzétételi ágat, törölje manuálisan.


### <a name="publish-code-changes"></a>Kód módosításának közzététele

Az együttműködési ág módosításainak egyesítése után a **Közzététel** gombra kattintva manuálisan közzéteheti a kód módosításait az együttműködési ágban a szinapszis szolgáltatásban.

![Változások közzétevése](media/gitmode-publish.png)

Ekkor megnyílik egy oldalsó ablaktábla, ahol megerősítheti, hogy a közzétételi ág és a függőben lévő módosítások helyesek. A módosítások ellenőrzése után kattintson az **OK** gombra a közzététel megerősítéséhez.

![A megfelelő közzétételi ág megerősítése](media/publish-change.png)

> [!IMPORTANT]
> Az együttműködési ág nem jellemző a szolgáltatásban üzembe helyezett funkciókra. Az együttműködési ág módosításait *manuálisan kell közzétenni* .

## <a name="switch-to-a-different-git-repository"></a>Váltás másik git-tárházra

Ha másik git-tárházra szeretne váltani, lépjen a git-konfiguráció lapra a felügyeleti központ **forrás vezérlőelem** területén. Válassza a **Leválasztás** lehetőséget. 

![Git ikon](media/remove-repository.png)

Adja meg a munkaterület nevét, és kattintson a **Leválasztás** elemre a munkaterülethez társított git-tárház eltávolításához.

Miután eltávolította az aktuális tárházhoz való társítást, beállíthatja, hogy a git-beállítások egy másik tárházat használjanak, majd meglévő erőforrásokat importáljon az új tárházba.

> [!IMPORTANT]
> A git-konfiguráció egy munkaterületről való eltávolítása nem töröl semmit az adattárból. A szinapszis munkaterület minden közzétett erőforrást tartalmaz majd. Továbbra is szerkesztheti a munkaterületet közvetlenül a szolgáltatáson.

## <a name="best-practices-for-git-integration"></a>Ajánlott eljárások a git-integrációhoz

-   **Engedélyek**. Miután egy git-tárház csatlakozott a munkaterülethez, bárki, aki hozzáférhet a git-adattárhoz a munkaterület bármely szerepkörével, frissítheti az összetevőket, például az SQL-parancsfájlt, a jegyzetfüzetet, a Spark-feladat definícióját, az adatkészletet, a adatfolyam és a folyamatot a git módban. Általában nem szeretné, hogy minden csapattag rendelkezzen a munkaterület frissítéséhez szükséges engedélyekkel. Csak a git-tárház engedélyezése a munkaterület-összetevőhöz tartozó szerzők számára. 
-   **Együttműködés**. Javasoljuk, hogy ne engedélyezze a közvetlen bejelentkezéseket az együttműködési ágban. Ez a korlátozás segít megakadályozni a hibákat, mivel minden bejelentkezés a [szolgáltatási ágak létrehozása](source-control.md#creating-feature-branches)című témakörben leírt lekéréses kérelem-felülvizsgálati folyamaton halad át.
-   **Szinapszis élő üzemmód**. A git módban való közzététel után az összes változás a szinapszis élő módban jelenik meg. A szinapszis élő módban a közzététel le van tiltva. És megtekintheti az összetevők élő módban való futtatását, ha a megfelelő engedélyt kapta. 
-   Összetevők **szerkesztése a Studióban**. A szinapszis Studio az egyetlen hely, amellyel engedélyezheti a munkaterület forrás-és szinkronizálási módosításait a git szolgáltatásban. Az SDK-val, a PowerShell-lel végzett módosítások nem lesznek szinkronizálva a git használatával. Javasoljuk, hogy mindig szerkessze az összetevőt a Studióban, ha a git engedélyezve van.

## <a name="troubleshooting-git-integration"></a>A git-integráció hibaelhárítása

### <a name="access-to-git-mode"></a>Hozzáférés a git-módhoz 

Ha engedélyt kapott a munkaterülethez társított GitHub git-tárházhoz, akkor a git-üzemmódhoz nem férhet hozzá: 

1. Törölje a gyorsítótárat, és frissítse az oldalt. 

1. Jelentkezzen be a GitHub-fiókjába.

### <a name="stale-publish-branch"></a>Elavult közzétételi ág

Ha a közzétételi ág nincs szinkronban az együttműködési ágban, és a legutóbbi közzététel ellenére elavult erőforrásokat tartalmaz, próbálkozzon az alábbi lépésekkel:

1. Az aktuális git-tárház eltávolítása

1. Konfigurálja újra a git-t ugyanazzal a beállításokkal, de győződjön meg arról, hogy a **meglévő erőforrások importálása a tárházba** jelölőnégyzet be van jelölve, és válassza ki ugyanazt az ágat.  

1. Lekéréses kérelem létrehozása a változások együttműködési ágba való egyesítéséhez 

## <a name="unsupported-features"></a>Nem támogatott funkciók

- A szinapszis Studio nem engedélyezi a bekötések vagy az erőforrások szelektív közzétételét. 
- A szinapszis Studio nem támogatja a véglegesítő üzenet testreszabását.
- A Studio alkalmazásban a DELETE művelet megtervezésével közvetlenül a git-ben lesz végrehajtva

## <a name="next-steps"></a>Következő lépések

* A folyamatos integráció és üzembe helyezés megvalósításához tekintse meg a [folyamatos integrációt és teljesítést (CI/CD)](continuous-integration-deployment.md).