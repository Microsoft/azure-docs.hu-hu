---
title: Active Directory hitelesítés – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ismerje meg a Azure Database for PostgreSQL-Single Serverrel való hitelesítéshez Azure Active Directory fogalmakat
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 0a19bd9d1547c16937ee575c08ea15a52589ccd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87171048"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Az Azure Active Directory használata a PostgreSQL-lel történő hitelesítéshez

A Microsoft Azure Active Directory (Azure AD) hitelesítés az Azure AD-ben meghatározott identitásokkal való Azure Database for PostgreSQL csatlakozáshoz használt mechanizmus.
Az Azure AD-hitelesítéssel egy központi helyen kezelheti az adatbázis-felhasználói identitásokat és más Microsoft-szolgáltatásokat, ami leegyszerűsíti az engedélyek kezelését.

Az Azure AD használatának előnyei a következők:

- A felhasználók hitelesítése egységes módon az Azure-szolgáltatásokon keresztül
- A jelszóházirend és a jelszó-elforgatás egyetlen helyen való kezelése
- A Azure Active Directory által támogatott több hitelesítési módszer, amely miatt nem szükséges a jelszavak tárolása.
- Az ügyfelek külső (Azure AD-) csoportok használatával kezelhetik az adatbázis-engedélyeket.
- Az Azure AD-hitelesítés PostgreSQL adatbázis-szerepkörök használatával hitelesíti az identitásokat az adatbázis szintjén
- Jogkivonat-alapú hitelesítés támogatása Azure Database for PostgreSQLhoz csatlakozó alkalmazások számára

Azure Active Directory hitelesítés konfigurálásához és használatához használja a következő folyamatot:

1. Szükség szerint hozza létre és töltse fel Azure Active Directory felhasználói identitásokkal.
2. Szükség esetén társíthatja vagy módosíthatja az Azure-előfizetéshez jelenleg társított Active Directory.
3. Hozzon létre egy Azure AD-rendszergazdát a Azure Database for PostgreSQL-kiszolgálóhoz.
4. Hozzon létre adatbázis-felhasználókat az adatbázisban az Azure AD-identitásokhoz rendelve.
5. Kapcsolódjon az adatbázishoz az Azure AD-identitás jogkivonatának beolvasásával és a bejelentkezéshez.

> [!NOTE]
> Ha szeretné megtudni, hogyan hozhat létre és tölthet fel Azure AD-t, majd hogyan konfigurálhatja az Azure AD-t a Azure Database for PostgreSQLsal, tekintse meg az Azure ad- [vel való konfigurálást és bejelentkezést Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md)

## <a name="architecture"></a>Architektúra

A következő magas szintű diagram összefoglalja, hogyan működik a hitelesítés az Azure AD-hitelesítéssel Azure Database for PostgreSQL használatával. A nyilak a kommunikációs útvonalakat jelzik.

![hitelesítési folyamat][1]

## <a name="administrator-structure"></a>Rendszergazdai struktúra

Az Azure AD-hitelesítés használatakor két rendszergazdai fiók van a PostgreSQL-kiszolgálóhoz; az eredeti PostgreSQL-rendszergazda és az Azure AD-rendszergazda. Csak az Azure AD-fiókon alapuló rendszergazda hozhatja létre az első Azure AD-beli adatbázis-felhasználót egy felhasználói adatbázisban. Az Azure AD rendszergazdai bejelentkezés egy Azure AD-felhasználó vagy egy Azure AD-csoport lehet. Ha a rendszergazda egy csoportfiók, bármely csoporttag használhatja, amely lehetővé teszi több Azure AD-rendszergazda használatát a PostgreSQL-kiszolgálóhoz. A csoportfiókok rendszergazdaként való használata javítja a kezelhetőséget azáltal, hogy lehetővé teszi a csoporttagok központi hozzáadását és eltávolítását az Azure AD-ben a PostgreSQL-kiszolgálón lévő felhasználók vagy engedélyek módosítása nélkül. Egyszerre csak egy Azure AD-rendszergazda (felhasználó vagy csoport) konfigurálható.

![felügyeleti struktúra][2]

## <a name="permissions"></a>Engedélyek

Az Azure AD-vel hitelesíteni képes új felhasználók létrehozásához a `azure_ad_admin` szerepkört az adatbázisban kell megadni. Ezt a szerepkört egy adott Azure Database for PostgreSQL-kiszolgáló Azure AD-rendszergazdai fiókjának konfigurálásával rendeli hozzá a rendszer.

Új Azure AD-adatbázis felhasználójának létrehozásához az Azure AD-rendszergazdaként kell csatlakoznia. Ez a [Azure Database for PostgreSQL Azure ad-vel való konfigurálásának és bejelentkezésének](howto-configure-sign-in-aad-authentication.md)bemutatása.

Az Azure AD-hitelesítés csak akkor lehetséges, ha az Azure AD-rendszergazda Azure Database for PostgreSQLhoz lett létrehozva. Ha a Azure Active Directory-rendszergazda el lett távolítva a kiszolgálóról, a korábban létrehozott Azure Active Directory felhasználók már nem tudnak csatlakozni az adatbázishoz a Azure Active Directory hitelesítő adataik használatával.

## <a name="connecting-using-azure-ad-identities"></a>Csatlakozás az Azure AD-identitások használatával

Azure Active Directory hitelesítés a következő módszereket támogatja az adatbázishoz való kapcsolódáshoz az Azure AD-identitások használatával:

- Azure Active Directory jelszó
- Integrált Azure Active Directory
- Univerzális Azure Active Directory MFA-val
- Active Directory alkalmazás-tanúsítványok vagy ügyfél-titkos kulcsok használata
- [Felügyelt identitás](howto-connect-with-managed-identity.md)

Miután hitelesítette a Active Directory, lekérheti a tokent. Ez a jogkivonat a bejelentkezéshez használt jelszó.

Vegye figyelembe, hogy a felügyeleti műveletek, például az új felhasználók hozzáadása csak az Azure AD-beli felhasználói szerepkörök esetében támogatottak.

> [!NOTE]
> A Active Directory-tokenekkel való kapcsolódással kapcsolatos további információkért lásd: [Konfigurálás és bejelentkezés az Azure ad-vel a Azure Database for PostgreSQLhoz](howto-configure-sign-in-aad-authentication.md).

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

- A kezelhetőség növelése érdekében javasoljuk, hogy hozzon létre egy dedikált Azure AD-csoportot rendszergazdaként.
- A Azure Database for PostgreSQL kiszolgálókhoz egyszerre csak egy Azure AD-rendszergazda (felhasználó vagy csoport) konfigurálható.
- Csak a PostgreSQL-hez készült Azure AD-rendszergazda csatlakozhat a Azure Database for PostgreSQL Azure Active Directory-fiókkal. A Active Directory rendszergazda konfigurálhatja a következő Azure AD-adatbázis felhasználóit.
- Ha töröl egy felhasználót az Azure AD-ből, a felhasználó nem fogja tudni hitelesíteni az Azure AD-t, így az adott felhasználó hozzáférési jogkivonata már nem lesz lehetséges. Ebben az esetben, bár a megfeleltetési szerepkör továbbra is az adatbázisban marad, nem lehet csatlakozni a kiszolgálóhoz ehhez a szerepkörhöz.
> [!NOTE]
> A törölt Azure AD-felhasználóval való bejelentkezés továbbra is végrehajtható, amíg a jogkivonat lejár (akár 60 percet a jogkivonat-kiállítótól számítva).  Ha a felhasználót a Azure Database for PostgreSQL is eltávolítja, a hozzáférés azonnal visszavonásra kerül.
- Ha az Azure AD-rendszergazda el lett távolítva a kiszolgálóról, a kiszolgáló már nem lesz társítva az Azure AD-bérlőhöz, ezért az összes Azure AD-bejelentkezés le lesz tiltva a kiszolgálón. Ha új Azure AD-rendszergazdát ad hozzá ugyanahhoz a bérlőhöz, az Azure AD-bejelentkezések ismét engedélyezve lesznek.
- A Azure Database for PostgreSQL a felhasználó egyedi Azure AD-felhasználói AZONOSÍTÓjának használatával egyezteti a hozzáférési jogkivonatokat a Azure Database for PostgreSQL szerepkörhöz, a Felhasználónév használata helyett. Ez azt jelenti, hogy ha egy Azure AD-felhasználót törölnek az Azure AD-ben, és egy azonos nevű új felhasználó lett létrehozva, Azure Database for PostgreSQL úgy véli, hogy egy másik felhasználó. Ezért ha egy felhasználó törlődik az Azure AD-ből, és egy új, azonos nevű felhasználó lett hozzáadva, az új felhasználó nem fog tudni csatlakozni a meglévő szerepkörhöz. Ennek engedélyezéséhez a Azure Database for PostgreSQL Azure AD-rendszergazdának vissza kell vonnia a felhasználót, majd a "azure_ad_user" szerepkört kell megadnia a felhasználónak az Azure AD felhasználói AZONOSÍTÓjának frissítéséhez.

## <a name="next-steps"></a>Következő lépések

- Ha szeretné megtudni, hogyan hozhat létre és tölthet fel Azure AD-t, majd hogyan konfigurálhatja az Azure AD-t a Azure Database for PostgreSQLsal, tekintse meg az Azure ad- [vel való konfigurálást és bejelentkezést Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md)
- A bejelentkezések, a felhasználók és az adatbázis-szerepkörök Azure Database for PostgreSQL áttekintését lásd: [felhasználók létrehozása a Azure Database for PostgreSQL-Single Serverben](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/admin-structure.png
