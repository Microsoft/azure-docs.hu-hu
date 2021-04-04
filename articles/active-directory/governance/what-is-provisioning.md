---
title: Mi az Azure Active Directory való kiépítés? | Microsoft Docs
description: Az identitás kiépítési és az ILM-forgatókönyvek áttekintését ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 640367d1b833f61e8a83fe9ce6b14d6d799cf9b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172452"
---
# <a name="what-is-provisioning"></a>Mi az az üzembe helyezés?

A kiépítés és a megszüntetés a különböző rendszereken a digitális identitások egységességét biztosító folyamatok.  Ezeket a folyamatokat általában a [személyazonossági életciklus felügyeletének](what-is-identity-lifecycle-management.md)részeként használják.

A **kiépítés** során a rendszer bizonyos feltételeken alapuló identitást hoz létre a célszámítógépen.  A **kiépítés** során a rendszer eltávolítja az identitást a céleszköz, ha a feltételek már nem teljesülnek. A **szinkronizálási** folyamat a kiépített objektum naprakészen tartása, így a Forrásobjektum és a célobjektum hasonló.

Ha például egy új alkalmazott csatlakozik a szervezethez, az alkalmazott bekerül a HR rendszerbe.  Ezen a ponton a HR- **ről** Azure Active Directoryra (Azure ad) **való** kiépítés egy megfelelő felhasználói fiókot hozhat létre az Azure ad-ben. Az Azure AD-t lekérdező alkalmazások megtekinthetik az új alkalmazott fiókját.  Ha vannak olyan alkalmazások, amelyek nem használják az Azure AD- **t** , akkor **Az Azure ad-ből az** alkalmazások adatbázisaiba való kiépítés biztosítja, hogy a felhasználó hozzáférhessen az összes olyan alkalmazáshoz, amelyhez a felhasználónak hozzáférést kell biztosítania.  Ez a folyamat lehetővé teszi a felhasználó számára, hogy megkezdje a munkát, és hozzáfér az első napon szükséges alkalmazásokhoz és rendszerekhez.  Hasonlóképpen, ha a tulajdonságok, például a részlegük vagy a foglalkoztatás állapota, a HR-rendszeren való változás, a frissítések szinkronizálása a HR rendszerből az Azure AD-be, továbbá más alkalmazásokra és megcélzott adatbázisokra is vonatkozik, biztosítja az egységességet.

Az Azure AD jelenleg három területet biztosít az automatizált üzembe helyezéshez.  Ezek a következők:  

- Kiépítés külső, nem címtárban lévő mérvadó rekordból az Azure AD-be, **[HR-alapú kiépítés](#hr-driven-provisioning) útján**  
- Kiépítés az Azure AD-ből alkalmazásokba az **[alkalmazás kiépítés](#app-provisioning) útján**  
- Az Azure AD és a Active Directory tartományi szolgáltatások közötti kiépítés az **[Inter-Directory kiépítés](#inter-directory-provisioning) útján** 

![identitás-életciklus kezelése](media/what-is-provisioning/provisioning.png)

## <a name="hr-driven-provisioning"></a>HR-vezérelt kiépítés

![HR-kiépítés](media/what-is-provisioning/cloud-2a.png)

A HR-ből az Azure AD-be való kiépítés magában foglalja az objektumok létrehozását, általában az egyes alkalmazottakat jelképező felhasználói identitásokat, de bizonyos esetekben a részlegeket vagy más struktúrákat képviselő objektumokat a HR-rendszer információi alapján.  

A leggyakoribb forgatókönyv a következő: amikor egy új alkalmazott csatlakozik a vállalathoz, azok bekerülnek a HR-rendszerbe.  Ha ez bekövetkezik, a rendszer automatikusan új felhasználóként helyezi üzembe az Azure AD-ben, anélkül, hogy minden új bérlet esetében rendszergazdai részvételt kellene befizetnie.  Általánosságban elmondható, hogy a HR-ből való kiépítés a következő forgatókönyvekre vonatkozik.

- **Új alkalmazottak felvétele** – ha új alkalmazottat vesznek fel egy HR-rendszerbe, automatikusan létrejön egy felhasználói fiók Active Directoryban, az Azure ad-ben és opcionálisan az Azure ad által támogatott egyéb alkalmazások könyvtáraiban, és az e-mail-cím visszaírása a HR rendszerbe.
- **Alkalmazotti attribútumok és a profil frissítései** – ha egy alkalmazotti rekord frissül az adott HR-rendszeren (például a nevük, a cím vagy a felettes), a felhasználói fiókja automatikusan frissül Active Directory, az Azure ad-ben és opcionálisan az Azure ad által támogatott egyéb alkalmazásokban is.
- **Alkalmazotti megszakítások** – ha egy alkalmazott leáll a HR-ben, a felhasználói fiókja automatikusan le lesz tiltva a bejelentkezésben vagy a Active Directory, az Azure ad-ben és más alkalmazásokban való eltávolításakor.
- **Alkalmazottak** újratelepítése – ha egy alkalmazottat a Felhőbeli HR-ben végeznek újra, a régi fiók automatikusan újraaktiválható vagy újraépíthető (a beállítástól függően).

Az Azure AD-vel három üzembe helyezési lehetőség van a HR-alapú kiépítés során:

1. Olyan szervezetek esetében, amelyek egyetlen előfizetéssel rendelkeznek munkanapokra vagy SuccessFactors, és nem használják a Active Directory
1. A munkanapokra vagy SuccessFactors vonatkozó egyetlen előfizetéssel rendelkező szervezetek esetében Active Directory és az Azure AD-vel egyaránt
1. Több HR-rendszert használó szervezetek vagy egy helyszíni HR-rendszer, például az SAP, az Oracle inporting vagy a PeopleSoft

További információ: [Mi az a HR-alapú kiépítés?](what-is-hr-driven-provisioning.md)

## <a name="app-provisioning"></a>Alkalmazás kiépítés

![alkalmazás kiépítés](media/what-is-provisioning/cloud-3b.png)

Az Azure AD-ben az **[alkalmazás üzembe](../app-provisioning/user-provisioning.md)** helyezésének kifejezése arra utal, hogy a felhasználói identitások példányait automatikusan hozza létre azon alkalmazásokban, amelyekhez a felhasználóknak hozzáférésre van szükségük, az Azure ad-től és a Active Directorytól eltérő alkalmazásokhoz. A felhasználói identitások létrehozása mellett az alkalmazások kiosztása magában foglalja az alkalmazásokból származó felhasználói identitások karbantartását és eltávolítását, mivel a felhasználó állapota vagy szerepkörei változnak. Gyakori forgatókönyvek közé tartozik például az Azure AD-felhasználók üzembe helyezése az olyan alkalmazásokban, mint a [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), a [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), a [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md), mivel mindegyik alkalmazás saját felhasználói tárháza különbözik az Azure ad-től.

További információ: [Mi az az App kiépítés?](what-is-app-provisioning.md)

## <a name="inter-directory-provisioning"></a>Könyvtárak közötti kiépítés

![könyvtárak közötti kiépítés](media/what-is-provisioning/cloud-4a.png)

Számos szervezet a Active Directory és az Azure AD szolgáltatásra támaszkodik, és lehet, hogy az alkalmazások Active Directoryhoz, például a helyszíni fájlkiszolgálókhöz vannak csatlakoztatva.

Mivel a legtöbb szervezet már korábban is üzembe helyezte a HR-alapú kiépítést, előfordulhat, hogy már rendelkezik felhasználói identitásokkal a Active Directory összes alkalmazottja számára.   A címtáron belüli kiépítés leggyakoribb forgatókönyve, ha egy felhasználó már Active Directory van kiépítve az Azure AD-be.  Ezt a kiépítést általában Azure AD Connect Sync vagy Azure AD Connect Cloud kiépítés hajtja végre. 

Emellett a szervezetek az Azure AD-ből is dönthetnek a helyszíni rendszerek kiépítéséhez.  Előfordulhat például, hogy egy szervezet elindította a vendégeket az Azure AD-címtárba, de ezeknek a vendégeknek az alkalmazás-proxyn keresztül hozzá kell férniük a helyszíni Windows integrált hitelesítési (WIA) alapú webalkalmazásokhoz.  Ehhez szükség van a helyszíni AD-fiókok üzembe helyezésére az Azure AD-ben lévő felhasználók számára.

További információ: [Mi az a Inter-Directory kiépítés?](what-is-inter-directory-provisioning.md)

 
## <a name="next-steps"></a>Következő lépések 
- [Mi az az identitáséletciklus-felügyelet?](what-is-identity-lifecycle-management.md)
- [Mi az a HR-alapú kiépítés?](what-is-hr-driven-provisioning.md)
- [Mi az az alkalmazás-üzembehelyezés?](what-is-app-provisioning.md)
- [Mi az a címtárak közötti üzembe helyezés?](what-is-inter-directory-provisioning.md)