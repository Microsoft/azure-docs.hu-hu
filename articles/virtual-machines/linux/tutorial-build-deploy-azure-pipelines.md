---
title: Oktatóanyag – CI/CD – Azure-beli virtuális gépek az Azure-folyamatokkal
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be egy Node.js-alkalmazás folyamatos integrációját (CI) és folyamatos üzembe helyezését (CD) az Azure-beli virtuális gépekhez a YAML-alapú Azure-folyamat használatával.
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops, devx-track-js
ms.openlocfilehash: 49282bf6cbc7c24b75fbe3f1bbe68bd1fac62ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552490"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Oktatóanyag: alkalmazás üzembe helyezése az Azure-beli linuxos virtuális gépeken az Azure DevOps Services és az Azure-folyamatok használatával

A folyamatos integráció (CI) és a folyamatos üzembe helyezés (CD) egy olyan folyamatot alkotnak, amellyel minden kód végrehajtása után létrehozhatja, felszabadíthatja és üzembe helyezheti a kódot. Ez a dokumentum az Azure-folyamatokat használó többszámítógépes üzembe helyezéshez szükséges CI/CD-folyamat beállításával kapcsolatos lépéseket tartalmazza.

Az Azure-folyamatok teljes körűen Kiemelt CI/CD Automation-eszközöket biztosítanak a virtuális gépekhez, helyszíni vagy bármilyen Felhőbeli üzembe helyezéshez.

Ebben az oktatóanyagban egy YAML-alapú CI/CD-folyamatot állít be az alkalmazás Azure-beli, Linux rendszerű virtuális gépekkel [való üzembe](/azure/devops/pipelines/process/environments) helyezéséhez erőforrásként, amelyek mindegyike webkiszolgálóként szolgál az alkalmazás futtatásához.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Minta alkalmazás beszerzése.
> * Hozzon létre egy YAML-alapú Azure-folyamatokat CI-folyamattal a minta alkalmazás létrehozásához.
> * Azure-adatfolyamatok környezetének létrehozása az Azure-beli virtuális gépekhez
> * Hozzon létre egy Azure-folyamatok CD-folyamatát.
> * Manuális és CI által kiváltott üzembe helyezések végrehajtása.

## <a name="before-you-begin"></a>Előkészületek

* Jelentkezzen be az Azure DevOps Services-szervezetbe ( **https://dev.azure.com/** ). 
  Beszerezhet egy [ingyenes Azure DevOps Services-szervezetet](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > További információ: [Csatlakozás az Azure DevOps Services-hez](/azure/devops/organizations/projects/connect-to-projects).

*  Telepítési célként egy Linux rendszerű virtuális gépre van szükség.  További információért lásd: [Linux rendszerű virtuális gépek létrehozása és kezelése az Azure CLI-vel](./tutorial-manage-vm.md).

*  Nyissa meg a virtuális géphez a 80-as a bejövő portot. További információ: [Hozzon létre egy hálózati biztonsági csoportot az Azure Portal használatával](../../virtual-network/tutorial-filter-network-traffic.md).

## <a name="get-your-sample-app-code"></a>A minta alkalmazás kódjának beolvasása

Ha már van egy olyan alkalmazás a GitHubban, amelyet telepíteni szeretne, próbáljon meg létrehozni egy folyamatot ehhez a kódhoz.

Ha azonban új felhasználó, akkor lehet, hogy a minta kódjával jobban megkezdheti a használatát. Ebben az esetben ez a tárház a GitHubban:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> A Petclinic egy [Maven](https://spring.io/guides/gs/maven/)használatával létrehozott [Java Spring boot](https://spring.io/guides/gs/spring-boot) -alkalmazás.

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Ezt a Node.js alkalmazást a [Yeoman](https://yeoman.io/learning/index.html)-on keresztül építették. Express-t bowert és gruntot használ. És rendelkezik néhány npm-csomaggal függőségként.
> A minta egy szkriptet is tartalmaz, amely beállítja az Nginx kiszolgálót, és üzembe helyezi az alkalmazást. A végrehajtása a virtuális gépeken történik. Pontosabban a szkript az alábbi műveleteket végzi:
> 1. Telepíti a Node-ot, az Nginx kiszolgálót és a PM2-t.
> 2. Konfigurálja az Nginx kiszolgálót és a PM2-t.
> 3. Elindítja a Node alkalmazást.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>A Linux rendszerű virtuális gép előfeltételei

A fent említett példák az Ubuntu 16,04-es verziójában lettek tesztelve, és azt javasoljuk, hogy a Linux virtuális gép ugyanazon verzióját használja ehhez a rövid útmutatóhoz.
Kövesse az alábbiakban ismertetett további lépéseket az alkalmazáshoz használt futtatókörnyezeti verem alapján.

#### <a name="java"></a>[Java](#tab/java)

- A Java Spring boot és a Spring Cloud alapú alkalmazások üzembe helyezéséhez hozzon létre egy Linux virtuális gépet az Azure-ban [a sablon használatával](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) , amely teljes körűen támogatott OpenJDK-alapú futtatókörnyezetet biztosít.
- Java servletek a Tomcat-kiszolgálón való üzembe helyezéséhez hozzon létre egy Linux virtuális gépet a Java 8 használatával [Az Azure-](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) sablonnal, és [konfigurálja a Tomcat 9. x-et szolgáltatásként](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- A Java EE-alapú alkalmazások üzembe helyezéséhez Azure-sablonnal hozzon létre egy [linuxos virtuális gépet + Java + WebSphere 9. x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) vagy Linux rendszerű [virtuális gépet + Java + Weblogic 12. x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) vagy Linux rendszerű virtuális [gép + Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) + WildFly/JBoss 14 


#### <a name="javascript"></a>[JavaScript](#tab/java-script)

JavaScript-alkalmazás vagy Node.js-alkalmazás telepítéséhez egy Linux rendszerű virtuális gépnek kell lennie az Nginx webkiszolgálóval az alkalmazás üzembe helyezéséhez.
Ha még nem rendelkezik a Linux rendszerű virtuális géppel, hozzon létre egyet most az Azure-ban az [ebben a példában](./quick-create-cli.md)szereplő lépések segítségével.

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Azure-folyamatokat tartalmazó környezet létrehozása Azure-beli virtuális gépekkel

A virtuális gépeket erőforrásként lehet felvenni [környezetekben](/azure/devops/pipelines/process/environments) , és több gépen üzemelő példányokhoz is megcélozható. A környezeten belüli üzembe helyezési előzmények a virtuális gépekről a folyamatra, majd a végrehajtásra vonatkozó nyomon követést biztosítanak.

Létrehozhat egy környezetet a "**környezetek**" csomóponton belül a "**folyamatok**" szakaszban.
1.  Jelentkezzen be az Azure DevOps-szervezetbe, és navigáljon a projekthez.
2.  A projektben navigáljon a **folyamatok** lapra. Ezután válassza a **környezetek** lehetőséget, majd kattintson a **környezet létrehozása** lehetőségre. Adja meg a környezet és a **Leírás** **nevét** (kötelező).
3.  Válassza a **Virtual Machines** lehetőséget a környezethez hozzáadni kívánt **erőforrásként** , majd kattintson a **tovább** gombra.
4.  Válassza az operációs rendszer (Windows/Linux) lehetőséget, és **másolja a PS regisztrációs parancsfájlt**. 
5.  Most futtassa az átmásolt parancsfájlt egy rendszergazdai PowerShell-parancssorból az egyes megcélzott virtuális gépeken, amelyek regisztrálva lesznek a környezetben.
    > [!NOTE]
    > - A bejelentkezett felhasználó személyes hozzáférési jogkivonata előre be van helyezve a parancsfájlba, amely ugyanazon a napon jár le, így a másolt parancsfájl használhatatlanná válik.
    > - Ha a virtuális gépen már fut ügynök, adjon meg egy egyedi nevet az ügynöknek a környezethez való regisztrációhoz.
6.  A virtuális gép regisztrálása után a rendszer a környezet "erőforrások" lapján megjelenő környezeti erőforrásként fog megjelenni.

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  További virtuális gépek hozzáadásához tekintse meg és másolja újra a szkriptet az "erőforrás hozzáadása" gombra kattintva, és válassza a "Virtual Machines" lehetőséget erőforrásként. Ez a szkript változatlan marad az összes virtuális gép számára, amelyet hozzá kíván adni ehhez a környezethez. 
8.  Mindegyik gép az Azure-folyamatokkal együttműködik az alkalmazás üzembe helyezésének koordinálásához.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. Hozzáadhat címkéket a virtuális géphez az interaktív PowerShell regisztrációs parancsfájl részeként (vagy), ha az erőforrások nézetben az egyes virtuálisgép-erőforrások végén lévő három pontra kattint, az erőforrás nézetből is hozzáadhat vagy eltávolíthat.

   A hozzárendelt címkék lehetővé teszik, hogy meghatározott virtuális gépekre korlátozza a telepítést, ha a környezet egy telepítési feladatokban van használatban. A címkék mindegyike 256 karakterből állhat, de a használható címkék száma nem korlátozott.

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>A CI-Build folyamatának meghatározása

Szüksége lesz egy folyamatos integrációs (CI) build folyamatra, amely közzéteszi a webalkalmazást, valamint egy üzembe helyezési parancsfájlt, amely helyileg futtatható az Ubuntu-kiszolgálón. Állítson be egy CI-Build folyamatot a használni kívánt futtatókörnyezet alapján. 

1. Jelentkezzen be az Azure DevOps-szervezetbe, és navigáljon a projekthez.

1. A projektben navigáljon a **folyamatok** lapra. Ezután válassza ki a műveletet egy új folyamat létrehozásához.

1. Tekintse át a varázsló lépéseit úgy, hogy először kiválasztja a **githubot** a forráskód helyeként.

1. Előfordulhat, hogy a rendszer átirányítja a GitHubra a bejelentkezéshez. Ha igen, adja meg a GitHub hitelesítő adatait.

1. Amikor megjelenik a Tárházak listája, válassza ki a kívánt minta alkalmazás-tárházat.

1. Az Azure-folyamatok elemzik a tárházat, és javaslatot tesznek a megfelelő folyamat-sablonra.

#### <a name="java"></a>[Java](#tab/java)

Válassza ki az **alapszintű** sablont, és másolja az alábbi YAML-kódrészletet, amely létrehozza a Java-projektet, és teszteket futtat az Apache Maven használatával:

```YAML
jobs:
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

További útmutatásért kövesse a [Java-alkalmazás létrehozása a mavenrel](/azure/devops/pipelines/ecosystems/java)című témakörben leírt lépéseket.

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Válassza ki az **alapszintű** sablont, és másolja az alábbi YAML-kódrészletet, amely létrehoz egy általános Node.js projektet a NPM.

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

További útmutatásért kövesse a [Node.js alkalmazás felépítése a Nyelő](/azure/devops/pipelines/ecosystems/javascript)használatával című témakör lépéseit.

- Tekintse meg a folyamatot, hogy megtudja, mi ez. Győződjön meg arról, hogy az összes alapértelmezett bemenet megfelel a kódnak.

- Válassza a **Mentés és Futtatás** lehetőséget, majd válassza **a végrehajtás közvetlenül a fő ágat** lehetőséget, majd válassza a **Mentés és Futtatás** újra lehetőséget.

- A rendszer elindít egy új futtatást. Várja meg, amíg a futtatás befejeződik.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>A Linux rendszerű virtuális gépen való üzembe helyezéshez szükséges CD-lépések megadása

1. Módosítsa a fenti folyamat YAML-fájlját úgy, hogy az a környezetre és a korábban a YAML szintaxist használó virtuálisgép-erőforrásokra hivatkozó [központi telepítési feladatot](/azure/devops/pipelines/process/deployment-jobs) tartalmazzon:

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web
   ```
2. A környezetből kiválaszthatja a virtuális gépek meghatározott készleteit, hogy a központi telepítést a környezetben minden egyes virtuális géphez definiált **címkék** megadásával kapja meg.
[Itt](/azure/devops/pipelines/yaml-schema?tabs=schema#deployment-job) látható az üzembe helyezési feladatokhoz tartozó teljes YAML séma.

3. Megadhatja `runOnce` a vagy `rolling` a as telepítési stratégiát is. 

   `runOnce` a legegyszerűbb üzembe helyezési stratégia, amelyben minden életciklus-Hook, azaz a `preDeploy` `deploy` `routeTraffic` és a `postRouteTraffic` , egyszerre kerül végrehajtásra. Ezt követően a `on:` `success` vagy `on:` `failure` a végrehajtása történik.

   Alább látható a következő példában szereplő YAML-kódrészlet `runOnce` :
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. Alább látható egy példa arra a YAML-kódrészletre, amelyet a virtuális gépekre vonatkozó gördülő stratégia definiálására használhat, és az egyes iterációkban 5 célt kell megadnia. `maxParallel` meghatározza, hogy a rendszer mely célokhoz helyezhető üzembe, párhuzamosan. A kiválasztási fiókokat az abszolút szám vagy a célok azon hányada alapján kell megtartani, amelynek a központi telepítése folyamatban van. A telepítés során a sikeres és a sikertelenség feltételeinek meghatározására is használható.

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   A feladatok minden futtatásakor a rendszer rögzíti a telepítési előzményeket a `<environment name>` létrehozott és a virtuális gépeket regisztráló környezettel.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>A folyamat futtatása és a nyomkövetési nézetek beolvasása a környezetben
A környezet üzembe helyezési nézete biztosítja a véglegesítés és a munkaelemek teljes nyomon követését, valamint a környezetek és erőforrások közötti, folyamaton belüli üzembe helyezési előzményeket.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Következő lépések
- Folytathatja az imént létrehozott [folyamat testreszabását](/azure/devops/pipelines/customize-pipeline) .
- Ha szeretné megtudni, hogy mit tehet a YAML-folyamatokban, tekintse meg a [YAML-séma referenciáját](/azure/devops/pipelines/yaml-schema).
- Annak elsajátításához, hogyan helyezhet üzembe LAMP (Linux, Apache, MySQL és PHP) stacket, lépjen tovább a következő oktatóanyagra.

> [!div class="nextstepaction"]
> [LAMP verem telepítése](tutorial-lamp-stack.md)
