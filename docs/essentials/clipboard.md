---
title: 'Xamarin.Essentials: Appunti'
description: Questo documento descrive la classe Clipboard in Xamarin.Essentials, che consente di copiare e incollare testo negli Appunti di sistema tra un'applicazione e l'altra.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 02/12/2019
ms.custom: video
ms.openlocfilehash: c186f5c61bd2fa3df305be92a03135e57e302d02
ms.sourcegitcommit: 6e04246207aa743820029e8c217a43cfdd24f991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2019
ms.locfileid: "67352121"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: Appunti

La classe **Clipboard** consente di copiare e incollare testo negli Appunti del sistema tra un'applicazione e l'altra.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-clipboard"></a>Uso di Clipboard

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per controllare se attualmente negli **Appunti** è presente testo da incollare:

```csharp
var hasText = Clipboard.HasText;
```

Per impostare il testo negli **Appunti**:

```csharp
await Clipboard.SetTextAsync("Hello World");
```

Per leggere il testo dagli **Appunti**:

```csharp
var text = await Clipboard.GetTextAsync();
```

> [!TIP]
> L'accesso agli Appunti deve essere eseguito sul thread dell'interfaccia utente principale. Vedere l'API [MainThread](~/essentials/main-thread.md) per informazioni su come richiamare i metodi sul thread dell'interfaccia utente principale.

## <a name="api"></a>API

- [Codice sorgente di Clipboard](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentazione dell'API Clipboard](xref:Xamarin.Essentials.Clipboard)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Clipboard-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
