---
title: Az Azure Active Directory B2C-vel egyéni szabályzatok |} A Microsoft Docs
description: Ismerje meg az Azure Active Directory B2C-vel egyéni szabályzatok.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7921454cc9269278db58fcc50bc63ca49b41b1e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317428"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C-vel egyéni szabályzatok

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Egyéni szabályzatok olyan konfigurációs fájlokat, amelyek az Azure Active Directory (Azure AD) B2C-bérlő viselkedésének megadása. Felhasználói folyamatok identitás leggyakoribb feladatokat az Azure AD B2C-vel portál előre meghatározott. Egyéni szabályzatok teljes körűen szerkeszthetik-identitás fejlesztői számos különböző feladat végrehajtásához.

## <a name="comparing-user-flows-and-custom-policies"></a>Felhasználói folyamatok és az egyéni szabályzatok összehasonlítása

| | Felhasználói folyamatok | Egyéni szabályzatok |
|-|-------------------|-----------------|
| Felhasználói célcsoport számára | Minden alkalmazás vagy fejlesztők számára az identitás szakértelem nélkül is. | Identitáskezelő szakemberek használhatják, rendszerintegrátoroktól, tanácsadókkal és házon belüli identitás csapatok. Ezek nem okoz gondot az OpenIDConnect folyamatok és identitás-szolgáltatóktól és jogcímalapú hitelesítést. |
| Konfigurációs mód | Egy felhasználóbarát felhasználói felület (UI) az Azure Portalon. | XML-fájlok közvetlen módosítása, majd feltölti az Azure Portalra. |
| Felhasználói felület testreszabása | Teljes felhasználói felületének testreszabását, beleértve a HTML, CSS és JavaScript.<br><br>Egyéni karakterláncot tartalmazó többnyelvű támogatás. | Azonos |
| Attribútum testreszabása | Standard és egyéni attribútumait. | Azonos |
| Jogkivonat és munkamenet-kezelés | Egyéni jogkivonatot, és több munkamenet-beállításokkal. | Azonos |
| Identitásszolgáltatók | Előre definiált helyi vagy a közösségi szolgáltató és a legtöbb OIDC Identitásszolgáltatók, például az összevonás az Azure Active Directory-bérlők. | OIDC szabványokon alapuló, az OAUTH és a SAML.  Hitelesítési integrációs a REST API-k használatával is lehetőség. |
| Identitáskezelési tevékenységek | Regisztrálási vagy bejelentkezési helyi vagy számos közösségi fiókok.<br><br>Az önkiszolgáló jelszó-visszaállítás.<br><br>Profil szerkesztése.<br><br>Multi-Factor Authentication.<br><br>Testre szabhatja a biztonsági jogkivonat és munkamenet.<br><br>Hozzáférési jogkivonat folyamatokat. | Felhasználói folyamatok használatával egyéni Identitásszolgáltatók megegyező feladatok elvégzéséhez, vagy használjon egyéni hatókörök.<br><br>Építhet ki egy felhasználói fiókot egy másik rendszer regisztrációs időpontjában.<br><br>A saját e-mail-szolgáltató használatával üdvözlő e-mail küldése.<br><br>Ezen kívül az Azure AD B2C-vel a felhasználókhoz tartozó tárolóban.<br><br>Ellenőrizze a felhasználó által megadott megbízható rendszerrel információkat egy API-val. |

## <a name="policy-files"></a>A házirend-fájlok

Ezek a házirend-fájlok három típusú használhatók:

- **Alap fájl** – a legtöbb kapcsolatos definíciókat tartalmazza. Javasoljuk, hogy ehhez a fájlhoz, a hibaelhárítási és hosszú távú karbantartási szabályzat segítségével győződjön meg a minimálisan szükséges módosításokat.
- **Bővítmények fájl** – az egyedi konfigurációs módosítások tárolja a bérlő számára.
- **Függő entitás (RP) fájl** -feladat témájú az egyetlen fájl, amelyet közvetlenül az alkalmazás vagy szolgáltatás hív (egy függő entitás néven is ismert). Minden egyedi tevékenységhez szükséges a saját RP és függően márkajelzési követelmények, a szám előfordulhat, hogy "az alkalmazások x használati esetek száma összesen."

Az Azure AD B2C felhasználói folyamatok hajtsa végre a fenti kitaláltak három Fájlmintát, de a fejlesztői csak látja a helyreállítási pont Védettként fájlt, amíg az Azure Portalon módosítást hajt végre a háttérben a bővítmények fájl.

## <a name="custom-policy-core-concepts"></a>Egyéni szabályzat alapfogalmak

Az identitás- és hozzáférés kezelése (CIAM) ügyfélszolgálat az Azure-ban tartalmazza:

- Egy felhasználó könyvtárat, amely elérhető a Microsoft Graph használatával, és ez tartalmazza a felhasználói adatok helyi felhasználói fiókok és a összevont fiókokat.
- A hozzáférést a **identitás-kezelőfelületi keretrendszer** , hogy koordinálja a felhasználók és entitások közötti megbízhatósági kapcsolat, és átadja a jogcímeket közöttük hajtsa végre az identitás és hozzáférés-kezelési feladatot. 
- Biztonságijogkivonat-szolgáltatás (STS), amely azonosító jogkivonatok, frissítési jogkivonatok, és a hozzáférési jogkivonatok (és egyenértékű SAML helyességi feltételek) hibákat, és ellenőrzi azokat erőforrások védelmét.

Az Azure AD B2C-identitás feladat eléréséhez sorrendben kommunikál az identitás-szolgáltatóktól, a felhasználók, a más rendszerekkel és helyi felhasználói címtárhoz. Például bejelentkeztetni egy felhasználót, egy új felhasználó regisztrálása vagy jelszó alaphelyzetbe állítása. Az identitás-kezelőfelületi keretrendszer és a egy szabályzatot a (más néven felhasználói út vagy a bizalmi keretrendszer házirend) több résztvevős megbízhatósági kapcsolatot hoz létre, és explicit módon meghatározza az actors, a műveleteket, a protokollok és a feladatütemezési lépések végrehajtásához.

Az identitás-kezelőfelületi keretrendszer egy teljes mértékben konfigurálhatók, házirendekkel vezérelt, felhőalapú Azure-platform szabványos protokoll formátumokban entitások közötti megbízhatósági kapcsolat vezénylő például OpenIDConnect, OAuth, SAML, WSFed és néhány nem szabványos is, például REST API-alapú rendszer-a-rendszer jogcímek cseréje. A keretrendszer, amely támogatja a HTML és CSS felhasználóbarát, fehér címkével környezeteket hoz létre.

Az egyéni szabályzatok egy vagy több XML formátumú fájlként jelennek meg, egymásra hierarchikus sorrendben hivatkozva. XML-elemeket határozza meg, a jogcímek séma, a jogcímek átalakítása, tartalomdefiníciók, Jogcímszolgáltatók, technikai profilok és felhasználói interakciósorozat vezénylési lépésekből, egyéb elemek mellett. Egyéni szabályzat által az identitás-kezelőfelületi keretrendszer, ha egy függő entitás által végrehajtott egy vagy több XML-fájlok formájában érhető el. Egyéni házirendek konfigurálása a fejlesztők meg kell határoznia a megbízható kapcsolatok gondos részletes metaadatok végpontokat felvenni, a pontos jogcímek exchange-definíciók és igényei szerint egyes identitásszolgáltató konfigurálása a titkos kulcsok és tanúsítványok.

### <a name="inheritance-model"></a>Öröklés modell

Amikor egy alkalmazás meghívja a helyreállítási pont Védettként házirendfájlt, az Azure AD B2C az identitás-kezelőfelületi keretrendszer összes elemét hozzáadja, alap-fájlból, a bővítmények fájlból, majd a helyreállítási pont Védettként házirendfájl összegyűjtése, a jelenlegi házirend érvényben.  Azonos típusú és nevét az RP-fájl elemeinek felülírja a bővítmények és bővítmények felülbírálások talál.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md)
