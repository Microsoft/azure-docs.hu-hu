---
title: Az Azure AD Application Proxy biztonsági szempontjai | Microsoft Docs
description: Az Azure AD Application Proxy használatának biztonsági szempontjait ismerteti
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1c0e91b08fff80c0308c0ead5fabce903421adc
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090120"
---
# <a name="security-considerations-for-accessing-apps-remotely-with-azure-ad-application-proxy"></a>Az alkalmazások távoli Azure-AD Application Proxy való elérésének biztonsági szempontjai

Ez a cikk ismerteti azokat az összetevőket, amelyek segítségével a felhasználók és alkalmazások biztonságban maradhatnak Azure Active Directory Application Proxy használatakor.

Az alábbi ábra azt mutatja be, hogy az Azure AD hogyan teszi lehetővé a biztonságos távoli hozzáférést a helyszíni alkalmazásokhoz.

 ![Biztonságos távoli hozzáférés diagramja az Azure AD Application Proxy](./media/application-proxy-security/secure-remote-access.png)

## <a name="security-benefits"></a>Biztonsági előnyök

Az Azure AD Application Proxy a következő biztonsági előnyöket kínálja:

### <a name="authenticated-access"></a>Hitelesített hozzáférés 

Ha úgy dönt, hogy Azure Active Directory előhitelesítést használ, csak a hitelesített kapcsolatok férhetnek hozzá a hálózathoz.

Az Azure AD Application Proxy az összes hitelesítéshez az Azure AD biztonsági jogkivonat szolgáltatás (STS) szolgáltatást használja.  Az előhitelesítés jellegéből adódóan jelentős számú névtelen támadást blokkol, mert csak hitelesített identitások férhetnek hozzá a háttérbeli alkalmazáshoz.

Ha a továbbítót az előhitelesítési módszerként választja, akkor nem kap ilyen kedvezményt. 

### <a name="conditional-access"></a>Feltételes hozzáférés

A hálózatra irányuló kapcsolatok létrehozása előtt alkalmazzon gazdagabb házirend-vezérlőket.

A [feltételes hozzáféréssel](../conditional-access/concept-conditional-access-cloud-apps.md)korlátozásokat határozhat meg, hogy a felhasználók hogyan férhetnek hozzá az alkalmazásokhoz. Olyan házirendeket hozhat létre, amelyek a hely, a hitelesítés erőssége és a felhasználói kockázati profil alapján korlátozzák a bejelentkezéseket.

A feltételes hozzáférés használatával Multi-Factor Authentication házirendeket is konfigurálhat, és további biztonsági réteget adhat hozzá a felhasználói hitelesítésekhez. Emellett az alkalmazások az Azure AD feltételes hozzáférés használatával Microsoft Cloud App Security is továbbíthatók, így valós idejű monitorozást és vezérlést biztosítanak a [hozzáférési](/cloud-app-security/access-policy-aad) és [munkamenet](/cloud-app-security/session-policy-aad) -szabályzatok segítségével.

### <a name="traffic-termination"></a>Forgalom leállítása

A felhőben minden forgalom megszakad.

Mivel az Azure AD Application Proxy egy fordított proxy, a rendszer a háttérbeli alkalmazások minden forgalmát leállítja a szolgáltatásnál. A munkamenetet csak a háttér-kiszolgálóval lehet újból létrehozni, ami azt jelenti, hogy a háttér-kiszolgálók nem lesznek elérhetők a HTTP-forgalom közvetlen eléréséhez. Ez a konfiguráció azt jelenti, hogy jobb védelmet biztosít a megcélzó támadásokkal szemben.

### <a name="all-access-is-outbound"></a>Minden hozzáférés kimenő 

Nincs szükség a vállalati hálózatra irányuló bejövő kapcsolatok megnyitására.

Az alkalmazásproxy-összekötők csak a kimenő kapcsolatokat használják az Azure AD Application Proxy szolgáltatáshoz, ami azt jelenti, hogy nincs szükség a tűzfal portjainak megnyitására a bejövő kapcsolatokhoz. A hagyományos proxyk számára szükséges a peremhálózat (más néven *DMZ*, *vagy demilitarizált zóna* vagy *szűrt alhálózat*), és engedélyezett a nem hitelesített kapcsolatokhoz való hozzáférés a hálózati Edge-ben. Ez a forgatókönyv szükséges a webalkalmazási tűzfal termékeiben a forgalom elemzéséhez és a környezet megóvásához szükséges beruházásokhoz. Az alkalmazásproxy használata esetén nincs szükség peremhálózati hálózatra, mert minden kapcsolat kimenő, és egy biztonságos csatornán történik.

További információ az összekötők használatáról: az [Azure ad Application proxy-összekötők ismertetése](application-proxy-connectors.md).

### <a name="cloud-scale-analytics-and-machine-learning"></a>Felhőalapú elemzés és gépi tanulás 

Az élvonalbeli biztonsági védelem beszerzése.

Mivel Azure Active Directory része, az alkalmazásproxy kihasználhatja a [Azure ad Identity Protection](../identity-protection/overview-identity-protection.md), a Microsoft Security Response Center és a digitális bűncselekmények egységből származó adatokkal. Együtt proaktív módon azonosítjuk a feltört fiókokat, és magas kockázatú bejelentkezések elleni védelmet biztosítunk. Számos tényezőt figyelembe veszünk annak meghatározásához, hogy mely bejelentkezési kísérletek nagy kockázatot jelentenek. Ezek a tényezők közé tartoznak a fertőzött eszközök, anonimizálásával hálózatok és atipikus vagy nem valószínű helyszínek megjelölése.

Ezen jelentések és események közül sok már elérhető egy API-n keresztül a biztonsági információkkal és az Event Management-(SIEM-) rendszerekkel való integrációhoz.

### <a name="remote-access-as-a-service"></a>Távoli elérés szolgáltatásként

Nem kell aggódnia a helyszíni kiszolgálók karbantartásával és javításával kapcsolatban.

A nem javított szoftverek nagy számú támadás esetén is fiókok. Az Azure AD Application Proxy egy Internet-méretezési szolgáltatás, amelyet a Microsoft birtokol, így mindig a legújabb biztonsági javításokat és frissítéseket kapja meg.

Az Azure AD Application Proxy által közzétett alkalmazások biztonságának javítása érdekében letiltjuk a webbejáró-robotok számára az alkalmazások indexelését és archiválását. Minden alkalommal, amikor egy weblánctalpas robot megpróbál beolvasni egy közzétett alkalmazás robotjának beállításait, az alkalmazásproxy egy robots.txt fájllal válaszol, amely tartalmazza a következőt: `User-agent: * Disallow: /` .

#### <a name="azure-ddos-protection-service"></a>Azure DDoS Protection szolgáltatás

Az Application proxyn keresztül közzétett alkalmazások védve vannak az elosztott szolgáltatásmegtagadási (DDoS) támadások ellen. Ezt a védelmet a Microsoft felügyeli, és minden adatközpontban automatikusan engedélyezve van. Az Azure DDoS Protection szolgáltatás mindig a forgalom figyelését és a gyakori hálózati szintű támadások valós idejű enyhítését biztosítja. 

## <a name="under-the-hood"></a>technikai részletek

Az Azure AD Application Proxy két részből áll:

* Felhőalapú szolgáltatás: Ez a szolgáltatás az Azure-ban fut, és a külső ügyfél-/felhasználói kapcsolatok.
* [A helyszíni összekötő](application-proxy-connectors.md): egy helyszíni összetevő, az összekötő figyeli az Azure ad Application proxy szolgáltatástól érkező kéréseket, és kezeli a belső alkalmazásokhoz való kapcsolódást. 

Az összekötő és az alkalmazásproxy szolgáltatás közötti folyamat az alábbiak szerint jön:

* Az összekötő először be van állítva.
* Az összekötő lekéri a konfigurációs adatokat az alkalmazásproxy szolgáltatásból.
* A felhasználó hozzáfér egy közzétett alkalmazáshoz.

>[!NOTE]
>Minden kommunikáció a TLS protokollal történik, és mindig az alkalmazásproxy szolgáltatáshoz tartozó összekötőből származnak. A szolgáltatás csak kimenő.

Az összekötő egy ügyféltanúsítványt használ az alkalmazásproxy szolgáltatásba való hitelesítéshez szinte minden híváshoz. A folyamat egyetlen kivétele a kezdeti telepítési lépés, ahol az ügyféltanúsítvány létrejött.

### <a name="installing-the-connector"></a>Az összekötő telepítése

Az összekötő első beállításakor a következő flow-események lépnek életbe:

1. A szolgáltatáshoz való regisztráció az összekötő telepítésének részeként történik. A rendszer felszólítja a felhasználókat az Azure AD-rendszergazdai hitelesítő adataik megadására. Ekkor a hitelesítésből beszerzett token jelenik meg az Azure AD Application Proxy szolgáltatásban.
2. Az alkalmazásproxy szolgáltatás kiértékeli a jogkivonatot. Ellenőrzi, hogy a felhasználó a bérlő globális rendszergazdája-e. Ha a felhasználó nem rendszergazda, a folyamat megszakad.
3. Az összekötő létrehoz egy ügyféltanúsítvány-kérelmet, és továbbítja azt a jogkivonattal együtt az alkalmazásproxy szolgáltatásnak. A szolgáltatás pedig ellenőrzi a jogkivonatot, és aláírja az ügyféltanúsítvány-kérelmet.
4. Az összekötő az ügyféltanúsítványt használja az alkalmazásproxy szolgáltatással folytatott jövőbeli kommunikációhoz.
5. Az összekötő a szolgáltatásból a rendszerkonfigurációs adatok kezdeti lekérését végzi az ügyféltanúsítvány használatával, és most már készen áll a kérelmekre.

### <a name="updating-the-configuration-settings"></a>A konfigurációs beállítások frissítése

Amikor az Application proxy szolgáltatás frissíti a konfigurációs beállításokat, a következő folyamatokra kerül sor:

1. Az összekötő az alkalmazásproxy szolgáltatáson belüli konfigurációs végponthoz kapcsolódik az ügyféltanúsítvány használatával.
2. Az ügyféltanúsítvány érvényesítése után az alkalmazásproxy szolgáltatás visszaadja a konfigurációs adatok az összekötőnek (például az összekötő csoportnak, amelyhez az összekötőnek kell lennie).
3. Ha az aktuális tanúsítvány több mint 180 nap, az összekötő létrehoz egy új tanúsítványkérelmet, amely minden 180 naponként frissíti az ügyféltanúsítványt.

### <a name="accessing-published-applications"></a>Közzétett alkalmazások elérése

Amikor a felhasználók egy közzétett alkalmazáshoz férnek hozzá, a következő események kerülnek az alkalmazásproxy szolgáltatás és az alkalmazásproxy-összekötő között:

1. A szolgáltatás hitelesíti a felhasználót az alkalmazáshoz
2. A szolgáltatás egy kérelmet helyez el az összekötő-várólistában.
3. Egy összekötő dolgozza fel a kérést a várólistából.
4. Az összekötő várakozik a válaszra
5. A szolgáltatás továbbítja az adatforgalmat a felhasználónak

Ha többet szeretne megtudni arról, hogy mi történik az egyes lépések során, folytassa az olvasást.


#### <a name="1-the-service-authenticates-the-user-for-the-app"></a>1. a szolgáltatás hitelesíti a felhasználót az alkalmazáshoz

Ha úgy állította be az alkalmazást, hogy a továbbítót használja az előhitelesítési módszerként, a szakasz lépései kimaradnak.

Ha az alkalmazást az Azure AD-vel való előzetes hitelesítésre konfigurálta, a rendszer átirányítja a felhasználókat az Azure AD STS-be a hitelesítéshez, és végrehajtja a következő lépéseket:

1. Az alkalmazásproxy ellenőrzi az adott alkalmazásra vonatkozó feltételes hozzáférési szabályzatok követelményeit. Ez a lépés biztosítja, hogy a felhasználó hozzá legyen rendelve az alkalmazáshoz. Ha kétlépéses ellenőrzésre van szükség, a hitelesítési folyamat megkéri a felhasználót egy második hitelesítési módszer megadására.

2. Az összes ellenőrzés után az Azure AD STS kibocsátja az alkalmazás aláírt jogkivonatát, és visszairányítja a felhasználót az alkalmazásproxy szolgáltatáshoz.

3. Az alkalmazásproxy ellenőrzi, hogy a jogkivonat ki lett-e állítva a megfelelő alkalmazás számára. Más ellenőrzéseket is végrehajt, például annak biztosítását, hogy a tokent az Azure AD aláírta, és hogy még mindig az érvényes ablakon belül legyen.

4. Az alkalmazásproxy egy titkosított hitelesítési cookie-t állít be, amely jelzi, hogy az alkalmazás hitelesítése megtörtént. A cookie tartalmaz egy lejárati időbélyeget, amely az Azure AD-től és egyéb adatoktól, például a hitelesítés alapjául szolgáló felhasználónévtől függ. A cookie titkosítása csak az alkalmazásproxy szolgáltatás által ismert titkos kulccsal történik.

5. Az Application proxy visszairányítja a felhasználót az eredetileg kért URL-címre.

Ha az előhitelesítés lépéseinek bármelyik része meghiúsul, a rendszer megtagadja a felhasználó kérését, és a felhasználó megjelenik egy üzenet, amely jelzi a probléma forrását.


#### <a name="2-the-service-places-a-request-in-the-connector-queue"></a>2. a szolgáltatás egy kérelmet helyez el az összekötő-várólistában.

Az összekötők az alkalmazásproxy szolgáltatás számára nyitva tartanak egy kimenő kapcsolatokat. Ha a kérelem bekerül, a szolgáltatás várólistára helyezi a kérést az összekötő egyik nyitott kapcsolatán, hogy felvegye a szolgáltatást.

A kérelem tartalmazza az alkalmazás elemeit, például a kérések fejléceit, a titkosított cookie adatait, a kérést kérő felhasználót és a kérés AZONOSÍTÓját. Bár a rendszer a titkosított cookie-ból érkező adatokkal küldi el a kérést, a hitelesítési cookie nem.

#### <a name="3-the-connector-processes-the-request-from-the-queue"></a>3. az összekötő dolgozza fel a kérést a várólistából. 

A kérelem alapján az Application proxy a következő műveletek egyikét hajtja végre:

* Ha a kérés egy egyszerű művelet (például nem áll rendelkezésre adatok a törzsben, mint egy REST *-kéréses kérelem)* , az összekötő csatlakozik a cél belső erőforráshoz, majd megvárja a választ.

* Ha a kérelemben hozzá van rendelve adatok a törzsben (például egy REST *utáni* művelet), az összekötő egy kimenő kapcsolódást végez az ügyféltanúsítvány használatával az alkalmazásproxy-példányhoz. Ez a kapcsolódás lehetővé teszi az adatkérést és a belső erőforrással létesített kapcsolatok megnyitását. Miután megkapta a kérést az összekötőtől, az alkalmazásproxy szolgáltatás megkezdi a tartalom fogadását a felhasználótól, és továbbítja az adatokat az összekötőnek. Az összekötő viszont a belső erőforrásnak továbbítja az adatforrást.

#### <a name="4-the-connector-waits-for-a-response"></a>4. az összekötő várakozik a válaszra.

Miután befejeződött az összes tartalom kérése és továbbítása a háttér végére, az összekötő várakozik a válaszra.

Miután a kapott választ, az összekötő kimenő kapcsolattal csatlakozik az alkalmazásproxy-szolgáltatáshoz, és visszaküldi a fejléc részleteit, és megkezdi a visszatérési adatok továbbítását.

#### <a name="5-the-service-streams-data-to-the-user"></a>5. a szolgáltatás továbbítja az adatforgalmat a felhasználó felé. 

Az alkalmazás egyes feldolgozása itt fordulhat elő. Ha úgy konfigurálta az alkalmazásproxy-t, hogy az alkalmazásban fejléceket vagy URL-címeket fordítson le, akkor ez a lépés a szükséges módon történik.


## <a name="next-steps"></a>Következő lépések

[Hálózati topológiai megfontolások az Azure AD Application Proxy használatakor](application-proxy-network-topology.md)

[Az Azure AD Application Proxy-összekötők ismertetése](application-proxy-connectors.md)