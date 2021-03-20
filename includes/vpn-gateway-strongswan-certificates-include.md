---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1c2525b352c25f470814ce909a8d10ff821d9e32
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "70961599"
---
A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány előállítása.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

Nyomtassa ki a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt Base64 formátumban. Ez az Azure által támogatott formátum. Ezt a tanúsítványt a [P2S konfigurációs lépéseinek](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)részeként feltölti az Azure-ba.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```

A felhasználói tanúsítvány előállítása.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```

A felhasználói tanúsítványt tartalmazó P12-köteg létrehozása. Ez a csomag a következő lépésekben lesz használatban az ügyfél konfigurációs fájljainak használatakor.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```