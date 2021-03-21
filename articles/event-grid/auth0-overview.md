---
title: Auth0 partneri témakörök Azure Event Grid
description: Események küldése a Auth0-ből az Azure-szolgáltatásokba Azure Event Grid használatával.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 340403111c094540ba7e66faf71ebf3cfe24fe7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93101289"
---
# <a name="auth0-partner-topics"></a>Auth0-partneri témakörök
![Auth0 embléma](./media/auth0-overview/auth0-logo.png)

A Auth0, az alkalmazás-építők számára készült Identity platform a fejlesztőknek és a vállalatoknak az alkalmazásai védelméhez szükséges építőelemeket biztosítja.

A Auth0-partneri témakör lehetővé teszi, hogy a Auth0's rendszer által kibocsátott eseményeket több feladat elvégzéséhez használja. A hitelesítés vagy a biztonsági és infrastrukturális feladatok automatizálása után a felhasználóknak értelmes módon kell foglalkoznia a felhasználókkal.

Az integráció lehetővé teszi, hogy magas szintű megbízhatósággal továbbítsa a Auth0-naplózási eseményeket az Azure-ba. Ott felhasználhatja az eseményeket kedvenc Azure-erőforrásaival. Ez az integráció lehetővé teszi az események megválaszolása, az elemzések, a biztonsági problémák figyelése és a más, hatékony adatfolyamatokkal való interakciót.

Az Auth0-t és az Azure-t használó szervezetek esetében ez az integráció lehetővé teszi, hogy zökkenőmentesen integrálja az adatait a teljes verembe. 
 
## <a name="available-event-types"></a>Elérhető események típusai
Az elérhető Auth0-események teljes listája és azok leírása elérhető [ezen a webhelyen](https://auth0.com/docs/logs/references/log-event-type-codes).

## <a name="use-cases"></a>Használati esetek

### <a name="engage-with-your-users"></a>Felhasználói részvétel
Az erős felhasználói élmény biztosítása kritikus fontosságú a forgalom csökkentése és a felhasználók megtartása érdekében. A Auth0-események Azure Functions és Azure Logic Apps használatával történő kihasználása több testre szabott alkalmazási élményt biztosít. 

### <a name="understand-user-behavior"></a>A felhasználó viselkedésének megértése
Ismerje meg, hogy mikor férhetnek hozzá a felhasználók a termékhez, hol vannak bejelentkezve, és milyen eszközöket használnak. A fenti jelek nyomon követésével megismerheti a leginkább fontos terméktartományoknak. Ezek a jelek segítenek a következő meghatározásában:
- A támogatni kívánt böngészők és eszközök. 
- Milyen nyelveket kell honosítani az alkalmazásához. 
- A maximális forgalmának időpontjában. 

### <a name="manage-user-data"></a>Felhasználói adatok kezelése
A felhasználói műveletek megőrzése és naplózása létfontosságú a biztonság fenntartásához és az iparági szabályozások követéséhez. A felhasználói adatok szerkesztésének, eltávolításának vagy exportálásának lehetősége egyre fontosabbá válik az adatvédelmi törvények, például az Európai Unió Általános adatvédelmi rendelet (GDPR) követése érdekében.

### <a name="secure-your-application"></a>Az alkalmazás biztonságossá tétele
A biztonsági figyelés és az incidensek válaszának összevonása fontos az elosztott rendszerek védelme során. Ezért fontos, hogy az összes adathalmazt egy helyen tárolja, és figyelje a teljes veremet. 

## <a name="next-steps"></a>Következő lépések

- [A partneri témakörök áttekintése](partner-events-overview.md)
- [Az Auth0-partneri témakör használata](auth0-how-to.md)
- [A Auth0 dokumentációja](https://auth0.com/docs/azure-tutorial)
- [Legyen Event Grid partner](partner-onboarding-overview.md)

