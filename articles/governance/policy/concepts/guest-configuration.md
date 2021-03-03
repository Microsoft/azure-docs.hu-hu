---
title: Tudnivalók a virtuális gépek tartalmának naplózásáról
description: Megtudhatja, hogyan használja a Azure Policy a vendég konfigurációs ügyfelet a beállítások naplózására a virtuális gépeken belül.
ms.date: 01/14/2021
ms.topic: conceptual
ms.openlocfilehash: 33a492eb3c8c175bfcdc6a13cb467ed2f180c1e1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702878"
---
# <a name="understand-azure-policys-guest-configuration"></a>Az Azure Policy vendégkonfigurációjának ismertetése


A Azure Policy a számítógépeken belül is naplózhatja a beállításokat, mind az Azure-ban, mind az [arc-csatlakoztatott gépeken](../../../azure-arc/servers/overview.md)futó gépek esetében. Az érvényesítést a Vendégkonfiguráció bővítmény és ügyfél végzi. A bővítmény az ügyfélen keresztül ellenőrzi a beállításokat, például a következőket:

- Az operációs rendszer konfigurációja
- Alkalmazás konfigurációja vagy jelenléte
- Környezeti beállítások

Jelenleg a legtöbb Azure Policy vendég konfigurációs házirend definíciója csak a számítógépen belüli naplózási beállításokat definiálja. Nem alkalmaznak konfigurációkat. A kivétel egy, az [alábbiakban hivatkozott](#applying-configurations-using-guest-configuration)beépített szabályzat.

[A dokumentum videó-átjárása elérhető](https://youtu.be/Y6ryD3gTHOs).

## <a name="enable-guest-configuration"></a>Vendég konfiguráció engedélyezése

A környezetben lévő gépek állapotának naplózásához, beleértve az Azure-ban és az arc-csatlakoztatott gépeken található gépeket is, tekintse át a következő adatokat.

## <a name="resource-provider"></a>Erőforrás-szolgáltató

A vendég konfiguráció használatához regisztrálnia kell az erőforrás-szolgáltatót. Ha a vendég konfigurációs szabályzatának hozzárendelését a portálon keresztül hajtja végre, vagy ha az előfizetés regisztrálva van Azure Security Center, akkor az erőforrás-szolgáltató automatikusan regisztrálva lesz. Manuálisan is regisztrálhat a [portálon](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShellon](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)vagy az [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)-n keresztül.

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Azure-beli virtuális gépekre vonatkozó követelmények üzembe helyezése

A beállítások számítógépeken belüli naplózásához a virtuálisgép- [bővítmény](../../../virtual-machines/extensions/overview.md) engedélyezve van, és a gépnek rendszer által felügyelt identitással kell rendelkeznie. A bővítmény letölti a vonatkozó szabályzat-hozzárendelést és a megfelelő konfigurációdefiníciót. Az identitás a gép hitelesítésére szolgál, ahogy az beolvassa és beírja a vendég konfigurációs szolgáltatásba. A bővítmény nem szükséges az arc-csatlakoztatott gépekhez, mert az az arc csatlakoztatott számítógép ügynökének része.

> [!IMPORTANT]
> Az Azure-beli virtuális gépek naplózásához a vendég konfiguráció kiterjesztése és a felügyelt identitás szükséges. A bővítmény nagy léptékű üzembe helyezéséhez rendelje hozzá a következő házirend-kezdeményezést:
> 
> `Deploy prerequisites to enable Guest Configuration policies on virtual machines`

### <a name="limits-set-on-the-extension"></a>A bővítményre beállított korlátok

Ha korlátozni szeretné a bővítménynek a gépen belül futó alkalmazásoktól való korlátozását, a vendég konfigurációja nem lépheti túl a CPU 5%-át. Ez a korlátozás a beépített és az egyéni definíciók esetében is létezik. Ugyanez érvényes a vendég konfigurációs szolgáltatáshoz az arc-csatlakoztatott gépi ügynökben.

### <a name="validation-tools"></a>Ellenőrzési eszközök

A gépen belül a vendég konfigurációs ügyfél helyi eszközöket használ a naplózás futtatásához.

Az alábbi táblázat felsorolja az egyes támogatott operációs rendszereken használt helyi eszközöket. Beépített tartalom esetén a vendég konfigurációja automatikusan betölti ezeket az eszközöket.

|Operációs rendszer|Érvényesítési eszköz|Jegyzetek|
|-|-|-|
|Windows|[PowerShell desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| A rendszer csak az Azure Policy által használt mappába betöltve. Nem ütközik a Windows PowerShell DSC-vel. A PowerShell Core nincs hozzáadva a rendszer elérési útjához.|
|Linux|[Chef Inspect](https://www.chef.io/inspec/)| Telepíti a Chef Inspect verzió 2.2.61 az alapértelmezett helyen, és hozzáadja a rendszer elérési útjához. Az inspec-csomag függőségei, például a Ruby és a Python is telepítve vannak. |

### <a name="validation-frequency"></a>Ellenőrzés gyakorisága

A vendég konfigurációs ügyfél 5 percenként keres új tartalmat. A vendég-hozzárendelés fogadása után a rendszer 15 perces időközönként újra bejelöli a konfiguráció beállításait. A rendszer a naplózás befejezésekor elküldi az eredményeket a vendég konfiguráció erőforrás-szolgáltatójának. A szabályzatok [kiértékelésének](../how-to/get-compliance-data.md#evaluation-triggers) bekövetkeztekor a számítógép állapota a vendég konfiguráció erőforrás-szolgáltatóba íródik. Ez a frissítés Azure Policyt okoz a Azure Resource Manager tulajdonságainak kiértékeléséhez. Az igény szerinti Azure Policy kiértékelése a vendég konfiguráció erőforrás-szolgáltató legújabb értékét kérdezi le. Azonban nem aktiválja a számítógép konfigurációjának új naplózását.

## <a name="supported-client-types"></a>Támogatott ügyfelek típusai

A vendég-konfigurációs szabályzatok definíciói az új verziókhoz tartoznak. Az Azure Marketplace-en elérhető operációs rendszerek régebbi verziói nem lesznek kizárva, ha a vendég konfigurációs ügyfél nem kompatibilis. Az alábbi táblázat az Azure-lemezképekben támogatott operációs rendszerek listáját tartalmazza:

|Publisher|Name|Verziók|
|-|-|-|
|Canonical|Ubuntu Server|14,04 – 20,04|
|Credativ|Debian|8 - 10|
|Microsoft|Windows Server|2012 – 2019|
|Microsoft|Windows-ügyfél|Windows 10|
|OpenLogic|CentOS|7,3 – 8|
|Red Hat|Red Hat Enterprise Linux|7,4 – 8|
|SUSE|SLES|12 SP3 – SP5, 15|

Az egyéni virtuálisgép-lemezképeket a vendég-konfigurációs házirend definíciói támogatják, feltéve, hogy a fenti táblázatban szereplő operációs rendszerek egyike.

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Az Azure-beli virtuális gépek a helyi hálózati adaptert vagy egy privát hivatkozást is használhatnak a vendég konfigurációs szolgáltatással való kommunikációhoz.

Az Azure arc-gépek a helyszíni hálózati infrastruktúrával csatlakozva érhetik el az Azure-szolgáltatásokat, és bejelenthetik a megfelelőségi állapotot.

### <a name="communicate-over-virtual-networks-in-azure"></a>Kommunikáció az Azure-beli virtuális hálózatokkal

A virtuális hálózatokat használó virtuális gépek számára a porton elérhető Azure-adatközpontokhoz kimenő hozzáférésre lesz szükség `443` . Ha az Azure-ban olyan magánhálózati virtuális hálózatot használ, amely nem engedélyezi a kimenő forgalmat, konfigurálja a kivételeket a hálózati biztonsági csoport szabályaival. A "GuestAndHybridManagement" szolgáltatási címke használható a vendég konfigurációs szolgáltatásra való hivatkozáshoz.

### <a name="communicate-over-private-link-in-azure"></a>Kommunikáció privát kapcsolaton keresztül az Azure-ban

A virtuális gépek [privát hivatkozást](../../../private-link/private-link-overview.md) is használhatnak a vendég konfigurációs szolgáltatással való kommunikációhoz. A szolgáltatás engedélyezéséhez alkalmazza a címkét `EnablePrivateNeworkGC` (a "t" nélkül a hálózatban) és az értéket `TRUE` . A címke a vendég-konfigurációs házirend definícióinak a gépre való alkalmazása előtt vagy után is alkalmazható.

A forgalmat az Azure [virtuális nyilvános IP-címével](../../../virtual-network/what-is-ip-address-168-63-129-16.md) irányítjuk, hogy biztonságos, hitelesített csatornát hozzon létre az Azure platform erőforrásaival.

### <a name="azure-arc-connected-machines"></a>Azure arc-csatlakoztatott gépek

Az Azure arc-on kívül található csomópontok a vendég konfigurációs szolgáltatáshoz való kapcsolódást igénylik. Az [Azure arc dokumentációjában](../../../azure-arc/servers/overview.md)megadott hálózati és proxy-követelmények részletei.

Az Azure-beli vendég-konfigurációs erőforrás-szolgáltatóval való kommunikációhoz a gépeknek kimenő hozzáférésre van szükségük az Azure-adatközpontok **443**-es portján Ha egy Azure-beli hálózat nem engedélyezi a kimenő forgalmat, konfigurálja a kivételeket a [hálózati biztonsági csoportokra](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) vonatkozó szabályokkal. A "GuestAndHybridManagement" [szolgáltatási címke](../../../virtual-network/service-tags-overview.md) használható a vendég konfigurációs szolgáltatásra való hivatkozáshoz.

A privát adatközpontokban lévő arc csatlakozó kiszolgálók esetében engedélyezze a forgalmat a következő minták használatával:

- Port: csak TCP 443 szükséges a kimenő internet-hozzáféréshez
- Globális URL-cím: `*.guestconfiguration.azure.com`

## <a name="managed-identity-requirements"></a>Felügyelt identitásokra vonatkozó követelmények

A kezdeményezésben szereplő szabályzat-definíciók a _vendég-konfigurációs szabályzatok virtuális gépeken való engedélyezéséhez_ a rendszer által hozzárendelt felügyelt identitás engedélyezése, ha az egyik nem létezik. Az identitás létrehozását kezelő kezdeményezésben két házirend-definíció található. Ha a házirend-definícióban szereplő feltételek biztosítják a megfelelő viselkedést az Azure-beli gépi erőforrás aktuális állapota alapján.

Ha a gép jelenleg nem rendelkezik felügyelt identitásokkal, a hatályos szabályzat a következő: [rendszerhez rendelt felügyelt identitás hozzáadása a vendég konfigurációs hozzárendeléseinek engedélyezéséhez identitás nélküli virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

Ha a gépnek jelenleg van felhasználó által hozzárendelt rendszeridentitása, a hatályos szabályzat a következő: [rendszerhez rendelt felügyelt identitás hozzáadása a vendég konfigurációs hozzárendeléseinek engedélyezéséhez felhasználó által hozzárendelt identitással rendelkező virtuális gépeken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="guest-configuration-definition-requirements"></a>A vendég konfigurációjának meghatározására vonatkozó követelmények

A vendég-konfigurációs házirend definíciói a **AuditIfNotExists** hatást használják. A definíció hozzárendelésekor a háttérrendszer automatikusan kezeli az Azure erőforrás-szolgáltató összes követelményének életciklusát `Microsoft.GuestConfiguration` .

A **AuditIfNotExists** házirend-definíciók nem adják vissza a megfelelőségi eredményeket, amíg az összes követelmény nem teljesül a gépen. A követelmények az Azure-beli [virtuális gépek üzembe helyezési követelményei](#deploy-requirements-for-azure-virtual-machines) című szakaszban olvashatók.

> [!IMPORTANT]
> A vendég konfigurációjának előzetes kiadásában egy kezdeményezésre volt szükség a **DeployIfNoteExists** és a **AuditIfNotExists** -definíciók összevonásához. A **DeployIfNotExists** -definíciók már nem szükségesek. A definíciók és a kezdeményezések címkével vannak ellátva, `[Deprecated]` de a meglévő hozzárendelések továbbra is működni fognak. További információért lásd a következő blogbejegyzéset: a [vendég konfigurációjának naplózási házirendjeihez kiadott fontos változás](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

### <a name="what-is-a-guest-assignment"></a>Mi az a vendég-hozzárendelés?

Ha egy Azure Policy van hozzárendelve, ha a "vendég konfigurációja" kategóriába tartozik, a rendszer metaadatokat tartalmaz a vendég-hozzárendelés leírásához.
A vendég-hozzárendelések egy gép és egy Azure Policy-forgatókönyv közötti kapcsolatként is megtekinthetők.
Az alábbi kódrészlet például társítja az Azure Windows alapkonfigurációt a minimális verziószámmal a `1.0.0` szabályzat hatálya alá tartozó gépekhez. Alapértelmezés szerint a vendég-hozzárendelés csak a gép naplózását fogja elvégezni.

```json
"metadata": {
    "category": "Guest Configuration",
    "guestConfiguration": {
        "name": "AzureWindowsBaseline",
        "version": "1.*"
    }
//additional metadata properties exist
```

A vendég-hozzárendeléseket a rendszer automatikusan létrehoz egy gépen a vendég konfigurációs szolgáltatásban. Az erőforrástípus `Microsoft.GuestConfiguration/guestConfigurationAssignments`.
Azure Policy a vendég-hozzárendelési erőforrás **complianceStatus** tulajdonságát használja a megfelelőségi állapot jelentéséhez. További információ: a [megfelelőségi adatok beszerzése](../how-to/get-compliance-data.md).

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Az operációs rendszer beállításainak naplózása az iparági alapkonfigurációkat követve

Azure Policy az operációs rendszer beállításainak naplózása az alapkonfigurációt követő egyik kezdeményezés. Az előzetes verzió definíciója _\[ \] : a Windows rendszerű számítógépeknek meg kell felelniük az Azure biztonsági alapkonfiguráció követelményeinek_ , Active Directory csoportházirend alapuló szabályokat tartalmaznak.

A beállítások többsége paraméterekként érhető el. A paraméterek lehetővé teszik a naplózott elemek testreszabását.
A szabályzatot a követelményekkel igazíthatja, vagy a szabályzatot harmadik féltől származó információkra, például iparági szabályozási szabványokra is kiképezheti.

Egyes paraméterek egy egész érték tartományát támogatják. A jelszó maximális kora beállítás például naplózhatja a hatályos Csoportházirend beállítást. Az "1, 70" tartomány megerősíti, hogy a felhasználóknak legalább 70 naponta meg kell változtatniuk a jelszavukat, de nem kevesebb mint egy napot.

Ha a szabályzatot egy Azure Resource Manager sablon (ARM-sablon) használatával rendeli hozzá, a kivételek kezeléséhez használja a parameters (paraméterek) fájlt. A fájlokat egy verziókövetés rendszerbe (például a git-ba) tekintheti meg. A fájl módosításaival kapcsolatos megjegyzések igazolják, hogy egy hozzárendelés miért kivétel a várt értéktől.

#### <a name="applying-configurations-using-guest-configuration"></a>Konfigurációk alkalmazása a vendég konfiguráció használatával

Csak a _Windows rendszerű gépeken beállított időzónát_ definiáló definíció állítja be a gépet az időzóna konfigurálásával. A számítógépeken belüli beállítások konfigurálásának egyéni szabályzat-definíciói nem támogatottak.

Ha a _konfigurálással_ kezdődő definíciókat rendeli hozzá, akkor a definíciók _központi telepítésének előfeltételeit is hozzá kell rendelnie a Windows rendszerű virtuális gépeken a vendég-konfigurációs szabályzat engedélyezés_ Ezeket a definíciókat a választott kezdeményezéssel kombinálhatja.

> [!NOTE]
> A beépített időzóna-házirend az egyetlen olyan definíció, amely támogatja a gépeken belüli beállítások konfigurálását, és az egyéni szabályzat-definíciókat, amelyek a gépek beállításait nem támogatják.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Szabályzatok kiosztása az Azure-on kívüli gépekhez

A vendég konfigurációhoz elérhető naplózási házirend-definíciók közé tartozik a **Microsoft. HybridCompute/Machines** erőforrástípus. Az Azure-ív részét képező, a házirend-hozzárendelés hatókörében lévő kiszolgálók automatikusan beletartoznak a [szolgáltatásba](../../../azure-arc/servers/overview.md) .

## <a name="troubleshooting-guest-configuration"></a>A vendég konfigurációjának hibaelhárítása

További információ a vendég konfigurációjának hibaelhárításáról: [Azure Policy hibaelhárítás](../troubleshoot/general.md).

### <a name="multiple-assignments"></a>Több hozzárendelés

A vendég-konfiguráció házirendjének definíciói jelenleg csak egyszer kell hozzárendelni ugyanazt a vendég-hozzárendelést, még akkor is, ha a házirend-hozzárendelés eltérő paramétereket használ.

### <a name="client-log-files"></a>Ügyfél naplófájljai

A vendég konfigurációs bővítmény naplófájlokat ír a következő helyszínekre:

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

### <a name="collecting-logs-remotely"></a>Naplók távoli gyűjtése

A vendég konfigurációs konfigurációk vagy modulok hibaelhárításának első lépéseként a parancsmagot kell használnia, amely azt `Test-GuestConfigurationPackage` ismerteti, hogyan [hozhat létre egyéni vendég-konfigurációs naplózási szabályzatot a Windows rendszerhez](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Ha ez nem sikerül, az ügyfél naplói összegyűjtése segíthet a problémák diagnosztizálásában.

#### <a name="windows"></a>Windows

Adatok rögzítése a naplófájlokból az [Azure VM Run paranccsal](../../../virtual-machines/windows/run-command.md), a következő példa a PowerShell-szkript hasznos lehet.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Adatok rögzítése a naplófájlokból az [Azure VM Run paranccsal](../../../virtual-machines/linux/run-command.md), a következő példa a bash-szkript hasznos lehet.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

### <a name="client-files"></a>Ügyféloldali fájlok

A vendég konfigurációs ügyfél letölti a csomagokat a gépre, és kibontja a tartalmat.
A letöltött és tárolt tartalmak ellenőrzéséhez tekintse meg az alább megadott mappák helyét.

Windows: `c:\programdata\guestconfig\configurations`

Linux: `/var/lib/guestconfig/configurations`

## <a name="guest-configuration-samples"></a>Vendég konfigurációs minták

A vendég konfiguráció beépített házirendjének mintái a következő helyszíneken érhetők el:

- [Beépített szabályzat-definíciók – vendég konfigurációja](../samples/built-in-policies.md#guest-configuration)
- [Beépített kezdeményezések – vendég konfigurációja](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy Samples GitHub-tárház](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

### <a name="video-overview"></a>Videó – áttekintés

A Azure Policy vendég konfigurációjának következő áttekintése a ITOps-beszélgetések 2021-es verziójában érhető el.

[Alapkonfigurációk szabályozása hibrid kiszolgálói környezetekben Azure Policy Guest Configuration használatával](https://techcommunity.microsoft.com/t5/itops-talk-blog/ops114-governing-baselines-in-hybrid-server-environments-using/ba-p/2109245)

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan tekintheti meg az egyes beállításokat a [vendég konfiguráció megfelelősége nézetből](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](./definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](./effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
