---
title: Azure HDInsight architektúra Enterprise Security Package
description: Ismerje meg, hogyan tervezheti meg az Azure HDInsight-biztonságot Enterprise Security Package használatával.
ms.reviewer: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/11/2020
ms.openlocfilehash: 404cd8652a4daf602f3326696ef5df2c3a23a548
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943314"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Enterprise Security Package használata a HDInsightban

A standard Azure HDInsight-fürt egyetlen felhasználóból álló fürt. Ez a legtöbb vállalat számára megfelelő, akik kisebb méretű alkalmazásokkal dolgoznak ki nagy mennyiségű adatszámítási feladatot. Minden felhasználó igény szerint létrehozhat egy dedikált fürtöt, és megsemmisítheti azt, ha már nincs rá szükség.

Számos vállalat egy olyan modell felé mozdult el, amelyben az informatikai csapatok kezelik a fürtöket, és több alkalmazás-csapat osztozik a fürtökön. Ezeknek a nagyobb vállalatoknak többfelhasználós hozzáféréssel kell rendelkezniük az egyes fürtökhöz az Azure HDInsight-ben.

A HDInsight egy népszerű identitás-szolgáltatóra támaszkodik – Active Directory – felügyelt módon. A HDInsight és a [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md)integrálásával a tartomány hitelesítő adataival érheti el a fürtöket.

A HDInsight lévő virtuális gépek (VM-EK) tartományhoz csatlakoznak a megadott tartományhoz. Így a HDInsight futó összes szolgáltatás (Apache Ambari, Apache Hive Server, Apache Ranger, Apache Spark takarékosság-kiszolgáló és mások) zökkenőmentesen működik a hitelesített felhasználó számára. A rendszergazdák ezután erős engedélyezési házirendeket hozhatnak létre az Apache Ranger használatával, amely szerepköralapú hozzáférés-vezérlést biztosít a fürt erőforrásaihoz.

## <a name="integrate-hdinsight-with-active-directory"></a>A HDInsight és az Active Directory integrálása

A nyílt forráskódú Apache Hadoop a Kerberos protokollra támaszkodik a hitelesítéshez és a biztonsághoz. Ezért az Enterprise Security Package (ESP) HDInsight rendelkező fürtcsomópontok az Azure AD DS által felügyelt tartományhoz csatlakoznak. A Kerberos-biztonság a fürt Hadoop-összetevőihez van konfigurálva.

A következő dolgok jönnek létre automatikusan:

- Egy egyszerű szolgáltatásnév az egyes Hadoop-összetevőkhöz
- A tartományhoz csatlakozó egyes gépekhez tartozó egyszerű számítógép
- Szervezeti egység (OU) az egyes fürtökhöz a szolgáltatás és a rendszerbiztonsági tag tárolásához

Az összegzéshez létre kell hoznia egy környezetet a következővel:

- Active Directory tartomány (az Azure AD DS által felügyelt). **Az Azure HDInsight való együttműködéshez a tartománynév 39 karakter vagy annál kisebb kell legyen.**
- Secure LDAP (LDAPs) engedélyezve az Azure AD DSban.
- Megfelelő hálózati kapcsolat a HDInsight virtuális hálózatról az Azure AD DS virtuális hálózatra, ha külön virtuális hálózatokat választ számukra. A HDInsight virtuális hálózatban lévő virtuális gépeknek a virtuális hálózati közvetítésen keresztül kell megnézniük az Azure AD DS. Ha a HDInsight és az Azure AD DS ugyanazon a virtuális hálózaton vannak telepítve, akkor a rendszer automatikusan megadja a kapcsolatot, és nincs szükség további műveletre.

## <a name="set-up-different-domain-controllers"></a>Eltérő tartományvezérlők beállítása

A HDInsight jelenleg csak az Azure-AD DS támogatja a fürt által a Kerberos-kommunikációhoz használt fő tartományvezérlőként. Más összetett Active Directory-beállítások azonban lehetségesek, feltéve, hogy az ilyen beállítások lehetővé teszik az Azure-AD DS HDInsight-hozzáférésének engedélyezését.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory tartományi szolgáltatások

Az [Azure AD DS](../../active-directory-domain-services/overview.md) a Windows Server Active Directory teljes mértékben kompatibilis felügyelt tartományt biztosít. A Microsoft gondoskodik a tartomány magas rendelkezésre állású (HA) telepítéssel történő kezeléséről, javításáról és figyeléséről. A fürt üzembe helyezése a tartományvezérlők fenntartása nélkül is elvégezhető.

A felhasználók, csoportok és jelszavak az Azure AD-ből lesznek szinkronizálva. Az Azure AD-példányról az Azure AD DSra való egyirányú szinkronizálás lehetővé teszi, hogy a felhasználók ugyanazzal a vállalati hitelesítő adatokkal jelentkezzenek be a fürtbe.

További információ: HDInsight- [fürtök konfigurálása az ESP-vel az Azure AD DS használatával](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Helyszíni Active Directory vagy Active Directory IaaS-alapú virtuális gépeken

Ha helyszíni Active Directory-példánnyal vagy összetettebb Active Directory-beállítással rendelkezik a tartományhoz, a Azure AD Connect használatával szinkronizálhatja ezeket az identitásokat az Azure AD-be. Ezután engedélyezheti az Azure AD DSt a Active Directory bérlőn.

Mivel a Kerberos a jelszó-kivonatokra támaszkodik, engedélyeznie kell [a jelszó-kivonatok szinkronizálását az Azure ad DSon](../../active-directory-domain-services/tutorial-create-instance.md).

Ha Active Directory összevonási szolgáltatások (AD FS) (AD FS) összevonást használ, engedélyeznie kell a jelszó-kivonatolási szinkronizálást. (Az ajánlott beállításhoz tekintse meg [ezt a videót](https://youtu.be/qQruArbu2Ew).) A jelszó-kivonatolási szinkronizálás segít a vész-helyreállításban abban az esetben, ha a AD FS-infrastruktúra meghibásodik, és segít a kiszivárgott hitelesítő adatok védelmében is. További információ: jelszó- [kivonatoló szinkronizálás engedélyezése Azure ad Connect szinkronizálással](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

Az Azure AD és az Azure AD DS nélküli helyszíni Active Directory vagy Active Directory használata a IaaS virtuális gépeken nem támogatott konfiguráció az ESP-vel rendelkező HDInsight-fürtökhöz.

Ha az összevonás használatban van, és a jelszó-kivonatok megfelelően vannak szinkronizálva, de hitelesítési hibák lépnek fel, ellenőrizze, hogy engedélyezve van-e a felhőalapú jelszó-hitelesítés a PowerShell egyszerű szolgáltatásnév számára. Ha nem, be kell állítania az Azure AD-bérlőhöz tartozó [HRD-házirendet](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) . A HRD házirend bejelölése és beállítása:

1. Telepítse az előzetes verziójú [Azure ad PowerShell-modult](/powershell/azure/active-directory/install-adv2).

   ```powershell
   Install-Module AzureAD
   ```

2. Kapcsolódjon a globális rendszergazda (bérlői rendszergazda) hitelesítő adataival.

   ```powershell
   Connect-AzureAD
   ```

3. Ellenőrizze, hogy a Microsoft Azure PowerShell egyszerű szolgáltatás már létrejött-e.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Ha nem létezik, hozza létre az egyszerű szolgáltatásnevet.

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Hozza létre és csatolja a szabályzatot ehhez az egyszerű szolgáltatáshoz.

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId

    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>Következő lépések

- [HDInsight-fürtök beállítása az ESP-vel](apache-domain-joined-configure-using-azure-adds.md)
- [HDInsight-fürtök Apache Hive szabályzatának konfigurálása ESP-vel](apache-domain-joined-run-hive.md)
- [HDInsight-fürtök az ESP-vel való kezelése](apache-domain-joined-manage.md)