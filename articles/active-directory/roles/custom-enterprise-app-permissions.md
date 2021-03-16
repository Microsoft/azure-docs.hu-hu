---
title: Alkalmazás-engedélyek a Azure Active Directory egyéni szerepköreihez | Microsoft Docs
description: Az egyéni Azure AD-szerepkörökre vonatkozó vállalati alkalmazás-engedélyek előzetes verziója a Azure Portal, a PowerShellben vagy a Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: d2159540c688a63082efb792fd4d261062ef65d9
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466793"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Vállalati alkalmazások engedélyei a Azure Active Directory egyéni szerepköreihez

Ez a cikk a Azure Active Directory (Azure AD) egyéni szerepkör-definícióinak jelenleg elérhető vállalati alkalmazási engedélyeit tartalmazza. Ebben a cikkben a gyakori forgatókönyvekhez és a vállalati alkalmazás engedélyeinek teljes listájához tartozó engedélyezési listákat talál. Az alkalmazásproxy engedélyei jelenleg nem jelennek meg ebben a kiadásban.

## <a name="required-license-plan"></a>Szükséges licencelési csomag

A szolgáltatás használatához prémium szintű Azure AD P1 licencre van szükség az Azure AD-szervezet számára. A követelményeinek leginkább megfelelő licenc kiválasztásáról lásd [az ingyenes, alapszintű és prémium kiadások általánosan elérhető szolgáltatásait összehasonlító cikket](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="enterprise-application-permissions"></a>Vállalati alkalmazás engedélyei

További információ az engedélyek használatáról: [Egyéni szerepkörök társítása vállalati alkalmazások kezeléséhez](custom-enterprise-apps.md)

### <a name="assigning-users-or-groups-to-an-application"></a>Felhasználók vagy csoportok társítása egy alkalmazáshoz

Azon felhasználók és csoportok hozzárendelésének delegálása, amelyek hozzáférhetnek az SAML-alapú egyszeri bejelentkezési alkalmazásokhoz. A szükséges engedélyek

- Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update

### <a name="creating-gallery-applications"></a>Katalógusbeli alkalmazások létrehozása

Az Azure AD Gallery-alkalmazások (például ServiceNow, F5, Salesforce) létrehozásának delegálására többek között. Szükséges engedélyek:

- Microsoft. Directory/applicationTemplates/példány

### <a name="configuring-basic-saml-urls"></a>Alapszintű SAML URL-címek konfigurálása

Az SAML-alapú egyszeri bejelentkezéses alkalmazások alapszintű SAML-konfigurációinak frissítésének és olvasásának delegálása. Szükséges engedélyek:

- Microsoft. Directory/servicePrincipals/hitelesítés/frissítés
- Microsoft. Directory/Applications. myOrganization/Authentication/Update

### <a name="rolling-over-or-creating-signing-certs"></a>Aláírási tanúsítványok átadása vagy létrehozása

Az SAML-alapú egyszeri bejelentkezési alkalmazások aláíró tanúsítványainak felügyeletének delegálása. Engedélyek szükségesek.

Microsoft. Directory/alkalmazások/hitelesítő adatok/frissítés

### <a name="update-expiring-sign-in-cert-notification-email-address"></a>A tanúsítvány lejáratára vonatkozó bejelentkezési értesítő e-mail cím frissítése

A lejárt bejelentkezési tanúsítványok frissítésének delegálása az SAML-alapú egyszeri bejelentkezési alkalmazások e-mail-címeinek értesítése. Szükséges engedélyek:

- Microsoft. Directory/Applications. myOrganization/Authentication/Update
- Microsoft. Directory/Applications. myOrganization/engedélyek/frissítés
- Microsoft. Directory/servicePrincipals/hitelesítés/frissítés
- Microsoft. Directory/servicePrincipals/Basic/Update

### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>SAML-jogkivonat aláírásának és bejelentkezési algoritmusának kezelése

Az SAML-jogkivonat aláírásának és bejelentkezési algoritmusának delegálása az SAML-alapú egyszeri bejelentkezési alkalmazásokhoz. Szükséges engedélyek:

- Microsoft. Directory/applicationPolicies/Basic/Update
- Microsoft. Directory/alkalmazások/hitelesítés/frissítés
- Microsoft. Directory/servicePrincipals/házirendek/frissítés

### <a name="manage-user-attributes-and-claims"></a>Felhasználói attribútumok és jogcímek kezelése

Az SAML-alapú egyszeri bejelentkezés alkalmazásaihoz tartozó felhasználói attribútumok és jogcímek létrehozásának, törlésének és frissítésének delegálása. Szükséges engedélyek:

- Microsoft. Directory/applicationPolicies/Basic/Update
- Microsoft. Directory/alkalmazások/hitelesítés/frissítés
- Microsoft. Directory/servicePrincipals/házirendek/frissítés

## <a name="app-provisioning-permissions"></a>Alkalmazás-kiépítési engedélyek

Ha bármilyen írási műveletet hajt végre, például a feladatot, a sémát vagy a hitelesítő adatokat a felhasználói felületen, az olvasási engedélyre is szüksége lesz a kiépítési lap megtekintéséhez.

Ha a hatókört az összes felhasználóra és csoportra, illetve a hozzárendelt felhasználókra és csoportokra állítja be, a synchronizationJob és a synchronizationCredentials engedély is szükséges.

### <a name="turn-on-or-restart-provisioning-jobs"></a>Kiépítési feladatok bekapcsolása vagy újraindítása

A kiépítési feladatok bekapcsolására, kikapcsolására és újraindítására vonatkozó lehetőség delegálása. Szükséges engedélyek:

- Microsoft. Directory/servicePrincipals/synchronizationJobs/Manage  

### <a name="configure-the-provisioning-schema"></a>A kiépítési séma konfigurálása  

Az attribútum-hozzárendelés frissítéseinek delegálása. Szükséges engedélyek:

- Microsoft. Directory/servicePrincipals/synchronizationSchema/Manage  

### <a name="read-provisioning-settings-associated-with-the-application-object"></a>Az alkalmazásobjektum-objektumhoz társított kiépítési beállítások olvasása

Az objektumhoz társított kiépítési beállítások beolvasásának lehetősége. Szükséges engedélyek:

- Microsoft. Directory/alkalmazások/szinkronizálás/standard/olvasás

### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>Az egyszerű szolgáltatáshoz társított kiépítési beállítások olvasása

Az egyszerű szolgáltatáshoz társított kiépítési beállítások beolvasásának lehetősége. Szükséges engedélyek:

- Microsoft. Directory/servicePrincipals/szinkronizálás/standard/olvasás

### <a name="authorize-application-access-for-provisioning"></a>Alkalmazás-hozzáférés engedélyezése a kiépítés számára  

Az alkalmazás-hozzáférés engedélyezésének engedélyezése az üzembe helyezéshez. Példa a bemeneti OAuth tulajdonosi jogkivonatra. Szükséges engedélyek:

- Microsoft. Directory/servicePrincipals/synchronizationCredentials/Manage

## <a name="full-list-of-permissions"></a>Engedélyek teljes listája

Engedély | Leírás
---------- | -----------
Microsoft. Directory/applicationPolicies/allProperties/READ | Az alkalmazás-házirendek összes tulajdonságának olvasása.
Microsoft. Directory/applicationPolicies/allProperties/Update | Az alkalmazás-házirendek összes tulajdonságának frissítése.
Microsoft. Directory/applicationPolicies/Basic/Update | Az alkalmazás-házirendek szabványos tulajdonságainak frissítése.
Microsoft. Directory/applicationPolicies/Create | Alkalmazás-házirendek létrehozása.
Microsoft. Directory/applicationPolicies/createAsOwner | Alkalmazás-házirendek létrehozása. A létrehozó hozzá lesz adva az első tulajdonosként.
Microsoft. Directory/applicationPolicies/delete | Alkalmazás-házirendek törlése.
Microsoft. Directory/applicationPolicies/tulajdonosok/olvasás | Az alkalmazás-szabályzatok tulajdonosának beolvasása.
Microsoft. Directory/applicationPolicies/owners/Update | Frissítse az alkalmazás-szabályzatok tulajdonos tulajdonságát.
Microsoft. Directory/applicationPolicies/policyAppliedTo/READ | Az objektumok listájára alkalmazott alkalmazás-házirendek olvasása.
Microsoft. Directory/applicationPolicies/standard/olvasás | Az alkalmazás-házirendek szabványos tulajdonságainak olvasása.
Microsoft. Directory/servicePrincipals/allProperties/allTasks | ServicePrincipals létrehozása és törlése, valamint az összes tulajdonság olvasása és frissítése Azure Active Directoryban.
Microsoft. Directory/servicePrincipals/allProperties/READ | A servicePrincipals összes tulajdonságának olvasása.
Microsoft. Directory/servicePrincipals/allProperties/Update | A servicePrincipals összes tulajdonságának frissítése.
Microsoft. Directory/servicePrincipals/appRoleAssignedTo/READ | Az egyszerű szolgáltatás szerepkör-hozzárendeléseinek beolvasása.
Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Egyszerű szolgáltatás szerepkör-hozzárendeléseinek frissítése.
Microsoft. Directory/servicePrincipals/appRoleAssignments/READ | Az egyszerű szolgáltatásokhoz hozzárendelt szerepkör-hozzárendelések olvasása.
Microsoft. Directory/servicePrincipals/hallgatóság/frissítés | A célközönség tulajdonságainak frissítése az egyszerű szolgáltatásokban.
Microsoft. Directory/servicePrincipals/hitelesítés/frissítés | Az egyszerű szolgáltatásnév hitelesítési tulajdonságainak frissítése.
Microsoft. Directory/servicePrincipals/Basic/Update | Egyszerű tulajdonságok frissítése az egyszerű szolgáltatásokban.
Microsoft. Directory/servicePrincipals/Create | Egyszerű szolgáltatásnév létrehozása.
Microsoft. Directory/servicePrincipals/createAsOwner | Egyszerű szolgáltatásnév létrehozása. A létrehozó hozzá lesz adva az első tulajdonosként.
Microsoft. Directory/servicePrincipals/hitelesítő adatok/frissítés | A hitelesítő adatok tulajdonságainak frissítése az egyszerű szolgáltatásokban.
Microsoft. Directory/servicePrincipals/delete | Egyszerű szolgáltatásnév törlése.
Microsoft. Directory/servicePrincipals/letiltás | Egyszerű szolgáltatásnév letiltása.
Microsoft. Directory/servicePrincipals/Enable | Egyszerű szolgáltatásnév engedélyezése.
Microsoft. Directory/servicePrincipals/getPasswordSingleSignOnCredentials | Olvasási jelszó egyszeri bejelentkezési hitelesítő adatai az egyszerű szolgáltatásokban.
Microsoft. Directory/servicePrincipals/managePasswordSingleSignOnCredentials | A jelszavas egyszeri bejelentkezés hitelesítő adatainak kezelése az egyszerű szolgáltatásokban.
Microsoft. Directory/servicePrincipals/Oauth2permissiongrant objektumok/READ | Az egyszerű szolgáltatásokra vonatkozó delegált engedélyek beolvasása.
Microsoft. Directory/servicePrincipals/tulajdonosok/olvasás | Az egyszerű szolgáltatások tulajdonosainak beolvasása.
Microsoft. Directory/servicePrincipals/owners/Update | Az egyszerű szolgáltatások tulajdonosainak frissítése.
Microsoft. Directory/servicePrincipals/engedélyek/frissítés |  
Microsoft. Directory/servicePrincipals/házirendek/olvasás | Az egyszerű szolgáltatásokra vonatkozó szabályzatok beolvasása.
Microsoft. Directory/servicePrincipals/házirendek/frissítés | Az egyszerű szolgáltatásokra vonatkozó szabályzatok frissítése.
Microsoft. Directory/servicePrincipals/standard/olvasás | Az egyszerű szolgáltatásnév általános tulajdonságainak olvasása.
Microsoft. Directory/servicePrincipals/szinkronizálás/standard/olvasás | Az egyszerű szolgáltatáshoz társított kiépítési beállítások olvasása.
Microsoft. Directory/servicePrincipals/tag/frissítés | A címkék tulajdonság frissítése az egyszerű szolgáltatásokban.
Microsoft. Directory/applicationTemplates/példány | Katalógusbeli alkalmazások példányainak példánya alkalmazás-sablonokból.
Microsoft. Directory/auditLogs/allProperties/READ | Naplófájlok olvasása.
Microsoft. Directory/signInReports/allProperties/READ | Bejelentkezési jelentések olvasása.
Microsoft. Directory/alkalmazások/szinkronizálás/standard/olvasás | Az Application objektumhoz társított kiépítési beállítások olvasása.
Microsoft. Directory/servicePrincipals/synchronizationJobs/Manage | Az egyszerű szolgáltatás erőforrásaihoz tartozó feladatok szinkronizálásának minden aspektusának kezelése
Microsoft. Directory/servicePrincipals/szinkronizálás/standard/olvasás | Az egyszerű szolgáltatásokhoz társított kiépítési beállítások olvasása
Microsoft. Directory/servicePrincipals/synchronizationSchema/Manage | A séma-szinkronizálás összes aspektusának kezelése a szolgáltatás egyszerű erőforrásaihoz
Microsoft. Directory/provisioningLogs/allProperties/READ | A kiépítési naplók összes tulajdonságának olvasása

## <a name="next-steps"></a>Következő lépések

- [Egyéni szerepkörök létrehozása a Azure Portal, az Azure AD PowerShell és a Graph API használatával](custom-create.md)
- [Szerepkör-hozzárendelések felsorolása](view-assignments.md)
