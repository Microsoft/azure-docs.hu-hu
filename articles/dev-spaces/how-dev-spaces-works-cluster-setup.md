---
title: Fürt beállítása az Azure dev Spaces működéséhez
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Útmutató Azure Kubernetes Service-fürt beállításához az Azure dev Spaces működéséhez
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: 841e67b96e95aa251fa5bf1ef469b68de30f54d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91972679"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Fürt beállítása az Azure dev Spaces működéséhez

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Az Azure dev Spaces lehetővé teszi a Kubernetes-alkalmazások gyors megismétlését és hibakeresését, valamint a csapattal való együttműködését egy Azure Kubernetes szolgáltatásbeli (ak-beli) fürtön. Az egyik módszer az, hogy engedélyezi az Azure dev-helyeket az AK-fürtön, így [közvetlenül a fürtön futtathatja a szolgáltatásokat][how-it-works-up] , és [további hálózatkezelési és útválasztási képességeket][how-it-works-routing]is használhat. Ez a cikk azt ismerteti, hogy mi történik a fürt előkészítésekor és az Azure dev Spaces használatának engedélyezésekor.

## <a name="prepare-your-aks-cluster"></a>Az AK-fürt előkészítése

Az AK-fürt fejlesztői tárhelyekre való előkészítéséhez ellenőrizze, hogy az AK-fürt az [Azure dev Spaces által támogatott][supported-regions] régióban van-e, és Kubernetes 1.10.3 vagy újabb verziót futtat. A futtatásával engedélyezheti az Azure dev Spaces szolgáltatást az Azure CLI-vel `az aks use-dev-spaces` .

Ha az Azure dev-helyek engedélyezve vannak az AK-fürtön, akkor a fürthöz telepíti a vezérlőt. A vezérlő az AK-fürtön kívül található. Az ügyféloldali eszközök és az AK-fürt közötti viselkedést és kommunikációt vezérli. Ha engedélyezve van, használhatja a vezérlőt az ügyféloldali eszközök használatával.

A vezérlő a következő műveleteket hajtja végre:

* Felügyeli a fejlesztési terület létrehozását és a kijelölést.
* Telepíti az alkalmazás Helm-diagramját, és létrehozza a Kubernetes objektumokat.
* Létrehozza az alkalmazás tárolójának rendszerképét.
* Üzembe helyezi az alkalmazást az AK-ban.
* A növekményes buildek és újraindítások a forráskód módosításakor.
* A naplókat és a HTTP-nyomkövetéseket kezeli.
* Az stdout és a stderr továbbítása az ügyféloldali eszközökhöz.
* Az útválasztást a szóközen belüli alkalmazások, valamint a szülő és a gyermek szóközök között konfigurálja.

A vezérlő egy különálló Azure-erőforrás a fürtön kívül, és a fürt erőforrásaihoz a következő:

* Létrehoz vagy kijelöl egy Kubernetes-névteret, amelyet fejlesztői területként kíván használni.
* Eltávolítja a *azds* nevű Kubernetes-névteret, ha létezik, és létrehoz egy újat.
* Üzembe helyez egy Kubernetes webhook-konfigurációt.
* Üzembe helyez egy webhook-beléptetési kiszolgálót.

Ugyanazt a egyszerű szolgáltatást használja, amelyet az AK-fürt más Azure dev Spaces-összetevőkhöz használ.

![Azure dev Spaces – fürt előkészítése](media/how-dev-spaces-works/prepare-cluster.svg)

Az Azure dev Spaces használatához legalább egy fejlesztői területnek kell lennie. Az Azure dev Spaces Kubernetes-névtereket használ az AK-fürtön belül a dev Spaces szolgáltatásban. A vezérlő telepítésekor a rendszer felszólítja, hogy hozzon létre egy új Kubernetes-névteret, vagy válasszon egy meglévő névteret, amelyet az első fejlesztői területként kíván használni. Alapértelmezés szerint a vezérlő a meglévő *alapértelmezett* Kubernetes-névtér frissítését kínálja az első fejlesztői területére.

Ha egy névtér fejlesztői területként van kijelölve, a vezérlő hozzáadja a *azds.IO/Space=True* címkét a névtérhez, hogy az a fejlesztői területként azonosítható legyen. A létrehozás vagy a kijelölés kezdeti fejlesztői területe alapértelmezés szerint ki van választva a fürt előkészítése után. Ha kijelöl egy szóközt, az Azure dev Spaces új munkaterhelések létrehozásához használja.

Az ügyféloldali eszközkészlet használatával új fejlesztői tárhelyeket hozhat létre, és eltávolíthatja a meglévő fejlesztői helyeket. A Kubernetes korlátozásai miatt az *alapértelmezett* fejlesztői terület nem távolítható el. A vezérlő eltávolítja a *azds* nevű meglévő Kubernetes-névtereket is, hogy elkerülje az `azds` ügyféloldali szerszámozás által használt parancs ütközését.

A Kubernetes webhook-belépésvezérlés a hüvelyek három tárolóval való beadására szolgál a rendszerállapot-kialakítás során: egy devspaces-tárolót, egy devspaces-proxy-init tárolót és egy devspaces-tárolót. **Mindhárom tároló a root hozzáféréssel fut az AK-fürtön.** Ugyanazt a szolgáltatásnevet is használják, amelyet az AK-fürt más Azure dev Spaces-összetevőkhöz használ.

![Azure dev Spaces Kubernetes webhook-beléptetési kiszolgáló](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

A devspaces-proxy tároló egy olyan oldalkocsi-tároló, amely az alkalmazás-tárolón belüli és kívüli összes TCP-forgalmat kezeli, és segít az útválasztásban. A devspaces-proxy tároló a HTTP-üzeneteket átirányítja, ha vannak ilyenek. Például segíthet a HTTP-üzenetek továbbításában a szülő és a gyermek szóközökben lévő alkalmazások között. Az összes nem HTTP-forgalom áthalad a devspaces-proxyn keresztül. A devspaces-proxy tároló az összes bejövő és kimenő HTTP-üzenetet is naplózza, és a nyomkövetésként elküldi őket az ügyféloldali eszközöknek. Ezeket a nyomkövetéseket a fejlesztő megtekintheti, hogy megvizsgálja az alkalmazás viselkedését.

A devspaces-proxy-init tároló egy olyan [init-tároló](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) , amely további útválasztási szabályokat is feltesz az alkalmazás tárolójának terület-hierarchiája alapján. Az útválasztási szabályok hozzáadásával az alkalmazás-tároló */etc/resolv.conf* -fájlját és az iptables-konfigurációt az indítás előtt frissíti. A */etc/resolv.conf* -frissítések lehetővé teszik a szolgáltatások DNS-feloldását a fölérendelt helyeken. Az iptables-konfiguráció frissítései biztosítják, hogy az alkalmazás tárolóján belüli és onnan kifelé irányuló összes TCP-forgalom átirányítva legyen, bár a devspaces-proxy. A devspaces-proxy-init összes frissítése a Kubernetes által hozzáadott szabályok mellett történik.

A devspaces-Build tároló egy init tároló, amelynek a projekt forráskódja és a Docker-szoftvercsatorna csatlakoztatva van. A projekt forráskódja és a Docker-hozzáférés lehetővé teszi, hogy az alkalmazás-tárolót közvetlenül a pod-hoz lehessen felépíteni.

> [!NOTE]
> Az Azure dev-helyek ugyanazt a csomópontot használják az alkalmazás tárolójának létrehozásához és futtatásához. Ennek eredményeképpen az Azure dev Spaces szolgáltatásnak nincs szüksége külső tároló beállításjegyzékre az alkalmazás létrehozásához és futtatásához.

A Kubernetes webhook-beléptetési kiszolgáló az AK-fürtben létrehozott összes új Pod-t figyeli. Ha a hüvely bármely, a *azds.IO/Space=True* címkével rendelkező névtérbe van telepítve, akkor a pod a további tárolókat is beadja. A devspaces-Build tároló csak akkor van befecskendezve, ha az alkalmazás tárolója az ügyféloldali eszköz használatával fut.

Miután felkészítette az AK-fürtöt, az ügyféloldali eszközkészlet segítségével előkészítheti és futtathatja a kódot a fejlesztői térben.

## <a name="client-side-tooling"></a>Ügyféloldali eszközök

Az ügyféloldali eszközök lehetővé teszik a felhasználó számára a következőket:
* Docker, Helm-diagram és Azure dev Spaces konfigurációs fájl létrehozása az alkalmazáshoz.
* Hozzon létre szülő és gyermek fejlesztői szóközöket.
* Kérje meg a vezérlőt, hogy hozza létre és indítsa el az alkalmazást.

Az alkalmazás futása közben az ügyféloldali eszköz is:
* Az stdout és a stderr fogadása és megjelenítése az alkalmazásban, az AK-ban fut.
* A [Port-Forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) használatával engedélyezi az alkalmazás webes elérését http: \/ /localhost.
* Egy hibakeresőt csatlakoztat a futó alkalmazáshoz az AK-ban.
* Szinkronizálja a forráskódot a fejlesztői területére, amikor változást észlel a növekményes buildek esetében, ami lehetővé teszi a gyors iterációt.
* Lehetővé teszi a fejlesztői gép közvetlen összekapcsolását az AK-fürthöz.

A parancs részeként használhatja az ügyféloldali eszközt a parancssorból `azds` . Az ügyféloldali eszközt az alábbiakkal is használhatja:

* Visual Studio Code az [Azure dev Spaces bővítmény](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)használatával.
* A Visual Studio és az Azure-fejlesztési számítási feladatok.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az ügyféloldali eszközök használatáról a kód előkészítéséhez és a fejlesztői tárhelyen való futtatásához, tekintse meg a [projekt előkészítése az Azure dev Spaces működéséhez][how-it-works-prep]című témakört.


[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service