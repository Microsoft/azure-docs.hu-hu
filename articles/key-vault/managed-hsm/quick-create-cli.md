---
title: Gyors útmutató – Azure Managed HSM kiépítése és aktiválása
description: Gyors útmutató, amely bemutatja, hogyan lehet felügyelt HSM-t az Azure CLI használatával kiépíteni és aktiválni
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 86d0a336a7d3f5d12ed8e53de802616f839f9eba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91756816"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>Gyors útmutató: felügyelt HSM kiépítése és aktiválása az Azure CLI-vel

Azure Key Vault felügyelt HSM egy teljes körűen felügyelt, magas rendelkezésre állású, egybérlős, szabványoknak megfelelő felhőalapú szolgáltatás, amely lehetővé teszi a felhőalapú alkalmazások titkosítási kulcsainak védelmét, az **FIPS 140-2 3. szintű** hitelesített HSM használatával. A felügyelt HSM-vel kapcsolatos további információkért tekintse át az [áttekintést](overview.md). 

Ebben a rövid útmutatóban egy felügyelt HSM-et hoz létre és aktivál az Azure CLI-vel. Miután végzett ezzel, titkos kulcsot fog tárolni.

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

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *ContosoResourceGroup* nevű erőforráscsoportot a *eastus2* helyen.

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location eastus2
```

## <a name="create-a-managed-hsm"></a>Felügyelt HSM létrehozása

A felügyelt HSM létrehozása két lépésből álló folyamat:
1. Felügyelt HSM-erőforrás kiépítése.
1. Aktiválja a felügyelt HSM-et a biztonsági tartomány letöltésével.

### <a name="provision-a-managed-hsm"></a>Felügyelt HSM kiépítése

`az keyvault create`Felügyelt HSM létrehozásához használja a parancsot. Ez a parancsfájl három kötelező paraméterrel rendelkezik: egy erőforráscsoport-név, egy HSM-név és a földrajzi hely.

Felügyelt HSM-erőforrás létrehozásához a következő bemeneteket kell megadnia:
- Egy erőforráscsoport, amelyben a rendszer elhelyezi az előfizetésében.
- Azure-beli hely.
- A kezdeti rendszergazdák listája.

Az alábbi példa egy **ContosoMHSM** nevű HSM-et hoz létre az **USA 2. keleti** régiójában található erőforráscsoport- **ContosoResourceGroup**, amely az **aktuálisan bejelentkezett felhasználó** , mint az egyetlen rendszergazda.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> A Create parancs eltarthat néhány percig. A sikeres visszatérés után készen áll a HSM aktiválásához.

A parancs kimenete a létrehozott felügyelt HSM tulajdonságait jeleníti meg. A két legfontosabb tulajdonság:

* **név**: a példában a név ContosoMHSM. Ezt a nevet fogja használni a többi Key Vault parancshoz.
* **hsmUri**: a PÉLDÁBAN az URI a következő: " https://contosohsm.managedhsm.azure.net ." A HSM-et a REST API használó alkalmazásoknak ezt az URI-t kell használniuk.

Az Azure-fiókja már jogosult bármilyen művelet végrehajtására ezen a felügyelt HSM-ben. Még senki más nem rendelkezik jogosultsággal.

### <a name="activate-your-managed-hsm"></a>A felügyelt HSM aktiválása

Az összes adatsík-parancs le van tiltva, amíg be nem aktiválja a HSM-et. A kulcsok nem hozhatók létre és nem rendelhetők hozzá szerepkörök. Csak a létrehozási parancs során hozzárendelt kijelölt rendszergazdák aktiválják a HSM-et. A HSM aktiválásához le kell töltenie a [biztonsági tartományt](security-domain.md).

A HSM aktiválásához a következőkre lesz szüksége:
- Minimum 3 RSA-kulcs – pár (legfeljebb 10)
- A biztonsági tartomány (kvórum) visszafejtéséhez szükséges kulcsok minimális számának megadása

A HSM aktiválásához legalább 3 (legfeljebb 10) RSA nyilvános kulcsot kell küldenie a HSM-nek. A HSM titkosítja a biztonsági tartományt ezekkel a kulcsokkal, és visszaküldi azt. Miután a biztonsági tartomány letöltése sikeresen befejeződött, a HSM készen áll a használatra. Meg kell adnia a kvórumot is, amely a biztonsági tartomány visszafejtéséhez szükséges titkos kulcsok minimális száma.

Az alábbi példa azt mutatja be, hogyan használható a  `openssl` 3 önaláírt tanúsítvány létrehozásához.

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Hozza létre és tárolja az ebben a lépésben létrehozott RSA-kulcspár és biztonsági tartományi fájl biztonságos tartományát.

A `az keyvault security-domain download` parancs használatával töltse le a biztonsági tartományt, és aktiválja a felügyelt HSM-et. Az alábbi példa 3 RSA-kulcspárt használ (csak a parancshoz tartozó nyilvános kulcsok szükségesek), és a kvórumot a 2 értékre állítja.

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

A biztonsági tartományi fájlt és az RSA-kulcs párokat biztonságosan tárolja. Szüksége lesz rájuk a vész-helyreállításhoz vagy egy olyan felügyelt HSM létrehozásához, amely azonos biztonsági tartománnyal osztozik, így a kulcsok megoszthatók.

A biztonsági tartomány sikeres letöltése után a HSM aktív állapotban lesz, és készen áll a használatra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.

Ha már nincs rá szükség, az [az group delete](/cli/azure/group) paranccsal eltávolíthatja az erőforráscsoportot és az összes kapcsolódó erőforrást. Az erőforrásokat a következőképpen törölheti:

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és egy titkos kulcsot tárolt benne. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Managed HSM áttekintése – áttekintés](overview.md)
- Útmutató a [kulcsok felügyelt HSM-ben való kezeléséhez](key-management.md)
- A [felügyelt HSM ajánlott eljárásainak](best-practices.md) áttekintése
