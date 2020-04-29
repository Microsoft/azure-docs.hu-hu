---
title: Hogyan működik az Útválasztás az Azure dev Spaces-szel
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Ismerteti azokat a folyamatokat, amelyekkel a Power Azure dev Spaces és az Útválasztás működik
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: e9bc1875c053335da6a8e2603406bcdb34a6dd04
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241386"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Hogyan működik az Útválasztás az Azure dev Spaces-szel

Az Azure dev Spaces lehetővé teszi a Kubernetes-alkalmazások gyors megismétlését és hibakeresését, valamint a csapattal való együttműködését egy Azure Kubernetes szolgáltatásbeli (ak-beli) fürtön. Miután a projekt egy fejlesztői területen fut, az Azure dev Spaces további hálózatkezelési és útválasztási funkciókat biztosít a projekthez.

Ez a cikk azt ismerteti, hogyan működik az Útválasztás a dev Spaces-szel.

## <a name="how-routing-works"></a>Az útválasztás működése

A fejlesztői terület az AK-ra épül, és ugyanazokat a [hálózatkezelési fogalmakat](../aks/concepts-network.md)használja. Az Azure dev Spaces egy központi *ingressmanager* -szolgáltatással is rendelkezik, és üzembe helyezi a saját bejövő vezérlőjét az AK-fürtön. A *ingressmanager* szolgáltatás FIGYELI az AK-fürtöket a dev Spaces-ben, és kibővíti az Azure dev Spaces-ba való belépést a fürtben a bejövő objektumokkal az Application hüvelyek útválasztásához. Az egyes Pod devspaces-tárolója egy `azds-route-as` http-fejlécet tesz elérhetővé az URL-cím alapján a fejlesztői területhez való http-adatforgalomhoz. Az URL-címre érkező kérés például egy *http://azureuser.s.default.serviceA.fedcba09...azds.io* http-fejlécet kap a `azds-route-as: azureuser`következővel:. A devspaces tároló nem ad hozzá `azds-route-as` fejlécet, ha az egyik már létezik.

Amikor HTTP-kérést végeznek a fürtön kívüli szolgáltatásra, a kérés a bejövő vezérlőre lép. A beáramló vezérlő közvetlenül a megfelelő Pod-ra irányítja át a kérést a bejövő objektumai és szabályai alapján. A pod devspaces-proxy tároló fogadja a kérést, hozzáadja a `azds-route-as` fejlécet az URL-cím alapján, majd átirányítja a kérést az alkalmazás-tárolóba.

Amikor HTTP-kérést végez egy másik szolgáltatásból a fürtön belül, a kérelem először a hívó szolgáltatás devspaces-tárolóján halad végig. A devspaces-proxy tároló a HTTP-kérést tekinti át `azds-route-as` , és ellenőrzi a fejlécet. A fejléc alapján a devspaces tároló a fejléc értékéhez tartozó szolgáltatás IP-címét fogja megkeresni. Ha a rendszer IP-címet talál, a devspaces tároló átirányítja a kérést az adott IP-címhez. Ha nem található IP-cím, a devspaces tároló továbbítja a kérést a szülő alkalmazás tárolójába.

Az Applications *servicea* és a *serviceB* például az *alapértelmezett*nevű szülő fejlesztői területre van telepítve. a *servicea* a *serviceB* -ra TÁMASZKODik, és http-hívásokat tesz lehetővé. Az Azure-felhasználó létrehoz egy gyermek-fejlesztési helyet az *azureuser*nevű *alapértelmezett* hely alapján. Az Azure User a *servicea* saját verzióját is üzembe helyezi a gyermek területére. Kérelem küldése *http://azureuser.s.default.serviceA.fedcba09...azds.io*:

![Az Azure dev Spaces útválasztása](media/how-dev-spaces-works/routing.svg)

1. A bejövő vezérlő megkeresi az URL-címhez társított Pod IP-címét, amely a *servicea. azureuser*.
1. A beáramló vezérlő megkeresi az Azure-felhasználó fejlesztői területén található Pod IP-címét, és átirányítja a kérést a *servicea. azureuser* Pod-ra.
1. A *servicea. azureuser* Pod devspaces-tárolója megkapja a kérést, és http `azds-route-as: azureuser` -fejlécként adja meg.
1. A servicea devspaces-tárolója *. az azureuser* Pod a servicea *. azureuser* POD szolgáltatásban továbbítja a kérést *a servicea alkalmazás* tárolójába.
1. A servicea *. azureuser* *Pod szolgáltatásbeli alkalmazása* meghívja a *serviceB*. A *servicea* alkalmazás emellett kódot is tartalmaz a meglévő `azds-route-as` fejléc megőrzése érdekében, ami ebben az esetben `azds-route-as: azureuser`a következő:.
1. A servicea devspaces-tárolója *. az azureuser* Pod fogadja a kérést, és a `azds-route-as` fejléc értéke alapján megkeresi a *serviceB* IP-címét.
1. A *servicea. azureuser* Pod devspaces-proxy tárolója nem talál IP-címet a *serviceB. azureuser*számára.
1. A *servicea. azureuser* Pod devspaces-proxy tárolója megkeresi a *serviceB* lévő IP-címet a szülő térben, amely a *serviceB. default*.
1. A servicea devspaces-tárolója *. az azureuser* Pod megkeresi a *serviceB. default* IP-címét, és átirányítja a kérést a *serviceB. default* Pod-ra.
1. A *serviceB. default* Pod devspaces-tárolója megkapja a kérést, és átirányítja a kérést a *serviceB. default* Pod *serviceB* -alkalmazás tárolójába.
1. A *serviceB* alkalmazás a *serviceB. az alapértelmezett* Pod a *servicea. azureuser* Pod-re adott választ ad vissza.
1. A *servicea. azureuser* Pod devspaces-tárolója megkapja a választ, és körözteti a választ a servicea *. azureuser* POD szolgáltatásban található *servicea* alkalmazás-tárolóba.
1. A *servicea* alkalmazás fogadja a választ, majd visszaadja a saját válaszát.
1. A *servicea. azureuser* Pod devspaces-tárolója fogadja a *servicea* alkalmazás-tárolótól érkező választ, és a fürtön kívülre irányítja a választ az eredeti hívónak.

Az összes többi TCP-forgalom, amely nem HTTP-továbbítást hajt végre a bejövő és a devspaces tárolóban, nem módosult.

## <a name="sharing-a-dev-space"></a>Fejlesztői terület megosztása

Ha csapattal dolgozik, [megoszthat egy fejlesztői területet egy teljes csapaton belül](how-to/share-dev-spaces.md) , és származtatott fejlesztői szóközöket hozhat létre. A fejlesztői területet bárki használhatja, aki közreműködői hozzáféréssel rendelkezik a fejlesztői terület erőforráscsoporthoz.

Létrehozhat egy másik fejlesztői területből származtatott új fejlesztői helyet is. Származtatott fejlesztői terület létrehozásakor a rendszer hozzáadja a *azds.IO/Parent-Space=PARENT-Space-Name* címkét a származtatott fejlesztői terület névteréhez. Emellett a szülő fejlesztői területről származó összes alkalmazás meg van osztva a származtatott fejlesztői területtel. Ha egy alkalmazás frissített verzióját telepíti a származtatott fejlesztői területre, akkor csak a származtatott fejlesztői területen fog megjelenni, és a szülő fejlesztői terület érintetlen marad. A származtatott fejlesztői szóközök és a *nagyszülő* szóközök közül legfeljebb három szint adható meg.

A származtatott fejlesztői terület is intelligens módon irányítja a kérelmeket a saját alkalmazásai és a szülőtől megosztott alkalmazások között. Az Útválasztás úgy működik, hogy megpróbál átirányítani egy alkalmazást a származtatott fejlesztői területen, és visszakerül a megosztott alkalmazásra a szülő fejlesztői területről. Ha az alkalmazás nincs a szülő térben, az Útválasztás visszakerül a szülő terület megosztott alkalmazására.

Például:
* A fejlesztői terület *alapértelmezett értéke* az Applications *Servicea* és a *serviceB*.
* A dev Space *Azureus* az *alapértelmezett értékből*származik.
* A *servicea* frissített verziója van üzembe helyezve az *azureuser*-ben.

Az *azureuser*használatakor a *servicea szolgáltatásnak* küldött összes kérelem a frissített verzióra lesz irányítva az *azureuser*-ben. A *serviceB* iránti kérés először a *serviceB* *azureuser* -verziójára lesz irányítva. Mivel nem létezik, a rendszer a *serviceB* *alapértelmezett* verziójára irányítja át. Ha a *servicea szolgáltatáshoz* tartozó *azureuser* verziója el lett távolítva, a *servicea* szolgáltatáshoz intézett összes kérelem vissza fog térni a *servicea* *alapértelmezett* verziójának használatára.

## <a name="next-steps"></a>További lépések

Ha szeretné megtekinteni, hogy az Azure dev Spaces hogyan használja az útválasztást a gyors iteráció és a fejlesztés érdekében, tekintse meg a [fejlesztői gép a fejlesztői tárhelyhez való csatlakoztatásának][how-it-works-connect]módját, [a kód távoli hibakeresését az Azure dev Spaces][how-it-works-remote-debugging]szolgáltatással, valamint a [GitHub-műveleteket & Azure Kubernetes szolgáltatást][pr-flow].

Az Útválasztás és az Azure dev Spaces közötti együttműködés használatának megkezdéséhez tekintse meg a [csapat fejlesztése az Azure dev Spaces][quickstart-team] rövid útmutatójában.

[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[troubleshooting]: troubleshooting.md