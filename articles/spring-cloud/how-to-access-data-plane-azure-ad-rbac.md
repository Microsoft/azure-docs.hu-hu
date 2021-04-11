---
title: A konfigurációs kiszolgáló és a szolgáltatás beállításjegyzékének elérése
titleSuffix: Azure Spring Cloud
description: A konfigurációs kiszolgáló és a szolgáltatás beállításjegyzékbeli végpontjának elérése Azure Active Directory szerepköralapú hozzáférés-vezérléssel.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 16433d5b148d7bc441e375591c64af497cd7b8de
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505332"
---
# <a name="access-config-server-and-service-registry"></a>A konfigurációs kiszolgáló és a szolgáltatás beállításjegyzékének elérése

Ez a cikk bemutatja, hogyan érheti el a Spring Cloud config Servert és a Spring Cloud Service-beállításjegyzéket az Azure Spring Cloud által felügyelt Azure Active Directory (Azure AD) szerepköralapú hozzáférés-vezérlés (RBAC) használatával.

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Szerepkör társítása az Azure AD-felhasználóhoz/csoporthoz, MSI-hez vagy egyszerű szolgáltatáshoz

Az Azure AD és a RBAC használatához az *Azure Spring Cloud Adatolvasó* szerepkört a következő eljárással kell hozzárendelni egy felhasználóhoz, csoporthoz vagy egyszerű szolgáltatáshoz:

1. Nyissa meg a szolgáltatási példány szolgáltatás áttekintés lapját.

2. Kattintson a **Access Control (iam)** elemre a hozzáférés-vezérlés panel megnyitásához.

3. Kattintson a **Hozzáadás** gombra, és **adja hozzá a szerepkör-hozzárendeléseket** (az engedélyezéshez szükség lehet a hozzáadáshoz).

4. Keresse meg és válassza ki az *Azure Spring Cloud Adatolvasót* a **szerepkör** alatt.
5. Rendeljen hozzá `User, group, or service principal` vagy `User assigned managed identity` a felhasználó típusa szerint. Keresse meg és válassza ki a felhasználót.  
6. Kattintson a következőre: `Save`

   ![szerepkör kiosztása](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-config-server-and-service-registry-endpoints"></a>A konfigurációs kiszolgáló és a szolgáltatás beállításjegyzékbeli végpontjának elérése

Az Azure Spring Cloud-Adatolvasó szerepkör hozzárendelése után az ügyfelek hozzáférhetnek a Spring Cloud config-kiszolgálóhoz és a Spring Cloud Service beállításjegyzékbeli végpontokhoz. Használja az alábbi eljárásokat:

1. Hozzáférési jogkivonat beszerzése. Miután egy Azure AD-felhasználó hozzá lett rendelve az Azure Spring Cloud Adatolvasó szerepkörhöz, az ügyfelek az alábbi parancsokkal jelentkezhetnek be az Azure CLI-be a felhasználó, a szolgáltatásnév vagy a felügyelt identitás használatával hozzáférési jogkivonat beszerzéséhez. Részletekért lásd: az [Azure CLI hitelesítése](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). 

    ```azurecli
    az login
    az account get-access-token
    ```
2. Állítsa össze a végpontot. A Spring Cloud config Server és az Azure Spring Cloud által kezelt Spring Cloud Service-beállításjegyzék alapértelmezett végpontját támogatjuk. További információ: [üzemi használatra kész végpontok](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints). A Spring Cloud config Server és az Azure Spring Cloud által kezelt Spring Cloud Service-beállításjegyzék támogatott végpontjai teljes listája a végpontok elérésével is elérhető.

    * *https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/*
    * *https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/* 

>[!NOTE]
> Ha az Azure China-t használja, cserélje le `*.azuremicroservices.io` a `*.microservices.azure.cn` -t, és [Tudjon meg többet](https://docs.microsoft.com/azure/china/resources-developer-guide#check-endpoints-in-azure).

3. Nyissa meg az összeállított végpontot a hozzáférési jogkivonattal. Az engedélyezéshez helyezze a hozzáférési tokent egy fejlécbe.  Csak a "GET" metódus támogatott.

    Például egy olyan végpont eléréséhez, mint *https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health* Az Eureka állapotának megtekintése.

    Ha a válasz *401 nem engedélyezett*, ellenőrizze, hogy sikerült-e a szerepkör hozzárendelése.  A szerepkör érvénybe léptetéséhez több percet is igénybe vehet, vagy ellenőrizheti, hogy a hozzáférési jogkivonat nem járt-e le.

## <a name="next-steps"></a>Következő lépések
* [Az Azure CLI hitelesítése](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Éles használatra kész végpontok](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Lásd még
* [Szerepkörök és engedélyek létrehozása](how-to-permissions.md)
