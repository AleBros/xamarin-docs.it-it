---
title: "compilazione XAML in Xamarin.Forms " Description: "questo articolo spiega in che modo è possibile compilare facoltativamente XAML direttamente in linguaggio intermedio (il) con il Xamarin.Forms compilatore XAML (XAMLC)."
ms. prod: Novell MS. AssetID: 9A2D10A6-5DFC-485F-A75A-2F7B98314025 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 08/22/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
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
