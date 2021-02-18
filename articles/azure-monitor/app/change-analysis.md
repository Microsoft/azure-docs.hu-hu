---
title: Alkalmazás-módosítási elemzés használata a Azure Monitor webalkalmazásokkal kapcsolatos problémák kereséséhez | Microsoft Docs
description: Az alkalmazással kapcsolatos problémák elhárításához használja a Azure Monitor az alkalmazások változási elemzését Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 0f541df091733c081c77e41ebff4d0d0d93dca96
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573917"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Alkalmazás-módosítási elemzés (előzetes verzió) használata Azure Monitor

Ha élő hely probléma vagy leállás történik, az alapvető ok gyors meghatározása kritikus fontosságú. A standard szintű figyelési megoldások riasztást kérhetnek a problémára. Akár azt is jelezhetik, hogy melyik összetevő meghibásodik. Ez a riasztás azonban nem mindig azonnal ismerteti a hiba okát. Tudja, hogy a hely öt perccel ezelőtt dolgozott, és most megszakadt. Mi változott az elmúlt öt percben? Ez az a kérdés, hogy az alkalmazás változásának elemzése úgy van kialakítva, hogy Azure Monitor válaszoljanak.

Az [Azure Resource Graph](../../governance/resource-graph/overview.md)teljesítményének növelésével a Change Analysis betekintést nyújt az Azure-alkalmazás változásaiba, hogy növelje a megfigyelt és csökkentse a MTTR (a javítási időt).

> [!IMPORTANT]
> A Change Analysis szolgáltatás jelenleg előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatási szintű szerződés nélkül biztosítjuk. Ez a verzió éles munkaterhelések esetén nem ajánlott. Előfordulhat, hogy egyes funkciók nem támogatottak, vagy korlátozott képességekkel rendelkeznek. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Áttekintés

A Change Analysis különböző típusú módosításokat észlel, az infrastruktúra rétegből egészen az alkalmazás üzembe helyezéséhez. Ez egy előfizetési szintű Azure-erőforrás-szolgáltató, amely ellenőrzi az erőforrás-változásokat az előfizetésben. A Change Analysis számos diagnosztikai eszközt tartalmaz, amelyek segítségével a felhasználók megismerhetik, hogy milyen változások okozták a hibákat.

A következő ábra a változások elemzésének architektúráját szemlélteti:

![Architektúra-diagram, amely bemutatja, hogyan módosulnak az elemzések, és hogyan biztosítható az ügyféleszközök számára](./media/change-analysis/overview.png)

## <a name="supported-resource-types"></a>Támogatott erőforrástípusok

Az Application Change Analysis Service támogatja az erőforrás-tulajdonságok szintjének változásait az összes Azure-erőforrástípus esetében, beleértve a gyakori erőforrásokat, például a következőket:
- Virtuális gép
- Virtuálisgép-méretezési csoport
- App Service
- Azure Kubernetes szolgáltatás
- Azure-függvény
- Hálózati erőforrások: hálózati biztonsági csoport, Virtual Network, Application Gateway stb.
- Adatszolgáltatások: tárolás, SQL, Redis Cache, Cosmos DB stb.

## <a name="data-sources"></a>Adatforrások

Az alkalmazás megváltoztatja az elemzési lekérdezéseket Azure Resource Manager követett tulajdonságok, a proxyzott konfigurációk és a webalkalmazás módosításaiban. Emellett a szolgáltatás nyomon követi az erőforrás-függőségi változásokat az alkalmazások végpontok közötti diagnosztizálásához és figyeléséhez.

### <a name="azure-resource-manager-tracked-properties-changes"></a>A nyomon követett tulajdonságok változásai Azure Resource Manager

Az [Azure Resource Graph](../../governance/resource-graph/overview.md)használatával a Change Analysis egy korábbi rekordot biztosít arról, hogy az alkalmazás által üzemeltetett Azure-erőforrások mennyi idő alatt változtak. A nyomon követett beállítások, például a felügyelt identitások, a platform operációsrendszer-frissítése és az állomásnevek is észlelhetők.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager proxyn végzett beállítások változásai

A beállítások, például az IP-konfigurációs szabály, a TLS-beállítások és a bővítmény-verziók még nem érhetők el az Azure Resource Graph-ban, ezért az elemzési lekérdezések módosítása és a módosítások biztonságos kiszámításával további részleteket tudhat meg az alkalmazásban megváltoztatott adatokról.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>A webalkalmazások telepítésének és konfigurációjának változásai (a vendég módosításaiban)

A Change Analysis egy alkalmazás központi telepítési és konfigurációs állapotát 4 óránként rögzíti. Képes azonosítani például az alkalmazás környezeti változóinak változásait. Az eszköz kiszámítja a különbségeket, és bemutatja, hogy mi változott. A Resource Manager változásaitól eltérően előfordulhat, hogy a programkód-telepítési változási információk nem lesznek azonnal elérhetők az eszközön. A Change Analysis legújabb változásainak megtekintéséhez válassza a **frissítés** lehetőséget.

![Képernyőkép a "változások ellenőrzése most" gombról](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Függőségi változások

Az erőforrás-függőségek változásai az erőforrásokkal kapcsolatos problémákat is okozhatnak. Ha például egy webalkalmazás meghívja a Redis cache-t, a Redis cache SKU hatással lehet a webalkalmazás teljesítményére. Egy másik példa, ha a 22-es port bezárult egy virtuális gép hálózati biztonsági csoportjában, a csatlakozási hibákat okoz.

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>Webalkalmazás diagnosztizálása és megoldása – navigátor (előzetes verzió)

A függőségek változásainak észleléséhez a Change Analysis ellenőrzi a webalkalmazás DNS-rekordját. Így minden olyan alkalmazás-összetevő változását azonosítja, amely problémákat okozhat.
Jelenleg a következő függőségek támogatottak a **webalkalmazások diagnosztizálásában és a problémák megoldásában | Navigátor (előzetes verzió)**:

- Web Apps
- Azure Storage
- Azure SQL

#### <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

Az alkalmazás módosításainak elemzése észleli a kapcsolódó erőforrásokat. Gyakori példák a virtuális géphez kapcsolódó hálózati biztonsági csoport, Virtual Network, Application Gateway és Load Balancer.
A hálózati erőforrásokat általában ugyanabban az erőforráscsoportban kell kiépíteni, mint az erőforrásokat használó erőforrásokat, így az erőforráscsoport-módosítások szűrésével a virtuális gép és a kapcsolódó hálózati erőforrások összes változása megjelenik.

![A hálózati változások képernyőképe](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>Application Change Analysis Service-engedélyezés

Az alkalmazás megváltoztatja az Analysis Service-t, és összesíti az adatokat a fent említett adatforrásokból. Elemzési lehetőségeket biztosít a felhasználók számára, hogy könnyen navigáljon az összes erőforrás-változáson, és azonosítsa, hogy melyik módosítás vonatkozik a hibaelhárítási vagy figyelési kontextusban.
A (z) "Microsoft. ChangeAnalysis" erőforrás-szolgáltatót regisztrálni kell egy előfizetésben a Azure Resource Manager követett tulajdonságok és a proxyn lévő beállítások módosítására vonatkozó adatváltozások számára. A webalkalmazás diagnosztizálása és megoldása eszköz beírásakor vagy a Change Analysis standalone (önálló módosítás) lapon az erőforrás-szolgáltató automatikusan regisztrálva lesz.
A webalkalmazások vendégen belüli változásaihoz külön engedélyezés szükséges a programkódok webalkalmazásban való vizsgálatához. További részletekért lásd a cikk későbbi, [a problémák diagnosztizálása és megoldása eszközének módosítása](change-analysis-visualizations.md#application-change-analysis-in-the-diagnose-and-solve-problems-tool) című szakaszát.

## <a name="cost"></a>Költségek
Az Application Change Analysis egy ingyenes szolgáltatás, amely nem terheli az általa engedélyezett előfizetések számlázási költségeit. A szolgáltatás emellett nem befolyásolja az Azure-erőforrás tulajdonságainak megváltozásának ellenőrzését. Ha engedélyezi a Web Apps-ban található webalkalmazások módosítási elemzését (vagy lehetővé teszi a problémák diagnosztizálását és megoldását), akkor az a webalkalmazásra és a számlázási díjakra nézve elhanyagolható teljesítménnyel járhat.

## <a name="visualizations-for-application-change-analysis"></a>Az alkalmazás változási elemzésének vizualizációi

### <a name="standalone-ui"></a>Önálló felhasználói felület

Azure Monitor a változások elemzésére szolgáló önálló ablaktábla a változásoknak az alkalmazás függőségeibe és erőforrásaiba való betekintéssel történő megtekintésére szolgál.

A felhasználói élmény elindításához keresse meg a Change Analysis kifejezést a Azure Portal található keresési sávon.

![Képernyőkép a keresési változások elemzéséről Azure Portal](./media/change-analysis/search-change-analysis.png)

A kiválasztott előfizetéshez tartozó összes erőforrás az elmúlt 24 órában történt változásokkal jelenik meg. A lap terhelési teljesítményének optimalizálása érdekében a szolgáltatás egyszerre 10 erőforrást jelenít meg. További erőforrások megtekintéséhez kattintson a következő lapokra. Dolgozunk a korlátozás eltávolításán.

![Képernyőkép a Change Analysis panelről Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Egy erőforrásra kattintva megtekintheti az összes módosítást. Ha szükséges, tekintse meg a JSON formátumú változás részleteit és az elemzéseket tartalmazó változást.

![Képernyőkép a változás részleteiről](./media/change-analysis/change-details.png)

Bármilyen visszajelzést a panelen vagy e-mailben a visszajelzés küldése gombra kattintva használhat changeanalysisteam@microsoft.com .

![Képernyőkép – visszajelzés gomb a Change Analysis panelen](./media/change-analysis/change-analysis-feedback.png)

#### <a name="multiple-subscription-support"></a>Több előfizetés támogatása
A felhasználói felület támogatja több előfizetés kiválasztását az erőforrások változásainak megtekintéséhez. Használja az előfizetés szűrőt:

![A több előfizetés kijelölését támogató előfizetés-szűrő képernyőképe](./media/change-analysis/multiple-subscriptions-support.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Webalkalmazás diagnosztizálása és megoldása

Azure Monitor a Change Analysis az önkiszolgáló **diagnosztizálása és a problémák megoldása** terén is beépül. Ezt a felhasználói élményt a App Service alkalmazás **Áttekintés** lapjáról érheti el.

![Az "áttekintés" gomb és a "problémák diagnosztizálása és megoldása" gomb képernyőképe](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Alkalmazás-változási elemzés a problémák diagnosztizálása és megoldása eszközben

Az Application Change Analysis egy önálló detektor a webalkalmazásban a problémák diagnosztizálásához és megoldásához. Az **alkalmazás összeomlik** és a **webalkalmazás-leállási érzékelők** is összesítve vannak. A problémák diagnosztizálása és megoldása eszköz beírása után a **Microsoft. ChangeAnalysis** erőforrás-szolgáltató automatikusan regisztrálva lesz. Kövesse az alábbi utasításokat a webalkalmazás vendégen belüli módosítás-követésének engedélyezéséhez.

1. Válassza ki **a rendelkezésre állást és a teljesítményt**.

    ![Képernyőkép a "rendelkezésre állás és teljesítmény" hibaelhárítási lehetőségeiről](./media/change-analysis/availability-and-performance.png)

2. Válassza az **alkalmazás módosítása** lehetőséget. A funkció az **alkalmazások összeomlása** során is elérhető.

   ![Képernyőkép az "alkalmazás-összeomlások" gombról](./media/change-analysis/application-changes.png)

3. A hivatkozás alkalmazás-változási Aalysis felhasználói felülete a webalkalmazásra terjed ki. Ha a Web App in-Guest Change Tracking nincs engedélyezve, a fájl-és Alkalmazásbeállítások módosításának beolvasásához kövesse a szalagcímet.

   ![Képernyőkép az "alkalmazás-összeomlások" lehetőségeiről](./media/change-analysis/enable-changeanalysis.png)

4. Kapcsolja be az **elemzést** , és válassza a **Mentés** lehetőséget. Az eszköz az összes webalkalmazást megjeleníti App Service csomag alatt. A csomag szintje kapcsoló használatával bekapcsolhatja a tervben szereplő összes webalkalmazáshoz tartozó változások elemzését.

    ![Képernyőfelvétel: a Change Analysis felhasználói felületének engedélyezése](./media/change-analysis/change-analysis-on.png)

5. Az adatváltozás a **webalkalmazás kiválasztása lefelé** és az **alkalmazás-összeomlási** érzékelők között is elérhető. Ekkor megjelenik egy gráf, amely összefoglalja a változások típusát a változások részleteivel együtt. Alapértelmezés szerint az elmúlt 24 órában történt változások segítenek az azonnali problémák megoldásában.

     ![Képernyőkép a Change diff nézetről](./media/change-analysis/change-view.png)



### <a name="virtual-machine-diagnose-and-solve-problems"></a>Virtuális gépek diagnosztizálása és megoldása

Nyissa meg a problémák diagnosztizálása és megoldása eszközt a virtuális gépen.  Nyissa meg a **hibaelhárítási eszközöket**, keresse meg az oldalt, és válassza a **legutóbbi módosítások elemzése** lehetőséget a módosítások megtekintéséhez a virtuális gépen.

![Képernyőfelvétel a virtuális gép diagnosztizálásáról és megoldásáról](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Az analizátor módosítása a hibaelhárítási eszközökben](./media/change-analysis/analyze-recent-changes.png)

### <a name="activity-log-change-history"></a>Tevékenység naplójának változási előzményei
A tevékenység naplójában megjelenő változási [előzmények megtekintése](../essentials/activity-log.md#view-change-history) funkció az Analysis Service-háttér változásával lekéri a művelethez kapcsolódó módosításokat. Az [Azure Resource Graph](../../governance/resource-graph/overview.md) közvetlen meghívásához használt **korábbi** változások, de a háttér meghívása az alkalmazás változási elemzésének meghívására, így a visszaadott változások tartalmazzák az [Azure Resource Graph](../../governance/resource-graph/overview.md)-ból származó erőforrás-szintű változásokat, a [Azure Resource Manager](../../azure-resource-manager/management/overview.md)erőforrás-tulajdonságait, valamint a további, a Pásti-szolgáltatásokból, például app Services webalkalmazásból származó Ahhoz, hogy az alkalmazás megváltoztatja az Analysis Service-t, hogy ellenőrizni tudja a felhasználói előfizetések módosításait, regisztrálni kell egy erőforrás-szolgáltatót. Amikor először adta meg az **Előzmények** fület, az eszköz automatikusan elkezdi regisztrálni a **Microsoft. ChangeAnalysis** erőforrás-szolgáltatót. A regisztrációt követően az **Azure Resource Graph** változásai azonnal elérhetővé válnak, és az elmúlt 14 napra kiterjednek. A más forrásokból származó változások az előfizetés után 4 órával azután lesznek elérhetők.

![A műveletnapló változási előzményeinek integrációja](./media/change-analysis/activity-log-change-history.png)

### <a name="vm-insights-integration"></a>A VM-alapú adatáttekintések integrációja
Azok a felhasználók, akiknél [engedélyezve van a virtuális gépek](../vm/vminsights-overview.md) megállapítása, megtekinthetik, hogy milyen változások történtek az egyes mérőszámokban, például a CPU-ban vagy a memóriában, és hogy mi okozta. Az adatváltozások integrálva vannak a virtuálisgép-elemzések oldalsó navigációs sávjában. A felhasználó megtekintheti, hogy történtek-e változások a virtuális gépen, majd kattintson a **módosítások vizsgálata** gombra a módosítás részleteinek megtekintéséhez az Application Change Analysis önálló felhasználói felületén.

[![A VM-alapú adatáttekintések integrációja](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)


## <a name="enable-change-analysis-at-scale"></a>Módosítási elemzés engedélyezése a skálán

Ha az előfizetése számos webalkalmazást tartalmaz, a webalkalmazás szintjén a szolgáltatás engedélyezése nem hatékony. Futtassa az alábbi parancsfájlt az előfizetésben lévő összes webalkalmazás engedélyezéséhez.

Előfeltételek:

- PowerShell az Module. Kövesse [az Azure PowerShell modul telepítésének](/powershell/azure/install-az-ps) utasításait

Futtassa a következő parancsfájlt:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"

# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [vizualizációk változási elemzéséről](change-analysis-visualizations.md)
- Tudnivalók a [változások elemzésével kapcsolatos problémák elhárításáról](change-analysis-troubleshoot.md)
- Az [Azure app Services-alkalmazások](azure-web-apps.md)Application Insights engedélyezése.
- Engedélyezze Application Insights az [Azure-beli virtuális gépek és az Azure-beli virtuálisgép-méretezési csoport IIS által üzemeltetett alkalmazásai](azure-vm-vmss-apps.md)számára.
