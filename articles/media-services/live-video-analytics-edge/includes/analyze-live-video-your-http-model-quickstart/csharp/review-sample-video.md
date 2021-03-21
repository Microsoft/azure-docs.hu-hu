---
ms.openlocfilehash: 69fd32c1569dbd1f08815be156585b0cce9f10d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98061179"
---
Az Azure-erőforrások beállításakor a rendszer átmásolja az autópálya-forgalom rövid videóját az Azure-ban lévő, IoT Edge eszközként használt linuxos virtuális gépre. Ez a rövid útmutató egy élő stream szimulálására használja a videofájl használatával.

Nyisson meg egy alkalmazást, például a [VLC Media Playert](https://www.videolan.org/vlc/). Válassza ki `Ctrl+N` , majd illessze be [az autópálya-metszeti minta videóra](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) mutató hivatkozást a lejátszás elindításához. Itt láthatja az autópálya-forgalomban mozgó számos jármű felvételét.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

Ebben a rövid útmutatóban élő videó-elemzéseket fog használni a IoT Edgeon az objektumok, például a gépjárművek és a személyek észleléséhez. A kapcsolódó következtetési eseményeket közzé kell tenni IoT Edge hubhoz.
