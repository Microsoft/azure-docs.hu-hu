---
title: Hibaelhárítási útmutató az élő közvetítéshez | Microsoft Docs
description: Ez a cikk a Azure Media Services élő közvetítéssel kapcsolatos problémák elhárításához nyújt javaslatokat.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 1b7a7ec746f5400fe65e3e1db88ae61e97ae710a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009045"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Hibaelhárítási útmutató az élő streameléshez

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Ez a cikk az élő közvetítéssel kapcsolatos problémák elhárításához nyújt javaslatokat.

## <a name="issues-related-to-on-premises-encoders"></a>Helyszíni kódolókkal kapcsolatos problémák
Ez a szakasz a helyszíni kódolókkal kapcsolatos olyan problémák megoldását ismerteti, amelyek egyetlen sávszélesség-továbbítási adatfolyam küldésére vannak konfigurálva az élő kódoláshoz engedélyezett AMS-csatornák számára.

### <a name="problem-would-like-to-see-logs"></a>Probléma: szeretné megtekinteni a naplókat
* **Lehetséges probléma**: a hibakeresési naplók nem találhatók, amelyek segíthetnek a hibák elhárításában.
  
  * **Wirecast**: a naplókat általában a C:\Users \{ username} \AppData\Roaming\Wirecast\ találja 
  * **Elemi élő**: a felügyeleti portálon található naplókra mutató hivatkozásokat tartalmaz. Kattintson a **stats**, majd a **naplók** elemre. A **naplófájlok** lapon megjelenik a naplók listája a liveevent összes eleméhez; Válassza ki az aktuális munkamenetnek megfelelőt. 
  * **Flash Media Live Encoder**: megtalálhatja a **naplózási könyvtárat...** a **kódolási napló** lapon navigálva.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Probléma: nincs lehetőség a progresszív streamek kihelyezésére
* **Lehetséges probléma**: a használt kódoló nem automatikusan deinterlace. 
  
    **Hibaelhárítási lépések**: keressen egy, a kódoló felületén belüli kikötési lehetőséget. Ha a lekötés engedélyezve van, a progresszív kimenet beállításainál próbálkozzon újra. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Probléma: a rendszer több kódoló kimeneti beállítást próbált meg, és továbbra sem tud csatlakozni.
* **Lehetséges probléma**: az Azure Encoding Channel nem volt megfelelően alaphelyzetbe állítva. 
  
    **Hibaelhárítási lépések**: gondoskodjon arról, hogy a kódoló többé ne álljon le AMS-re, állítsa le és állítsa alaphelyzetbe a csatornát. Ha ismét fut, próbálja meg csatlakoztatni a kódolót az új beállításokkal. Ha ez továbbra sem orvosolja a problémát, próbáljon meg teljesen új csatornát létrehozni, időnként előfordulhat, hogy a csatornák több sikertelen próbálkozás után is megsérülnek.  
* **Lehetséges probléma**: a GOP-méret vagy a kulcstároló-beállítások nem optimálisak. 
  
    **Hibaelhárítási lépések**: az ajánlott GOP-méret vagy a kulcsképek-intervallum két másodperc. Néhány kódoló kiszámítja ezt a beállítást a keretek számában, míg mások a másodperceket használják. Például: a 30 fps kihelyezése esetén a GOP mérete 60 képkockákból áll, ami 2 másodpercnek felel meg.  
* **Lehetséges probléma**: a lezárt portok blokkolja az adatfolyamot. 
  
    **Hibaelhárítási lépések**: Ha RTMP-n keresztül végez adatfolyam-továbbítást, ellenőrizze a tűzfal és/vagy a proxybeállítások beállításait, hogy meggyőződjön arról, hogy a 1935-es és a 1936 

> [!NOTE]
> Ha a hibaelhárítási lépések követése után még mindig nem tud adatfolyamot továbbítani, küldjön egy támogatási jegyet a Azure Portal használatával.
> 
> 

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

