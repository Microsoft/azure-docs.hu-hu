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
ms.openlocfilehash: e63931f62398e1344d001bedb27cc8d0d776bef1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102185397"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Azure Active Directory PowerShell-példák az alkalmazások kezeléséhez

Az alábbi táblázat az Azure AD-alkalmazások kezeléséhez használható PowerShell-parancsfájlokra mutató hivatkozásokat tartalmaz. A mintákhoz a következők szükségesek:
- A [AzureAD v2 PowerShell a Graph modulhoz](/powershell/azure/active-directory/install-adv2) vagy
- A [Graph modul előzetes verziójának AzureAD v2 PowerShell-verziója](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), hacsak másként nincs jelezve.

A példákban használt parancsmagokkal kapcsolatos további információkért lásd: [alkalmazások](/powershell/module/azuread/?view=azureadps-2.0#applications&preserve-view=true).

| Hivatkozás | Leírás |
|---|---|
|**Application Management-parancsfájlok**||
| [Az összes alkalmazás regisztrációjának, titkos kulcsának és tanúsítványának exportálása](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | A címtárban lévő megadott alkalmazások összes alkalmazás-regisztrációjának, titkos kulcsának és tanúsítványának exportálása. |
