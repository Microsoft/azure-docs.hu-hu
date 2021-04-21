---
title: Runbook végrehajtása az Azure Automationben
description: Ez a cikk áttekintést nyújt a runbookok feldolgozásának Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/23/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0807b11adfc46b9c32a8f7bd36a2f7d4db519975
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830519"
---
# <a name="runbook-execution-in-azure-automation"></a>Runbook végrehajtása az Azure Automationben

A folyamatautomatizálás Azure Automation lehetővé teszi a PowerShell, a PowerShell-munkafolyamat és a grafikus runbookok létrehozására és kezelésére. Részletekért lásd: [Azure Automation runbookok.](automation-runbook-types.md) 

Az Automation a bennük definiált logika alapján hajtja végre a runbookokat. Ha egy runbook megszakad, az elején újraindul. Ehhez a viselkedéshez olyan runbookokat kell írnia, amelyek támogatják az újraindítást átmeneti problémák esetén.

Ha egy runbookot a Azure Automation létrehoz egy feladatot, amely a runbook egyetlen végrehajtási példánya. Minden feladat úgy fér hozzá az Azure-erőforrásokhoz, hogy kapcsolatot létesít az Azure-előfizetésével. A feladat csak akkor fér hozzá az adatközpont erőforrásaihoz, ha azok elérhetők a nyilvános felhőből.

Azure Automation hozzárendel egy feldolgozót, hogy a runbook végrehajtása során minden feladatot futtason. Míg a dolgozókat számos Azure-fiók használja, a különböző Automation-fiókokból származó feladatok el vannak különítve egymástól. Nem szabályozhatja, hogy a feladat mely feldolgozói szolgáltatásokat kérje.

Amikor megtekinti a runbookok listáját a Azure Portal megjeleníti az egyes runbookok egyes elindított feladatának állapotát. Azure Automation legfeljebb 30 napig tárolja a feladatnaplókat.

Az alábbi ábrán egy Runbook-feladat életciklusa látható [a PowerShell-runbookokhoz,](automation-runbook-types.md#powershell-runbooks)a [PowerShell-munkafolyamat-runbookokhoz](automation-runbook-types.md#powershell-workflow-runbooks)és a [grafikus runbookokhoz.](automation-runbook-types.md#graphical-runbooks)

![Feladatállapotok – PowerShell-munkafolyamat](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Runbook végrehajtási környezete

A runbookok Azure Automation azure-beli vagy hibrid [runbook-feldolgozón is futtathatók.](automation-hybrid-runbook-worker.md) 

Ha a runbookokat úgy tervezték, hogy hitelesítsék és futtassanak az Azure-beli erőforrásokon, egy Azure-védőkörnyezetben futnak, amely egy olyan megosztott környezet, amelyet több feladat is használhat. Az ugyanazt a védőfalat használó feladatokat a sandbox erőforráskorlátai kötik. Az Azure-beli sandbox-környezet nem támogatja az interaktív műveleteket. Ez megakadályozza az összes folyamaton kívüli COM-kiszolgálóhoz való hozzáférést, és nem támogatja [WMI-hívások](/windows/win32/wmisdk/wmi-architecture) kezdeményezését a Win32-szolgáltatóhoz a runbookban.  Ezeket a forgatókönyveket csak a runbook windowsos hibrid runbook-feldolgozón való futtatásával lehet támogatni.


Hibrid runbook-feldolgozó használatával [runbookokat](automation-hybrid-runbook-worker.md) futtathat közvetlenül a szerepkört tartalmazó számítógépen és a környezet helyi erőforrásain. Azure Automation tárolja és kezeli a runbookokat, majd kézbesíti azokat egy vagy több hozzárendelt számítógépnek.

>[!NOTE]
>Linux rendszerű hibrid runbook-feldolgozón való futtatáshoz a szkripteket alá kell írni, és a feldolgozót ennek megfelelően kell konfigurálni. Másik lehetőségként az [aláírás érvényesítését ki kell kapcsolva.](automation-linux-hrw-install.md#turn-off-signature-validation)

Az alábbi táblázat néhány runbook-végrehajtási feladatot sorol fel, amelyekhez az ajánlott végrehajtási környezetek vannak felsorolva.

|Feladat|Ajánlás|Jegyzetek|
|---|---|---|
|Integrálás Azure-erőforrásokkal|Azure-beli sandbox|Az Azure-ban üzemeltetett hitelesítés egyszerűbb. Ha hibrid runbook-feldolgozót használ egy Azure-beli virtuális gépen, használhat runbook-hitelesítést felügyelt [identitásokkal.](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)|
|Optimális teljesítmény az Azure-erőforrások kezeléséhez|Azure-beli sandbox|A szkript ugyanabban a környezetben fut, amely kisebb késéssel rendelkezik.|
|Működési költségek minimalizálása|Azure-beli sandbox|Nincs számítási többletterhelés, és nincs szükség virtuális gépre.|
|Hosszan futó szkript végrehajtása|hibrid runbook-feldolgozó|Az Azure-beli védőfalak [erőforráskorlátokkal is szolgálnak.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Helyi szolgáltatások interakciója|hibrid runbook-feldolgozó|Közvetlenül elérheti a gazdagépet, más felhőkörnyezetekben vagy a helyszíni környezetben található erőforrásokat. |
|Harmadik féltől származó szoftverek és végrehajtható fájlok megkövetelve|hibrid runbook-feldolgozó|Ön felügyeli az operációs rendszert, és szoftvereket telepíthet.|
|Fájl vagy mappa figyelése runbook segítségével|hibrid runbook-feldolgozó|[Figyelőfeladat használata](automation-watchers-tutorial.md) hibrid runbook-feldolgozón.|
|Erőforrás-igényes szkript futtatása|hibrid runbook-feldolgozó| Az Azure-beli védőfal [erőforráskorlátokkal is szolgál.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Adott követelményekkel kapcsolatos modulok használata| hibrid runbook-feldolgozó|Néhány példa:</br> WinSCP – függőség a winscp.exe </br> IIS-felügyelet – függőség az IIS engedélyezésével vagy kezelésével|
|Modul telepítése telepítővel|hibrid runbook-feldolgozó|A sandbox moduljainak támogatniuk kell a másolást.|
|Olyan runbookok vagy modulok használata, amelyek .NET-keretrendszer 4.7.2-estől eltérő verziójúak|hibrid runbook-feldolgozó|Az Azure-.NET-keretrendszer támogatja a 4.7.2-es verziót, és a másik verzióra való frissítés nem támogatott.|
|Jogosultságszint-emelést igénylő szkriptek futtatása|hibrid runbook-feldolgozó|A védőfal nem engedélyezi a jogosultságszint-emelést. Hibrid runbook-feldolgozóval kikapcsolhatja az UAC-t, és használhatja az [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command) parancsot a jogosultságszint-emelést igénylő parancs futtatásakor.|
|Hozzáféréssel igénylő szkriptek futtatása Windows Management Instrumentation (WMI)|hibrid runbook-feldolgozó|A felhőben lévő védőfalon futó feladatok nem férnek hozzá a WMI-szolgáltatóhoz. |

## <a name="temporary-storage-in-a-sandbox"></a>Ideiglenes tároló egy védőfalban

Ha ideiglenes fájlokat kell létrehoznia a runbook logikájának részeként, használhatja az Azure-beli runbookok Azure-beli futtatásához használt Temp mappát (azaz ) az Azure-beli `$env:TEMP` runbookok számára. Az egyetlen korlátozás az, hogy nem használhat 1 GB-nál több lemezterületet, amely az egyes védőfalak kvótája. PowerShell-munkafolyamatok használata esetén ez a forgatókönyv problémát okozhat, mivel a PowerShell-munkafolyamatok ellenőrzőpontokat használnak, és a szkriptet egy másik védőfalon lehet újra használni.

A hibrid védőfal használata a hibrid runbook-feldolgozón a tárolás rendelkezésre `C:\temp` állása alapján használható. Az Azure-beli virtuális gépekre [](../virtual-machines/managed-disks-overview.md#temporary-disk) vonatkozó javaslatok szerint azonban ne használja az ideiglenes lemezt Windows vagy Linux rendszeren a megőrzni szükséges adatokhoz.

## <a name="resources"></a>Források

A runbooknak tartalmaznia kell [](/rest/api/resources/resources)az erőforrásokkal (például a virtuális gépekkel, a hálózattal és a hálózaton futó erőforrásokkal) való foglalkozó logikát. Az erőforrások egy Azure-előfizetéshez vannak kötve, és a runbookok minden erőforrás eléréséhez megfelelő hitelesítő adatokat igényelnek. Egy runbook erőforrásainak kezelésére vonatkozó példáért lásd: [Erőforrások kezelése.](manage-runbooks.md#handle-resources)

## <a name="security"></a>Biztonság

Azure Automation az Azure Security Center [(ASC)](../security-center/security-center-introduction.md) segítségével biztosítja az erőforrások biztonságát, és észleli a biztonsági réseket a Linux-rendszerekben. A biztonság minden számítási feladatban biztosított, függetlenül attól, hogy az erőforrások az Azure-ban vannak-e vagy sem. Lásd: [Bevezetés a Azure Automation.](automation-security-overview.md)

Az ASC korlátozásokat vezet be az olyan felhasználókra, akik bármilyen ( aláírt vagy aláíratlan) szkriptet futtatnak egy virtuális gépen. Ha Ön rendszergazdai hozzáféréssel rendelkezik egy virtuális géphez, akkor a gépet kifejezetten digitális aláírással kell konfigurálnia, vagy ki kell kapcsolnia. Ellenkező esetben csak az Automation-fiók létrehozása és a megfelelő funkció engedélyezése után futtathat olyan parancsfájlt, amely az operációs rendszer frissítéseit alkalmazza.

## <a name="subscriptions"></a>Előfizetések

Az [Azure-előfizetés](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) egy szerződés a Microsofttal egy vagy több felhőalapú szolgáltatás használatára, amelyekért díjat kell fizetni. A Azure Automation előfizetések egy Azure Automation fiókhoz vannak kapcsolva, és [](manage-runbooks.md#work-with-multiple-subscriptions) több előfizetést is létrehozhat a fiókban.

## <a name="credentials"></a>Hitelesítő adatok

A runbooknak megfelelő [hitelesítő adatokra van szüksége](shared-resources/credentials.md) bármely erőforrás eléréséhez, akár Azure-beli, akár külső rendszerek esetében. Ezeket a hitelesítő adatokat a rendszer Azure Automation, Key Vault stb. tárolja.  

## <a name="azure-monitor"></a>Azure Monitor

Azure Automation a virtuális gép [Azure Monitor](../azure-monitor/overview.md) a gépműveletei figyelése érdekében. A műveletekhez Log Analytics-munkaterület és [Log Analytics-ügynök szükséges.](../azure-monitor/agents/log-analytics-agent.md)

### <a name="log-analytics-agent-for-windows"></a>Log Analytics-ügynök Windowshoz

A [Windowshoz használható Log Analytics-ügynök Azure Monitor](../azure-monitor/agents/agent-windows.md) windowsos virtuális gépek és fizikai számítógépek kezeléséhez. A gépek az Azure-ban vagy nem Azure-környezetben, például helyi adatközpontban is futnak.

>[!NOTE]
>A Windowshoz használt Log Analytics-ügynök korábban Microsoft Monitoring Agent (MMA) néven volt ismert.

### <a name="log-analytics-agent-for-linux"></a>Log Analytics-ügynök Linuxhoz

A [Linuxhoz használható Log Analytics-ügynök](../azure-monitor/agents/agent-linux.md) hasonlóan működik, mint a Windows-ügynök, de linuxos számítógépeket csatlakoztat a Azure Monitor. Az ügynök egy **nxautomation** felhasználói fiókkal van telepítve, amely lehetővé teszi a gyökér szintű engedélyeket igénylő parancsok végrehajtását, például egy hibrid runbook-feldolgozón. Az **nxautomation fiók** egy olyan rendszerfiók, amely nem igényel jelszót.

A megfelelő sudo engedélyekkel rendelkező **nxautomation** fióknak jelen kell lennie egy Linux rendszerű hibrid [runbook-feldolgozó telepítése során.](automation-linux-hrw-install.md) Ha megpróbálja telepíteni a feldolgozót, és a fiók nincs jelen, vagy nem rendelkezik a megfelelő engedélyekkel, a telepítés sikertelen lesz.

Ne módosítsa a mappa engedélyét vagy `sudoers.d` tulajdonosát. Sudo engedély szükséges az **nxautomation** fiókhoz, és az engedélyeket nem szabad eltávolítani. Ha ezt bizonyos mappákra vagy parancsokra korlátozza, az a feltörést eredményez.

A Log Analytics-ügynökhöz és az **nxautomation-fiókhoz** elérhető naplók a következőek:

* /var/opt/microsoft/omsagent/log/omsagent.log – Log Analytics-ügynök naplója
* /var/opt/microsoft/omsagent/run/automationworker/worker.log – Automation feldolgozói napló

>[!NOTE]
>Az **nxautomation** felhasználó az alkalmazás részeként Update Management csak aláírt runbookokat hajt végre.

## <a name="runbook-permissions"></a>Runbook-engedélyek

A runbooknak engedélyekre van szüksége az Azure-ba történő, hitelesítő adatokon keresztüli hitelesítéshez. Lásd [a Azure Automation áttekintését.](automation-security-overview.md)

## <a name="modules"></a>Modulok

Azure Automation több alapértelmezett modult is támogat, beleértve néhány AzureRM-modult (AzureRM.Automation) és egy modult, amely számos belső parancsmagot tartalmaz. Emellett támogatottak a telepíthető modulok is, beleértve az Az modulokat (Az.Automation), amelyek jelenleg az AzureRM-modulok helyett használatosak. A runbookok és a DSC-konfigurációk számára elérhető modulok részleteiért lásd: Modulok kezelése a [Azure Automation.](shared-resources/modules.md)

## <a name="certificates"></a>Tanúsítványok

Azure Automation [tanúsítványokat használ az](shared-resources/certificates.md) Azure-beli hitelesítéshez, vagy hozzáadja őket az Azure- vagy külső erőforrásokhoz. A tanúsítványok biztonságosan vannak tárolva a runbookok és a DSC-konfigurációk számára.

A runbookok használhatnak önaírt tanúsítványokat, amelyeket nem hitelesítésszolgáltató (CA) írt alá. Lásd: [Create a new certificate (Új tanúsítvány létrehozása).](shared-resources/certificates.md#create-a-new-certificate)

## <a name="jobs"></a>Feladatok

Azure Automation környezetben futtathat feladatokat ugyanatból az Automation-fiókból. Egy runbookon egyszerre több feladat is futhat. Minél több feladat fut egyszerre, annál gyakrabban küldik el őket ugyanabba a sandboxba. 

Az ugyanabban a sandbox-folyamatban futó feladatok hatással lehetnek egymásra. Erre példa a [Disconnect-AzAccount](/powershell/module/az.accounts/disconnect-azaccount) parancsmag futtatása. A parancsmag végrehajtása megszakítja a megosztott sandbox-folyamat minden runbook-feladatát. Példa a forgatókönyvvel való munkavégzésre: [Egyidejű feladatok megakadályozása.](manage-runbooks.md#prevent-concurrent-jobs)

>[!NOTE]
>Előfordulhat, hogy az Azure-védőfalon futó runbookból indított PowerShell-feladatok nem futnak teljes PowerShell nyelvi [módban.](/powershell/module/microsoft.powershell.core/about/about_language_modes)

### <a name="job-statuses"></a>Feladat állapotai

Az alábbi táblázat a feladat lehetséges állapotokat ismerteti. Megtekintheti az összes Runbook-feladat állapotösszegzését, vagy részletezheti egy adott runbook-feladat részleteit a Azure Portal. Konfigurálhatja a Log Analytics-munkaterület integrációját is a runbook-feladat állapotának és a feladatstreamek továbbítása érdekében. További információ a naplók integrálásáról Azure Monitor feladatállapot és [feladatstreamek továbbítása](automation-manage-send-joblogs-log-analytics.md)az Automationből a Azure Monitor naplókba. A [Runbookok állapotával](manage-runbooks.md#obtain-job-statuses) végzett munka példájért lásd még a Feladatállapotok beszerzése.

| Állapot | Leírás |
|:--- |:--- |
| Aktiváló |A feladat aktiválása folyamatban van. |
| Befejeződött |A feladat sikeresen befejeződött. |
| Sikertelen |Egy grafikus vagy PowerShell-munkafolyamati runbook fordítása sikertelen volt. Egy PowerShell-runbook nem tudott elindulni, vagy a feladat kivételt okozott. Lásd: [Azure Automation runbooktípusok.](automation-runbook-types.md)|
| Sikertelen, erőforrásokra vár |A feladat sikertelen volt, mert háromszor érte el az igazságos megosztási korlátot, és minden alkalommal ugyanattól az ellenőrzőponttól vagy a runbook kezdetéből indult el. [](#fair-share) |
| Várólistán |A feladat arra vár, hogy egy Automation-feldolgozó erőforrásai elérhetővé válnak, és elindítható legyen. |
| Folytatás |A rendszer a felfüggesztés után újra a feladatot munkához áll. |
| Futó |A feladat fut. |
| Fut, várakozik az erőforrásokra |A feladatot eltávolította a memóriából, mert elérte az igazságos megosztási korlátot. Rövidesen az utolsó ellenőrzőponttól folytatódik. |
| Indítás |A feladat hozzá lett rendelve egy feldolgozóhoz, és a rendszer el is kezdi azt indulni. |
| Leállítva |A feladatot leállította a felhasználó, mielőtt az befejeződött volna. |
| Leállítás |A rendszer leállítja a feladatot. |
| Felfüggesztve |Csak a [grafikus és a PowerShell-munkafolyamat forgatókönyvére vonatkozik.](automation-runbook-types.md) A felhasználó, a rendszer vagy a runbook egy parancsa felfüggesztette a feladatot. Ha egy runbooknak nincs ellenőrzőpontja, akkor az elejétől indul. Ha rendelkezik ellenőrzőponttal, újraindulhat, és az utolsó ellenőrzőponttól folytathatja. A rendszer kivétel esetén csak felfüggeszti a runbookot. Alapértelmezés szerint a változó Értéke Folytatás, ami azt jelzi, hogy a `ErrorActionPreference` feladat folyamatosan hiba esetén fut. Ha a beállításváltozó Leállításra van állítva, a feladat egy hiba miatt felfüggeszti a feladatot.  |
| Felfüggesztés |Csak a [grafikus és a PowerShell-munkafolyamati runbookra vonatkozik.](automation-runbook-types.md) A rendszer a felhasználó kérésére próbálja felfüggeszteni a feladatot. A runbooknak el kell érnie a következő ellenőrzőpontot a felfüggesztés előtt. Ha már az utolsó ellenőrzőponton is áthaladt, akkor a felfüggesztés előtt befejeződik. |

## <a name="activity-logging"></a>Tevékenységnaplózás

A runbookok végrehajtása a Azure Automation az Automation-fiók tevékenységnaplójában írja a részleteket. A napló használatával kapcsolatos részletekért lásd: [Részletek lekérése a tevékenységnaplóból.](manage-runbooks.md#retrieve-details-from-activity-log)

## <a name="exceptions"></a>Kivételek

Ez a szakasz néhány módszert ismertet a runbookok kivételei vagy időszakos problémáinak kezelésével kapcsolatban. Ilyen például a WebSocket-kivétel. A kivételkezelés kijavítása megakadályozza, hogy az átmeneti hálózati hibák miatt a runbookok meghiúsulnak.

### <a name="erroractionpreference"></a>ErrorActionPreference

Az [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) változó határozza meg, hogy a PowerShell hogyan reagál a megszakítás nélküli hibákra. A megszakítást megszakító hibák mindig leállnak, és a nem érinti `ErrorActionPreference` a következőt: .

Ha a runbook a függvényt használja, egy általában megszakítást nem követő hiba ( például `ErrorActionPreference` a `PathNotFound` [Get-ChildItem](/powershell/module/microsoft.powershell.management/get-childitem) parancsmag) megakadályozza a runbook befejezését. Az alábbi példa a használatát mutatja `ErrorActionPreference` be. A [végső Write-Output parancs](/powershell/module/microsoft.powershell.utility/write-output) soha nem lesz végrehajtva, mivel a szkript leáll.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Próbálja ki a Catchet végül

[A Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) (Catch utolsó próbálkozás) a PowerShell-szkriptek használatával kezeli a lezáró hibákat. A szkript ezzel a mechanizmussal meghatározott kivételeket vagy általános kivételeket képes kifogni. A `catch` utasítással nyomon követhetők vagy kezelhetők a hibák. Az alábbi példa egy nem létező fájlt próbál letölteni. Elfogja `System.Net.WebException` a kivételt, és bármely más kivétel utolsó értékét adja vissza.

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

### <a name="throw"></a>Dobja

[A](/powershell/module/microsoft.powershell.core/about/about_throw) throw (visszadobás) segítségével megszakítási hiba generálható. Ez a mechanizmus akkor lehet hasznos, ha saját logikát definiál egy runbookban. Ha a szkript eleget tesz egy olyan feltételnek, amely leállítja, a utasítással `throw` leállíthatja azt. Az alábbi példa ezt az utasítást használja a szükséges függvényparaméterek kimutatásához.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Hibák

A runbooknak kezelnie kell a hibákat. Azure Automation a PowerShell-hibák két típusát támogatja: a megszakítást és a megszakítást nem. 

A megszakítási hibák leállítják a runbook végrehajtását azok előfordulásakor. A runbook Sikertelen feladatállapottal leáll.

A megszakítást nem lehetővé tő hibák lehetővé teszik, hogy a szkript azok bekövetkezését követően is folytatódjon. Megszakítás nélküli hiba lehet például az, amikor egy runbook a parancsmagot egy nem létező elérési út `Get-ChildItem` segítségével használja. A PowerShell látja, hogy az elérési út nem létezik, hibát jelez, és folytatja a következő mappával. Ebben az esetben a hiba nem a Sikertelen állapotú runbook-feladatra van állítva, és a feladat akár be is fejeződhet. Ha a runbookot megszakítást nem akadályozó hiba esetén le kell állítania, használja a `ErrorAction Stop` parancsmagon a következőt: .

## <a name="calling-processes"></a>Hívási folyamatok

Az Azure-védőfalon futó runbookok nem támogatják a hívási folyamatokat, például a végrehajtható fájlokat **(.exe** fájlokat) vagy az alfolyamatokat. Ennek az az oka, hogy az Azure-beli sandbox egy megosztott folyamat, amely egy olyan tárolóban fut, amely esetleg nem fér hozzá az összes mögöttes API-hoz. A harmadik féltől származó szoftvereket igénylő forgatókönyvek vagy alfolyamatok hívásai esetén runbookot kell végrehajtania egy hibrid [runbook-feldolgozón.](automation-hybrid-runbook-worker.md)

## <a name="device-and-application-characteristics"></a>Eszköz- és alkalmazásjellemzők

Az Azure-beli runbook-feladatok nem férhetnek hozzá az eszköz vagy az alkalmazás tulajdonságaihoz. A windowsos teljesítménymetrikák lekérdezéséhez leggyakrabban használt API a WMI, a gyakori metrikák között pedig a memória- és PROCESSZORhasználat. Nem számít azonban, hogy milyen API-t használ, mivel a felhőben futó feladatok nem férhetnek hozzá a Web-Based Enterprise Management (WBEM) MicrosoftWeb-Based implementációhoz. Ez a platform a CIM (CIM) alapján készült, és iparági szabványokat biztosít az eszközök és alkalmazások jellemzőinek meghatározásához.

## <a name="webhooks"></a>Webhookok

A külső szolgáltatások, például az Azure DevOps Services és a GitHub, elindíthat egy runbookot a Azure Automation. Az ilyen típusú indításhoz a szolgáltatás [egy webhookot használ](automation-webhooks.md) egyetlen HTTP-kérésen keresztül. A webhookok használata lehetővé teszi a runbookok teljes körű szolgáltatás implementációja Azure Automation nélkül.

## <a name="shared-resources"></a><a name="fair-share"></a>Megosztott erőforrások

Ha az erőforrásokat meg kell osztania a felhőben futó összes runbook között, az Azure egy igazságos megosztás nevű koncepciót használ. Igazságos megosztás használata esetén az Azure ideiglenesen eltávolít vagy leállít minden olyan feladatot, amely több mint három órán keresztül futott. A [PowerShell-runbookok](automation-runbook-types.md#powershell-runbooks) és [Python-runbookok](automation-runbook-types.md#python-runbooks) feladata le lesz állítva és nem indul újra, és a feladat állapota Leállítva lesz.

A hosszú ideig futó Azure Automation feladatokhoz javasolt hibrid runbook-feldolgozót használni. A hibrid runbook-dolgozókat nem korlátozza az igazságos megosztás, és nincs korlátozva, hogy egy runbook mennyi ideig futhat. A többi [feladatkorlát](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) az Azure-védőfalra és a hibrid runbook-dolgozókra is vonatkozik. Bár a hibrid runbook-dolgozókat nem korlátozza a háromórás igazságos megosztási korlát, érdemes olyan runbookokat fejleszteni, amelyek a nem várt helyi infrastruktúra-problémákból való újraindításokat támogató dolgozókon futnak.

Egy másik lehetőség a runbook gyermek runbookokkal való optimalizálása. Előfordulhat például, hogy a runbook több erőforráson is végigfut ugyanazon a függvényen, például egy adatbázis-művelettel több adatbázison. A függvényt áthelyezheti egy [gyermekrunbookba,](automation-child-runbooks.md) és a Runbook a [Start-AzAutomationRunbook használatával hívhatja meg.](/powershell/module/az.automation/start-azautomationrunbook) A gyermek runbookok párhuzamosan futnak különálló folyamatokban.

A gyermek runbookok használata csökkenti a szülő runbook befejezéséhez szükséges teljes időt. A runbook a [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) parancsmag használatával ellenőrizheti egy gyermek runbook feladatállapotát, ha a gyermek befejeződése után is további műveleteket végez.

## <a name="next-steps"></a>Következő lépések

* A PowerShell-runbookok első lépésekhez [lásd: Oktatóanyag: PowerShell-runbook létrehozása.](learn/automation-tutorial-runbook-textual-powershell.md)
* Runbookok kezeléséhez lásd: [Runbookok kezelése](manage-runbooks.md)a Azure Automation.
* A PowerShell részleteiért lásd: [PowerShell Docs](/powershell/scripting/overview).
* A PowerShell-parancsmagok referenciáját lásd: [Az.Automation](/powershell/module/az.automation#automation).
