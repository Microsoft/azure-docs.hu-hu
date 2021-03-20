---
title: Mi a teendő, ha olyan Azure-szolgáltatás megszakad, amely hatással van a felügyelt HSM-Azure Key Vaultra | Microsoft Docs
description: Ismerje meg, mi a teendő, ha a felügyelt HSM-et érintő Azure-szolgáltatás megszakad.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 8c284e9993002f6e05e41ca00d00b388feef8f82
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376003"
---
# <a name="managed-hsm-disaster-recovery"></a>Felügyelt HSM vész-helyreállítás

Előfordulhat, hogy létre kell hoznia egy pontos replikát a HSM-ből, ha az eredeti elveszett vagy nem érhető el az alábbi okok valamelyike miatt:

- Törölve lett, majd törlődik.
- A régió végzetes meghibásodása az összes tag partíció megsemmisítését eredményezte.

A HSM-példányt újra létrehozhatja azonos vagy más régióban, ha rendelkezik a következőkkel:
- A forrás HSM [biztonsági tartománya](security-domain.md) .
- A biztonsági tartományt titkosító titkos kulcsok (legalább kvórum száma).
- A legújabb teljes HSM [biztonsági mentés](backup-restore.md) a forrás HSM-ből.

A vész-helyreállítási eljárás lépései a következők:

1. Hozzon létre egy új HSM-példányt.
1. Aktiválja a "biztonsági tartomány helyreállítása" lehetőséget. A rendszer létrehoz egy új RSA kulcspárt (biztonsági tartományi Exchange-kulcsot) a biztonsági tartományok átviteléhez, és válaszként küldi el, amely SecurityDomainExchangeKey (nyilvános kulcs) lesz letöltve.
1. Hozza létre, majd töltse fel a "biztonsági tartomány átvitele" nevű fájlt. Szüksége lesz a biztonsági tartományt titkosító titkos kulcsokra. A titkos kulcsok helyileg vannak használatban, és soha nem kerülnek át a folyamatba.
1. Készítsen biztonsági másolatot az új HSM-ről. A visszaállítás előtt biztonsági másolatra van szükség, még akkor is, ha a HSM üres. A biztonsági mentések lehetővé teszik az egyszerű visszaállítást.
1. A legújabb HSM biztonsági mentés visszaállítása a forrás HSM-ből

Ezek a lépések lehetővé teszik a HSM tartalmának manuális replikálását egy másik régióba. A HSM neve (és a szolgáltatási végpont URI-ja) eltérő lesz, ezért előfordulhat, hogy módosítania kell az alkalmazás konfigurációját, hogy más helyről is használhassa ezeket a kulcsokat.

## <a name="create-a-new-managed-hsm"></a>Új felügyelt HSM létrehozása

`az keyvault create`Felügyelt HSM létrehozásához használja a parancsot. Ez a parancsfájl három kötelező paraméterrel rendelkezik: egy erőforráscsoport-név, egy HSM-név és a földrajzi hely.

Felügyelt HSM-erőforrás létrehozásához a következő bemeneteket kell megadnia:

- A HSM neve.
- Az az erőforráscsoport, amelyben a rendszer elhelyezi az előfizetésében.
- Az Azure-beli hely.
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

## <a name="activate-the-security-domain-recovery-mode"></a>A biztonsági tartomány helyreállítási módjának aktiválása

A normál létrehozási folyamat során ezen a ponton inicializáljuk és letöltünk egy új biztonsági tartományt. Mivel azonban a vész-helyreállítási eljárást hajtjuk végre, a HSM-t a biztonsági tartomány helyreállítási módjának megadására, valamint egy biztonsági tartományi Exchange-kulcs letöltésére kérik. A biztonsági tartományi Exchange-kulcs egy olyan RSA nyilvános kulcs, amelyet a rendszer a biztonsági tartomány titkosítására használ a HSM-hez való feltöltés előtt. A megfelelő titkos kulcs védett a HSM-ben, hogy a biztonsági tartomány tartalma biztonságban legyen az átvitel során.

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>Biztonsági tartomány feltöltése a cél HSM-be

Ehhez a lépéshez a következőkre lesz szüksége:
- Az előző lépésben letöltött biztonsági tartományi Exchange-kulcs.
- A forrás HSM biztonsági tartománya.
- Legalább a biztonsági tartomány titkosításához használt titkos kulcsok Kvórumának száma.

A `az keyvault security-domain upload` parancs a következő műveleteket hajtja végre:

- A forrás HSM biztonsági tartományának visszafejtése a megadott titkos kulcsokkal. 
- Hozzon létre egy biztonsági tartományi feltöltési blobot, amely az előző lépésben letöltött biztonsági tartományi Exchange-kulccsal lett titkosítva, majd
- Töltse fel a biztonsági tartomány feltöltési blobját a HSM-be a biztonsági tartomány helyreállításának befejezéséhez

Az alábbi példában a biztonsági tartományt használjuk a **ContosoMHSM**, a megfelelő titkos kulcsok közül 2, és fel kell tölteni a **ContosoMHSM2**-be, amely egy biztonsági tartomány fogadására vár. 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

Most mind a forrás HSM (ContosoMHSM), mind a cél HSM (ContosoMHSM2) ugyanazzal a biztonsági tartománnyal rendelkezik. Most már visszaállíthatjuk a forrás HSM teljes biztonsági mentését a cél HSM-be.

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>Biztonsági másolat létrehozása (visszaállítási pontként) az új HSM-hez

Mindig érdemes teljes biztonsági mentést készíteni a HSM teljes visszaállításának végrehajtása előtt, hogy egy visszaállítási ponttal rendelkezzen, ha valamilyen hiba történik a visszaállítás során.

HSM biztonsági mentés létrehozásához a következőkre lesz szüksége
- Egy Storage-fiók, amelyben a biztonsági mentés tárolva lesz
- Az ebben a Storage-fiókban található blob Storage-tároló, amelyben a biztonsági mentési folyamat létrehoz egy új mappát a titkosított biztonsági mentés tárolásához.

A `az keyvault backup` következő példában szereplő Storage-fiókhoz tartozó **mhsmbackupcontainer** használja a HSM biztonsági mentésre szolgáló parancsot a  tároló-tárolóban. Létrehozunk egy 30 percen belül lejáró SAS-jogkivonatot, és a felügyelt HSM-nek kell megírnia a biztonsági mentést.

```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
az storage container create --account-name  mhsmdemobackup --name mhsmbackupcontainer  --account-key $skey
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>Biztonsági másolat visszaállítása a forrás HSM-ből

Ehhez a lépéshez a következőkre lesz szüksége:

- A Storage-fiók, valamint a forrás HSM biztonsági másolatait tároló blob-tároló.
- A mappa neve, ahonnan vissza kívánja állítani a biztonsági mentést. Ha rendszeres biztonsági mentéseket hoz létre, akkor a tárolóban sok mappa található.


```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

Most elvégezte a teljes vész-helyreállítási folyamatot. A forrás HSM tartalmát a biztonsági másolat készítésekor a rendszer a cél HSM-be másolja, beleértve az összes kulcsot, verziót, attribútumot, címkét és szerepkör-hozzárendelést.

## <a name="next-steps"></a>Következő lépések

- További információ a biztonsági tartományról: [a felügyelt HSM biztonsági tartomány ismertetése](security-domain.md)
- A [felügyelt HSM ajánlott eljárásainak](best-practices.md) követése
