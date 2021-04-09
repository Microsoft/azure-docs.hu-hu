---
title: Önálló fürt üzembe helyezésének előkészítése
description: A környezet előkészítéséhez és a fürtkonfiguráció létrehozásához kapcsolódó dokumentáció, amely az éles számítási feladatok kezelésére szolgáló fürt üzembe helyezése előtt megfontolandó.
ms.topic: conceptual
ms.date: 9/11/2018
ms.openlocfilehash: 18b8b0ce8c0e877bf9dd274596b19f85b1febe12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790952"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>A Service Fabric önálló fürt üzembe helyezésének megtervezése és előkészítése

<a id="preparemachines"></a>A fürt létrehozása előtt hajtsa végre a következő lépéseket.

## <a name="plan-your-cluster-infrastructure"></a>A fürt infrastruktúrájának megtervezése
Arra készül, hogy létrehoz egy Service Fabric-fürtöt a "saját" számítógépeken, így eldöntheti, hogy a fürt milyen hibákat szeretne túlélni. Szükség van-e például külön hálózati vonalakra vagy internetkapcsolatra a gépekhez? Emellett vegye figyelembe a gépek fizikai biztonságát is. Hol találhatók a gépek, és kinek van szüksége hozzájuk? A döntések elvégzése után logikailag leképezheti a gépeket a különböző tartalék tartományokra (lásd a következő lépést). A termelési fürtök infrastruktúra-tervezése nagyobb szerepet játszik, mint a tesztelési fürtök esetében.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>A tartalék tartományok és a frissítési tartományok számának meghatározása
A tartalék [ *tartomány* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) a meghibásodás fizikai egysége, és közvetlenül kapcsolódik az adatközpontok fizikai infrastruktúrához. A tartalék tartományok olyan hardver-összetevőkből (számítógépek, kapcsolók, hálózatok stb.) állnak, amelyek egyetlen meghibásodási ponton osztoznak. Bár a tartalék tartományok és az állványok között nincs 1:1-es leképezés, az egyes állványok meghibásodási tartománynak tekinthetők.

Ha a ClusterConfig.json tartalék megadását választja, megadhatja az egyes FD-ket. Service Fabric támogatja a hierarchikus tartalék, így az infrastruktúra-topológiát is tükrözheti bennük.  Például a következő tartalék érvényesek:

* "faultDomain": "FD:/Room1/Rack1/Machine1"
* "faultDomain": "FD:/FD1"
* "faultDomain": "FD:/Room1/Rack1/PDU1/M1"

A *frissítési tartomány* (UD) a csomópontok logikai egysége. Service Fabric összehangolt frissítések (egy alkalmazás frissítése vagy egy fürt frissítése) során a rendszer az UD-ben lévő összes csomópontot leállítja a frissítés végrehajtásához, miközben a más frissítési lévő csomópontok továbbra is elérhetők lesznek a kérelmek kiszolgálásához. A gépeken végrehajtott belső vezérlőprogram-frissítés nem tartja tiszteletben a frissítési, ezért egyszerre csak egy gépet kell végrehajtania.

Ezeknek az elképzeléseknek a legegyszerűbb módja, ha úgy gondolja, hogy tartalék, mint a nem tervezett meghibásodások egysége és a tervezett karbantartás frissítési.

Ha a ClusterConfig.json frissítési megadását választja, megadhatja az egyes UD-nevek nevét. Például a következő nevek érvényesek:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

A tartalék és a frissítési kapcsolatos részletes információkért lásd: [Service Fabric-fürt leírása](service-fabric-cluster-resource-manager-cluster-description.md).

Az éles környezetben üzemelő fürtöknek legalább három tartalék kell kiterjedniük ahhoz, hogy éles környezetben is támogatni lehessen őket, ha teljes mértékben kézben tarthatja a csomópontok karbantartását és felügyeletét, vagyis Ön felelős a gépek frissítéséhez és lecseréléséhez. Olyan környezetekben futó fürtök esetén (azaz Amazon Web Services VM-példányok), ahol nincs teljes hozzáférése a gépekhez, legalább öt tartalék kell lennie a fürtben. Mindegyik FD rendelkezhet egy vagy több csomóponttal is. Ennek célja, hogy megakadályozza a gépek frissítései és frissítései által okozott problémákat, ami az Időzítéstől függően zavarhatja a fürtökben lévő alkalmazások és szolgáltatások futtatását.

## <a name="determine-the-initial-cluster-size"></a>A fürt kezdeti méretének meghatározása

Általában a fürtben lévő csomópontok száma az üzleti igényeknek megfelelően van meghatározva, vagyis azt, hogy hány szolgáltatás és tároló fut a fürtön, és hány erőforrásra van szükség a számítási feladatok fenntartásához. Éles fürtök esetében javasoljuk, hogy a fürtben legalább öt csomóponttal lássa el az 5 tartalék. Ha azonban a fentiekben leírtak szerint teljes mértékben szabályozhatja a csomópontjait, és három tartalék is kiterjedhet, akkor három csomópontnak is el kell végeznie a feladatot.

Az állapot-nyilvántartó számítási feladatokat futtató fürtökön három csomópontnak kell lennie, míg a csak állapot nélküli munkaterheléseket futtató tesztelési fürtökön csak egy csomópontra van szükség. Azt is meg kell jegyezni, hogy a fejlesztési célokra több csomópont is tartozhat egy adott gépen. Éles környezetben azonban a Service Fabric fizikai vagy virtuális gépenként csak egy csomópontot támogat.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>A csomópontként szolgáló gépek előkészítése

Az alábbiakban a Service Fabric-fürthöz tartozó gépekre vonatkozó ajánlott specifikációk találhatók:

* Legalább 16 GB RAM
* Legalább 40 GB szabad lemezterület
* 4 mag vagy nagyobb CPU
* Kapcsolat biztonságos hálózattal vagy hálózattal az összes géphez
* Telepített Windows Server operációs rendszer (érvényes verziók: 2012 R2, 2016, 1709 vagy 1803). Service Fabric a 6.4.654.9590 és újabb verziója is támogatja a 2019-es és a 1809-es kiszolgálót.
* [.NET-keretrendszer 4.5.1 vagy újabb](https://www.microsoft.com/download/details.aspx?id=40773), teljes telepítés
* [Windows PowerShell 3.0](/powershell/scripting/windows-powershell/install/installing-windows-powershell)
* A [RemoteRegistry szolgáltatásnak](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754820(v=ws.11)) az összes gépen futnia kell
* **Service Fabric telepítési meghajtójának NTFS fájlrendszerrel kell rendelkeznie**
* **A Windows-szolgáltatások *teljesítményadatait & riasztásokat* és a *Windows-eseménynaplót* [engedélyezni](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc755249(v=ws.11))** kell.

> [!IMPORTANT]
> A fürt telepítéséhez és konfigurálásához a fürtnek [rendszergazdai jogosultságokkal](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) kell rendelkeznie az egyes gépeken. A Service Fabric tartományvezérlőn nem telepíthető.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>A Windows Serverhez készült Service Fabric önálló csomag letöltése
[Töltse le a hivatkozást – Service Fabric önálló csomagot – a Windows Servert](https://go.microsoft.com/fwlink/?LinkId=730690) és csomagolja ki a csomagot egy olyan központi telepítési gépre, amely nem része a fürtnek, vagy a fürt részét képező gépek egyikének.

## <a name="modify-cluster-configuration"></a>Fürt konfigurációjának módosítása
Önálló fürt létrehozásához létre kell hoznia egy önálló fürtkonfiguráció-ClusterConfig.jsa fájlon, amely leírja a fürt specifikációját. A konfigurációs fájlt a lenti hivatkozásban található sablonok alapján alapozhatja. <br>
[Önálló fürt konfigurációi](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

További információ a fájl fejezeteiről: [önálló Windows-fürt konfigurációs beállításai](service-fabric-cluster-manifest.md).

Nyissa meg az egyik ClusterConfig.jsa letöltött csomag fájljai közül, és módosítsa a következő beállításokat:

| **Konfigurációs beállítás** | **Leírás** |
| --- | --- |
| **NodeTypes** |A csomópont-típusok lehetővé teszik a fürtcsomópontok különböző csoportokba való elkülönítését. A fürtnek rendelkeznie kell legalább egy NodeType. A csoport összes csomópontja a következő általános jellemzőkkel rendelkezik: <br> **Név** – ez a csomópont típusának neve. <br>**Végponti portok** – ezek különböző elnevezett végpontok (portok), amelyek ehhez a csomópont-típushoz vannak társítva. Tetszőleges portszámot használhat, ha nem ütköznek a jegyzékfájlban található minden más alkalmazással, és a gép/virtuális gépen futó más alkalmazások még nem használják azokat. <br> **Elhelyezési tulajdonságok** – a csomópont azon típusának tulajdonságait írja le, amelyet elhelyezési megkötésként használ a rendszerszolgáltatások vagy a szolgáltatások számára. Ezek a tulajdonságok olyan felhasználó által definiált kulcs/érték párok, amelyek egy adott csomópontra vonatkozóan extra metaadatokat biztosítanak. Csomópont-tulajdonságok például az, hogy a csomópont rendelkezik-e merevlemezzel vagy grafikus kártyával, a merevlemezen, a magokon és egyéb fizikai tulajdonságoknál található orsók számával. <br> **Kapacitások** – a csomóponti kapacitások határozzák meg egy adott erőforrás nevét és mennyiségét, amelyet egy adott csomópont felhasználhat a felhasználáshoz. Egy csomópont például meghatározhat egy "MemoryInMb" nevű metrikai kapacitást, és alapértelmezés szerint 2048 MB érhető el. Ezek a kapacitások futásidőben használhatók annak biztosítására, hogy bizonyos mennyiségű erőforrást igénylő szolgáltatások a szükséges mennyiségeken elérhetők legyenek az adott erőforrásokkal rendelkező csomópontokon.<br>**IsPrimary** – ha egynél több NodeType van meghatározva, győződjön meg arról, hogy csak egy az *igaz* értékre van beállítva, amely a rendszerszolgáltatások futtatásának helye. Minden más csomópont-típust *false* értékre kell beállítani |
| **Csomópontok** |Ezek a fürt részét képező csomópontok (csomópont típusa, csomópont neve, IP-cím, tartalék tartomány és a csomópont frissítési tartománya) részletei. Azokat a gépeket, amelyekhez létre szeretné hozni a fürtöt, itt fel kell vennie az IP-címüket. <br> Ha ugyanazt az IP-címet használja az összes csomóponthoz, akkor létrejön egy egyablakos fürt, amely tesztelési célokra használható. Ne használjon egydobozos fürtöket éles számítási feladatok telepítéséhez. |

Miután a fürtkonfiguráció minden beállítást beállított a környezethez, a rendszer tesztelheti a fürt környezetét (7. lépés).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>A környezet beállítása

Ha egy fürt rendszergazdája Service Fabric önálló fürtöt konfigurál, a környezetet a következő feltételekkel kell beállítani: <br>
1. A fürtöt létrehozó felhasználónak rendszergazdai szintű biztonsági jogosultságokkal kell rendelkeznie minden olyan gépen, amely csomópontként szerepel a fürt konfigurációs fájljában.
2. Az a gép, amelyről a fürtöt létrehozták, valamint a fürt csomópontjainak a következőket kell tennie:
   * Service Fabric SDK eltávolítása megtörtént
   * Service Fabric futtatókörnyezet eltávolítása
   * Engedélyezve van-e a Windows tűzfal szolgáltatás (MpsSvc)
   * Engedélyezve van-e a távoli beállításjegyzék szolgáltatás (távoli beállításjegyzék)
   * Engedélyezve van a fájlmegosztás (SMB)
   * Szükséges portok megnyitva a fürt konfigurációs portjai alapján
   * Meg kell nyitni a Windows SMB és a távoli beállításjegyzék szolgáltatáshoz szükséges portokat: 135, 137, 138, 139 és 445
   * Hálózati kapcsolattal rendelkezik egymással
3. A fürtcsomópont-gépek egyikének sem kell tartományvezérlőnek lennie.
4. Ha a telepítendő fürt egy biztonságos fürt, ellenőrizze a szükséges biztonsági előfeltételeket, és helyesen vannak konfigurálva a konfiguráción.
5. Ha a fürtben lévő gépek nem érhetők el az internetről, állítsa be a következőt a fürt konfigurációjában:
   * Telemetria letiltása: a "EnableTelemetry" *tulajdonságban* állítsa be a *következőt: false*
   * Letilthatja az automatikus háló verziójának letöltését & értesítéseket arról, hogy a fürt aktuális verziója hamarosan megszűnik a támogatás vége: a *"fabricClusterAutoupgradeEnabled"* *tulajdonság* beállítása alatt: false
   * Ha a hálózati internet-hozzáférés allowlisted tartományokra korlátozódik, az alábbi tartományokra van szükség az automatikus frissítéshez: go.microsoft.com download.microsoft.com

6. A megfelelő Service Fabric Antivirus-kizárások beállítása:

| **Víruskereső által kizárt könyvtárak** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (a fürt konfigurációjától) |
| FabricLogRoot (a fürt konfigurációjától) |

| **Víruskereső által kizárt folyamatok** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Környezet ellenőrzése a TestConfiguration parancsfájl használatával
A TestConfiguration.ps1 szkript az önálló csomagban található. A rendszer az ajánlott eljárásokat elemző eszközként használja a fenti feltételek némelyikének ellenőrzéséhez, és az alapértékként ellenőrzi, hogy egy fürt üzembe helyezhető-e egy adott környezetben. Ha hiba lép fel, a hibaelhárításhoz tekintse meg a [környezet beállítása](service-fabric-cluster-standalone-deployment-preparation.md) szakaszban található listát.

Ez a parancsfájl bármely olyan gépen futtatható, amely rendszergazdai hozzáféréssel rendelkezik az összes olyan géphez, amely csomópontként szerepel a fürt konfigurációs fájljában. A parancsfájl futtatásához használt gépnek nem kell a fürt részét képeznie.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True
```

Ez a konfigurációs tesztelési modul jelenleg nem ellenőrzi a biztonsági beállításokat, így azt egymástól függetlenül kell elvégezni.

> [!NOTE]
> Folyamatosan fejlesztjük ezt a modult, így ha van olyan hibás vagy hiányzó eset, amelyet a TestConfiguration jelenleg nem észlel, kérjük, tudassa velünk a [támogatási csatornákon](./service-fabric-support.md)keresztül.
>
>

## <a name="next-steps"></a>Következő lépések
* [Windows Serveren futó különálló fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
