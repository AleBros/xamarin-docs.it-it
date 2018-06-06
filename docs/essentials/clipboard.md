---
title: 'Xamarin.Essentials: negli Appunti'
description: Questo documento descrive la classe negli Appunti in Xamarin.Essentials, che consente di copiare e incollare il testo negli Appunti di sistema tra applicazioni.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 41b15b480fa23bd49667b68e904043e4f1a95732
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782359"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: negli Appunti

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
Clipboard.SetText("Hello World");
```

Leggere il testo dal **Appunti**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [Codice sorgente negli Appunti](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentazione API Appunti](xref:Xamarin.Essentials.Clipboard)
