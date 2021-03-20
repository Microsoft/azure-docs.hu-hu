---
title: Feltételes hozzáférési szolgáltatás függőségei – Azure Active Directory
description: Megtudhatja, hogyan használhatók a feltételek a Azure Active Directory feltételes hozzáférésben a szabályzat kiváltásához.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6592add5e33ba240c0f1d9fdbd23d82adfe5229
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91258608"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Mi a szolgáltatás függőségei Azure Active Directory feltételes hozzáférésben? 

A feltételes hozzáférési szabályzatok segítségével megadhatja a webhelyekhez és a szolgáltatásokhoz való hozzáférési követelményeket. A hozzáférési követelmények például többek között a többtényezős hitelesítés (MFA) vagy a [felügyelt eszközök](require-managed-devices.md)megkövetelését is tartalmazhatják. 

Ha közvetlenül fér hozzá egy webhelyhez vagy szolgáltatáshoz, a kapcsolódó szabályzatok hatása általában könnyen felmérhető. Ha például olyan szabályzattal rendelkezik, amely a SharePoint Online-hoz a többtényezős hitelesítés (MFA) használatát igényli, a rendszer minden egyes bejelentkezéskor MFA-t alkalmaz a SharePoint webes portálra. Azonban nem mindig áll készen a szabályzat hatásának felmérésére, mert a Felhőbeli alkalmazások függőségekkel rendelkeznek más felhőalapú alkalmazásokkal. A Microsoft Teams például hozzáférést biztosíthat az erőforrásokhoz a SharePoint Online-ban. Így ha a Microsoft csapatait a jelenlegi forgatókönyvben éri el, a SharePoint MFA-szabályzata is érvényes. 

> [!TIP]
> Az [office 365](concept-conditional-access-cloud-apps.md#office-365) alkalmazással az Office-alkalmazások minden Office-alkalmazást megcéloznak, hogy elkerülje a szolgáltatási függőségek problémáit az Office-veremben.

## <a name="policy-enforcement"></a>Szabályzatbetartatás 

Ha a szolgáltatás függősége konfigurálva van, a szabályzatot a rendszer a korai vagy késői kötésű kényszerítéssel is alkalmazhatja. 

- A **korai kötésű szabályzatok kényszerítése** azt jelenti, hogy a felhasználónak meg kell felelnie a függő szolgáltatási házirendnek a hívó alkalmazás elérése előtt. A felhasználóknak például meg kell felelniük a SharePoint-házirendnek, mielőtt bejelentkeznek az MS Teams szolgáltatásba. 
- A **késői kötésű szabályzat kényszerítése** akkor következik be, amikor a felhasználó bejelentkezik a hívó alkalmazásba. Az alkalmazásra vonatkozó kérelmek meghívásakor a kényszerítés az alárendelt szolgáltatáshoz tartozó jogkivonat. Ilyenek például azok az MS-csapatok, akik a Plannerhez és a Office.com hozzáférnek a SharePointhoz. 

Az alábbi ábra az MS Teams szolgáltatás függőségeit mutatja be. A folytonos nyilak jelzik a korai kötésű kényszerítést a Planner szaggatott nyila jelzi a késői kötések kényszerítését. 

![MS Teams szolgáltatás függőségei](./media/service-dependencies/01.png)

Ajánlott eljárásként a kapcsolódó alkalmazásokban és szolgáltatásokban közös házirendeket kell beállítania, amikor csak lehetséges. A konzisztens biztonsági testhelyzet a legjobb felhasználói élményt nyújtja. Például az Exchange Online, a SharePoint Online, a Microsoft Teams és a Skype for Business közös házirendjének beállítása jelentősen csökkenti az alárendelt szolgáltatásokra alkalmazott különböző házirendektől esetlegesen felmerülő váratlan kéréseket. 

Ezt az Office-veremben lévő alkalmazásokkal nagyszerű módszerként használhatja az [office 365 alkalmazás](concept-conditional-access-cloud-apps.md#office-365) használata az egyes alkalmazások célzása helyett.

Az alábbi táblázat felsorolja azokat a szolgáltatási függőségeket, amelyeknek meg kell felelniük az ügyfélalkalmazások számára  

| Ügyfélalkalmazások         | Alsóbb rétegbeli szolgáltatás                          | Kényszerítési |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure-felügyelet (portál és API) | Korai kötésű |
| Microsoft tanterem | Exchange                                    | Korai kötésű |
|                     | SharePoint                                  | Korai kötésű |
| Microsoft Teams     | Exchange                                    | Korai kötésű |
|                     | MS Planner                                  | Késői kötés  |
|                     | Microsoft Stream                            | Késői kötés  |
|                     | SharePoint                                  | Korai kötésű |
|                     | Skype Vállalati online verzió                   | Korai kötésű |
| Office-portál       | Exchange                                    | Késői kötés  |
|                     | SharePoint                                  | Késői kötés  |
| Outlook-csoportok      | Exchange                                    | Korai kötésű |
|                     | SharePoint                                  | Korai kötésű |
| PowerApps           | Microsoft Azure-felügyelet (portál és API) | Korai kötésű |
|                     | Microsoft Azure Active Directory              | Korai kötésű |
| Project             | Dynamics CRM                                | Korai kötésű |
| Skype Vállalati verzió  | Exchange                                    | Korai kötésű |
| Visual Studio       | Microsoft Azure-felügyelet (portál és API) | Korai kötésű |
| Microsoft Forms     | Exchange                                    | Korai kötésű |
|                     | SharePoint                                  | Korai kötésű |
| Microsoft To-Do     | Exchange                                    | Korai kötésű |

## <a name="next-steps"></a>Következő lépések

Ha meg szeretné tudni, hogyan valósítható meg a feltételes hozzáférés a környezetben, tekintse meg [a feltételes hozzáférés Megtervezése Azure Active Directoryban](plan-conditional-access.md)című témakört.
