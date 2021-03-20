---
author: billmath
ms.service: active-directory
ms.subservice: governance
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: be33e492f44f6926f2ae2d133cf112245fba15f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93135338"
---
## <a name="cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Felhőbeli HR-alkalmazás Azure Active Directory a felhasználók üzembe helyezéséhez

Az informatikai munkatársak az alkalmazottak létrehozására, frissítésére és törlésére szolgáló manuális metódusokra támaszkodtak. Olyan módszereket használtak, mint például a CSV-fájlok vagy egyéni parancsfájlok feltöltése az alkalmazotti adatszinkronizáláshoz. Ezek a kiépítési folyamatok a hibákra hajlamosak, nem biztonságosak, és nehezen kezelhetők.

Az alkalmazottak, szállítók vagy függő munkavégzők identitási életciklusának kezeléséhez [Azure Active Directory (Azure ad) felhasználó-kiépítési szolgáltatás](../articles/active-directory/app-provisioning/user-provisioning.md) a felhőalapú emberi erőforrások (HR) alkalmazásaival való integrációt is biztosítja. Ilyenek például a munkanap vagy a SuccessFactors.

Az Azure AD ezt az integrációt használja a következő Felhőbeli HR-alkalmazás-munkafolyamatok engedélyezéséhez:

- **Felhasználók kiépítése a Active Directoryba:** Egy Felhőbeli HR-alkalmazásból kiválasztott felhasználói készletek kiépítése egy vagy több Active Directory tartományba.
- **Csak felhőalapú felhasználók kiépítése az Azure ad-be:** Olyan helyzetekben, ahol a Active Directory nincs használatban, közvetlenül a Cloud HR-alkalmazásból kiépítheti a felhasználókat az Azure AD-be.
- **Írjon vissza a Cloud HR alkalmazásba:** Az Azure AD-beli e-mail-címek és felhasználónevek attribútumainak megírása a Cloud HR alkalmazásba.


## <a name="enabled-hr-scenarios"></a>Engedélyezett HR-forgatókönyvek

Az Azure AD-beli felhasználói kiépítési szolgáltatás lehetővé teszi a következő HR-alapú identitás-kezelési forgatókönyvek automatizálását:

- **Új alkalmazottak felvétele:** Amikor új alkalmazott van hozzáadva a Cloud HR-alkalmazáshoz, a rendszer automatikusan létrehoz egy felhasználói fiókot a Active Directory és az Azure AD-ben, hogy az e-mail-cím és a username attribútumokat a Cloud HR alkalmazásba írja vissza.
- **Az Employee attribútum és a profil frissítései:** Ha a Cloud HR alkalmazásban egy alkalmazotti rekord, például a név, a cím vagy a felettes frissül, a felhasználói fiókja automatikusan frissül Active Directory és az Azure AD-ben.
- **Alkalmazotti megszakítások:** Ha egy alkalmazott leáll a Cloud HR alkalmazásban, a felhasználói fiókja automatikusan le lesz tiltva Active Directory és az Azure AD-ben.
- **Alkalmazottak újrabérlése:** Ha egy alkalmazottat a Cloud HR alkalmazásban újra felvesznek, a régi fiókja automatikusan újraaktiválható vagy újraépíthető a Active Directory és az Azure AD szolgáltatásba.

## <a name="who-is-this-integration-best-suited-for"></a>Ki a legmegfelelőbb integrációs csomag?

A Felhőbeli HR-alkalmazás integrációja az Azure AD-vel – a felhasználók üzembe helyezése ideális olyan szervezetek számára, amelyek:

- Szeretne egy előre elkészített felhőalapú megoldást használni a Felhőbeli HR-felhasználók üzembe helyezéséhez.
- A Felhőbeli HR-alkalmazásból Active Directory vagy az Azure AD-be való közvetlen felhasználói üzembe helyezés szükséges.
- A Felhőbeli HR-alkalmazásból beszerzett adatok használatával kell kiépíteni a felhasználókat.
- A felhasználók egy vagy több Active Directory-erdő, tartomány és szervezeti egység számára való szinkronizálásának megkövetelése csak a Cloud HR alkalmazásban észlelt változási információk alapján.
- Az Office 365 e-mail-cím használata.


### <a name="key-benefits"></a>Főbb előnyök

A HR-alapú IT-kiépítés ezen funkciója a következő jelentős üzleti előnyöket kínálja:

- **Növelje a termelékenységet:** Mostantól automatizálhatja a felhasználói fiókok és az Office 365-licencek hozzárendelését, és hozzáférést biztosíthat a kulcsfontosságú csoportokhoz. A hozzárendelések automatizálása lehetővé teszi, hogy az új Hirek azonnal hozzáférjenek a feladathoz, és növelik a termelékenységet.
- **Kockázat kezelése:** Növelheti a biztonságot úgy, hogy az alkalmazottak állapota vagy csoporttagságok alapján automatizálja a módosításokat a Felhőbeli HR-alkalmazásból származó adatokkal. A változások automatizálása biztosítja, hogy a felhasználói identitások és a Key apps hozzáférése automatikusan megtörténjen, amikor a felhasználók áttérnek vagy elhagyják a szervezetet.
- **Címek megfelelősége és szabályozása:** Az Azure AD támogatja a natív naplókat a forrás-és a célként megadott alkalmazások által végrehajtott felhasználói kiépítési kérelmek esetében. A naplózás segítségével nyomon követheti, hogy ki férhet hozzá az alkalmazásokhoz egyetlen képernyőről.
- **Kezelés díja:** Az automatikus kiépítés csökkenti a költségeket, és elkerüli a manuális kiépítés során felmerülő eredménytelenség és emberi hibák elkerülését. Ez csökkenti a régi és elavult platformok használatával az idő múlásával létrehozott, egyéni fejlesztésű felhasználói megoldások igényét.
