---
title: Progetti di Windows il programma di installazione
description: Soluzioni più vecchie in xamarin. Forms (o quelle create in macOS) non avrà i progetti Universal Windows Platform, e pertanto in questo articolo illustra come aggiungere un nuovo progetto UWP a una soluzione xamarin. Forms esistente.
ms.prod: xamarin
ms.assetid: A0774D2E-6994-4D91-84E8-DAB66FC92320
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: fb166b69c76ca4c87746358258d97f1cb81cb301
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123232"
---
# <a name="setup-windows-projects"></a>Progetti di Windows il programma di installazione

_Aggiunta di nuovi progetti di Windows a una soluzione xamarin. Forms esistente_

Soluzioni più vecchie in xamarin. Forms (o quelle create in macOS) non avrà i progetti di app Universal Windows Platform (UWP). Pertanto, è necessario aggiungere manualmente un progetto UWP per compilare un'app di Windows 10 (UWP).

## <a name="add-a-universal-windows-platform-app"></a>Aggiungere un Windows Universal app della piattaforma

È consigliabile eseguire **Visual Studio 2017** sul **Windows 10** per compilare le app UWP. Per altre informazioni sulla piattaforma Windows universale, vedere [Introduzione alla piattaforma Windows Universal](/windows/uwp/get-started/universal-application-platform-guide/).

La piattaforma UWP è disponibile in xamarin. Forms 2.1 e versioni successive e verifica è supportata in xamarin. Forms 2.2 e successive.

Verificare i <a href="#troubleshooting">risoluzione dei problemi</a> sezione per suggerimenti utili.

Seguire queste istruzioni per aggiungere un'app UWP che verrà eseguita su telefoni, Tablet e desktop Windows 10:

 1 . Pulsante destro del mouse sulla soluzione e selezionare **Aggiungi > Nuovo progetto...**  e aggiungere un **App vuota (Windows universale)** progetto:

  ![](universal-images/add-wu.png "Aggiungi finestra di dialogo Nuovo progetto")

 2 . Nel **nuovo progetto di piattaforma di Windows universale** finestra di dialogo, selezionare le versioni minima e di destinazione di Windows 10 che verrà eseguita l'app:

  ![](universal-images/target-version.png "Finestra Nuovo progetto della piattaforma di Windows universale")

 3 . Pulsante destro del mouse sul progetto UWP e selezionare **Gestisci pacchetti NuGet...**  e aggiungere i **xamarin. Forms** pacchetto. Verificare che gli altri progetti nella soluzione vengono inoltre aggiornati alla stessa versione del pacchetto di xamarin. Forms.

 4 . Assicurarsi che il nuovo progetto UWP verrà compilato **compilazione > Configuration Manager** finestra (ciò probabilmente non essersi verificato in per impostazione predefinita). Segni di graduazione il **compilare** e **Distribuisci** caselle per il progetto universale:

  [![](universal-images/configuration-sml.png "Finestra Gestione configurazione")](universal-images/configuration.png#lightbox "finestra Gestione configurazione")

 5 . Pulsante destro del mouse sul progetto e scegliere **Add > riferimento** e creare un riferimento al progetto di applicazione xamarin. Forms (.NET Standard o progetti condivisi).

  ![](universal-images/addref-sml.png "Finestra di dialogo Gestione riferimenti")

 6 . Nel progetto UWP, modificare **App.xaml.cs** per includere il `Init` chiamata al metodo all'interno di `OnLaunched` metodo intorno alla riga 52:

```csharp
// under this line
rootFrame.NavigationFailed += OnNavigationFailed;
// add this line
Xamarin.Forms.Forms.Init (e); // requires the `e` parameter
```

 7 . Nel progetto UWP, modificare **MainPage. XAML** rimuovendo il `Grid` contenute all'interno di `Page` elemento.

 8 . Nelle **MainPage. XAML**, aggiungere un nuovo `xmlns` voce per `Xamarin.Forms.Platform.UWP`:

```csharp
xmlns:forms="using:Xamarin.Forms.Platform.UWP"
```

 9 . Nelle **MainPage. XAML**, modificare la radice `<Page` elemento `<forms:WindowsPage`:

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
...
</forms:WindowsPage>
```

 10 . Nel progetto UWP, modificare **MainPage.xaml.cs** per rimuovere il `: Page` identificatore di ereditarietà per il nome della classe (poiché ora erediterà dalla `WindowsPage` a causa della modifica effettuata nel passaggio precedente):

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 11 . Nelle **MainPage.xaml.cs**, aggiungere il `LoadApplication` chiamare nel `MainPage` costruttore per avviare l'app xamarin. Forms:

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

<!--
11 . Double-click **Package.appxmanifest** to set these capabilities
  that are often required:

  Capabilities set:

  * Internet (Client)
  * Location
-->

12 . Aggiungere qualsiasi risorsa locale (ad es. file di immagine) dai progetti della piattaforma esistenti che sono necessari.

## <a name="troubleshooting"></a>Risoluzione dei problemi

<a name="target-invocation-exception" />

### <a name="target-invocation-exception-when-using-compile-with-net-native-tool-chain"></a>"Eccezione della chiamata di destinazione" quando si usa "Per la compilazione con toolchain di .NET Native"

Se l'app UWP fa riferimento a più assembly (ad esempio librerie di controlli terza parte o dell'app stessa è suddivisa in più librerie), xamarin. Forms potrebbe non essere possibile caricare gli oggetti da tali assembly (ad esempio renderer personalizzati).

Questa situazione può verificarsi quando si usa la **esegue la compilazione con toolchain .NET Native** che è un'opzione per le app UWP nel **proprietà > compilazione > Generale** finestra per il progetto.

È possibile risolvere il problema utilizzando un overload specifico UWP del `Forms.Init` chiamare nel **App.xaml.cs** come illustrato nel codice seguente (è consigliabile sostituire `ClassInOtherAssembly` con un'effettiva classe fa riferimento il codice):

```csharp
// You'll need to add `using System.Reflection;`
List<Assembly> assembliesToInclude = new List<Assembly>();

// Now, add in all the assemblies your app uses
assembliesToInclude.Add(typeof (ClassInOtherAssembly).GetTypeInfo().Assembly);

// Also do this for all your other 3rd party libraries
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// replaces Xamarin.Forms.Forms.Init(e);
```

Aggiungere una voce per ogni assembly che sono stati aggiunti come riferimento in Esplora soluzioni, tramite un riferimento diretto o NuGet.

#### <a name="dependency-services-and-net-native-compilation"></a>Compilazione di servizi di dipendenza e .NET Native

Compilazione .NET Native build di rilascio può non riuscire a risolvere i servizi di dipendenza definite all'esterno dell'eseguibile di app principale (ad esempio un progetto separato o libreria).

Usare il `DependencyService.Register<T>()` metodo per registrare manualmente classi del servizio di dipendenza. In base all'esempio precedente, aggiungere il metodo register simile al seguente:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
Xamarin.Forms.DependencyService.Register<ClassInOtherAssembly>(); // add this
```
