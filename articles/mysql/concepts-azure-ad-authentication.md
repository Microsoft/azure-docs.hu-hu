---
title: Active Directory hitelesítés – Azure Database for MySQL
description: Ismerje meg a hitelesítési Azure Active Directory fogalmait Azure Database for MySQL
author: sunilagarwal
ms.author: sunila
ms.service: mysql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: e3afa7e7129c34aae8487affe979bd7704580a1e
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612449"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Az Azure Active Directory használata a MySQL-lel történő hitelesítéshez

A Microsoft Azure Active Directory (Azure AD) hitelesítés az Azure AD-ben meghatározott identitásokkal való Azure Database for MySQL csatlakozáshoz használt mechanizmus.
Az Azure AD-hitelesítéssel egy központi helyen kezelheti az adatbázis-felhasználói identitásokat és más Microsoft-szolgáltatásokat, ami leegyszerűsíti az engedélyek kezelését.

Az Azure AD használatának előnyei a következők:

- A felhasználók hitelesítése egységes módon az Azure-szolgáltatásokon keresztül
- A jelszóházirend és a jelszó-elforgatás egyetlen helyen való kezelése
- A Azure Active Directory által támogatott több hitelesítési módszer, amely miatt nem szükséges a jelszavak tárolása.
- Az ügyfelek külső (Azure AD-) csoportok használatával kezelhetik az adatbázis-engedélyeket.
- Az Azure AD-hitelesítés a MySQL-adatbázis felhasználóinak használatával hitelesíti az identitásokat az adatbázis szintjén
- Jogkivonat-alapú hitelesítés támogatása Azure Database for MySQLhoz csatlakozó alkalmazások számára

Azure Active Directory hitelesítés konfigurálásához és használatához használja a következő folyamatot:

1. Szükség szerint hozza létre és töltse fel Azure Active Directory felhasználói identitásokkal.
2. Szükség esetén társíthatja vagy módosíthatja az Azure-előfizetéshez jelenleg társított Active Directory.
3. Hozzon létre egy Azure AD-rendszergazdát a Azure Database for MySQL-kiszolgálóhoz.
4. Hozzon létre adatbázis-felhasználókat az adatbázisban az Azure AD-identitásokhoz rendelve.
5. Kapcsolódjon az adatbázishoz az Azure AD-identitás jogkivonatának beolvasásával és a bejelentkezéshez.

> [!NOTE]
> Ha szeretné megtudni, hogyan hozhat létre és tölthet fel Azure AD-t, majd hogyan konfigurálhatja az Azure AD-t a Azure Database for MySQLsal, tekintse meg az Azure ad- [vel való konfigurálást és bejelentkezést Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)

## <a name="architecture"></a>Architektúra

A következő magas szintű diagram összefoglalja, hogyan működik a hitelesítés az Azure AD-hitelesítéssel Azure Database for MySQL használatával. A nyilak a kommunikációs útvonalakat jelzik.

![hitelesítési folyamat][1]

## <a name="administrator-structure"></a>Rendszergazdai struktúra

Az Azure AD-hitelesítés használatakor a MySQL-kiszolgálónak két rendszergazdai fiókja van; az eredeti MySQL-rendszergazda és az Azure AD-rendszergazda. Csak az Azure AD-fiókon alapuló rendszergazda hozhatja létre az első Azure AD-beli adatbázis-felhasználót egy felhasználói adatbázisban. Az Azure AD rendszergazdai bejelentkezés egy Azure AD-felhasználó vagy egy Azure AD-csoport lehet. Ha a rendszergazda egy csoportfiók, bármely csoporttag használhatja, amely lehetővé teszi több Azure AD-rendszergazda használatát a MySQL-kiszolgálóhoz. Ha rendszergazdai jogosultságokkal látja el a csoport fiókját, a rendszergazdák központilag adhatják hozzá és távolíthatók el a csoporttagokat az Azure AD-ben anélkül, hogy módosítani kellene a felhasználókat vagy engedélyeket a MySQL-kiszolgálón. Egyszerre csak egy Azure AD-rendszergazda (felhasználó vagy csoport) konfigurálható.

![felügyeleti struktúra][2]

## <a name="permissions"></a>Engedélyek

Az Azure AD-vel hitelesíteni képes új felhasználók létrehozásához a tervezett Azure AD-rendszergazdának kell lennie. Ezt a felhasználót egy adott Azure Database for MySQL-kiszolgáló Azure AD-rendszergazdai fiókjának konfigurálásával rendeli hozzá.

Új Azure AD-adatbázis felhasználójának létrehozásához az Azure AD-rendszergazdaként kell csatlakoznia. Ez a [Azure Database for MySQL Azure ad-vel való konfigurálásának és bejelentkezésének](howto-configure-sign-in-azure-ad-authentication.md)bemutatása.

Az Azure AD-hitelesítés csak akkor lehetséges, ha az Azure AD-rendszergazda Azure Database for MySQLhoz lett létrehozva. Ha a Azure Active Directory-rendszergazda el lett távolítva a kiszolgálóról, a korábban létrehozott Azure Active Directory felhasználók már nem tudnak csatlakozni az adatbázishoz a Azure Active Directory hitelesítő adataik használatával.

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
> A Active Directory-tokenekkel való kapcsolódással kapcsolatos további információkért lásd: [Konfigurálás és bejelentkezés az Azure ad-vel a Azure Database for MySQLhoz](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

- Azure Active Directory hitelesítés csak a MySQL 5,7-es és újabb verzióiban érhető el.
- A Azure Database for MySQL kiszolgálókhoz egyszerre csak egy Azure AD-rendszergazda konfigurálható.
- Először csak a MySQL-hez készült Azure AD-rendszergazda csatlakozhat a Azure Database for MySQL egy Azure Active Directory-fiókkal. A Active Directory rendszergazda konfigurálhatja a következő Azure AD-adatbázis felhasználóit.
- Ha töröl egy felhasználót az Azure AD-ből, a felhasználó nem fogja tudni hitelesíteni az Azure AD-t, így az adott felhasználó hozzáférési jogkivonata már nem lesz lehetséges. Ebben az esetben, bár a megfelelő felhasználó továbbra is az adatbázisban marad, nem lehet csatlakozni a kiszolgálóhoz az adott felhasználóval.
> [!NOTE]
> A törölt Azure AD-felhasználóval való bejelentkezés továbbra is végrehajtható, amíg a jogkivonat lejár (akár 60 percet a jogkivonat-kiállítótól számítva).  Ha a felhasználót a Azure Database for MySQL is eltávolítja, a hozzáférés azonnal visszavonásra kerül.
- Ha az Azure AD-rendszergazda el lett távolítva a kiszolgálóról, a kiszolgáló már nem lesz társítva az Azure AD-bérlőhöz, ezért az összes Azure AD-bejelentkezés le lesz tiltva a kiszolgálón. Ha új Azure AD-rendszergazdát ad hozzá ugyanahhoz a bérlőhöz, az Azure AD-bejelentkezések ismét engedélyezve lesznek.
- A Azure Database for MySQL a felhasználó egyedi Azure AD-beli felhasználói AZONOSÍTÓjának használatával egyezteti a hozzáférési jogkivonatokat a Azure Database for MySQL felhasználó számára, a Felhasználónév használata helyett. Ez azt jelenti, hogy ha egy Azure AD-felhasználót törölnek az Azure AD-ben, és egy azonos nevű új felhasználó lett létrehozva, Azure Database for MySQL úgy véli, hogy egy másik felhasználó. Ezért ha egy felhasználó törlődik az Azure AD-ből, és egy új, azonos nevű felhasználó lett hozzáadva, az új felhasználó nem fog tudni csatlakozni a meglévő felhasználóhoz.

## <a name="next-steps"></a>Következő lépések

- Ha szeretné megtudni, hogyan hozhat létre és tölthet fel Azure AD-t, majd hogyan konfigurálhatja az Azure AD-t a Azure Database for MySQLsal, tekintse meg az Azure ad- [vel való konfigurálást és bejelentkezést Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)
- A bejelentkezések és az adatbázis-felhasználók Azure Database for MySQL való áttekintését lásd: [felhasználók létrehozása a Azure Database for MySQLban](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
