---
title: Letiltott Azure Access Control Service (ACS) névterek újraaktiválása
description: Megkeresheti és engedélyezheti az Azure Access Control Service (ACS) névtereit, és megadhat egy bővítményt, hogy az a 2019. február 4-én is engedélyezve legyen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7bb572e9e22519491290e54417f5ca350d6c0b5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383717"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Útmutató: a letiltott Access Control Service névterek újraaktiválása

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

November 2017-én bejelentettük, hogy az Azure Active Directory (Azure AD) Microsoft Azure Access Control Service (ACS) a 2018. november 7-én megszűnik.

Azóta e-maileket küldtünk az ACS-előfizetések rendszergazdai e-mail-címére az ACS 12 hónap, 9 hónap, 6 hónap, 3 hónap, 1 hónap, 2 hét, 1 hét, valamint 1 nappal a 2018. november 7. előtt.

2018. október 3-án bejelentettük (e-mailben és [blogbejegyzésben](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) egy bővítményt az ügyfeleknek, akik nem tudják befejezni az áttelepítést az 2018. november 7. előtt. A bejelentés a bővítmény igénylésére vonatkozó utasításokat is tartalmaz.

## <a name="why-your-namespace-is-disabled"></a>Miért van letiltva a névtér?

Ha még nem választotta ki a bővítményt, az ACS-névterek letiltását a 2018. november 7. után fogjuk megkezdeni. A kiterjesztést a 2019. február 4. előtt kérelmezte. Ellenkező esetben nem fogja tudni engedélyezni a névtereket a PowerShell használatával.

> [!NOTE]
> A PowerShell-parancsok futtatásához és a bővítmény igényléséhez az előfizetés szolgáltatás-rendszergazdájának vagy társtulajdonosa kell lennie.

## <a name="find-and-enable-your-acs-namespaces"></a>Az ACS-névterek megkeresése és engedélyezése

Az ACS PowerShell használatával listázhatja az összes ACS-névteret, és újraaktiválhatja azokat, amelyek le vannak tiltva.

1. Az ACS PowerShell letöltése és telepítése:
    1. Lépjen a PowerShell-galériara, és töltse le az [ACS. Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2)szolgáltatást.
    1. Telepítse a modult:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. A lehetséges parancsok listájának beolvasása:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Ha segítséget szeretne kérni egy adott parancsról, futtassa a következőt:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        ahol az `[Command-Name]` ACS-parancs neve.
1. Kapcsolódjon az ACS-hez a **Kapcsolódás-AcsAccount** parancsmag használatával. 

    A parancs futtatása előtt előfordulhat, hogy módosítania kell a végrehajtási házirendet a **Set-ExecutionPolicy** futtatásával.
1. Sorolja fel az elérhető Azure-előfizetéseket a **Get-AcsSubscription** parancsmag használatával.
1. Sorolja fel az ACS-névtereket a **Get-AcsNamespace** parancsmag használatával.
1. Ellenőrizze, hogy a névterek le vannak-e tiltva a () megerősítésével `State` `Disabled` .

    [![Győződjön meg arról, hogy a névterek le vannak tiltva](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Azt is `nslookup {your-namespace}.accesscontrol.windows.net` megerősítheti, hogy a tartomány még aktív-e.

1. Engedélyezze az ACS-névtér (eke) t az **enable-AcsNamespace** parancsmag használatával.

    Miután engedélyezte a névteret (ka) t, kérhet egy bővítményt, hogy a rendszer ne tiltsa le a névtereket 2019 február 4. előtt. Ezt követően az ACS-ra irányuló összes kérelem sikertelen lesz.

## <a name="request-an-extension"></a>Bővítmény kérése

Az új bővítményekre vonatkozó kérelmeket a 2019. január 21-én kezdődően vesszük igénybe.

A rendszer megkezdi a névterek letiltását azon ügyfelek számára, akik a bővítményeket a 2019. február 4. között kérték. A névtereket továbbra is engedélyezheti a PowerShell használatával, de a névterek 48 óra elteltével újra le lesznek tiltva.

2019. március 4. után az ügyfelek többé nem tudják újra engedélyezni a névtereket a PowerShellen keresztül.

A További bővítmények többé nem lesznek automatikusan jóváhagyva. Ha további időre van szüksége az áttelepítéshez, lépjen kapcsolatba az [Azure támogatási szolgálatával](https://portal.azure.com/#create/Microsoft.Support) , és adjon meg egy részletes áttelepítési ütemtervet.

### <a name="to-request-an-extension"></a>Bővítmény igénylése

1. Jelentkezzen be a Azure Portalba, és hozzon létre egy [új támogatási kérelmet](https://portal.azure.com/#create/Microsoft.Support).
1. Töltse ki az új támogatási kérés űrlapot az alábbi példában látható módon.

    | Support request mező | Érték |
    |-----------------------|--------------------|
    | **Probléma típusa** | `Technical` |
    | **Előfizetés** | Beállítás az előfizetésre |
    | **Szolgáltatás** | `All services` |
    | **Erőforrás** | `General question/Resource not available` |
    | **Probléma típusa** | `ACS to SAS Migration` |
    | **Tárgy** | A probléma leírása |

   ![Egy új technikai támogatási kérelemre mutat példát.](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>Súgó és támogatás

- Ha a fenti útmutató követése után bármilyen problémába ütközik, forduljon az [Azure ügyfélszolgálatához](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Ha kérdése vagy visszajelzése van az ACS kivonásáról, lépjen kapcsolatba velünk a következő címen: acsfeedback@microsoft.com .

## <a name="next-steps"></a>Következő lépések

- Tekintse át az ACS-kivezetéssel kapcsolatos információkat az [Azure Access Control Serviceról: Migrálás az Azure-ból](active-directory-acs-migration.md).
