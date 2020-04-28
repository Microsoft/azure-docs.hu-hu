---
title: Környezeti változók megadása a szolgáltatásokhoz
description: Bemutatja, hogyan használhatók környezeti változók a Service Fabric lévő alkalmazásokhoz
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: f4c4f2a1c140e3d0f181c4fd55482056f9f91b62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614315"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Környezeti változók megadása a Service Fabric szolgáltatásaihoz

Ez a cikk bemutatja, hogyan határozhat meg környezeti változókat egy szolgáltatás vagy tároló számára a Service Fabricban.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>A szolgáltatások környezeti változóinak megadására szolgáló eljárás

Ebben a példában egy környezeti változót állít be egy tárolóhoz. A cikk feltételezi, hogy már rendelkezik egy alkalmazás-és szolgáltatás-jegyzékfájlval.

1. Nyissa meg a ServiceManifest. xml fájlt.
2. A `CodePackage` elemben adjon hozzá egy új `EnvironmentVariables` elemet és egy `EnvironmentVariable` elemet az egyes környezeti változókhoz.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   A környezeti változók felülbírálása az alkalmazás jegyzékfájljában lehetséges.

3. Az alkalmazás jegyzékfájljában lévő környezeti változók felülbírálásához használja az `EnvironmentOverrides` elemet.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Környezeti változók dinamikus meghatározása a Docker-összeállítás használatával

A Service Fabric támogatja a [Docker-összeállítás használatát az üzembe helyezéshez](service-fabric-docker-compose.md#supported-compose-directives). A fájlok összeállításával környezeti változók is megadhatók a rendszerhéjból. Ez a viselkedés használható a kívánt környezeti értékek dinamikus helyettesítésére:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az ebben a cikkben tárgyalt alapfogalmakról, tekintse meg az [Alkalmazások kezelése több környezethez című cikket](service-fabric-manage-multiple-environment-app-configuration.md).

További információ a Visual Studióban elérhető egyéb alkalmazás-felügyeleti lehetőségekről: [Service Fabric alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md).
