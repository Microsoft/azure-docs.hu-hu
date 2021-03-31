---
title: Offline biztonsági mentés a DPM és a MABS Azure Data Box
description: A Azure Data Box használatával a kezdeti biztonsági mentési adatok a DPM és a MABS offline állapotba helyezhetők.
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 1cfd9131099ad6a8ccd3d43e93f3d97641514f03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96752549"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>Offline előkészítés a DPM és a MABS Azure Data Box használatával (előzetes verzió)

> [!NOTE]
> Ez a funkció Data Protection Manager (DPM) 2019 UR2 és újabb rendszerekre alkalmazható.<br><br>
> Ez a szolgáltatás jelenleg előzetes verzióban érhető el Microsoft Azure Backup-kiszolgáló (MABS) számára. Ha szeretné, hogy a MABS-vel való kapcsolat nélküli kivezetéshez Azure Data Box használjon, a következő címen juthat el hozzánk: [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) .

Ez a cikk azt ismerteti, hogyan használhatók a Azure Data Box a kezdeti biztonsági mentési adatok a DPM és a MABS szolgáltatásból az Azure Recovery Services-tárolóba való összevetéséhez.

A [Azure Data Box](../databox/data-box-overview.md) használatával a nagyméretű kezdeti DPM/MABS biztonsági mentések offline állapotba helyezhetők (a hálózat használata nélkül) egy Recovery Services-tárolóba. Ezzel a folyamattal időt és hálózati sávszélességet takaríthat meg, amely egyébként nagy mennyiségű biztonsági mentési adat online állapotba helyezésével nagy késleltetésű hálózaton keresztül történik. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

A Azure Data Boxon alapuló offline biztonsági mentés két különböző előnyt biztosít [Az Azure import/export szolgáltatás alapján az offline biztonsági mentéshez](backup-azure-backup-server-import-export.md):

- Nem kell saját Azure-kompatibilis lemezeket és összekötőket beszereznie. Azure Data Box hajók a kiválasztott [Data Box SKU](https://azure.microsoft.com/services/databox/data/)-hoz társított lemezeket.

- A Azure Backup (MARS-ügynök) közvetlenül a Azure Data Box támogatott SKU-jéről írhatja fel a biztonsági mentési adatmennyiséget. Ezzel a képességgel nem kell kiépíteni egy átmeneti helyet a kezdeti biztonsági mentési adatai számára. Emellett a segédprogramok nem szükségesek az adatlemezek formázásához és másolásához.

## <a name="supported-platforms"></a>Támogatott platformok

A következő platformok támogatottak:

- Windows Server 2019 64 bit (standard, Datacenter, Essentials)
- Windows Server 2016 64 bit (standard, Datacenter, Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>Biztonsági másolatok méretének és támogatott Data Box SKU-i

A következő Data Box SKU-ket támogatja:

| Biztonsági mentési adatméret (a MARS tömörítése után) kiszolgálónkénti bontásban \* | Támogatott Azure Data Box SKU |
| --- | --- |
| \<= 7,2 TB | [Azure Data Box Disk](../databox/data-box-disk-overview.md) |
| > 7,2 TB és <= 80 TB\*\* | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

\*A tipikus tömörítési arányok 10-20%-ban változnak <br>
\*\*[SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com)Ha várhatóan több mint 80 TB-os kezdeti biztonsági mentési adatra van szüksége egyetlen adatforrás esetében.

> [!IMPORTANT]
> Egyetlen adatforrásból származó kezdeti biztonsági mentési adatoknak egy Azure Data Box vagy Azure Data Box lemezen kell lenniük, és nem oszthatók meg több azonos vagy eltérő SKU-ból származó eszköz között. Azonban előfordulhat, hogy egy Azure Data Box több adatforrásból származó kezdeti biztonsági másolatokat tartalmaz.

## <a name="before-you-begin"></a>Előkészületek

A DPM/MABS-on futó MARS-ügynököt a [legújabb verzióra](https://aka.ms/azurebackup_agent) kell frissíteni (2.0.9171.0 vagy újabb).

Ellenőrizze a következőket:

### <a name="azure-subscription-and-required-permissions"></a>Azure-előfizetés és szükséges engedélyek

- Érvényes Azure-előfizetés.
- Az offline biztonsági mentési szabályzat elvégzésére szolgáló felhasználónak az Azure-előfizetés tulajdonosának kell lennie.
- Az Data Box feladatsornak és a Recovery Services-tárolónak, amelyre az adatoknak kell szerepelniük, ugyanabban az előfizetésben kell elérhetőnek lennie.
    > [!NOTE]
    > Javasoljuk, hogy a cél Storage-fiók és a Recovery Services-tároló ugyanabban a régióban legyen. Ez azonban nem kötelező.

### <a name="order-and-receive-the-data-box-device"></a>A Data Box eszköz megrendelése és fogadása

Az offline biztonsági mentés elindítása előtt győződjön meg arról, hogy a szükséges Data Box eszközök *kézbesítése* folyamatban van. A szükséglethez legmegfelelőbb SKU megrendeléséhez tekintse meg a [biztonsági másolatok mérete és a támogatott Data Box SKU](#backup-data-size-and-supported-data-box-skus) -ket. A Data Box-eszközök megrendeléséhez és fogadásához kövesse az [ebben a cikkben](../databox/data-box-disk-deploy-ordered.md) ismertetett lépéseket.

> [!IMPORTANT]
> Ne válassza a *BlobStorage* lehetőséget a **fiókhoz**. A DPM/MABS-kiszolgálónak olyan fiókra van szüksége, amely támogatja a *BlobStorage* kiválasztásakor nem támogatott lapok blobokat. Válassza a  **Storage v2 (általános célú v2)** lehetőséget **a Azure Data Box** feladathoz tartozó cél Storage-fiók létrehozásakor.

![Az Azure databox beállítása](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>Azure Data Box eszközök beállítása

Miután megkapta a Azure Data Box eszközt a megrendelt Azure Data Box SKU alapján, hajtsa végre az alábbi lépéseket az DPM/MABS-kiszolgáló Data Box eszközeinek beállításához és előkészítéséhez a kezdeti biztonsági mentési adatok azonosításához és átviteléhez.

### <a name="setup-azure-data-box-disk"></a>Azure Data Box lemez beállítása

Ha egy vagy több Azure Data Box lemezt (egyenként 8 TB-ig) rendelt, kövesse az [itt](../databox/data-box-disk-deploy-set-up.md) leírt lépéseket a Data Box lemez kicsomagolásához, összekapcsolásához és feloldásához.

> [!NOTE]
> Lehetséges, hogy a DPM/MABS-kiszolgáló nem rendelkezik USB-porttal. Ilyen esetben összekapcsolhatók a Azure Data Box lemez egy másik kiszolgálóval vagy ügyféllel, és elérhetővé tehetik az eszköz gyökerét hálózati megosztásként.

## <a name="setup-azure-data-box"></a>Telepítő Azure Data Box

Ha egy Azure Data Box (akár 100 TB-ig) rendelt, kövesse az [itt](../databox/data-box-deploy-set-up.md) leírt lépéseket a Data Box beállításához.

### <a name="mount-your-azure-data-box-as-local-system"></a>Azure Data Box csatlakoztatása helyi rendszerszolgáltatásként

A DPM/MABS-kiszolgáló a rendszerkörnyezetben működik, ezért ugyanazt a jogosultsági szintet kell megadni a csatlakoztatási útvonalhoz, ahol a Azure Data Box csatlakoztatva van. Az alábbi lépésekkel biztosíthatja, hogy a Data Box eszközt helyi rendszerként csatlakoztassa az NFS protokoll használatával.

1. Engedélyezze az NFS-ügyfélszolgáltatás funkcióját a DPM/MABS-kiszolgálón.
Alternatív forrás meghatározása: *wim: D: \Sources\Install.wim: 4*
2. Töltse le a **PSExec** -t a rendszerből [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) a DPM/MABS-kiszolgálóra.
3. Nyisson meg egy rendszergazda jogú parancssort, és hajtsa végre a következő parancsot az *PSExec.exet* tartalmazó könyvtárral az aktuális könyvtárként.

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. A fenti parancs eredményeképpen megnyíló parancssori ablak a helyi rendszerkörnyezetben található. Ezen a parancsablakban hajthatja végre az Azure-oldal blob-megosztásának hálózati meghajtóként való csatlakoztatásának lépéseit a Windows-kiszolgálón.
5. Kövesse az alábbi [lépéseket a](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) DPM/MABS-Data Box kiszolgáló NFS-en keresztüli csatlakoztatásához, majd futtassa a következő parancsot a helyi rendszerparancssorban az Azure-oldal Blobok megosztásának csatlakoztatásához:

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. A csatlakoztatás után ellenőrizze, hogy a kiszolgálóról fér-e el az X:. Ha tud, folytassa a cikk következő szakaszával.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>A kezdeti biztonsági mentési adatok átvitele Azure Data Box eszközökre

1. A DPM/MABS-kiszolgálón hajtsa végre az [új védelmi csoport létrehozásának](/system-center/dpm/create-dpm-protection-groups)lépéseit. Ha online védelmet ad a meglévő védelmi csoportnak, kattintson a jobb gombbal a meglévő védelmi csoportra, és válassza az **online védelem hozzáadása** lehetőséget, és kezdje a **8. lépéssel**.
2. A **csoporttagok kiválasztása** lapon adja meg azokat a számítógépeket és forrásokat, amelyekről biztonsági másolatot szeretne készíteni.
3. Az **adatvédelmi módszer kiválasztása** lapon adja meg, hogyan szeretné kezelni a rövid és a hosszú távú biztonsági mentést. Győződjön meg arról, hogy az **online védelem** lehetőséget választotta.

   ![Új védelmi csoport létrehozása](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. A **rövid távú célok kiválasztása** lapon adja meg, hogyan szeretné biztonsági mentést készíteni a lemez rövid távú tárolására.
5. A **lemez kiosztásának áttekintése** lapon tekintse át a védelmi csoport számára lefoglalt tárterület lemezterületét.
6. A **replika-létrehozási módszer kiválasztása** lapon válassza az **automatikusan a hálózaton keresztül lehetőséget.**
7. A **konzisztencia-ellenőrzési beállítások kiválasztása** lapon válassza ki, hogyan szeretné automatizálni a konzisztencia-ellenőrzéseket.
8. Az **online védelmi adatforrások megadása** lapon válassza ki azt a tagot, amely számára engedélyezni szeretné az online védelmet.

    ![Online védelmi adatértékek meghatározása](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. Az **online biztonsági mentési ütemezés megadása** oldalon határozza meg, hogy milyen gyakran történjen növekményes biztonsági mentés az Azure-ba.
10. Az **online adatmegőrzési szabály meghatározása** oldalon megtudhatja, hogyan őrizze meg a napi/heti/havi/éves biztonsági másolatokből létrehozott helyreállítási pontokat az Azure-ban.
11. A varázsló **online replikáció kiválasztása** képernyőjén válassza az **átvitel a Microsoft tulajdonában lévő lemezek használatával** lehetőséget, majd kattintson a **tovább** gombra.

    ![A kezdeti online replikáció kiválasztása](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > A **Microsoft tulajdonában lévő lemezekkel való átvitel** kiválasztásának lehetősége nem érhető el a MABS v3-hez, mivel a funkció előzetes verzióban érhető el. [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com)Ha ezt a funkciót szeretné használni a MABS v3-hez, a következő címen juthat el hozzánk:.

12. Ha a rendszer kéri, jelentkezzen be az Azure-ba, és használja az Azure-előfizetéshez tulajdonosi hozzáféréssel rendelkező felhasználói hitelesítő adatokat. Sikeres bejelentkezés után a következő képernyő jelenik meg:

    ![Sikeres bejelentkezés után](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     A DPM/MABS-kiszolgáló ezután beolvassa az előfizetésben lévő Data Box feladatokat a *szállított* állapotban.

     > [!NOTE]
     > Az első bejelentkezés a szokásosnál hosszabb időt vesz igénybe. A Azure PowerShell modul a háttérben települ, és az Azure AD-alkalmazás is regisztrálva lesz.
     >
     >  - A következő PowerShell-modulok vannak telepítve:<br>
          -AzureRM. profil     *5.8.3*<br>
          -AzureRM. Resources   *6.7.3*<br>
          -AzureRM. Storage     *5.2.0*<br>
          – Azure. Storage       *4.6.1*<br>
     >  - Az Azure AD-alkalmazás *AzureOfflineBackup_ként \<object GUID of the user>* van regisztrálva.

13. Válassza ki a megfelelő adatmező-sorrendet, amelyhez kicsomagolta, csatlakoztatta és zárolta Data Box lemezét. Kattintson a **Tovább** gombra.

    ![Válassza ki a databox](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. A **DataBox észlelése** képernyőn adja meg Data Box eszközének elérési útját, majd válassza az **eszköz észlelése** elemet.

    ![Hálózati elérési út megadása](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > Adja meg a Azure Data Box lemez gyökérkönyvtárának hálózati elérési útját. Ennek a könyvtárnak tartalmaznia kell egy könyvtárat a *PageBlob* néven az alábbi ábrán látható módon:
    >
    > ![USB-meghajtó](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > Ha például a lemez elérési útja, a `\\mydomain\myserver\disk1\` *Disk1* pedig a *PageBlob* nevű KÖNYVTÁRAT tartalmazza, akkor a DPM/MABS-kiszolgáló varázslóban meg kell adni az elérési utat `\\mydomain\myserver\disk1\` .
    > Ha [Azure Data Box 100 TB-os eszközt állít](./offline-backup-azure-data-box.md#set-up-azure-data-box)be, adja meg a következőt az eszköz hálózati elérési útjaként `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` .

15. Kattintson a **Tovább** gombra. Az **Összefoglalás** lapon tekintse át a beállításokat, majd válassza a **csoport létrehozása** lehetőséget.

    ![Databox észlelése](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    A következő képernyő megerősíti, hogy a védelmi csoport létrehozása sikeresen megtörtént.

    ![Védelmi csoport létrehozva](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. Válassza a fenti képernyő **Bezárás** elemét.

    Ezzel az adatai kezdeti replikálása a DPM-/MABS-lemezre történik. Amikor befejezi a védelmet, a csoport állapota **OK** értékként jelenik meg a **védelem** lapon.

17. Az offline biztonsági másolat Azure Data Box eszközre való elindításához kattintson a jobb gombbal a **védelmi csoportra**, majd válassza a **helyreállítási pont létrehozása** lehetőséget. Ezután válassza az **online védelem** lehetőséget.

    ![Helyreállítási pont létrehozása](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![Helyreállítási pont](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   A DPM/MABS-kiszolgáló elindítja a kiválasztott adatAzure Data Box eszközre történő biztonsági mentést. Ez több órát is igénybe vehet, az adatok méretétől és a DPM/MABS-kiszolgáló és a Azure Data Box Disk közötti kapcsolati sebességtől függően.

   A feladatok állapotát a **figyelés** ablaktáblán követheti nyomon. Miután az adatbiztonsági másolat elkészült, megjelenik egy képernyő, amely az alábbihoz hasonló:

   ![Felügyeleti konzol](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>Biztonsági mentés utáni lépések

Az Azure Data Box Disk sikeres biztonsági mentése után kövesse az alábbi lépéseket.

- A Azure Data Box lemez Azure-ba való szállításához kövesse az [ebben a cikkben](../databox/data-box-disk-deploy-picked-up.md) ismertetett lépéseket. Ha Azure Data Box 100-TB eszközt használt, kövesse az [alábbi lépéseket](../databox/data-box-deploy-picked-up.md) a Azure Data Box Azure-ba való szállításához.
- [Figyelje a Data Box feladatot](../databox/data-box-disk-deploy-upload-verify.md) a Azure Portalban. A Azure Data Box-feladatok *befejezése* után a DPM/MABS-kiszolgáló automatikusan áthelyezi az adatait a Storage-fiókból a Recovery Services-tárolóba a következő ütemezett biztonsági mentés időpontjában. Ezután megjelöli a biztonsági mentési feladatot, *Ha a* helyreállítási pont létrehozása sikeresen megtörtént.

  > [!NOTE]
  > A DPM/MABS-kiszolgáló elindítja a biztonsági mentést a védelmi csoport létrehozásakor ütemezett időpontokban. Ezek a feladatok azonban *arra várnak, hogy Azure Data Box feladat befejeződjön* , amíg a feladat befejeződik.

- Miután a DPM/MABS-kiszolgáló sikeresen létrehozta a kezdeti biztonsági mentéshez tartozó helyreállítási pontot, törölheti a Azure Data Box feladatokhoz társított Storage-fiókot (vagy adott tartalmakat).

## <a name="troubleshooting"></a>Hibaelhárítás

A DPM-kiszolgáló Microsoft Azure Backup (Mohácsi) ügynöke létrehoz egy Azure AD-alkalmazást a bérlőben. Az alkalmazáshoz tanúsítvány szükséges a kapcsolat nélküli kitöltési házirend konfigurálásakor létrehozott és feltöltött hitelesítéshez.

A Azure PowerShell a tanúsítvány Azure AD-alkalmazásba való létrehozásához és feltöltéséhez használjuk.

### <a name="issue"></a>Probléma

A kapcsolat nélküli biztonsági mentés konfigurálásakor a Azure PowerShell parancsmag ismert hibakódja miatt nem adhat hozzá több tanúsítványt ugyanahhoz az Azure AD-alkalmazáshoz, amelyet a Mohácsi-ügynök hozott létre. Ez hatással lesz arra, ha egy offline előkészítési szabályzatot konfigurált ugyanahhoz vagy egy másik kiszolgálóhoz.

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Annak ellenőrzése, hogy a problémát az adott alapvető ok okozza-e

A következő lépések egyikének végrehajtásával biztosíthatja, hogy a hiba a fenti [probléma](#issue) miatt megtörténjen:

#### <a name="step-1"></a>1. lépés

Ellenőrizze, hogy a következő hibaüzenet jelenik-e meg a DPM/MABS-konzolon az offline biztonsági mentés konfigurálásakor:

![Azure Recovery Services-ügynök](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>2. lépés

1. Nyissa meg a **temp** mappát a telepítési útvonalon (alapértelmezett ideiglenes mappa elérési útja: *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Keresse meg a *CBUICurr* fájlt, és nyissa meg a fájlt.
2. A *CBUICurr* fájlban görgessen az utolsó sorra, és ellenőrizze, hogy a hiba oka "nem sikerült létrehozni az Azure ad-alkalmazás hitelesítő adatait az ügyfél fiókjában. Kivétel: a KeyId-mel való meglévő hitelesítő adatok frissítése \<some guid> nem engedélyezett.

### <a name="workaround"></a>Áthidaló megoldás

A probléma megoldásához végezze el a következő lépéseket, majd próbálja megismételni a házirend-konfigurációt.

1. Jelentkezzen be az Azure bejelentkezési oldalára, amely a DPM/MABS-kiszolgáló felhasználói felületén jelenik meg egy olyan fiókkal, amely rendszergazdai hozzáféréssel rendelkezik az előfizetéshez, amelyhez a Data Box-feladatot létrehozta.
2. Ha egy másik kiszolgáló nem rendelkezik kapcsolat nélküli előkészítéssel, és nem függ az `AzureOfflineBackup_<Azure User Id>` alkalmazástól, akkor törölje az alkalmazást **Azure Portal > Azure Active Directory > Alkalmazásregisztrációk**.

   > [!NOTE]
   > Ellenőrizze, hogy az alkalmazás `AzureOfflineBackup_<Azure User Id>` nem rendelkezik-e más kapcsolat nélküli előkészítéssel, és nem függ-e az alkalmazástól. Lépjen a **beállítások > kulcsok** elemre a nyilvános kulcsok szakaszban. Nem adhat hozzá további **nyilvános kulcsokat** . Tekintse meg a következő képernyőképet a hivatkozáshoz:
   >
   > ![Nyilvános kulcsok](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>3. lépés

A DPM/MABS-kiszolgálóról, amelyen az offline biztonsági mentést kívánja konfigurálni, hajtsa végre a következő műveleteket:

1. Nyissa meg a **számítógép-tanúsítvány alkalmazása**  >  **személyes** kezelése lapot, és keresse meg a nevet tartalmazó tanúsítványt `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
2. Válassza ki a fenti tanúsítványt, kattintson a jobb gombbal a **minden feladat** elemre, és exportálja a titkos kulcs nélküli **exportálást** . cer formátumban.
3. Nyissa meg a **2. pontban** említett Azure offline Backup alkalmazást. A **Beállítások**  >  **kulcsban**  >  **töltse fel a nyilvános kulcsot,** töltse fel a fenti lépésben exportált tanúsítványt.

   ![Nyilvános kulcsok feltöltése](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. A-kiszolgálón nyissa meg a beállításjegyzéket úgy, hogy beírja a **Regedit parancsot** a **Futtatás** ablakba.
5. Nyissa meg a beállításjegyzék *Computer\HKEY \_ helyi \_ MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*. Kattintson a jobb gombbal a **CloudBackupProvider** elemre, és adjon hozzá egy új karakterlánc-értéket a névvel `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Az Azure User ID beszerzéséhez hajtsa végre az alábbi műveletek egyikét:
    >
    >- Az Azure-hoz csatlakoztatott PowerShellben futtassa a `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` parancsot.
    > - Navigáljon az CurrentUserId nevű beállításjegyzékbeli elérési útra `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` . 

6. Kattintson a jobb gombbal a fenti lépésben hozzáadott sztringre, majd válassza a **módosítás** lehetőséget. Az érték mezőben adja meg a **2. pontban** exportált tanúsítvány ujjlenyomatát, és kattintson az **OK gombra**.
7. Az ujjlenyomat értékének lekéréséhez kattintson duplán a tanúsítványra, majd válassza a **részletek**  elemet, és görgessen lefelé, amíg meg nem jelenik az ujjlenyomat mező. Válassza ki az **ujjlenyomatot** , és másolja az értéket.

   ![Ujjlenyomat értéke](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>Következő lépések

- [Offline előkészítés saját lemez használatával (az Azure import/export szolgáltatás használatával)](backup-azure-backup-server-import-export.md)
