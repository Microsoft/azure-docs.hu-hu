---
title: Szabályzatkulcsok áttekintése – Azure Active Directory B2C
description: Megismerheti a titkosítási házirendkulcsok típusait, amelyek a Azure Active Directory B2C jogkivonatok, titkos ügyfélkulcsok, tanúsítványok és jelszavak aláírásához és érvényességének hitelesítéshez használhatók.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a41717e9be0918dead9f77a5f5472494d734b38a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717530"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>A szabályzatkulcsok áttekintése a Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) házirendkulcsok formájában tárolja a titkos kulcsokat és tanúsítványokat, hogy megbízható kapcsolatot létesítsen azokkal a szolgáltatásokkal, amelyekbe integrálva van. Ezek a megbízhatósági kapcsolat a következőkből áll:

- Külső identitásszolgáltatók
- Csatlakozás REST API [szolgáltatásokkal](restful-technical-profile.md)
- Jogkivonat aláírása és titkosítása

 Ez a cikk a szabályzatok által használt szabályzatkulcsokkal kapcsolatos szükséges Azure AD B2C.

> [!NOTE]
> A házirendkulcsok konfigurálása jelenleg csak egyéni [szabályzatok esetén érhető](./user-flow-overview.md) el.

Titkos kulcsokat és tanúsítványokat konfigurálhat a szolgáltatások közötti megbízhatóság létrehozásához Azure Portal szabályzatkulcsok **menüben.** A kulcsok szimmetrikusak vagy aszimmetrikusak is. *A szimmetrikus* titkosítás vagy titkos kulcsos titkosítás az a hely, ahol az adatok titkosítására és visszafejtéséhez közös titkos adatokat használnak. Az *aszimmetrikus* kriptográfia vagy nyilvános kulcsos titkosítás kulcspárokat használó titkosítási rendszer, amely a függő fél alkalmazásával megosztott nyilvános kulcsokból és a csak az által ismert titkos kulcsokból Azure AD B2C.

## <a name="policy-keyset-and-keys"></a>Házirendkulcsok és -kulcsok

A házirendkulcsok legfelső szintű erőforrása a Azure AD B2C **a Keyset tároló.** Minden kulcskészlet tartalmaz legalább egy **kulcsot.** A kulcs a következő attribútumokkal rendelkezik:

| Attribútum |  Kötelező | Megjegyzések |
| --- | --- |--- |
| `use` | Yes | Használat: Azonosítja a nyilvános kulcs kívánt használatát. Adatok `enc` titkosítása, vagy az adatok aláírásának `sig` ellenőrzése.|
| `nbf`| No | Aktiválás dátuma és időpontja. |
| `exp`| No | Lejárat dátuma és időpontja. |

Javasoljuk, hogy a kulcs aktiválását és lejárati értékét a PKI-szabványoknak megfelelően írja be. Biztonsági vagy házirendi okokból szükség lehet a tanúsítványok rendszeres időközönkénti váltogatása. Előfordulhat például, hogy van egy szabályzata, amely minden évben az összes tanúsítványt elforgatja.

Kulcs létrehozásához az alábbi módszerek egyikét választhatja:

- **Manuális** – Hozzon létre egy titkos titokban egy Ön által létrehozott sztringet. A titkos kulcs szimmetrikus kulcs. Beállíthatja az aktiválási és lejárati dátumokat.
- **Létrehozva** – Kulcs automatikus létrehozása. Beállíthatja az aktiválási és lejárati dátumokat. Két lehetőség érhető el:
  - **Titkos** kulcs – Szimmetrikus kulcsot hoz létre.
  - **RSA** – Létrehoz egy kulcspárt (aszimmetrikus kulcsokat).
- **Feltöltés** – Töltsön fel egy tanúsítványt vagy egy PKCS12-kulcsot. A tanúsítványnak tartalmaznia kell a titkos és a nyilvános kulcsokat (aszimmetrikus kulcsok).

## <a name="key-rollover"></a>Kulcsváltás

Biztonsági okokból a Azure AD B2C rendszeres időközönként, vagy vészhelyzet esetén azonnal átveheti a kulcsokat. A Azure AD B2C integrálható alkalmazásokat, identitásszolgáltatókat REST API vagy alkalmazásokat fel kell készíteni a kulcsváltási események kezelésére, függetlenül attól, hogy milyen gyakran fordulnak elő. Ellenkező esetben, ha az alkalmazás vagy Azure AD B2C megpróbál lejárt kulcsot használni egy titkosítási művelet végrehajtásához, a bejelentkezési kérelem sikertelen lesz.

Ha egy Azure AD B2C kulcskészlet több kulcsból áll, egyszerre csak az egyik kulcs aktív, az alábbi feltételek alapján:

- A kulcs aktiválása az aktiválás **dátumán alapul.**
  - A kulcsok az aktiválási dátum szerint növekvő sorrendbe vannak rendezve. Az aktiválási dátumokkal későbbi kulcsok a listában lejjebb jelennek meg. Az aktiválási dátum nélküli kulcsok a lista alján találhatók.
  - Ha az aktuális dátum és idő nagyobb, mint a kulcs aktiválási dátuma, a Azure AD B2C aktiválja a kulcsot, és leállítja az előző aktív kulcs használata.
- Ha az aktuális kulcs lejárati ideje lejárt, és a kulcstároló  egy új  kulcsot tartalmaz, amely érvényes nem korábban és lejárati idővel rendelkezik, az új kulcs automatikusan aktívvá válik.
- Ha az aktuális kulcs lejárati ideje lejárt, és a kulcstároló nem  tartalmaz olyan  új kulcsot, amely érvényes nem volt előtte és lejárati ideje is, a Azure AD B2C nem fogja tudni használni a lejárt kulcsot.  Azure AD B2C az egyéni szabályzat egy függő összetevőjére vonatkozó hibaüzenetet küld. A probléma elkerülése érdekében létrehozhat egy alapértelmezett kulcsot aktiválás és lejárati dátumok nélkül biztonsági hálóként.
- Az OpenId Connect jól ismert konfigurációs végpontjának kulcsvégpontja (JWKS URI) a kulcstárolóban konfigurált kulcsokat tükrözi, ha a kulcsra a [JwtIssuer](./jwt-issuer-technical-profile.md)technikai profil hivatkozik. Az OIDC-kódtárat használó alkalmazások automatikusan lekérik ezeket a metaadatokat, hogy a megfelelő kulcsokat használják-e a jogkivonatok érvényesítéséhez. További információért ismerje meg a [Microsoft Authentication Library](../active-directory/develop/msal-b2c-overview.md)használatát, amely mindig automatikusan lekéri a legújabb jogkivonat-aláíró kulcsokat.

## <a name="policy-key-management"></a>Házirendkulcs-kezelés

Az aktuális aktív kulcstárolón belüli lekért adatokat a Microsoft Graph API [getActiveKey végpont](/graph/api/trustframeworkkeyset-getactivekey) használatával.

Aláíró és titkosítási kulcsok hozzáadása vagy törlése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza a **Címtár és előfizetés** ikont a portál eszköztárán, majd válassza ki azt a címtárat, amely Azure AD B2C bérlőt.
1. A Azure Portal keresse meg és válassza **a** Azure AD B2C.
1. Az áttekintési lap Szabályzatok **területén válassza** **a** Identity Experience Framework.
1. **Szabályzatkulcsok kiválasztása** 
    1. Új kulcs hozzáadásához válassza a Hozzáadás **lehetőséget.**
    1. Új kulcs eltávolításához válassza ki a kulcsot, majd válassza a **Törlés lehetőséget.** A kulcs törléséhez írja be a törölni kívánt kulcstároló nevét. Azure AD B2C törli a kulcsot, és létrehoz egy másolatot a kulcsról a .bak utótaggal.

### <a name="replace-a-key"></a>Kulcs cseréje

A kulcskészletben található kulcsok nem cserélhetők és nem cserélhetők. Ha módosítania kell egy meglévő kulcsot:

- Javasoljuk, hogy új kulcsot ad hozzá az **aktiválási dátummal** az aktuális dátumra és időpontra beállítva. Azure AD B2C aktiválja az új kulcsot, és nem használja tovább az előző aktív kulcsot.
- Másik lehetőségként létrehozhat egy új kulcskészletet a megfelelő kulcsokkal. Frissítse a szabályzatot úgy, hogy az új kulcskészletet használja, majd távolítsa el a régi kulcskészletet. 

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan használhatja Microsoft Graph [kulcskészletek](microsoft-graph-operations.md#trust-framework-policy-keyset) és szabályzatkulcsok üzembe [helyezésének automatizálását.](microsoft-graph-operations.md#trust-framework-policy-key)

::: zone-end
