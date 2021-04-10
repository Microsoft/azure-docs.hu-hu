---
title: Az Azure SQL felügyelt példányainak időzónái
description: Tudnivalók az Azure SQL felügyelt példányának időzóna-specifikus jellemzőiről
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 10/12/2020
ms.openlocfilehash: b4ba5f50f5c66b404ec76128a828a1060328f81c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690991"
---
# <a name="time-zones-in-azure-sql-managed-instance"></a>Az Azure SQL felügyelt példányának időzónái
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az egyezményes világidő (UTC) a felhőalapú megoldások adatszintjéhez ajánlott időzóna. Az Azure SQL felügyelt példánya a dátum-és időértékeket tároló meglévő alkalmazások igényeinek kielégítésére, valamint a dátum-és időfüggvények meghívására is lehetőséget nyújt az adott időzóna implicit környezetében.

A T-SQL függvények, például a [getdate ()](/sql/t-sql/functions/getdate-transact-sql) vagy a CLR-kód betartják a példány szintjén beállított időzónát. SQL Server Agent a feladatok a példány időzónája szerint is követik az ütemtervet.

  > [!NOTE]
  > A Azure SQL Database nem támogatja az időzóna-beállításokat; mindig UTC-t követ. Ha a dátumot és időpontot nem UTC időzónában szeretné értelmezni, használja a SQL Database [IDŐzónában](/sql/t-sql/queries/at-time-zone-transact-sql) .

## <a name="supported-time-zones"></a>Támogatott időzónák

A támogatott időzónák készlete örökölt a felügyelt példány mögöttes operációs rendszertől. Rendszeresen frissül az új Időzóna-definíciók beszerzése és a meglévők változásainak tükrözése.

A [nyári időszámítási idő/időzóna-változások házirendje](/troubleshoot/windows-client/system-management-components/daylight-saving-time-help-support) az 2010-ig terjedő korábbi pontosságot biztosítja.

A támogatott időzónák nevét tartalmazó lista a [sys.time_zone_info](/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) rendszernézeten keresztül érhető el.

## <a name="set-a-time-zone"></a>Időzóna beállítása

A felügyelt példányok időzónája a példány létrehozásakor állítható be. Az alapértelmezett időzóna UTC.

  >[!NOTE]
  > Egy meglévő felügyelt példány időzónája nem módosítható.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Az időzóna beállítása a Azure Portal

Új példány paramétereinek megadásakor válasszon ki egy időzónát a támogatott időzónák listájából.
  
![Időzóna beállítása a példány létrehozásakor](./media/timezones-overview/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Adja meg az timezoneId tulajdonságot a [Resource Manager-sablonban](./create-template-quickstart.md) az időzóna beállításához a példány létrehozásakor.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

A timezoneId tulajdonság támogatott értékeinek listája a cikk végén található.

Ha nincs megadva, az időzóna UTC értékre van állítva.

## <a name="check-the-time-zone-of-an-instance"></a>Példány időzónájának keresése

A [CURRENT_TIMEZONE](/sql/t-sql/functions/current-timezone-transact-sql) függvény a példány időzónájának megjelenített nevét adja vissza.

## <a name="cross-feature-considerations"></a>Szolgáltatások közötti megfontolások

### <a name="restore-and-import"></a>Visszaállítás és importálás

Visszaállíthat egy biztonságimásolat-fájlt, vagy importálhat egy felügyelt példányra egy példányból vagy egy másik időzóna-beállításokkal rendelkező kiszolgálóról. Ügyeljen rá, hogy körültekintően járjon el. Elemezheti az alkalmazások viselkedését és a lekérdezések és jelentések eredményét, ugyanúgy, mint amikor két SQL Server példány közötti adatátvitelt végez különböző időzóna-beállításokkal.

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

Ha időponthoz tartozó visszaállítást végez, a visszaállítási idő UTC-időként lesz értelmezve. Így elkerülhetők a nyári időszámítások és a lehetséges változások miatti esetleges kétségek.

### <a name="auto-failover-groups"></a>Automatikus feladatátvételi csoportok

Egy feladatátvételi csoportban lévő elsődleges és másodlagos példányon azonos időzóna használata nem kényszerített, de erősen ajánlott.

  >[!WARNING]
  > Azt javasoljuk, hogy a feladatátvételi csoportban lévő elsődleges és másodlagos példányhoz ugyanazt az időzónát használja. Bizonyos ritka használati esetek miatt az elsődleges és a másodlagos példányok közötti egyidejű zónát nem kényszeríti ki. Fontos tisztában lenni azzal, hogy manuális vagy automatikus feladatátvétel esetén a másodlagos példány megtartja az eredeti időzónáját.

## <a name="limitations"></a>Korlátozások

- A meglévő felügyelt példány időzónája nem módosítható. Megkerülő megoldásként hozzon létre egy új felügyelt példányt a megfelelő időzónával, majd végezze el a manuális biztonsági mentést és visszaállítást, vagy azt, amit ajánlunk, végezze el az adott [példány időpontjának visszaállítását](./point-in-time-restore.md?tabs=azure-portal#restore-an-existing-database).
- A SQL Server Agenti feladatokból indított külső folyamatok nem veszik figyelembe a példány időzónáját.

## <a name="list-of-supported-time-zones"></a>Támogatott időzónák listája

| **Időzóna-azonosító** | **Időzóna megjelenítendő neve** |
| --- | --- |
| Dátumvonali téli idő | (UTC-12:00) Nemzetközi dátumválasztó vonal (Nyugat) |
| UTC – 11 | (UTC-11:00) Egyezményes világidő-11 |
| Aleut téli idő | (UTC-10:00) Aleut-szigetek |
| Hawaii téli idő | (UTC-10:00) Hawaii |
| Marquesas téli idő | (UTC-09:30) Marquesas-szigetek |
| Alaszkai téli idő | (UTC-09:00) Alaszka |
| UTC-09 | (UTC-09:00) Egyezményes világidő-09 |
| Csendes-óceáni téli idő (Mexikó) | (UTC-08:00) Baja California |
| UTC – 08 | (UTC-08:00) Egyezményes világidő-08 |
| Csendes-óceáni téli idő | (UTC-08:00) Csendes-óceáni idő (USA & Kanada) |
| Amerikai hegyi téli idő | (UTC-07:00) Arizona |
| Hegyi téli idő (Mexikó) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Hegyi téli idő | (UTC-07:00) Hegyi idő (Egyesült Államok & Kanada) |
| Közép-amerikai téli idő | (UTC-06:00) Közép-Amerika |
| Központi téli idő | (UTC-06:00) Központi idő (USA & Kanada) |
| Húsvét-szigeti téli idő | (UTC-06:00) Húsvét-sziget |
| Közép-téli idő (Mexikó) | (UTC-06:00) Guadalajara, Mexikóváros, Monterrey |
| Kanadai középső téli idő | (UTC-06:00) Saskatchewan |
| SA csendes-óceáni téli idő | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Keleti téli idő (Mexikó) | (UTC-05:00) Chetumal |
| Keleti téli idő | (UTC-05:00) Keleti idő (USA & Kanada) |
| Haiti téli idő | (UTC-05:00) Haiti |
| Kubai téli idő | (UTC-05:00) Havanna |
| USA keleti téli idő | (UTC-05:00) Indiana (kelet) |
| Turks-és Caicos-i téli idő | (UTC-05:00) Turks-és Caicos-szigetek |
| Paraguayi téli idő | (UTC-04:00) Asunción |
| Atlanti-óceáni téli idő | (UTC-04:00) Atlanti-óceáni idő (Kanada) |
| Venezuelai téli idő | (UTC-04:00) Caracas |
| Közép-brazíliai téli idő | (UTC-04:00) Cuiaba |
| Dél-nyugati téli idő | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Pacific SA – téli idő | (UTC-04:00) Santiago |
| Fundland téli idő | (UTC-03:30) Fundland |
| Tocantins téli idő | (UTC-03:00) Araguaina |
| E. Dél-amerikai téli idő | (UTC-03:00) Brazíliaváros |
| Dél-amerikai keleti téli idő | (UTC-03:00) Cayenne, Fortaleza |
| Argentínai téli idő | (UTC-03:00) Buenos Aires városa |
| Grönlandi téli idő | (UTC-03:00) Grönland |
| Montevideoi téli idő | (UTC-03:00) Montevideoi |
| Magallanesi téli idő | (UTC-03:00) Punta Arenas |
| Saint-Pierre téli idő | (UTC-03:00) Saint-Pierre és Miquelon |
| Bahiai téli idő | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Egyezményes világidő-02 |
| Mid-Atlantic téli idő | (UTC-02:00) Mid-Atlantic-régi |
| Azori-szigeteki téli idő | (UTC-01:00) Azori-szigetek |
| Cape Verdei téli idő | (UTC-01:00) A Cabo Verde. |
| UTC | (UTC) Egyezményes világidő |
| GREENWICHi téli idő | (UTC + 00:00) Dublin, Edinburgh, Lisszabon, London |
| Greenwichi téli idő | (UTC + 00:00) Monrovia, Reykjavik |
| W. Európai téli idő | (UTC + 01:00) Amszterdam, Berlin, Bern, Róma, Stockholm, Bécs |
| Közép-európai téli idő | (UTC + 01:00) Belgrád, Pozsony, Budapest, Ljubljana, Prága |
| Romantikus téli idő | (UTC + 01:00) Brüsszel, Koppenhága, Madrid, Párizs |
| Marokkói téli idő | (UTC + 01:00) Casablanca |
| Sao Tomei téli idő | (UTC + 01:00) Sao Tome |
| Közép-európai téli idő | (UTC + 01:00) Szarajevó, Szkopje, Varsó, Zágráb |
| W. Közép-afrikai téli idő | (UTC + 01:00) Nyugat-Közép-Afrika |
| Jordán téli idő | (UTC + 02:00) Ammán |
| GTB téli idő | (UTC + 02:00) Athén, Bukarest |
| Közel-keleti téli idő | (UTC + 02:00) Bejrút |
| Egyiptomi téli idő | (UTC + 02:00) Cairo |
| E. Európai téli idő | (UTC + 02:00) Chisinau |
| Szíriai téli idő | (UTC + 02:00) Damaszkusz |
| Nyugati banki téli idő | (UTC + 02:00) Gáza, Hebron |
| Dél-afrikai téli idő | (UTC + 02:00) Harare, Pretoria |
| FLE téli idő | (UTC + 02:00) Helsinki, Kijev, Riga, Szófia, Tallinn, Vilnius |
| Izraeli téli idő | (UTC + 02:00) Jeruzsálem |
| Kalinyingrádi téli idő | (UTC + 02:00) Kaliningrad |
| Szudáni téli idő | (UTC + 02:00) Khartoum |
| Líbiai téli idő | (UTC + 02:00) Tripoli |
| Namíbiai téli idő | (UTC + 02:00) Windhoek |
| Arab téli idő | (UTC + 03:00) Bagdad |
| Törökországi téli idő | (UTC + 03:00) Isztambul |
| Arab téli idő | (UTC + 03:00) Kuvait, Rijád |
| Fehérorosz téli idő | (UTC + 03:00) Minszk |
| Orosz téli idő | (UTC + 03:00) Moszkva, Szentpétervár |
| E. Afrikai téli idő | (UTC + 03:00) Nairobi |
| Iráni téli idő | (UTC + 03:30) Teherán |
| Arab téli idő | (UTC + 04:00) Abu Dhabi, Muscat |
| Asztrahán téli idő | (UTC + 04:00) Asztrahán, Ulyanovsk |
| Azerbajdzsáni téli idő | (UTC + 04:00) Baku |
| Oroszországi idő 3. zóna | (UTC + 04:00) Izhevsk, Samara |
| Mauritiusi téli idő | (UTC + 04:00) Louis-i port |
| Saratov téli idő | (UTC + 04:00) Saratov |
| Grúz téli idő | (UTC + 04:00) Tbiliszi |
| Volgográd téli idő | (UTC + 04:00) Volgográd |
| Kaukázusi téli idő | (UTC + 04:00) Jereván |
| Afganisztáni téli idő | (UTC + 04:30) Kabul |
| Nyugat-ázsiai téli idő | (UTC + 05:00) Aşgabat, Taskent |
| Jekatyerinburgi téli idő | (UTC + 05:00) Ekaterinburg |
| Pakisztáni téli idő | (UTC + 05:00) Iszlámábád, Karacsi |
| Indiai téli idő | (UTC + 05:30) Chennai, Kolkata, Mumbai, Új-Delhi |
| Srí Lanka-i téli idő | (UTC + 05:30) Sri Jayawardenepura |
| Nepáli téli idő | (UTC + 05:45) Kathmandu |
| Közép-ázsiai téli idő | (UTC + 06:00) Asztana |
| Bangladesi téli idő | (UTC + 06:00) Dakka |
| Omszki téli idő | (UTC + 06:00) Omszki |
| Mianmari téli idő | (UTC + 06:30) Yangon (Rangoon) |
| Délkelet-ázsiai téli idő | (UTC + 07:00) Bangkok, Hanoi, Jakarta |
| Az az-i téli idő | (UTC + 07:00) Barnaul, Gorno-Altaysk |
| W. Mongóliai téli idő | (UTC + 07:00) Hovd |
| Észak-ázsiai téli idő | (UTC + 07:00) Krasznojarszk |
| N. Közép-ázsiai téli idő | (UTC + 07:00) Novoszibirszk |
| Tomsk téli idő | (UTC + 07:00) Tomszk |
| Kínai téli idő | (UTC + 08:00) Peking, Chongqing, Hongkong, Urumqi |
| Észak-ázsiai keleti téli idő | (UTC + 08:00) Irkutsk |
| Szingapúri téli idő | (UTC + 08:00) Kuala Lumpur, Szingapúr |
| W. Ausztráliai téli idő | (UTC + 08:00) Perth |
| Tajpeji téli idő | (UTC + 08:00) Taipei |
| Ulánbátor téli idő | (UTC + 08:00) Ulánbátor |
| Közép-ausztráliai téli idő | (UTC + 08:45) Eucla |
| Transzbajkáli téli idő | (UTC + 09:00) Csita |
| Tokiói téli idő | (UTC + 09:00) Osaka, Sapporo, Tokió |
| Észak-koreai téli idő | (UTC + 09:00) Phenjan |
| Koreai téli idő | (UTC + 09:00) Szöul |
| Yakutsk téli idő | (UTC + 09:00) Yakutsk |
| CEN. Ausztráliai téli idő | (UTC + 09:30) Adelaide |
| Közép-AUSZTRÁLIAI téli idő | (UTC + 09:30) Darwin |
| E. Ausztráliai téli idő | (UTC + 10:00) Brisbane |
| AUSZTRÁLIAI keleti téli idő | (UTC + 10:00) Canberra, Melbourne, Sydney |
| Nyugat-csendes-óceáni téli idő | (UTC + 10:00) Guam, Port Moresby |
| Tasmaniai téli idő | (UTC + 10:00) Hobart |
| Vlagyivosztoki téli idő | (UTC + 10:00) Vlagyivosztok |
| Lord Howe – téli idő | (UTC + 10:30) Lord Howe-sziget |
| Bougainville téli idő | (UTC + 11:00) Bougainville-sziget |
| Orosz időzóna, 10 | (UTC + 11:00) Csokurdah |
| Önmagát téli idő | (UTC + 11:00) Magadan |
| Norfolki téli idő | (UTC + 11:00) Norfolk-sziget |
| Szahalin téli idő | (UTC + 11:00) Szahalin |
| Közép-csendes-óceáni téli idő | (UTC + 11:00) Salamon:, Új-Kaledónia |
| Orosz időzóna, 11 | (UTC + 12:00) Anadíri, Petropavlovsk-Kamchatsky |
| Új-zélandi téli idő | (UTC + 12:00) Auckland, Wellington |
| UTC + 12 | (UTC + 12:00) Egyezményes világidő + 12 |
| Fidzsi-szigeteki téli idő | (UTC + 12:00) Fidzsi |
| Kamcsatkai téli idő | (UTC + 12:00) Petropavlovsk-Kamchatsky-régi |
| Chatham-szigeteki téli idő | (UTC + 12:45) Chatham-szigetek |
| UTC + 13 | (UTC + 13:00) Egyezményes világidő + 13 |
| Tonga-i téli idő | (UTC + 13:00) Nuku ' Alofa |
| Szamoai téli idő | (UTC + 13:00) Szamoa |
| Line-szigeteki téli idő | (UTC + 14:00) Karácsony-sziget |

## <a name="see-also"></a>Lásd még 

- [CURRENT_TIMEZONE (Transact-SQL)](/sql/t-sql/functions/current-timezone-transact-sql)
- [CURRENT_TIMEZONE_ID (Transact-SQL)](/sql/t-sql/functions/current-timezone-id-transact-sql)
- [Időzóna (Transact-SQL)](/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)