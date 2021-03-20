---
title: Azure Service Fabric Docker-összeállítás – előzetes verzió
description: Az Azure Service Fabric a Docker-összeállítás formátumát, így megkönnyíti a meglévő tárolók összehangolása Service Fabric használatával. Ez a támogatás jelenleg előzetes verzióban érhető el.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: f84dd0ecb7a4002182c8455bfd86354d794a6f7c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84691288"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Docker-összeállítás támogatása az Azure Service Fabric (előzetes verzió)

A Docker a [Docker-compose. YML](https://docs.docker.com/compose) fájlt használja több tárolós alkalmazások definiálásához. Annak érdekében, hogy az ügyfelek könnyen megismerjék a Docker-t, hogy az Azure-Service Fabric meglévő tároló-alkalmazásaikat hangolják össze, a platformon natív módon elérhetővé tettük a Docker-összeállítás üzembe helyezésének előzetes támogatását. A Service Fabric a fájlok 3. vagy újabb verzióját is elfogadhatja `docker-compose.yml` . 

Mivel ez a támogatás előzetes verzióban érhető el, csak az összeállítási irányelvek egy részhalmaza támogatott.

Az előzetes verzió használatához hozza létre a 5,7-es vagy újabb verziójú fürtöt a Service Fabric futtatókörnyezettel együtt a Azure Portal és a hozzá tartozó SDK használatával. 

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el, és éles környezetben nem támogatott.
> Az alábbi példák az 6,0-es és az SDK 2,8-es verzióján alapulnak.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Docker-összeállítási fájl üzembe helyezése Service Fabric

A következő parancsok létrehoznak egy Service Fabric alkalmazást (named `fabric:/TestContainerApp` ), amelyet bármely más Service Fabric alkalmazáshoz hasonlóan nyomon követheti és kezelheti. Használhatja a megadott alkalmazásnév állapot-lekérdezéseket.
Service Fabric felismeri a "DeploymentName" kifejezést az összeállítási telepítés azonosítójaként.

### <a name="use-powershell"></a>A PowerShell használata

Hozzon létre egy Service Fabric összeállítási telepítést egy Docker-compose. YML fájlból a következő parancs futtatásával a PowerShellben:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` és `RegistryPassword` tekintse meg a tároló beállításjegyzékének felhasználónevét és jelszavát. Az üzembe helyezés befejezése után a következő paranccsal ellenőrizhető az állapota:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

A következő paranccsal törölheti az összeállítási telepítést a PowerShell használatával:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Az összeállítások PowerShell használatával történő frissítésének elindításához használja a következő parancsot:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Az összeállítási telepítés a PowerShell használatával történő visszaállításához használja a következő parancsot:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

A frissítés elfogadása után a frissítési folyamat nyomon követhető a következő parancs használatával:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Az Azure Service Fabric CLI (sfctl) használata

Másik lehetőségként használhatja a következő Service Fabric CLI-parancsot:

```shell
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

A központi telepítés létrehozása után a következő paranccsal ellenőrizhető az állapota:

```shell
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Az összeállítási telepítés törléséhez használja a következő parancsot:

```shell
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Az összeállítás-telepítési frissítés elindításához használja a következő parancsot:

```shell
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Az összeállítási telepítés frissítésének visszaállításához használja a következő parancsot:

```shell
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

A frissítés elfogadása után a frissítési folyamat nyomon követhető a következő parancs használatával:

```shell
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Támogatott összeállítási irányelvek

Ez az előzetes verzió az összeállítás 3 formátumában lévő konfigurációs beállítások egy részhalmazát támogatja, beleértve a következő primitíveket:

* Szolgáltatások > > replikák központi telepítése
* Szolgáltatások > > elhelyezési > megkötések üzembe helyezése
* Szolgáltatások > > erőforrások > korlátozásai
    * -CPU-megosztások
    * – memória
    * – memória – swap
* Szolgáltatások > parancsok
* Szolgáltatások > környezet
* Szolgáltatások > portok
* Szolgáltatások > rendszerkép
* Szolgáltatások > elkülönítése (csak Windows esetén)
* Szolgáltatások > naplózási > illesztőprogram
* Szolgáltatások > naplózás > illesztőprogram > beállításai
* Kötet & > kötet központi telepítése

Állítsa be úgy a fürtöt, hogy az erőforrás-korlátokat érvényesítse a [Service Fabric erőforrás-szabályozás](service-fabric-resource-governance.md)című témakörben leírtak szerint. Ez az előzetes verzió nem támogatja az összes többi Docker-összeállítási direktívát.

### <a name="ports-section"></a>Portok szakasz

A portok szakaszban adjon meg http-vagy HTTPS-protokollt, amelyet a Service Fabric Service-figyelő fog használni. Ezzel biztosíthatja, hogy a végponti protokoll megfelelően legyen közzétéve az elnevezési szolgáltatással, hogy a fordított proxy engedélyezze a kérelmek továbbítását:
* A nem biztonságos Service Fabric összeállítási szolgáltatásokhoz való átirányításhoz válassza a **/http** lehetőséget. Például **:-"80:80/http"**.
* A biztonságos Service Fabric összeállításához válassza a **/https** lehetőséget. Például **:-"443:443/https"**.

> [!NOTE]
> A/http és a/https portok szakaszának szintaxisa Service Fabric a megfelelő Service Fabric figyelő URL-címének regisztrálásához.  Ha a Docker-összeállítási fájl szintaxisa programozott módon van érvényesítve, az érvényesítési hibát eredményezhet.

## <a name="servicednsname-computation"></a>ServiceDnsName kiszámítása

Ha az összeállítási fájlban megadott szolgáltatásnév teljes tartománynév (azaz egy pont [.]), akkor a Service Fabric által regisztrált DNS-név `<ServiceName>` (a ponttal együtt). Ha nem, akkor az alkalmazás nevének minden egyes elérésiút-szegmense a szolgáltatás DNS-neve tartomány címkéjévé válik, és az első elérésiút-szegmens lesz a legfelső szintű tartomány címkéje.

Ha például a megadott alkalmazásnév `fabric:/SampleApp/MyComposeApp` , `<ServiceName>.MyComposeApp.SampleApp` a a regisztrált DNS-név.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Üzembe helyezés (példány definíciója) és Service Fabric alkalmazás modellje (típus definíciója)

A Docker-compose. YML fájl a tárolók telepíthető készletét írja le, beleértve azok tulajdonságait és konfigurációit.
A fájl például környezeti változókat és portokat tartalmazhat. A Docker-compose. YML fájlban is megadhat központi telepítési paramétereket, például elhelyezési megkötéseket, erőforrás-korlátokat és DNS-neveket.

A [Service Fabric alkalmazás-modell](service-fabric-application-model.md) a szolgáltatások típusát és az alkalmazások típusát használja, ahol több azonos típusú alkalmazás-példány is lehet. Megadhatja például, hogy ügyfélként egy alkalmazás példánya legyen. Ez a típus-alapú modell a futtatókörnyezetben regisztrált azonos típusú alkalmazások több verzióját támogatja.

Például az A ügyfél rendelkezhet olyan alkalmazással, amely a AppTypeA 1,0 típussal lett létrehozva, és a B ügyfél egy másik alkalmazással is rendelkezhet ugyanazzal a típussal és verzióval. Az alkalmazás-jegyzékfájlokban definiálhatja az alkalmazás típusát, és az alkalmazás létrehozásakor meg kell adnia az alkalmazás nevét és a telepítési paramétereket.

Bár ez a modell rugalmasságot biztosít, azt is tervezzük, hogy egy egyszerűbb, példány-alapú üzembe helyezési modellt is támogatunk, ahol a típusok implicitek a manifest-fájlból. Ebben a modellben minden alkalmazás saját független jegyzékfájlt kap. A Docker-compose. YML, amely egy példány-alapú központi telepítési formátum támogatása.

## <a name="next-steps"></a>Következő lépések

* A [Service Fabric alkalmazás modelljének](service-fabric-application-model.md) olvasása
* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)
