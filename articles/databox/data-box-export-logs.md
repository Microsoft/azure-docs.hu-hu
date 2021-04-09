---
title: Azure Data Box nyomon követése és naplózása, Azure Data Box Heavy az exportálási sorrendtel kapcsolatos események | Microsoft Docs
description: Ismerteti, hogyan lehet nyomon követni és naplózni az eseményeket a Azure Data Box különböző szakaszaiban, és Azure Data Box Heavy exportálási sorrendet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 3a915ac8de83a5e183660ec4a3d05044eafff4a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94337508"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-export-orders"></a>A Azure Data Box nyomon követése és eseménynaplózása, valamint Azure Data Box Heavy exportálási megrendelések

Egy Data Box vagy Data Box Heavy exportálási sorrend a következő lépésekből áll: sorrend, beállítás, Adatmásolás, visszatérés és adattörlés. A sorrend egyes lépéseinek megfelelően több műveletet is végrehajthat a rendeléshez való hozzáférés szabályozásához, az események naplózásához, a sorrend nyomon követéséhez és a létrehozott naplók értelmezéséhez.

Ez a cikk részletesen ismerteti a Data Box vagy Data Box Heavy exportálási rendeléseinek nyomon követéséhez és naplózásához rendelkezésre álló különböző mechanizmusokat vagy eszközöket. A cikkben található információk a következőkre vonatkoznak: Data Box és Data Box Heavy. A következő szakaszban a Data Boxra való hivatkozás a Data Box Heavyra is vonatkozik.

Az alábbi táblázat a Data Box exportálási sorrend lépéseinek összegzését, valamint az egyes lépések során a sorrend nyomon követésére és naplózására szolgáló eszközöket tartalmazza.

| Data Box exportálási sorrend szakasza       | Nyomon követhető és naplózható eszköz                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Rendelés létrehozása               | [A megrendelés hozzáférés-vezérlésének beállítása az Azure RBAC](#set-up-access-control-on-the-order) <br> [Részletes napló engedélyezése a sorrendben](#enable-verbose-log-in-the-order)                                                    |
| Megrendelés feldolgozva            | [A sorrend nyomon követése](#track-the-order) <ul><li> Azure Portal </li><li> Szállítási szolgáltató webhelye </li><li>E-mail-értesítések</ul> |
| Eszköz beállítása              | Az eszköz hitelesítő adatai naplózása a [tevékenység naplófájljaiban](#query-activity-logs-during-setup)              |
| Adatok másolása az eszközről        | [A másolási naplók áttekintése](#copy-log) <br> [Részletes naplók áttekintése](#verbose-log) az Adatmásolás előtt            |
| Adatok törlése az eszközről   | [Felügyeleti naplók láncának megtekintése](#get-chain-of-custody-logs-after-data-erasure) , beleértve a naplókat és a sorrendi előzményeket                |


## <a name="set-up-access-control-on-the-order"></a>Hozzáférés-vezérlés beállítása a rendeléshez

Megadhatja, hogy ki férhet hozzá a rendeléshez a rendelés első létrehozásakor. Állítsa be az Azure-szerepköröket különböző hatókörökön a Data Boxi sorrend elérésének szabályozásához. Az Azure-szerepkörök határozzák meg a hozzáférés típusát – írható és olvasható, írásvédett, írás és olvasás a műveletek egy részhalmazára.

A Azure Data Box szolgáltatáshoz definiálható két szerepkör a következő:

- **Data Box-olvasó** – a hatókörben megadott rendelés (ek) hez csak olvasási hozzáférése van. Csak egy megrendelés részleteit tekinthetik meg. Nem férhetnek hozzá a Storage-fiókokkal kapcsolatos egyéb adatokhoz, vagy szerkesztheti a rendelés részleteit, például a címeket és így tovább.
- **Data Box közreműködő** – csak olyan megrendelés hozható létre, amely az adatok átvitelét egy adott Storage-fiókba, *Ha már rendelkezik írási hozzáféréssel egy Storage-fiókhoz*. Ha nem fér hozzá egy Storage-fiókhoz, akkor sem hozhatnak létre Data Box, hogy az Adatmásolást a fiókba. Ez a szerepkör nem határozza meg a Storage-fiókhoz kapcsolódó engedélyeket, és nem biztosít hozzáférést a Storage-fiókokhoz.  

Egy megrendelés elérésének korlátozásához a következőket teheti:

- Rendeljen hozzá egy szerepkört egy rendelési szinten. A felhasználónak csak a szerepkörök által meghatározott engedélyekkel kell megfelelnie az adott Data Box sorrendtel való interakcióhoz, és semmi más nem.
- Rendeljen hozzá egy szerepkört az erőforráscsoport szintjén, a felhasználó hozzáfér az adott erőforráscsoporthoz tartozó összes Data Box-rendeléshez.

További információ az Azure-RBAC használatáról: ajánlott [eljárások az Azure RBAC](../role-based-access-control/best-practices.md).

## <a name="enable-verbose-log-in-the-order"></a>Részletes napló engedélyezése a sorrendben

Ha Data Box exportálási sorrendet helyez el, lehetősége van a részletes napló gyűjteményének engedélyezésére. Itt látható a részletes naplót engedélyező megrendelési képernyő:

![Exportálási beállítás kiválasztása](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

Ha bejelöli a **részletes napló belefoglalása** lehetőséget, akkor a rendszer részletes naplófájlt hoz létre az adatok Azure Storage-fiókból való másolása során. Ez a napló az összes sikeresen exportált fájl listáját tartalmazza.

Az exportálási sorrendtel kapcsolatos további információkért lásd: [exportálási sorrend létrehozása Data boxhoz](data-box-deploy-export-ordered.md)

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

- A rendszer minden bejelentkezést valós időben naplóz a Data Boxba. Ezek az információk azonban csak a megrendelés sikeres befejeződése után érhetők el a [felügyeleti naplók láncában](#chain-of-custody-audit-logs) .

## <a name="view-logs-during-data-copy"></a>Naplók megtekintése az Adatmásolás során

Az adatoknak a Data Boxból való másolása előtt letöltheti és áttekintheti a *másolási naplót* és a *részletes naplót* a Data boxba másolt adatokhoz. Ezek a naplók akkor jönnek létre, amikor az adatok az Azure Storage-fiókjából a Data Boxba másolódnak. 

### <a name="copy-log"></a>Napló másolása

A Data Box adatainak másolása előtt töltse le a másolási naplót a **kapcsolat és másolás** lapról.

Itt látható a *másolási napló* kimenete, ha nem voltak hibák, és az összes fájl másolása az Azure-ból Data Box eszközre történő Adatmásolás során történt.

```output
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
  <TotalFiles_Blobs>5521</TotalFiles_Blobs>
  <FilesErrored>0</FilesErrored>
</CopyLog>
``` 
    
Íme egy minta kimenet, ha a *másolási napló* hibákat tartalmaz, és néhány fájl nem másolható az Azure-ból.

```output
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>    
```

Az alábbi lehetőségek állnak rendelkezésére, hogy exportálja ezeket a fájlokat: 

- Átviheti azokat a fájlokat, amelyeket a hálózaton nem sikerült átmásolni. 
- Ha az adatméret meghaladja a felhasználható eszközkapacitást, akkor részleges másolás történik, és a másolásból kihagyott fájlok mind szerepelnek ebben a naplóban. Ezt a naplót használhatja bemeneti XML-ként egy új Data Box-megrendelés létrehozásához, majd átmásolhatja ezeket a fájlokat.

### <a name="verbose-log"></a>Részletes napló

A *részletes napló* az Azure Storage-fiókból sikeresen exportált összes fájl listáját tartalmazza. A napló a fájlméreteket és az ellenőrzőösszegek számítását is tartalmazza.

A részletes napló a következő formátumban adja meg az adatokat:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Az alábbi példa a részletes napló kimenetét jeleníti meg. 

```powershell
  <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
  </File>
  ``````

A részletes naplók az Azure Storage-fiókba is másolódnak. Alapértelmezés szerint a rendszer a naplókat egy nevű tárolóba írja `copylog` . A naplók tárolása a következő elnevezési konvencióval történik:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

A másolási napló elérési útja a portál **Áttekintés** paneljén is megjelenik.

<!-- add a screenshot-->

A naplók segítségével ellenőrizheti, hogy az Azure-ból másolt fájlok megfelelnek-e a helyszíni kiszolgálóra másolt adatoknak. 

A részletes naplófájl használata:

- A tényleges nevekkel és a Data Boxból másolt fájlok számával kapcsolatos ellenőrzés.
- A fájlok tényleges méreteinek ellenőrzéséhez.
- Annak ellenőrzéséhez, hogy a *crc64* nem nulla karakterláncnak felel-e meg. Az Azure-ból való exportálás során ciklikus redundancia-ellenőrzési (CRC) számítás történik. A rendszer összehasonlíthatja az exportálási FCSF, és azt követően, hogy az adatok Data Boxról a helyszíni kiszolgálóra másolódnak. A CRC-eltérés azt jelzi, hogy a megfelelő fájlokat nem sikerült megfelelően másolni.


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Adattörlési lánc beolvasása az adattörlés után

Miután az adatok törlődnek a Data Box lemezekről a NIST SP 800-88 1. verziójának útmutatása szerint, a felügyeleti naplók lánca elérhető. Ezek a naplók tartalmazzák a felügyeleti naplók láncát és a sorrendi előzményeket. A rendszer a naplókat is másolja az AJ-vagy manifest-fájlokba.

### <a name="chain-of-custody-audit-logs"></a>Felügyeleti naplók lánca

A felügyeleti naplók lánca információt tartalmaz a Data Box vagy Data Box Heavy megosztásának bekapcsolásáról és eléréséről, ha az Azure-adatközponton kívül van. Ezek a naplók a következő helyeken találhatók: `storage-account/azuredatabox-chainofcustodylogs`

Íme egy példa a naplóból a Data Box:

```output
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
