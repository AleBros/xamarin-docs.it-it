---
title: 'Xamarin.Essentials: negli Appunti'
description: Questo documento descrive la classe negli Appunti in Xamarin.Essentials, che consente di copiare e incollare il testo negli Appunti di sistema tra le applicazioni.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 41b15b480fa23bd49667b68e904043e4f1a95732
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38842615"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: negli Appunti

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

La classe **Clipboard** consente di copiare e incollare il testo negli appunti di sistema tra le applicazioni.

## <a name="using-clipboard"></a>Utilizzo degli Appunti

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per verificare se la classe Clipboard include testo pronto per essere incollato:

```csharp
var hasText = Clipboard.HasText;
```

Per impostare il testo della **Clipboard**:

```csharp
Clipboard.SetText("Hello World");
```

Leggere il testo dal **negli Appunti**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [Codice sorgente negli Appunti](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentazione API Appunti](xref:Xamarin.Essentials.Clipboard)
