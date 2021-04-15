---
title: PowerShell-minták Azure Active Directory alkalmazáskezeléshez
description: Ezek a PowerShell-minták a saját bérlőben Azure Active Directory alkalmazásokhoz használhatók. Ezekkel a példaszk szkriptekkel lejárati információkat találhat a titkos kulcsokról és tanúsítványokról.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 6b6314935bfafc2fe6288c30619e1d01242a991d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378822"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Azure Active Directory PowerShell-példák az alkalmazáskezeléshez

Az alábbi táblázat az Azure AD-alkalmazáskezelés PowerShell-példaszk szkriptpépéire mutató hivatkozásokat tartalmaz. Ezekhez a mintákhoz a következő két szükséges:
- Az [AzureAD V2 PowerShell for Graph modul,](/powershell/azure/active-directory/install-adv2) vagy
- Az [AzureAD V2 PowerShell for Graph-modul](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)előzetes verziója, hacsak másként nincs jelezve.

A mintákban használt parancsmagokkal kapcsolatos további információkért lásd: [Applications (Alkalmazások).](/powershell/module/azuread/#applications)

| Hivatkozás | Leírás |
|---|---|
|**Alkalmazáskezelési szkriptek**||
| [Titkos kulcsok és tanúsítványok exportálása (alkalmazásregisztrációk)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Titkos kulcsok és tanúsítványok exportálása az alkalmazásregisztrációkhoz Azure Active Directory bérlőben. |
| [Titkos kulcsok és tanúsítványok exportálása (vállalati alkalmazások)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Titkos kulcsok és tanúsítványok exportálása vállalati alkalmazásokhoz Azure Active Directory bérlőben. |
| [Lejáró titkos kulcsok és tanúsítványok exportálása](scripts/powershell-export-apps-with-expriring-secrets.md) | Exportálja az alkalmazásregisztrációkat a lejáró titkos adatokat és tanúsítványokat, valamint azok tulajdonosait a Azure Active Directory bérlőben. |
| [Titkos kulcsok és tanúsítványok exportálása a szükséges dátumon túl](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Exportálja a titkos adatokat és tanúsítványokat a bérlőben a szükséges dátumnál Azure Active Directory tanúsítványokat. Ez a nem interaktív Oauth-Client_Credentials használja. |
