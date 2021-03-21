---
title: CI/CD-munkafolyamat GitOps használatával – Azure arc-kompatibilis Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: tcare
ms.author: tcare
description: Ez a cikk a CI/CD-munkafolyamatok GitOps használatával történő fogalmi áttekintését tartalmazza
keywords: GitOps, Kubernetes, K8s, Azure, Helm, arc, AK, Azure Kubernetes szolgáltatás, tárolók, CI, CD, Azure DevOps
ms.openlocfilehash: a51a9f2b32f1088cec390dc4d74300a38f37b160
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121779"
---
# <a name="cicd-workflow-using-gitops---azure-arc-enabled-kubernetes"></a>CI/CD-munkafolyamat GitOps használatával – Azure arc-kompatibilis Kubernetes

Modern Kubernetes üzemelő példányok több alkalmazást, fürtöt és környezetet is magukban foglalhatnak. A GitOps segítségével könnyebben kezelheti ezeket az összetett beállításokat, és nyomon követheti a Kubernetes-környezetek kívánt állapotát a git használatával. A fürt állapotának nyomon követéséhez a közös git-eszközökkel növelheti az elszámoltathatóságot, megnövelheti a hibák vizsgálatát, és engedélyezheti az automatizálást a környezetek kezeléséhez.

Ez a fogalmi áttekintés azt ismerteti, hogy az Azure arc, az Azure Repos és az Azure-folyamatok segítségével a teljes alkalmazás-változás életciklusának GitOps valósága. [Ugorjon arra a példára](#example-workflow) , amely egyetlen alkalmazásra vált a GitOps által vezérelt Kubernetes-környezetekre.

## <a name="architecture"></a>Architektúra

Vegyünk egy vagy több Kubernetes-környezetbe telepített alkalmazást.

![GitOps CI/CD-architektúra](./media/gitops-arch.png)

### <a name="application-repo"></a>Alkalmazás-tárház
Az alkalmazás-tárház tartalmazza azt az programkódot, amelyet a fejlesztők a belső hurok során dolgoznak. Az alkalmazás üzembe helyezési sablonjai ebben a tárházban jelennek meg egy általános formában, például Helm vagy Kustomize. A környezetre jellemző értékeket nem tárolja a rendszer. A tárház módosításai egy olyan PR-vagy CI-folyamatot indítanak, amely elindítja a telepítési folyamatot.
### <a name="container-registry"></a>Container Registry
A Container Registry tartalmazza a Kubernetes-környezetekben használt összes első és harmadik féltől származó rendszerképet. Az első féltől származó alkalmazás-lemezképek címkézése emberi olvasási címkékkel és a kép felépítéséhez használt git-véglegesít. A harmadik féltől származó rendszerképek gyorsítótárazása a biztonság, a gyorsaság és a rugalmasság érdekében. Tervezze meg a biztonsági frissítések időben történő tesztelését és integrálását. További információkért tekintse meg a [nyilvános tartalmi útmutató az ACR használatáról és karbantartásáról](https://docs.microsoft.com/azure/container-registry/tasks-consume-public-content) szóló útmutatót.
### <a name="pr-pipeline"></a>PR-folyamat
Az alkalmazás-tárházhoz tartozó lekéréses kérelmeket a PR-folyamat sikeres futtatásával kezdeményezték. Ez a folyamat az alapszintű minőségi kapukat futtatja, például az alkalmazás kódjában a kivonást és az egység teszteket. A folyamat a Kubernetes-környezetbe való központi telepítéshez használt alkalmazás-és Dockerfiles és Helm-sablonokat teszteli. A Docker-rendszerképeket fel kell építeni és tesztelni kell, de nem kell leküldeni. Tartsa meg viszonylag rövid ideig a folyamat időtartamát, hogy a gyors iteráció lehetővé váljon.
### <a name="ci-pipeline"></a>CI-folyamat
Az Application CI-folyamat futtatja az összes PR-folyamat lépését, és kibővíti a tesztelési és üzembe helyezési ellenőrzéseket. A folyamat futtatható minden egyes véglegesítés esetén, vagy egy véglegesített csoporttal rendelkező rendszeres lépésszám esetén. Ebben a szakaszban olyan alkalmazás-tesztelést hajt végre, amely túl hosszú egy PR-folyamathoz. Az üzembe helyezés előkészítése után a Docker-rendszerképek leküldése a Container Registryra. A lecserélt sablon tesztelési értékekkel állítható be. A szolgáltatási futtatókörnyezetben használt képeket ezen a ponton kell kialakítani, felépíteni és tesztelni. A CI-Build esetében az összetevők közzé lesznek téve a CD-lépéshez az üzembe helyezés előkészítése során.
### <a name="flux"></a>Flux
A Flux egy olyan szolgáltatás, amely az egyes fürtökön fut, és felelős a kívánt állapot fenntartásáért. A szolgáltatás gyakran kérdezi le a GitOps-tárházat a fürt változásaihoz, és alkalmazza azokat.
### <a name="cd-pipeline"></a>CD-folyamat
A sikeres CI-buildek automatikusan elindítják a CD-folyamatot. A korábban közzétett sablonokat használja, helyettesíti a környezeti értékeket, és egy PR-t nyit meg a GitOps-tárházban, amely egy vagy több Kubernetes-fürt kívánt állapotának módosítását kéri. A fürt rendszergazdái áttekintik az állapot változását, és jóváhagyják az egyesítést a GitOps-tárházban. A folyamat ezután megvárja, amíg a PR befejeződik, ami lehetővé teszi, hogy a flow felvegye az állapot változását.
### <a name="gitops-repo"></a>GitOps-tárház
A GitOps-tárház a fürtök összes környezetének aktuálisan kívánt állapotát jelöli. A tárház bármely változását az egyes fürtökön lévő Flux-szolgáltatás vette át, és üzembe helyezi. A rendszer a kívánt állapot módosításával, felülvizsgálatával és egyesítésével hozza létre a lekéréses kérelmeket. Ezek a kérelmek mind a telepítési sablonok, mind az eredményül kapott Kubernetes-jegyzékek változásait tartalmazzák. Az alacsony szintű renderelt jegyzékek lehetővé teszik a változások körültekintő ellenőrzését a sablon szintjén általában láthatatlanként.
### <a name="kubernetes-clusters"></a>Kubernetes-fürtök
Legalább egy Azure arc-kompatibilis Kubernetes-fürt az alkalmazás által igényelt különböző környezeteket szolgálja ki. Egy fürt például különböző névtereken keresztül is kiszolgálhatja a fejlesztési és a QA-környezetet. A második fürt megkönnyíti a környezetek elkülönítését és a részletes szabályozást.
## <a name="example-workflow"></a>Példa munkafolyamat
Alkalmazás-fejlesztőként Alice:
* Az alkalmazás kódjának írása.
* Meghatározza, hogyan futtathatja az alkalmazást egy Docker-tárolóban.
* Meghatározza azokat a sablonokat, amelyek a tárolót és a függő szolgáltatásokat futtatják egy Kubernetes-fürtben.

Míg Alice tudja, hogy az alkalmazásnak több környezetben kell futnia, nem ismeri az egyes környezetek konkrét beállításait.

Tegyük fel, hogy Alice egy alkalmazás módosítását kívánja módosítani, amely megváltoztatja az alkalmazás központi telepítési sablonjában használt Docker-rendszerképet.

1. Alice megváltoztatja a telepítési sablont, leküldi azt egy távoli ágra az alkalmazás-tárházban, és megnyit egy kérelmet a felülvizsgálathoz.
2. Alice kéri a csapatot, hogy tekintse át a változást.
    * A PR-folyamat érvényesítést futtat.
    * A sikeres folyamat futtatása után a csapat kijelentkezik, és a módosítás egyesítve lesz.
3. A CI-folyamat ellenőrzi Alice módosításait, és sikeresen befejeződik.
    * A módosítás biztonságos a fürtön való üzembe helyezésre, és az összetevők a CI-folyamat futtatására lesznek mentve.
4. Alice módosítása egyesít, és elindítja a CD-folyamatot.
    * A CD-folyamat felveszi az Alice CI-folyamatának futtatásával tárolt összetevőket.
    * A CD-folyamat a környezettel kapcsolatos értékekkel helyettesíti a sablonokat, és a GitOps-tárházban lévő meglévő fürt állapotával kapcsolatos változásokat hajtja végre.
    * A CD-folyamat létrehoz egy PR-t a GitOps-tárházban a fürt állapotának kívánt módosításaival.
5. Alice csapata felülvizsgálja és jóváhagyja a PR-t.
    * A változást a rendszer a környezetnek megfelelő cél ágra egyesíti.
6. Percek alatt a Flux a GitOps-tárház változásait észleli, és lekéri Alice változásait.
    * A Docker-rendszerkép módosítása miatt az Application Pod frissítést igényel.
    * A Flux alkalmazza a módosítást a fürtön.
7. Alice ellenőrzi, hogy az alkalmazás-végpont sikeresen befejeződött-e.
   > [!NOTE]
   > Az üzembe helyezést célzó további környezetek esetén a CD-folyamat megismétli a következő környezethez tartozó PR-t, és megismétli a 4-7-es lépést. A folyamatnak számos további jóváhagyásra van szüksége a kockázatosabb környezetekben vagy környezetekben, például a biztonsággal kapcsolatos változásokhoz vagy éles környezetekhez.
8.  Ha az összes környezet sikeres telepítést kapott, a folyamat befejeződik.

## <a name="next-steps"></a>Következő lépések
További információ a fürt és a git-tárház közötti kapcsolatok létrehozásáról [konfigurációs erőforrásként az Azure arc-kompatibilis Kubernetes](./conceptual-configurations.md)
