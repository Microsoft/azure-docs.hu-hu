---
title: Azure-foglalás vásárlása
description: Ismerje meg a fontosabb szempontokat, amelyek segíthetnek az Azure-foglalás vásárlásakor.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/12/2021
ms.author: banders
ms.openlocfilehash: b2cdb106ab1c77c23626258f7e0708a20ffd3c4e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772252"
---
# <a name="buy-a-reservation"></a>Foglalás vásárlása

Az Azure Reservationsszel csökkentheti költségeit, ha több Azure-erőforrásra egy vagy három évre előre kötelezettséget vállal. Mielőtt elkötelezné magát a foglalásvásárlás mellett, tekintse át a következő szakaszokat, hogy felkészülhessen a vásárlásra.

## <a name="who-can-buy-a-reservation"></a>Ki vásárolhat foglalást?

Egy foglalás megvásárlásához tulajdonosi vagy foglalásvásárlói szerepkörrel kell rendelkeznie egy nagyvállalati (MS-AZR-0017P vagy MS-AZR-0148P) vagy használatalapú fizetéses (MS-AZR-0003P vagy MS-AZR-0023P) vagy Microsoft-ügyfélszerződéses Azure-előfizetésben. A felhőszolgáltatók az Azure Portalt vagy a  [Partnerközpontot](/partner-center/azure-reservations)  használhatják az Azure Reservations megvásárlásához.

A Nagyvállalati Szerződéssel (EA) rendelkező ügyfelek az EA-rendszergazdákra korlátozhatják a vásárlásokat a **Fenntartott példányok hozzáadása** beállítás EA Portalon történő letiltásával. Foglalás vásárlásához az EA-rendszergazdáknak tulajdonosi vagy foglalásvásárlói hozzáféréssel kell rendelkezniük legalább egy EA-előfizetésben. Ez a beállítás olyan vállalatok számára hasznos, amelyek azt szeretnék, hogy egy központi csapat vásárolja meg a foglalásokat.

A foglalási kedvezmény csak olyan erőforrásokra érvényes, amelyek nagyvállalati, felhőszolgáltatói (CSP), Microsoft-ügyfélszerződéses vagy egyéni, használatalapú díjas csomagokkal lettek megvásárolva.

## <a name="scope-reservations"></a>A foglalások hatókörének beállítása

A foglalásokhoz egy előfizetést vagy erőforráscsoportokat állíthat be hatókörként. A foglalás hatókörének beállítása megadja a foglalási kedvezmények alkalmazásának helyét. Ha egy erőforráscsoportot állít be a foglalás hatóköreként, akkor a foglalási kedvezmények csak az erőforráscsoportra érvényesek, nem a teljes előfizetésre.

### <a name="reservation-scoping-options"></a>A foglalás hatókörkezelési lehetőségei

Az igényeitől függően három lehetősége van a foglalás hatókörének beállítására:

- **Egyetlen erőforráscsoport hatókör** – A foglalási kedvezményt csak a kiválasztott erőforráscsoportban található egyező erőforrásokra alkalmazza.
- **Egy előfizetésre kiterjedő hatókör** – A foglalási kedvezményt a kiválasztott előfizetésben található, egyező erőforrásokra alkalmazza.
- **Megosztott hatókör** – A foglalási kedvezményt a számlázási környezet jogosult előfizetéseiben található, egyező erőforrásokra alkalmazza.
    - A Nagyvállalati Szerződéssel rendelkező ügyfelek esetében a számlázási környezet a regisztráció. A foglalás megosztott hatóköre több Active Directory-bérlőt tartalmazna egy regisztrációban.
    - Microsoft Ügyfélszerződéses ügyfelek esetében a számlázási környezet a számlázási profil.
    - A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.

A foglalási kedvezmények a használatra történő alkalmazása során az Azure a következő sorrendben dolgozza fel a foglalást:

1. Foglalások egyetlen erőforráscsoport-hatókörrel
2. Foglalások egyetlen előfizetési hatókörrel
3. Foglalások megosztott hatókörrel (több előfizetéssel), korábban leírtak szerint

A foglalás megvásárlása után bármikor frissítheti a hatókört. Ehhez lépjen a foglalásra, kattintson a **Konfigurálás** lehetőségre, és állítsa be újból a foglalás hatókörét. A foglalás hatókörének újbóli beállítása nem számít kereskedelmi tranzakciónak. A foglalási időszak nem módosul. A hatókör frissítésével kapcsolatos további információkért lásd [a foglalás megvásárlása utáni hatókörfrissítést](manage-reserved-vm-instance.md#change-the-reservation-scope) bemutató cikket.

![A foglalási hatókör módosítását bemutató példa](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="discounted-subscription-and-offer-types"></a>Kedvezményes előfizetés- és ajánlattípusok

A foglalási kedvezmények a következő jogosult előfizetésekre és ajánlattípusokra vonatkoznak.

- Nagyvállalati szerződés (ajánlatszámok: MS-AZR-0017P vagy MS-AZR-0148P)
- Microsoft-ügyfélszerződéses előfizetések.
- Egyéni csomagok a használatalapú fizetés díjszabásával (ajánlatszámok: MS-AZR-0003P vagy MS-AZR-0023P).
- CSP-előfizetések

A más ajánlattípusokkal rendelkező előfizetésekben futó erőforrásokra nem érvényes a foglalási kedvezmény.

## <a name="purchase-reservations"></a>Foglalások vásárlása

Foglalásokat az Azure Portalon, API-kkal, a PowerShellben és a parancssori felületen vásárolhat. Ha készen áll a foglalás vásárlására, olvassa el az alábbi, az Ön által használt szolgáltatásokra vonatkozó cikkeket:

- [APP SERVICE](prepay-app-service.md)
- [Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](/azure/data-explorer/pricing-reserved-capacity)
- [Disk Storage](../../virtual-machines/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Szoftvercsomagok](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure Database for PostgreSQL](../../postgresql/concept-reserved-pricing.md)
- [Azure Database for MySQL](../../mysql/concept-reserved-pricing.md)
- [Azure Database for MariaDB](../../mariadb/concept-reserved-pricing.md)
- [Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md)
- [Virtuális gépek](../../virtual-machines/prepay-reserved-vm-instances.md)

## <a name="buy-reservations-with-monthly-payments"></a>Foglalások vásárlása havi kifizetésekkel

Havi kifizetésekkel is fizethet a foglalásokért. Az előre fizetéses vásárlástól eltérően, amikor kifizeti a teljes összeget, a havi fizetéses lehetőség egyenlően osztja el a foglalás teljes költségét az időszak minden hónapjára. A foglalások előre vagy havonta fizetett összege megegyezik, és ha a havi fizetést választja, nem kell extra díjakat fizetnie.

Ha foglalását Microsoft Ügyfélszerződés (Microsoft Customer Agreement, MCA) keretén belül vásárolta, a havonta fizetendő összeg a helyi pénznem aktuális hónapban érvényes átváltási árfolyamától függően változhat.

A havi kifizetések az alábbiakhoz nem érhetőek el: Databricks, SUSE Linux-foglalások, Red Hat-csomagok és Azure Red Hat OpenShift-licencek.

### <a name="view-payments-made"></a>A megtörtént kifizetések megtekintése

A megtörtént kifizetéseket az API-k és a használati adatok használatával, valamint a költségelemzésben tekintheti meg. A havonta kifizetett foglalások esetében a gyakoriság értéke **ismétlődőként** jelenik meg a használati adatokban és a Reservation Charges API-ban. Az előre kifizetett foglalások esetében az érték **egyszeriként** jelenik meg.

A költségelemzés az alapértelmezett nézetben jeleníti meg a havi vásárlásokat. A vásárlások megtekintéséhez alkalmazza a **vásárlás** szűrőt a **Költségtípus** elemre és az **ismétlődő** szűrőt a **Gyakoriság** elemre. Ha csak a foglalásokat szeretné megtekinteni, a **Foglalások** esetében alkalmazzon szűrőt.

![Példa a foglalásvásárlási költségekre a költségelemzésben](./media/prepare-buy-reservation/cost-analysis.png)

### <a name="exchange-and-refunds"></a>Csere és visszatérítés

A többi foglaláshoz hasonlóan a havi számlázással vásárolt foglalások esetében is lehetőség van visszatérítésre és cserére. 

Ha egy havi kifizetéses foglalást cserél le, akkor az új vásárlás teljes élettartamra vetített költségének nagyobbnak kell lennie, mint a lecserélni kívánt foglalás lemondott, fennmaradó kifizetései. Csere esetén nincsenek más korlátozások vagy díjak. Cserére akkor van lehetősége, ha egy előre kifizetett foglalás helyett vásárol egy havi számlázású, új foglalást. Az új foglalás élettartamra vetített értékének azonban nagyobbnak kell lennie, mint a lecserélni kívánt foglalás időarányos értéke.

Ha lemond egy havonta fizetett foglalást, a lemondott későbbi kifizetések le lesznek vonva az 50 000 USD értékű visszatérítési korlátból.

A cserével és a visszatérítéssel kapcsolatos további információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](exchange-and-refund-azure-reservations.md).

## <a name="reservation-notifications"></a>Foglalási értesítések

Az Azure-előfizetésért történő fizetés módjától függően e-mailes foglalási értesítést küldünk a szervezet alábbi felhasználóinak. Számos különféle eseményről küldünk értesítést, ilyenek például: 

- Vásárlás
- Foglalás közelgő lejárata
- Lejárat
- Megújítás
- Lemondás
- Hatókör módosítása

Nagyvállalati előfizetéssel rendelkező ügyfelek esetében:

- Az értesítéseket csak az EA-értesítési kapcsolattartóknak küldjük el.
- A foglaláshoz Azure RBAC- (IAM-) engedéllyel hozzáadott felhasználók nem kapnak semmilyen e-mailes értesítést.

Az egyéni előfizetéssel rendelkező ügyfelek esetében:

- A vásárló kap egy vásárlási értesítést.
- A vásárlás időpontjában az előfizetéshez tartozó számlázási fiók tulajdonosa kap egy vásárlási értesítést.
- A fióktulajdonos kapja meg az összes többi értesítést.

## <a name="next-steps"></a>További lépések

- [További információ a Foglalási engedélyekről](view-reservations.md)
- [Foglalások kezelése az Azure-erőforrásoknál](manage-reserved-vm-instance.md)
- [Automatizálás REST API-k használatával](/rest/api/reserved-vm-instances/reservationorder)
- [Automatizálás a Azure PowerShell](/powershell/module/az.reservations)
- [Automatizálás a CLI használatával](/cli/azure/reservations)
