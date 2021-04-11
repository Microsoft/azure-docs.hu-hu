---
title: 'Útmutató: nemrég törölt alkalmazások visszaállítása vagy eltávolítása a Microsoft Identity platformmal | Azure'
titleSuffix: Microsoft identity platform
description: Ebben a útmutatóban megismerheti, hogyan lehet visszaállítani vagy véglegesen törölni a Microsoft Identity platformon regisztrált, nemrég törölt alkalmazásokat.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 3/22/2021
ms.author: arcrowe
ms.custom: aaddev
ms.openlocfilehash: dbe3e16bf56c5480fbe4aff8dad78bbbb5591f67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080212"
---
# <a name="restore-or-remove-a-recently-deleted-application-with-the-microsoft-identity-platform"></a>Nemrég törölt alkalmazás visszaállítása vagy eltávolítása a Microsoft Identity platformmal
Az alkalmazás regisztrációjának törlése után az alkalmazás 30 napig felfüggesztett állapotban marad. A 30 napos időszak alatt az alkalmazás regisztrálása visszaállítható az összes tulajdonságával együtt. A 30 napos időszak elteltével az alkalmazás regisztrációi nem állíthatók vissza, és az állandó törlési folyamat automatikusan elindul.  Ez a funkció csak a címtárhoz társított alkalmazásokra vonatkozik.  Nem érhető el olyan alkalmazásokhoz, amelyek személyes Microsoft-fiók nem állíthatók vissza.

Megtekintheti a törölt alkalmazásokat, visszaállíthat egy törölt alkalmazást, vagy véglegesen törölhet egy alkalmazást az Azure Portal Azure Active Directory (Azure AD) területén található Alkalmazásregisztrációk-élmény használatával.

Vegye figyelembe, hogy sem Ön, sem a Microsoft ügyfélszolgálata nem állíthatja vissza véglegesen törölt alkalmazást, vagy az alkalmazás több mint 30 napja törölve lett.

> [!IMPORTANT]
> A törölt alkalmazások portál felhasználói felületének funkciója [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="required-permissions"></a>Szükséges engedélyek
Az alkalmazások végleges törléséhez a következő szerepkörök egyikének kell tartoznia.

- Globális rendszergazda
- Alkalmazás-rendszergazda
- Cloud Application Administrator
- Hibrid identitás-rendszergazda
- Alkalmazás tulajdonosa

Az alkalmazások visszaállításához a következő szerepkörök egyikét kell megadnia.

- Globális rendszergazda
- Alkalmazás tulajdonosa

### <a name="view-your-deleted-applications"></a>Törölt alkalmazások megtekintése
Az összes alkalmazást megtekintheti egy nem kötelezően törölt állapotban.  Csak a 30 napnál régebben törölt alkalmazások állíthatók vissza.

#### <a name="to-view-your-restorable-applications"></a>A helyreállítható alkalmazások megtekintése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Keresse meg és válassza ki a **Azure Active Directory**, válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza a **törölt alkalmazások (előzetes verzió)** lapot.

Tekintse át az alkalmazások listáját. Csak az elmúlt 30 napban törölt alkalmazások lesznek elérhetők a visszaállításhoz. Ha a Alkalmazásregisztrációk keresési előnézet funkciót használja, a "törölt dátum" oszlop alapján szűrheti, hogy csak ezeket az alkalmazásokat jelenítse meg.

## <a name="restore-a-recently-deleted-application"></a>Nemrég törölt alkalmazás visszaállítása

Ha egy alkalmazás regisztrációját törlik a szervezetből, az alkalmazás felfüggesztett állapotba kerül, és a konfigurációit megőrzi a rendszer. Az alkalmazások regisztrálásának visszaállításakor a rendszer a konfigurációkat is visszaállítja.  Ha azonban a **vállalati alkalmazásokban** az alkalmazás otthoni bérlője számára bármilyen szervezetre vonatkozó beállítás található, akkor azok nem lesznek visszaállítva.  

Ennek az az oka, hogy a szervezeti beállítások tárolása egy különálló objektumon történik, az egyszerű szolgáltatásnév néven.  Az egyszerű szolgáltatásban tárolt beállítások magukban foglalják az engedélyek beleegyezését, valamint a felhasználók és csoportok hozzárendelését egy adott szervezet számára; ezeket a konfigurációkat a rendszer nem állítja vissza az alkalmazás visszaállításakor. További információ: [alkalmazás-és egyszerű szolgáltatások objektumai](app-objects-and-service-principals.md). 


### <a name="to-restore-an-application"></a>Alkalmazás visszaállítása
1. A **törölt alkalmazások (előzetes verzió)** lapon keresse meg és válassza ki a 30 napnál régebben törölt alkalmazások egyikét.
2. Válassza az **alkalmazás regisztrációjának visszaállítása** lehetőséget.

## <a name="permanently-delete-an-application"></a>Alkalmazás végleges törlése
Manuálisan véglegesen törölheti az alkalmazást a szervezetből. Egy véglegesen törölt alkalmazást nem lehet visszaállítani Ön, egy másik rendszergazda vagy a Microsoft ügyfélszolgálata.

### <a name="to-permanently-delete-an-application"></a>Alkalmazás végleges törlése

1. A **törölt alkalmazások (előzetes verzió)** lapon keresse meg és válassza ki az elérhető alkalmazások egyikét.
2. Válassza a **véglegesen törlés** lehetőséget.
3. Olvassa el a figyelmeztetés szövegét, és válassza az **Igen** lehetőséget.

## <a name="next-steps"></a>Következő lépések
Miután visszaállította vagy véglegesen törölte az alkalmazást, a következőket teheti:

- [Alkalmazás hozzáadása](quickstart-register-app.md).
- További információ az [alkalmazások és szolgáltatások egyszerű objektumairól](app-objects-and-service-principals.md) a Microsoft Identity platformon.
