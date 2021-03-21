---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 8c77efe9e3e301573b032d1dc1dd32bb4a5ab1a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417738"
---
A tömörített hang kezelésére a [GStreamer](https://gstreamer.freedesktop.org)használatával kerül sor. Licencelési okokból a GStreamer bináris fájlok nincsenek lefordítva és csatolva a Speech SDK-hoz. A fejlesztőknek több függőséget és beépülő modult kell telepíteniük, lásd: [telepítés Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) rendszeren vagy [Linux rendszeren való telepítés](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). A GStreamer bináris fájljainak a rendszer elérési útjában kell lenniük, így a beszédfelismerési SDK a futásidejű környezetekben is betöltheti a bináris fájlokat. Windows rendszeren például, ha a Speech SDK képes megtalálni `libgstreamer-1.0-0.dll` vagy `gstreamer-1.0-0.dll` (a legújabb GStreamer) a futtatókörnyezet során, az azt jelenti, hogy a GStreamer bináris fájljai a rendszer elérési útjában vannak.

