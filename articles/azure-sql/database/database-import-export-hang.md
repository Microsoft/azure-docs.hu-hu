---
title: Az adatbázisok importálása és exportálása hosszú időt vesz igénybe
description: A Azure SQL Database és az Azure SQL felügyelt példányának importálási/exportálási szolgáltatása hosszú időt vesz igénybe az adatbázisok importálásához vagy exportálásához
ms.custom: seo-lt-2019, sqldbrb=1
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: e69bba858ccf62f1b3a3b45b08771ddba71f11cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92671395"
---
# <a name="azure-sql-database-and-managed-instance-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>A Azure SQL Database és a felügyelt példány importálási/exportálási szolgáltatása hosszú időt vesz igénybe az adatbázisok importálásához vagy exportálásához.

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Az import/export szolgáltatás használatakor a folyamat a vártnál több időt is igénybe vehet. Ez a cikk a késés és az alternatív megoldási módszerek lehetséges okait ismerteti.

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database importálási/exportálási szolgáltatás

A Azure SQL Database import/export szolgáltatás egy REST-alapú webszolgáltatás, amely minden Azure-adatközpontban fut. A szolgáltatás akkor lesz meghívva, ha az adatbázis [importálása](database-import.md#using-azure-portal) vagy [exportálása](./database-import.md#using-azure-portal) lehetőséget használja az adatbázis Azure Portal való áthelyezéséhez. A szolgáltatás ingyenes kérések üzenetsor-kezelő és számítási szolgáltatásokat biztosít a Azure SQL Database és az Azure Blob Storage közötti importálás és exportálás végrehajtásához.

Az importálási és exportálási műveletek nem a hagyományos fizikai adatbázis biztonsági mentését jelentik, hanem a speciális BACPAC formátumot használó adatbázis logikai biztonsági mentését. A BACPAC formátuma lehetővé teszi, hogy ne kelljen olyan fizikai formátumot használnia, amely a Microsoft SQL Server, a Azure SQL Database és az Azure SQL felügyelt példányának verziói között változhat.

## <a name="what-causes-delays-in-the-process"></a>Mi okozza a folyamat késéseit?

A Azure SQL Database importálási/exportálási szolgáltatás korlátozott számú számítási virtuális gépet (VM) kínál régiónként az importálási és exportálási műveletek feldolgozásához. A számítási virtuális gépek régiónként vannak tárolva, így biztos lehet benne, hogy az Importálás vagy az Exportálás elkerüli a régiók közötti sávszélesség késését és díját. Ha túl sok kérést hajtanak végre ugyanabban a régióban, a műveletek feldolgozásakor jelentős késések merülhetnek fel. A kérelmek teljesítéséhez szükséges idő néhány másodperctől akár több óráig is változhat.

> [!NOTE]
> Ha a kérést négy napon belül nem dolgozzák fel, a szolgáltatás automatikusan megszakítja a kérést.

## <a name="recommended-solutions"></a>Javasolt megoldások

Ha az adatbázis-exportálást csak a véletlen adattörlésből történő helyreállításra használja, az összes Azure SQL Database kiadás a rendszer által létrehozott biztonsági másolatok önkiszolgáló visszaállítási funkcióját biztosítja. Ha azonban szükség van ezekre az exportálásokra más okokból, és ha következetesen gyorsabb vagy kiszámítható importálási/exportálási teljesítményre van szüksége, vegye figyelembe a következő lehetőségeket:

* [EXPORTÁLÁS BACPAC-fájlba az SQLPackage segédprogram használatával](./database-export.md#sqlpackage-utility).
* [EXPORTÁLÁS BACPAC-fájlba SQL Server Management Studio (SSMS) használatával](./database-export.md#sql-server-management-studio-ssms).
* Futtassa a BACPAC Importálás vagy exportálás közvetlenül a kódban a Microsoft SQL Server Data-Tier Application Framework (DacFx) API használatával. További információt a következő témakörben talál:
  * [Adatrétegbeli alkalmazás exportálása](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft. SqlServer. DAC névtér](/dotnet/api/microsoft.sqlserver.dac)
  * [DACFx letöltése](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-a-database"></a>Az adatbázisok exportálásakor vagy importálásakor megfontolandó szempontok

* A cikkben tárgyalt összes módszer az adatbázis-tranzakciós egység (DTU) kvótáját használja, amely a Azure SQL Database szolgáltatás általi szabályozást okoz. Az [adatbázis DTU-statisztikáit a Azure Portal tekintheti meg](./monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring). Ha az adatbázis elérte az erőforrás-korlátozásokat, [frissítse a szolgáltatási szintet](./scale-resources.md) további erőforrások hozzáadásához.
* Ideális esetben az ügyfélalkalmazások (például a sqlpackage segédprogram vagy az egyéni DAC-alkalmazás) egy olyan virtuális gépről futnak, amely ugyanabban a régióban található, mint az adatbázis. Ellenkező esetben a hálózati késéssel kapcsolatos teljesítményproblémák merülhetnek fel.
* A nagyméretű táblák fürtözött indexek nélküli exportálása nagyon lassú lehet, vagy akár hibát okozhat. Ez a viselkedés azért fordul elő, mert a tábla nem bontható fel és nem exportálható párhuzamosan. Ehelyett egyetlen tranzakcióban kell exportálni, ami lassú teljesítményt és lehetséges meghibásodást okoz az exportálás során, különösen nagy táblák esetén.


## <a name="related-documents"></a>Kapcsolódó dokumentumok

[Az adatbázisok exportálásának szempontjai](./database-export.md#considerations)