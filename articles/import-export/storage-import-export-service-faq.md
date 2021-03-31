---
title: Az Azure import/export szolgáltatással kapcsolatos gyakori kérdések | Microsoft Docs
description: Válaszok az Azure import export szolgáltatással kapcsolatos gyakori kérdésekre.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: be6c48efc77880addf814b1609d4a371c7c5c73b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98706483"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Azure import/export szolgáltatás: gyakori kérdések

A következő kérdések és válaszok, amelyek akkor merülhetnek fel, ha az Azure import/export szolgáltatást használja az adatok Azure Storage-ba történő átviteléhez. A kérdéseket és a válaszokat az alábbi kategóriák szerint csoportosítottuk:

- Az import/export szolgáltatás ismertetése
- A lemezek előkészítése importálásra/exportálásra
- Importálási/exportálási feladatok
- Szállítási lemezek
- Különböző veszélyes anyagok és tárgyak

## <a name="about-importexport-service"></a>Az import/export szolgáltatás ismertetése

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Másolhatom az Azure file Storage szolgáltatást az Azure import/export szolgáltatás használatával?

Igen. Az Azure import/export szolgáltatás támogatja az Azure File Storageba való importálást. Jelenleg nem támogatja a Azure Files exportálását.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Elérhető-e az Azure import/export szolgáltatás a CSP-előfizetésekhez?

Igen. Az Azure import/export szolgáltatás támogatja a Cloud Solution Providers (CSP) előfizetéseket.

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-microsoft-365"></a>Használhatom az Azure import/export szolgáltatást a PST-postaládák és a SharePoint-alapú adatMicrosoft 365ek másolására?

Igen. További információkért tekintse meg a [szervezete PST-fájljainak importálását ismertető](/microsoft-365/compliance/importing-pst-files-to-office-365)cikket.

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Használhatom az Azure import/export szolgáltatást a biztonsági másolatok offline másolásához a Azure Backup szolgáltatásba?

Igen. További információért látogasson el a [Azure Backup offline biztonsági mentési munkafolyamatára](../backup/backup-azure-backup-import-export.md).

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Vásárolhatok meghajtókat importálási/exportálási feladatokhoz a Microsofttól?

Nem. Az importálási és exportálási feladatokhoz el kell végeznie a saját meghajtók szállítását.

## <a name="preparing-disks-for-importexport"></a>Lemezek előkészítése importáláshoz/exportáláshoz

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Kihagyhatom a meghajtó-előkészítő lépést az importálási feladatokhoz? Elő lehet készíteni egy meghajtót másolás nélkül?

Nem. Az adatimportáláshoz használt meghajtókat az Azure WAImportExport eszköz használatával kell előkészíteni. Az eszköz használatával Adatmásolást is készíthet a meghajtóra.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Kell-e lemez-előkészítést végezni az exportálási feladatok létrehozásakor?

Nem. Néhány előzetes vizsgálat ajánlott. A szükséges lemezek számának ellenőrzéséhez használja a WAImportExport eszköz PreviewExport parancsát. További információ: a [meghajtó használatának előzetes megtekintése exportálási feladathoz](/previous-versions/azure/storage/common/storage-import-export-tool-previewing-drive-usage-export-v1). A parancs segítségével megtekintheti a kiválasztott Blobok meghajtó-használatát a használni kívánt meghajtók méretétől függően. Azt is győződjön meg arról, hogy az exportálási feladatokhoz tartozó merevlemezre beolvasható és írható.

## <a name="importexport-jobs"></a>Importálási/exportálási feladatok

### <a name="can-i-cancel-my-job"></a>Törölhetem a feladatot?

Igen. A feladat megszakítható, ha az állapota **Létrehozás** vagy **szállítás**. Ezen szakaszok után a feladat nem szakítható meg, és az utolsó szakaszig folytatódik.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Meddig lehet megtekinteni a Befejezett feladatok állapotát a Azure Portal?

A Befejezett feladatok állapotát akár 90 napig is megtekintheti. A befejezett feladatokat 90 nap után törli a rendszer.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Ha több mint 10 meghajtót szeretne importálni vagy exportálni, mi a teendő?

Egy importálási vagy exportálási feladattípus csak 10 meghajtót tud hivatkozni egyetlen feladatokban. Több, mint 10 meghajtó szállításához több feladatot kell létrehoznia. Az ugyanahhoz a feladatokhoz tartozó meghajtóknak ugyanabban a csomagban kell szerepelniük.
Ha további információkat és útmutatást szeretne, ha az adatkapacitás több lemez importálási feladatra terjed ki, forduljon a Microsoft ügyfélszolgálatahoz.

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>A feltöltött blob a "bérlet lejárt" állapotot jeleníti meg. Mit tegyek?

Figyelmen kívül hagyhatja a "bérlet lejárt" mezőt. Az importálási/exportálási művelet a blob bérletét veszi fel a feltöltés során, így meggyőződhet róla, hogy más folyamat nem tudja párhuzamosan frissíteni a blobot. A bérlet lejárt azt jelenti, hogy az Importálás/exportálás már nem lesz feltöltve, és a blob elérhető a használathoz.

## <a name="shipping-disks"></a>Szállítási lemezek

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Mi a maximálisan megengedett HDD egy adott szállítmányban?

Egy adott szállítmányban nincs korlátozva a HDD-k száma. Ha a lemezek több feladathoz tartoznak, javasoljuk, hogy:

- címkézze fel a lemezeket a megfelelő feladatokkal.
- a feladatokat a-1,-2 stb. utótaggal rendelkező nyomkövetési számmal frissítheti.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>A csomagban lévő HDD-n kívül is szerepeljenek?

Csak a merevlemezek szállítása a szállítási csomagban. Ne tartalmazzon olyan elemeket, mint például az áramellátási kábelek vagy az USB-kábelek.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Kell-e a meghajtókat a FedEx vagy a DHL használatával leszállítani?

A meghajtókat az Azure-adatközpontba bármely olyan ismert szolgáltató használatával szállíthatja, mint a FedEx, a DHL, a UPS vagy az USA postai szolgáltatása. A meghajtóknak az adatközpontból való visszaküldéséhez azonban a következőket kell megadnia:

- Egy FedEx-fiók száma az Egyesült Államokban és az EU-ban, vagy
- Egy DHL-fiók száma az ázsiai és ausztráliai régiókban.

> [!NOTE]
> Az Indiában lévő adatközpontok deklarációs levelet igényelnek a fejlécben (kézbesítési challan) a meghajtók visszaadásához. A kötelező belépési lépés elvégzéséhez fel kell vennie a kiválasztott szolgáltatóval való felvételt is, és meg kell osztania a részleteket az adatközpontban.

### <a name="are-there-any-restrictions-with-shipping-and-returning-my-drive-internationally"></a>Vannak korlátozások a szállításra és a meghajtóm nemzetközi visszaküldésére?

Vegye figyelembe, hogy a kiszállított fizikai adathordozónak több nemzetközi határt kell használnia. Ön felelős annak biztosításáért, hogy a fizikai adathordozót és az adatait a vonatkozó törvények szerint importálja és/vagy exportálja. A fizikai adathordozó szállítása előtt forduljon az Advisors szolgáltatáshoz, és győződjön meg róla, hogy az adathordozót és az adatait jogilag el lehet szállítani az azonosított adatközpontba. Ez segít annak biztosításában, hogy a Microsoft időben elérje a Microsoftnak.

A feltöltés befejezése után a meghajtó (k) egy nemzetközi címnek való visszaadásának folyamata hosszabb időt vehet igénybe, mint a helyi szállításhoz szükséges átlagos 2-3 nap. A Azure Portal csomagolásban felsorolt szakaszban a Data Box csapata gondoskodik arról, hogy a megfelelő dokumentáció biztosítva legyen, hogy a szállítás megfeleljen a különböző nemzetközi importálási és exportálási követelményeknek.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Vannak speciális követelmények a lemezek adatközpontba történő kézbesítéséhez?

A követelmények az adott Azure-adatközpont korlátozásai függenek.

- Van néhány olyan webhely, mint például Ausztrália, Németország és Egyesült Királyság déli régiója, amelyek biztonsági okokból a Microsoft Datacenter bejövő AZONOSÍTÓjának számát igénylik a parcellán való íráshoz. A meghajtók vagy lemezek adatközpontba való szállítása előtt forduljon az Azure DataBox Operations () szolgáltatáshoz, és kérje le adbops@microsoft.com ezt a számot. A szám nélkül a csomag el lesz utasítva.
- Az indiai adatközpontok megkövetelik az illesztőprogram személyes adatait, például a kormányzati azonosító kártyáját vagy a nem. (például a PAN, a ICUKA, a DL), a név, a Contact és az autótelefon száma, hogy beszerezze a kapu belépési menetét. A kézbesítés késésének elkerülése érdekében tájékoztassa a szolgáltatót ezekről a követelményekről.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>A feladatok létrehozásakor a szállítási címe nem azonos a saját Storage-fiók helyétől. Mit tegyek?

Egyes tárolási fiókok helyei másodlagos szállítási helyszínekre vannak leképezve. A korábban elérhető szállítási helyszínek is lehetnek ideiglenesen leképezve más helyszínekre. A meghajtók szállítása előtt mindig keresse meg a feladatok létrehozása során megadott szállítási címet.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>A meghajtó szállításakor a szállító kéri az adatközpont kapcsolattartási címet és a telefonszámot. Mit kell megadnia?

A telefonszám és a tartományvezérlő címe a feladatok létrehozásának részeként van megadva.

## <a name="miscellaneous"></a>Különböző veszélyes anyagok és tárgyak

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Mi történik, ha véletlenül olyan HDD-t küldek, amely nem felel meg a támogatott követelményeknek?

Az Azure-adatközpont azt a meghajtót fogja visszaadni, amely nem felel meg a támogatott követelményeknek. Ha a csomag egyes meghajtói megfelelnek a támogatási követelményeknek, a rendszer feldolgozza ezeket a meghajtókat, és a követelményeknek meg nem felelő meghajtókat a rendszer visszaadja Önnek.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>A szolgáltatás formázza a meghajtókat, mielőtt visszaadná őket?

Nem. Minden meghajtó titkosítva van a BitLocker szolgáltatással.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Hogyan férhetnek hozzá a szolgáltatás által importált adatkezelési lehetőségekhez?

Az Azure Storage-fiókjában lévő adat eléréséhez használja a Azure Portal vagy a [Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) .  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Az importálás befejezése után az adataim hogyan néznek ki a Storage-fiókban? Megőrzött a címtár-hierarchia?

A merevlemezek importálási feladathoz való előkészítésekor a célhelyet az adatkészlet CSV-fájljának DstBlobPathOrPrefix mezőjében kell megadni. Ez a Storage-fiókban lévő cél tároló, amelybe a merevlemezről másolt adatok másolása történik. A célként megadott tárolóban a virtuális könyvtárak a merevlemezről jönnek létre, és a Blobok a fájlokhoz jönnek létre.

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Ha a meghajtón már vannak olyan fájlok, amelyek már léteznek a saját Storage-fiókomban, akkor a szolgáltatás felülírja a meglévő blobokat vagy fájlokat?

Függ. A meghajtó előkészítésekor megadhatja, hogy a rendszer felülírja vagy figyelmen kívül hagyja-e a célhelyeket az adathalmaz CSV-fájljában, a (z) <átnevezése | nem felülírt | felülírási>. Alapértelmezés szerint a szolgáltatás átnevezi az új fájlokat ahelyett, hogy felülírja a meglévő blobokat vagy fájlokat.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Kompatibilis a WAImportExport eszköz a 32 bites operációs rendszerekkel?

Nem. A WAImportExport eszköz csak a 64 bites Windows operációs rendszerekkel kompatibilis. A támogatott operációs rendszerek teljes listájáért keresse fel a [támogatott operációs rendszereket](./storage-import-export-requirements.md).

### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Mekkora a blobok és az oldal Blobok maximális mérete az Azure import/export által támogatott?

- A blokk maximális mérete körülbelül 4.768 TB vagy 5 000 000 MB.
- Az oldal maximális blobjának mérete 8TB tárhely.

### <a name="does-azure-importexport-support-aes-256-encryption"></a>Az Azure import/export támogatja az AES-256 titkosítást?

Igen. Az Azure import/export szolgáltatás AES-256 BitLocker-titkosítást használ.

## <a name="next-steps"></a>Következő lépések

* [Mi az Azure Import/Export?](storage-import-export-service.md)