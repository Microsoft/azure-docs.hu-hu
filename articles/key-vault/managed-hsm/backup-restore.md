---
title: Teljes biztonsági mentés/visszaállítás és szelektív visszaállítás az Azure Managed HSM-hez
description: Ez a dokumentum a teljes biztonsági mentést és visszaállítást, valamint a szelektív visszaállítást ismerteti
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: e926dcd4b05d137c7927bdfe5221923d25d4670c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100093488"
---
# <a name="full-backup-and-restore"></a>Teljes biztonsági mentés és visszaállítás

> [!NOTE]
> Ez a funkció csak az erőforrás típusú felügyelt HSM esetében érhető el.

A felügyelt HSM támogatja a HSM teljes tartalmának teljes biztonsági mentését, beleértve az összes kulcsot, verziót, attribútumot, címkét és szerepkör-hozzárendelést. A biztonsági mentés a HSM biztonsági tartományához társított titkosítási kulcsokkal van titkosítva.

A biztonsági mentés adatsíkművelet. A biztonsági mentési műveletet kezdeményező hívónak rendelkeznie kell engedéllyel a dataAction **Microsoft. kulcstartó/managedHsm/Backup/Start/művelet** végrehajtásához.

Csak a következő beépített szerepkörök jogosultak teljes biztonsági mentés végrehajtására:
- Felügyeleti HSM rendszergazdája
- Felügyelt HSM biztonsági mentése

Teljes biztonsági mentés végrehajtásához meg kell adnia az alábbi adatokat:
- HSM neve vagy URL-címe
- Tárfiók neve
- Tárfiók Blob Storage-tárolója
- Storage-tároló SAS-jogkivonata engedélyekkel `crdw`

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>Teljes biztonsági mentés

A Backup egy hosszú ideig futó művelet, de azonnal visszaküldi a feladatot. A biztonsági mentési folyamat állapotát ennek a feladattípusnak a használatával tekintheti meg. A biztonsági mentési folyamat egy mappát hoz létre a kijelölt tárolóban a következő elnevezési mintával **`mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`** , ahol a HSM_NAME a felügyelt HSM biztonsági mentésének neve, amely éééé, hh, NN, hh, mm, mm, SS a Backup parancs kézhezvételének éve, hónap, dátum, óra, perc és másodperc.

Amíg a biztonsági mentés folyamatban van, előfordulhat, hogy a HSM nem működik teljes átviteli sebességgel, mivel egyes HSM-partíciók el lesznek foglalva a biztonsági mentési művelet végrehajtásával.

```azurecli-interactive
# time for 500 minutes later for SAS token expiry

end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Create a container

az storage container create --account-name  mhsmdemobackup --name mhsmdemobackupcontainer  --account-key $skey

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>Teljes visszaállítás

A teljes visszaállítással teljesen visszaállíthatja a HSM tartalmát egy korábbi biztonsági mentéssel, beleértve az összes kulcsot, verziót, attribútumot, címkét és szerepkör-hozzárendelést. A rendszer törli a HSM-ben jelenleg tárolt összes adatát, és a forrás biztonsági másolat létrehozásakor megegyező állapotba tér vissza.

> [!IMPORTANT]
> A teljes visszaállítás nagyon romboló és zavaró művelet. Ezért a művelet elvégzése előtt az elmúlt 30 percben kötelező befejezni a teljes biztonsági mentést `restore` .

A Restore egy adatsík-művelet. A visszaállítási művelet elindításához engedéllyel kell rendelkeznie a **Microsoft. kulcstartó/managedHsm/Restore/Start/Action** dataAction végrehajtásához. A forrás HSM, ahol a biztonsági mentés létrejött, és a cél HSM- **nek** , ahol a visszaállítást el kell végrehajtani, ugyanazzal a biztonsági tartománnyal kell rendelkeznie. További információ [a felügyelt HSM biztonsági tartományáról](security-domain.md).

Teljes visszaállítás végrehajtásához a következő információkat kell megadnia:
- HSM neve vagy URL-címe
- Tárfiók neve
- Storage-fiók blob-tárolója
- Storage-tároló SAS-jogkivonata engedélyekkel `rl`
- A tárolási tároló mappájának neve, ahol a forrás biztonsági másolata tárolva van

A Restore egy hosszú ideig futó művelet, amely azonnal visszaadja a feladatot. A visszaállítási folyamat állapotát ennek a feladattípusnak a használatával tekintheti meg. Ha a visszaállítási folyamat folyamatban van, a HSM helyreállító módba lép, és az összes adatsík parancs (kivéve a visszaállítási állapotot) le van tiltva.

```azurecli-interactive
#### time for 500 minutes later for SAS token expiry

end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="restore-hsm"></a>HSM visszaállítása

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>Következő lépések
- Lásd: [felügyelt HSM kezelése az Azure CLI használatával](key-management.md).
- További információ a [felügyelt HSM biztonsági tartományról](security-domain.md)
