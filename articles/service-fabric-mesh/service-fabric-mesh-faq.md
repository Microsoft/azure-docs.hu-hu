---
title: Azure Service Fabric Mesh – gyakori kérdések
description: Ismerkedjen meg az Azure Service Fabric Mesh szolgáltatással kapcsolatos gyakori kérdésekkel és válaszokkal.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 8e53ab0ae4cc463bea8a6a8cb6d339f94fdcac6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626036"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Gyakori kérdések Service Fabric Mesh kérdésekről

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül. Ez a cikk a gyakran ismételt kérdésekre adott válaszokat tartalmazza.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Hogyan egy problémát, vagy felteheti a kérdést?

Tegye fel kérdéseit, kapjon választ a Microsoft mérnököktől, és jelentse a problémát a [Service-Fabric-Mesh – Preview GitHub-](https://aka.ms/sfmeshissues)tárházban.

## <a name="quota-and-cost"></a>Kvóta és Cost

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Mennyibe kerül az előzetes verzióban való részvétel?

Jelenleg nem számítunk fel díjat az alkalmazások és a tárolók üzembe helyezéséhez a háló előzetes verziójában. Előfordulhat, hogy a frissítések számlázásának engedélyezése a következő címen lehetséges:. Javasoljuk azonban, hogy törölje a telepített erőforrásokat, és ne hagyja őket futni, ha aktívan teszteli őket.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Korlátozva van a magok és a RAM mennyisége?

Igen. Az egyes előfizetések kvótái a következők:

- Alkalmazások száma: 5
- Magok száma alkalmazásban: 12
- RAM-memória teljes száma: 48 GB
- A hálózat és a bejövő forgalom végpontja: 5
- Az Azure által csatolható kötetek: 10
- A szolgáltatás replikáinak száma: 3
- Az üzembe helyezhető legnagyobb tároló a 4 maggal és a 16GB RAM-mal van korlátozva.
- A tárolók számára kioszthat részleges magokat 0,5 mag-onként, legfeljebb 6 magot vehet fel.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Mennyi ideig hagyható az alkalmazás üzembe helyezése?

Az alkalmazások élettartama jelenleg két napra korlátozódik. Ez az előzetes verzióhoz lefoglalt ingyenes magok használatának maximalizálása érdekében. Ennek eredményeképpen csak a 48 órán át folyamatosan futtathat egy adott üzembe helyezést, amely után a rendszer leállítja a leállítási időt.

Ha ez megtörténik, ellenőrizheti, hogy a rendszer le van-e állítva az `az mesh app show` Azure CLI-ben a parancs futtatásával. Ellenőrizze, hogy visszaadja-e a függvényt `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Például: 

```azurecli
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

```output
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

Az erőforráscsoport törléséhez használja az `az group delete <nameOfResourceGroup>` parancsot.

## <a name="deployments"></a>Központi telepítés

### <a name="what-container-images-are-supported"></a>Milyen tároló-lemezképek támogatottak?

Ha a Windows Fall Creators Update (1709-es verzió) gépen fejleszt, csak a Windows Version 1709 Docker-rendszerképeket használhatja.

Ha a Windows 10 április 2018 Update (1803-es verzió) gépen dolgozik, a Windows-verzió 1709-es vagy a Windows-verzió 1803 Docker-rendszerképeket is használhatja.

A következő Container operációsrendszer-lemezképeket használhatja a szolgáltatások telepítéséhez:
- Windows – windowsservercore és nanoserver
    - Windows Server 1709
    - Windows Server 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Nincsenek ismert korlátozások

> [!NOTE]
> A Visual Studio-eszközök a rácsvonalak számára még nem támogatják a Windows Server 2019-és 1809-tárolók üzembe helyezését.

### <a name="what-types-of-applications-can-i-deploy"></a>Milyen típusú alkalmazásokat telepíthetek? 

Minden olyan tárolón futtatható, amely az alkalmazás-erőforrásra vonatkozó korlátozásoknak megfelelően működik (lásd a fenti további információkat a kvótákkal kapcsolatban). Ha azt tapasztalja, hogy hálót használ a szabálytalan munkaterhelések futtatásához vagy a rendszer (azaz a bányászat) megsértéséhez, akkor fenntartjuk a jogot, hogy leállítsák az üzemelő példányokat, és Blocklist az előfizetést a szolgáltatáson. Ha bármilyen kérdése van egy adott számítási feladat futtatásához, lépjen kapcsolatba velünk. 

## <a name="developer-experience-issues"></a>Fejlesztői élmény – problémák

### <a name="dns-resolution-from-a-container-doesnt-work"></a>Egy tároló DNS-feloldása nem működik

A tárolóból a Service Fabric DNS szolgáltatásba küldött kimenő DNS-lekérdezések bizonyos körülmények között sikertelenek lehetnek. A vizsgálat folyamatban van. Az alábbiak enyhítése:

- Használja a Windows Fall Creators Update (1709-es verzió) vagy újabb verzióját az alaptároló rendszerképének használatával.
- Ha a szolgáltatás neve nem működik, próbálja meg a teljes nevet: szolgáltatásnév. ApplicationName.
- A szolgáltatás Docker-fájljában adja meg, hogy a `EXPOSE <port>` port melyik porton teszi elérhetővé a szolgáltatást. Például:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>A DNS nem működik ugyanúgy, mint Service Fabric fejlesztési fürtöknél és a hálóban

Előfordulhat, hogy a helyi fejlesztési fürtön eltérő szolgáltatásokat kell használnia, mint az Azure Meshban.

A helyi fejlesztési fürtben használja a t `{serviceName}.{applicationName}` . Az Azure Service Fabric Mesh szolgáltatásban használja a t `{servicename}` . 

Az Azure Mesh jelenleg nem támogatja a DNS-feloldást az alkalmazások között.

A Service Fabric fejlesztői fürt Windows 10 rendszeren való futtatásával kapcsolatos egyéb ismert DNS-problémákkal kapcsolatban lásd: [Windows-tárolók](../service-fabric/service-fabric-how-to-debug-windows-containers.md) és [ismert DNS-problémák](../service-fabric/service-fabric-dnsservice.md#known-issues)hibakeresése.

### <a name="networking"></a>Hálózatkezelés

Előfordulhat, hogy a ServiceFabric hálózati NAT eltűnnek az alkalmazás helyi gépen való futtatásakor. Annak diagnosztizálásához, hogy ez történt-e, futtassa a következő parancsot egy parancssorból:

`docker network ls` Figyelje meg, hogy szerepel-e `servicefabric_nat` a lista.  Ha nem, futtassa a következő parancsot: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Ez akkor is problémát jelent, ha az alkalmazás már helyileg és nem kifogástalan állapotban van telepítve.

### <a name="issues-running-multiple-apps"></a>Több alkalmazást futtató problémák

Előfordulhat, hogy a CPU rendelkezésre állása és a korlátozások az összes alkalmazásban rögzítettek. Az alábbiak enyhítése:
- Hozzon létre egy öt csomópontos fürtöt.
- A szolgáltatások CPU-használatának csökkentése a telepített alkalmazások között. A szolgáltatás Service. YAML fájljában `cpu: 1.0` például a következőre váltson: `cpu: 0.5`

Több alkalmazás nem telepíthető egyetlen csomópontos fürtre. Az alábbiak enyhítése:
- Használjon öt csomópontos fürtöt, ha több alkalmazást helyez üzembe egy helyi fürtön.
- Távolítsa el azokat az alkalmazásokat, amelyeket jelenleg nem tesztel.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>A VS-eszközök korlátozott támogatást biztosítanak a Windows-tárolók számára

A Visual Studio-eszközök csak a Windows Server 1709 és a 1803 operációs rendszert futtató Windows-tárolók telepítését támogatják. 

## <a name="feature-gaps-and-other-known-issues"></a>Szolgáltatások hiányosságainak és más ismert problémák

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Az alkalmazás telepítése után a hozzá társított hálózati erőforrás nem rendelkezik IP-címmel

Létezik egy ismert probléma, amelyben az IP-cím nem válik azonnal elérhetővé. Ellenőrizze a hálózati erőforrás állapotát néhány perc alatt, hogy megtekintse a társított IP-címet.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Az alkalmazásom nem fér hozzá a megfelelő hálózati/mennyiségi erőforráshoz

Az alkalmazás modelljében használja a hálózatok és kötetek teljes erőforrás-AZONOSÍTÓját a társított erőforrás eléréséhez. Íme egy példa a rövid útmutató mintából:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Ha felskálázást végezek, az összes tároló érintett, beleértve a futókat is

Ez egy hiba, és a javítás megvalósítása folyamatban van.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Service Fabric Meshról, olvassa el az [áttekintést](service-fabric-mesh-overview.md).
