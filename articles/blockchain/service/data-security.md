---
title: Az Azure Blockchain szolgáltatás biztonsága
description: Az Azure Blockchain szolgáltatás adathozzáférési és biztonsági fogalmai
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80879598"
---
# <a name="azure-blockchain-service-security"></a>Az Azure Blockchain szolgáltatás biztonsága

Az Azure Blockchain szolgáltatás számos Azure-funkciót használ az adatai védelméhez és rendelkezésre állásához. Az adatai elkülönítéssel, titkosítással és hitelesítéssel biztonságosak.

## <a name="isolation"></a>Elkülönítés

Az Azure Blockchain szolgáltatás erőforrásai elkülönítettek egy magánhálózati virtuális hálózaton. Minden tranzakció és érvényesítési csomópont egy virtuális gép (VM). Az egyik virtuális hálózatban lévő virtuális gépek nem tudnak közvetlenül kommunikálni egy másik virtuális hálózatban lévő virtuális gépekkel. Az elkülönítés biztosítja, hogy a kommunikáció a virtuális hálózaton belül maradjon. Az Azure Virtual Network elkülönítésével kapcsolatos további információkért lásd: [elkülönítés az Azure nyilvános felhőben](../../security/fundamentals/isolation-choices.md#networking-isolation).

![VNET diagram](./media/data-security/vnet.png)

## <a name="encryption"></a>Titkosítás

A felhasználói adattárolók tárolása az Azure Storage szolgáltatásban történik. A felhasználói adatok titkosítása mozgásban és nyugalmi állapotban a biztonság és a titkosság érdekében történik. További információkért lásd: az [Azure Storage biztonsági útmutatója](../../storage/blobs/security-recommendations.md).

## <a name="authentication"></a>Hitelesítés

A tranzakciókat RPC-végponton keresztül lehet elküldeni a blockchain-csomópontoknak. Az ügyfelek egy tranzakciós csomóponttal kommunikálnak egy fordított proxykiszolgáló használatával, amely kezeli a felhasználói hitelesítést, és titkosítja az adattitkosítást a TLS protokollon keresztül.

![Hitelesítési diagram](./media/data-security/authentication.png)

Az RPC-hozzáféréshez háromféle hitelesítési mód van.

### <a name="basic-authentication"></a>Alapszintű hitelesítés

Az egyszerű hitelesítés a felhasználónevet és a jelszót tartalmazó HTTP-hitelesítési fejlécet használ. A Felhasználónév a blockchain csomópont neve. A jelszó beállítása egy tag vagy csomópont kiépítés során történik. A jelszót a Azure Portal vagy a parancssori felület használatával lehet megváltoztatni.

### <a name="access-keys"></a>Elérési kulcs

A hozzáférési kulcsok véletlenszerűen generált karakterláncot használnak a végpont URL-címében. A kulcsok elforgatásának engedélyezése két hozzáférési kulcs segítségével. A kulcsok a Azure Portal és a parancssori felületről is újrahozhatók.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) egy jogcím-alapú hitelesítési mechanizmust használ, ahol a felhasználó hitelesítése az Azure AD-ben az Azure AD felhasználói hitelesítő adataival történik. Az Azure AD biztosítja a felhőalapú Identitáskezelés használatát, és lehetővé teszi, hogy az ügyfelek egyetlen identitást használjanak a teljes vállalati és a felhőben elérhető alkalmazások számára. Az Azure Blockchain szolgáltatás integrálható az Azure AD-vel, amely lehetővé teszi az AZONOSÍTÓk összevonását, az egyszeri bejelentkezést és a többtényezős hitelesítést. A szervezeten belül felhasználókat, csoportokat és blockchain rendelhet hozzá a tagokhoz és a csomópontokhoz.

Az Azure AD-ügyfél proxyja elérhető a [githubon](https://github.com/Microsoft/azure-blockchain-connector/releases). Az ügyfél-proxy irányítja a felhasználót az Azure AD bejelentkezési oldalára, és sikeres hitelesítés után beolvas egy tulajdonosi jogkivonatot. Ezt követően a felhasználó egy Ethereum-ügyfélalkalmazás, például a geth vagy a szarvasgomba összekapcsolja az ügyfél proxyjának végpontját. Végül, ha egy tranzakciót küld el, az ügyfél proxyja beadja a tulajdonosi jogkivonatot a HTTP-fejlécben, és a fordított proxy érvényesíti a jogkivonatot a OAuth protokoll használatával.

## <a name="keys-and-ethereum-accounts"></a>Kulcsok és Ethereum fiókok

Egy Azure Blockchain-szolgáltatási tag kiépítés után létrejön egy Ethereum-fiók, valamint egy nyilvános és titkos kulcspár. A titkos kulcs használatával tranzakciókat küldhet a blockchain. A Ethereum fiók a nyilvános kulcs kivonatának utolsó 20 bájtja. A Ethereum-fiók neve is mobiltárca.

A privát és a nyilvános kulcspár keyfile JSON formátumban van tárolva. A titkos kulcs titkosítása a blockchain-Főkönyv szolgáltatás létrehozásakor megadott jelszó használatával történik.

A titkos kulcsok a tranzakciók digitális aláírására szolgálnak. A privát blokkláncok egy titkos kulccsal aláírt intelligens szerződés az aláíró identitását jelöli. Az aláírás érvényességének ellenőrzéséhez a fogadó össze tudja hasonlítani az aláíró nyilvános kulcsát az aláírásból kiszámított címtől.

A konstellációs kulcsok a kvórum-csomópontok egyedi azonosítására szolgálnak. A konstellációs kulcsok a csomópont-kiépítés időpontjában jönnek létre, és a kvórumban található privát tranzakció privateFor paraméterében vannak megadva.

## <a name="next-steps"></a>Következő lépések

Lásd: [Azure Active Directory hozzáférés konfigurálása az Azure Blockchain szolgáltatáshoz](configure-aad.md).
