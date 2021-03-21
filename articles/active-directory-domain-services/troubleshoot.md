---
title: Azure Active Directory Domain Services hibaelhárítás | Microsoft Docs "
description: A Azure Active Directory Domain Services létrehozásakor és kezelésekor előforduló gyakori hibák elhárítása
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 89b04f86d41f8e4828580f70a9aec8acea3e0053
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618450"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Gyakori hibák és hibaelhárítási lépések a Azure Active Directory Domain Services

Az alkalmazások identitásának és hitelesítésének központi részeként Azure Active Directory Domain Services (Azure AD DS) időnként problémák léptek fel. Ha problémákba ütközik, bizonyos gyakori hibaüzenetek és a hozzájuk kapcsolódó hibaelhárítási lépések segítséget nyújtanak az újbóli futtatásához. A további hibaelhárítási segítségért [egy Azure-támogatási kérelem is megnyitható][azure-support] .

Ez a cikk az Azure AD DS gyakori problémáinak hibaelhárítási lépéseit ismerteti.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Az Azure AD-címtárhoz nem engedélyezhető Azure AD Domain Services

Ha problémába ütközik az Azure AD DS engedélyezésekor, tekintse át a következő gyakori hibákat és lépéseket a megoldásához:

| **Példa hibaüzenet** | **Resolution** (Osztás) |
| --- |:--- |
| *A aaddscontoso.com név már használatban van ezen a hálózaton. Olyan nevet adjon meg, amely nincs használatban.* |[Tartománynév-ütközés a virtuális hálózaton](troubleshoot.md#domain-name-conflict) |
| *Nem lehet engedélyezni a tartományi szolgáltatásokat ebben az Azure AD-bérlőben. A szolgáltatás nem rendelkezik megfelelő engedélyekkel a (z) "Azure AD Domain Services Sync" nevű alkalmazáshoz. Törölje a "Azure AD Domain Services Sync" nevű alkalmazást, majd próbálja meg engedélyezni az Azure AD-bérlő tartományi szolgáltatásait.* |[A tartományi szolgáltatások nem rendelkeznek megfelelő engedélyekkel a Azure AD Domain Services Sync alkalmazáshoz](troubleshoot.md#inadequate-permissions) |
| *Nem lehet engedélyezni a tartományi szolgáltatásokat ebben az Azure AD-bérlőben. Az Azure AD-bérlőben található tartományi szolgáltatások alkalmazás nem rendelkezik a tartományi szolgáltatások engedélyezéséhez szükséges engedélyekkel. Törölje az alkalmazást az alkalmazás-azonosító d87dcbc6-a371-462e-88e3-28ad15ec4e64, majd próbálja meg engedélyezni az Azure AD-bérlő tartományi szolgáltatásait.* |[A tartományi szolgáltatások alkalmazás nincs megfelelően konfigurálva az Azure AD-bérlőben](troubleshoot.md#invalid-configuration) |
| *Nem lehet engedélyezni a tartományi szolgáltatásokat ebben az Azure AD-bérlőben. Az Microsoft Azure AD alkalmazás le van tiltva az Azure AD-bérlőben. Engedélyezze az alkalmazást az 00000002-0000-0000-C000-000000000000 alkalmazás-azonosítóval, majd próbálja meg engedélyezni az Azure AD-bérlő tartományi szolgáltatásait.* |[Az Microsoft Graph alkalmazás le van tiltva az Azure AD-bérlőben](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Tartomány neve ütközés

**Hibaüzenet**

*A aaddscontoso.com név már használatban van ezen a hálózaton. Olyan nevet adjon meg, amely nincs használatban.*

**Resolution** (Osztás)

Győződjön meg arról, hogy nem rendelkezik olyan meglévő AD DS-környezettel, amelynek azonos a tartományneve ugyanazon a tartományon, vagy egy egyenrangú virtuális hálózaton. Előfordulhat például, hogy rendelkezik egy *aaddscontoso.com* nevű AD DS tartománnyal, amely Azure-beli virtuális gépeken fut. Ha olyan Azure AD DS felügyelt tartományt próbál engedélyezni, amelynek a *aaddscontoso.com* ugyanaz a tartományneve a virtuális hálózaton, a kért művelet meghiúsul.

Ezt a hibát az okozza, hogy a virtuális hálózaton lévő tartománynév ütközik a tartomány nevével. A DNS-keresés ellenőrzi, hogy egy meglévő AD DS környezet válaszol-e a kért tartománynévre. A hiba megoldásához adjon meg egy másik nevet a felügyelt tartomány beállításához, vagy szüntesse meg a meglévő AD DS tartomány kiépítését, majd próbálja meg újra az Azure AD DS engedélyezését.

### <a name="inadequate-permissions"></a>Nem megfelelő engedélyek

**Hibaüzenet**

*Nem lehet engedélyezni a tartományi szolgáltatásokat ebben az Azure AD-bérlőben. A szolgáltatás nem rendelkezik megfelelő engedélyekkel a (z) "Azure AD Domain Services Sync" nevű alkalmazáshoz. Törölje a "Azure AD Domain Services Sync" nevű alkalmazást, majd próbálja meg engedélyezni az Azure AD-bérlő tartományi szolgáltatásait.*

**Resolution** (Osztás)

Ellenőrizze, hogy van-e *Azure ad Domain Services Sync* nevű alkalmazás az Azure ad-címtárban. Ha az alkalmazás létezik, törölje, majd próbálkozzon újra az Azure AD DS engedélyezéséhez. Ha egy meglévő alkalmazást szeretne megkeresni, és szükség esetén törölni szeretné, hajtsa végre a következő lépéseket:

1. A Azure Portal válassza a bal oldali navigációs menü **Azure Active Directory** elemét.
1. Válassza a **vállalati alkalmazások** lehetőséget. Válassza a *minden alkalmazás* lehetőséget az **alkalmazás típusa** legördülő menüben, majd válassza az **alkalmaz** lehetőséget.
1. A keresőmezőbe írja be *Azure ad Domain Services Sync* kifejezést. Ha az alkalmazás létezik, válassza ki, majd válassza a **Törlés** lehetőséget.
1. Miután törölte az alkalmazást, próbálja meg újból engedélyezni az Azure AD DS.

### <a name="invalid-configuration"></a>Érvénytelen konfiguráció

**Hibaüzenet**

*Nem lehet engedélyezni a tartományi szolgáltatásokat ebben az Azure AD-bérlőben. Az Azure AD-bérlőben található tartományi szolgáltatások alkalmazás nem rendelkezik a tartományi szolgáltatások engedélyezéséhez szükséges engedélyekkel. Törölje az alkalmazást az alkalmazás-azonosító d87dcbc6-a371-462e-88e3-28ad15ec4e64, majd próbálja meg engedélyezni az Azure AD-bérlő tartományi szolgáltatásait.*

**Resolution** (Osztás)

Ellenőrizze, hogy rendelkezik-e egy *AzureActiveDirectoryDomainControllerServices* nevű meglévő alkalmazással az Azure ad-címtárban található *d87dcbc6-a371-462e-88e3-28ad15ec4e64* alkalmazás-azonosítójával. Ha az alkalmazás létezik, törölje, majd próbálja újra az Azure AD DS engedélyezéséhez.

A következő PowerShell-parancsfájl használatával megkeresheti a meglévő alkalmazás-példányokat, és szükség esetén törölheti azt:

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```

### <a name="microsoft-graph-disabled"></a>Microsoft Graph letiltva

**Hibaüzenet**

*Nem lehet engedélyezni a tartományi szolgáltatásokat ebben az Azure AD-bérlőben. Az Microsoft Azure AD alkalmazás le van tiltva az Azure AD-bérlőben. Engedélyezze az alkalmazást az 00000002-0000-0000-C000-000000000000 alkalmazás-azonosítóval, majd próbálja meg engedélyezni az Azure AD-bérlő tartományi szolgáltatásait.*

**Resolution** (Osztás)

Ellenőrizze, hogy a *00000002-0000-0000-C000-000000000000* azonosítójú alkalmazást letiltotta-e. Ez az alkalmazás a Microsoft Azure AD alkalmazás, és Graph API hozzáférést biztosít az Azure AD-bérlőhöz. Az Azure AD-bérlő szinkronizálásához engedélyezni kell az alkalmazást.

Az alkalmazás állapotának vizsgálatához és szükség esetén történő engedélyezéséhez hajtsa végre a következő lépéseket:

1. A Azure Portal válassza a bal oldali navigációs menü **Azure Active Directory** elemét.
1. Válassza a **vállalati alkalmazások** lehetőséget. Válassza a *minden alkalmazás* lehetőséget az **alkalmazás típusa** legördülő menüben, majd válassza az **alkalmaz** lehetőséget.
1. A keresőmezőbe írja be a *00000002-0000-0000-C000-00000000000* értéket. Válassza ki az alkalmazást, majd válassza a **Tulajdonságok** lehetőséget.
1. Ha **engedélyezve van a felhasználók bejelentkezni** a *nem* értékre, állítsa az értéket *Igen* értékre, majd válassza a **Mentés** lehetőséget.
1. Ha engedélyezte az alkalmazást, próbálja meg újból engedélyezni az Azure AD DS.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>A felhasználók nem tudnak bejelentkezni az Azure AD Domain Services által felügyelt tartományba

Ha az Azure AD-bérlő egy vagy több felhasználója nem tud bejelentkezni a felügyelt tartományba, hajtsa végre a következő hibaelhárítási lépéseket:

* **Hitelesítő adatok formátuma** – próbálja meg az UPN formátumot használni a hitelesítő adatok megadásához, például: `dee@aaddscontoso.onmicrosoft.com` . Az egyszerű felhasználónév formátuma az ajánlott módszer a hitelesítő adatok megadására az Azure AD DSban. Győződjön meg arról, hogy az UPN helyesen van konfigurálva az Azure AD-ben.

    A fiókhoz tartozó *sAMAccountName* , például a *AADDSCONTOSO\driley* automatikusan előállíthatók, ha több felhasználó rendelkezik ugyanazzal az UPN-előtaggal a bérlőben, vagy ha az UPN-előtag túl hosszú. Ezért előfordulhat, hogy a fiók *sAMAccountName* -formátuma eltér a helyszíni tartományban várttól vagy használattól.

* **Jelszó-szinkronizálás** – ellenőrizze, hogy engedélyezte-e a jelszó-szinkronizálást a [csak felhőalapú felhasználók][cloud-only-passwords] vagy a [hibrid környezetek számára a Azure ad Connect használatával][hybrid-phs].
    * **Hibrid szinkronizált fiókok:** Ha az érintett felhasználói fiókok egy helyszíni címtárból vannak szinkronizálva, ellenőrizze a következő területeket:
    
      * Telepítette vagy frissítette a ( [Azure ad Connect) legújabb javasolt kiadását](https://www.microsoft.com/download/details.aspx?id=47594).
      * Úgy konfigurálta a Azure AD Connectt, hogy [teljes szinkronizálást végezzen][hybrid-phs].
      * A címtár méretétől függően eltarthat egy ideig, amíg a felhasználói fiókok és a hitelesítő adatok kivonatai elérhetők lesznek a felügyelt tartományban. Győződjön meg arról, hogy elég hosszú ideig vár a felügyelt tartományon való hitelesítés megkísérlése előtt.
      * Ha a probléma az előző lépések ellenőrzése után is fennáll, próbálja meg újraindítani a *Microsoft Azure ad Sync szolgáltatást*. Nyisson meg egy parancssort a Azure AD Connect-kiszolgálóról, majd futtassa a következő parancsokat:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Csak felhőalapú fiókok**: Ha az érintett felhasználói fiók csak felhőalapú felhasználói fiók, akkor győződjön meg arról, hogy a [felhasználó az Azure AD DS engedélyezése után megváltoztatta a jelszavát][cloud-only-passwords]. Ez a jelszó-visszaállítás a felügyelt tartományhoz szükséges hitelesítőadat-kivonatok generálását okozza.

* **Ellenőrizze, hogy a felhasználói fiók aktív-e**: alapértelmezés szerint a felügyelt tartományon 2 percen belül öt érvénytelen jelszóval próbálkozik a felhasználói fiók 30 percen belüli zárolása miatt. A felhasználó nem tud bejelentkezni, amíg a fiók ki van zárva. 30 perc elteltével a felhasználói fiók automatikusan fel lesz oldva.
  * A felügyelt tartomány jelszavas próbálkozásai érvénytelenek, az Azure AD-ben nem zárhatók ki a felhasználói fiókok. A felhasználói fiók csak a felügyelt tartományon belül van zárolva. A felügyeleti [virtuális gép][management-vm]használatával, az Azure ad-ben nem, az *Active Directory felügyeleti konzolon (ADAC)* keresse meg a felhasználói fiók állapotát.
  * Az alapértelmezett zárolási küszöbérték és az időtartam megváltoztatásához részletes jelszóházirendek is [konfigurálható][password-policy] .

* **Külső fiókok** – győződjön meg arról, hogy az érintett felhasználói fiók nem külső fiók az Azure ad-bérlőben. Külső fiókok például Microsoft-fiókok, például `dee@live.com` külső Azure ad-címtárbeli vagy felhasználói fiókok. Az Azure AD DS nem tárolja a külső felhasználói fiókok hitelesítő adatait, így nem jelentkezhetnek be a felügyelt tartományba.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Egy vagy több riasztás van a felügyelt tartományon

Ha vannak aktív riasztások a felügyelt tartományon, akkor előfordulhat, hogy a hitelesítési folyamat megfelelően működik.

A [felügyelt tartomány állapotának megtekintéséhez ellenőrizze][check-health], hogy vannak-e aktív riasztások. Ha a rendszer riasztást jelenít meg, [hárítsa el és][troubleshoot-alerts]hárítsa el azokat.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Az Azure AD-bérlőjéből eltávolított felhasználók nem törlődnek a felügyelt tartományából

Az Azure AD védi a felhasználói objektumok véletlen törlését. Amikor egy Azure AD-bérlőből töröl egy felhasználói fiókot, a rendszer áthelyezi a megfelelő felhasználói objektumot a Lomtárba. Ha ezt a törlési műveletet szinkronizálja a felügyelt tartományba, a megfelelő felhasználói fiók le van jelölve letiltottként. Ez a funkció segítséget nyújt a felhasználói fiók helyreállításához vagy törléséhez.

A felhasználói fiók a felügyelt tartomány letiltott állapotában marad, még akkor is, ha újból létrehoz egy felhasználói fiókot ugyanazzal az egyszerű felhasználónévvel az Azure AD-címtárban. A felhasználói fióknak a felügyelt tartományból való eltávolításához kényszerített törlést kell végeznie az Azure AD-bérlőből.

Ha egy felügyelt tartományból teljesen el szeretné távolítani a felhasználói fiókokat, törölje a felhasználót véglegesen az Azure AD-bérlőből a [Remove-MsolUser PowerShell-][Remove-MsolUser] parancsmaggal a (z `-RemoveFromRecycleBin` ) paraméterrel.

## <a name="next-steps"></a>Következő lépések

Ha továbbra is problémákba ütközik, [Nyisson meg egy Azure-támogatási kérést][azure-support] további hibaelhárítási segítségért.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
