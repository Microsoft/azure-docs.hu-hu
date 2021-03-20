---
title: A szolgáltatások központi telepítésével kapcsolatos problémák elhárítása Azure Automation
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a Azure Automation szolgáltatások telepítésekor felmerülő problémákat.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.openlocfilehash: 43bc367be9ad9bb32f33f94df774acb3e808182a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100651119"
---
# <a name="troubleshoot-feature-deployment-issues"></a>Szolgáltatás üzembehelyezési problémáinak elhárítása

A Azure Automation Update Management funkció, illetve a virtuális gépek Change Tracking és leltár szolgáltatásának telepítésekor hibaüzenetek jelenhetnek meg. Ez a cikk az esetlegesen előforduló hibákat és azok megoldását ismerteti.

## <a name="known-issues"></a>Ismert problémák

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Forgatókönyv: a regisztrált csomópontok átnevezéséhez regisztrációt kell törölni, vagy újra regisztrálni kell

#### <a name="issue"></a>Probléma

Egy csomópont regisztrálva van Azure Automationban, és az operációs rendszer számítógépneve módosul. A csomópont jelentései továbbra is az eredeti névvel jelennek meg.

#### <a name="cause"></a>Ok

A regisztrált csomópontok átnevezése nem frissíti a csomópont nevét a Azure Automationban.

#### <a name="resolution"></a>Feloldás

Törölje a csomópont regisztrációját Azure Automation állapot-konfigurációból, majd regisztrálja újra. A szolgáltatásban közzétett jelentések már nem lesznek elérhetők.

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>Forgatókönyv: a tanúsítványoknak a HTTPS-proxyn keresztül történő ismételt aláírása nem támogatott

#### <a name="issue"></a>Probléma

Ha olyan proxyn keresztül csatlakozik, amely megszakítja a HTTPS-forgalmat, majd újratitkosítja a forgalmat egy új tanúsítvánnyal, a szolgáltatás nem teszi lehetővé a kapcsolat használatát.

#### <a name="cause"></a>Ok

Azure Automation nem támogatja a forgalom titkosításához használt tanúsítványok újraaláírását.

#### <a name="resolution"></a>Feloldás

Ehhez a hibához jelenleg nincs Áthidaló megoldás.

## <a name="general-errors"></a>Általános hibák

### <a name="scenario-feature-deployment-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Forgatókönyv: a szolgáltatások központi telepítése a "a megoldás nem engedélyezhető" üzenettel meghiúsul.

#### <a name="issue"></a>Probléma

A következő üzenetek egyike jelenik meg, amikor megpróbál engedélyezni egy szolgáltatást egy virtuális gépen:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Ok

Ezt a hibát a virtuális gép vagy munkaterület, illetve a felhasználó számára helytelen vagy hiányzó engedélyek okozzák.

#### <a name="resolution"></a>Feloldás

Győződjön meg arról, hogy a [szolgáltatás központi telepítési engedélyei](../automation-role-based-access-control.md#feature-setup-permissions)megfelelőek, majd próbálja meg újra telepíteni a szolgáltatást. Ha a hibaüzenet jelenik meg `The solution cannot be enabled on this VM because the permission to read the workspace is missing` , tekintse meg a következő [hibaelhárítási információkat](update-management.md#failed-to-enable-error).

### <a name="scenario-feature-deployment-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Forgatókönyv: a szolgáltatások központi telepítése nem sikerül, a "nem sikerült konfigurálni az Automation-fiókot a diagnosztikai naplózáshoz" üzenet jelenik meg.

#### <a name="issue"></a>Probléma

A következő üzenet jelenik meg, amikor megpróbál engedélyezni egy szolgáltatást egy virtuális gépen:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Ok

Ez a hiba akkor okozható, ha a díjszabási csomag nem egyezik az előfizetés számlázási modelljével. További információ: [a használat figyelése és a becsült költségek Azure monitorban](../../azure-monitor//usage-estimated-costs.md).

#### <a name="resolution"></a>Feloldás

Hozza létre manuálisan a Log Analytics munkaterületet, és ismételje meg a szolgáltatás központi telepítési folyamatát a létrehozott munkaterület kiválasztásához.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Forgatókönyv: ComputerGroupQueryFormatError

#### <a name="issue"></a>Probléma

Ez a hibakód azt jelenti, hogy a mentett keresés számítógépcsoport-lekérdezés a szolgáltatás megcélzásához nem megfelelően van formázva. 

#### <a name="cause"></a>Ok

Lehetséges, hogy módosította a lekérdezést, vagy megváltoztatta a rendszeren.

#### <a name="resolution"></a>Feloldás

Törölheti a szolgáltatás lekérdezését, majd ismét engedélyezheti a szolgáltatást, amely újból létrehozza a lekérdezést. A lekérdezés a munkaterület **mentett keresések** területén található. A lekérdezés neve **MicrosoftDefaultComputerGroup**, a lekérdezés kategóriája pedig a társított szolgáltatás neve. Ha több funkció is engedélyezve van, a **MicrosoftDefaultComputerGroup** -lekérdezés többször is megjeleníti a **mentett keresések** alatt.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Forgatókönyv: PolicyViolation

#### <a name="issue"></a>Probléma

Ez a hibakód azt jelzi, hogy a központi telepítés egy vagy több házirend megsértése miatt meghiúsult.

#### <a name="cause"></a>Ok 

Egy házirend megakadályozza a művelet befejezését.

#### <a name="resolution"></a>Feloldás

A szolgáltatás sikeres üzembe helyezéséhez meg kell fontolnia a jelzett házirend módosítását. Mivel számos különböző típusú szabályzat definiálható, a szükséges módosítások a megsértett házirendtől függenek. Ha például egy olyan erőforráscsoport-házirend van meghatározva, amely megtagadja az egyes foglalt erőforrások tartalmának módosítására vonatkozó engedélyt, a következő javítások közül választhat:

* Távolítsa el a szabályzatot teljes egészében.
* Próbálja meg engedélyezni a szolgáltatást egy másik erőforráscsoporthoz.
* A szabályzatot egy adott erőforrásra, például egy Automation-fiókra kell átcélozni.
* Módosítsa azon erőforrások készletét, amelyeknek a szabályzata el van tiltva.

Tekintse át az értesítéseket a Azure Portal jobb felső sarkában, vagy nyissa meg az Automation-fiókot tartalmazó erőforráscsoportot, és a **Beállítások** területen válassza a **központi telepítések** lehetőséget a sikertelen telepítés megtekintéséhez. További információ a Azure Policyről: [Azure Policy áttekintése](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Forgatókönyv: a munkaterület leválasztását megkísérelő hibák

#### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg, amikor megpróbál leválasztani egy munkaterületet:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha továbbra is aktív funkciók vannak a Log Analytics munkaterületen, amely az Automation-fióktól függ, és Log Analytics a munkaterület csatolva van.

### <a name="resolution"></a>Feloldás

Ha ezeket használja, távolítsa el a munkaterület következő funkcióinak erőforrásait:

* Frissítéskezelés
* Change Tracking és Inventory
* Virtuális gépek indítása és leállítása munkaidőn kívül

A szolgáltatás erőforrásainak eltávolítása után megszüntetheti a munkaterület összekapcsolását. Fontos, hogy a munkaterületről és az Automation-fiókból törölje a meglévő összetevőkből származó összes összetevőt:

* Update Management esetén távolítsa el az Automation-fiókból a **frissítési központi telepítéseket (ütemterveket)** .
* Start/Stop VMS During off-hours esetén távolítsa el az Automation-fiókban lévő funkció-összetevők zárolásait a **Beállítások**  >  **zárolása** alatt. További információ: [a szolgáltatás eltávolítása](../automation-solution-vm-management-remove.md).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>A Windows-bővítményekkel kapcsolatos hibák Log Analytics

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

A Windows-bővítmény Log Analytics ügynökének telepítése különböző okok miatt sikertelen lehet. A következő szakasz ismerteti a szolgáltatások központi telepítésével kapcsolatos problémákat, amelyek a Windows-ügynök Log Analytics ügynökének telepítése során hibákhoz vezethetnek.

>[!NOTE]
>A Windows rendszerhez készült Log Analytics ügynök a Microsoft monitoring Agent (MMA) számára jelenleg Azure Automation használt név.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Forgatókönyv: kivétel történt egy WebClient-kérelem során

A virtuális gépen futó Windows-bővítmény Log Analytics nem tud kommunikálni a külső erőforrásokkal, és a telepítés meghiúsul.

#### <a name="issue"></a>Probléma

Az alábbi példák a visszaadott hibaüzenetekre mutatnak:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Ok

A hiba lehetséges okai a következők:

* A virtuális gépen konfigurált proxyk csak bizonyos portokat engedélyeznek.
* A tűzfalbeállítások letiltotta a szükséges portokhoz és címekhez való hozzáférést.

#### <a name="resolution"></a>Feloldás

Győződjön meg arról, hogy a megfelelő portok és címek nyitva vannak a kommunikációhoz. A portok és címek listáját a [hálózat megtervezése](../automation-hybrid-runbook-worker.md#network-planning)című témakörben tekintheti meg.

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>Forgatókönyv: az átmeneti környezettel kapcsolatos problémák miatt nem sikerült a telepítés

Nem sikerült telepíteni a Windows rendszerhez készült Log Analytics bővítményt a telepítés során, mert egy másik telepítés vagy művelet blokkolja a telepítést.

#### <a name="issue"></a>Probléma

Az alábbi példák az esetleg visszaadott hibaüzenetekre mutatnak:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Ok

A hiba lehetséges okai a következők:

* Egy másik telepítés folyamatban van.
* A rendszer újraindítást kezdeményez a sablon üzembe helyezésekor.

#### <a name="resolution"></a>Feloldás

Ez a hiba átmeneti jellegű. Próbálja megismételni a telepítést a bővítmény telepítéséhez.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Forgatókönyv: telepítési időtúllépés

A Windows-bővítményhez tartozó Log Analytics telepítése időtúllépés miatt nem fejeződött be.

#### <a name="issue"></a>Probléma

A következő példa egy hibaüzenetet jelenít meg:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Ok

Ez a típusú hiba azért fordul elő, mert a virtuális gép a telepítés során nagy terhelés alatt áll.

### <a name="resolution"></a>Feloldás

Ha a virtuális gép alacsonyabb terhelés alatt van, próbálja meg telepíteni a Windows-bővítmény Log Analytics-ügynökét.

## <a name="next-steps"></a>Következő lépések

Ha itt nem találja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* A szolgáltatással való együttműködéshez [@AzureSupport](https://twitter.com/azuresupport) a hivatalos Microsoft Azure fiók a felhasználói élmény javítása érdekében. Az Azure-támogatás a válaszokat, támogatást és szakértőket az Azure-Közösséggel köti össze.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése** lehetőséget.