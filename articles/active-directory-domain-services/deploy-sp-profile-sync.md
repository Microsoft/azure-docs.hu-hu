---
title: A SharePoint felhasználói profil szolgáltatás engedélyezése az Azure AD DS segítségével | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy Azure Active Directory Domain Services felügyelt tartományt a SharePoint Server-profilok szinkronizálásának támogatásához
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 9d983015927d2635f69a327a9c5b168056542519
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77613868"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Azure Active Directory Domain Services konfigurálása a SharePoint Server felhasználói profilok szinkronizálásának támogatásához

A SharePoint-kiszolgáló tartalmaz egy szolgáltatást a felhasználói profilok szinkronizálásához. Ez a funkció lehetővé teszi, hogy a felhasználói profilok központi helyen legyenek tárolva, és elérhetők legyenek több SharePoint-hely és-Farm között. A SharePoint Server felhasználói profil szolgáltatás konfigurálásához meg kell adni a megfelelő engedélyeket egy Azure Active Directory Domain Services (Azure AD DS) felügyelt tartományban. További információ: [felhasználói profil szinkronizálása a SharePoint-kiszolgálón](https://technet.microsoft.com/library/hh296982.aspx).

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure AD DSt a SharePoint Server felhasználói profil szinkronizálási szolgáltatásának engedélyezéséhez.

## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, fejezze be az oktatóanyagot [egy Azure Active Directory Domain Services-példány létrehozásához és konfigurálásához][create-azure-ad-ds-instance].
* Az Azure AD DS felügyelt tartományhoz csatlakoztatott Windows Server Management VM.
    * Ha szükséges, fejezze be az oktatóanyagot [egy felügyeleti virtuális gép létrehozásához][tutorial-create-management-vm].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.
* Egy SharePoint-szolgáltatásfiók a felhasználói profil szinkronizálási szolgáltatásához.
    * Ha szükséges, tekintse [meg a felügyeleti és szolgáltatási fiókok tervezése a SharePoint-kiszolgálón][sharepoint-service-account]című témakört.

## <a name="service-accounts-overview"></a>Szolgáltatásfiókok áttekintése

Az Azure AD DS felügyelt tartományokban az **HRE DC szolgáltatásfiókok** nevű biztonsági csoport a *felhasználók* szervezeti egység (OU) részeként létezik. A biztonsági csoport tagjai a következő jogosultságokat delegálják:

- **Replikálja a címtár-módosítási** jogosultságot a gyökérszintű dse.
- A címtár-módosítások jogosultságának **replikálása** a *konfiguráció* névhasználati környezetében (`cn=configuration` tároló).

A **HRE DC Service accounts** biztonsági csoport tagja a beépített **Windows 2000-kompatibilis hozzáférésnek**is.

Ha hozzáadja ezt a biztonsági csoportot, a SharePoint Server felhasználói profil szinkronizálási szolgáltatásának szolgáltatási fiókja megkapja a szükséges jogosultságokat a megfelelő működéshez.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>A SharePoint Server felhasználói profiljának szinkronizálásának engedélyezése

A SharePoint Server szolgáltatási fiókjának megfelelő jogosultságokkal kell rendelkeznie a címtár változásainak replikálásához, és lehetővé kell tennie a SharePoint Server felhasználói profilok szinkronizálásának megfelelő működését. A jogosultságok megadásához adja hozzá a SharePoint felhasználói profil szinkronizálásához használt szolgáltatásfiókot a **HRE DC szolgáltatásfiók** csoporthoz.

Az Azure AD DS felügyeleti virtuális gépről hajtsa végre a következő lépéseket:

> [!NOTE]
> Egy Azure AD DS felügyelt tartományban lévő csoporttagság szerkesztéséhez be kell jelentkeznie egy olyan felhasználói fiókba, amely az *HRE DC-rendszergazdák* csoport tagja.

1. A kezdőképernyőn válassza a **felügyeleti eszközök**elemet. Megjelenik a rendelkezésre álló felügyeleti eszközök listája, amely az oktatóanyagban a [felügyeleti virtuális gép létrehozásához][tutorial-create-management-vm]lett telepítve.
1. A csoporttagság kezeléséhez válassza a **Active Directory felügyeleti központ** elemet a felügyeleti eszközök listájából.
1. A bal oldali ablaktáblán válassza ki az Azure AD DS felügyelt tartományát, például *aaddscontoso.com*. Megjelenik a meglévő szervezeti egységek és erőforrások listája.
1. Válassza ki a **felhasználók** szervezeti egységet, majd válassza a *HRE DC szolgáltatásfiók* biztonsági csoportot.
1. Válassza a **tagok**lehetőséget, majd válassza a **Hozzáadás...** lehetőséget.
1. Adja meg a SharePoint-szolgáltatásfiók nevét, majd kattintson az **OK gombra**. A következő példában a SharePoint-szolgáltatásfiók neve *SPAdmin*:

    ![Adja hozzá a SharePoint-szolgáltatásfiókot a HRE DC szolgáltatásfiók biztonsági csoportjához.](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Következő lépések

További információ: [Active Directory tartományi szolgáltatások engedélyek megadása a SharePoint-kiszolgálón a profilok szinkronizálásához](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
