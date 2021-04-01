---
title: Azure AD Domain Services migrálása klasszikus virtuális hálózatról | Microsoft Docs
description: Megtudhatja, hogyan telepíthet át egy meglévő Azure AD Domain Services felügyelt tartományt a klasszikus virtuális hálózati modellből egy Resource Manager-alapú virtuális hálózatra.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2020
ms.author: justinha
ms.openlocfilehash: 694ed5304e838057141b7df043565d58188fc870
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013039"
---
# <a name="migrate-azure-active-directory-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Azure Active Directory Domain Services migrálása a klasszikus virtuális hálózati modellből a Resource Managerbe

Azure Active Directory Domain Services (Azure AD DS) támogatja az egyszeri áthelyezést a klasszikus virtuális hálózati modellt használó ügyfelek számára a Resource Manager virtuális hálózati modelljére. Az Azure AD DS a Resource Manager-alapú üzemi modellt használó felügyelt tartományok olyan további funkciókat biztosítanak, mint például a részletes jelszóházirendek, a naplók és a fiókzárolás elleni védelem.

Ez a cikk az áttelepítéssel kapcsolatos szempontokat ismerteti, majd egy meglévő felügyelt tartomány sikeres áttelepítéséhez szükséges lépéseket. Néhány előnyért lásd: [Az Azure AD DS klasszikusról Resource Manager-alapú üzemi modelljére történő áttelepítés előnyei][migration-benefits].

> [!NOTE]
> A (z) 2017-es verziójában Azure AD Domain Services elérhetővé vált a Azure Resource Manager hálózaton lévő gazdagép számára. Azóta egy biztonságosabb szolgáltatást hoztunk létre a Azure Resource Manager modern képességeinek használatával. Mivel Azure Resource Manager központi telepítések teljes mértékben lecserélik a klasszikus üzemelő példányokat, az Azure-AD DS a klasszikus virtuális hálózati telepítések 2023. március 1-től megszűnnek.
>
> További információkért tekintse meg a [hivatalos elavult közleményt](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

## <a name="overview-of-the-migration-process"></a>Az áttelepítési folyamat áttekintése

Az áttelepítési folyamat egy meglévő felügyelt tartományt használ, amely egy klasszikus virtuális hálózaton fut, és egy meglévő Resource Manager-alapú virtuális hálózatra helyezi át. Az áttelepítés a PowerShell használatával történik, és a végrehajtás két fő szakasza van: *előkészítés* és *áttelepítés*.

![Az Azure AD DS áttelepítési folyamatának áttekintése](media/migrate-from-classic-vnet/migration-overview.png)

Az *előkészítési* szakaszban az Azure AD DS biztonsági másolatot készít a tartományról a felügyelt tartományhoz szinkronizált felhasználók, csoportok és jelszavak legújabb pillanatképének lekéréséhez. A szinkronizálás ezután le van tiltva, és a felügyelt tartományt futtató felhőalapú szolgáltatás törlődik. Az előkészítési fázisban a felügyelt tartomány nem tudja hitelesíteni a felhasználókat.

![Az Azure AD DS áttelepítésének előkészítési szakasza](media/migrate-from-classic-vnet/migration-preparation.png)

Az *áttelepítési* fázisban a klasszikus felügyelt tartományhoz tartozó tartományvezérlők mögöttes virtuális lemezeket a rendszer a Resource Manager-alapú üzemi modell használatával másolja át a virtuális gépek létrehozásához. A felügyelt tartományt ezután újra létrehozza a rendszer, amely magában foglalja az LDAPs és a DNS-konfigurációt. Az Azure AD-vel való szinkronizálás újraindul, és a rendszer visszaállítja az LDAP-tanúsítványokat. A gépeket nem kell újracsatlakoztatni egy felügyelt tartományhoz – továbbra is csatlakoztatva lesznek a felügyelt tartományhoz, és a módosítások nélkül futnak.

![Azure-AD DS áttelepítése](media/migrate-from-classic-vnet/migration-process.png)

## <a name="example-scenarios-for-migration"></a>Áttelepítési forgatókönyvek – példa

A felügyelt tartományok áttelepítésének néhány gyakori forgatókönyve a következő példákat tartalmazza.

> [!NOTE]
> Ne alakítsa át a klasszikus virtuális hálózatot, amíg meg nem erősítette a sikeres áttelepítést. A virtuális hálózat konvertálása eltávolítja a felügyelt tartomány visszaállítására vagy helyreállítására vonatkozó lehetőséget, ha az áttelepítés és az ellenőrzési fázis során problémák merülnek fel.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Azure-AD DS migrálása meglévő Resource Manager-beli virtuális hálózatra (ajánlott)

Gyakori forgatókönyv, hogy más meglévő klasszikus erőforrásokat már áthelyezett egy Resource Manager-alapú üzemi modellbe és virtuális hálózatba. Ezt követően a rendszer a Resource Manager virtuális hálózatról a klasszikus virtuális hálózatra használja, amely továbbra is az Azure AD DS fut. Ez a megközelítés lehetővé teszi, hogy a Resource Manager-alkalmazások és-szolgáltatások a felügyelt tartomány hitelesítési és felügyeleti funkcióit használják a klasszikus virtuális hálózaton. Az áttelepítés után az összes erőforrás a Resource Manager-alapú üzemi modell és a virtuális hálózat használatával fut.

![Azure-AD DS migrálása meglévő Resource Manager-beli virtuális hálózatra](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

Az ebben a példában áttelepítési forgatókönyvben szereplő magas szintű lépések a következő részekre terjednek ki:

1. A klasszikus virtuális hálózaton konfigurált meglévő VPN-átjárók vagy virtuális hálózati társítások eltávolítása.
1. Telepítse át a felügyelt tartományt a jelen cikkben ismertetett lépések alapján.
1. Tesztelje és erősítse meg a sikeres áttelepítést, majd törölje a klasszikus virtuális hálózatot.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Több erőforrás migrálása, beleértve az Azure AD DS

Ebben a példában az Azure AD DS és az egyéb kapcsolódó erőforrásokat a klasszikus üzemi modellből a Resource Manager-alapú üzemi modellbe telepíti át. Ha egyes erőforrások továbbra is a klasszikus virtuális hálózaton futnak a felügyelt tartomány mellett, az összes előnyt élvezheti a Resource Manager-alapú üzemi modellbe való Migrálás során.

![Több erőforrás migrálása a Resource Manager-alapú üzemi modellbe](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

Az ebben a példában áttelepítési forgatókönyvben szereplő magas szintű lépések a következő részekre terjednek ki:

1. A klasszikus virtuális hálózaton konfigurált meglévő VPN-átjárók vagy virtuális hálózati társítások eltávolítása.
1. Telepítse át a felügyelt tartományt a jelen cikkben ismertetett lépések alapján.
1. Virtuális hálózati társítás beállítása a klasszikus virtuális hálózat és a Resource Manager-hálózat között.
1. Tesztelje és erősítse meg a sikeres áttelepítést.
1. [Helyezze át a további klasszikus erőforrásokat, például a virtuális gépeket][migrate-iaas].

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Az Azure AD DS migrálása, de egyéb erőforrások megőrzése a klasszikus virtuális hálózaton

Ebben a példában a minimális állásidő van az egyik munkamenetben. Az Azure AD DS-t csak Resource Manager-alapú virtuális hálózatra telepíti át, és megtartja a meglévő erőforrásokat a klasszikus üzemi modellben és a virtuális hálózaton. A következő karbantartási időszakban a további erőforrásokat áttelepítheti a klasszikus üzemi modellből és a virtuális hálózatból, igény szerint.

![Csak az Azure AD DS migrálása a Resource Manager-alapú üzemi modellbe](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

Az ebben a példában áttelepítési forgatókönyvben szereplő magas szintű lépések a következő részekre terjednek ki:

1. A klasszikus virtuális hálózaton konfigurált meglévő VPN-átjárók vagy virtuális hálózati társítások eltávolítása.
1. Telepítse át a felügyelt tartományt a jelen cikkben ismertetett lépések alapján.
1. Virtuális hálózati társítás beállítása a klasszikus virtuális hálózat és az új Resource Manager-alapú virtuális hálózat között.
1. Később [telepítse át a további erőforrásokat][migrate-iaas] a klasszikus virtuális hálózatból igény szerint.

## <a name="before-you-begin"></a>Előkészületek

A felügyelt tartományok előkészítésekor és áttelepítését követően néhány szempontot figyelembe kell venni a hitelesítési és a felügyeleti szolgáltatások rendelkezésre állása körül. A felügyelt tartomány az áttelepítés során hosszabb ideig nem érhető el. Az Azure AD DS-t használó alkalmazások és szolgáltatások az áttelepítés során állásidőt tapasztalnak.

> [!IMPORTANT]
> Az áttelepítési folyamat megkezdése előtt olvassa el az összes áttelepítési cikket és útmutatást. Az áttelepítési folyamat az Azure AD DS tartományvezérlők rendelkezésre állását is befolyásolja az adott időszakban. A felhasználók, szolgáltatások és alkalmazások nem tudnak hitelesíteni a felügyelt tartományon az áttelepítési folyamat során.

### <a name="ip-addresses"></a>IP-címek

A felügyelt tartomány tartományvezérlői IP-címei módosulnak az áttelepítés után. Ez a változás tartalmazza a biztonságos LDAP-végpont nyilvános IP-címét. Az új IP-címek a Resource Manager virtuális hálózatban lévő új alhálózat címtartomány-tartományán belül találhatók.

Ha vissza kell térnie, az IP-címek a visszagörgetés után változhatnak.

Az Azure AD DS általában a címtartomány első két elérhető IP-címét használja, ez azonban nem garantált. Az áttelepítés után jelenleg nem adhatja meg a használni kívánt IP-címeket.

### <a name="downtime"></a>Állásidő

Az áttelepítési folyamat során a tartományvezérlők egy ideig offline állapotban vannak. A tartományvezérlők nem érhetők el, amíg az Azure AD DS át lett telepítve a Resource Manager-alapú üzemi modellbe és a virtuális hálózatba.

Az állásidő átlaga körülbelül 1 – 3 óra. Ez az időszak az, amikor a tartományvezérlők offline állapotba kerülnek, amikor az első tartományvezérlő ismét online állapotba kerül. Ez az átlag nem tartalmazza a második tartományvezérlő replikálásához szükséges időt, vagy a további erőforrások áttelepítéséhez szükséges időt a Resource Manager-alapú üzemi modellre.

### <a name="account-lockout"></a>Fiókzárolási

A klasszikus virtuális hálózatokon futó felügyelt tartományokhoz nem tartoznak AD fiókzárolási szabályzatok. Ha a virtuális gépek elérhetők az interneten, a támadók jelszó-szórási módszereket használhatnak a fiókok bevezetéséhez. A próbálkozások leállításához nincs fiókzárolási házirend. A Resource Manager-alapú üzemi modellt és virtuális hálózatokat használó felügyelt tartományok esetében az AD-fiókzárolási házirendek védik ezeket a jelszó-szórásos támadásokat.

Alapértelmezés szerint az 5 hibás jelszó 2 percen belül kizárja a fiókot 30 percig.

Kizárt fiók nem használható a bejelentkezéshez, ami zavarhatja a fiók által kezelt felügyelt tartomány vagy alkalmazások felügyeletének képességét. A felügyelt tartomány migrálása után a fiókok megtapasztalják, hogy az állandó zárolási kísérlet miatt hogyan lehet maradandó zárolást létrehozni. Az áttelepítés utáni két gyakori forgatókönyv a következő:

* Olyan szolgáltatásfiók, amely lejárt jelszót használ.
    * A szolgáltatásfiók többször is megpróbál bejelentkezni egy lejárt jelszóval, amely kizárja a fiókot. A probléma megoldásához keresse meg a lejárt hitelesítő adatokkal rendelkező alkalmazást vagy virtuális gépet, és frissítse a jelszót.
* A rosszindulatú entitások találgatásos kísérletekkel jelentkeznek be a fiókba.
    * Ha a virtuális gépek ki vannak téve az internethez, a támadók gyakran megpróbálnak közös felhasználóneveket és jelszavakat használni a bejelentkezés megkísérlése során. Ezek az ismétlődő sikertelen bejelentkezési kísérletek le tudják zárni a fiókokat. A rendszergazdai fiókokat nem ajánlott általános névvel *(például rendszergazda vagy* *rendszergazda*) használni, például a rendszergazdai fiókok kizárásának minimalizálására.
    * Csökkentse az interneten elérhetővé tett virtuális gépek számát. Az [Azure Bastion][azure-bastion] segítségével biztonságosan csatlakozhat a virtuális gépekhez a Azure Portal használatával.

Ha azt gyanítja, hogy egyes fiókok kizárhatók az áttelepítés után, akkor a végső áttelepítési lépések azt ismertetik, hogyan lehet a naplózást engedélyezni, vagy módosítani a részletes jelszóházirendek beállításait.

### <a name="roll-back-and-restore"></a>Visszaállítás és helyreállítás

Ha az áttelepítés sikertelen, folyamatban van egy felügyelt tartomány visszavonása vagy visszaállítása. A visszaállítás önkiszolgáló lehetőség, amely azonnal visszaadja a felügyelt tartomány állapotát az áttelepítési kísérlet előtt. Az Azure-támogatási mérnökök a felügyelt tartományokat is visszaállíthatják a biztonsági mentésből a végső megoldásként. További információ: Hogyan lehet [visszagörgetni vagy visszaállítani a sikertelen áttelepítést](#roll-back-and-restore-from-migration).

### <a name="restrictions-on-available-virtual-networks"></a>A rendelkezésre álló virtuális hálózatokra vonatkozó korlátozások

A felügyelt tartomány áttelepíthető virtuális hálózatokra vonatkozó korlátozások vannak. A cél Resource Manager virtuális hálózatnak meg kell felelnie a következő követelményeknek:

* A Resource Manager virtuális hálózatnak ugyanabban az Azure-előfizetésben kell lennie, mint a klasszikus virtuális hálózatnak, amelyre az Azure AD DS jelenleg telepítve van.
* A Resource Manager virtuális hálózatnak ugyanabban a régióban kell lennie, mint a klasszikus virtuális hálózatnak, amelyre az Azure AD DS jelenleg telepítve van.
* A Resource Manager virtuális hálózat alhálózatának legalább 3-5 elérhető IP-címmel kell rendelkeznie.
* A Resource Manager virtuális hálózat alhálózatának dedikált alhálózatnak kell lennie az Azure AD DS számára, és nem kell más számítási feladatokat üzemeltetni.

A virtuális hálózati követelményekkel kapcsolatos további információkért lásd a [virtuális hálózatok kialakításával kapcsolatos szempontokat és a konfigurációs beállításokat][network-considerations].

Egy hálózati biztonsági csoportot is létre kell hoznia a felügyelt tartományhoz tartozó virtuális hálózat forgalmának korlátozásához. Az áttelepítési folyamat során létrejön egy Azure standard Load Balancer, amely megköveteli ezeket a szabályokat. Ez a hálózati biztonsági csoport biztosítja az Azure AD DSét, és szükséges a felügyelt tartomány megfelelő működéséhez.

További információ a szükséges szabályokról: [Azure AD DS hálózati biztonsági csoportok és szükséges portok](network-considerations.md#network-security-groups-and-required-ports).

### <a name="ldaps-and-tlsssl-certificate-expiration"></a>LDAPs és TLS/SSL-tanúsítvány lejárata

Ha a felügyelt tartomány LDAPs használatára van konfigurálva, győződjön meg róla, hogy az aktuális TLS/SSL-tanúsítvány több mint 30 napig érvényes. A következő 30 napon belül lejáró tanúsítvány miatt sikertelen lesz az áttelepítési folyamat. Szükség esetén újítsa meg a tanúsítványt, és alkalmazza azt a felügyelt tartományra, majd indítsa el az áttelepítési folyamatot.

## <a name="migration-steps"></a>A migrálás lépései

A Resource Manager-alapú üzemi modellre és a virtuális hálózatra történő áttelepítés 5 fő lépésből áll:

| Lépés    | Végrehajtás  | Becsült idő  | Állásidő  | Vissza/vissza? |
|---------|--------------------|-----------------|-----------|-------------------|
| [1. lépés – az új virtuális hálózat frissítése és megkeresése](#update-and-verify-virtual-network-settings) | Azure Portal | 15 perc | Nincs szükség állásidőre | N/A |
| [2. lépés – a felügyelt tartomány előkészítése áttelepítésre](#prepare-the-managed-domain-for-migration) | PowerShell | 15 – 30 perc átlagosan | Az Azure AD DS leállása a parancs befejezése után kezdődik. | Visszaállítás és helyreállítás elérhető. |
| [3. lépés – a felügyelt tartomány áthelyezése meglévő virtuális hálózatra](#migrate-the-managed-domain) | PowerShell | átlagosan 1 – 3 óra | A parancs végrehajtása után egy tartományvezérlő érhető el. | Hiba esetén mind a visszaállítás (önkiszolgáló), mind a visszaállítás elérhető. |
| [4. lépés – tesztelés és várakozás a replika tartományvezérlőre](#test-and-verify-connectivity-after-the-migration)| PowerShell és Azure Portal | 1 óra vagy több, a tesztek számától függően | Mindkét tartományvezérlő elérhető, és a szokásos módon működik, a leállás véget ér. | N/A. Az első virtuális gép sikeres áttelepítése után nincs lehetőség visszaállításra vagy visszaállításra. |
| [5. lépés – választható konfigurációs lépések](#optional-post-migration-configuration-steps) | Azure Portal és virtuális gépek | N/A | Nincs szükség állásidőre | N/A |

> [!IMPORTANT]
> Az áttelepítési folyamat megkezdése előtt olvassa el az összes áttelepítési cikket és útmutatást a további állásidő elkerüléséhez. Az áttelepítési folyamat egy adott időszakra hatással van az Azure AD DS tartományvezérlők rendelkezésre állására. A felhasználók, szolgáltatások és alkalmazások nem tudnak hitelesíteni a felügyelt tartományon az áttelepítési folyamat során.

## <a name="update-and-verify-virtual-network-settings"></a>Virtuális hálózati beállítások frissítése és ellenőrzése

Az áttelepítési folyamat megkezdése előtt végezze el a következő kezdeti ellenőrzéseket és frissítéseket. Ezek a lépések bármikor megtörténnek az áttelepítés előtt, és nem befolyásolhatják a felügyelt tartomány működését.

1. Frissítse helyi Azure PowerShell környezetét a legújabb verzióra. Az áttelepítési lépések végrehajtásához legalább *2.3.2*-es verziójúnak kell lennie.

    További információ a PowerShell-verziójának vizsgálatáról és frissítéséről: [Azure PowerShell Overview (áttekintés][azure-powershell]).

1. Hozzon létre vagy válasszon ki egy meglévő, Resource Manager-beli virtuális hálózatot.

    Győződjön meg arról, hogy a hálózati beállítások nem blokkolja az Azure-AD DS szükséges portokat. A portoknak a klasszikus virtuális hálózaton és a Resource Manager virtuális hálózaton is nyitva kell lenniük. Ezek a beállítások útválasztási táblákat tartalmaznak (bár az útválasztási táblák használata nem ajánlott) és a hálózati biztonsági csoportok.

    Az Azure AD DS hálózati biztonsági csoportra van szüksége a felügyelt tartományhoz szükséges portok biztonságossá tételéhez és az összes többi bejövő forgalom blokkolásához. Ez a hálózati biztonsági csoport a felügyelt tartomány elérésének zárolására szolgáló extra védelmi rétegként működik. A szükséges portok megtekintéséhez tekintse meg a [hálózati biztonsági csoportok és a szükséges portok][network-ports]című témakört.

    Ha biztonságos LDAP-t használ, vegyen fel egy szabályt a hálózati biztonsági csoportba, hogy engedélyezze a bejövő forgalmat a *636*-as *TCP* -porton. További információ: [biztonságos LDAP-hozzáférés zárolása az interneten keresztül](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)

    Jegyezze fel a célként megadott erőforráscsoportot, a célként megadott virtuális hálózatot és a célként megadott virtuális hálózati alhálózatot. Ezeket az erőforrásokat az áttelepítési folyamat során használja a rendszer.

1. A Azure Portal felügyelt tartomány állapotának ellenőrzését. Ha a felügyelt tartományhoz bármilyen riasztás tartozik, hárítsa el az áttelepítési folyamat megkezdése előtt.
1. Ha más erőforrásokat is át szeretne helyezni a Resource Manager-alapú üzemi modellbe és a virtuális hálózatba, ellenőrizze, hogy az erőforrások áttelepíthetők-e. További információ: a [platform által támogatott IaaS-erőforrások áttelepítése a Klasszikusból a Resource Managerbe][migrate-iaas].

    > [!NOTE]
    > Ne alakítsa át a klasszikus virtuális hálózatot Resource Manager-alapú virtuális hálózatra. Ha így tesz, nincs lehetőség a felügyelt tartomány visszaállítására vagy helyreállítására.

## <a name="prepare-the-managed-domain-for-migration"></a>A felügyelt tartomány előkészítése áttelepítésre

A Azure PowerShell a felügyelt tartomány áttelepítésre való előkészítésére szolgál. Ezek a lépések magukban foglalják a biztonsági mentést, a szinkronizálás szüneteltetését és az Azure AD DS-t futtató felhőalapú szolgáltatás törlését. Ha ez a lépés befejeződik, az Azure AD DS egy ideig offline állapotba kerül. Ha az előkészítési lépés meghiúsul, visszaállíthatja [az előző állapotot](#roll-back).

A felügyelt tartomány áttelepítésre való előkészítéséhez végezze el a következő lépéseket:

1. Telepítse a `Migrate-Aaads` parancsfájlt a [PowerShell-Galéria][powershell-script]. Ez a PowerShell-áttelepítési parancsfájl az Azure AD mérnöki csapata által digitálisan aláírt.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Hozzon létre egy változót, amely az áttelepítési parancsfájl hitelesítő adatait a [Get-hitelesítőadat][get-credential] parancsmag használatával fogja tárolni.

    A megadott felhasználói fióknak *globális rendszergazdai* jogosultságokkal kell rendelkeznie az Azure ad-bérlőben az Azure AD DS, majd az Azure-előfizetéshez tartozó *közreműködői* jogosultságok létrehozásához a szükséges Azure AD DS-erőforrások létrehozásához.

    Ha a rendszer kéri, adjon meg egy megfelelő felhasználói fiókot és jelszót:

    ```powershell
    $creds = Get-Credential
    ```
    
1. Adjon meg egy változót az Azure-előfizetési AZONOSÍTÓhoz. Ha szükséges, a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) parancsmag használatával listázhatja és megtekintheti az előfizetési azonosítókat. Adja meg saját előfizetés-AZONOSÍTÓját a következő parancsban:

   ```powershell
   $subscriptionId = 'yourSubscriptionId'
   ```

1. Most futtassa a `Migrate-Aadds` parancsmagot a *-Prepare* paraméter használatával. Adja meg a *-ManagedDomainFqdn* a saját felügyelt tartományhoz, például *aaddscontoso.com*:

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds `
        -SubscriptionId $subscriptionId
    ```

## <a name="migrate-the-managed-domain"></a>A felügyelt tartomány migrálása

A felügyelt tartomány előkészítése és biztonsági mentése után a tartomány migrálható. Ez a lépés újra létrehozza az Azure AD DS tartományvezérlő virtuális gépeket a Resource Manager-alapú üzemi modell használatával. Ez a lépés 1 – 3 órát is igénybe vehet.

Futtassa a `Migrate-Aadds` parancsmagot a *-commit* paraméter használatával. Adja meg a *-ManagedDomainFqdn* az előző szakaszban előkészített saját felügyelt tartományhoz, például *aaddscontoso.com*:

Válassza ki azt a célként megadott erőforráscsoportot, amely az Azure AD DS áttelepíteni kívánt virtuális hálózatot tartalmazza, például *myResourceGroup*. Adja meg a cél virtuális hálózatot, például a *myVnet* és az alhálózatot, például a *DomainServices*.

A parancs futtatása után visszaállíthatja a következőt:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds `
    -SubscriptionId $subscriptionId
```

Miután a parancsfájl ellenőrzi, hogy a felügyelt tartomány készen áll-e az áttelepítésre, írja be az *Y* értéket az áttelepítési folyamat indításához.

> [!IMPORTANT]
> Az áttelepítési folyamat során ne alakítsa át a klasszikus virtuális hálózatot egy Resource Manager-alapú virtuális hálózatra. Ha átalakítja a virtuális hálózatot, akkor a felügyelt tartományt nem lehet visszaállítani vagy helyreállítani, mert az eredeti virtuális hálózat már nem létezik.

Az áttelepítési folyamat során két percenként a folyamatjelző jelzi az aktuális állapotot, ahogy az a következő példában látható:

![Az Azure-AD DS áttelepítésének előrehaladási mutatója](media/migrate-from-classic-vnet/powershell-migration-status.png)

Az áttelepítési folyamat továbbra is fut, még akkor is, ha bezárta a PowerShell-szkriptet. A Azure Portal a felügyelt tartomány jelentéseinek állapota *Migrálás*.

Az áttelepítés sikeres befejeződése után megtekintheti az első tartományvezérlő IP-címét a Azure Portal vagy a Azure PowerShell használatával. A második tartományvezérlő elérhetővé tételének becsült időpontja is megjelenik.

Ebben a szakaszban igény szerint áthelyezheti a többi meglévő erőforrást a klasszikus üzembe helyezési modellből és a virtuális hálózatból. Vagy megtarthatja az erőforrásokat a klasszikus üzemi modellben, és a virtuális hálózatokat az Azure AD DS Migrálás befejezése után egymással is elvégezheti.

## <a name="test-and-verify-connectivity-after-the-migration"></a>A kapcsolat tesztelése és ellenőrzése az áttelepítés után

Eltarthat egy ideig, amíg a második tartományvezérlő sikeresen üzembe helyezi, és elérhetővé válik a felügyelt tartományban való használatra. Az áttelepítési parancsmag befejezését követően a második tartományvezérlő 1-2 órán belül elérhetőnek kell lennie. A Resource Manager-alapú üzemi modellel a felügyelt tartomány hálózati erőforrásai láthatók a Azure Portal vagy Azure PowerShell. A második tartományvezérlő elérhetővé tételének vizsgálatához tekintse meg a felügyelt tartomány **tulajdonságlapját** a Azure Portal. Ha két IP-cím látható, a második tartományvezérlő készen áll.

A második tartományvezérlő elérhetővé tétele után végezze el a következő konfigurációs lépéseket a virtuális gépekkel való hálózati kapcsolathoz:

* **DNS-kiszolgáló beállításainak frissítése** Ha szeretné, hogy a Resource Manager virtuális hálózat más erőforrásai megoldják és használják a felügyelt tartományt, frissítse a DNS-beállításokat az új tartományvezérlők IP-címeivel. A Azure Portal automatikusan konfigurálhatja ezeket a beállításokat.

    A Resource Manager virtuális hálózat konfigurálásával kapcsolatos további tudnivalókért tekintse meg [Az Azure virtuális hálózat DNS-beállításainak frissítése][update-dns]című témakört.
* **Tartományhoz csatlakoztatott virtuális gépek újraindítása (nem kötelező)** Az Azure AD DS tartományvezérlők DNS-kiszolgálói IP-címeinek megváltozása esetén újraindíthatja a tartományhoz csatlakoztatott virtuális gépeket, hogy azok az új DNS-kiszolgáló beállításait használják. Ha az alkalmazások vagy virtuális gépek manuálisan konfigurálták a DNS-beállításokat, manuálisan frissítse azokat a tartományvezérlők új DNS-kiszolgáló IP-címeivel, amelyek megjelennek a Azure Portal. A tartományhoz csatlakoztatott virtuális gépek újraindítása megakadályozza a nem frissülő IP-címek által okozott csatlakozási problémákat.

Most tesztelje a virtuális hálózati kapcsolatokat és a névfeloldást. A Resource Manager-alapú virtuális hálózathoz csatlakoztatott virtuális GÉPEN vagy a hozzá tartozó kapcsolaton próbálja ki a következő hálózati kommunikációs teszteket:

1. Ellenőrizze, hogy tud-e pingelni az egyik tartományvezérlő IP-címét, például `ping 10.1.0.4`
    * A tartományvezérlők IP-címei a Azure Portal felügyelt tartományának **Tulajdonságok** lapján jelennek meg.
1. Ellenőrizze a felügyelt tartomány névfeloldását, például `nslookup aaddscontoso.com`
    * Adja meg a saját felügyelt tartomány DNS-nevét annak ellenőrzéséhez, hogy a DNS-beállítások helyesek-e, majd oldja fel.

További információ a többi hálózati erőforrásról: az [Azure AD DS által használt hálózati erőforrások][network-resources].

## <a name="optional-post-migration-configuration-steps"></a>Választható áttelepítés utáni konfigurációs lépések

Ha az áttelepítési folyamat sikeresen befejeződött, néhány választható konfigurációs lépés tartalmazza a naplók és az e-mail-értesítések engedélyezését, illetve a részletes jelszóházirendek frissítését.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Előfizetés a naplókra Azure Monitor használatával

Az Azure AD DS a tartományvezérlőkön található események hibakeresését és megtekintését megkönnyítő naplókat tesz elérhetővé. További információt a [naplók engedélyezése és használata][security-audits]című témakörben talál.

A sablonok segítségével figyelheti a naplókban elérhető fontos információkat. A naplózási napló munkafüzet-sablonja például a felügyelt tartományon figyelheti a lehetséges fiókzárolási lehetőségeket.

### <a name="configure-email-notifications"></a>E-mail-értesítések konfigurálása

Ha értesítést szeretne kapni, ha problémát észlel a felügyelt tartományon, frissítse a Azure Portal e-mail értesítési beállításait. További információ: az [értesítési beállítások konfigurálása][notifications].

### <a name="update-fine-grained-password-policy"></a>Részletes jelszóházirendek frissítése

Ha szükséges, frissítheti a részletes jelszóházirendek, hogy az alapértelmezett konfigurációnál kevésbé legyen korlátozó. A naplók segítségével meghatározhatja, hogy egy kevésbé korlátozó beállítás logikus-e, majd szükség szerint konfigurálja a házirendet. A következő magas szintű lépésekkel tekintheti át és frissítheti az áttelepítés után ismétlődően zárolt fiókok házirend-beállításait:

1. [Állítsa be a jelszóházirend beállítását][password-policy] a felügyelt tartomány kevesebb korlátozására, és figyelje meg az eseményeket a naplókban.
1. Ha bármelyik szolgáltatásfiók a naplókban azonosított lejárt jelszavakat használ, frissítse ezeket a fiókokat a megfelelő jelszóval.
1. Ha egy virtuális gép elérhető az internethez, tekintse át az általános fiókok neveit, például a rendszergazda, a *felhasználó* vagy a *vendég* *szerepkört* a magas bejelentkezési kísérletekkel. Ha lehetséges, frissítse ezeket a virtuális gépeket úgy, hogy azok kevésbé általános névvel ellátott fiókokat használjanak.
1. A virtuális gépen található hálózati nyomkövetés használatával keresse meg a támadások forrását, és tiltsa le az IP-címeket, hogy a rendszer megkísérelje a bejelentkezést.
1. Ha minimális zárolási probléma merül fel, frissítse a részletes jelszóházirendek a szükségesnél szigorúbb korlátozást.

## <a name="roll-back-and-restore-from-migration"></a>Visszaállítás és helyreállítás az áttelepítésből

Az áttelepítési folyamat egy bizonyos pontjáig dönthet úgy, hogy visszaállítja vagy helyreállítja a felügyelt tartományt.

### <a name="roll-back"></a>Visszaállítás

Ha hiba lép fel a PowerShell-parancsmag futtatásakor a 2. lépésben vagy a 3. lépésben a Migrálás előkészítéséhez, a felügyelt tartomány visszaállíthatja az eredeti konfigurációt. A visszaállításhoz az eredeti klasszikus virtuális hálózat szükséges. Az IP-címek továbbra is változhatnak a visszaállítás után.

Futtassa a `Migrate-Aadds` parancsmagot a *-megszakítás* paraméter használatával. Adja meg a *-ManagedDomainFqdn* az előző szakaszban előkészített saját felügyelt tartományhoz, például a *Aaddscontoso.com* és a klasszikus virtuális hálózat nevét, például *myClassicVnet*:

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds `
    -SubscriptionId $subscriptionId
```

### <a name="restore"></a>Visszaállítás

Végső megoldásként Azure AD Domain Services visszaállíthatók a legutóbbi rendelkezésre álló biztonsági mentésből. A rendszer biztonsági másolatot készít az áttelepítés 1. lépésében, hogy biztosan elérhető legyen a legfrissebb biztonsági mentés. A biztonsági mentés 30 napig tárolódik.

A felügyelt tartomány biztonsági másolatból történő visszaállításához [Nyisson meg egy támogatási esetet a Azure Portal használatával][azure-support]. Adja meg a címtár-azonosítót, a tartománynevet és a visszaállítás okát. A támogatási és visszaállítási folyamat több napot is igénybe vehet.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a Resource Manager-alapú üzemi modellre való áttelepítést követően problémák léptek fel, tekintse át a következő gyakori hibaelhárítási területek némelyikét:

* [A tartományhoz való csatlakozással kapcsolatos problémák elhárítása][troubleshoot-domain-join]
* [Fiókzárolási problémák elhárítása][troubleshoot-account-lockout]
* [Fiók bejelentkezési problémáinak elhárítása][troubleshoot-sign-in]
* [A biztonságos LDAP-kapcsolati problémák elhárítása][tshoot-ldaps]

## <a name="next-steps"></a>Következő lépések

A felügyelt tartomány áttelepítése a Resource Manager-alapú üzemi modellre, a [Létrehozás és a tartomány csatlakoztatása Windows rendszerű virtuális géphez][join-windows] , majd a [felügyeleti eszközök telepítése][tutorial-create-management-vm].

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential
[migration-benefits]: concepts-migration-benefits.md

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/