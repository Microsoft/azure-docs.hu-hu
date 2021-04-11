---
title: DNS-alias
description: Az alkalmazások csatlakozni tudnak a Azure SQL Database kiszolgálójának nevéhez tartozó aliashoz. Eközben megváltoztathatja az aliasok SQL Databaseét bármikor, a tesztelés megkönnyítéséhez és így tovább.
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
ms.openlocfilehash: c25a9d69df4786afa6600c4749d02fc148d0f1c3
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385785"
---
# <a name="dns-alias-for-azure-sql-database"></a>A Azure SQL Database DNS-aliasa
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL Database DNS-kiszolgálóval rendelkezik. A PowerShell és a REST API-k fogadnak hívásokat a [logikai SQL-kiszolgáló](logical-servers.md) neveként a [DNS-aliasok létrehozásához és kezeléséhez](#anchor-powershell-code-62x) .

A kiszolgáló neve helyett egy *DNS-alias* is használható. Az ügyfélalkalmazások használhatják az aliast a kapcsolataik karakterláncában. A DNS-alias olyan fordítási réteget biztosít, amely különböző kiszolgálókra irányítja át az ügyfélszoftvereket. Ebben a rétegben az összes ügyfél és a hozzájuk tartozó kapcsolódási karakterláncok megtalálásának és szerkesztésének nehézségei merülnek fel.

A DNS-aliasok gyakori felhasználási módjai a következők lehetnek:

- Hozzon létre egy könnyen megjegyezhető nevet a kiszolgáló számára.
- A kezdeti fejlesztés során az alias egy teszt-kiszolgálóra is hivatkozhat. Ha az alkalmazás életbe lép, módosíthatja az aliast, hogy az éles kiszolgálóra hivatkozzon. A tesztről a termelésre való áttérés nem igényli a konfigurációk módosítását több, a kiszolgálóhoz csatlakozó ügyfél számára.
- Tegyük fel, hogy az alkalmazás egyetlen adatbázisát áthelyezi egy másik kiszolgálóra. Az aliast úgy módosíthatja, hogy ne kelljen módosítania több ügyfél konfigurációját.
- Regionális leállás esetén a Geo-visszaállítás használatával helyreállíthatja az adatbázist egy másik kiszolgálón és régióban. A meglévő alias módosítható úgy, hogy az új kiszolgálóra mutasson, hogy a meglévő ügyfélalkalmazás újra csatlakozhasson hozzá.

## <a name="domain-name-system-dns-of-the-internet"></a>Az Internet Domain Name System (DNS)

Az Internet a DNS-re támaszkodik. A DNS lefordítja a felhasználóbarát neveket a kiszolgáló nevére.

## <a name="scenarios-with-one-dns-alias"></a>Forgatókönyvek egyetlen DNS-aliassal

Tegyük fel, hogy a rendszerét egy új kiszolgálóra kell váltania. A múltban az összes ügyfélprogram összes kapcsolódási karakterláncának megkereséséhez és frissítéséhez szükséges. Ha azonban a kapcsolatok karakterlánca DNS-aliast használ, csak az alias tulajdonságot kell frissíteni.

A Azure SQL Database DNS-alias szolgáltatása a következő helyzetekben nyújt segítséget:

### <a name="test-to-production"></a>Tesztelés éles környezetben

Az ügyfélalkalmazások fejlesztésének megkezdése előtt használjon DNS-aliast a kapcsolódási karakterláncokban. Az alias tulajdonságait a kiszolgáló tesztelési verziójára kell tenni.

Később, amikor az új rendszer éles környezetben üzemel, frissítheti az alias tulajdonságait úgy, hogy az éles kiszolgálóra mutasson. Nincs szükség az ügyfélalkalmazások módosítására.

### <a name="cross-region-support"></a>Régiók közötti támogatás

A vész-helyreállítási lehet, hogy a kiszolgálót egy másik földrajzi régióra váltsa át. A DNS-aliast használó rendszerek esetében az összes ügyfél kapcsolódási karakterláncának megkeresése és frissítése elkerülhető. Ehelyett frissíthet egy aliast arra az új kiszolgálóra, amely most már üzemelteti a Azure SQL Database.

## <a name="properties-of-a-dns-alias"></a>DNS-alias tulajdonságai

A következő tulajdonságok a kiszolgáló minden DNS-aliasára érvényesek:

- *Egyedi név:* Minden létrehozott aliasnév egyedi az összes kiszolgálón, ugyanúgy, mint a kiszolgálók neve.
- *A kiszolgáló megadása kötelező:* Nem hozható létre DNS-alias, ha pontosan egy kiszolgálóra hivatkozik, és a kiszolgálónak már léteznie kell. A frissített aliasoknak mindig pontosan egy meglévő kiszolgálóra kell hivatkoznia.
  - A kiszolgálók eldobásakor az Azure-rendszer a kiszolgálóra hivatkozó összes DNS-aliast is eldobja.
- *Nincs a régióhoz kötve:* A DNS-aliasok nincsenek régióhoz kötve. Bármely DNS-alias frissíthető úgy, hogy egy olyan kiszolgálóra hivatkozzon, amely bármely földrajzi régióban található.
  - Ha azonban egy alias frissítése egy másik kiszolgálóra hivatkozik, mindkét kiszolgálónak ugyanabban az Azure- *előfizetésben* kell lennie.
- *Engedélyek:* A DNS-aliasok kezeléséhez a felhasználónak *kiszolgálói közreműködői* engedélyekkel kell rendelkeznie vagy magasabbnak kell lennie. További információ: [Az Azure szerepköralapú hozzáférés-vezérlésének első lépései a Azure Portal](../../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>A DNS-aliasok kezelése

A REST API-k és a PowerShell-parancsmagok is elérhetők, így lehetővé válik a DNS-aliasok programozott kezelése.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API-k a DNS-aliasok kezeléséhez

A REST API-k dokumentációja a következő webes hely közelében érhető el:

- [Azure SQL Database REST API](/rest/api/sql/)

Emellett a REST API-k a GitHubon is megtekinthetők:

- [Azure SQL Database DNS-alias REST API-k](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell a DNS-aliasok kezeléséhez

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatott, de a jövőbeli fejlesztés az az. SQL modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

A REST API-kat meghívó PowerShell-parancsmagok érhetők el.

A DNS-aliasok kezeléséhez használt PowerShell-parancsmagok például a következő címen vannak dokumentálva:

- [PowerShell DNS-aliashoz Azure SQL Database](dns-alias-powershell-create.md)

A kódban használt parancsmagok a következők:

- [New-AzSqlServerDNSAlias](/powershell/module/az.Sql/New-azSqlServerDnsAlias): új DNS-Alias létrehozása a Azure SQL Database szolgáltatási rendszerben. Az alias az 1. kiszolgálóra hivatkozik.
- [Get-AzSqlServerDNSAlias](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): az 1. kiszolgálóhoz rendelt összes DNS-alias beolvasása és listázása.
- [Set-AzSqlServerDNSAlias](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): módosítja annak a kiszolgálónak a nevét, amelyre az alias hivatkozik, az 1. kiszolgálóról a 2. kiszolgálóra.
- [Remove-AzSqlServerDNSAlias](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): távolítsa el a DNS-aliast a 2. kiszolgálóról az alias nevének használatával.

## <a name="limitations"></a>Korlátozások

A DNS-aliasok jelenléte a következő korlátozásokkal jár:

- *Legfeljebb 2 perc késés:* A DNS-alias frissítése vagy eltávolítása akár 2 percet is igénybe vehet.
  - A rövid késleltetéstől függetlenül az alias azonnal leállítja a hivatkozó ügyfélkapcsolatokat a régi kiszolgálóval.
- *DNS-címkeresés:* Egyelőre az egyetlen mérvadó módszer annak ellenőrzéséhez, hogy egy adott DNS-alias melyik kiszolgálóra hivatkozik a [DNS-címkeresés](/windows-server/administration/windows-commands/nslookup)végrehajtásával.
- _A tábla naplózása nem támogatott:_ Nem használhat DNS-aliast olyan kiszolgálón, amelyen engedélyezve van a *tábla naplózása* egy adatbázison.
  - A tábla naplózása elavult.
  - Javasoljuk, hogy térjen át a [blob-naplózásra](../../azure-sql/database/auditing-overview.md).

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

- [A Azure SQL Database üzleti folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md), beleértve a vész-helyreállítást is.
- [Azure REST API-referencia](/rest/api/azure/)
- [Kiszolgálói DNS-aliasok API](/rest/api/sql/2020-11-01-preview/serverdnsaliases)

## <a name="next-steps"></a>Következő lépések

- [PowerShell DNS-aliashoz Azure SQL Database](dns-alias-powershell-create.md)
