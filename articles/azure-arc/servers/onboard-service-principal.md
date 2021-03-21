---
title: Hibrid gépek összekötése az Azure-on nagy méretekben
description: Ebből a cikkből megtudhatja, hogyan csatlakoztathatók a gépek az Azure-hoz az Azure arc-kompatibilis kiszolgálók használatával az egyszerű szolgáltatásnév használatával.
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c1ad3d4619896ff46db266789a17bfca80712e70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175940"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Hibrid gépek összekötése az Azure-on nagy méretekben

Az Azure arc-kompatibilis kiszolgálókat több Windows-vagy Linux-gépen is engedélyezheti a környezetben, és a követelményektől függően számos rugalmas lehetőség közül választhat. Az általunk biztosított sablon-parancsfájl segítségével automatizálhatja a telepítés minden lépését, beleértve az Azure arc-hoz való kapcsolódás létrehozását is. Azonban a parancsfájlt interaktív módon kell végrehajtani egy olyan fiókkal, amely emelt szintű engedélyekkel rendelkezik a célszámítógépen és az Azure-ban.

A gépek Azure arc-kompatibilis kiszolgálókhoz való összekapcsolásához használhat egy Azure Active Directory [egyszerű szolgáltatást](../../active-directory/develop/app-objects-and-service-principals.md) , ahelyett, hogy a Kiemelt identitást használja a [számítógép interaktív összekapcsolásához](onboard-portal.md). Az egyszerű szolgáltatásnév egy speciális, korlátozott felügyeleti identitás, amely csak a gépek az Azure-hoz való összekapcsolásához szükséges minimális engedélyeket kapja meg a `azcmagent` parancs használatával. Ez biztonságosabb, mint egy magasabb jogosultsági szintű fiók használata, mint például egy Bérlői rendszergazda, és a hozzáférés-vezérléssel kapcsolatos ajánlott biztonsági eljárásokat követi. Az egyszerű szolgáltatás csak a bevezetéskor használatos, ezért semmilyen más célra nem használható.  

A csatlakoztatott számítógép-ügynök telepítéséhez és konfigurálásához szükséges telepítési módszerek megkövetelik, hogy a használt automatizált módszer rendszergazdai engedélyekkel rendelkezik a gépeken. Linux rendszeren a rendszergazdai fiók és a Windows rendszeren a helyi Rendszergazdák csoport tagjaként.

Mielőtt elkezdené, tekintse át az [előfeltételeket](agent-overview.md#prerequisites) , és győződjön meg arról, hogy az előfizetés és az erőforrások megfelelnek a követelményeknek. A támogatott régiókkal és az egyéb kapcsolódó megfontolásokkal kapcsolatos információkért lásd: [támogatott Azure-régiók](overview.md#supported-regions). Tekintse át a részletes [tervezési útmutatót](plan-at-scale-deployment.md) is a tervezési és üzembe helyezési feltételek, valamint a felügyeleti és monitorozási javaslatok megismeréséhez.  

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Egyszerű szolgáltatás létrehozása a méretezéshez

A [Azure PowerShell](/powershell/azure/install-az-ps) használatával létrehozhat egy egyszerű szolgáltatást a [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) parancsmaggal. Vagy követheti az egyszerű szolgáltatásnév létrehozása című témakörben felsorolt lépéseket a feladat végrehajtásához [a Azure Portal használatával](../../active-directory/develop/howto-create-service-principal-portal.md) .

> [!NOTE]
> Az egyszerű szolgáltatás létrehozása előtt a fióknak a **tulajdonos** vagy a **felhasználói hozzáférés rendszergazdai** szerepkör tagjának kell lennie abban az előfizetésben, amelyet a bevezetéshez használni szeretne. Ha nem rendelkezik megfelelő engedélyekkel a szerepkör-hozzárendelések konfigurálásához, akkor előfordulhat, hogy a szolgáltatásnév létre lett hozva, de nem fogja tudni bevezetni a gépeket.
>

Az egyszerű szolgáltatásnév a PowerShell használatával történő létrehozásához hajtsa végre az alábbi lépéseket.

1. Futtassa az alábbi parancsot. A parancsmag kimenetét [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) egy változóban kell tárolnia, vagy egy későbbi lépésben nem fogja tudni lekérni a szükséges jelszót.

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

2. A változóban tárolt jelszó lekéréséhez `$sp` futtassa a következő parancsot:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. A kimenetben keresse meg a jelszó értéket a mező **jelszava** alatt, és másolja. Keresse meg a **ApplicationId** mező alatti értéket is, és másolja azt is. Mentse őket később egy biztonságos helyen. Ha elfelejti vagy elveszíti a szolgáltatás egyszerű jelszavát, a parancsmag használatával visszaállíthatja [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) .

A következő tulajdonságok értékeit használja a rendszer a paraméternek átadott paraméterekkel `azcmagent` :

* A **ApplicationId** tulajdonság értékét a rendszer a `--service-principal-id` paraméter értékéhez használja.
* A **Password (jelszó** ) tulajdonság értéke az  `--service-principal-secret` ügynök összekapcsolásához használt paraméterhez használatos.

> [!NOTE]
> Ügyeljen arra, hogy az egyszerű szolgáltatásnév **ApplicationId** tulajdonságát használja, ne az **ID** tulajdonságot.
>

Az **Azure Connected Machine** bevezetési szerepkör csak a gép bevezetéséhez szükséges engedélyeket tartalmazza. Hozzárendelheti az egyszerű szolgáltatás engedélyét, hogy a hatóköre tartalmazzon egy erőforráscsoportot vagy egy előfizetést. A szerepkör-hozzárendelés hozzáadásával kapcsolatban tekintse meg az Azure- [szerepkörök hozzárendelése a Azure Portal](../../role-based-access-control/role-assignments-portal.md) vagy [Azure-szerepkörök hozzárendelése az Azure CLI használatával](../../role-based-access-control/role-assignments-cli.md)című témakört.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>A telepítési parancsfájl létrehozása a Azure Portal

A letöltés és telepítés automatizálására szolgáló parancsfájl, valamint az Azure arc-kapcsolat létesítése a Azure Portal érhető el. A folyamat befejezéséhez hajtsa végre a következő lépéseket:

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com).

1. A **kiszolgálók – Azure arc** lapon válassza a **Hozzáadás** lehetőséget a bal felső sarokban.

1. A **módszer kiválasztása** lapon jelölje be a **több kiszolgáló hozzáadása** csempét, majd válassza a **parancsfájl létrehozása** lehetőséget.

1. A **parancsfájl létrehozása** lapon válassza ki azt az előfizetést és erőforráscsoportot, ahol a gépet az Azure-ban szeretné felügyelni. Válasszon ki egy Azure-helyet, ahol a rendszer a számítógép metaadatait tárolja. Ez a hely lehet azonos vagy eltérő, mint az erőforráscsoport helye.

1. Az **Előfeltételek** lapon tekintse át az információkat, majd kattintson a **Tovább gombra: erőforrás részletei**.

1. Az **erőforrás részletei** lapon adja meg a következőket:

    1. Az **erőforráscsoport** legördülő listában válassza ki azt az erőforráscsoportot, amelyből a gépet felügyelni kívánja.
    1. A **régió** legördülő listában válassza ki az Azure-régiót a kiszolgálók metaadatainak tárolásához.
    1. Az **operációs rendszer** legördülő listában válassza ki azt az operációs rendszert, amelyen a parancsfájl konfigurálva van.
    1. Ha a gép egy proxykiszolgálón keresztül kommunikál az internethez, adja meg a proxykiszolgáló IP-címét, vagy azt a nevet és portszámot, amelyet a gép használni fog a proxykiszolgálóhoz való kommunikációhoz. Adja meg az értéket a formátumban `http://<proxyURL>:<proxyport>` .
    1. Válassza a **Next (tovább): hitelesítés** lehetőséget.

1. A **hitelesítés** lapon, az **egyszerű szolgáltatásnév** legördülő listában válassza az **ív-kiszolgálók** lehetőséget.  Ezután válassza a **következő: címkék** lehetőséget.

1. A **címkék** lapon tekintse át a javasolt alapértelmezett **fizikai hely címkéit** , és adjon meg egy értéket, vagy adjon meg egy vagy több **Egyéni címkét** a szabványok támogatásához.

1. Válassza **a tovább lehetőséget: parancsfájl letöltése és futtatása**.

1. A **letöltési és futtatási parancsfájl** lapon tekintse át az összegzési információkat, majd kattintson a **Letöltés** gombra. Ha továbbra is módosításokat kell végeznie, válassza az **előző** lehetőséget.

A Windows esetében a rendszer megkéri, hogy mentse `OnboardingScript.ps1` , és Linux rendszerű `OnboardingScript.sh` számítógépekre.

## <a name="install-the-agent-and-connect-to-azure"></a>Az ügynök telepítése és az Azure-hoz való kapcsolódás

Ha korábban létrehozta a parancsfájl-sablont, telepítheti és konfigurálhatja a csatlakoztatott Machine Agent szolgáltatást több hibrid Linux-és Windows-gépen a szervezet előnyben részesített automatizálási eszközével. A parancsfájl a [hibrid gépek az Azure-ba való összekapcsolásához a Azure Portal](onboard-portal.md) cikkben ismertetett hasonló lépéseket hajtja végre. A különbség az utolsó lépésben történik, ahol az Azure arc kapcsolatát az paranccsal az egyszerű szolgáltatásnév használatával hozza létre `azcmagent` .

Az alábbi beállításokkal konfigurálhatja az `azcmagent` egyszerű szolgáltatáshoz használt parancsot.

* `service-principal-id` : Az egyedi azonosító (GUID), amely az egyszerű szolgáltatásnév alkalmazás-AZONOSÍTÓját jelöli.
* `service-principal-secret` | Az egyszerű szolgáltatásnév jelszava.
* `tenant-id` : Az egyedi azonosító (GUID), amely az Azure AD dedikált példányát jelöli.
* `subscription-id` : Annak az Azure-előfizetésnek az előfizetés-azonosítója (GUID), amelyhez a gépeket szeretné használni.
* `resource-group` : Az erőforráscsoport neve, ahová a csatlakoztatott gépek tartoznak.
* `location` : Tekintse meg a [támogatott Azure-régiókat](overview.md#supported-regions). Ez a hely lehet azonos vagy eltérő, mint az erőforráscsoport helye.
* `resource-name` : (Nem *kötelező*) a helyszíni gép Azure-beli erőforrás-ábrázolásához használatos. Ha nem megadja ezt az értéket, a rendszer a gép állomásnevét használja.

A parancssori eszközzel kapcsolatos további információkért tekintse meg `azcmagent` a [Azcmagent-hivatkozás](./manage-agent.md)áttekintését.

>[!NOTE]
>A Windows PowerShell-parancsfájl csak a Windows PowerShell 64 bites verziójáról támogatja a futtatást.
>

Miután telepítette az ügynököt, és konfigurálta az Azure arc-kompatibilis kiszolgálókhoz való csatlakozáshoz, lépjen a Azure Portal, és ellenőrizze, hogy a kiszolgáló sikeresen csatlakozott-e. A gépet megtekintheti az [Azure Portalon](https://aka.ms/hybridmachineportal).

![Sikeres kiszolgálói kapcsolatok](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Következő lépések

- A hibaelhárítási információ a [csatlakoztatott gép ügynökének hibaelhárítása című útmutatóban](troubleshoot-agent-onboard.md)található.

- Megtudhatja, hogyan kezelheti a gépet a [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, lehetővé teszi a figyelést a [virtuális gépekkel Azure monitor](../../azure-monitor/vm/vminsights-enable-policy.md)és még sok minden más.

- További információ a [log Analytics-ügynökről](../../azure-monitor/agents/log-analytics-agent.md). A Windows és a Linux rendszerhez készült Log Analytics ügynökre akkor van szükség, ha az operációs rendszer és a számítási feladatok figyelésére szolgáló adatokat Azure Monitor for VMs használatával szeretné összegyűjteni, az Automation-runbookok vagy-szolgáltatásokkal, például a Update Managementekkel, vagy más Azure-szolgáltatásokkal (például [Azure Security Center](../../security-center/security-center-introduction.md)
