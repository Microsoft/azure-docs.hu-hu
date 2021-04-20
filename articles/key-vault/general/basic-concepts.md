---
title: Mi az Azure Key Vault? | Microsoft Docs
description: Megtudhatja, Azure Key Vault hogyan védik a felhőalkalmazások és -szolgáltatások által használt titkosítási kulcsokat és titkos kódokat.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 46c8845b4f01db09bf5f96eb1e67078b4e361f9f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728319"
---
# <a name="azure-key-vault-basic-concepts"></a>Azure Key Vault alapfogalmak

Azure Key Vault a titkos kulcsok biztonságos tárolására és elérésére szolgáló felhőszolgáltatás. A titkos kulcs bármi, amihez szorosan szabályozni szeretné a hozzáférést, például AZ API-kulcsok, jelszavak, tanúsítványok vagy titkosítási kulcsok. Key Vault szolgáltatás kétféle tárolót támogat: tárolókat és felügyelt hardveres biztonsági modulkészleteket (HSM- készleteket). A tárolók támogatják a szoftverek és A HSM által támogatott kulcsok, titkos kulcsok és tanúsítványok tárolását. A felügyelt HSM-készletek csak a HSM-támogatású kulcsokat támogatják. A [Azure Key Vault REST API az áttekintést.](about-keys-secrets-certificates.md)

További fontos kifejezések:

- **Bérlő**: A bérlő az a cég vagy intézmény, amely egy Microsoft-felhőszolgáltatás egy adott példányát birtokolja és kezeli. Leggyakrabban az Azure és a Microsoft 365 szolgáltatáskészletére hivatkoznak.

- **Kulcstartó-tulajdonos**: Létrehozhat egy Key Vaultot, amely felett teljes körű hozzáféréssel és irányítással rendelkezik. Emellett naplózást is beállíthat, amellyel naplózhatja a titkos kulcsok és a kulcsok elérését. A kulcsok életciklusát a rendszergazdák kezelhetik. Kiadhatnak új kulcsverziókat, biztonsági másolatokat készíthetnek, és elvégezhetik a kapcsolódó feladatokat.

- **Kulcstartóhasználó**: A kulcstartóhasználó műveleteket hajthat végre a Key Vaultban található objektumokon, ha a kulcstartó-tulajdonos felruházta hozzáféréssel. Az elérhető műveletek a kiosztott jogosultságoktól függnek.

- **Felügyelt HSM-rendszergazdák:** A Rendszergazda szerepkörhöz rendelt felhasználók teljes körű irányítás alatt állnak a felügyelt HSM-készlet felett. További szerepkör-hozzárendeléseket hozhatnak létre, amelyek szabályozott hozzáférést delegálnak más felhasználók számára.

- **Felügyelt HSM** titkosítási igazgató/felhasználó: Beépített szerepkörök, amelyek általában olyan felhasználókhoz vagy szolgáltatásnévhez vannak hozzárendelve, amelyek titkosítási műveleteket hajt végre a Managed HSM kulcsait használva. A kriptográfiai felhasználó létrehozhat új kulcsokat, de nem törölheti a kulcsokat.

- **Felügyelt HSM titkosítási** szolgáltatás titkosítása: Beépített szerepkör, amely általában egy szolgáltatásfiók felügyeltszolgáltatás-identitásához (pl. tárfiókhoz) van rendelve az adatok ügyfél által felügyelt kulccsal való titkosításához.

- **Erőforrás**: Az erőforrás egy olyan kezelhető elem, amely az Azure-on keresztül érhető el. Gyakori példák a virtuális gép, a tárfiók, a webalkalmazás, az adatbázis és a virtuális hálózat. Sokkal több is van.

- **Erőforráscsoport**: Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A cég számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között.

- **Rendszerbiztonsági tag:** Az Azure-beli rendszerbiztonsági tag olyan biztonsági identitás, amely segítségével a felhasználó által létrehozott alkalmazások, szolgáltatások és automatizálási eszközök hozzáférhetnek bizonyos Azure-erőforrásokhoz. Gondoljon rá úgy, mint egy "felhasználói identitásra" (felhasználónévre és jelszóra vagy tanúsítványra) egy adott szerepkör és szigorúan szabályozott engedélyekkel. A rendszerbiztonsági tagnak csak bizonyos dolgokat kell megtennie, az általános felhasználói identitástól eltérően. Javítja a biztonságot, ha csak a felügyeleti feladatok végrehajtásához szükséges minimális engedélyszintet adja meg számára. Az alkalmazásokhoz vagy szolgáltatásokhoz használt rendszerbiztonsági tagokat kifejezetten **szolgáltatásnévnek nevezzük.**

- [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md): Az Azure AD egy bérlő Active Directory-szolgáltatása. Minden címtárhoz tartozik egy vagy több tartomány. Egy címtárhoz számos előfizetés tartozhat, de csak egyetlen bérlő.

- **Azure-bérlő azonosítója**: A bérlőazonosító egy egyedi módszer az Azure AD-példány azonosítására az Azure-előfizetésen belül.

- **Felügyelt identitások:** Azure Key Vault lehetővé teszi a hitelesítő adatok és egyéb kulcsok és titkos kódok biztonságos tárolására, de a kódnak hitelesítenie kell magát a Key Vault a lekérésük érdekében. A felügyelt identitások használata egyszerűbbé teszi ezt a problémát azáltal, hogy az Azure-szolgáltatások számára egy automatikusan felügyelt identitást biztosít az Azure AD-ban. Ezzel az identitással anélkül végezhet hitelesítést a Key Vaultban vagy bármely, Azure AD-hitelesítést támogató szolgáltatásban, hogy a hitelesítő adatokat a kódban kellene tárolnia. További információt az alábbi képen és az [Azure-erőforrások felügyelt identitásának áttekintésében talál.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="authentication"></a>Hitelesítés
Ha bármilyen műveletet el Key Vault, először hitelesítenie kell rajta. A hitelesítésnek három módja van a Key Vault:

- [Az Azure-erőforrások felügyelt identitása:](../../active-directory/managed-identities-azure-resources/overview.md)Ha alkalmazást helyez üzembe egy Azure-beli virtuális gépen, hozzárendelhet egy identitást a virtuális géphez, amely hozzáféréssel rendelkezik a Key Vault. Identitásokat más [Azure-erőforrásokhoz is hozzárendelhet.](../../active-directory/managed-identities-azure-resources/overview.md) Ennek a megközelítésnek az az előnye, hogy az alkalmazás vagy a szolgáltatás nem kezeli az első titkos ékkedő rotációját. Az Azure automatikusan váltja az identitást. Ajánlott eljárásként ezt a megközelítést javasoljuk. 
- **Szolgáltatásnév és tanúsítvány:** Használhat szolgáltatásnév és egy társított tanúsítványt, amely hozzáféréssel rendelkezik a Key Vault. Ez a módszer nem ajánlott, mert az alkalmazás tulajdonosának vagy fejlesztőjének át kell forgatnia a tanúsítványt.
- **Egyszerű szolgáltatás és titkos:** Bár szolgáltatásnév és titkos adatokat is használhat a Key Vault hitelesítéséhez, nem javasoljuk. A hitelesítéshez használt bootstrap-titkos adatokat nehéz automatikusan elforgatni a Key Vault.


## <a name="key-vault-roles"></a>Key Vault-szerepkörök

Az alábbi táblázat segítségével jobban megértheti, hogyan segíti a Key Vault a fejlesztők és a biztonsági rendszergazdák igényeinek kielégítését.

| Szerepkör | Probléma leírása | Az Azure Key Vault megoldása |
| --- | --- | --- |
| Azure-alkalmazásfejlesztő |"Olyan alkalmazást szeretnék írni az Azure-hoz, amely kulcsokat használ az aláíráshoz és a titkosításhoz. Azt szeretném azonban, hogy ezek a kulcsok az alkalmazáson kívülre is kivehetőek, hogy a megoldás megfelelő legyen egy földrajzilag elosztott alkalmazáshoz. <br/><br/>Azt szeretném, hogy ezek a kulcsok és titkos kulcsok el legyenek látva védelemmel, anélkül, hogy meg kellene írnom a kódot. Emellett azt szeretném, hogy ezek a kulcsok és titkos kulcsok könnyen használhatók legyenek az alkalmazásaimból, optimális teljesítménnyel." |√ A kulcsok tárolása tárolóban történik, és szükség esetén egy URI segítségével lehet előhívni őket.<br/><br/> √ A kulcsok számára az Azure biztosít védelmet az ipari szabványoknak megfelelő algoritmusok, kulcshosszok és hardveres biztonsági modulok segítségével.<br/><br/> √ A kulcsok feldolgozása hardveres biztonsági modulokban történik, amelyek ugyanazokban az Azure-adatközpontokban találhatók, mint az alkalmazások. Ez a módszer nagyobb megbízhatóságot és kisebb késést eredményez ahhoz képest, mintha a kulcsok egy külön helyen, például a helyszínen lennének. |
| Szolgáltatott szoftverek fejlesztője (SaaS-fejlesztő) |"Nem szeretnék felelősséget vagy esetleges felelősséget vállalni az ügyfeleim bérlői kulcsait és titkos kulcsait. <br/><br/>Azt szeretném, hogy az ügyfelek a saját kulcsukat kezelve a lehető legjobban tudjanak tenni, amihez a legjobban tudok, az alapvető szoftveres funkciók biztosításához." |√ Az ügyfelek importálhatják a saját kulcsaikat az Azure rendszerbe, és kezelhetik őket. Ha egy SaaS-alkalmazásnak kriptográfiai műveleteket kell végrehajtania az ügyfelek kulcsait használva, Key Vault a műveleteket az alkalmazás nevében végzi el. Az alkalmazás nem látja az ügyfelek kulcsait. |
| Biztonsági vezető (CSO) |"Tudni szeretném, hogy az alkalmazásaink megfelelnek a FIPS 140-2 Level 2 vagy a FIPS 140-2 3. szintű HSM-nek a biztonságos kulcskezelés érdekében. <br/><br/>Biztos szeretnék lenni abban is, hogy a cégünk kézben tartja a kulcsok életciklusát, valamint meg tudja figyelni a kulcshasználatot. <br/><br/>És bár több Azure-szolgáltatást és -erőforrást is használunk, egyetlen helyről szeretném kezelni a kulcsokat az Azure-ban." |√ válasszon **tárolókat** a FIPS 140-2 2. szintje szerint ellenőrzött HSM-hez.<br/>√ felügyelt **HSM-készletek** kiválasztása a FIPS 140-2 3. szintje szerint ellenőrzött HSM-hez.<br/><br/>√ A Key Vault kialakításának köszönhetően a Microsoft nem tekintheti meg, illetve nem nyerheti ki a kulcsokat.<br/>√ A kulcshasználatot közel valós időben naplózza rendszer.<br/><br/>√ A tároló egyetlen felületet biztosít függetlenül attól, hogy Ön hány tárolóval rendelkezik az Azure rendszerben, ezek mely régiókat támogatják, illetve mely alkalmazások használják őket. |

Azure-előfizetés birtokában bárki létrehozhat és használhat kulcstárolót. Bár Key Vault a fejlesztők és a biztonsági rendszergazdák számára előnyös, más Azure-szolgáltatásokat kezelő szervezet rendszergazdája is megvalósíthatja és felügyelheti. Ez a rendszergazda például bejelentkezhet egy Azure-előfizetéssel, létrehozhat egy tárolót a szervezet kulcstárolójához, majd az alábbihoz hasonló üzemeltetési feladatokért felelhet:

- A kulcsok vagy titkos kulcsok létrehozása és importálása
- A kulcsok vagy titkos kulcsok visszavonása, illetve törlése
- A felhasználók vagy alkalmazások feljogosítása a kulcstárolóhoz való hozzáférésre, hogy azután kezelhessék és használhassák a benne tárolt kulcsokat és titkos kulcsokat
- A kulcshasználat konfigurálása (például aláíráshoz vagy titkosításhoz)
- A kulcshasználat figyelése

A rendszergazda ezután a fejlesztőknek URI-okat ad meg az alkalmazásaikból való híváshoz. Ez a rendszergazda a kulcshasználat naplózási adatait is megadja a biztonsági rendszergazdának. 

![A Azure Key Vault áttekintése][1]

A fejlesztők közvetlenül is kezelhetik a kulcsokat API-k használatával. További információkért tekintse meg a [Key Vault fejlesztői útmutatóját](developers-guide.md).

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [biztosíthatja a tároló biztonságát.](secure-your-key-vault.md)
- Megtudhatja, hogyan [biztosíthatja a felügyelt HSM-készletek biztonságát](../managed-hsm/access-control.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).
