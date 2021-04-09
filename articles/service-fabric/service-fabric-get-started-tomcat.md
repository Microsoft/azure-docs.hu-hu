---
title: Tároló létrehozása az Apache Tomcat számára Linuxon
description: Hozzon létre Linux-tárolót az Apache Tomcat Serveren futó alkalmazások elérhetővé tétele érdekében az Azure Service Fabricon. Hozzon létre egy Docker-rendszerképet az alkalmazással és az Apache Tomcat-kiszolgálóval, küldje el a lemezképet egy tároló-beállításjegyzékbe, és hozzon létre és helyezzen üzembe egy Service Fabric Container alkalmazást
ms.topic: conceptual
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 3de97bc277195dff2daf5868c0eb9aec5d6e27c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96534029"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Apache Tomcat Servert futtató Service Fabric tároló létrehozása Linux rendszeren
Az Apache Tomcat a Java servlet és a Java Server technológiák népszerű, nyílt forráskódú implementációja. Ez a cikk bemutatja, hogyan hozhat létre egy olyan tárolót az Apache Tomcat használatával és egy egyszerű webalkalmazással, hogyan helyezheti üzembe a tárolót egy Linux rendszerű Service Fabric-fürtön, és hogyan csatlakozhat a webalkalmazáshoz.  

Az Apache Tomcat szolgáltatással kapcsolatos további tudnivalókért tekintse meg az [Apache Tomcat kezdőlapját](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Előfeltételek
* Egy fejlesztői számítógép, amelyen a következők futnak:
  * [Service FABRIC SDK-t és eszközöket](service-fabric-get-started-linux.md).
  * [Linuxhoz készült Docker CE](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric parancssori felület](service-fabric-cli.md)

* Egy Azure Container Registry tároló-beállításjegyzék. Az Azure-előfizetésben létrehozhat egy tároló-beállításjegyzéket [az Azure Portal](../container-registry/container-registry-get-started-portal.md) vagy [Az Azure CLI](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry)használatával. 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Tomcat-rendszerkép létrehozása és helyi futtatása
Az ebben a szakaszban ismertetett lépésekkel egy Apache Tomcat-rendszerkép és egy egyszerű webalkalmazás alapján hozhat létre Docker-rendszerképet, majd futtathatja azt egy tárolóban a helyi rendszeren. 
 
1. A Service Fabric klónozása a Java-minták tárházának [első lépései](https://github.com/Azure-Samples/service-fabric-java-getting-started) a fejlesztői számítógépen.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Módosítsa a címtárakat az Apache Tomcat Server Sample könyvtárba (*Service-Fabric-Java-Getting-Started/Container-Apache-Tomcat-Web-Server-Sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Hozzon létre egy Docker-fájlt a Docker hub-on és a Tomcat Server-mintán található hivatalos [tomcat-rendszerkép](https://hub.docker.com/_/tomcat/) alapján. A *Service-Fabric-Java-Getting-Started/Container-Apache-Tomcat-Web-Server-Sample Directory lapon* hozzon létre egy *Docker* nevű fájlt (fájlkiterjesztés nélkül). Adja hozzá a következőket a *Docker-fájlhoz*, és mentse a módosításokat:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   További információért tekintse meg a [Docker-referenciát](https://docs.docker.com/engine/reference/builder/) .


4. Jelentkezzen be a Docker szolgáltatásba, és futtassa a `docker build` parancsot a webalkalmazást futtató rendszerkép létrehozásához:

   ```bash
   docker login
   docker build . -t tomcattest
   ```

   Ez a parancs létrehozza az új rendszerképet a Docker, a rendszerkép elnevezése (-t címkézés) című rész utasításai alapján `tomcattest` . A tárolók rendszerképének létrehozásához először le kell töltenie az alapképet a Docker hub-ból, és hozzá kell adni az alkalmazást. 

   Miután az összeállító parancs lefutott, futtassa a `docker images` parancsot az új rendszerkép információinak megtekintéséhez:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Ellenőrizze, hogy a tároló alkalmazás helyileg fut-e a tároló beállításjegyzékének leküldése előtt:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` nevezze el a tárolót, így az azonosítója helyett egy rövid nevet használva hivatkozhat rá.
   * `-p` Megadja a tároló és a gazda operációs rendszer közötti port-hozzárendelést. 

   > [!Note]
   > A paraméterrel megnyitott portnak annak `-p` a portnak kell lennie, amelyen a Tomcat-alkalmazás megfigyeli a kérelmeket. Az aktuális példában egy összekötő van konfigurálva a *ApacheTomcat/conf/server.xml* FÁJLBAN a HTTP-kérések 8080-es portjának figyeléséhez. Ez a port a gazdagép 8080-es portjára van leképezve. 

   A többi paraméterrel kapcsolatos további tudnivalókért tekintse meg a [Docker-futtatási dokumentációt](https://docs.docker.com/engine/reference/commandline/run/).

1. A tároló teszteléséhez nyisson meg egy böngészőt, és adja meg a következő URL-címek egyikét. Ekkor megjelenik a "Hello World!" egy változata. üdvözlő képernyő minden URL-címhez.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Hello World/sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Állítsa le a tárolót, és törölje a fejlesztői számítógépről:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>A Tomcat-rendszerkép leküldése a tároló-beállításjegyzékbe
Most, hogy ellenőrizte, hogy a Tomcat-rendszerkép a fejlesztői számítógép egyik tárolójában fut, leküldi egy adattárba egy tároló-beállításjegyzékben, hogy csökkentse a rendszerkép-fejlesztési és üzembe helyezési munkafolyamatok [megszakadását](../container-registry/buffer-gate-public-content.md) . Ez a cikk a rendszerkép tárolására Azure Container Registry használ, de a lépések némelyikének módosításával bármely kiválasztott tároló-beállításjegyzéket használhat. Ebben a cikkben a rendszer feltételezi, hogy a beállításjegyzék neve *myregistry* , és a teljes beállításjegyzék neve myregistry.azurecr.IO. Módosítsa ezeket megfelelően a forgatókönyvhöz. 

1. A futtatásával `docker login` Jelentkezzen be a tároló-beállításjegyzékbe a [beállításjegyzékbeli hitelesítő adataival](../container-registry/container-registry-authentication.md).

   Az alábbi példában a rendszer egy Azure Active Directory [egyszerű szolgáltatás](../active-directory/develop/app-objects-and-service-principals.md) azonosítóját és jelszavát adja át. Például lehet, hogy hozzárendelt egy egyszerű szolgáltatást a beállításjegyzékhez egy automatizálási forgatókönyvhöz. Vagy bejelentkezhet a beállításjegyzék felhasználónevével és jelszavával.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. A következő parancs létrehoz egy címkét vagy aliast a rendszerképről a beállításjegyzékre mutató teljes elérési úttal. Az alábbi példa a rendszerképet a `samples` névtérben helyezi el, hogy ne legyen zsúfolt a beállításjegyzék gyökere.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. Küldje le a rendszerképet tároló-beállításjegyzékbe:

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Az Service Fabric Container-alkalmazás létrehozása és üzembe helyezése
Most, hogy leküldte a Tomcat-lemezképet egy tároló-beállításjegyzékbe, létrehozhat és üzembe helyezhet egy Service Fabric Container-alkalmazást, amely lekéri a Tomcat-rendszerképet a beállításjegyzékből, és a fürtben tároló szolgáltatásként futtatja azt. 

1. Hozzon létre egy új könyvtárat a helyi klónon kívül (a *Service-Fabric-Java-Getting-Started címtár-* fában kívül). Váltson rá, és a Yeoman használatával hozzon létre egy állványt egy tároló alkalmazáshoz: 

   ```bash
   yo azuresfcontainer 
   ```
   Ha a rendszer kéri, adja meg a következő értékeket:

   * Adja meg az alkalmazás nevét: ServiceFabricTomcat
   * Az Application Service neve: TomcatService
   * Adja meg a rendszerkép nevét: adja meg a tároló beállításjegyzékének URL-címét; például: myregistry.azurecr.io/samples/tomcattest.
   * Parancsok: hagyja üresen ezt a mezőt. Mivel ez a rendszerkép meghatározott számításifeladat-belépési ponttal rendelkezik, így nem kell explicit módon megadni a bemeneti parancsokat (tárolón belül futó parancsok, amelyek az indítás után biztosítják a tároló futtatását).
   * A vendég tároló alkalmazás példányainak száma: 1

   ![Tárolókhoz készült Service Fabric Yeoman-generátor](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. A szolgáltatás jegyzékfájljában (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*) adja hozzá a következő XML-kódot a root **ServiceManfest** címkéhez, hogy megnyissa az alkalmazás által a kérelmeket figyelő portot. A **végpont** címkéje deklarálja a protokollt és a portot a végpont számára. Ebben a cikkben a tároló szolgáltatás a 8080-as portot figyeli: 

   ```xml
   <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
       listen. Please note that if your service is partitioned, this port is shared with 
       replicas of different partitions that are placed in your code. -->
      <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
    </Endpoints>
   </Resources>
   ```

11. Az alkalmazás jegyzékfájljában (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*) a **ServiceManifestImport** címke alatt adja hozzá a következő XML-kódot. Cserélje le a **accountname** és a **jelszót** a **RepositoryCredentials** címkére a tároló-beállításjegyzék nevével és a bejelentkezéshez szükséges jelszóval.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   A **ContainerHostPolicies** címke meghatározza a tároló gazdagépek aktiválására vonatkozó házirendeket.
    
   * A **PortBinding** címke konfigurálja a tároló port – gazdagép port-hozzárendelési házirendjét. A **ContainerPort** attribútum értéke 8080, mert a tároló a 8080-es portot teszi elérhetővé a Docker megadott módon. A **EndpointRef** attribútum értéke "endpointTest", az előző lépésben a szolgáltatás jegyzékfájljában meghatározott végpont. Így az 8080-as porton a szolgáltatás felé irányuló bejövő kérések a tároló 8080-es portjára vannak leképezve. 
   * A **RepositoryCredentials** címke meghatározza azokat a hitelesítő adatokat, amelyeket a tárolónak hitelesítenie kell a (privát) adattárral, ahol lekéri a rendszerképet. Nincs szüksége erre a szabályzatra, ha a rendszerkép le lesz húzva egy nyilvános tárházból.
    

12. A *ServiceFabricTomcat* mappában kapcsolódjon a Service Fabric-fürthöz. 

   * A helyi Service Fabric fürthöz való kapcsolódáshoz futtassa a következőt:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Ha biztonságos Azure-fürthöz szeretne csatlakozni, győződjön meg róla, hogy az ügyféltanúsítvány. PEM-fájlként van jelen a *ServiceFabricTomcat* könyvtárban, és futtassa a következőt: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     Az előző parancsban cserélje le az `your-certificate.pem` nevet az ügyféltanúsítvány-fájl nevére. A fejlesztési és tesztelési környezetekben a fürt tanúsítványát gyakran használják ügyféltanúsítványt. Ha a tanúsítvány nem önaláírt, hagyja ki a `-no-verify` paramétert. 
       
     A fürtözött tanúsítványok általában. pfx-fájlként vannak letöltve. Ha még nem rendelkezik a tanúsítvány PEM formátumban való használatával, a következő parancs futtatásával hozhat létre egy. pfx-fájlból származó. PEM-fájlt:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Ha a. pfx fájl jelszava nem védett, használja `-passin pass:` az utolsó paramétert.


13. Futtassa a sablonban megadott telepítési parancsfájlt az alkalmazás fürtön való üzembe helyezéséhez. A szkript átmásolja az alkalmazáscsomag a fürt rendszerkép-tárolójába, regisztrálja az alkalmazás típusát, és létrehozza az alkalmazás egy példányát.

     ```bash
     ./install.sh
     ```

   A telepítési parancsfájl futtatása után nyisson meg egy böngészőt, és navigáljon a Service Fabric Explorer:
    
   * Helyi fürtön használja a `http://localhost:19080/Explorer` ( *localhost* a virtuális gép magánhálózati IP-címére), ha Mac OS X-ben a Csavargót használja.
   * Biztonságos Azure-fürtön használja a következőt: `https://PublicIPorFQDN:19080/Explorer` . 
    
   Bontsa ki az **alkalmazások** csomópontot, és figyelje meg, hogy most már van egy bejegyzés az alkalmazás típusához, a **ServiceFabricTomcatType** és egy másikhoz az adott típus első példányához. Az alkalmazás teljes körű üzembe helyezése eltarthat néhány percig, tehát türelmesnek kell lennie.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Ha a Tomcat-kiszolgálón szeretné elérni az alkalmazást, nyisson meg egy böngészőablakot, és adja meg a következő URL-címek bármelyikét. Ha központilag telepítette a helyi fürtöt, használja a *localhost* elemet a *PublicIPorFQDN*. Ekkor megjelenik a "Hello World!" egy változata. üdvözlő képernyő minden URL-címhez.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
A sablonban megadott eltávolítási parancsfájl használatával törölje az alkalmazás példányát a fürtből, és törölje az alkalmazás típusának regisztrációját.

```bash
./uninstall.sh
```

Miután leküldte a rendszerképet a tároló-beállításjegyzékbe, törölheti a helyi rendszerképet a fejlesztői számítógépről:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Következő lépések
* A Linux-tárolók további funkcióinak gyors lépéseihez olvassa el [az első Service Fabric Container-alkalmazás létrehozása Linux](service-fabric-get-started-containers-linux.md)rendszeren című témakört.
* A Linux-tárolókkal kapcsolatos részletesebb lépésekért olvassa el a [Linux Container App-alkalmazás létrehozása oktatóanyagot](service-fabric-tutorial-create-container-images.md) .
* További információk a [tárolók futtatásáról a Service Fabricban](service-fabric-containers-overview.md).


