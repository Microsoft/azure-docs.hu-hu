---
title: Adatsíkok elérése az Azure AD-vel, RBAC
description: Adatsíkon való hozzáférés Azure Active Directory szerepköralapú hozzáférés-vezérléssel.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 2909d40fbc7cb5b310ea17f17a6e683ce47638ce
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220120"
---
# <a name="access-the-data-plane-with-azure-active-directory-and-role-based-access-control"></a>Az adatsík elérése Azure Active Directory és szerepkör-alapú Access Control

Ez a cikk bemutatja, hogyan érheti el az Azure Spring Cloud config Server és a szolgáltatás beállításjegyzékbeli végpontját Azure Active Directory (HRE) szerepköralapú hozzáférés-vezérlés (RBAC) használatával.

## <a name="assign-role-to-aad-usergroup-msi-or-service-principal"></a>Szerepkör kiosztása HRE felhasználó/csoport, MSI vagy egyszerű szolgáltatásnév számára

A HRE és a RBAC használatához az *Azure Spring Cloud Adatolvasó* szerepkört a következő eljárással kell hozzárendelni egy felhasználóhoz, csoporthoz vagy egyszerű szolgáltatáshoz:

1. Nyissa meg a szolgáltatási példány szolgáltatás áttekintés lapját.

2. Kattintson a **Access Control (iam)** elemre a hozzáférés-vezérlés panel megnyitásához.

3. Kattintson a **Hozzáadás** gombra, és **adja hozzá a szerepkör-hozzárendeléseket** (az engedélyezéshez szükség lehet a hozzáadáshoz).

4. Keresse meg és válassza ki az *Azure Spring Cloud Adatolvasót* a **szerepkör** alatt.
5. Rendeljen hozzá `User, group, or service principal` vagy `User assigned managed identity` a felhasználó típusa szerint. Keresse meg és válassza ki a felhasználót.  
6. Kattintson a következőre: `Save`

   ![szerepkör kiosztása](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-data-plane"></a>Hozzáférés az adatsíkon

Miután egy HRE-felhasználó hozzárendelte az *Azure Spring Cloud Adatolvasó* szerepkört, az ügyfelek bejelentkezhetnek az Azure CLI-be a felhasználó, a szolgáltatásnév vagy a felügyelt identitás használatával.  Lásd: az [Azure CLI hitelesítése](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) hozzáférési jogkivonat beszerzéséhez.  Ezután használja a következő parancsokat.

```azurecli
az login
az account get-access-token
```

Jelenleg a CLI támogatja a konfigurációs kiszolgáló és a szolgáltatás beállításjegyzékének alapértelmezett végpontját. További információ: [üzemi használatra kész végpontok](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints). 

Az ügyfelek a konfigurációs kiszolgáló és a szolgáltatás beállításjegyzékének támogatott végpontjai teljes listáját is megkapják a végpontok elérésével:
* *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/*
* *https://SERVICE_NAME.Root_Endpoint/config/actuator/* 

A fejlécben található hozzáférési jogkivonat lehetővé teszi az engedélyezést. Csak a "GET" metódus támogatott.

Például egy olyan végpont eléréséhez, mint *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/health* Az Eureka állapotának megtekintése.

A különböző gyökérszintű végpontok a különböző Felhőbeli típusok szerint jelennek meg.

| Felhőbeli          | Gyökérszintű végpont              |
| -------------- | -------------------------- |
| Nyilvános         | svc.azuremicroservices.io  |
| Mooncake/Kína | svc.microservices.azure.cn |

Ha a válasz *401 nem engedélyezett*, ellenőrizze, hogy sikerült-e a szerepkör hozzárendelése.  A szerepkör érvénybe léptetéséhez több percet is igénybe vehet, vagy ellenőrizheti, hogy a hozzáférési jogkivonat nem járt-e le.

## <a name="next-steps"></a>Következő lépések
* [Az Azure CLI hitelesítése](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Éles használatra kész végpontok](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Lásd még
* [Szerepkörök és engedélyek létrehozása](spring-cloud-howto-permissions.md)