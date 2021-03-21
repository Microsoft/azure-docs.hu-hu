---
title: Azure Disk Encryption hibaelhárítási útmutató
description: Ez a cikk hibaelhárítási tippeket tartalmaz a Windows rendszerű virtuális gépek Microsoft Azure lemezes titkosításához.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 057aba3e49d32694563f412101be499342f2aad0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550603"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure Disk Encryption hibaelhárítási útmutató

Ez az útmutató olyan informatikai szakemberek, Információbiztonsági elemzők és Felhőbeli rendszergazdák számára készült, akiknek a vállalata Azure Disk Encryptiont használ. Ez a cikk segítséget nyújt a lemezes titkosítással kapcsolatos problémák elhárításában.

Az alábbi lépések bármelyikének megkezdése előtt győződjön meg arról, hogy a titkosítani kívánt virtuális gépek a támogatott virtuálisgép- [méretek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)közé tartoznak, és az összes előfeltétel teljesült:

- [Hálózati követelmények](disk-encryption-overview.md#networking-requirements)
- [Csoportházirend-követelmények](disk-encryption-overview.md#group-policy-requirements)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Hibaelhárítás Azure Disk Encryption tűzfal mögött

Ha a kapcsolatot tűzfal, proxy vagy hálózati biztonsági csoport (NSG) beállításai korlátozzák, előfordulhat, hogy a bővítménynek a szükséges feladatok elvégzésére való képessége megszakad. Ez a megszakítás olyan állapotüzenetek elvégzését eredményezheti, mint például a "bővítmény állapota nem érhető el a virtuális gépen". A várt helyzetekben a titkosítás nem fejeződik be. Az alábbi szakaszokban néhány gyakori tűzfallal kapcsolatos probléma merülhet fel.

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)
Az alkalmazott hálózati biztonsági csoportok beállításai továbbra is lehetővé teszik, hogy a végpont megfeleljen a lemez titkosításának dokumentált hálózati konfigurációs [előfeltételeinek](disk-encryption-overview.md#networking-requirements) .

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault tűzfal mögött

Ha engedélyezve van a titkosítás az [Azure ad hitelesítő adataival](disk-encryption-windows-aad.md#), a CÉLKÉNT megadott virtuális gépnek Azure Active Directory végpontokhoz és Key Vault végpontokhoz is engedélyeznie kell a kapcsolatot. Az aktuális Azure Active Directory hitelesítési végpontok a [Microsoft 365 URL-címek és az IP-címtartományok](/microsoft-365/enterprise/urls-and-ip-address-ranges) dokumentációjának 56-es és 59-es szakaszában maradnak. Key Vault útmutatást a [tűzfal mögötti Azure Key Vault elérésének](../../key-vault/general/access-behind-firewall.md)dokumentációjában talál.

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata szolgáltatás 
A virtuális gépnek képesnek kell lennie elérni az [Azure-példány metaadatainak szolgáltatási](../windows/instance-metadata-service.md) végpontját, amely egy jól ismert, nem irányítható IP-címet ( `169.254.169.254` ) használ, amely csak a virtuális gépről érhető el.  A helyi HTTP-forgalmat az erre a címmé megváltoztató proxy-konfigurációk nem támogatottak.

## <a name="troubleshooting-windows-server-2016-server-core"></a>A Windows Server 2016 Server Core hibaelhárítása

A Windows Server 2016 Server Core rendszeren a bdehdcfg összetevő alapértelmezés szerint nem érhető el. Ezt az összetevőt Azure Disk Encryption kell megadnia. A rendszerkötet az operációs rendszer kötetéről való felosztására szolgál, amelyet csak egyszer kell elvégezni a virtuális gép élettartama során. Ezek a bináris fájlok a későbbi titkosítási műveletek során nem szükségesek.

A probléma megkerüléséhez másolja a következő négy fájlt egy Windows Server 2016 adatközpontbeli virtuális gépről ugyanarra a helyre a Server Core-on:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Írja be a következő parancsot:

   ```
   bdehdcfg.exe -target default
   ```

1. Ez a parancs egy 550 MB rendszerpartíciót hoz létre. Indítsa újra a rendszerét.

1. A DiskPart használatával jelölje ki a köteteket, majd folytassa a következővel:.  

Például:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Titkosítási állapot hibaelhárítása 

A portál titkosított lemezként jelenhet meg, miután a virtuális gépen titkosítva lett.  Ez akkor fordulhat elő, ha az alacsony szintű parancsok használatával a rendszer a virtuális gépről közvetlenül titkosítja a lemezt a magasabb szintű Azure Disk Encryption felügyeleti parancsok használata helyett.  A magasabb szintű parancsok nem csak a virtuális gépről titkosítják a lemezt, hanem a virtuális GÉPEN kívül is frissítik a virtuális géphez tartozó fontos platform szintű titkosítási beállításokat és bővítmény-beállításokat.  Ha ezeket nem tartja összhangban, a platform nem fogja tudni bejelenteni a titkosítási állapotot, vagy megfelelően kiépíteni a virtuális gépet.

A Azure Disk Encryption PowerShell-lel való letiltásához használja a [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsot, majd a [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). A Remove-AzVMDiskEncryptionExtension futtatása a titkosítás letiltása előtt sikertelen lesz.

A CLI-vel való Azure Disk Encryption letiltásához használja [az az VM encryption disable](/cli/azure/vm/encryption)parancsot. 

## 



## <a name="next-steps"></a>Következő lépések

Ebben a dokumentumban többet is megtudhat a Azure Disk Encryption és a problémák elhárításával kapcsolatos gyakori problémákról. A szolgáltatással és képességeivel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Lemez titkosításának alkalmazása Azure Security Center](../../security-center/asset-inventory.md)
- [Inaktív adatok titkosítása az Azure-ban](../../security/fundamentals/encryption-atrest.md)