---
title: Appunti Xamarin.Essentials
description: La classe Appunti consente di copiare e incollare il testo negli Appunti di sistema tra applicazioni.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 44ba8090851b65327682b3d290d58fb23bb8aae4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
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
