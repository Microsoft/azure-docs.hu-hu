---
title: Tudnivalók a Azure DevTest Labsról | Microsoft Docs
description: Ismerje meg, hogy a DevTest Labs hogyan könnyítheti meg az Azure-beli virtuális gépek létrehozását, kezelését és figyelését
ms.topic: article
ms.date: 06/20/2020
ms.openlocfilehash: b7e6493e5fb1c99dfa7962ed464dca3ebc6b0c7e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85480201"
---
# <a name="about-azure-devtest-labs"></a>Információk az Azure DevTest Labs szolgáltatásról
A Azure DevTest Labs lehetővé teszi a csapatoknak a fejlesztők számára a virtuális gépek (VM-EK) és a Pásti-erőforrások hatékony önálló felügyeletét jóváhagyások nélkül.

A DevTest Labs előre konfigurált alapokból vagy Azure Resource Manager sablonokból álló Labs-t hoz létre. Ezek rendelkeznek a környezetek létrehozásához használható összes szükséges eszközzel és szoftverrel. A környezeteket néhány perc alatt létrehozhatja, nem pedig órákat vagy napokat.

A DevTest Labs használatával az alábbi feladatok végrehajtásával ellenőrizheti az alkalmazások legújabb verzióit:

- Újrahasznosítható sablonok és összetevők használatával gyorsan kiépítheti a Windows-és Linux-környezeteket.
- Az igény szerinti környezetek kiépítéséhez egyszerűen integrálhatja az üzembe helyezési folyamatait a DevTest Labs-zel.
- A terheléses tesztelés vertikális felskálázásával több tesztoldalt is kiépíthető, és előre kiépített környezetek hozhatók létre a képzéshez és a bemutatóhoz.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Képességek
A DevTest Labs a következő lehetőségeket biztosítja a virtuális gépekkel dolgozó fejlesztők számára:

- Hozzon létre gyorsan virtuális gépeket kevesebb mint öt egyszerű lépés után.
- Válassza ki a csapat által létrehozott, jóváhagyott és engedélyezett virtuálisgép-bázisok válogatott listáját.
- Hozzon létre virtuális gépeket olyan előre létrehozott egyéni rendszerképekből, amelyeken már telepítve van az összes szoftver és eszköz. 
- Hozzon létre virtuális gépeket olyan képletekben, amelyek lényegében egyéni lemezképek, és a virtuális gépek létrehozásakor telepített szoftverek legújabb buildével együtt. 
- Telepítse a virtuális gépeken a kiépítés után telepített bővítményeket.
- Állítsa be az automatikus leállítást és az automatikus indítást a virtuális gépeken.
- Egy előre létrehozott virtuális gép igénylése a létrehozási folyamat nélkül.

A DevTest Labs a következő képességeket biztosítja a fejlesztők számára a Pásti környezetekben:

- A Resource Manager használatával gyorsan hozhat létre Pásti-környezeteket három egyszerű lépés után.
- Válassza ki a Resource Manager-sablonok által beállított, és a csapat által vagy a központi informatikai részleg által jóváhagyott, a forrásként megadott listáját.
- Hozzon létre egy üres erőforráscsoportot (homokozó) egy Resource Manager-sablon segítségével, amely egy labor kontextusában vizsgálja az Azure-t.

A DevTest Labs Emellett lehetővé teszi a központi informatikai rendszer számára a hulladékok szabályozását, az erőforrások költségeinek optimalizálását és a költségvetéseken belüli tartózkodást a következő feladatok elvégzésével:  

- A virtuális gépek automatikus leállításának és automatikus indításának beállítása.
- Szabályzatok beállítása a felhasználók által létrehozható virtuális gépek számához.
- Házirendeket állíthat be a virtuális gépek méretei és a felhasználók által választott katalógus-lemezképek számára.
- A költségek követése és a célok beállítása a laborokban.
- A szükséges műveletek elvégzése érdekében bejelentheti a laborok magas prognosztizált költségeinek beszerzését.

A DevTest Labs a következő előnyöket biztosítja a Felhőbeli környezetek létrehozásához, konfigurálásához és kezeléséhez.

## <a name="cost-control-and-governance"></a>Cost Control és irányítás
A DevTest Labs megkönnyíti a költségek szabályozását azáltal, hogy lehetővé teszi a következő feladatok elvégzését:

- [Szabályzatokat állíthat be a laborban](devtest-lab-set-lab-policy.md), például a virtuális gépek számát felhasználónként vagy egy laborban. 
- Hozzon létre [házirendeket a virtuális gépek automatikus leállításához](devtest-lab-set-lab-policy.md) és elindításához.
- A virtuális gépek és a Pásti-erőforrások költségeinek nyomon követése a laborban, hogy [a költségvetésen](devtest-lab-configure-cost-management.md)belül maradjon.
- Maradjon a laborok kontextusában, hogy ne hozzon létre erőforrásokat a rajtuk kívül.

## <a name="quickly-get-to-ready-to-test"></a>Gyors felkészülés a kész tesztre
A DevTest Labs lehetővé teszi, hogy az előre kiépített környezeteket a csapata által igényelt, az alkalmazások fejlesztéséhez és teszteléséhez szükséges összes funkcióval létrehozza. Csak [azt a környezetet](devtest-lab-add-claimable-vm.md) kell kialakítania, amelyben az alkalmazás utolsó jó buildje telepítve van, és megkezdi a munkát. Vagy használjon tárolókat még gyorsabb és sovány környezet létrehozásához.

## <a name="create-once-use-everywhere"></a>Egyszeri létrehozás, bárhol használható
A teljes körű fejlesztői és tesztelési környezeteket egyszerűen hozhatja létre, és megoszthatja a saját csapatán vagy szervezetén [belül a](add-artifact-repository.md) Pásti- [környezet sablonjait](devtest-lab-create-environment-from-arm.md) és összetevőit.

## <a name="worry-free-self-service"></a>Problémamentes önkiszolgáló szolgáltatás
A DevTest Labs lehetővé teszi a fejlesztők és a tesztelők számára, hogy gyorsan és egyszerűen [hozzanak létre IaaS-alapú virtuális gépeket](devtest-lab-add-vm.md) és a [Pásti-erőforrásokat](devtest-lab-create-environment-from-arm.md) az előre konfigurált erőforrások készletének használatával.

## <a name="use-iaas-and-paas-resources"></a>IaaS-és Pásti-erőforrások használata 
A fejlesztők az Azure Service Fabric-fürtöket, a Azure App Service és a SharePoint-farmok Web Apps funkcióját is felhasználhatják Resource Manager-sablonok használatával. A (z) Labs-beli Pásti használatának megkezdéséhez használja a [nyilvános környezet tárházának](devtest-lab-configure-use-public-environments.md) sablonjait, vagy [kapcsolja a labort a saját git-tárházához](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Ezen erőforrások költségeit is követheti, hogy a költségkereten belül maradjon.

## <a name="integrate-with-your-existing-toolchain"></a>Integrálás meglévő toolchain
Az előre elkészített beépülő modulok vagy az API segítségével közvetlenül a kívánt [folyamatos integrációs (CI) eszközről](devtest-lab-integrate-ci-cd.md), az integrált fejlesztői környezetből (ide) vagy az automatizált kiadási folyamatból is kiépítheti a fejlesztési és tesztelési környezeteket. Használhatja az átfogó parancssori eszközt is.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- További információ a DevTest Labs szolgáltatásról: [DevTest Labs-fogalmak](devtest-lab-concepts.md).
- Részletes útmutatást a [bemutató: oktatóanyag: tesztkörnyezet beállítása Azure DevTest Labs használatával](tutorial-create-custom-lab.md)című témakörben talál.