---
title: Classe App
description: Funzionalità della classe App predefinito, che può essere c# o XAML
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: 1e1039f513534885dffe9fef348d567243651e22
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="app-class"></a>Classe App

Il `Application` classe di base offre le funzionalità seguenti, che sono esposte nel predefinito progetti `App` sottoclasse:

* Oggetto `MainPage` proprietà, è possibile impostare la pagina iniziale per l'app.
* Permanente [ `Properties` dizionario](#Properties_Dictionary) per archiviare valori semplici tra le modifiche dello stato del ciclo di vita.
* Un valore statico `Current` proprietà che contiene un riferimento all'oggetto applicazione corrente.

Espone inoltre [metodi del ciclo di vita](~/xamarin-forms/app-fundamentals/app-lifecycle.md) , ad esempio `OnStart`, `OnSleep`, e `OnResume` , nonché gli eventi di spostamento modale.

A seconda del modello è stata selezionata, la `App` classe può essere definita in uno dei due modi:

* **C#**, o
* **XAML E C#**

Per creare un **App** classe tramite XAML, il valore predefinito **App** classe deve essere sostituita con un file XAML **App** classe e code-behind associato, come illustrato nell'esempio di codice seguente:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Photos.App">

</Application>
```

Esempio di codice seguente viene illustrato il code-behind associato:

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

Nonché l'impostazione di [ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) proprietà, il code-behind deve chiamare anche il `InitializeComponent` metodo per caricare e analizzare il codice XAML associato.

## <a name="mainpage-property"></a>Proprietà MainPage

Il `MainPage` proprietà la `Application` classe imposta la pagina principale dell'applicazione.

Ad esempio, è possibile creare la logica nella `App` classe per visualizzare una pagina diversa a seconda se l'utente è connesso o non.

Il `MainPage` proprietà deve essere impostata `App` , costruttore

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

Il `Application` sottoclasse ha un valore statico `Properties` dizionario utilizzato per archiviare i dati, in particolare da utilizzare per il `OnStart`, `OnSleep`, e `OnResume` metodi. Questo è possibile accedere da un punto qualsiasi nel codice, xamarin. Forms utilizzando `Application.Current.Properties`.

Il `Properties` dizionario utilizza un `string` chiave e archivia un `object` valore.

Ad esempio, è possibile impostare un permanente `"id"` proprietà in qualsiasi punto nel codice (quando è selezionato un elemento, in una pagina `OnDisappearing` metodo, o nel `OnSleep` (metodo)) come segue:

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

Nel `OnStart` o `OnResume` metodi è quindi possibile utilizzare questo valore per ricreare l'esperienza dell'utente in qualche modo. Il `Properties` archivi dizionario `object`s, pertanto è necessario eseguire il cast di valore prima di utilizzarlo.

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

Verificare sempre la presenza della chiave prima di accedere in modo da evitare errori imprevisti.

> [!NOTE]
> Il `Properties` dizionario consente di serializzare solo i tipi primitivi per l'archiviazione. Il tentativo di archiviare altri tipi (ad esempio `List<string>`) può avere esito negativo senza avvisare.

<!-- bugzilla 28657 -->

### <a name="persistence"></a>Persistenza

Il `Properties` dizionario viene salvato automaticamente nel dispositivo.
I dati aggiunti al dizionario sarà disponibili quando l'applicazione restituisce dallo sfondo o anche dopo il riavvio.

Xamarin. Forms 1.4 introdotte un metodo aggiuntivo il `Application` class - `SavePropertiesAsync()` -che può essere chiamato in modo permanente in modo proattivo il `Properties` dizionario. Questa operazione consente di salvare le proprietà dopo gli aggiornamenti importanti anziché rischiare li non recupero serializzati a causa di un arresto anomalo o viene terminato dal sistema operativo.

È possibile trovare i riferimenti all'utilizzo di `Properties` dizionario il **la creazione di App per dispositivi mobili con xamarin. Forms** book capitoli [6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf), [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf), e [20 ](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)e nell'oggetto associato [esempi](https://github.com/xamarin/xamarin-forms-book-preview-2).



## <a name="the-application-class"></a>Classe Application

Completa `Application` di seguito è riportato l'implementazione della classe di riferimento:

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

Questa classe viene quindi creata un'istanza in ogni progetto specifico della piattaforma e passata al `LoadApplication` metodo dove il `MainPage` viene caricato e visualizzato all'utente.
Il codice per ogni piattaforma è illustrato nelle sezioni seguenti. I modelli di soluzioni xamarin. Forms più recenti contengono già tutto questo codice, preconfigurato per l'app.


### <a name="ios-project"></a>Progetto iOS

IOS `AppDelegate` ora eredita da `FormsApplicationDelegate`. È necessario:

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

Il Android `MainActivity` ora eredita da `FormsApplicationActivity`. Nel `OnCreate` eseguire l'override di `LoadApplication` metodo viene chiamato con un'istanza del `App` classe.

```csharp
[Activity (Label = "App Lifecycle Sample", Icon = "@drawable/icon", MainLauncher = true,
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity :
    global::Xamarin.Forms.Platform.Android.FormsApplicationActivity // superclass new in 1.3
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

> [!NOTE]
> È una nuova [ `FormsAppCompatActivity` ](~/xamarin-forms/platform/android/appcompat.md) classe base che può essere utilizzato per migliorare il supporto Android materiale di progettazione.
> Che diventerà il modello di Android predefinito in futuro, ma è possibile seguire [queste istruzioni](~/xamarin-forms/platform/android/appcompat.md) per aggiornare le app Android esistente.


### <a name="windows-phone-project"></a>Progetto Windows Phone

Pagina principale del progetto (basata su Silverlight) di Windows Phone deve ereditare da `FormsApplicationPage`. Ciò significa XAML e c# per `MainPage` riferimento il `FormsApplicationPage` come illustrato.

Il codice XAML usa uno spazio dei nomi personalizzato in modo che l'elemento radice riflette la `FormsApplicationPage` classe:

```xaml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

In c# eredita il `FormsApplicationPage` classe e le chiamate `LoadApplication` per creare un'istanza del xamarin. Forms `App`. Si noti che è consigliabile utilizzare in modo esplicito lo spazio dei nomi dell'applicazione per qualificare il `App` poiché le applicazioni Windows Phone presentano anche i propri `App` classe non correlati a xamarin. Forms.

```csharp
public partial class MainPage :
    global::Xamarin.Forms.Platform.WinPhone.FormsApplicationPage // superclass new in 1.3
{
    public MainPage()
    {
        InitializeComponent();
        SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new YOUR_APP_NAMESPACE.App()); // new in 1.3, use the correct namespace
    }
 }
```

### <a name="windows-81-project"></a>Progetto Windows 8.1

Pagina principale nella [Windows 8.1 (basato su WinRT)](~/xamarin-forms/platform/windows/installation/tablet.md) progetti devono ora ereditano da `WindowsPage`. Ciò significa che il codice XAML per `MainPage` riferimento il `WindowsPage` come illustrato:

Il codice XAML usa uno spazio dei nomi personalizzato in modo che l'elemento radice riflette la `FormsApplicationPage` classe:

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.WinRT"
   ...>
</forms:WindowsPage>
```

Costruzione del codice c# è necessario chiamare `LoadApplication` per creare un'istanza del xamarin. Forms `App`. Si noti che è consigliabile utilizzare in modo esplicito lo spazio dei nomi dell'applicazione per qualificare il `App` poiché le applicazioni UWP presentano anche i propri `App` classe non correlati a xamarin. Forms.

```csharp
public partial class MainPage
{
    public MainPage()
    {
        InitializeComponent();
        LoadApplication(new YOUR_APP_NAMESPACE.App());
    }
 }
```

Si noti che `Forms.Init()` deve essere chiamato **App.xaml.cs** alla riga 65.

### <a name="universal-windows-project-uwp-for-windows-10"></a>Progetto Windows universale (UWP) per Windows 10

[Il progetto Windows universale](~/xamarin-forms/platform/windows/installation/universal.md) supporto in xamarin. Forms è attualmente in anteprima.

Pagina principale nel progetto UWP deve ereditare da `WindowsPage`. Ciò significa XAML e c# per `MainPage` riferimento il `FormsApplicationPage` come illustrato.

Il codice XAML usa uno spazio dei nomi personalizzato in modo che l'elemento radice riflette la `FormsApplicationPage` classe:

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

Costruzione del codice c# è necessario chiamare `LoadApplication` per creare un'istanza del xamarin. Forms `App`. Si noti che è consigliabile utilizzare in modo esplicito lo spazio dei nomi dell'applicazione per qualificare il `App` poiché le applicazioni UWP presentano anche i propri `App` classe non correlati a xamarin. Forms.

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

Si noti che `Forms.Init()` deve essere chiamato **App.xaml.cs** alla riga 63.