---
title: Felhasználók – az Azure Active Directory-csoportokkal terméklicencek közötti migrálása |} A Microsoft Docs
description: Azt ismerteti, hogyan javasolt a felhasználók (Office 365 nagyvállalati E1 csomag és E3) különböző terméklicencek közötti migrálása Csoportalapú licenceléssel
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2019
ms.author: curtand
ms.reviewer: sumitp
ms.openlocfilehash: 68d4cdf3c7ba08f7cf37132936c6769c99c177cc
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54319418"
---
# <a name="how-to-safely-migrate-users-between-product-licenses-by-using-group-based-licensing"></a>Biztonságosan a felhasználók a Csoportalapú licencelés terméklicencek közötti migrálása

Ez a cikk ismerteti az ajánlott módszer, ha Csoportalapú licencelést használ terméklicencek közötti áthelyezése a felhasználók. Ez a megközelítés célja annak biztosítására, hogy ne legyen adatvesztés nélkül szolgáltatás vagy az adatok az áttelepítés során: felhasználók át kell váltania termékek közötti zökkenőmentes. Az áttelepítési folyamat két változata terjed ki:

-   Egyszerű áttelepítés ütköző szolgáltatási csomagok, például az Office 365 nagyvállalati E3 csomag és az Office 365 nagyvállalati E5 csomag közötti migrálás nem tartalmazó terméklicencek közötti.

-   A migrálás összetettebb néhány ütköző szolgáltatási csomagok, például az Office 365 nagyvállalati E1 csomag és az Office 365 nagyvállalati E3 csomag közötti áttelepítés tartalmazó termékek között. Ütközések kapcsolatos további információkért lásd: [ütköző szolgáltatási csomagok](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal#conflicting-service-plans) és [szolgáltatási csomagok esetében nem rendelhetők hozzá egyszerre](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-product-and-service-plan-reference#service-plans-that-cannot-be-assigned-at-the-same-time).

Ez a cikk tartalmazza a minta PowerShell-kód, az áttelepítési és ellenőrzési lépések végrehajtásához használható. A kód különösen hasznos nagy léptékű műveletekhez, amikor is nem manuális lépések végrehajtásához.

## <a name="before-you-begin"></a>Előkészületek
Az áttelepítés megkezdése előtt fontos igaz az összes áttelepíteni kívánt felhasználó bizonyos Előfeltételek ellenőrzése. Ha az előfeltételek nem igaz az összes felhasználó, az áttelepítés bizonyos sikertelen lehet. Ennek eredményeképpen a felhasználói egy része lehet, hogy elveszíti a hozzáférést szolgáltatásaihoz vagy adataihoz. Ellenőrizni kell a következő előfeltételek:

-   Felhasználók rendelkeznek a *forrás licenc* rendelt Csoportalapú Licencelés használatával. Helyezze át a termék licencét egyetlen forráscsoport öröklődnek, és közvetlenül nem rendel.

    >[!NOTE]
    >Licenceket rendelt is közvetlenül, ha az alkalmazásának megakadályozhatja a *cél licenc*. Tudjon meg többet [közvetlen és a licenc-hozzárendelés](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-advanced#direct-licenses-coexist-with-group-licenses). Akkor érdemes használni egy [PowerShell-parancsprogram](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-ps-examples#check-if-user-license-is-assigned-directly-or-inherited-from-a-group) annak ellenőrzésére, ha a felhasználók a közvetlen licenc.

-   A termék elég rendelkezésre álló licenc van. Nincs elég licence, ha néhány felhasználó nem kaphat a *cél licenc*. Is [ellenőrizze a rendelkezésre álló licencek számát](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products).

-   Felhasználók nem rendelkeznek más hozzárendelt terméklicencek, amelyek ütközhetnek a *cél licenc* vagy eltávolításának megakadályozása a *forrás licenc*. Ha például egy kiegészítő termék például Workplace Analytics vagy a Project Online-licenccel, amely maga más termékekkel.

-   Ismerje meg, hogyan kezeli az csoportok a környezetben. Például ha helyi csoportok kezelése, és szinkronizálhatja őket az Azure Active Directoryba (Azure AD) az Azure AD Connect használatával, majd, hozzáadása/eltávolítása felhasználók a helyszíni rendszerrel. A módosítások az Azure AD-ba való szinkronizálásához, és első mértékének növelése a Csoportalapú licencelés időt vesz igénybe. Az Azure AD dinamikus csoporttagságok használja, ha, hozzáadása/eltávolítása felhasználók ehelyett a tulajdonságaik módosításával. Az áttelepítési folyamat azonban változatlan marad. Az egyetlen különbség hogyan, felhasználók hozzáadása/törlése a csoporttagság.

## <a name="migrate-users-between-products-that-dont-have-conflicting-service-plans"></a>Áttelepítheti a felhasználókat, amelyek nem rendelkeznek ütköző szolgáltatási csomagok termékek között
Az áttelepítés cél az, hogy Csoportalapú licencelés felhasználói licenc módosítása egy *forrás licenc* (ebben a példában: Az Office 365 nagyvállalati E3 csomag), egy *cél licenc* (ebben a példában: Az Office 365 nagyvállalati E5 csomag). Ebben a forgatókönyvben a két termék nem tartalmazhatnak ütköző szolgáltatási csomagok, ezért teljes mértékben hozzárendelhető egyszerre egy konfliktus nélkül. Az áttelepítés során kell felhasználók elveszíteni a hozzáférésüket szolgáltatásaihoz vagy adataihoz. Az áttelepítés történik, és kötegekben kis"." Ellenőrizze az eredményt az egyes kötegek, és minimalizálja a hatókörébe tartozó bármely, a folyamat során esetleg felmerülő problémákat. A teljes a folyamat a következőképpen történik:

1.  Felhasználók csoport tagjai, egy forrás és öröklik a *forrás licenc* a csoportból.

2.  Hozzon létre egy célcsoportot a a *cél licenc* , de egyetlen tagot sem nélkül.

3.  Adja hozzá a felhasználók kötegelt a csoportnak. Csoportalapú licencelés észleli a módosítást, és hozzárendeli a *cél licenc*. A kiterjesztett mennyi idő, a batch- és egyéb tevékenységeket a bérlő méretétől függően eltarthat.

4.  Győződjön meg arról, hogy a felhasználók a köteg teljes dolgozza fel a Csoportalapú licencelés. Győződjön meg arról, hogy minden felhasználó rendelkezik-e a *cél licenc* hozzárendelve. Ellenőrizze, hogy a felhasználók végül nem a hibás állapotú, például a ütközik más termékeket vagy hiánya ahány elérhető licenccel. Hibákkal kapcsolatos további információkért lásd: [Active Directory csoport probléma megoldása licencelési](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal).

5.  Ezen a ponton a felhasználói is vannak a *forrás licenc* és *cél licenc* hozzárendelve.

6.  Távolítsa el az ugyanazon felhasználói batch a forrás csoportból. Csoportalapú licencelés válaszol a változás- és a *forrás-licencek* a felhasználók el lesznek távolítva.

7.  Ismételje meg a folyamat a felhasználók ezt követő kötegek.

### <a name="migrate-a-single-user-by-using-the-azure-portal"></a>Egyetlen felhasználó áttelepítése az Azure portal használatával
Ez az egyetlen felhasználó áttelepítése egy egyszerű bemutató.

**1. LÉPÉS**: A felhasználó rendelkezik egy *forrás licenc* , amely örökli a csoportból. Nincsenek nem közvetlen licenc-hozzárendelések:

![Forrás-licenccel rendelkező felhasználó a csoporttól örökölt](./media/licensing-groups-change-licenses/UserWithSourceLicenseInherited.png)

**2. LÉPÉS**: A felhasználó bekerül a célcsoportot, és a módosítás Csoportalapú licencelés dolgozza fel. A felhasználó már rendelkezik mind a *forrás licenc* és *cél licenc* , amely örökli csoportok:

![A forrás és a cél-licenccel rendelkező felhasználó örökölt](./media/licensing-groups-change-licenses/UserWithBothSourceAndTargetLicense.png)

**3. LÉPÉS**: A felhasználó törlődik a forrás-csoport és a Csoportalapú licencelés dolgozza fel a módosítást. A felhasználó ezentúl csak a *cél licenc*:

![Cél-licenccel rendelkező felhasználó a csoporttól örökölt](./media/licensing-groups-change-licenses/UserWithTargetLicenseAssigned.png)

### <a name="automate-migration-by-using-azure-powershell"></a>Áttelepítési automatizálja az Azure PowerShell-lel
Az alábbi kódrészlet bemutatja, hogyan automatizálhatja az áttelepítési folyamat nagy méretű művelet.

> [!NOTE]
> A mintakód használja a PowerShell-függvények, amelyek szerepelnek a [szakasz utolsó](#powershell-automation-of-migration-and-verification-steps) ebben a cikkben.

```
# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com','MigrationUser2@tailspinonline.com'

############### NON-CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:ENTERPRISEPACK'      # <-- This is the Office 365 Enterprise E3 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPREMIUM'   # <-- This is the Office 365 Enterprise E5 product.

if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will assign the target license $targetSkuId to all users"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceandTargetLicensePresent} 'STEP 2: Checking if all users still have the source license and now also have the target license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that the target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId
```

**Kimeneti példa (áttelepítés két felhasználó)**

```
Verifying initial assumptions:
Enough TailspinOnline:ENTERPRISEPREMIUM licenses available (13) for users: 2.
migrationuser@tailspinonline.com                OK
migrationuser2@tailspinonline.com               OK
Checks passed for all 2 users.

STEP 1: Adding users to the target group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe. This will assign the target license TailspinOnline:ENTERPRISEPREMIUM to all users
Adding user MigrationUser@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe
Adding user MigrationUser2@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe

STEP 2: Checking if all users still have the source license and now also have the target license from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 2: Checking if all users still have the source license and now also have the target license from target group. Check iteration: 2

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.

STEP 3: Removing users from the source group b82c04f0-ce30-4ff1-bac7-735d92d83036. This will remove the source license TailspinOnline:ENTERPRISEPACK.
Removing user MigrationUser@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036
Removing user MigrationUser2@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 2

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.
```

## <a name="migrate-users-between-products-that-have-conflicting-service-plans"></a>Ütköző szolgáltatási csomagok rendelkező termékek között felhasználók migrálása
Az áttelepítés cél az, hogy Csoportalapú licencelés felhasználói licenc módosítása egy *forrás licenc* (ebben a példában: Az Office 365 nagyvállalati E1 csomag), egy *cél licenc* (ebben a példában: Az Office 365 nagyvállalati E3 csomag). Ebben a forgatókönyvben a két termék ütköző szolgáltatási csomagok tartalmaznak, így zökkenőmentesen át a felhasználóit az ütközést megkerüléséhez. Az ütközések kapcsolatos további információkért lásd: [Active Directory licencelése csoport probléma megoldása: Ütköző szolgáltatási csomagok](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal#conflicting-service-plans). Az áttelepítés során kell felhasználók elveszíteni a hozzáférésüket szolgáltatásaihoz vagy adataihoz. Az áttelepítés történik, és kötegekben kis"." Ellenőrizze az eredményt az egyes kötegek, és minimalizálja a hatókörébe tartozó bármely, a folyamat során esetleg felmerülő problémákat. A teljes a folyamat a következőképpen történik:

1.  Felhasználók csoport tagjai, egy forrás és öröklik a *forrás licenc* a csoportból.

2.  Hozzon létre egy célcsoportot a a *cél licenc* , de egyetlen tagot sem nélkül.

3.  Adja hozzá a felhasználók kötegelt a csoportnak. Csoportalapú licencelés észleli a módosítást, és megpróbálja hozzárendelni a *cél licenc*. A helyhozzárendelés meghiúsul, a két termék a szolgáltatások közötti ütközés miatt. A hiba Csoportalapú licencelés rögzíti a felhasználói hibát. A kiterjesztett mennyi idő, a batch- és egyéb tevékenységeket a bérlő méretétől függően eltarthat.

4.  Győződjön meg arról, hogy a felhasználók a köteg teljes dolgozza fel a Csoportalapú licencelés. Győződjön meg arról, hogy minden felhasználó rendelkezik-e a rögzített ütközés hiba. Ellenőrizze, hogy néhány felhasználó nem végül váratlan hibás állapotú. Hibákkal kapcsolatos további információkért lásd: [Active Directory csoport probléma megoldása licencelési](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal).

5.  Ezen a ponton a felhasználók továbbra is fennáll a *forrás licenc* és a egy ütközést hiba a *cél licenc*. A felhasználók még nem rendelkezik a *cél licenc* hozzárendelve.

6.  Távolítsa el az ugyanazon felhasználói batch a forrás csoportból. Csoportalapú licencelés válaszol a változás- és a *forrás licenc* eltávolítják az egyes felhasználók. Az ütközés hiba eltávolítása (Ha nincs más terméklicenc hozzájárul a hiba) egy időben, és a *cél licenc* hozzá van rendelve. Ez a folyamat biztosítja, hogy nincs-e szolgáltatások vagy adat az adatvesztés az áttérés során.

7.  Ismételje meg a folyamat a felhasználók ezt követő kötegek.

### <a name="migrate-a-single-user-by-using-the-azure-portal"></a>Egyetlen felhasználó áttelepítése az Azure portal használatával
Ez az egyetlen felhasználó áttelepítése egy egyszerű bemutató.

**1. LÉPÉS**: A felhasználó rendelkezik egy *forrás licenc* , amely örökli a csoportból. Nincsenek nem közvetlen licenc-hozzárendelések:

![Forrás-licenccel rendelkező felhasználó a csoporttól örökölt](./media/licensing-groups-change-licenses/UserWithSourceLicenseInheritedConflictScenario.png)

**2. LÉPÉS**: A felhasználó bekerül a célcsoportot, és a módosítás Csoportalapú licencelés dolgozza fel. Mivel a felhasználó még a *forrás licenc*, a *cél licenc* az ütközés miatt hibaállapotban van:

![Forrás-licenccel rendelkező felhasználó örökli a csoport és a cél licenc hibás állapotú](./media/licensing-groups-change-licenses/UserWithSourceLicenseAndTargetLicenseInConflict.png)

**3. LÉPÉS**: A felhasználó törlődik a forrás-csoport és a Csoportalapú licencelés dolgozza fel a módosítást. A *cél licenc* van alkalmazva a felhasználóra:

![Cél-licenccel rendelkező felhasználó a csoporttól örökölt](./media/licensing-groups-change-licenses/UserWithTargetLicenseAssignedConflictScenario.png)


### <a name="automate-migration-by-using-azure-powershell"></a>Áttelepítési automatizálja az Azure PowerShell-lel
Az alábbi kódrészlet bemutatja, hogyan automatizálhatja az áttelepítési folyamat nagy méretű művelet.

> [!NOTE]
> A mintakód használja a PowerShell-függvények, amelyek szerepelnek a [szakasz utolsó](#powershell-automation-of-migration-and-verification-steps) ebben a cikkben.

```
# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com', 'MigrationUser2@tailspinonline.com'

############### CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:STANDARDPACK'             # <-- This is the Office 365 Enterprise E1 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPACK'           # <-- This is the Office 365 Enterprise E3 product.

# Assumptions before migration:
# 1. Users are already in the source group and they have the source license assigned from that group.
# 2. Users don't have the same source license assigned from another group at the same time,
#    and they don't have the source license assigned directly.
#    IMPORTANT: If Assumption 2 isn't true, removing users from the source group in STEP 3
#               won't result in the target license being correctly applied.
# 3. There are enough available licenses to assign a target license to all of the users that are being migrated.
if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will put target license $targetSkuId in conflict state with the source license $sourceSkuId"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceLicensePresentAndTargetLicenseInConflictState} 'STEP 2: Checking if all users still have the source license and are in conflict state for license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId and remove the conflict on target license $targetSkuId which will become assigned."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that the target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId
```

**Kimeneti példa (áttelepítés két felhasználó)**

```
Verifying initial assumptions:
Enough TailspinOnline:ENTERPRISEPACK licenses available (61) for users: 2.
migrationuser@tailspinonline.com                OK
migrationuser2@tailspinonline.com               OK
Checks passed for all 2 users.
STEP 1: Adding users to the target group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe. This will put target license TailspinOnline:ENTERPRISEPACK in conflict state with the source license TailspinOnline:STANDARDPACK
Adding user MigrationUser@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe
Adding user MigrationUser2@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe

STEP 2: Checking if all users still have the source license and are in conflict state for license from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 2: Checking if all users still have the source license and are in conflict state for license from target group. Check iteration: 3

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.

STEP 3: Removing users from the source group b82c04f0-ce30-4ff1-bac7-735d92d83036. This will remove the source license TailspinOnline:STANDARDPACK and remove the conflict on target license TailspinOnline:ENTERPRISEPACK which will become assigned.
Removing user MigrationUser@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036
Removing user MigrationUser2@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 3

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.
```

<h2 id="powershell-automation-of-migration-and-verification-steps">Hajtsa végre a PowerShell-kód automatizálhatja és az áttelepítés ellenőrzése</h2>

Ez a szakasz tartalmazza a PowerShell-kóddal, amely ebben a cikkben ismertetett a parancsfájlok futtatásához szükséges.

>[!WARNING]
>Ez a kód biztosítunk példaként, bemutatási céllal. Ha szeretne használni, a környezetében, érdemes a kód először teszteléshez a kis léptékű, vagy külön tesztelési célú bérlői. Előfordulhat, hogy kell módosítania a kódot az adott környezet igényeinek kielégítése érdekében.

Futtassa a kódot, használja a következő témakör utasításait a [az Azure AD PowerShell 1.0-s verziójú kódtárak](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0). A parancsprogram futtatása előtt futtassa a `connect-msolservice` parancsmagot, hogy jelentkezzen be a bérlő.

```
# BEGIN: Helper functions that are used in the scripts.

# GetUserObject function
# Retrieve a user object based on the ObjectId or UserPrincipalName.
function GetUserObject
{
    [OutputType([Microsoft.Online.Administration.User])]
    Param([object]$userId)

    $userIdType = $userId.GetType()

    if($userIdType -eq [Guid])
    {
        return Get-MsolUser -ObjectId $userId
    }
    elseif($userIdType -eq [string])
    {
        return Get-MsolUser -UserPrincipalName $userId
    }
    else
    {
        throw "User Id type must be a Guid or a string (UserPrincipalName). The user id type was: $($userIdType.Name)"
    }
}

# GetGuidUserId function
# Get a Guid objectId for a user, even when a UserPrincipal string is passed in.
# Guid ids are needed for group membership manipulation, where UPNs cannot be used.
function GetGuidUserId
{
    [OutputType([Guid])]
    Param([object]$userId)

    [Guid]$guidId = [Guid]::Empty
    if($userId.GetType() -eq [String])
    {
        $user = GetUserObject $userId
        return $user.ObjectId
    }
    elseif($userId.GetType() -eq [Guid])
    {
        return $userId
    }
    else
    {
        throw "UserId type must be a Guid or a string (UserPrincipalName). The user id type was: $($userId.GetType().Name)"
    }
}

# AddUsersToGroup function
# Add a collection of users to a group.
# Note: This function fails if a user is already a member of the specified group.
function AddUsersToGroup
{
    Param([object[]]$userIds, [Guid]$groupId)

    foreach($userId in $userIds)
    {
        $fetchId = GetGuidUserId $userId
        Write-Host "Adding user $userId to group $groupId"
        Add-MsolGroupMember -GroupObjectId $groupId -GroupMemberType User -GroupMemberObjectId $fetchId
    }
}

# RemoveUsersFromGroup function
# Remove a collection of users from a group.
# Note: This function fails if a user is not a member of the specified group.
function RemoveUsersFromGroup
{
    Param([object[]]$userIds, [Guid]$groupId)

    foreach($userId in $userIds)
    {
        $fetchId = GetGuidUserId $userId
        Write-Host "Removing user $userId from group $groupId"
        Remove-MsolGroupMember -GroupObjectId $groupId -GroupMemberType User -GroupMemberObjectId $fetchId
    }
}

# GetUserLicense function
# Return the license object that corresponds to the skuId.
# Return NULL if no object is found.
function GetUserLicense
{
    [OutputType([Microsoft.Online.Administration.UserLicense])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    # Look for the specific license SKU in all of the licenses that are assigned to the user.
    foreach($license in $user.Licenses)
    {
        if ($license.AccountSkuId -ieq $skuId)
        {
            return $license
        }
    }
    return $null
}

# IsLicenseAssignedToUser function
# Check if the specific SKU license is assigned to the user,
#    regardless of how the license is assigned (directly or via GBL).
function IsLicenseAssignedToUser
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [string]$skuId)

    $license = GetUserLicense $user $skuId

    return ($license -ne $null)
}

# GetObjectIdsAssigningLicense function
function GetObjectIdsAssigningLicense
{
    [OutputType([Guid[]])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        return [Guid[]]$license.GroupsAssigningLicense
    }
    return [Array]::CreateInstance([Guid],0)
}

# UserHasLicenseAssignedFromThisGroup function
# Return TRUE if the user inherits the license from the specific group.
# Note: This function returns true only if the license is successfully assigned from the group.
#       If the license is in an error state, the function return false.
function UserHasLicenseAssignedFromThisGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)

    [Guid[]]$objectsAssigningLicense = GetObjectIdsAssigningLicense $user $skuId

    # GroupsAssigningLicense contains a collection of object IDs for assigning the license.
    # This could be a group object or a user object (contrary to what the name suggests).
    foreach ($assignmentSource in $objectsAssigningLicense)
    {
        # If the collection contains at least one ID that doesn't match the user ID, the license is inherited from a group.
        # Note: The license might also be assigned directly, in addition to being inherited.
        if ($assignmentSource -ieq $groupId)
        {
            return $true
        }
    }
    return $false
}

# GetErrorsForLicense function
# Return error objects for a specific license.
function GetErrorsForLicense
{
    [OutputType([Microsoft.Online.Administration.IndirectLicenseError[]])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    [Microsoft.Online.Administration.IndirectLicenseError[]] $errorObjects = $user.IndirectLicenseErrors | Where {"$($_.AccountSku.AccountName):$($_.AccountSku.SkuPartNumber)" -ieq $skuId}

    if($errorObjects -eq $null)
    {
        #there are no errors at all
        return [Array]::CreateInstance([Microsoft.Online.Administration.IndirectLicenseError],0)
    }

    return $errorObjects
}

# GetErrorForLicenseFromGroup function
# Return an error label that's associated with a specific license that's inherited from a specific group.
# Return $null if there's no error.
function GetErrorForLicenseFromGroup
{
    [OutputType([string])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$groupId, [string]$skuId)


    # There are some errors. Check if any of the errors are associated with the group.
    foreach($licenseError in GetErrorsForLicense $user $skuId)
    {
        if($licenseError.ReferencedObjectId -eq $groupId)
        {
            return $licenseError.Error
        }
    }
    return $null
}

# IsExpectedLicenseStateForGroup function
# Check if the license is in an expected state for a given group.
# If expectedError is set to a value, the function checks if the license is in the specific error state for the group.
# If expectedError is NULL, the function checks if the license is successfully assigned from the group.
function IsExpectedLicenseStateForGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$groupId, [string]$skuId, [string]$expectedError)

    # The license is expected to be fully assigned from the group and not in an error state.
    if([string]::IsNullOrEmpty($expectedError))
    {
        # Check if the assigned license is inherted from the expected group and without an error on it.
        return (UserHasLicenseAssignedFromThisGroup $user $skuId $groupId)
    }
    # The license is expected to be in the specific error state on the specific group.
    else
    {
        $error = GetErrorForLicenseFromGroup $user $groupId $skuId
        return ($error -ieq $expectedError)
    }
}

# VerifySourceLicensePresentAndTargetLicenseInConflictState function
# Detect if the licenses are in a specific state where the source license is assigned, but the target license is in a conflict state.
# Note: If the source license is gone, the function throws an exception to abort the script.
#       The conflict state can signify that something went wrong with the migration steps and the user lost access to services.
function VerifySourceLicensePresentAndTargetLicenseInConflictState
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # Check if the user still has the source license. If not, abort the script because something is seriously wrong.
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $sourceSkuId from source group $sourceGroupId, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    # Check if the target license is in conflict, as expected.
    $conflictError = 'MutuallyExclusiveViolation'
    return (IsExpectedLicenseStateForGroup $user $targetGroupId $targetSkuId $conflictError)
}

# VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup function
# Detect if the licenses are in a specific state where the source license isn't present,
#    but the target license is correctly assigned.
# Note: If the source license is gone and the target license isn't present,
#       the function throws an exception to abort the script.
#       Something went wrong and the user may have lost access to services.
function VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # Check if user has the source license completely removed, which is a prerequisite to the target license eventually kicking in.
    if(IsLicenseAssignedToUser $user $sourceSkuId)
    {
        return $false
    }

    # Check if the user has the target license at all. If not, abort the script because something is seriously wrong.
    if(-Not (IsLicenseAssignedToUser $user $targetSkuId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $targetSkuId assigned, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    # Check if the target license is assigned from the expected target group, and no longer in an error state.
    return (IsExpectedLicenseStateForGroup $user $targetGroupId $targetSkuId $null)
}

# VerifySourceandTargetLicensePresent function
# Detect if the licenses are in the specific state where the source license is assigned and the target license is also assigned.
# Note: If the source license is gone, the function throws an exception to abort the script.
#       This state can signify that something went wrong with the migration steps and the user lost access to services.
function VerifySourceandTargetLicensePresent
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    #check user still has source license - if not, abort because something is seriously wrong
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $sourceSkuId from source group $sourceGroupId, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    #check if the target license is also assigned from the target group
    return (UserHasLicenseAssignedFromThisGroup $user $targetSkuId $targetGroupId)
}

# VerifyAssumptionsForUser function
# Verify basic assumptions that should be true for a user before we execute the migration process.
# The function prints details about the verification steps.
# Return TRUE if all of the assumptions are true.
function VerifyAssumptionsForUser
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    $userName = $user.UserPrincipalName
    # 1. The user has the source license assigned from the source group.
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        Write-Host "$userName does not have source license $sourceSkuId assigned from source group $sourceGroupId."
        return $false
    }

    # 2. The user does't have the same source license assigned from another group at the same time,
    #    and the user doesn't have the source license assigned directly.
    [Guid[]]$otherObjectsAssigningLicense = GetObjectIdsAssigningLicense $user $sourceSkuId | Where {$_ -ne $sourceGroupId}
    foreach($otherObject in $otherObjectsAssigningLicense)
    {
        if($otherObject -eq $user.ObjectId)
        {
            Write-Host "$userName has source license assigned directly to the user."
        }
        else
        {
            Write-Host "$username has source license assigned from an additional unexpected group $otherObject."
        }
    }
    if($otherObjectsAssigningLicense -and $otherObjectsAssigningLicense.Count -gt 0)
    {
        return $false
    }

    # 3. The user doesn't have the target license assigned.
    if(IsLicenseAssignedToUser $user $targetSkuId)
    {
        Write-Host "$userName already has target license assigned."
        return $false
    }

    # 4. The user doesn't have the target license in an error state from some groups.
    [Microsoft.Online.Administration.IndirectLicenseError[]]$licenseErrors = GetErrorsForLicense $user $targetSkuId
    foreach($licenseError in $licenseErrors)
    {
        Write-Host "$userName has target license in error state $($licenseError.Error) from unexpected group $($licenseError.ReferencedObjectId)."
    }
    if($licenseErrors -and $licenseErrors.Count -gt 0)
    {
        return $false
    }

    Write-Host "$userName`t`tOK"
    return $true
}

# VerifyAssumptions function
# Check if all of the users to be migrated are in a correct state.
function VerifyAssumptions
{
    [OutputType([bool])]
    Param([object[]]$userIds, [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    Write-Host "Verifying initial assumptions:"

    # Check if there are enough target licenses for all of the users.
    $skuState = Get-MsolAccountSku | Where {$_.AccountSkuId -ieq $targetSkuId}

    if($skuState -eq $null)
    {
        Write-Host "Target license SKU $targetSkuId does not exist in the tenant at all."
        return $false
    }

    $availableLicenses = $skuState.ActiveUnits - $skuState.ConsumedUnits

    if($userIds.Count -gt $availableLicenses)
    {
        Write-Host "Not enough licenses for all users. User count: $($userIds.Count), licenses available: $availableLicenses"
        return $false
    }
    else
    {
        Write-Host "Enough $targetSkuId licenses available ($availableLicenses) for users: $($userIds.Count)."
    }

    # Check if each user to be migrated is in an expected state.
    $usersOK = 0
    $usersNotOK = 0
    foreach($userId in $userIds)
    {
        $user = GetUserObject $userId
        if(VerifyAssumptionsForUser $user $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId)
        {
            ++$usersOK
        }
        else
        {
            ++$usersNotOK
        }
    }
    if($usersNotOK -gt 0)
    {
        Write-Host "Checks passed for $usersOK users, but failed for $usersNotOK users."
        return $false
    }
    Write-Host "Checks passed for all $usersOK users."
    return $true
}

# ExecuteVerificationLoop function
# Execute a verification function (passed in as a delegate by using $checkFunction) for each user.
# The function tracks how many users passed/failed verification.
# The function repeats the verification loop until all of the users have passed the check.
#   The loop may never terminate if some users never reach the expected state.
#   If the loop doesn't terminate, you should investigate to determine the cause.
# Note: If the verification function fails with an exception,
#       such as the function detects an unexpected user state,
#       the loop terminates and investigation into the user state is needed.
function ExecuteVerificationLoop
{
    Param([System.Management.Automation.ScriptBlock]$checkFunction, [string]$consoleMessage, [object[]]$userIds,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # How long to wait until the loop is retried.
    $sleepIntervalSeconds = 60
    $retryIteration = 1

    While($true)
    {
        Write-Host "`n$consoleMessage. Check iteration: $retryIteration`n"

        $usersInExpectedState = 0
        $usersNotYet = 0

        foreach ($userId in $userIds)
        {
            $user = GetUserObject $userId
            if($checkFunction.InvokeReturnAsIs($user, $sourceGroupId, $sourceSkuId, $targetGroupId, $targetSkuId))
            {
                Write-Host "$userId`t`tExpected state: YES"
                ++$usersInExpectedState
            }
            else
            {
                Write-Host "$userId`t`tExpected state: NO"
                ++$usersNotYet
            }
        }

        Write-Host "`nTotal users checked: $($userIds.Count). In expected state: $usersInExpectedState. Not yet: $usersNotYet"

        if($usersNotYet -eq 0)
        {
            Write-Host "Check passed for all users. Exiting check loop."
            return
        }

        ++$retryIteration
        Write-Host "Not all users are in expected state. Waiting $sleepIntervalSeconds seconds to try again."
        Start-Sleep -Seconds $sleepIntervalSeconds
    }
}
# END: Helper functions that are used in the script.

# BEGIN: Execute the script.

# Enable strict execution mode.
Set-StrictMode -Version latest
# Stop the script when the first exception is thrown.
$ErrorActionPreference = "Stop"

# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com', 'MigrationUser2@tailspinonline.com'

############### CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:STANDARDPACK'             # <-- This is the Office 365 Enterprise E1 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPACK'           # <-- This is the Office 365 Enterprise E3 product.

# Assumptions before migration:
# 1. Users are already in the source group and they have the source license assigned from that group.
# 2. Users don't have the same source license assigned from another group at the same time,
#    and they don't have the source license assigned directly.
#    IMPORTANT: If Assumption 2 isn't true, removing users from the source group in STEP 3
#               won't result in the target license being correctly applied.
# 3. There are enough available licenses to assign a target license to all of the users that are being migrated.
if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will put target license $targetSkuId in conflict state with the source license $sourceSkuId"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceLicensePresentAndTargetLicenseInConflictState} 'STEP 2: Checking if all users still have the source license and are in conflict state for license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId and remove the conflict on target license $targetSkuId which will become assigned."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# END: Execute the script.
```

## <a name="next-steps"></a>További lépések

Ismerje meg a következő cikkekben csoportokon keresztül Licenckezelés kapcsolatos egyéb forgatókönyvek:

* [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](../users-groups-roles/licensing-groups-assign.md)
* [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Egyéni, licenccel rendelkező felhasználók migrálása csoportalapú licencelésre az Azure Active Directoryban](../users-groups-roles/licensing-groups-migrate-users.md)
* [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](../users-groups-roles/licensing-group-advanced.md)
* [PowerShell forgatókönyvek Csoportalapú licenceléshez az Azure Active Directoryban](../users-groups-roles/licensing-ps-examples.md)
