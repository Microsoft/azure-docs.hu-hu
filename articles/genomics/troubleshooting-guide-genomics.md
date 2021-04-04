---
title: Hibaelhárítási útmutató
titleSuffix: Microsoft Genomics
description: További információ a Microsoft Genomics használatának hibaelhárítási módszereiről, beleértve a hibaüzeneteket és azok megoldásának módját.
keywords: hibaelhárítás, hiba, hibakeresés
services: genomics
author: ruchir
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: c508c10d619cde1a16d89b446c5cfd1a3ce81daf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96023823"
---
# <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató

Íme néhány hibaelhárítási tipp a Microsoft Genomics szolgáltatás használatakor esetlegesen előforduló gyakori problémák némelyikének MSGEN.

 A [Gyakori kérdések](frequently-asked-questions-genomics.md)a hibaelhárításhoz nem kapcsolódó gyakori kérdések című témakörben találhatók.
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>1. lépés: a munkafolyamathoz társított hibakódok megkeresése

A munkafolyamathoz társított hibaüzeneteket a következő módszerekkel érheti el:

1. A parancssor használata és a beírása  `msgen status`
2. standardoutput.txt tartalmának vizsgálata.

### <a name="1-using-the-command-line-msgen-status"></a>1. a parancssor használata `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Három kötelező argumentum:

* URL – az API alap URI-ja
* KULCS – a genomikai fiók elérési kulcsa
    * Az URL-cím és a kulcs megkereséséhez nyissa meg a Azure Portal és nyissa meg a Microsoft Genomics-fiók lapot. A **felügyelet** fejléc alatt válassza a **hozzáférési kulcsok** elemet. Itt megtalálja az API URL-címét és a hozzáférési kulcsokat is.

  
* AZONOSÍTÓ – a munkafolyamat azonosítója
    * A munkafolyamat-azonosító típusának megkereséséhez a `msgen list` parancsban. Feltételezve, hogy a konfigurációs fájl tartalmazza az URL-címet és a hozzáférési kulcsokat, és az a msgen-exe-fájllal azonos helyen található, a parancs a következőképpen fog kinézni: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        A parancs kimenete a következőképpen fog kinézni:
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  Azt is megteheti, hogy a konfigurációs fájl elérési útját nem közvetlenül az URL-cím és a kulcs megadása helyett adja meg. Ha ezeket az argumentumokat a parancssorban és a konfigurációs fájlban is tartalmazza, akkor a parancssori argumentumok elsőbbséget élveznek.  

A 1001-es AZONOSÍTÓJÚ munkafolyamathoz és config.txt fájlhoz a msgen végrehajtható fájllal megegyező elérési útra helyezve a parancs a következőképpen fog kinézni:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. a standardoutput.txt tartalmának vizsgálata 
Keresse meg a kérdéses munkafolyamat kimeneti tárolóját. A MSGEN   `[workflowfilename].logs.zip` minden munkafolyamat-végrehajtás után létrehoz egy mappát. Bontsa ki a mappát a tartalmának megtekintéséhez:

* outputFileList.txt – a munkafolyamat során létrehozott kimeneti fájlok listája
* standarderror.txt – ez a fájl üres.
* standardoutput.txt – naplózza az összes legfelső szintű állapotüzenetek, beleértve a hibákat, amelyek a munkafolyamat futtatásakor történtek.
* GATK naplófájlok – a mappában található összes többi fájl `logs`

Hibaelhárításhoz vizsgálja meg standardoutput.txt tartalmát, és jegyezze fel a megjelenő hibaüzeneteket.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>2. lépés: a gyakori hibákra vonatkozó ajánlott lépések kipróbálása

Ez a szakasz röviden kiemeli Microsoft Genomics szolgáltatás (msgen) és a megoldásához használható stratégiák általános hibáit. 

A Microsoft Genomics szolgáltatás (msgen) a következő két típusú hibát képes eldobni:

1. Belső szolgáltatási hibák: a szolgáltatáson belül belső hibák, amelyek nem oldhatók meg a paraméterek vagy a bemeneti fájlok kijavításával. Előfordulhat, hogy a munkafolyamat ismételt elküldése megoldhatja ezeket a hibákat.
2. Bemeneti hibák: a megfelelő argumentumokkal vagy a fájlformátumok kijavításával feloldható hibák.

### <a name="1-internal-service-errors"></a>1. belső szolgáltatási hibák

A belső szolgáltatási hiba nem a felhasználó számára hajtható végre. Előfordulhat, hogy a munkafolyamatot újra elküldheti, de ha ez nem működik, forduljon Microsoft Genomics támogatási szolgálathoz

| Hibaüzenet                                                                                                                            | Javasolt hibaelhárítási lépések                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Belső hiba történt. Próbálja meg újra elküldeni a munkafolyamatot. Ha ismét ezt a hibát látja, forduljon a Microsoft Genomics támogatási szolgálatához segítségért | Küldje el újra a munkafolyamatot. Ha a probléma továbbra is fennáll, vegye fel a kapcsolatot a támogatási [szolgálattal](file-support-ticket-genomics.md )Microsoft Genomics támogatással. |

### <a name="2-input-errors"></a>2. bemeneti hibák

Ezek a hibák a felhasználók számára hajthatók végre. A fájl típusa és a hibakód alapján Microsoft Genomics szolgáltatás kimenete különböző hibakódokat eredményez. Kövesse az alább felsorolt hibaelhárítási lépéseket.

| A fájl típusa | Hibakód | Hibaüzenet                                                                           | Javasolt hibaelhárítási lépések                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Bármelyik          | 701        | A [readId] Read [numberOfBases] bases, de a korlát [maxReadLength].           | Ennek a hibának a leggyakoribb oka a fájl sérülése, amely két olvasás összefűzését eredményezi. Győződjön meg róla, hogy a bemeneti fájlok vannak. |
| BAM          | 200        |   A következő fájl nem olvasható: "[yourFileName]".                                                                                       | Vizsgálja meg a BAM-fájl formátumát. Küldje el újra a munkafolyamatot egy megfelelően formázott fájllal.                                                                           |
| BAM          | 201        |  Nem olvasható a következő BAM-fájl: [File_name].                                                                                      |Vizsgálja meg a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                            |
| BAM          | 202        | Nem olvasható a következő BAM-fájl: [File_name]. A fájl túl kicsi és hiányzó fejléc.                                                                                        | Vizsgálja meg a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                            |
| BAM          | 203        |   Nem olvasható a következő BAM-fájl: [File_name]. A fájl fejléce sérült volt.                                                                                      |Vizsgálja meg a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                           |
| BAM          | 204        |    Nem olvasható a következő BAM-fájl: [File_name]. A fájl fejléce sérült volt.                                                                                     | Vizsgálja meg a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                           |
| BAM          | 205        |    Nem olvasható a következő BAM-fájl: [File_name]. A fájl fejléce sérült volt.                                                                                     | Vizsgálja meg a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                            |
| BAM          | 206        |   Nem olvasható a következő BAM-fájl: [File_name]. A fájl fejléce sérült volt.                                                                                      | Vizsgálja meg a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                            |
| BAM          | 207        |  Nem olvasható a következő BAM-fájl: [File_name]. A fájl csonkolt eltolása [eltolás] közelében van.                                                                                       | Vizsgálja meg a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                            |
| BAM          | 208        |   Érvénytelen BAM-fájl. A ReadID [Read_Id] nem tartalmaz sorozatot a (File_name) fájlban.                                                                                      | Vizsgálja meg a BAM-fájl formátumát.  Küldje el a munkafolyamatot egy megfelelően formázott fájllal.                                                                             |
| FASTQ        | 300        |  Nem lehet olvasni a FASTQ fájlt. [File_name] nem végződhet sortöréssel.                                                                                     | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                           |
| FASTQ        | 301        |   Nem olvasható a következő FASTQ-fájl: [File_name]. A FASTQ-rekord nagyobb, mint a puffer mérete (eltolás: [_offset])                                                                                      | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
| FASTQ        | 302        |     FASTQ szintaktikai hiba. A ([File_name] fájl üres sorral rendelkezik.                                                                                    | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
| FASTQ        | 303        |       FASTQ szintaktikai hiba. A (z) [File_name] fájl érvénytelen kezdő karaktert tartalmaz a következő eltolásnál: [_offset], sor típusa: [line_type], karakter: [_char]                                                                                  | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
| FASTQ        | 304      |  FASTQ szintaktikai hiba a következő helyen: readID [_ReadID].  A Batch első beolvasása nem rendelkezik a (readID) [File_name] fájlban található/1 végződéssel.                                                                                       | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
| FASTQ        | 305        |  FASTQ szintaktikai hiba a következő helyen: readID [_readID]. A Batch második olvasata nem rendelkezik a (readID) [File_name] fájlban lévő/2 végződéssel.                                                                                      | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                          |
| FASTQ        | 306        |  FASTQ szintaktikai hiba a következő helyen: readID [_ReadID]. A pair első olvasása nem rendelkezik olyan AZONOSÍTÓval, amely/1 a következő fájlban: [File_name].                                                                                       | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                          |
| FASTQ        | 307        |   FASTQ szintaktikai hiba a következő helyen: readID [_ReadID]. A ReadID nem végződik/1 vagy/2. A ([File_name] fájl nem használható párosított FASTQ-fájlként.                                                                                      |Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                          |
| FASTQ        | 308        |  FASTQ olvasási hiba. Mindkét végpont olvasása másképp reagált. Kiválasztotta a megfelelő FASTQ-fájlokat?                                                                                       | Javítsa ki a FASTQ-fájl formátumát, és küldje el újra a munkafolyamatot.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>3. lépés: Kapcsolatfelvétel a Microsoft Genomics támogatással

Ha továbbra is felmerülnek a feladatok, vagy ha további kérdései vannak, forduljon Microsoft Genomics támogatási szolgálathoz a Azure Portal. A támogatási kérések beküldéséről [itt](file-support-ticket-genomics.md)találhat további információt.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan lehet elhárítani a Microsoft Genomics szolgáltatással kapcsolatos gyakori problémákat. További információt és általánosabb gyakori [kérdéseket a gyakori kérdések](frequently-asked-questions-genomics.md)című témakörben talál. 
