---
title: Azure-támogatáskérés kezelése
description: Ismerteti a támogatási kérések megtekintését, az üzenetek küldését, a kérés súlyossági szintjének megváltoztatását, a diagnosztikai adatok megosztását az Azure-támogatással, a lezárt támogatási kérelem ismételt megnyitását és a fájlok feltöltését.
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 4d0c03e0035f6b71a23891ac1691f5421c1bdb76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502518"
---
# <a name="manage-an-azure-support-request"></a>Azure-támogatáskérés kezelése

Miután [létrehozta az Azure-támogatási kérést](how-to-create-azure-support-request.md), felügyelheti azt a [Azure Portalban](https://portal.azure.com), amely ebben a cikkben is szerepel. A kéréseket programozott módon is létrehozhatja és kezelheti az [Azure támogatási jegy REST API](/rest/api/support)vagy az [Azure CLI](/cli/azure/azure-cli-support-request)használatával.

## <a name="view-support-requests"></a>Támogatási kérelmek megtekintése

Tekintse meg a támogatási kérések részleteit és állapotát a **Súgó + támogatási kérelmek támogatásával**  >   .

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="Minden támogatási kérelem":::

Ezen a lapon megkeresheti, szűrheti és rendezheti a támogatási kérelmeket. Válasszon ki egy támogatási kérést a részletek megtekintéséhez, beleértve annak súlyosságát és a kéréshez kapcsolódó üzeneteket.

## <a name="send-a-message"></a>Üzenet küldése

1. A **minden támogatási kérelem** lapon válassza ki a támogatási kérelmet.

1. A **támogatási kérelem** lapon válassza az **új üzenet** elemet.

1. Adja meg az üzenetet, és válassza a **Küldés** lehetőséget.

## <a name="change-the-severity-level"></a>Súlyossági szint módosítása

> [!NOTE]
> A maximális súlyossági szint a [támogatási csomagtól](https://azure.microsoft.com/support/plans)függ.
>

1. A **minden támogatási kérelem** lapon válassza ki a támogatási kérelmet.

1. A **támogatási kérelem** lapon válassza a **módosítás** lehetőséget.

    :::image type="content" source="media/how-to-manage-azure-support-request/change-severity.png" alt-text="A támogatási kérelem súlyosságának módosítása":::

1. A Azure Portal a két képernyő egyikét jeleníti meg attól függően, hogy a kérés már hozzá van-e rendelve egy támogatási szakemberhez:

    - Ha a kérelem még nincs hozzárendelve, a következőhöz hasonló képernyő jelenik meg. Válasszon új súlyossági szintet, majd kattintson a **módosítás** gombra.

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="Új súlyossági szint kiválasztása":::

    - Ha a kérelem hozzá van rendelve, a következőhöz hasonló képernyő jelenik meg. Kattintson **az OK gombra**, majd hozzon létre egy [új üzenetet](#send-a-message) a súlyossági szint módosításának kéréséhez.

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="Új súlyossági szint nem választható ki":::

## <a name="share-diagnostic-information-with-azure-support"></a>Diagnosztikai információk megosztása az Azure-támogatással

Ha támogatási kérelmet hoz létre, alapértelmezés szerint a **megosztás diagnosztikai információi** beállítás van kiválasztva. Ez lehetővé teszi, hogy az Azure-támogatás [diagnosztikai adatokat](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) gyűjtsön az Azure-erőforrásokból:

* A kérés létrehozása után ezt a beállítást nem lehet törölni.

* Ha a kérés létrehozásakor törölte a beállítást, akkor a kérés létrehozása után kiválaszthatja azt.

    1. A **minden támogatási kérelem** lapon válassza ki a támogatási kérelmet.
    
    1. A **támogatási kérelem** lapon válassza az **engedély megadása** lehetőséget, majd válassza az **Igen** , majd **az OK gombot**.
    
        :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="Engedélyek megadása diagnosztikai adatokhoz":::

## <a name="upload-files"></a>Fájlok feltöltése

A file upload (fájlfeltöltés) lehetőséggel feltölthet olyan diagnosztikai fájlokat vagy más fájlokat, amelyekre a támogatási kérelem vonatkozik.

1. A **minden támogatási kérelem** lapon válassza ki a támogatási kérelmet.

1. A **támogatási kérelem** oldalon tallózással keresse meg a fájlt, majd válassza a **feltöltés** lehetőséget. Ha több fájllal rendelkezik, ismételje meg a folyamatot.

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Fájl feltöltése":::

### <a name="file-upload-guidelines"></a>Fájlfeltöltés-irányelvek

A fájlfeltöltés lehetőség használata esetén kövesse az alábbi irányelveket:

* Az adatvédelem védelme érdekében ne adjon meg személyes adatokat a feltöltéshez.
* A fájl neve nem lehet hosszabb 110 karakternél.
* Legfeljebb egy fájlt tölthet fel.
* A fájlok nem lehetnek nagyobbak 4 MB-nál.
* Minden fájlnak rendelkeznie kell fájlnévkiterjesztés (például *. docx* vagy *. xlsx*). A következő táblázat a feltöltésre jogosult filename-bővítményeket tartalmazza.

| 0-9, A – C    | D-G   | H-N         | O-Q   | R-T      | U-W        | X – Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | . dat  | . har        | . ODX  | . rar     | .uccapilog | .xlam   |
| . a          | . db   | .hwl        | . gyakran  | . rdl     | .uccplog   | . XLR    |
| . ABC        | . Eszközkezelő pont –  | . ics        | . régi  | . RDLC    | . udcx      | .xls    |
| . adm        | .do_  | . ini        | . egy  | .re_     | .vb_       | .xlsb   |
| . aspx       | .doc  | .java       | . OSD  | . Eltávolítás  | .vbs_      | .xlsm   |
| . ATF        | .docm | .jpg        | . KI  | . ren     | . vcf       | .xlsx   |
| . b          | .docx | . LDF        | . P1   | . Átnevezés  | . vsd       | .xlt    |
| .ba_        | .dotm | . levélpapírfejléc | . Pcap | . RFT     | . wdb       | .xltx   |
| . bak        | .dotx | .lo_        | . pdb  | . RPT     | . WKS       | .xml    |
| . blg        | .dtsx | . log        | .pdf  | . RTE     | . WMA       | . XMLA   |
| .CA_        | . EDS  | . LPK        | . Piz  | .rtf     | . wmv       | .xps    |
| . CAB        | . EMF  | . manifest   | .pmls | . Run     | . WMZ       | . xsd    |
| . Cap        | . EML  | . Master     | .png  | . saz     | . wps       | . xsn    |
| .catx       | . a. a  | . mdmp       | .potx | .sql     | . WPT       | . xxx    |
| . CFG        | . err  | . MOF        | .ppt  | .sqlplan | . WSDL      | .z_     |
| . tömörített | . etl  | . mp3        | .pptm | . stp     | . wsp       | . Z01    |
| . Config     | . evt  | . mpg        | .pptx | .svclog  | .wtl       | .z02    |
| . CPK        | . evtx | .ms_        | . prn  | . TDB     | -          | . zi     |
| . cpp        | . PÉLDÁUL   | . msg        | . PSF  | . TDF     | -          | .zi_    |
| .cs         | .ex_  | . MSO        | . pst  | . Text    | -          | .zip    |
| . CSV        | .ex0  | . msu        | . pub  | .thmx    | -          | .zip_   |
| . CVR        | . FRD  | . nfo        | -     | .tif     | -          | .zipp   |
| -           | .gif  | -           | -     | . TRC     | -          | . zip |
| -           | . GUID | -           | -     | . TTD     | -          | . villámgyors  |
| -           | . gz   | -           | -     | .tx_     | -          | . ZIPX   |
| -           | -     | -           | -     | .txt     | -          | . zit    |
| -           | -     | -           | -     | -        | -          | .zix    |
| -           | -     | -           | -     | -        | -          | . ZZZ    |

## <a name="close-a-support-request"></a>Támogatási kérelem lezárása

Ha le kell zárnia egy támogatási kérelmet, [küldjön egy üzenetet](#send-a-message) , amely arra kéri, hogy zárja be a kérést.

## <a name="reopen-a-closed-request"></a>Lezárt kérelem újbóli megnyitása

Ha újra meg kell nyitnia egy lezárt támogatási kérelmet, hozzon létre egy [új üzenetet](#send-a-message), amely automatikusan újra megnyitja a kérést.

## <a name="cancel-a-support-plan"></a>Támogatási terv lemondása

Ha meg kell szakítania egy támogatási csomagot, tekintse meg [a támogatási csomag megszakítása](../../cost-management-billing/manage/cancel-azure-subscription.md#cancel-a-support-plan)című témakört.

## <a name="next-steps"></a>Következő lépések

[Azure-támogatáskérések létrehozása](how-to-create-azure-support-request.md)

[Azure-beli támogatási jegy REST API](/rest/api/support)
