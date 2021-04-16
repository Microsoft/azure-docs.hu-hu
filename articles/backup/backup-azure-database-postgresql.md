---
title: Az Azure Database for PostgreSQL biztonsági mentése
description: Tudnivalók a Azure Database for PostgreSQL megőrzéssel (előzetes verzió) való biztonsági mentésről
ms.topic: conceptual
ms.date: 04/12/2021
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 8fd69e016c7f0b175ef49b98add5692743858f62
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480069"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Azure Database for PostgreSQL biztonsági mentés készítése hosszú távú megőrzéssel (előzetes verzió)

Azure Backup és az Azure Database Services együttesen olyan nagyvállalati szintű biztonsági mentési megoldást hoz létre Azure Database for PostgreSQL-kiszolgálókhoz, amelyek legfeljebb 10 évig őrzik meg a biztonsági másolatokat.

A hosszú távú megőrzés mellett a megoldásnak számos más képessége is van, az alábbiakban látható módon:

- Azure-beli szerepköralapú hozzáférés-vezérlés (Azure RBAC) az adatbázishoz Azure Active Directory msi-hitelesítéssel.
- Ügyfél által vezérelt ütemezett és igény szerinti biztonsági mentés az egyes adatbázisok szintjén.
- Adatbázisszintű visszaállítás bármely Postgres-kiszolgálóra vagy közvetlenül a blobtárolóba.
- Hosszú távú megőrzés.
- Az összes művelet és feladat központi figyelése.
- A biztonsági mentések külön biztonsági és tartalék tartományokban vannak tárolva. Így még ha a forráskiszolgálót is feltörik vagy akár le is állják, a biztonsági másolatok biztonságban maradnak a [Backup-tárolóban.](backup-vault-overview.md)
- A **pg_dump** nagyobb rugalmasságot biztosít a visszaállítások terén, így az adatbázisverziók között, vagy akár csak a biztonsági másolat egy részét is visszaállíthatja.

Ezt a megoldást használhatja függetlenül, vagy az Azure PostgreSQL által kínált natív biztonsági mentési megoldás mellett, amely akár 35 napig is biztosít megőrzést. A natív megoldás működési helyreállításhoz megfelelő, például ha a legújabb biztonsági másolatokat szeretné helyreállítani. A Azure Backup megoldás segít a megfelelőségi igényeknek, valamint a részletesebb és rugalmasabb biztonsági mentésnek és visszaállításnak.

## <a name="support-matrix"></a>Támogatási mátrix

|Támogatás  |Részletek  |
|---------|---------|
|Támogatott központi telepítések   |  [Azure Database for PostgreSQL – Önálló kiszolgáló](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|Támogatott Azure-régiók |  USA keleti régiója, USA 2. keleti régiója, USA középső régiója, USA déli középső régiója, USA nyugati régiója, USA 2. nyugati régiója, Dél-Brazília, Közép-Kanada, Észak-Európa, Nyugat-Európa, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Közép-Németország, Svájc északi régiója, Usa nyugati régiója, Kelet-Ázsia Kelet-Ázsia, Délkelet-Japán, Nyugat-Korea, Korea déli régiója, Közép-Ausztrália, Ausztrália középső régiója, Ausztrália 2. középső régiója, Egyesült Arab Emírségek északi régiója  |
|Támogatott Azure PostgreSQL-verziók    |   9.5, 9.6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>A funkciókra vonatkozó szempontok és korlátozások

- Az összes művelet csak a Azure Portal támogatott.
- A maximális adatbázisméret javasolt korlátja 400 GB.
- A régiók közötti biztonsági mentés nem támogatott. Ez azt jelenti, hogy egy Azure PostgreSQL-kiszolgálóról nem lehet biztonsági adatbázist egy másik régióban található tárolóba biztonsági ásni. Hasonlóképpen, csak a tárolóval egy régióban található kiszolgálóra állítható vissza biztonsági másolat.
- A visszaállításkor csak az adatok állíthatók helyre. A "Szerepkörök" nem állíthatók vissza.
- Az előzetes verzióban azt javasoljuk, hogy a megoldást csak a saját tesztkörnyezetében futtassa.

## <a name="backup-process"></a>A biztonsági mentés folyamata

1. Ez a **megoldás** pg_dump az Azure PostgreSQL-adatbázisok biztonsági mentéséhez.

2. Miután megadja azokat az Azure PostgreSQL-adatbázisokat, amelyekről biztonsági másolatot szeretne készíteni, a szolgáltatás ellenőrzi, hogy rendelkezik-e a megfelelő engedélyekkel és hozzáféréssel a biztonsági mentési művelet végrehajtásához a kiszolgálón és az adatbázisban.

3. Azure Backup egy feldolgozói szerepkört, amelybe telepítve van egy biztonsági mentési bővítmény. Ez a bővítmény kommunikál a Postgres-kiszolgálóval.

4. Ez a bővítmény egy koordinátorból és egy Azure Postgres beépülő modulból áll. Bár a koordinátor felelős a különböző műveletek (például a biztonsági mentés, a biztonsági mentés és a visszaállítás) munkafolyamatának aktiválásáért, a beépülő modul felelős a tényleges adatfolyamért.
  
5. Miután elindította a védelem konfigurálását a kiválasztott adatbázisokon, a biztonsági mentési szolgáltatás beállítja a koordinátort a biztonsági mentési ütemezésekkel és a szabályzat egyéb részleteivel.

6. Az ütemezett időpontban a koordinátor kommunikál a beépülő modullal, és elindítja a biztonsági mentési adatok streamelését a Postgres-kiszolgálóról a **következő pg_dump.**

7. A beépülő modul közvetlenül a Backup-tárolóba küldi az adatokat, így nincs szükség átmeneti helyre. Az adatokat a Microsoft által felügyelt kulcsokkal titkosítja, és a Azure Backup tárfiókban tárolja.

8. Ha az adatátvitel befejeződött, a koordinátor megerősíti a véglegesítést a biztonsági mentési szolgáltatással.

    ![A biztonsági mentés folyamata](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Biztonsági mentés konfigurálása Azure PostgreSQL-adatbázisokon

Több Azure PostgreSQL-kiszolgálón több adatbázisra is konfigurálhat biztonsági mentést. Győződjön meg [](#prerequisite-permissions-for-configure-backup-and-restore) arról, hogy a szolgáltatás számára a Postgres-kiszolgálók biztonsági frissítéséhez szükséges előfeltétel-engedélyek már konfigurálva vannak.

Az alábbi utasítások részletes útmutatót tartalmaznak az Azure PostgreSQL-adatbázisok biztonsági mentésének konfigurálásához a Azure Backup:

1. A folyamat kétféleképpen kezdődhet:

    1. Ugrás a [Backup Center](backup-center-overview.md)  ->  **áttekintési biztonsági**  ->  **mentéséhez.**

        ![Ugrás a Backup Centerre](./media/backup-azure-database-postgresql/backup-center.png)

        Az **Initiate: Configure Backup (Biztonsági mentés konfigurálása)** alatt válassza a **Datasource type** (Adatforrás típusa) lehetőséget **Azure Database for PostgreSQL.**

        ![Az Initiate: Configure Backup (Kezdeményezés: Biztonsági mentés konfigurálása) mezőben válassza az Adatforrás típusa lehetőséget.](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. Másik lehetőségként közvetlenül a [Backup-tárolók biztonsági](backup-vault-overview.md)mentését is  ->  **használhatja.**

        ![Ugrás a Backup-tárolókra](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Válassza a Biztonsági mentés lehetőséget a Backup-tárolóban](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. A **Biztonsági mentés konfigurálása alatt** válassza ki azt a Backup-tárolót, amelybe biztonsági másolatot szeretne készíteni a Postgres-adatbázisokról.  Ezek az információk előre ki vannak töltve, ha már a tároló kontextusában van.

    ![A Biztonsági mentés konfigurálása beállításban válassza a Backup-tároló lehetőséget](./media/backup-azure-database-postgresql/configure-backup.png)

1. Válasszon ki vagy hozzon létre egy **Biztonsági mentési szabályzatot.**

    ![Biztonsági mentési szabályzat kiválasztása](./media/backup-azure-database-postgresql/backup-policy.png)

1. Válassza ki az erőforrásokat vagy a Postgres-adatbázisokat a biztonsági mentése érdekében.

    ![Válassza ki a biztonságimentni kívánt erőforrásokat](./media/backup-azure-database-postgresql/select-resources.png)

1. Az előfizetések összes Azure PostgreSQL-kiszolgálója közül választhat, ha azok ugyanabban a régióban vannak, mint a tároló. Bontsa ki a nyilat a kiszolgálón belüli adatbázisok listájának a listájának a kibontásához.

    ![Kiszolgálók kiválasztása](./media/backup-azure-database-postgresql/choose-servers.png)

1. A szolgáltatás ezeket az ellenőrzéseket a kiválasztott adatbázisokon futtatja annak ellenőrzéséhez, hogy a tároló rendelkezik-e engedélyekkel a kiválasztott Postgres-kiszolgálók és -adatbázisok biztonsági mentésekor.
    1. **A folytatáshoz minden** adatbázis  biztonsági mentésének sikeresnek kell lennie.
    1. Hiba esetén javítsa ki  a hibát,  és javítsa újra, vagy távolítsa el az adatbázist a kijelölésből.

    ![Kijavítható érvényesítési hibák](./media/backup-azure-database-postgresql/validation-errors.png)

1. Erősítse meg az Áttekintés és konfigurálás alatti összes **részletet,** majd válassza a **Biztonsági mentés konfigurálása lehetőséget** a művelet elküldéhez.

    ![Részletek megerősítése az Áttekintés és konfigurálás során](./media/backup-azure-database-postgresql/review-and-configure.png)

1. Az aktiválás után a **Biztonsági mentés konfigurálása** művelet létrehoz egy biztonsági mentési példányt. A művelet állapotát a Backup [](backup-center-monitor-operate.md#backup-instances) Center vagy a tároló nézet Biztonságimásolat-példányok panelje alatt követheti nyomon.

    ![Biztonságimásolat-példányok](./media/backup-azure-database-postgresql/backup-instances.png)

1. A biztonsági mentések a szabályzatban meghatározott ütemezés szerint aktiválódnak. A feladatok a Biztonsági mentési feladatok alatt [követhetők nyomon.](backup-center-monitor-operate.md#backup-jobs) Jelenleg az elmúlt hét napban lévő feladatokat lehet megtekinteni.

    ![Biztonsági mentési feladatok](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

1. Ugrás a **Backup Center biztonsági mentési**  ->  **házirendek Hozzáadás gombra.**  ->   Másik lehetőségként a **Backup-tároló biztonsági mentési**  ->  **szabályzatának Hozzáadása lehetőségéhez**  ->  is használhatja a **et.**

    ![Biztonsági mentési szabályzat hozzáadása](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Adja meg **az új** szabályzat nevét.

    ![Adja meg a szabályzat nevét](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Határozza meg a Biztonsági mentés ütemezését. Jelenleg a heti biztonsági **mentést** támogatjuk. A biztonsági mentéseket a hét egy vagy több napján ütemezheti.

    ![A biztonsági mentés ütemezésének meghatározása](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. **Megőrzési beállítások** megadása. Hozzáadhat egy vagy több adatmegőrzési szabályt. Minden megőrzési szabály adott biztonsági mentések bemenetét, valamint a biztonsági mentések adattárát és megőrzési időtartamát feltételezi.

1. A biztonsági másolatokat a két adattár (vagy szint) egyikében tárolhatja: Backup **data store** (standard szint) vagy **Archív** adattár (előzetes verzióban).

   A Lejáratkor **lehetőséget választva** a biztonsági mentést a biztonsági mentési adattárban való lejáratkor archivált adattárba is áthelyezheti.

1. Az **alapértelmezett adatmegőrzési** szabály bármely más adatmegőrzési szabály hiányában lesz alkalmazva, és az alapértelmezett értéke három hónap.

    - A megőrzési időtartam hét nap és 10 év között lehet a **Backup adattárában.**
    - A megőrzési időtartam hat hónap és 10 év között lehet az **archív adattárban.**

    ![Megőrzési időtartam szerkesztése](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>A megőrzési szabályokat a rendszer előre meghatározott prioritási sorrendben értékeli ki. A prioritás az  éves szabályhoz a legmagasabb, amelyet a **havi,** majd a heti **szabály követ.** Az alapértelmezett adatmegőrzési beállítások akkor vannak alkalmazva, ha nincs más szabály megfelelő. Például ugyanaz a helyreállítási pont lehet az első sikeres biztonsági mentés minden héten, valamint az első sikeres biztonsági mentés minden hónapban. Mivel azonban a havi szabály prioritása magasabb, mint a heti szabály, a havonta végzett első sikeres biztonsági mentésnek megfelelő megőrzési idő érvényes.

## <a name="restore"></a>Visszaállítás

Az adatbázist bármely Azure PostgreSQL-kiszolgálóra visszaállíthatja ugyanazon az előfizetésen belül, ha a szolgáltatás rendelkezik a megfelelő engedélyekkel a célkiszolgálón. Győződjön meg [arról,](#prerequisite-permissions-for-configure-backup-and-restore) hogy a szolgáltatásnak a Postgres-kiszolgálók biztonsági frissítéséhez szükséges előfeltétel-engedélyei már konfigurálva vannak.

Kövesse ezt a részletes útmutatót a visszaállítás aktiváláshoz:

1. A visszaállítási folyamat kétféleképpen indítható el:
    1. Ugrás a [Backup Center](backup-center-overview.md)  ->  **áttekintési**  ->  **visszaállítására.**

    ![Válassza a Visszaállítás lehetőséget a Backup Centerben](./media/backup-azure-database-postgresql/backup-center-restore.png)

    Az **Initiate: Restore (Kezdeményezés: Visszaállítás)** alatt válassza a **Datasource type** (Adatforrás típusa) lehetőséget a **Azure Database for PostgreSQL.** Válassza ki **a Biztonságimásolat-példányt.**

    ![Válassza az Adatforrás típusa lehetőséget az Initiate:Restore (Kezdeményezés:Visszaállítás) mezőben](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. Másik lehetőségként közvetlenül a Backup-tároló Backup Instances **(Biztonságimásolat-példányok)**  ->  **szolgáltatását is használhatja.** Válassza **ki a visszaállítani** kívánt adatbázisnak megfelelő Biztonsági mentési példányt.

    ![Biztonságimásolat-példányok visszaállításhoz](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Biztonságimásolat-példányok listája](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Válassza a Visszaállítás lehetőséget](./media/backup-azure-database-postgresql/select-restore.png)

1. **Válassza ki a helyreállítási** pontot a kiválasztott biztonságimásolat-példányhoz elérhető összes teljes biztonsági mentés listájából. Alapértelmezés szerint a legutóbbi helyreállítási pont van kiválasztva.

    ![Helyreállítási pont kiválasztása](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Helyreállítási pontok listája](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. Bemeneti **visszaállítási paraméterek.** Ezen a ponton kétféle visszaállítás közül választhat: Visszaállítás adatbázisként **és** Visszaállítás **fájlként.**

1. **Visszaállítás adatbázisként:** Állítsa vissza a biztonsági mentési adatokat egy új adatbázis létrehozásához a cél PostgreSQL-kiszolgálón.

    - A célkiszolgáló és a forráskiszolgáló is lehet. Az eredeti adatbázis felülírása azonban nem támogatott.
    - Választhat a kiszolgálóról az összes előfizetésben, de ugyanabban a régióban, mint a tároló.
    - Válassza a **Felülvizsgálat + visszaállítás lehetőséget.** Ez elindítja az ellenőrzést, és ellenőrzi, hogy a szolgáltatás rendelkezik-e megfelelő visszaállítási engedélyekkel a célkiszolgálón.

    ![Visszaállítás adatbázisként](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Visszaállítás fájlokként:** A biztonsági mentési fájlok kiírása a cél tárfiókba (blobokba).

    - Választhat a tárfiókok közül az összes előfizetésben, de ugyanabban a régióban, mint a tároló.
    - Válassza ki a céltárolót a kiválasztott tárfiókra szűrt tárolólistából.
    - Válassza a **Felülvizsgálat + visszaállítás lehetőséget.** Ez elindítja az ellenőrzést, és ellenőrzi, hogy a szolgáltatás rendelkezik-e megfelelő visszaállítási engedélyekkel a célkiszolgálón.

    ![Visszaállítás fájlként](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Ha a helyreállítási pont az archív szinten van, a visszaállítás előtt rehidratálni kell a helyreállítási pontot.
   
   ![Rehidratálás beállításai](./media/backup-azure-database-postgresql/rehydration-settings.png)
   
   Adja meg a rehidratáláshoz szükséges következő további paramétereket:
   - **Rehidratálás prioritása:** Az alapértelmezett érték **a Standard.**
   - **Rehidratálás időtartama:** A rehidratálás maximális időtartama 30 nap, a rehidratálás minimális időtartama pedig 10 nap. Az alapértelmezett érték **15.**
   
   A helyreállítási pont a biztonsági mentési adattárban van tárolva **a** megadott rehidratálás időtartamára.


1. Tekintse át az adatokat, és válassza a **Visszaállítás lehetőséget.** Ez elindít egy megfelelő visszaállítási feladatot, amely nyomon követhető a **Biztonsági mentési feladatok alatt.**

>[!NOTE]
>Az archív Azure Database for PostgreSQL korlátozott nyilvános előzetes verzióban érhető el.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>Előfeltételekre vonatkozó engedélyek a biztonsági mentés és a visszaállítás konfiguráláshoz

Azure Backup biztonsági irányelvek szigorúak. Bár ez ™ Azure-szolgáltatás, az erőforrásra vonatkozó engedélyeket a rendszer nem feltételezi, és a felhasználónak explicit módon meg kell adnia.  Hasonlóképpen, az adatbázishoz való csatlakozáshoz szükséges hitelesítő adatokat a rendszer nem tárolja. Ez fontos az adatok védelme érdekében. Ehelyett a hitelesítést Azure Active Directory használjuk.

[Töltse le ezt](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) a dokumentumot egy automatizált szkript és a kapcsolódó utasítások letöltéséhez. Megfelelő engedélyeket ad egy Azure PostgreSQL-kiszolgálónak a biztonsági mentéshez és a visszaállításhoz.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>A biztonságimentett Azure PostgreSQL-adatbázisok kezelése

Ezeket a felügyeleti műveleteket hajthatja végre a **Biztonságimásolat-példányon:**

### <a name="on-demand-backup"></a>Igény szerinti biztonsági mentés

Ha nem a szabályzatban megadott ütemezés szerint aktivál egy biztonsági mentést, a Biztonsági másolati példányok biztonsági mentése  ->  **most ás a következővel indítható el:**.
Válasszon a társított biztonsági mentési szabályzatban meghatározott megőrzési szabályok közül.

![Biztonsági mentés aktiválása](./media/backup-azure-database-postgresql/backup-now.png)

![Választás a megőrzési szabályok listájából](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>A védelem kikapcsolása

A biztonsági mentési elemek védelmét leállíthatja. Ezzel törli az adott biztonsági mentési elemhez tartozó helyreállítási pontokat is. Ha a helyreállítási pontok legalább hat hónapig nincsenek az archív szinten, akkor ezeknek a helyreállítási pontoknak a törlése korai törlési költséggel jár. A meglévő helyreállítási pontok megtartásával még nem állítjuk le a védelmet.

![A védelem kikapcsolása](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>Szabályzat módosítása

A társított szabályzatot egy biztonsági mentési példányhoz módosíthatja.

1. Válassza ki **a Biztonságimásolat-példány**  ->  **módosítási szabályzatát.**

    ![Szabályzat módosítása](./media/backup-azure-database-postgresql/change-policy.png)

1. Válassza ki az adatbázisra alkalmazni kívánt új szabályzatot.

    ![Szabályzat ismételt hozzárendelése](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Ez a szakasz hibaelhárítási információkat tartalmaz az Azure PostgreSQL-adatbázisok biztonsági mentése Azure Backup.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Adjon olvasási hozzáférést  a Backup-tároló MSI-nek a PG-kiszolgálón, amelyről biztonsági másolatot szeretne készíteni vagy vissza kíván állítani.

A PostgreSQL-adatbázissal való biztonságos kapcsolat létrehozásához Azure Backup felügyeltszolgáltatás-identitás [(MSI) hitelesítési](../active-directory/managed-identities-azure-resources/overview.md) modellt használ. Ez azt jelenti, hogy a Backup-tároló csak a felhasználó által explicit módon megadott erőforrásokhoz fér hozzá.

A rendszer a létrehozáskor automatikusan hozzárendel egy MSI-t a tárolóhoz. Hozzáférést kell adni a tároló MSI-nek azon PostgreSQL-kiszolgálókhoz, amelyekről adatbázisokat kíván biztonsági mentésekor.

Lépések:

1. A Postgres-kiszolgálón válassza a Access Control **(IAM)** panelt.

    ![Access Control panel](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Válassza **a Szerepkör-hozzárendelések hozzáadása lehetőséget.**

    ![Szerepkör-hozzárendelés hozzáadása](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. A megnyíló jobb oldali helyi panelen adja meg a következőket:<br>

   - **Szerepkör:** Válassza az **Olvasó szerepkört** a legördülő listában.<br>
   - **Hozzáférés hozzárendelése a következő szolgáltatásokhoz:** Válassza a **Felhasználó, csoport vagy** szolgáltatásnév lehetőséget a legördülő listában.<br>
   - **Válassza a következőt:** Adja meg annak a Backup-tárolónak a nevét, amelyről biztonsági másolatot szeretne készíteni a kiszolgálóról és annak adatbázisairól.<br>

    ![Válasszon szerepkört](./media/backup-azure-database-postgresql/select-role-and-enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Hozzon létre egy adatbázis biztonságimásolat-készítő felhasználója, aki hitelesítheti magát a Azure Active Directory:

Ez a hiba akkor jelenhet meg, ha nincs Azure Active Directory-rendszergazda a PostgreSQL-kiszolgálóhoz, vagy ha nincs olyan biztonságimásolat-felhasználó, amely hitelesíteni tud Azure Active Directory.

Lépések:

Adjon hozzá Active Directory rendszergazdát az OSS-kiszolgálóhoz:

Erre a lépésre azért van szükség, hogy egy olyan felhasználón keresztül csatlakozzon az adatbázishoz, amely Azure Active Directory helyett egy jelszóval hitelesítheti magát. Az Azure AD rendszergazdai Azure Database for PostgreSQL szerepköre a következő **lesz: azure_ad_admin.** Csak egy **azure_ad_admin** hozhat létre új adatbázis-felhasználókat, akik hitelesítést tudnak végezni az Azure AD-val.

1. A kiszolgálónézet bal Active Directory válassza a Rendszergazda lapfület, és adja hozzá saját magát (vagy valaki mást) Active Directory rendszergazdaként.

    ![Rendszergazda Active Directory beállítása](./media/backup-azure-database-postgresql/set-admin.png)

1. Mindenképpen mentse **az** AD rendszergazdai felhasználói beállítást.

    ![A Active Directory felhasználói beállítás mentése](./media/backup-azure-database-postgresql/save-admin-setting.png)

Az [engedély-megadó](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) lépések végrehajtásához szükséges lépések listájáért tekintse meg ezt a dokumentumot.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

A hálózati rálátást  az Azure-szolgáltatásokhoz való hozzáférés engedélyezése jelző kiszolgálónézetben való engedélyezésével állapítsa meg. A kiszolgáló nézet Kapcsolatbiztonság **panelje** alatt állítsa az **Azure-szolgáltatásokhoz** való hozzáférés engedélyezése jelzőt **Igenre.**

![Hozzáférés engedélyezése Azure-szolgáltatások számára](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Engedély tárfióktárolóba való visszaállításhoz fájlokként való visszaállításkor

1. Adjon engedélyt a Backup-tároló MSI-nek, hogy hozzáférjen a tárfiók tárolóihoz a Azure Portal.
    1. A **Tárfiók hozzáadása Access Control**  ->    ->  **meg.**
    1. Rendeljen **Storage-blobadatok közreműködője szerepkört** a Backup-tároló MSI-hez.

    ![Storage-blobadatok közreműködői szerepkörének hozzárendelése](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. Vagy adjon részletes engedélyeket ahhoz a tárolóhoz, amelybe visszaállítást fog létrehozni az Azure CLI [az role assignment create parancsával.](/cli/azure/role/assignment)

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Cserélje le az assignee paramétert **a** tároló MSI-jának alkalmazásazonosítójára, a scope paramétert pedig az adott tárolóra való hivatkozáshoz.
    1. A tároló **MSI-azonosítójának** leához válassza a **Minden alkalmazás** lehetőséget az Alkalmazás típusa **alatt:**

        ![Válassza a Minden alkalmazás lehetőséget](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Keresse meg a tároló nevét, és másolja ki az alkalmazásazonosítót:

        ![Tárolónév keresése](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Következő lépések

- [Backup-tárolók áttekintése](backup-vault-overview.md)
