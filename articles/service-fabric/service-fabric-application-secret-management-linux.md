---
title: Titkosítási tanúsítvány beállítása Linux-fürtökön
description: Megtudhatja, hogyan állíthat be titkosítási tanúsítványt, és hogyan titkosíthatja a titkokat Linux-fürtökön.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: b8e0a19e3f654fc561e7c7e26c6a2da463e24d5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "78969032"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Titkosítási tanúsítvány beállítása és a titkok titkosítása Linux-fürtökön
Ez a cikk bemutatja, hogyan állíthat be titkosítási tanúsítványt, és hogyan titkosíthatja a titkokat a Linux-fürtökön. Windows-fürtök esetén lásd: [titkosítási tanúsítvány beállítása és a titkok titkosítása Windows-fürtökön][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Titkosítási-tanúsítvány beszerzése
A titkosítási-tanúsítványokat szigorúan a szolgáltatás Settings.xml és [környezeti változói][environment-variables-link] által használt [Paraméterek][parameters-link] titkosítására és visszafejtésére használják a szolgáltatás ServiceManifest.xml. Nem használatos a titkosított szöveg hitelesítéséhez vagy aláírásához. A tanúsítványnak meg kell felelnie a következő követelményeknek:

* A tanúsítványnak tartalmaznia kell egy titkos kulcsot.
* A tanúsítvány használatának tartalmaznia kell az adatok titkosítási (10), és nem tartalmazhat kiszolgálói hitelesítést vagy ügyfél-hitelesítést.

  Például a következő parancsokat használhatja a szükséges tanúsítvány létrehozásához az OpenSSL használatával:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>A tanúsítvány telepítése a fürtben
A tanúsítványt a fürt mindegyik csomópontján telepíteni kell `/var/lib/sfcerts` . Az a felhasználói fiók, amelyben a szolgáltatás fut (alapértelmezés szerint sfuser) **olvasási hozzáféréssel kell rendelkeznie** a telepített tanúsítványhoz (azaz `/var/lib/sfcerts/TestCert.pem` az aktuális példához).

## <a name="encrypt-secrets"></a>Titkos kulcsok titkosítása
A titkos kód titkosításához a következő kódrészlet használható. Ez a kódrészlet csak az értéket titkosítja; **nem** írja alá a titkosítási szöveget. Ugyanazt a titkosítási-tanúsítványt **kell használnia** , amely a fürtbe van telepítve, hogy rejtjelezett hozzon létre a titkos értékekhez.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt | tr -d '\n' > plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
Az eredményül kapott Base-64 kódolású karakterlánc kimenete encrypted.txt tartalmazza a titkos rejtjelezett, valamint a titkosításhoz használt tanúsítvánnyal kapcsolatos információkat is. Az érvényességét az OpenSSL-vel való visszafejtéssel ellenőrizheti.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [határozhat meg titkosított titkot egy alkalmazásban.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
