---
title: Imposta progetti Windows
description: Xamarin.FormsLe soluzioni precedenti, o quelle create in MacOS, non disporranno di progetti di piattaforma UWP (Universal Windows Platform), quindi in questo articolo viene illustrato come aggiungere un nuovo progetto UWP a una Xamarin.Forms soluzione esistente.
ms.prod: xamarin
ms.assetid: A0774D2E-6994-4D91-84E8-DAB66FC92320
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 13b46fd06b0116332241b0d523aea707d56b39ec
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573365"
---
# <a name="setup-windows-projects"></a>Imposta progetti Windows

_Aggiunta di nuovi progetti Windows a una Xamarin.Forms soluzione esistente_

Xamarin.FormsLe soluzioni precedenti, o quelle create in MacOS, non disporranno di progetti di app piattaforma UWP (Universal Windows Platform) (UWP). Sarà quindi necessario aggiungere manualmente un progetto UWP per compilare un'app di Windows 10 (UWP).

## <a name="add-a-universal-windows-platform-app"></a>Aggiungere un'app piattaforma UWP (Universal Windows Platform)

È consigliabile usare **Visual Studio 2019** in **Windows 10** per creare app UWP. Per ulteriori informazioni sulla piattaforma UWP (Universal Windows Platform), vedere [Introduzione al piattaforma UWP (Universal Windows Platform)](/windows/uwp/get-started/universal-application-platform-guide/).

UWP è disponibile in Xamarin.Forms 2,1 e versioni successive, e Xamarin.Forms . Maps è supportato in Xamarin.Forms 2,2 e versioni successive.

Per suggerimenti utili, vedere la sezione <a href="#troubleshooting">risoluzione dei problemi</a> .

Seguire queste istruzioni per aggiungere un'app UWP che viene eseguita su telefoni, tablet e desktop Windows 10:

 1. Fare clic con il pulsante destro del mouse sulla soluzione e scegliere **aggiungi > nuovo progetto** e aggiungere un progetto **app vuota (Windows universale)** :

  ![](universal-images/add-wu.png "Add New Project Dialog")

 2. Nella finestra di dialogo **nuovo progetto di piattaforma UWP (Universal Windows Platform)** selezionare le versioni minima e di destinazione di Windows 10 in cui viene eseguita l'app:

  ![](universal-images/target-version.png "New Universal Windows Platform Project Dialog")

 3. Fare clic con il pulsante destro del mouse sul progetto UWP e scegliere **Gestisci pacchetti NuGet...** e aggiungere il **Xamarin.Forms** pacchetto. Assicurarsi che gli altri progetti della soluzione vengano aggiornati anche alla stessa versione del Xamarin.Forms pacchetto.

 4. Verificare che il nuovo progetto UWP venga compilato nella finestra di **> Configuration Manager di compilazione** . questa operazione probabilmente non è stata eseguita per impostazione predefinita. Seleziona le caselle **Compila** e **Distribuisci** per il progetto universale:

  [![](universal-images/configuration-sml.png "Configuration Manager Window")](universal-images/configuration.png#lightbox "Configuration Manager Window")

 5. Fare clic con il pulsante destro del mouse sul progetto e scegliere **aggiungi > riferimento** e creare un riferimento al Xamarin.Forms progetto dell'applicazione (.NET standard o progetto condiviso).

  ![](universal-images/addref-sml.png "Reference Manager Dialog")

 6. Nel progetto UWP modificare **app.XAML.cs** per includere la chiamata al `Init` metodo all'interno del `OnLaunched` metodo intorno alla riga 52:

```csharp
// under this line
rootFrame.NavigationFailed += OnNavigationFailed;
// add this line
Xamarin.Forms.Forms.Init (e); // requires the `e` parameter
```

 7. Nel progetto UWP modificare **MainPage. XAML** rimuovendo l'oggetto `Grid` contenuto all'interno dell' `Page` elemento.

 8. In **MainPage. XAML**aggiungere una nuova `xmlns` voce per `Xamarin.Forms.Platform.UWP` :

```csharp
xmlns:forms="using:Xamarin.Forms.Platform.UWP"
```

 9. In **MainPage. XAML**, modificare l' `<Page` elemento radice in `<forms:WindowsPage` :

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
...
</forms:WindowsPage>
```

 10. Nel progetto UWP modificare **MainPage.XAML.cs** per rimuovere l'identificatore di `: Page` ereditarietà per il nome della classe (poiché ora eredita da a `WindowsPage` causa della modifica apportata nel passaggio precedente):

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 11. In **MainPage.XAML.cs**aggiungere la `LoadApplication` chiamata nel `MainPage` costruttore per avviare l' Xamarin.Forms app:

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

> [!NOTE]
> L'argomento del `LoadApplication` metodo è l' `Xamarin.Forms.Application` istanza definita nel progetto .NET standard.

<!--
11 . Double-click **Package.appxmanifest** to set these capabilities
  that are often required:

  Capabilities set:

  * Internet (Client)
  * Location
-->

12. Aggiungere le risorse locali, ad esempio file di immagine) dai progetti della piattaforma esistenti richiesti.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="target-invocation-exception-when-using-compile-with-net-native-tool-chain"></a>"Eccezione di chiamata di destinazione" quando si usa la "compilazione con la catena di strumenti .NET Native"

Se l'app UWP fa riferimento a più assembly, ad esempio librerie di controlli di terze parti o se l'app stessa è suddivisa in più librerie, Xamarin.Forms potrebbe non essere in grado di caricare oggetti da tali assembly (ad esempio renderer personalizzati).

Questo problema può verificarsi quando si usa la **catena di strumenti di compilazione con .NET native** , che è un'opzione per le app UWP nella finestra **proprietà > compila > generale** per il progetto.

È possibile risolvere questo problema usando un overload specifico di UWP della `Forms.Init` chiamata in **app.XAML.cs** , come illustrato nel codice seguente (sostituire `ClassInOtherAssembly` con una classe effettiva a cui fa riferimento il codice):

```csharp
// You'll need to add `using System.Reflection;`
List<Assembly> assembliesToInclude = new List<Assembly>();

// Now, add in all the assemblies your app uses
assembliesToInclude.Add(typeof (ClassInOtherAssembly).GetTypeInfo().Assembly);

// Also do this for all your other 3rd party libraries
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// replaces Xamarin.Forms.Forms.Init(e);
```

Aggiungere una voce per ogni assembly aggiunto come riferimento nel Esplora soluzioni tramite un riferimento diretto o un NuGet.

#### <a name="dependency-services-and-net-native-compilation"></a>Servizi di dipendenza e compilazione di .NET Native

Le build di rilascio con .NET Native compilazione possono non riuscire a risolvere i servizi di dipendenza definiti all'esterno del file eseguibile dell'app principale, ad esempio in un progetto o una libreria separata.

Usare il `DependencyService.Register<T>()` metodo per registrare manualmente le classi del servizio di dipendenza. In base all'esempio precedente, aggiungere il metodo Register come indicato di seguito:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
Xamarin.Forms.DependencyService.Register<ClassInOtherAssembly>(); // add this
```
