---
title: Migrálás a Bridge to Kubernetesre
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Ismerteti az Azure Dev Spacesből a Bridge to Kubernetesbe irányuló migrálási folyamatot
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók, Bridge to Kubernetes
ms.openlocfilehash: 8ffb7693ff223a9cb952964ded1e6967ceeb326e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499291"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Migrálás a Bridge to Kubernetesre

> [!IMPORTANT]
> Az Azure Dev Spaces 2023. október 31-én lesz kivezetve. Az ügyfeleknek át kell lépniük a Bridge to Kubernetes ügyfélfejlesztői eszközre.
>
> Az Azure Dev Spaces célja az volt, hogy a felhasználókat a Kubernetesen történő fejlesztéshez is megterjesszen. Az Azure Dev Spaces megközelítésének jelentős visszafogalmazása a felhasználókra hárult, hogy megértsék a Docker- és Kubernetes-konfigurációkat, valamint a Kubernetes üzembe helyezési fogalmait. Idővel az is nyilvánvalóvá vált, hogy az Azure Dev Spaces megközelítése nem csökkenti hatékonyan a kubernetes belső ciklusok fejlesztésének sebességét. A Kuberneteshez való híd hatékonyan csökkenti a belső ciklusok fejlesztésének sebességét, és szükségtelenül terheli a felhasználókat.
>
> Az alapvető küldetés változatlan marad: A mikroszolgáltatás-kód fejlesztéséhez, teszteléséhez és hibakereséséhez szükséges legjobb élmények kiépítése a nagyobb alkalmazás kontextusában.

A Bridge to Kubernetes (Kuberneteshez való híd) az Azure Dev Spaces számos fejlesztési forgatókönyvének könnyebb alternatívája. A Bridge to Kubernetes egy csak ügyféloldali élmény, amely bővítményeket használ a Visual Studio [][vs]   és Visual Studio [Code-ban.][vsc]  

A Bridge to Kubernetes (Híd a Kuberneteshez) szolgáltatás segít a fejlesztési élményben azáltal, hogy lehetővé teszi, hogy egy már létrehozott Kubernetes-alkalmazás tartalmazzon egy, a helyi fejlesztői munkaállomáson futó szolgáltatást. A Dev Spacestől eltérően a Bridge to Kubernetes csökkenti a belső hurkok összetettségeit azáltal, hogy a docker- és Kubernetes-konfigurációk létrehozásának szükségét azáltal csökkenti, hogy a fejlesztők kizárólag a mikroszolgáltatás-kód üzleti logikáját összpontosítják.

A Bridge to Kubernetes (Híd a Kuberneteshez) segítségével iterálhatja a fejlesztői számítógépen futó kódot, miközben a Kubernetes-környezet függőségeit és meglévő konfigurációját használja. Ezzel szemben az Azure Dev Spaces üzembe helyezheti a mikroszolgáltatást a Kubernetes-környezetben, mielőtt távolról hibakeresést futtathat a szolgáltatáson, és iterálhatja a kódot.

Ez a cikk összehasonlítja az Azure Dev Spacest és a Bridge to Kubernetes-t, valamint útmutatást nyújt az Azure Dev Spacesből a Bridge to Kubernetesbe való áttelepítéshez.

## <a name="development-approaches"></a>Fejlesztési megközelítések

![Fejlesztési megközelítések](media/migrate-to-btk/development-approaches.svg)

Az Azure Dev Spaces segített a Kubernetes-fejlesztőknek a közvetlenül az AKS-fürtjükben futó kódokkal való munkában, így nem volt szükség olyan helyi környezetre, amely nem hasonlít az üzembe helyezett környezetre. Ez a megközelítés javította a fejlesztés bizonyos aspektusait, de az Azure Dev Spaces használatának elkezdődte előtt bevezette az olyan további fogalmak tanulásának és fenntartásának előfeltételeit is, mint a Docker, a Kubernetes és a Helm.

A Bridge to Kubernetes (Kuberneteshez való híd) lehetővé teszi a fejlesztők számára, hogy közvetlenül a fejlesztői számítógépükön dolgozva kommunikálhatnak a fürt többi részével. Ez a megközelítés kihasználja a kód közvetlenül a fejlesztői számítógépeken való futtatásának ismerete és gyorsasága előnyeit, miközben megosztja a fürt által biztosított függőségeket és környezetet. Ez a megközelítés a Kubernetesben való futtatásból származó hűséget és skálázást is kihasználja.

## <a name="feature-comparison"></a>Funkciók összehasonlítása

Az Azure Dev Spaces és a Bridge to Kubernetes hasonló funkciókkal rendelkezik, és több területen is eltérnek:

| Követelmény  | Azure Dev Spaces  | Bridge to Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | 15 Azure-régióban | Bármely AKS-szolgáltatási régió    |
| **Biztonság** |
| A fürtön szükséges biztonsági hozzáférés  | AKS-fürt közreműködője  | Kubernetes RBAC – Üzembe helyezési frissítés   |
| A fejlesztési számítógépen szükséges biztonsági hozzáférés  | N/A  | Helyi rendszergazda /sudo   |
| **Használhatóság** |
| Független a Kubernetestől és a Docker-összetevőktől  | Nem  | Igen   |
| A módosítások automatikus visszaállítás, hibakeresés után  | Nem  | Igen   |
| **Támogatott ügyfél-eszközök** |
| A 2019 Visual Studio együtt működik  | Igen  | Igen   |
| A Visual Studio Code-kóddal működik  | Igen  | Igen   |
| Működik a CLI-vel  | Igen  | Nem   |
| **Operációs rendszer kompatibilitása** |
| A Windows 10  | Igen  | Igen  |
| Linux rendszeren működik  | Igen  | Igen  |
| MacOS rendszeren működik  | Igen  | Igen  |
| **Képességek** |
| Fejlesztői elkülönítés vagy csapatfejlesztés  | Igen  | Igen  |
| Környezeti változók szelektív felülírása  | Nem  | Igen  |
| Dockerfile és Helm-diagram létrehozása  | Igen  | Nem  |
| Kód állandó üzembe helyezése a Kubernetesben  | Igen  | Nem  |
| Távoli hibakeresés Kubernetes-podban  | Igen  | Nem  |
| Helyi hibakeresés, csatlakoztatva a Kuberneteshez  | Nem  | Igen  |
| Egyszerre több szolgáltatás hibakeresése ugyanazon a munkaállomáson  | Igen  | Igen  |

## <a name="kubernetes-inner-loop-development"></a>Belső Kubernetes-ciklus fejlesztése

Az Azure Dev Spaces és a Bridge to Kubernetes közötti legnagyobb különbség a kód futtatása. Az Azure Dev Spaces segít a mikroszolgáltatás-kód fejlesztésében és hibakeresésében, de ehhez a kódot a fürtön kell futtatnia. A Bridge to Kubernetes (Kuberneteshez való híd) lehetővé teszi a mikroszolgáltatás-kód fejlesztését és hibakeresését közvetlenül a fejlesztői számítógépen, miközben a Kubernetesben futó nagyobb alkalmazás kontextusában marad. A Kubernetes-híd kibővíti a Kubernetes-fürt szegélyhálózatát, és lehetővé teszi a helyi folyamatok számára, hogy a konfigurációt a Kubernetestől öröklik.

![Belső hurok fejlesztése](media/migrate-to-btk/btk-graphic-non-isolated.gif)

A Bridge to Kubernetes használata esetén létrejön egy hálózati kapcsolat a fejlesztői számítógép és a fürt között.A kapcsolat teljes élettartama alatt a rendszer hozzáad egy proxyt a fürthöz a Kubernetes üzemelő példánya helyett, amely átirányítja a kéréseket a szolgáltatáshoz a fejlesztői számítógéphez. A kapcsolat megszakadása esetén az alkalmazástelepítés visszaáll a fürtön futó üzemelő példány eredeti verziójára. Ez a megközelítés eltér attól, ahogy az Azure Dev Spaces működik, amelyben a kód szinkronizálva van a fürthöz, majd felépíti, majd futtatja azt. Az Azure Dev Spaces a kódot sem teljesen újítja meg.

A Bridge to Kubernetes rugalmasan használható a Kubernetesben futó alkalmazásokkal, az üzembe helyezési módszertől függetlenül. Ha CI/CD-t használ az alkalmazás létrehozásához és futtatásához, függetlenül attól, hogy már létrehozott eszközöket vagy egyéni szkripteket használ, a Bridge to Kubernetes használatával továbbra is fejlesztheti és hibakeresést futtathat a kódon.

> [!TIP]
> A [Microsoft Kubernetes-bővítmény segítségével][kubernetes-extension] gyorsan fejleszthet Kubernetes-jegyzékfájlokat az IntelliSense használatával, és segít a Helm-diagramok kialakításában.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Átállás a Bridge to Kubernetesre az Azure Dev Spacesből

1. Ha az Visual Studio-t használja, frissítse az Visual Studio IDE-t a 16.7-es vagy újabb verzióra, és telepítse a Bridge to Kubernetes bővítményt a Visual Studio [Marketplace-ről.][vs-marketplace] Ha a Visual Studio Code-et használja, telepítse a [Bridge to Kubernetes bővítményt.][vsc-marketplace]
1. Tiltsa le az Azure Dev Spaces-vezérlőt a Azure Portal vagy az [Azure Dev Spaces CLI][azds-delete] használatával.
1. Használja a [Azure Cloud Shell.](https://shell.azure.com) Vagy Mac, Linux vagy Windows rendszeren, telepített Bash-környezetben nyisson meg egy Bash-parancssort. Győződjön meg arról, hogy a következő eszközök elérhetők a parancssori környezetben: Azure CLI, docker, kubectl, curl, tar és gunzip.
1. Hozzon létre egy tároló-beállításjegyzéket, vagy használjon egy meglévőt. Tároló-beállításjegyzéket az Azure-ban az Azure Container Registry [vagy](https://azure.microsoft.com/services/container-registry/) a használatával [Docker Hub.](https://hub.docker.com/) A docker Azure Cloud Shell csak Azure Container Registry docker-lemezképek üzemeltetése esetén érhető el.
1. Futtassa a migrálási szkriptet az Azure Dev Spaces-eszközök Kubernetes-hez való hídként való átalakításához. A szkript felépít egy, a Bridge to Kubernetes-sel kompatibilis új rendszerképet, feltölti azt a kijelölt regisztrációs adatbázisba, majd a [Helm](https://helm.sh) használatával frissíti a fürtöt a rendszerképpel. Meg kell adnia az erőforráscsoportot, az AKS-fürt nevét és egy tároló-beállításjegyzéket. Más parancssori lehetőségek is vannak, ahogy az itt látható:

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   A szkript a következő jelzőket támogatja:

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -c Docker build context path. (default = project root path passed to '-r' option)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. Az *azds.yaml* fájlban található testreszabásokat, például a környezeti változók beállításait manuálisan át kell telepíteni a projekt *values.yml fájlába.*
1. (nem kötelező) Távolítsa el `azds.yaml` a fájlt a projektből.
1. Konfigurálja a Bridge to Kubernetes -et az üzembe helyezett alkalmazásban. A Bridge to Kubernetes használatával kapcsolatos további Visual Studio: [A Bridge to Kubernetes][use-btk-vs]használata a Visual Studio. A VS Code-hoz [lásd: Bridge to Kubernetes in VS Code (A Bridge to Kubernetes használata a VS Code-ban).][use-btk-vsc]
1. Indítsa el a hibakeresést az újonnan létrehozott Bridge to Kubernetes hibakeresési/indítási profillal.
1. A szkriptet szükség szerint újra futtathatja a fürtön való újratelepítéshez.

## <a name="team-development-in-a-shared-cluster"></a>Csoportos fejlesztés megosztott fürtben

A Bridge to Kubernetes használatával fejlesztői útválasztást is használhat. Az Azure Dev Spaces csapatfejlesztési forgatókönyve több Kubernetes-névteret használ a szolgáltatás elkülönítéséhez az alkalmazás többi részéhez a szülő- és gyermeknévterek fogalmával. A Bridge to Kubernetes ugyanezt a képességet kínálja, de jobb teljesítményjellemzővel és ugyanazon alkalmazásnévtéren belül.

A Bridge to Kubernetes és az Azure Dev Spaces esetében is SZÜKSÉG van HTTP-fejlécek jelen és propagálva az alkalmazáson belül. Ha már konfigurálta az alkalmazást az Azure Dev Spaces fejlécpropagálásának kezeléséhez, akkor a fejlécet frissíteni kell. Az Azure Dev Spacesből a Bridge to Kubernetesre való átálláshoz frissítse a konfigurált fejlécet *az azds-route-as* útvonalról a *kubernetes-route-as verzióra.*

## <a name="evaluate-bridge-to-kubernetes"></a>A Bridge to Kubernetes értékelése

Ha a Bridge to Kubernetes használatával szeretne kísérletezni, mielőtt letiltja az Azure Dev Spacest a fürtben, a legegyszerűbb módszer egy új fürt használata. Ha egyidejűleg próbálja használni az Azure Dev Spacest és a Bridge to Kubernetes-t ugyanazon a fürtön, problémákba fog belefutni, amikor mindkettőn útválasztási funkciókat használ.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>A Visual Studio a Bridge to Kubernetes kiértékeléséhez

1. Frissítse az Visual Studio IDE-t a 16.7-es vagy újabb verzióra, és telepítse a Bridge to Kubernetes bővítményt a [Visual Studio Marketplace-ről.][vs-marketplace]
1. Hozzon létre egy új AKS-fürtöt, és telepítse az alkalmazást. Egy mintaalkalmazást [is használhat.][btk-sample-app]
1. Konfigurálja a Bridge to Kubernetes -t az üzembe helyezett alkalmazásban. A Bridge to Kubernetes használatával kapcsolatos további Visual Studio: [A Bridge to Kubernetes használata.][use-btk-vs]
1. Indítsa el a hibakeresést a Visual Studio az újonnan létrehozott Bridge to Kubernetes hibakeresési profillal.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>A Visual Studio Code használata a Bridge to Kubernetes kiértékeléséhez

1. Telepítse a [Bridge to Kubernetes bővítményt.][vsc-marketplace]
1. Hozzon létre egy új AKS-fürtöt, és telepítse az alkalmazást. Egy mintaalkalmazást [is használhat.][btk-sample-app]
1. Konfigurálja a Bridge to Kubernetes -t az üzembe helyezett alkalmazásban. További információ a Bridge to Kubernetes használatával kapcsolatban a Visual Studio Code-ban: [A Bridge to Kubernetes használata.][use-btk-vsc]
1. Indítsa el a hibakeresést a Visual Studio az újonnan létrehozott Bridge to Kubernetes indítási profillal.

## <a name="next-steps"></a>Következő lépések

További információ a Bridge to Kubernetes (A Kuberneteshez való híd) működése.

> [!div class="nextstepaction"]
> [A Bridge to Kubernetes működése][how-it-works-bridge-to-kubernetes]


[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
