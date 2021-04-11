---
title: A házirend-kulcsok áttekintése – Azure Active Directory B2C
description: Ismerje meg, hogy milyen típusú titkosítási szabályzatok használhatók a tokenek, az ügyfél titkos kulcsainak, a tanúsítványoknak és a jelszavaknak az aláírásához és ellenőrzéséhez Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37ff3300935e90304754b5f2b375df0adecdaefa
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256242"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>A Azure Active Directory B2C lévő szabályzati kulcsok áttekintése

A Azure Active Directory B2C (Azure AD B2C) házirend-kulcsok formájában tárolja a titkokat és a tanúsítványokat, hogy megbízhatóan hozzon létre megbízhatóságot az általa integrált szolgáltatásokkal. Ezek a megbízhatósági kapcsolatok a következőkből állnak:

- Külső identitásszolgáltatók
- Csatlakozás [REST API szolgáltatásokkal](restful-technical-profile.md)
- Jogkivonat-aláírás és-titkosítás

 Ez a cikk azt ismerteti, hogy mit kell tudnia a Azure AD B2C által használt szabályzati kulcsokról.

> [!NOTE]
> A házirend-kulcsok konfigurálása jelenleg csak az [Egyéni házirendekre](./user-flow-overview.md) korlátozódik.

A Azure Portal szolgáltatások közötti megbízhatósági kapcsolat létrehozásához szükséges titkokat és tanúsítványokat a **házirend-kulcsok** menü alatt állíthatja be. A kulcsok szimmetrikusak vagy aszimmetrikusak lehetnek. A *szimmetrikus* titkosítás vagy a titkos kulcs titkosítása, ahol egy közös titkot használ a titkosításhoz és az adatvisszafejtéshez. Az *aszimmetrikus* titkosítás vagy a nyilvános kulcsú titkosítás egy olyan titkosítási rendszer, amely kulcsokat használ, és amelyek a függő entitás alkalmazásával megosztott nyilvános kulcsokból és a kizárólag Azure ad B2C ismert titkos kulcsokból állnak.

## <a name="policy-keyset-and-keys"></a>Szabályzat kulcskészlet és kulcsok

A Azure AD B2C található házirend-kulcsok legfelső szintű erőforrása a **kulcskészlet** tároló. Mindegyik kulcskészlet legalább egy **kulcsot** tartalmaz. A kulcsnak a következő attribútumokkal kell rendelkezniük:

| Attribútum |  Kötelező | Megjegyzések |
| --- | --- |--- |
| `use` | Yes | Használat: a nyilvános kulcs rendeltetésszerű használatát azonosítja. Adattitkosítás `enc` vagy az aláírás ellenőrzése az adatain `sig` .|
| `nbf`| No | Aktiválás dátuma és időpontja. |
| `exp`| No | Lejárati dátum és idő. |

Javasoljuk, hogy a kulcs aktiválását és a lejárati értékeket a PKI-szabványoknak megfelelően állítsa be. Előfordulhat, hogy ezeket a tanúsítványokat időnként biztonsági vagy házirendi okokból kell elforgatnia. Előfordulhat például, hogy egy szabályzattal minden évben el szeretné forgatni a tanúsítványokat.

A kulcsok létrehozásához az alábbi módszerek közül választhat:

- **Manuális** – hozzon létre egy titkot a definiált karakterlánccal. A titok egy szimmetrikus kulcs. Beállíthatja az aktiválási és a lejárati dátumokat.
- **Generált** – a kulcs automatikus generálása. Beállíthatja az aktiválási és a lejárati dátumokat. Két lehetőség érhető el:
  - **Secret** – szimmetrikus kulcsot hoz létre.
  - **RSA** – létrehoz egy kulcspárt (aszimmetrikus kulcsokat).
- **Feltöltés** – töltsön fel egy tanúsítványt vagy egy PKCS12/pfx-profil-kulcsot. A tanúsítványnak tartalmaznia kell a titkos és a nyilvános kulcsokat (aszimmetrikus kulcsokat).

## <a name="key-rollover"></a>Kulcs rollover

Biztonsági okokból a Azure AD B2C rendszeres időközönként, vagy vészhelyzet esetén azonnal átadhatja a kulcsokat. Minden olyan alkalmazásra, identitás-szolgáltatóra vagy REST APIra, amely a Azure AD B2C integrálódik, elő kell készíteni a kulcs átváltási eseményének kezeléséhez, függetlenül attól, hogy milyen gyakran fordul elő. Ellenkező esetben, ha az alkalmazás vagy Azure AD B2C egy lejárt kulccsal próbálkozik egy titkosítási művelet elvégzéséhez, a bejelentkezési kérelem sikertelen lesz.

Ha egy Azure AD B2C kulcskészlet több kulccsal rendelkezik, akkor a következő feltételek alapján csak az egyik kulcs aktív egyszerre:

- A kulcs aktiválása az **aktiválási dátumon** alapul.
  - A kulcsok az aktiválási dátum szerint növekvő sorrendben vannak rendezve. A jövőbeli aktiválási dátummal rendelkező kulcsok lejjebb jelennek a listában. Az aktiválási dátum nélküli kulcsok a lista alján találhatók.
  - Ha az aktuális dátum és idő nagyobb, mint a kulcs aktiválási dátuma, Azure AD B2C aktiválja a kulcsot, és leállítja az előző aktív kulcs használatát.
- Ha az aktuális kulcs lejárati ideje eltelt, és a kulcstároló tartalmaz egy olyan új kulcsot, amely *nem a korábbi* és a *lejárati* idő alatt érvényes, akkor az új kulcs automatikusan aktívvá válik.
- Ha az aktuális kulcs lejárati ideje eltelt, és *a kulcstároló nem tartalmaz olyan* új kulcsot, amely nem rendelkezik a *korábbi* és a *lejárati* időpontok érvényességi idejével, Azure ad B2C nem fogja tudni használni a lejárt kulcsot. A Azure AD B2C egy hibaüzenetet jelenít meg az egyéni szabályzat függő összetevőjén belül. A probléma elkerüléséhez hozzon létre egy alapértelmezett kulcsot az aktiválás és a lejárat dátuma nélkül biztonsági hálóként.
- Az OpenId Connect jól ismert konfigurációs végpontjának kulcs végpontja (JWKS URI) a kulcstárolóban konfigurált kulcsokat tükrözi, ha a kulcsra a [JwtIssuer műszaki profiljában](./jwt-issuer-technical-profile.md)hivatkozunk. Egy OIDC-függvénytárat használó alkalmazás automatikusan beolvassa ezt a metaadatokat, így biztosítva, hogy a megfelelő kulcsokat használja a jogkivonatok érvényesítéséhez. További információ: a [Microsoft Authentication Library](../active-directory/develop/msal-b2c-overview.md)használata, amely mindig automatikusan beolvassa a legújabb jogkivonat-aláíró kulcsokat.

## <a name="policy-key-management"></a>Házirend-kulcs kezelése

A kulcstárolón belüli aktuális aktív kulcs lekéréséhez használja a Microsoft Graph API [getActiveKey](/graph/api/trustframeworkkeyset-getactivekey) -végpontot.

Aláírási és titkosítási kulcsok hozzáadása vagy törlése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Az Áttekintés lap **szabályzatok** területén válassza az **identitási élmény keretrendszere** elemet.
1. **Házirend-kulcsok** kiválasztása 
    1. Új kulcs hozzáadásához válassza a **Hozzáadás** lehetőséget.
    1. Új kulcs eltávolításához jelölje ki a kulcsot, majd válassza a **Törlés** lehetőséget. A kulcs törléséhez írja be a törlendő kulcstároló nevét. Azure AD B2C törli a kulcsot, és létrehozza a kulcs másolatát a. bak utótaggal.

### <a name="replace-a-key"></a>Kulcs cseréje

A kulcskészlet kulcsai nem helyezhetők át vagy nem cserélhetők le. Ha módosítania kell egy meglévő kulcsot:

- Azt javasoljuk, hogy adjon hozzá egy új kulcsot az **aktiválási dátumhoz** az aktuális dátumra és időpontra vonatkozóan. Azure AD B2C aktiválja az új kulcsot, és leállítja az előző aktív kulcs használatát.
- Azt is megteheti, hogy létrehoz egy új kulcskészlet-t a megfelelő kulcsokkal. Frissítse a szabályzatot az új kulcskészlet használatára, majd távolítsa el a régi kulcskészlet-t. 

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan használható a Microsoft Graph a [kulcskészlet](microsoft-graph-operations.md#trust-framework-policy-keyset) és a [házirend-kulcsok](microsoft-graph-operations.md#trust-framework-policy-key) üzembe helyezésének automatizálására.