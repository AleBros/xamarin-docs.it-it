---
title: Compilazione XAML
description: "Se necessario, il linguaggio XAML può essere compilato direttamente nel linguaggio intermedio (IL) con il compilatore XAML (XAMLC)."
ms.topic: article
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/21/2016
ms.openlocfilehash: 3afb7608838a2c34f143d0563b50f03ad7f6ecf4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="xaml-compilation"></a>Compilazione XAML

_XAML possono essere compilate facoltativamente direttamente nel linguaggio intermedio (IL) con il compilatore XAML (XAMLC)._

XAMLC offre alcuni vantaggi:

- Esegue il controllo del codice XAML in fase di compilazione, notificando all'utente eventuali errori.
- Rimuove parte del tempo di carico e di creazione dell'istanza per gli elementi XAML.
- Consente di ridurre le dimensioni del file dell'assembly finale non includendo più i file XAML.

XAMLC è disabilitato per impostazione predefinita per garantire la compatibilità con le versioni precedenti. Può essere abilitata a livello di assembly e la classe aggiungendo il `XamlCompilation` attributo.

Esempio di codice seguente viene illustrato come consentire XAMLC a livello di assembly:

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

In questo esempio, il controllo in fase di compilazione di tutto il codice XAML contenuto all'interno di `PhotoApp` dello spazio dei nomi verrà eseguita con errori XAML da segnalare in fase di compilazione anziché in fase di esecuzione.
Il `assembly` come prefisso il `XamlCompilation` attributo specifica che l'attributo si applica all'intero assembly.

Esempio di codice seguente viene illustrato come consentire XAMLC a livello di classe:

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

In questo esempio, il controllo del codice XAML per la fase di compilazione di `HomePage` classe verrà eseguita e gli errori segnalati come parte del processo di compilazione.

> [!NOTE]
> **Nota**: il `XamlCompilation` attributo e `XamlCompilationOptions` enumerazione si trovano nel `Xamarin.Forms.Xaml0` spazio dei nomi, che deve essere importato per utilizzarli.


## <a name="related-links"></a>Collegamenti correlati

- [XamlCompilation](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/)
- [XamlCompilationOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationOptions/)
