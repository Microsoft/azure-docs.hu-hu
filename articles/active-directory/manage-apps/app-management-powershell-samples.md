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
ms.openlocfilehash: f5f7ec8245a43440a400b9ca6b55bf1093eb62cc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636188"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Azure Active Directory PowerShell-példák az alkalmazások kezeléséhez

Az alábbi táblázat az Azure AD-alkalmazások kezeléséhez használható PowerShell-parancsfájlokra mutató hivatkozásokat tartalmaz. A mintákhoz a következők szükségesek:
- A [AzureAD v2 PowerShell a Graph modulhoz](/powershell/azure/active-directory/install-adv2) vagy
- A [Graph modul előzetes verziójának AzureAD v2 PowerShell-verziója](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), hacsak másként nincs jelezve.

A példákban használt parancsmagokkal kapcsolatos további információkért lásd: [alkalmazások](/powershell/module/azuread/#applications).

| Hivatkozás | Description |
|---|---|
|**Application Management-parancsfájlok**||
| [Titkok és tanúsítványok exportálása (alkalmazás-regisztrációk)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | A titkok és tanúsítványok exportálása Azure Active Directory bérlőben az alkalmazások regisztrálásához. |
| [Titkok és tanúsítványok exportálása (vállalati alkalmazások)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | A Azure Active Directory bérlőben lévő nagyvállalati alkalmazásokhoz tartozó titkok és tanúsítványok exportálása. |
| [A lejáró titkok és tanúsítványok exportálása](scripts/powershell-export-apps-with-expriring-secrets.md) | Az alkalmazások regisztrációjának exportálása a lejáró titkokkal és tanúsítványokkal, valamint azok tulajdonosainak Azure Active Directory bérlőben. |
| [A titkok és tanúsítványok exportálása a szükséges dátum után lejár](scripts/powershell-export-apps-with-secrets-beyond-required.md) | A titkokat és tanúsítványokat tartalmazó alkalmazás-regisztrációk exportálása a Azure Active Directory bérlőn a szükséges dátumon túl lejár. Ez a nem interaktív Client_Credentials OAuth folyamatot használja. |
