---
title: Az Azure Key Vault kezelése a CLI használatával – Azure Key Vault | Microsoft Docs
description: Ebből a cikkből a gyakori feladatokat automatizálhatja a Key Vault Az Azure CLI használatával
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0b29b668b21c375dd1202652b5093526f648c300
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749599"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Az Key Vault kezelése az Azure CLI használatával 

Ez a cikk bemutatja, hogyan kezdik el a Azure Key Vault az Azure CLI használatával.  A következő információk adatokat láthatja:

- Lekért tároló (tároló) létrehozása az Azure-ban
- Kulcs, titkos kulcs vagy tanúsítvány hozzáadása a kulcstartóhoz
- Alkalmazás regisztrálása a Azure Active Directory
- Egy alkalmazás kulcs vagy titkos kulcs használatának lehetővé való használata
- Kulcstartó speciális hozzáférési szabályzatának beállítása
- Hardveres biztonsági modulok (HSM-ek) használata
- A kulcstartó és a hozzá tartozó kulcsok és titkos kulcsok törlése
- Különböző Azure platformfüggetlen parancssori felületi parancsok


Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Ez a cikk nem tartalmaz utasításokat arra az Azure-alkalmazás megírásához, amelyet az egyik lépés tartalmaz, és amely bemutatja, hogyan engedélyezheti egy alkalmazás számára egy kulcs vagy titkos kulcs használatát a kulcstartóban.
>

A jelentés áttekintését Azure Key Vault Mi az a [Azure Key Vault?](overview.md)) Ha nem rendelkezik Azure-előfizetéssel, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) kezdés előtt hozzon létre egy ingyenes fiókot.

## <a name="prerequisites"></a>Előfeltételek

A cikkben található Azure CLI-parancsok csak a következő elemek használatával használhatók:

* Egy Microsoft Azure-előfizetésre. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial).
* Az Azure Command-Line Interface 2.0-s vagy újabb verziója. A legújabb verzió telepítéséhez lásd: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)
* Egy alkalmazás, amely a cikkben létrehozott kulcs vagy jelszó használatára lesz konfigurálva. Egy mintaalkalmazás elérhető a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=45343). Útmutatásért tekintse meg a mellékelt readme fájlt.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Segítség az Azure platformfüggetlen Command-Line felületén

Ez a cikk feltételezi, hogy ismeri a parancssori felületet (Bash, Terminál, Parancssor).

A --help vagy -h paraméterrel adott parancsok súgóját lehet megtekinteni. Másik lehetőségként az Azure Súgó [parancs] [beállítások] formátuma is használható. Ha nem biztos a parancsokhoz szükséges paraméterekben, tekintse meg a súgót. Az alábbi parancsok például ugyanazt az információt adhatja vissza:

```azurecli
az account set --help
az account set -h
```

A következő cikkekből megismerheti az Azure platformfüggetlen Azure Resource Manager felületének Command-Line való használatot:

* [Telepítse az Azure CLI-t](/cli/azure/install-azure-cli)
* [Az Azure CLI használatának első lépései](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Megkétesedő tároló (tároló) létrehozása az Azure-ban

A tárolók hardveres biztonsági modulokkal védett tárolók. A tárolók a titkos alkalmazáskulcsok tárolásának központosításával csökkentik a biztonsági információk véletlen elvesztésének kockázatát. A kulcstartók a bennük tárolt tartalomhoz való hozzáférést vezérlik és naplózzák is. Az Azure Key Vault kezelni tudja a Transport Layer Security- (TLS-) tanúsítványok kérelmezését és megújítását, biztosítva a tanúsítvány életciklusának megbízható kezelési szolgáltatásához szükséges funkciókat. A következő lépésekben létre fog hozni egy tárolót.

### <a name="connect-to-your-subscriptions"></a>Csatlakozás az előfizetésekhez

Az interaktív bejelentkezéshez használja a következő parancsot:

```azurecli
az login
```
Szervezeti fiókkal való bejelentkezéshez meg kell adni a felhasználónevét és jelszavát.

```azurecli
az login -u username@domain.com -p password
```

Ha egynél több előfizetéssel rendelkezik, és meg kell adnia, hogy melyiket használja, írja be a következőt a fiókjához használt előfizetések lásd:

```azurecli
az account list
```

Adjon meg egy előfizetést az előfizetési paraméterrel.

```azurecli
az account set --subscription <subscription name or ID>
```

További információ az Azure platformfüggetlen Command-Line felületének konfigurálásával kapcsolatban: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

### <a name="create-a-new-resource-group"></a>Új erőforráscsoport létrehozása

A Azure Resource Manager minden kapcsolódó erőforrás egy erőforráscsoporton belül jön létre. Létrehozhat egy kulcstartót egy meglévő erőforráscsoportban. Ha új erőforráscsoportot szeretne használni, létrehozhat egy újat.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

Az első paraméter az erőforráscsoport neve, a második paraméter pedig a hely. Az összes lehetséges hely listájának leához írja be a következőt:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>A Key Vault erőforrás-szolgáltató regisztrálása

 Amikor megpróbál létrehozni egy új kulcstartót, a "The subscription is not registered to use namespace 'Microsoft.KeyVault' (Az előfizetés nincs regisztrálva a Microsoft.KeyVault névtér használatára) hibaüzenet jelenhet meg. Ha megjelenik ez az üzenet, győződjön meg arról, Key Vault erőforrás-szolgáltató regisztrálva van az előfizetésben. Ez a műveletet minden egyes előfizetés esetén csak egyszer kell elvégezni.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

A `az keyvault create` paranccsal hozzon létre egy kulcstartót. Ez a szkript három kötelező paramétert használ: az erőforráscsoport nevét, a kulcstartó nevét és a földrajzi helyet.

A **ContosoResourceGroup** erőforráscsoportban egy **ContosoKeyVault** nevű új tároló létrehozásához írja be a **következőt Kelet-Ázsia** helyen: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

A parancs kimenete a létrehozott kulcstartó tulajdonságait jeleníti meg. A két legfontosabb tulajdonság:

* **name**: A példában a név ContosoKeyVault. Ezt a nevet fogja használni más Key Vault parancsokhoz.
* **vaultUri:** A példában az URI a https://contosokeyvault.vault.azure.net következő: . A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Azure-fiókja most már engedéllyel rendelkezik arra, hogy bármilyen műveletet végezzen ezen a kulcstartón. Egyelőre senki más nem jogosult erre.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Kulcs, titkos kulcs vagy tanúsítvány hozzáadása a kulcstartóhoz

Ha szoftveres Azure Key Vault szeretne létrehozni, használja az `az key create` parancsot.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Ha már van egy .pem-fájlban lévő kulcsa, feltöltheti a Azure Key Vault. Választhat, hogy szoftverrel vagy HSM-sel védi a kulcsot. Ez a példa importálja a kulcsot a .pem fájlból, és szoftverrel védi a "hVFkk965BuUv" jelszóval:

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Most már hivatkozhat a létrehozott vagy a Azure Key Vault feltöltött kulcsra annak URI-ján. Mindig `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey` az aktuális verziót az használatával lehet lekérte. A https:// a[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] paraméter használatával szerezze be ezt a verziót. Például: `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87`. 

Adjon hozzá egy titkos kulcsot a tárolóhoz, amely egy SQLPassword nevű jelszó, és amelynek értéke "hVFkk965BuUv" az Azure Key Vaultban. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Hivatkozhat erre a jelszóra az URI-ját használva. A használatával mindig lekérte az aktuális verziót, a **https://ContosoVault.vault.azure.net/secrets/SQLPassword** https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] használatával pedig lekérte ezt a verziót. Például: **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Tanúsítvány importálása a tárolóba .pem vagy .pfx használatával.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Nézzük meg a létrehozott kulcsot, titkos kulcsot vagy tanúsítványt:

* A kulcsok megtekintéséhez írja be a következőt: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* A titkos kulcsok megtekintéséhez írja be a következőt: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* A tanúsítványok megtekintéséhez írja be a következőt: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Alkalmazás regisztrálása a Azure Active Directory

Ezt a lépést általában egy fejlesztő végzi egy másik számítógépről. Nem csak a Azure Key Vault, hanem a tudatosság érdekében itt szerepel. Az alkalmazásregisztráció befejezéséhez a fióknak, a tárolónak és az alkalmazásnak ugyanabban az Azure-címtárban kell lennie.

A kulcstartót használó alkalmazásoknak az Azure Active Directoryból származó jogkivonat használatával kell hitelesítést végezniük.  Az alkalmazás tulajdonosának először regisztrálnia kell Azure Active Directory alkalmazásban. A regisztrációt követően az alkalmazás tulajdonosa az alábbi értékeket kapja:

- Egy **alkalmazásazonosító** (más néven AAD-ügyfélazonosító vagy appID)
- egy **hitelesítési kulcsot** (más néven közös titkos kódot). 

Az alkalmazásnak mindkét értékkel rendelkeznie kell ahhoz, hogy Azure Active Directory-jogkivonatot kapjon. A jogkivonatok lekért alkalmazásra való konfigurálása az alkalmazástól függ. A [Key Vault-mintaalkalmazás](https://www.microsoft.com/download/details.aspx?id=45343) esetében az alkalmazás tulajdonosa adja meg ezeket az értékeket az app.config fájlban.

Az alkalmazások Azure Active Directory-ban való regisztrálásának részletes lépéseit az Alkalmazások integrálása az [Azure Active Directory-val](../../active-directory/develop/quickstart-register-app.md)című cikkekben, a portálon hozhat létre [egy Azure Active Directory-alkalmazás](../../active-directory/develop/howto-create-service-principal-portal.md)és -szolgáltatásnév létrehozásához, amely hozzáférhet az erőforrásokhoz, valamint azure-beli szolgáltatásnév létrehozása az [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli)használatával.

Alkalmazás regisztrálása a Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Egy alkalmazás kulcs vagy titkos kulcs használatának lehetővé való használata

Ha engedélyeznie kell, hogy az alkalmazás hozzáférjen a kulcshoz vagy titkos kulcshoz a tárolóban, használja az `az keyvault set-policy` parancsot.

Ha például a tároló neve ContosoKeyVault, az alkalmazás alkalmazásazonosítója 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, és engedélyezni szeretné, hogy az alkalmazás visszafejtsen és jelentkezzen be a tárolóban található kulcsokkal, használja a következő parancsot:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Ha engedélyeznie kell, hogy ugyanaz az alkalmazás beolvassa a titkos kulcsokat a tárolóban, írja be a következő parancsot:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a> A Key Vault speciális hozzáférési szabályzatának beállítása

Az [az keyvault update parancs használatával](/cli/azure/keyvault#az-keyvault-update) engedélyezheti a speciális szabályzatokat a kulcstartóhoz.

 Az Key Vault engedélyezése: Engedélyezi a virtuális gépek számára a titkos kulcsként tárolt tanúsítványok lekérését a tárolóból.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

A Key Vault engedélyezése a lemeztitkosításhoz: Kötelező, ha a tárolót az Azure Disk Encryption használatára használja.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

A Key Vault engedélyezése sablontelepítéshez: Lehetővé Resource Manager a titkos kulcsok lekérését a tárolóból.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Hardveres biztonsági modulok (HSM-ek) használata

A nagyobb biztonság érdekében olyan hardveres biztonsági modulokat (HSM-eket) importálhat vagy hozhat létre, amelyek soha nem hagyják el a HSM határait. A hardveres biztonsági modulok a 2. szintű FIPS 140-2 szerint vannak érvényesítve. Ha ez a követelmény nem vonatkozik Önre, ugorja át ezt a szakaszt, és folytassa a Kulcsartó és a hozzá tartozó kulcsok és titkos kódok törlése szakasszal.

HSM által védett kulcsok létrehozásához HSM által védett kulcsokat támogató tárolóra való előfizetéssel kell rendelkeznie.

A keyvault létrehozásakor adja hozzá az "sku" paramétert:

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Ehhez a tárolóhoz szoftveresen védett (korábban bemutatva) és HSM által védett kulcsokat is hozzáadhat. HSM által védett kulcs létrehozásához állítsa a Destination paramétert a "HSM" (HSM) paraméterre:

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

A következő paranccsal importálhat egy kulcsot egy .pem fájlból a számítógépen. Ez a parancs a hardveres Key Vault szolgáltatás biztonsági moduljaiba importálja a kulcsot:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

A következő parancs egy "saját kulcs használata" (BYOK) csomagot importál. Ezzel a helyi HSM-ben hozhatja létre a kulcsot, majd helyezheti át a Key Vault szolgáltatás HSM-jeire anélkül, hogy a kulcs elhagyná a HSM határait:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

A BYOK-csomag létrehozásáról a How [to use HSM-Protected Keys with Azure Key Vault](../keys/hsm-protected-keys.md)(A kulcskulcsok használata a Azure Key Vault.

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>A kulcstartó és a hozzá tartozó kulcsok és titkos kulcsok törlése

Ha már nincs szüksége a kulcstartóra és annak kulcsára vagy titkos kulcsára, az paranccsal törölheti `az keyvault delete` a kulcstartót:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Lehetősége van a teljes Azure-erőforráscsoport törlésére is, amely magában foglalja a kulcstartót és a csoport összes erőforrását:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Különböző Azure platformfüggetlen parancssori felületi parancsok

Egyéb parancsok, amelyek hasznosak lehetnek a Azure Key Vault.

Ez a parancs felsorolja az összes kulcs és a kiválasztott tulajdonságok táblázatos megjelenítését:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Ez a parancs a megadott kulcs tulajdonságainak teljes listáját jeleníti meg:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Ez a parancs táblázatos listában jeleníti meg az összes titkos nevet és a kiválasztott tulajdonságokat:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Az alábbi példa egy adott kulcs eltávolítására mutat be példát:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Az alábbi példa egy adott titkos gombra mutat be példát:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Következő lépések

- A Key Vault-parancsok teljes Azure CLI-referenciáját lásd: [Key Vault CLI-referencia.](/cli/azure/keyvault)

- Programozási referenciákért tekintse meg a Azure Key Vault [fejlesztői útmutatóját](developers-guide.md)

- További információ a Azure Key Vault HSM-ekkel kapcsolatban: [How to use HSM-Protected Keys with Azure Key Vault](../keys/hsm-protected-keys.md).
