---
title: Váratlan jóváhagyás kérése az alkalmazás-| Microsoft Docs
description: Hibaelhárítás, ha egy felhasználó olyan alkalmazás hozzájárulási kérését látja, amely nem várt módon van integrálva az Azure AD-be
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37e4384b5a1b400f11b7b7d6ab15beec4d2f8de9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378058"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Váratlan jóváhagyás kérése az alkalmazásba való bejelentkezéskor

Számos, a Azure Active Directory integrálható alkalmazásnak engedélyekre van szüksége a különböző erőforrásokhoz a futtatáshoz. Ha ezek az erőforrások integrálva vannak a Azure Active Directory, az ezekhez való hozzáférésre vonatkozó engedélyeket az Azure AD hozzájárulási keretrendszerével kell igényelni. 

Ez azt jelenti, hogy az alkalmazás első használatakor egy hozzájárulási kérés jelenik meg, ami gyakran egy egyszeres művelet. 

> [!VIDEO https://www.youtube.com/embed/a1AjdvNDda4]

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Forgatókönyvek, amelyekben a felhasználók a hozzájárulási kéréseket látják

A különböző forgatókönyvekben további kérések várhatók:

* Az alkalmazáshoz szükséges engedélyek készlete megváltozott.

* Az a felhasználó, aki eredetileg hozzájárult az alkalmazáshoz, nem rendszergazda volt, és most egy másik (nem rendszergazdai) felhasználó először használja az alkalmazást.

* Az a felhasználó, aki eredetileg hozzájárult az alkalmazáshoz, rendszergazda volt, de nem a teljes szervezet nevében járult hozzá.

* Az alkalmazás [növekményes és dinamikus](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) hozzájárulással kér további engedélyeket az első hozzájárulás után. Ezt gyakran használják, ha egy alkalmazás további választható funkciói az alapkonfigurációhoz szükségesnél nagyobb engedélyeket igényelnek.

* A jóváhagyást a kezdeti jóváhagyás után visszavonták.

* A fejlesztő úgy konfigurálta az alkalmazást, hogy minden alkalommal jóváhagyást igényeljon, amikor használja azt (megjegyzés: ez nem az ajánlott eljárás).

## <a name="next-steps"></a>Következő lépések

-   [Alkalmazások, engedélyek és hozzájárulás Azure Active Directory (1.0-s végpont)](../develop/quickstart-register-app.md)

-   [Hatókörök, engedélyek és hozzájárulás a Azure Active Directory (2.0-s végpont)](../develop/v2-permissions-and-consent.md)
