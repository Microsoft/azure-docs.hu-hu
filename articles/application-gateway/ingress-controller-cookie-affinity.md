---
title: Cookie-alapú affinitás engedélyezése Application Gateway
description: Ez a cikk tájékoztatást nyújt arról, hogyan engedélyezhető a cookie-alapú affinitás egy Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a7806cf9518090539ba540a9a522af1aae2691f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397416"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Cookie-alapú affinitás engedélyezése Application Gateway
Az [Azure Application Gateway dokumentációjában](./application-gateway-components.md#http-settings)leírtaknak megfelelően Application Gateway támogatja a cookie-alapú affinitást, ami azt jelenti, hogy a felhasználói munkamenetből egy adott kiszolgálóra irányuló, a feldolgozásra irányuló további forgalmat is átirányíthatja.

## <a name="example"></a>Példa
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```