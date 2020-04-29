---
title: A RHEL/CentOS 7 – Speech szolgáltatás konfigurálása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan konfigurálhatja a RHEL/CentOS 7-et, hogy a Speech SDK használható legyen.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80639162"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>A RHEL/CentOS 7 beállítása a Speech SDK-hoz

Az Red Hat Enterprise Linux (RHEL) 8 x64 és CentOS 8 x64-et hivatalosan a Speech SDK verziójának 1.10.0 és újabb verziói támogatják. Az RHEL/CentOS 7 x64-es verzióban is használhatja a Speech SDK-t, de ehhez frissítenie kell a C++-fordítót (C++-fejlesztés esetén) és a megosztott C++ futtatókörnyezeti könyvtárat a rendszeren.

A C++ fordító verziójának vizsgálatához futtassa a következőt:

```bash
g++ --version
```

Ha a fordító telepítve van, a kimenetnek a következőhöz hasonlóan kell kinéznie:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

Ez az üzenet azt jeleníti meg, hogy a GCC fő 4-es verziója telepítve van. Ez a verzió nem támogatja teljes mértékben a C++ 11 szabványt, amelyet a Speech SDK használ. A C++ program ezen GCC-verzióval való fordítására tett kísérlet során a beszédfelismerési SDK-fejlécek fordítási hibákat eredményeznek.

Fontos továbbá a megosztott C++ futtatókörnyezeti függvénytár (libstdc + +) verziójának a megkeresése is. A legtöbb Speech SDK natív C++ kódtáraként lett implementálva, ami azt jelenti, hogy az alkalmazások fejlesztéséhez használt nyelvtől függetlenül a libstdc + + függvénytől függ.

A libstdc + + helyének a rendszeren való megtalálásához futtassa a következőt:

```bash
ldconfig -p | grep libstdc++
```

A Vanilla RHEL/CentOS 7 (x64) kimenete a következő:

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

Ezen üzenet alapján a következő paranccsal tekintheti meg a verziók definícióit:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

A kimenetnek a következőket kell tennie:

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

A Speech SDK használatához a **CXXABI_1.3.9** és a **GLIBCXX_3.4.21**szükséges. Ezt az információt a Linux-csomagból a Speech SDK könyvtáraiban futtatva `ldd libMicrosoft.CognitiveServices.Speech.core.so` érheti el.

> [!NOTE]
> Azt javasoljuk, hogy a rendszerre telepített GCC-verziónak legalább **5.4.0**kell lennie, a megfelelő futásidejű könyvtárakkal.

## <a name="example"></a>Példa

Ez egy minta-parancs, amely bemutatja, hogyan konfigurálhatja a RHEL/CentOS 7 x64-et fejlesztésre (C++, C#, Java, Python) a Speech SDK 1.10.0 vagy újabb verziójára:

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free

# Build GCC 5.4.0 and runtimes and install them under /usr/local
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip

# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Speech SDK ismertetése](speech-sdk.md)
