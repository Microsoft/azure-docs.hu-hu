---
title: Azure AD Domain Services erőforrás-erdő létrehozása a Azure PowerShell használatával | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure Active Directory Domain Services erőforrás-erdőt és egy kimenő erdőt a helyszíni Active Directory Domain Services-környezethez Azure PowerShell használatával.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: justinha
ms.openlocfilehash: ebfc2476b7955b926f86094de03973155386eb8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619967"
---
# <a name="create-an-azure-active-directory-domain-services-resource-forest-and-outbound-forest-trust-to-an-on-premises-domain-using-azure-powershell"></a>Hozzon létre egy Azure Active Directory Domain Services erőforrás-erdőt és a kimenő erdőszintű megbízhatóságot a helyszíni tartományba Azure PowerShell

Olyan környezetekben, ahol nem lehet szinkronizálni a jelszavakat, vagy ha olyan felhasználókkal rendelkezik, akik kizárólag intelligens kártyákkal jelentkeznek be, így nem ismerik a jelszavukat, használhat egy erőforrás-erdőt Azure Active Directory Domain Services (Azure AD DS). Az erőforrás-erdő egyirányú kimenő bizalmi kapcsolatot használ az Azure AD DS egy vagy több helyszíni AD DS környezetbe. Ez a megbízhatósági kapcsolat lehetővé teszi a felhasználók, az alkalmazások és a számítógépek számára a helyszíni tartományon belüli hitelesítést az Azure AD DS felügyelt tartományból. Egy erőforrás-erdőben a helyszíni jelszavak kivonatait soha nem szinkronizálja a rendszer.

![Az Azure AD DS és a helyszíni AD DS közötti erdőszintű megbízhatóság diagramja](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Azure-AD DS erőforrás-erdő létrehozása Azure PowerShell használatával
> * Egyirányú kimenő erdőszintű megbízhatósági kapcsolat létrehozása a felügyelt tartományban a Azure PowerShell használatával
> * A DNS konfigurálása helyszíni AD DS környezetben a felügyelt tartományi kapcsolatok támogatásához
> * Egyirányú bejövő erdőszintű megbízhatósági kapcsolat létrehozása helyszíni AD DS környezetben
> * A hitelesítés és az erőforrás-hozzáférés megbízhatósági kapcsolatának tesztelése és ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

> [!IMPORTANT]
> A felügyelt tartományi erőforrás-erdők jelenleg nem támogatják az Azure HDInsight vagy a Azure Files. Az alapértelmezett felügyelt tartományi felhasználói erdők mindkét további szolgáltatást támogatják.

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].

* Az Azure PowerShell telepítése és konfigurálása.
    * Ha szükséges, kövesse az utasításokat a [Azure PowerShell modul telepítéséhez és az Azure-előfizetéshez való kapcsolódáshoz](/powershell/azure/install-az-ps).
    * Győződjön meg róla, hogy bejelentkezik az Azure-előfizetésbe a [AzAccount][Connect-AzAccount] parancsmag használatával.
* Az Azure AD PowerShell telepítése és konfigurálása.
    * Ha szükséges, kövesse az [Azure ad PowerShell-modul telepítésére és az Azure ad-hez való kapcsolódásra](/powershell/azure/active-directory/install-adv2)vonatkozó utasításokat.
    * Győződjön meg arról, hogy bejelentkezik az Azure AD-bérlőbe a [AzureAD][Connect-AzureAD] parancsmag használatával.
* Az Azure AD DS engedélyezéséhez *globális rendszergazdai* jogosultságok szükségesek az Azure ad-bérlőben.
* Az Azure-előfizetésben *közreműködő* jogosultságokkal kell rendelkeznie a szükséges Azure AD DS-erőforrások létrehozásához.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben a cikkben a Azure Portal használatával hozza létre és konfigurálja a kimenő erdő megbízhatóságát egy felügyelt tartományból. Első lépésként jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="deployment-process"></a>Üzembehelyezési folyamat

Ez egy többrészes folyamat, amely egy felügyelt tartományi erőforrás-erdőt és egy helyszíni AD DS megbízhatósági kapcsolatot hoz létre. A következő magas szintű lépések felépítik a megbízható, hibrid környezetet:

1. Felügyelt tartományi szolgáltatásnév létrehozása.
1. Hozzon létre egy felügyelt tartományi erőforrás-erdőt.
1. Hibrid hálózati kapcsolat létrehozása helyek közötti VPN vagy Express Route használatával.
1. Hozza létre a megbízhatósági kapcsolat felügyelt tartományának oldalát.
1. Hozza létre a megbízhatósági kapcsolat helyszíni AD DS oldalát.

Mielőtt elkezdené, győződjön meg róla, hogy tisztában van a [hálózati megfontolásokkal, az erdő elnevezésével és a DNS-követelményekkel](tutorial-create-forest-trust.md#networking-considerations). A felügyelt tartomány erdő neve nem módosítható a telepítés után.

## <a name="create-the-azure-ad-service-principal"></a>Az Azure AD egyszerű szolgáltatás létrehozása

Az Azure AD DS használatához az Azure AD-ből adatokat kell szinkronizálni a szolgáltatással. Ezt a rendszerbiztonsági tag létrehozása előtt létre kell hozni az Azure AD-bérlőben, mielőtt létrehozta a felügyelt tartományi erőforrás-erdőt.

Hozzon létre egy Azure AD egyszerű szolgáltatást az Azure AD DS számára a kommunikációhoz és a hitelesítéshez. A rendszer egy adott alkalmazásspecifikus azonosítót használ a *6BA9A5D4-8456-4118-B521-9C5CA10CDF84* azonosítóval rendelkező *tartományvezérlői szolgáltatások* névvel. Ne módosítsa az alkalmazás AZONOSÍTÓját.

Hozzon létre egy Azure AD-szolgáltatásnevet a [New-azureadserviceprincipal parancsmagot][New-AzureADServicePrincipal] parancsmag használatával:

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

## <a name="create-a-managed-domain-resource-forest"></a>Felügyelt tartományhoz tartozó erőforrás-erdő létrehozása

Felügyelt tartományi erőforrás-erdő létrehozásához használja a `New-AzureAaddsForest` parancsfájlt. Ez a szkript a felügyelt tartományi erőforrás-erdők létrehozását és felügyeletét támogató parancsok szélesebb körének részét képezi, beleértve az egyirányú kötött erdő létrehozását a következő szakaszban. Ezek a szkriptek a [PowerShell-Galéria](https://www.powershellgallery.com/) érhetők el, és digitálisan alá vannak írva az Azure ad Engineering csapata.

1. Először hozzon létre egy erőforráscsoportot a [New-AzResourceGroup][New-AzResourceGroup] parancsmag használatával. A következő példában az erőforráscsoport neve *myResourceGroup* , és a *westus* régióban jön létre. Saját név és kívánt régió használata:

    ```azurepowershell
    New-AzResourceGroup `
      -Name "myResourceGroup" `
      -Location "WestUS"
    ```

1. Telepítse a `New-AaddsResourceForestTrust` parancsfájlt a [PowerShell-Galéria][powershell-gallery] az [install-script][Install-Script] parancsmag használatával:

    ```powershell
    Install-Script -Name New-AaddsResourceForestTrust
    ```

1. Tekintse át a parancsfájlhoz szükséges következő paramétereket `New-AzureAaddsForest` . Győződjön meg arról, hogy az előfeltétel **Azure PowerShell** és az **Azure ad PowerShell** -modulok is rendelkezésre állnak. Győződjön meg arról, hogy megtervezte a virtuális hálózati követelményeket az alkalmazás és a helyszíni kapcsolat biztosításához.

    | Name                         | Parancsfájl paramétere          | Leírás |
    |:-----------------------------|---------------------------|:------------|
    | Előfizetés                 | *-azureSubscriptionId*    | Az Azure AD DS számlázáshoz használt előfizetés-azonosító. A [Get-AzureRMSubscription][Get-AzureRMSubscription] parancsmag használatával lekérheti az előfizetések listáját. |
    | Erőforráscsoport               | *-aaddsResourceGroupName* | A felügyelt tartomány és a kapcsolódó erőforrások erőforráscsoport neve. |
    | Hely                     | *-aaddsLocation*          | Az Azure-régió, amely a felügyelt tartományt üzemelteti. Az elérhető régiók esetében tekintse meg [Az Azure AD DS támogatott régiói](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all) című témakört. |
    | Azure AD DS-rendszergazda    | *-aaddsAdminUser*         | Az első felügyelt tartományi rendszergazda egyszerű felhasználóneve. Ennek a fióknak a Azure Active Directory meglévő felhőalapú felhasználói fiókjának kell lennie. A felhasználó és a parancsfájlt futtató felhasználó hozzá lesz adva a *HRE DC rendszergazdák* csoportjához. |
    | Azure AD DS tartomány neve      | *-aaddsDomainName*        | A felügyelt tartomány teljes tartományneve az erdő nevének kiválasztására vonatkozó korábbi útmutatás alapján. |

    A `New-AzureAaddsForest` szkript létrehozhatja az Azure-beli virtuális hálózatot és az azure AD DS alhálózatot, ha ezek az erőforrások még nem léteznek. A parancsfájl opcionálisan létrehozhatja a számítási feladatok alhálózatait, ha meg van adva:

    | Name                              | Parancsfájl paramétere                  | Leírás |
    |:----------------------------------|:----------------------------------|:------------|
    | Virtuális hálózat neve              | *-aaddsVnetName*                  | A felügyelt tartomány virtuális hálózatának neve.|
    | Címtér                     | *-aaddsVnetCIDRAddressSpace*      | A virtuális hálózat CIDR jelölése (a virtuális hálózat létrehozásakor).|
    | Azure AD DS alhálózat neve           | *-aaddsSubnetName*                | A felügyelt tartományt üzemeltető *aaddsVnetName* virtuális hálózat alhálózatának neve. Ne helyezzen üzembe saját virtuális gépeket és munkaterheléseket ebbe az alhálózatba. |
    | Azure AD DS-címtartomány         | *-aaddsSubnetCIDRAddressRange*    | Az alhálózati címtartomány a CIDR-ben a HRE DS-példányhoz, például *192.168.1.0/24*. A címtartományt a virtuális hálózat címtartomány és más alhálózatok között kell tartalmaznia. |
    | Számítási feladatok alhálózatának neve (nem kötelező)   | *-workloadSubnetName*             | A *aaddsVnetName* virtuális hálózatban a saját alkalmazások számítási feladataihoz létrehozandó alhálózat nem kötelezően megadandó neve. Virtuális gépek és alkalmazások, valamint az Azure-beli virtuális hálózatokhoz is csatlakoztathatók. |
    | Munkaterhelés-címtartomány (nem kötelező) | *-workloadSubnetCIDRAddressRange* | Opcionális alhálózat-címtartomány az alkalmazás számítási feladataihoz (például *192.168.2.0/24*) CIDR. A címtartományt a virtuális hálózat címtartomány és más alhálózatok között kell tartalmaznia.|

1. Most hozzon létre egy felügyelt tartományi erőforrás-erdőt a `New-AzureAaaddsForest` szkript használatával. Az alábbi példa egy *addscontoso.com* nevű erdőt hoz létre, és létrehoz egy munkaterhelés-alhálózatot. Adja meg a saját paraméterek nevét és IP-címtartományt, vagy a meglévő virtuális hálózatokat.

    ```azurepowershell
    New-AzureAaddsForest `
        -azureSubscriptionId <subscriptionId> `
        -aaddsResourceGroupName "myResourceGroup" `
        -aaddsLocation "WestUS" `
        -aaddsAdminUser "contosoadmin@contoso.com" `
        -aaddsDomainName "aaddscontoso.com" `
        -aaddsVnetName "myVnet" `
        -aaddsVnetCIDRAddressSpace "192.168.0.0/16" `
        -aaddsSubnetName "AzureADDS" `
        -aaddsSubnetCIDRAddressRange "192.168.1.0/24" `
        -workloadSubnetName "myWorkloads" `
        -workloadSubnetCIDRAddressRange "192.168.2.0/24"
    ```

    A felügyelt tartományi erőforrás-erdő és a támogató erőforrások létrehozása elég időbe telik. A szkript befejezésének engedélyezése. Folytassa a következő szakasszal a helyszíni hálózati kapcsolat konfigurálásával, miközben az Azure AD-erőforrás erdője a háttérben található.

## <a name="configure-and-validate-network-settings"></a>Hálózati beállítások konfigurálása és érvényesítése

Mivel a felügyelt tartomány továbbra is üzembe helyezi, konfigurálja és érvényesíti a hibrid hálózati kapcsolatot a helyszíni adatközponthoz. Szükség van egy felügyeleti virtuális gépre is, amelyet a felügyelt tartományhoz kell használni a rendszeres karbantartáshoz. Előfordulhat, hogy a hibrid kapcsolatok némelyike már létezik a környezetben, vagy a kapcsolatok konfigurálásához a csapat más tagjaival is dolgoznia kell.

Mielőtt elkezdené, győződjön meg róla, hogy tisztában van a [hálózati megfontolásokkal és javaslatokkal](tutorial-create-forest-trust.md#networking-considerations).

1. Hozzon létre hibrid kapcsolatot a helyszíni hálózattal az Azure-ban egy Azure VPN vagy Azure ExpressRoute-kapcsolat használatával. A hibrid hálózati konfiguráció meghaladja a dokumentáció hatókörét, és előfordulhat, hogy már létezik a környezetében. Az egyes forgatókönyvekkel kapcsolatos részletekért tekintse meg a következő cikkeket:

    * [Azure-helyek közötti VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
    * Az [Azure ExpressRoute áttekintése](../expressroute/expressroute-introduction.md).

    > [!IMPORTANT]
    > Ha közvetlenül a felügyelt tartomány virtuális hálózatához hozza létre a kapcsolódást, használjon külön átjáró-alhálózatot. Ne hozza létre az átjárót a felügyelt tartomány alhálózatában.

1. Felügyelt tartomány felügyeletéhez létre kell hoznia egy felügyeleti virtuális gépet, csatlakoztatni kell a felügyelt tartományhoz, és telepítenie kell a szükséges AD DS felügyeleti eszközöket.

    A felügyelt tartományi erőforrás-erdő üzembe helyezése közben [hozzon létre egy Windows Server rendszerű virtuális gépet](./join-windows-vm.md) , majd [telepítse a Core AD DS felügyeleti eszközöket](./tutorial-create-management-vm.md) a szükséges felügyeleti eszközök telepítéséhez. Várja meg a felügyeleti virtuális gép a felügyelt tartományhoz való csatlakoztatását, amíg a tartomány sikeres telepítése után a következő lépések egyike be nem fejeződik.

1. Ellenőrizze a hálózati kapcsolatot a helyszíni hálózat és az Azure-beli virtuális hálózat között.

    * Győződjön meg arról, hogy a helyszíni tartományvezérlő a vagy a távoli asztal használatával tud csatlakozni a felügyelt virtuális géphez `ping` , például:.
    * Győződjön meg arról, hogy a felügyeleti virtuális gép képes csatlakozni a helyszíni tartományvezérlőkre, majd egy olyan segédprogramot használva, mint például a `ping` .

1. A Azure Portal keresse meg és válassza a **Azure ad Domain Services** lehetőséget. Válassza ki a felügyelt tartományt (például *aaddscontoso.com* ), és várjon, amíg a jelentés **futtatása folyamatban** állapotú.

    A futtatásakor [frissítse az Azure-beli virtuális hálózat DNS-beállításait](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network) , majd [engedélyezze az Azure AD DS felhasználói fiókjainak](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) a felügyelt tartományi erőforrás-erdő konfigurációinak véglegesítését.

1. Jegyezze fel az Áttekintés lapon megjelenő DNS-címeket. Ezekre a címekre akkor van szükség, amikor a megbízhatósági kapcsolat helyszíni Active Directory oldalát konfigurálja a következő szakaszban.
1. Indítsa újra a felügyeleti virtuális gépet, hogy megkapja az új DNS-beállításokat, majd [csatlakoztassa a virtuális gépet a felügyelt tartományhoz](join-windows-vm.md#join-the-vm-to-the-managed-domain).
1. Miután a felügyeleti virtuális gép csatlakoztatva lett a felügyelt tartományhoz, csatlakozzon újra a távoli asztal használatával.

    A parancssorból használja a `nslookup` és a felügyelt tartomány erőforrás-erdő nevét, hogy érvényesítse az erőforrás-erdő névfeloldását.

    ```console
    nslookup aaddscontoso.com
    ```

    A parancsnak két IP-címet kell visszaadnia az erőforrás-erdőben.

## <a name="create-the-forest-trust"></a>Az erdőszintű megbízhatóság létrehozása

Az erdőszintű megbízhatósági kapcsolat két részből áll: a felügyelt tartományi erőforrás erdőben lévő egyirányú kimenő erdőszintű megbízhatósági kapcsolattal, valamint a helyszíni AD DS erdőben lévő egyirányú bejövő erdőszintű megbízhatósággal. A megbízhatósági kapcsolat mindkét oldalát manuálisan hozza létre. Ha mindkét oldal létrejött, a felhasználók és az erőforrások sikeresen hitelesíthetők az erdőszintű megbízhatóság használatával. A felügyelt tartományi erőforrás-erdők akár 5 1 irányú kimenő erdőszintű megbízhatóságot is támogatnak a helyszíni erdőkben.

### <a name="create-the-managed-domain-side-of-the-trust-relationship"></a>A megbízhatósági kapcsolat felügyelt tartománya oldalának létrehozása

A `Add-AaddsResourceForestTrust` parancsfájl használatával hozza létre a megbízhatósági kapcsolat felügyelt tartományának oldalát. Először telepítse a `Add-AaddsResourceForestTrust` parancsfájlt a [PowerShell-Galéria][powershell-gallery] az [install-script][Install-Script] parancsmag használatával:

```powershell
Install-Script -Name Add-AaddsResourceForestTrust
```

Most adja meg a parancsfájlt a következő információkkal:

| Name                               | Parancsfájl paramétere     | Leírás |
|:-----------------------------------|:---------------------|:------------|
| Azure AD DS tartomány neve            | *-ManagedDomainFqdn* | A felügyelt tartomány teljes tartományneve, például *aaddscontoso.com* |
| Helyszíni AD DS tartomány neve      | *-TrustFqdn*         | A megbízható erdő teljes tartományneve, például *onprem.contoso.com* |
| Megbízhatósági kapcsolat rövid neve                | *-TrustFriendlyName* | A megbízhatósági kapcsolat rövid neve. |
| Helyszíni AD DS DNS IP-címei | *-TrustDnsIPs*       | A listán szereplő megbízható tartomány DNS-kiszolgálói IPv4-címeinek vesszővel tagolt listája. |
| Megbízhatósági kapcsolat jelszava                     | *-TrustPassword*     | A megbízhatósági kapcsolathoz tartozó összetett jelszó. Ezt a jelszót akkor is meg kell adni, ha az egyirányú bejövő megbízhatóságot a helyszíni AD DSon hozza létre. |
| Hitelesítő adatok                        | *– Hitelesítő adatok*       | Az Azure-ban való hitelesítéshez használt hitelesítő adatok. A felhasználónak a *HRE DC-rendszergazdák csoportban* kell lennie. Ha nincs megadva, a parancsfájl kéri a hitelesítést. |

A következő példa létrehoz egy *myAzureADDSTrust* nevű megbízhatósági kapcsolatot a *onprem.contoso.com*. Használja a saját paraméterek nevét és jelszavát:.

```azurepowershell
Add-AaddsResourceForestTrust `
    -ManagedDomainFqdn "aaddscontoso.com" `
    -TrustFqdn "onprem.contoso.com" `
    -TrustFriendlyName "myAzureADDSTrust" `
    -TrustDnsIPs "10.0.1.10,10.0.1.11" `
    -TrustPassword <complexPassword>
```

> [!IMPORTANT]
> Jegyezze meg a megbízhatósági jelszót. A bizalmi kapcsolat helyszíni oldalának létrehozásakor ugyanazt a jelszót kell használnia.

## <a name="configure-dns-in-the-on-premises-domain"></a>A DNS konfigurálása a helyszíni tartományban

A felügyelt tartomány helyszíni környezetből való megfelelő feloldásához lehetséges, hogy továbbítókat kell hozzáadnia a meglévő DNS-kiszolgálókhoz. Ha nem konfigurálja a helyszíni környezetet a felügyelt tartománysal való kommunikációra, hajtsa végre a következő lépéseket a helyszíni AD DS tartomány felügyeleti munkaállomásán:

1. Válassza a **Start | Felügyeleti eszközök | DNS**
1. Kattintson a jobb gombbal a DNS-kiszolgáló, például a *myAD01* lehetőségre, majd válassza a **Tulajdonságok** lehetőséget.
1. Válassza a **továbbítók**, majd a **Szerkesztés** lehetőséget a további továbbítók hozzáadásához.
1. Adja hozzá a felügyelt tartomány IP-címeit, például a *10.0.1.4* és a *10.0.1.5*.
1. Helyi parancssorból ellenőrizze a névfeloldást a felügyelt tartomány erőforrás-erdő tartománynevének **nslookup** paranccsal. Például a `Nslookup aaddscontoso.com` felügyelt tartományi erőforrás-erdő két IP-címét kell visszaadnia.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Bejövő erdőszintű megbízhatóság létrehozása a helyszíni tartományban

A helyszíni AD DS tartománynak rendelkeznie kell egy bejövő erdőszintű megbízhatósági kapcsolattal a felügyelt tartományhoz. Ezt a megbízhatóságot manuálisan kell létrehozni a helyszíni AD DS tartományban, nem hozható létre a Azure Portalból.

A helyi AD DS tartomány bejövő megbízhatóságának konfigurálásához hajtsa végre az alábbi lépéseket a helyszíni AD DS tartomány felügyeleti munkaállomásáról:

1. Válassza a **Start | Felügyeleti eszközök | Tartományok és megbízhatósági kapcsolatok Active Directory**
1. Kattintson a jobb gombbal a tartomány, például a *onprem.contoso.com*, majd a **Tulajdonságok** elemre.
1. Válassza a **Megbízhatóságok** lapot, majd az **új megbízhatóság** elemet.
1. Adja meg a felügyelt tartomány nevét, például *aaddscontoso.com*, majd kattintson a **Next (tovább** ) gombra.
1. Válassza az **erdőszintű megbízhatóság** létrehozása lehetőséget, majd hozzon létre egy **módszert: bejövő** megbízhatóság.
1. Válassza **ezt a tartományt csak** a megbízhatósági kapcsolat létrehozásához. A következő lépésben létrehozza a megbízhatóságot a felügyelt tartomány Azure Portalban.
1. Válassza az **erdőszintű hitelesítés** használata lehetőséget, majd adja meg és erősítse meg a megbízhatósági jelszót. Ugyanezt a jelszót is megadta a Azure Portal a következő szakaszban.
1. Lépjen be a következő néhány Windows alapértelmezett beállításokkal, majd válassza a nem lehetőséget **, ne erősítse meg a kimenő megbízhatóságot**. A megbízhatósági kapcsolat nem érvényesíthető, mert a felügyelt tartomány erőforrás-erdőhöz delegált rendszergazdai fiókja nem rendelkezik a szükséges engedélyekkel. Ez a működésmód szándékos.
1. Válassza a **Befejezés** lehetőséget

## <a name="validate-resource-authentication"></a>Erőforrás-hitelesítés ellenőrzése

A következő gyakori forgatókönyvekkel ellenőrizheti, hogy az erdőszintű megbízhatóság helyesen hitelesíti-e a felhasználókat és az erőforrásokhoz való hozzáférést:

* [Helyszíni felhasználói hitelesítés az Azure AD DS erőforrás-erdőben](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Erőforrásokhoz való hozzáférés az Azure AD DS erőforrás-erdőben helyszíni felhasználó használatával](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Fájl-és nyomtatómegosztás engedélyezése](#enable-file-and-printer-sharing)
    * [Biztonsági csoport létrehozása és Tagok hozzáadása](#create-a-security-group-and-add-members)
    * [Fájlmegosztás létrehozása erdők közötti hozzáféréshez](#create-a-file-share-for-cross-forest-access)
    * [Erdők közötti hitelesítés ellenőrzése erőforráshoz](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Helyszíni felhasználói hitelesítés az Azure AD DS erőforrás-erdőben

A felügyelt tartományhoz tartozó erőforrás-tartományhoz csatlakoztatva kell lennie a Windows Server rendszerű virtuális gépnek. Ezzel a virtuális géppel ellenőrizheti, hogy a helyszíni felhasználó tud-e hitelesítést végezni egy virtuális gépen.

1. Kapcsolódjon a felügyelt tartomány erőforrás-erdőhöz csatlakozó Windows Server rendszerű virtuális géphez a Távoli asztal és a felügyelt tartományi rendszergazdai hitelesítő adataival. Ha hálózati szintű hitelesítés (NLA) hibaüzenetet kap, ellenőrizze, hogy a használt felhasználói fiók nem tartományi felhasználói fiók-e.

    > [!TIP]
    > A Azure AD Domain Serviceshoz csatlakozó virtuális gépekhez való biztonságos csatlakozáshoz használhatja az Azure-beli [megerősített gazdagép szolgáltatást](../bastion/bastion-overview.md) a támogatott Azure-régiókban.

1. Nyisson meg egy parancssort, és a `whoami` parancs használatával jelenítse meg az aktuálisan hitelesített felhasználó megkülönböztető nevét:

    ```console
    whoami /fqdn
    ```

1. A `runas` parancs használatával hitelesítheti magát a helyszíni tartományból. A következő parancsban cserélje le a `userUpn@trusteddomain.com` elemet a megbízható helyszíni tartomány felhasználójának egyszerű felhasználónevére. A parancs a felhasználó jelszavát kéri:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Ha a hitelesítés sikeres, megnyílik egy új parancssori ablak. Az új parancssor címe tartalmazza `running as userUpn@trusteddomain.com` .
1. Az `whoami /fqdn` új parancssorban a helyi Active Directory a hitelesített felhasználó megkülönböztető nevének megtekintésére használható.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Erőforrásokhoz való hozzáférés az Azure AD DS erőforrás-erdőben helyszíni felhasználó használatával

A felügyelt tartományi erőforrás-erdőhöz csatlakoztatott Windows Server rendszerű virtuális gép használatával tesztelheti azt a forgatókönyvet, ahol a felhasználók hozzáférhetnek az erőforrás-erdőben található erőforrásokhoz, amikor a helyszíni tartomány számítógépeiről hitelesítik magukat a helyi tartományban lévő felhasználóktól. Az alábbi példák bemutatják, hogyan hozhat létre és tesztelheti a különböző gyakori forgatókönyveket.

#### <a name="enable-file-and-printer-sharing"></a>Fájl-és nyomtatómegosztás engedélyezése

1. Kapcsolódjon a felügyelt tartomány erőforrás-erdőhöz csatlakozó Windows Server rendszerű virtuális géphez a Távoli asztal és a felügyelt tartományi rendszergazdai hitelesítő adataival. Ha hálózati szintű hitelesítés (NLA) hibaüzenetet kap, ellenőrizze, hogy a használt felhasználói fiók nem tartományi felhasználói fiók-e.

    > [!TIP]
    > A Azure AD Domain Serviceshoz csatlakozó virtuális gépekhez való biztonságos csatlakozáshoz használhatja az Azure-beli [megerősített gazdagép szolgáltatást](../bastion/bastion-overview.md) a támogatott Azure-régiókban.

1. Nyissa meg a **Windows-beállításokat**, majd keresse meg és válassza ki a **hálózati és megosztási központot**.
1. Válassza a **Speciális megosztási beállítások módosítása** lehetőséget.
1. A **tartományi profil** területen válassza a **fájl-és nyomtatómegosztás bekapcsolása** , majd a **módosítások mentése** lehetőséget.
1. **A hálózati és megosztási központ** bezárásához.

#### <a name="create-a-security-group-and-add-members"></a>Biztonsági csoport létrehozása és Tagok hozzáadása

1. Nyissa meg az **Active Directory – felhasználók és számítógépek** beépülő modult.
1. Kattintson a jobb gombbal a tartománynévre, válassza az **új**, majd a **szervezeti egység** elemet.
1. A név mezőbe írja be a *LocalObjects* nevet, majd kattintson az **OK gombra**.
1. Válassza ki, majd kattintson a jobb gombbal a **LocalObjects** elemre a navigációs ablaktáblán. Válassza az **új** , majd a **csoport** lehetőséget.
1. Írja  be a FileServerAccess **nevet a csoport neve** mezőbe. A **Csoport hatóköre** területen válassza a **tartomány helyi** lehetőséget, majd kattintson **az OK gombra**.
1. A tartalom ablaktáblán kattintson duplán a **FileServerAccess** elemre. Válassza a **tagok** lehetőséget, válassza a **Hozzáadás**, majd a **helyszínek** lehetőséget.
1. Válassza ki a helyszíni Active Directory a **hely** nézetből, majd kattintson **az OK gombra**.
1. Írja be a *tartományi felhasználók* értéket az **adja meg a kijelölendő objektumok nevét** mezőbe. Jelölje be a Névellenőrzés **jelölőnégyzetet**, adja meg a helyszíni Active Directory hitelesítő adatait, majd kattintson **az OK gombra**.

    > [!NOTE]
    > Meg kell adnia a hitelesítő adatokat, mert a megbízhatósági kapcsolat csak egy módszer. Ez azt jelenti, hogy a felügyelt tartományba tartozó felhasználók nem férhetnek hozzá az erőforrásokhoz, vagy megkereshetik a megbízható (helyszíni) tartományban lévő felhasználókat vagy csoportokat.

1. A helyszíni Active Directory **tartományi felhasználók** csoportjának a **FileServerAccess** csoport tagjának kell lennie. A csoport mentéséhez és az ablak bezárásához kattintson **az OK gombra** .

#### <a name="create-a-file-share-for-cross-forest-access"></a>Fájlmegosztás létrehozása erdők közötti hozzáféréshez

1. A felügyelt tartomány erőforrás-erdőhöz csatlakozó Windows Server rendszerű virtuális gépen hozzon létre egy mappát, és adja meg a nevet (például *CrossForestShare*).
1. Kattintson a jobb gombbal a mappára, és válassza a **Tulajdonságok** lehetőséget.
1. Válassza a **Biztonság** fület, majd kattintson a **Szerkesztés** elemre.
1. A *CrossForestShare engedélyei* párbeszédpanelen válassza a **Hozzáadás** lehetőséget.
1. Írja be a *FileServerAccess* **nevet az írja be a kijelölendő objektumok nevét**, majd kattintson **az OK gombra**.
1. A **csoportok vagy a felhasználónevek** listából válassza a *FileServerAccess* lehetőséget. A **FileServerAccess engedélyei** listán válassza az *Engedélyezés lehetőséget* a **módosítási** és **írási** engedélyekhez, majd kattintson **az OK gombra**.
1. Válassza a **megosztás** fület, majd kattintson a speciális megosztás elemre. **..**
1. Válassza a **mappa megosztása** lehetőséget, majd adjon meg egy emlékezetes nevet a fájlmegosztás számára a **megosztás nevében** , például *CrossForestShare*.
1. Válassza az **Engedélyek** lehetőséget. Az **engedélyek mindenki** számára listában válassza az **Engedélyezés lehetőséget** a **módosítási** engedélyhez.
1. Kattintson kétszer **az OK** , majd a **Bezárás** gombra.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Erdők közötti hitelesítés ellenőrzése erőforráshoz

1. Jelentkezzen be a helyszíni Active Directoryhoz csatlakoztatott Windows-számítógép használatával a helyszíni Active Directory felhasználói fiókjával.
1. A **Windows Intéző** használatával kapcsolódjon a létrehozott megosztáshoz a teljes állomásnévvel és a megosztással, például: `\\fs1.aaddscontoso.com\CrossforestShare` .
1. Az írási engedély ellenőrzéséhez kattintson a jobb gombbal a mappára, válassza az **új**, majd a **szöveges dokumentum** lehetőséget. Használja az alapértelmezett név **új szöveges dokumentumot**.

    Ha az írási engedélyek helyesen vannak beállítva, létrejön egy új szöveges dokumentum. A következő lépésekkel megnyithatja, szerkesztheti és szükség szerint törölheti a fájlt.
1. Az olvasási engedély ellenőrzéséhez nyissa meg az **új szöveges dokumentumot**.
1. A módosítási engedély érvényességének ellenőrzéséhez adjon hozzá szöveget a fájlhoz, és lépjen be a **Jegyzettömbbe**. Amikor a rendszer kéri a módosítások mentését, válassza a **Mentés** lehetőséget.
1. A törlési engedély ellenőrzéséhez kattintson a jobb gombbal az **új szöveges dokumentum** elemre, és válassza a **Törlés** lehetőséget. A fájl törlésének megerősítéséhez válassza az **Igen** lehetőséget.

## <a name="update-or-remove-outbound-forest-trust"></a>Kimenő erdő megbízhatóságának frissítése vagy eltávolítása

Ha egy meglévő egyirányú kimenő erdőt kell frissítenie a felügyelt tartományból, használhatja a `Get-AaddsResourceForestTrusts` és a `Set-AaddsResourceForestTrust` szkripteket. Ezek a parancsfájlok segítenek olyan forgatókönyvekben, amelyekben frissíteni szeretné az erdőszintű megbízhatósági kapcsolat felhasználóbarát nevét vagy megbízhatósági jelszavát. Egy egyirányú kimenő megbízhatósági kapcsolatnak a felügyelt tartományból való eltávolításához használhatja a `Remove-AaddsResourceForestTrust` parancsfájlt. Az egyirányú bejövő erdőszintű megbízhatósági kapcsolatot manuálisan kell eltávolítani a helyszíni AD DS erdőben.

### <a name="update-a-forest-trust"></a>Erdőszintű megbízhatósági kapcsolat frissítése

Normál működés esetén a felügyelt tartományi erdő és a helyszíni erdő egy normál jelszó-frissítési folyamatot egyeztet egymás között. Ez a normális AD DS megbízhatósági kapcsolat biztonsági folyamatának része. Nem kell manuálisan elforgatni a megbízhatósági jelszót, kivéve, ha a megbízhatósági kapcsolat hibát észlelt, és manuálisan szeretné visszaállítani egy ismert jelszót. További információ: [megbízható tartománybeli objektum jelszavának módosítása](concepts-forest-trust.md#tdo-password-changes).

Az alábbi példák bemutatják, hogyan frissíthet egy meglévő megbízhatósági kapcsolatot, ha manuálisan szeretné visszaállítani a kimenő megbízhatósági jelszót:

1. Telepítse a `Get-AaddsResourceForestTrusts` és a `Set-AaddsResourceForestTrust` parancsfájlokat a [PowerShell-Galéria][powershell-gallery] az [install-script][Install-Script] parancsmag használatával:

    ```powershell
    Install-Script -Name Get-AaddsResourceForestTrusts,Set-AaddsResourceForestTrust
    ```

1. A meglévő megbízhatósági kapcsolatok frissítése előtt először kérje le a megbízhatósági erőforrást a `Get-AaddsResourceForestTrusts` parancsfájl használatával. A következő példában a meglévő megbízhatósági kapcsolat hozzá van rendelve egy *existingTrust* nevű objektumhoz. A frissítéshez adja meg a saját felügyelt tartományi erdő nevét és a helyszíni erdő nevét:

    ```powershell
    $existingTrust = Get-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

1. A meglévő megbízhatósági jelszó frissítéséhez használja a `Set-AaddsResourceForestTrust` parancsfájlt. Adja meg az előző lépésben meglévő megbízhatósági objektumot, majd az új megbízhatósági kapcsolat jelszavát. A PowerShell nem kényszeríti ki a jelszó bonyolultságát, ezért ügyeljen arra, hogy biztonságos jelszót hozzon igénybe a környezetéhez.

    ```powershell
    Set-AaddsResourceForestTrust `
        -Trust $existingTrust `
        -TrustPassword <newComplexPassword>
    ```

### <a name="delete-a-forest-trust"></a>Erdőszintű megbízhatósági kapcsolat törlése

Ha már nincs szüksége az egyirányú kimenő erdő megbízhatóságára a felügyelt tartományból egy helyszíni AD DS erdőre, akkor eltávolíthatja. Győződjön meg arról, hogy a megbízhatóság eltávolítása előtt egyetlen alkalmazásnak vagy szolgáltatásnak sem kell hitelesítést végeznie a helyszíni AD DS erdőben. Az egyirányú bejövő megbízhatósági kapcsolatot manuálisan kell eltávolítani a helyszíni AD DS erdőben is.

1. Telepítse a `Remove-AaddsResourceForestTrust` parancsfájlt a [PowerShell-Galéria][powershell-gallery] az [install-script][Install-Script] parancsmag használatával:

    ```powershell
    Install-Script -Name Remove-AaddsResourceForestTrust
    ```

1. Most távolítsa el az erdőszintű megbízhatóságot a `Remove-AaddsResourceForestTrust` parancsfájl használatával. A következő példában a *aaddscontoso.com* nevű felügyelt tartományi erdő és a helyszíni erdő *onprem.contoso.com* közötti megbízhatósági kapcsolat el lett távolítva a *myAzureADDSTrust* . Adja meg a saját felügyelt tartomány erdő nevét és a helyszíni erdő nevét az eltávolításhoz:

    ```powershell
    Remove-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

Ha el szeretné távolítani az egyirányú bejövő megbízhatóságot a helyszíni AD DS erdőből, kapcsolódjon a helyszíni AD DS erdőhöz hozzáféréssel rendelkező felügyeleti számítógéphez, és végezze el a következő lépéseket:

1. Válassza a **Start | Felügyeleti eszközök | Tartományok és megbízhatósági kapcsolatok Active Directory**
1. Kattintson a jobb gombbal a tartomány, például a *onprem.contoso.com*, majd a **Tulajdonságok** elemre.
1. Válassza a **Megbízhatóságok** lapot, majd válassza ki a felügyelt tartomány erdőből a meglévő bejövő megbízhatóságot.
1. Válassza az **Eltávolítás** lehetőséget, majd erősítse meg, hogy el kívánja távolítani a bejövő megbízhatóságot.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Felügyelt tartományi erőforrás-erdő létrehozása Azure PowerShell használatával
> * Egyirányú kimenő erdőszintű megbízhatósági kapcsolat létrehozása a felügyelt tartományban a Azure PowerShell használatával
> * A DNS konfigurálása helyszíni AD DS környezetben a felügyelt tartományi kapcsolat támogatásához
> * Egyirányú bejövő erdőszintű megbízhatósági kapcsolat létrehozása helyszíni AD DS környezetben
> * A hitelesítés és az erőforrás-hozzáférés megbízhatósági kapcsolatának tesztelése és ellenőrzése

Az Azure AD DS erdők típusaival kapcsolatos további információk: Mik az [erőforrás-erdők?][concepts-forest] és [hogyan működnek az erdőszintű megbízhatósági kapcsolatok az Azure ad DSban?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[network-peering]: ../virtual-network/virtual-network-peering-overview.md
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[Get-AzureRMSubscription]: /powershell/module/AzureRM.Profile/Get-AzureRmSubscription
[Install-Script]: /powershell/module/powershellget/install-script

<!-- EXTERNAL LINKS -->
[powershell-gallery]: https://www.powershellgallery.com/