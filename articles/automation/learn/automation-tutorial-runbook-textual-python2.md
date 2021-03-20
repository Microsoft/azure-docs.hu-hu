---
title: Python-runbook létrehozása a Azure Automationban
description: Ez a cikk egy egyszerű Python-runbook létrehozását, tesztelését és közzétételét ismerteti.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: e12327651165606e6a9b571d410f547a09a8ec8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87847924"
---
# <a name="tutorial-create-a-python-runbook"></a>Oktatóanyag: Python-runbook létrehozása

Ez az oktatóanyag végigvezeti egy [Python-runbook](../automation-runbook-types.md#python-runbooks) létrehozásán Azure Automationban. Python-runbookok fordítása a Python 2 alatt. Közvetlenül szerkesztheti a runbook kódját a Azure Portal szövegszerkesztő használatával.

> [!div class="checklist"]
> * Egyszerű Python-runbook létrehozása
> * A runbook tesztelése és közzététele
> * A runbook-feladatokhoz tartozó állapot futtatása és nyomon követése
> * A runbook frissítése egy Azure-beli virtuális gép runbook paraméterekkel való elindításához

> [!NOTE]
> Egy webhook használata a Python-runbook elindításához nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra lesz szüksége:

- Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Automation-fiók](../index.yml) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
- Egy Azure virtuális gép. Ezt a gépet leállítja és elindítja, tehát ne olyan virtuális gépet használjon, amely élesben működik.

## <a name="create-a-new-runbook"></a>Új runbook létrehozása

Első lépésként hozzon létre egy egyszerű runbook, amely kiírja a *Hello World* szöveget.

1. Az Azure Portalon nyissa meg az Automation-fiókját.

    Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.

2. A runbookok listájának megnyitásához válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.

3. Új runbook létrehozásához válassza **a Runbook hozzáadása** elemet.

4. Adja meg a runbook a **MyFirstRunbook-Python** nevet.

5. Válassza a **Python 2** lehetőséget a **Runbook típushoz**.

6. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="add-code-to-the-runbook"></a>Kód hozzáadása a runbook

Most adjon hozzá egy egyszerű parancsot a szöveg kinyomtatásához `Hello World` .

```python
print("Hello World!")
```

A runbook mentéséhez kattintson a **Save (Mentés** ) gombra.

## <a name="test-the-runbook"></a>A runbook tesztelése

Mielőtt közzéteszi a runbookot, hogy éles üzemben is elérhető legyen, tesztelnie kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a Piszkozat verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.

2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.

3. Létrejön egy [forgatókönyv-feladat](../automation-runbook-execution.md), és megjelenik annak állapota.
   A feladatok állapota várólistára változik, ami azt jelzi, hogy a felhőben lévő runbook-feldolgozó elérhetővé válására vár. A művelet akkor indul el, amikor egy feldolgozó kiállítja a feladatot, majd fut, amikor a runbook ténylegesen elindul.

4. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. Ebben az esetben a következőnek kell megjelennie: `Hello World` .

5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="publish-and-start-the-runbook"></a>A runbook közzététele és elindítása

A létrehozott runbook még mindig Piszkozat módban van. Az éles környezetben való futtatás előtt közzé kell tennie. Amikor közzétesz egy runbook, felülírja a meglévő közzétett verziót a Piszkozat verziójával. Ebben az esetben még nem rendelkezik közzétett verzióval, mert most létrehozta a runbook.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.

2. Ha balra görgetve megtekinti a runbook a Runbookok lapon, a **közzétett** **szerzői állapot** jelenik meg.

3. Görgessen vissza jobbra a **MyFirstRunbook-Python** ablaktáblájának megtekintéséhez.

   A felső beállítások lehetővé teszik a runbook elindítását, megtekintheti a runbook, vagy ütemezhet úgy, hogy később elkezdődjön.

4. Kattintson a **Start** gombra, majd az **OK** gombra a Runbook indítása panel megnyitásakor.

5. Megnyílik a létrehozott runbook-feladatokhoz tartozó feladatok ablaktábla. Lezárhatja ezt a panelt, de hagyja nyitva, hogy megtekintse a feladatok előrehaladását.

6. A feladatok állapota megjelenik a **feladatok összegzése** területen, és megfelel a runbook tesztelésekor megtekintett állapotoknak.

7. Miután a runbook állapota megjelenik, kattintson a **kimenet** elemre. Megnyílik a kimeneti ablaktábla, ahol megtekintheti a következőt: `Hello World` .

8. Zárja be a Kimenet panelt.

9. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. Csak `Hello World` a kimeneti adatfolyamban látható. Ez a panel azonban megjeleníthet más streameket egy runbook-feladatokhoz, például a részletes és a hibaüzenetet, ha a runbook ezeket írja.

10. A streamek ablaktábla és a feladatok ablaktábla bezárásával térjen vissza a MyFirstRunbook-Python panelre.

11. Kattintson a **feladatok** lehetőségre a runbook feladatok lapjának megnyitásához. Ezen a lapon a runbook által létrehozott összes feladat szerepel. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtatta a feladatot.

12. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már látott a runbook elindításakor. Ezen a panelen visszatérhet az időben, és megtekintheti az adott runbook létrehozott feladatok részleteit.

## <a name="add-authentication-to-manage-azure-resources"></a>Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen.
Ehhez a szkriptnek az Automation-fiókja hitelesítő adataival kell hitelesítenie magát. Ha segítségre van szüksége, használhatja a [Azure Automation segédprogram-csomagot](https://github.com/azureautomation/azure_automation_utility) , hogy könnyebb legyen az Azure-erőforrások hitelesítése és használata.

> [!NOTE]
> Az Automation-fióknak létre kell hoznia az egyszerű szolgáltatásnév szolgáltatással, hogy egy futtató tanúsítvány legyen.
> Ha az Automation-fiókja nem az egyszerű szolgáltatással lett létrehozva, akkor a hitelesítés az [Azure felügyeleti kódtárak a Pythonhoz](/azure/python/python-sdk-azure-authenticate)című témakörben leírtak szerint.

1. A MyFirstRunbook-Python ablaktáblán a **Szerkesztés** gombra kattintva nyissa meg a szöveges szerkesztőt.

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

Amikor elindít egy Python-runbook (akár a teszt ablaktáblán, akár közzétett runbook), megadhatja a paraméterek értékeit a Start Runbook ( **Paraméterek**) lapon.

Miután megkezdte egy érték beírását az első mezőbe, egy másodperc jelenik meg, és így tovább, így tetszőleges számú paraméter-értéket adhat meg.

Az értékek a tömbben lévő szkriptek számára elérhetők `sys.argv` , ahogy az imént hozzáadott kódban.

Adja meg az erőforráscsoport nevét az első paraméter értékeként, valamint a második paraméter értékeként elindítani kívánt virtuális gép nevét.

![Adja meg a paraméter értékeit](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

A runbook elindításához kattintson **az OK** gombra. A runbook fut, és elindítja a megadott virtuális gépet.

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
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> A Azure Automation nem támogatja `sys.stderr` .

## <a name="next-steps"></a>Következő lépések

- A PowerShell-runbookok megkezdéséhez tekintse meg [a PowerShell-Runbook létrehozása](automation-tutorial-runbook-textual-powershell.md)című témakört.
- A grafikus runbookok megkezdéséhez tekintse meg [a grafikus Runbook létrehozását](automation-tutorial-runbook-graphical.md)ismertető témakört.
- A PowerShell-munkafolyamat runbookok megkezdéséhez tekintse meg [a PowerShell-munkafolyamat Runbook létrehozása](automation-tutorial-runbook-textual.md)című témakört.
- Ha többet szeretne megtudni a runbook típusairól, azok előnyeiről és korlátairól, tekintse meg [Azure Automation runbook típusait](../automation-runbook-types.md).
- Az Azure Pythonban való fejlesztéséről az [Azure Python-fejlesztők](/azure/python/)számára című témakörben olvashat bővebben.
- A Python 2 runbookok megtekintéséhez tekintse meg a [Azure Automation githubat](https://github.com/azureautomation/runbooks/tree/master/Utility/Python).
