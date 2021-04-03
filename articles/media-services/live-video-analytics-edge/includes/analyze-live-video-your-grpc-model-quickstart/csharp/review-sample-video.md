---
ms.openlocfilehash: eaa44ae9d8bf4d723944da695d9a063937b7e020
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98061177"
---
Az Azure-erőforrások beállításakor a rendszer átmásolja az autópálya-forgalom rövid videóját az Azure-ban lévő, IoT Edge eszközként használt linuxos virtuális gépre. Ez a rövid útmutató egy élő stream szimulálására használja a videofájl használatával.

Nyisson meg egy alkalmazást, például a [VLC Media Playert](https://www.videolan.org/vlc/). Válassza a CTRL + N billentyűkombinációt, majd illesszen be egy hivatkozást [az országúti metszet minta videóra](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) a lejátszás megkezdéséhez. Itt láthatja az autópálya-forgalomban mozgó számos jármű felvételét.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

Ebben a rövid útmutatóban élő videó-elemzéseket fog használni a IoT Edgeon az objektumok, például a gépjárművek és a személyek észleléséhez. A kapcsolódó következtetési eseményeket közzé kell tenni IoT Edge hubhoz.
