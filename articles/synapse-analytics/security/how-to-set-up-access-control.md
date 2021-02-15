---
title: Hozzáférés-vezérlés beállítása a szinapszis-munkaterülethez
description: Ez a cikk bemutatja, hogyan vezérelheti a szinapszis-munkaterülethez való hozzáférést az Azure-szerepkörök, a szinapszis-szerepkörök, az SQL-engedélyek és a git-engedélyek használatával.
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 513d23de3d7f823ec934af32e5f3776e876f02d8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368999"
---
# <a name="how-to-set-up-access-control-for-your-synapse-workspace"></a>Hozzáférés-vezérlés beállítása a szinapszis-munkaterülethez 

Ez a cikk bemutatja, hogyan vezérelheti a szinapszis-munkaterülethez való hozzáférést az Azure-szerepkörök, a szinapszis-szerepkörök, az SQL-engedélyek és a git-engedélyek használatával.   

Ebben az útmutatóban egy munkaterületet állít be, és egy alapszintű hozzáférés-vezérlési rendszer állítható be, amely számos szinapszis-projekt számára megfelelő.  Ezután részletesen leírja, hogy milyen speciális beállításokra van szükség.  

A szinapszis hozzáférés-vezérlését olyan biztonsági csoportok használatával lehet egyszerűsíteni, amelyek összhangban vannak a szervezet szerepköreivel és Personával.  A hozzáférés kezeléséhez csak a biztonsági csoportokból származó felhasználókat kell felvennie és eltávolítania.

Mielőtt elkezdené ezt a bemutatót, olvassa el a [szinapszis hozzáférés-vezérlésének áttekintése](./synapse-workspace-access-control-overview.md) című cikket, és ismerkedjen meg a szinapszis által használt hozzáférés-vezérlési mechanizmusokkal.   

## <a name="access-control-mechanisms"></a>Hozzáférés-vezérlési mechanizmusok

> [!NOTE]
> A jelen útmutatóban ismertetett megközelítés több biztonsági csoport létrehozása, majd szerepkörök kiosztása ezekhez a csoportokhoz. A csoportok beállítása után a munkaterület elérésének vezérléséhez csak a biztonsági csoportokon belüli tagságot kell kezelnie.

A szinapszis-munkaterület biztonságossá tételéhez kövesse az alábbi elemek konfigurálásának mintáját:

- **Biztonsági csoportok** a felhasználók hasonló hozzáférési követelményekkel való csoportosításához.
- **Azure-szerepkörök**, amelyek segítségével szabályozhatja, hogy ki hozhat létre és kezelhet SQL-készleteket, Apache Spark-készleteket és integrációs modulokat, és hogyan érheti el ADLS Gen2 tárolót
- **Szinapszis-szerepkörök**, a közzétett kódokhoz való hozzáférés szabályozása, Apache Spark számítási erőforrások és integrációs modulok használata 
- **SQL-engedélyek** a felügyeleti és az ADATsík SQL-készletekhez való hozzáférésének szabályozásához. 
- **Git-engedélyek**, amelyekkel szabályozhatja, hogy ki férhet hozzá a verziókövetés kódjához, ha a git-támogatást konfigurálja a munkaterülethez 
 
## <a name="steps-to-secure-a-synapse-workspace"></a>A szinapszis-munkaterület biztonságossá tételének lépései

Ez a dokumentum szabványos neveket használ az utasítások egyszerűsítéséhez. Cserélje le őket a választott nevekre.

|Beállítás | Normál név | Description |
| :------ | :-------------- | :---------- |
| **Szinapszis-munkaterület** | `workspace1` |  A szinapszis munkaterület neve. |
| **ADLSGEN2-fiók** | `storage1` | A munkaterülethez használni kívánt ADLS-fiók. |
| **Tároló** | `container1` | A STG1 azon tárolója, amelyet a munkaterület alapértelmezés szerint fog használni. |
| **Active Directory-bérlő** | `contoso` | az Active Directory-bérlő neve.|
||||

## <a name="step-1-set-up-security-groups"></a>1. lépés: biztonsági csoportok beállítása

>[!Note] 
>Az előzetes verzió ideje alatt javasolt biztonsági csoportokat létrehozni a szinapszis **SZINAPSZIS SQL-rendszergazdához** és a **szinapszis Apache Spark rendszergazdai** szerepkörökhöz.  Az új, finomabb RBAC-szerepkörök és-hatókörök bevezetésével mostantól ajánlott ezeket az új képességeket használni a munkaterület elérésének szabályozásához.  Ezek az új szerepkörök és hatókörök nagyobb konfigurációs rugalmasságot biztosítanak, és felismerik, hogy a fejlesztők gyakran használják az SQL és a Spark kombinációját az elemzési alkalmazások létrehozásához, és előfordulhat, hogy a teljes munkaterület helyett hozzáférést kell biztosítani bizonyos erőforrásokhoz. [További](./synapse-workspace-synapse-rbac.md) információ a szinapszis RBAC.

Hozza létre a következő biztonsági csoportokat a munkaterülethez:

- **`workspace1_SynapseAdministrators`**, azon felhasználók számára, akiknek szükségük van a munkaterület feletti felügyeletre.  Adja hozzá magát ehhez a biztonsági csoporthoz, legalábbis kezdetben.
- **`workspace1_SynapseContributors`**, olyan fejlesztők számára, akiknek szükségük van kód a szolgáltatásba való fejlesztésére, hibakeresésére és közzétételére.   
- **`workspace1_SynapseComputeOperators`** azon felhasználók számára, akiknek Apache Spark-készleteket és integrációs modulokat kell kezelnie és figyelniük.
- **`workspace1_SynapseCredentialUsers`** azon felhasználók számára, akik a munkaterület MSI (felügyelt szolgáltatás identitása) hitelesítő adataival és a folyamat futtatásának megszakításával hibakeresési és futtatási folyamatokat igényelnek.   

A szinapszis-szerepköröket hamarosan a munkaterület hatókörében rendeli hozzá ezekhez a csoportokhoz.  

Hozza létre a következő biztonsági csoportot is: 
- **`workspace1_SQLAdmins`**, azon felhasználók csoportja, akiknek a munkaterületen az SQL-Active Directory rendszergazdai jogosultsággal kell rendelkezniük. 

A `workspace1_SQLAdmins` rendszer akkor fogja használni a csoportot, ha az SQL-készletekben konfigurálja az SQL-engedélyeket a létrehozásuk során. 

Alapszintű beállítás esetén ez az öt csoport elegendő. Később hozzáadhat biztonsági csoportokat azon felhasználók kezeléséhez, akiknek több specializált hozzáférésre van szükségük, vagy csak adott erőforrásokhoz férnek hozzá a felhasználóknak.

> [!NOTE]
>- [Ebből a cikkből](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)megtudhatja, hogyan hozhat létre biztonsági csoportot ebben a cikkben.
>- [Ebből a cikkből](../../active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)megtudhatja, hogyan adhat hozzá biztonsági csoportot egy másik biztonsági csoportból ebben a cikkben.

>[!Tip]
>Az egyes szinapszis-felhasználók a Azure Portal Azure Active Directory segítségével megtekinthetik a csoporttagság alapján, hogy meghatározzák, mely szerepköröket adtak meg.

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>2. lépés: a ADLS Gen2 Storage-fiók előkészítése

A szinapszis-munkaterület alapértelmezett tárolót használ a következőhöz:
  - A Spark-táblák biztonsági mentésére szolgáló adatfájlok tárolása
  - Végrehajtási naplók a Spark-feladatokhoz
  - A telepítendő könyvtárak kezelése

Azonosítsa az alábbi adatokat a tárhelyéről:

- A munkaterülethez használni kívánt ADLS Gen2-fiók. Ez a dokumentum hívja meg `storage1` . `storage1` a munkaterület "elsődleges" Storage-fiókjának számít.
- Az a tároló `workspace1` , amelybe a szinapszis-munkaterület alapértelmezés szerint fog használni. Ez a dokumentum hívja meg `container1` . 

- A Azure Portal használatával rendelje hozzá a következő Azure-szerepköröket a `container1` biztonsági csoportokhoz 

  - Rendelje hozzá a **Storage blob adatközreműködői** szerepkört a következőhöz `workspace1_SynapseAdmins` 
  - Rendelje hozzá a **Storage blob adatközreműködői** szerepkört a következőhöz `workspace1_SynapseContributors`
  - Rendelje hozzá a **Storage blob adatközreműködői** szerepkört a következőhöz `workspace1_SynapseComputeOperators`

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>3. lépés: a szinapszis-munkaterület létrehozása és konfigurálása

A Azure Portal hozzon létre egy szinapszis-munkaterületet:

- Az előfizetés kiválasztása
- Válasszon ki vagy hozzon létre egy erőforráscsoportot, amelyhez az Azure **tulajdonosi** szerepköre van.
- A munkaterület elnevezése `workspace1`
- Válassza ki `storage1` a Storage-fiókot
- Válassza ki azt a `container1` tárolót, amelyet "fájlrendszerként" használ.
- WS1 megnyitása a szinapszis Studióban
- Navigáljon a Access Control **kezelése** elemre,  >   és rendeljen hozzá szinapszis-szerepköröket a *munkaterület hatókörében* a biztonsági csoportokhoz a következő módon:
  - A **szinapszis rendszergazda** szerepkörének kiosztása `workspace1_SynapseAdministrators` 
  - A **szinapszis közreműködő** szerepkörének kiosztása `workspace1_SynapseContributors` 
  - A **szinapszis számítási operátori** szerepkörének kiosztása `workspace1_SynapseComputeOperators`

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>4. lépés: a munkaterület MSI-hozzáférésének megadása az alapértelmezett tárolóhoz

A folyamatok futtatásához és a rendszerfeladatok végrehajtásához a szinapszis megköveteli, hogy a munkaterület által felügyelt szolgáltatás identitásának (MSI) hozzá kell férnie `container1` az alapértelmezett ADLS Gen2 fiókhoz.

- Nyissa meg az Azure Portalt
- Keresse meg a Storage-fiókot, `storage1` majd `container1`
- A **Access Control (iam)** használatával győződjön meg arról, hogy a **Storage blob-adatközreműködői** szerepkör hozzá van rendelve a munkaterület MSI-fájlhoz
  - Ha nincs hozzárendelve, rendelje hozzá.
  - Az MSI neve megegyezik a munkaterülettel. Ebben a cikkben a következő lenne: `workspace1` .

## <a name="step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>5. lépés: a szinapszis-rendszergazdák számára az Azure közreműködő szerepkör megadása a munkaterületen 

SQL-készletek, Apache Spark készletek és integrációs modulok létrehozásához a felhasználóknak legalább Azure közreműködői hozzáféréssel kell rendelkezniük a munkaterülethez. A közreműködői szerepkör lehetővé teszi a felhasználók számára az erőforrások kezelését, beleértve a felfüggesztést és a skálázást is.

- Nyissa meg az Azure Portalt
- Keresse meg a munkaterületet, `workspace1`
- Rendelje hozzá az **Azure közreműködő** szerepkört a rendszerhez `workspace1` `workspace1_SynapseAdministrators` . 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>6. lépés: az SQL Active Directory rendszergazdai szerepkörének kiosztása

A munkaterület létrehozója automatikusan be lesz állítva a munkaterület SQL Active Directory-rendszergazdájaként.  Csak egyetlen felhasználó vagy csoport kaphatja meg ezt a szerepkört. Ebben a lépésben az SQL Active Directory-rendszergazdát rendeli hozzá a munkaterületen a `workspace1_SQLAdmins` biztonsági csoporthoz.  A szerepkör hozzárendelésével ez a csoport magas jogosultsági szintű rendszergazdai hozzáférést biztosít a munkaterületen található összes SQL-készlethez és adatbázishoz.   

- Nyissa meg az Azure Portalt
- Navigáljon ide: `workspace1`
- A **Beállítások** területen válassza az **SQL Active Directory-rendszergazda** elemet.
- Válassza a **rendszergazda beállítása** elemet, és válassza a **`workspace1_SQLAdmins`**

>[!Note]
>A 6. lépés megadása nem kötelező.  Dönthet úgy is, hogy a `workspace1_SQLAdmins` csoportot egy kevésbé Kiemelt szerepkörrel ruházza fel. `db_owner`Ha vagy más SQL-szerepkört szeretne hozzárendelni, minden SQL-adatbázishoz parancsfájlokat kell futtatnia. 

## <a name="step-7-grant-access-to-sql-pools"></a>7. lépés: az SQL-készletek elérésének engedélyezése

Alapértelmezés szerint a szinapszis-rendszergazdai szerepkörrel rendelkező összes felhasználó az SQL-szerepkört is hozzárendeli a `db_owner` kiszolgáló nélküli SQL-készlethez, a "beépített" és az összes adatbázisához.

Az SQL-készletek más felhasználók számára való elérését és a munkaterület MSI-fájlját az SQL-engedélyek segítségével szabályozhatja.  Az SQL-engedélyek hozzárendeléséhez az szükséges, hogy az SQL-parancsfájlok a létrehozás után mindegyik SQL-adatbázisban fussanak.  Három esetben kell futtatnia ezeket a parancsfájlokat:
1. Más felhasználók hozzáférésének biztosítása a kiszolgáló nélküli SQL-készlethez, a "beépített" és a hozzá tartozó adatbázisokhoz
2. Felhasználói hozzáférés biztosítása a dedikált készlet adatbázisaihoz
3. A munkaterület MSI-hozzáférésének megadása egy SQL-készlet adatbázisához, amely lehetővé teszi az SQL-készlet elérését igénylő folyamatok sikeres futtatását.

Az alábbi példa SQL-parancsfájlokat tartalmaz.

Ahhoz, hogy hozzáférést biztosítson egy dedikált SQL Pool-adatbázishoz, a szkripteket a munkaterület létrehozója vagy a csoport bármelyik tagja is futtathatja `workspace1_SQLAdmins` .  

A "beépített" kiszolgáló nélküli SQL-készlethez való hozzáférés biztosításához a parancsfájlokat a `workspace1_SQLAdmins` csoport vagy a csoport bármelyik tagja futtathatja  `workspace1_SynapseAdministrators` . 

> [!TIP]
> Az alábbi lépéseket **minden** SQL-készlet esetében futtatni kell, hogy az összes SQL-adatbázishoz felhasználói hozzáférést biztosítson, kivéve a [munkaterület – hatókörrel rendelkező engedélyek](#workspace-scoped-permission) szakaszát, ahol a felhasználóhoz rendszergazdai szerepkört rendelhet a munkaterület szintjén.

### <a name="step-71-serverless-sql-pool-built-in"></a>7,1. lépés: kiszolgáló nélküli SQL-készlet, beépített

Ebben a szakaszban olyan parancsfájl-példákat mutatjuk be, amelyekkel egy adott adatbázishoz vagy a kiszolgáló nélküli SQL-készletben található összes adatbázishoz hozzáférést biztosíthat a felhasználónak.

> [!NOTE]
> A parancsfájl példákban cserélje le az *aliast* a hozzáférést biztosító felhasználó vagy csoport aliasára, valamint a *tartományra* a használt vállalati tartománnyal.

#### <a name="database-scoped-permission"></a>Adatbázis hatókörű engedélye

Ha **egyetlen** kiszolgáló nélküli SQL-adatbázishoz szeretne hozzáférést adni egy felhasználónak, kövesse az alábbi példában szereplő lépéseket:

1. Bejelentkezés létrehozása

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. FELHASZNÁLÓ létrehozása

    ```sql
    use yourdb -- Use your database name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. FELHASZNÁLÓ hozzáadása a megadott szerepkör tagjaihoz

    ```sql
    use yourdb -- Use your database name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>Munkaterület – hatókörön belüli engedély

Ha teljes hozzáférést szeretne biztosítani a munkaterület **összes** kiszolgáló nélküli SQL-készletéhez, használja a következő példában szereplő parancsfájlt:

```sql
use master
go
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE sysadmin ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>7,2. lépés: dedikált SQL-készletek

Ha **egyetlen** dedikált SQL Pool-adatbázishoz szeretne hozzáférést biztosítani, kövesse az alábbi lépéseket a szinapszis SQL Script Editorban:

1. Hozza létre a felhasználót az adatbázisban a következő parancs futtatásával a célként megadott adatbázison, majd válassza a *Kapcsolódás* legördülő lista használatával lehetőséget.

    ```sql
    --Create user in the database
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Adja meg a felhasználónak az adatbázis eléréséhez szükséges szerepkört:

    ```sql
    --Grant role to the user in the database
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> a *db_datareader* és a *db_datawriter* írási/olvasási engedélyekkel is dolgozhat, ha nem kívánja megadni *db_owner* engedélyt.
> Ha egy Spark-felhasználónak közvetlenül a Sparkból vagy egy SQL-készletből kell olvasnia és írnia, *db_owner* engedélyre van szükség.

A felhasználók létrehozása után futtasson lekérdezéseket annak ellenőrzéséhez, hogy a kiszolgáló nélküli SQL-készlet képes-e lekérdezni a Storage-fiókot.

### <a name="step-73-sql-access-control-for-synapse-pipeline-runs"></a>7,3. lépés: a szinapszis-folyamat futtatásának SQL-hozzáférés-vezérlése

### <a name="workspace-managed-identity"></a>Munkaterület által felügyelt identitás

> [!IMPORTANT]
> Ha olyan folyamatokat szeretne futtatni, amelyek olyan adatkészleteket vagy tevékenységeket tartalmaznak, amelyek SQL-készletre hivatkoznak, akkor a munkaterület identitásának hozzáférést kell biztosítania az SQL-készlethez.

Futtassa az alábbi parancsokat az egyes SQL-készleteken, hogy a munkaterület által felügyelt rendszeridentitás a folyamatok futtatását az SQL Pool-adatbázis (ok) ban futtassa:  

>[!note]
>Az alábbi parancsfájlokban egy dedikált SQL Pool-adatbázis esetében a databasename megegyezik a készlet nevével.  A "beépített" kiszolgáló nélküli SQL-készletben található adatbázis databasename az adatbázis neve.

```sql
--Create a SQL user for the workspace MSI in database
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<databasename> TO <workspacename>;
```

Ez az engedély a következő parancsfájl ugyanazon SQL-készleten való futtatásával távolítható el:

```sql
--Revoke permission granted to the workspace MSI
REVOKE CONTROL ON DATABASE::<databasename> TO <workspacename>;

--Delete the workspace MSI user in the database
DROP USER [<workspacename>];
```

## <a name="step-8-add-users-to-security-groups"></a>8. lépés: felhasználók hozzáadása biztonsági csoportokhoz

A hozzáférés-vezérlési rendszer kezdeti konfigurációja befejeződött.

A hozzáférés kezeléséhez hozzáadhat és eltávolíthat felhasználókat az Ön által beállított biztonsági csoportokhoz.  Bár a felhasználók manuálisan is hozzárendelhetők a szinapszis-szerepkörökhöz, ha igen, nem konfigurálja az engedélyeiket konzisztensen. Ehelyett csak felhasználókat adhat hozzá vagy távolíthat el a biztonsági csoportokhoz.

## <a name="step-9-network-security"></a>9. lépés: hálózati biztonság

A munkaterület biztonságossá tételének utolsó lépéseként biztonságossá kell tennie a hálózati hozzáférést a következők használatával:
- [Munkaterület tűzfala](./synapse-workspace-ip-firewall.md)
- [Felügyelt virtuális hálózat](./synapse-workspace-managed-vnet.md) 
- [Privát végpontok](./synapse-workspace-managed-private-endpoints.md)
- [Privát kapcsolat](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-10-completion"></a>10. lépés: Befejezés

A munkaterület mostantól teljesen konfigurálva van és biztonságos.

## <a name="supporting-more-advanced-scenarios"></a>Fejlettebb forgatókönyvek támogatása

Ez az útmutató egy alapszintű hozzáférés-vezérlési rendszer beállítására koncentrál. További speciális forgatókönyveket is támogathat, ha további biztonsági csoportokat hoz létre, és ezeket a csoportokat részletesebben szeretné hozzárendelni a pontosabb hatókörökhöz. Vegye figyelembe a következő eseteket:

A **git engedélyezése –** a munkaterület támogatása fejlettebb fejlesztési forgatókönyvekhez, például CI/CD-hez.  Git módban a git-engedélyek határozzák meg, hogy a felhasználó véglegesítheti-e a munkaágat.  A szolgáltatáshoz való közzététel csak az együttműködési ág alapján történik.  Érdemes lehet olyan biztonsági csoportot létrehozni a fejlesztőknek, akik egy működő ágban szeretnének fejlesztést és hibakeresést végezni, de nem kell közzétenniük az élő szolgáltatás módosításait.

A **fejlesztői hozzáférés korlátozása** adott erőforrásokhoz.  További finomabb biztonsági csoportokat hozhat létre azon fejlesztők számára, akiknek csak bizonyos erőforrásokhoz kell hozzáférni.  Ezek a csoportok megfelelő szinapszis-szerepköröket rendelnek hozzá, amelyek meghatározott Spark-készletekre, integrációs futtatókörnyezetekre vagy hitelesítő adatokra vannak korlátozva.

**Korlátozza a kezelők számára a kódok elérését**.  Hozzon létre biztonsági csoportokat azon operátorok számára, akiknek figyelniük kell a szinapszis számítási erőforrások működési állapotát, és meg kell tekintenie a naplókat, de nincs szükségük a kódok elérésére vagy a szolgáltatás frissítéseinek közzétételére. Rendelje hozzá ezeket a csoportokat a számítási operátori szerepkörhöz, amely meghatározott Spark-készletekre és integrációs modulokra terjed ki.  

## <a name="next-steps"></a>Következő lépések

Ismerje meg [, hogyan kezelheti a szinapszis-RBAC szerepkör-hozzárendeléseket](./how-to-manage-synapse-rbac-role-assignments.md) egy [szinapszis-munkaterület](../quickstart-create-workspace.md) létrehozásával
