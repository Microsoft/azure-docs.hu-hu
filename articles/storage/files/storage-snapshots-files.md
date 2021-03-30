---
title: A Azure Files megosztási pillanatképének áttekintése | Microsoft Docs
description: A megosztási pillanatkép egy Azure Files-megosztás írásvédett verziója, amelyet egy adott időpontban végeznek el a megosztás biztonsági mentésének módját illetően.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d415ef165da18312a458d7d14fba18acd1bf44cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84235608"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Az Azure Files megosztási pillanatképeinek áttekintése

Az Azure Files lehetővé teszi, hogy a fájlmegosztásokról megosztási pillanatképeket készítsen. A pillanatképek rögzítik a megosztás adott időpontra jellemző állapotát. Ebben a cikkben a megosztási pillanatképek által biztosított képességeket ismertetjük, valamint azt, hogyan élhet az előnyeikkel egy adott használati esetben.

## <a name="when-to-use-share-snapshots"></a>Mikor érdemes megosztási pillanatképeket használni?

### <a name="protection-against-application-error-and-data-corruption"></a>Alkalmazáshibák és adatsérülések elleni védelem

A fájlmegosztásokat használó alkalmazások olyan műveleteket hajtanak végre, mint például az írás, az olvasás, a tárolás, az átvitel és a feldolgozás. Ha egy alkalmazás nem megfelelően van konfigurálva vagy egy véletlen hiba lép fel, előfordulhat, hogy a rendszer véletlenül felülír egyes blokkokat, vagy kárt okoz bennük. Ezen helyzetek elkerülése érdekében az új alkalmazáskód üzembe helyezése előtt készítse el a megosztási pillanatképet. Ha az új üzemelő példány hibát vagy egy alkalmazáshibát okoz, visszatérhet az adatok fájlmegosztásban őrzött korábbi verziójához. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Véletlen törlések vagy módosítások elleni védelem

Tegyük fel, hogy egy szövegfájlon dolgozik egy fájlmegosztásban. A szövegfájl bezárása után nem fogja tudni visszavonni a módosításokat. Ebben az esetben helyre kell állítania a fájl egyik korábbi verzióját. A megosztási pillanatképekkel visszaállíthatja a fájl korábbi verzióit, ha a fájlt véletlenül átnevezte vagy törölte.

### <a name="general-backup-purposes"></a>Általános biztonsági mentési célok

A fájlmegosztás létrehozása után rendszeresen létrehozhatja annak megosztási pillanatképét, amelyeket felhasználhat az adatok biztonsági mentéséhez. A rendszeresen készített pillanatképpel megőrizhetők az adatok korábbi verziói, amelyek későbbi naplózási követelményekhez vagy vészhelyreállításhoz is felhasználhatók. Javasoljuk, hogy az [Azure fájlmegosztás biztonsági mentését](../../backup/azure-file-share-backup-overview.md) biztonsági mentési megoldásként használja a pillanatképek készítéséhez és kezeléséhez. A pillanatképeket saját kezűleg is elvégezheti és kezelheti a CLI vagy a PowerShell használatával.

## <a name="capabilities"></a>Képességek

A megosztási pillanatkép az adatok egy időponthoz tartozó, írásvédett másolata. A pillanatképeket a REST API használatával hozhatja létre, törölheti és kezelheti. Ugyanezek a képességek az ügyféloldali függvénytárban, az Azure CLI-ben és a Azure Portal is elérhetők. 

A megosztások pillanatképeit a REST API és az SMB használatával is megtekintheti. Lekérheti a címtár vagy a fájl verzióinak listáját, és közvetlenül meghajtóként is csatlakoztathat egy adott verziót (csak Windows – lásd: [korlátozások](#limits)). 

A megosztási pillanatkép létrehozása után a fájl olvasható, másolható vagy törölhető, de nem módosítható. Nem másolhat teljes megosztási pillanatképet egy másik Storage-fiókba. Ezt a fájlt fájl alapján kell végrehajtania a AzCopy vagy más másolási mechanizmusok használatával.

A megosztott Pillanatképek funkció a fájlmegosztás szintjén van megadva. A lekérést az egyes fájlok szintjén kell megadnia, hogy lehetővé váljon az egyes fájlok visszaállítása. A teljes fájlmegosztást az SMB, a REST API, a portál, az ügyféloldali kódtár vagy a PowerShell/CLI-eszközök használatával állíthatja vissza.

A fájlmegosztás megosztási pillanatképe megegyezik az alapfájl-megosztással. Az egyetlen különbség, hogy egy **datetime** érték van hozzáfűzve a megosztási URI-hoz, hogy jelezze a megosztási pillanatkép készítésének időpontját. Ha például a fájlmegosztás URI-ja http: \/ /storagesample.Core.file.Windows.net/MyShare, a megosztási pillanatkép URI-ja a következőhöz hasonló:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

A pillanatképek megosztása mindaddig megmarad, amíg a rendszer explicit módon nem törli őket. A megosztási pillanatkép nem tudja megélni az alapfájl megosztását. Az alapfájl-megosztáshoz társított Pillanatképek enumerálásával nyomon követheti az aktuális pillanatképeket. 

Amikor megosztási pillanatképet hoz létre egy fájlmegosztás számára, a megosztás rendszertulajdonságaiban lévő fájlokat a rendszer a megosztási pillanatképre másolja, és ugyanazokat az értékeket használja. A rendszer az alapfájlokat és a fájlmegosztás metaadatait is átmásolja a megosztási pillanatképbe, hacsak nem ad meg külön metaadatokat a megosztási pillanatképhez a létrehozásakor.

A megosztási pillanatképekkel rendelkező megosztásokat nem törölheti, ha először törli az összes megosztási pillanatképet.

## <a name="space-usage"></a>Lemezterület-használat

A megosztási Pillanatképek növekményes jellegűek. Csak a legutóbbi megosztási pillanatkép mentése után módosult adatok lettek mentve. Ez minimálisra csökkenti a megosztási pillanatkép létrehozásához szükséges időt, és menti a tárolási költségeket. Az objektumra vagy tulajdonságra vagy metaadat-frissítési műveletre vonatkozó bármilyen írási művelet beleszámít a "megváltozott tartalom" felé, és a megosztási pillanatképben tárolódik. 

A tárterület megőrzése érdekében törölheti a megosztási pillanatképet arra az időszakra, amikor a változás a legmagasabb volt.

Annak ellenére, hogy a megosztási Pillanatképek Növekményesen vannak mentve, csak a legutóbbi megosztási pillanatképet kell megőriznie a megosztás visszaállításához. Ha töröl egy megosztási pillanatképet, a rendszer csak az adott megosztási pillanatképhez egyedi adatok törlődnek. Az aktív Pillanatképek tartalmazzák az összes olyan információt, amelyre szüksége van az adatok tallózásához és visszaállításához (a megosztási pillanatkép készítésének időpontjáról) az eredeti helyre vagy egy másik helyre. Az elemek szintjén állítható vissza.

A pillanatképek nem számítanak bele az 5 TB-os megosztási korlátba. A tárolóhelyek megosztási pillanatképei összesen nem megengedettek. A Storage-fiókra vonatkozó korlátozások továbbra is érvényesek.

## <a name="limits"></a>Korlátok

A Azure Files ma 200-es megosztási Pillanatképek maximális száma. A 200-es megosztási pillanatképek létrehozása után törölni kell a régebbi megosztási pillanatképeket, hogy újakat hozzon létre. 

A megosztási Pillanatképek létrehozásának egyidejű hívása nincs korlátozva. Az adott fájlmegosztás pillanatképeit használó tárterületnek nincs korlátja. 

A megosztási Pillanatképek jelenleg nem csatlakoztathatók Linux rendszeren. Ennek az az oka, hogy a Linux SMB-ügyfél nem támogatja az olyan csatlakoztatási pillanatképeket, mint a Windows.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Adatok visszamásolása megosztásba egy megosztási pillanatképből

A fájlokra és megosztási pillanatképekre vonatkozó másolási műveletek az alábbi szabályokat követik:

A fájlmegosztási pillanatképben található egyes fájlokat az alapszintű megosztásba vagy bármely más helyre átmásolhatja. A pillanatképből történő fájlonkénti másolással visszaállíthatja az egyes fájlok korábbi verzióit, illetve a teljes fájlmegosztást is. A megosztási pillanatkép nem lesz előléptetve az alapszintű megosztásba. 

A megosztási pillanatkép nem módosul a másolás során, azonban a rendszer felülírja az alapszintű fájlmegosztást a megosztási pillanatképben elérhető adatok másolatával. Az összes visszaállított fájl „módosított tartalomnak” számít.

Azt is megteheti, hogy a fájlmegosztási pillanatképben található egyes fájlokat átmásolja egy eltérő névvel rendelkező másik célhelyre. Az eredményül kapott célfájl egy írható fájl, nem megosztási pillanatkép. Ebben az esetben az alapszintű megosztás nem módosul.

Amikor a rendszer felülírja a célfájlt egy másolattal, az eredeti célfájlhoz társított megosztási pillanatképek nem módosulnak.

## <a name="general-best-practices"></a>Általános ajánlott eljárások

Javasoljuk, hogy az [Azure-fájlmegosztás biztonsági mentését](../../backup/azure-file-share-backup-overview.md) biztonsági mentési megoldásként használja a pillanatképek készítésének automatizálásához, valamint a pillanatképek kezeléséhez. Amikor infrastruktúrát futtat az Azure-ban, automatizálja az Adathelyreállítás biztonsági mentését, amikor csak lehetséges. Az automatizált műveletek sokkal megbízhatóbbak, mint a manuális folyamatok, ami segít az adatvédelem és a helyreállítás javításában. Az automatizáláshoz használhatja az Azure-fájlmegosztás biztonsági mentését, a REST APIt, az ügyfél-SDK-t vagy a parancsfájlok futtatását.

A megosztási pillanatkép-ütemező üzembe helyezése előtt körültekintően vegye figyelembe a megosztás pillanatképének gyakoriságát és a megőrzési beállításokat, hogy elkerülje a szükségtelen költségek felmerülését.

A megosztási Pillanatképek csak a fájl szintű védelmet biztosítják. A megosztási Pillanatképek nem akadályozzák meg a FAT-Finger törlését egy fájlmegosztás vagy egy Storage-fiók esetében. A Storage-fiókok véletlen törlések elleni védelméhez engedélyezheti a helyreállítható [törlést](storage-files-prevent-file-share-deletion.md), vagy zárolhatja a Storage-fiókot és/vagy az erőforráscsoportot.

## <a name="next-steps"></a>Következő lépések
- A megosztási Pillanatképek használata a ben:
    - [Azure-fájlmegosztás biztonsági mentése](../../backup/azure-file-share-backup-overview.md)
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [Parancssori felület](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Pillanatképek megosztása – gyakori kérdések](storage-files-faq.md#share-snapshots)