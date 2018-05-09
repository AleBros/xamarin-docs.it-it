---
title: Appunti Xamarin.Essentials
description: La classe Appunti consente di copiare e incollare il testo negli Appunti di sistema tra applicazioni.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 218f90746f130f02c47040a9191b1001fa80c203
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-clipboard"></a>Appunti Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Appunti** classe consente di copiare e incollare il testo negli Appunti di sistema tra applicazioni.

## <a name="using-clipboard"></a>Utilizzo degli Appunti

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per verificare se il **Appunti** attualmente pronto per essere incollato il testo:

```csharp
var hasText = Clipboard.HasText;
```

Per impostare il testo il **Appunti**:

```csharp
ClipBoard.SetText("Hello World");
```

Leggere il testo dal **Appunti**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [Codice sorgente negli Appunti](https://github.com/xamarin/Essentials/tree/master/Essentials/Clipboard)
- [Documentazione API Appunti](xref:Xamarin.Essentials.Clipboard)
