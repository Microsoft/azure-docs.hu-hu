---
title: Python 3 runbook létrehozása (előzetes verzió) Azure Automation
description: Ez a cikk egy egyszerű Python 3 runbook (előzetes verzió) létrehozását, tesztelését és közzétételét ismerteti.
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: tutorial
ms.openlocfilehash: e03eba29d634fafa9302441b17ca3a6bf6598556
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104977"
---
# <a name="tutorial-create-a-python-3-runbook-preview"></a>Oktatóanyag: Python 3 runbook létrehozása (előzetes verzió)

Ez az oktatóanyag végigvezeti a [Python 3 runbook](../automation-runbook-types.md#python-runbooks) (előzetes verzió) létrehozásán Azure Automationban. Python-runbookok fordítása a Python 2 és 3 alatt. Közvetlenül szerkesztheti a runbook kódját a Azure Portal szövegszerkesztő használatával.

> [!div class="checklist"]
> * Egyszerű Python-runbook létrehozása
> * A runbook tesztelése és közzététele
> * A runbook-feladatokhoz tartozó állapot futtatása és nyomon követése
> * A runbook frissítése egy Azure-beli virtuális gép runbook paraméterekkel való elindításához

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra lesz szüksége:

- Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Automation-fiók](../automation-security-overview.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.

- Egy Azure virtuális gép. Ebben az oktatóanyagban elindítja és leállítja ezt a gépet, ezért nem lehet éles virtuális gép.

- A Python 3 runbookok a Linux vagy a Windows Hybrid Runbook Worker használatával is futtathatja.Kövesse a [Linux](../automation-linux-hrw-install.md)   vagy a [Windows](../automation-windows-hrw-install.md)   Hybrid Runbook Worker telepítésének utasításait.A Linux hibrid feldolgozók esetében nincs konkrét előfeltétel. Ha azonban a Windows Hybrid Worker for Python 3 runbookok kívánja használni, konfigurálja a runbook Worker szolgáltatást futtató gépet, ha csak a Python 3-at támogatja, vagy ha a Python 2 és 3 együttes használata szükséges.

   * Ha *csak* a Python 2 vagy a Python 3 van telepítve, akkor azt a mappa elérési útját kell hozzáfűzni, amelyben python.exe található a **path** környezeti változóhoz. Ha például python.exe van a telepítési útvonalon `C:\Python` , akkor azt **a Path** környezeti változóhoz kell fűzni.

   * Ha a Python 2 és a Python 3 is telepítve van, és mindkét típusú runbookok szeretné futtatni, akkor konfigurálnia kell a következő környezeti változókat:

     * Python 2 – hozzon létre egy új környezeti változót `PYTHON_2_PATH` , és határozza meg a telepítési mappát. Ha például a telepítési mappa `C:\Python27` , akkor ezt az elérési utat hozzá kell adni a változóhoz.
     
     * Python 3 – hozzon létre egy új környezeti változót `PYTHON_3_PATH` , és határozza meg a telepítési mappát. Ha például a telepítési mappa `C:\Python3` , akkor ezt az elérési utat hozzá kell adni a változóhoz.

## <a name="create-a-new-runbook"></a>Új runbook létrehozása

Első lépésként hozzon létre egy egyszerű runbook, amely kiírja a *Hello World* szöveget.

1. Az Azure Portalon nyissa meg az Automation-fiókját.

    Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.

2. A runbookok listájának megnyitásához válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.

3. Új runbook létrehozásához válassza **a Runbook hozzáadása** elemet.

4. Adja meg a runbook a **MyFirstRunbook-Python** nevet.

5. Válassza a **Python 3** lehetőséget a **Runbook típushoz**.

6. Válassza a **Létrehozás** lehetőséget a runbook létrehozásához és a szöveges szerkesztő megnyitásához.

## <a name="add-code-to-the-runbook"></a>Kód hozzáadása a runbook

Most adjon hozzá egy egyszerű parancsot a szöveg kinyomtatásához `Hello World` .

```python
print("Hello World!")
```

A runbook mentéséhez kattintson a **Save (Mentés** ) gombra.

## <a name="test-the-runbook"></a>A runbook tesztelése

Mielőtt közzéteszi a runbookot, hogy éles üzemben is elérhető legyen, tesztelnie kell, hogy biztosan jól működik-e. A runbook tesztelésekor futtatja a Piszkozat verzióját, és interaktív módon megtekinti a kimenetét.

1. **A teszt** panel megnyitásához válassza a **teszt panelt** .

2. A teszt elindításához kattintson a **Start** gombra. Elvileg ez az egyetlen engedélyezett lehetőség.

3. Létrejön egy [forgatókönyv-feladat](../automation-runbook-execution.md), és megjelenik annak állapota.
   A feladatok állapota a **várólistán** kezdődik, ami azt jelzi, hogy a felhőben lévő runbook-feldolgozó elérhetővé válására vár. Akkor változik, **Ha egy feldolgozó elindít egy** feladatot, majd **futtatja** , amikor a runbook ténylegesen elindul.

4. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Ebben az esetben a következőnek kell megjelennie: `Hello World` .

5. A vászonra való visszatéréshez zárjuk be a **teszt** panelt.

## <a name="publish-and-start-the-runbook"></a>A runbook közzététele és elindítása

A létrehozott runbook még mindig Piszkozat módban van. Az éles környezetben való futtatás előtt közzé kell tennie. Amikor közzétesz egy runbook, felülírja a meglévő közzétett verziót a Piszkozat verziójával. Ebben az esetben még nem rendelkezik közzétett verzióval, mert most létrehozta a runbook.

1. Válassza a **Közzététel** lehetőséget a runbook közzétételéhez, majd az **Igen** gombra, amikor a rendszer kéri.

2. Ha balra görgetve megtekinti a runbook a **runbookok** lapon, a **közzétett** **szerzői állapot** jelenik meg.

3. Görgessen vissza jobbra a **MyFirstRunbook-Python3** panel megtekintéséhez.

   A felső beállítások lehetővé teszik a runbook elindítását, megtekintheti a runbook, vagy ütemezhet úgy, hogy később elkezdődjön.

4. Válassza az **Indítás** lehetőséget, majd kattintson az **OK gombra** , amikor megnyílik a **Start Runbook** panel.

5. Megnyílik a létrehozott runbook-feladatokhoz tartozó **feladatok** ablaktábla. Lezárhatja ezt a panelt, de hagyja nyitva, hogy megtekintse a feladatok előrehaladását.

6. A feladatok állapota megjelenik a **feladatok összegzése** területen, és megfelel a runbook tesztelésekor megtekintett állapotoknak.

7. Miután a runbook **állapota megjelenik,** válassza a **kimenet** lehetőséget. Megnyílik a **kimeneti** ablaktábla, ahol megtekintheti a következőt: `Hello World` .

8. Zárjuk be a **kimenet** panelt.

9. Válassza a **minden napló** lehetőséget a runbook-feladatokhoz tartozó **streamek** panel megnyitásához. Csak `Hello World` a kimeneti adatfolyamban látható. Ez a panel azonban megjeleníthet más streameket egy runbook-feladatokhoz, például a **részletes** és a **hibaüzenetet**, ha a runbook ezeket írja.

10. A **MyFirstRunbook-Python3** panelre való visszatéréshez zárjuk be a **streamek** panelt és a **feladatok** panelt.

11. Válassza a **feladatok** lehetőséget a runbook **feladatok** lapjának megnyitásához. Ezen a lapon a runbook által létrehozott összes feladat szerepel. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtatta a feladatot.

12. Ezt a feladatot kiválasztva megnyithatja a runbook indításakor megtekintett **feladatok** ablaktábláját. Ezen a panelen visszatérhet az időben, és megtekintheti az adott runbook létrehozott feladatok részleteit.

## <a name="add-authentication-to-manage-azure-resources"></a>Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen.
Ehhez a szkriptnek az Automation-fiókja hitelesítő adataival kell hitelesítenie magát.

> [!NOTE]
> Az Automation-fióknak létre kell hoznia az egyszerű szolgáltatásnév szolgáltatással, hogy egy futtató tanúsítvány legyen.
> Ha az Automation-fiókja nem az egyszerű szolgáltatással lett létrehozva, akkor a hitelesítés az [Azure felügyeleti kódtárak a Pythonhoz](/azure/python/python-sdk-azure-authenticate)című témakörben leírtak szerint.

1. Nyissa meg a szöveges szerkesztőt a **MyFirstRunbook-Python3** ablaktáblán a **Szerkesztés** lehetőség kiválasztásával.

2. Adja hozzá a következő kódot az Azure-hitelesítéshez:

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
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
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Kód hozzáadása a Python számítási ügyfél létrehozásához és a virtuális gép elindításához

Az Azure-beli virtuális gépekkel való együttműködéshez hozzon létre egy példányt a [Pythonhoz készült Azure számítási ügyfélből](/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

A virtuális gép elindításához használja a számítási ügyfelet. Adja hozzá a következő kódot a runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Ahol a a `MyResourceGroup` virtuális gépet tartalmazó erőforráscsoport neve, a pedig annak a virtuális gépnek a neve, `TestVM` amelyet el szeretne indítani.

Tesztelje és futtassa újra a runbook, és ellenőrizze, hogy elindul-e a virtuális gép.

## <a name="use-input-parameters"></a>Bemeneti paraméterek használata

A runbook jelenleg rögzített értékeket használ az erőforráscsoport és a virtuális gép neveihez. Most vegyünk fel egy kódot, amely beolvassa ezeket az értékeket a bemeneti paraméterekből.

A `sys.argv` paraméter értékeinek lekéréséhez használja a változót. Adja hozzá a következő kódot a runbook közvetlenül a többi `import` utasítás után:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Ezzel importálja a `sys` modult, és két változót hoz létre az erőforráscsoport és a virtuális gépek neveinek tárolására. Figyelje meg, hogy az argumentumok listája, a a `sys.argv[0]` parancsfájl neve, és a felhasználó nem adja meg a bemenetet.

Most módosíthatja a runbook utolsó két sorát úgy, hogy a bemeneti paraméterek értékeit a rögzített értékek használata helyett használja:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Amikor elindít egy Python-runbook a **teszt** ablaktábláról vagy egy közzétett runbook, megadhatja a paraméterek értékeit a **Start runbook** ( **Paraméterek**) lapon.

Miután megkezdte egy érték beírását az első mezőbe, egy másodperc jelenik meg, és így tovább, így tetszőleges számú paraméter-értéket adhat meg.

Az értékek a tömbben lévő szkriptek számára elérhetők `sys.argv` , ahogy az imént hozzáadott kódban.

Adja meg az erőforráscsoport nevét az első paraméter értékeként, valamint a második paraméter értékeként elindítani kívánt virtuális gép nevét.

![Adja meg a paraméter értékeit](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

A runbook elindításához kattintson **az OK gombra** . A runbook fut, és elindítja a megadott virtuális gépet.

## <a name="error-handling-in-python"></a>Hibakezelés a Pythonban

A következő konvenciókat is használhatja a különböző streamek lekéréséhez a Python-runbookok, beleértve a FIGYELMEZTETÉSeket, a hibákat és a HIBAKERESÉSi adatfolyamokat.

```python
print("Hello World output")
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

A következő példa egy blokkban használt egyezményt mutatja be `try...except` .

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print ('ERROR: Handling run-time error:', detail)
```

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni a runbook típusairól, azok előnyeiről és korlátairól, tekintse meg [Azure Automation runbook típusait](../automation-runbook-types.md).

- Az Azure Pythonban való fejlesztéséről az [Azure Python-fejlesztők](/azure/python/)számára című témakörben olvashat bővebben.

- A Python 3 runbookok megtekintéséhez tekintse meg a [Azure Automation githubat](https://github.com/azureautomation/runbooks/tree/master/Utility/Python).
