---
title: Git-tárház hozzáadása laborhoz Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá egy GitHub-vagy Azure DevOps Services git-tárházat az egyéni összetevők forrásához Azure DevTest Labsban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 225aae5e0a017b711f29a47829b06f8bb7a6a8ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483142"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Git-tárház hozzáadása egyéni összetevők és Resource Manager-sablonok tárolásához

[Létrehozhat egyéni](devtest-lab-artifact-author.md) összetevőket a laborban található virtuális gépekhez, vagy [használhat Azure Resource Manager sablonokat egyéni tesztkörnyezet létrehozásához](devtest-lab-create-environment-from-arm.md). Hozzá kell adnia egy privát git-tárházat a csapat által létrehozott összetevőkhöz vagy Resource Manager-sablonokhoz. A tárház a [githubon](https://github.com) vagy az [Azure DevOps-szolgáltatásokban](https://visualstudio.com)is üzemeltethető.

Olyan GitHub- [tárházat](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) is kínálunk, amelyet a-ként telepíthet, vagy testre szabhatja őket a laborban. Ha egy összetevőt testreszab vagy hoz létre, nem tudja tárolni az összetevőt a nyilvános tárházban. Létre kell hoznia egy saját privát tárházat az egyéni összetevőkhöz és a létrehozott összetevőkhöz. 

Amikor létrehoz egy virtuális gépet, mentheti a Resource Manager-sablont, testreszabhatja, ha kívánja, majd később további virtuális gépek létrehozásához is használhatja. Létre kell hoznia egy saját privát tárházat az egyéni Resource Manager-sablonok tárolásához.  

* A GitHub-tárház létrehozásával kapcsolatos információkért lásd: [GitHub bootcamp](https://help.github.com/categories/bootcamp/).
* Ha meg szeretné tudni, hogyan hozhat létre egy git-tárházat tartalmazó Azure DevOps Services-projektet, tekintse meg a következőt: [Kapcsolódás az Azure DevOps Serviceshez](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Az alábbi ábrán egy példa látható arra, hogyan jelenhetnek meg az összetevők a GitHubban:  

![Minta GitHub-összetevők tárháza](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>A tárház adatainak és hitelesítő adatainak beolvasása
Ahhoz, hogy egy tárházat vegyen fel a laborba, először szerezze be a legfontosabb információkat a tárházból. A következő szakaszok azt ismertetik, hogyan kérhető le a GitHubon vagy az Azure DevOps-szolgáltatásokban üzemeltetett Tárházak szükséges adatai.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>A GitHub-adattár klónozási URL-címének és a személyes hozzáférési jogkivonat beszerzése

1. Nyissa meg a GitHub-tárház kezdőlapját, amely tartalmazza az összetevő vagy a Resource Manager-sablon definícióit.
2. Válassza a **Clone or download** (Klónozás vagy letöltés) lehetőséget.
3. Az URL-cím vágólapra másolásához válassza a **https-klón URL-címe** gombot. Mentse az URL-címet későbbi használatra.
4. A GitHub jobb felső sarkában válassza ki a profil rendszerképét, majd válassza a **Beállítások** lehetőséget.
5. A bal oldali **személyes beállítások** menüben válassza a **személyes hozzáférési tokenek** lehetőséget.
6. Válassza az **új jogkivonat előállítása** lehetőséget.
7. Az **új személyes hozzáférési jogkivonat** lapon a **jogkivonat leírása** területen adjon meg egy leírást. Fogadja el az alapértelmezett elemeket a **hatókörök kiválasztása** területen, majd válassza a **jogkivonat előállítása** lehetőséget.
8. Mentse a generált tokent. Később a tokent használja.
9. A GitHub bezárásához.   
10. Folytassa a [labor összekapcsolásával a tárház](#connect-your-lab-to-the-repository) szakaszával.

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Az Azure Repos Clone URL-cím és a személyes hozzáférési jogkivonat beszerzése

1. Nyissa meg a csapat gyűjteményének kezdőlapját (például: `https://contoso-web-team.visualstudio.com` ), majd válassza ki a projektet.
2. A projekt kezdőlapján válassza a **kód** lehetőséget.
3. A klónozott URL-cím megtekintéséhez a projekt **kódja** lapon válassza a **klónozás** lehetőséget.
4. Mentse az URL-címet. Később az URL-címet használja.
5. Személyes hozzáférési jogkivonat létrehozásához a felhasználói fiók legördülő menüjében válassza a **saját profil** lehetőséget.
6. A profil adatai lapon válassza a **Biztonság** elemet.
7. A **Biztonság** lapon válassza a **Hozzáadás** lehetőséget.
8. A **személyes hozzáférési jogkivonat létrehozása** oldalon:
   1. Adja meg a jogkivonat **leírását** .
   2. A **lejárat** listán válassza a **180 nap** lehetőséget.
   3. A **fiókok** listában válassza az **összes elérhető fiók** lehetőséget.
   4. Válassza az **írásvédett** lehetőséget.
   5. Válassza a **Jogkivonat létrehozása** lehetőséget.
9. Az új jogkivonat megjelenik a **személyes hozzáférési tokenek** listájában. Válassza a **token másolása** lehetőséget, majd mentse a jogkivonat értékét későbbi használatra.
10. Folytassa a [labor összekapcsolásával a tárház](#connect-your-lab-to-the-repository) szakaszával.

## <a name="connect-your-lab-to-the-repository"></a>A labor összekötése az adattárral
1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza a **További szolgáltatások** lehetőséget, majd válassza a **DevTest Labs** lehetőséget a szolgáltatások listájából.
3. A Labs listából válassza ki a labort. 
4. Válassza **a konfiguráció és szabályzatok**  >  **Tárházak**  >  **+ Hozzáadás** lehetőséget.

    ![Az adattár hozzáadása gomb](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. A második **adattárak** lapon a következő információkat kell megadnia:
   1. **Név**. Adja meg az adattár nevét.
   2. **Git Clone URL-cím**. Adja meg a korábban a GitHub vagy az Azure DevOps Services szolgáltatásból másolt git HTTPS Clone URL-címet.
   3. **Ág**. A definíciók beszerzéséhez adja meg a ágat.
   4. **Személyes hozzáférési jogkivonat**. Adja meg azt a személyes hozzáférési jogkivonatot, amelyet korábban a GitHub vagy az Azure DevOps Services szolgáltatásból kapott.
   5. **Mappa elérési útjai** Adja meg legalább egy olyan mappa elérési útját, amely az összetevő vagy a Resource Manager-sablon definícióit tartalmazó klónozási URL-címhez képest van. Alkönyvtár megadásakor ügyeljen arra, hogy a mappa elérési útja tartalmazza a továbbítás perjelét.

      ![Adattárakhoz tartozó területek](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Kattintson a **Mentés** gombra.

### <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Az DevTest Labs meghibásodási hibáinak kiküszöbölése](devtest-lab-troubleshoot-artifact-failure.md)
* [Virtuális gép csatlakoztatása meglévő Active Directory tartományhoz egy Resource Manager-sablonnal a DevTest Labs szolgáltatásban](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Következő lépések
Miután létrehozta a privát git-tárházat, igény szerint az alábbiak közül választhat:
* Tárolja az [Egyéni](devtest-lab-artifact-author.md)összetevőket. Az új virtuális gépek létrehozásához később is felhasználhatja őket.
* [Több virtuális gépre kiterjedő környezetek és a Pásti-erőforrások létrehozása Resource Manager-sablonok használatával](devtest-lab-create-environment-from-arm.md). Ezután a sablonokat a privát tárházban tárolhatja.

Amikor létrehoz egy virtuális gépet, ellenőrizheti, hogy az összetevők vagy sablonok hozzá lettek-e adva a git-tárházhoz. Ezek azonnal elérhetők az összetevők vagy sablonok listájában. A privát tárház neve megjelenik a forrást megadó oszlopban. 
