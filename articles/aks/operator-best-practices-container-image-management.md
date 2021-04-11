---
title: Kezelői ajánlott eljárások – az Azure Kubernetes Services tároló-rendszerkép kezelése
description: Ismerje meg az Azure Kubernetes Service-ben (ak) található tároló-lemezképek felügyeletére és védelmére vonatkozó ajánlott eljárásokat.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 998d8602b6aa0e71a04f75aff1c29551ba09c8a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105119"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a tárolók rendszerképének kezeléséhez és biztonságához az Azure Kubernetes szolgáltatásban (ak)

Az Azure Kubernetes szolgáltatásban (ak) lévő alkalmazások fejlesztése és futtatása nagy prioritást jelent a tárolók és a tárolók rendszerképének biztonsága terén. Az elavult alapképekkel vagy a nem javított alkalmazás-futtatókörnyezettel rendelkező tárolók biztonsági kockázatot és lehetséges támadási vektort vezetnek be. 

A kiépítés és a futtatókörnyezet segítségével csökkentheti a kockázatokat a tárolók vizsgálati és szervizelési eszközeinek integrálásával és futtatásával. A korábban a biztonsági rést vagy az elavult alapképet fogja megtekinteni, annál biztonságosabb a fürt. 

Ebben a cikkben a *"tárolók"* a következőket jelenti:
* A tároló-beállításjegyzékben tárolt tároló-lemezképek.
* A futó tárolók.

Ebből a cikkből megtudhatja, hogyan védheti meg a tárolókat az AK-ban. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Képbiztonsági rések keresése és szervizelése.
> * A tároló lemezképének automatikus elindítása és újbóli üzembe helyezése alaprendszerkép frissítésekor.

Emellett elolvashatja a [fürt biztonságával][best-practices-cluster-security] és a [Pod biztonsággal][best-practices-pod-security]kapcsolatos ajánlott eljárásokat is.

A [tárolók biztonsága Security Center][security-center-containers] is használható a tárolók biztonsági rések vizsgálatához. [Azure Container Registry integrációja][security-center-acr] Security Center segítségével megvédheti a lemezképeket és a beállításjegyzéket a biztonsági rések ellen.

## <a name="secure-the-images-and-run-time"></a>A képek és a futtatási idő védelme

> **Útmutatás az ajánlott eljárásokhoz** 
>
> A biztonsági rések ellenőrzése a tároló rendszerképein. Csak az érvényesített lemezképek telepítése. Rendszeresen frissítse az alaplemezképeket és az alkalmazás futtatókörnyezetét. Telepítse újra a számítási feladatokat az AK-fürtben.

A tároló-alapú számítási feladatok bevezetésekor ellenőriznie kell a saját alkalmazások létrehozásához használt lemezképek és futtatókörnyezet biztonságát. Hogyan kerülhető el a biztonsági rések bevezetése az üzemelő példányokra? 
* Belefoglalás a telepítési munkafolyamatba a tárolók rendszerképeinek a [Twistlock][twistlock] vagy [Aqua][aqua]eszközzel történő vizsgálatára szolgáló folyamat.
* Csak az ellenőrzött lemezképek üzembe helyezésének engedélyezése.

![A tároló lemezképének vizsgálata és szervizelése, érvényesítése és üzembe helyezése](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Például a folyamatos integráció és a folyamatos üzembe helyezés (CI/CD) folyamat segítségével automatizálhatja a képek vizsgálatát, ellenőrzését és központi telepítését. Azure Container Registry a biztonsági rések vizsgálatának funkcióit is tartalmazza.

## <a name="automatically-build-new-images-on-base-image-update"></a>Új rendszerképek automatikus létrehozása az alaprendszerkép frissítésével

> **Útmutatás az ajánlott eljárásokhoz** 
>
> Az alkalmazási lemezképek alaprendszerképének használatakor az Automation használatával új rendszerképeket hozhat létre az alaplemezkép frissítésekor. Mivel a frissített alaplemezképek általában biztonsági javításokat tartalmaznak, frissítheti az alsóbb rétegbeli alkalmazás-tárolók lemezképeit.

Minden alkalommal, amikor egy alaprendszerkép frissül, frissítenie kell az alsóbb rétegbeli tárolók lemezképeit is. Integrálja ezt a felépítési folyamatot olyan ellenőrzési és üzembe helyezési folyamatokban, mint például az [Azure-folyamatok][azure-pipelines] vagy a Jenkins. Ezek a folyamatok gondoskodnak arról, hogy az alkalmazások továbbra is fussanak a frissített alapú rendszerképeken. Az alkalmazás-tároló lemezképének ellenőrzése után az AK-beli központi telepítések a legújabb, biztonságos rendszerképek futtatására is frissíthetők.

Azure Container Registry a feladatok a lemezkép frissítésekor is automatikusan frissíthetik a tároló lemezképeit. Ezzel a funkcióval néhány alaprendszerképet hozhat létre, és azokat a hibákkal és biztonsági javításokkal frissítheti.

További információ az alapszintű rendszerkép frissítéseiről: [a rendszerkép-buildek automatizálása az alapszintű rendszerkép frissítése Azure Container Registry feladatokkal][acr-base-image-update].

## <a name="next-steps"></a>Következő lépések

Ez a cikk a tárolók biztonságossá tételére koncentrál. Ezen területek némelyikének megvalósításához tekintse meg a következő cikkeket:

* [Rendszerkép-buildek automatizálása Azure Container Registry feladatokkal az alapszintű rendszerkép frissítése során][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/defender-for-container-registries-introduction.md