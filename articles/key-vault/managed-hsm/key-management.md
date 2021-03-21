---
title: Kulcsok kezelése felügyelt HSM-Azure Key Vaultban | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan kezelhetők a kulcsok a felügyelt HSM-ben
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 8d0cbd35b53bc8460ac8a19e5197d1f560657263
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212042"
---
# <a name="manage-a-managed-hsm-using-the-azure-cli"></a>Felügyelt HSM kezelése az Azure CLI használatával

> [!NOTE]
> A Key Vault két típusú erőforrást támogat: a tárolókat és a felügyelt HSM. Ez a cikk a **felügyelt HSM**-ről szól. Ha szeretné megismerni a tárolók kezelését, tekintse meg [a Key Vault kezelése az Azure CLI használatával](../general/manage-with-cli2.md)című témakört.

A Managed HSM áttekintését lásd: [Mi a felügyelt HSM?](overview.md)

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések végrehajtásához a következő elemek szükségesek:

* Egy Microsoft Azure-előfizetésre. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial).
* Az Azure CLI verziója 2.12.0 vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.
* Felügyelt HSM az előfizetésében. Lásd [: gyors útmutató: felügyelt HSM kiépítése és aktiválása az Azure CLI használatával](quick-create-cli.md) a FELÜGYELt HSM üzembe helyezéséhez és aktiválásához.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha a parancssori felülettel szeretne bejelentkezni az Azure-ba, írja be a következőt:

```azurecli
az login
```

A parancssori felületről való bejelentkezéssel kapcsolatos további információkért lásd: [Bejelentkezés az Azure CLI](/cli/azure/authenticate-azure-cli) használatával

> [!NOTE]
> Az alábbi parancsok két használati módszert mutatnak be. Egy a **--HSM-Name** és **--Name** (a Key Name) paramétereknél és egy másik, a **--ID** paraméterrel, ahol a teljes URL-címet megadhatja, beleértve a kulcs nevét is, ahol szükséges. Az utóbbi módszer akkor hasznos, ha a hívó (felhasználó vagy alkalmazás) nem rendelkezik olvasási hozzáféréssel a vezérlési síkon, és csak korlátozott hozzáférés az adatsíkon.

## <a name="create-an-hsm-key"></a>HSM-kulcs létrehozása

`az keyvault key create`Kulcs létrehozásához használja a parancsot.

### <a name="create-an-rsa-key"></a>RSA-kulcs létrehozása

Az alábbi példa azt mutatja be, hogyan hozható létre egy 3072 bites **RSA** -kulcs, amely csak **wrapKey, unwrapKey** műveletekhez (--Ops) lesz használva. 


```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072

## OR
# Note the key name (myrsakey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072
```

Vegye figyelembe, hogy a `get` művelet csak a nyilvános kulcsot és a kulcs attribútumokat adja vissza. A titkos kulcsot nem küldi vissza (aszimmetrikus kulcs esetén vagy a kulcs anyaga (szimmetrikus kulcs esetén).

### <a name="create-an-ec-key"></a>EK-kulcs létrehozása

Az alábbi példa azt mutatja be, hogyan hozható létre olyan, P-256 görbével rendelkező **EK** -kulcs, amely csak **aláírási és ellenőrzési** műveletekhez használható (--Ops), és két címkével, **használattal** és **AppName** rendelkezik. A címkék segítségével további metaadatokat adhat hozzá a kulcshoz a nyomon követéshez és kezeléshez.

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256

## OR
# Note the key name (myec256key) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

### <a name="create-a-256-bit-symmetric-key"></a>256 bites szimmetrikus kulcs létrehozása

Az alábbi példa azt mutatja be, hogyan hozható létre egy 256 bites **szimmetrikus** kulcs, amely csak **titkosítási és visszafejtési** műveletekhez (--Ops) használható.

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myaeskey --ops encrypt decrypt  --tags --kty oct-HSM --size 256

## OR
# Note the key name (myaeskey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myaeskey --ops encrypt decrypt  --tags ‘usage=signing] appname=myapp’ --kty oct-HSM --size 256
```

## <a name="view-key-attributes-and-tags"></a>Fő attribútumok és címkék megtekintése

A `az keyvault key show` parancs használatával megtekintheti a kulcs attribútumait, verzióit és címkéit.

```azurecli-interactive
az keyvault key show --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key show --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey

```

## <a name="list-keys"></a>Kulcsok listázása

`az keyvault key list`A parancs használatával a felügyelt HSM-ben lévő összes kulcsot listázhatja.

```azurecli-interactive
az keyvault key list --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list --id https://ContosoMHSM.managedhsm.azure.net/

```

## <a name="delete-a-key"></a>Kulcs törlése

A `az keyvault key delete` parancs használatával törölheti a kulcsokat egy felügyelt HSM-ből. Vegye figyelembe, hogy a Soft delete mindig be van kapcsolva. Ezért a törölt kulcsok törölve maradnak, és csak akkor állíthatók helyre, ha a kulcs törlésére (véglegesen törölve) nem kerül sor a megőrzési napok számára.

```azurecli-interactive
az keyvault key delete --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key delete --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey
```

## <a name="list-deleted-keys"></a>Törölt kulcsok listázása

A `az keyvault key list-deleted` parancs használatával a felügyelt HSM-ben törölt állapotban lévő összes kulcsot listázhatja.

```azurecli-interactive
az keyvault key list-deleted --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list-deleted --id https://ContosoMHSM.managedhsm.azure.net/
```

## <a name="recover-undelete-a-deleted-key"></a>Törölt kulcs helyreállítása (törlésének visszavonása)

A `az keyvault key list-deleted` parancs használatával a felügyelt HSM-ben törölt állapotban lévő összes kulcsot listázhatja. Ha a--ID paraméter használatával kell helyreállítani (törölni) egy kulcsot a törölt kulcs helyreállítása közben, jegyezze fel a `recoveryId` parancsból beszerzett törölt kulcs értékét `az keyvault key list-deleted` .

```azurecli-interactive
az keyvault key recover --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey
```

## <a name="purge-permanently-delete-a-key"></a>Kulcs kiürítése (végleges törlése)

Használja `az keyvault key purge` a parancsot a kulcs kiürítéséhez (végleges törléséhez).

> [!NOTE]
> Ha a felügyelt HSM esetében engedélyezve van a védelem, a kiürítési művelet nem lesz engedélyezett. A kulcs automatikusan törlődik, ha a megőrzési időtartam betelt.

```azurecli-interactive
az keyvault key purge --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey

```

## <a name="create-a-single-key-backup"></a>Egyetlen kulcsos biztonsági másolat létrehozása

`az keyvault key backup`Kulcs biztonsági mentésének létrehozásához használja a következőt:. A biztonságimásolat-fájl egy titkosított blob, amely kriptográfiailag a forrás HSM biztonsági tartományához van kötve. Csak olyan HSM lehet visszaállítani, amelyek ugyanazt a biztonsági tartományt használják. További információ a [biztonsági tartományról](security-domain.md).

```azurecli-interactive
az keyvault key backup --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key backup --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey  --file myrsakey.backup

```

## <a name="restore-a-single-key-from-backup"></a>Egyetlen kulcs visszaállítása biztonsági másolatból

`az keyvault key restore`Egyetlen kulcs visszaállítására használható. A biztonsági másolat létrehozásához használt forrás HSM-nek ugyanazt a biztonsági tartományt kell megadnia, mint a kulcsot helyreállító cél HSM-nek.

> [!NOTE]
> A visszaállítás sikertelen lesz, ha az azonos nevű kulcs aktív vagy törölt állapotban van.

```azurecli-interactive
az keyvault key restore --hsm-name ContosoHSM --name myrsakey --file myrsakey.bakup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --file myrsakey.bakup

```

## <a name="import-a-key-from-a-file"></a>Kulcs importálása fájlból

A `az keyvault key import` parancs használatával importálhat egy kulcsot (csak RSA és EC) egy fájlból. A tanúsítványfájl titkos kulccsal kell rendelkeznie, és a PEM-kódolást kell használnia (az RFC [1421](https://tools.ietf.org/html/rfc1421), [1422](https://tools.ietf.org/html/rfc1422), [1423](https://tools.ietf.org/html/rfc1423), [1424](https://tools.ietf.org/html/rfc1424)) definiálva.

```azurecli-interactive
az keyvault key import --hsm-name ContosoHSM --name myrsakey --pem-file mycert.key --password 'mypassword'

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --pem-file mycert.key --password 'mypassword'
```

Kulcs importálása a helyszíni HSM-ből a felügyelt HSM-be: [HSM-védelemmel ellátott kulcsok importálása felügyelt HSM-be (BYOK)](hsm-protected-keys-byok.md)

## <a name="next-steps"></a>Következő lépések

- A Key Vault-parancsokkal kapcsolatos teljes Azure CLI-referenciáért lásd: [Key Vault CLI-hivatkozás](/cli/azure/keyvault).
- Programozási referenciák: [Azure Key Vault fejlesztői útmutató](../general/developers-guide.md)
- További információ a [felügyelt HSM szerepkör-kezelésről](role-management.md)
- További információ a [felügyelt HSM ajánlott eljárásairól](best-practices.md)
