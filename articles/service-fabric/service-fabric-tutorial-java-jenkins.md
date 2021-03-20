---
title: A Jenkins konfigurálása Java-alkalmazásokhoz Service Fabric az Azure-ban
description: Ez az oktatóanyag azt mutatja be, hogyan állíthat be folyamatos integrációt a Jenkins segítségével egy Java Service Fabric-alkalmazás üzembe helyezéséhez.
ms.topic: tutorial
ms.date: 08/27/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 4e29104f0586a8e18afb7a640903c57ce988b692
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91531972"
---
# <a name="tutorial-configure-a-jenkins-environment-to-enable-cicd-for-a-java-application-on-service-fabric"></a>Oktatóanyag: Jenkins-környezet konfigurálása a CI/CD engedélyezéséhez Service Fabric-fürtön lévő Java-alkalmazás számára

Ez az oktatóanyag egy sorozat ötödik része. Azt mutatja be, hogy a Jenkins segítségével hogyan helyezheti üzembe egy alkalmazás frissítéseit. Ebben az oktatóanyagban a Service Fabric Jenkins beépülő modult a szavazati alkalmazást futtató GitHub-adattárral együtt használja az alkalmazás fürtön való üzembe helyezéséhez.

A sorozat ötödik részében az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Service Fabric Jenkins-tároló üzembe helyezése a számítógépen
> * Jenkins-környezet előkészítése a Service Fabric-környezetben való üzembe helyezésre
> * Az alkalmazás frissítése

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Service Fabric Reliable Services Java-alkalmazás létrehozása](service-fabric-tutorial-create-java-app.md)
> * [Az alkalmazás üzembe helyezése és hibakeresése egy helyi fürtön](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Alkalmazás üzembe helyezése egy Azure-fürtön](service-fabric-tutorial-java-deploy-azure.md)
> * [Figyelés és diagnosztika beállítása az alkalmazáshoz](service-fabric-tutorial-java-elk.md)
> * CI/CD beállítása

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a Gitet a helyi számítógépre [a Git letöltési oldaláról](https://git-scm.com/downloads). A Gitre vonatkozó további információért lásd a [Git dokumentációját](https://git-scm.com/docs).
* A [Jenkins](https://jenkins.io/) gyakorlati ismerete szükséges.
* Hozzon létre egy [GitHub](https://github.com/)-fiókot. A GitHub működését is ismernie kell.
* Telepítse a [Dockert](https://www.docker.com/community-edition) a számítógépre.

## <a name="pull-and-deploy-service-fabric-jenkins-container-image"></a>A Service Fabric Jenkins-tároló rendszerképének letöltése és telepítése

A Jenkinst egy Service Fabric-fürtben vagy azon kívül is beállíthatja. Az alábbi utasítások a fürtön kívüli beállítást mutatják be egy Docker-rendszerkép használatával. Ha szeretné, a Jenkins előre konfigurált összeállítási környezetét is használhatja. A következő tárolórendszerképen előre telepítve van a Service Fabric beépülő modul, és azonnal használható a Service Fabricben.

1. Kérje le a Service Fabric Jenkins-tárolójának rendszerképét: ``docker pull rapatchi/jenkins:v10``. A rendszerképhez előre telepítve van a Service Fabric Jenkins beépülő modulja.

1. Futtassa a tárolórendszerképet, és adja meg azt a helyet, ahol az Azure tanúsítványait tárolja a csatlakoztatott helyi gépen.

    ```bash
    docker run -itd -p 8080:8080 -v /service-fabric-java-quickstart/AzureCluster rapatchi/jenkins:v10
    ```

1. Kérje le a tárolórendszerkép-példány azonosítóját. A ``docker ps –a`` paranccsal az összes Docker-tárolót listázhatja

1. Kérje le a Jenkins-példány jelszavát a következő parancs futtatásával:

    ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```

    Ha a tároló azonosítója 2d24a73b5964, használja a 2d24 értéket.
    * Ez a jelszó szükséges ahhoz, hogy bejelentkezzen a Jenkins-irányítópultra a ``http://<HOST-IP>:8080`` portálról.
    * Az első bejelentkezés után létrehozhatja saját felhasználói fiókját, vagy maradhat a rendszergazdai fiók használata mellett.

1. Állítsa be a GitHubot a Jenkins használatához az [új SSH-kulcs létrehozásával és SSH-ügynökhöz adásával](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) foglalkozó cikk lépéseit követve. Mivel a parancsok futtatása a Docker-tárolóból történik, kövesse a Linux-környezetre vonatkozó útmutatást.
   * Kövesse a GitHub utasításait az SSH-kulcs létrehozásához. Ezután adja hozzá az SSH-kulcsot ahhoz a GitHub-fiókhoz, amely az adattárat üzemelteti.
   * Futtassa a fenti hivatkozással elérhető parancsokat a Jenkins Docker-felületén (és ne a saját gazdagépén).
   * Ahhoz, hogy a saját gazdagépéről jelentkezhessen be a Jenkinsbe, használja a következő parancsokat:

     ```sh
     docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
     ```

     Győződjön meg arról, hogy a fürt vagy gép, ahol a Jenkins-tároló rendszerképe fut, nyilvános IP-címmel rendelkezik. A nyilvános IP-cím lehetővé teszi, hogy a Jenkins-példány megkapja a GitHub értesítéseit.

## <a name="create-and-configure-a-jenkins-job"></a>Jenkins-feladatok létrehozása és konfigurálása

1. Először is, ha nem rendelkezik olyan adattárral, amelyet a szavazási projekt a GitHubon való üzemeltetéséhez használhat, hozzon létre egyet. Az oktatóanyag a továbbiakban **dev_test** néven hivatkozik erre az adattárra.

1. Hozzon létre egy **új elemet** a Jenkins irányítópultján a ``http://<HOST-IP>:8080`` címen.

1. Adjon nevet az elemnek (pl. **MyJob**). Válassza a **free-style project** (szabad projekt) lehetőséget, majd kattintson az **OK** gombra.

1. Nyissa meg a feladat oldalát, majd kattintson a **Configure** (Konfigurálás) elemre.

   a. Az általános beállításokban jelölje be a **GitHub project** (GitHub-projekt) jelölőnégyzetet, és adja meg a GitHub-projekt URL-címét. Ez az URL-cím üzemelteti azt a Service Fabric Java-alkalmazást, amelyet integrálni szeretne a Jenkins folyamatos integrációs és üzembe helyezési (CI/CD) folyamatával (például: ``https://github.com/testaccount/dev_test``).

   b. A **Source Code Management** (Forráskódkezelés) szakaszban válassza a **Git** elemet. Itt adhatja meg azt az adattár-URL-címet, amely a Jenkins CI/CD folyamatával integrálni kívánt Service Fabric Java-alkalmazást tartalmazza (például: *`https://github.com/testaccount/dev_test.git`* ). Itt adhatja meg azt is, hogy melyik ágat kívánja létrehozni, például: **/master**.

1. Adja meg a *GitHub* beállítását (amelyiken az adattár üzemel), így létrejöhet a kommunikáció a Jenkinsszel. Ehhez a következő lépések szükségesek:

   a. Nyissa meg a GitHub-adattár oldalát. Lépjen a **Beállítások**  >  **integrációk és szolgáltatások** menüpontra.

   b. Válassza az **Add Service** (Szolgáltatás hozzáadása) elemet, írja be a **Jenkins** kifejezést, majd válassza ki a **Jenkins-GitHub beépülő modult**.

   c. Adja meg a Jenkins-webhook URL-címét (alapértelmezés szerint ez a következő: ``http://<PublicIPorFQDN>:8081/github-webhook/``). Kattintson az **add/update service** (Szolgáltatás hozzáadása/frissítése) elemre.

   d. A Jenkins-példányra egy tesztesemény érkezik. A GitHubban egy zöld pipa jelenik meg a webhook mellett, és a projekt létrejön.

   ![A Service Fabric Jenkins konfigurálása](./media/service-fabric-tutorial-java-jenkins/jenkinsconfiguration.png)

1. A **Build Triggers** (Eseményindítók létrehozása) területen válassza ki a kívánt felépítési lehetőséget. Jelen esetben minden alkalommal szeretnénk elindítani egy felépítést, amikor valamilyen módosításra kerül sor az adattárban. Így a kiválasztandó lehetőség a következő: **GitHub hook trigger for GITScm polling** (GitHub beavatkozási pont eseményindító GITScm lekérdezés esetén).

1. A **Build** (Felépítés) szakaszban az **Add build step** (Felépítési lépés hozzáadása) legördülő listából válassza az **Invoke Gradle Script** (Gradle szkript meghívása) lehetőséget. A megjelenő vezérlőn nyissa meg a speciális menüt, és adja meg a **fő felépítési szkript** elérési útját az alkalmazásához. A rendszer felveszi a build.gradle elemet a megadott elérési útból, és annak megfelelően működik.

    ![Service Fabric, Jenkins felépítési művelet](./media/service-fabric-tutorial-java-jenkins/jenkinsbuildscreenshot.png)

1. A **Post-Build Actions** (Felépítés utáni műveletek) legördülő menüből válassza a **Deploy Service Fabric Project** (Service Fabric-projekt üzembe helyezése) elemet. Itt meg kell adnia annak a fürtnek az adatait, ahová a Jenkins által lefordított Service Fabric-alkalmazást üzembe kívánja helyezni. A tanúsítvány elérési útja megegyezik a hellyel, ahová a kötet csatlakoztatva van (/tmp/myCerts).

    Az üzembe helyezéshez szükséges egyéb adatokat is megadhat. Az alábbi képernyőképen áttekintheti, hogy milyen alkalmazásadatokat lehet megadni:

    ![Felépítés utáni műveletek párbeszédpanel további Service Fabric projekt-telepítési lehetőségekkel](./media/service-fabric-tutorial-java-jenkins/sfjenkins.png)

    > [!NOTE]
    > Ha a Service Fabricet használja a Jenkins-tároló rendszerképének üzembe helyezéséhez, a fürtnek meg kell egyeznie azzal, ahol a Jenkins-tárolóalkalmazás található.
    >

1. Kattintson a **Mentés** gombra.

## <a name="update-your-existing-application"></a>Meglévő alkalmazás frissítése

1. Frissítse a HTML címét a *VotingApplication/VotingWebPkg/Code/wwwroot/index.html* fájlban a **Service Fabric Voting Sample v2** hozzáadásával.

    ```html
    <div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
        <div class="container-fluid">
            <div class="row">
                <div class="col-xs-8 col-xs-offset-2 text-center">
                    <h2>Service Fabric Voting Sample V2</h2>
                </div>
            </div>
        </div>
    </div>
    ```

1. Frissítse az **ApplicationTypeVersion** és a **ServiceManifestVersion** verziószámát **2.0.0** értékre a *Voting/VotingApplication/ApplicationManifest.xml* fájlban.

    ```xml
    <?xml version="1.0" encoding="utf-8" standalone="no"?>
    <ApplicationManifest xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingApplicationType" ApplicationTypeVersion="2.0.0">
      <Description>Voting Application</Description>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="2.0.0"/>
      </ServiceManifestImport>
      <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="VotingDataServicePkg" ServiceManifestVersion="1.0.0"/>
        </ServiceManifestImport>
        <DefaultServices>
          <Service Name="VotingWeb">
             <StatelessService InstanceCount="1" ServiceTypeName="VotingWebType">
                <SingletonPartition/>
             </StatelessService>
          </Service>
       <Service Name="VotingDataService">
                <StatefulService MinReplicaSetSize="3" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="3">
                    <UniformInt64Partition HighKey="9223372036854775807" LowKey="-9223372036854775808" PartitionCount="1"/>
                </StatefulService>
            </Service>
        </DefaultServices>
    </ApplicationManifest>
    ```

1. Frissítse a **ServiceManifest****Version** mezőjét és a **CodePackage****Version** mezőjét a *Voting/VotingApplication/VotingWebPkg/ServiceManifest.xml* fájlban a **2.0.0** értékre.

    ```xml
    <CodePackage Name="Code" Version="2.0.0">
    <EntryPoint>
        <ExeHost>
        <Program>entryPoint.sh</Program>
        </ExeHost>
    </EntryPoint>
    </CodePackage>
    ```

1. Egy olyan Jenkins-feladat inicializálásához, amely egy alkalmazás frissítését végzi, küldje le az új módosításokat a GitHub-adattárba.

1. A Service Fabric Explorerben kattintson az **Applications** (Alkalmazások) legördülő menüre. A frissítés állapotának megtekintéséhez kattintson az **Upgrades in Progress** (Folyamatban lévő frissítések) fülre.

    ![Folyamatban lévő frissítés](./media/service-fabric-tutorial-create-java-app/upgradejava.png)

1. Ha a **http:// \<Host-IP> : 8080**  -as verzióhoz fér hozzá, a teljes funkcionalitással rendelkező szavazási alkalmazás már működik.

    ![Szavazóalkalmazás – helyi](./media/service-fabric-tutorial-java-jenkins/votingv2.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Service Fabric Jenkins-tároló üzembe helyezése a számítógépen
> * Jenkins-környezet előkészítése a Service Fabric-környezetben való üzembe helyezésre
> * Az alkalmazás frissítése

* Egyéb [Java-minták](https://github.com/Azure-Samples/service-fabric-java-getting-started) megtekintése.
