---
title: A Azure Data Lake Storage Gen1 biztonságának áttekintése | Microsoft Docs
description: Ismerje meg Azure Data Lake Storage Gen1 biztonsági képességeit, beleértve a hitelesítést, az engedélyezést, a hálózati elkülönítést, az adatvédelmet és a naplózást.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: twooley
ms.openlocfilehash: 240018381a3139a6378141d78514e43ae469de5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92146303"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Biztonság a Azure Data Lake Storage Gen1

Számos vállalat kihasználja a big data Analytics for Business-elemzéseket, amelyek segítségével intelligens döntéseket hozhat. Egy szervezet összetett és szabályozott környezettel rendelkezik, amely egyre több különböző felhasználót tartalmaz. Létfontosságú, hogy a vállalat gondoskodjon arról, hogy a kritikus fontosságú üzleti adatok tárolása biztonságosabb legyen, az egyes felhasználók számára biztosított megfelelő hozzáférési szinttel. Azure Data Lake Storage Gen1 úgy lett kialakítva, hogy segítse a biztonsági követelmények teljesítését. Ebben a cikkben megismerheti a Data Lake Storage Gen1 biztonsági képességeit, beleértve a következőket:

* Hitelesítés
* Engedélyezés
* Hálózatelkülönítés
* Adatvédelem
* Naplózás

## <a name="authentication-and-identity-management"></a>Hitelesítés és Identitáskezelés

A hitelesítés az a folyamat, amellyel a felhasználó identitása ellenőrizhető, ha a felhasználó a Data Lake Storage Gen1 vagy bármely olyan szolgáltatással kommunikál, amely kapcsolódik Data Lake Storage Gen1hoz. Az Identitáskezelés és a hitelesítés esetében Data Lake Storage Gen1 a [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), egy átfogó identitás-és hozzáférés-kezelési felhőalapú megoldást használ, amely leegyszerűsíti a felhasználók és csoportok felügyeletét.

Minden Azure-előfizetés társítható Azure Active Directory egy példányával. Csak a Azure Active Directory szolgáltatásban definiált felhasználók és szolgáltatások férhetnek hozzá a Data Lake Storage Gen1-fiókhoz a Azure Portal, parancssori eszközök vagy a szervezet által az Data Lake Storage Gen1 SDK használatával létrehozott ügyfélalkalmazások használatával. A Azure Active Directory központi hozzáférés-vezérlési mechanizmusként való használatának fő előnyei a következők:

* Egyszerűsített identitás-életciklus kezelése. Egy felhasználó vagy szolgáltatás (egyszerű szolgáltatásnév) identitása gyorsan létrehozható és gyorsan visszavonható, ha egyszerűen törli vagy letiltja a fiókot a címtárban.
* Multi-Factor Authentication. A [többtényezős hitelesítés](../active-directory/authentication/concept-mfa-howitworks.md) további biztonsági réteget biztosít a felhasználói bejelentkezések és tranzakciók számára.
* Hitelesítés bármely ügyféltől egy szabványos nyílt protokollon keresztül, például OAuth vagy OpenID.
* Összevonás az Enterprise Directory Services és a Cloud Identity Providers szolgáltatással.

## <a name="authorization-and-access-control"></a>Engedélyezési és hozzáférés-vezérlés

Miután Azure Active Directory hitelesíti a felhasználót, hogy a felhasználó hozzáférhessen a Data Lake Storage Gen1hoz, az engedélyezés szabályozza a Data Lake Storage Gen1 hozzáférési engedélyeit. A Data Lake Storage Gen1 a következő módon választja el a fiókkal kapcsolatos és az adathoz kapcsolódó tevékenységek engedélyezését:

* [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md) a fiókok felügyeletéhez
* Az áruházban tárolt adatok elérésére szolgáló POSIX ACL

### <a name="azure-rbac-for-account-management"></a>Azure-RBAC a fiókok felügyeletéhez

Alapértelmezés szerint négy alapszintű szerepkör van definiálva Data Lake Storage Gen1hoz. A szerepkörök a Azure Portal, a PowerShell-parancsmagok és a REST API-k használatával engedélyezhetik a különböző műveleteket egy Data Lake Storage Gen1 fiókon. A tulajdonos és a közreműködő szerepkör számos felügyeleti funkciót képes végrehajtani a fiókon. Az olvasó szerepkört hozzárendelheti azokhoz a felhasználókhoz, akik csak a fiókkezelés-adatbázisokat tekintik meg.

![Azure-szerepkörök](./media/data-lake-store-security-overview/rbac-roles.png "Azure-szerepkörök")

Vegye figyelembe, hogy bár a szerepkörök hozzá vannak rendelve a fiókok felügyeletéhez, egyes szerepkörök hatással vannak az adathozzáférésre. Az ACL-eket kell használnia a felhasználók által a fájlrendszeren végrehajtható műveletekhez való hozzáférés vezérléséhez. Az alábbi táblázat az alapértelmezett szerepkörökhöz tartozó felügyeleti jogosultságokat és adathozzáférési jogosultságokat tartalmazza.

| Szerepkörök | Felügyeleti jogosultságok | Adathozzáférési jogosultságok | Magyarázat |
| --- | --- | --- | --- |
| Nincs hozzárendelt szerepkör |Nincsenek |Az ACL szabályozza |A felhasználó nem használhatja a Azure Portal vagy Azure PowerShell parancsmagot a Data Lake Storage Gen1 tallózásához. A felhasználó csak parancssori eszközöket tud használni. |
| Tulajdonos |Mind |Mind |A tulajdonosi szerepkör a rendszergazda. Ez a szerepkör mindent tud kezelni, és teljes hozzáféréssel rendelkezik az összes adathoz. |
| Olvasó |Csak olvasható |Az ACL szabályozza |Az olvasó szerepkör mindent megtekinthet a fiókok kezelésével kapcsolatban, például azt, hogy melyik felhasználóhoz van hozzárendelve a szerepkör. Az olvasó szerepkör nem végezhet módosításokat. |
| Közreműködő |A Szerepkörök hozzáadása és eltávolítása kivételével |Az ACL szabályozza |A közreműködői szerepkör a fiókok bizonyos aspektusait képes kezelni, például a központi telepítéseket, valamint a riasztások létrehozását és kezelését. A közreműködő szerepkör nem tudja hozzáadni vagy eltávolítani a szerepköröket. |
| Felhasználói hozzáférés rendszergazdája |Szerepkörök hozzáadása és eltávolítása |Az ACL szabályozza |A felhasználói hozzáférés rendszergazdai szerepköre kezelheti a felhasználói hozzáférést a fiókokhoz. |

Útmutatásért lásd: [felhasználók vagy biztonsági csoportok társítása Data Lake Storage Gen1-fiókokhoz](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>ACL-ek használata a fájlrendszerek műveleteihez

Data Lake Storage Gen1 hierarchikus fájlrendszer, például Hadoop elosztott fájlrendszer (HDFS), és támogatja a [POSIX ACL-eket](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Az olvasás (r), a Write (w) és a Execute (x) engedélyeket vezérli a tulajdonosi szerepkör, a tulajdonosok csoport, valamint más felhasználók és csoportok erőforrásaihoz. Data Lake Storage Gen1 az ACL-ek engedélyezhetők a gyökérkönyvtárban, az almappákban és az egyes fájlokban is. Az ACL-ek Data Lake Storage Gen1 környezetében való működésével kapcsolatos további információkért lásd: [Data Lake Storage Gen1 hozzáférés-vezérlése](data-lake-store-access-control.md).

Azt javasoljuk, hogy a [biztonsági csoportok](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)használatával több felhasználó hozzáférés-vezérlési listáit is definiálja. Vegyen fel felhasználókat egy biztonsági csoportba, majd rendelje hozzá egy fájlhoz vagy mappához tartozó ACL-eket az adott biztonsági csoporthoz. Ez akkor hasznos, ha a hozzárendelt engedélyeket szeretné megadni, mert a hozzárendelt engedélyekhez legfeljebb 28 bejegyzés van korlátozva. Ha további információt szeretne arról, hogyan védheti meg az Data Lake Storage Gen1 tárolt adatokat Azure Active Directory biztonsági csoportok használatával, tekintse meg [a felhasználók vagy biztonsági csoport társítása ACL-ként a Data Lake Storage Gen1 fájlrendszerhez](data-lake-store-secure-data.md#filepermissions)című témakört.

![Hozzáférési engedélyek listázása](./media/data-lake-store-security-overview/adl.acl.2.png "Hozzáférési engedélyek listázása")

## <a name="network-isolation"></a>Hálózatelkülönítés

A Data Lake Storage Gen1 segítségével szabályozhatja az adattárhoz való hozzáférést a hálózati szinten. Tűzfalat hozhat létre, és meghatározhatja a megbízható ügyfelek IP-címtartományt. Az IP-címtartomány csak a megadott tartományon belüli IP-címmel rendelkező ügyfelek csatlakozhatnak Data Lake Storage Gen1hoz.

![Tűzfalbeállítások és IP-hozzáférés](./media/data-lake-store-security-overview/firewall-ip-access.png "Tűzfalbeállítások és IP-cím")

Az Azure Virtual Networks (VNet) támogatja a Data Lake 1. generációs szolgáltatáshoz tartozó címkéket. A szolgáltatás címkéje egy adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával. További információ: Azure- [szolgáltatás címkék áttekintése](../virtual-network/service-tags-overview.md).

## <a name="data-protection"></a>Adatvédelem

Data Lake Storage Gen1 a teljes életciklusában védi az adatait. Az átvitt adatforgalomban a Data Lake Storage Gen1 az iparági szabványnak megfelelő Transport Layer Security (TLS 1,2) protokollt használja az adat hálózaton keresztüli biztonságossá tételéhez.

![Titkosítás Data Lake Storage Gen1](./media/data-lake-store-security-overview/adls-encryption.png "Titkosítás Data Lake Storage Gen1")

A Data Lake Storage Gen1 a fiókban tárolt adathalmazok titkosítását is biztosítja. Dönthet úgy, hogy titkosítja az adatokat, vagy választhatja a titkosítás nélküli lehetőséget. Ha a titkosítást választja, a rendszer a Data Lake Storage Gen1 tárolt adatvédelmet az állandó adathordozón való tárolás előtt titkosítja. Ilyen esetben a Data Lake Storage Gen1 automatikusan titkosítja az adatok megtartását, és visszafejti azokat a lekérés előtt, így az adatokhoz hozzáférő ügyfél teljesen átlátható marad. Az ügyfél oldalán nincs szükség a kód módosítására az adattitkosításhoz/visszafejtéshez.

A kulcskezelő szolgáltatásban a Data Lake Storage Gen1 két módot biztosít a fő titkosítási kulcsok (MEKs) kezelésére, amelyek a Data Lake Storage Gen1 tárolt adatok visszafejtéséhez szükségesek. Data Lake Storage Gen1 kezelheti a MEKs, vagy dönthet úgy, hogy megőrzi a MEKs tulajdonjogát a Azure Key Vault-fiók használatával. Data Lake Storage Gen1 fiók létrehozásakor megadhatja a kulcskezelő módot. További információ a titkosítással kapcsolatos konfiguráció megadásáról: [az Azure Data Lake Storage Gen1 használatának első lépései az Azure Portalon](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Tevékenység és diagnosztikai naplók

Tevékenység-vagy diagnosztikai naplókat használhat, attól függően, hogy a fiókokat a fiókok felügyeletével kapcsolatos tevékenységekhez vagy az adathoz kapcsolódó tevékenységekhez szeretné-e naplózni.

* A fiókkezelés szolgáltatással kapcsolatos tevékenységek Azure Resource Manager API-kat használnak, és a Azure Portal a tevékenység naplófájljain keresztül vannak felszínben.
* Az adatokkal kapcsolatos tevékenységek a WebHDFS REST API-kat használják, és a Azure Portal a diagnosztikai naplókon keresztül.

### <a name="activity-log"></a>Tevékenységnapló

A szabályozásoknak való megfelelés érdekében a szervezetnek a fiókok felügyeleti tevékenységeinek megfelelő naplózási nyomvonalat kell megkövetelni, ha adott incidensekre van szüksége. A Data Lake Storage Gen1 beépített figyeléssel rendelkezik, és naplózza az összes Fiókkezelés tevékenységet.

A Fiókkezelés naplózása elemnél tekintse meg és válassza ki a naplózni kívánt oszlopokat. A tevékenység-naplókat az Azure Storage-ba is exportálhatja.

![Tevékenységnapló](./media/data-lake-store-security-overview/activity-logs.png "Tevékenységnapló")

További információ a tevékenységi naplók használatáról: tevékenységek [naplóinak megtekintése az erőforrásokon végzett műveletek naplózása érdekében](../azure-resource-manager/management/view-activity-logs.md).

### <a name="diagnostics-logs"></a>Diagnosztikai naplók

Engedélyezheti az adathozzáférés naplózását és a diagnosztikai naplózást a Azure Portalban, és elküldheti a naplókat egy Azure Blob Storage-fiókba, egy Event hub-ba vagy egy Azure Monitor naplóba.

![Diagnosztikai naplók](./media/data-lake-store-security-overview/diagnostic-logs.png "Diagnosztikai naplók")

A diagnosztikai naplók Data Lake Storage Gen1val való használatáról további információt a [diagnosztikai naplók Data Lake Storage Gen1hoz való hozzáférésével](data-lake-store-diagnostic-logs.md)foglalkozó témakörben talál.

## <a name="summary"></a>Összefoglalás

A nagyvállalati ügyfelek egy biztonságos és könnyen használható adatelemzési felhőalapú platformot igényelnek. A Data Lake Storage Gen1 úgy lett kialakítva, hogy az Identitáskezelés és a hitelesítés révén segítse ezeket a követelményeket Azure Active Directory integráció, ACL-alapú hitelesítés, hálózati elkülönítés, adattitkosítás az átvitelben és a nyugalmi állapotban, valamint a naplózás.

Ha a Data Lake Storage Gen1 új funkcióit szeretné megtekinteni, küldje el nekünk visszajelzését a [Data Lake Storage Gen1 UserVoice fórumban](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Lásd még

* [A Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
* [Ismerkedés a Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)