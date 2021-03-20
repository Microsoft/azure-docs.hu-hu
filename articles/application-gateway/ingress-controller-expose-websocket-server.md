---
title: WebSocket-kiszolgáló közzététele Application Gateway
description: Ez a cikk tájékoztatást nyújt arról, hogyan tehet elérhetővé egy WebSocket-kiszolgálót az AK-fürtök bejövő adatvezérlési szolgáltatásával Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 68d4ff7e4617136e4c58ce672f34de56e46f0229
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85207787"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>WebSocket-kiszolgáló közzététele Application Gateway

A Application Gateway v2 dokumentációjában leírtaknak megfelelően [a WebSocket és a http/2 protokollok natív támogatást biztosítanak](features.md#websocket-and-http2-traffic). Vegye figyelembe, hogy mind a Application Gateway, mind a Kubernetes beáramlása esetén nincs felhasználó által konfigurálható beállítás a WebSocket-támogatás szelektív engedélyezéséhez vagy letiltásához.

Az alábbi Kubernetes-telepítési YAML a WebSocket-kiszolgáló üzembe helyezésének minimális konfigurációját mutatja be, amely megegyezik egy normál webkiszolgáló üzembe helyezésével:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Tekintettel arra, hogy minden előfeltétel teljesült, és rendelkezik egy Application Gateway Kubernetes bevezetéssel, a fenti üzembe helyezés a Application Gateway nyilvános IP-címének és a tartományának 80-as portján elérhető WebSocket-kiszolgálóval fog rendelkezni `ws.contoso.com` .

A következő cURL-parancs teszteli a WebSocket-kiszolgáló telepítését:
```shell
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket-állapotú tesztek

Ha az üzemelő példány nem határozza meg explicit módon az állapot-mintavételt, Application Gateway megkísérli egy HTTP GET-t a WebSocket Server-végponton.
A kiszolgáló megvalósításának függvényében ([itt az egyik szeretjük](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) WebSocket-specifikus fejlécekre lehet szükség (például `Sec-Websocket-Version` ).
Mivel Application Gateway nem ad hozzá WebSocket-fejléceket, a WebSocket-kiszolgálóról érkező Application Gateway állapot-mintavételi válasz valószínűleg a következő lesz: `400 Bad Request` .
Ennek eredményeképpen Application Gateway a hüvelyét nem megfelelő állapotba fogja megjelölni, ami végül a `502 Bad Gateway` WebSocket-kiszolgáló felhasználói számára eredményez.
Ennek elkerüléséhez előfordulhat, hogy egy HTTP GET kezelőt kell hozzáadnia az állapot-ellenőrzési kiszolgálóhoz (például a visszaadott `/health` értékhez `200 OK` ).
