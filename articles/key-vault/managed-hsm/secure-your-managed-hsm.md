---
title: Biztonságos hozzáférés felügyelt HSM-Azure Key Vault felügyelt HSM-hez
description: Ismerje meg, hogyan biztosíthat biztonságos hozzáférést a felügyelt HSM-hez az Azure RBAC és a felügyelt HSM helyi RBAC használatával
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 5a3fcc10f318f2a8065550a48eb2bfb4bbdd4915
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218400"
---
# <a name="secure-access-to-your-managed-hsms"></a>Biztonságos hozzáférés a felügyelt HSM

Azure Key Vault felügyelt HSM egy olyan felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsoknak. Mivel ezek az adatok bizalmasak és üzleti szempontból kritikus fontosságúak, biztonságos hozzáférést kell biztosítani a felügyelt HSM ahhoz, hogy csak a jogosult alkalmazások és felhasználók férhessenek hozzá. Ez a cikk áttekintést nyújt a felügyelt HSM hozzáférés-vezérlési modelljéről. Ismerteti a hitelesítést és az engedélyezést, valamint ismerteti a felügyelt HSM való hozzáférés biztonságossá tételét.

Ez az oktatóanyag egy egyszerű példát mutat be, amely bemutatja, hogyan lehet elkülöníteni a feladatokat és a hozzáférés-vezérlést az Azure RBAC és a felügyelt HSM helyi RBAC használatával. A felügyelt HSM hozzáférés-vezérlési modelljének megismeréséhez lásd: [felügyelt HSM hozzáférés-vezérlése](access-control.md) .

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

## <a name="example"></a>Példa

Ebben a példában egy olyan alkalmazást fejlesztünk, amely egy RSA 2 048 bites kulcsot használ az aláírási műveletekhez. Az alkalmazás egy [felügyelt identitással](../../active-directory/managed-identities-azure-resources/overview.md)rendelkező Azure-beli virtuális GÉPEN (VM) fut. Az aláíráshoz használt RSA-kulcsot is a felügyelt HSM tárolja.

A következő szerepköröket azonosította az alkalmazás felügyeletével, üzembe helyezésével és auditálásával kapcsolatban:

- **Biztonsági csapat**: a KSH (a biztonsági tisztviselő) vagy hasonló közreműködők irodájában dolgozó személyzet. A biztonsági csapat feladata a kulcsok megfelelő megóvása. Az adattitkosításhoz használt RSA-vagy EK-kulcsok az aláíráshoz, valamint az RSA-vagy AES-kulcsok.
- **Fejlesztők és operátorok**: az alkalmazást fejlesztő és az Azure-ban üzembe helyezett munkatársak. A csapat tagjai nem tartoznak a biztonsági személyzetbe. Nem férhetnek hozzá a bizalmas adatokhoz, például az RSA-kulcsokhoz. Csak az általuk telepített alkalmazások férhetnek hozzá ehhez a bizalmas adatokhoz.
- **Könyvvizsgálók**: Ez a szerepkör olyan közreműködők számára szól, akik nem tagjai a fejlesztési vagy általános informatikai munkatársainak. A biztonsági szabványoknak való megfelelés biztosítása érdekében áttekintik a tanúsítványok, kulcsok és titkok használatát és karbantartását.

Van egy másik szerepkör, amely az alkalmazás hatókörén kívül esik: az előfizetés (vagy erőforráscsoport) rendszergazdája. Az előfizetés-rendszergazda beállítja a kezdeti hozzáférési engedélyeket a biztonsági csapat számára. Hozzáférést biztosítanak a biztonsági csapathoz egy olyan erőforráscsoport használatával, amely az alkalmazás által igényelt erőforrásokkal rendelkezik.

A következő műveleteket kell engedélyeznie a szerepkörökhöz:

**Biztonsági csapat**
- Hozza létre a felügyelt HSM-et.
- A felügyelt HSM biztonsági tartomány letöltése (vész-helyreállításhoz)
- A naplózás bekapcsolása.
- Kulcsok generálása vagy importálása
- Hozza létre a felügyelt HSM biztonsági mentéseit a vész-helyreállításhoz.
- Állítsa be a felügyelt HSM helyi RBAC, hogy adott műveletekhez engedélyeket adjon a felhasználóknak és az alkalmazásoknak.
- A kulcsok rendszeres legördítése.

**Fejlesztők és üzemeltetők**
- Az aláíráshoz használt RSA-kulcs beszerzése (kulcs URI-ja) a biztonsági csapattól.
- Fejlesszen és telepítsen egy alkalmazást, amely programozott módon fér hozzá a kulcshoz.

**Ellenőrök**
- A kulcsok naprakészen tartása a kulcsok lejárati dátumának ellenőrzésével
- Szerepkör-hozzárendelések figyelése, hogy a kulcsok csak jogosult felhasználók vagy alkalmazások számára legyenek elérhetők
- Tekintse át a felügyelt HSM-naplókat, hogy megerősítse a kulcsok megfelelő használatát az adatbiztonsági szabványoknak megfelelően.

A következő táblázat összefoglalja a csoportokra és erőforrásokra vonatkozó szerepkör-hozzárendeléseket a felügyelt HSM eléréséhez.

| Szerepkör | Felügyeleti sík szerepkör | Adatsík szerepkör |
| --- | --- | --- |
| Biztonsági csapat | Felügyelt HSM-közreműködő | Felügyelt HSM-rendszergazda |
| Fejlesztők és üzemeltetők | Nincs | Nincs |
| Ellenőrök | Nincs | Felügyelt HSM titkosítási auditor |
| Az alkalmazás által használt virtuális gép felügyelt azonosítása| Nincs | Felügyelt HSM kriptográfiai felhasználó |
| Az alkalmazás által használt Storage-fiók felügyelt identitása| Nincs| Felügyelt HSM szolgáltatás titkosítása |

A három csapat szerepkörhöz a felügyelt HSM-engedélyek mellett más erőforrásokhoz is hozzá kell férnie. A virtuális gépek (vagy a Azure App Service Web Apps funkciójának üzembe helyezéséhez) a fejlesztőknek és a kezelőknek `Contributor` hozzá kell férniük az ilyen típusú erőforrásokhoz. A könyvvizsgálóknak olvasási hozzáféréssel kell rendelkezniük ahhoz a Storage-fiókhoz, amelyben a felügyelt HSM-naplókat tárolják.

A felügyeleti sík szerepköreinek (Azure RBAC) hozzárendeléséhez használhatja Azure Portal vagy bármely más felügyeleti felületet, például az Azure CLI-t vagy a Azure PowerShell. A felügyelt HSM-adatsík szerepkörök hozzárendeléséhez az Azure CLI-t kell használnia.

Az ebben a szakaszban található Azure CLI-kódrészletek a következő feltételezésekkel vannak összeépítve:

- A Azure Active Directory rendszergazdája olyan biztonsági csoportokat hozott létre, amelyek a következő három szerepkört jelölik: contoso Security Team, contoso app DevOps és contoso app Auditors. A rendszergazda hozzáadta a felhasználókat a saját csoportjaihoz.
- Az összes erőforrás a **ContosoAppRG** erőforráscsoport területén található.
- A felügyelt HSM-naplókat a **contosologstorage** Storage-fiók tárolja.
- A **ContosoMHSM** Managed HSM és a **contosologstorage** Storage-fiók ugyanazon az Azure-helyen található.

Az előfizetés-rendszergazda hozzárendeli a `Managed HSM Contributor` szerepkört a biztonsági csapathoz. Ez a szerepkör lehetővé teszi, hogy a biztonsági csapat felügyelt meglévő felügyelt HSM kezelje, és újakat hozzon létre. Ha vannak meglévő felügyelt HSM, hozzá kell rendelni a "felügyelt HSM rendszergazda" szerepkört, hogy kezelni tudják őket.

```azurecli-interactive
# This role assignment allows Contoso Security Team to create new Managed HSMs
az role assignment create --assignee-object-id $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --assignee-principal-type Group --role "Managed HSM Contributor"

# This role assignment allows Contoso Security Team to become administrator of existing managed HSM
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --scope / --role "Managed HSM Administrator"
```

A biztonsági csapat beállítja a naplózást, és hozzárendeli a szerepköröket a könyvvizsgálók és a VM-alkalmazás számára.

```azurecli-interactive
# Enable logging
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name contosologstorage --query id -o tsv)
az monitor diagnostic-settings create --name MHSM-Diagnostics --resource $hsmresource --logs    '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource

# Assign the "Crypto Auditor" role to Contoso App Auditors group. It only allows them to read.
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso App Auditors' --query 'objectId' -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Grant the "Crypto User" role to the VM's managed identity. It allows to create and use keys. 
# However it cannot permanently delete (purge) keys
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az vm identity show --name "vmname" --resource-group "ContosoAppRG" --query objectId -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Assign "Managed HSM Crypto Service Encryption" role to the Storage account ID
storage_account_principal=$(az storage account show --id $storageresource --query identity.principalId -o tsv)
# (if no identity exists), then assign a new one
[ "$storage_account_principal" ] || storage_account_principal=$(az storage account update --assign-identity --id $storageresource)

az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Service Encryption" --assignee $storage_account_principal
```

Ez az oktatóanyag csak a legtöbb esetben a hozzáférés-vezérléssel kapcsolatos műveleteket jeleníti meg. Az alkalmazás üzembe helyezésével kapcsolatos egyéb műveletek és műveletek a virtuális gépen, az ügyfél által felügyelt kulccsal való titkosítás bekapcsolása egy Storage-fiók esetében, a felügyelt HSM létrehozása nem jelenik meg itt, hogy a példa a hozzáférés-vezérlésre és a szerepkör-felügyeletre koncentráljon.

Példánkban egy egyszerű forgatókönyvet ismertetünk. A valós életbeli forgatókönyvek összetettebbek lehetnek. Az engedélyeket a Key Vault igényei szerint módosíthatja. Feltételezzük, hogy a biztonsági csapat biztosítja a kulcs-és titkos referenciákat (URI-k és ujjlenyomatai megfelelnek), amelyeket az DevOps munkatársai használnak az alkalmazásaikban. A fejlesztőknek és az operátoroknak nincs szükségük adatsíkon való hozzáférésre. A Key Vault biztonságossá tételére koncentrálunk. [A virtuális gépek, a](https://azure.microsoft.com/services/virtual-machines/security/) [Storage-fiókok](../../storage/blobs/security-recommendations.md)és az egyéb Azure-erőforrások biztonságossá tételéhez hasonló szempontokat kell figyelembe venni.

## <a name="resources"></a>Források

- [Az Azure RBAC dokumentációja](../../role-based-access-control/overview.md)
- [Azure RBAC: beépített szerepkörök](../../role-based-access-control/built-in-roles.md)
- [Azure-RBAC kezelése az Azure CLI-vel](../../role-based-access-control/role-assignments-cli.md)

## <a name="next-steps"></a>Következő lépések

Az első lépéseket ismertető oktatóanyagért lásd: [Mi a felügyelt HSM?](overview.md).

A felügyelt HSM-naplózás használati naplózásával kapcsolatos további információkért lásd: [felügyelt HSM naplózása](logging.md).
