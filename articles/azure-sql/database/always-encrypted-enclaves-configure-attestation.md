---
title: Az Azure-igazolás konfigurálása az Azure SQL logikai kiszolgálóhoz
description: Konfigurálja az Azure-igazolást Always Encrypted biztonságos enklávékkal Azure SQL Databaseban.
keywords: adatok titkosítása, SQL-titkosítás, adatbázis-titkosítás, bizalmas adatok, Always Encrypted, biztonságos enklávék, SGX ENKLÁVÉHOZ, igazolás
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: a51aa15e1338380d4b4179e7fb8899273750c374
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106107180"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>Az Azure-igazolás konfigurálása az Azure SQL logikai kiszolgálóhoz

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted a biztonságos enklávékkal a Azure SQL Database jelenleg **nyilvános előzetes** verzióban érhető el.

[Microsoft Azure igazolás](../../attestation/overview.md) a megbízható végrehajtási környezetek (pólók) igazolására szolgáló megoldás, beleértve az Intel Software Guard Extensions (Intel SGX enklávéhoz) enklávékat. 

Ha az Azure-igazolást szeretné használni a Azure SQL Database [biztonságos enklávékkal Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) használt Intel SGX enklávéhoz enklávék igazolásához, a következőket kell tennie:

1. Hozzon létre egy [igazolási szolgáltatót](../../attestation/basic-concepts.md#attestation-provider) , és konfigurálja az ajánlott igazolási szabályzattal.

2. Adja meg az Azure SQL logikai kiszolgáló hozzáférését az igazolási szolgáltatóhoz.

> [!NOTE]
> Az igazolás konfigurálása az igazolási rendszergazda feladata. Tekintse meg a [szerepköröket és a felelősségi köröket a SGX enklávéhoz enklávék és igazolások konfigurálásakor](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

## <a name="requirements"></a>Követelmények

Az Azure SQL logikai kiszolgálónak és az igazolási szolgáltatónak ugyanahhoz a Azure Active Directory bérlőhöz kell tartoznia. A több-bérlős interakciók nem támogatottak. 

Az Azure SQL logikai kiszolgálónak hozzá kell rendelnie egy Azure AD-identitást. Az igazolási rendszergazdaként be kell szereznie a kiszolgáló Azure AD-identitását az adott kiszolgáló Azure SQL Database rendszergazdájától. Az identitást fogja használni a kiszolgáló hozzáférésének biztosításához az igazolási szolgáltatóhoz. 

A kiszolgáló identitással való létrehozásával vagy az identitás egy meglévő kiszolgálóhoz való hozzárendelésével kapcsolatban a PowerShell és az Azure CLI használatával a következő témakörben talál útmutatást: [Azure ad-identitás kiosztása a kiszolgálóhoz](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server).

## <a name="create-and-configure-an-attestation-provider"></a>Igazolási szolgáltató létrehozása és konfigurálása

Az [igazolási szolgáltató](../../attestation/basic-concepts.md#attestation-provider) olyan erőforrás az Azure-tanúsítványokban, amely kiértékeli az igazolási [kérelmeket](../../attestation/basic-concepts.md#attestation-request) a tanúsítvány- [szabályzatok](../../attestation/basic-concepts.md#attestation-request) és az [igazolási jogkivonatok](../../attestation/basic-concepts.md#attestation-token)ellen. 

Az igazolási házirendek a jogcím- [szabály nyelvtana](../../attestation/claim-rule-grammar.md)alapján vannak megadva.

A Microsoft a következő szabályzatot javasolja a Azure SQL Database Always Encrypted használt Intel SGX ENKLÁVÉHOZ enklávék igazolásához:

```output
version= 1.0;
authorizationrules 
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
};
```

A fenti szabályzat a következőket ellenőrzi:

- A Azure SQL Databaseon belüli enklávé nem támogatja a hibakeresést. 
  > Az enklávék a letiltott vagy engedélyezett hibakereséssel tölthetők be. A hibakeresési támogatás úgy lett kialakítva, hogy lehetővé tegye a fejlesztők számára, hogy elhárítsák az enklávéban futó kódot. Éles rendszerekben a hibakeresés lehetővé teheti a rendszergazdák számára az enklávé tartalmának vizsgálatát, ami csökkenti az enklávé által biztosított védelem szintjét. Az ajánlott szabályzat letiltja a hibakeresést annak érdekében, hogy ha egy rosszindulatú rendszergazda megpróbálja bekapcsolni a hibakeresési támogatást az enklávé gép átvételével, az igazolás sikertelen lesz. 
- Az enklávé termékazonosítója megegyezik a biztonságos enklávékkal Always Encryptedhoz rendelt termékazonosítóval.
  > Mindegyik enklávé egyedi termékazonosítóval rendelkezik, amely megkülönbözteti az enklávét más enklávék. Az Always Encrypted enklávéhoz rendelt termékazonosító 4639. 
- A könyvtár biztonsági verziószáma (SVN) nagyobb, mint 0.
  > Az SVN lehetővé teszi a Microsoft számára, hogy válaszoljon az enklávé kódjában azonosított esetleges biztonsági hibákra. Ha biztonsági problémát talál és kijavítanak, a Microsoft új (megnövelt) SVN-t helyez üzembe az enklávé új verziójában. A fenti ajánlott szabályzat frissül, hogy tükrözze az új SVN-t. Ha a szabályzatot úgy frissíti, hogy az megfeleljen az ajánlott szabályzatnak, gondoskodhat arról, hogy ha egy rosszindulatú rendszergazda megkísérel betölteni egy régebbi és nem biztonságos enklávét, az igazolás sikertelen lesz.
- Az enklávéban található függvénytár a Microsoft aláírási kulcsával lett aláírva (az x-MS-SGX enklávéhoz-mrsigner jogcím értéke az aláíró kulcs kivonata).
  > Az igazolás egyik fő célja, hogy meggyőzze az ügyfeleket arról, hogy az enklávéban futó bináris fájl az a bináris, amelyet futtatni kellene. Az igazolási szabályzatok két mechanizmust biztosítanak erre a célra. Az egyik az a **mrenclave** jogcím, amely az enklávéban futtatandó bináris fájl kivonata. A **mrenclave** problémája, hogy a bináris kivonat a kód triviális változásaival is megváltozik, ami megnehezíti az enklávéban futó kód megváltoztatását. Ezért javasoljuk a **mrsigner** használatát, amely egy olyan kulcs kivonata, amely az enklávé bináris fájljának aláírására szolgál. Ha a Microsoft az enklávét, a **mrsigner** ugyanaz marad, amíg az aláíró kulcs nem változik. Így a frissített bináris fájlok üzembe helyezése az ügyfelek alkalmazásainak megszakítása nélkül valósítható meg. 

> [!IMPORTANT]
> A rendszer létrehoz egy igazolási szolgáltatót az Intel SGX ENKLÁVÉHOZ enklávés alapértelmezett házirendjével, amely nem ellenőrzi az enklávén belül futó kódot. A Microsoft nyomatékosan javasolja, hogy állítsa be a fenti javasolt szabályzatot, és ne használja az alapértelmezett szabályzatot a biztonságos enklávékkal való Always Encrypted.

Az igazolási szolgáltató létrehozásával és az igazolási szabályzattal való konfigurálásával kapcsolatos utasítások a következő használatával:

- [Gyors útmutató: Azure-igazolás beállítása Azure Portal](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > Ha az igazolási házirendet Azure Portal használatával konfigurálja, állítsa be az igazolási típust a következőre: `SGX-IntelSDK` .
- [Gyors útmutató: Azure-igazolás beállítása Azure PowerShell](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > Ha az igazolási házirendet a Azure PowerShellával konfigurálja, állítsa a paramétert a következőre: `Tee` `SgxEnclave` .
- [Gyors útmutató: Azure-igazolás beállítása az Azure CLI-vel](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > Ha az igazolási szabályzatot az Azure CLI-vel konfigurálja, állítsa a paramétert a következőre: `attestation-type` `SGX-IntelSDK` .

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>Az igazolási szabályzat igazolási URL-címének meghatározása

Az igazolási házirend konfigurálását követően meg kell osztania az igazolási URL-címet, hivatkozva a szabályzatra, a Always Encryptedt használó alkalmazások rendszergazdáit Azure SQL Database-ben. Az alkalmazás-rendszergazdáknak vagy/vagy az alkalmazás felhasználóinak az igazolás URL-címével kell konfigurálniuk az alkalmazásaikat, hogy a biztonságos enklávékat használó utasításokat futtassák.

### <a name="use-powershell-to-determine-the-attestation-url"></a>A tanúsítvány URL-címének meghatározása a PowerShell használatával

Az igazolási URL-cím meghatározásához használja az alábbi parancsfájlt:

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + "/attest/SgxEnclave"
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>A tanúsítvány URL-címének meghatározása a Azure Portal használatával

1. Az igazolási szolgáltató áttekintés ablaktáblájában másolja a tanúsító URI tulajdonság értékét a vágólapra. A tanúsító URI-nak így kell kinéznie: `https://MyAttestationProvider.us.attest.azure.net` .

2. Fűzze hozzá a következőt a tanúsító URI azonosítóhoz: `/attest/SgxEnclave` . 

Az eredményül kapott igazolási URL-címnek így kell kinéznie: `https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>Az Azure SQL logikai kiszolgáló hozzáférésének biztosítása az igazolási szolgáltatóhoz

Az igazolási munkafolyamat során az adatbázist tartalmazó Azure SQL logikai kiszolgáló meghívja az igazolási szolgáltatót az igazolási kérelem elküldéséhez. Ahhoz, hogy az Azure SQL logikai kiszolgáló igazoló kérelmeket küldjön, a kiszolgálónak rendelkeznie kell engedéllyel az `Microsoft.Attestation/attestationProviders/attestation/read` igazolási szolgáltatón végzett művelethez. Az engedély megadásának ajánlott módja a tanúsító szolgáltató rendszergazdája számára, hogy hozzárendelje a kiszolgáló Azure AD-identitását az igazolási szolgáltató vagy az azt tartalmazó erőforráscsoport tanúsítvány-olvasó szerepköréhez.

### <a name="use-azure-portal-to-assign-permission"></a>Engedélyek kiosztása Azure Portal használatával

Ahhoz, hogy egy Azure SQL-kiszolgáló identitását az igazolási szolgáltatóhoz társítsa a tanúsító olvasó szerepkörhöz, kövesse az [Azure-szerepkörök az Azure Portal használatával történő hozzárendelésének](../../role-based-access-control/role-assignments-portal.md)általános utasításait. A **szerepkör-hozzárendelés hozzáadása** panelen:

1. A **szerepkör** legördülő menüben válassza ki az **igazolási olvasó** szerepkört.
1. A **kiválasztás** mezőben adja meg annak az Azure SQL Servernek a nevét, amelyet meg szeretne keresni.

Példaként tekintse meg az alábbi képernyőképet.

![igazolási olvasó szerepkör-hozzárendelés](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> Ahhoz, hogy egy kiszolgáló megjelenjen a **szerepkör-hozzárendelés hozzáadása** panelen, a kiszolgálónak rendelkeznie kell Azure ad Identity Assigned – lásd a [követelményeket](#requirements).

### <a name="use-powershell-to-assign-permission"></a>Engedélyek kiosztása a PowerShell használatával

1. Keresse meg az Azure SQL logikai kiszolgálót.

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 
```
 
2. Rendelje hozzá a kiszolgálót az igazoló olvasó szerepkörhöz az igazolási szolgáltatót tartalmazó erőforráscsoport számára.

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

További információ: Azure- [szerepkörök Kiosztása Azure PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md#assign-role-examples).

## <a name="next-steps"></a>Következő lépések

- [A biztonságok enklávékkal ellátott Always Encrypted kulcsainak kezelése](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>Lásd még

- [Oktatóanyag: a Always Encrypted első lépései a biztonságos enklávékkal Azure SQL Database](always-encrypted-enclaves-getting-started.md)
