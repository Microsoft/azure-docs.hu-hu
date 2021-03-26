---
title: Azure Key Vault kezelése a CLI-Azure Key Vault használatával | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan automatizálhatja Key Vault gyakori feladatait az Azure CLI használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: c084df60fa2293a614726be44669a1465b399d2c
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557207"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Key Vault kezelése az Azure CLI használatával 

Ez a cikk azt ismerteti, hogyan kezdheti meg a Azure Key Vault használatát az Azure CLI használatával.  A következő információkat tekintheti meg:

- Megerősített tároló létrehozása az Azure-ban
- Kulcs, titok vagy tanúsítvány hozzáadása a Key vaulthoz
- Alkalmazás regisztrálása a Azure Active Directory
- Kulcs vagy titkos kód használatának engedélyezése az alkalmazás számára
- A Key Vault speciális hozzáférési házirendjeinek beállítása
- Hardveres biztonsági modulok (HSM-EK) használata
- A Key Vault és a hozzá tartozó kulcsok és titkos kódok törlése
- Egyéb Azure platformfüggetlen parancssori felületi parancsok


Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Ez a cikk nem tartalmaz útmutatást arról, hogyan írhat az Azure-alkalmazást, hogy az egyik lépés tartalmazza, amely bemutatja, hogyan engedélyezheti az alkalmazások számára a kulcsok vagy titkos kulcsok használatát a kulcstartóban.
>

A Azure Key Vault áttekintése: [Mi az a Azure Key Vault?](overview.md)) Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő Azure CLI-parancsok használatához a következő elemek szükségesek:

* Egy Microsoft Azure-előfizetésre. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial).
* Az Azure Command-Line felületének 2,0-es vagy újabb verziója. A legújabb verzió telepítéséhez tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.
* Egy alkalmazás, amely a cikkben létrehozott kulcs vagy jelszó használatára lesz konfigurálva. Egy mintaalkalmazás elérhető a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=45343). Útmutatásért tekintse meg a mellékelt Readme fájlt.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Segítség az Azure platformfüggetlen Command-Line felületének használatában

Ez a cikk azt feltételezi, hogy már ismeri a parancssori felületet (bash, Terminal, parancssor).

A--help vagy a-h paraméter használatával megtekintheti az adott parancsok súgóját. Másik lehetőségként az Azure help [Command] [Options] formátum is használható. Ha kétségei vannak a parancshoz szükséges paraméterekkel kapcsolatban, tekintse meg a súgót. A következő parancsok például mind ugyanazt az információt adják vissza:

```azurecli
az account set --help
az account set -h
```

A következő cikkekben megismerheti az Azure platformfüggetlen Command-Line felületének Azure Resource Manager megismerését:

* [Telepítse az Azure CLI-t](/cli/azure/install-azure-cli)
* [Az Azure CLI használatának első lépései](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Megerősített tároló létrehozása az Azure-ban

A tárolók a hardveres biztonsági modulok által támogatott biztonságos tárolók. A tárolók a titkos alkalmazáskulcsok tárolásának központosításával csökkentik a biztonsági információk véletlen elvesztésének kockázatát. A kulcstartók a bennük tárolt tartalomhoz való hozzáférést vezérlik és naplózzák is. Az Azure Key Vault kezelni tudja a Transport Layer Security- (TLS-) tanúsítványok kérelmezését és megújítását, biztosítva a tanúsítvány életciklusának megbízható kezelési szolgáltatásához szükséges funkciókat. A következő lépésekben létre kell hoznia egy tárolót.

### <a name="connect-to-your-subscriptions"></a>Csatlakozás az előfizetésekhez

Interaktív bejelentkezéshez használja a következő parancsot:

```azurecli
az login
```
Ha szervezeti fiókkal szeretne bejelentkezni, adja meg a felhasználónevét és a jelszavát.

```azurecli
az login -u username@domain.com -p password
```

Ha egynél több előfizetéssel rendelkezik, és meg kell adnia, hogy melyiket szeretné használni, írja be a következőt a fiókhoz tartozó előfizetések megtekintéséhez:

```azurecli
az account list
```

Előfizetés-paraméterrel rendelkező előfizetést kell megadni.

```azurecli
az account set --subscription <subscription name or ID>
```

Az Azure platformfüggetlen Command-Line felületének konfigurálásával kapcsolatos további információkért lásd: az [Azure CLI telepítése](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Új erőforráscsoport létrehozása

Azure Resource Manager használatakor az összes kapcsolódó erőforrás egy erőforráscsoport belsejében jön létre. A kulcstárolót meglévő erőforráscsoporthoz is létrehozhatja. Ha új erőforráscsoportot szeretne használni, létrehozhat egy újat.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

Az első paraméter az erőforráscsoport neve, a második paraméter pedig a hely. Az összes lehetséges tárolóhely listájának beolvasása:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>A Key Vault erőforrás-szolgáltató regisztrálása

 Ha új kulcstartót próbál létrehozni, a következő hibaüzenet jelenhet meg: "az előfizetés nincs regisztrálva a Microsoft. Key Vault" névtér használatára. Ha ez az üzenet jelenik meg, ellenőrizze, hogy Key Vault erőforrás-szolgáltató regisztrálva van-e az előfizetésében. Ez a műveletet minden egyes előfizetés esetén csak egyszer kell elvégezni.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

`az keyvault create`Kulcstartó létrehozásához használja az parancsot. Ez a parancsfájl három kötelező paraméterrel rendelkezik: egy erőforráscsoport-név, egy kulcstartó neve és a földrajzi hely.

Ha a **ContosoKeyVault** nevű új tárolót szeretne létrehozni, akkor a **Kelet-Ázsia** helyen található erőforráscsoport- **ContosoResourceGroup** írja be a következőt: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

A parancs kimenete a létrehozott kulcstartó tulajdonságait jeleníti meg. A két legfontosabb tulajdonság:

* **név**: a példában a név ContosoKeyVault. Ezt a nevet fogja használni a többi Key Vault parancshoz.
* **vaultUri**: a PÉLDÁBAN az URI a (z https://contosokeyvault.vault.azure.net ). A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Azure-fiókja most már engedéllyel rendelkezik arra, hogy bármilyen műveletet végezzen ezen a kulcstartón. Még senki más nem rendelkezik jogosultsággal.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Kulcs, titok vagy tanúsítvány hozzáadása a Key vaulthoz

Ha azt szeretné, hogy a Azure Key Vault szoftveresen védett kulcsot hozzon létre, használja az `az key create` parancsot.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Ha van meglévő kulcsa egy. PEM-fájlban, feltöltheti Azure Key Vaultba. Dönthet úgy, hogy a kulcsot szoftverrel vagy HSM-védelemmel látja el. Ez a példa a. PEM-fájlból importálja a kulcsot, és a szoftverrel, a "hVFkk965BuUv" jelszó használatával gondoskodik a védelemről:

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Most már hivatkozhat arra a kulcsra, amelyet az Ön által létrehozott vagy a Azure Key Vaultba töltött fel az URI használatával. `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey`A használatával mindig megkapja az aktuális verziót. A megadott verzió beszerzéséhez használja a https://[kulcstartó-név]. Vault. Azure. net/Keys/[kulcsnév]/[Key-Unique-id] értéket. Például: `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87`. 

Adjon hozzá egy titkos kulcsot a tárolóhoz, amely egy SQLPassword nevű jelszó, amely a "hVFkk965BuUv" értékkel rendelkezik az Azure Key Vaults szolgáltatásban. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Hivatkozzon erre a jelszóra az URI használatával. A használatával **https://ContosoVault.vault.azure.net/secrets/SQLPassword** mindig megkapja a jelenlegi verziót, és a https://[kulcstartó-name]. Vault. Azure. net/Secret/[Secret-name]/[Secret-Unique-id] értéket a megadott verzió beszerzéséhez. Például: **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Tanúsítvány importálása a tárolóba a. PEM vagy a. pfx használatával.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Tekintse meg a létrehozott kulcsot, titkot vagy tanúsítványt:

* A kulcsok megtekintéséhez írja be a következőt: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* A titkok megtekintéséhez írja be a következőt: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* A tanúsítványok megtekintéséhez írja be a következőt: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Alkalmazás regisztrálása a Azure Active Directory

Ezt a lépést általában egy fejlesztő végzi egy másik számítógépről. Ez nem kifejezetten a Azure Key Vaultra vonatkozik, de itt is szerepel a tájékoztatásban. Az alkalmazás regisztrációjának befejezéséhez a fióknak, a tárolónak és az alkalmazásnak ugyanabban az Azure-címtárban kell lennie.

A kulcstartót használó alkalmazásoknak az Azure Active Directoryból származó jogkivonat használatával kell hitelesítést végezniük.  Az alkalmazás tulajdonosának regisztrálnia kell Azure Active Directory először. A regisztrációt követően az alkalmazás tulajdonosa az alábbi értékeket kapja:

- **Alkalmazás-azonosító** (más néven HRE ügyfél-azonosító vagy appID)
- egy **hitelesítési kulcsot** (más néven közös titkos kódot). 

Az alkalmazásnak mindkét értékkel rendelkeznie kell ahhoz, hogy Azure Active Directory-jogkivonatot kapjon. A jogkivonatok lekérésére konfigurált alkalmazások az alkalmazástól függenek. A [Key Vault-mintaalkalmazás](https://www.microsoft.com/download/details.aspx?id=45343) esetében az alkalmazás tulajdonosa adja meg ezeket az értékeket az app.config fájlban.

Az alkalmazások Azure Active Directorysal való regisztrálásának részletes lépéseiért tekintse át az [alkalmazások integrálása a Azure Active Directory](../../active-directory/develop/quickstart-register-app.md)használatával című cikket, és a [portálon hozzon létre egy Azure Active Directory alkalmazást és egy egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz](../../active-directory/develop/howto-create-service-principal-portal.md), és [hozzon létre egy Azure-szolgáltatást az Azure CLI-vel](/cli/azure/create-an-azure-service-principal-azure-cli).

Alkalmazás regisztrálása Azure Active Directoryban:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Kulcs vagy titkos kód használatának engedélyezése az alkalmazás számára

Ha engedélyezni szeretné, hogy az alkalmazás hozzáférhessen a kulcshoz vagy a titkos kulcshoz a tárolóban, használja az `az keyvault set-policy` parancsot.

Ha például a tár neve ContosoKeyVault, az alkalmazáshoz tartozik egy 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed-appID, és engedélyezni szeretné az alkalmazásnak, hogy visszafejtse és aláírja a kulcsokat a tárolóban, használja a következő parancsot:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Ha ugyanazt az alkalmazást szeretné engedélyezni a tárolóban található titkos kódok olvasásához, írja be a következő parancsot:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a> A Key Vault speciális hozzáférési házirendjeinek beállítása

A Key Vault speciális házirendjeinek engedélyezéséhez használja az [az kulcstartó Update](/cli/azure/keyvault#az-keyvault-update) lehetőséget.

 Key Vault engedélyezése a központi telepítéshez: lehetővé teszi, hogy a virtuális gépek a tárolóban titkos kulcsként tárolt tanúsítványokat kérjenek le.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Key Vault engedélyezése a lemezes titkosításhoz: kötelező, ha az Azure Disk Encryption-tárolót használja.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Key Vault engedélyezése a sablonok üzembe helyezéséhez: lehetővé teszi, hogy a Resource Manager beolvassa a titkos kulcsokat a tárból.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Hardveres biztonsági modulok (HSM-EK) használata

A kiegészítő garanciához a hardveres biztonsági modulok (HSM-EK) olyan kulcsait importálhatja vagy generálhatja, amelyek soha nem hagyják el a HSM-határt. A hardveres biztonsági modulok a 2. szintű FIPS 140-2 szerint vannak érvényesítve. Ha ez a követelmény nem vonatkozik Önre, ugorja át ezt a szakaszt, és folytassa a Kulcsartó és a hozzá tartozó kulcsok és titkos kódok törlése szakasszal.

HSM által védett kulcsok létrehozásához HSM által védett kulcsokat támogató tárolóra való előfizetéssel kell rendelkeznie.

A kulcstartó létrehozásakor adja hozzá az "SKU" paramétert:

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Ehhez a tárolóhoz szoftveresen védett (korábban bemutatva) és HSM által védett kulcsokat is hozzáadhat. HSM-védelemmel ellátott kulcs létrehozásához állítsa a cél paramétert a "HSM" értékre:

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

A következő parancs használatával importálhat egy kulcsot egy. PEM-fájlból a számítógépén. Ez a parancs a hardveres Key Vault szolgáltatás biztonsági moduljaiba importálja a kulcsot:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

A következő parancs importálja a "saját kulcs használata" (BYOK) csomagot. Ezzel a helyi HSM-ben hozhatja létre a kulcsot, majd helyezheti át a Key Vault szolgáltatás HSM-jeire anélkül, hogy a kulcs elhagyná a HSM határait:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

A BYOK-csomag létrehozásával kapcsolatos részletes utasításokért lásd: [HSM-Protected kulcsok használata Azure Key Vault használatával](../keys/hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>A Key Vault és a hozzá tartozó kulcsok és titkos kódok törlése

Ha már nincs szüksége a kulcstartóra és annak kulcsaira vagy titkaira, a Key vaultot a paranccsal törölheti `az keyvault delete` :

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Lehetősége van a teljes Azure-erőforráscsoport törlésére is, amely magában foglalja a kulcstartót és a csoport összes erőforrását:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Egyéb Azure platformfüggetlen parancssori felületi parancsok

Más parancsok, amelyek hasznosak lehetnek a Azure Key Vault kezeléséhez.

Ez a parancs felsorolja az összes kulcs és a kijelölt tulajdonságok táblázatos megjelenítését:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Ez a parancs a megadott kulcs tulajdonságainak teljes listáját jeleníti meg:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Ez a parancs felsorolja az összes titkos név és a kijelölt tulajdonságok táblázatos megjelenítését:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Íme egy példa egy adott kulcs eltávolítására:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Íme egy példa arra, hogyan lehet eltávolítani egy adott titkot:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Következő lépések

- A Key Vault-parancsokkal kapcsolatos teljes Azure CLI-referenciáért lásd: [Key Vault CLI-hivatkozás](/cli/azure/keyvault).

- Programozási referenciák: [Azure Key Vault fejlesztői útmutató](developers-guide.md)

- A Azure Key Vault-és HSM kapcsolatos információkért lásd: [HSM-Protected kulcsok használata Azure Key Vault használatával](../keys/hsm-protected-keys.md).
