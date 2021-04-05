---
title: Rövid útmutató – blob létrehozása Azure Storage Explorer
titleSuffix: Azure Storage
description: Megtudhatja, hogyan hozhat létre egy tárolót és egy blobot a Azure Storage Explorer használatával, letöltheti a blobot a helyi számítógépre, és megtekintheti a tárolóban lévő összes blobot.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 2477107105b6dbcab96db8d44ac982554dc0c48d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95543170"
---
# <a name="quickstart-use-azure-storage-explorer-to-create-a-blob"></a>Gyors útmutató: Blobok létrehozása a Azure Storage Explorer használatával

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy tárolót és egy blobot a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) használatával. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan tekintheti meg a tárolóban található összes blobot. Ezenkívül megtanulja, hogyan készíthet pillanatfelvételt egy blobról, hogyan kezelheti a tárolók hozzáférési szabályzatait, illetve hogyan hozhat létre közös hozzáférésű jogosultságkódot.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

A rövid útmutatóhoz az Azure Storage Explorer telepítése szükséges. Az Azure Storage Explorer Windows, Macintosh vagy Linux rendszerre való letöltése: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="log-in-to-storage-explorer"></a>Bejelentkezés a Storage Explorerbe

Első indításkor megjelenik a **Microsoft Azure Storage Explorer – Csatlakozás** ablak. A Storage Explorer számos különféle módot kínál a tárfiókokhoz való csatlakozáshoz. A csatlakozási lehetőségeket az alábbi táblázat ismerteti:

|Feladat|Cél|
|---|---|
|Azure-fiók hozzáadása | Átirányítja a szervezet bejelentkezési lapjára az Azure-ba való hitelesítéshez. |
|Kapcsolati sztring vagy közös hozzáférésű jogosultságkód URI azonosítójának használata | Egy tároló vagy tárfiók közvetlen elérésére szolgál egy SAS-token vagy egy közös kapcsolati sztring segítségével. |
|Tárfióknév és -kulcs használata| Csatlakozzon az Azure Storage-hoz a tárfiók nevével és kulcsával.|

Válassza **Az Azure-fiók hozzáadása** lehetőséget, és kattintson **a bejelentkezés..**. elemre. A képernyőn megjelenő utasításokat követve jelentkezzen be az Azure-fiókjába.

![A Microsoft Azure Storage Explorer-csatlakozási ablakot megjelenítő képernyőkép.](media/storage-quickstart-blobs-storage-explorer/connect.png)

A csatlakozás befejeztével az Azure Storage Explorer betöltést követően megjeleníti az **Explorer** lapot. Ez a nézet betekintést nyújt az összes Azure Storage-fiókba, valamint a [Azurite-tároló emulátoron](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos db](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) fiókokon vagy [Azure stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) környezeteken keresztül konfigurált helyi tárhelyre.

![Microsoft Azure Storage Explorer – Csatlakozás ablak](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Tároló létrehozása

A blobok minden esetben egy tárolóba lesznek feltöltve. Így a blobok csoportjait ugyanúgy rendszerezheti, ahogy a fájlokat a számítógép mappáiban.

Tároló létrehozásához bontsa ki az előző lépésben létrehozott tárfiókot. Válassza a **Blobtárolók** lehetőséget, kattintson a jobb gombbal, majd válassza a **Blobtároló létrehozása** lehetőséget. Adja meg a blobtároló nevét. A blob-tárolók elnevezésére vonatkozó szabályok és korlátozások listáját a [tároló létrehozása](storage-quickstart-blobs-dotnet.md#create-a-container) című szakaszban találja. Ha végzett, nyomja le az **Enter** billentyűt a blobtároló létrehozásához. A sikeresen létrehozott blobtároló megjelenik a kiválasztott tárfiókhoz tartozó **Blobtárolók** mappában.

## <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. Az IaaS típusú virtuális gépek biztonsági mentéséhez használt VHD-fájlok lapblobok. A hozzáfűző blobok a naplózáshoz használhatók, például amikor egy fájlba szeretne írni, majd folyamatosan újabb információkat szeretne hozzáadni. A blobtárolókban tárolt fájlok a legtöbb esetben blokkblobok.

A tároló menüszalagján válassza a **Feltöltés** lehetőséget. Ez a művelet lehetővé teszi egy mappa vagy fájl feltöltését.

Válassza ki a feltölteni kívánt fájlokat vagy mappát. Válassza ki a **blob típusát**. A választható lehetőségek: **Hozzáfűző**, **Lap** és **Blokk**.

.vhd- vagy .vhdx-fájlok feltöltésekor válassza a **.vhd- vagy .vhdx-fájlok feltöltése lapblobként (ajánlott)** lehetőséget.

A **Feltöltés mappába (nem kötelező)** mezőben adjon meg egy nevet a mappának, amely a fájlokat vagy mappákat tartalmazza a tárolón. Ha nem választ mappát, a fájlok közvetlenül a tároló gyökérkönyvtárába töltődnek fel.

![Microsoft Azure Storage Explorer – Blob feltöltése](media/storage-quickstart-blobs-storage-explorer/uploadblob.png)

Ha az **OK** gombra kattint, a kiválasztott fájlokat a rendszer sorba állítja a feltöltéshez, majd egyesével feltölti azokat. A feltöltés befejeztével az eredmények megjelennek a **Tevékenységek** ablakban.

## <a name="view-blobs-in-a-container"></a>Blobok megtekintése egy tárolón

Az **Azure Storage Explorerben** válasszon ki egy tárfiókhoz tartozó tárolót. A fő panelen a kiválasztott tárolóban található blobok listája látható.

![Képernyőkép, amely bemutatja, hol válasszon ki egy tárolót Microsoft Azure Storage Explorerban.](media/storage-quickstart-blobs-storage-explorer/listblobs.png)

## <a name="download-blobs"></a>Blobok letöltése

Blobok az **Azure Storage Explorer** használatával való letöltéséhez jelöljön ki egy blobot, majd válassza a menüszalag **Letöltés** elemét. Megnyílik egy fájl párbeszédpanel, amelyen megadhat egy fájlnevet. Kattintson a **Mentés** lehetőségre a blob letöltéséhez a helyi helyre.

## <a name="manage-snapshots"></a>Pillanatképek kezelése

Az Azure Storage Explorer lehetővé teszi a blobokról készült [pillanatképek](./snapshots-overview.md) rögzítését és kezelését. Egy blob pillanatképének elkészítéséhez kattintson a jobb gombbal a blobra, majd válassza a **Pillanatkép létrehozása** lehetőséget. Egy blob pillanatképének megtekintéséhez kattintson a jobb gombbal a blobra, majd válassza a **Pillanatképek kezelése** lehetőséget. Az aktuális lapon megjelenik a blob pillanatképeinek listája.

![A Microsoft Azure Storage Explorerban található Blobok listáját bemutató képernyőkép.](media/storage-quickstart-blobs-storage-explorer/snapshots.png)

## <a name="manage-access-policies"></a>Hozzáférési szabályzatok kezelése

A Storage Explorer felhasználói felületén lehetőség nyílik a tárolók hozzáférési szabályzatainak kezelésére. Kétféle biztonságos hozzáférési szabályzat (SAS) létezik: szolgáltatás- és fiókszintű. A fiókszintű SAS a tárfiókra érvényes, továbbá több szolgáltatásra és erőforrásra is vonatkozhat. A szolgáltatásszintű SAS egy adott szolgáltatás erőforrására érvényes. A szolgáltatási szint SAS létrehozásához kattintson a jobb gombbal bármelyik tárolóra, és válassza a **hozzáférési házirendek kezelése...** lehetőséget. A fiók szintű SAS létrehozásához kattintson a jobb gombbal a Storage-fiókra.

Válassza a **Hozzáadás** lehetőséget egy új hozzáférési szabályzat hozzáadásához és a szabályzat engedélyeinek megadásához. Ha végzett, kattintson a **Mentés** gombra a hozzáférési szabályzat mentéséhez. A szabályzat innentől kezdve elérhető lesz a közös hozzáférésű jogosultságkódok konfigurálásakor.

## <a name="work-with-shared-access-signatures"></a>Közös hozzáférésű jogosultságkódok használata

A közös hozzáférésű jogosultságkódok (SAS) a Storage Exploreren keresztül kérhetők le. Kattintson a jobb gombbal a Storage-fiókra, a tárolóra vagy a blobra, majd válassza a **közös hozzáférésű aláírás beolvasása...** lehetőséget. Válassza ki a kezdő és a lejárati időt, valamint a SAS URL-címére vonatkozó engedélyeket, és válassza a **Létrehozás** lehetőséget. A lekérdezési sztringgel ellátott teljes URL-cím, illetve a lekérdezési sztring önmagában is megjelenik a következő képernyőn, ahonnan mindkettő másolható.

![Microsoft Azure Storage Explorer – Tárolóban lévő blobok listázása](media/storage-quickstart-blobs-storage-explorer/sharedaccesssignature.png)

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között az **Azure Storage Explorer** használatával. Ha bővebb információra van szüksége a Blob Storage használatával kapcsolatban, lépjen tovább a Blob Storage használati útmutatójára.

> [!div class="nextstepaction"]
> [Blob Storage-műveletek használati útmutatója](./storage-quickstart-blobs-powershell.md)