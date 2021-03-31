---
title: Azure Data Box Disk a lemezek feloldásával kapcsolatos problémák elhárítása | Microsoft Docs
description: Ismerje meg a munkafolyamatokat, amelyekkel elháríthatja a feloldó eszközt a Azure Data Box Disk használatával. Tekintse meg Data Box Disk feloldási eszköz hibáit.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 08/05/2020
ms.author: alkohli
ms.openlocfilehash: 866cf01243983863292ada0b086f8f5b2f94e412
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87925561"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Hibák elhárítása a lemezek Azure Data Box Diskban való feloldásával kapcsolatban

Ez a cikk Microsoft Azure Data Box Diskra vonatkozik, és ismerteti a feloldási eszköz használata során felmerülő problémák elhárításához használt munkafolyamatokat. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>A Data Box Disk lemezzárolás-feloldó eszközének hibái


| Hibaüzenet/az eszköz viselkedése      | Javaslatok                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| A jelenlegi .NET-keretrendszer nem támogatott. A 4.5-ös és újabb verziók támogatottak.<br><br>Az eszköz hibaüzenettel bezárul.  | A .NET 4.5-ös verziója nincs telepítve. Telepítse a .NET 4.5-ös verzióját a Data Box Disk zárolásának feloldására szolgáló eszközt futtató számítógépre.                                                                            |
| Nem sikerült egyetlen kötetet sem feloldani vagy ellenőrizni. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával.  <br><br>Az eszköz nem tudott egyetlen zárolt meghajtót sem feloldani vagy ellenőrizni. | Az eszköz egyik zárolt meghajtót sem tudta feloldani a megadott hozzáférési kulccsal. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától.                                                |
| Az alábbi kötetek lettek feloldva és ellenőrizve. <br>Kötetek meghajtóbetűjele: E:<br>A következő hozzáférési kulcsokkal egyetlen kötetet sem sikerült feloldani: werwerqomnf, qwerwerqwdfda <br><br>Az eszköz felold egyes meghajtókat, és listázza a sikeres és sikertelen meghajtók betűjelét.| Részleges siker. Egyes meghajtókat nem sikerült feloldani a megadott hozzáférési kulccsal. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától. |
| Az eszköz nem talált zárolt köteteket. Ellenőrizze, hogy a Microsofttól kapott lemez megfelelően csatlakoztatva és zárolt állapotban van-e.          | Az eszköz nem talált egyetlen zárolt meghajtót sem. A meghajtók már fel lettek oldva, vagy a rendszer nem észleli őket. Győződjön meg arról, hogy a meghajtók csatlakoztatva vannak és zároltak. <br> <br>Ezt a hibát akkor is láthatja, ha formázta a lemezeket. Ha formázta a lemezeket, ezek már nem használhatók. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától.                                                          |
| Végzetes hiba: Érvénytelen paraméter<br>Paraméter neve: invalid_arg<br>HASZNÁLAT:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Példa: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Példa: DataBoxDiskUnlock /SystemCheck<br>Példa: DataBoxDiskUnlock /Help<br><br>/PassKeys:       A hozzáférési kulcsot az Azure Data Box Disk-rendelésből töltheti le. A hozzáférési kulccsal oldhatók fel a lemezek.<br>/Help:           Ez a beállítás a parancsmagok használatával kapcsolatos súgóinformációkat és példákat kínál.<br>/SystemCheck:    Ez a beállítás ellenőrzi, hogy a rendszer megfelel-e az eszköz futtatásához szükséges követelményeknek.<br><br>A kilépéshez nyomja le bármelyik billentyűt. | Érvénytelen paraméter lett megadva. Az egyetlen megengedett paraméter a/SystemCheck, a/PassKey és a/help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Lemezek zárolásának feloldása Windows-ügyfél használata esetén

Ez a szakasz részletesen ismerteti a Data Box Disk üzembe helyezése során felmerülő leggyakoribb problémákat, amikor Windows-ügyfelet használ az adatok másolásához.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Probléma: a meghajtó nem oldható fel a BitLockerből
 
**Ok** 

A BitLocker párbeszédablakban használta a jelszót, és a BitLocker zárolásának feloldása párbeszédpanelen megpróbálta feloldani a lemez zárolását. Ez nem fog működni.

**Resolution** (Osztás)

A Data Box lemezek zárolásának feloldásához a Data Box Disk-feloldási eszközt kell használnia, és meg kell adnia a jelszót a Azure Portal. További információt a következő [oktatóanyagban olvashat: kicsomagolás, kapcsolódás és zárolás feloldása Azure Data Box Disk](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Probléma: nem lehetett feloldani vagy ellenőrizni néhány kötetet. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával.
 
**Ok**

A következő hibaüzenet jelenhet meg a naplófájlban, és nem oldható fel vagy ellenőrizhető néhány kötet.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Ez azt jelzi, hogy a Windows PowerShell megfelelő verziója valószínűleg hiányzik a Windows-ügyfélen.

**Resolution** (Osztás)

Telepítheti a [Windows PowerShell v 5,0](https://www.microsoft.com/download/details.aspx?id=54616) -et, és újrapróbálkozhat a művelettel.
 
Ha továbbra sem tudja feloldani a kötetek zárolását, másolja a naplófájlokat a Data Box Disk-feloldási eszközt tartalmazó mappából, és [vegye fel a kapcsolatot Microsoft ügyfélszolgálata](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Következő lépések

- Útmutató az [érvényesítési problémák elhárításához](data-box-disk-troubleshoot.md).
