---
title: Alkalmazás láthatósága és vezérlése Microsoft Cloud App Security
description: Megtudhatja, hogyan azonosíthatja az alkalmazás kockázati szintjeit, hogyan állíthatja le valós időben a biztonsági incidenseket és szivárgásokat, és hogyan használhatja az alkalmazás-összekötőket a szolgáltatói API-k láthatósága és irányítása érdekében.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9a9f56d70e049200cee0c3655a9998feccfa301
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377836"
---
# <a name="cloud-app-visibility-and-control"></a>Felhőalkalmazások láthatósága és vezérlése

A felhőalkalmazások és -szolgáltatások teljes körű kihasználása érdekében az it-csapatnak meg kell találnia a megfelelő egyensúlyt a hozzáférés támogatásában, miközben fenn kell tartania a kritikus fontosságú adatok védelmét is. Microsoft Cloud App Security adatok részletes átláthatóságát, az adatok utazásának szabályozását, valamint kifinomult elemzéseket biztosít az összes Microsoft- és külső felhőszolgáltatás kiber veszélyének azonosításához és elleni támadásokhoz.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Árnyék-infrastruktúra feltárása és kezelése a hálózatban

Amikor a rendszergazdáktól azt kérdezik, hogy szerintük hány felhőalkalmazást használnak az alkalmazottaik, átlagosan 30-40-et, a valóságban pedig az átlag több mint 1000 különböző alkalmazás, amelyet a szervezet alkalmazottai használnak. Az árnyék-itazák segítenek a használt alkalmazások és a kockázati szint azonosításában. Az alkalmazottak 80%-a nem olyan nem megfelelő alkalmazásokat használ, amelyek nincsenek ellenőrizve, és nem követelményeknek hatnak a biztonsági és megfelelőségi szabályzatok. És mivel az alkalmazottak a vállalati hálózaton kívülről is hozzáférhetnek az erőforrásokhoz és alkalmazásokhoz, többé nem elég, ha szabályok és szabályzatok vannak a tűzfalakon.

A Microsoft Cloud App Discovery (egy prémium szintű Azure Active Directory P1 funkció) használatával felderítheti, hogy mely alkalmazások vannak használatban, megismerheti az alkalmazások kockázatát, szabályzatokat konfigurálhat az új kockázatos alkalmazások azonosításához, és letilthatja ezeket az alkalmazásokat, hogy natív módon letiltsa őket a proxy vagy a tűzfalberendezés használatával.

- Árnyék-infrastruktúra feltárása és azonosítása
- Értékelés és elemzés
- Alkalmazások kezelése
- Speciális árnyék-felderítési jelentéskészítés
- Az jóváhagyott alkalmazások vezérlése
 
### <a name="learn-more"></a>Tudjon meg többet

- [Árnyék-ita felderítése és kezelése a hálózaton ](/cloud-app-security/tutorial-shadow-it)
- [Felderített alkalmazások Cloud App Security ](/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Felhasználói munkamenet láthatósága és vezérlése 

Napjaink munkahelyén gyakran nem elég tudni, hogy mi történik a felhőalapú környezetben a tény után. Meg szeretné akadályozni az adatszivárgást és a szivárgást valós időben, mielőtt az alkalmazottak szándékosan vagy akaratlanul is kockázatot vállalnak az adatok és a szervezet számára. A Azure Active Directory (Azure AD) Microsoft Cloud App Security együttesen holisztikus és integrált felhasználói élményben biztosítja ezeket a feltételes hozzáférést biztosító alkalmazás-vezérlő. 

A munkamenet-vezérlés fordított proxy architektúrát használ, és egyedi módon integrálva van az Azure AD feltételes hozzáféréssel. Az Azure AD feltételes hozzáférés lehetővé teszi, hogy bizonyos feltételek alapján hozzáférési vezérlőket kényszerítsen a szervezet alkalmazásán. A feltételek határozzák meg, hogy ki (felhasználó vagy felhasználócsoport) és mire (mely felhőalkalmazások) és hol (mely helyekre és hálózatokra) vonatkozik a feltételes hozzáférési szabályzat. Miután meghatározta a feltételeket, a felhasználókat olyan Cloud App Security, ahol valós időben védheti az adatokat.  

Ezzel a vezérlővel a következőt használhatja:  
- Fájlletöltések vezérlése
- B2B-forgatókönyvek figyelése  
- Fájlokhoz való hozzáférés szabályozása  
- Dokumentumok védelme letöltés esetén  
 
### <a name="learn-more"></a>Tudjon meg többet

- [Alkalmazások védelme a munkamenet-vezérléssel a Cloud App Security ](/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Alkalmazások speciális láthatósága és vezérlői 

Az alkalmazás-összekötők az alkalmazásszolgáltatók API-kkal teszik lehetővé a jobb átláthatóságot és Microsoft Cloud App Security a kapcsolódó alkalmazások felett. Cloud App Security a felhőszolgáltató által biztosított API-kat használja. Mindegyik szolgáltatás saját keretrendszer- és API-korlátozásokkal rendelkezik, például szabályozással, API-korlátokkal, dinamikus időáttűnő API-ablakokkal stb. A Cloud App Security termékcsapat ezekkel a szolgáltatásokkal optimalizálta az API-k használatát, és a legjobb teljesítményt nyújtotta. Az API-kra vonatkozó különböző korlátozásokat figyelembe véve a Cloud App Security motorja a maximálisan engedélyezett kapacitást használja. Egyes műveletekhez, például a bérlő összes fájljának beolvasásához számos API-hívásra van szükség, hogy hosszabb ideig terjedjen. Egyes szabályzatok várhatóan több órán vagy napon át futnak. 
 
### <a name="learn-more"></a>Tudjon meg többet  

- [Alkalmazások csatlakoztatása Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Következő lépések

- [Árnyék-ita felderítése és kezelése a hálózaton ](/cloud-app-security/tutorial-shadow-it)
- [Felderített alkalmazások Cloud App Security ](/cloud-app-security/discovered-apps)
- [Alkalmazások védelme a munkamenet-vezérléssel a Cloud App Security ](/cloud-app-security/proxy-intro-aad)
- [Alkalmazások csatlakoztatása Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)