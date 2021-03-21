---
title: Azure biztonsági szolgáltatások és technológiák | Microsoft Docs
description: A cikk az Azure biztonsági szolgáltatásainak és technológiáinak válogatott listáját tartalmazza.
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: terrylan
ms.openlocfilehash: 27443eb9c04c69567b3a1374f11e8b53837040f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585411"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Az Azure-ban elérhető biztonsági szolgáltatások és technológiák

A jelenlegi és jövőbeli Azure-ügyfelekkel folytatott beszélgetésekben gyakran megkérdezjük, hogy van-e az Azure által kínált biztonsággal kapcsolatos szolgáltatások és technológiák listája?

Ha kiértékeli a Cloud Service Provider beállításait, hasznos lehet ezt az információt. Ezért ezt a listát a kezdéshez biztosítottuk.

Ez a lista idővel megváltozik és növekedni fog, ugyanúgy, mint az Azure. Ügyeljen arra, hogy rendszeresen ellenőrizze ezt a lapot, hogy naprakész maradjon a biztonsággal kapcsolatos szolgáltatásainkról és technológiákról.

## <a name="general-azure-security"></a>Általános Azure-biztonság
|Szolgáltatás|Leírás|
|--------|--------|
|[Azure &nbsp; Security &nbsp; Center](../../security-center/security-center-introduction.md)| Felhőalapú munkaterhelés-védelmi megoldás, amely biztonsági felügyeletet és komplex veszélyforrások elleni védelmet biztosít a hibrid Felhőbeli munkaterhelések között.|
|[Azure Key Vault](../../key-vault/general/overview.md)| Biztonságos titkos kulcsok tárolása a jelszavakhoz, a kapcsolati karakterláncokhoz és az alkalmazások működésének megőrzéséhez szükséges egyéb információkhoz. |
|[Azure Monitor-naplók](../../azure-monitor/logs/log-query-overview.md)|Egy figyelési szolgáltatás, amely telemetria és más adatokat gyűjt, valamint egy lekérdezési nyelvet és analitikai motort biztosít az alkalmazások és erőforrások működési elemzéséhez. Önállóan vagy más szolgáltatásokkal, például Security Center használható. |
|[Azure dev/test Labs](../../devtest-labs/devtest-lab-overview.md)|Egy olyan szolgáltatás, amely segít a fejlesztőknek és a tesztelőknek gyorsan létrehozni az Azure-beli környezeteket, miközben minimalizálja a hulladékot és szabályozza a költségeket.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Tárbiztonság
|Szolgáltatás|Leírás|
|------|--------|
| [Azure &nbsp; Storage &nbsp; szolgáltatás &nbsp; titkosítása](../../storage/common/storage-service-encryption.md)|Egy biztonsági funkció, amely automatikusan titkosítja az adatait az Azure Storage-ban.   |
|[StorSimple titkosított hibrid tárolás](../../storsimple/storsimple-ova-overview.md)| Integrált tárolási megoldás, amely a helyszíni eszközök és az Azure Cloud Storage közötti tárolási feladatokat kezeli.|
|[Azure Client-Side titkosítás](../../storage/common/storage-client-side-encryption.md)| Ügyféloldali titkosítási megoldás, amely az Azure Storage-ba való feltöltés előtt titkosítja az ügyfélalkalmazások adatain belüli adattitkosítást. a letöltése közben visszafejti az adatvesztést is. |
| [Azure Storage – közös hozzáférésű aláírások](../../storage/common/storage-sas-overview.md)|A közös hozzáférésű aláírások delegált hozzáférést biztosítanak a Storage-fiók erőforrásaihoz.  |
|[Azure Storage-fiók kulcsainak](../../storage/common/storage-account-create.md)| Hozzáférés-vezérlési módszer az Azure Storage-hoz, amelyet a rendszer a Storage-fiók elérésekor hitelesítésre használ. |
|[Azure-fájlmegosztás SMB 3,0 titkosítással](../../storage/files/storage-files-introduction.md)|Olyan hálózati biztonsági technológia, amely lehetővé teszi az automatikus hálózati titkosítást a Server Message Block (SMB) fájlmegosztás protokollja számára. |
|[Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics)| Naplózási és metrikai technológia a Storage-fiókban lévő adatokhoz. |

<!------>

## <a name="database-security"></a>Adatbázis-biztonság
|Szolgáltatás|Leírás|
|------|--------|
| [Azure &nbsp; SQL &nbsp; tűzfal](../../azure-sql/database/firewall-configure.md)|Egy hálózati hozzáférés-vezérlési funkció, amely védelmet biztosít a hálózati alapú támadásokkal szemben az adatbázison. |
|[Azure &nbsp; SQL &nbsp; Cell &nbsp; szintű titkosítás](/archive/blogs/sqlsecurity/recommendations-for-using-cell-level-encryption-in-azure-sql-database)| Adatbázis-biztonsági technológia, amely részletességi szinten biztosítja a titkosítást.  |
| [Azure &nbsp; SQL- &nbsp; kapcsolatok titkosítása](../../azure-sql/database/logins-create-manage.md)|A biztonság garantálása érdekében az SQL Database IP-cím alapján történő kapcsolatkorlátozást érvényesítő tűzfalszabályokkal, a felhasználói identitás igazolását megkövetelő hitelesítési mechanizmusokkal, illetve csak bizonyos műveletek és adatok elérését lehetővé tévő engedélyezési mechanizmusokkal vezérli a hozzáférést. |
| [Az Azure SQL Always encryption](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Védi a bizalmas adatokat, például a hitelkártya-számokat vagy a nemzeti azonosító számokat (például az Egyesült államokbeli társadalombiztosítási számokat), Azure SQL Database vagy SQL Server adatbázisokban tárolva.  |
| [Azure &nbsp; SQL &nbsp; transzparens adattitkosítás](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Adatbázis-biztonsági szolgáltatás, amely egy teljes adatbázis tárterületét titkosítja. |
| [Azure SQL Database naplózás](../../azure-sql/database/auditing-overview.md)|Adatbázis-naplózási funkció, amely nyomon követi az adatbázis-eseményeket, és az Azure Storage-fiókban lévő naplóba írja azokat.  |


## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés
|Szolgáltatás|Leírás|
|------|--------|
| [Azure &nbsp; szerepköralapú hozzáférés- &nbsp; vezérlés](../../role-based-access-control/role-assignments-portal.md)|Hozzáférés-vezérlési funkció, amely lehetővé teszi, hogy a felhasználók csak azokat az erőforrásokat férhessenek hozzá, amelyekre a szervezeten belüli szerepköreik alapján van szükségük.  |
| [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)|Egy felhőalapú hitelesítési tárház, amely több-bérlős, felhőalapú címtárat és több Identitáskezelés-kezelő szolgáltatást támogat az Azure-on belül.  |
| [Azure Active Directory B2C](../../active-directory-b2c/overview.md)|Identitáskezelés-kezelő szolgáltatás, amely lehetővé teszi, hogy az ügyfelek regisztráljanak, jelentkezzenek be és kezeljenek profilokat Azure-alapú alkalmazások használatakor.   |
| [Azure Active Directory tartományi szolgáltatások](../../active-directory-domain-services/overview.md)| A Active Directory Domain Services felhőalapú és felügyelt verziója. |
| [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)| A biztonságos információkhoz való hozzáférés engedélyezése előtt a hitelesítés és ellenőrzés számos különböző formáját alkalmazó biztonsági rendelkezés. |

## <a name="backup-and-disaster-recovery"></a>Biztonsági mentés és vész-helyreállítás
|Szolgáltatás|Leírás|
|------|--------|
| [Azure &nbsp; Backup](../../backup/backup-overview.md)| Azure-alapú szolgáltatás, amely az Azure-felhőben tárolt adatbiztonsági mentésére és helyreállítására szolgál. |
| [Azure &nbsp; site &nbsp; Recovery](../../site-recovery/site-recovery-overview.md)|Egy online szolgáltatás, amely a fizikai és virtuális gépeken futó munkaterheléseket replikálja egy elsődleges helyről egy másodlagos helyre, hogy a meghibásodás után engedélyezze a szolgáltatások helyreállítását. |

## <a name="networking"></a>Hálózatkezelés
|Szolgáltatás|Leírás|
|------|--------|
| [Hálózati &nbsp; biztonsági &nbsp; csoportok](../../virtual-network/virtual-network-vnet-plan-design-arm.md)| Egy 5 rekordos hálózati hozzáférés-vezérlési funkció, amely lehetővé teszi vagy megtagadja a döntéseket.  |
| [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)| VPN-végpontként használt hálózati eszköz, amely lehetővé teszi a létesítmények közötti hozzáférést az Azure-beli virtuális hálózatokhoz.  |
| [Azure Application Gateway](../../application-gateway/overview.md)|Egy speciális webalkalmazás-terheléselosztó, amely URL-cím alapján irányítható át, és SSL-kiürítést hajt végre. |
|[Webalkalmazási tűzfal](../../web-application-firewall/afds/afds-overview.md) (WAF)|A Application Gateway szolgáltatása, amely a webalkalmazások központosított védelmét nyújtja a gyakori biztonsági rések és sebezhetőségek ellen|
| [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)|TCP/UDP-alkalmazás hálózati terheléselosztó. |
| [Azure ExpressRoute](../../expressroute/expressroute-introduction.md)| Dedikált WAN-kapcsolat a helyszíni hálózatok és az Azure-beli virtuális hálózatok között. |
| [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)| Globális DNS-terheléselosztó.|
| [Azure-alkalmazásproxy](../../active-directory/manage-apps/application-proxy.md)| A helyszíni környezetben üzemeltetett webalkalmazások távoli elérésének biztonságossá tételéhez használt hitelesítő kezelőfelület. |
|[Azure Firewall](../../firewall/overview.md)|Felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely megvédi az Azure Virtual Network-erőforrásait.|
|[Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md)|Az alkalmazások kialakításával kapcsolatos ajánlott eljárásokkal kombinálva védelmet nyújt a DDoS-támadásokkal szemben.|
|[Virtuális hálózati szolgáltatásvégpontok](../../virtual-network/virtual-network-service-endpoints-overview.md)|Közvetlen kapcsolaton keresztül kiterjesztheti a virtuális hálózat privát címterület-területét és a VNet identitását az Azure-szolgáltatásokra.|