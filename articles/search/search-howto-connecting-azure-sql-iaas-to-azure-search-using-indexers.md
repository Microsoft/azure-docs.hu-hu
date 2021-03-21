---
title: Azure SQL VM-kapcsolatok a keresés indexeléséhez
titleSuffix: Azure Cognitive Search
description: Engedélyezze a titkosított kapcsolatokat, és konfigurálja a tűzfalat úgy, hogy engedélyezze az Azure-beli virtuális gépeken (VM) SQL Server való csatlakozást az Azure-beli Cognitive Search.
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: 1f9169d4f3f6361e557c41a4d612cf6c439257fb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722514"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Azure Cognitive Search indexelő használatával létesített kapcsolatok konfigurálása Azure-beli virtuális gépeken való SQL Server

Ha úgy konfigurálja az [Azure SQL indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq) szolgáltatást, hogy egy Azure-beli virtuális gépen lévő adatbázisból kinyerje a tartalmat, további lépésekre van szükség a biztonságos kapcsolatokhoz. 

Az Azure Cognitive Search és a virtuális gépek közötti SQL Server egy nyilvános internetkapcsolat. Ahhoz, hogy a biztonságos kapcsolatok sikeresek legyenek, hajtsa végre a következő lépéseket:

+ Tanúsítvány beszerzése egy hitelesítésszolgáltató [szolgáltatótól](https://en.wikipedia.org/wiki/Certificate_authority#Providers) a SQL Server példány teljes tartománynevéhez a virtuális gépen

+ Telepítse a tanúsítványt a virtuális gépre, majd a jelen cikkben található utasítások alapján engedélyezze és konfigurálja a titkosított kapcsolatokat a virtuális gépen.

## <a name="enable-encrypted-connections"></a>Titkosított kapcsolatok engedélyezése

Az Azure Cognitive Search minden indexelő kérelemhez titkosított csatornát igényel nyilvános internetkapcsolaton keresztül. Ez a szakasz a működésének lépéseit sorolja fel.

1. Ellenőrizze a tanúsítvány tulajdonságait annak ellenőrzéséhez, hogy a tulajdonos neve az Azure-beli virtuális gép teljes tartományneve (FQDN). A tulajdonságok megtekintéséhez használhatja a CertUtils vagy a Certificates beépülő modult. A teljes tartománynevet a virtuálisgép-szolgáltatás panel Essentials szakaszának "a **nyilvános IP-cím/DNS-név címkéje** " mezőjében érheti el a [Azure Portal](https://portal.azure.com/).
  
   + Az újabb **Resource Manager** -sablonnal létrehozott virtuális gépek esetében a teljes tartománynevet a következő formátumban kell formázni: `<your-VM-name>.<region>.cloudapp.azure.com`

   + A **klasszikus** virtuális gépként létrehozott régebbi virtuális gépek esetében a teljes tartománynév a következőként van formázva: `<your-cloud-service-name.cloudapp.net>` .

1. Konfigurálja SQL Server a tanúsítvány használatára a Beállításszerkesztő (Regedit) használatával. 

   Bár a feladathoz gyakran SQL Server Konfigurációkezelő van használatban, ezt a forgatókönyvet nem használhatja. Nem találja az importált tanúsítványt, mert az Azure-beli virtuális gép teljes tartományneve nem egyezik meg a virtuális gép által meghatározott teljes tartománynévvel (a tartományt a helyi számítógépnek vagy a hálózati tartománynak, amelyhez csatlakozik). Ha a nevek nem egyeznek, a regedit használatával adhatja meg a tanúsítványt.

   + A regeditben keresse meg a következő beállításkulcsot: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate` .

     A `[MSSQL13.MSSQLSERVER]` rész a verzió és a példány neve alapján változik. 

   + Állítsa be a **tanúsítvány** kulcsának értékét a virtuális GÉPRE importált TLS/SSL-tanúsítvány **ujjlenyomatára** .

     Több módon is megszerezheti az ujjlenyomatot, ami jobb, mint a többi. Ha a **tanúsítványkezelő** beépülő modulból másolja azt az MMC-ben, akkor valószínűleg egy láthatatlan vezető karaktert fog felvenni a [jelen támogatási cikkben leírtak szerint](https://support.microsoft.com/kb/2023869/), ami hibát eredményez a kapcsolódás megkísérlése során. A probléma megoldásához több Áthidaló megoldás is létezik. A legkönnyebben a BACKSPACE billentyűt lenyomva, majd újra be kell írnia az ujjlenyomat első karakterét, hogy eltávolítsa a kezdő karaktert a regedit kulcs értéke mezőjében. Másik megoldásként másik eszközt is használhat az ujjlenyomat másolásához.

1. Engedélyek megadása a szolgáltatásfiók számára. 

    Győződjön meg arról, hogy az SQL Server-szolgáltatásfiók megfelelő engedélyekkel rendelkezik a TLS/SSL-tanúsítvány titkos kulcsához. Ha figyelmen kívül hagyja ezt a lépést, SQL Server nem fog elindulni. Ehhez a feladathoz használhatja a **tanúsítványok** beépülő modult vagy a **certutilt** .

1. Indítsa újra az SQL Server szolgáltatást.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>SQL Server-kapcsolat konfigurálása a virtuális gépen

Az Azure Cognitive Search által igényelt titkosított kapcsolatok beállítása után az Azure-beli virtuális gépeken SQL Server további konfigurációs lépések is elérhetők. Ha még nem tette meg, a következő lépés a konfiguráció befejezése az alábbi cikkek valamelyikének használatával:

+ **Resource Manager** -alapú virtuális gépek esetén lásd: [Kapcsolódás SQL Server virtuális géphez az Azure-ban a Resource Manager használatával](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md). 

+ **Klasszikus** virtuális gép esetén lásd: [Kapcsolódás SQL Server virtuális géphez a klasszikus Azure](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect)-ban.

Tekintse át a "kapcsolódás az internethez" című cikket az egyes cikkekben.

## <a name="configure-the-network-security-group-nsg"></a>A hálózati biztonsági csoport (NSG) konfigurálása

Nem szokatlan, hogy konfigurálja a NSG és a megfelelő Azure-végpontot vagy Access Control listát (ACL) ahhoz, hogy az Azure-beli virtuális gép elérhető legyen a többi fél számára. Ezt csak akkor teheti meg, ha engedélyezi a saját alkalmazás logikáját a SQL Azure virtuális géphez való kapcsolódáshoz. Nem különbözik az Azure Cognitive Search a SQL Azure virtuális géphez való kapcsolódáshoz. 

Az alábbi hivatkozások a virtuális gépek központi telepítésének NSG-konfigurációját ismertetik. Ezek az utasítások az Azure Cognitive Search végpontok IP-címe alapján történő hozzáférés-vezérlésére használhatók.

> [!NOTE]
> A háttérben tekintse meg a [Mi az a hálózati biztonsági csoport?](../virtual-network/network-security-groups-overview.md) című témakört.

+ **Resource Manager** -alapú virtuális gépek esetén lásd: [NSG létrehozása ARM](../virtual-network/tutorial-filter-network-traffic.md)-környezetekhez.

+ **Klasszikus** virtuális gép esetén lásd: [NSG létrehozása klasszikus központi telepítésekhez](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps).

Az IP-címzés néhány olyan kihívást jelenthet, amely könnyen leküzdhető, ha tisztában van a problémával és a lehetséges megkerülő megoldásokkal. A fennmaradó szakaszokban az ACL IP-címeivel kapcsolatos problémák kezelésére vonatkozó ajánlásokat talál.

### <a name="restrict-access-to-the-azure-cognitive-search"></a>Az Azure-Cognitive Search való hozzáférés korlátozása

Erősen ajánlott korlátozni a keresési szolgáltatás IP-címéhez való hozzáférést és a `AzureCognitiveSearch` [szolgáltatási címke](../virtual-network/service-tags-overview.md#available-service-tags) IP-CÍMTARTOMÁNY használatát az ACL-ben ahelyett, hogy a SQL Azure virtuális gépek minden kapcsolódási kérelem számára nyitva legyenek.

Az IP-címet a keresési szolgáltatás teljes tartománynevének (például:) pingelésével keresheti meg `<your-search-service-name>.search.windows.net` . Habár lehetséges, hogy a keresési szolgáltatás IP-címe megváltozik, nem valószínű, hogy meg fog változni. Az IP-cím általában statikus a szolgáltatás élettartama szempontjából.

A `AzureCognitiveSearch` [Service tag](../virtual-network/service-tags-overview.md#available-service-tags) IP-CÍMTARTOMÁNY a [letölthető JSON-fájlok](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) használatával vagy a [Service tag Discovery API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview)-n keresztül található meg. Az IP-címtartomány hetente frissül.

### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Az Azure Cognitive Search-portál IP-címeinek belefoglalása

Ha az Azure Portalt használja az indexelő létrehozásához, az Azure Cognitive Search Portal Logic is hozzáférést igényel a SQL Azure virtuális géphez a létrehozás ideje alatt. Az Azure Cognitive Search-portál IP-címei a pingeléssel találhatók `stamp2.search.ext.azure.com` , amely a Traffic Manager tartománya.

A különböző régiókban lévő fürtök csatlakoznak ehhez a Traffic Managerhez. A pingelés az IP-címet és a tartományát is visszaküldheti `stamp2.search.ext.azure.com` , de ha a szolgáltatás egy másik régióban található, akkor az IP-cím és a tartománynév eltérő lesz. A pingelésből visszaadott IP-cím a megfelelő Azure Portal a régiójában.

## <a name="next-steps"></a>Következő lépések

Az Azure-beli Cognitive Search indexelő adatforrásaként megadhat egy SQL Servert az Azure-beli virtuális gépen. További információ: [Azure SQL Database csatlakoztatása az Azure Cognitive Search az indexelő használatával](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).