---
title: Az első alkalmazás üzembe helyezése Cloud Foundry a Microsoft Azure
description: Alkalmazás üzembe helyezése Cloud Foundry Azure-ban
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 1cffc36cbd4f24bbcbb5996a323ffa963e311693
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530952"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Az első alkalmazás üzembe helyezése Cloud Foundry a Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) egy népszerű, nyílt forráskódú alkalmazásplatform, amely a Microsoft Azure. Ebben a cikkben azt mutatjuk be, hogyan helyezhet üzembe és kezelhet egy alkalmazást Cloud Foundry azure-környezetben.

## <a name="create-a-cloud-foundry-environment"></a>Új Cloud Foundry létrehozása

Az Azure-ban számos Cloud Foundry létrehozására van lehetőség:

- A [Cloud Foundry Pivotal Azure Marketplace][pcf-azuremarketplace] ajánlattal olyan szabványos környezetet hozhat létre, amely tartalmazza a PCF Ops Managert és az Azure Service Broker. A [marketplace-ajánlat üzembe][pcf-azuremarketplace-pivotaldocs] helyezésére vonatkozó részletes utasításokat a Pivotal dokumentációjában találja.
- Hozzon létre egy testreszabott környezetet a [Pivotal-Cloud Foundry manuális telepítésével.][pcf-custom]
- [A nyílt forráskódú Cloud Foundry közvetlenül][oss-cf-bosh] egy [BOSH-könyvtár](https://bosh.io) beállításával helyezheti üzembe, amely a virtuális gép üzembe helyezését koordinálja Cloud Foundry környezetben.

> [!IMPORTANT] 
> Ha a PCF-et a Azure Marketplace-ről telepíti, jegyezze fel a SYSTEMDOMAINURL és a Pivotal Apps Manager eléréséhez szükséges rendszergazdai hitelesítő adatokat. Mindkettő leírását a Marketplace üzembe helyezési útmutatója ismerteti. Az oktatóanyag befejezéséhez szükség van rájuk. Piactéren üzemelő példányok esetén a SYSTEMDOMAINURL a következő formában van: `https://system.*ip-address*.cf.pcfazure.com` .

## <a name="connect-to-the-cloud-controller"></a>Csatlakozás a Felhővezérlőhöz

A Cloud Controller az elsődleges belépési pont egy Cloud Foundry környezetben az alkalmazások üzembe helyezéséhez és kezeléséhez. A Cloud Controller API (CCAPI) alapvető REST API, de különböző eszközökkel érhető el. Ebben az esetben a következő CLI-Cloud Foundry [használni.][cf-cli] A CLI Linux, macOS vagy Windows rendszeren is telepíthető, de ha nem szeretné, hogy egyáltalán telepítse, akkor az előre telepítve van a [Azure Cloud Shell.][cloudshell-docs]

A bejelentkezéshez az előtagként adja meg a `api` SYSTEMDOMAINURL adatokat, amelyet a marketplace-ről szerzett be. Mivel az alapértelmezett telepítés önaírt tanúsítványt használ, a kapcsolót is bele kell `skip-ssl-validation` foglalnia.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

A rendszer arra kéri, hogy jelentkezzen be a Felhővezérlőbe. Használja a rendszergazdai fióknak a Marketplace üzembe helyezési lépéseiből beszerzett hitelesítő adatait.

Cloud Foundry *szervezeteket és* szóközöket *biztosít* névterekként a csapatok és a környezetek elkülönítéséhez egy megosztott üzemelő példányon belül. A PCF Marketplace üzemelő példánya tartalmazza az alapértelmezett rendszerrendszert és az alapösszetevőkhöz létrehozott tárhelyeket, például az automatikus skálázás szolgáltatást és az Azure-szolgáltatásközvetítőt.  Most válassza ki a *rendszerterületet.*


## <a name="create-an-org-and-space"></a>Szervezet és tér létrehozása

Ha begépeli a parancsot, olyan rendszeralkalmazásokat lát, amelyek a rendszertérben vannak üzembe állítva a `cf apps` szervezeten belül. 

A rendszerrendszert fenn kell tartania a rendszeralkalmazások számára, ezért hozzon létre egy szervezetet és egy helyet a mintaalkalmazásunk számára. 

```bash
cf create-org myorg
cf create-space dev -o myorg
```

A célparancs használatával váltson az új szervezetre és helyre:

```bash
cf target -o testorg -s dev
```

Az alkalmazás üzembe helyezésekor az automatikusan létrejön az új szervezetben és térben. Annak megerősítéséhez, hogy az új szervezetben/térben jelenleg nincsenek alkalmazások, írja be `cf apps` újra a következőt: .

> [!NOTE] 
> A szervezetekkel és a terekkel kapcsolatos további információkért, valamint Cloud Foundry szerepköralapú hozzáférés-vezérléshez (RBAC) való Cloud Foundry lásd a Cloud Foundry [dokumentációját.][cf-orgs-spaces-docs]

## <a name="deploy-an-application"></a>Alkalmazás üzembe helyezése

Használjuk a Hello Cloud Foundry nevű mintaalkalmazást Spring Cloud, amely Java nyelven íródott, és a [Spring Framework](https://spring.io) és a [Spring Boot.](https://projects.spring.io/spring-boot/)

### <a name="clone-the-hello-spring-cloud-repository"></a>A Hello Spring Cloud klónozása

A Hello Spring Cloud mintaalkalmazás elérhető a GitHubon. Klónozza a környezetbe, és váltsa át az új könyvtárra:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Az alkalmazás létrehozása

Az alkalmazás összeállítása az [Apache Maven használatával.](https://maven.apache.org)

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Az alkalmazás üzembe helyezése a cf push-el

A legtöbb alkalmazást üzembe helyezheti a Cloud Foundry `push` paranccsal:

```bash
cf push
```

Alkalmazás  leküldésekor a Cloud Foundry észleli az alkalmazás típusát (ebben az esetben egy Java-alkalmazást), és azonosítja annak függőségeit (ebben az esetben a Spring-keretrendszert). Ezután a kód futtatásához szükséges összes kódot egy különálló tárolólemezképbe, más néven egy *dropletbe () tartalmazza.* Végül a Cloud Foundry az alkalmazást a környezet egyik elérhető gépére ütemezi, és létrehoz egy URL-címet, ahol elérhető, amely a parancs kimenetében érhető el.

![A cf push parancs kimenete][cf-push-output]

A hello-spring-cloud alkalmazás megnyitásához nyissa meg a megadott URL-címet a böngészőben:

![A Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Ha többet szeretne megtudni arról, hogy mi történik a (folyamat) során, tekintse meg az Alkalmazások szakaszának lépése szakaszt a Cloud Foundry `cf push` dokumentációjában. [][cf-push-docs]

## <a name="view-application-logs"></a>Alkalmazásnaplók megtekintése

A Cloud Foundry CLI használatával megtekintheti az alkalmazás naplóit a neve alapján:

```bash
cf logs hello-spring-cloud
```

Alapértelmezés szerint a logs parancs a *tail* parancsot használja, amely az új naplókat jeleníti meg az írásukhoz. Az új naplók megtekintéséhez frissítse a hello-spring-cloud alkalmazást a böngészőben.

A már megírt naplók megtekintéséhez adja hozzá a `recent` kapcsolót:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Az alkalmazás méretezése

Alapértelmezés szerint a `cf push` csak az alkalmazás egyetlen példányát hozza létre. A magas rendelkezésre állás biztosításához és a horizontális felskálás nagyobb átviteli sebességhez való engedélyezéséhez általában az alkalmazások több példányát szeretné futtatni. A már üzembe helyezett alkalmazásokat egyszerűen felskálálhatja az `scale` paranccsal:

```bash
cf scale -i 2 hello-spring-cloud
```

A parancs `cf app` alkalmazáson való futtatása azt mutatja, Cloud Foundry egy másik példányt hoz létre az alkalmazásból. Az alkalmazás elindulása után a Cloud Foundry automatikusan elindítja a forgalom terheléselosztását.


## <a name="next-steps"></a>Következő lépések

- [Olvassa el a Cloud Foundry dokumentációját][cloudfoundry-docs]
- [Az Azure DevOps Services beépülő modul beállítása Cloud Foundry][vsts-plugin]
- [A Microsoft Log Analytics Nozzle konfigurálása Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/ops-manager/2-10/install/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: ../cloud-shell/overview.md
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png
