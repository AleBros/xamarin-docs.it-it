---
title: Xamarin.FormsClasse App
description: Questo articolo illustra le funzionalità della classe App predefinita, che include una proprietà per l'impostazione della pagina iniziale per l'app e un dizionario persistente per l'archiviazione di valori semplici per tutte le modifiche di stato del ciclo di vita.
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
ms.custom: video
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1e761830fb08662a82a8bf98b4544aafaa6eccf7
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573352"
---
# <a name="xamarinforms-app-class"></a>Xamarin.FormsClasse App

La classe di base `Application` offre le funzionalità seguenti, che vengono esposte nella sottoclasse predefinita `App` del progetto:

* Una proprietà `MainPage`, dove deve essere impostata la pagina iniziale per l'app.
* [ `Properties` Dizionario](#properties-dictionary) permanente per archiviare valori semplici tra le modifiche dello stato del ciclo di vita.
* Una proprietà `Current` statica che contiene un riferimento all'oggetto applicazione corrente.

Espone inoltre i [metodi del ciclo](~/xamarin-forms/app-fundamentals/app-lifecycle.md) di vita, ad esempio `OnStart` , e, nonché `OnSleep` `OnResume` gli eventi di navigazione modale.

A seconda del modello scelto, la classe `App` può essere definita in uno dei due modi seguenti:

* **C#** o
* **XAML e C#**

Per creare una classe **App** usando XAML, è necessario sostituire la classe **App** predefinita con una classe **App** XAML e il code-behind associato, come illustrato nell'esempio di codice seguente:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Photos.App">

</Application>
```

L'esempio di codice seguente illustra il code-behind associato:

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

Oltre a impostare la [`MainPage`](xref:Xamarin.Forms.Application.MainPage) proprietà, il code-behind deve chiamare anche il `InitializeComponent` metodo per caricare e analizzare il codice XAML associato.

## <a name="mainpage-property"></a>Proprietà MainPage

La proprietà `MainPage` della classe `Application` imposta la pagina radice dell'applicazione.

Ad esempio, è possibile creare nella classe `App` la logica che consente di visualizzare una pagina diversa in base al fatto che l'utente abbia eseguito o meno l'accesso.

La proprietà `MainPage` deve essere impostata nel costruttore `App`,

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }
}
```

## <a name="properties-dictionary"></a>Dizionario delle proprietà

La sottoclasse `Application` ha un dizionario `Properties` statico che può essere usato per archiviare i dati, in particolare per l'uso nei metodi `OnStart`, `OnSleep` e `OnResume`. È possibile accedervi da qualsiasi punto del Xamarin.Forms codice usando `Application.Current.Properties` .

Il dizionario `Properties` usa una chiave `string` e archivia un valore `object`.

Ad esempio, è possibile impostare una proprietà `"id"` persistente in qualsiasi punto del codice (quando è selezionato un elemento, nel metodo `OnDisappearing` di una pagina o nel metodo `OnSleep`) come illustrato di seguito:

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

Nel metodo `OnStart` o `OnResume` è quindi possibile usare questo valore per ricreare l'esperienza dell'utente in qualche modo. Il dizionario `Properties` archivia elementi `object` quindi è necessario eseguire il cast del relativo valore prima di usarlo.

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

Verificare sempre la presenza della chiave prima di accedervi in modo da evitare errori imprevisti.

> [!NOTE]
> Il dizionario `Properties` è in grado di serializzare solo i tipi primitivi per l'archiviazione. Il tentativo di archiviare altri tipi, ad esempio `List<string>`, può avere esito negativo senza che appaiano avvisi.

<!-- bugzilla 28657 -->

### <a name="persistence"></a>Persistenza

Il dizionario `Properties` viene salvato automaticamente nel dispositivo.
I dati aggiunti al dizionario saranno disponibili quando l'applicazione torna dall'esecuzione in background o viene riavviata.

Xamarin.Forms1,4 introduce un metodo aggiuntivo sulla `Application` classe, `SavePropertiesAsync()` che può essere chiamato per salvare in modo proattivo il `Properties` dizionario. Questo consente di salvare le proprietà dopo gli aggiornamenti importanti senza rischiare che non vengano serializzate a causa di un arresto anomalo o terminate dal sistema operativo.

È possibile trovare i riferimenti all'uso del `Properties` dizionario nella **creazione di app per Xamarin.Forms dispositivi mobili con** i capitoli [6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf), [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf)e [20](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)del libro e negli [esempi](https://github.com/xamarin/xamarin-forms-book-preview-2)associati.

## <a name="the-application-class"></a>Classe Application

Un'implementazione completa della classe `Application` è illustrata di seguito per riferimento:

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

Un'istanza di questa classe viene creata in ogni progetto specifico della piattaforma e passata al metodo `LoadApplication`, che rappresenta la posizione in cui l'elemento `MainPage` viene caricato e visualizzato all'utente.
Nelle sezioni seguenti viene illustrato il codice per ogni piattaforma. I Xamarin.Forms modelli di soluzione più recenti contengono già tutto questo codice, preconfigurato per l'app.

### <a name="ios-project"></a>Progetto iOS

La classe `AppDelegate` di iOS eredita da `FormsApplicationDelegate`. Deve:

* Chiamare `LoadApplication` con un'istanza della classe `App`.

* Restituire sempre `base.FinishedLaunching (app, options);`.

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

L'elemento `MainActivity` di Android eredita da `FormsAppCompatActivity`. Nell'override di `OnCreate` il metodo `LoadApplication` viene chiamato con un'istanza della classe `App`.

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

### <a name="universal-windows-project-uwp-for-windows-10"></a>Progetto UWP (Universal Windows Platform) per Windows 10

La pagina principale nel progetto UWP deve ereditare da `WindowsPage`:

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

Il codice C# sottostante alla costruzione deve chiamare `LoadApplication` per creare un'istanza del Xamarin.Forms `App` . Si noti che è consigliabile usare in modo esplicito lo spazio dei nomi dell'applicazione per qualificare `App` perché le applicazioni UWP hanno anche una propria `App` classe non correlata a Xamarin.Forms .

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

Si noti che `Forms.Init()` deve essere chiamato da **App.xaml.cs** nel progetto UWP.

Per altre informazioni, vedere [Setup Windows Projects](~/xamarin-forms/platform/windows/installation/index.md), che include i passaggi per aggiungere un progetto UWP a una Xamarin.Forms soluzione esistente che non è destinata a UWP.

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
