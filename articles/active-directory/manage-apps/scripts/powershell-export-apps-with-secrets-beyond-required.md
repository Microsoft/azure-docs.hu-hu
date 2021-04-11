---
title: PowerShell-minta – a titkokat és tanúsítványokat tartalmazó alkalmazások exportálása a Azure Active Directory bérlőn megkövetelt időpontnál tovább lejár.
description: PowerShell-példa, amely a titkokat és tanúsítványokat tartalmazó összes alkalmazást exportálja a Azure Active Directory bérlő megadott alkalmazásaihoz szükséges dátum után.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: daeea48758a9f08e7eedbfcaddcde3815f5c1e16
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729099"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>A titkokat és tanúsítványokat tartalmazó alkalmazások exportálása a szükséges dátum után lejár

Ez a PowerShell-parancsfájl például exportálja az összes alkalmazás-regisztráció titkát és tanúsítványát, amely egy szükséges időszakon túlian lejár a megadott alkalmazásoknak a címtárból egy CSV-fájlban, nem interaktív módon.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript nem interaktív módon működik. A-t használó rendszergazdának módosítania kell a "#PARAMETERS a MÓDOSÍTÁSra" szakaszt a saját alkalmazás-azonosítójával, az alkalmazás titkos kódjával, a bérlő nevével, az alkalmazások hitelesítő adatainak lejárati idejével, valamint a CSV exportálásának elérési útjával.
Ez a szkript a [Client_Credential OAuth flow](../../develop/v2-oauth2-client-creds-grant-flow.md) -t használja, és a "RefreshToken" függvény létrehozza a hozzáférési jogkivonatot a rendszergazda által módosított paraméterek értékei alapján.

A "tag hozzáadása" parancs feladata az oszlopok létrehozása a CSV-fájlban.

| Parancs | Jegyzetek |
|---|---|
| [Meghívás – webkérés](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1&preserve-view=true) | HTTP-és HTTPS-kéréseket küld egy weblapra vagy webszolgáltatásnak. Elemzi a választ, és visszaadja a hivatkozások, képek és egyéb jelentős HTML-elemek gyűjteményeit. |

## <a name="next-steps"></a>Következő lépések

Az Azure AD PowerShell-modulról az [Azure ad PowerShell-modul áttekintése](/powershell/azure/active-directory/overview)című témakörben olvashat bővebben.

További PowerShell-példák az alkalmazások kezeléséhez: [Azure ad PowerShell-példák az alkalmazások kezeléséhez](../app-management-powershell-samples.md).
