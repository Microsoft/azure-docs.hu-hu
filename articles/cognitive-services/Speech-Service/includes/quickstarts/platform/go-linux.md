---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 29433e7ecaa4135c790f7cafb36d56c4c07ac684
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096997"
---
Ez az útmutató bemutatja, hogyan telepítheti a Linux rendszerhez készült [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) -t

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Rendszerkövetelmények

Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8)

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

* GCC
* [Go Binary (1,13 vagy újabb)](https://golang.org/dl/)

* A támogatott linuxos platformokhoz telepíteni kell bizonyos kódtárakat ( `libssl` a Secure Sockets Layer támogatásához és a hangalapú `libasound2` támogatáshoz). A könyvtárak megfelelő verzióinak telepítéséhez szükséges parancsokért tekintse meg az alábbi disztribúciót.

   * Ubuntu rendszeren:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Debian 9 rendszeren:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

   * RHEL/CentOS 8 rendszeren:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> A RHEL/CentOS 8 rendszeren kövesse az [OpenSSL Linux rendszerhez való konfigurálásának](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)utasításait.

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Go-környezet konfigurálása

1. Mivel a kötések támaszkodnak `cgo` , meg kell adnia a környezeti változókat, hogy a go megtalálja az SDK-t:

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. Emellett az SDK-t is tartalmazó alkalmazások futtatásához tájékoztatni kell az operációs rendszert, ahol a következő könyvtárakat kell megkeresni:

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<arch>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>További lépések

[!INCLUDE [windows](../quickstart-list-go.md)]