---
title: Linux-Azure Disk Encryption
description: Üzembe helyezi Azure Disk Encryption Linux rendszerű virtuális gépre egy virtuálisgép-bővítmény használatával.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.date: 03/19/2020
ms.collection: linux
ms.openlocfilehash: 7c79391e3459804a4b5ce72c2230d17af3269641
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566260"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Linux-Azure Disk Encryption (Microsoft. Azure. Security. AzureDiskEncryptionForLinux)

## <a name="overview"></a>Áttekintés

Azure Disk Encryption kihasználja a Linux dm-crypt alrendszerét, hogy teljes lemezes titkosítást biztosítson az [Azure Linux-disztribúciók kiválasztása](../linux/disk-encryption-overview.md)során.  Ez a megoldás integrálva van Azure Key Vault a lemezes titkosítási kulcsok és titkos kódok kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

Az előfeltételek teljes listájáért lásd: [Azure Disk Encryption Linux rendszerű virtuális gépekhez](../linux/disk-encryption-overview.md), konkrétan a következő részekben:

- [Támogatott virtuális gépek és operációs rendszerek](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [További virtuális gépekre vonatkozó követelmények](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Hálózati követelmények](../linux/disk-encryption-overview.md#networking-requirements)
- [Titkosítási kulcs tárolási követelményei](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Kiterjesztési séma

A Azure Disk Encryption (ADE) bővítmény sémájának két verziója létezik:
- v 1.1 – egy újabb ajánlott séma, amely nem használ Azure Active Directory (HRE) tulajdonságokat.
- v 0.1 – egy régebbi séma, amelyhez Azure Active Directory (HRE) tulajdonság szükséges. 

A célként használandó séma kiválasztásához a `typeHandlerVersion` tulajdonságot a használni kívánt séma verziójával egyenlő értékre kell állítani.

### <a name="schema-v11-no-aad-recommended"></a>Séma v 1.1: nincs HRE (ajánlott)

A v 1.1 séma használata javasolt, és nem igényel Azure Active Directory (HRE) tulajdonságokat.

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


### <a name="schema-v01-with-aad"></a>A Schema v 0.1: a HRE 

Az 0,1 `AADClientID` -es séma használatához és a vagy a vagy a `AADClientSecret` `AADClientCertificate` .

Használat `AADClientSecret` :

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

Használat `AADClientCertificate` :

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

| Name | Érték/példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | dátum |
| közzétevő | Microsoft. Azure. Security | sztring |
| típus | AzureDiskEncryptionForLinux | sztring |
| typeHandlerVersion | 1,1, 0,1 | int |
| (0,1 séma) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0,1 séma) AADClientSecret | jelszó | sztring |
| (0,1 séma) AADClientCertificate | ujjlenyomat | sztring |
| választható (0,1 séma) Jelszót | jelszó | sztring |
| DiskFormatQuery | {"dev_path": "", "Name": "", "file_system": ""} | JSON-szótár |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | sztring | 
| (opcionális – alapértelmezett RSA-OAEP) KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | sztring |
| KeyVaultURL | url | sztring |
| KeyVaultResourceId | url | sztring |
| választható KeyEncryptionKeyURL | url | sztring |
| választható KekVaultResourceId | url | sztring |
| választható SequenceVersion | uniqueidentifier | sztring |
| VolumeType | Operációs rendszer, az összes | sztring |

## <a name="template-deployment"></a>Sablonalapú telepítés

Az 1.1-es séma alapján történő központi telepítésre példaként tekintse meg az Azure gyors üzembe helyezési sablon [201-encrypt-Running-Linux-VM-Without-HRE](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

A sablon v 0.1-es sémán alapuló központi telepítésére példaként tekintse meg az Azure gyors üzembe helyezési sablon [201-encrypt-Running-Linux-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)című témakört.

>[!WARNING]
> - Ha korábban már használta Azure Disk Encryption az Azure AD-vel egy virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához.
> - A Linux operációs rendszer köteteinek titkosításakor a virtuális gépet nem szabad megfontolni. Határozottan javasoljuk, hogy az SSH-bejelentkezések elkerülése érdekében a titkosítási folyamat során ne akadályozza meg az összes olyan megnyitott fájl blokkolását, amelyet a titkosítási folyamat során el kell érnie. A folyamat ellenőrzéséhez használja a [Get-AzVMDiskEncryptionStatus PowerShell-](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot vagy a [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI parancsot. Ez a folyamat várhatóan néhány órát is igénybe vehet egy 30 GB-OS kötetre, és további időt az adatkötetek titkosítására. Az adatmennyiség-titkosítási idő arányos lesz az adatkötetek méretétől és mennyiségétől, kivéve, ha az összes titkosítás formátuma beállítást használja. 
> - A Linux rendszerű virtuális gépek titkosításának letiltása csak az adatkötetek esetében támogatott. Ha az operációsrendszer-kötet titkosítva van, a rendszer nem támogatja az adatvagy operációsrendszer-köteteken. 

>[!NOTE]
> Ha a `VolumeType` paraméter értéke mind, az adatlemezek csak akkor lesznek titkosítva, ha megfelelően vannak csatlakoztatva.

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Hibaelhárításhoz tekintse meg a [Azure Disk Encryption hibaelhárítási útmutatót](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/community/)is. 

Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási szolgálatát](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Következő lépések

* További információ a virtuálisgép-bővítményekről: [virtuálisgép-bővítmények és-szolgáltatások Linux rendszerhez](features-linux.md).
* További információ a Linux Azure Disk Encryptionáról: [Linux Virtual Machines](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines).