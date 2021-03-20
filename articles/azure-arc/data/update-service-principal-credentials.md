---
title: Egyszerű szolgáltatásnév hitelesítő adatainak frissítése
description: Egyszerű szolgáltatásnév hitelesítő adatainak frissítése
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: mikeray
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 1c7ccec6228a6dcfb457bda8f6ecd384775d4b27
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669542"
---
# <a name="update-service-principal-credentials"></a>Egyszerű szolgáltatásnév hitelesítő adatainak frissítése

Az egyszerű szolgáltatás hitelesítő adatainak megváltozásakor frissítenie kell a titkos kulcsokat az adatkezelőben.

Ha például központilag telepítette az adatkezelőt az egyszerű szolgáltatásnév, az ügyfél-azonosító és az ügyfél titkos értékeinek egy adott halmazával, majd egy vagy több ilyen értéket módosít, akkor frissítenie kell az adatvezérlőn található titkos kódokat.  A következő útmutatást követve frissítheti a bérlői azonosítót, az ügyfél-azonosítót vagy az ügyfél titkos kulcsát. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="background"></a>Háttér

Az egyszerű szolgáltatás a [szolgáltatásnév létrehozásakor](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)lett létrehozva. 

## <a name="steps"></a>Lépések

1. A szolgáltatás egyszerű titkának elérése az alapértelmezett szerkesztőben.

   ```console
   kubectl edit secret/upload-service-principal-secret -n <name of namespace>
   ```

   Ha például a szolgáltatás egyszerű titkát szeretné szerkeszteni a névtér egyik adatvezérlőjére `arc` , futtassa a következő parancsot:

   ```console
   kubectl edit secret/upload-service-principal-secret -n arc
   ```

   A `kubecl edit` parancs megnyitja a hitelesítő adatok. YML fájlt az alapértelmezett szerkesztőben. 


1. Az egyszerű szolgáltatás titkos kódjának szerkesztése. 

   Az alapértelmezett szerkesztőben cserélje le az adatok szakaszban található értékeket a frissített hitelesítő adatokkal.

   Ilyenek például a következők:

   ```console
   # Please edit the object below. Lines beginning with a '#' will be ignored,
   # and an empty file will abort the edit. If an error occurs while saving this file will be
   # reopened with the relevant failures.
   #
   apiVersion: v1
   data:
     authority: aHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29t
     clientId: NDNiNDcwYrFTGWYzOC00ODhkLTk0ZDYtNTc0MTdkN2YxM2Uw
     clientSecret: VFA2RH125XU2MF9+VVhXenZTZVdLdECXFlNKZi00Lm9NSw==
     tenantId: NzJmOTg4YmYtODZmMRFVBGTJLSATkxYWItMmQ3Y2QwMTFkYjQ3
   kind: Secret
   metadata:
     creationTimestamp: "2020-12-02T05:02:04Z"
     name: upload-service-principal-secret
     namespace: arc
     resourceVersion: "7235659"
     selfLink: /api/v1/namespaces/arc/secrets/upload-service-principal-secret
     uid: 7fb693ff-6caa-4a31-b83e-9bf22be4c112
   type: Opaque
   ```

   Szerkessze a `clientID` `clientSecret` és/vagy a `tenantID` megfelelő értékeket. 

> [!NOTE]
>Az értékeknek Base64 kódolással kell rendelkezniük. A többi tulajdonságot ne szerkessze.

Ha helytelen érték van megadva a (z) számára `clientId` , `clientSecret` vagy `tenantID` a rendszer a következő hibaüzenetet fogja látni a `control-xxxx` Pod/Controller tároló naplóiban:

```output
YYYY-MM-DD HH:MM:SS.mmmm | ERROR | [AzureUpload] Upload task exception: A configuration issue is preventing authentication - check the error message from the server for details.You can modify the configuration in the application registration portal. See https://aka.ms/msal-net-invalid-client for details.  Original exception: AADSTS7000215: Invalid client secret is provided.
```



## <a name="next-steps"></a>Következő lépések

[Felhasználónév és jelszó lekérése az Arc-adatkezelőhöz való kapcsolódáshoz](retrieve-the-username-password-for-data-controller.md)

[Egyszerű szolgáltatás létrehozása](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
