---
title: Runbook végrehajtása az Azure Automationben
description: Ez a cikk áttekintést nyújt a runbookok feldolgozásáról a Azure Automationban.
services: automation
ms.subservice: process-automation
ms.date: 03/23/2021
ms.topic: conceptual
ms.openlocfilehash: 165c9ea721bec7fc7a1657f5dde5c19d9e254e20
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954343"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook végrehajtása az Azure Automationben

A Azure Automation folyamatok automatizálása lehetővé teszi a PowerShell, a PowerShell-munkafolyamatok és a grafikus runbookok létrehozását és kezelését. Részletekért lásd: [Azure Automation runbookok](automation-runbook-types.md). 

Az Automation a bennük definiált logikán alapuló runbookok hajtja végre. Ha egy runbook megszakad, a rendszer az elején újraindítja. Ennek a viselkedésnek a működéséhez olyan runbookok kell írnia, amelyeken a támogatás átmeneti problémák esetén újraindul.

Egy runbook elindítása Azure Automation létrehoz egy feladatot, amely a runbook egyetlen végrehajtási példánya. Minden egyes feladatokhoz hozzáfér az Azure-erőforrásokhoz az Azure-előfizetéshez való kapcsolódással. A feladatok csak akkor férhetnek hozzá az adatközpont erőforrásaihoz, ha ezek az erőforrások elérhetők a nyilvános felhőből.

Azure Automation hozzárendel egy feldolgozót az egyes feladatok futtatásához a runbook végrehajtása során. Míg a feldolgozókat számos Azure-fiók megosztja, a különböző Automation-fiókoktól származó feladatok egymástól el vannak különítve. Nem szabályozhatja, hogy mely munkavégző szolgáltatások számára kéri a feladatokat.

Amikor megtekinti a Azure Portal runbookok listáját, az egyes runbook elindított feladatok állapotát jeleníti meg. A Azure Automation a feladatok naplóit legfeljebb 30 napig tárolja.

Az alábbi ábrán a [PowerShell-runbookok](automation-runbook-types.md#powershell-runbooks), a [PowerShell-munkafolyamatok runbookok](automation-runbook-types.md#powershell-workflow-runbooks)és a [grafikus runbookok](automation-runbook-types.md#graphical-runbooks)tartozó runbook-feladatok életciklusa látható.

![Feladatok állapota – PowerShell-munkafolyamat](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Runbook végrehajtási környezet

Azure Automation runbookok Azure-beli vagy [hibrid Runbook-feldolgozón](automation-hybrid-runbook-worker.md)is futtathatók. 

Ha a runbookok az Azure-ban lévő erőforrásokkal történő hitelesítésre és futtatásra tervezték, akkor egy Azure-beli homokozóban futnak, amely egy megosztott környezet, amelyet több feladat használhat. Az azonos Sandboxot használó feladatokat a rendszer a sandbox erőforrás-korlátaihoz köti. Az Azure-beli homokozó környezet nem támogatja az interaktív műveleteket. Ez megakadályozza az összes folyamaton kívüli COM-kiszolgáló elérését, és nem támogatja a [WMI-hívásokat](/windows/win32/wmisdk/wmi-architecture) a Runbook lévő Win32-szolgáltatóhoz.  Ezek a forgatókönyvek csak a runbook Windows hibrid Runbook-feldolgozón való futtatásával támogatottak.


A [hibrid Runbook-feldolgozók](automation-hybrid-runbook-worker.md) a runbookok futtatására is használhatók közvetlenül azon a számítógépen, amely a szerepkört üzemelteti, valamint a helyi erőforrásokat a környezetben. Azure Automation tárolja és kezeli a runbookok, majd egy vagy több hozzárendelt számítógéphez továbbítja azokat.

>[!NOTE]
>Linux hibrid Runbook-feldolgozón való futtatáshoz a parancsfájlokat alá kell írni, és a feldolgozónak megfelelően kell konfigurálni. Másik lehetőségként [ki kell kapcsolni az aláírás-ellenőrzést](automation-linux-hrw-install.md#turn-off-signature-validation).

Az alábbi táblázat néhány runbook végrehajtási feladatot sorol fel, amelyek az ajánlott végrehajtási környezettel rendelkeznek.

|Feladat|Ajánlás|Jegyzetek|
|---|---|---|
|Integráció az Azure-erőforrásokkal|Azure-beli homokozó|Az Azure-ban üzemeltetett hitelesítés egyszerűbb. Ha hibrid Runbook-feldolgozót használ egy Azure-beli virtuális gépen, akkor a [Runbook-hitelesítést felügyelt identitásokkal használhatja](automation-hrw-run-runbooks.md#runbook-auth-managed-identities).|
|Optimális teljesítmény az Azure-erőforrások kezeléséhez|Azure-beli homokozó|A parancsfájl ugyanabban a környezetben fut, amelynek kevesebb a késése.|
|Működési költségek csökkentése|Azure-beli homokozó|Nincs számítási terhelés, és nincs szükség virtuális gépre.|
|Hosszan futó parancsfájl végrehajtása|hibrid runbook-feldolgozó|Az Azure-beli munkaterületeken [erőforrás-korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)vannak.|
|Helyi szolgáltatásokkal való kommunikáció|hibrid runbook-feldolgozó|Közvetlenül hozzáférhet a gazdagéphez vagy más felhőalapú környezetekben található erőforrásokhoz vagy a helyszíni környezetekhez. |
|Harmadik féltől származó szoftverek és végrehajtható fájlok megkövetelése|hibrid runbook-feldolgozó|Kezelheti az operációs rendszert, és telepíthet szoftvereket.|
|Fájl vagy mappa figyelése runbook|hibrid runbook-feldolgozó|[Figyelő feladat](automation-watchers-tutorial.md) használata hibrid Runbook-feldolgozón.|
|Erőforrás-igényes parancsfájl futtatása|hibrid runbook-feldolgozó| Az Azure-beli munkaterületeken [erőforrás-korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)vannak.|
|Speciális követelményekkel rendelkező modulok használata| hibrid runbook-feldolgozó|Néhány példa:</br> Megnyerő-függőség winscp.exe </br> IIS-felügyelet – az IIS engedélyezésével vagy kezelésével kapcsolatos függőség|
|Modul telepítése telepítővel|hibrid runbook-feldolgozó|A sandbox moduljainak támogatnia kell a másolást.|
|A 4.7.2-től eltérő .NET-keretrendszer verziót igénylő runbookok vagy modulok használata|hibrid runbook-feldolgozó|Az Azure-beli munkaterületek támogatják a .NET-keretrendszer 4.7.2, és más verzióra történő frissítés nem támogatott.|
|Jogosultságszint-emelést igénylő parancsfájlok futtatása|hibrid runbook-feldolgozó|A sandboxok nem engedélyezik a jogosultságszint-emelést. A hibrid Runbook-feldolgozók kikapcsolhatják az UAC-t, és a jogosultságszint-emelést igénylő parancs futtatásakor a [Meghívási parancsot](/powershell/module/microsoft.powershell.core/invoke-command) is használhatják.|
|Windows Management Instrumentation (WMI) elérését igénylő parancsfájlok futtatása|hibrid runbook-feldolgozó|A Felhőbeli munkaterületeken futó feladatok nem férnek hozzá a WMI-szolgáltatóhoz. |

## <a name="temporary-storage-in-a-sandbox"></a>Ideiglenes tároló a homokozóban

Ha ideiglenes fájlokat kell létrehoznia a runbook logikájának részeként, az Azure- `$env:TEMP` ban futó Runbookok Azure-beli Homokozójában használhatja a Temp mappát (azaz). Az egyetlen korlátozás nem használható több mint 1 GB lemezterületre, amely az egyes munkaterületek kvótája. A PowerShell-munkafolyamatok használatakor ez a forgatókönyv problémát okozhat, mivel a PowerShell-munkafolyamatok ellenőrzőpontokat használnak, és a szkriptet egy másik Sandboxban lehet újrapróbálni.

A hibrid homokozóval a `C:\temp` hibrid Runbook-feldolgozók tárterületének rendelkezésre állása alapján lehet használni. Az Azure-beli virtuális gépekre vonatkozó javaslatok azonban nem használhatják az [ideiglenes lemezt](../virtual-machines/managed-disks-overview.md#temporary-disk) Windows vagy Linux rendszeren a megőrizni kívánt adatmennyiséghez.

## <a name="resources"></a>Források

A runbookok tartalmaznia kell a logikai [erőforrásokat](/rest/api/resources/resources), például a virtuális gépeket, a hálózatot és az erőforrásokat a hálózaton. Az erőforrások egy Azure-előfizetéshez vannak kötve, és a megfelelő hitelesítő adatok megkövetelése az erőforrásokhoz való runbookok. A runbook erőforrásainak kezelésével kapcsolatos példát az [erőforrások kezelése](manage-runbooks.md#handle-resources)című témakörben talál.

## <a name="security"></a>Biztonság

A Azure Automation a [Azure Security Center (ASC)](../security-center/security-center-introduction.md) használatával biztosítja az erőforrások biztonságát, és felismeri a támadásokat a Linux rendszerekben. A biztonság a munkaterhelések között érhető el, függetlenül attól, hogy az erőforrások az Azure-ban vannak-e. Lásd: [Bevezetés a hitelesítésbe Azure Automation](automation-security-overview.md).

ASC – olyan felhasználók számára, akik aláírt vagy aláíratlan parancsfájlokat futtathatnak egy virtuális gépen. Ha olyan felhasználó, aki rendszergazdai jogosultságokkal rendelkezik egy virtuális géphez, explicit módon konfigurálnia kell a gépet digitális aláírással, vagy ki kell kapcsolni. Ellenkező esetben csak olyan parancsfájlt futtathat, amely az operációs rendszer frissítéseit az Automation-fiók létrehozása és a megfelelő funkció engedélyezése után alkalmazza.

## <a name="subscriptions"></a>Előfizetések

Az Azure- [előfizetések](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) a Microsofttal kötött szerződéssel rendelkeznek egy vagy több felhőalapú szolgáltatás használatára, amelyhez díjat számítunk fel. Azure Automation esetében minden előfizetés egy Azure Automation-fiókhoz van csatolva, és [több előfizetést is létrehozhat](manage-runbooks.md#work-with-multiple-subscriptions) a fiókban.

## <a name="credentials"></a>Hitelesítő adatok

A runbook megfelelő [hitelesítő adatokat](shared-resources/credentials.md) igényel minden erőforráshoz való hozzáféréshez, akár az Azure-ban, akár a harmadik féltől származó rendszerekben. Ezeket a hitelesítő adatokat Azure Automation, Key Vault stb. tárolja.  

## <a name="azure-monitor"></a>Azure Monitor

A Azure Automation a [Azure monitor](../azure-monitor/overview.md) használja a gépi műveleteinek figyeléséhez. A műveletekhez Log Analytics munkaterület és egy [log Analytics ügynök](../azure-monitor/agents/log-analytics-agent.md)szükséges.

### <a name="log-analytics-agent-for-windows"></a>Windows-ügynök Log Analytics

A Windows rendszerhez készült [log Analytics agent](../azure-monitor/agents/agent-windows.md) Azure monitor a Windows rendszerű virtuális gépek és a fizikai számítógépek kezelésére használható. A gépek akár az Azure-ban, akár egy nem Azure-környezetben, például egy helyi adatközpontban is futtathatók.

>[!NOTE]
>A Windows rendszerhez készült Log Analytics ügynök korábban Microsoft monitoring Agent (MMA) néven ismert.

### <a name="log-analytics-agent-for-linux"></a>Linux-Log Analytics ügynök

A [linux log Analytics-ügynöke](../azure-monitor/agents/agent-linux.md) hasonlóan működik a Windows-ügynökhöz, de a Linux rendszerű számítógépeket csatlakoztatja a Azure monitorhoz. Az ügynök olyan **nxautomation** -felhasználói fiókkal van telepítve, amely lehetővé teszi a rendszergazdai jogosultságokat igénylő parancsok végrehajtását, például egy hibrid Runbook-feldolgozón. A **nxautomation** fiók olyan rendszerfiók, amely nem igényel jelszót.

A [Linux Hybrid Runbook Worker telepítése](automation-linux-hrw-install.md)során a megfelelő sudo engedélyekkel rendelkező **nxautomation** -fióknak jelen kell lennie. Ha megpróbálja telepíteni a munkavégzőt, és a fiók nem létezik, vagy nem rendelkezik a megfelelő engedélyekkel, a telepítés sikertelen lesz.

Ne módosítsa a `sudoers.d` mappa vagy a tulajdonosának engedélyeit. A **nxautomation** -fiókhoz sudo engedély szükséges, és az engedélyek nem távolíthatók el. Ha bizonyos mappákra vagy parancsokra korlátozza ezt a korlátozást, előfordulhat, hogy a rendszer megszakítja a változást.

A Log Analytics-ügynökhöz és a **nxautomation** -fiókhoz elérhető naplók a következők:

* /var/opt/Microsoft/omsagent/log/omsagent.log – Log Analytics ügynök naplója
* /var/opt/Microsoft/omsagent/Run/automationworker/Worker.log – Automation Worker-napló

>[!NOTE]
>A Update Management részeként engedélyezett **nxautomation** -felhasználó csak az aláírt runbookok hajtja végre.

## <a name="runbook-permissions"></a>Runbook-engedélyek

A runbook engedélyekkel kell rendelkeznie az Azure-ba történő hitelesítéshez a hitelesítő adatokkal. Lásd: [Azure Automation hitelesítés áttekintése](automation-security-overview.md).

## <a name="modules"></a>Modulok

Azure Automation számos alapértelmezett modult támogat, köztük néhány AzureRM modult (AzureRM. Automation) és egy modult, amely több belső parancsmagot is tartalmaz. A támogatottak a telepíthető modulok is, beleértve az az modulokat (az. Automation), amelyek jelenleg a AzureRM modulok előnyben részesített használatával működnek. A runbookok és a DSC-konfigurációkhoz elérhető modulok részletes ismertetését lásd: [Azure Automation-modulok kezelése](shared-resources/modules.md).

## <a name="certificates"></a>Tanúsítványok

A Azure Automation [tanúsítványokat](shared-resources/certificates.md) használ az Azure-ba történő hitelesítéshez, vagy hozzáadja azokat az Azure-hoz vagy harmadik féltől származó erőforrásokhoz. A rendszer biztonságosan tárolja a tanúsítványokat a runbookok és a DSC-konfigurációkhoz való hozzáféréshez.

A runbookok olyan önaláírt tanúsítványokat is használhat, amelyeket nem egy hitelesítésszolgáltató (CA) aláír. Lásd: [új tanúsítvány létrehozása](shared-resources/certificates.md#create-a-new-certificate).

## <a name="jobs"></a>Feladatok

Azure Automation támogatja, hogy egy környezet ugyanazt az Automation-fiókból futtassa a feladatokat. Egyetlen runbook egyszerre több feladat is futhat. Minél több feladatot futtat egyszerre, annál gyakrabban lehet ugyanarra a sandboxra elküldeni. 

Az ugyanabban a homokozóban futó feladatok hatással lehetnek egymásra. Az egyik példa a [Disconnect-AzAccount](/powershell/module/az.accounts/disconnect-azaccount) parancsmagot futtatja. Ennek a parancsmagnak a végrehajtása megszakítja az egyes runbook-feladatokat a megosztott homokozó folyamat során. Ehhez a forgatókönyvhöz a következő témakörben talál példát: [egyidejű feladatok megakadályozása](manage-runbooks.md#prevent-concurrent-jobs).

>[!NOTE]
>Előfordulhat, hogy az Azure-beli homokozóban futó runbook indított PowerShell-feladatok nem teljes körű [PowerShell nyelvi módban](/powershell/module/microsoft.powershell.core/about/about_language_modes)futnak.

### <a name="job-statuses"></a>Feladatok állapota

A következő táblázat a feladatokhoz lehetséges állapotokat ismerteti. Megtekintheti az összes runbook-feladat állapotának összegzését, vagy részletezheti a Azure Portal adott runbook-feladatának részleteit. Az Log Analytics-munkaterülettel való integrációt úgy is konfigurálhatja, hogy továbbítsa a runbook feladatok állapotát és a feladatok folyamait. A Azure Monitor-naplók integrálásával kapcsolatos további információkért lásd: a [feladatok állapotának és a feladatok adatfolyamának továbbítása az automatizálásból a Azure monitor naplókba](automation-manage-send-joblogs-log-analytics.md). Lásd még: a [feladatok állapotának beszerzése](manage-runbooks.md#obtain-job-statuses) a runbook lévő állapotok kezeléséhez.

| Állapot | Leírás |
|:--- |:--- |
| Aktiválása |A rendszer aktiválja a feladatot. |
| Befejeződött |A feladat sikeresen befejeződött. |
| Sikertelen |A grafikus vagy a PowerShell-munkafolyamat runbook nem sikerült lefordítani. Nem sikerült elindítani egy PowerShell-runbook, vagy kivétel történt a feladatokban. Lásd: [Azure Automation runbook-típusok](automation-runbook-types.md).|
| Sikertelen, várakozás erőforrásokra |A feladatot nem sikerült végrehajtani, mert elérte a [valós megosztási](#fair-share) korlátot háromszor, és ugyanabból az ellenőrzőpontból vagy a runbook elejétől indul el. |
| Várólistán |A művelet arra vár, hogy az automatizálási feldolgozón lévő erőforrások elérhetővé váljanak, hogy el lehessen indítani. |
| Folytatás |A rendszer a felfüggesztést követően folytatja a feladat folytatását. |
| Futó |A feladat fut. |
| Futtatás, várakozás erőforrásokra |A feladatot eltávolították a memóriából, mert elérte a méltányos megosztási korlátot. Hamarosan folytatódik az utolsó ellenőrzőponttól. |
| Indítás |A feladat hozzá lett rendelve egy feldolgozóhoz, és a rendszer megkezdi. |
| Leállítva |A feladatot leállította a felhasználó, mielőtt az befejeződött volna. |
| Leállítás |A rendszer leállítja a feladatot. |
| Felfüggesztve |Csak a [grafikus és a PowerShell munkafolyamat-runbookok](automation-runbook-types.md) vonatkozik. A felhasználó, a rendszer vagy a runbook egy parancsa felfüggesztette a feladatot. Ha egy runbook nem rendelkezik ellenőrzőponttal, az elejétől kezdődik. Ha ellenőrzőponttal rendelkezik, újra elindíthatja, és folytathatja az utolsó ellenőrzőpontot. A rendszer csak akkor felfüggeszti a runbook, ha kivétel történik. Alapértelmezés szerint a `ErrorActionPreference` változó a Continue (folytatás) értékre van állítva, ami azt jelzi, hogy a művelet hibán fut. Ha a preferencia változó leállítás értékre van állítva, a művelet egy hibára felfüggeszti a feladatot.  |
| Felfüggesztés |Csak a [grafikus és a PowerShell munkafolyamat-runbookok](automation-runbook-types.md) vonatkozik. A rendszer megkísérli a feladat felfüggesztését a felhasználó kérésére. A runbooknak el kell érnie a következő ellenőrzőpontot a felfüggesztés előtt. Ha már elvégezte az utolsó ellenőrzőpontot, akkor a felfüggesztés előtt befejeződik. |

## <a name="activity-logging"></a>Tevékenységnaplózás

A runbookok végrehajtása Azure Automation az Automation-fiókhoz tartozó tevékenység naplójában lévő adatokat ír. A napló használatával kapcsolatos részletekért lásd: [részletek beolvasása a tevékenység naplójából](manage-runbooks.md#retrieve-details-from-activity-log).

## <a name="exceptions"></a>Kivételek

Ez a szakasz néhány módszert ismertet a runbookok kivételek vagy időszakos problémák kezelésére. Ilyen például egy WebSocket-kivétel. A kivételek kezelése nem akadályozza meg, hogy az átmeneti hálózati hibák okozzák a runbookok.

### <a name="erroractionpreference"></a>ErrorActionPreference

A [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) változó határozza meg, hogy a PowerShell hogyan válaszol a megszakítást nem okozó hibára. A hibák megszakítása mindig megszűnik, és a nem érinti `ErrorActionPreference` .

A runbook használatakor a `ErrorActionPreference` szokásos módon megszakítást okozó hiba (például `PathNotFound` a [Get-ChildItem](/powershell/module/microsoft.powershell.management/get-childitem) parancsmagból) leállítja a runbook befejezését. A következő példa a használatát mutatja be `ErrorActionPreference` . A végső [írási-kimeneti](/powershell/module/microsoft.powershell.utility/write-output) parancs soha nem hajtható végre, mert a szkript leáll.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Próbálja ki a fogást

[Próbálja meg a Catch végül](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) a PowerShell-szkriptekben használni a leállítási hibák kezelésére. A szkript ezzel a mechanizmussal meghatározott kivételeket vagy általános kivételeket tud kifogni. Az `catch` utasítást a hibák nyomon követésére vagy kipróbálására kell használni. A következő példa egy nem létező fájl letöltését kísérli meg. A kivételt kigyűjti `System.Net.WebException` , és a többi kivétel utolsó értékét adja vissza.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Throw

A [dob](/powershell/module/microsoft.powershell.core/about/about_throw) használatával leállítási hiba hozható fel. Ez a mechanizmus akkor lehet hasznos, ha saját logikát definiál egy runbook. Ha a parancsfájl megfelel egy olyan feltételnek, amelynek meg kell szüntetnie, akkor az `throw` utasítással leállíthatja. A következő példa ezt az utasítást használja egy kötelező Function paraméter megjelenítéséhez.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Hibák

A runbookok hibákat kell kezelnie. Azure Automation támogatja a PowerShell-hibák két típusát, a megszakítást és a nem megszakítást. 

Ha leállítja a hibákat, a runbook végrehajtása leáll. A runbook nem sikerült, mert a feladatok állapota sikertelen.

A nem megszakítást okozó hibák lehetővé teszik a parancsfájlok folytatását még azután is, hogy azok bekövetkeztek. A megszakítást nem okozó hiba egy példa, amely akkor fordul elő, ha egy runbook a `Get-ChildItem` parancsmagot olyan elérési úttal használja, amely nem létezik. A PowerShell látja, hogy az elérési út nem létezik, hibát jelez, és folytatja a következő mappába. A hiba ebben az esetben nem állítja be a runbook-feladatok állapotát Sikertelenre, és előfordulhat, hogy a feladatot még végre kell hajtani. Ha kényszeríteni szeretné, hogy egy runbook megszakítása leálljon egy megszakítást nem okozó hiba esetén, a `ErrorAction Stop` parancsmagot használhatja.

## <a name="calling-processes"></a>Folyamatok hívása

Az Azure-beli munkaterületeken futó runbookok nem támogatja a hívási folyamatokat, például a végrehajtható fájlok (**. exe** fájlok) vagy az alfolyamatok használatát. Ennek az az oka, hogy az Azure-beli sandbox egy olyan tárolóban fut, amely esetleg nem fér hozzá az összes mögöttes API-hoz. A harmadik féltől származó szoftvereket vagy az alfolyamatok hívásait igénylő forgatókönyvek esetén runbook kell végrehajtania egy [hibrid runbook-feldolgozón](automation-hybrid-runbook-worker.md).

## <a name="device-and-application-characteristics"></a>Eszköz és alkalmazás jellemzői

Az Azure-beli Runbook-feladatok nem férnek hozzá semmilyen eszközhöz vagy alkalmazáshoz. A Windowson a teljesítmény-mérőszámok lekérdezéséhez használt leggyakoribb API a WMI, amely a memóriában és a CPU-használatban előforduló gyakori mérőszámokat is használja. Nem számít azonban, hogy milyen API-t használ, mivel a felhőben futó feladatok nem férnek hozzá a Web-Based Enterprise Management (WBEM) Microsoft általi megvalósításához. Ez a platform a CIMre (CIM) épül, amely az eszközök és alkalmazások jellemzőinek meghatározására szolgáló iparági szabványokat biztosítja.

## <a name="webhooks"></a>Webhookok

A külső szolgáltatások, például az Azure DevOps Services és a GitHub, Azure Automationban indíthatnak el runbook. Ilyen típusú indítás esetén a szolgáltatás egy [webhookot](automation-webhooks.md) használ egyetlen HTTP-kérelem használatával. A webhookok használata lehetővé teszi, hogy a runbookok teljes Azure Automation funkció megvalósítása nélkül induljon el.

## <a name="shared-resources"></a><a name="fair-share"></a>Megosztott erőforrások

Az Azure a felhőben lévő összes runbookok között megoszthatja az erőforrásokat, a Fair Share nevű koncepciót alkalmazva. A méltányos megosztás használatával az Azure átmenetileg kitölti vagy leállítja a három óránál hosszabb ideig futó feladatokat. A [PowerShell-runbookok](automation-runbook-types.md#powershell-runbooks) és a [Python-runbookok](automation-runbook-types.md#python-runbooks) kapcsolatos feladatok leállnak, és nem indulnak újra, és a feladat állapota leáll.

A hosszú ideig futó Azure Automation feladatok esetében ajánlott hibrid Runbook-feldolgozót használni. A hibrid Runbook-feldolgozók nem korlátozódnak a méltányos megosztásra, és nincs korlátozás arra vonatkozóan, hogy mennyi ideig lehet végrehajtani a Runbook. A többi [feladattípus az](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) Azure-beli és a hibrid Runbook-feldolgozókra is érvényes. Míg a hibrid Runbook-feldolgozók nem korlátozzák a három órás igazságos részvény korlátot, a runbookok-t olyan feldolgozók futtatására kell fejleszteni, amelyek támogatják a váratlan helyi infrastruktúra-problémákból való újraindítást.

Egy másik lehetőség, hogy optimalizálja a runbook a gyermek runbookok használatával. Előfordulhat például, hogy a runbook több erőforráson ugyanazt a függvényt is felvehetik, például egy adatbázis-művelettel több adatbázison. Ezt a függvényt áthelyezheti egy [alárendelt runbook](automation-child-runbooks.md) , és a runbook meghívja azt a [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook)használatával. A gyermek runbookok párhuzamosan hajthatók végre külön folyamatokban.

A gyermek runbookok használata csökkenti a szülő runbook befejezésének teljes időtartamát. A runbook használhatja a [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) parancsmagot a gyermek runbook feladatainak vizsgálatához, ha még több művelet van a gyermek befejeződése után.

## <a name="next-steps"></a>Következő lépések

* A PowerShell-runbook megkezdéséhez tekintse meg az [oktatóanyag: PowerShell-Runbook létrehozása](learn/automation-tutorial-runbook-textual-powershell.md)című témakört.
* A runbookok használatával kapcsolatban lásd: [Runbookok kezelése a Azure Automationban](manage-runbooks.md).
* A PowerShell részleteiért lásd: [PowerShell-dokumentumok](/powershell/scripting/overview).
* A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation#automation).
