---
title: Windows Azure Disk Encryption
description: Azure Disk Encryption üzembe helyezése egy Windows rendszerű virtuális gépen a virtuálisgép-bővítmény használatával.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.collection: windows
ms.date: 03/19/2020
ms.openlocfilehash: 10268f8041f21f74e8ebcfaee41d207a53618260
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566243"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Windows Azure Disk Encryption (Microsoft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Áttekintés

Azure Disk Encryption a BitLockert a Windows rendszerű Azure-beli virtuális gépek teljes lemezes titkosításának biztosítására használja.  Ez a megoldás integrálva van Azure Key Vault a lemez titkosítási kulcsainak és a titkos kulcsoknak a Key Vault-előfizetésben való kezeléséhez. 

## <a name="prerequisites"></a>Előfeltételek

Az előfeltételek teljes listájáért lásd: [Azure Disk Encryption Windows rendszerű virtuális gépekhez](../windows/disk-encryption-overview.md), konkrétan a következő részekben:

- [Támogatott virtuális gépek és operációs rendszerek](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Hálózati követelmények](../windows/disk-encryption-overview.md#networking-requirements)
- [Csoportházirend követelmények](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Kiterjesztési séma

A Azure Disk Encryption (ADE) bővítmény sémájának két verziója létezik:
- v 2.2 – egy újabb ajánlott séma, amely nem használ Azure Active Directory (HRE) tulajdonságokat.
- v 1.1 – egy régebbi séma, amelyhez Azure Active Directory (HRE) tulajdonság szükséges. 

A célként használandó séma kiválasztásához a `typeHandlerVersion` tulajdonságot a használni kívánt séma verziójával egyenlő értékre kell állítani.

### <a name="schema-v22-no-aad-recommended"></a>Schema v 2.2: nincs HRE (ajánlott)

A v 2.2 séma minden új virtuális gép esetében ajánlott, és nem igényel Azure Active Directory tulajdonságokat.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[kekVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Schema v 1.1: a HRE 

Az 1,1-es séma megköveteli `aadClientID` , `aadClientSecret` hogy az `AADClientCertificate` új virtuális gépek esetében ne legyen ajánlott, vagy a vagy a vagy a.

Használat `aadClientSecret` :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[kekVaultResourceID]",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[kekVaultResourceID]",
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
| típus | AzureDiskEncryption | sztring |
| typeHandlerVersion | 2,2, 1,1 | sztring |
| (1,1 séma) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (1,1 séma) AADClientSecret | jelszó | sztring |
| (1,1 séma) AADClientCertificate | ujjlenyomat | sztring |
| EncryptionOperation | EnableEncryption | sztring | 
| (opcionális – alapértelmezett RSA-OAEP) KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | sztring |
| KeyVaultURL | url | sztring |
| KeyVaultResourceId | url | sztring |
| választható KeyEncryptionKeyURL | url | sztring |
| választható KekVaultResourceId | url | sztring |
| választható SequenceVersion | uniqueidentifier | sztring |
| VolumeType | Operációs rendszer, az összes | sztring |

## <a name="template-deployment"></a>Sablonalapú telepítés

A sablon 2.0-s verzión alapuló központi telepítésének példáját az Azure rövid útmutató sablonjának [201-encrypting-Running-Windows-VM-Without-HRE](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).

Az 1.1-es séma alapján történő központi telepítésre példa: Azure Gyorsindítás sablon [201-encrypt-Running-Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

>[!NOTE]
> Ha a `VolumeType` paraméter értéke mind, az adatlemezek csak akkor lesznek titkosítva, ha megfelelően vannak formázva. 

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Hibaelhárításhoz tekintse meg a [Azure Disk Encryption hibaelhárítási útmutatót](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/community/)is. 

Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási szolgálatát](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Következő lépések

* További információ a bővítményekről: [virtuálisgép-bővítmények és-szolgáltatások a Windows rendszerhez](features-windows.md).
* További információ a Windows Azure Disk Encryptionről: [Windows Virtual Machines](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines).
