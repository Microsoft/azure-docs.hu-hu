---
title: Windows Container-alkalmazás létrehozása Service Fabric az Azure-ban
description: Ebben a gyors útmutatóban létrehozza az első saját, Windows-alapú tárolóalkalmazását az Azure Service Fabricban.
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 934b679813323fe144f935811eb613ab68b561db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87089617"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Rövid útmutató: Windows-tárolók üzembe helyezése a Service Fabricben

Az Azure Service Fabric egy elosztott rendszerplatform, amely skálázható és megbízható mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál.

A meglévő alkalmazások Service Fabric-fürtökön lévő Windows-tárolókban való futtatásához nem szükséges módosítania az alkalmazást. Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe a Docker-tárolók előre összeállított rendszerképeit egy Service Fabric-alkalmazásban. Ha elkészült, a Windows Server Core 2016-kiszolgáló és az IIS-tároló fut. Ez a rövid útmutató egy Windows-tároló üzembe helyezését ismerteti. Egy Linux-tároló üzembe helyezéséhez olvassa el [ezt](service-fabric-quickstart-containers-linux.md) a rövid útmutatót.

![Az IIS alapértelmezett webhelye][iis-default]

Ennek a rövid útmutatónak a segítségével megtanulhatja az alábbiakat:

* Docker-rendszerképtároló becsomagolása
* A kommunikáció konfigurálása
* Service Fabric-alkalmazás felépítése és becsomagolása
* A tárolóalkalmazás üzembe helyezése az Azure-on


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés (létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* Egy fejlesztői számítógép, amelyen a következők futnak:
  * Visual Studio 2019 vagy Windows 2019.
  * [Service FABRIC SDK-t és eszközöket](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Docker-rendszerképtároló becsomagolása a Visual Studióval

A Service Fabric SDK és -eszközök egy szolgáltatássablont biztosítanak, amelynek segítségével a tároló üzembe helyezhető egy Service Fabric-fürtben.

Indítsa el a Visual Studiót „rendszergazdaként”.  Válassza a **fájl**  >  **új**  >  **projekt** lehetőséget.

Válassza ki **Service Fabric alkalmazást**, nevezze el "MyFirstContainer", majd kattintson a **Létrehozás** gombra.

A **Hosted Containers and Applications** (Üzemeltetett tárolók és alkalmazások) sablonokból válassza a **Container** (Tároló) elemet.

A **rendszerkép neve** mezőbe írja be a "MCR.microsoft.com/Windows/ServerCore/IIS:windowsservercore-ltsc2016", a [Windows Server Core Server és az IIS alaprendszerképét](https://hub.docker.com/_/microsoft-windows-servercore-iis).

Konfigurálja úgy a tárolóport–gazdagépport leképezést, hogy a szolgáltatáshoz a 80-as porton beérkező kérések a tárolón a 80-as portra legyenek leképezve.  Állítsa a **Tárolóport** beállítást 80, a **Gazdaport** beállítást pedig szintén 80 értékre.  

Nevezze el a szolgáltatást „MyContainerService” néven, majd kattintson az **OK** gombra.

![Új szolgáltatás párbeszédpanel][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>Az operációs rendszer buildjének megadása a tárolórendszerképhez

Előfordulhat, hogy a Windows Server egy bizonyos verziójával épített tárolók a Windows Server egy más verzióját futtató gazdagépen nem működnek. A Windows Server 1709-es verziójának használatával létrehozott tárolók például nem futnak a Windows Server 2016 rendszert futtató gazdagépeken. További tudnivalókat a [Windows Server tároló operációs rendszerének és a gazdagép operációs rendszerének kompatibilitását ismertető cikket](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). 

A Service Fabric-futtatókörnyezet 6.1-es vagy újabb verziójával több operációsrendszer-képet is megadhat tárolónként, és mindegyik rendszerképet megcímkézheti azon operációs rendszer buildverziójával, amelybe az üzembe helyezése történik. Így meggyőződhet arról, hogy az alkalmazása különböző verziójú Windows operációs rendszereket futtató gazdagépeken is működik. További tudnivalókat a [specifikus tárolórendszerképek operációs rendszer buildje alapján végzett megadását ismertető cikkben talál](service-fabric-get-started-containers.md#specify-os-build-specific-container-images). 

A Microsoft különböző rendszerképeket tesz közzé a Windows Server különböző verzióira épülő IIS-verziókhoz. Ahhoz, hogy a Service Fabric biztosan azon Windows Server verziójával kompatibilis tárolót telepítsen, amely azon fürtcsomópontokon fut ahová az alkalmazását is telepíti, az *ApplicationManifest.xml* fájlhoz adja hozzá a következő sorokat. A Windows Server 2016 buildverziója 14393, a 1709-es verzió buildverziója 16299.

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1803" /> 
          <Image Name= "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016" Os="14393" /> 
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

A szolgáltatásjegyzék továbbra is csak egy rendszerképet ad meg a nanoserverhez, `mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016`.

A *ApplicationManifest.xml* fájlban is módosítsa a **PasswordEncrypted** **hamis** értékre. A fiók és a jelszó üres a Docker hub-on található nyilvános tároló-rendszerkép esetében, ezért kikapcsoljuk a titkosítást, mivel az üres jelszavak titkosítása felépítési hibát eredményez.

```xml
<RepositoryCredentials AccountName="" Password="" PasswordEncrypted="false" />
```

## <a name="create-a-cluster"></a>Fürt létrehozása

Az alábbi parancsfájl egy öt csomópontos Service Fabric fürtöt hoz létre, amely egy X. 509 tanúsítvánnyal védett. A parancs létrehoz egy önaláírt tanúsítványt, és feltölti azt egy új kulcstartóba. A rendszer emellett a tanúsítványt egy helyi könyvtárba is átmásolja. További információ a fürtök létrehozásáról a parancsfájl használatával a Service Fabric- [fürt létrehozása](scripts/service-fabric-powershell-create-secure-cluster-cert.md)című témakörben olvasható.

Ha szükséges, telepítse a Azure PowerShellt a [Azure PowerShell útmutatóban](/powershell/azure/)található utasítások alapján.

A következő szkript futtatása előtt a PowerShell futtatásával `Connect-AzAccount` hozzon létre egy, az Azure-hoz való kapcsolódást.

Másolja az alábbi parancsfájlt a vágólapra, és nyissa meg **Windows PowerShell integrált parancsprogram-kezelési környezet**.  Illessze be a tartalmat az üres Untitled1.ps1 ablakába. Ezután adja meg a változók értékeit a következő parancsfájlban:,,, `subscriptionId` `certpwd` `certfolder` `adminuser` , `adminpwd` stb.  A parancsfájl futtatása előtt a megadott címtárnak `certfolder` léteznie kell.

[!code-powershell[main](../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

Miután megadta a változók értékeit, nyomja le az **F5** billentyűt a szkript futtatásához.

A szkript futtatása és a fürt létrehozása után keresse meg a `ClusterEndpoint` kimenetben a következőt:. Például:

```powershell
...
ClusterEndpoint : https://southcentralus.servicefabric.azure.com/runtime/clusters/b76e757d-0b97-4037-a184-9046a7c818c0
```

### <a name="install-the-certificate-for-the-cluster"></a>A fürt tanúsítványának telepítése

Most a PFX-t a *et currentuser\my tanúsítványtárolóba* -tanúsítványtárolóban fogjuk telepíteni. A PFX-fájl a `certfolder` fenti PowerShell-parancsfájl környezeti változója alapján megadott könyvtárba kerül.

Váltson erre a könyvtárra, majd futtassa a következő PowerShell-parancsot, és cserélje le a könyvtárban található PFX-fájl nevét `certfolder` , valamint a `certpwd` változóban megadott jelszót. Ebben a példában az aktuális könyvtár a `certfolder` PowerShell-parancsfájlban a változó által megadott könyvtárra van beállítva. A `Import-PfxCertificate` parancs futtatása:

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysfclustergroup20190130193456.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString Password#1234 -AsPlainText -Force)
```

A parancs visszaadja az ujjlenyomatot:

```powershell
  ...
  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
0AC30A2FA770BEF566226CFCF75A6515D73FC686  CN=mysfcluster.SouthCentralUS.cloudapp.azure.com
```

Jegyezze meg az ujjlenyomat értékét a következő lépéshez.

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Alkalmazás üzembe helyezése az Azure-ban a Visual Studio használatával

Az alkalmazást a létrehozása után telepítheti a fürtben, közvetlenül a Visual Studióból.

A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a **MyFirstContainer** elemre, majd kattintson a **Publish** (Közzététel) parancsra. Ekkor megjelenik a Publish (Közzététel) párbeszédpanel.

Másolja a következőt a **CN =** nevű tartalmat a PowerShell-ablakban a `Import-PfxCertificate` fenti parancs futtatásakor, és adja hozzá a portot `19000` . Például: `mysfcluster.SouthCentralUS.cloudapp.azure.com:19000`. Másolja azt a **kapcsolatok végpont** mezőjébe. Jegyezze fel ezt az értéket, mert egy későbbi lépésben szüksége lesz rá.

Kattintson az **Advanced Connection Parameters** (Speciális kapcsolati paraméterek) elemre, és ellenőrizze a kapcsolati paraméterek információit.  A *FindValue* és a *servercertthumbprint értékeknek egyezniük* értékének meg kell egyeznie az előző lépésben futtatott tanúsítvány ujjlenyomatával `Import-PfxCertificate` .

![Publish (Közzététel) párbeszédpanel](./media/service-fabric-quickstart-containers/publish-app.png)

Kattintson a **Közzététel** gombra.

A fürtben szereplő minden alkalmazásnak egyedi névvel kell rendelkeznie. Ha névütközés van, nevezze át a Visual Studio-projektet, és telepítse újra.

Nyisson meg egy böngészőt, és navigáljon az előző lépésben a **kapcsolódási végpont** mezőjébe helyezett címre. Azt is megteheti, hogy beilleszti előre a `http://` sémaazonosítót, vagy hozzáfűzheti a `:80` portot az URL-címhez. Például http: \/ /mysfcluster.SouthCentralUS.cloudapp.Azure.com:80.

 Ekkor az IIS alapértelmezett webhelyének kell megjelennie: ![Az IIS alapértelmezett webhelye][iis-default]

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A fürt futása során továbbra is felmerülnek a költségek. Érdemes lehet [törölni a fürtöt](./service-fabric-tutorial-delete-cluster.md).

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Docker-rendszerképtároló becsomagolása
* A kommunikáció konfigurálása
* Service Fabric-alkalmazás felépítése és becsomagolása
* A tárolóalkalmazás üzembe helyezése az Azure-on

Ha bővebb információra van szüksége a Windows-tárolók Service Fabricben való használatával kapcsolatban, lépjen tovább a Windows-tárolóalkalmazásokról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Tárolóalkalmazás létrehozása Windowshoz](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
