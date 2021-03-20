---
title: Beépített tárolók futtatása – gyakori kérdések
description: Válaszokat talál a Azure App Service beépített Linux-tárolókkal kapcsolatos gyakori kérdésekre.
keywords: Azure app Service, webalkalmazás, GYIK, Linux, OSS, Web App for containers, multi-Container, többtárolós
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 6faec27bf368b3eb45e05a91307df6027bda93b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100093998"
---
# <a name="azure-app-service-on-linux-faq"></a>Azure App Service Linuxon – gyakori kérdések

A Linuxon App Service kiadásával dolgozunk a funkciók hozzáadásán és a platform tökéletesítésén. Ez a cikk válaszokat ad az ügyfeleinknek a közelmúltban feltett kérdésekre.

Ha kérdése van, véleményezze ezt a cikket.

## <a name="built-in-images"></a>Beépített rendszerképek

**Le szeretném Villa a platform által biztosított beépített Docker-tárolókat. Hol találhatom meg ezeket a fájlokat?**

A [githubon](https://github.com/azure-app-service)található összes Docker-fájl megtalálható. A [Docker hub](https://hub.docker.com/u/appsvc/)összes Docker-tárolója megtalálható.

<a id="#startup-file"></a>

**Mi a várt érték az indítási fájl szakaszban a futásidejű verem konfigurálásakor?**

| Verem           | Várt érték                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | a JAR-alkalmazás elindítására szolgáló parancs (például `java -jar /home/site/wwwroot/app.jar --server.port=80` ) |
| Tomcat          | egy parancsfájl helye a szükséges konfigurációk végrehajtásához (például `/home/site/deployments/tools/startup_script.sh` )          |
| Node.js         | a PM2 konfigurációs fájl vagy a parancsfájl                                |
| .NET Core       | a lefordított DLL-név a következőképpen `dotnet <myapp>.dll`                                 |
| Ruby            | a Ruby-parancsfájl, amelybe az alkalmazást inicializálni szeretné                     |

Ezeket a parancsokat vagy parancsfájlokat a rendszer a beépített Docker-tároló elindítása után hajtja végre, de az alkalmazás kódjának elindítása előtt.

## <a name="management"></a>Kezelés

**Mi történik, ha lenyomom az újraindítás gombot a Azure Portal?**

Ez a művelet megegyezik a Docker újraindításával.

**Használhatom a Secure Shell (SSH) szolgáltatást az App Container virtuális géphez (VM) való kapcsolódáshoz?**

Igen, ezt megteheti a verziókövetés-kezelő (SCM) webhelyen.

> [!NOTE]
> Az alkalmazástárolóhoz közvetlenül a helyi fejlesztési számítógépről is csatlakozhat SSH, SFTP vagy Visual Studio Code segítségével (Node.js-alkalmazások élő hibakereséséhez). További információkért tekintse meg a [Linuxon futó App Service-ben elérhető távoli hibakereséssel és SSH-val](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html) kapcsolatos cikket.
>

**Hogyan hozhatok létre Linux App Service csomagot SDK-n vagy egy Azure Resource Manager sablonon keresztül?**

Állítsa az App Service **fenntartott** mezőjét *igaz* értékre.

## <a name="continuous-integration-and-deployment"></a>Folyamatos integráció és üzembe helyezés

**A webalkalmazásom továbbra is egy régi Docker-tároló rendszerképet használ, miután Frissítettem a rendszerképet a Docker hub-on. Támogatja az egyéni tárolók folyamatos integrációját és üzembe helyezését?**

Igen, a Azure Container Registry-vagy DockerHub folyamatos integrációjának és üzembe helyezésének beállításához [Web App for containers használatával folytatott folyamatos telepítést](./deploy-ci-cd-custom-container.md)követve. Privát kibocsátásiegység-forgalmi jegyzékek esetén a tárolót a webalkalmazás leállításával, majd indításával frissítheti. Vagy megváltoztathatja vagy hozzáadhat egy dummy-alkalmazás beállítást a tároló frissítésének kényszerítéséhez.

**Támogatja az átmeneti környezeteket?**

Igen.

**A webalkalmazások üzembe helyezéséhez használhatom a web *Deploy/MSDeploy* szolgáltatást?**

Igen, meg kell adnia egy `WEBSITE_WEBDEPLOY_USE_SCM` *false (hamis*) nevű alkalmazás-beállítást.

**Az alkalmazás git-telepítése meghiúsul a Linux-webalkalmazások használatakor. Hogyan lehet megkerülni a problémát?**

Ha a git-telepítés nem sikerül a linuxos webalkalmazáshoz, az alkalmazás kódjának üzembe helyezéséhez válasszon az alábbi lehetőségek közül:

- A folyamatos teljesítés (előzetes verzió) funkció használata: az alkalmazás forráskódját egy Azure DevOps git-adattárban vagy GitHub-tárházban tárolhatja, hogy az Azure folyamatos kézbesítést is használhassa. További információ: a [folyamatos teljesítés konfigurálása a Linux-webalkalmazásokhoz](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- A [zip üzembe helyezési API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)használatával: Ha ezt az API-t szeretné használni, [SSH-t a webalkalmazásba](configure-linux-open-ssh-session.md) , és lépjen abba a mappába, ahová a kódot telepíteni szeretné. Futtassa az alábbi kódot:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Ha hibaüzenet jelenik meg, hogy a `curl` parancs nem található, győződjön meg róla, hogy az `apt-get install curl` előző parancs futtatása előtt telepíti a curlot `curl` .

## <a name="language-support"></a>Nyelvi támogatás

**Webes szoftvercsatornát szeretnék használni a Node.js alkalmazásban, a beállítani kívánt speciális beállításokat vagy konfigurációkat?**

Igen, tiltsa le a `perMessageDeflate` kiszolgálóoldali Node.js kódot. Ha például a socket.io-t használja, használja a következő kódot:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Támogatja a lefordított .NET Core-alkalmazásokat?**

Igen.

**Támogatja a zeneszerzőt a PHP-alkalmazások függőség-kezelője számára?**

Igen, a git üzembe helyezése során a kudu meg kell állapítania, hogy egy PHP-alkalmazást helyez üzembe (a zeneszerző. Lock fájl jelenlétének köszönhetően), és a kudu elindítja a zeneszerző telepítését.

## <a name="custom-containers"></a>Egyéni tárolók

**Saját egyéni tárolót használok. Szeretném, hogy a platform csatlakoztatjon egy SMB-megosztást a `/home/` címtárhoz.**

Ha `WEBSITES_ENABLE_APP_SERVICE_STORAGE` a beállítás nincs **megadva** vagy *false* értékre van állítva, a `/home/` **rendszer nem osztja** meg a könyvtárat a méretezési példányok között, és a megírt fájlok az újraindítások között **nem maradnak** meg. Ha explicit módon beállítja az `WEBSITES_ENABLE_APP_SERVICE_STORAGE` *igaz* értéket, a rendszer engedélyezi a csatlakoztatást.

**Az egyéni tároló hosszú időt vesz igénybe, és a platform újraindítja a tárolót, mielőtt befejezi a kezdést.**

Beállíthatja azt az időtartamot, ameddig a platform várakozik, mielőtt újraindítja a tárolót. Ehhez állítsa a `WEBSITES_CONTAINER_START_TIME_LIMIT` kívánt értékre az alkalmazás beállítását. Az alapértelmezett érték 230 másodperc, a maximális érték pedig 1800 másodperc.

**Mi a saját beállításjegyzék-kiszolgáló URL-címének formátuma?**

Adja meg a beállításjegyzék teljes URL-címét, beleértve a vagy a-t `http://` `https://` .

**Mi a rendszerkép nevének formátuma a privát beállításjegyzékben?**

Adja hozzá a teljes rendszerkép nevét, beleértve a privát beállításjegyzék URL-címét (például myacr.azurecr.io/dotnet:latest). Egyéni portot használó képnevek [nem vihetők be a portálon keresztül](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). A beállításhoz `docker-custom-image-name` használja a [ `az` parancssori eszközt](/cli/azure/webapp/config/container#az-webapp-config-container-set).

**Ki lehet-e tenni egynél több portot az egyéni tároló rendszerképén?**

Nem támogatunk egynél több portot.

**Használhatom a saját tárhelyet?**

Igen, [a saját tárterülete](./configure-connect-to-azure-storage.md) előzetes verzióban érhető el.

**Miért nem tudok böngészni az egyéni tároló fájlrendszerén, vagy hogyan futnak a folyamatok az SCM-helyről?**

Az SCM-hely külön tárolóban fut. Nem lehet megtekinteni az alkalmazás-tároló fájlrendszerét vagy futó folyamatait.

**Az egyéni tároló a 80-es porttól eltérő portot figyel. Hogyan állíthatom be, hogy az alkalmazásom a kérelmeket a portra irányítsa?**

Automatikus portok észlelése. Megadhat egy *WEBSITES_PORT* nevű alkalmazást, és megadja a várt portszám értékét is. Korábban a platform a *port* alkalmazás beállításait használta. Azt tervezzük, hogy ezt az Alkalmazásbeállítások elavulttá válik, és kizárólag a *WEBSITES_PORT* használatára van szükség.

**Be kell-e hajtani a HTTPS-t az egyéni tárolóban?**

Nem, a platform kezeli a HTTPS-megszakítást a megosztott előtér-végpontokon.

## <a name="multi-container-with-docker-compose"></a>Több tároló a Docker-összeállítással

**Hogyan konfigurálja a Azure Container Registry (ACR) szolgáltatást több tárolóval való használatra?**

Ahhoz, hogy az ACR-t több tárolóval is használhassa, az **összes tároló lemezképét** ugyanarra az ACR-beállításjegyzék-kiszolgálóra kell üzemeltetni. Ha ugyanezen a beállításjegyzék-kiszolgálón vannak, létre kell hoznia az alkalmazás beállításait, majd frissítenie kell a Docker-összeállítás konfigurációs fájlját, hogy tartalmazza az ACR-rendszerkép nevét.

Hozza létre az alábbi Alkalmazásbeállítások:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (teljes URL-cím, pl.: `https://<server-name>.azurecr.io` )
- DOCKER_REGISTRY_SERVER_PASSWORD (rendszergazdai hozzáférés engedélyezése az ACR-beállításokban)

A konfigurációs fájlon belül az alábbi példához hasonló módon hivatkozhat az ACR-képre:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Hogyan tudni, hogy melyik tároló az Internet elérhető?**

- Csak egy tároló lehet nyitva a hozzáféréshez
- Csak a 80-es és a 8080-es port érhető el (elérhető portok)

Az alábbi szabályok határozzák meg, hogy melyik tároló legyen elérhető – elsőbbségi sorrendben:

- Az Alkalmazásbeállítás `WEBSITES_WEB_CONTAINER_NAME` beállítása a tároló nevére
- Az első tároló, amely a 80-as vagy 8080-as portot határozza meg
- Ha a fentiek egyike sem igaz, a fájlban megadott első tároló elérhető lesz (láthatóvá válik)


## <a name="web-sockets"></a>Webes szoftvercsatornák

A webes szoftvercsatornák Linux-alkalmazásokon támogatottak.

> [!IMPORTANT]
> A webes szoftvercsatornák jelenleg nem támogatottak a Ingyenes App Service csomagok Linux-alkalmazásaiban. Dolgozunk a korlátozás eltávolításán, és a Ingyenes App Service csomagokban akár 5 web socket-kapcsolat támogatását tervezzük.

## <a name="pricing-and-sla"></a>Díjszabás és SLA

**Mi a díjszabás, most, hogy a szolgáltatás általánosan elérhető?**

A díjszabás az SKU és a régió szerint változik, de további részleteket a díjszabási oldalon talál: [app Service díjszabás](https://azure.microsoft.com/pricing/details/app-service/linux/).

## <a name="other-questions"></a>Egyéb kérdések

**Mit jelent a "kért szolgáltatás nem érhető el az erőforrás-csoportban" kifejezés?**

Ez az üzenet akkor jelenik meg, amikor Azure Resource Manager (ARM) használatával hoz létre webalkalmazást. Egy aktuális korlátozás alapján ugyanazon erőforráscsoport esetében nem keverheti össze a Windows és Linux rendszerű alkalmazásokat ugyanabban a régióban.

**Mik a támogatott karakterek az Alkalmazásbeállítások neveiben?**

Az Alkalmazásbeállítások csak betűk (A-Z, a-z), számok (0-9) és aláhúzás karakter (_) használatával használhatók.

**Hol igényelhetek új szolgáltatásokat?**

Ötleteit a [Web Apps visszajelzési fórumában](https://aka.ms/webapps-uservoice)küldheti el. Adja hozzá a "[Linux]" címet az ötlete címéhez.

## <a name="next-steps"></a>Következő lépések

- [Mi a Linux Azure App Service?](overview.md#app-service-on-linux)
- [Átmeneti környezetek beállítása az Azure App Service-ben](deploy-staging-slots.md)
- [Folyamatos üzembe helyezés a Web App for Containers](./deploy-ci-cd-custom-container.md)
