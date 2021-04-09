---
title: Hitelesítés – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Ismerje meg a Threat Modeling Tool hitelesítésének mérséklését. Tekintse meg a kockázatcsökkentő információkat és a kód megjelenítése példákat.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: ee4dd70faab9ed44b1aa6ca8ca0ec517c7746f66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94832530"
---
# <a name="security-frame-authentication--mitigations"></a>Biztonsági keret: hitelesítés | Enyhítését

| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás**    | <ul><li>[A webalkalmazások hitelesítéséhez használjon szabványos hitelesítési mechanizmust](#standard-authn-web-app)</li><li>[Az alkalmazásoknak biztonságosan kell kezelniük a sikertelen hitelesítési forgatókönyveket](#handle-failed-authn)</li><li>[Lépésenkénti vagy adaptív hitelesítés engedélyezése](#step-up-adaptive-authn)</li><li>[Győződjön meg arról, hogy a felügyeleti felületek megfelelően vannak zárolva](#admin-interface-lockdown)</li><li>[Elfelejtett jelszó funkcióinak biztonságos implementálása](#forgot-pword-fxn)</li><li>[Győződjön meg arról, hogy a jelszó és a fiók házirendje implementálva van](#pword-account-policy)</li><li>[Vezérlőelemek implementálása a Felhasználónév enumerálásának megakadályozása érdekében](#controls-username-enum)</li></ul> |
| **Adatbázis** | <ul><li>[Ha lehetséges, a Windows-hitelesítés használatával csatlakozzon SQL Server](#win-authn-sql)</li><li>[Ha lehetséges, Azure Active Directory hitelesítést használ SQL Databasehoz való csatlakozáshoz](#aad-authn-sql)</li><li>[SQL-hitelesítési mód használata esetén győződjön meg arról, hogy a fiók és a jelszó házirendje kényszerítve van az SQL Serveren.](#authn-account-pword)</li><li>[SQL-hitelesítés használata a foglalt adatbázisokban](#autn-contained-db)</li></ul> |
| **Azure Event Hub** | <ul><li>[Eszközönkénti hitelesítő adatok használata SaS-tokenek használatával](#authn-sas-tokens)</li></ul> |
| **Azure-beli megbízhatósági kapcsolat határa** | <ul><li>[Azure AD-Multi-Factor Authentication engedélyezése az Azure-rendszergazdák számára](#multi-factor-azure-admin)</li></ul> |
| **Service Fabric megbízhatósági kapcsolat határa** | <ul><li>[Névtelen hozzáférés korlátozása Service Fabric fürthöz](#anon-access-cluster)</li><li>[Győződjön meg arról, hogy Service Fabric ügyfél és csomópont közötti tanúsítvány különbözik a csomópontok közötti tanúsítványtól](#fabric-cn-nn)</li><li>[Az ügyfelek hitelesítése a Service Fabric-fürtökön a HRE használatával](#aad-client-fabric)</li><li>[Győződjön meg arról, hogy a Service Fabric-tanúsítványok beszerzése jóváhagyott hitelesítésszolgáltatótól történik](#fabric-cert-ca)</li></ul> |
| **Identity Server** | <ul><li>[Az Identity Server által támogatott szabványos hitelesítési forgatókönyvek használata](#standard-authn-id)</li><li>[Az Identity Server-jogkivonat alapértelmezett gyorsítótárának felülbírálása skálázható alternatív megoldással](#override-token)</li></ul> |
| **Számítógép-megbízhatósági kapcsolat határa** | <ul><li>[Győződjön meg arról, hogy a telepített alkalmazás bináris fájljai digitálisan vannak aláírva](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Hitelesítés engedélyezése az MSMQ-várólistákhoz való csatlakozáskor a WCF-ben](#msmq-queues)</li><li>[WCF – ne állítsa be az üzenet clientCredentialType nincs értékre](#message-none)</li><li>[WCF – ne állítson be átviteli clientCredentialType nincs értékre](#transport-none)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy a webes API-k biztonságossá tétele a szabványos hitelesítési módszerekkel történik](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[A Azure Active Directory által támogatott szabványos hitelesítési forgatókönyvek használata](#authn-aad)</li><li>[Az alapértelmezett ADAL-jogkivonat gyorsítótárának felülbírálása méretezhető alternatív megoldással](#adal-scalable)</li><li>[Győződjön meg arról, hogy a TokenReplayCache a ADAL-hitelesítési tokenek visszajátszásának megakadályozására szolgál](#tokenreplaycache-adal)</li><li>[ADAL-kódtárak használata a OAuth2-ügyfelektől a HRE (vagy a helyszíni AD-hez) való jogkivonat-kérelmek kezeléséhez](#adal-oauth2)</li></ul> |
| **IoT-mező átjárója** | <ul><li>[A Field Gatewayhez csatlakozó eszközök hitelesítése](#authn-devices-field)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Győződjön meg arról, hogy a felhőalapú átjáróhoz csatlakozó eszközök hitelesítése megtörtént](#authn-devices-cloud)</li><li>[Eszközönkénti hitelesítő adatok használata](#authn-cred)</li></ul> |
| **Azure Storage** | <ul><li>[Győződjön meg arról, hogy csak a szükséges tárolók és Blobok névtelen olvasási hozzáférést kapnak](#req-containers-anon)</li><li>[Korlátozott hozzáférés biztosítása az Azure Storage-beli objektumokhoz SAS vagy SAP használatával](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>A webalkalmazások hitelesítéséhez használjon szabványos hitelesítési mechanizmust

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| Részletek | <p>A hitelesítés az a folyamat, amelyben az entitás igazolja az identitását, jellemzően hitelesítő adatokkal, például felhasználónévvel és jelszóval. Több hitelesítési protokoll is rendelkezésre áll, amelyek megtekinthetők. Némelyikük alább látható:</p><ul><li>Ügyféltanúsítványok</li><li>Windows-alapú</li><li>Űrlapok alapján</li><li>Összevonás – ADFS</li><li>Összevonás – Azure AD</li><li>Összevonási-identitás kiszolgálója</li></ul><p>Érdemes lehet szabványos hitelesítési mechanizmust használni a forrás folyamat azonosítására.</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>Az alkalmazásoknak biztonságosan kell kezelniük a sikertelen hitelesítési forgatókönyveket

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| Részletek | <p>A felhasználókat explicit módon hitelesítő alkalmazásoknak biztonságosan kell kezelniük a sikertelen hitelesítési forgatókönyveket. A hitelesítési mechanizmusnak a következőket kell tennie:</p><ul><li>Jogosultsággal rendelkező erőforrásokhoz való hozzáférés megtagadása, ha a hitelesítés meghiúsul</li><li>Általános hibaüzenet megjelenítése a sikertelen hitelesítés után, és a hozzáférés megtagadva</li></ul><p>Tesztelés:</p><ul><li>A rendszerjogosultságú erőforrások védelme sikertelen bejelentkezések után</li><li>Általános hibaüzenet jelenik meg a sikertelen hitelesítés és a hozzáférés-megtagadási esemény (ek) esetén</li><li>A fiókok túl sok sikertelen kísérlet után le vannak tiltva</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>Lépésenkénti vagy adaptív hitelesítés engedélyezése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| Részletek | <p>Ellenőrizze, hogy az alkalmazás rendelkezik-e további engedélyekkel (például a Step up vagy az adaptív hitelesítéssel, többtényezős hitelesítéssel, például az egyszeri jelszavas küldéssel SMS-ben, e-mailben stb.), illetve az ismételt hitelesítés megadásával. Ez a szabály arra is vonatkozik, hogy a fiók vagy a művelet kritikus fontosságú módosításokat hajtson végre.</p><p>Ez azt is jelenti, hogy a hitelesítés hozzáigazítását olyan módon kell megvalósítani, hogy az alkalmazás helyesen kényszerítse ki a kontextus-érzékeny hitelesítést, hogy ne engedélyezze a jogosulatlan kezelést például a paraméterek módosításával</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>Győződjön meg arról, hogy a felügyeleti felületek megfelelően vannak zárolva

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| Részletek | Az első megoldás az, hogy csak egy adott forrás IP-címről biztosít hozzáférést a felügyeleti felülethez. Ha ez a megoldás nem lenne lehetséges, mint a felügyeleti felületen való bejelentkezéshez szükséges lépésenkénti vagy adaptív hitelesítés kikényszerítés. |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>Elfelejtett jelszó funkcióinak biztonságos implementálása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| Részletek | <p>Először is meg kell győződnie arról, hogy az elfelejtett jelszó és az egyéb helyreállítási útvonalak egy hivatkozást küldenek, beleértve egy időkorlátos aktiválási jogkivonatot is, nem pedig magát a jelszót. A további hitelesítés a Soft-tokenek (például SMS-token, natív mobil alkalmazások stb.) alapján is megkövetelhető, még a hivatkozás elküldése előtt is. Másodszor, ne zárja be a felhasználói fiókot, amíg az új jelszó beszerzésének folyamata folyamatban van.</p><p>Ez szolgáltatásmegtagadást idézhet elő, amikor egy támadó úgy dönt, hogy szándékosan zárolja a felhasználókat egy automatizált támadással. Harmadszor, ha az új jelszó kérésének beállítása folyamatban van, a megjelenített üzenetnek általánosítva kell lennie a Felhasználónév enumerálásának megelőzése érdekében. Negyedszer, mindig letiltja a régi jelszavak használatát és erős jelszóházirend megvalósítását.</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>Győződjön meg arról, hogy a jelszó és a fiók házirendje implementálva van

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| Részletek | <p>A szervezeti házirendnek és az ajánlott eljárásoknak megfelelően a jelszó-és a fiók házirendjét kell megvalósítani.</p><p>A találgatásos támadásokkal és a szótáron alapuló találgatásokkal szembeni védelem érdekében: erős jelszóházirend szükséges ahhoz, hogy a felhasználók összetett jelszót hozzanak létre (például 12 karakter hosszúságú, alfanumerikus és speciális karaktereket).</p><p>A fiókzárolási házirendeket a következő módon lehet megvalósítani:</p><ul><li>**Puha zárolás:** Ez jó lehetőség lehet a felhasználók elleni védelemre a találgatásos támadásokkal szemben. Ha például a felhasználó háromszor helytelen jelszót ad meg, akkor az alkalmazás egy percig zárolhatja a fiókot, hogy lelassítsa a jelszó kikényszerítésének folyamatát, hogy a támadók a folytatáshoz kevésbé nyereségesek legyenek. Ha a fenti példában a nehezen kizáró ellenintézkedéseket kívánja megvalósítani, a fiókok végleges kikapcsolásával "DoS-t" érhet el. Azt is megteheti, hogy az alkalmazás létrehoz egy egyszeri jelszavas (egyszer használatos) jelszót, és sávon kívüli (e-mailben, SMS-ben stb.) küldi el a felhasználónak. Egy másik megközelítés lehet a CAPTCHA implementálása a sikertelen kísérletek küszöbértékének elérésekor.</li><li>**Rögzített zárolás:** Ezt a zárolási típust akkor kell alkalmazni, amikor az alkalmazást támadó felhasználót azonosít, és a fiókját tartósan kizárja, amíg a válasz csapatnak nem volt ideje a kriminalisztikai munkára. A folyamat után dönthet úgy, hogy a felhasználó visszaadja a fiókját, vagy további jogi műveleteket hajt végre rajtuk. Ez a fajta megközelítés megakadályozza, hogy a támadó továbbra is behatoljon az alkalmazásba és az infrastruktúrába.</li></ul><p>Az alapértelmezett és kiszámítható fiókok elleni támadások elleni védelem érdekében ellenőrizze, hogy az összes kulcs és jelszó áthelyezhető-e, és hogy a telepítés után a rendszer létrehozza vagy lecseréli-e azokat.</p><p>Ha az alkalmazásnak automatikusan kell létrehoznia a jelszavakat, ügyeljen arra, hogy a generált jelszavak véletlenszerűek legyenek, és magas entrópia legyen.</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>Vezérlőelemek implementálása a Felhasználónév enumerálásának megakadályozása érdekében

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Az összes hibaüzenetet általánosítani kell a Felhasználónév enumerálásának megelőzése érdekében. Időnként előfordulhat, hogy nem kerülheti el az adatok szivárgását olyan funkciókkal, mint a regisztrációs oldal. Itt kell használnia a rate-limiting metódusokat, például a CAPTCHA-t, hogy megakadályozza a támadók automatizált támadását. |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>Ha lehetséges, a Windows-hitelesítés használatával csatlakozzon SQL Server

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | OnPrem |
| **Attribútumok**              | SQL-verzió – mind |
| **Hivatkozások**              | [SQL Server – hitelesítési mód kiválasztása](/sql/relational-databases/security/choose-an-authentication-mode) |
| **Lépések** | A Windows-hitelesítés Kerberos biztonsági protokollt használ, a jelszó-házirend kényszerítését biztosítja az erős jelszavak összetettségének ellenőrzésével kapcsolatban, és támogatja a fiók zárolását, és támogatja a jelszó lejáratát.|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>Ha lehetséges, Azure Active Directory hitelesítést használ SQL Databasehoz való csatlakozáshoz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | SQL Azure |
| **Attribútumok**              | SQL-verzió – V12 |
| **Hivatkozások**              | [Csatlakozás SQL Databasehoz Azure Active Directory hitelesítés használatával](../../azure-sql/database/authentication-aad-overview.md) |
| **Lépések** | **Minimális verzió:** Azure SQL Database V12 szükséges ahhoz, hogy a Azure SQL Database HRE-hitelesítést használhassanak a Microsoft-címtárral |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>SQL-hitelesítési mód használata esetén győződjön meg arról, hogy a fiók és a jelszó házirendje kényszerítve van az SQL Serveren.

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [SQL Server jelszóházirend](/previous-versions/sql/sql-server-2012/ms161959(v=sql.110)) |
| **Lépések** | SQL Server hitelesítés használatakor a rendszer olyan SQL Server hozza létre a bejelentkezéseket, amelyek nem Windows felhasználói fiókokon alapulnak. A felhasználónevet és a jelszót is létrehozhatja a SQL Server használatával, és SQL Server tárolja. SQL Server használhatja a Windows-jelszóházirend mechanizmusait. A Windowsban a SQL Serverban használt jelszavakkal megegyező összetettségi és elévülési szabályzatokat alkalmazhat. |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>SQL-hitelesítés használata a foglalt adatbázisokban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Helyszíni, SQL Azure |
| **Attribútumok**              | SQL-verzió – MSSQL2012, SQL-verzió – V12 |
| **Hivatkozások**              | [Ajánlott biztonsági eljárások a foglalt adatbázisokkal](/sql/relational-databases/databases/security-best-practices-with-contained-databases) |
| **Lépések** | A kényszerített jelszóházirend hiánya növelheti a gyenge hitelesítő adatoknak a tárolt adatbázisban való létrehozása valószínűségét. Windows-hitelesítés kihasználása. |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>Eszközönkénti hitelesítő adatok használata SaS-tokenek használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Event Hub |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Event Hubs hitelesítési és biztonsági modell áttekintése](../../event-hubs/authenticate-shared-access-signature.md) |
| **Lépések** | <p>A Event Hubs biztonsági modell a közös hozzáférésű aláírási (SAS-) tokenek és az esemény-közzétevők kombinációján alapul. A közzétevő neve a jogkivonatot fogadó DeviceID. Ez segít a megfelelő eszközökhöz generált jogkivonatok hozzárendelésében.</p><p>Minden üzenet a szolgáltatás oldalán található kezdeményezővel van megjelölve, amely lehetővé teszi a hasznos adatokkal való adatgyűjtési kísérletek észlelését. Az eszközök hitelesítése esetén az eszközönkénti SaS-tokent egy egyedi közzétevőre kell állítani.</p>|

## <a name="enable-azure-ad-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Azure AD-Multi-Factor Authentication engedélyezése az Azure-rendszergazdák számára

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure-beli megbízhatósági kapcsolat határa |
| **SDL-fázis**               | Üzembe helyezés |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Mi az az Azure AD Multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md) |
| **Lépések** | <p>A többtényezős hitelesítés (MFA) olyan hitelesítési módszer, amely egynél több ellenőrzési módszert igényel, és kritikus második biztonsági réteget vesz fel a felhasználói bejelentkezésekhez és tranzakciókra. Úgy működik, hogy az alábbi ellenőrzési módszerek közül kettőt vagy többet igényel:</p><ul><li>Amit ismer (általában jelszó)</li><li>Valami, ami rendelkezik (olyan megbízható eszköz, amely nem könnyen duplikált, például telefon)</li><li>Egy dolog (biometria)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Névtelen hozzáférés korlátozása Service Fabric fürthöz

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határa |
| **SDL-fázis**               | Üzembe helyezés |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure  |
| **Hivatkozások**              | [Service Fabric fürt biztonsági forgatókönyvei](../../service-fabric/service-fabric-cluster-security.md) |
| **Lépések** | <p>A fürtöket mindig védeni kell annak megakadályozása érdekében, hogy jogosulatlan felhasználók csatlakozzanak a fürthöz, különösen akkor, ha az éles környezetben futó munkaterhelésekkel rendelkezik.</p><p>Service Fabric-fürt létrehozásakor győződjön meg arról, hogy a biztonsági mód "Secure" értékre van állítva, és konfigurálja a szükséges X. 509 kiszolgálói tanúsítványt. Ha egy "nem biztonságos" fürtöt hoz létre, akkor a névtelen felhasználók csatlakozhatnak hozzá, ha a felügyeleti végpontokat a nyilvános internetre teszik közzé.</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>Győződjön meg arról, hogy Service Fabric ügyfél és csomópont közötti tanúsítvány különbözik a csomópontok közötti tanúsítványtól

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határa |
| **SDL-fázis**               | Üzembe helyezés |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure, környezet – önálló |
| **Hivatkozások**              | Az [ügyfél és a csomópont közötti biztonság Service Fabric](../../service-fabric/service-fabric-cluster-security.md#client-to-node-certificate-security), [Kapcsolódás biztonságos fürthöz ügyféltanúsítvány használatával](../../service-fabric/service-fabric-connect-to-secure-cluster.md) |
| **Lépések** | <p>Az ügyfél és a csomópont közötti tanúsítvány biztonsága a fürt létrehozásakor a Azure Portal, a Resource Manager-sablonokon vagy egy önálló JSON-sablonon keresztül történik egy rendszergazdai ügyféltanúsítvány és/vagy felhasználói ügyféltanúsítvány megadásával.</p><p>Az Ön által megadott rendszergazdai ügyfél és felhasználói ügyféltanúsítvány nem lehet azonos a csomópontok közötti biztonsághoz megadott elsődleges és másodlagos tanúsítványokkal.</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>Az ügyfelek hitelesítése a Service Fabric-fürtökön a HRE használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határa |
| **SDL-fázis**               | Üzembe helyezés |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure |
| **Hivatkozások**              | [Fürtök biztonsági forgatókönyvei – biztonsági javaslatok](../../service-fabric/service-fabric-cluster-security.md#security-recommendations) |
| **Lépések** | Az Azure-on futó fürtök Azure Active Directory (HRE) használatával is biztonságossá tehetik a felügyeleti végpontokat, az Ügyféltanúsítványok mellett. Az Azure-fürtök esetében ajánlott a HRE biztonság használatával hitelesíteni az ügyfeleket és a tanúsítványokat a csomópontok közötti biztonság érdekében.|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>Győződjön meg arról, hogy a Service Fabric-tanúsítványok beszerzése jóváhagyott hitelesítésszolgáltatótól történik

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határa |
| **SDL-fázis**               | Üzembe helyezés |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure |
| **Hivatkozások**              | [X. 509 tanúsítványok és Service Fabric](../../service-fabric/service-fabric-cluster-security.md#x509-certificates-and-service-fabric) |
| **Lépések** | <p>A Service Fabric X. 509 kiszolgálói tanúsítványokat használ a csomópontok és az ügyfelek hitelesítéséhez.</p><p>A tanúsítványok a Service fabricben való használata során megfontolandó szempontok:</p><ul><li>Az éles munkaterheléseket futtató fürtökben használt tanúsítványokat megfelelően konfigurált Windows Server Certificate Service használatával kell létrehozni, vagy egy jóváhagyott hitelesítésszolgáltatótól (CA) kell beszerezni. A HITELESÍTÉSSZOLGÁLTATÓ lehet jóváhagyott külső HITELESÍTÉSSZOLGÁLTATÓ vagy megfelelően felügyelt belső nyilvános kulcsokra épülő infrastruktúra (PKI)</li><li>Soha ne használjon olyan ideiglenes vagy tesztelési tanúsítványt az éles környezetben, amely olyan eszközökkel lett létrehozva, mint a MakeCert.exe</li><li>Használhat önaláírt tanúsítványt is, de csak tesztelési fürtök esetében, és nem éles környezetben</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Az Identity Server által támogatott szabványos hitelesítési forgatókönyvek használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Server |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [IdentityServer3 – a nagy kép](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Lépések** | <p>Az alábbi jellemző interakciókat támogatja az Identity Server:</p><ul><li>Böngészőkkel kommunikáló webalkalmazások</li><li>A webalkalmazások webes API-kkal kommunikálnak (esetenként a felhasználók nevében)</li><li>A böngészőalapú alkalmazások kommunikálnak a webes API-kkal</li><li>A natív alkalmazások a webes API-kkal kommunikálnak</li><li>A kiszolgáló-alapú alkalmazások kommunikálnak a webes API-kkal</li><li>A webes API-k webes API-kkal kommunikálnak (esetenként a felhasználók nevében)</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>Az Identity Server-jogkivonat alapértelmezett gyorsítótárának felülbírálása skálázható alternatív megoldással

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Identity Server |
| **SDL-fázis**               | Üzembe helyezés |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Identity Server-telepítés – gyorsítótárazás](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Lépések** | <p>A IdentityServer egy egyszerű beépített memóriabeli gyorsítótárral rendelkezik. Habár ez jó megoldás a kis méretű natív alkalmazások esetében, a közepes szintű és a háttérbeli alkalmazások esetében nem méretezhető a következő okok miatt:</p><ul><li>Ezeket az alkalmazásokat számos felhasználó egyszerre éri el. Az azonos tárolóban lévő összes hozzáférési jogkivonat mentése elszigetelési problémákat okoz, és kihívásokat jelent, ha a skálán működik: sok felhasználó, akik a legtöbb tokent használják, mint az alkalmazás által a nevükben elérhető erőforrások, nagy mennyiségű és nagyon költséges keresési műveletet jelenthetnek.</li><li>Ezek az alkalmazások jellemzően elosztott topológiákban vannak telepítve, ahol több csomópontnak ugyanahhoz a gyorsítótárhoz kell hozzáférnie.</li><li>A gyorsítótárazott tokeneknek meg kell túlélni a folyamat újrahasznosítását és inaktiválását</li><li>A fenti okok miatt a webalkalmazások megvalósítása során ajánlott felülbírálni az alapértelmezett Identity Server jogkivonat-gyorsítótárát egy skálázható alternatíva, például az Azure cache for Redis</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>Győződjön meg arról, hogy a telepített alkalmazás bináris fájljai digitálisan vannak aláírva

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Számítógép-megbízhatósági kapcsolat határa |
| **SDL-fázis**               | Üzembe helyezés |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Győződjön meg arról, hogy a telepített alkalmazás bináris fájljai digitálisan vannak aláírva, hogy a bináris fájlok integritása ellenőrizhető legyen|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>Hitelesítés engedélyezése az MSMQ-várólistákhoz való csatlakozáskor a WCF-ben

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános, NET-keretrendszer 3 |
| **Attribútumok**              | N/A |
| **Hivatkozások**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)) |
| **Lépések** | A program nem tudja engedélyezni a hitelesítést az MSMQ-várólistákhoz való csatlakozáskor, a támadók névtelenül küldhetnek üzeneteket a várólistába feldolgozásra. Ha a hitelesítés nem használható olyan MSMQ-várólistához való csatlakozáshoz, amelyet egy másik programba való üzenet kézbesítésére használ, a támadó rosszindulatú üzenetet küldhet.|

### <a name="example"></a>Példa
Az `<netMsmqBinding/>` alábbi WCF-konfigurációs fájl eleme arra utasítja a WCF-t, hogy tiltsa le a hitelesítést, amikor MSMQ-várólistához csatlakozik az üzenetek kézbesítéséhez.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Konfigurálja az MSMQ-t úgy, hogy minden bejövő vagy kimenő üzenet esetében a Windows-tartomány vagy a tanúsítvány hitelesítését mindig megkövetelje.

### <a name="example"></a>Példa
Az `<netMsmqBinding/>` alábbi WCF konfigurációs fájl eleme arra utasítja a WCF-t, hogy engedélyezze a tanúsítvány-hitelesítést egy MSMQ-várólistához való csatlakozáskor. Az ügyfél X. 509 tanúsítvánnyal lett hitelesítve. Az ügyféltanúsítványt a-kiszolgáló tanúsítványtárolójában kell megjelenni.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF – ne állítsa be az üzenet clientCredentialType nincs értékre

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | Ügyfél hitelesítő adatainak típusa – none |
| **Hivatkozások**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [megerősítés](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Lépések** | A hitelesítés hiánya azt jelenti, hogy mindenki hozzáférhet a szolgáltatáshoz. Egy olyan szolgáltatás, amely nem hitelesíti az ügyfeleit, hozzáférést biztosít az összes felhasználóhoz. Konfigurálja úgy az alkalmazást, hogy az ügyfél hitelesítő adataival hitelesítse magát. Ezt úgy teheti meg, hogy az üzenetet Windows vagy tanúsítvány clientCredentialType állítja be. |

### <a name="example"></a>Példa
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF – ne állítson be átviteli clientCredentialType nincs értékre

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános, .NET-keretrendszer 3 |
| **Attribútumok**              | Ügyfél hitelesítő adatainak típusa – none |
| **Hivatkozások**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [megerősítés](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Lépések** | A hitelesítés hiánya azt jelenti, hogy mindenki hozzáférhet a szolgáltatáshoz. Egy szolgáltatás, amely nem hitelesíti az ügyfeleit, lehetővé teszi az összes felhasználó számára a funkciók elérését. Konfigurálja úgy az alkalmazást, hogy az ügyfél hitelesítő adataival hitelesítse magát. Ezt úgy teheti meg, hogy a Windows vagy a tanúsítvány átviteli clientCredentialType állítja be. |

### <a name="example"></a>Példa
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>Győződjön meg arról, hogy a webes API-k biztonságossá tétele a szabványos hitelesítési módszerekkel történik

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Hitelesítés és engedélyezés a ASP.net web API-ban](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [külső hitelesítési szolgáltatások a ASP.net web API-val (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Lépések** | <p>A hitelesítés az a folyamat, amelyben az entitás igazolja az identitását, jellemzően hitelesítő adatokkal, például felhasználónévvel és jelszóval. Több hitelesítési protokoll is rendelkezésre áll, amelyek megtekinthetők. Némelyikük alább látható:</p><ul><li>Ügyféltanúsítványok</li><li>Windows-alapú</li><li>Űrlapok alapján</li><li>Összevonás – ADFS</li><li>Összevonás – Azure AD</li><li>Összevonási-identitás kiszolgálója</li></ul><p>A hivatkozások szakaszban található hivatkozások alacsony szintű részleteket biztosítanak arról, hogy az egyes hitelesítési sémák hogyan valósíthatók meg a webes API-k biztonságossá tételéhez.</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>A Azure Active Directory által támogatott szabványos hitelesítési forgatókönyvek használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Hitelesítési forgatókönyvek az Azure ad-hez](../../active-directory/develop/authentication-vs-authorization.md), [Azure Active Directory mintakód](../../active-directory/azuread-dev/sample-v1-code.md), [Azure Active Directory fejlesztői útmutató](../../active-directory/develop/index.yml) |
| **Lépések** | <p>Azure Active Directory (Azure AD) leegyszerűsíti a fejlesztők hitelesítését szolgáltatásként, az iparági szabványnak megfelelő protokollok, például a OAuth 2,0 és az OpenID Connect támogatásával. Az alábbi öt elsődleges alkalmazási forgatókönyv az Azure AD által támogatott:</p><ul><li>Webböngésző webes alkalmazásba: A felhasználónak be kell jelentkeznie egy Azure AD által védett webalkalmazásba</li><li>Egyoldalas alkalmazás (SPA): A felhasználónak be kell jelentkeznie egy egyoldalas alkalmazásba, amelyet az Azure AD biztosít</li><li>Natív alkalmazás webes API-hoz: egy telefonon, táblaszámítógépen vagy számítógépen futó natív alkalmazásnak hitelesítenie kell egy felhasználót az Azure AD által védett webes API erőforrásainak lekéréséhez.</li><li>Webalkalmazás webes API-hoz: egy webalkalmazásnak az Azure AD által védett webes API-val kell lekérnie az erőforrásokat</li><li>Daemon vagy Server Application to web API: egy olyan démon alkalmazás vagy kiszolgálóalkalmazás, amely nem rendelkezik webes felhasználói felülettel, az Azure AD által védett webes API-k erőforrásainak lekérése szükséges.</li></ul><p>Az alacsony szintű megvalósítás részleteiért tekintse meg a hivatkozások szakaszban található hivatkozásokat</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>Az alapértelmezett ADAL-jogkivonat gyorsítótárának felülbírálása méretezhető alternatív megoldással

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Modern hitelesítés Azure Active Directory webalkalmazásokhoz](/archive/blogs/microsoft_press/new-book-modern-authentication-with-azure-active-directory-for-web-applications), [Redis használata ADAL jogkivonat-gyorsítótárként](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Lépések** | <p>A ADAL (Active Directory-hitelesítési tár) által használt alapértelmezett gyorsítótár a memóriabeli gyorsítótár, amely egy statikus tárolóra támaszkodik, amely elérhető a teljes folyamatra. Habár ez a natív alkalmazások esetében is működik, a közepes szintű és háttérbeli alkalmazások esetében nem méretezhető a következő okok miatt:</p><ul><li>Ezeket az alkalmazásokat számos felhasználó egyszerre éri el. Az azonos tárolóban lévő összes hozzáférési jogkivonat mentése elszigetelési problémákat okoz, és kihívásokat jelent, ha a skálán működik: sok felhasználó, akik a legtöbb tokent használják, mint az alkalmazás által a nevükben elérhető erőforrások, nagy mennyiségű és nagyon költséges keresési műveletet jelenthetnek.</li><li>Ezek az alkalmazások jellemzően elosztott topológiákban vannak telepítve, ahol több csomópontnak ugyanahhoz a gyorsítótárhoz kell hozzáférnie.</li><li>A gyorsítótárazott tokeneknek meg kell túlélni a folyamat újrahasznosítását és inaktiválását</li></ul><p>A fenti okok miatt a webalkalmazások megvalósítása során ajánlott felülbírálni az alapértelmezett ADAL-jogkivonat gyorsítótárát egy skálázható alternatív megoldással, például az Azure cache for Redis szolgáltatással.</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>Győződjön meg arról, hogy a TokenReplayCache a ADAL-hitelesítési tokenek visszajátszásának megakadályozására szolgál

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Modern hitelesítés Azure Active Directory webalkalmazásokhoz](/archive/blogs/microsoft_press/new-book-modern-authentication-with-azure-active-directory-for-web-applications) |
| **Lépések** | <p>A TokenReplayCache tulajdonság lehetővé teszi a fejlesztők számára a jogkivonat-Visszajátszási gyorsítótár meghatározását, amely egy olyan tároló, amely a jogkivonatok mentésére használható annak ellenőrzése céljából, hogy a jogkivonat nem használható többször.</p><p>Ez egy közös támadáson alapuló mérték, a "jogkivonat-Visszajátszási támadás": a bejelentkezéskor küldött tokent elfogó támadó megpróbálhatja elküldeni az alkalmazásba ("Replay") egy új munkamenet létrehozásához. Például a sikeres felhasználói hitelesítés után a OIDC-engedélyezési folyamat során a függő entitás "/signin-oidc" végpontjának kérése "id_token", "code" és "State" paraméterekkel történik.</p><p>A függő entitás érvényesíti ezt a kérést, és új munkamenetet hoz létre. Ha egy támadó rögzíti ezt a kérést, és visszajátssza, akkor sikeres munkamenetet hozhat létre, és meghamisíthatja a felhasználót. Az Egypéldányos kapcsolat megléte az OpenID Connectben korlátozható, de nem teljes mértékben kizárja azokat a körülményeket, amelyekben a támadás sikeresen elvégezhető. Az alkalmazások megvédése érdekében a fejlesztők a ITokenReplayCache megvalósítását és a TokenReplayCache-példányok hozzárendelését is lehetővé teszik.</p>|

### <a name="example"></a>Példa
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Példa
Íme egy példa a ITokenReplayCache felület megvalósítására. (A projekt-specifikus gyorsítótárazási keretrendszer testreszabása és implementálása)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
A megvalósított gyorsítótárat a következőképpen kell hivatkozni a OIDC-beállításokban a "TokenValidationParameters" tulajdonságon keresztül.
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Vegye figyelembe, hogy a konfiguráció hatékonyságának teszteléséhez jelentkezzen be a helyi OIDC-védelemmel ellátott alkalmazásba, és rögzítse a kérést a `"/signin-oidc"` Hegedűs végpontjának. Ha a védelem nincs érvényben, a Hegedűs új munkamenet-cookie-t állít vissza. Ha a kérést a TokenReplayCache-védelem hozzáadása után is visszajátsszák, az alkalmazás kivételt jelez az alábbiak szerint: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>ADAL-kódtárak használata a OAuth2-ügyfelektől a HRE (vagy a helyszíni AD-hez) való jogkivonat-kérelmek kezeléséhez

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure AD |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [ADAL](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) |
| **Lépések** | <p>Az Azure AD Authentication Library (ADAL) lehetővé teszi az ügyfélalkalmazások fejlesztői számára, hogy egyszerűen hitelesítsék a felhasználókat a felhőben vagy a helyszíni Active Directoryban (AD), majd hozzáférési jogkivonatokat kapjanak az API-hívások biztonságossá tételéhez.</p><p>A ADAL számos funkcióval rendelkezik, amelyek megkönnyítik a hitelesítést a fejlesztők számára, például az aszinkron támogatást, a hozzáférési jogkivonatokat tároló konfigurálható jogkivonat-gyorsítótárat és a tokenek frissítési jogkivonatait, az automatikus jogkivonat-frissítést, ha a hozzáférési jogkivonat lejár, és a frissítési jogkivonat elérhető, és így tovább.</p><p>A ADAL a legtöbb összetettségtel segíti a fejlesztőket az alkalmazás üzleti logikájának, valamint az erőforrások egyszerű biztonsága nélkül. Külön könyvtárak érhetők el a .NET, a JavaScript (Client és Node.js), a Python, az iOS, az Android és a Java rendszerekhez.</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>A Field Gatewayhez csatlakozó eszközök hitelesítése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-mező átjárója |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Győződjön meg arról, hogy minden eszközt a Field Gateway hitelesít, mielőtt az adatokat befogadja tőlük, és még a Felhőbeli átjáróval való felsőbb szintű kommunikáció megkönnyítése előtt. Győződjön meg arról is, hogy az eszközök eszközönkénti hitelesítő adatokkal kapcsolódnak, hogy az egyes eszközök egyedi azonosításra alkalmasak legyenek.|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>Győződjön meg arról, hogy a felhőalapú átjáróhoz csatlakozó eszközök hitelesítése megtörtént

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános, C#, Node.JS,  |
| **Attribútumok**              | N/A, átjáró választása – Azure IoT Hub |
| **Hivatkozások**              | N/A, [Azure IoT hub .net](../../iot-hub/quickstart-send-telemetry-dotnet.md) [-tel](../../iot-hub/iot-hub-devguide-security.md), [első lépések az IOT hub és a Node js](../../iot-hub/quickstart-send-telemetry-node.md)használatával [](https://github.com/Azure/azure-iot-sdks/) |
| **Lépések** | <ul><li>**Általános:** Az eszköz hitelesítése Transport Layer Security (TLS) vagy IPSec használatával. Az infrastruktúrának támogatnia kell az előmegosztott kulcs (PSK) használatát azokon az eszközökön, amelyek nem képesek a teljes aszimmetrikus titkosítás kezelésére. Használja ki az Azure AD-t, a OAuth-t.</li><li>**C#:** DeviceClient-példány létrehozásakor alapértelmezés szerint a Create metódus létrehoz egy DeviceClient-példányt, amely a AMQP protokollt használja a IoT Hub való kommunikációhoz. A HTTPS protokoll használatához használja a Create metódus felülírását, amely lehetővé teszi a protokoll megadását. Ha a HTTPS protokollt használja, a NuGet csomagot is hozzá kell adnia a `Microsoft.AspNet.WebApi.Client` projekthez, hogy tartalmazza a `System.Net.Http.Formatting` névteret.</li></ul>|

### <a name="example"></a>Példa
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Példa
**Node.JS: hitelesítés**
#### <a name="symmetric-key"></a>Szimmetrikus kulcs
* IoT hub létrehozása az Azure-ban
* Bejegyzés létrehozása az eszköz identitásának beállításjegyzékében
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Szimulált eszköz létrehozása
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
  #### <a name="sas-token"></a>SAS-token
* Belsőleg generált a szimmetrikus kulcs használatakor, de explicit módon is létrehozható és használható.
* Protokoll definiálása: `var Http = require('azure-iot-device-http').Http;`
* Sas-token létrehozása:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
  + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* Kapcsolat sas-token használatával:
    ```javascript
    Client.fromSharedAccessSignature(sas, Http);
    ```
  #### <a name="certificates"></a>Tanúsítványok
* Önaláírt X509-tanúsítvány létrehozása bármilyen eszközzel, például OpenSSL használatával a tanúsítvány és a kulcs tárolására szolgáló. CERT és. Key fájlok létrehozásához.
* Olyan eszköz kiépítése, amely a tanúsítványokkal biztonságos kapcsolatokat fogad el.
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Eszköz csatlakoztatása tanúsítvány használatával
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    };
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>Eszközönkénti hitelesítő adatok használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway  |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Átjáró választása – Azure IoT Hub |
| **Hivatkozások**              | [Azure IoT Hub biztonsági tokenek](../../iot-hub/iot-hub-devguide-security.md) |
| **Lépések** | Használjon eszközönkénti hitelesítő adatokat SaS-jogkivonatok használatával az eszköz kulcs vagy ügyféltanúsítvány alapján, IoT Hub szintű közös hozzáférésű házirendek helyett. Ez megakadályozza, hogy egy eszköz vagy egy mező átjárójának hitelesítési jogkivonatait egy másik |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>Győződjön meg arról, hogy csak a szükséges tárolók és Blobok névtelen olvasási hozzáférést kapnak

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | StorageType – blob |
| **Hivatkozások**              | A [tárolók és Blobok névtelen olvasási hozzáférésének kezelése](../../storage/blobs/anonymous-read-access-configure.md), [közös hozzáférési aláírások, 1. rész: az SAS-modell megismerése](../../storage/common/storage-sas-overview.md) |
| **Lépések** | <p>Alapértelmezés szerint a tárolót és a benne található blobokat csak a Storage-fiók tulajdonosa érheti el. Ahhoz, hogy a névtelen felhasználók olvasási engedélyt adjanak a tárolók és a Blobok számára, az egyik megadhatja a tároló engedélyeit a nyilvános hozzáférés engedélyezéséhez. A névtelen felhasználók a kérelem hitelesítése nélkül is elolvashatják a blobokat egy nyilvánosan elérhető tárolón belül.</p><p>A tárolók a következő beállításokat biztosítják a tárolók hozzáférésének kezeléséhez:</p><ul><li>Teljes nyilvános olvasási hozzáférés: a tároló és a blob-adat névtelen kéréssel olvasható. Az ügyfelek névtelen kéréssel enumerálják a tárolóban lévő blobokat, de nem tudják enumerálni a tároló fiókjában lévő tárolókat.</li><li>Nyilvános olvasási hozzáférés csak blobokhoz: a tárolóban található blob-információk névtelen kéréssel olvashatók, de a tárolók nem érhetők el. Az ügyfelek névtelen kéréssel nem tudják enumerálni a tárolóban lévő blobokat</li><li>Nincs nyilvános olvasási hozzáférés: a tároló-és a blob-adatkészleteket csak a fiók tulajdonosa tudja beolvasni</li></ul><p>A névtelen hozzáférés a legjobb olyan helyzetekben, amikor bizonyos bloboknak mindig elérhetőnek kell lenniük a névtelen olvasási hozzáféréshez. A részletes szabályozáshoz létrehozhat egy közös hozzáférési aláírást, amely lehetővé teszi a korlátozott hozzáférés delegálását a különböző engedélyekkel és a megadott időintervallumon belül. Győződjön meg arról, hogy a tárolók és Blobok, amelyek esetleg bizalmas adatokat is tartalmazhatnak, nem kapnak véletlenül névtelen hozzáférést</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>Korlátozott hozzáférés biztosítása az Azure Storage-beli objektumokhoz SAS vagy SAP használatával

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage |
| **SDL-fázis**               | Létrehozás |
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A |
| **Hivatkozások**              | [Közös hozzáférésű aláírások, 1. rész: az SAS-modell, a](../../storage/common/storage-sas-overview.md) [közös hozzáférési aláírások ismertetése, 2. rész: sas létrehozása és használata blob Storage](../../storage/common/storage-sas-overview.md)-mel, a [fiókban lévő objektumokhoz való hozzáférés delegálása közös hozzáférési aláírások és tárolt hozzáférési szabályzatok használatával](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Lépések** | <p>A közös hozzáférésű aláírások (SAS) használatával a Storage-fiókban lévő objektumok számára korlátozott hozzáférést biztosíthat a többi ügyfélnek, anélkül, hogy a fiók elérési kulcsát kellene kitennie. Az SAS egy URI, amely magában foglalja a lekérdezési paramétereit a tárolási erőforrásokhoz való hitelesített hozzáféréshez szükséges összes információval. A tároló erőforrásainak SAS-vel való eléréséhez az ügyfélnek csak az SAS-t kell átadnia a megfelelő konstruktorba vagy metódusba.</p><p>Ha olyan ügyfél számára kíván hozzáférést biztosítani a Storage-fiók erőforrásaihoz, amely nem megbízható a fiók kulcsával, akkor használhat SAS-t. A Storage-fiók kulcsa egy elsődleges és egy másodlagos kulcsot is tartalmaz, amelyek mindegyike rendszergazdai hozzáférést biztosít a fiókjához és a benne lévő összes erőforráshoz. A fiók kulcsainak bármelyike megnyithatja a fiókját a kártékony vagy gondatlan használat lehetőségével. A közös hozzáférésű aláírások biztonságos alternatívát biztosítanak, amely lehetővé teszi más ügyfelek számára, hogy a megadott engedélyek alapján a Storage-fiókban tárolt adatait olvassák, írják és töröljék, és a fiók kulcsa nélkül legyenek szükségesek.</p><p>Ha van olyan logikai készlete, amely minden alkalommal hasonló, a tárolt hozzáférési szabályzat (SAP) használatával jobb ötlet. Mivel a tárolt hozzáférési házirendből származtatott SAS-t használva azonnal visszavonhatja az SAS-t, az ajánlott eljárás, hogy mindig a tárolt hozzáférési szabályzatokat használja, ha lehetséges.</p>|