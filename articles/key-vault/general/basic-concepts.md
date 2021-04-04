---
title: Mi az Azure Key Vault? | Microsoft Docs
description: Ismerje meg, hogy Azure Key Vault védi a Felhőbeli alkalmazások és szolgáltatások által használt titkosítási kulcsokat és titkokat.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: cc00a4f1c1551932b4a30a8ef9b27cb1d4082667
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99071596"
---
# <a name="azure-key-vault-basic-concepts"></a>Azure Key Vault alapvető fogalmak

A Azure Key Vault egy felhőalapú szolgáltatás a titkok biztonságos tárolásához és eléréséhez. A titkos kulcs minden, ami szigorúan szabályozni kívánja a hozzáférést, például az API-kulcsokat, a jelszavakat, a tanúsítványokat és a titkosítási kulcsokat. Key Vault a szolgáltatás két típusú tárolót támogat: a tárakat és a felügyelt HSM-készleteket. A tárolók támogatják a szoftverek és a HSM által támogatott kulcsok, titkok és tanúsítványok tárolását. A felügyelt HSM-készletek csak a HSM által támogatott kulcsokat támogatják. A részletekért tekintse meg a [Azure Key Vault REST API áttekintése](about-keys-secrets-certificates.md) című témakört.

További fontos feltételek:

- **Bérlő**: A bérlő az a cég vagy intézmény, amely egy Microsoft-felhőszolgáltatás egy adott példányát birtokolja és kezeli. Leggyakrabban a szervezet Azure-és Microsoft 365 szolgáltatásaira utal.

- **Kulcstartó-tulajdonos**: Létrehozhat egy Key Vaultot, amely felett teljes körű hozzáféréssel és irányítással rendelkezik. Emellett naplózást is beállíthat, amellyel naplózhatja a titkos kulcsok és a kulcsok elérését. A kulcsok életciklusát a rendszergazdák kezelhetik. Kiadhatnak új kulcsverziókat, biztonsági másolatokat készíthetnek, és elvégezhetik a kapcsolódó feladatokat.

- **Kulcstartóhasználó**: A kulcstartóhasználó műveleteket hajthat végre a Key Vaultban található objektumokon, ha a kulcstartó-tulajdonos felruházta hozzáféréssel. Az elérhető műveletek a kiosztott jogosultságoktól függnek.

- **Felügyelt HSM-rendszergazdák**: a rendszergazdai szerepkörhöz hozzárendelt felhasználók teljes mértékben szabályozzák a FELÜGYELt HSM-készleteket. Több szerepkör-hozzárendelést is létrehozhatnak a más felhasználók számára vezérelt hozzáférés delegálására.

- **Felügyelt HSM titkosítási felelős/felhasználó**: általában olyan beépített szerepkörök, amelyek a felügyelt HSM-kulcsok használatával titkosítási műveleteket végző felhasználókhoz vagy egyszerű szolgáltatásokhoz vannak rendelve. A kriptográfiai felhasználók új kulcsokat hozhatnak létre, de nem törölhetnek kulcsokat.

- **Felügyelt HSM titkosítási szolgáltatás titkosítása**: beépített szerepkör, amely általában egy szolgáltatásfiók által felügyelt szolgáltatás identitásához (pl. Storage-fiókhoz) van rendelve az ügyfelek által felügyelt kulccsal való inaktív adatok titkosításához.

- **Erőforrás**: Az erőforrás egy olyan kezelhető elem, amely az Azure-on keresztül érhető el. Gyakori példák a virtuális gép, a Storage-fiók, a webalkalmazás, az adatbázis és a virtuális hálózat. Sokkal több van.

- **Erőforráscsoport**: Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A cég számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között.

- **Rendszerbiztonsági tag**: az Azure rendszerbiztonsági tagja olyan biztonsági identitás, amelyet a felhasználó által létrehozott alkalmazások, szolgáltatások és automatizálási eszközök használnak az adott Azure-erőforrások eléréséhez. Egy adott szerepkörrel rendelkező "felhasználói identitásnak" (Felhasználónév és jelszó vagy tanúsítvány) kell lennie, és szigorúan szabályozott engedélyekkel kell rendelkezniük. Egy rendszerbiztonsági tag esetében csak bizonyos dolgokra van szükség, az általános felhasználói identitástól eltérően. Ez növeli a biztonságot, ha csak a felügyeleti feladatainak végrehajtásához szükséges minimális jogosultsági szintet adja meg. Az alkalmazással vagy szolgáltatással használt rendszerbiztonsági tag neve kifejezetten **egyszerű szolgáltatásnév**.

- [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md): Az Azure AD egy bérlő Active Directory-szolgáltatása. Minden címtárhoz tartozik egy vagy több tartomány. Egy címtárhoz számos előfizetés tartozhat, de csak egyetlen bérlő.

- **Azure-bérlő azonosítója**: A bérlőazonosító egy egyedi módszer az Azure AD-példány azonosítására az Azure-előfizetésen belül.

- **Felügyelt identitások**: Azure Key Vault lehetővé teszi a hitelesítő adatok és egyéb kulcsok és titkos kódok biztonságos tárolását, de a kódnak hitelesítenie kell a Key Vault a lekéréséhez. A felügyelt identitás használatával egyszerűbbé válik a probléma megoldása azáltal, hogy az Azure-szolgáltatások automatikusan felügyelt identitást biztosítanak az Azure AD-ben. Ezzel az identitással anélkül végezhet hitelesítést a Key Vaultban vagy bármely, Azure AD-hitelesítést támogató szolgáltatásban, hogy a hitelesítő adatokat a kódban kellene tárolnia. További információkért tekintse meg az alábbi ábrát, valamint az [Azure-erőforrások felügyelt identitásának áttekintését](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authentication"></a>Hitelesítés
A Key Vaultkal végzett műveletekhez először hitelesítenie kell magát. A Key Vault háromféleképpen lehet hitelesíteni:

- [Felügyelt identitások az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md): amikor egy Azure-beli virtuális gépre telepít egy alkalmazást, olyan identitást rendelhet hozzá a virtuális géphez, amely hozzáféréssel rendelkezik a Key Vaulthoz. Az identitásokat [más Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md)is hozzárendelheti. Ennek a megközelítésnek az az előnye, hogy az alkalmazás vagy szolgáltatás nem kezeli az első titok rotációját. Az Azure automatikusan elforgatja az identitást. Ajánlott eljárásként javasoljuk ezt a megközelítést. 
- **Egyszerű szolgáltatásnév és tanúsítvány**: használhat egy egyszerű szolgáltatásnevet és egy hozzá tartozó, Key Vaulthoz hozzáféréssel rendelkező tanúsítványt. Ezt a megközelítést nem javasoljuk, mert az alkalmazás tulajdonosának vagy fejlesztőének el kell forgatnia a tanúsítványt.
- **Egyszerű szolgáltatásnév és titkos** kód: bár a Key Vault való hitelesítéshez használhat egy egyszerű szolgáltatásnevet és egy titkos kulcsot, a rendszer nem javasolja. Nem nehéz automatikusan elforgatni a Key Vault hitelesítéséhez használt rendszerindítási titkot.


## <a name="key-vault-roles"></a>Key Vault-szerepkörök

Az alábbi táblázat segítségével jobban megértheti, hogyan segíti a Key Vault a fejlesztők és a biztonsági rendszergazdák igényeinek kielégítését.

| Szerepkör | Probléma leírása | Az Azure Key Vault megoldása |
| --- | --- | --- |
| Azure-alkalmazásfejlesztő |"Olyan alkalmazást szeretnék írni az Azure-hoz, amely kulcsokat használ az aláíráshoz és a titkosításhoz. De szeretném, hogy ezek a kulcsok az alkalmazáson kívül legyenek, hogy a megoldás megfelelő legyen a földrajzilag elosztott alkalmazásokhoz. <br/><br/>Azt szeretném, hogy ezek a kulcsok és titkos kulcsok el legyenek látva védelemmel, anélkül, hogy meg kellene írnom a kódot. Azt is szeretném, hogy ezek a kulcsok és titkok könnyen használhatók legyenek az alkalmazásaim és az optimális teljesítmény érdekében. " |√ A kulcsok tárolása tárolóban történik, és szükség esetén egy URI segítségével lehet előhívni őket.<br/><br/> √ A kulcsok számára az Azure biztosít védelmet az ipari szabványoknak megfelelő algoritmusok, kulcshosszok és hardveres biztonsági modulok segítségével.<br/><br/> √ A kulcsok feldolgozása hardveres biztonsági modulokban történik, amelyek ugyanazokban az Azure-adatközpontokban találhatók, mint az alkalmazások. Ez a módszer nagyobb megbízhatóságot és kisebb késést eredményez ahhoz képest, mintha a kulcsok egy külön helyen, például a helyszínen lennének. |
| Szolgáltatott szoftverek fejlesztője (SaaS-fejlesztő) |"Nem szeretném, hogy az ügyfelek bérlői kulcsainak és titkainak felelőssége vagy lehetséges felelőssége. <br/><br/>Azt szeretném, hogy az ügyfelek saját maguk kezeljék a kulcsaikat, így a legfontosabb szoftvereket biztosító funkciókra koncentrálok. |√ Az ügyfelek importálhatják a saját kulcsaikat az Azure rendszerbe, és kezelhetik őket. Ha egy SaaS-alkalmazásnak titkosítási műveleteket kell végeznie az ügyfelek kulcsainak használatával, Key Vault hajtja végre ezeket a műveleteket az alkalmazás nevében. Az alkalmazás nem látja az ügyfelek kulcsait. |
| Biztonsági vezető (CSO) |"Szeretném tudni, hogy alkalmazásaink megfelelnek az FIPS 140-2 2. vagy a FIPS 140-2 3. szintű HSM a biztonságos kulcsok kezeléséhez. <br/><br/>Biztos szeretnék lenni abban is, hogy a cégünk kézben tartja a kulcsok életciklusát, valamint meg tudja figyelni a kulcshasználatot. <br/><br/>Habár több Azure-szolgáltatást és-erőforrást is használunk, egyetlen helyről szeretném kezelni a kulcsokat az Azure-ban. " |√ Válassza **a** tárolók lehetőséget a FIPS 140-2 2-es szint ellenőrzött HSM.<br/>√ Válassza a **felügyelt HSM-készletek** a FIPS 140-2 3. szintű hitelesített HSM elemet.<br/><br/>√ A Key Vault kialakításának köszönhetően a Microsoft nem tekintheti meg, illetve nem nyerheti ki a kulcsokat.<br/>√ A kulcshasználatot közel valós időben naplózza rendszer.<br/><br/>√ A tároló egyetlen felületet biztosít függetlenül attól, hogy Ön hány tárolóval rendelkezik az Azure rendszerben, ezek mely régiókat támogatják, illetve mely alkalmazások használják őket. |

Azure-előfizetés birtokában bárki létrehozhat és használhat kulcstárolót. Bár a Key Vault a fejlesztők és a biztonsági rendszergazdák számára is, a szervezet rendszergazdája is megvalósíthatja és felügyelheti, aki más Azure-szolgáltatásokat felügyel. Például a rendszergazda bejelentkezhet egy Azure-előfizetéssel, létrehozhat egy tárolót azon szervezet számára, amelyben a kulcsokat tárolni kívánja, majd a következőhöz hasonló operatív feladatokért felelős:

- A kulcsok vagy titkos kulcsok létrehozása és importálása
- A kulcsok vagy titkos kulcsok visszavonása, illetve törlése
- A felhasználók vagy alkalmazások feljogosítása a kulcstárolóhoz való hozzáférésre, hogy azután kezelhessék és használhassák a benne tárolt kulcsokat és titkos kulcsokat
- A kulcshasználat konfigurálása (például aláíráshoz vagy titkosításhoz)
- A kulcshasználat figyelése

Ez a rendszergazda ezután lehetővé teszi a fejlesztők URI-k számára, hogy meghívja az alkalmazásaikat. Ez a rendszergazda a kulcs-használati naplózási információkat is megadja a biztonsági rendszergazdának. 

![A Azure Key Vault működésének áttekintése][1]

A fejlesztők közvetlenül is kezelhetik a kulcsokat API-k használatával. További információkért tekintse meg a [Key Vault fejlesztői útmutatóját](developers-guide.md).

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [védheti meg a](secure-your-key-vault.md)tárolót.
- Ismerje meg, hogyan [védheti meg felügyelt HSM-készleteit](../managed-hsm/access-control.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).