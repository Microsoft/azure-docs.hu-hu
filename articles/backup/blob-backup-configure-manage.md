---
title: Az Azure-Blobok működési biztonsági mentésének konfigurálása
description: Ismerje meg, hogyan konfigurálhatja és kezelheti az Azure-Blobok működési biztonsági mentését (előzetes verzió)
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 0dc490842389ba9286799aef5d37c1cf7c1ba64e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051072"
---
# <a name="configure-operational-backup-for-azure-blobs-in-preview"></a>Az Azure-Blobok működési biztonsági mentésének konfigurálása (előzetes verzió)

Azure Backup lehetővé teszi, hogy egyszerűen konfigurálja a biztonsági mentést a tárolási fiókokban lévő blokk-Blobok védelmére. Ez a cikk azt ismerteti, hogyan konfigurálhatja az operatív biztonsági mentést egy vagy több Storage-fiókra a Azure Portal használatával. A cikk a következőket ismerteti:

- A Kezdés előtt szükséges tudnivalók
- Backup-tároló létrehozása
- Engedélyek megadása a mentési tárolóhoz a védeni kívánt Storage-fiókokon
- Biztonsági mentési szabályzat létrehozása
- Az operatív biztonsági mentés konfigurálása egy vagy több Storage-fiókon
- A tárolási fiókok biztonsági mentésének hatásai

## <a name="before-you-start"></a>Előkészületek

- A Blobok működési biztonsági mentése egy helyi biztonsági mentési megoldás, amely a forrás Storage-fiókban megadott időtartamra vonatkozó adatmennyiséget tárolja. Ez a megoldás nem tart fenn további adatmásolatot a tárolóban.
- Ez a megoldás lehetővé teszi, hogy az adatait legfeljebb 360 napig őrizze meg visszaállításra. A hosszú megőrzési időtartam azonban hosszabb időt is igénybe vehet a visszaállítási művelet során.
- A megoldás csak a forrásként szolgáló Storage-fiókra történő visszaállításra használható, és az adatok felülírását is eredményezheti.
- Ha töröl egy tárolót a Storage-fiókból a tároló törlése művelet meghívásával, a tároló nem állítható vissza visszaállítási művelettel. A teljes tároló törlése helyett törölje az egyes blobokat, ha később szeretné visszaállítani őket. A Microsoft emellett azt is javasolja, hogy az üzemeltetési biztonsági mentésen felül engedélyezze a tárolók törlését a tárolók véletlen törlésével szembeni védelem érdekében.
- A támogatott forgatókönyvekkel, korlátozásokkal és rendelkezésre állással kapcsolatos további információkért tekintse meg a [támogatási mátrixot](blob-backup-support-matrix.md) .

## <a name="create-a-backup-vault"></a>Backup-tároló létrehozása

A [Backup-tároló](backup-vault-overview.md) egy olyan felügyeleti entitás, amely az idő múlásával létrehozott helyreállítási pontokat tárolja, és egy felületet biztosít a biztonsági mentéssel kapcsolatos műveletek elvégzéséhez. Ezek közé tartoznak az igény szerinti biztonsági másolatok készítése, a visszaállítások végrehajtása, illetve a biztonsági mentési szabályzatok létrehozása. Bár a Blobok működési biztonsági mentése egy helyi biztonsági másolat, és nem tárolja a tárolóban lévő adatok tárolását, a tár különböző felügyeleti műveletekhez szükséges.

>[!NOTE]
>A Backup-tároló egy új erőforrás, amely az új támogatott munkaterhelések biztonsági mentésére szolgál, és eltér a már meglévő Recovery Services-tárolótól.

A Backup-tároló létrehozásával kapcsolatos utasításokért tekintse meg a [Backup Vault dokumentációját](backup-vault-overview.md#create-a-backup-vault).

## <a name="grant-permissions-to-the-backup-vault-on-storage-accounts"></a>Engedélyek megadása a Backup-tárolóhoz a Storage-fiókokban

Az operatív biztonsági mentés védi a Storage-fiókot (amely a védeni kívánt blobokat tartalmazza) minden véletlen törlésből a biztonsági mentési tulajdonos törlési zárolásának alkalmazásával. Ehhez a Backup-tárolónak bizonyos engedélyekkel kell rendelkeznie a védeni kívánt Storage-fiókokhoz. A használat kényelme érdekében ezeket az engedélyeket a Storage-fiók biztonsági mentése közreműködő szerepkör alatt konszolidálták. Kövesse az alábbi utasításokat a védeni kívánt Storage-fiókok esetében:

1. A védeni kívánt Storage-fiókban navigáljon a bal oldali navigációs ablaktábla **Access Control (iam) lapjára** .
1. Válassza a **szerepkör-hozzárendelések hozzáadása** lehetőséget a szükséges szerepkör hozzárendeléséhez.

    ![Szerepkör-hozzárendelések hozzáadása](./media/blob-backup-configure-manage/add-role-assignments.png)

1. A szerepkör-hozzárendelés hozzáadása panelen:

    1. A **szerepkör** területen válassza a **Storage-fiók biztonsági mentése közreműködő** elemet.
    1. A **hozzáférés társítása** alatt válassza a **felhasználó, csoport vagy egyszerű szolgáltatásnév** lehetőséget.
    1. Írja be annak a Backup-tárolónak a **nevét** , amelyet a Blobok védelemmel kíván biztosítani a Storage-fiókban, és válassza ki ugyanezt a keresési eredmények közül.
    1. Ha elkészült, válassza a **Mentés** lehetőséget.

        ![Szerepkör-hozzárendelési beállítások](./media/blob-backup-configure-manage/role-assignment-options.png)

        >[!NOTE]
        >Akár 10 percet is igénybe vehet, amíg a szerepkör-hozzárendelés érvénybe lép.

## <a name="create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

A biztonsági mentési szabályzat általában a biztonsági másolatok megőrzését és ütemezését szabályozza. Mivel a Blobok operatív biztonsági mentése folyamatos jellegű, a biztonsági mentések elvégzéséhez nincs szükség ütemezésre. A házirend alapvetően szükséges a megőrzési időtartam megadásához. A biztonsági mentési házirendet használhatja és újból felhasználhatja, hogy a biztonsági mentést több Storage-fiókra is konfigurálja egy tárolóba.

A következő lépésekkel hozhat létre biztonsági mentési szabályzatot a Blobok operatív biztonsági mentéséhez:

1. A Backup-tárolóban navigáljon a **biztonsági mentési házirendek** elemre, és válassza a **+ Hozzáadás** elemet a biztonsági mentési szabályzat létrehozásához.

    ![Biztonsági mentési szabályzatok](./media/blob-backup-configure-manage/backup-policies.png)

1. Az **alapvető beállítások** lapon adja meg a biztonsági mentési szabályzat nevét, és válassza ki az **Azure-blobokat** az adatforrás típusaként. A kiválasztott tároló adatait is megtekintheti.

    ![Biztonsági mentési szabályzat létrehozása](./media/blob-backup-configure-manage/create-backup-policy.png)

    >[!NOTE]
    >Bár látni fogja a **tár biztonsági mentési tárterületének redundanciát** , a redundancia nem igazán vonatkozik a Blobok működési biztonsági másolatából, mivel a biztonsági mentés helyi jellegű, és a Backup-tárolóban nem tárolnak semmilyen információt. A Backup-tároló itt a felügyeleti entitás, amely segítséget nyújt a Storage-fiókokban található blokk-Blobok védelmének kezelésében.

1. A **biztonsági mentési szabályzat** lapon megadhatja a megőrzés részleteit. Megtekintheti, hogy az **alapértelmezett** megőrzési szabály 30 napos megőrzési időtartammal rendelkezik. Ha szerkeszteni szeretné a megőrzés időtartamát, az **adatmegőrzési szabály** szerkesztése ikon használatával szerkessze és határozza meg azt az időtartamot, amelynek meg szeretné őrizni az adatát. Megadhatja, hogy legfeljebb 360 nap legyen megtartva.

    ![Biztonsági mentési szabályzat lap](./media/blob-backup-configure-manage/backup-policy-tab.png)

    >[!NOTE]
    >A hosszú időtartamok visszaállítása a visszaállítási műveletek elvégzéséhez vezethet. Emellett az adatok egy halmazának visszaállításához szükséges idő a visszaállítási időszak során végrehajtott írási és törlési műveletek számától függ. Például egy 1 000 000 objektummal rendelkező, naponta hozzáadott 3 000-objektummal rendelkező fiók, valamint a naponta törölt 1 000-objektumok esetében körülbelül két óra elteltével vissza kell állítani egy 30 napos pontot a múltban. A megőrzési időtartam és a helyreállítás több mint 90 nappal a múltban nem ajánlott olyan fiók esetében, amely ezen a változási aránnyal rendelkezik.

1. A **felülvizsgálat + létrehozás** ablaktáblán ellenőrizze a szabályzat összes adatát, és válassza a **Létrehozás** gombra a szabályzat létrehozásához. Egy értesítés megerősíti, hogy a biztonsági mentési házirend létrejött, és készen áll a használatra.

    ![Házirend áttekintése és létrehozása](./media/blob-backup-configure-manage/review-create.png)

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása

A Blobok biztonsági mentése a Storage-fiók szintjén van konfigurálva. Így a Storage-fiókban lévő összes blob védett a működési biztonsági mentéssel.

A biztonsági mentés konfigurálásának megkezdéséhez:

1. Keresse meg a **Backup centert** a keresősáv alatt.
1. Navigáljon az **Áttekintés**  ->  **+ biztonsági mentés** elemre.

    ![A Backup Center áttekintése](./media/blob-backup-configure-manage/backup-center-overview.png)

1. A **kezdeményezés: biztonsági mentés konfigurálása** lapon válassza az **Azure-Blobok (Azure Storage)** lehetőséget az adatforrás típusaként.

    ![Kezdeményezés: biztonsági mentés konfigurálása lap](./media/blob-backup-configure-manage/initiate-configure-backup.png)

1. Az **alapvető beállítások** lapon adja meg az **Azure-blobokat (Azure Storage)** az **adatforrás** típusaként, és válassza ki azt a mentési tárolót, amelyhez hozzá szeretné rendelni a Storage-fiókokat. A panelen megtekintheti a kijelölt tároló adatait.

    ![Alapbeállítások lap](./media/blob-backup-configure-manage/basics-tab.png)

1. Ezután válassza ki az adatmegőrzés megadásához használni kívánt biztonsági mentési szabályzatot. A kiválasztott szabályzat részleteit a képernyő alsó részén tekintheti meg. Az operatív adattár oszlopban látható a szabályzatban meghatározott adatmegőrzés. A "működési" érték azt jelenti, hogy az adattárolást a forrás Storage-fiókban helyileg kell fenntartani.

    ![Biztonsági mentési házirend kiválasztása](./media/blob-backup-configure-manage/choose-backup-policy.png)

    Létrehozhat egy új biztonsági mentési szabályzatot is. Ehhez válassza az **új létrehozása** lehetőséget, és kövesse az alábbi lépéseket:

    1. Adja meg a létrehozni kívánt szabályzat nevét. Győződjön meg arról, hogy a többi mező a megfelelő adatforrás-típust és tároló nevét jeleníti meg.
    1. A **biztonsági mentési szabályzat** lapon jelölje be az adatmegőrzési szabály szerkesztése ikont a szerkesztéshez, és adja meg azt az időtartamot, amelynek meg szeretné őrizni az adatát. Megadhatja, hogy legfeljebb 360 nap legyen megtartva. A hosszú időtartamok visszaállítása a visszaállítási műveletek elvégzéséhez vezethet.

        ![Új biztonsági mentési szabályzat létrehozása](./media/blob-backup-configure-manage/new-backup-policy.png)

    1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** lehetőséget a biztonsági mentési szabályzat létrehozásához.

1. Ezután ki kell választania azokat a Storage-fiókokat, amelyeken a Blobok védelmét konfigurálni szeretné. Egyszerre több Storage-fiókot is kiválaszthat, és válassza a **kiválasztás** lehetőséget.

    Azonban győződjön meg arról, hogy a kiválasztott tároló rendelkezik a megfelelő engedélyekkel a biztonsági mentés konfigurálásához a Storage-fiókokon a fentiekben részletezett módon, a [Storage-fiókokban lévő Backup-tároló engedélyeinek megadásakor](#grant-permissions-to-the-backup-vault-on-storage-accounts).

    ![Válassza ki az erőforrásokat a biztonsági mentéshez](./media/blob-backup-configure-manage/select-resources.png)

    A biztonsági mentés ellenőrzi, hogy a tároló rendelkezik-e megfelelő engedélyekkel a biztonsági mentés konfigurálásához a kiválasztott Storage-fiókokon.

    ![Biztonsági mentés érvényesíti az engedélyeket](./media/blob-backup-configure-manage/validate-permissions.png)

    Ha az érvényesítés hibákat eredményez (mint a képernyőképen lévő egyik Storage-fiók esetében), lépjen a kiválasztott Storage-fiókokra, és rendelje hozzá a megfelelő szerepköröket az [itt](#grant-permissions-to-the-backup-vault-on-storage-accounts)részletezett módon, és válassza az **újraellenőrzés** lehetőséget. Az új szerepkör-hozzárendelés akár 10 percet is igénybe vehet.

1. Miután az érvényesítés sikeres volt az összes kiválasztott Storage-fiók esetében, folytassa a biztonsági mentés konfigurálását **és konfigurálását** . A védelem konfigurálásának és befejezésének állapotáról tájékoztató értesítések jelennek meg.

## <a name="effects-on-backed-up-storage-accounts"></a>A Storage-fiókok biztonsági mentésének hatásai

Ha a biztonsági mentés be van állítva, a rendszer nyomon követi a tárolási fiókokban lévő blokk-blobokon végrehajtott módosításokat, és a biztonsági mentési szabályzatnak megfelelően megőrzi az adatmegőrzési időt. A következő változásokat fogja látni a Storage-fiókokban, amelyekhez a biztonsági mentés konfigurálva van:

- A Storage-fiókban a következő lehetőségek engedélyezettek: Ezeket a Storage-fiók **Adatvédelem** lapján lehet megtekinteni.
  - A tárolók időponthoz való visszaállítása: a biztonsági mentési szabályzatban megadott megőrzési idővel
  - A Blobok Soft DELETE: a biztonsági mentési szabályzatban megadott megőrzési időtartammal + 5 nap
  - Blobok verziószámozása
  - BLOB változási csatornája

  Ha a biztonsági mentéshez konfigurált Storage-fiókhoz már be van állítva  **a tárolók vagy a** **Soft delete** (a biztonsági mentés konfigurálása előtt), akkor a biztonsági mentés biztosítja, hogy a megőrzés legalább a biztonsági mentési szabályzatban meghatározott módon történjen. Ezért az egyes tulajdonságokhoz:

  - Ha a biztonsági mentési szabályzat megőrzése nagyobb, mint a Storage-fiókban eredetileg tárolt adatmegőrzési szabály: a Storage-fiók megőrzése a biztonsági mentési szabályzatnak megfelelően módosul.
  - Ha a biztonsági mentési házirend megőrzése nem éri el a tárolási fiókban eredetileg tárolt adatmegőrzési szabályt, akkor a Storage-fiók megőrzése az eredetileg beállított időtartamon belül változatlan marad.

  ![Adatvédelem lap](./media/blob-backup-configure-manage/data-protection.png)

- A védett Storage-fiók biztonsági másolata a **törlési zárolást** alkalmazza. A zárolás célja, hogy megvédje a Storage-fiók véletlen törlésének eseteit. Ez a **Storage-fiókok**  >  **zárolásai** alatt tekinthető meg.

    ![Törlési zárolások](./media/blob-backup-configure-manage/delete-lock.png)

## <a name="manage-operational-backup"></a>Működési biztonsági mentés kezelése

Az összes biztonsági mentés felügyeletéhez használhatja a Backup centert az üveg egyetlen ablaktábláján. Az Azure-Blobok működési biztonsági mentésével kapcsolatban a Backup Center használatával végezheti el a következőket:

- Ahogy már láttuk, használhatja a Backup-tárolók és-szabályzatok létrehozásához. A kijelölt előfizetésekben lévő összes tárat és szabályzatot is megtekintheti.
- A Backup Center egyszerűen figyelheti a védett tárolási fiókok védelmének állapotát, valamint azokat a Storage-fiókokat, amelyekhez jelenleg nincs konfigurálva a biztonsági másolat.
- A biztonsági mentést bármely Storage-fiókhoz a **+ Backup (biztonsági mentés** ) gomb használatával konfigurálhatja.
- A visszaállítás gomb használatával **visszaállíthatja** a visszatárolást, és nyomon követheti a **biztonsági mentési feladatok** segítségével. További információ a visszatárolásról: [Azure-Blobok visszaállítása](blob-backup-support-matrix.md).
- A biztonsági másolatok használatának elemzése biztonsági mentési jelentések használatával.

    ![Biztonsági mentési központ](./media/blob-backup-configure-manage/backup-center.png)

További információ: [a Backup Center áttekintése (előzetes verzió)](backup-center-overview.md).

## <a name="next-steps"></a>Következő lépések

- [Azure-Blobok visszaállítása](blob-restore.md)
