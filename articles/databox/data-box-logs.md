---
title: Azure Data Box nyomon követése és naplózása, Azure Data Box Heavy az importálási sorrendtel kapcsolatos események | Microsoft Docs
description: Ismerteti, hogyan lehet nyomon követni és naplózni az eseményeket a Azure Data Box különböző szakaszaiban, és Azure Data Box Heavy importálási sorrendet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: a9304936f746b82b59550d62e8b60a9e0035d188
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147939"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-import-order"></a>A Azure Data Box nyomon követése és eseménynaplózása, valamint Azure Data Box Heavy importálási sorrend

A Data Box vagy Data Box Heavy importálási sorrend a következő lépésekből áll: megrendelés, beállítás, adatok másolása, visszaadása, feltöltés az Azure-ba, ellenőrzés és adatok törlése. A sorrend egyes lépéseinek megfelelően több műveletet is végrehajthat a rendeléshez való hozzáférés szabályozásához, az események naplózásához, a sorrend nyomon követéséhez és a létrehozott naplók értelmezéséhez.

Az alábbi táblázat a Data Box vagy Data Box Heavy importálási sorrend lépéseinek összegzését, valamint az egyes lépések során a sorrend nyomon követésére és naplózására szolgáló eszközöket tartalmazza.

| Data Box importálási sorrend szakasza       | Nyomon követhető és naplózható eszköz                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Rendelés létrehozása               | [A megrendelés hozzáférés-vezérlésének beállítása az Azure RBAC](#set-up-access-control-on-the-order)                                                    |
| Megrendelés feldolgozva            | [A sorrend nyomon követése](#track-the-order) <ul><li> Azure Portal </li><li> Szállítási szolgáltató webhelye </li><li>E-mail-értesítések</ul> |
| Eszköz beállítása              | Az eszköz hitelesítő adatai naplózása a [tevékenység naplófájljaiban](#query-activity-logs-during-setup)                                              |
| Adatmásolás az eszközre        | Adatmásolási [ *error.xml* fájlok megtekintése](#view-error-log-during-data-copy)                                                             |
| A szállítás előkészítése            | Az [Anyagjegyzék-fájlok](#inspect-bom-during-prepare-to-ship) vagy a jegyzékfájlok vizsgálata az eszközön                                      |
| Adatok feltöltése az Azure-ba       | A hibák [másolási naplóinak áttekintése](#review-copy-log-during-upload-to-azure) az adatok feltöltésekor az Azure-adatközpontban                         |
| Adatok törlése az eszközről   | [Felügyeleti naplók láncának megtekintése](#get-chain-of-custody-logs-after-data-erasure) , beleértve a naplókat és a sorrendi előzményeket                |

Ez a cikk részletesen ismerteti a Data Box vagy Data Box Heavy importálási sorrend nyomon követéséhez és naplózásához rendelkezésre álló különböző mechanizmusokat vagy eszközöket. A cikkben található információk mindkét, Data Box és Data Box Heavy importálási rendelésre vonatkoznak. A következő szakaszban a Data Boxra való hivatkozás a Data Box Heavyra is vonatkozik.

## <a name="set-up-access-control-on-the-order"></a>Hozzáférés-vezérlés beállítása a rendeléshez

Megadhatja, hogy ki férhet hozzá a rendeléshez a rendelés első létrehozásakor. Állítsa be az Azure-szerepköröket különböző hatókörökön a Data Boxi sorrend elérésének szabályozásához. Az Azure-szerepkörök határozzák meg a hozzáférés típusát – írható és olvasható, írásvédett, írás és olvasás a műveletek egy részhalmazára.

A Azure Data Box szolgáltatáshoz definiálható két szerepkör a következő:

- **Data Box-olvasó** – a hatókörben megadott rendelés (ek) hez csak olvasási hozzáférése van. Csak egy megrendelés részleteit tekinthetik meg. Nem férhetnek hozzá a Storage-fiókokkal kapcsolatos egyéb adatokhoz, vagy szerkesztheti a rendelés részleteit, például a címeket és így tovább.
- **Data Box közreműködő** – csak olyan megrendelés hozható létre, amely az adatok átvitelét egy adott Storage-fiókba, *Ha már rendelkezik írási hozzáféréssel egy Storage-fiókhoz*. Ha nem fér hozzá egy Storage-fiókhoz, akkor sem hozhatnak létre Data Box, hogy az Adatmásolást a fiókba. Ez a szerepkör nem határozza meg a Storage-fiókhoz kapcsolódó engedélyeket, és nem biztosít hozzáférést a Storage-fiókokhoz.  

Egy megrendelés elérésének korlátozásához a következőket teheti:

- Rendeljen hozzá egy szerepkört egy rendelési szinten. A felhasználónak csak a szerepkörök által meghatározott engedélyekkel kell megfelelnie az adott Data Box sorrendtel való interakcióhoz, és semmi más nem.
- Rendeljen hozzá egy szerepkört az erőforráscsoport szintjén, a felhasználó hozzáfér az adott erőforráscsoporthoz tartozó összes Data Box-rendeléshez.

További információ az Azure-RBAC használatáról: ajánlott [eljárások az Azure RBAC](../role-based-access-control/best-practices.md).

## <a name="track-the-order"></a>A megrendelés nyomon követése

A megrendelés nyomon követhető a Azure Portal és a szállítási szolgáltató webhelyén. A következő mechanizmusok állnak rendelkezésre a Data Box sorrendjének nyomon követéséhez:

- Ha nyomon szeretné követni a sorrendet, amikor az eszköz az Azure-adatközpontban vagy az Ön telephelyén található, lépjen a **Data Box order > áttekintés** Azure Portal.

    ![Megrendelés állapotának és követésének megtekintése](media/data-box-logs/overview-view-status-1.png)

- Ha nyomon szeretné követni a sorrendet az eszköz továbbítása közben, lépjen a regionális szolgáltató webhelyére, például az Egyesült államokbeli UPS webhelyre. Adja meg a rendeléshez társított követési számot.
- Data Box e-mail-értesítéseket is küld, amikor a megrendelés állapota megváltozik a rendelés létrehozásakor megadott e-mailek alapján. A Data Box rendelés állapotának listáját a [sorrend állapotának megtekintése](data-box-portal-admin.md#view-order-status)című részben tekintheti meg. A rendeléshez társított értesítési beállítások módosításához tekintse meg az [értesítési adatok szerkesztése](data-box-portal-admin.md#edit-notification-details)című részt.

## <a name="query-activity-logs-during-setup"></a>Lekérdezési tevékenység naplófájljai a telepítés során

- A Data Box zárolt állapotban érkezik a helyszínen. A megrendeléséhez a Azure Portal elérhető hitelesítő adatok használhatók.  

    Egy Data Box beállításakor előfordulhat, hogy tudnia kell, hogy ki fér hozzá az eszköz hitelesítő adataihoz. Annak megállapításához, hogy ki fér hozzá az **eszköz hitelesítő adatai** panelhez, lekérdezheti a tevékenység naplóit.  Minden olyan művelet, amely magában foglalja az **eszköz adatainak elérését > a hitelesítő adatok panelje** műveletként van bejelentkezve a tevékenység naplóiba `ListCredentials` .

    ![Tevékenységnaplók lekérdezése](media/data-box-logs/query-activity-log-1.png)

- A rendszer minden bejelentkezést valós időben naplóz a Data Boxba. Ezek az információk azonban [csak a sorrend sikeres befejezése után érhetők](#audit-logs) el a naplókban.

## <a name="view-error-log-during-data-copy"></a>Hibanapló megtekintése az Adatmásolás során

Az Adatmásolás során Data Box vagy Data Box Heavy a rendszer hibaüzenetet küld, ha az Adatmásolás során problémák merülnek fel.

### <a name="errorxml-file"></a>Error.xml fájl

Győződjön meg arról, hogy a másolási feladatok végrehajtása hibák nélkül befejeződött. Ha a másolási folyamat során hibák léptek fel, töltse le a naplókat a **csatlakozás és másolás** lapról.

- Ha olyan fájlt másolt, amely nem 512 bájtra van igazítva a Data Box felügyelt lemez mappájába, a fájl nem lesz feltöltve az átmeneti tárolási fiókba. Hibaüzenet jelenik meg a naplókban. Távolítsa el a fájlt, és másolja a 512 bájtra igazított fájlt.
- Ha másolt egy VHDX, vagy egy dinamikus VHD-t vagy egy különbséglemezek VHD-t (ezek a fájlok nem támogatottak), akkor hibaüzenet jelenik meg a naplókban.

Íme egy minta a *error.xml* a különböző hibákhoz a felügyelt lemezekre történő másolás során.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Íme egy példa a *error.xml* a különböző hibákra, amikor az oldal blobokra másol.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Íme egy minta a *error.xml* a különböző hibákhoz, amikor a másoláskor blokkolja a blobokat.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Íme egy példa a *error.xml* a különböző hibákra, amikor Azure Filesre másol.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

A fenti esetek mindegyikében hárítsa el a hibákat, mielőtt továbblépne a következő lépésre. Az adatmásolások SMB-vagy NFS-protokollon keresztüli Data Box során kapott hibákról további információt a [Data Box és a Data Box Heavy problémák elhárítása](data-box-troubleshoot.md)című témakörben talál. További információ az Adatmásolás során a REST-Data Box során kapott hibákról: [Data Box blob Storage-problémák elhárítása](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>ANYAGJEGYZÉK vizsgálata a szállítás előkészítése során

A szállítás előkészítése során létrejön az anyagjegyzék (AJ) vagy a manifest-fájl néven ismert fájlok listája.

- Ezzel a fájllal ellenőrizheti a tényleges neveket és a Data Boxba másolt fájlok számát.
- Ezt a fájlt használva ellenőrizheti a fájlok tényleges méretét.
- Győződjön meg arról, hogy a *crc64* nem nulla karakterláncnak felel meg. <!--A null value for crc64 indicates that there was a reparse point error)-->

A szállítás előkészítése során kapott hibákról a [Data Box és Data Box Heavy problémák elhárítása](data-box-troubleshoot.md)című témakörben olvashat bővebben.

### <a name="bom-or-manifest-file"></a>AJ-vagy manifest-fájl

Az AJ vagy a manifest fájl tartalmazza a Data Box eszközre másolt fájlok listáját. Az AJ-fájl fájlnevei és a hozzájuk tartozó méretek, valamint az ellenőrzőösszeg. A rendszer külön AJ-fájlt hoz létre a blokk Blobok, a Blobok, a Azure Files, a REST API-k használatával történő másoláshoz, valamint a Data Box felügyelt lemezekre történő másolásához. Az ANYAGJEGYZÉK-fájlokat letöltheti az eszköz helyi webes KEZELŐFELÜLETéről a felkészülés a szállításra lehetőségre.

Ezek a fájlok a Data Box eszközön is találhatók, és az Azure-adatközpontban a társított Storage-fiókba lesznek feltöltve.

### <a name="bom-file-format"></a>AJ-fájl formátuma

Az AJ-vagy manifest-fájl formátuma a következő:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Az alábbi példa egy olyan jegyzékfájlt mutat be, amely akkor jön létre, amikor a rendszer átmásolta az adatmennyiséget a Data Box a blob megosztására.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

Az AJ-vagy MANIFEST-fájlokat a rendszer az Azure Storage-fiókba is másolja. Az AJ vagy a manifest fájlok segítségével ellenőrizheti, hogy az Azure-ba feltöltött fájlok megfelelnek-e a Data Boxba másolt adatfájloknak.

## <a name="review-copy-log-during-upload-to-azure"></a>A másolási napló áttekintése az Azure-ba való feltöltés során

Az Azure-ba történő adatfeltöltés során létrejön egy másolási napló.

### <a name="copy-log"></a>Napló másolása

A Data Box szolgáltatás minden feldolgozott rendeléshez létrehoz egy másolási naplót a társított Storage-fiókban. A másolási naplóban szerepel a feltöltött fájlok teljes száma, valamint a Data Boxról az Azure Storage-fiókba való Adatmásolás során hibás fájlok száma.

Az Azure-ba való feltöltés során ciklikus redundancia-ellenőrzési (CRC) számítás történik. A FCSF az adatmásolatból és az adatok feltöltése után összehasonlítjuk. A CRC-eltérés azt jelzi, hogy a megfelelő fájlokat nem sikerült feltölteni.

Alapértelmezés szerint a rendszer a naplókat egy nevű tárolóba írja `copylog` . A naplók tárolása a következő elnevezési konvencióval történik:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

A másolási napló elérési útja a portál **Áttekintés** paneljén is megjelenik.

![Elérési út a másolási napló áttekintő paneljén, ha elkészült](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>A feltöltés sikeresen befejeződött 

A következő minta a másolási napló általános formátumát írja le egy Data Box feltöltéshez, amely sikeresen befejeződött:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>A feltöltés hibákkal fejeződött be 

A feltöltés az Azure-ba hibákkal is elvégezhető.

![Elérési út a másolási napló áttekintő paneljén a hibákkal fejeződött be](media/data-box-logs/copy-log-path-2.png)

Íme egy példa arra a másolási naplóra, amelyben a feltöltés hibákkal fejeződött be:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
### <a name="upload-completed-with-warnings"></a>A feltöltés figyelmeztetésekkel fejeződött be

Az Azure-ba való feltöltés figyelmeztetésekkel fejeződött be, ha az adatok olyan tároló/blob/fájlnévvel rendelkeznek, amelyek nem feleltek meg az Azure elnevezési konvencióinak, és a nevek módosultak az adatok Azure-ba való feltöltéséhez.

![Elérési út a másolási napló áttekintés paneljén, ha figyelmeztetésekkel fejeződött be](media/data-box-logs/copy-log-path-3.png)

Íme egy példa arra a másolási naplóra, amelyben az Azure elnevezési konvenciókkal nem egyező tárolók át lettek nevezve az Azure-ba történő adatfeltöltés során.

A tárolók új egyedi nevei a formátum `DataBox-GUID` , a tárolók pedig az új átnevezve tárolóba kerülnek. A másolási napló megadja a régi és az új tároló nevét a tárolóhoz.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Íme egy példa arra a másolási naplóra, amelyben az Azure elnevezési konvenciókkal nem egyező Blobok vagy fájlok át lettek nevezve az Azure-ba történő adatfeltöltés során. Az új blob-vagy fájlneveket a rendszer a tároló relatív elérési útjának SHA256-kivonatára konvertálja, és a célhely típusa alapján feltölti az elérési útra. A cél lehet a Blobok, a Blobok vagy a Azure Files blokkolása.

A a `copylog` régi és az új blob vagy fájl nevét és elérési útját adja meg az Azure-ban.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Adattörlési lánc beolvasása az adattörlés után

Miután az adatok törlődnek a Data Box lemezekről a NIST SP 800-88 1. verziójának útmutatása szerint, a felügyeleti naplók lánca elérhető. Ezek a naplók tartalmazzák a naplókat és a megrendelési előzményeket. A rendszer a naplókat is másolja az AJ-vagy manifest-fájlokba.

### <a name="audit-logs"></a>Naplók

A naplók az Azure-adatközponton kívüli Data Box vagy Data Box Heavy megosztásának bekapcsolásával és elérésével kapcsolatos információkat tartalmaznak. Ezek a naplók a következő helyeken találhatók: `storage-account/azuredatabox-chainofcustodylogs`

Íme egy példa a naplóból a Data Box:

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Rendelés előzményeinek letöltése

A rendelési előzmények a Azure Portalban érhetők el. Ha a megrendelés befejeződött, és az eszköz tisztítása (a lemezekről történő adattörlés) befejeződött, lépjen az eszköz sorrendjébe, és navigáljon a **Rendelés részletei** elemre. Elérhető a **Rendelés előzményeinek letöltése** lehetőség. További információ: a [rendelési előzmények letöltése](data-box-portal-admin.md#download-order-history).

Ha a sorrendet az előzmények között görgeti, a következőt látja:

- Az eszköz hordozófrekvencia-követési adatai.
- Események *SecureErase* -tevékenységgel. Ezek az események a lemezen tárolt adattörlésnek felelnek meg.
- Data Box a napló hivatkozásait. A naplók elérési *útjait, a* *másolási naplókat* és az *Anyagjegyzék* -fájlokat mutatjuk be.

Az alábbi példa az Azure Portal:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogy miként lehet [elhárítani a Data Box és Data Box Heavy kapcsolatos problémákat](data-box-troubleshoot.md).
