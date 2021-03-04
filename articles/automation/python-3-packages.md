---
title: Python 3 csomagok kezelése Azure Automation
description: Ez a cikk azt ismerteti, hogyan kezelhető a Python 3 csomag (előzetes verzió) a Azure Automationban.
services: automation
ms.subservice: process-automation
ms.date: 02/19/2021
ms.topic: conceptual
ms.openlocfilehash: fd4d8ee92b670bc2544619a0dce16a26d9342c13
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122034"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Python 3 csomag (előzetes verzió) kezelése Azure Automation

Azure Automation lehetővé teszi a Python 3 runbookok (előzetes verzió) futtatását az Azure-ban és a Linux hibrid Runbook-feldolgozókon. A runbookok egyszerűsítése érdekében a Python-csomagokat használhatja a szükséges modulok importálásához. Egyetlen csomag importálásához tekintse meg a [csomagok importálása](#import-a-package)című témakört. Ha több csomaggal szeretne csomagokat importálni, tekintse meg a csomagok [importálása függőségekkel](#import-a-package-with-dependencies)című témakört. Ez a cikk bemutatja, hogyan kezelheti és használhatja a Python 3 csomagokat (előzetes verzió) a Azure Automationban.

## <a name="import-a-package"></a>Csomag importálása

Az Automation-fiókban válassza a **Python-csomagok** elemet a **megosztott erőforrások** területen. válassza **a + Python-csomag hozzáadása** elemet.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="A Python 3 csomagok oldalának képernyőképe a bal oldali menüben a Python 3 csomagot jeleníti meg, és egy kiemelt Python 2 csomagot ad hozzá.":::

A **Python-csomag hozzáadása** lapon válassza a **Python 3** elemet a **verzióhoz**, és válassza ki a feltölteni kívánt helyi csomagot. A csomag lehet egy **. WHL** vagy **. tar. gz** fájl. Ha a csomag ki van választva, kattintson az **OK gombra** a feltöltéshez.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="A képernyőképen a Python 3 csomag hozzáadása oldal jelenik meg, amelyhez feltöltött tar. gz fájl van kijelölve.":::

A csomag importálása után a rendszer az Automation-fiók Python-csomagok lapján, a **Python 3 csomagok (előzetes verzió)** lapon jelenik meg. Ha el kell távolítania egy csomagot, válassza ki a csomagot, és kattintson a **Törlés** gombra.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="A csomag importálása után a képernyőkép a Python 3 csomagok oldalát jeleníti meg.":::

### <a name="import-a-package-with-dependencies"></a>Csomag importálása függőségekkel

A Python 3 csomag és a hozzá tartozó függőségek importálásához importálja a következő Python-szkriptet egy Python 3 runbook, majd futtatja azt.

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>A szkript importálása egy runbook
A runbook importálásával kapcsolatos információkért lásd: [Runbook importálása a Azure Portalból](manage-runbooks.md#import-a-runbook-from-the-azure-portal). Másolja a fájlt a GitHubról a Storage-ba, hogy a portál hozzáférhessen az importálás futtatása előtt.

A **Runbook importálása** lap alapértelmezett értéke a runbook neve, hogy egyezzen a parancsfájl nevével. Ha hozzáfér a mezőhöz, módosíthatja a nevet. A **Runbook típusa** alapértelmezés szerint a **Python 2** lehet. Ha igen, ügyeljen arra, hogy a **Python 3**-ra váltson.

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="A képernyőképen a Python 3 runbook importálási lapja látható.":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>A runbook végrehajtása a csomag és a függőségek importálásához

A runbook létrehozása és közzététele után futtassa azt a csomag importálásához. A runbook végrehajtásával kapcsolatos részletekért tekintse meg a [Runbook elindítása a Azure Automationben](start-runbooks.md) című témakört.

A parancsfájlnak ( `import_py3package_from_pypi.py` ) a következő paramétereket kell megadnia.

| Paraméter | Leírás |
|---------------|-----------------|
|subscription_id | Az Automation-fiók előfizetés-azonosítója |
| resource_group | Azon erőforráscsoport neve, amelyben az Automation-fiók definiálva van |
| automation_account | Automation-fiók neve |
| module_name | Az importálni kívánt modul neve `pypi.org` |

A paraméterek runbookok használatával történő használatáról további információt a [runbook paramétereinek](start-runbooks.md#work-with-runbook-parameters)használata című témakörben talál.

## <a name="use-a-package-in-a-runbook"></a>Csomag használata runbook

Az importált csomaggal használhatja azt egy runbook. Adja hozzá a következő kódot az Azure-előfizetésben található összes erőforráscsoport listázásához.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

## <a name="next-steps"></a>Következő lépések

Python-runbook előkészítéséhez tekintse meg [a Python-Runbook létrehozása](learn/automation-tutorial-runbook-textual-python-3.md)című témakört.
