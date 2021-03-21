---
title: A Visual Studio használata Azure-beli virtuális gépeken
description: A Visual Studio használata Azure-beli virtuális gépeken.
author: cathysull
manager: cathys
ms.service: virtual-machines
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: cathys
keywords: VisualStudio
ms.openlocfilehash: a6acf27743f17941e14adc3f6deea903f4f5ca87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562078"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio-lemezképek az Azure-ban
A Visual Studio egy előre konfigurált Azure-beli virtuális gépen (VM) gyorsan és egyszerűen elvégezhető, hogy ne legyenek elérhetők a felhasználható fejlesztési környezetek. A különböző Visual Studio-konfigurációkkal rendelkező rendszerképek az [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure)érhetők el.

Most ismerkedik az Azure-ral? [Hozzon létre egy ingyenes Azure-fiókot](https://azure.microsoft.com/free).

> [!NOTE]
> Nem minden előfizetés jogosult a Windows 10-es lemezképek üzembe helyezésére. További információ: [Windows-ügyfél használata az Azure-ban fejlesztési és tesztelési helyzetekben](./client-images.md)

## <a name="what-configurations-and-versions-are-available"></a>Milyen konfigurációk és verziók érhetők el?
A legújabb főverziók, a Visual Studio 2019, a Visual Studio 2017 és a Visual Studio 2015 rendszerképei az Azure piactéren találhatók.  Az egyes kiadott főverziók esetében az eredeti "megjelent a web" (RTW) és a legújabb frissített verziók láthatók.  Ezen verziók mindegyike a Visual Studio Enterprise és a Visual Studio Community kiadásait kínálja.  Ezek a lemezképek legalább minden hónapban frissülnek, hogy tartalmazzák a legújabb Visual Studio-és Windows-frissítéseket.  Míg a képek nevei változatlanok maradnak, az egyes képek leírása tartalmazza a telepített termék verzióját és a lemezkép "as of" dátumát.

| Kiadás verziója                                                                                                                                                | Kiadások              | Termékváltozat   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: legújabb (16,8-es verzió)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Vállalat, Közösség | 16.8.0 verziója    |
| Visual Studio 2019: RTW                         | Enterprise | 16.0.20 verziója    |
| Visual Studio 2017: legújabb (15,9-es verzió)           | Vállalat, Közösség | 15.9.29 verziója   |
| Visual Studio 2017: RTW                             | Vállalat, Közösség | 15.0.28 verziója  |
| Visual Studio 2015: legújabb (3. frissítés)               | Vállalat, Közösség | 14.0.25431.01 verziója |

> [!NOTE]
> A Microsoft karbantartási szabályzatának megfelelően a Visual Studio 2015 eredetileg kiadott (RTW) verziója lejárt a karbantartáshoz. A Visual Studio 2015 Update 3 az egyetlen, a Visual Studio 2015 termékcsalád számára elérhető verzió.

További információkért lásd a [Visual Studio karbantartási szabályzatát](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Milyen funkciók vannak telepítve?
Mindegyik rendszerkép tartalmazza az adott Visual Studio-kiadáshoz ajánlott szolgáltatáskészlet-készletet. A telepítés általában az alábbiakat tartalmazza:

* Az összes rendelkezésre álló számítási feladat, beleértve az egyes munkaterhelések javasolt választható összetevőit is
* .NET 4.6.2 és .NET 4,7 SDK-k, célzott csomagok és Fejlesztői eszközök
* Visual F #
* GitHub-bővítmény a Visual studióhoz
* Eszközök LINQ to SQL

A Visual Studio telepítéséhez a rendszerképek létrehozásakor használt parancssor a következő:

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

Ha a lemezképek nem tartalmazzák a szükséges Visual Studio-szolgáltatást, küldjön visszajelzést az oldal jobb felső sarkában található visszajelzési eszközön keresztül.

## <a name="what-size-vm-should-i-choose"></a>Milyen méretű virtuális gépet választok?
Az Azure a virtuálisgép-méretek teljes skáláját kínálja. Mivel a Visual Studio egy hatékony, többszálas alkalmazás, olyan virtuálisgép-méretet szeretne használni, amely legalább két processzort és 7 GB memóriát tartalmaz. A Visual Studio-lemezképek esetében a következő virtuálisgép-méreteket javasoljuk:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
A legújabb gépi méretekről további információt a [Windows rendszerű virtuális gépek méretei az Azure-ban](../sizes.md)című témakörben talál.

Az Azure-ban a virtuális gép átméretezésével lehetősége van a kezdeti választás kiegyensúlyozására. Létrehozhat egy új virtuális gépet megfelelőbb mérettel, vagy átméretezheti a meglévő virtuális gépet különböző mögöttes hardverre. További információ: [Windows rendszerű virtuális gép átméretezése](./resize-vm.md).

## <a name="after-the-vm-is-running-whats-next"></a>A virtuális gép futása után mi a következő lépés?
A Visual Studio a "saját licenc használata" modellt követi az Azure-ban. A saját hardveres telepítéshez hasonlóan az egyik első lépés a Visual Studio telepítésének licencelése. A Visual Studio zárolásának feloldásához tegye a következők egyikét:
- Bejelentkezés egy Visual Studio-előfizetéshez társított Microsoft-fiók 
- A Visual Studio zárolásának feloldása a kezdeti vásárláshoz kapott termékkulccsal

További információ: [Bejelentkezés a Visual studióba](/visualstudio/ide/signing-in-to-visual-studio) és a [Visual Studio zárolásának feloldása](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Hogyan menteni a fejlesztési virtuális gépet jövőbeni vagy csoportos használatra?

A fejlesztési környezetek spektruma óriási, és a bonyolultabb környezetek kiépítéséhez is valós díjak tartoznak. A környezet konfigurációjától függetlenül elmentheti vagy rögzítheti a konfigurált virtuális gépet "alaprendszerképként" a későbbi használatra, illetve a csapat más tagjai számára. Ezután az új virtuális gép indításakor kiépítheti azt az alaprendszerképből, nem az Azure Marketplace-rendszerképből.

Gyors Összefoglalás: használja a rendszer-előkészítő eszközt (Sysprep), és állítsa le a futó virtuális gépet, majd rögzítse *(1. ábra)* a virtuális gépet lemezképként a Azure Portal felhasználói felületén. Az Azure menti a `.vhd` képet tartalmazó fájlt a választott Storage-fiókban. Az új rendszerkép megjelenik a rendszerkép-erőforrásként az előfizetés erőforrásainak listájában.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(1. ábra) Rendszerkép rögzítése a Azure Portal felhasználói felületén keresztül.*</center>

További információ: [általánosított virtuális gép felügyelt rendszerképének létrehozása az Azure-ban](./capture-image-resource.md).

> [!IMPORTANT]
> Ne felejtse el használni a Sysprept a virtuális gép előkészítéséhez. Ha kihagyja ezt a lépést, az Azure nem tud virtuális gépet kiépíteni a rendszerképből.

> [!NOTE]
> A lemezképek tárolásának költségei továbbra is fennállnak, de a növekményes költségek nem jelentősek, mint a virtuális gép teljesen új, az egyes csapattagok számára történő újraépítéséhez szükséges költségek. Például a teljes csapat által felhasználható havi 127 GB-os rendszerképek létrehozása és tárolása több dollárt is igénybe vehet. Ezek a költségek azonban jelentéktelenek, mint az egyes alkalmazottak által a megfelelő módon konfigurált dev Box kiépítésének és érvényesítésének ideje.

Emellett előfordulhat, hogy a fejlesztési feladatokhoz vagy technológiákhoz több méretezésre van szükség, például a fejlesztési konfigurációk és a több gép konfigurációjának változatosságára. A Azure DevTest Labs használatával olyan _recepteket_ hozhat létre, amelyek automatizálják az "arany rendszerkép" kialakítását. A DevTest Labs szolgáltatással is kezelheti a csoportok futó virtuális gépek szabályzatait. A DevTest Labs szolgáltatással kapcsolatos további információkért a [Azure DevTest Labs for Developers használata](../../devtest-labs/devtest-lab-developer-lab.md) a legjobb forrás.

## <a name="next-steps"></a>Következő lépések
Most, hogy már ismeri az előre konfigurált Visual Studio-rendszerképeket, a következő lépés egy új virtuális gép létrehozása:

* [Virtuális gép létrehozása a Azure Portal](quick-create-portal.md)
* [Windows Virtual Machines áttekintése](overview.md)