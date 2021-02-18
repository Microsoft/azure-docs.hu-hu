---
title: Azure Storage-fiók alapértelmezett hozzáférési szintjeinek kezelése
description: Megtudhatja, hogyan módosíthatja egy GPv2 vagy Blob Storage fiók alapértelmezett hozzáférési szintjét
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 637f748882b3ac84127c8b71761a06629e1e0957
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653832"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>Azure Storage-fiók alapértelmezett hozzáférési szintjeinek kezelése

Minden egyes Azure Storage-fiókhoz tartozik egy alapértelmezett hozzáférési szint, amely akár gyors, akár ritka. A hozzáférési szintet a Storage-fiók létrehozásakor rendelheti hozzá. Az alapértelmezett hozzáférési szint a gyors.

Az alapértelmezett fiók szintet úgy változtathatja meg, hogy a Storage-fiók **hozzáférési szintje** attribútumát állítja be. A fiók rétegének módosítása a fiókban tárolt összes olyan objektumra vonatkozik, amely nem rendelkezik explicit szintű készlettel. A fiók rétegét a gyakori és a ritka elérésű írási műveletekre (10 000) állítja be a GPv2-fiókokban lévő set szint nélkül, és az olvasási műveletek (10 000) és az lekérési (GB-os) Adatlekérdezési díjak a blob Storage-és GPv2-fiókokban lévő összes blob esetében nem használhatók.

Ha a Blobok az objektum szintjén beállított szinttel rendelkeznek, a fiók szintje nem érvényes. Az archív szint csak az objektumok szintjén alkalmazható. Bármikor válthat a hozzáférési szintek között.

A Storage-fiók létrehozása után a következő lépésekkel módosíthatja az alapértelmezett hozzáférési szintet.

## <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>GPv2 vagy Blob Storage fiókhoz tartozó alapértelmezett fiók hozzáférési szintjeinek módosítása

A következő forgatókönyvek a Azure Portal vagy a PowerShellt használják:

# <a name="portal"></a>[Portál](#tab/portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A Azure Portal keresse meg és válassza ki az **összes erőforrás** elemet.

1. Válassza ki a tárfiókot.

1. A **Beállítások** területen válassza a **konfiguráció** lehetőséget a fiók konfigurációjának megtekintéséhez és módosításához.

1. Válassza ki az igényeinek megfelelő hozzáférési szintet: állítsa be a **hozzáférési szintet** a lassú **vagy** a **gyors** eléréshez.

1. Kattintson a felső **Mentés** gombra.

![Az alapértelmezett fiók szintjeinek módosítása Azure Portal](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi PowerShell-parancsfájl használatával módosíthatja a fiók szintjét. A `$rgName` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a Storage-fiók nevével.

```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

---

## <a name="next-steps"></a>Következő lépések

- [Blobok szintjeinek kezelése Azure Storage-fiókban](../blobs/manage-access-tier.md)
- [Annak megállapítása, hogy előnyös-e a prémium szintű teljesítmény az alkalmazásban](../blobs/storage-blob-performance-tiers.md)
- [Aktuális tárfiókjai használatának értékelése az Azure Storage mérőszámainak engedélyezésével](../blobs/monitor-blob-storage.md)
