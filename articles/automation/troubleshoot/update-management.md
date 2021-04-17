---
title: A Azure Automation Update Management elhárítása
description: Ez a cikk bemutatja, hogyan háríthatja el és háríthatja el a Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 04/16/2021
ms.topic: troubleshooting
ms.openlocfilehash: f23632ba6a6b83f92b2bfc90beb4c1a8613c090a
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587363"
---
# <a name="troubleshoot-update-management-issues"></a>Az Update Management hibáinak elhárítása

Ez a cikk olyan problémákat tárgyal, amelyek akkor előfordulhatnak, amikor a Update Management üzembe helyezésekor a gépeken. A hibrid runbook-feldolgozó ügynökének ügynök-hibaelhárítója határozza meg a mögöttes problémát. A hibaelhárítóval kapcsolatos további információkért lásd: [A Windows Update Agent](update-agent-issues.md) hibáinak elhárítása és Linux frissítési [ügynökkel kapcsolatos problémák elhárítása.](update-agent-issues-linux.md) A funkciók üzembe helyezésével kapcsolatos egyéb problémákról [lásd: A szolgáltatástelepítéssel kapcsolatos problémák elhárítása.](onboarding.md)

>[!NOTE]
>Ha problémákba merül fel a Update Management windowsos gépen való telepítése során, nyissa meg a Windows Eseménynapló-t, és ellenőrizze az **Operations Manager** eseménynaplóját a helyi gépen az Alkalmazás- és szolgáltatásnaplók alatt.  Keresse meg a 4502-es azonosítójú eseményeket és az adatokat tartalmazó `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` eseményadatokat.

## <a name="scenario-linux-updates-shown-as-pending-and-those-installed-vary"></a><a name="updates-linux-installed-different"></a>Forgatókönyv: A függőben lévőként és telepítettként megjelenő Linux-frissítések eltérőek lehetnek

### <a name="issue"></a>Probléma

Linux rendszerű gépe számára a Update Management biztonsági és egyéb besorolás alatt elérhető **konkrét** frissítéseket **jeleníti meg.** Ha azonban egy frissítés ütemezése fut a gépen, például  ha csak a Biztonsági besorolásnak megfelelő frissítéseket telepít, a telepített frissítések eltérnek a korábban bemutatott frissítésektől, vagy a besorolásnak megfelelő részkészlettől.

### <a name="cause"></a>Ok

Amikor a Linux-géphez függőben lévő operációsrendszer-frissítések értékelése megnyílik, a linuxos disztribúció szállítója által biztosított Open [Vulnerability and Assessment Language](https://oval.mitre.org/) (OVAL) fájlokat a Update Management használja a besoroláshoz. A Linux-frissítések biztonsági vagy  egyéb ként való kategorizálása az OVAL-fájlok alapján történik, amelyek a biztonsági problémák vagy biztonsági rések frissítéseit ják meg. A frissítés ütemezése azonban a linuxos gépen fut a megfelelő csomagkezelővel, például az YUM, az APT vagy a ZYPPER használatával a telepítésükhöz. Előfordulhat, hogy a Linux-disztribúció csomagkezelője más mechanizmussal sorolja be a frissítéseket, ahol az eredmények eltérhetnek az OVAL-fájlokból származó Update Management.

### <a name="resolution"></a>Feloldás

Manuálisan ellenőrizheti a Linux rendszerű gépet, a vonatkozó frissítéseket és azok besorolását a disztribúció csomagkezelője szerint. A következő parancsok futtatásával megértheti, hogy a csomagkezelő mely frissítéseket sorolja be Biztonságként. 

A YUM esetén a következő parancs a Red Hat által  biztonságként kategorizált frissítések nem nulla listáját adja vissza. Vegye figyelembe, hogy CentOS esetén mindig üres listát ad vissza, és nem kerül sor biztonsági besorolásra.

```bash
sudo yum -q --security check-update
```

A ZYPPER esetén a következő parancs a SUSE által  biztonságként kategorizált frissítések nem nulla listáját adja vissza.

```bash
sudo LANG=en_US.UTF8 zypper --non-interactive patch --category security --dry-run
```

Az APT-hez a következő parancs a Canonical  által a disztribúciókhoz a Canonical által biztonságként kategorizált frissítések nem nulla Ubuntu Linux ad vissza.

```bash
sudo grep security /etc/apt/sources.list > /tmp/oms-update-security.list LANG=en_US.UTF8 sudo apt-get -s dist-upgrade -oDir::Etc::Sourcelist=/tmp/oms-update-security.list
```

Ebből a listából ezután az paranccsal lekérte az összes `grep ^Inst` függőben lévő biztonsági frissítést.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>Forgatókönyv: A "Nem sikerült engedélyezni a frissítési megoldást" hibaüzenet jelenik meg

### <a name="issue"></a>Probléma

Amikor megpróbálja engedélyezni a Update Management Automation-fiókjában, a következő hibaüzenet jelenik meg:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* Előfordulhat, hogy a Log Analytics-ügynök hálózati tűzfalkövetelményei nem megfelelően vannak konfigurálva. Ez a helyzet azt okozhatja, hogy az ügynök sikertelen lesz a DNS URL-címek feloldásakor.

* Update Management célcsoport-beállítás helytelenül van konfigurálva, és a gép nem a várt módon kap frissítéseket.

* Azt is észreveheti, hogy a gép megfelelőségi `Non-compliant` állapottal **jelenik meg.** Az ügynök azonban **a következőként Asztali elemzés** az `Disconnected` ügynököt: .

### <a name="resolution"></a>Feloldás

* Futtassa a [hibaelhárítót Windows vagy](update-agent-issues.md#troubleshoot-offline) [Linux rendszeren](update-agent-issues-linux.md#troubleshoot-offline)az operációs rendszertől függően.

* A Hálózati [konfiguráció részen](../automation-hybrid-runbook-worker.md#network-planning) megtudhatja, hogy mely címek és portok számára kell Update Management számára.  

* Ellenőrizze, hogy vannak-e hatókör-konfigurációs problémák. [A hatókör-konfiguráció](../update-management/scope-configuration.md) határozza meg, hogy mely gépek vannak konfigurálva a Update Management. Ha a gép a munkaterületen jelenik meg, de a Update Management nem, a hatókör-konfigurációt úgy kell beállítania, hogy a gépeket célozza meg. A hatókör-konfigurációval kapcsolatos további információkért lásd: [Gépek engedélyezése a munkaterületen.](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace)

* Távolítsa el a feldolgozó konfigurációját a Hibrid [runbook-feldolgozó](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) eltávolítása helyszíni Windows-számítógépről vagy A hibrid runbook-feldolgozó eltávolítása helyszíni [Linux-számítógépről lépéseit követve.](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker)

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Forgatókönyv: A frissítésben hiányzóként megjelölt, a Update Management

### <a name="issue"></a>Probléma

Az Automation-fiók régi frissítései hiányzóként jelennek meg, annak ellenére, hogy azok fel vannak véve. A felesleges frissítéseket nem kell telepítenie, mert egy későbbi frissítés is elérhetővé válik, amely ugyanezen biztonsági rést javítja ki. Update Management figyelmen kívül hagyja a feltevéses frissítést, és nem alkalmazható a feltevéses frissítés mellett. A kapcsolódó problémákra vonatkozó információkért lásd: A frissítés [fel van frissítve.](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)

### <a name="cause"></a>Ok

A rendszer nem utasítja el a Windows Server Update Services (WSUS) frissítéseit, így azok nem alkalmazhatónak minősülnek.

### <a name="resolution"></a>Feloldás

Ha egy olyan frissítés, amely 100%-os nem alkalmazhatóvá válik, módosítsa a frissítés jóváhagyási állapotát a következőre a `Declined` WSUS-ben: . Az összes frissítés jóváhagyási állapotának módosítása:

1. Az Automation-fiókban válassza **az** Update Management lehetőséget a gép állapotának megtekintéséhez. Lásd: [Frissítési értékelések megtekintése.](../update-management/view-update-assessments.md)

2. Ellenőrizze, hogy a 100%-os nem alkalmazható-e a frissítés.

3. A WSUS-kiszolgálón, amely számára a gépek jelentést készítnek, [elutasíthatja a frissítést.](/windows-server/administration/windows-server-update-services/manage/updates-operations#declining-updates)

4. Válassza **a Számítógépek** lehetőséget, majd a Megfelelőség **oszlopban** kényszerítsen ki egy újra ellenőrzésére a megfelelőséget. Lásd: [Virtuális gépek frissítésének kezelése.](../update-management/manage-updates-for-vm.md)

5. Ismételje meg a fenti lépéseket az egyéb, feltehető frissítéseknél is.

6. A Windows Server Update Services (WSUS) esetében az összes felesleges frissítést megtisztítva frissítse az infrastruktúrát a [WSUS-kiszolgáló tisztítási varázslójával.](/windows-server/administration/windows-server-update-services/manage/the-server-cleanup-wizard)

7. Ismételje meg ezt az eljárást rendszeresen a megjelenítési probléma kijavítása és a frissítéskezeléshez szükséges lemezterület minimalizálása érdekében.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Forgatókönyv: A gépek nem omjnak meg a portálon a Update Management

### <a name="issue"></a>Probléma

A gépeken a következő tünetek jelentkeznek:

* A gép `Not configured` a virtuális Update Management nézetében jelenik meg.

* A gépek hiányoznak a Update Management fiókjának Azure Automation nézetből.

* Vannak olyan gépei, amelyek a Compliance `Not assessed` (Megfelelőség) **alatt stb. ként mutatjak a következőt:**. A hibrid runbook-feldolgozó szívverési adatait Azure Monitor naplókban látja, de a Update Management.

### <a name="cause"></a>Ok

Ezt a problémát helyi konfigurációs problémák vagy nem megfelelően konfigurált hatókör-konfiguráció okozhatja. A lehetséges konkrét okok a következőek:

* Előfordulhat, hogy újra kell regisztrálnia és újra kell telepítenie a hibrid runbook-feldolgozót.

* Előfordulhat, hogy olyan kvótát adott meg a munkaterületen, amely el lett érve, és amely megakadályozza a további adattárolást.

### <a name="resolution"></a>Feloldás

1. Futtassa a [hibaelhárítót Windows vagy](update-agent-issues.md#troubleshoot-offline) [Linux rendszeren](update-agent-issues-linux.md#troubleshoot-offline)az operációs rendszertől függően.

2. Győződjön meg arról, hogy a gép a megfelelő munkaterületnek jelent. Az aspektus ellenőrzéséhez lásd: [Verify agent connectivity to Azure Monitor](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Győződjön meg arról is, hogy ez a munkaterület a Azure Automation van kapcsolva. A megerősítéshez kattintson az Automation-fiókjára, és válassza a **Csatolt munkaterület lehetőséget** a Kapcsolódó erőforrások **alatt.**

3. Győződjön meg arról, hogy a gépek az Automation-fiókhoz csatolt Log Analytics-munkaterületen adatokat mutatjak. Futtassa a következő lekérdezést a Log Analytics-munkaterületen.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

    Ha a gép nem látható a lekérdezési eredmények között, az nemrég nem jelentkezett be. Valószínűleg helyi konfigurációs probléma van, és újra kell [telepítenie az ügynököt.](../../azure-monitor/vm/quick-collect-windows-computer.md#install-the-agent-for-windows)

    Ha a gép szerepel a lekérdezési eredmények között, ellenőrizze a **Solutions (Megoldások)** tulajdonság alatt, hogy a **frissítések szerepelnek-e** a listán. Ez ellenőrzi, hogy regisztrálva van-e a Update Management. Ha nem, ellenőrizze, hogy vannak-e hatókör-konfigurációs problémák. A [hatókör-konfiguráció](../update-management/scope-configuration.md) határozza meg, hogy mely gépek vannak konfigurálva a Update Management. A célgép hatókör-konfigurációjának konfigurálásért lásd: [Gépek engedélyezése a munkaterületen.](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace)

4. Futtassa ezt a lekérdezést a munkaterületen.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

   Ha eredményt kap, elérte a munkaterületen meghatározott kvótát, ami miatt nem menthetők `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` az adatok. A munkaterületen a  Használat és becsült költségek alatt váltson az adatmennyiség-kezelésre, és módosítsa vagy távolítsa el a kvótát.

5. Ha a probléma továbbra is megoldatlan, [kövesse a Hibrid runbook-feldolgozó](../automation-windows-hrw-install.md) üzembe helyezése a hibrid feldolgozó Windows rendszeren történő újratelepítéséhez témakörben található lépéseket. Linux rendszeren kövesse a Linux rendszerű hibrid [runbook-feldolgozó üzembe helyezése lépéseit.](../automation-linux-hrw-install.md)

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Forgatókönyv: Nem sikerült regisztrálni az Automation erőforrás-szolgáltatót az előfizetések számára

### <a name="issue"></a>Probléma

Amikor szolgáltatástelepítésekkel dolgozik az Automation-fiókjában, a következő hiba történik:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Ok

Az Automation erőforrás-szolgáltató nincs regisztrálva az előfizetésben.

### <a name="resolution"></a>Feloldás

Az Automation erőforrás-szolgáltató regisztrálásához kövesse az alábbi lépéseket a Azure Portal.

1. A portál alján található Azure-szolgáltatások listájában válassza a **Minden** szolgáltatás lehetőséget, majd az **Előfizetések** lehetőséget az Általános szolgáltatáscsoportban.

2. Válassza ki előfizetését.

3. A **Beállítások alatt** válassza az **Erőforrás-szolgáltatók lehetőséget.**

4. Az erőforrás-szolgáltatók listájában ellenőrizze, hogy a Microsoft.Automation erőforrás-szolgáltató regisztrálva van-e.

5. Ha nem szerepel a listán, regisztrálja a Microsoft.Automation szolgáltatót az erőforrás-szolgáltató regisztrációjával kapcsolatos hibák elhárításával [kapcsolatos cikk lépéseit követve.](../../azure-resource-manager/templates/error-register-resource-provider.md)

## <a name="scenario-scheduled-update-did-not-patch-some-machines"></a><a name="scheduled-update-missed-machines"></a>Forgatókönyv: Az ütemezett frissítés nem javít egyes gépeket

### <a name="issue"></a>Probléma

A frissítési előzetes verzióban szereplő gépek nem mind jelennek meg az ütemezett futtatás során javított gépek listájában, vagy a dinamikus csoport kiválasztott hatóköréhez tartozó virtuális gépek nem jelennek meg a portál frissítési előnézeti listájában.

A frissítési előzetes verzió listája a kiválasztott hatókörökre vonatkozó lekérdezés által [lekért Azure Resource Graph](../../governance/resource-graph/overview.md) összes gépből áll. A hatókörök olyan gépekre vannak szűrve, amelyeken telepítve van egy hibrid runbook-feldolgozó rendszer, és amelyekhez hozzáférési engedélyekkel rendelkezik.

### <a name="cause"></a>Ok

Ennek a problémának a következő okai lehetnek:

* A dinamikus lekérdezés hatókörében meghatározott előfizetések nincsenek konfigurálva a regisztrált Automation erőforrás-szolgáltatóhoz.

* A gépek nem voltak elérhetők, vagy nem voltak megfelelő címkék az ütemezés végrehajtásakor.

* Nem rendelkezik a megfelelő hozzáféréssel a kiválasztott hatókörökben.

* A Azure Resource Graph lekérdezés nem a várt gépeket olvassa be.

* A rendszer hibrid runbook-feldolgozója nincs telepítve a gépeken.

### <a name="resolution"></a>Feloldás

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>A regisztrált Automation erőforrás-szolgáltatóhoz nem konfigurált előfizetések

Ha az előfizetés nincs konfigurálva az Automation erőforrás-szolgáltatóhoz, nem tud adatokat lekérdezni vagy lekérni az előfizetésben található gépekről. Az előfizetés regisztrációját az alábbi lépésekkel ellenőrizheti.

1. A [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)meg az Azure-szolgáltatások listáját.

2. Válassza **a Minden szolgáltatás,** majd az **Előfizetések** lehetőséget az Általános szolgáltatáscsoportban.

3. Keresse meg az üzemelő példány hatókörében meghatározott előfizetést.

4. A **Beállítások alatt** válassza az **Erőforrás-szolgáltatók lehetőséget.**

5. Ellenőrizze, hogy a Microsoft.Automation erőforrás-szolgáltató regisztrálva van-e.

6. Ha nem szerepel a listán, regisztrálja a Microsoft.Automation szolgáltatót a következő cikk lépéseit követve: [Erőforrás-szolgáltató regisztrációjával kapcsolatos hibák elhárítása.](../../azure-resource-manager/templates/error-register-resource-provider.md)

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>A gépek nem érhetők el vagy nem címkézték megfelelően az ütemezés végrehajtásakor

Ha az előfizetése az Automation erőforrás-szolgáltatóhoz van konfigurálva, de [](../update-management/configure-groups.md) a frissítési ütemezést a megadott dinamikus csoportokkal együtt futtatva kihagyott néhány gépet, használja a következő eljárást.

1. A Azure Portal nyissa meg az Automation-fiókot, és válassza a **Update Management.**

2. Tekintse [Update Management a frissítési](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) előzményeket, és állapítsa meg a frissítéstelepítés futtatásának pontos idejét.

3. Az olyan gépeknél, amelyekről úgy gyanítja, hogy Update Management, a Azure Resource Graph (ARG) segítségével keresse meg a gép [módosításait.](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details)

4. Jelentős időtartamon, például egy napon, a frissítéstelepítés futtatása előtt keressen módosításokat.

5. Ellenőrizze a keresési eredmények között a gépek rendszerszintű változásait, például a törlés vagy a frissítés változásait ebben az időszakban. Ezek a módosítások módosíthatják a gépek állapotát vagy címkéit, így a frissítések telepítésekor a gépek nem választhatók ki a gépek listájában.

6. Módosítsa a gép és az erőforrás beállításait a gép állapotával vagy címkével kapcsolatos problémáinak kijavítása érdekében.

7. Futtassa újra a frissítési ütemezést, és győződjön meg arról, hogy a megadott dinamikus csoportokkal való üzembe helyezés az összes gépet tartalmazza.

#### <a name="incorrect-access-on-selected-scopes"></a>Helytelen hozzáférés a kiválasztott hatókörökben

A Azure Portal csak azokat a gépeket jeleníti meg, amelyekhez írási hozzáféréssel rendelkezik egy adott hatókörben. Ha nem rendelkezik a megfelelő hozzáféréssel egy hatókörben, tekintse meg az [oktatóanyagot: Oktatóanyag:](../../role-based-access-control/quickstart-assign-role-user-portal.md)Azure-erőforrásokhoz való hozzáférés megadása egy felhasználó számára a Azure Portal.

#### <a name="resource-graph-query-doesnt-return-expected-machines"></a>Resource Graph lekérdezés nem a várt gépeket adja vissza

Az alábbi lépéseket követve kideríti, hogy a lekérdezések megfelelően működnek-e.

1. Futta Azure Resource Graph az alább látható módon formázott lekérdezést a Resource Graph Explorer Azure Portal. Ha most ismerkedik a Azure Resource Graph, [](../../governance/resource-graph/first-query-portal.md) ebből a rövid útmutatóból megtudhatja, hogyan munkához Resource Graph Explorerben. Ez a lekérdezés a dinamikus csoport létrehozásakor kiválasztott szűrőket utánozza a Update Management. Lásd: [Dinamikus csoportok használata Update Management.](../update-management/configure-groups.md)

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Alább bemutatunk egy példát:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```

2. Ellenőrizze, hogy a keresett gépek szerepelnek-e a lekérdezési eredmények között.

3. Ha a gépek nem szerepelnek a listában, valószínűleg probléma van a dinamikus csoportban kiválasztott szűrővel. Szükség szerint módosítsa a csoport konfigurációját.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>A hibrid runbook-feldolgozó nincs telepítve a gépeken

A gépek megjelennek a Azure Resource Graph lekérdezési eredmények között, de továbbra sem jelennek meg a dinamikus csoport előnézetében. Ebben az esetben előfordulhat, hogy a gépek nem lesznek rendszer hibrid runbook-Azure Automation és Update Management futtatni. Annak biztosítása érdekében, hogy a várt gépek hibrid runbook-dolgozókként vannak beállítva:

1. A Azure Portal egy nem megfelelően megjelenő gép Automation-fiókjához.

2. A **Folyamatautomatizálás alatt válassza a** Hibrid **feldolgozói csoportok lehetőséget.**

3. Válassza a **Rendszer hibrid feldolgozói csoportok lapot.**

4. Ellenőrizze, hogy a hibrid feldolgozó jelen van-e az adott gépen.

5. Ha [a](../update-management/overview.md#enable-update-management) gép nincs beállítva rendszer hibrid runbook-feldolgozóként, tekintse át a gép engedélyezésére vonatkozó módszereket a Update Management engedélyezése című cikk Update Management szakaszában. Az engedélyezni szükséges metódus azon a környezeten alapul, amelyben a gép fut.

6. Ismételje meg a fenti lépéseket minden olyan gépnél, amely nem jelenik meg az előzetes verzióban.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Forgatókönyv: Update Management, miközben a virtuális gép továbbra is konfigurálva van

### <a name="issue"></a>Probléma

Az üzembe helyezés megkezdése után 15 perccel a következő üzenet jelenik meg a virtuális gépen:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* Az Automation-fiókkal való kommunikáció le van tiltva.

* A számítógép neve megkettőzve, eltérő forrás-számítógép-névvel. Ez a forgatókönyv akkor fordul elő, ha egy adott számítógépnévvel létrehozott virtuális gép különböző erőforráscsoportokban van létrehozva, és az előfizetés ugyanazon Logistics Agent munkaterületének jelent.

* Előfordulhat, hogy az üzembe helyezett virtuálisgép-rendszerkép olyan klónozott gépről jön, amely nincs előkészítve a rendszer-előkészítéssel (sysprep) a Windowshoz készült Log Analytics-ügynökkel.

### <a name="resolution"></a>Feloldás

A virtuális gép pontos problémájának meghatározásához futtassa a következő lekérdezést az Automation-fiókjához kapcsolt Log Analytics-munkaterületen.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Az Automation-fiókkal való kommunikáció le van tiltva

A [Hálózattervezés részen](../update-management/overview.md#ports) megtudhatja, hogy mely címeknek és portoknak kell Update Management működniük.

#### <a name="duplicate-computer-name"></a>Duplikált számítógépnév

Nevezze át a virtuális gépeket, hogy egyedi neveket biztosítsanak a környezetben.

#### <a name="deployed-image-from-cloned-machine"></a>Klónozott gépről üzembe helyezett rendszerkép

Klónozott lemezkép használata esetén a különböző számítógépneveknek ugyanaz a forrás-számítógépazonosítójuk. Ebben az esetben:

1. A Log Analytics-munkaterületen távolítsa el a virtuális gépet a mentett keresésből, és keresse meg a `MicrosoftDefaultScopeConfig-Updates` hatókör-konfigurációt, ha az megjelenik. A mentett keresések a munkaterület Általános **ás ában** találhatók.

2. Futtassa a következő parancsmagot.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Az `Restart-Service HealthService` állapotszolgáltatás újraindításához futtassa a következőt: . Ez a művelet újra létrehozza a kulcsot, és létrehoz egy új UUID-t.

4. Ha ez a módszer nem működik, először futtassa a sysprep eszközt a lemezképen, majd telepítse a Windowshoz készült Log Analytics-ügynököt.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Forgatókönyv: Összekapcsolt előfizetési hibát kap, amikor egy másik Azure-bérlőben üzemelő gépekhez hoz létre frissítéstelepítést

### <a name="issue"></a>Probléma

Amikor egy másik Azure-bérlőben üzemelő gépekhez próbál frissítéstelepítést létrehozni, a következő hibaüzenet jelenik meg:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha olyan frissítéstelepítést hoz létre, amely azure-beli virtuális gépeket tartalmaz egy másik bérlőben, amely egy frissítéstelepítés része.

### <a name="resolution"></a>Feloldás

Ezeket az elemeket az alábbi megkerülő megoldással ütemezhatja. Ütemezés létrehozásához használhatja a [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) parancsmagot `ForUpdateConfiguration` a paraméterrel. Ezután használja a [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) parancsmagot, és adja át a másik bérlő gépeit a `NonAzureComputer` paraméternek. Az alábbi példa bemutatja, hogyan lehet ezt megtenni:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Forgatókönyv: Megmagyarázhatatlan újraindítások

### <a name="issue"></a>Probléma

Annak ellenére, hogy  az Újraindítás-vezérlést Never **Reboot (Soha** ne indítsa újra) beállításra beállította, a gépek a frissítések telepítése után is újraindulnak.

### <a name="cause"></a>Ok

Windows Update számos beállításkulcs módosítható, amelyek bármelyike módosíthatja az újraindítási viselkedést.

### <a name="resolution"></a>Feloldás

Tekintse át az [](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) Automatikus frissítések konfigurálása alatt felsorolt [](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) beállításkulcsokat az újraindítás kezeléséhez használt beállításjegyzék és beállításkulcsok szerkesztésével, hogy ellenőrizze, hogy a gépek megfelelően vannak-e konfigurálva.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Forgatókönyv: A gépen a "Sikertelen indítás" jelenik meg egy frissítéstelepítésben

### <a name="issue"></a>Probléma

A gép egy állapotot `Failed to start` mutat. A gép részleteinek megtekintésekor a következő hibaüzenet jelenik meg:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok valamelyike miatt jelentkezhet:

* A gép már nem létezik.
* A gép ki van kapcsolva és nem érhető el.
* A gépen hálózati kapcsolati probléma lépett fel, ezért a gépen található hibrid feldolgozó nem érhető el.
* Frissült a Log Analytics-ügynök, amely módosította a forrás számítógép azonosítóját.
* A frissítési futtatás le lett korlátozva, ha túllépi az Automation-fiók 200 egyidejű feladatának korlátját. Minden üzembe helyezés feladatnak számít, és a frissítéstelepítésben található összes gép feladatnak számít. Az Automation-fiókban jelenleg futó összes többi automatizálási feladat vagy frissítéstelepítés beleszámít az egyidejű feladatkorlátba.

### <a name="resolution"></a>Feloldás

Ha lehetséges, használjon [dinamikus csoportokat](../update-management/configure-groups.md) a frissítéstelepítéshez. Emellett a következő lépéseket is végre lehet venni.

1. Ellenőrizze, hogy a gép vagy kiszolgáló megfelel-e a [követelményeknek.](../update-management/overview.md#system-requirements)
2. Ellenőrizze a hibrid runbook-feldolgozóval való kapcsolatot a hibrid runbook-feldolgozó ügynök hibaelhárítójának használatával. A hibaelhárítóval kapcsolatos további információkért lásd: A frissítési [ügynökkel kapcsolatos problémák elhárítása.](update-agent-issues.md)

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Forgatókönyv: A frissítések központi telepítés nélkül vannak telepítve

### <a name="issue"></a>Probléma

Amikor Windows rendszerű gépet regisztrál a Update Management, láthatja a telepítés nélkül telepített frissítéseket.

### <a name="cause"></a>Ok

Windows rendszeren a frissítések automatikusan telepítve lesznek, amint elérhetők. Ez a viselkedés félreértéseket okozhat, ha nem ütemezte a frissítés üzembe helyezését a gépen.

### <a name="resolution"></a>Feloldás

A  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` beállításkulcs alapértelmezett beállítása 4: `auto download and install` .

A Update Management esetében javasoljuk, hogy ezt a kulcsot 3-ra omjon: `auto download but do not auto install` .

További információ: [Automatikus frissítések konfigurálása.](/windows/deployment/update/waas-wu-settings#configure-automatic-updates)

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Forgatókönyv: A gép már regisztrálva van egy másik fiókhoz

### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Ok

A gép már telepítve van egy másik munkaterületre Update Management.

### <a name="resolution"></a>Feloldás

1. Kövesse a Gépek nem follow [up in the portal](#nologs) under Update Management annak ellenőrzéséhez, hogy a gép a megfelelő munkaterületre jelent-e.
2. A hibrid [runbookcsoport](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)törlésével törölje a gép összetevőit, majd próbálkozzon újra.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Forgatókönyv: A gép nem tud kommunikálni a szolgáltatással

### <a name="issue"></a>Probléma

Az alábbi hibaüzenetek egyike jelenik meg:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Ok

Előfordulhat, hogy egy proxy, átjáró vagy tűzfal blokkolja a hálózati kommunikációt.

### <a name="resolution"></a>Feloldás

Tekintse át a hálózatot, és győződjön meg arról, hogy a megfelelő portok és címek engedélyezve vannak. Tekintse meg a [hálózati követelményeket](../automation-hybrid-runbook-worker.md#network-planning) a hálózati és hibrid runbook-Update Management számára szükséges portok és címek listájáért.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Forgatókönyv: Nem hozható létre önaírt tanúsítvány

### <a name="issue"></a>Probléma

Az alábbi hibaüzenetek egyike jelenik meg:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Ok

A hibrid runbook-feldolgozó nem tudott önaírt tanúsítványt létrehozni.

### <a name="resolution"></a>Feloldás

Ellenőrizze, hogy a rendszerfiók olvasási hozzáféréssel rendelkezik-e a **C:\ProgramData\Microsoft\Crypto\RSA mappához,** majd próbálkozzon újra.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Forgatókönyv: Az ütemezett frissítés MaintenanceWindowExceeded hibával meghiúsult

### <a name="issue"></a>Probléma

A frissítések alapértelmezett karbantartási időszaka 120 perc. A karbantartási időszak legfeljebb 6 órára, azaz 360 percre is megnövelheti.

### <a name="resolution"></a>Feloldás

Ha meg kell értenie, hogy miért történt ez a frissítés sikeres indítása [után,](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) ellenőrizze a futtatás során az érintett gép feladatkimenetét. Konkrét hibaüzeneteket találhat a gépeiről, amelyekre rá tud kutatni, és amelyeken lépéseket tud majdni.  

Szerkessze a sikertelen ütemezett frissítéstelepítéseket, és növelje a karbantartási időszakkeretet.

A karbantartási időszakokkal kapcsolatos további információkért lásd: [Frissítések telepítése.](../update-management/deploy-updates.md#schedule-an-update-deployment)

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Forgatókönyv: A gép "Nincs értékelve"ként jelenik meg, és HRESULT-kivételt mutat

### <a name="issue"></a>Probléma

* A gépek a Megfelelőség alatt `Not assessed` **megjelenik,** és alattuk egy kivételüzenet jelenik meg.
* A portálon HRESULT hibakód jelenik meg.

### <a name="cause"></a>Ok

A Frissítési ügynök (Windows Update Ügynök Windows rendszeren; a Linux-disztribúció csomagkezelője) nincs megfelelően konfigurálva. Update Management a gép frissítési ügynöke biztosítja a szükséges frissítéseket, a javítás állapotát és az üzembe helyezett javítások eredményeit. Ezen információk nélkül a Update Management nem tudnak megfelelően jelenteni a szükséges vagy telepített javításokat.

### <a name="resolution"></a>Feloldás

Próbálja meg helyben végrehajtani a frissítéseket a gépen. Ha ez a művelet sikertelen, az általában azt jelenti, hogy a frissítési ügynök konfigurációs hibája van.

Ezt a problémát gyakran hálózati konfigurációval és tűzfallal kapcsolatos problémák okják. A probléma megoldásához használja az alábbi ellenőrzéseket.

* Linux esetén ellenőrizze a megfelelő dokumentációt, és győződjön meg arról, hogy el tudja érni a csomagtár hálózati végpontját.

* Windows rendszeren ellenőrizze az ügynök konfigurációját a Frissítések nem tölthetők le az intranetes végpontról [(WSUS/SCCM) listában felsoroltak szerint.](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)

  * Ha a gépek konfigurálva vannak az Windows Update, győződjön meg arról, hogy el tudja érni a [HTTP-hez/proxyhoz](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)kapcsolódó problémákat ismerteti.
  * Ha a gépek a Windows Server Update Services (WSUS) számára vannak konfigurálva, győződjön meg arról, hogy el tudja érni a [WUServer](/windows/deployment/update/waas-wu-settings)beállításkulcs által konfigurált WSUS-kiszolgálót.

Ha HRESULT jelenik meg, kattintson duplán a pirossal megjelenített kivételre a teljes kivételüzenet megjelenítéséhez. Tekintse át az alábbi táblázatban a lehetséges megoldásokat vagy a javasolt műveleteket.

|Kivétel  |Megoldás vagy művelet  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | A kivétel okának további részleteiért keresse meg a megfelelő hibakódot a [Windows Update hibakód-listájában.](https://support.microsoft.com/help/938205/windows-update-error-code-list)        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Ezek hálózati kapcsolati problémákra utalnak. Ellenőrizze, hogy a gép rendelkezik-e hálózati kapcsolattal Update Management. A szükséges [portok és](../update-management/overview.md#ports) címek listáját a hálózattervezési szakaszban láthatja.        |
|`0x8024001E`| A frissítési művelet nem fejeződött be, mert a szolgáltatás vagy a rendszer leállt.|
|`0x8024002E`| Windows Update szolgáltatás le van tiltva.|
|`0x8024402C`     | Ha WSUS-kiszolgálót használ, győződjön meg arról, hogy a beállításkulcsban és a beállításkulcsban található beállításazonosítók a megfelelő `WUServer` `WUStatusServer`  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` WSUS-kiszolgálót határozzák meg.        |
|`0x80072EE2`|Hálózati kapcsolati probléma vagy probléma lépett fel a konfigurált WSUS-kiszolgálóval való beszélgetésben. Ellenőrizze a WSUS beállításait, és győződjön meg arról, hogy a szolgáltatás elérhető az ügyfélről.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Győződjön meg arról, Windows Update szolgáltatás (wuauserv) fut, és nincs letiltva.        |
|`0x80070005`| A hozzáférés megtagadva hibát az alábbiak bármelyike okozhatja:<br> Fertőzött számítógép<br> Windows Update beállítások nem megfelelően vannak konfigurálva<br> Fájlengedély-hiba a %WinDir%\SoftwareDistribution mappával<br> Nincs elegendő lemezterület a rendszermeghajtón (C:).
|Bármely egyéb általános kivétel     | Futtatassa az interneten a lehetséges megoldásokat, és működjön együtt a helyi it-támogatási szolgálattal.         |

A **%Windir%\Windowsupdate.log** fájl áttekintése is segíthet a lehetséges okok meghatározásában. A napló olvasásának további információiért lásd: [How to read the Windowsupdate.log file (A Windowsupdate.log fájl olvasása).](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file)

Emellett letöltheti és futtathatja a Windows Update [hibaelhárítót,](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) és ellenőrizheti, hogy vannak-e Windows Update hibák a gépen.

> [!NOTE]
> A [Windows Update hibaelhárító](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) dokumentációja azt jelzi, hogy Windows-ügyfeleken való használatra szolgál, de Windows Serveren is működik.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Forgatókönyv: A frissítés futtatása Sikertelen állapotot ad vissza (Linux)

### <a name="issue"></a>Probléma

Elindul a frissítés futtatása, de hibákba ütközik a futtatás során.

### <a name="cause"></a>Ok

Lehetséges okok:

* A csomagkezelő nem jó.
* A frissítési ügynök (WuA for Windows, linuxos disztribúció-specifikus csomagkezelő) helytelenül van konfigurálva.
* Bizonyos csomagok zavarják a felhőalapú javításokat.
* A gép nem érhető el.
* A frissítések nem feloldott függőségeket tartalmaztak.

### <a name="resolution"></a>Feloldás

Ha a sikeres indításakor hiba lép fel egy frissítés futtatása [során,](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) ellenőrizze a futtatás során az érintett gép feladatkimenetét. Konkrét hibaüzeneteket találhat a gépeiről, amelyekre rá tud kutatni, és amelyeken lépéseket lehet majd találni. Update Management frissítés sikeres telepítése esetén a csomagkezelő megfelelő állapotúnak kell lennie.

Ha bizonyos javítások, csomagok vagy frissítések azonnal láthatók a feladat [](../update-management/deploy-updates.md#schedule-an-update-deployment) meghiúsulása előtt, megpróbálhatja kizárni ezeket az elemeket a következő frissítéstelepítésből. A naplófájlok naplóinformációit Windows Update naplófájlok [Windows Update gyűjti.](/windows/deployment/update/windows-update-logs)

Ha nem tudja megoldani a javítási problémát, készítse el a **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** fájlt, és őrizze meg hibaelhárítási célokra a következő frissítés üzembe helyezése előtt.

## <a name="patches-arent-installed"></a>A javítások nincsenek telepítve

### <a name="machines-dont-install-updates"></a>A gépek nem telepítenek frissítéseket

Próbálja meg közvetlenül a gépen lefuttatni a frissítéseket. Ha a gép nem tudja alkalmazni a frissítéseket, tekintse meg a lehetséges hibák listáját [a hibaelhárítási útmutatóban.](#hresult)

Ha a frissítések helyileg futnak, próbálja meg eltávolítani és újratelepíteni az ügynököt a gépen a Virtuális gép eltávolítása a virtuális gépről a [Update Management.](../update-management/remove-vms.md)

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Tudom, hogy a frissítések elérhetők, de nem érhetők el a gépeimen

Ez gyakran előfordul, ha a gépek úgy vannak konfigurálva, hogy a WSUS vagy a Microsoft Endpoint Configuration Manager frissítéseket szerezzék be, de a WSUS és Konfigurációkezelő nem hagytak jóvá a frissítéseket.

Annak ellenőrzéséhez, hogy a gépek konfigurálva vannak-e a WSUS és az SCCM számára, a beállításkulcsnak a beállításkulcsra való kereszthivatkozásával ellenőrizheti a cikk `UseWUServer` [Configuring Automatic Updates by Editing the Registry](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)(Automatikus frissítések konfigurálása a beállításjegyzék szerkesztésével) című szakaszában.

Ha a WSUS nem hagyja jóvá a frissítéseket, nincsenek telepítve. A Log Analyticsben a következő lekérdezés futtatásával ellenőrizheti a nem jóváhagyott frissítéseket.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>A frissítések telepítettként jelennek meg, de nem találom őket a számítógépen

A frissítéseket gyakran felülírják más frissítések. További információkért lásd a hibaelhárítási [útmutató](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) frissítéssel Windows Update cikkét.

### <a name="installing-updates-by-classification-on-linux"></a>Frissítések telepítése besorolás szerint Linuxon

Ha Linuxon dolgozik, a frissítések besorolás szerinti („kritikus és biztonsági frissítések”) üzembe helyezésekor fontos kikötéseket kell figyelembe venni, különösen CentOS használata esetén. Ezeket a korlátozásokat a következő [áttekintő Update Management dokumentálja:](../update-management/overview.md#linux).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 folyamatosan hiányzik

A KB2267602 a [Windows Defender definíciófrissítése](https://www.microsoft.com/wdsi/definitions). Naponta frissül.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért próbálkozzon az alábbi csatornák egyikével.

* Azure-szakértőktől kaphat választ az [Azure fórumain.](https://azure.microsoft.com/support/forums/)
* Csatlakozzon [@AzureSupport](https://twitter.com/azuresupport) a -hez, Microsoft Azure a felhasználói élmény javítása érdekében.
* Incidens Azure-támogatás be. A webhely Azure-támogatás [válassza](https://azure.microsoft.com/support/options/) a **Támogatás et.**
