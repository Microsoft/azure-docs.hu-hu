---
title: PowerShell-minta – A titkos adatokat és tanúsítványokat tartalmazó alkalmazások exportálása a bérlőben a szükséges Azure Active Directory követően.
description: PowerShell-példa, amely exportálja az összes olyan alkalmazást, amely titkos okkal és tanúsítványokkal rendelkezik, amelyek lejárnak a bérlőben a megadott Azure Active Directory követően.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 692ab2cfd480fd15760c13c63922fe76d23058ea
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375389"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>A titkos adatokat és tanúsítványokat a szükséges dátumon túlra lejáró alkalmazások exportálása

Ez a PowerShell-példaszkvóta nem interaktív módon exportálja az alkalmazásregisztrációs titkos okat és tanúsítványokat, amelyek a megadott időtartamon túl lejárnak a megadott alkalmazások számára a címtárból egy CSV-fájlban.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript nem interaktív módon működik. Az alkalmazást használó rendszergazdának módosítania kell a "#PARAMETERS TO CHANGE" szakaszban található értékeket a saját alkalmazásazonosítójával, az alkalmazás titkos kódjával, a bérlő nevével, az alkalmazás hitelesítő adatainak lejárati idejével és a CSV exportálásának elérési útjával.
Ez a szkript az [Client_Credential Oauth-folyamatot](../../develop/v2-oauth2-client-creds-grant-flow.md) használja. A "RefreshToken" függvény a rendszergazda által módosított paraméterek értékei alapján buildeli a hozzáférési jogkivonatot.

Az "Add-Member" parancs felelős a CSV-fájlban lévő oszlopok létrehozásáért.

| Parancs | Jegyzetek |
|---|---|
| [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1&preserve-view=true) | HTTP- és HTTPS-kéréseket küld egy weblapnak vagy webszolgáltatásnak. Ez elemezi a választ, és hivatkozások, képek és más jelentős HTML-elemek gyűjteményét adja vissza. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure AD PowerShell-modulról: [Az Azure AD PowerShell-modul áttekintése.](/powershell/azure/active-directory/overview)

További PowerShell-példák az alkalmazáskezeléshez: [Azure AD PowerShell-példák az alkalmazáskezeléshez.](../app-management-powershell-samples.md)
