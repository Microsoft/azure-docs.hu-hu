---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 10e7db521781c21b3fe5feda0880fee7e603afab
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128324"
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
> - A RHEL/CentOS 7 rendszeren kövesse a [RHEL/CentOS 7 for SPEECH SDK konfigurálására](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)vonatkozó utasításokat.
> - A RHEL/CentOS 8 rendszeren kövesse az [OpenSSL Linux rendszerhez való konfigurálásának](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)utasításait.

> [!IMPORTANT]
> A RHEL/CentOS 7 rendszeren a Speech SDK nem támogatja az Opus hangkódolási formátumot.

---