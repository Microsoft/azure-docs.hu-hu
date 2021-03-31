---
title: Az Azure Disk Encryption használata virtuális gépeken és virtuálisgép-méretezési csoportokban
description: Ismerje meg a Virtual Machines (VM) és a virtuálisgép-méretezési csoportok Azure Disk Encryption szolgáltatását. Az Azure Disk Encryption a Linux és a Windows rendszerű virtuális gépek esetében is működik.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 21194bf2fe76a7eb0ee034d4a502c20ee3032dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87543673"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Az Azure Disk Encryption használata virtuális gépeken és virtuálisgép-méretezési csoportokban

Az Azure Disk Encryption a Linux és a Windows rendszerű virtuális gépekre, valamint a virtuálisgép-méretezési csoportokra is alkalmazható. 

## <a name="linux-virtual-machines"></a>Linux rendszerű virtuális gépek

A következő cikkek útmutatást nyújtanak a linuxos virtuális gépek titkosításához.

### <a name="current-version-of-azure-disk-encryption"></a>A Azure Disk Encryption aktuális verziója

- [A Linux rendszerű virtuális gépekhez készült Azure Disk Encryption áttekintése](../../virtual-machines/linux/disk-encryption-overview.md)
- [Azure Disk Encryption-forgatókönyvek Linux rendszerű virtuális gépekhez](../../virtual-machines/linux/disk-encryption-linux.md)
- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure Portallal](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [A Linux rendszerhez készült Azure Disk Encryption-bővítmény sémája](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure Disk Encryption – mintaszkriptek](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Azure Disk Encryption – hibaelhárítás](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption – gyakori kérdések](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption az Azure AD-vel (előző verzió)

- [A Linux rendszerű virtuális gépekhez készült Azure AD-Azure Disk Encryption áttekintése](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Azure AD-forgatókönyvek Azure Disk Encryption Linux rendszerű virtuális gépeken](../../virtual-machines/linux/disk-encryption-linux.md)
- [Kulcstartó létrehozása és konfigurálása az Azure AD-vel való Azure Disk Encryptionhoz (előző kiadás)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Windows rendszerű virtuális gépek

A következő cikkek útmutatást nyújtanak a Windows rendszerű virtuális gépek titkosításához.

### <a name="current-version-of-azure-disk-encryption"></a>A Azure Disk Encryption aktuális verziója

- [A Windows rendszerű virtuális gépekhez készült Azure Disk Encryption áttekintése](../../virtual-machines/windows/disk-encryption-overview.md)
- [Azure Disk Encryption-forgatókönyvek Windows rendszerű virtuális gépekhez](../../virtual-machines/windows/disk-encryption-windows.md)
- [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure Portallal](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Azure Disk Encryption Windows-bővítmény sémája](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure Disk Encryption – mintaszkriptek](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Azure Disk Encryption – hibaelhárítás](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption – gyakori kérdések](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption az Azure AD-vel (előző verzió)

- [A Windows rendszerű virtuális gépekhez készült Azure AD-Azure Disk Encryption áttekintése](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Azure Disk Encryption Azure AD-forgatókönyvekkel Windows rendszerű virtuális gépeken](../../virtual-machines/windows/disk-encryption-windows.md)
- [Kulcstartó létrehozása és konfigurálása az Azure AD-vel való Azure Disk Encryptionhoz (előző kiadás)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok

A következő cikkek útmutatást nyújtanak a virtuálisgép-méretezési csoportok titkosításához.

- [A virtuálisgép-méretezési csoportokra vonatkozó Azure Disk Encryption áttekintése](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Virtuálisgép-méretezési csoportok titkosítása az Azure CLI használatával](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Virtuálisgép-méretezési csoportok titkosítása az Azure PowerShell használatával](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Virtuálisgép-méretezési csoportok titkosítása a Azure Resource Manager használatával](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [A Azure Disk Encryption használata a virtuálisgép-méretezési csoport bővítményének előkészítésével](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Következő lépések

- [Az Azure-titkosítás áttekintése](encryption-overview.md)
- [Adat-titkosítás inaktív állapotban](encryption-atrest.md)
- [Az adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárások](data-encryption-best-practices.md)
