---
title: Azure Disk Encryption Linuxhoz
description: Linux Azure Disk Encryption virtuális gépre telepít egy virtuális gépre egy virtuálisgép-bővítmény használatával.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.date: 03/19/2020
ms.collection: linux
ms.openlocfilehash: 8c0f233c2eb154636d64f747bb43bd392295aa9b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792380"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption Linuxhoz (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Áttekintés

Azure Disk Encryption linuxos dm-crypt alrendszer használatával teljes lemeztitkosítást biztosít egyes [Azure Linux-disztribúciókhoz.](../linux/disk-encryption-overview.md)  Ez a megoldás integrálva van a Azure Key Vault a lemeztitkosítási kulcsok és titkos kulcsok kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

Az előfeltételek teljes listájáért tekintse meg a Linux rendszerű virtuális [Azure Disk Encryption való](../linux/disk-encryption-overview.md)telepítésével kapcsolatos szakaszt, pontosabban a következő szakaszokat:

- [Támogatott virtuális gépek és operációs rendszerek](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [További virtuálisgép-követelmények](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Hálózati követelmények](../linux/disk-encryption-overview.md#networking-requirements)
- [Titkosítási kulcs tárolási követelményei](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Bővítményséma

A bővítménysémának két verziója van a Azure Disk Encryption (ADE):
- v1.1 – Egy újabb ajánlott séma, amely nem Azure Active Directory (AAD) tulajdonságokat.
- 0.1-es verzió – Egy régebbi séma, amelyhez Azure Active Directory (AAD) tulajdonságokra van szükség. 

Célsémát úgy választhat ki, ha a tulajdonság értéke megegyezik a használni kívánt séma `typeHandlerVersion` verziójával.

### <a name="schema-v11-no-aad-recommended"></a>1.1-es séma: Nincs AAD (ajánlott)

Az 1.1-es séma használata ajánlott, és nem igényel Azure Active Directory (AAD) tulajdonságokat.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Séma v0.1: AAD-vel 

A 0.1 sémához és vagy `AADClientID` vagy `AADClientSecret` `AADClientCertificate` szükséges.

A `AADClientSecret` használatával:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

A `AADClientCertificate` használatával:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Tulajdonságértékek

| Name | Érték / Példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | dátum |
| közzétevő | Microsoft.Azure.Security | sztring |
| típus | AzureDiskEncryptionForLinux | sztring |
| typeHandlerVersion | 1.1, 0.1 | int |
| (0.1 séma) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0.1 séma) AADClientSecret (AADClientSecret) | jelszó | sztring |
| (0.1 séma) AADClientCertificate | Ujjlenyomat | sztring |
| (nem kötelező) (0.1 séma) Jelszót | jelszó | sztring |
| DiskFormatQuery | {"dev_path":","name":"","file_system":""} | JSON-szótár |
| EncryptionOperation (Titkosítási művelet) | EnableEncryption, EnableEncryptionFormatAll | sztring | 
| (nem kötelező – alapértelmezett RSA-OAEP ) KeyEncryptionAlgorithm | RSA-OAEP, RSA-OAEP-256, RSA1_5 | sztring |
| KeyVaultURL | url | sztring |
| KeyVaultResourceId | url | sztring |
| (nem kötelező) KeyEncryptionKeyURL | url | sztring |
| (nem kötelező) KekVaultResourceId | url | sztring |
| (nem kötelező) SequenceVersion | uniqueidentifier | sztring |
| VolumeType (Kötet típusa) | Operációs rendszer, Adatok, Mind | sztring |

## <a name="template-deployment"></a>Sablonalapú telepítés

Az 1.1-es séma alapján való sablontelepítésre példát az Azure gyorsindítási sablon [201-encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)(Azure gyorsindítási sablon) tartalmaz.

A 0.1-es séma alapján való sablontelepítésre példát a [201-encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)Azure gyorsindítási sablonban láthat.

>[!WARNING]
> - Ha korábban már használta Azure Disk Encryption Azure AD-val a virtuális gép titkosításához, akkor továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához.
> - Linux operációsrendszer-kötetek titkosítása esetén a virtuális gépet nem elérhetőnek kell tekinteni. Határozottan javasoljuk, hogy kerülje az SSH-bejelentkezéseket, amíg a titkosítás folyamatban van, hogy elkerülje a titkosítási folyamat során elérni szükséges nyitott fájlok blokkolásával kapcsolatos problémákat. A folyamat előrehaladásának ellenőrzéshez használja a [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell-parancsmagot vagy a [vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) CLI parancsot. Ez a folyamat várhatóan néhány órát fog igénybe venni egy 30 GB-os operációsrendszer-köteten, valamint további időt az adatkötetek titkosításához. Az adatmennyiség titkosítási ideje arányos az adatkötetek méretével és mennyiségével, kivéve, ha az összes titkosítási formátumot használja. 
> - A titkosítás letiltása Linux rendszerű virtuális gépeken csak adatkötetek esetén támogatott. Az adat- vagy operációsrendszer-kötetek nem támogatják, ha az operációs rendszer kötete titkosított. 

>[!NOTE]
> Ha a paraméter Értéke Mind, az adatlemezek csak akkor lesznek titkosítva, ha `VolumeType` megfelelően vannak csatlakoztatva.

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A hibaelhárításhoz tekintse meg a Azure Disk Encryption [útmutatóját.](../linux/disk-encryption-troubleshooting.md)

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, lépjen kapcsolatba az Azure-szakértőkkel az [MSDN Azure-on](https://azure.microsoft.com/support/community/)és a Stack Overflow fórumain. 

Alternatív megoldásként be is Azure-támogatás incidenst. A Támogatás [Azure-támogatás](https://azure.microsoft.com/support/options/) válassza a Támogatás lehetőséget. További információ a Azure ügyfélszolgálata [támogatással kapcsolatos gyakori Microsoft Azure tartalmaz.](https://azure.microsoft.com/support/faq/)

## <a name="next-steps"></a>Következő lépések

* További információ a virtuálisgép-bővítményekről: Virtuálisgép-bővítmények [és -funkciók Linux rendszeren.](features-linux.md)
* További információ a Linux Azure Disk Encryption való telepítésről: [Linux rendszerű virtuális gépek.](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)