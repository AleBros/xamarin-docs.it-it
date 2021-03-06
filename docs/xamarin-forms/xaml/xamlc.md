---
title: Compilazione XAML in Xamarin.Forms
description: Questo articolo illustra come XAML può essere compilato direttamente in linguaggio intermedio (IL) con il compilatore di Xamarin.Forms XAML (XAMLC).
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2018
ms.openlocfilehash: 9567f3ad8d748a94a03cd1c86254072d4ba3bbdc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075127"
---
# <a name="xaml-compilation-in-xamarinforms"></a>Compilazione XAML in Xamarin.Forms

_XAML può essere compilato direttamente in linguaggio intermedio (IL) con il compilatore XAML (XAMLC)._

Compilazione XAML offre numerosi vantaggi:

- Esegue il controllo del codice XAML in fase di compilazione, notificando all'utente eventuali errori.
- Rimuove parte del tempo di carico e di creazione dell'istanza per gli elementi XAML.
- Consente di ridurre le dimensioni del file dell'assembly finale non includendo più i file XAML.

Compilazione XAML è disabilitata per impostazione predefinita per garantire la compatibilità. Può essere abilitata a livello di assembly e di classe, aggiungere il [ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attributo.

Esempio di codice seguente viene illustrata l'abilitazione di compilazione XAML a livello di assembly:

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

In questo esempio, controllo di tutto il XAML contenute all'interno dell'assembly in fase di compilazione verrà eseguita con errori XAML da segnalare in fase di compilazione anziché in fase di esecuzione. Pertanto, il `assembly` prefisso per il [ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attributo specifica che l'attributo viene applicato all'intero assembly.

> [!NOTE]
> Il [ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attributo e il [ `XamlCompilationOptions` ](xref:Xamarin.Forms.Xaml.XamlCompilationOptions) enumerazione si trovano nel `Xamarin.Forms.Xaml` dello spazio dei nomi, che deve essere importato per usarli.

Esempio di codice seguente viene illustrata l'abilitazione di compilazione XAML a livello di classe:

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

In questo esempio, il controllo del XAML per la fase di compilazione il `HomePage` classe verrà eseguita e gli errori segnalati come parte del processo di compilazione.

> [!NOTE]
> Le associazioni compilate possono essere abilitate per migliorare le prestazioni di associazione dati nelle applicazioni Xamarin.Forms. Per altre informazioni, vedere [associazioni compilate](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="related-links"></a>Collegamenti correlati

- [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
