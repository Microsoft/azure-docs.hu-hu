---
title: Az Azure AD többtényezős hitelesítésének adattárolása
description: Ismerje meg, hogy milyen személyes és szervezeti adatai vannak az Azure AD többtényezős hitelesítési tárolói az Ön és a felhasználók számára, és hogy milyen információk maradnak a származási országban vagy régióban.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7381ab62eb39c555c6b4eb34f150fc71bea1f10f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561464"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Az Azure AD többtényezős hitelesítésének adattárolási és ügyféladatok

A Azure Active Directory (Azure AD) a szervezet által a Microsoft Online szolgáltatásba, például Microsoft 365 vagy Azure-ba való feliratkozáskor a vállalat által megadott címen alapuló földrajzi helyen tárolja az ügyféladatokat. További információ a vásárlói adatok tárolásáról: [Hol találhatók az adatok?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) a Microsoft adatvédelmi központban.

A felhőalapú Azure AD többtényezős hitelesítés és az Azure többtényezős hitelesítési kiszolgáló dolgozza fel a személyes és a szervezeti adatkezelési és adattárolási folyamatokat. Ez a cikk azt ismerteti, hogy mit és hol tárolja a rendszer az adattárolást.

Az Azure AD többtényezős hitelesítési szolgáltatás adatközpontokkal rendelkezik a Egyesült Államok, Európa és Ázsia és a Csendes-óceáni térség. A következő tevékenységek a regionális adatközpontokból származnak, kivéve ha az alábbiak szerepelnek:

* A többtényezős hitelesítésű telefonhívások Egyesült Államok adatközpontokból származnak, és a globális szolgáltatók irányítják őket.
* A más régiókból származó általános célú felhasználói hitelesítési kérelmek feldolgozása jelenleg a felhasználó helye alapján történik.
* A Microsoft Authenticator alkalmazást használó leküldéses értesítések jelenleg a felhasználó helyétől függően a regionális adatközpontokban lesznek feldolgozva. A szállító-specifikus eszközök, például a Apple Push Notification Service a felhasználó helyétől eltérőek lehetnek.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Az Azure AD többtényezős hitelesítése által tárolt személyes adattárolás

A személyes adatok olyan felhasználói szintű információk, amelyek egy adott személyhez tartoznak. A következő adattárak személyes adatokat tartalmaznak:

* Letiltott felhasználók
* Megkerülő felhasználók
* Microsoft Authenticator eszköz jogkivonat-módosítási kérelmek
* Többtényezős hitelesítési tevékenységgel kapcsolatos jelentések
* Microsoft Authenticator aktiválása

Ezeket az információkat 90 napig őrzi meg a rendszer.

Az Azure AD többtényezős hitelesítése nem naplózza a személyes adatok, például a felhasználónevek, a telefonszámok vagy az IP-címek. A *UserObjectId* azonban azonosítja a hitelesítési kísérleteket a felhasználók számára. A naplófájlok tárolása 30 napig történik.

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Az Azure AD többtényezős hitelesítéssel tárolt adatszolgáltatások

Az Azure nyilvános felhők esetében, kivéve a Azure AD B2C hitelesítést, a hálózati házirend-kiszolgáló bővítményét és a Windows Server 2016 vagy 2019 Active Directory összevonási szolgáltatások (AD FS) (AD FS) adaptert, a következő személyes adattárolók tárolódnak:

| Eseménytípus                           | Adattár típusa |
|--------------------------------------|-----------------|
| ESKÜ token                           | Többtényezős hitelesítési naplók     |
| Egyirányú SMS                          | Többtényezős hitelesítési naplók     |
| Hanghívás                           | Többtényezős hitelesítési naplók<br/>Többtényezős hitelesítési tevékenység jelentésének adattára<br/>Letiltott felhasználók (ha a rendszer csalást jelentett) |
| Értesítés Microsoft Authenticator | Többtényezős hitelesítési naplók<br/>Többtényezős hitelesítési tevékenység jelentésének adattára<br/>Letiltott felhasználók (ha a rendszer csalást jelentett)<br/>A Microsoft Authenticator eszköz jogkivonatának módosításakor megjelenő kérelmek módosítása |

Microsoft Azure Government, Microsoft Azure Germany, Microsoft Azure 21Vianet, Azure AD B2C hitelesítést, a hálózati házirend-kiszolgáló bővítményét, valamint a Windows Server 2016 vagy 2019 AD FS adaptert, a következő személyes adattárolók tárolódnak:

| Eseménytípus                           | Adattár típusa |
|--------------------------------------|-----------------|
| ESKÜ token                           | Többtényezős hitelesítési naplók<br/>Többtényezős hitelesítési tevékenység jelentésének adattára |
| Egyirányú SMS                          | Többtényezős hitelesítési naplók<br/>Többtényezős hitelesítési tevékenység jelentésének adattára |
| Hanghívás                           | Többtényezős hitelesítési naplók<br/>Többtényezős hitelesítési tevékenység jelentésének adattára<br/>Letiltott felhasználók (ha a rendszer csalást jelentett) |
| Értesítés Microsoft Authenticator | Többtényezős hitelesítési naplók<br/>Többtényezős hitelesítési tevékenység jelentésének adattára<br/>Letiltott felhasználók (ha a rendszer csalást jelentett)<br/>A Microsoft Authenticator eszköz jogkivonatának módosításakor megjelenő kérelmek módosítása |

### <a name="data-stored-by-azure-multifactor-authentication-server"></a>Az Azure többtényezős hitelesítési kiszolgálója által tárolt adatszolgáltatások

Ha az Azure többtényezős hitelesítési kiszolgálót használja, a rendszer a következő személyes adattárolást tárolja.

> [!IMPORTANT]
> 2019. július 1-től a Microsoft már nem kínál többtényezős hitelesítési kiszolgálót az új üzemelő példányokhoz. Azok az új ügyfelek, akik többtényezős hitelesítést kívánnak megkövetelni a felhasználóktól, felhőalapú Azure AD többtényezős hitelesítést kell használniuk. A többtényezős hitelesítési kiszolgálót a 2019. július 1. előtt aktivált meglévő ügyfelek letöltik a legújabb verziót és frissítéseket, és a szokásos módon előállítják az aktiválási hitelesítő adatokat.

| Eseménytípus                           | Adattár típusa |
|--------------------------------------|-----------------|
| ESKÜ token                           | Többtényezős hitelesítési naplók<br />Többtényezős hitelesítési tevékenység jelentésének adattára |
| Egyirányú SMS                          | Többtényezős hitelesítési naplók<br />Többtényezős hitelesítési tevékenység jelentésének adattára |
| Hanghívás                           | Többtényezős hitelesítési naplók<br />Többtényezős hitelesítési tevékenység jelentésének adattára<br />Letiltott felhasználók (ha a rendszer csalást jelentett) |
| Értesítés Microsoft Authenticator | Többtényezős hitelesítési naplók<br />Többtényezős hitelesítési tevékenység jelentésének adattára<br />Letiltott felhasználók (ha a rendszer csalást jelentett)<br />Módosítási kérelmek Microsoft Authenticator eszköz-jogkivonat módosításakor |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Az Azure AD többtényezős hitelesítéssel tárolt szervezeti adatszolgáltatások

A szervezeti adatok olyan bérlői szintű információk, amelyek lehetővé teszik a konfiguráció vagy a környezet beállítását. A következő Azure Portal a többtényezős hitelesítési lapok bérlői beállításai tárolhatják a szervezeti adatokat, például a zárolási küszöbértékeket vagy a hívó azonosító adatait a bejövő telefonos hitelesítési kérésekhez:

* Fiókzárolási
* Csalási riasztás
* Értesítések
* Telefonhívás beállításai

Az Azure többtényezős hitelesítési kiszolgáló esetében a következő Azure Portal-lapok szervezeti adattal rendelkezhetnek:

* Kiszolgáló beállításai
* Egyszeri Mellőzés
* Gyorsítótárazási szabályok
* Többtényezős hitelesítési kiszolgáló állapota

## <a name="multifactor-authentication-logs-location"></a>Többtényezős hitelesítési naplók helye

A következő táblázat a nyilvános felhők szolgáltatási naplóinak helyét mutatja be.

| Nyilvános felhő| Bejelentkezési naplók | Többtényezős hitelesítési tevékenység jelentés        | Többtényezős hitelesítési szolgáltatás naplói       |
|-------------|--------------|----------------------------------------|------------------------|
| Egyesült Államok          | Egyesült Államok           | Egyesült Államok                                     | Egyesült Államok                     |
| Európa      | Európa       | Egyesült Államok                                     | Európa <sup>2</sup>    |
| Ausztrália   | Ausztrália    | Egyesült Államok<sup>1</sup>                         | Ausztrália <sup>2</sup> |

<sup>1</sup> Az eskü kód naplói Ausztráliában vannak tárolva.

<sup>2</sup> Hanghívások a többtényezős hitelesítési szolgáltatás naplói a Egyesült Államok tárolódnak.

A következő táblázat a szuverén felhők szolgáltatási naplóinak helyét mutatja be.

| Szuverén felhő                      | Bejelentkezési naplók                         | Többtényezős hitelesítési tevékenységről szóló jelentés (személyes adatbevitelt is tartalmaz)| Többtényezős hitelesítési szolgáltatás naplói |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Germany              | Németország                              | Egyesült Államok                            | Egyesült Államok               |
| Azure China 21Vianet                 | Kína                                | Egyesült Államok                            | Egyesült Államok               |
| Microsoft Government Cloud           | Egyesült Államok                                   | Egyesült Államok                            | Egyesült Államok               |

A többtényezős hitelesítési tevékenység jelentései személyes, például egyszerű felhasználónevet (UPN) és teljes telefonszámot tartalmaznak.

A többtényezős hitelesítési szolgáltatás naplói a szolgáltatás működtetésére szolgálnak.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy a felhőalapú Azure AD többtényezős hitelesítés és az Azure többtényezős hitelesítési kiszolgáló milyen felhasználói adatokat gyűjt, lásd: [Azure ad többtényezős hitelesítés felhasználói adatok gyűjtése](howto-mfa-reporting-datacollection.md).
