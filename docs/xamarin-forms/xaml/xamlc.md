---
title: Compilazione XAML inXamarin.Forms
description: Questo articolo illustra come è possibile compilare facoltativamente XAML direttamente in linguaggio intermedio (IL) con il Xamarin.Forms compilatore XAML (XAMLC).
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: eebbb3040175118320639bcb4482ec77b5c16ac7
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137293"
---
# <a name="xaml-compilation-in-xamarinforms"></a>Compilazione XAML inXamarin.Forms

_Se necessario, il linguaggio XAML può essere compilato direttamente nel linguaggio intermedio (IL) con il compilatore XAML (XAMLC)._

La compilazione XAML offre una serie di vantaggi:

- Esegue il controllo del codice XAML in fase di compilazione, notificando all'utente eventuali errori.
- Rimuove parte del tempo di carico e di creazione dell'istanza per gli elementi XAML.
- Consente di ridurre le dimensioni del file dell'assembly finale non includendo più i file XAML.

Per impostazione predefinita, la compilazione XAML è disabilitata per garantire la compatibilità con le versioni precedenti. Può essere abilitata a livello di assembly e di classe aggiungendo l' [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attributo.

Nell'esempio di codice seguente viene illustrata l'abilitazione della compilazione XAML a livello di assembly:

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

In questo esempio viene eseguito il controllo in fase di compilazione di tutto il codice XAML contenuto nell'assembly, con errori XAML segnalati in fase di compilazione anziché in fase di esecuzione. Pertanto, il `assembly` prefisso dell' [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attributo specifica che l'attributo viene applicato all'intero assembly.

> [!NOTE]
> L' [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attributo e l' [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions) enumerazione si trovano nello `Xamarin.Forms.Xaml` spazio dei nomi, che deve essere importato per utilizzarli.

Nell'esempio di codice seguente viene illustrata l'abilitazione della compilazione XAML a livello di classe:

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

In questo esempio il controllo in fase di compilazione del codice XAML per la `HomePage` classe verrà eseguito ed errori segnalati come parte del processo di compilazione.

> [!NOTE]
> Le associazioni compilate possono essere abilitate per migliorare le prestazioni di data binding nelle Xamarin.Forms applicazioni. Per altre informazioni, vedere [Binding compilati](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="related-links"></a>Collegamenti correlati

- [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [`XamlCompilationOptions`](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
