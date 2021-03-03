---
title: Az eszköz frissítésének ismertetése IoT Hub hitelesítéshez és engedélyezéshez | Microsoft Docs
description: Ismerje meg, hogyan használja az IoT Hub az Azure RBAC a felhasználók és a szolgáltatási API-k hitelesítésének és engedélyezésének biztosítására.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 07310a5b6f275d4a35a3649c22aeea68045dde8b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662276"
---
# <a name="azure-role-based-access-control-rbac-and-device-update"></a>Azure szerepköralapú hozzáférés-vezérlés (RBAC) és eszköz frissítése

Az eszköz frissítése az Azure RBAC-t használja a felhasználók és a szolgáltatások API-k hitelesítésének és engedélyezésének biztosítására.

## <a name="configure-access-control-roles"></a>Hozzáférés-vezérlési szerepkörök konfigurálása

Ahhoz, hogy más felhasználók és alkalmazások hozzáférhessenek az eszköz frissítéséhez, a felhasználóknak vagy alkalmazásoknak hozzáférést kell adni ehhez az erőforráshoz. Az eszköz frissítése által támogatott szerepkörök

|   Szerepkör neve   | Leírás  |
| :--------- | :---- |
|  Eszköz frissítésének rendszergazdája | Hozzáfér az összes eszköz frissítési erőforrásához  |
|  Eszköz frissítési olvasója| Megtekintheti az összes frissítést és központi telepítést |
|  Eszköz frissítése – tartalom rendszergazdája | Megtekintheti, importálhatja és törölheti a frissítéseket  |
|  Eszköz frissítési tartalom olvasója | Megtekintheti a frissítéseket  |
|  Eszköz frissítése központi telepítések rendszergazdája | Felügyelheti az eszközök frissítéseinek központi telepítését.|
|  Eszköz frissítése központi telepítések olvasója| Megtekintheti az eszközök frissítéseinek központi telepítését. |

Szerepkörök kombinációja használható a megfelelő szintű hozzáférés biztosításához. Egy fejlesztő például importálhatja és kezelheti a frissítéseket az eszköz frissítése a tartalom-rendszergazdai szerepkör használatával, de a frissítés előrehaladásának megtekintéséhez szükség van egy eszköz-frissítési központi telepítési szerepkörre. Ezzel szemben az eszköz frissítési olvasó szerepkörrel rendelkező megoldás-kezelője megtekintheti az összes frissítést, de az eszköz frissítésének központi telepítése rendszergazdai szerepkört kell használnia egy adott frissítés telepítéséhez az eszközökön.


## <a name="authenticate-to-device-update-rest-apis-for-publishing-and-management"></a>Hitelesítés az eszköz frissítési REST API-khoz közzététel és felügyelet céljából

Az eszköz frissítése az Azure AD-t is használja a hitelesítéshez a tartalom a Service API-kon keresztül történő közzétételéhez és kezeléséhez. Első lépésként létre kell hoznia és konfigurálnia kell egy ügyfélalkalmazás alkalmazást.

### <a name="create-client-azure-ad-app"></a>Ügyfél létrehozása Azure AD alkalmazás

Egy alkalmazás vagy szolgáltatás Azure AD-val való integrálásához [először regisztráljon](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app) egy alkalmazást az Azure ad-ben. Az ügyfélalkalmazás beállítása a használt engedélyezési folyamattól függően változhat.  Az alábbi konfiguráció útmutatást nyújt az eszköz frissítése REST API-k használata esetén.

* Ügyfél-hitelesítés beállítása: "a natív vagy webes ügyfélhez tartozó URI-k átirányítása".
* API-engedélyek beállítása – IoT Hub elérhető eszköz frissítése:
  * Delegált engedélyek: "user_impersonation"
  * Nem **kötelező**, rendszergazdai jóváhagyás megadása

[Következő lépések: eszköz frissítési erőforrásainak létrehozása és hozzáférés-vezérlési szerepkörök konfigurálása](./create-device-update-account.md)
