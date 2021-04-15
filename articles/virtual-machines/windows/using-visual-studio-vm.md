---
title: Az Visual Studio használata Azure-beli virtuális gépen
description: A Visual Studio Azure-beli virtuális gépen.
author: andysterland
manager: andster
ms.service: virtual-machines
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: andster
keywords: visualstudio
ms.openlocfilehash: f26bec64012e0b0909b7df5422c57ff2cb1c347e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478556"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio Azure-ban
A Visual Studio előre konfigurált Azure-beli virtuális gépeken (VM) gyorsan és egyszerűen át lehet térni a semmiről egy futó fejlesztési környezetre. A különböző konfigurációval Visual Studio rendszerképek a következő [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure)

Most ismerkedik az Azure-ral? [Hozzon létre egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free)

> [!NOTE]
> Nem minden előfizetés jogosult a rendszerképek Windows 10 üzembe helyezésére. További információ: [Use Windows client in Azure for dev/test scenarios (Windows-ügyfél használata az Azure-ban fejlesztési/tesztelési forgatókönyvekhez)](./client-images.md)

## <a name="what-configurations-and-versions-are-available"></a>Milyen konfigurációk és verziók érhetők el?
A legújabb főverziók (Visual Studio 2019, Visual Studio 2017 és Visual Studio 2015) rendszerképei a következő Azure Marketplace.  Minden kiadott főverziónál az eredetileg a "webes kiadás" (RTW) verzió és a legújabb frissített verziók látszanának.  Ezen verziók mindegyikében elérhető a Visual Studio Enterprise és a Visual Studio Community kiadása.  Ezek a lemezképek legalább havonta frissülnek, hogy tartalmazzák a legújabb Visual Studio windowsos frissítéseket.  Bár a lemezképek nevei változatlanok maradnak, az egyes lemezképek leírása tartalmazza a telepített termékverziót és a rendszerkép "aktuális" dátumát.

| Kiadási verzió                                                                                                                                                | Kiadások              | Termékváltozat   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: Legújabb (16.8-as verzió)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Enterprise, Community | 16.8.0-s verzió    |
| Visual Studio 2019: RTW                         | Enterprise | 16.0.20-as verzió    |
| Visual Studio 2017: Legújabb (15.9-es verzió)           | Enterprise, Community | 15.9.29-es verzió   |
| Visual Studio 2017: RTW                             | Enterprise, Community | 15.0.28-as verzió  |
| Visual Studio 2015: Legújabb (3. frissítés)               | Enterprise, Community | 14.0.25431.01-es verzió |

> [!NOTE]
> A Microsoft karbantartási szabályzatának megfelelően a 2015-ös Visual Studio eredetileg kiadott (RTW) verziója lejárt karbantartásra. Visual Studio 2015 3. frissítés az egyetlen elérhető verzió a 2015-ös Visual Studio termékváltozathoz.

További információkért lásd a Visual Studio [szabályzatot.](https://www.visualstudio.com/productinfo/vs-servicing-vs)

## <a name="what-features-are-installed"></a>Milyen szolgáltatások vannak telepítve?
Minden rendszerkép tartalmazza az egyes kiadásokhoz ajánlott Visual Studio készletet. A telepítés általában a következőket tartalmazza:

* Minden elérhető számítási feladat, beleértve az egyes számítási feladatok javasolt választható összetevőit is. A számítási feladatokkal, összetevőkkel és az Visual Studio kapcsolatos további részleteket a [dokumentációban Visual Studio találhat.](https://docs.microsoft.com/visualstudio/install/workload-and-component-ids)
* .NET 4.6.2 és .NET 4.7 SDK-k, célcsoport-csomag és Fejlesztői eszközök
* Visual F #
* GitHub-bővítmény Visual Studio
* LINQ to SQL Tools

A lemezképek kiépítésekor Visual Studio parancssor a következő:

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

Ha a képeken nincs szükség Visual Studio funkcióra, akkor az oldal jobb felső sarkában található visszajelzési eszközzel küldhet visszajelzést.

## <a name="what-size-vm-should-i-choose"></a>Milyen méretű virtuális gépet válasszak?
Az Azure a virtuális gépek méretének teljes skáláját kínálja. Mivel Visual Studio egy hatékony, többszálas alkalmazás, olyan virtuálisgép-méretet szeretne, amely legalább két processzort és 7 GB memóriát tartalmaz. Az alábbi virtuálisgép-méreteket javasoljuk a Visual Studio rendszerképhez:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
A legújabb gépméretekkel kapcsolatos további információkért lásd: Sizes for Windows virtual machines in Azure (Windows rendszerű virtuális gépek méretei [az Azure-ban).](../sizes.md)

Az Azure-ral újra egyensúlyba hozhatja a kezdeti választást a virtuális gép átméretezése által. Kiépíthet egy új virtuális gépet egy megfelelőbb mérettel, vagy átméretezheti a meglévő virtuális gépet egy másik mögöttes hardverre. További információ: [Windows rendszerű virtuális gép átméretezése.](./resize-vm.md)

## <a name="after-the-vm-is-running-whats-next"></a>A virtuális gép futtatása után mi a következő lépés?
Visual Studio a "saját licenc használatának" modelljét követi az Azure-ban. Akár csak a saját hardverre való telepítés esetén, az egyik első lépés a saját Visual Studio licencelése. A zárolás Visual Studio:
- Jelentkezzen be egy Microsoft-fiók előfizetéshez társított Visual Studio fiókkal 
- Oldja Visual Studio a kezdeti vásárláskor lekért termékkulcsot

További információ: [Bejelentkezés](/visualstudio/ide/signing-in-to-visual-studio) a Visual Studio és a zárolás [Visual Studio.](/visualstudio/ide/how-to-unlock-visual-studio)

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Hogyan a fejlesztői virtuális gépet későbbi vagy csapatbeli használatra?

A fejlesztési környezetek spektruma hatalmas, és az összetettebb környezetek kiépítése valós költségeket jelent. A környezet konfigurációjától függetlenül a konfigurált virtuális gépet mentheti vagy rögzítheti "alapként" későbbi használatra vagy a csapat más tagjai számára. Ezután egy új virtuális gép rendszerindításakor azt az alap rendszerképből kell kiépítenünk, nem pedig a Azure Marketplace rendszerképből.

Rövid összefoglalás: Használja a rendszer-előkészítési eszközt (Sysprep), állítsa le a futó virtuális gépet, majd rögzítse *(1. ábra)* a virtuális gépet rendszerképként a virtuális gép felhasználói Azure Portal. Az Azure menti a lemezképet tartalmazó fájlt a választott `.vhd` tárfiókba. Az új rendszerkép ezután rendszerkép-erőforrásként jelenik meg az előfizetés erőforrásainak listájában.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(1. ábra) Kép rögzítése a Azure Portal felületén.*</center>

További információ: Felügyelt rendszerkép létrehozása általánosított virtuális gépről az [Azure-ban.](./capture-image-resource.md)

> [!IMPORTANT]
> Ne felejtse el a Sysprepet használni a virtuális gép előkészítéséhez. Ha kihagyja ezt a lépést, az Azure nem tud virtuális gépet kiépítni a rendszerképből.

> [!NOTE]
> A rendszerképek tárolása továbbra is jár némi költséggel, de ez a növekményes költség összehasonlítható a virtuális gép nulláról való újraépítésével kapcsolatos többletköltségekkel minden olyan csapattagnál, akinek szüksége van rá. Például néhány dollárba kerül egy 127 GB-os rendszerkép létrehozása és tárolása egy hónapig, amelyet a teljes csapat újra fel használ. Ezek a költségek azonban az egyes alkalmazottak által az egyéni használatra megfelelően konfigurált fejlesztői doboz építésére és érvényesítésére fektetett órákhoz képest vannak összehasonlítva.

Emellett előfordulhat, hogy a fejlesztési feladatokhoz vagy technológiákhoz nagyobb léptékre van szükség, például különböző fejlesztési konfigurációkra és több gépkonfigurációra. A segítségével Azure DevTest Labs _"arany_ kép" automatizálható receptjeit. A DevTest Labs használatával a csapat virtuális gépeinek szabályzatai is kezelhetők. [A Azure DevTest Labs a](../../devtest-labs/devtest-lab-developer-lab.md) legjobb forrás a DevTest Labs használatával kapcsolatos további információkhoz.

## <a name="next-steps"></a>Következő lépések
Most, hogy már ismeri az előre konfigurált Visual Studio rendszerképeket, a következő lépés egy új virtuális gép létrehozása:

* [Virtuális gép létrehozása a Azure Portal](quick-create-portal.md)
* [Windows Virtual Machines áttekintés](overview.md)
