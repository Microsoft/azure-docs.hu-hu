---
title: Beépített tárolók futtatása – gyakori kérdések
description: Választ találhat az alkalmazás beépített Linux-tárolóival kapcsolatos gyakori Azure App Service.
keywords: azure app service, webalkalmazás, gyik, linux, oss, webalkalmazás tárolókhoz, többtárolós, többtárolós
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 82fc5707800e06e3221754bfa29d8e981ccdbd2d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782584"
---
# <a name="azure-app-service-on-linux-faq"></a>Azure App Service Linuxon – gyakori kérdések

A App Service on Linux már dolgozunk a funkciók hozzáadásán és a platformunk továbbfejlesztésen. Ez a cikk az ügyfeleink által a közelmúltban feltett kérdésekre ad választ.

Ha kérdése van, megjegyzést fűz ehhez a cikkhez.

## <a name="built-in-images"></a>Beépített rendszerképek

**El szeretném forkni a platform által tartalmazott beépített Docker-tárolókat. Hol találom ezeket a fájlokat?**

Az összes Docker-fájlt megtalálja a [GitHubon.](https://github.com/azure-app-service) Az összes Docker-tárolót megtalálja a [Docker Hub.](https://hub.docker.com/u/appsvc/)

<a id="#startup-file"></a>

**Mik az indítási fájl szakasz várt értékei a futásidejű verem konfigurálásakor?**

| Verem           | Várt érték                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | a JAR-alkalmazás indító parancsa (például `java -jar /home/site/wwwroot/app.jar --server.port=80` ) |
| Tomcat          | a szükséges konfigurációk végrehajtásához szükséges parancsfájl helye (például `/home/site/deployments/tools/startup_script.sh` )          |
| Node.js         | a PM2 konfigurációs fájl vagy a szkriptfájl                                |
| .NET Core       | a lefordított DLL-név a következőként: `dotnet <myapp>.dll`                                 |
| Ruby            | a Ruby-szkript, amely segítségével inicializálni szeretné az alkalmazást                     |

Ezek a parancsok vagy szkriptek a beépített Docker-tároló elindulás után, de az alkalmazáskód elindul előtt vannak végrehajtva.

## <a name="management"></a>Kezelés

**Mi történik, ha megnyomom az újraindítási gombot a Azure Portal?**

Ez a művelet megegyezik a Docker újraindításával.

**Használhatom a Secure Shellt (SSH) az alkalmazástároló virtuális géphez (VM) való csatlakozáshoz?**

Igen, ezt a forráskezelés (SCM) webhelyén keresztül is meg lehet tenni.

> [!NOTE]
> Az alkalmazástárolóhoz közvetlenül a helyi fejlesztési számítógépről is csatlakozhat SSH, SFTP vagy Visual Studio Code segítségével (Node.js-alkalmazások élő hibakereséséhez). További információkért tekintse meg a [Linuxon futó App Service-ben elérhető távoli hibakereséssel és SSH-val](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html) kapcsolatos cikket.
>

**Hogyan hozhatok létre Linux-App Service sdk-val vagy Azure Resource Manager sablonnal?**

Állítsa **az** App Service fenntartott mezőjét true *(igaz) értékre.*

## <a name="continuous-integration-and-deployment"></a>Folyamatos integráció és üzembe helyezés

**A webalkalmazásom továbbra is egy régi Docker-tároló rendszerképet használ, miután frissítettem a rendszerképet a Docker Hub. Támogatja az egyéni tárolók folyamatos integrációját és üzembe helyezését?**

Igen, a folyamatos integráció/üzembe helyezés beállítását a Azure Container Registry vagy a DockerHubhoz a folyamatos üzembe helyezés és a [Web App for Containers.](./deploy-ci-cd-custom-container.md) Privát beállításregisztrációkhoz a webalkalmazás leállításával és indításával frissítheti a tárolót. Vagy módosíthatja vagy hozzáadhat egy üres alkalmazásbeállítást a tároló frissítésének kényszerítésében.

**Támogatja az átmeneti környezeteket?**

Igen.

**Használhatom a *WebDeploy/MSDeployt* a webalkalmazásom üzembe helyezéséhez?**

Igen, a nevű alkalmazásbeállítást false (hamis) `WEBSITE_WEBDEPLOY_USE_SCM` értékre kell *állítania.*

**Az alkalmazás Gitben való üzembe helyezése meghiúsul Linux-webalkalmazás használata esetén. Hogyan lehet a probléma megoldásához?**

Ha a Git üzembe helyezése nem sikerül a Linux-webalkalmazásban, válasszon az alábbi lehetőségek közül az alkalmazáskód üzembe helyezéséhez:

- A Folyamatos teljesítés (előzetes verzió) funkció használata: Az alkalmazás forráskódját egy Azure DevOps Git-adattárban vagy GitHub-adattárban tárolhatja az Azure Continuous Delivery használatához. További információ: Folyamatos teljesítés [konfigurálása Linux-webalkalmazáshoz.](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/)

- Használja a [ZIP üzembe helyezési](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)API-t: Ennek az API-nak a használatával [SSH-n](configure-linux-open-ssh-session.md) keresztül telepítse a webalkalmazást, és abban a mappában, ahol üzembe szeretné helyezni a kódot. Futtassa az alábbi kódot:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   Ha hibaüzenetet kap arról, hogy a parancs nem található, az előző parancs futtatása előtt telepítse a curl-t a `curl` `apt-get install curl` `curl` használatával.

## <a name="language-support"></a>Nyelvi támogatás

**Webes szoftvercsatornákat szeretnék használni a Node.js alkalmazásban, speciális beállításokat vagy konfigurációkat beállítani?**

Igen, `perMessageDeflate` tiltsa le a kiszolgálóoldali Node.js kódban. Ha például a következő kódot socket.io, használja a következőt:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Támogatja a befejezetlen .NET Core-alkalmazásokat?**

Igen.

**Támogatja a Composert a PHP-alkalmazások függőségkezelőjeként?**

Igen, a Git üzembe helyezése során a Kudunak észlelnie kell, hogy PHP-alkalmazást helyez üzembe (a composer.lock fájl jelenléte miatt), és a Kudu ezután elindítja a composer telepítését.

## <a name="custom-containers"></a>Egyéni tárolók

**Saját egyéni tárolót használok. Azt szeretném, hogy a platform csatlakoztassa az SMB-megosztást a `/home/` könyvtárhoz.**

Ha a beállítás nincs meghatározva, vagy hamisra van állítva, a könyvtár nem lesz megosztva a méretezési példányok között, és az írt fájlok nem maradnak meg az `WEBSITES_ENABLE_APP_SERVICE_STORAGE`   `/home/` újraindítások között.   Ha explicit módon `WEBSITES_ENABLE_APP_SERVICE_STORAGE` *true (igaz) beállítást ad* meg, az engedélyezi a csatlakoztatást.

**Az egyéni tároló indítása hosszú időt vesz igénybe, és a platform az indítás befejezése előtt újraindítja a tárolót.**

Beállíthatja, hogy a platform mennyi ideig várjon, mielőtt újraindítja a tárolót. Ezt úgy állíthatja be, hogy `WEBSITES_CONTAINER_START_TIME_LIMIT` az alkalmazásbeállítás a kívánt értékre van állítva. Az alapértelmezett érték 230 másodperc, a maximális érték pedig 1800 másodperc.

**Mi a privát beállításjegyzék-kiszolgáló URL-címének formátuma?**

Adja meg a beállításjegyzék teljes URL-címét, beleértve a következőket: `http://` vagy `https://` .

**Mi a lemezkép nevének formátuma a privát beállításjegyzékben?**

Adja hozzá a rendszerkép teljes nevét, beleértve a privát beállításjegyzék URL-címét (például myacr.azurecr.io/dotnet:latest). Az egyéni portot használó rendszerképnevek [nem adatokat adhatnak meg a portálon.](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650) A `docker-custom-image-name` beállításhoz használja a [ `az` parancssori eszközt.](/cli/azure/webapp/config/container#az_webapp_config_container_set)

**Egynél több portot is elérhetővé tudok téve az egyéni tároló rendszerképében?**

Egynél több port felfedése nem támogatott.

**Van saját tárhelyem?**

Igen, [a saját tárhelyek előzetes](./configure-connect-to-azure-storage.md) verzióban érhetőek el.

**Miért nem tudok böngészni az egyéni tárolóm fájlrendszerében vagy folyamatokat futtatni az SCM webhelyről?**

Az SCM-hely egy külön tárolóban fut. Nem ellenőrizheti a fájlrendszert vagy az alkalmazástároló futó folyamatait.

**Az egyéni tároló a 80-as porttól más portot figyel. Hogyan konfigurálható az alkalmazásom, hogy a kéréseket erre a portra irányítsa?**

A portok automatikus észlelése automatikus. Megadhat egy WEBSITES_PORT nevű  alkalmazásbeállítást is, és megadhatja a várt portszám értékét. Korábban a platform a *PORT alkalmazásbeállítást* használta. Tervezzük az alkalmazásbeállítás elavultként való használatát, és kizárólag a WEBSITES_PORT *használni.*

**Https-t kell implementáljam az egyéni tárolóban?**

Nem, a platform kezeli a HTTPS-lezárásokat a megosztott előfelületen.

**Port változót kell használnom a kódban a beépített tárolókhoz?**

Nem, a PORT változóra az automatikus portészlelés miatt nincs szükség. Ha a rendszer nem észlel portot, az alapértelmezett érték 80.
Egyéni port manuális konfiguráláshoz használja az EXPOSE utasítást a Dockerfile-ban és az alkalmazásbeállítást (WEBSITES_PORT, valamint egy portértéket a tárolóhoz való kötéshez.

**Egyéni tárolókhoz kell WEBSITES_PORT-t használnom?**

Igen, ez az egyéni tárolókhoz szükséges. Egyéni port manuális konfiguráláshoz használja az EXPOSE utasítást a Dockerfile-ban és az alkalmazásbeállítást (WEBSITES_PORT, valamint egy portértéket a tárolóhoz való kötéshez.

**Használhatom a ASPNETCORE_URLS a Docker-rendszerképben?**

Igen, írja felül a környezeti változót a .NET Core-alkalmazás indítása előtt.
Például Az init.sh szkriptben: export ASPNETCORE_URLS={Saját érték}

## <a name="multi-container-with-docker-compose"></a>Többtárolós tároló a Docker Compose-sal

**Hogyan konfigurálni Azure Container Registry (ACR) többtárolóssal való használatra?**

Ahhoz, hogy az ACR-t  többtárolós tárolóval használjuk, az összes tároló rendszerképét ugyanazon az ACR beállításjegyzék-kiszolgálón kell üzemeltetni. Ha ugyanazon a beállításkiszolgálón vannak, létre kell hoznia az alkalmazásbeállításokat, majd frissítenie kell a Docker Compose konfigurációs fájlt, hogy tartalmazza az ACR-rendszerkép nevét.

Hozza létre a következő alkalmazásbeállításokat:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (teljes URL-cím, például: `https://<server-name>.azurecr.io` )
- DOCKER_REGISTRY_SERVER_PASSWORD (rendszergazdai hozzáférés engedélyezése az ACR-beállításokban)

A konfigurációs fájlban hivatkozhat az ACR-rendszerképre az alábbi példához hasonlóan:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Hogyan tudja, melyik tároló érhető el az internetről?**

- Csak egy tároló nyitható meg hozzáféréshez
- Csak a 80-as és a 8080-as port érhető el (elérhető portok)

A következő szabályok érhetők el annak meghatározásához, hogy melyik tároló érhető el a prioritási sorrendben:

- Az `WEBSITES_WEB_CONTAINER_NAME` alkalmazásbeállítás a tároló nevére van állítva
- Az első tároló, amely a 80-as vagy a 8080-as portot definiálja
- Ha a fentiek közül egyik sem igaz, a fájlban definiált első tároló elérhető (elérhető)


## <a name="web-sockets"></a>Webes szoftvercsatornák

A webes szoftvercsatornák Linux-alkalmazásokban támogatottak.

> [!IMPORTANT]
> A webes szoftvercsatornák jelenleg nem támogatottak az ingyenes App Service linuxos alkalmazásokhoz. Dolgozunk a korlátozás eltávolításán, és tervezzük legfeljebb 5 webes szoftvercsatornás kapcsolat támogatását ingyenes App Service csomagokon.

## <a name="pricing-and-sla"></a>Díjszabás és SLA

**Milyen díjszabással érhető el a szolgáltatás általánosan elérhetővé válik?**

A díjszabás termékváltozatonként és régiónként eltérő, de további részleteket a díjszabási oldalon talál: [App Service díjszabás.](https://azure.microsoft.com/pricing/details/app-service/linux/)

## <a name="other-questions"></a>Egyéb kérdések

**Mit jelent a "Kért funkció nem érhető el az erőforráscsoportban"?**

Ez az üzenet akkor jelenik meg, amikor webalkalmazást hoz létre Azure Resource Manager (ARM) használatával. Egy adott erőforráscsoport jelenlegi korlátozásai alapján nem keverhet Windows- és Linux-alkalmazásokat ugyanabban a régióban.

**Melyek az alkalmazásbeállítások neveiben támogatott karakterek?**

Az alkalmazásbeállításokhoz csak betűket (A–Z, a–z), számokat (0–9) és aláhúzás karaktert (_) használhat.

**Hol kérhetek új funkciókat?**

Ötletét a Web Apps [fórumon küldheti be.](https://aka.ms/webapps-uservoice) Adja hozzá a "[Linux]" címet az ötlet címhez.

## <a name="next-steps"></a>Következő lépések

- [Mi az Azure App Service on Linux?](overview.md#app-service-on-linux)
- [Átmeneti környezetek beállítása az Azure App Service-ben](deploy-staging-slots.md)
- [Folyamatos üzembe helyezés Web App for Containers](./deploy-ci-cd-custom-container.md)
- [Dolgok, amit érdemes tudni: Web Apps Linux](https://techcommunity.microsoft.com/t5/apps-on-azure/things-you-should-know-web-apps-and-linux/ba-p/392472)
