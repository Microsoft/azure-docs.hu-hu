---
title: Azure Digital Twins Explorer hitelesítési hiba
description: A "Sikertelen hitelesítés" okai és megoldásai. a Azure Digital Twins Explorer.
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/8/2021
ms.openlocfilehash: 1f8373130fbead2204dd0ac2515595d68dd3b2e8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495045"
---
# <a name="authentication-failed"></a>A hitelesítés sikertelen

Ez a cikk azt ismerteti, hogy miért és milyen [](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) megoldási lépésekkel lehet "Sikertelen hitelesítés" hibaüzenetet kapni a Azure Digital Twins Explorer futtatásakor a helyi gépen. 

## <a name="symptoms"></a>Hibajelenségek

A Azure Digital Twins Explorer beállításakor és futtatásakor a rendszer a következő hibaüzenettel megkísérli az alkalmazással való hitelesítést:

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Képernyőkép egy hibaüzenetről a Azure Digital Twins következő szöveggel: A hitelesítés sikertelen volt. Ha helyileg futtatja az alkalmazást, győződjön meg arról, hogy be van jelentkezve az Azure-ba a gazdagépen, vagy futta-e például az &quot;az login&quot; parancsot egy parancssorban az Visual Studio-ba vagy a VS Code-ba való bejelentkezéssel vagy környezeti változók beállításával. Ha további információra van szüksége, tekintse meg az azure.identity dokumentációjának readme (információs fájl) vagy a DefaultAzureCredential (AlapértelmezettAzureCredential) adatokat. Ha a felhőben üzemeltetett adt-explorert futtat, győződjön meg arról, hogy az üzemeltetett Azure-függvényhez be van állítva a rendszer által hozzárendelt felügyelt identitás. További információt az információs fájlban olvashat.":::

## <a name="causes"></a>Okok

### <a name="cause-1"></a>Ok #1

A Azure Digital Twins Explorer a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (a kódtár része) függvényt használja, amely a helyi környezetben keresi a `Azure.Identity` hitelesítő adatokat.

Ahogy a hibaüzenet is kikövetkezteti, ez a hiba akkor fordulhat elő, ha nem adott meg helyi hitelesítő adatokat a számára a `DefaultAzureCredential` be- és behozáshoz.

A helyi hitelesítő adatok és a Azure Digital Twins Explorer használatával kapcsolatos további információkért tekintse meg a következő rövid útmutató helyi [*Azure Azure Digital Twins*](quickstart-adt-explorer.md#set-up-local-azure-credentials) hitelesítő adatainak beállítása című *szakaszát: Példaforgatókönyv.*

### <a name="cause-2"></a>Ok #2

Ez a hiba akkor is előfordulhat, ha az Azure-fiók nem rendelkezik a szükséges Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) engedélyekkel a Azure Digital Twins példányon. A példányban az adatok eléréséhez az Azure Digital Twins-adatolvasó vagy az **Azure Digital Twins-adattulajdonos** szerepkör szükséges az olvasni vagy kezelni próbált példányon.  

További információ a biztonsági és szerepkörökről a [*Azure Digital Twins: Alapfogalmak:*](concepts-security.md)Biztonság a Azure Digital Twins megoldásokhoz.

## <a name="solutions"></a>Megoldások

### <a name="solution-1"></a>Megoldás #1

Először győződjön meg arról, hogy megadotta a szükséges hitelesítő adatokat az alkalmazáshoz.

#### <a name="provide-local-credentials"></a>Helyi hitelesítő adatok megadása

`DefaultAzureCredential` hitelesíti magát a szolgáltatásban a helyi Azure-bejelentkezésből származó információk használatával. Azure-beli hitelesítő adatait úgy használhatja, hogy bejelentkezik Azure-fiókjába egy helyi [Azure CLI-ablakban,](/cli/azure/install-azure-cli) vagy egy Visual Studio vagy Visual Studio Code-ban.

Az elfogadó hitelesítő adatok típusait és a próbálkozásuk sorrendjét a `DefaultAzureCredential` [DefaultAzureCredential Azure-identitás](/dotnet/api/overview/azure/identity-readme#defaultazurecredential)dokumentációjában találhatja meg.

Ha már helyileg bejelentkezett a megfelelő Azure-fiókba, és a probléma nem oldódik meg, folytassa a következő megoldással.

### <a name="solution-2"></a>Megoldás #2

Ellenőrizze, hogy az Azure-felhasználó rendelkezik-e **Azure Digital Twins-adatolvasó** szerepkörvel a Azure Digital Twins-példányon, ha csak beolvassa az adatait, vagy az Azure Digital Twins Data **Owner** szerepkört a példányon, ha az adatokat próbálja kezelni.

Vegye figyelembe, hogy ez a szerepkör eltér a...
* a szerepkör korábbi neve az előzetes verzióban, *Azure Digital Twins tulajdonos (előzetes verzió)* (a szerepkör ugyanaz, de a név megváltozott)
* a *tulajdonosi* szerepkör a teljes Azure-előfizetésen. *Azure Digital Twins adattulajdonos* egy szerepkör a Azure Digital Twins, és erre az adott Azure Digital Twins van kiszűkve.
* a *tulajdonosi* szerepkör a Azure Digital Twins. Ez két különálló Azure Digital Twins felügyeleti szerepkör, Azure Digital Twins az *Adattulajdonos* szerepkört kell használni a felügyelethez.

 Ha nem tudja ezt a szerepkört, állítsa be a probléma megoldásához.

#### <a name="check-current-setup"></a>Az aktuális beállítás ellenőrzése

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Problémák megoldása 

Ha nem rendelkezik ezzel a szerepkör-hozzárendeléssel,  akkor az Azure-előfizetésben tulajdonosi szerepkörsel bíró felhasználónak a következő parancsot kell futtatnia, hogy megfelelő szerepkört adjon az Azure-felhasználónak a Azure Digital Twins **példányon.** 

Ha Ön tulajdonos az előfizetésben, saját maga is futtathatja ezt a parancsot. Ha nem, lépjen kapcsolatba egy tulajdonossal, és futtassa a parancsot az Ön nevében. A szerepkör neve **adattulajdonos Azure Digital Twins** szerkesztési hozzáféréshez, **vagy Azure Digital Twins adatolvasó az** olvasási hozzáféréshez.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

A szerepkörre vonatkozó követelményekkel és a [](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) hozzárendelési folyamattal kapcsolatos további részletekért tekintse meg a *How-to: Set up an instance and authentication (CLI* or portal) (Útmutató: Példány és hitelesítés beállítása (CLI vagy portál) ) című szakaszt.

## <a name="next-steps"></a>Következő lépések

Olvassa el az új új példány létrehozásának és hitelesítésének telepítési Azure Digital Twins lépéseit:
* [*How-to: Set up a instance and authentication (CLI) (A példány és a hitelesítés beállítása (CLI)*](how-to-set-up-instance-cli.md)

További információ a biztonságról és az engedélyekről a Azure Digital Twins:
* [*Alapfogalmak: Biztonsági Azure Digital Twins megoldások*](concepts-security.md)
