---
title: Azure DevTest Labs kiterjesztése a Azure Functions használatával | Microsoft Docs
description: Ismerje meg, hogyan bővíthető Azure DevTest Labs a Azure Functions használatával.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014359"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>A DevTest Labs bővítésének Azure Functions használata
A DevTest Labs által már támogatott további forgatókönyvek támogatásához Azure Functions is használhatja. A Azure Functions használatával kiterjesztheti a szolgáltatás beépített funkcióit az üzleti igények kielégítése érdekében. Az alábbi lista néhány lehetséges forgatókönyvet tartalmaz. Ez a cikk bemutatja, hogyan hajthatja végre az alábbi példák egyikét.

- A laborban található virtuális gépek (VM-EK) legfelső szintű összefoglalásának megadása
- [Tesztkörnyezet konfigurálása távoli asztali átjáró használatára](configure-lab-remote-desktop-gateway.md)
- Megfelelőségi jelentéskészítés a belső támogatási oldalon
- Annak engedélyezése, hogy a felhasználók elvégezzék a megnövelt engedélyeket igénylő műveleteket az előfizetésben
- [Munkafolyamatok indítása DevTest Labs-események alapján](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Áttekintés
[Azure functions](../azure-functions/functions-overview.md) egy kiszolgáló nélküli számítástechnikai platform az Azure-ban. A DevTest Labs szolgáltatással való Azure Functions használata lehetővé teszi, hogy a meglévő funkciókat a saját egyéni kódjával egészítse ki. További információ a Azure Functionsről: [Azure functions dokumentáció](../azure-functions/functions-overview.md). Ennek a cikknek a segítségével bemutathatja, hogyan segíthetnek a Azure Functions a követelmények teljesítésében vagy a DevTest Labs-beli forgatókönyvekben, ez a cikk egy példát mutat be a virtuális gépek legfelső szintű összegzésének biztosítására a laborban az alábbiak szerint:

**Példa a követelményre/forgatókönyvre**: A felhasználók a laborban lévő összes virtuális gép adatait megtekinthetik, beleértve az operációs rendszert, a tulajdonost és az összes alkalmazott összetevőt.  Emellett, ha a **Windows Updates** összetevőt nem nemrég alkalmazták, egyszerűen alkalmazható.

A forgatókönyv végrehajtásához két függvényt fog használni az alábbi ábrán leírtak szerint:  

![Teljes folyamat](./media/extend-devtest-labs-azure-functions/flow.png)

A minta függvények forráskódja a [DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) -adattárban található (a C# és a PowerShell implementáció is elérhető).

- **UpdateInternalSupportPage**: Ez a függvény lekérdezi a DevTest Labs szolgáltatást, és közvetlenül a virtuális gépek részleteivel frissíti a belső támogatási lapot.
- **ApplyWindowsUpdateArtifact**: A laborban található virtuális gépek esetében ez a függvény a **Windows Update** összetevőt alkalmazza.

## <a name="how-it-works"></a>Működés
Ha a felhasználók a DevTest Labs **belső támogatási** lapját választja, a virtuális gépekkel, a labor-tulajdonosokkal és a támogatási partnerekkel kapcsolatos információkat tartalmazó előre feltöltött oldalról van szó.  

Amikor bejelöli a **kattintson ide** a frissítéshez gombra, az oldal meghívja az első Azure-függvényt: **UpdateInternalSupportPage**. A függvény lekérdezi az DevTest Labs szolgáltatás adatait, majd újraírja a **belső támogatási** lapot az új információkkal.

Van egy további művelet is, amely minden olyan virtuális gép esetében, amelyen a Windows Update-összetevők nem lettek alkalmazva a közelmúltban, a Windows-frissítések a virtuális gépre való alkalmazására szolgáló gomb jelenik meg. Ha a virtuális géphez a**Windows Update futtatása** gombot választja, akkor az oldal meghívja a második Azure-függvényt: **ApplyWindowsUpdateArtifact**. Ez a függvény ellenőrzi, hogy a virtuális gép fut-e, és ha igen, alkalmazza-e közvetlenül a [Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) -összetevőt.

## <a name="step-by-step-walkthrough"></a>Részletes útmutató
Ez a szakasz részletesen ismerteti a **belső támogatási** oldal frissítéséhez szükséges Azure-erőforrások beállításának lépéseit. Ez az útmutató egy példát mutat be a DevTest Labs kiterjesztésére. Ezt a mintát használhatja más forgatókönyvek esetében is.

### <a name="step-1-create-a-service-principal"></a>1\. lépés: Egyszerű szolgáltatás létrehozása 
Az első lépés egy egyszerű szolgáltatásnév beszerzése, amely engedéllyel rendelkezik a labort tartalmazó előfizetéshez. Az egyszerű szolgáltatásnak a jelszó-alapú hitelesítést kell használnia. Az [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)vagy a [Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)használatával végezhető el. Ha már rendelkezik egy egyszerű szolgáltatással, akkor kihagyhatja ezt a lépést.

Jegyezze fel az **alkalmazás azonosítóját**, kulcsát és **bérlői azonosítóját** az egyszerű szolgáltatáshoz. Az útmutató későbbi részében szüksége lesz rájuk. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>2\. lépés: Töltse le a mintát, és nyissa meg a Visual Studio 2019
Töltse le helyileg a [ C# Azure functions minta](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) másolatát (akár a tárház klónozásával, akár a tárház letöltésével [](https://github.com/Azure/azure-devtestlab/archive/master.zip)).  

1. Nyissa meg a minta megoldást a Visual Studio 2019-mel.  
1. Ha még nincs telepítve, telepítse az **Azure-fejlesztési** számítási feladatot a Visual studióba. Az eszközök beolvasása -> eszközök**és szolgáltatások** menüpont használatával telepíthető.

    ![Azure-fejlesztési munkaterhelés](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Hozza létre a megoldást. Válassza a **Létrehozás** , majd a **megoldás létrehozása** menüpontot.

### <a name="step-3-deploy-the-sample-to-azure"></a>3\. lépés: A minta üzembe helyezése az Azure-ban
A Visual Studióban, a **megoldáskezelő** ablakban kattintson a jobb gombbal a **AzureFunctions** projektre, majd válassza a **Közzététel**lehetőséget. Kövesse a varázslót egy új vagy egy meglévő Azure-függvényalkalmazás közzétételének befejezéséhez. Az Azure functions Visual Studióval történő fejlesztésével és üzembe helyezésével kapcsolatos részletes információkért lásd: [Azure functions fejlesztése a Visual Studio használatával](../azure-functions/functions-develop-vs.md).

![Közzététel párbeszédpanel](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>4\. lépés:  Alkalmazásbeállítások összegyűjtése
A függvények közzétételekor le kell kérnie a függvények URL-címeit a Azure Portalból. 

1. Lépjen az [Azure Portalra](https://portal.azure.com). 
1. Keresse meg a Function alkalmazást.
1. A **Function apps** lapon válassza ki a függvényt. 
1. Válassza a **függvény URL-címének** beolvasása lehetőséget az alábbi ábrán látható módon. 

    ![Azure functions URL-címek](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Másolja és mentse az URL-címet. Ismételje meg ezeket a lépéseket a másik Azure-függvényhez. 

Emellett további információra van szüksége az egyszerű szolgáltatásról, például az alkalmazás-AZONOSÍTÓról, a kulcsról és a bérlői AZONOSÍTÓról.


### <a name="step-5--update-application-settings"></a>5\. lépés:  Alkalmazás beállításainak frissítése
Az Azure-függvény közzétételét követően a Visual Studióban válassza a **Azure app Service beállítások szerkesztése** lehetőséget a **műveletek**területen. A következő Alkalmazásbeállítások frissítése (távoli):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (alapértelmezett érték 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Alkalmazásbeállítások](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>6\. lépés: Az Azure-függvény tesztelése
Az útmutató utolsó lépése az Azure-függvény tesztelése.  

1. Navigáljon a **UpdateInternalSupportPage** függvényhez a 3. lépésben létrehozott Function alkalmazásban. 
1. Válassza a **teszt** elemet az oldal jobb oldalán. 
1. Adja meg az útvonal tulajdonságait (LABNAME, RESOURCEGROUPNAME és SUBSCRIPTIONID).
1. A függvény végrehajtásához válassza a **Futtatás** lehetőséget.  

    Ez a függvény frissíti a megadott labor belső támogatási lapját. Emellett azt is megteheti, hogy a felhasználók közvetlenül a következő alkalommal hívhatják meg a függvényt

    ![Teszt függvény](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>További lépések
A Azure Functions segítségével kiterjesztheti a DevTest Labs funkcióit a már beépített funkciókon túl, és segítheti az ügyfeleket abban, hogy megfeleljenek a csapatuk egyedi követelményeinek. Ezt a mintát kiterjesztheti & tovább bővítheti, hogy még nagyobb legyen.  A DevTest Labs szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket: 

- [DevTest Labs nagyvállalati hivatkozási architektúrája](devtest-lab-reference-architecture.md)
- [Gyakori kérdések](devtest-lab-faq.md)
- [A DevTest Labs vertikális felskálázása](devtest-lab-guidance-scale.md)
- [DevTest Labs automatizálása a PowerShell-lel](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








