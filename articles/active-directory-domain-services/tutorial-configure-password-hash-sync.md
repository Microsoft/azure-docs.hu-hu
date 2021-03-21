---
title: Jelszó-kivonatolási szinkronizálás engedélyezése a Azure AD Domain Serviceshoz | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti a jelszó-kivonatok szinkronizálását a Azure AD Connect használatával egy Azure Active Directory Domain Services felügyelt tartományhoz.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: e83d8941d1be7fd36f53a881a21716252ad01954
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618348"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Oktatóanyag: a jelszó-szinkronizálás engedélyezése Azure Active Directory Domain Services hibrid környezetekben

Hibrid környezetek esetén egy Azure Active Directory (Azure AD) bérlő konfigurálható úgy, hogy Azure AD Connect használatával szinkronizáljon a helyszíni Active Directory Domain Services (AD DS) környezettel. Alapértelmezés szerint a Azure AD Connect nem szinkronizálja az Azure Active Directory Domain Services (Azure AD DS) számára szükséges örökölt NT LAN Manager-(NTLM-) és Kerberos-jelszavak kivonatait.

Ha az Azure AD DSt a helyszíni AD DS-környezetből szinkronizált fiókokkal szeretné használni, konfigurálnia kell Azure AD Connect az NTLM-és Kerberos-hitelesítéshez szükséges jelszavak kivonatok szinkronizálásához. Azure AD Connect konfigurálása után a helyszíni fiók létrehozása vagy a jelszó módosítása esemény is szinkronizálja az örökölt jelszó-kivonatokat az Azure AD-be.

Ezeket a lépéseket nem kell végrehajtania, ha csak felhőalapú fiókokat használ helyszíni AD DS környezet nélkül, vagy ha *erőforrás-erdőt* használ. Az erőforrás-erdőt használó felügyelt tartományok esetében a helyszíni jelszó-kivonatok soha nem szinkronizálhatók. A helyszíni fiókok hitelesítése az erdőszintű megbízhatóság (ok) t használja a saját AD DS tartományvezérlőkre.

Ez az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Miért van szükség örökölt NTLM-és Kerberos-jelszó-kivonatokra
> * A Azure AD Connect örökölt jelszó-kivonatolási szinkronizálásának konfigurálása

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra lesz szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, amely Azure AD Connect használatával szinkronizálva van egy helyszíni címtárral.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
    * Ha szükséges, [engedélyezze a jelszó-kivonatok szinkronizálását Azure ad Connect][enable-azure-ad-connect].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, [hozzon létre és konfiguráljon egy Azure Active Directory Domain Services felügyelt tartományt][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Jelszó-kivonatolási szinkronizálás Azure AD Connect használatával

A Azure AD Connect objektumok, például felhasználói fiókok és csoportok szinkronizálására használható egy helyszíni AD DS-környezetből egy Azure AD-bérlőbe. A folyamat részeként a jelszó-kivonatolási szinkronizálás lehetővé teszi, hogy a fiókok ugyanazt a jelszót használják a helyszíni AD DS környezetben és az Azure AD-ben.

A felügyelt tartományon lévő felhasználók hitelesítéséhez az Azure AD DS az NTLM-és Kerberos-hitelesítéshez megfelelő formátumú jelszó-kivonatokat kell használnia. Az Azure AD nem az NTLM-vagy Kerberos-hitelesítéshez szükséges formátumban tárolja a jelszó-kivonatokat, amíg nem engedélyezi az Azure-AD DS a bérlő számára. Biztonsági okokból az Azure AD nem tárolja a jelszó hitelesítő adatait a tiszta szöveges formában. Ezért az Azure AD nem tudja automatikusan előállítani ezeket az NTLM-vagy Kerberos-jelszó-kivonatokat a felhasználók meglévő hitelesítő adatai alapján.

A Azure AD Connect konfigurálható úgy, hogy szinkronizálja az Azure-AD DS szükséges NTLM-vagy Kerberos-jelszavak kivonatait. Győződjön meg arról, hogy elvégezte a [jelszó-kivonatok szinkronizálásához szükséges Azure ad Connect engedélyezésének][enable-azure-ad-connect]lépéseit. Ha Azure AD Connect meglévő példányát használta, [töltse le és frissítse a legújabb verzióra][azure-ad-connect-download] , és győződjön meg róla, hogy szinkronizálni tudja az NTLM-és Kerberos-alapú örökölt jelszavak kivonatait. Ez a funkció nem érhető el a Azure AD Connect korábbi kiadásaiban vagy az örökölt rSync eszközzel. Azure AD Connect *1.1.614.0* vagy újabb verzió szükséges.

> [!IMPORTANT]
> A Azure AD Connect csak a helyszíni AD DS környezetekkel való szinkronizálásra kell telepíteni és konfigurálni. Nem támogatott Azure AD Connect telepítése Azure AD DS felügyelt tartományba az objektumok Azure AD-be való visszaszinkronizálásához.

## <a name="enable-synchronization-of-password-hashes"></a>Jelszó-kivonatok szinkronizálásának engedélyezése

A Azure AD Connect az Azure AD-vel való szinkronizálásra van telepítve és konfigurálva, most konfigurálja az örökölt jelszó-kivonatoló szinkronizálást az NTLM és a Kerberos számára. A szükséges beállítások konfigurálásához PowerShell-parancsfájlt kell használni, majd teljes jelszó-szinkronizálást kell elindítani az Azure AD-be. Ha a jelszó-kivonat szinkronizációs folyamata Azure AD Connect, a felhasználók az Azure-AD DSon keresztül jelentkezhetnek be az alkalmazásokba, amelyek örökölt NTLM-vagy Kerberos-jelszó-kivonatokat használnak.

1. A Azure AD Connect telepített számítógépen a Start menüben nyissa meg a **Azure AD Connect > szinkronizációs szolgáltatást**.
1. Válassza az **Összekötők** fület. A helyszíni AD DS környezet és az Azure AD közötti szinkronizálás létrehozásához használt kapcsolati adatok listája.

    A **típus** a helyszíni AD DS-összekötőhöz tartozó *Windows Azure Active Directory (Microsoft)* értéket jelöli az Azure ad-összekötőhöz vagy a *Active Directory Domain Serviceshoz* . Jegyezze fel a PowerShell-parancsfájlban használandó összekötők nevét a következő lépésben.

    ![Az összekötők neveinek listázása a Sync Service Managerban](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    Ebben a példában a képernyőképen a következő összekötők használatosak:

    * Az Azure AD-összekötő neve *contoso.onmicrosoft.com-HRE*
    * A helyszíni AD DS-összekötő neve *onprem.contoso.com*

1. Másolja és illessze be a következő PowerShell-parancsfájlt a számítógépre, amelyen Azure AD Connect telepítve van. A parancsfájl egy teljes jelszó-szinkronizálást indít el, amely örökölt jelszó-kivonatokat tartalmaz. Frissítse a `$azureadConnector` és a `$adConnector` változókat az előző lépésből származó összekötők neveivel.

    Futtassa ezt a szkriptet minden egyes AD-erdőben, hogy szinkronizálja a helyszíni fiók NTLM-és Kerberos-jelszavas kivonatait az Azure AD-be.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    
    Import-Module "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    A címtár méretétől függően a fiókok és csoportok száma alapján a régi jelszó-kivonatok Azure AD-ba való szinkronizálása hosszabb időt is igénybe vehet. A rendszer ezután szinkronizálja a jelszavakat a felügyelt tartományba az Azure AD-vel való szinkronizálás után.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta a következőket:

> [!div class="checklist"]
> * Miért van szükség örökölt NTLM-és Kerberos-jelszó-kivonatokra
> * A Azure AD Connect örökölt jelszó-kivonatolási szinkronizálásának konfigurálása

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan működik a szinkronizáció Azure AD Domain Services felügyelt tartományban](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
