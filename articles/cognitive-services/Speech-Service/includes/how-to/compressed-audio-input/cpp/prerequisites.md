---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: d555322c0fd4067a4232e53387d811de7b50a8f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103417719"
---
A tömörített hang kezelésére a [GStreamer](https://gstreamer.freedesktop.org)használatával kerül sor. Licencelési okokból a GStreamer bináris fájlok nincsenek lefordítva és csatolva a Speech SDK-hoz. A fejlesztőknek több függőséget és beépülő modult kell telepíteniük.

> [!NOTE]
> Tekintse meg a [támogatott Linux-disztribúciók és a megcélzott architektúrák](~/articles/cognitive-services/speech-service/speech-sdk.md)listáját.

# <a name="ubuntudebian"></a>[Ubuntu/Debian](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rhelcentos"></a>[RHEL/CentOS](#tab/centos)

```sh
sudo yum install gstreamer1 \
gstreamer1-plugins-base \
gstreamer1-plugins-good \
gstreamer1-plugins-bad-free \
gstreamer1-plugins-ugly-free
```

> [!NOTE]
> - A RHEL/CentOS 7 és a RHEL/CentOS 8 esetében, ha "bármilyen" tömörített formátumot használ, a rendszer több GStreamer beépülő modult kell telepíteni, ha a stream Media Format beépülő modul nem a fenti telepített beépülő moduloknál van. 
> - A RHEL/CentOS 7 rendszeren kövesse a [RHEL/CentOS 7 for SPEECH SDK konfigurálására](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)vonatkozó utasításokat.
> - A RHEL/CentOS 8 rendszeren kövesse az [OpenSSL Linux rendszerhez való konfigurálásának](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)utasításait.


---