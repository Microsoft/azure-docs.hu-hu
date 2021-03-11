---
title: PowerShell-minták a Azure Active Directory-alkalmazások kezeléséhez
description: Ezek a PowerShell-minták a Azure Active Directory-bérlőben felügyelt alkalmazásokhoz használatosak. Ezeket a parancsfájlokat használhatja a titkokkal és tanúsítványokkal kapcsolatos lejárati információk megkereséséhez.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 46297f7f0f648c8bebc887a9093e25dfea99f695
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561500"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Azure Active Directory PowerShell-példák az alkalmazások kezeléséhez

Az alábbi táblázat az Azure AD-alkalmazások kezeléséhez használható PowerShell-parancsfájlokra mutató hivatkozásokat tartalmaz. A mintákhoz a következők szükségesek:
- A [AzureAD v2 PowerShell a Graph modulhoz](/powershell/azure/active-directory/install-adv2) vagy
- A [Graph modul előzetes verziójának AzureAD v2 PowerShell-verziója](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), hacsak másként nincs jelezve.

A példákban használt parancsmagokkal kapcsolatos további információkért lásd: [alkalmazások](/powershell/module/azuread/#applications).

| Hivatkozás | Leírás |
|---|---|
|**Application Management-parancsfájlok**||
| [Titkok és tanúsítványok exportálása (alkalmazás-regisztrációk)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | A titkok és tanúsítványok exportálása Azure Active Directory bérlőben az alkalmazások regisztrálásához. |
| [Titkok és tanúsítványok exportálása (vállalati alkalmazások)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | A Azure Active Directory bérlőben lévő nagyvállalati alkalmazásokhoz tartozó titkok és tanúsítványok exportálása. |
| [A lejáró titkok és tanúsítványok exportálása](scripts/powershell-export-apps-with-expriring-secrets.md) | Alkalmazások exportálása lejáró titkokkal és tanúsítványokkal Azure Active Directory bérlőben. |
| [A titkok és tanúsítványok exportálása a szükséges dátum után lejár](scripts/powershell-export-apps-with-secrets-beyond-required.md) | A titkokat és tanúsítványokat tartalmazó alkalmazások exportálása a Azure Active Directory bérlőn megkövetelt időpontnál tovább lejár. |
