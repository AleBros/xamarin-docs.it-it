---
title: Classe di App xamarin. Forms
description: Questo articolo illustra le funzionalità della classe di App predefinito, che include una proprietà da impostare per la pagina iniziale per l'app, e un dizionario persistente per archiviare valori semplici tra modifiche dello stato del ciclo di vita.
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: 6de4380f2ce2d19df4ff912b7c86b75ca9e7821b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38999080"
---
# <a name="xamarinforms-app-class"></a>Classe di App xamarin. Forms

Il `Application` classe di base offre le funzionalità seguenti, che vengono esposti nel predefinito progetti `App` sottoclasse:

* Oggetto `MainPage` proprietà, che è possibile impostare la pagina iniziale per l'app.
* Un persistente [ `Properties` dizionario](#Properties_Dictionary) per archiviare valori semplici tra modifiche dello stato del ciclo di vita.
* Un valore statico `Current` proprietà che contiene un riferimento all'oggetto applicazione corrente.

Espone inoltre [i metodi del ciclo di vita](~/xamarin-forms/app-fundamentals/app-lifecycle.md) , ad esempio `OnStart`, `OnSleep`, e `OnResume` , nonché gli eventi di navigazione modale.

A seconda del modello scelto, il `App` classe può essere definita in uno dei due modi:

* **C#**, o
* **XAML E C#**

Per creare un **App** classe usando XAML, il valore predefinito **App** classe deve essere sostituita con un XAML **App** classe e code-behind associato, come illustrato nell'esempio di codice seguente:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Photos.App">

</Application>
```

Esempio di codice seguente mostra il code-behind associato:

```csharp
public partial class App : Application
{
    public App ()
    {
        InitializeComponent ();
        MainPage = new HomePage ();
    }
    ...
}
```

Nonché impostazione di [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) proprietà, il code-behind deve anche chiamare il `InitializeComponent` metodo per caricare e analizzare il XAML associato.

## <a name="mainpage-property"></a>Proprietà MainPage

Il `MainPage` proprietà di `Application` classe imposta la radice dell'applicazione.

Ad esempio, è possibile creare per la logica nel `App` classe per visualizzare una pagina diversa a seconda del fatto che l'utente è connesso o meno.

Il `MainPage` proprietà deve essere impostata `App` costruttore,

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }
}
```

<a name="Properties_Dictionary" />

## <a name="properties-dictionary"></a>Dizionario delle proprietà

Il `Application` sottoclasse ha un valore statico `Properties` dizionario che consente di archiviare i dati, in particolare per l'uso nel `OnStart`, `OnSleep`, e `OnResume` metodi. Ciò è possibile accedere da qualsiasi punto nel codice xamarin. Forms usando `Application.Current.Properties`.

Il `Properties` dizionario utilizza un `string` chiave e archivia un `object` valore.

Ad esempio, è possibile impostare un persistente `"id"` proprietà in qualsiasi punto nel codice (quando l'elemento è selezionato, in una pagina `OnDisappearing` metodo, o nel `OnSleep` metodo) simile al seguente:

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

Nel `OnStart` o `OnResume` metodi è quindi possibile utilizzare questo valore per ricreare l'esperienza dell'utente in qualche modo. Il `Properties` dizionario archivi `object`s, pertanto è necessario eseguire il cast il relativo valore prima di poterla usare.

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

Controllare sempre la presenza della chiave prima di accedere in modo da evitare errori imprevisti.

> [!NOTE]
> Il `Properties` dizionario può serializzare solo i tipi primitivi per l'archiviazione. Tentativo di archiviare altri tipi (ad esempio `List<string>`) possano un esito negativo.

<!-- bugzilla 28657 -->

### <a name="persistence"></a>Persistenza

Il `Properties` dizionario viene salvato automaticamente al dispositivo.
I dati aggiunti al dizionario sarà disponibili quando l'applicazione torna di background o anche dopo il riavvio.

Xamarin. Forms 1.4 presentato un metodo aggiuntivo al `Application` classe - `SavePropertiesAsync()` -che può essere chiamato in modo permanente in modo proattivo il `Properties` dizionario. Questo è quello di consentire a salvare le proprietà dopo gli aggiornamenti importanti invece che a loro non serializzata a causa di un arresto anomalo o essere terminati dal sistema operativo di rischio.

È possibile trovare riferimenti a usando la `Properties` dizionario nel **creazione di App per dispositivi mobili con xamarin. Forms** libro capitoli [6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf), [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf), e [20 ](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)e nel blocco [campioni](https://github.com/xamarin/xamarin-forms-book-preview-2).



## <a name="the-application-class"></a>Classe Application

Completa `Application` implementazione della classe è illustrato di seguito per riferimento:

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }

    protected override void OnStart()
    {
        // Handle when your app starts
        Debug.WriteLine ("OnStart");
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Debug.WriteLine ("OnSleep");
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
        Debug.WriteLine ("OnResume");
    }
}

```

Questa classe viene quindi creata un'istanza in ogni progetto specifico della piattaforma e passata al `LoadApplication` che rappresenta la posizione di `MainPage` viene caricato e visualizzato all'utente.
Nelle sezioni seguenti viene illustrato il codice per ogni piattaforma. Gli ultimi modelli di soluzione xamarin. Forms già contengono tutto questo codice, preconfigurato per l'app.


### <a name="ios-project"></a>Progetto iOS

IOS `AppDelegate` classe eredita da `FormsApplicationDelegate`. È necessario:

* Chiamare `LoadApplication` con un'istanza di `App` classe.

* Restituisce sempre `base.FinishedLaunching (app, options);`.

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="android-project"></a>Progetto Android

Android `MainActivity` eredita da `FormsAppCompatActivity`. Nel `OnCreate` eseguire l'override di `LoadApplication` viene chiamato con un'istanza del `App` classe.

```csharp
[Activity (Label = "App Lifecycle Sample", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true,
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="universal-windows-project-uwp-for-windows-10"></a>Progetto Windows universale (UWP) per Windows 10

Visualizzare [Setup Windows Projects](~/xamarin-forms/platform/windows/installation/index.md) per informazioni sul supporto di piattaforma UWP in xamarin. Forms.

La pagina principale nel progetto UWP deve ereditare da `WindowsPage`:

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

Costruzione del codebehind c# deve chiamare `LoadApplication` per creare un'istanza di xamarin. Forms `App`. Si noti che è buona norma usare in modo esplicito lo spazio dei nomi dell'applicazione per qualificare le `App` poiché le applicazioni UWP presentano anche i propri `App` classe non correlati a xamarin. Forms.

```csharp
public sealed partial class MainPage
{
    public MainPage()
    {
        InitializeComponent();

        LoadApplication(new YOUR_NAMESPACE.App());
    }
 }
```

Si noti che `Forms.Init()` deve essere chiamato **App.xaml.cs** intorno alla riga 63.
