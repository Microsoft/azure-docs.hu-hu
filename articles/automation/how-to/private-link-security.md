---
title: A Azure Private Link segítségével biztonságosan csatlakoztathat hálózatokat a Azure Automation
description: A Azure Private Link segítségével biztonságosan csatlakoztathat hálózatokat a Azure Automation
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/11/2020
ms.subservice: ''
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 28f4c314b65a27c71c7620ff5941463b1ea68b55
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831455"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>A Azure Private Link segítségével biztonságosan csatlakoztathat hálózatokat a Azure Automation

Az Azure privát végpont egy hálózati adapter, amely privát és biztonságos módon csatlakoztatja Önt egy Azure privát kapcsolat által működtetett szolgáltatáshoz. A privát végpont a virtuális hálózat egyik magánhálózati IP-címét használja, így hatékonyan behozza az Automation szolgáltatást a virtuális hálózatba. A virtuális hálózaton lévő gépek és az Automation-fiók közötti hálózati forgalom áthalad a VNeten és egy privát kapcsolaton a Microsoft gerinchálózatán, így kiküszöbölve a nyilvános internetről való kitettséget.

Tegyük fel például, hogy rendelkezik egy virtuális hálózattal, ahol letiltotta a kimenő internet-hozzáférést. Az Automation-fiókot azonban privát módon szeretné elérni, és olyan Automation-funkciókat szeretne használni, mint a webhookok, State Configuration és runbook-feladatok a hibrid runbook-dolgozókon. Emellett azt is szeretné, hogy a felhasználók csak a VNET-en keresztül férnek hozzá az Automation-fiókhoz.  A privát végpont üzembe helyezése ezeket a célokat éri el.

Ez a cikk bemutatja, mikor használható, és hogyan állíthat be privát végpontot az Automation-fiókjával.

![A Private Link fogalmi Azure Automation](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Private Link a Azure Automation csak az Azure kereskedelmi és az Azure US Government-felhőkben érhető el.

## <a name="advantages"></a>Előnyök

A Private Link a következőt tudja:

- Privát csatlakozás a Azure Automation nyilvános hálózati hozzáférés megnyitása nélkül.
- Privát módon csatlakozhat Azure Monitor Log Analytics-munkaterülethez nyilvános hálózati hozzáférés megnyitása nélkül.

    >[!NOTE]
    >Külön privát végpontra van szükség a Log Analytics-munkaterülethez, ha az Automation-fiók egy Log Analytics-munkaterülethez van csatolva a feladatadatok továbbítása érdekében, és ha engedélyezte az olyan funkciókat, mint az Update Management, változáskövetés és leltározás, State Configuration vagy Start/Stop VMs during off-hours. További információ a Private Link való csatlakozásról Azure Monitor [lásd: Azure Private Link](../../azure-monitor/logs/private-link-security.md)segítségével biztonságosan csatlakoztathat hálózatokat a Azure Monitor.

- Győződjön meg arról, hogy az Automation-adatok csak engedélyezett magánhálózaton keresztül érhetők el.
- A magánhálózatok adatkiszivárgásának megelőzése érdekében határozza meg Azure Automation, amely a privát végponton keresztül csatlakozik.
- Az ExpressRoute és a Azure Automation használatával biztonságosan csatlakoztathatja a helyszíni Private Link.
- A gerinchálózaton belül tartsa Microsoft Azure forgalmat.

További információ:  [A](../../private-link/private-link-overview.md#key-benefits)Private Link.

## <a name="limitations"></a>Korlátozások

- A szolgáltatás jelenlegi implementációja Private Link Automation-fiók felhőalapú feladatok nem férnek hozzá a privát végponttal védett Azure-erőforrásokhoz. Például: Azure Key Vault, Azure SQL, Azure Storage-fiók stb. Ennek áthidaló megoldásához használjon inkább egy hibrid [runbook-feldolgozót.](../automation-hybrid-runbook-worker.md)
- A [Log Analytics-ügynök](../../azure-monitor/agents/log-analytics-agent.md) legújabb verzióját kell használnia Windows vagy Linux rendszeren.
- A [Log Analytics-átjáró](../../azure-monitor/agents/gateway.md) nem támogatja a Private Link.

## <a name="how-it-works"></a>Működés

Azure Automation Private Link egy vagy több privát végpontot (és ezáltal az őket tartalmazó virtuális hálózatokat) csatlakoztatja az Automation-fiók erőforrásához. Ezek a végpontok olyan gépek, amelyek webhookokkal indítják el a runbookokat, a hibrid runbook-feldolgozói szerepkört üzemeltető gépek és Desired State Configuration (DSC) csomópontok.

Miután privát végpontokat hoz létre az Automation számára, a rendszer a nyilvános Automation URL-címeket a virtuális hálózat egy privát végpontjára leképezi. Ön vagy egy gép közvetlenül kapcsolatba léphet az Automation URL-címekkel.

### <a name="webhook-scenario"></a>Webhook-forgatókönyv

A runbookok a webhook URL-címének POST-címével indíthatóak el. Az URL-cím például így néz ki: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="hybrid-runbook-worker-scenario"></a>Hibrid runbook-feldolgozó forgatókönyv

A Azure Automation felhasználó hibrid runbook-feldolgozó szolgáltatása lehetővé teszi runbookok futtatását közvetlenül az Azure-on vagy nem Azure-beli gépen, beleértve az engedélyezett Azure Arc regisztrált kiszolgálókat is. A szerepkört üzemeltető gépről vagy kiszolgálóról közvetlenül futtathat runbookokat a környezet erőforrásain a helyi erőforrások kezeléséhez.

A HYBRIDDS-végpontot a hibrid feldolgozó használja a runbookok indításához/leállításához, a runbookok letöltéséhez a feldolgozóhoz, valamint a feladatnapló-streamnek az Automation szolgáltatásba való visszaszolgáltatásához.A JREDS-végpont engedélyezése után az URL-cím a következő lesz: `https://<automationaccountID>.jobruntimedata.<region>.azure-automation.net` . Ez biztosítja, hogy a runbook végrehajtása az Azure Virtual Network-hoz csatlakoztatott hibrid feldolgozón anélkül tudja végrehajtani a feladatokat, hogy meg kellene nyitnia egy kimenő internetkapcsolatot.  

> [!NOTE]
>Az Azure Automation privát kapcsolatainak jelenlegi implementációja csak az Azure-beli virtuális hálózathoz csatlakoztatott hibrid runbook-feldolgozón futó feladatokat támogatja, a felhőalapú feladatokat nem.

## <a name="hybrid-worker-scenario-for-update-management"></a>Hibrid feldolgozói forgatókönyv Update Management  

A hibrid runbook-feldolgozó rendszer támogatja a Update Management szolgáltatás által használt rejtett runbookok készletét, amelyek célja a felhasználó által megadott frissítések telepítése Windows és Linux rendszerű gépeken. Ha Azure Automation Update Management van beállítva, a Log Analytics-munkaterülethez csatlakozó összes gép automatikusan hibrid runbook-feldolgozóként lesz konfigurálva.

A konfigurálás & áttekintését Update Management About Update Management ( [Áttekintés) Update Management.](../update-management/overview.md) Az Update Management funkció egy Log Analytics-munkaterülettől függ, ezért a munkaterületet egy Automation-fiókkal kell összekapcsolni. A Log Analytics-munkaterület tárolja a megoldás által gyűjtött adatokat, és tárolja a naplókereséseket és -nézeteket.

Ha azt szeretné, hogy Private Link-csatornán keresztül az Update Managementhez konfigurált gépek biztonságosan csatlakozzon az Automation & Log Analytics-munkaterülethez, engedélyeznie kell az Private Link-t a Private Link-val konfigurált Automation-fiókhoz csatolt Log Analytics-munkaterületen.

A Log Analytics konfigurálásacímű dokumentum lépéseit követve szabályozhatja, hogy a Log Analytics-munkaterület Private Link-hatókörön kívülről [is elérhető legyen.](../../azure-monitor/logs/private-link-security.md#configure-log-analytics) Ha a Nyilvános hálózati hozzáférés engedélyezése **a betöltéshez** **beállításnál** a Nem lehetőséget adja meg, akkor a csatlakoztatott hatókörön kívüli gépek nem tölthetnek fel adatokat erre a munkaterületre. Ha a Nyilvános hálózati **hozzáférés** engedélyezése a lekérdezésekhez **beállításnál** a Nem lehetőséget adja meg, akkor a hatóköröken kívüli gépek nem férhetnek hozzá ezen a munkaterületen lévő adatokhoz.

A **DSCAndHybridWorker** célalerőforrás használatával engedélyezheti a Private Link számára a & hibrid dolgozók számára.

> [!NOTE]
> Az Azure-ban üzemeltetett, Update Management által felügyelt gépek, amelyek ExpressRoute privát társviszony-létesítésen, VPN-alagutakon és privát végpontokat használó társviszony-alapú virtuális hálózatokon keresztül csatlakoznak az Azure VNethez, Private Link.

### <a name="state-configuration-agentsvc-scenario"></a>State Configuration (agentsvc) forgatókönyv

State Configuration azure-beli konfigurációkezelési szolgáltatást biztosít, amely lehetővé teszi PowerShell Desired State Configuration-konfigurációk írását, kezelését és fordítását bármely felhőalapú vagy helyszíni adatközpont csomópontja számára.

A gépen található ügynök regisztrál a DSC szolgáltatásban, majd a szolgáltatásvégpont használatával lekért DSC-konfigurációt használ. Az ügynök szolgáltatásvégpontja a következőhöz hasonló: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

A nyilvános és & végpont URL-címe megegyezik, de a privát kapcsolat engedélyezése esetén egy magánhálózati IP-címre lesz leképezve.

## <a name="planning-based-on-your-network"></a>Tervezés a hálózat alapján

Az Automation-fiókerőforrás beállítása előtt gondolja át a hálózatelszigetelési követelményeit. Értékelje ki a virtuális hálózatok nyilvános internethez való hozzáférését és az Automation-fiók hozzáférési korlátozásait (beleértve egy Private Link-csoporthatókör beállítását Azure Monitor-naplókra, ha integrálva van az Automation-fiókkal). A terv részeként tekintse [](./automation-region-dns-records.md) át az Automation szolgáltatás DNS-rekordjait is, hogy a támogatott funkciók probléma nélkül működjön.

### <a name="connect-to-a-private-endpoint"></a>Csatlakozás privát végponthoz

Hozzon létre egy privát végpontot a hálózat csatlakoztatásához. A következő központban [hozhatja Azure Portal Private Link:](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints). A publicNetworkAccess és a Private Link módosításainak alkalmazása után akár 35 percet is igénybe vehet, hogy életbe lépnek.

Ebben a szakaszban egy privát végpontot fog létrehozni az Automation-fiókhoz.

1. A képernyő bal felső részén válassza az Erőforrás létrehozása > **Hálózat > Private Link-központ** lehetőséget.

2. A **Private Link-központ – Áttekintés** lehetőség Privát kapcsolat létrehozása szolgáltatáshoz **lehetőségében** válassza az **Indítás lehetőséget.**

3. A **Virtuális gép létrehozása – Alapvető beállítások** beállításnál adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Name | Adja meg a *PrivateEndpoint -et.* |
    | Region | Válassza **a SajátRégió lehetőséget.** |
    |||

4. Válassza **a Tovább: Erőforrás lehetőséget.**

5. A **Privát végpont létrehozása – Erőforrás** mezőben adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza a Csatlakozás a címtárban található Azure-erőforráshoz lehetőséget.|
    | Előfizetés| Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza **a Microsoft.Automation/automationAccounts lehetőséget.** |
    | Erőforrás |Válassza *a myAutomationAccount lehetőséget*|
    |Cél-alforrás |A forgatókönyvtől függően válassza a *Webhook* vagy a *DSCAndHybridWorker* lehetőséget.|
    |||

6. Válassza **a Tovább: Konfiguráció lehetőséget.**

7. A **Privát végpont létrehozása – Konfiguráció beállításnál** adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |**HÁLÓZATKEZELÉS**| |
    | Virtuális hálózat| Válassza *a MyVirtualNetwork lehetőséget.* |
    | Alhálózat | Válassza *a mySubnet lehetőséget.* |
    |**PRIVÁT DNS-INTEGRÁCIÓ**||
    |Integrálás saját DNS-zónával |Válassza az **Igen** lehetőséget. |
    |Privát DNS-zóna |Válassza *az (Új)privatelink.azure-automation.net* |
    |||

8. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját.

9. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget.

A Private Link-központ válassza **a** **Privát végpontok** lehetőséget a privát kapcsolati erőforrás megtekintéséhez.

![Automation-erőforrás privát hivatkozása](./media/private-link-security/private-link-automation-resource.png)

Az összes részletet az erőforrás kiválasztásával láthatja. Ez létrehoz egy új privát végpontot az Automation-fiók számára, és magánhálózati IP-címet rendel hozzá a virtuális hálózatról. A **Kapcsolat állapota jóváhagyottként** **jelenik meg.**

Ehhez hasonlóan a rendszer létrehoz egy egyedi teljes tartománynevet (FQDN) a State Configuration (agentsvc) és a hibrid runbook-feldolgozó feladat-futtatás (jds) számára. Mindegyikhez külön IP-cím van rendelve a virtuális hálózattól, és a **Kapcsolat állapota** jóváhagyottként **jelenik meg.**

Ha a szolgáltatás felhasználója Azure RBAC-engedélyekkel rendelkezik az Automation-erőforráson, kiválaszthatja az automatikus jóváhagyási módszert. Ebben az esetben, amikor a kérés eléri az Automation-szolgáltatói erőforrást, a szolgáltatónak nincs teendőja, és a rendszer automatikusan jóváhagyja a kapcsolatot.

## <a name="set-public-network-access-flags"></a>Nyilvános hálózati hozzáférés jelzőinek beállítása

Konfigurálhat egy Automation-fiókot úgy, hogy az tiltsa le az összes nyilvános konfigurációt, és csak privát végpontok közötti kapcsolatokat engedélyezzen a hálózati biztonság további növelése érdekében. Ha csak a virtuális hálózatról szeretné korlátozni az Automation-fiókhoz való hozzáférést, és nem szeretné engedélyezni a nyilvános internetről való hozzáférést, a tulajdonságot a következőre `publicNetworkAccess` állíthatja: `$false` .

Ha a **Nyilvános hálózati** hozzáférés beállítás beállítása , csak a privát végponton keresztüli kapcsolatok engedélyezettek, és a rendszer minden nyilvános végponton keresztüli kapcsolatot letilt jogosulatlan hibaüzenettel és `$false` 401-es HTTP-állapottal.

Az alábbi PowerShell-szkript bemutatja, hogyan kell és a Nyilvános hálózati hozzáférés `Get` `Set` **tulajdonságot** az Automation-fiók szintjén:

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

A nyilvános hálózat hozzáférési tulajdonságát a nyilvános hálózati Azure Portal. Az Automation-fiókjában  válassza a Hálózatelszigetelés lehetőséget a bal oldali panel **Fiókbeállítások szakaszában.** Ha a Nyilvános hálózati hozzáférés beállítás Nem, csak a privát végpontok kapcsolatai engedélyezettek, és a nyilvános végpontok kapcsolatai le vannak tiltva.

![Nyilvános hálózati hozzáférés beállítás](./media/private-link-security/allow-public-network-access.png)

## <a name="dns-configuration"></a>DNS-konfiguráció

Amikor a kapcsolati sztring részeként teljes tartománynévvel (FQDN) csatlakozik egy privát kapcsolati erőforráshoz, fontos, hogy a DNS-beállításokat megfelelően konfigurálja a lefoglalt magánhálózati IP-címre való feloldáshoz. Előfordulhat, hogy a meglévő Azure-szolgáltatások dns-konfigurációt használnak a nyilvános végponton keresztüli csatlakozáshoz. A DNS-konfigurációt át kell vizsgálni és frissíteni kell a privát végponttal való csatlakozáshoz.

A privát végponthoz társított hálózati adapter tartalmazza a DNS konfigurálásához szükséges összes információt, beleértve az adott privát kapcsolati erőforráshoz lefoglalt teljes tartományneveket és magánhálózati IP-címeket.

A következő beállításokkal konfigurálhatja a DNS-beállításokat a privát végpontok számára:

* Használja a gazdafájlt (csak teszteléshez ajánlott). A virtuális gépen lévő gazdafájllal felülbírálhatja először a DNS-t a névfeloldáshoz. A DNS-bejegyzésnek az alábbi példához hasonlónak kell lennie: `privatelinkFQDN.jrds.sea.azure-automation.net` .

* Használjon privát [DNS-zónát.](../../dns/private-dns-privatednszone.md) Privát DNS-zónák használatával felülbírálhatja egy adott privát végpont DNS-feloldásait. Egy privát DNS-zóna összekapcsolható a virtuális hálózattal adott tartományok feloldása érdekében. Ha engedélyezni szeretné, hogy a virtuális gépen lévő ügynök kommunikáljon a privát végponton keresztül, hozzon létre egy saját DNS a következőként: `privatelink.azure-automation.net` . Adjon hozzá egy új DNS *A* rekordleképezést a privát végpont IP-címével.

* Használja a DNS-továbbítót (nem kötelező). A DNS-továbbító használatával felülbírálhatja egy adott privát kapcsolati erőforrás DNS-feloldási műveletét. Ha a [DNS-kiszolgáló](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) virtuális hálózaton üzemel, létrehozhat egy DNS-továbbítási szabályt, amely egy privát DNS-zónát használ az összes privát kapcsolati erőforrás konfigurációjának egyszerűsítéséhez.

További információ: [Azure-beli privát végpont DNS-konfigurálása](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Következő lépések

További információ a privát végpontról: [Mi az az Azure privát végpont?](../../private-link/private-endpoint-overview.md).
