---
title: Azure Data Box/Azure Data Box Heavy felügyelete helyi webes felhasználói felület használatával
description: Ismerteti, hogyan használható a helyi webes KEZELŐFELÜLET a Data Box és Data Box Heavy eszközök felügyeletéhez
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: e84f39fa5b9245d1874e60d651156e99c0885040
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97678925"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box-and-data-box-heavy"></a>A helyi webes felhasználói felület használata a Data Box és Data Box Heavy felügyeletéhez

Ez a cikk a Data Box és Data Box Heavy eszközökön végrehajtott konfigurációs és felügyeleti feladatokat ismerteti. A Data Box és Data Box Heavy eszközöket a Azure Portal felhasználói felületen és az eszköz helyi webes FELÜLETén keresztül kezelheti. Ez a cikk a helyi webes felhasználói felület használatával végrehajtott feladatokra összpontosít.

A Data Box és a Data Box Heavy helyi webes FELÜLETe az eszköz kezdeti konfigurálására szolgál. A helyi webes felhasználói felület segítségével leállíthatja vagy újraindíthatja az eszközt, diagnosztikai teszteket futtathat, szoftvereket frissíthet, megtekintheti a másolási naplókat, törölheti a helyi adatait az eszközről, és létrehozhat egy támogatási csomagot a Microsoft ügyfélszolgálatahoz. Két független csomóponttal rendelkező Data Box Heavy eszközön két különálló helyi webes felület érhető el, amely az eszköz egyes csomópontjainak felel meg.

## <a name="generate-support-package"></a>Támogatási csomag létrehozása

Ha eszközproblémákat tapasztal, létrehozhat egy támogatási csomagot a rendszernaplókból. A Microsoft támogatási szolgálata a csomag segítségével hárítja el a problémát.

Támogatási csomag létrehozásához hajtsa végre a következő lépéseket:

1. A helyi webes KEZELŐFELÜLETen lépjen a **Kapcsolatfelvétel a támogatási szolgálatba**. Ha szeretné, jelölje be a memóriaképek **belefoglalása** jelölőnégyzetet. Ezután válassza a **támogatási csomag létrehozása** lehetőséget.

    A memóriakép az eszköz memóriájának tartalma, rendszerhiba után mentve.

    Ne jelölje be a **memóriaképek belefoglalása** beállítást, ha a támogatás nem kér ilyet. A rendszer hosszú időt vesz igénybe, hogy olyan támogatási csomagot gyűjtsön, amely tartalmazza a memóriaképeket, valamint a bizalmas adatokat is.

    ![Támogatási csomag létrehozása – 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

    A rendszer összeállítja a támogatási csomagot. A művelet eltarthat néhány percig, ha csak rendszernaplókat tartalmaz. Ha memóriaképeket is tartalmaz, a rendszer sok időt vesz igénybe.

    ![Támogatási csomag létrehozása – 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

2. Ha a támogatási csomag létrehozása befejeződött, válassza a **támogatási csomag letöltése** lehetőséget.

    ![3. támogatási csomag létrehozása](media/data-box-local-web-ui-admin/create-support-package-3.png)

3. Tallózással válassza ki a letöltés helyét. Nyissa meg a mappát a tartalmak megtekintéséhez.

    ![Támogatási csomag létrehozása – 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

## <a name="erase-local-data-from-your-device"></a>Helyi adatok törlése az eszközről

A helyi webes felhasználói felület használatával törölheti az eszköz helyi adatait, mielőtt visszaadná azt az Azure-adatközpontba.

> [!IMPORTANT]
> Az adattörlés nem vonható vissza. Mielőtt törli az eszközről a helyi adatok törlését, készítsen biztonsági mentést a fájlokról.

Az eszközön található helyi adatok törléséhez hajtsa végre az alábbi lépéseket:

1. A helyi webes KEZELŐFELÜLETen lépjen az **adattörlés elemre**.
2. Adja meg az eszköz jelszavát, és válassza az **adattörlés** lehetőséget.

    ![Eszköz adattörlési beállítása](media/data-box-local-web-ui-admin/erase-local-data-1.png)

3. A megerősítő üzenetben kattintson az **Igen** gombra a folytatáshoz. Az adattörlés 50 percnél hosszabb időt is igénybe vehet.

   Mielőtt törli az eszközről, készítsen biztonsági másolatot a helyi adatokról. Az adattörlés nem vonható vissza.

    ![Adat-törlés megerősítésének kérése](media/data-box-local-web-ui-admin/erase-local-data-2.png)

## <a name="shut-down-or-restart-your-device"></a>Az eszköz leállítása és újraindítása

Az eszközt a helyi webes felhasználói felület használatával állíthatja le vagy indíthatja újra. Azt javasoljuk, hogy az újraindítás előtt a megosztások offline állapotba kerülnek a gazdagépen, majd az eszközön. Így a lehető legkisebbre csökkentheti az adatsérülés lehetőségét. Az eszköz leállításakor ügyeljen arra, hogy az Adatmásolás ne legyen folyamatban.

Az eszköz leállításához hajtsa végre az alábbi lépéseket.

1. A helyi webes felhasználói felületen lépjen a **Shut down or restart** (Leállítás vagy újraindítás) lapra.

2. Válassza a **Leállítás** lehetőséget.

    ![A Data Box leállítása – 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. Ha a rendszer megerősítést kér, kattintson **az OK gombra** a folytatáshoz.

    ![A Data Box leállítása – 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

Az eszköz leállítását követően az előlapon található bekapcsoló gombbal kapcsolhatja be az eszközt.

A Data Box újraindításához hajtsa végre az alábbi lépéseket.

1. A helyi webes felhasználói felületen lépjen a **Shut down or restart** (Leállítás vagy újraindítás) lapra.
2. Válassza az **Újraindítás** lehetőséget.

    ![A Data Box újraindítása – 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. Ha a rendszer megerősítést kér, kattintson **az OK gombra** a folytatáshoz.

   Az eszköz leáll, majd újraindul.

## <a name="download-bom-or-manifest-files"></a>ANYAGJEGYZÉK-vagy manifest-fájlok letöltése

Az AJ vagy a manifest-fájlok tartalmazzák a Data Box vagy Data Box Heavyba másolt fájlok listáját. Ezeket a fájlokat a rendszer importálási sorrendben hozza létre, amikor előkészíti az eszközt a szállításra.

Mielőtt elkezdené, kövesse az alábbi lépéseket az importálási sorrendhez tartozó AJ-vagy manifest-fájlok letöltéséhez:

1. Nyissa meg az eszköz helyi webes FELÜLETét. Ellenőrizze, hogy az eszköz befejezte-e a **szállításra való előkészítés** lépést. Az eszköz előkészítésének befejezése után az eszköz állapota a **szállításra kész** állapotban jelenik meg.

    ![Az eszköz szállításra kész](media/data-box-local-web-ui-admin/prepare-to-ship-3.png)

2. Válassza a **fájlok listájának letöltése** lehetőséget a Data Box másolt fájlok listájának letöltéséhez.

    <!-- ![Select Download list of files](media/data-box-portal-admin/download-list-of-files.png) -->

3. A Fájlkezelőben a rendszer az eszközhöz való kapcsolódáshoz használt protokolltól és a használt Azure Storage-típustól függően külön listát hoz létre a fájlokról.

    <!-- ![Files for storage type and connection protocol](media/data-box-portal-admin/files-storage-connection-type.png) -->
    ![Tárolási típus és kapcsolati protokoll fájljai](media/data-box-local-web-ui-admin/prepare-to-ship-5.png)

   Az alábbi táblázat a fájlneveket az Azure Storage-típusra és a használt kapcsolati protokollra képezi le.

    |Fájlnév  |Azure Storage-típus  |Használt kapcsolati protokoll |
    |---------|---------|---------|
    |utSAC1_202006051000_BlockBlob-BOM.txt     |Blokkblobok         |SMB/NFS         |
    |utSAC1_202006051000_PageBlob-BOM.txt     |Lapblobok         |SMB/NFS         |
    |utSAC1_202006051000_AzFile-BOM.txt    |Azure Files         |SMB/NFS         |
    |utsac1_PageBlock_Rest-BOM.txt     |Lapblobok         |REST        |
    |utsac1_BlockBlock_Rest-BOM.txt    |Blokkblobok         |REST         |

Ezt a listát használhatja az Azure Storage-fiókba feltöltött fájlok ellenőrzéséhez, miután a Data Box visszatér az Azure-adatközpontba. Alább látható egy minta manifest-fájl.

> [!NOTE]
> Data Box Heavy a fájlok (AJ-fájlok) két csoportja szerepel az eszköz két csomópontjának megfelelően.

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

Ez a fájl tartalmazza a Data Boxre vagy Data Box Heavyra másolt fájlok listáját. Ebben a fájlban a *crc64* érték a megfelelő fájlhoz generált ellenőrzőösszeggel kapcsolódik.

## <a name="view-available-capacity-of-the-device"></a>Az eszköz rendelkezésre álló kapacitásának megtekintése

Az eszköz irányítópultján tekintheti meg az eszköz rendelkezésre álló és felhasznált kapacitását.

1. A helyi webes felhasználói felületen lépjen a **View dashboard** (Irányítópult megtekintése) lapra.
2. A **Connect and copy** (Kapcsolódás és másolás) részen az eszköz szabad és felhasznált területe látható.

    ![Rendelkezésre álló kapacitás megtekintése](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)

## <a name="skip-checksum-validation"></a>Ellenőrzőösszeg-érvényesítés kihagyása

A szállításra való felkészüléskor a rendszer alapértelmezés szerint ellenőrzőösszegeket hoz létre az adataihoz. Bizonyos ritka esetekben az adattípustól (kis fájlmérettől függően) a teljesítmény lassú lehet. Ilyen esetben kihagyhatja az ellenőrzőösszeg létrehozását.

Az ellenőrzőösszeg kiszámítása a szállítás előkészítése során csak importálási rendelésekhez, és nem exportálási rendelésekhez történik.

Azt javasoljuk, hogy ne tiltsa le az ellenőrzőösszeget, kivéve, ha a teljesítmény súlyosan érintett.

1. Az eszköz helyi webes FELÜLETének jobb felső sarkában válassza a **Beállítások lehetőséget**.

    ![Ellenőrzőösszeg letiltása](media/data-box-local-web-ui-admin/disable-checksum.png)

2. Tiltsa le az ellenőrzőösszeg-érvényesítést a **Disable** (Letiltás) paranccsal.
3. Kattintson az **Alkalmaz** gombra.

> [!NOTE]
> Az ellenőrzőösszeg-számítás kihagyása beállítás csak akkor érhető el, ha a Azure Data Box fel van oldva. Ez a beállítás nem jelenik meg, ha az eszköz zárolva van.

## <a name="enable-smb-signing"></a>SMB-aláírás engedélyezése

A kiszolgáló-üzenetblokk (SMB) aláírása olyan szolgáltatás, amelyen keresztül az SMB-t használó kommunikáció digitálisan aláírható a csomagok szintjén. Ez az aláírás megakadályozza, hogy a támadások az SMB-csomagokat módosítsák az átvitel során.

Az SMB-aláírással kapcsolatos további információkért lásd: [a kiszolgáló-üzenetblokk aláírásának áttekintése](https://support.microsoft.com/help/887429/overview-of-server-message-block-signing).

Az SMB-aláírás engedélyezése az Azure-eszközön:

1. Az eszköz helyi webes FELÜLETének jobb felső sarkában válassza a **Beállítások** lehetőséget.

    ![Beállítások megnyitása](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Engedélyezés** SMB-aláírás.

    ![SMB-aláírás engedélyezése](media/data-box-local-web-ui-admin/data-box-smb-signing-1.png)

3. Kattintson az **Alkalmaz** gombra.
4. A helyi webes felhasználói felületen lépjen a **Shut down or restart** (Leállítás vagy újraindítás) lapra.
5. Válassza az **Újraindítás** lehetőséget.

## <a name="enable-backup-operator-privileges"></a>Biztonsági mentési operátori jogosultságok engedélyezése

A webes felhasználói felület felhasználói biztonsági mentési operátori jogosultságokkal rendelkeznek az SMB-megosztásokon alapértelmezés szerint. Ha ezt nem szeretné, a jogosultságok letiltásához vagy engedélyezéséhez használja az **operátori jogosultságok engedélyezése** lehetőséget.

További információ: Backup Operators in [Active Directory Security groups](/windows/security/identity-protection/access-control/active-directory-security-groups#backup-operators).

Biztonsági mentési operátori jogosultságok engedélyezése az Azure-eszközön:

1. Az eszköz helyi webes FELÜLETének jobb felső sarkában válassza a **Beállítások** lehetőséget.

   ![Data Box beállítások megnyitása – 1](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Engedélyezés** Biztonsági mentési operátori jogosultságok.

   ![Biztonsági mentési operátori jogosultságok engedélyezése](media/data-box-local-web-ui-admin/data-box-backup-operator-privileges-1.png)

3. **Válassza az alkalmaz lehetőséget**.
4. A helyi webes felhasználói felületen lépjen a **Shut down or restart** (Leállítás vagy újraindítás) lapra.
5. Válassza az **Újraindítás** lehetőséget.

## <a name="enable-acls-for-azure-files"></a>ACL-ek engedélyezése Azure Fileshoz

A fájlok metaadatait a rendszer alapértelmezés szerint átadja, amikor a felhasználók SMB-n keresztül töltenek fel adatokat a Data Box. A metaadatok közé tartoznak a hozzáférés-vezérlési listák (ACL-ek), a fájlattribútumok és az időbélyegek. Ha ezt nem szeretné, a funkció letiltásához vagy engedélyezéséhez használjon **ACL-eket az Azure Files** szolgáltatáshoz.

<!--For more information about metadata that is transferred, see [Preserving the ACLs and metadata with Azure Data Box](./data-box-local-web-ui-admin.md#enable-backup-operator-privileges) - IN DEVELOPMENT-->

> [!Note]
> A metaadatok fájlokkal történő átviteléhez biztonságimásolat-felelősnek kell lennie. Ha ezt a szolgáltatást használja, ügyeljen arra, hogy a webes felhasználói felület helyi felhasználói a biztonságimásolat-felelősök legyenek. Lásd: a [biztonságimásolat-kezelő jogosultságának engedélyezése](#enable-backup-operator-privileges).

Az ACL-ek átvitelének engedélyezése az Azure Files szolgáltatásban:

1. Az eszköz helyi webes FELÜLETének jobb felső sarkában válassza a **Beállítások** lehetőséget.

    ![Data Box beállítások megnyitása – 2](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Engedélyezés** Az Azure Files hozzáférés-vezérlési listái.

     ![ACL-ek engedélyezése az Azure Files szolgáltatáshoz](media/data-box-local-web-ui-admin/data-box-acls-for-azure-files-1.png)
  
3. Kattintson az **Alkalmaz** gombra.
4. A helyi webes felhasználói felületen lépjen a **Shut down or restart** (Leállítás vagy újraindítás) lapra.
5. Válassza az **Újraindítás** lehetőséget.

## <a name="enable-tls-11"></a>TLS 1,1 engedélyezése

Alapértelmezés szerint a Azure Data Box a Transport Layer Security (TLS) 1,2-et használja a titkosításhoz, mert az biztonságosabb, mint a TSL 1,1. Ha azonban Ön vagy az ügyfelei egy böngészővel férnek hozzá a TLS 1,2-et nem támogató adataihoz, akkor engedélyezheti a TLS 1,1 használatát.

A TLS-vel kapcsolatos további információkért lásd: [Azure Data Box Gateway biztonság](../databox-gateway/data-box-gateway-security.md).

A TLS 1,1 engedélyezése az Azure-eszközön:

1. Az eszköz helyi webes FELÜLETének jobb felső sarkában válassza a **Beállítások** lehetőséget.

    ![Data Box beállítások megnyitása – 3](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. **Engedélyezés** TLS 1,1.

    ![TLS 1,1 engedélyezése](media/data-box-local-web-ui-admin/data-box-tls-1-1.png)

3. Kattintson az **Alkalmaz** gombra.
4. A helyi webes felhasználói felületen lépjen a **Shut down or restart** (Leállítás vagy újraindítás) lapra.
5. Válassza az **Újraindítás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [kezelheti a Data Box és Data Box Heavy a Azure Portalon keresztül](data-box-portal-admin.md).
