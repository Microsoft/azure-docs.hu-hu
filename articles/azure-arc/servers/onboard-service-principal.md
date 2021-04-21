---
title: Hibrid gépek nagy léptékű csatlakoztatása az Azure-hoz
description: Ebből a cikkből megtudhatja, hogyan csatlakoztathat gépeket az Azure-hoz az engedélyezett Azure Arc szolgáltatásnévvel.
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4aad01fd6991c059b2cf891fd4f06ae83a78a0e4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831599"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Hibrid gépek nagy léptékű csatlakoztatása az Azure-hoz

Az engedélyezett Azure Arc több Windows- vagy Linux-géphez is engedélyezheti a környezetben, a követelményektől függően számos rugalmas beállítással. Az általunk megírt sablonszkprogrammal a telepítés minden lépését automatizálhatja, beleértve a kapcsolat létesítése Azure Arc. A szkriptet azonban interaktívan kell végrehajtania egy olyan fiókkal, amely emelt szintű engedélyekkel rendelkezik a célgépen és az Azure-ban.

Ha a gépeket Azure Arc engedélyezett kiszolgálókhoz szeretne csatlakoztatni, használhat egy Azure Active Directory-szolgáltatásnévt ahelyett, hogy [a](../../active-directory/develop/app-objects-and-service-principals.md) rendszer-jogosultságú identitásával interaktív módon csatlakoztatja a [gépet.](onboard-portal.md) A szolgáltatásnév egy speciális, korlátozott felügyeleti identitás, amely csak a gépek az Azure-hoz való csatlakoztatásához szükséges minimális jogosultságokkal rendelkezik az `azcmagent` paranccsal. Ez biztonságosabb, mint egy magasabb jogosultsági szintű fiók, például Bérlői rendszergazda használata, és követi a hozzáférés-vezérlésre vonatkozó ajánlott biztonsági eljárásokat. A szolgáltatásnév csak az bevezető során használatos, más célra nem.  

A Csatlakoztatott gép ügynök telepítésének és konfigurálásának telepítési módszereihez rendszergazdai engedélyekkel kell rendelkeznie a használt automatizált módszernek a gépeken. Linux rendszeren a gyökérfiókkal, Windows rendszeren pedig a Helyi rendszergazdák csoport tagjaként.

Mielőtt hozzá kezd, tekintse át [](agent-overview.md#prerequisites) az előfeltételeket, és ellenőrizze, hogy az előfizetés és az erőforrások megfelelnek-e a követelményeknek. További információ a támogatott régiókról és egyéb kapcsolódó szempontokról: [Támogatott Azure-régiók.](overview.md#supported-regions) Tekintse át a [nagy léptékű](plan-at-scale-deployment.md) tervezési útmutatót is a tervezési és üzembe helyezési feltételek, valamint a felügyeleti és monitorozási javaslatok áttekintéséhez.  

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Egyszerű szolgáltatás létrehozása nagy léptékű bevezetők számára

A [parancsmaggal Azure PowerShell](/powershell/azure/install-az-ps) szolgáltatásnév a [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) parancsmaggal hozható létre. Vagy a feladat végrehajtásához kövesse [a Szolgáltatásnév](../../active-directory/develop/howto-create-service-principal-portal.md) létrehozása a Azure Portal lépéseit.

> [!NOTE]
> Szolgáltatásnév létrehozása előtt a fióknak a tulajdonos  vagy  a felhasználói hozzáférés rendszergazdája szerepkör tagja kell, hogy legyen abban az előfizetésben, amit az előfizetésben szeretne használni az előfizetésben. Ha nem rendelkezik megfelelő engedélyekkel a szerepkör-hozzárendelések konfigurálához, előfordulhat, hogy a szolgáltatásnév létre lesz hozva, de nem tudja majd a gépeket bevetni.
>

A szolgáltatásnév PowerShell használatával való létrehozásához hajtsa végre az alábbi lépéseket.

1. Futtassa az alábbi parancsot. A parancsmag kimenetét egy változóban kell tárolnia, különben egy későbbi lépésben nem fogja tudni [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) lekérni a szükséges jelszót.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. A változóban tárolt jelszó lekéréshez `$sp` futtassa a következő parancsot:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. A kimenetben keresse meg a jelszó értéket a jelszó mező alatt, **és** másolja ki. Keresse meg az ApplicationId mező alatti értéket **is,** és másolja ki azt is. Mentse őket későbbre egy biztonságos helyre. Ha elfelejti vagy elveszíti a szolgáltatásnév jelszavát, a parancsmag használatával alaphelyzetbe [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) állíthatja.

Az alábbi tulajdonságok értékeit a paraméterekkel együtt `azcmagent` használjuk:

* A paraméter értéke az **ApplicationId** tulajdonság `--service-principal-id` értéke
* A jelszó **tulajdonság** értéke lesz használva az ügynök  `--service-principal-secret` csatlakoztatásához használt paraméterhez.

> [!NOTE]
> Ügyeljen arra, hogy a szolgáltatásnév **ApplicationId tulajdonságát** használja, ne az **Id tulajdonságot.**
>

Az **Azure Connected Machine Beiratás szerepkör** csak a géphez szükséges engedélyeket tartalmazza. A szolgáltatásnévhez hozzárendelheti az engedélyt, hogy a hatóköre tartalmazni tudja az erőforráscsoportot vagy az előfizetést. A szerepkör-hozzárendelés hozzáadásához lásd: Azure-szerepkörök hozzárendelése a Azure Portal vagy [Azure-szerepkörök](../../role-based-access-control/role-assignments-portal.md) [hozzárendelése az Azure CLI használatával.](../../role-based-access-control/role-assignments-cli.md)

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Hozza létre a telepítési szkriptet a Azure Portal

A letöltést és telepítést automatizálni, valamint a kapcsolat létesítése Azure Arc szkript elérhető a Azure Portal. A folyamat befejezéséhez tegye a következőket:

1. A böngészőből a következőt [kell](https://portal.azure.com)Azure Portal.

1. A Kiszolgálók **– Azure Arc** bal  felső részén válassza a Hozzáadás lehetőséget.

1. A Select **a method (Módszer kiválasztása) lapon** válassza a Add multiple servers (Több kiszolgáló **hozzáadása)** csempét, majd a Generate script (Szkript **létrehozása) lehetőséget.**

1. A **Szkript létrehozása lapon** válassza ki azt az előfizetést és erőforráscsoportot, amelyben a gépet az Azure-ban szeretné kezelni. Válassza ki azt az Azure-helyet, ahol a gép metaadatait tárolni fogja. Ez a hely lehet ugyanaz vagy eltérő, mint az erőforráscsoport helye.

1. Az **Előfeltételek lapon** tekintse át az adatokat, majd válassza a **Tovább: Erőforrás részletei lehetőséget.**

1. Az Erőforrás **részletei lapon** adja meg a következőket:

    1. Az **Erőforráscsoport legördülő** listában válassza ki azt az erőforráscsoportot, amelyből a gépet kezelni fogja.
    1. A Régió **legördülő** listában válassza ki azt az Azure-régiót, ahol tárolnia kell a kiszolgálók metaadatait.
    1. Az Operációs **rendszer legördülő** listában válassza ki azt az operációs rendszert, amely a parancsfájl futtatására van konfigurálva.
    1. Ha a gép proxykiszolgálón keresztül kommunikál az internethez való csatlakozáshoz, adja meg a proxykiszolgáló IP-címét vagy a proxykiszolgálóval való kommunikációhoz használt nevet és portszámot. Adja meg az értéket a következő `http://<proxyURL>:<proxyport>` formátumban: .
    1. Válassza **a Tovább: Hitelesítés lehetőséget.**

1. A Hitelesítés **lap** szolgáltatásnév legördülő listájában válassza az **Arc-for-servers lehetőséget.**   Ezután válassza a **Tovább: Címkék lehetőséget.**

1. A Címkék **lapon tekintse**  át a javasolt alapértelmezett fizikaihely-címkéket, és adjon meg egy értéket, vagy adjon meg egy vagy több **Egyéni** címkét a szabványok támogatásához.

1. Válassza **a Tovább: Szkript letöltése és futtatása lehetőséget.**

1. A Szkript **letöltése és futtatása lapon** tekintse át az összefoglaló adatokat, majd válassza a Letöltés **lehetőséget.** Ha továbbra is módosításokat kell tennie, válassza az Előző **lehetőséget.**

Windows rendszeren a rendszer felszólítja, hogy mentse a `OnboardingScript.ps1` ,, Linux esetén `OnboardingScript.sh` pedig a-t a számítógépre.

## <a name="install-the-agent-and-connect-to-azure"></a>Az ügynök telepítése és csatlakozás az Azure-hoz

A korábban létrehozott szkriptsablon használatával több hibrid Linux- és Windows-gépen is telepítheti és konfigurálhatja a csatlakoztatottgép-ügynököt a szervezet előnyben részesített automatizálási eszközével. A szkript a Hibrid gépek csatlakoztatása az [Azure-hoz](onboard-portal.md) az azure-hoz cikkben ismertetett hasonló lépéseket Azure Portal cikkben. A különbség az utolsó lépésben van, amelyben létrehozza a kapcsolatot Azure Arc a paranccsal `azcmagent` a szolgáltatásnévvel.

A következő beállításokat kell konfigurálnia a parancsnak a `azcmagent` szolgáltatásnévhez való használatára.

* `service-principal-id` : A szolgáltatásnév alkalmazásazonosítóját képviselő egyedi azonosító (GUID).
* `service-principal-secret` | A szolgáltatásnév jelszava.
* `tenant-id` : Az Azure AD dedikált példányát képviselő egyedi azonosító (GUID).
* `subscription-id` : Annak az Azure-előfizetésnek az előfizetés-azonosítója (GUID), amelybe a gépeket szeretné.
* `resource-group` : Az erőforráscsoport neve, amelyhez a csatlakoztatott gépeket tartozni szeretné.
* `location`: Lásd [a támogatott Azure-régiókat.](overview.md#supported-regions) Ez a hely lehet ugyanaz vagy eltérő, mint az erőforráscsoport helye.
* `resource-name` : (*Nem kötelező*) A helyszíni gép Azure-erőforrás reprezentációja. Ha nem adja meg ezt az értéket, a számítógép állomásneve lesz használva.

A parancssori eszközről az `azcmagent` [Azcmagent](./manage-agent.md)referenciában olvashat bővebben.

>[!NOTE]
>A Windows PowerShell szkript csak az operációs rendszer 64 bites Windows PowerShell.
>

Miután telepíti és konfigurálja az ügynököt az Azure Arc-kompatibilis kiszolgálókhoz való csatlakozásra, lépjen a Azure Portal, és ellenőrizze, hogy a kiszolgáló sikeresen csatlakozott-e. A gépet megtekintheti az [Azure Portalon](https://aka.ms/hybridmachineportal).

![Sikeres kiszolgálókapcsolat](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Következő lépések

- Hibaelhárítási információkat a Csatlakoztatott gép ügynök hibaelhárítása [útmutatóban talál.](troubleshoot-agent-onboard.md)

- Megtudhatja, hogyan kezelheti a gépet a [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gépek vendégkonfigurációja, ellenőrizze, hogy a gép a várt Log Analytics-munkaterületnek jelent-e, hogyan engedélyezheti a Azure Monitor virtuális gépekkel való monitorozást, és még sok más. [](../../governance/policy/concepts/guest-configuration.md) [](../../azure-monitor/vm/vminsights-enable-policy.md)

- További információ a [Log Analytics-ügynökről:](../../azure-monitor/agents/log-analytics-agent.md). A Windowshoz és Linuxhoz használható Log Analytics-ügynökre akkor van szükség, ha az operációs rendszer és a számítási feladatok figyelési adatait az Azure Monitor for VMs-val szeretné gyűjteni, Automation-runbookokkal vagy -funkciókkal szeretné kezelni, például az Update Management-hez, vagy más Azure-szolgáltatások, például a [Azure Security Center.](../../security-center/security-center-introduction.md)
