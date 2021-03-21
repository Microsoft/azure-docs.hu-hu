---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/02/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 52b9bee1d43c0f136889a6a54277d4bb45dd4a45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750828"
---
Az egyes felügyelt lemezek szintjén is kezelheti a titkosítást a saját kulcsaival. Az ügyfél által felügyelt kulcsokkal rendelkező felügyelt lemezek kiszolgálóoldali titkosítása integrált élményt nyújt a Azure Key Vault. Importálhatja az [RSA-kulcsokat](../articles/key-vault/keys/hsm-protected-keys.md) a Key Vault, vagy LÉTREHOZHAT új RSA-kulcsokat Azure Key Vault. 

Az Azure Managed Disks a titkosítást és a visszafejtést teljes mértékben átlátható módon kezeli a [boríték-titkosítás](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)használatával. Titkosítja az adatait egy [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256-alapú adattitkosítási kulccsal (adattitkosítási kulcsot), amely viszont a kulcsok használatával védett. A Storage szolgáltatás adattitkosítási kulcsokat hoz létre, és az ügyfél által felügyelt kulcsokkal titkosítja azokat az RSA encryption használatával. A borítékok titkosítása lehetővé teszi a kulcsok rendszeres elforgatását a megfelelőségi szabályzatok szerint, a virtuális gépek befolyásolása nélkül. A kulcsok elforgatásakor a Storage szolgáltatás újra titkosítja az adattitkosítási kulcsokat az új ügyfél által felügyelt kulcsokkal. 

#### <a name="full-control-of-your-keys"></a>A kulcsok teljes körű vezérlése

A ADATTITKOSÍTÁSI kulcsot titkosítására és visszafejtésére szolgáló kulcsok használatához hozzáférést kell biztosítania a Key Vault felügyelt lemezéhez. Ez lehetővé teszi, hogy teljes körűen vezérelje adatait és kulcsait. Bármikor letilthatja a kulcsokat, vagy visszavonhatja a hozzáférést a felügyelt lemezekhez. A titkosítási kulcs használatát Azure Key Vault figyeléssel is naplózhatja, így biztosítva, hogy csak a felügyelt lemezek vagy más megbízható Azure-szolgáltatások férhessenek hozzá a kulcsokhoz.

Prémium SSD-k, standard SSD-k és standard HDD-k esetén: Ha letiltja vagy törli a kulcsot, a kulcsot használó virtuális gépek automatikusan le lesznek állítva. Ezt követően a virtuális gépek nem lesznek használhatók, kivéve, ha a kulcs ismét engedélyezve van, vagy új kulcsot rendel hozzá.    

Ultra Disks esetén: Ha letilt vagy töröl egy kulcsot, a kulcsot használó, ultra lemezzel rendelkező virtuális gépek nem lesznek automatikusan leállítva. Ha felszabadítja és újraindítja a virtuális gépeket, akkor a lemezek nem fogják tudni használni a kulcsot, és a virtuális gépek nem fognak ismét online állapotba állni. Ahhoz, hogy a virtuális gépek ismét online állapotba kerüljenek, hozzá kell rendelnie egy új kulcsot, vagy engedélyeznie kell a meglévő kulcsot.    

A következő ábra azt mutatja be, hogy a felügyelt lemezek hogyan használják a Azure Active Directory és Azure Key Vault a kérelmeket az ügyfél által felügyelt kulcs használatával:

:::image type="content" source="media/virtual-machines-managed-disks-description-customer-managed-keys/customer-managed-keys-sse-managed-disks-workflow.png" alt-text="Felügyelt lemez és ügyfél által felügyelt kulcsok munkafolyamata. A rendszergazda létrehoz egy Azure Key Vault, majd létrehoz egy lemezes titkosítási készletet, és beállítja a lemez titkosítási készletét. A készlet egy virtuális géphez van társítva, amely lehetővé teszi, hogy a lemez használhassa az Azure AD-t a hitelesítéshez":::

A következő lista részletesebben ismerteti a diagramot:

1. A Azure Key Vault rendszergazdája létrehoz egy Key Vault-erőforrásokat.
1. A Key Vault rendszergazdája importálja az RSA-kulcsokat, hogy Key Vault vagy új RSA-kulcsokat generáljon a Key Vaultban.
1. A rendszergazda a lemez titkosítási készletének erőforrásának egy példányát hozza létre, amely egy Azure Key Vault azonosítót és egy kulcs URL-címet ad meg. A lemezes titkosítási készlet egy új erőforrás, amely leegyszerűsíti a felügyelt lemezek kulcsfontosságú felügyeletét. 
1. Lemezes titkosítási csoport létrehozásakor a [rendszer hozzárendelt felügyelt identitást](../articles/active-directory/managed-identities-azure-resources/overview.md) hoz létre Azure Active Directory (ad) és a lemez titkosítási készletéhez társítva. 
1. Az Azure Key Vault rendszergazdája ezt követően engedélyt ad a felügyelt identitásnak a kulcstartó műveleteinek elvégzésére.
1. A virtuálisgép-felhasználók lemezek létrehozásával társítják őket a lemezes titkosítási készlettel. A virtuális gép felhasználója a meglévő erőforrások ügyfél által felügyelt kulcsaival is engedélyezheti a kiszolgálóoldali titkosítást, ha a lemezes titkosítási készlettel társítja őket. 
1. A felügyelt lemezek a felügyelt identitás használatával küldenek kéréseket a Azure Key Vault.
1. Az adatok olvasásához vagy írásához a felügyelt lemezek kéréseket küldenek Azure Key Vaultnek az adatok titkosításához és visszafejtéséhez (tördeléséhez), valamint az adattitkosítási kulcs visszafejtéséhez (kicsomagolásához). 

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához lásd: [Azure Key Vault PowerShell](/powershell/module/azurerm.keyvault/) és [Azure Key Vault parancssori](/cli/azure/keyvault)felület. A hozzáférés visszavonása hatékonyan blokkolja a Storage-fiókban lévő összes adattal való hozzáférést, mivel a titkosítási kulcs nem érhető el az Azure Storage-ban.

#### <a name="automatic-key-rotation-of-customer-managed-keys-preview"></a>Az ügyfél által felügyelt kulcsok automatikus elforgatása (előzetes verzió)

Dönthet úgy, hogy engedélyezi a kulcs automatikus elforgatását a legújabb verzióra. A lemez a lemez titkosítási készletén keresztül hivatkozik a kulcsra. Ha engedélyezi a lemezes titkosítási készlet automatikus elforgatását, a rendszer automatikusan frissíti az összes felügyelt lemezt, pillanatképet és lemezképet, amely a lemez titkosítási készletére hivatkozik, hogy egy órán belül a kulcs új verzióját használja. A szolgáltatás jelenleg csak korlátozott számú régióban érhető el előzetes verzióban. A regionális elérhetőséget a [támogatott régiók](#supported-regions) szakaszban találja.