---
title: Alkalmazás áthelyezése másik régióba
description: Megtudhatja, hogyan helyezhet át App Service erőforrásokat az egyik régióból a másikba.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 87a2da31802a8b6858e875c23ef1dbd2d6d006bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86524854"
---
# <a name="move-an-app-service-resource-to-another-region"></a>App Service erőforrás áthelyezése másik régióba

Ez a cikk azt ismerteti, hogyan helyezhet át App Service erőforrásokat egy másik Azure-régióba. Az erőforrásokat több okból is áthelyezheti egy másik régióba. Ha például egy új Azure-régió előnyeit szeretné kihasználni, csak meghatározott régiókban elérhető szolgáltatásokat vagy szolgáltatásokat telepíthet, a belső házirend-és irányítási követelmények teljesítéséhez, vagy a kapacitás megtervezésének követelményeire reagálva.

App Service erőforrások régió-specifikusak, és nem helyezhetők át régiók között. Létre kell hoznia a meglévő App Service erőforrásainak másolatát a célként megadott régióban, majd át kell helyeznie a tartalmat az új alkalmazásba. Ha a forrásoldali alkalmazás egyéni tartományt használ, a befejezéskor [áttelepítheti azt az új alkalmazásba a cél régióban](manage-custom-dns-migrate-domain.md) .

Annak érdekében, hogy az alkalmazás könnyebben átmásolható legyen, [egyetlen app Service alkalmazást is klónozott](app-service-web-app-cloning.md) egy másik régióban lévő app Service-csomagba, de [korlátozásokkal](app-service-web-app-cloning.md#current-restrictions)rendelkezik, különösen, ha nem támogatja a Linux-alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy a App Service alkalmazás abban az Azure-régióban található, amelyről át kívánja helyezni.
- Győződjön meg arról, hogy a célként megadott régió támogatja a App Service és az összes kapcsolódó szolgáltatást, amelynek erőforrásait át kívánja helyezni.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Előkészítés

Azonosítsa az összes jelenleg használt App Service-erőforrást. Például:

- App Service-alkalmazások
- [App Service-csomagok](overview-hosting-plans.md)
- [Üzembe helyezési pontok](deploy-staging-slots.md)
- [Az Azure-ban vásárolt egyéni tartományok](manage-custom-dns-buy-domain.md)
- [SSL-tanúsítványok](configure-ssl-certificate.md)
- [Azure Virtual Network integráció](web-sites-integrate-with-vnet.md)
- [Hibrid kapcsolatok](app-service-hybrid-connections.md).
- [Felügyelt identitások](overview-managed-identity.md)
- [Biztonsági mentési beállítások](manage-backup.md)

Bizonyos erőforrások, például az importált tanúsítványok vagy a hibrid kapcsolatok, más Azure-szolgáltatásokkal való integrációt tartalmaznak. Az erőforrások régiók közötti áthelyezésével kapcsolatos információkért tekintse meg a megfelelő szolgáltatások dokumentációját.

## <a name="move"></a>Áthelyezés

1. Készítsen [biztonsági másolatot a forrásoldali alkalmazásról](manage-backup.md).
1. [Hozzon létre egy alkalmazást egy új App Service tervben a célként megadott régióban](app-service-plan-manage.md#create-an-app-service-plan).
2. [A cél alkalmazás biztonsági másolatának visszaállítása](web-sites-restore.md)
2. Ha egyéni tartományt használ, [kötést megelőző jelleggel a célalkalmazás számára](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) , `awverify.` és [engedélyezze a tartományt a célalkalmazás](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app)alkalmazásban.
3. Konfigurálja a célalkalmazás minden más elemét, hogy megegyezzen a forrásoldali alkalmazással, és ellenőrizze a konfigurációt.
4. Ha készen áll arra, hogy az egyéni tartomány a célalkalmazás felé mutasson, adja meg újra [a tartománynevet](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Forrás erőforrásainak törlése

A forrásoldali alkalmazás és App Service terv törlése. [A nem ingyenes csomagban lévő App Service terv díjat számít fel, még akkor is, ha nem fut alkalmazás.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Következő lépések

[Alkalmazások klónozásának Azure App Service a PowerShell használatával](app-service-web-app-cloning.md)