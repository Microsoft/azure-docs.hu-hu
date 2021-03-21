---
title: A CI/CD és az Azure Dev Spaces használata
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Ismerje meg, hogyan állíthat be folyamatos integrációt/folyamatos üzembe helyezést az Azure DevOps és az Azure dev Spaces használatával
keywords: Docker, Kubernetes, Azure, AK, Azure Container Service, tárolók
ms.openlocfilehash: 142f01a04066b919a81e1ad1ea95efa31f51f6ca
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548733"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>A CI/CD és az Azure Dev Spaces használata

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Ez a cikk végigvezeti a folyamatos integráció/folyamatos üzembe helyezés (CI/CD) az Azure Kubernetes szolgáltatásban (ak) való beállításán, és lehetővé teszi a dev Spaces használatát. A CI/CD to AK lehetővé teszi, hogy az alkalmazások frissítései automatikusan telepítve legyenek, amikor a rendszer leküldi a véglegesítési kódot a forrás adattárba. Ha a CI/CD-t egy dev Spaces-kompatibilis fürttel együtt használja, hasznos lehet, mert az alkalmazás naprakészen tarthatja, hogy a csapat működjön együtt.

![Példa CI/CD diagramra](../media/common/ci-cd-simple.png)

Bár ez a cikk végigvezeti Önt az Azure DevOps, ugyanazok a fogalmak érvényesek a CI/CD-rendszerekre, mint például a Jenkins, a TeamCity stb.

## <a name="prerequisites"></a>Előfeltételek
* Azure Kubernetes Service (ak) fürt az Azure dev Spaces szolgáltatással
* [Azure dev Spaces CLI telepítve](upgrade-tools.md)
* [Azure DevOps-szervezet projekttel](/azure/devops/user-guide/sign-up-invite-teammates)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Azure Container Registry [rendszergazdai fiók](../../container-registry/container-registry-authentication.md#admin-account) adatai érhetők el
* [Engedélyezze az AK-fürt számára a Azure Container Registry való lekérését](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Mintakód letöltése
Az idő kedvéért hozzunk létre egy elágazást a GitHub-adattárból. Nyissa meg a https://github.com/Azure/dev-spaces elemet, és válassza az **elágazás** lehetőséget. Az elágazási folyamat befejezése után a tárház helyi verziójának **klónozása** helyben. Alapértelmezés szerint a rendszer kiveszi a _fő_ ágat, de a _azds_updates_ ág időmegtakarítása is megtörtént, amelyet az elágazás során is át kell vinni. A _azds_updates_ ág olyan frissítéseket tartalmaz, amelyekkel manuálisan elvégezhető a dev Spaces oktatóanyag szakasza, valamint néhány előre elkészített YAML és JSON-fájl, amelyekkel EGYSZERŰSÍTHETI a CI/CD rendszer üzembe helyezését. Az alábbi parancsokkal `git checkout -b azds_updates origin/azds_updates` tekintheti meg az _azds_updates_ ágat a helyi tárházban.

## <a name="dev-spaces-setup"></a>Fejlesztői szóközök beállítása
Hozzon létre egy _fejlesztői_ nevű új helyet a `azds space select` parancs használatával. A CI/CD-folyamat a kód módosításainak leküldéséhez a _fejlesztői_ területet fogja használni. _A rendszer_ a fejlesztés alapján is létrehozza a _gyermekeket_ .

```cmd
azds space select -n dev
```

Amikor a rendszer rákérdez a szülő fejlesztői terület kiválasztására, válassza a elemet _\<none\>_ .

A fejlesztői terület létrehozása után meg kell határoznia a gazdagép utótagját. A `azds show-context` parancs használatával jelenítse meg az Azure dev Spaces bemenő vezérlő gazdagépének utótagját.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

A fenti példában a _fedcba098.EUs.azds.IO_ a gazdagép utótagja. Ezt az értéket később a kiadás definíciójának létrehozásakor használja a rendszer.

A _fejlesztői terület mindig_ a tárház legújabb állapotát fogja tartalmazni, amely egy alapkonfiguráció, így a fejlesztők a _fejlesztési_ környezetből hozhatnak létre _gyermekeket_ , hogy a nagyobb alkalmazás kontextusában tesztelik elszigetelt módosításaikat. Ezt a koncepciót részletesebben tárgyaljuk a dev Spaces oktatóanyagokban.

## <a name="creating-the-build-definition"></a>A Build definíciójának létrehozása
Nyissa meg az Azure DevOps-csapat projektjét egy böngészőben, és navigáljon a _folyamatok_ szakaszhoz. Először kattintson az Azure DevOps-webhely jobb felső sarkában található profilra fényképre, nyissa meg az előzetes verziójú szolgáltatások ablaktáblát, és tiltsa le az _új YAML-folyamat létrehozási élményét_:

![Előzetes verziójú funkciók ablaktábla megnyitása](../media/common/preview-feature-open.png)

A letiltási lehetőség:

![Új YAML-folyamat létrehozási élménye lehetőség](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Az Azure DevOps _új YAML-folyamat létrehozási_ funkciójának előzetes verziója ütközik az előre definiált Build-folyamatok létrehozásával. Most le kell tiltania az előre definiált build-folyamat üzembe helyezéséhez.

Az _azds_updates_ ág tartalmaz egy egyszerű [Azure pipeline-YAML](/azure/devops/pipelines/yaml-schema?tabs=schema) , amely a *mywebapi* és a *webfrontendhez* szükséges Build-lépéseket határozza meg.

A választott nyelvtől függően a folyamat YAML a következőhöz hasonló elérési úton lett bejelentkezve: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Folyamat létrehozása ebből a fájlból:
1. A DevOps projekt főoldalán navigáljon a folyamatok > builds elemre.
1. Válassza az **új** létrehozási folyamat létrehozása lehetőséget.
1. Válassza a **GitHub** lehetőséget forrásként, ha szükséges, engedélyezze a GitHub-fiókját, majd válassza ki a _azds_updates_ ágat a _dev-Spaces_ minta alkalmazás-tárház elágazó verziójából.
1. Sablonként válassza a **konfiguráció kódként** vagy a **YAML** lehetőséget.
1. Ekkor megjelenik a build-folyamat konfigurációs lapja. Ahogy fent említettük, a **...** gomb használatával navigáljon a **YAML** elérési útjához. Például: `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Lépjen a **változók** lapra.
1. Manuálisan adja hozzá a _dockerId_ változóként, amely a [Azure Container Registry rendszergazdai fiókjának](../../container-registry/container-registry-authentication.md#admin-account)felhasználóneve. (A cikk előfeltételei című cikkben szerepel)
1. Manuálisan adja hozzá a _dockerPassword_ -t változóként, amely a [Azure Container Registry rendszergazdai fiókjának](../../container-registry/container-registry-authentication.md#admin-account)jelszava. Ügyeljen arra, hogy biztonsági okokból a _dockerPassword_ legyen titkosként megadva (a zárolás ikon kiválasztásával).
1. Válassza a **mentés & üzenetsor** lehetőséget.

Most már rendelkezik egy CI-megoldással, amely automatikusan felépíti a *mywebapi* -t és a *webfrontendt* a GitHub-elágazás _azds_updates_ ágra. Ellenőrizheti, hogy a Docker-rendszerképek leküldése megtörtént-e a Azure Portalra való navigálással, a Azure Container Registry kiválasztásával és a **Tárházak** lapon való böngészéssel. Több percet is igénybe vehet, amíg a lemezképek felépíthetnek és megjelennek a tároló beállításjegyzékében.

![Azure Container Registry adattárak](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>A kiadás definíciójának létrehozása

1. A DevOps projekt főoldalán navigáljon a folyamatok > kiadásokhoz
1. Ha olyan vadonatúj DevOps-projektet használ, amely még nem tartalmaz kiadási definíciót, először létre kell hoznia egy üres kiadási definíciót a továbblépés előtt. Az importálási lehetőség addig nem jelenik meg a felhasználói felületen, amíg nem rendelkezik meglévő kiadási definícióval.
1. A bal oldalon kattintson az **+ új** gombra, majd a **folyamat importálása** elemre.
1. Kattintson a **Tallózás** gombra, és válassza ki `samples/release.json` a projektet.
1. Kattintson az **OK** gombra. Figyelje meg, hogy a folyamat ablaktábla a kiadás definíciójának szerkesztési lapjával lett betöltve. Figyelje meg, hogy van-e olyan piros figyelmeztető ikon, amely a még konfigurálni kívánt fürtre vonatkozó adatokat jelzi.
1. A folyamat ablaktábla bal oldalán kattintson az **összetevő hozzáadása** buborékra.
1. A **forrás** legördülő menüben válassza ki a korábban létrehozott Build folyamatot.
1. Az **alapértelmezett verziónál** válassza **a legutóbbi lehetőséget a build folyamat alapértelmezett ága címkékkel**.
1. Hagyja üresen a **címkéket** .
1. Állítsa a **forrás aliast** a következőre: `drop` . A **forrás alias** értékét az előre meghatározott kiadási feladatok használják, így azt be kell állítani.
1. Kattintson a **Hozzáadás** parancsra.
1. Most kattintson a villám ikonra az újonnan létrehozott összetevő `drop` -forráson, az alábbi ábrán látható módon:

    ![Kiadási összetevő folyamatos üzembe helyezésének beállítása](../media/common/release-artifact-cd-setup.png)
1. Engedélyezze a **folyamatos üzembe helyezési triggert**.
1. Vigye a kurzort a **folyamat** melletti **feladatok** lapra, és kattintson a _dev_ (fejlesztés) elemre a _fejlesztői_ fázis feladatainak szerkesztéséhez.
1. Ellenőrizze, **Azure Resource Manager** van-e kiválasztva a **kapcsolattípus területen.** a Red: Release definition Setup (vörös) beállításban látható három legördülő menü jelenik meg ![](../media/common/release-setup-tasks.png)
1. Válassza ki azt az Azure-előfizetést, amelyet az Azure dev Spaces használatával használ. Az **Engedélyezés** elemre is szükség lehet.
1. Válassza ki azt az erőforráscsoportot és fürtöt, amelyet az Azure dev Spaces használatával használ.
1. Kattintson az **ügynök feladata** elemre.
1. Válassza az **üzemeltetett Ubuntu 1604** az **ügynök-készletben** lehetőséget.
1. Vigye a kurzort a fent található **feladatok** választó fölé, kattintson a _Prod_ elemre a _gyártási_ fázis feladatainak szerkesztéséhez.
1. Ellenőrizze, **Azure Resource Manager** van-e kiválasztva a **kapcsolattípus területen.** Válassza ki azt az Azure-előfizetést, erőforráscsoportot és fürtöt, amelyet az Azure dev Spaces használatával használ.
1. Kattintson az **ügynök feladata** elemre.
1. Válassza az **üzemeltetett Ubuntu 1604** az **ügynök-készletben** lehetőséget.
1. Kattintson a **változók** lapra a kiadás változóinak frissítéséhez.
1. Frissítse a **DevSpacesHostSuffix** értékét **UPDATE_MEról** a gazdagép-utótagra. A gazdagép utótagja akkor jelenik meg, ha korábban futtatta a `azds show-context` parancsot.
1. Kattintson a jobb felső sarokban található **Mentés** elemre, majd **az OK gombra**.
1. Kattintson a **+ kiadás** elemre (a Mentés gomb mellett), és **hozzon létre egy kiadást**.
1. Az összetevők területen **ellenőrizze, hogy ki van-e** választva a build-folyamat legújabb buildje.
1. Kattintson a **Létrehozás** lehetőségre.

Az automatizált kiadási folyamat most elindul, üzembe helyezi a *mywebapi* és a *webfrontend* -diagramokat a Kubernetes-fürtön a _fejlesztői_ legfelső szintű helyen. A kiadás előrehaladását az Azure DevOps webes portálján követheti nyomon:

1. Navigáljon a **kiadások** szakaszra a **folyamatok** területen.
1. Kattintson a minta alkalmazás kiadási folyamatára.
1. Kattintson a legújabb kiadás nevére.
1. Vigye az **egérmutatót** a **szakaszok szakaszban** , majd kattintson a **naplók** elemre.

A kiadás akkor történik meg, amikor az összes feladat befejeződött.

> [!TIP]
> Ha a kiadás sikertelen, például a *frissítés meghiúsult: időtúllépés történt a feltételre való várakozás közben*, próbálja meg megvizsgálni a hüvelyt a fürtben [a Kubernetes-irányítópult használatával](../../aks/kubernetes-dashboard.md). Ha úgy látja, hogy a hüvelyek nem tudják elindítani a következő hibaüzeneteket: *nem sikerült lekérni a (z) "azdsexample.azurecr.IO/mywebapi:122" nevű rendszerképet: RPC-hiba: kód = ismeretlen desc = hiba a démontól: Get https: \/ /azdsexample.azurecr.IO/v2/mywebapi/Manifests/122: jogosulatlan: hitelesítés szükséges*, lehet, hogy a fürt nem jogosult lekérni a Azure Container Registry. Győződjön meg arról, hogy végrehajtotta a [hitelesítő AK-fürt engedélyezését az Azure Container Registry előfeltételének lekéréséhez](../../aks/cluster-container-registry-integration.md) .

Most már rendelkezik egy teljesen automatizált CI/CD-folyamattal a dev Spaces minta-alkalmazások GitHub-villájában. Minden alkalommal, amikor véglegesíti és leküldi a kódot, a build folyamat létrehozza és leküldi a *mywebapi* és a *webfrontend* -LEMEZKÉPeket az egyéni ACR-példányba. Ezután a kiadási folyamat üzembe helyezi a Helm-diagramot minden egyes alkalmazáshoz a dev Spaces-kompatibilis fürt _fejlesztői_ területére.

## <a name="accessing-your-_dev_-services"></a>A _fejlesztői_ szolgáltatások elérése
Az üzembe helyezést követően a *webfrontend* _fejlesztői_ verziója a következőhöz hasonló nyilvános URL-címmel érhető el: `http://dev.webfrontend.fedcba098.eus.azds.io` . Ezt az URL-címet a következő parancs futtatásával érheti el `azds list-uri` : 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Üzembe helyezés éles környezetben

Ha az oktatóanyagban létrehozott CI/CD rendszer használatával manuálisan szeretné előléptetni egy adott kiadást a _prodra_ :
1. Navigáljon a **kiadások** szakaszra a **folyamatok** területen.
1. Kattintson a minta alkalmazás kiadási folyamatára.
1. Kattintson a legújabb kiadás nevére.
1. Vigye az egérmutatót a **gyártási** mező fölé a **szakaszok szakaszban** , majd kattintson a **telepítés** elemre.
    ![Előléptetés éles környezetbe](../media/common/prod-promote.png)
1. Vigye az egérmutatót a **gyártási** mező fölé a **szakaszok szakaszban** , majd kattintson a **naplók** elemre.

A kiadás akkor történik meg, amikor az összes feladat befejeződött.

A CI/CD-folyamat _gyártási_ fázisa egy terheléselosztó használatával használja a dev Spaces beáramló vezérlőt, hogy hozzáférést _biztosítson a termelési szolgáltatásokhoz_ . A _gyártási_ szakaszban üzembe helyezett szolgáltatások a DNS-nevek helyett IP-címekként érhetők el. Éles környezetben dönthet úgy, hogy saját DNS-konfiguráció alapján saját bevezetési vezérlőt hoz létre a szolgáltatások üzemeltetéséhez.

A webfrontend szolgáltatás IP-címének meghatározásához kattintson a  **webfrontend nyilvános IP-címének kinyomtatása** lépésre a napló kimenetének kibontásához. Használja a napló kimenetében megjelenő IP-címet a **webfrontend** alkalmazás eléréséhez.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Fejlesztői tárolóhelyek az éles környezetben
Bár a dev Spaces Instrumentation úgy lett kialakítva, hogy _nem_ az alkalmazás normál működésének megfelelően fusson, javasoljuk, hogy az éles munkaterheléseket olyan Kubernetes-névtérben futtassa, amely nincs engedélyezve a dev Spaces szolgáltatásban. Ha ezt a Kubernetes-névteret használja, akkor az éles névteret a parancssori felület használatával kell létrehoznia `kubectl` , vagy engedélyeznie kell a CI/CD rendszer számára az első Helm-telepítés során. _Ha kijelöli_ vagy egyéb módon létrehoz egy helyet a dev Spaces-eszközök használatával, a dev Spaces-eszközöket is hozzáadja a névtérhez.

Íme egy példa a szolgáltatások fejlesztését, a "dev" környezetet _és_ a termelést támogató, egyetlen Kubernetes-fürtben:

![Példa a névtér struktúrájára](../media/common/cicd-namespaces.png)

> [!Tip]
> Ha már létrehozott egy `prod` helyet, és egyszerűen csak szeretné kizárni a fejlesztői helyek rendszerállapotból (törlés nélkül), ezt a következő fejlesztői Spaces CLI-paranccsal teheti meg:
>
> `azds space remove -n prod --no-delete`
>
> Előfordulhat, hogy törölnie kell az összes hüvelyt a `prod` névtérben, miután ezt megtette, így a fejlesztői szóközök kialakítása nélkül is újra létrehozhatók.

## <a name="next-steps"></a>Következő lépések

További információ az Azure dev Spaces működéséről.

> [!div class="nextstepaction"]
> [Az Azure Dev Spaces működése](../how-dev-spaces-works.md)
