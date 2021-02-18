---
title: Azure-támogatáskérés kezelése
description: Ismerteti a támogatási kérések megtekintését, az üzenetek küldését, a kérés súlyossági szintjének megváltoztatását, a diagnosztikai adatok megosztását az Azure-támogatással, a lezárt támogatási kérelem ismételt megnyitását és a fájlok feltöltését.
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: d6c68dd341e0794a690b41b73ecc4be954db7359
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653849"
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

| 0-9, A – C     | D-G   | H-M         | N – P   | R-T      | U-W        | X – Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | . dat  | . har        | . ODX  | . rar     | . TDB       | .xlam   |
| . a          | . db   | .hwl        | . gyakran  | . rdl     | . TDF       | . XLR    |
| . ABC        | . Eszközkezelő pont –  | . ics        | . régi  | . RDLC    | . Text      | .xls    |
| . adm        | .do_  | . ini        | . egy  | .re_     | .thmx      | .xlsb   |
| . aspx       | .doc  | .java       | . OSD  | . reg     | .tif       | .xlsm   |
| . ATF        | .docm | .jpg        | . KI  | . Eltávolítás  | . TRC       | .xlsx   |
| . b          | .docx | . LDF        | . P1   | . ren     | . TTD       | .xlt    |
| .ba_        | .dotm | . levélpapírfejléc | . Pcap | . Átnevezés  | .tx_       | .xltx   |
| . bak        | .dotx | . lnk        | . pdb  | . RFT     | .txt       | .xml    |
| .bat        | .dtsx | .lo_        | .pdf  | . RPT     | .uccapilog | . XMLA   |
| . blg        | . EDS  | . log        | . Piz  | . RTE     | .uccplog   | .xps    |
| .CA_        | . EMF  | . LPK        | .pmls | .rtf     | . udcx      | . xsd    |
| . CAB        | . EML  | . manifest   | .png  | . Run     | .vb_       | . xsn    |
| . Cap        | . a. a  | . Master     | .potx | . saz     | .vbs_      | . xxx    |
| .catx       | . err  | . mdmp       | .ppt  | .sql     | . vcf       | .z_     |
| . CFG        | . etl  | . MOF        | .pptm | .sqlplan | . vsd       | . Z01    |
| . tömörített | . evt  | . mp3        | .pptx | . stp     | . wdb       | .z02    |
| . Config     | . evtx | . mpg        | . prn  | .svclog  | . WKS       | . zi     |
| . CPK        | . PÉLDÁUL   | .ms_        | . PSF  | -        | . WMA       | .zi_    |
| . cpp        | .ex_  | . msg        | . pst  | -        | . wmv       | .zip    |
| .cs         | .ex0  | .msi        | . pub  | -        | . WMZ       | .zip_   |
| . CSV        | . FRD  | . MSO        | -     | -        | . wps       | .zipp   |
| . CVR        | .gif  | . msu        | -     | -        | . WPT       | . zip |
| -           | . GUID | . nfo        | -     | -        | . WSDL      | . villámgyors  |
| -           | . gz   | -           | -     | -        | . wsp       | . ZIPX   |
| -           | -     | -           | -     | -        | .wtl       | . zit    |
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
