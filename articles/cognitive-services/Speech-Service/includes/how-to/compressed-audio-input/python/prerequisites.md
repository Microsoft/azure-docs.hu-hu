---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: b8dda0347e5713ef35705425b54f29a110803488
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821519"
---
A tömörített hang kezelésére a [GStreamer](https://gstreamer.freedesktop.org)használatával kerül sor. Licencelési okokból a GStreamer bináris fájlok nincsenek lefordítva és csatolva a Speech SDK-hoz. A fejlesztőknek több függőséget és beépülő modult kell telepíteniük, lásd: [telepítés Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) rendszeren vagy [Linux rendszeren való telepítés](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). A GStreamer bináris fájljainak a rendszer elérési útjában kell lenniük, így a beszédfelismerési SDK a futásidejű környezetekben is betöltheti a bináris fájlokat. Windows rendszeren például, ha a Speech SDK képes megtalálni a `libgstreamer-1.0-0.dll` futtatókörnyezetet, az azt jelenti, hogy a GStreamer bináris fájlok a rendszer elérési útjában vannak.

