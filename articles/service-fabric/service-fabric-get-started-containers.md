---
title: Azure Service Fabric Container-alkalmazás létrehozása
description: Hozza létre első saját, Windows-alapú tárolóalkalmazását az Azure Service Fabricban. Hozzon létre egy Docker-rendszerképet egy Python-alkalmazással, küldje le a lemezképet egy tároló-beállításjegyzékbe, majd hozza létre és telepítse a tárolót az Azure Service Fabricba.
ms.topic: conceptual
ms.date: 01/25/2019
ms.custom: devx-track-python
ms.openlocfilehash: 197423670ffe05f15fdc5bfd351efdfba33b53cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96533774"
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Az első Service Fabric-tárolóalkalmazás létrehozása Windows rendszeren

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

A meglévő alkalmazások Service Fabric-fürtökön lévő Windows-tárolókban való futtatásához nem szükséges módosítania az alkalmazást. Ez a cikk bemutatja, hogyan hozhat létre [egy Python-](http://flask.pocoo.org/) alapú webalkalmazást tartalmazó Docker-rendszerképet, és hogyan helyezheti üzembe azt egy Azure Service Fabric-fürtön. Emellett meg is fogja osztani a tárolóalapú alkalmazást az [Azure Container Registry](../container-registry/index.yml) használatával. A cikk feltételezi, hogy rendelkezik a Docker használatára vonatkozó alapvető ismeretekkel. A Docker megismeréséhez olvassa el a [Docker áttekintő ismertetését](https://docs.docker.com/engine/understanding-docker/).

> [!NOTE]
> Ez a cikk a Windows fejlesztési környezetére vonatkozik.  A Service Fabric fürt futtatókörnyezetének és a Docker-futtatókörnyezetnek ugyanazon az operációs rendszeren kell futnia.  A Windows-tárolók nem futtathatók Linux-fürtön.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

* Egy fejlesztői számítógép, amelyen a következők futnak:
  * Visual Studio 2015 vagy Visual Studio 2019.
  * [Service FABRIC SDK-t és eszközöket](service-fabric-get-started.md).
  *  Windows rendszerhez készült Docker. [A Docker CE for Windows (stable) letöltése](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Miután telepítette és elindította a Dockert, kattintson a jobb gombbal a tálca ikonjára, és válassza a **Switch to Windows containers** (Váltás Windows-tárolókra) lehetőséget. Ez a lépés szükséges ahhoz, hogy Windows-alapú Docker-rendszerképeket tudjon futtatni.

* Windows-fürt három vagy több olyan csomóponttal, amelyen a Windows Server tárolók futnak. 

  Ebben a cikkben a fürt csomópontjain futó Windows Server verziójának (buildének) meg kell egyeznie a fejlesztői gépen. Ennek az az oka, hogy a Docker-rendszerképet a fejlesztői gépen hozza létre, és kompatibilitási korlátok vannak a tároló operációs rendszer és a gazdagép operációs rendszerének verziói között. További információ: a [Windows Server Container operációs rendszer és a gazdagép operációs rendszerének kompatibilitása](#windows-server-container-os-and-host-os-compatibility). 
  
    A Windows Server-verziónak a fürthöz szükséges tárolókkal való meghatározásához futtassa a `ver` parancsot egy Windows-parancssorból a fejlesztői gépen. [Fürt létrehozása](service-fabric-cluster-creation-via-portal.md)előtt tekintse meg a [Windows Server Container operációs rendszer és az operációs rendszer kompatibilitása](#windows-server-container-os-and-host-os-compatibility) című témakört.

* Egy Azure Container Registry-beállításjegyzék – ehhez [hozzon létre egy tároló-beállításjegyzéket](../container-registry/container-registry-get-started-portal.md) Azure-előfizetésében.

> [!NOTE]
> A tárolók üzembe helyezése a Windows 10 rendszerű Service Fabric-fürtökön támogatott.  [Ebből a cikkből](service-fabric-how-to-debug-windows-containers.md) megtudhatja, hogyan konfigurálhatja a Windows 10 rendszert Windows-tárolók futtatására.

> [!NOTE]
> Service Fabric 6,2-es és újabb verziók támogatják a tárolók üzembe helyezését a Windows Server 1709-es verzióján futó fürtökön.

## <a name="define-the-docker-container"></a>A Docker-tároló definiálása

Állítson össze egy rendszerképet a Docker Hubban található [Python-rendszerkép](https://hub.docker.com/_/python/) alapján.

Adja meg a Docker-tárolót egy Docker-fájlban. A Docker-fájl a környezet tárolón belüli beállítására, a futtatni kívánt alkalmazás betöltésére és a portok hozzárendelésére vonatkozó utasításokat tartalmazza. A Docker-fájl a `docker build` parancs bemenete, amely a rendszerképet létrehozza.

Hozzon létre egy üres könyvtárat és a *Docker-fájlt* (fájlkiterjesztés nélkül). Adja hozzá a következőket a *Docker-fájlhoz*, és mentse a módosításokat:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

További információkért olvassa el a [Docker-fájlra vonatkozó referenciákat](https://docs.docker.com/engine/reference/builder/).

## <a name="create-a-basic-web-application"></a>Alapszintű webalkalmazás létrehozása
Hozzon létre egy olyan Flask-webalkalmazást, amely a 80-as portot figyeli, és a `Hello World!` szöveget adja vissza. Ugyanebben a könyvtárban hozza létre a *requirements.txt* fájlt. Adja hozzá a következőket, és mentse a módosításokat:
```
Flask
```

Hozza létre az *app.py* fájlt, és adja hozzá a következő kódrészletet:

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():

    return 'Hello World!'


if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

<a id="Build-Containers"></a>

## <a name="login-to-docker-and-build-the-image"></a>Bejelentkezés a Docker-be és a rendszerkép összeállítása

A következő lépésben létrehozjuk a webalkalmazást futtató rendszerképet. Ha nyilvános rendszerképeket húz a Docker-ből (például a `python:2.7-windowsservercore` Docker), ajánlott a Docker hub-fiókkal hitelesíteni a névtelen lekéréses kérelem helyett.

> [!NOTE]
> Gyakori névtelen lekéréses kérelmek esetében előfordulhat, hogy a hibák elkerülése érdekében a Docker-hubhoz hasonló, vagy az ahhoz való hitelesítéshez szükséges Docker-hibák jelennek meg `ERROR: toomanyrequests: Too Many Requests.` `You have reached your pull rate limit.` . További információért lásd: [nyilvános tartalom kezelése Azure Container Registryokkal](../container-registry/buffer-gate-public-content.md) .

Nyisson meg egy PowerShell-ablakot, és lépjen a Docker-fájlt tartalmazó könyvtárra. Ezután futtassa le a következő parancsokat:

```
docker login
docker build -t helloworldapp .
```

Ez a parancs létrehozza az új rendszerképet a Docker-fájlban foglalt utasítások alapján, és a `helloworldapp` nevet adja a rendszerképnek (-t címkézéssel). A tárolórendszerkép létrehozásához először az alaprendszerképet kell letöltenie a Docker Hubról, és ahhoz kell hozzáadnia az alkalmazást. 

Miután az összeállító parancs lefutott, futtassa a `docker images` parancsot az új rendszerkép információinak megtekintéséhez:

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása
Ellenőrizze helyben a rendszerkép működését, mielőtt leküldené azt a tároló-beállításjegyzékbe. 

Futtassa az alkalmazást:

```
docker run -d --name my-web-site helloworldapp
```

A *name* nevet ad a futtató tárolónak (a tárolóazonosító helyett).

Miután a tároló elindult, keresse meg az IP-címét, hogy böngészőből is el tudja érni a futó tárolót:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Ha a parancs nem ad vissza semmit, futtassa a következő parancsot, és vizsgálja  -> meg az IP-címhez tartozó NetworkSettings **Networks** elemet:
```
docker inspect my-web-site
```

Csatlakozzon a futó tárolóhoz. Nyisson meg egy webböngészőt, amely a visszaadott IP-címhez mutat, például: "http: \/ /172.31.194.61". A „Hello World!” címsornak kell megjelennie a böngészőben.

A tároló leállításához futtassa a következő parancsot:

```
docker stop my-web-site
```

Törölje a tárolót a fejlesztői gépről:

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>A rendszerkép leküldése a tároló-beállításjegyzékbe

Miután ellenőrizte, hogy a tároló fut-e a fejlesztői gépen, küldje le a rendszerképet a beállításjegyzékébe az Azure Container Registryben.

A futtatásával ``docker login`` Jelentkezzen be a tároló-beállításjegyzékbe a [beállításjegyzékbeli hitelesítő adataival](../container-registry/container-registry-authentication.md).

Az alábbi példában a rendszer egy Azure Active Directory [egyszerű szolgáltatás](../active-directory/develop/app-objects-and-service-principals.md) azonosítóját és jelszavát adja át. Például lehet, hogy hozzárendelt egy egyszerű szolgáltatást a beállításjegyzékhez egy automatizálási forgatókönyvhöz. Vagy bejelentkezhet a beállításjegyzék felhasználónevével és jelszavával.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

A következő parancs létrehoz egy címkét vagy aliast a rendszerképről a beállításjegyzékre mutató teljes elérési úttal. Az alábbi példa a rendszerképet a ```samples``` névtérben helyezi el, hogy ne legyen zsúfolt a beállításjegyzék gyökere.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Küldje le a rendszerképet tároló-beállításjegyzékbe:

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>A tárolóalapú szolgáltatás létrehozása a Visual Studióban
A Service Fabric SDK és -eszközök egy szolgáltatássablont biztosítanak, amellyel tárolóalapú alkalmazást hozhat létre.

1. Indítsa el a Visual Studiót. Válassza a **fájl**  >  **új**  >  **projekt** lehetőséget.
2. Válassza a **Service Fabric application** (Service Fabric-alkalmazás) lehetőséget, nevezze el „MyFirstContainer” néven, és kattintson az **OK** gombra.
3. Az **szolgáltatássablonok** listájában válassza a **Tároló** elemet.
4. Az **Image Name** (Rendszerkép neve) mezőben adja meg a „myregistry.azurecr.io/samples/helloworldapp” rendszerképet, amelyet leküldött a tároló-beállításjegyzékbe.
5. Nevezze el a szolgáltatást, és kattintson az **OK** gombra.

## <a name="configure-communication"></a>A kommunikáció konfigurálása
A tárolóalapú szolgáltatáshoz szükség van egy kommunikációs végpontra. Adja hozzá a protokollt, a portot és a típust tartalmazó `Endpoint` elemet a servicemanifest.xml fájlhoz. Ez a példa egy rögzített 8081-es portot használ erre a célra. Ha nincs megadva port, a rendszer egy véletlenszerű portot választ az alkalmazás porttartományából. 

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```
> [!NOTE]
> A szolgáltatásokhoz további végpontok is hozzáadhatók, ha további végponti elemeket deklarálnak a megfelelő tulajdonságértékek használatával. Mindegyik port csak egy protokoll értékét deklarálhatja.

Egy végpont megadásával a Service Fabric közzéteszi a végpontot az elnevezési szolgáltatásban. A fürtben futó más szolgáltatások feloldhatják ezt a tárolót. Tárolók közötti kommunikációt is folytathat a [fordított proxyval](service-fabric-reverseproxy.md). A kommunikációhoz környezeti változókként adja meg a fordított proxy HTTP-figyelő portját és azon szolgáltatások nevét, amelyekkel kommunikálni kíván.

A szolgáltatás egy adott portot figyel (ebben a példában a 8081-est). Az alkalmazások Azure-beli fürtön való üzembe helyezésekor a fürt és az alkalmazás is Azure-terheléselosztó mögött fut. Az alkalmazásportnak nyitva kell lennie Azure Load Balancerben, hogy a bejövő forgalom elérhesse a szolgáltatást.  Ezt a portot egy [PowerShell-szkripttel](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) vagy az [Azure Portalon](https://portal.azure.com) nyithatja meg az Azure Load Balancerben.

## <a name="configure-and-set-environment-variables"></a>Környezeti változók konfigurálása és beállítása
A szolgáltatásjegyzékben minden kódcsomaghoz megadhatók környezeti változók. Ez a funkció az összes szolgáltatáshoz elérhető attól függetlenül, hogy tárolókként, folyamatokként vagy vendég futtatható fájlokként vannak-e üzembe helyezve. A környezeti változó értékeit felülbírálhatja az alkalmazásjegyzékben, vagy az üzembe helyezés alatt megadhatja őket alkalmazásparaméterekként.

A következő szolgáltatásjegyzékbeli XML-kódrészlet arra mutat be egy példát, hogyan adhat meg környezeti változókat egy kódcsomaghoz:
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

Ezek a környezeti változók bírálhatók felül az alkalmazásjegyzékben:

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <EnvironmentOverrides CodePackageRef="FrontendService.Code">
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Tárolóport–gazdagépport hozzárendelés és tároló–tároló felderítés konfigurálása
Konfiguráljon egy gazdagépportot a tárolóval való kommunikációhoz. A portkötés a gazdagép egyik portjához rendeli hozzá a szolgáltatás által figyelt tárolóportot. Adjon hozzá egy `PortBinding` elemet az ApplicationManifest.xml fájl `ContainerHostPolicies` eleméhez. Ebben a cikkben a `ContainerPort` értéke 80 (a tároló a 80-as portot használja a Docker-fájlban foglalt beállítások szerint), az `EndpointRef` pedig „Guest1TypeEndpoint” (a szolgáltatásjegyzékben korábban definiált végpont). A szolgáltatáshoz a 8081-es porton beérkező kérések a tárolón a 80-as portra vannak leképezve.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```
> [!NOTE]
> A szolgáltatások további PortBindings hozzáadásához deklaráljon további PortBinding elemeket a megfelelő tulajdonságértékek használatával.

## <a name="configure-container-repository-authentication"></a>Tároló-adattár hitelesítésének konfigurálása

Lásd: [tároló-adattár hitelesítése](configure-container-repository-credentials.md), amelyből megtudhatja, hogyan konfigurálhat különböző hitelesítési típusokat a tárolók rendszerképének letöltéséhez.

## <a name="configure-isolation-mode"></a>Az elkülönítési mód konfigurálása
A Windows a tárolók két elkülönítési módját támogatja: a folyamatalapú és a Hyper-V módot. Folyamatelkülönítési módban az ugyanazon a gazdagépen futó összes tároló ugyanazt a kernelt használja, mint a gazdagép. Hyper-V elkülönítési módban az egyes Hyper-V tárolók és a tároló gazdagép kernelei elkülönülnek. Az elkülönítési mód az alkalmazásjegyzék-fájl `ContainerHostPolicies` elemében van meghatározva. A megadható elkülönítési módok a következők: `process`, `hyperv` és `default`. Az alapértelmezett érték a Windows Server rendszerű gazdagépeken futó elkülönítési mód. Windows 10 rendszerű gazdagépeken csak a Hyper-V elkülönítési mód támogatott, így a tároló a Hyper-V elkülönítési módban fut, függetlenül az elkülönítési mód beállításától. A következő kódrészlet azt mutatja be, hogyan van határozható meg az elkülönítési mód az alkalmazásjegyzék-fájlban.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > A HyperV elkülönítési módja az Azure beágyazott virtualizálástámogatással rendelkező Ev3 és Dv3 SKU-ján érhető el. 
   >
   >

## <a name="configure-resource-governance"></a>Az erőforrás-szabályozás konfigurálása
Az [erőforrás-szabályozás](service-fabric-resource-governance.md) korlátozza a tároló által a gazdagépen használható erőforrásokat. Az alkalmazásjegyzékben megadott `ResourceGovernancePolicy` elemmel határozhatók meg erőforráskorlátok a szolgáltatások kódcsomagjaihoz. A következő erőforrásokhoz állíthatók be erőforráskorlátok: Memory, MemorySwap, CpuShares (CPU relatív súlya), MemoryReservationInMB, BlkioWeight (BlockIO relatív súlya). Ebben a példában a Guest1Pkg szolgáltatáscsomag egy magot kap a fürtcsomópontokon, amelyekre el van helyezve. A memóriakorlátok abszolútak, ezért a kódcsomag 1024 MB memóriára van korlátozva (és ugyanennyi a gyenge garanciás foglalás). A kódcsomagok (tárolók vagy folyamatok) nem tudnak ennél a korlátnál több memóriát lefoglalni, és ennek megkísérlése memóriahiány miatti kivételt eredményez. Az erőforráskorlát érvényesítéséhez a szolgáltatáscsomagokban lévő minden kódcsomaghoz memóriakorlátokat kell meghatároznia.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```
## <a name="configure-docker-healthcheck"></a>Docker HEALTHCHECK konfigurálása 

A 6.1-es verzióval kezdődően a Service Fabric automatikusan integrálja a [docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) eseményeket a rendszerállapot-jelentésbe. Ez azt jelenti, hogy ha a tárolón engedélyezett a **HEALTHCHECK**, a Service Fabric jelenti az állapotát, valahányszor a tároló állapota módosul a Docker jelentése szerint. Egy **OK** állapotjelentés jelenik meg a [Service Fabric Explorerben](service-fabric-visualizing-your-cluster.md), amikor a *health_status* értéke *healthy* (megfelelő), és egy **WARNING** jelenik meg, ha a *health_status* értéke *unhealthy* (nem megfelelő). 

A v 6.4 legújabb frissítésének megkezdése után lehetősége van megadnia, hogy a Docker HEALTHCHECK-értékelések hibát jelentsenek. Ha ez a beállítás engedélyezve van, akkor megjelenik egy **OK** állapot jelentés, ha *health_status* *kifogástalan* , és a **hiba** akkor jelenik meg, ha *health_status* *sérült*.

A **HEALTHCHECK** ellenőrzéséhez használt tényleges ellenőrzésre mutató utasításnak jelen kell lennie a tároló rendszerképének létrehozásakor használt Docker.

![A képernyőképen a központilag telepített szervizcsomag NodeServicePackage részletes adatai láthatók.][3]

![HealthCheckUnhealthyApp][4]

![HealthCheckUnhealthyDsp][5]

A **HEALTHCHECK** viselkedését konfigurálhatja az egyes tárolókhoz, ha megadja a **HealthConfig** beállításait a **ContainerHostPolicies** részeként az ApplicationManifest fájlban.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true"
              RestartContainerOnUnhealthyDockerHealthStatus="false" 
              TreatContainerUnhealthyStatusAsError="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
Alapértelmezés szerint a *IncludeDockerHealthStatusInSystemHealthReport* értéke **true (igaz**), a *RestartContainerOnUnhealthyDockerHealthStatus* értéke false ( **hamis**), a *TreatContainerUnhealthyStatusAsError* pedig **false (hamis**) értékre van állítva. 

Ha a *RestartContainerOnUnhealthyDockerHealthStatus* beállítása **true**, egy újra és újra nem megfelelő állapotúnak jelentett tároló újraindul (lehetőleg más csomópontokon).

Ha a *TreatContainerUnhealthyStatusAsError* értéke **true (igaz**), akkor a **hiba** állapotáról szóló jelentések akkor jelennek meg, ha a tároló *health_status* *állapota* nem kifogástalan.

Ha az egész Service Fabric-fürthöz le szeretné tiltani a **HEALTHCHECK** integrációját, az [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) elemet **false** értékre kell állítania.

## <a name="deploy-the-container-application"></a>A tárolóalkalmazás üzembe helyezése
Mentse az összes módosítást, és hozza létre az alkalmazást. Az alkalmazás közzétételéhez kattintson a jobb gombbal a **MyFirstContainer** elemre a Megoldáskezelőben, és válassza a **Közzététel** lehetőséget.

A **Kapcsolati végpont** területen adja meg a fürt kezelési végpontját, Például: `containercluster.westus2.cloudapp.azure.com:19000`. Az ügyfél csatlakozási végpontját a fürt Áttekintés lapján találja az [Azure Portalon](https://portal.azure.com).

Kattintson a **Közzététel** gombra.

A [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) egy webalapú eszköz az alkalmazások és csomópontok vizsgálatához és kezeléséhez a Service Fabric-fürtökben. Nyisson meg egy böngészőt, lépjen a `http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/` helyre, és kövesse az alkalmazás üzembe helyezését. Az alkalmazás üzembe lesz helyezve, azonban hibaállapotban van, amíg a rendszerkép le nem töltődik a fürtcsomópontokra (ez a rendszerkép méretétől függően némi időt vehet igénybe): ![Hiba][1]

Az alkalmazás akkor kész, amikor ```Ready``` állapotba kerül: ![Kész][2]

Nyisson meg egy böngészőt, majd lépjen a következő helyre: `http://containercluster.westus2.cloudapp.azure.com:8081`. A „Hello World!” címsornak kell megjelennie a böngészőben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A fürt futtatása költségekkel jár, ezért érdemes lehet [törölni a fürtöt](./service-fabric-tutorial-delete-cluster.md).

Miután leküldte a rendszerképet a tárolóregisztrációs adatbázisba, törölheti a helyi rendszerképet a fejlesztői számítógépről:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="windows-server-container-os-and-host-os-compatibility"></a>A Windows Server Container operációs rendszer és a gazdagép operációs rendszerének kompatibilitása

A Windows Server-tárolók nem kompatibilisek a gazdagép operációs rendszerének összes verziójával. Például:
 
- A Windows Server 1709-es verziójának használatával létrehozott Windows Server-tárolók nem működnek a Windows Server 2016-es verzióját futtató gazdagépeken. 
- A Windows Server 2016 használatával létrehozott Windows Server-tárolók Hyper-V elkülönítési módban működnek, csak a Windows Server 1709-es verzióját futtató gazdagépeken. 
- A Windows Server 2016-et használó Windows Server-tárolók esetében szükség lehet annak biztosítására, hogy a tároló operációs rendszer és a gazdagép operációs rendszerének változata azonos legyen a Windows Server 2016 rendszert futtató gazdagépen futó folyamatok elkülönítési módjában.
 
További információ: a [Windows-tároló verziójának kompatibilitása](/virtualization/windowscontainers/deploy-containers/version-compatibility).

Vegye figyelembe a gazdagép operációs rendszerének és a tároló operációs rendszerének kompatibilitását a tárolók Service Fabric-fürtön való létrehozásakor és telepítésekor. Például:

- Győződjön meg arról, hogy a fürt csomópontjain lévő operációs rendszernek megfelelő operációs rendszerrel rendelkező tárolókat telepít.
- Győződjön meg arról, hogy a tároló alkalmazáshoz megadott elkülönítési mód konzisztens a tároló operációs rendszer azon csomópontjának támogatásával, amelyen üzembe van helyezve.
- Vegye figyelembe, hogy az operációs rendszernek a fürtcsomópontok vagy tárolók számára történő frissítése hatással lehet a kompatibilitásra. 

A következő eljárásokkal gondoskodhat arról, hogy a tárolók megfelelően legyenek telepítve a Service Fabric-fürtön:

- Használjon explicit képcímkézést a Docker-rendszerképekkel annak a Windows Server operációs rendszernek a verziójának megadásához, amelyből egy tároló készült. 
- Használja az [operációsrendszer-címkézést](#specify-os-build-specific-container-images) az alkalmazás jegyzékfájljában annak biztosításához, hogy az alkalmazás kompatibilis legyen a különböző Windows Server-verziók és-frissítések között.

> [!NOTE]
> A Service Fabric 6,2-es és újabb verzióiban a Windows Server 2016-alapú tárolókat helyileg telepítheti Windows 10 rendszerű gazdagépen. Windows 10 rendszeren a tárolók Hyper-V elkülönítési módban futnak, az alkalmazás jegyzékfájljában beállított elkülönítési módtól függetlenül. További információ: az [elkülönítési mód konfigurálása](#configure-isolation-mode).   
>
 
## <a name="specify-os-build-specific-container-images"></a>Specifikus tárolórendszerképek megadása az operációs rendszer buildje alapján 

Előfordulhat, hogy a Windows Server-tárolók nem kompatibilisek az operációs rendszer különböző verzióival. A Windows Server 2016-et használó Windows Server-tárolók például nem működnek a Windows Server 1709-es verziójában a folyamat elkülönítési üzemmódjában. Ezért ha a fürtcsomópontok a legújabb verzióra frissülnek, az operációs rendszer korábbi verzióival létrehozott Container Services sikertelen lehet. Ha meg szeretné kerülni ezt a futtatókörnyezet 6,1-es verziójával, Service Fabric támogatja a több operációsrendszer-lemezkép megadását a tárolóban, és az operációs rendszer kiépítésével megjelöli azokat az alkalmazás-jegyzékfájlban található operációsrendszer-verziókkal. Az operációs rendszer kiépítési verzióját Windows-parancssorban futtatva érheti el `winver` . Frissítse az alkalmazásjegyzékeket, és operációsrendszer-verziónként adjon meg külön rendszerkép-felülbírálásokat, mielőtt frissítené az operációs rendszert a csomópontokon. A következő kódrészlet azt mutatja be, hogyan adható meg több tároló-rendszerkép az **ApplicationManifest.xml** alkalmazásjegyzék-fájlban:


```xml
      <ContainerHostPolicies> 
         <ImageOverrides> 
           <Image Name="myregistry.azurecr.io/samples/helloworldappDefault" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
      </ContainerHostPolicies> 
```
A Windows Server 2016 buildverziója 14393, a 1709-es verzió buildverziója 16299. A szolgáltatásjegyzék továbbra is csak egy rendszerképet ad meg mindegyik tárolószolgáltatáshoz, ahogy az alábbi kódrészletben is látható:

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > Az operációs rendszer buildverzióját címkéző szolgáltatás csak a Windowson futó Service Fabric esetében érhető el
   >

Ha a virtuális gép mögöttes operációs rendszerének buildverziója 16299 (1709-es verzió), a Service Fabric az ehhez Windows Server verzióhoz tartozó tárolórendszerképet választja ki. Ha a felcímkézett tárolórendszerképek mellett egy fel nem címkézett tárolórendszerkép is található az alkalmazásjegyzékben, a Service Fabric a fel nem címkézett rendszerképet az összes verzióban használható rendszerképként kezeli. A frissítések során felmerülő hibák elkerülése érdekében explicit módon címkézze fel a tárolórendszerképeket.

A címke nélküli tárolórendszerkép a ServiceManifest elemben megadott tárolórendszerképet felülíró értékként fog működni. Ennek értelmében a „myregistry.azurecr.io/samples/helloworldappDefault” rendszerkép felülírja a „myregistry.azurecr.io/samples/helloworldapp” rendszerképnevet a ServiceManifest elemben.

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Példa teljes Service Fabric-alkalmazásra és szolgáltatásjegyzékre
Itt találja a jelen cikkben használt teljes szolgáltatás- és alkalmazásjegyzéket.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>A tároló kényszerített leállítását megelőző időköz beállítása

Konfigurálhat egy időintervallumot a futtatókörnyezet számára, ezzel megadva, hogy az mennyit várjon a tároló eltávolítása előtt, miután megkezdődött a szolgáltatás törlése (vagy másik csomópontba áthelyezése). Az időintervallum konfigurálásával a `docker stop <time in seconds>` parancsot küldi a tárolónak.  További információ: [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). A várakozási időköz a `Hosting` szakaszban van meghatározva. A `Hosting` szakasz a fürt létrehozásakor vagy később is hozzáadható a konfiguráció frissítéséhez. Az alábbi fürtjegyzék kódrészlete azt mutatja be, hogyan adható meg a várakozási időköz:

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
    }
]
```
Az alapértelmezett időintervallum 10 másodperc. Mivel ez egy dinamikus konfiguráció, a csak konfigurációs frissítés a fürtön frissíti az időkorlátot. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Futtatókörnyezet konfigurálása a nem használt tárolórendszerképek eltávolításához

A Service Fabric-fürtöt úgy is konfigurálhatja, hogy eltávolítsa a nem használt tárolórendszerképeket a csomópontról. Ez a konfiguráció lehetővé teszi a lemezterület visszanyerését, ha túl sok tárolórendszerkép található a csomóponton. A szolgáltatás engedélyezéséhez frissítse a fürt jegyzékfájljának [üzemeltetési](service-fabric-cluster-fabric-settings.md#hosting) szakaszát az alábbi kódrészletben látható módon: 


```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "mcr.microsoft.com/windows/servercore|mcr.microsoft.com/windows/nanoserver|mcr.microsoft.com/dotnet/framework/aspnet|..."
          }
          ...
          }
        ]
    } 
]
```

A `ContainerImagesToSkip` paraméternél megadhatja azokat a rendszerképeket, amelyeket nem szabad törölni.  


## <a name="configure-container-image-download-time"></a>Tárolórendszerkép letöltési idejének konfigurálása

A Service Fabric futtatókörnyezete 20 percet foglal le a tárolórendszerképek letöltésére és kicsomagolására, és ez a tárolórendszerképek többségénél elegendő is. Nagyobb rendszerképek esetében, vagy ha a hálózati kapcsolat lassú, szükséges lehet növelni a rendszerkép letöltésének és kibontásának megszakításáig rendelkezésre álló időtartamot. Ez az időtúllépési érték a **ContainerImageDownloadTimeout** attribútummal állítható be a fürtjegyzék **Üzemeltetés** szakaszában, az alábbi kódrészletben látható módon:

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
    }
]
```


## <a name="set-container-retention-policy"></a>Tárolómegőrzési szabályzat megadása

A tárolóindítási hibák diagnosztizálásának elősegítése céljából a Service Fabric (6.1-es vagy újabb verzió esetén) támogatja a megszakadt működésű vagy el sem induló tárolók megőrzését. Ez a szabályzat az **ApplicationManifest.xml** fájlban állítható be az alábbi kódrészletben látható módon:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

A **ContainersRetentionCount** beállítása megadja a hiba esetén megőrzendő tárolók számát. Ha negatív érték van megadva, a rendszer minden olyan tárolót megőriz, amelyen hiba jelentkezik. Ha a **ContainersRetentionCount** attribútum nincs megadva, a rendszer nem őriz meg tárolókat. A **ContainersRetentionCount** attribútum az Alkalmazásparamétereket is támogatja, így a felhasználók különböző értékeket adhatnak meg a tesztelési és az éles fürtökön. A funkció használatakor alkalmazzon elhelyezési korlátozásokat, hogy a tárolószolgáltatás egy adott csomóponton maradjon, és a rendszer ne kerüljön át más csomópontokra. Az ezzel a funkcióval megőrzött tárolókat manuálisan kell eltávolítani.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>A Docker-démon indítása egyéni argumentumokkal

A Service Fabric-futtatókörnyezet 6.2-es vagy újabb verzióiban a Docker-démon egyéni argumentumokkal is elindítható. Ha egyéni argumentumok vannak megadva, a Service Fabric csak a `--pidfile` argumentumot továbbítja a Docker-motornak. A `--pidfile` argumentumként való megadása ezért nem szükséges. Emellett az argumentumnak továbbra is meg kell határoznia, hogy a Docker-démon figyelje a Windows (vagy Linux esetén a Unix-tartománycsatorna) nevesített csövét, hogy a Service Fabric kommunikálni tudjon a démonnal. Az egyéni argumentumok a **ContainerServiceArguments****Üzemeltetés** szakaszában, a fürtjegyzékben adhatók át, amint az az alábbi kódrészletben látható: 
 

```json
"fabricSettings": [
    ...,
    { 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
    } 
]
```

## <a name="next-steps"></a>Következő lépések
* További információk a [tárolók futtatásáról a Service Fabricban](service-fabric-containers-overview.md).
* Tekintse meg a [.NET-alkalmazás üzembe helyezését](service-fabric-host-app-in-a-container.md) ismertető oktatóanyagot.
* További információk a Service Fabric [alkalmazásainak élettartamáról](service-fabric-application-lifecycle.md).
* Tekintse meg [a Service Fabric-tárolók mintakódjait](https://github.com/Azure-Samples/service-fabric-containers) a GitHubon.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
