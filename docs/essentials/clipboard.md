---
title: Appunti Xamarin.Essentials
description: La classe Appunti consente di copiare e incollare il testo negli Appunti di sistema tra applicazioni.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 67a0218325918b57e5ed2618b57d52d3fe3ee820
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
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

- [Codice sorgente negli Appunti](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentazione API Appunti](xref:Xamarin.Essentials.Clipboard)
