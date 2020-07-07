---
title: Azure Disk Encryption izolált hálózaton
description: Ez a cikk hibaelhárítási tippeket tartalmaz a Linux rendszerű virtuális gépekhez Microsoft Azure lemezes titkosításhoz.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: f2b84427b9aad2d18368d808fc618f3bfbe774ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81460120"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Azure Disk Encryption izolált hálózaton

Ha a kapcsolatot tűzfal, proxy vagy hálózati biztonsági csoport (NSG) beállításai korlátozzák, előfordulhat, hogy a bővítménynek a szükséges feladatok elvégzésére való képessége megszakad. Ez a megszakítás olyan állapotüzenetek elvégzését eredményezheti, mint például a "bővítmény állapota nem érhető el a virtuális gépen".

## <a name="package-management"></a>Csomagkezelés

Azure Disk Encryption számos összetevőtől függ, amelyek általában az ADE-engedélyezés részeként települnek, ha még nem jelennek meg. Tűzfal mögött, vagy az internettől eltérő módon elszigetelve ezeket a csomagokat előre kell telepíteni vagy helyileg elérhetőnek kell lenniük.

Az alábbi csomagok szükségesek az egyes terjesztésekhez. A támogatott disztribúciók és mennyiségi típusok teljes listáját lásd: [támogatott virtuális gépek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14,04, 16,04, 18,04**: lsscsi, psmisc, at, cryptsetup program változatának-bin, Python-elváltott, Python-Six, procps
- **CentOS 7,2-7,7**: lsscsi, psmisc, LVM2, UUID, at, patch, cryptsetup program változatának, cryptsetup program változatának-reencrypt, pyparted, procps-ng, util-linux
- **CentOS 6,8**: lsscsi, psmisc, LVM2, UUID, at, cryptsetup program változatának-retitkosítás, pyparted, Python-Six
- **RedHat 7,2-7,7**: lsscsi, psmisc, LVM2, UUID, at, patch, cryptsetup program változatának, cryptsetup program változatának-reencrypt, procps-ng, util-linux
- **RedHat 6,8**: lsscsi, psmisc, LVM2, UUID, at, patch, cryptsetup program változatának-reencrypt
- **openSUSE 42,3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup program változatának

Red Hat esetén, ha proxyra van szükség, meg kell győződnie arról, hogy az előfizetés-kezelő és a yum megfelelően van beállítva. További információ: [az előfizetés-kezelő és a yum-problémák elhárítása](https://access.redhat.com/solutions/189533).  

Ha a csomagokat manuálisan telepítik, akkor azokat is manuálisan kell frissíteni, mivel a rendszer új verziókat szabadít fel.

## <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)
Az alkalmazott hálózati biztonsági csoportok beállításai továbbra is lehetővé teszik, hogy a végpont megfeleljen a lemez titkosításának dokumentált hálózati konfigurációs előfeltételeinek.  Lásd [Azure Disk Encryption: hálózati követelmények](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption az Azure AD-vel (előző verzió)

Ha az [Azure Disk Encryptiont az Azure ad-vel (előző verzió)](disk-encryption-overview-aad.md)használja, a [Azure Active Directory-függvénytárat](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) manuálisan kell telepíteni az összes disztribúcióhoz (a [fentiekben](#package-management)leírtak szerint a disztribúciónak megfelelő csomagokon kívül).

Ha engedélyezve van a titkosítás az [Azure ad hitelesítő adataival](disk-encryption-linux-aad.md), a CÉLKÉNT megadott virtuális gépnek Azure Active Directory végpontokhoz és Key Vault végpontokhoz is engedélyeznie kell a kapcsolatot. Az aktuális Azure Active Directory hitelesítési végpontok az [Office 365 URL-címeinek és IP-címtartományok](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) dokumentációjának 56-es és 59-es szakaszában maradnak. Key Vault útmutatást a [tűzfal mögötti Azure Key Vault elérésének](../../key-vault/general/access-behind-firewall.md)dokumentációjában talál.

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata szolgáltatás 

A virtuális gépnek képesnek kell lennie az [Azure-példány metaadatainak szolgáltatás](instance-metadata-service.md) -végpontjának elérésére, amely egy jól ismert, nem irányítható IP-címet () használ, `169.254.169.254` amely csak a virtuális gépről érhető el.  A helyi HTTP-forgalmat az erre a címmé megváltoztató proxy-konfigurációk nem támogatottak.

## <a name="next-steps"></a>További lépések

- További lépések az [Azure Disk Encryption hibaelhárításához](disk-encryption-troubleshooting.md)
- [Az Azure-beli adattitkosítás inaktív állapotban](../../security/fundamentals/encryption-atrest.md)
