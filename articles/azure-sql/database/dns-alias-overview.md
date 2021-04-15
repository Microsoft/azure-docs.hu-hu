---
title: DNS-alias
description: Az alkalmazások csatlakozhatnak a kiszolgáló nevének aliashoz a Azure SQL Database. Addig is bármikor módosíthatja az alias SQL Database, hogy megkönnyítse a tesztelést és így tovább.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 6ef268b349d5a21cdbadd55ffd2199a35f650e5b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376290"
---
# <a name="dns-alias-for-azure-sql-database"></a>Dns-alias a Azure SQL Database
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL Database dns-kiszolgálóval rendelkezik. A PowerShell és a REST API-k hívásokat fogadnak el a logikai SQL-kiszolgáló nevéhez szükséges [DNS-aliasok](#anchor-powershell-code-62x) [létrehozásához és kezeléséhez.](logical-servers.md)

A *kiszolgálónév* helyén DNS-alias használható. Az ügyfélprogramok használhatják az aliast a kapcsolati sztringjükben. A DNS-alias egy fordítási réteget biztosít, amely átirányíthatja az ügyfélprogramokat különböző kiszolgálókra. Ez a réteg nem okoz nehézséget az összes ügyfél és a kapcsolati sztringek megkeresése és szerkesztése során.

A DNS-aliasok gyakori használati esete a következő:

- Hozzon létre egy könnyen jegyezhető nevet a kiszolgáló számára.
- A kezdeti fejlesztés során az alias hivatkozhat egy tesztkiszolgálóra. Az alkalmazás éles környezetben való létrehozása után az alias módosításával hivatkozhat az éles kiszolgálóra. A tesztelésről az éles környezetre való váltáshoz nincs szükség a konfigurációk módosítására a kiszolgálóhoz csatlakozó számos ügyfélen.
- Tegyük fel, hogy az alkalmazásban az egyetlen adatbázis egy másik kiszolgálóra van áthelyezve. Az aliast anélkül módosíthatja, hogy több ügyfél konfigurációját is módosítania kell.
- Regionális kimaradás esetén a georedúnis visszaállítással helyreállíthatja az adatbázist egy másik kiszolgálón és régióban. Módosíthatja a meglévő aliast úgy, hogy az új kiszolgálóra mutasson, hogy a meglévő ügyfélalkalmazás újra kapcsolódhat hozzá.

## <a name="domain-name-system-dns-of-the-internet"></a>Az internet tartománynévrendszere (DNS)

Az internet a DNS-t támaszkodik. A DNS lefordítja a rövid neveket a kiszolgáló nevére.

## <a name="scenarios-with-one-dns-alias"></a>Forgatókönyvek egy DNS-aliassal

Tegyük fel, hogy át kell váltania a rendszert egy új kiszolgálóra. Korábban minden ügyfélprogramban meg kellett találnia és frissítenie kellett minden kapcsolati sztringet. Ha azonban a kapcsolati sztringek DNS-aliast használnak, csak az aliastulajdonságokat kell frissíteni.

A dns-alias Azure SQL Database a következő esetekben segíthet:

### <a name="test-to-production"></a>Tesztelés éles környezetbe

Amikor elkezdi az ügyfélprogramok fejlesztését, használjon DNS-aliast a kapcsolati sztringekban. Az alias tulajdonságait a kiszolgáló tesztverziójának megfelelőre kell tenni.

Később, amikor az új rendszer éles környezetben működik, frissítheti az alias tulajdonságait, hogy az éles kiszolgálóra mutasson. Az ügyfélprogramokat nem szükséges módosítani.

### <a name="cross-region-support"></a>Régiók közötti támogatás

Előfordulhat, hogy egy vészhelyreállítás másik földrajzi régióba váltja át a kiszolgálót. DNS-aliast használó rendszerek esetében elkerülhető az összes ügyfél összes kapcsolati sztringének megkeresése és frissítése. Ehelyett frissíthet egy aliast, hogy az az új kiszolgálóra hivatkozik, amely most már a Azure SQL Database.

## <a name="properties-of-a-dns-alias"></a>DNS-alias tulajdonságai

A következő tulajdonságok vonatkoznak a kiszolgáló összes DNS-aliasa esetén:

- *Egyedi név:* Minden létrehozott aliasnév egyedi az összes kiszolgálón, csakúgy, mint a kiszolgálónevek.
- *Kiszolgáló szükséges:* CSAK akkor lehet DNS-aliast létrehozni, ha pontosan egy kiszolgálóra hivatkozik, és a kiszolgálónak már léteznie kell. A frissített aliasnak mindig pontosan egy meglévő kiszolgálóra kell hivatkozni.
  - Amikor eldob egy kiszolgálót, az Azure-rendszer a kiszolgálóra hivatkozó összes DNS-aliast is eldobja.
- *Nincs régióhoz kötve:* A DNS-aliasok nincsenek régióhoz kötve. A DNS-aliasok frissíthetők úgy, hogy bármely földrajzi régióban található kiszolgálóra hivatkozni tudjanak.
  - Ha azonban egy aliast úgy frissít, hogy egy másik kiszolgálóra hivatkozik, mindkét kiszolgálónak ugyanabban az Azure-előfizetésben kell *lennie.*
- *Engedélyek:* DNS-alias kezeléséhez a felhasználónak kiszolgálói közreműködői vagy magasabb szintű engedélyekkel kell rendelkeznie.  További információkért lásd az Azure szerepköralapú [hozzáférés-vezérlésének](../../role-based-access-control/overview.md)első lépésekről a Azure Portal.

## <a name="manage-your-dns-aliases"></a>DNS-aliasok kezelése

A DNS-aliasok programozott kezeléséhez a REST API-k és a PowerShell-parancsmagok is elérhetők.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API-k a DNS-aliasok kezeléséhez

A REST API-k dokumentációja a következő webes hely közelében érhető el:

- [Azure SQL Database REST API](/rest/api/sql/)

A REST API-k a GitHubon a következő webhelyen érhetők el:

- [Azure SQL Database DNS-alias REST API-k létrehozása](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell a DNS-aliasok kezeléséhez

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatott, de minden jövőbeli fejlesztés az Az.Sql modulra fog kifejlesztésére. A parancsmagokért lásd: [AzureRM.Sql.](/powershell/module/AzureRM.Sql/) Az Az modulban és az AzureRm-modulokban található parancsok argumentumai jelentősen megegyeznek.

Elérhetők a REST API-kat hívó PowerShell-parancsmagok.

A DNS-aliasok kezeléséhez használt PowerShell-parancsmagok kód példája a következő dokumentumban található:

- [PowerShell DNS-alias Azure SQL Database](dns-alias-powershell-create.md)

A példakódban használt parancsmagok a következők:

- [New-AzSqlServerDnsAlias:](/powershell/module/az.Sql/New-azSqlServerDnsAlias)Új DNS-aliast hoz létre a Azure SQL Database szolgáltatásrendszerben. Az alias az 1. kiszolgálóra hivatkozik.
- [Get-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Get-azSqlServerDnsAlias)Az 1. kiszolgálóhoz rendelt összes DNS-alias le- és listából.
- [Set-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Set-azSqlServerDnsAlias)Módosítja annak a kiszolgálónak a nevét, amelyre az alias konfigurálva van, az 1. kiszolgálótól a 2. kiszolgálóig.
- [Remove-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias)Távolítsa el a DNS-aliast a 2. kiszolgálóról az alias nevével.

## <a name="limitations"></a>Korlátozások

Jelenleg a DNS-aliasok a következő korlátozásokkal rendelkezik:

- *Legfeljebb 2 perces késleltetés:* Egy DNS-alias frissítése vagy eltávolítása akár 2 percet is igénybe vesz.
  - A rövid késleltetéstől függetlenül az alias azonnal leállítja az ügyfélkapcsolatok az örökölt kiszolgálóra való hivatkozását.
- *DNS-keresés:* Jelenleg az egyetlen mérvadó módja annak, hogy egy adott DNS-alias melyik kiszolgálóra hivatkozik, egy [DNS-keresés végrehajtása.](/windows-server/administration/windows-commands/nslookup)
- _A tábla naplózása nem támogatott:_ Nem használhat DNS-aliast olyan  kiszolgálón, amely adatbázison engedélyezve van a táblavizsgálat.
  - A tábla naplózása elavult.
  - Javasoljuk, hogy lépjen a [Blob naplózása lapra.](../../azure-sql/database/auditing-overview.md)

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

- [Az üzletmenet folytonosságának áttekintése a Azure SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md)beleértve a vészhelyreállítást is.
- [Azure REST API-referencia](/rest/api/azure/)
- [Kiszolgálói DNS-aliasok API](/rest/api/sql/2020-11-01-preview/serverdnsaliases)

## <a name="next-steps"></a>Következő lépések

- [PowerShell DNS-alias Azure SQL Database](dns-alias-powershell-create.md)
