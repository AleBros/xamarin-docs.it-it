---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4d934fd2155a6a088dd543658555bf104b38f302
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138541"
---
# <a name="entry-cursor-color-on-ios"></a>Colore cursore voce in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS imposta il colore del cursore di un oggetto [`Entry`](xref:Xamarin.Forms.Entry) su un colore specificato. Viene utilizzato in XAML impostando la [`Entry.CursorColor`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.CursorColorProperty) proprietà associabile su un oggetto [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry ... ios:Entry.CursorColor="LimeGreen" />
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var entry = new Xamarin.Forms.Entry();
entry.On<iOS>().SetCursorColor(Color.LimeGreen);
```

Il `Entry.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. [ `Entry.SetCursorColor` ] (Xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. SetCursorColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Voce}, Xamarin.Forms . Color)), nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, imposta il colore del cursore su un oggetto specificato [`Color`](xref:Xamarin.Forms.Color) . Inoltre, [ `Entry.GetCursorColor` ] (xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. GetCursorColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry})) il metodo può essere utilizzato per recuperare il colore corrente del cursore.

Il risultato è che il colore del cursore in un oggetto [`Entry`](xref:Xamarin.Forms.Entry) può essere impostato su uno specifico [`Color`](xref:Xamarin.Forms.Color) :

![](entry-cursor-color-images/entry-cursorcolor.png "Entry Cursor Color")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
