---
title: Az ügyfél által felügyelt kulcsok használata a konfigurációs adatai titkosításához
description: A konfigurációs adatai titkosítása az ügyfél által felügyelt kulcsokkal
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 07/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46a1e99a7bc75efa85fcb5eff649c14af5abd17b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930500"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Az alkalmazás konfigurációs adatai titkosítása az ügyfél által felügyelt kulcsok használatával
Az Azure-alkalmazás konfigurációja [titkosítja a bizalmas adatokat a nyugalmi](../security/fundamentals/encryption-atrest.md)állapotban. Az ügyfél által felügyelt kulcsok használata fokozott adatvédelmet biztosít, mivel lehetővé teszi a titkosítási kulcsok kezelését.  A felügyelt kulcs titkosításának használatakor az alkalmazás konfigurációjában található összes bizalmas információ titkosítása egy felhasználó által megadott Azure Key Vault kulccsal történik.  Ez lehetővé teszi a titkosítási kulcs igény szerinti elforgatását.  Emellett lehetővé teszi az Azure-alkalmazások konfigurációjának bizalmas adatokhoz való hozzáférésének visszavonását azáltal, hogy visszavonja az alkalmazás konfigurációs példányának kulcshoz való hozzáférését.

## <a name="overview"></a>Áttekintés 
Az Azure-alkalmazás konfigurációja a Microsoft által biztosított 256 bites AES-titkosítási kulccsal titkosítja a bizalmas adatokat a nyugalmi állapotban. Minden alkalmazás-konfigurációs példány saját titkosítási kulccsal rendelkezik, amelyet a szolgáltatás kezel, és a bizalmas adatok titkosítására szolgál. A bizalmas adatok tartalmazzák a kulcs-érték párokban található értékeket.  Ha az ügyfél által felügyelt kulcs funkció engedélyezve van, az alkalmazás konfigurációja egy felügyelt identitást használ, amelyet az alkalmazás konfigurációs példányához rendeltek, hogy a hitelesítés a Azure Active Directory használatával történjen. A felügyelt identitás ezután meghívja a Azure Key Vault és becsomagolja az alkalmazás konfigurációs példányának titkosítási kulcsát. Ekkor a rendszer tárolja a burkolt titkosítási kulcsot, és a kicsomagolt titkosítási kulcsot egy órára gyorsítótárazza az alkalmazás konfigurációjában. Az alkalmazás konfigurációja óránként frissíti az alkalmazás-konfigurációs példány titkosítási kulcsának nem burkolt verzióját. Ez a szokásos üzemi körülmények között biztosítja a rendelkezésre állást. 

>[!IMPORTANT]
> Ha az alkalmazás-konfigurációs példányhoz rendelt identitás már nem jogosult a példány titkosítási kulcsának kicsomagolására, vagy ha a felügyelt kulcs véglegesen törlődik, akkor többé nem lehet visszafejteni az alkalmazás konfigurációs példányában tárolt bizalmas adatokat. Azure Key Vault [Soft delete](../key-vault/general/soft-delete-overview.md) függvényének használata csökkenti a titkosítási kulcs véletlen törlésének esélyét.

Ha a felhasználók engedélyezik az ügyfél által felügyelt kulcs funkcióját az Azure-alkalmazás konfigurációs példányán, akkor a szolgáltatás képes a bizalmas adatokhoz való hozzáférésre. A felügyelt kulcs a gyökér titkosítási kulcsaként szolgál. A felhasználó visszavonhatja az alkalmazás konfigurációs példányának hozzáférését a felügyelt kulcshoz a Key Vault hozzáférési házirendjének módosításával. A hozzáférés visszavonása után az alkalmazás konfigurációja elvész a felhasználói adat egy órán belüli visszafejtésének lehetősége. Ezen a ponton az alkalmazás konfigurációs példánya tiltja az összes hozzáférési kísérletet. Ez a helyzet helyreállítható azáltal, hogy ismét megadja a szolgáltatás hozzáférését a felügyelt kulcshoz.  Egy órán belül az alkalmazás konfigurációja visszafejtheti a felhasználói adatait, és normál körülmények között működhet.

>[!NOTE]
>A rendszer az összes Azure-alkalmazás konfigurációs adatkészletét egy elkülönített biztonsági mentésben akár 24 óráig tárolja. Ez magában foglalja a csomagolatlan titkosítási kulcsot is. Ezek az adatközpontok nem azonnal elérhetők a szolgáltatás vagy a szolgáltatás csapatának. Ha vészhelyzeti visszaállítást végez, az Azure-alkalmazás konfigurációja újra visszavonja magát a felügyelt kulcs adatainak megfelelően.

## <a name="requirements"></a>Követelmények
A következő összetevők szükségesek ahhoz, hogy sikeresen engedélyezzék az ügyfél által felügyelt kulcs használatát az Azure-alkalmazások konfigurálásához:
- Standard szintű Azure-alkalmazás konfigurációs példánya
- Azure Key Vault a Soft-delete és a Purge-Protection funkciók engedélyezve
- Egy RSA-vagy RSA-HSM-kulcs a Key Vaulton belül
    - A kulcs nem lehet lejárt, engedélyezve kell lennie, és a becsomagolási és a kicsomagolási képességeket is engedélyezni kell.

Miután konfigurálta ezeket az erőforrásokat, két lépéssel engedélyezheti az Azure-alkalmazások konfigurációját a Key Vault kulcs használatára:
1. Felügyelt identitás társítása az Azure app Configuration-példányhoz
2. Adja meg az identitást `GET` , `WRAP` és az `UNWRAP` engedélyeket a cél Key Vault hozzáférési házirendjében.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Az ügyfél által felügyelt kulcs titkosításának engedélyezése az Azure app Configuration-példányhoz
A kezdéshez szüksége lesz egy megfelelően konfigurált Azure app Configuration-példányra. Ha még nem rendelkezik elérhető alkalmazás-konfigurációs példánnyal, kövesse az alábbi rövid útmutatók egyikét:
- [ASP.NET Core-alkalmazás létrehozása az Azure app Configurationvel](quickstart-aspnet-core-app.md)
- [.NET Core-alkalmazás létrehozása az Azure app Configuration használatával](quickstart-dotnet-core-app.md)
- [.NET-keretrendszerbeli alkalmazás létrehozása az Azure app Configuration használatával](quickstart-dotnet-app.md)
- [Java Spring-alkalmazás létrehozása az Azure app Configuration szolgáltatással](quickstart-java-spring-app.md)

>[!TIP]
> A Azure Cloud Shell egy ingyenes interaktív felület, amellyel a cikkben ismertetett parancssori utasításokat futtathatja.  A közös Azure-eszközök előre telepítve vannak, beleértve a .NET Core SDK. Ha bejelentkezett az Azure-előfizetésbe, indítsa el a [Azure Cloud shellt](https://shell.azure.com) a shell.Azure.com webhelyről.  A Azure Cloud Shellről a [dokumentáció beolvasásával](../cloud-shell/overview.md) tájékozódhat bővebben

### <a name="create-and-configure-an-azure-key-vault"></a>Azure Key Vault létrehozása és konfigurálása
1. Hozzon létre egy Azure Key Vault az Azure CLI használatával.  Vegye figyelembe, hogy a `vault-name` és `resource-group-name` a felhasználó által megadott és egyedinek kell lennie.  `contoso-vault` `contoso-resource-group` Ezeket a példákat használjuk.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. A Key Vault törlésének és eltávolításának engedélyezése. Helyettesítse `contoso-vault` `contoso-resource-group` be az 1. lépésben létrehozott Key Vault () és erőforráscsoport () nevét.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Hozzon létre egy Key Vault kulcsot. Adjon meg egyedi `key-name` nevet ehhez a kulcshoz, és cserélje le az `contoso-vault` 1. lépésben létrehozott Key Vault () nevét. Válassza ki, hogy szeretné `RSA` -e vagy `RSA-HSM` titkosítani.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    A parancs kimenete a generált kulcs kulcs-AZONOSÍTÓját ("Kid") jeleníti meg.  Jegyezze fel a feladat későbbi részében használandó kulcs AZONOSÍTÓját.  A kulcs AZONOSÍTÓjának formátuma: `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}` .  A kulcs AZONOSÍTÓjának három fontos összetevője van:
    1. Key Vault URI: "https://{My Key Vault}. Vault. Azure. net
    1. Key Vault kulcsnév: {Key Name}
    1. Key Vault kulcs verziója: {Key Version}

1. Hozzon létre egy rendszerhez rendelt felügyelt identitást az Azure CLI használatával, és helyettesítse be az alkalmazás konfigurációs példányának nevét és az előző lépésekben használt erőforráscsoportot. A felügyelt identitást a felügyelt kulcs elérésére fogja használni a rendszer. Az `contoso-app-config` alkalmazás segítségével szemlélteti az alkalmazások konfigurációs példányának nevét:
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    A parancs kimenete tartalmazza a rendszerhez rendelt identitás elsődleges AZONOSÍTÓját ("principalId") és a bérlő AZONOSÍTÓját ("tenandId").  Ezek az azonosítók a felügyelt kulcshoz való hozzáférés biztosításához lesznek használva.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Az Azure app Configuration-példány felügyelt identitásának hozzáférést kell biztosítania a kulcshoz a kulcs érvényesítéséhez, titkosításához és visszafejtéséhez. Azon műveletek meghatározott csoportja, amelyekhez hozzáférésre van szükség, az alábbiakat tartalmazza: `GET` , `WRAP` , és `UNWRAP` a kulcsokhoz.  A hozzáférés megadása megköveteli az alkalmazás konfigurációs példányának felügyelt identitásának résztvevő-AZONOSÍTÓját. Ez az érték az előző lépésben kapott értéket. Az alábbiakban látható `contoso-principalId` . Engedély megadása a felügyelt kulcsnak a parancssor használatával:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Miután az Azure-alkalmazás konfigurációs példánya hozzáfér a felügyelt kulcshoz, az Azure CLI használatával engedélyezheti az ügyfél által felügyelt kulcs funkcióját a szolgáltatásban. A legfontosabb létrehozási lépések során rögzített következő tulajdonságokat hívja fel: `key name` `key vault URI` .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Az Azure-alkalmazás konfigurációs példánya mostantól a Azure Key Vaultban tárolt, ügyfél által felügyelt kulcs használatára van konfigurálva.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben az Azure-alkalmazás konfigurációs példányát az ügyfél által felügyelt kulcs használatára konfigurálta a titkosításhoz.  Ismerje meg, hogyan [integrálhatja szolgáltatását az Azure felügyelt identitásokkal](howto-integrate-azure-managed-service-identity.md).
