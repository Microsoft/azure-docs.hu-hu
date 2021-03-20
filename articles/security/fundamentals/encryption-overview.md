---
title: Az Azure-titkosítás áttekintése | Microsoft Docs
description: Az Azure titkosítási lehetőségeinek megismerése. Tekintse meg a inaktív adatok titkosításával, a repülés titkosításával és a Azure Key Vaultsal való kulcskezelő szolgáltatással foglalkozó témakört.
services: security
author: msmbaldwin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: d7d438b369c863660a032f101e466b6fadf639fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879714"
---
# <a name="azure-encryption-overview"></a>Az Azure-titkosítás áttekintése

Ez a cikk áttekintést nyújt a titkosítás használatáról Microsoft Azureban. A titkosítás főbb területeit fedi le, beleértve a inaktív titkosítást, a repülés közbeni titkosítást és a Azure Key Vaultsal való kulcskezelő felügyeletet. Minden szakasz részletesebb információkra mutató hivatkozásokat tartalmaz.

## <a name="encryption-of-data-at-rest"></a>Inaktív adatok titkosítása

A nyugalmi állapotban lévő adatok olyan információkat tartalmaznak, amelyek a fizikai adathordozón található állandó tárolóban találhatók, bármilyen digitális formátumban. Az adathordozó tartalmazhat mágneses vagy optikai adathordozón található fájlokat, archivált adatokat és az adatok biztonsági másolatait. Microsoft Azure különféle adattárolási megoldásokat kínál a különböző igények kielégítéséhez, többek között fájl-, lemez-, blob-és Table Storage-szolgáltatásokhoz. A Microsoft emellett titkosítást is biztosít [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md), [Azure Cosmos db](../../data-factory/introduction.md)és Azure Data Lake védelemmel.

Az inaktív adatok titkosítása a szolgáltatott szoftver (SaaS), a szolgáltatásként nyújtott platform (IaaS) és az infrastruktúra-szolgáltatás (Cloud models) között érhető el. Ez a cikk az Azure titkosítási beállításainak használatának megkönnyítésére szolgáló forrásokat összegzi és biztosítja.

A REST-beli adatok Azure-ban való titkosításának részletes ismertetését lásd: [Azure-beli adatok titkosítása – Rest](encryption-atrest.md).

## <a name="azure-encryption-models"></a>Azure titkosítási modellek

Az Azure különböző titkosítási modelleket támogat, beleértve a kiszolgálóoldali titkosítást, amely a szolgáltatás által felügyelt kulcsokat, az ügyfél által felügyelt kulcsokat használja Key Vault vagy az ügyfél által felügyelt kulcsokat az ügyfél által vezérelt hardveren. Ügyféloldali titkosítás esetén a kulcsok a helyszínen vagy más biztonságos helyen kezelhetők és tárolhatók.

### <a name="client-side-encryption"></a>Ügyféloldali titkosítás

Az ügyféloldali titkosítás az Azure-on kívül történik. A következőket tartalmazza:

- Az ügyfél adatközpontjában vagy szolgáltatásalkalmazás által futtatott alkalmazás által titkosított adatforgalom.
- Az Azure által fogadott, már titkosított adatforgalom.

Az ügyféloldali titkosítással a felhőalapú szolgáltatók nem férnek hozzá a titkosítási kulcsokhoz, és nem tudják visszafejteni ezeket az adatfájlokat. A kulcsok teljes körű vezérlését megtarthatja.

### <a name="server-side-encryption"></a>Kiszolgálóoldali titkosítás

A három kiszolgálóoldali titkosítási modell különböző kulcskezelő jellemzőket kínál, amelyeket az igényei szerint választhat:

- **Szolgáltatás által felügyelt kulcsok**: az alacsony terhelésű felügyelet és kényelem kombinációját biztosítja.

- **Ügyfél által felügyelt kulcsok**: lehetővé teszi a kulcsok vezérlését, beleértve a saját kulcsok használatát (BYOK), vagy újakat is létrehozhat.

- **Szolgáltatás által felügyelt kulcsok az ügyfél által vezérelt hardverben**: lehetővé teszi, hogy a Microsoft vezérlőn kívül a védett adattárban lévő kulcsokat kezelje. Ezt a tulajdonságot a saját kulcs (HYOK) neve tárolja. Azonban a konfiguráció összetett, és a legtöbb Azure-szolgáltatás nem támogatja ezt a modellt.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

A Windows-és Linux-alapú virtuális gépeket az [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md)használatával biztosíthatja, amely a [Windows BitLocker](/previous-versions/windows/it-pro/windows-vista/cc766295(v=ws.10)) Technology és a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) segítségével védi az operációs rendszer lemezeit és adatlemezeit teljes kötet titkosítással.

A titkosítási kulcsok és a titkos kódok védelme a [Azure Key Vault-előfizetésben](../../key-vault/general/overview.md)történik. A Azure Backup szolgáltatás használatával biztonsági mentést készíthet és visszaállíthatja a titkosítási kulcs (KEK) konfigurációját használó titkosított virtuális gépeket (VM).

### <a name="azure-storage-service-encryption"></a>Azure-Storage Service Encryption

Az Azure Blob Storage-ban tárolt adatok és az Azure-fájlmegosztás a kiszolgálóoldali és az ügyféloldali helyzetekben is titkosítható.

Az [Azure Storage Service encryption (SSE)](../../storage/common/storage-service-encryption.md) képes automatikusan titkosítani az adataikat a tárolás előtt, és a lekéréskor automatikusan visszafejti az adataikat. A folyamat teljes mértékben átlátható a felhasználók számára. A Storage Service Encryption 256 bites [Advanced Encryption Standard (AES) titkosítást](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)használ, amely az egyik legerősebb blokk titkosítási algoritmus. Az AES transzparens módon kezeli a titkosítást, a visszafejtést és a kulcsok felügyeletét.

### <a name="client-side-encryption-of-azure-blobs"></a>Az Azure-Blobok ügyféloldali titkosítása

Az Azure-Blobok ügyféloldali titkosítását többféleképpen is elvégezheti.

Az Azure Storage ügyféloldali kódtára a .NET NuGet-csomaghoz az ügyfélalkalmazások adatait az Azure Storage-ba való feltöltés előtt titkosíthatja.

Ha többet szeretne megtudni az Azure Storage ügyféloldali kódtára és a .NET NuGet-csomag letöltéséről, tekintse meg a következőt: [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Ha Key Vault használatával ügyféloldali titkosítást használ, a rendszer az adatokat az Azure Storage ügyféloldali SDK-val létrehozott egyszeri szimmetrikus Content encryption Key (CEK) használatával titkosítja. A CEK titkosítási kulcs (KEK) használatával van titkosítva, amely szimmetrikus kulcs vagy aszimmetrikus kulcspár lehet. Kezelheti helyileg, vagy tárolhatja Key Vaultban. Ezt követően a rendszer feltölti a titkosított fájlokat az Azure Storage-ba.

Ha többet szeretne megtudni az Key Vault-vel való ügyféloldali titkosításról és az útmutató útmutatásának megismeréséről, tekintse meg a következőt [: oktatóanyag: Blobok titkosítása és visszafejtése az Azure Storage-ban Key Vault használatával](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Végül az Azure Storage ügyféloldali kódtár használatával is elvégezheti az ügyféloldali titkosítást, mielőtt feltölti az Azure Storage-ba, és visszafejti az adatokra, amikor letölti az ügyfelet. Ez a függvénytár támogatja a Storage-fiókok kulcsának felügyeletéhez [Key Vault](https://azure.microsoft.com/services/key-vault/) integrációt is.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Inaktív adatok titkosítása Azure SQL Database

A [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md) egy általános célú, az Azure-ban található, olyan struktúrákat támogató adatbázis-szolgáltatás, mint például a viszonyítási adat, a JSON, a térbeli és az XML. A SQL Database támogatja a kiszolgálóoldali titkosítást a transzparens adattitkosítás (TDE) szolgáltatással és az ügyféloldali titkosítással az Always Encrypted funkció használatával.

#### <a name="transparent-data-encryption"></a>Transzparens adattitkosítás

A [TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-tde) a [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), a [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)és az [Azure szinapszis Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) -adatfájlok valós idejű titkosítására szolgál, amely az adatbázis-rendszerindító rekordban tárolódik a helyreállítás során rendelkezésre álló adatbázis-rendszerindítási rekordban.

A TDE az AES és a Triple Encryption Standard (3DES) titkosítási algoritmusok használatával védi az adatfájlokat és a naplófájlokat. Az adatbázisfájl titkosítása az oldal szintjén történik. A titkosított adatbázisban lévő lapok titkosítva lesznek a lemezre írásuk előtt, és a memóriába való beolvasás során visszafejtésre kerülnek. A TDE mostantól alapértelmezés szerint engedélyezve van az újonnan létrehozott Azure SQL-adatbázisokon.

#### <a name="always-encrypted-feature"></a>Always Encrypted funkció

Az Azure SQL [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkciójának használatával a Azure SQL Databaseban való tárolás előtt titkosíthatja az ügyféladatokat az ügyfélalkalmazások között. Emellett engedélyezheti a helyszíni adatbázis-felügyelet delegálását is harmadik feleknek, és megtarthatja a saját tagjai közötti elkülönítést, és megtekintheti azokat, akik kezelik, de nem férhetnek hozzá.

#### <a name="cell-level-or-column-level-encryption"></a>Cella szintű vagy oszlop szintű titkosítás

A Azure SQL Database segítségével a Transact-SQL használatával szimmetrikus titkosítást alkalmazhat egy adatoszlopra. Ennek a módszernek a neve a [cella szintű titkosítás vagy az oszlop szintű titkosítás (CLE)](/sql/relational-databases/security/encryption/encrypt-a-column-of-data), mert a segítségével meghatározott oszlopokat vagy akár meghatározott cellákat is titkosíthat különböző titkosítási kulcsokkal. Így részletesebb titkosítási képességet biztosít, mint a TDE, amely titkosítja az adatokat a lapokon.

A CLE olyan beépített funkciókkal rendelkezik, amelyekkel az adatok a szimmetrikus vagy aszimmetrikus kulcsok, a tanúsítvány nyilvános kulcsa vagy a 3DES használatával titkosított jelszó használatával titkosíthatók.

### <a name="cosmos-db-database-encryption"></a>Adatbázis-titkosítás Cosmos DB

[Azure Cosmos db](../../cosmos-db/database-encryption-at-rest.md) a Microsoft globálisan elosztott, többmodelles adatbázisa. A nem felejtő tárolóban (SSD-meghajtók) Cosmos DB tárolt felhasználói adatforgalom alapértelmezés szerint titkosítva van. Nincsenek be-és kikapcsolható vezérlők. A REST-alapú titkosítás számos biztonsági technológiával valósul meg, többek között a biztonságos kulcsú tárolási rendszerek, a titkosított hálózatok és a titkosítási API-k használatával. A titkosítási kulcsokat a Microsoft felügyeli, és a Microsoft belső iránymutatásai szerint elforgatva.

### <a name="at-rest-encryption-in-data-lake"></a>REST-alapú titkosítás Data Lake

A [Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md) a követelmények vagy séma formális meghatározása előtt egyetlen helyen gyűjtött, minden típusú adat vállalati szintű tárháza. Data Lake Store támogatja a "bekapcsolva" állapotú adatok transzparens titkosítását, amely a fiók létrehozásakor van beállítva. Alapértelmezés szerint a Azure Data Lake Store felügyeli a kulcsokat, de lehetősége van saját maga is kezelni őket.

Az adatok titkosítása és visszafejtése három típusú kulcsot használ: a Master encryption Key (MEK), az adattitkosítási kulcs (ADATTITKOSÍTÁSI kulcsot) és a Letiltás titkosítási kulcs (BEK). A MEK az állandó adathordozón tárolt ADATTITKOSÍTÁSI kulcsot titkosítására szolgál, a BEK pedig a ADATTITKOSÍTÁSI kulcsot és az adatblokkból származik. Ha saját kulcsokat kezel, akkor elforgathatja a MEK.

## <a name="encryption-of-data-in-transit"></a>Az adatforgalom titkosítása

Az Azure számos mechanizmust kínál az adatok magánjellegű tárolásához, ahogy az egyik helyről a másikra helyezi.

### <a name="data-link-layer-encryption-in-azure"></a>Adatkapcsolati réteg titkosítása az Azure-ban

Amikor az Azure-ügyfelek forgalmának továbbítása az adatközpontok között történik – a Microsoft által nem szabályozott fizikai határok (vagy a Microsoft nevében) – egy adatkapcsolati rétegbeli titkosítási módszer, amely az [IEEE 802.1 AE Mac biztonsági szabványokat](https://1.ieee802.org/security/802-1ae/) (más néven MACsec) használja az alapul szolgáló hálózati hardveren. A csomagokat a rendszer a küldés előtt titkosítja és visszafejti az eszközökön, megelőzve a fizikai "ember-középen" vagy a figyelési/wiretapping támadásokat. Mivel ez a technológia önmagában is a hálózati hardverhez van integrálva, a hálózati hardverek esetében a sebesség nélküli titkosítás biztosítja a kapcsolati késleltetés növelését. Ez a MACsec-titkosítás alapértelmezés szerint a régión vagy régión belüli összes Azure-forgalomra vonatkozik, és az ügyfelek részéről nincs szükség beavatkozásra. 

### <a name="tls-encryption-in-azure"></a>TLS-titkosítás az Azure-ban

A Microsoft lehetővé teszi az ügyfeleknek, hogy [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) protokollt használjanak az adatvédelemhez, amikor a felhőalapú szolgáltatások és az ügyfelek között utaznak. A Microsoft-adatközpontok egy TLS-kapcsolatot egyeztetnek az Azure-szolgáltatásokhoz csatlakozó ügyfélszámítógépekkel. A TLS erős hitelesítést, az üzenetek védelmét és integritását (az üzenetek illetéktelen módosításának, elfogásának és hamisításának észlelését), az együttműködési képességet, az algoritmus rugalmasságát, valamint a könnyű üzembe helyezést és használatot biztosít.

A [tökéletes továbbítási titoktartás](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) védi az ügyfelek és a Microsoft Cloud Services közötti kapcsolatokat egyedi kulcsok használatával. A kapcsolatok az RSA-alapú 2 048 bites titkosítási kulcs hosszait is használják. Ez a kombináció megnehezíti, hogy valaki elhallgassa és hozzáférjen az átvitt adatforgalomhoz.

### <a name="azure-storage-transactions"></a>Azure Storage-tranzakciók

Ha az Azure Storage-t a Azure Portal keresztül használja, az összes tranzakció HTTPS-kapcsolaton keresztül történik. A Storage REST API a HTTPS protokollon keresztül is használhatja az Azure Storage szolgáltatással való kommunikációhoz. A HTTPS használatát akkor kényszerítheti, ha a REST API-kat hívja a Storage-fiókokban lévő objektumok eléréséhez, így a Storage-fiókhoz szükséges biztonságos átvitelt engedélyezheti.

Közös hozzáférésű aláírások ([sas](../../storage/common/storage-sas-overview.md)), amelyek az Azure Storage-objektumokhoz való hozzáférés delegálására használhatók, és azt is megadhatják, hogy csak a HTTPS protokollt lehessen használni a megosztott hozzáférési aláírások használatakor. Ez a megközelítés biztosítja, hogy bárki, aki SAS-tokenekkel rendelkező hivatkozásokat küld, a megfelelő protokollt használja.

Az [SMB 3,0](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn551363(v=ws.11)#BKMK_SMBEncryption), amely a Azure Files megosztások elérésére szolgál, támogatja a titkosítást, és a windows Server 2012 R2, a Windows 8, a Windows 8,1 és a Windows 10 rendszerben érhető el. Lehetővé teszi a régiók közötti hozzáférést, és akár az asztalon is elérhetővé válik.

Az ügyféloldali titkosítás az Azure Storage-példányba való elküldés előtt titkosítja az adatait, hogy az titkosítva legyen a hálózaton keresztül.

### <a name="smb-encryption-over-azure-virtual-networks"></a>SMB-titkosítás Azure-beli virtuális hálózatokon keresztül 

Ha Windows Server 2012 vagy újabb operációs rendszert futtató virtuális gépeken használ [SMB 3,0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) -et, az adatátvitelt biztonságossá teheti az Azure-beli virtuális hálózatokon keresztül továbbított adatforgalom titkosításával. Az adattitkosítással megvédheti a hamisítást és a lehallgatás elleni támadásokat. A rendszergazdák engedélyezhetik az SMB-titkosítást a teljes kiszolgálón vagy csak adott megosztásokon.

Alapértelmezés szerint a megosztások vagy kiszolgálók SMB-titkosításának engedélyezése után csak az SMB 3,0-ügyfelek férhetnek hozzá a titkosított megosztásokhoz.

## <a name="in-transit-encryption-in-vms"></a>Átvitel közbeni titkosítás a virtuális gépeken

A (z), a rendszerből és a Windows rendszerű virtuális gépek közötti, a kapcsolat természetétől függően több módon is titkosítható adatok.

### <a name="rdp-sessions"></a>RDP-munkamenetek

A virtuális géphez a [RDP protokoll (RDP)](/windows/win32/termserv/remote-desktop-protocol) használatával csatlakozhat, és bejelentkezhet egy Windows rendszerű ügyfélszámítógépről vagy egy telepített RDP-ügyféllel rendelkező Mac számítógépen. Az RDP-munkamenetekben a hálózaton keresztül áthaladó adatforgalom védelme a TLS protokollal lehetséges.

A Linux rendszerű virtuális gépekhez való kapcsolódáshoz Távoli asztal is használhatja az Azure-ban.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Biztonságos hozzáférés Linux rendszerű virtuális gépekhez SSH-val

A Távoli felügyelet érdekében a [Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) (SSH) használatával csatlakozhat az Azure-ban futó linuxos virtuális gépekhez. Az SSH egy titkosított kapcsolati protokoll, amely biztonságos bejelentkezéseket tesz lehetővé a nem biztonságos kapcsolatokon keresztül. Ez az alapértelmezett kapcsolati protokoll az Azure-ban üzemeltetett linuxos virtuális gépekhez. Ha SSH-kulcsokat használ a hitelesítéshez, nem kell bejelentkeznie a jelszavakba. Az SSH egy nyilvános/titkos kulcspárt (aszimmetrikus titkosítás) használ a hitelesítéshez.

## <a name="azure-vpn-encryption"></a>Azure VPN-titkosítás

Kapcsolódhat az Azure-hoz egy olyan virtuális magánhálózati hálózaton keresztül, amely biztonságos alagutat hoz létre a hálózaton keresztül továbbított adatok védelmének védelme érdekében.

### <a name="azure-vpn-gateways"></a>Azure VPN-átjárók

Az [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) használatával titkosított forgalmat küldhet a virtuális hálózat és a helyszíni hely között egy nyilvános kapcsolaton keresztül, illetve a virtuális hálózatok közötti adatforgalmat is elküldheti.

A helyek közötti VPN-ek az [IPSec protokollt](https://en.wikipedia.org/wiki/IPsec) használják a továbbítási titkosításhoz. Az Azure VPN-átjárók az alapértelmezett javaslatok készletét használják. Az Azure-beli VPN-átjárókat úgy konfigurálhatja, hogy egyéni IPsec/IKE-házirendet használjanak az egyes titkosítási algoritmusokkal és kulcsokkal, és nem az Azure alapértelmezett házirendjeit.

### <a name="point-to-site-vpns"></a>Pont–hely VPN-ek

A pont – hely VPN-EK lehetővé teszik, hogy az egyes ügyfélszámítógépek hozzáférjenek egy Azure-beli virtuális hálózathoz. A VPN-alagút létrehozásához használja [a Secure Socket Tunneling Protocol (SSTP) protokollt](/previous-versions/technet-magazine/cc162322(v=msdn.10)) . Képes a tűzfalak bejárására (az alagút HTTPS-kapcsolatként jelenik meg). A pont – hely kapcsolathoz saját belső nyilvános kulcsokra épülő infrastruktúra (PKI) legfelső szintű hitelesítésszolgáltató (CA) használható.

A pont – hely VPN-kapcsolat egy virtuális hálózathoz konfigurálható a tanúsítvány-hitelesítéssel vagy a PowerShell-lel Azure Portal használatával.

További információ a pont – hely VPN-kapcsolatokról az Azure Virtual Networks szolgáltatással kapcsolatban:

[Pont – hely kapcsolat konfigurálása egy virtuális hálózathoz a hitelesítési hitelesítés használatával: Azure Portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Pont – hely kapcsolat konfigurálása egy virtuális hálózathoz tanúsítványalapú hitelesítés használatával: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Helyek közötti VPN-EK 

A helyek közötti VPN Gateway-kapcsolattal a helyszíni hálózatot egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül csatlakoztathatja egy Azure-beli virtuális hálózathoz. Az ilyen típusú kapcsolathoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy külső elérésű nyilvános IP-cím.

A helyek közötti VPN-kapcsolat a Azure Portal, a PowerShell vagy az Azure CLI használatával konfigurálható egy virtuális hálózathoz.

További információkért lásd:

[Helyek közötti kapcsolat létrehozása a Azure Portalban](../../vpn-gateway/tutorial-site-to-site-portal.md)

[Helyek közötti kapcsolat létrehozása a PowerShellben](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal a parancssori felület használatával](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Átvitel közbeni titkosítás a Data Lakeban

Az átvitt adatok (azaz a mozgásban lévő adatok) titkosítása is mindig a Data Lake Store-ban történik. Az adatok titkosítása az állandó adathordozón való tárolást megelőzően kívül az adatok a HTTPS protokollon keresztül is mindig az átvitel során lesznek biztosítva. A HTTPS az egyetlen olyan protokoll, amely támogatott a Data Lake Store REST-felületeihez.

Ha többet szeretne megtudni az átvitt adattitkosításról Data Lakeban, tekintse meg az [adattitkosítás Data Lake Storeban](../../data-lake-store/data-lake-store-encryption.md)című témakört.

## <a name="key-management-with-key-vault"></a>Kulcskezelő Key Vault

A kulcsok megfelelő védelme és kezelése nélkül a titkosítás használhatatlanná tehető. A Key Vault a Microsoft által ajánlott megoldás a Cloud Services által használt titkosítási kulcsok elérésének kezelésére és szabályozására. A kulcsok eléréséhez szükséges engedélyek a szolgáltatásokhoz vagy a felhasználókhoz Azure Active Directory fiókokon keresztül rendelhetők hozzá.

A Key Vault leveszi a vállalatok válláról a hardveres biztonsági modulok (HSM-ek) és a kulcskezelő szoftverek konfigurálásának, frissítésének és karbantartásának terhét. Key Vault használatakor a vezérlést is megtarthatja. A Microsoft soha nem látja a kulcsokat, és az alkalmazások nem rendelkeznek közvetlen hozzáféréssel. A HSM-ben is importálhat és létrehozhat kulcsokat.

## <a name="next-steps"></a>Következő lépések

- [Az Azure biztonsági szolgáltatásainak áttekintése](./overview.md)
- [A nagyvállalati hálózati biztonság áttekintése](network-overview.md)
- [Az Azure Database biztonsági áttekintése](../../azure-sql/database/security-overview.md)
- [Az Azure Virtual Machines biztonsági áttekintése](virtual-machines-overview.md)
- [Adat-titkosítás inaktív állapotban](encryption-atrest.md)
- [Az adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárások](data-encryption-best-practices.md)