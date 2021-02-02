---
title: PowerShell-minták az Azure AD Application Proxy
description: Ezekkel a PowerShell-mintákkal az Azure AD Application Proxy segítségével információkat kaphat a címtárban található alkalmazásproxy-alkalmazásokról és-összekötőről, felhasználókat és csoportokat rendelhet az alkalmazásokhoz, valamint tanúsítvány-információkat kérhet le.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 117b4fec13c22ffb2f3163d50be44734d24ef3fa
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259101"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD PowerShell-példák az Azure AD Application Proxy

Az alábbi táblázat az Azure AD Application Proxy PowerShell-parancsfájlokra mutató hivatkozásokat tartalmaz. A mintákhoz a Graph modul [AzureAD v2 PowerShell](/powershell/azure/active-directory/install-adv2) -je vagy a [Graph modul előzetes verziójának AzureAD v2](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)PowerShell-verziója szükséges, hacsak másként nincs jelezve.


A példákban használt parancsmagokkal kapcsolatos további információkért lásd: alkalmazásproxy- [alkalmazás kezelése](/powershell/module/azuread/#application_proxy_application_management) és alkalmazásproxy- [összekötő kezelése](/powershell/module/azuread/#application_proxy_connector_management).

| Hivatkozás | Leírás |
|---|---|
|**Alkalmazásproxy-alkalmazások**||
| [Az összes alkalmazásproxy-alkalmazás alapszintű információinak listázása](scripts/powershell-get-all-app-proxy-apps-basic.md) | A címtárban lévő összes alkalmazásproxy-alkalmazás alapvető információit (AppId, DisplayName, ObjId) listázza. |
| [Az összes alkalmazásproxy-alkalmazás részletes adatainak listázása](scripts/powershell-get-all-app-proxy-apps-extended.md) | Felsorolja a címtárban található alkalmazásproxy-alkalmazásokkal kapcsolatos kiterjesztett információkat (AppId, DisplayName, Alkalmazásazonosítójának, InternalUrl, ExternalAuthenticationType).  |
| [Az összes alkalmazásproxy-alkalmazás listázása összekötő csoport szerint](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Felsorolja a címtárban található alkalmazásproxy-alkalmazásokkal kapcsolatos információkat, valamint azt, hogy az alkalmazások melyik összekötő csoportokba vannak rendelve. |
| [Az összes alkalmazásproxy-alkalmazás beolvasása jogkivonat-élettartam-házirenddel](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Felsorolja a címtárban található összes alkalmazásproxy-alkalmazást a token élettartama szabályzattal és annak részleteivel. Ehhez a mintához a [Graph modul előzetes verziójának AzureAD v2 PowerShell-verziója](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)szükséges. |
|**Összekötőcsoportok**||
| [Az összes összekötő-csoport és összekötő beolvasása a címtárban](scripts/powershell-get-all-connectors.md) | A címtárban lévő összes összekötő-csoportot és összekötőt listázza. |
| [Összekötő csoporthoz rendelt összes alkalmazás áthelyezése egy másik összekötő csoportba](scripts/powershell-move-all-apps-to-connector-group.md) | Áthelyezi az összes olyan alkalmazást, amely jelenleg hozzá van rendelve egy összekötő-csoporthoz egy másik összekötő csoportba. |
|**Hozzárendelt felhasználók és csoportok**||
| [Alkalmazásproxy-alkalmazáshoz rendelt felhasználók és csoportok megjelenítése](scripts/powershell-display-users-group-of-app.md) | Egy adott alkalmazásproxy-alkalmazáshoz rendelt felhasználókat és csoportokat sorolja fel. |
| [Felhasználó társítása egy alkalmazáshoz](scripts/powershell-assign-user-to-app.md) | Egy adott felhasználót rendel hozzá egy alkalmazáshoz. |
| [Csoport társítása egy alkalmazáshoz](scripts/powershell-assign-group-to-app.md) | Egy adott csoportot rendel hozzá egy alkalmazáshoz. |
|**Külső URL-konfiguráció**||
| [Az összes alkalmazásproxy-alkalmazás beolvasása az alapértelmezett tartományokkal (. msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Az alapértelmezett tartományokat (. msappproxy.net) használó alkalmazásproxy-alkalmazásokat sorolja fel. |
| [Az összes alkalmazásproxy-alkalmazás beolvasása a helyettesítő karakteres közzététel használatával](scripts/powershell-get-all-wildcard-apps.md) | A helyettesítő karakteres közzétételt használó összes alkalmazásproxy-alkalmazás felsorolása. |
|**Egyéni tartományi konfiguráció**||
| [Az összes alkalmazásproxy-alkalmazás beolvasása egyéni tartományokkal és tanúsítványokkal kapcsolatos információk használatával](scripts/powershell-get-all-custom-domains-and-certs.md) | Felsorolja az összes olyan alkalmazásproxy-alkalmazást, amely egyéni tartományokat használ, valamint az egyéni tartományokhoz társított tanúsítvány-információkat. |
| [A tanúsítvány feltöltése nélkül közzétett összes Azure AD-alkalmazásproxy letöltése](scripts/powershell-get-all-custom-domain-no-cert.md) | Felsorolja az összes olyan alkalmazásproxy-alkalmazást, amely egyéni tartományokat használ, de nincs feltöltve érvényes TLS/SSL-tanúsítvány. |
| [Az azonos tanúsítvánnyal közzétett összes Azure AD-alkalmazásproxy alkalmazásának beolvasása](scripts/powershell-get-custom-domain-identical-cert.md) | Felsorolja az azonos tanúsítvánnyal közzétett összes Azure AD-alkalmazásproxy alkalmazást. |
| [Az azonos tanúsítvánnyal közzétett összes Azure AD-alkalmazásproxy beolvasása és cseréje](scripts/powershell-get-custom-domain-replace-cert.md) | Az azonos tanúsítvánnyal közzétett Azure AD proxy alkalmazások esetében lehetővé teszi a tanúsítvány tömeges cseréjét. |
