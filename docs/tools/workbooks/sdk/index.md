---
title: Introduzione con Xamarin Workbooks SDK
description: Questo documento descrive come iniziare a usare l'SDK Xamarin Workbooks, che può essere usato per sviluppare integrazioni per Xamarin Workbooks.
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: dd75270b3b14b0b770808bbc3ffc88240f868eae
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511002"
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>Introduzione con Xamarin Workbooks SDK

Questo documento fornisce una guida rapida per iniziare a sviluppare integrazioni per Xamarin Workbooks. Gran parte di questo funziona con la Xamarin Workbooks stabile, ma il **caricamento delle integrazioni tramite i pacchetti NuGet è supportato solo nelle cartelle di lavoro 1,3**, nel canale alfa al momento della stesura del documento.

## <a name="general-overview"></a>Panoramica generale

Xamarin Workbooks integrazioni sono librerie di piccole dimensioni che usano [ `Xamarin.Workbooks.Integrations` NuGet][nuget] SDK per integrarsi con gli agenti Xamarin Workbooks e Inspector per fornire esperienze avanzate.

Ci sono tre passaggi principali per iniziare a sviluppare un'integrazione, che verranno illustrati qui.

## <a name="creating-the-integration-project"></a>Creazione del progetto di integrazione

Le librerie di integrazione sono più sviluppate come librerie multipiattaforma. Poiché si desidera fornire la migliore integrazione su tutti gli agenti disponibili, oltre e in futuro, è consigliabile scegliere un set di librerie supportato a ampio. È consigliabile usare il modello "libreria portatile" per il supporto più ampio:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Modello di libreria portabile Visual Studio per Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Modello di libreria portabile Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

In Visual Studio è necessario assicurarsi di selezionare le piattaforme di destinazione seguenti per la libreria portatile:

[![Piattaforme libreria portabile Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

Dopo aver creato il progetto di libreria, aggiungere un riferimento alla `Xamarin.Workbooks.Integration` libreria NuGet tramite Gestione pacchetti NuGet.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Visual Studio per Mac NuGet](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Visual Studio NuGet](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

È possibile eliminare la classe vuota creata automaticamente come parte del progetto, perché non sarà necessaria per questa operazione. Dopo aver eseguito questi passaggi, si è pronti per iniziare a creare l'integrazione.

## <a name="building-an-integration"></a>Creazione di un'integrazione

Verrà compilata una semplice integrazione. Il colore verde è molto apprezzato, quindi verrà aggiunto il colore verde come rappresentazione a ogni oggetto. Prima di tutto, creare una nuova `SampleIntegration`classe denominata e impostarla come `IAgentIntegration` implementazione dell'interfaccia:

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

È necessario aggiungere una [rappresentazione](~/tools/workbooks/sdk/representations.md) per ogni oggetto che è un colore verde. Questa operazione verrà eseguita usando un provider di rappresentazione. I provider ereditano `RepresentationProvider` dalla classe: per i nostri, è sufficiente eseguire `ProvideRepresentations`l'override di:

```csharp
using Xamarin.Interactive.Representations;

class SampleRepresentationProvider : RepresentationProvider
{
    public override IEnumerable<object> ProvideRepresentations (object obj)
    {
        // This corresponds to Pantone 2250 XGC, our favorite color.
        yield return new Color (0.0, 0.702, 0.4314);
    }
}
```

Viene restituito `Color`, un tipo di rappresentazione precompilata nell'SDK.
Si noterà che il tipo restituito è un `IEnumerable<object>` &mdash;provider di rappresentazione che può restituire molte rappresentazioni per un oggetto. Tutti i provider di rappresentazione vengono chiamati per ogni oggetto, quindi è importante non creare ipotesi sugli oggetti che vengono passati all'utente.

Il passaggio finale consiste nell'eseguire effettivamente la registrazione del provider con l'agente e indicare alle cartelle di lavoro dove trovare il tipo di integrazione. Per registrare il provider, aggiungere il codice seguente al `IntegrateWith` metodo `SampleIntegration` nella classe creata in precedenza:

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

L'impostazione del tipo di integrazione viene eseguita tramite un attributo a livello di assembly. È possibile inserirlo in AssemblyInfo.cs o nella stessa classe del tipo di integrazione per praticità:

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

Durante lo sviluppo, può risultare più comodo usare `AddProvider` gli `RepresentationProvider` overload in `RepresentationManager` che consentono di registrare un semplice callback per fornire rappresentazioni all'interno di una cartella di lavoro e quindi spostare tale codice nell'implementazione una volta operazione completata. Un esempio per il rendering [`OxyPlot`][oxyplot] di un oggetto `PlotModel` potrebbe essere simile al seguente:

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> Queste API offrono un modo rapido per iniziare subito a usare&mdash;, ma non è consigliabile distribuire un'intera integrazione solo se si tratta di un sistema che fornisce un controllo molto ridotto sulla modalità di elaborazione dei tipi da parte del client.

Con la rappresentazione registrata, l'integrazione è pronta per la spedizione.

## <a name="shipping-your-integration"></a>Spedizione dell'integrazione

Per distribuire l'integrazione, è necessario aggiungerla a un pacchetto NuGet.
È possibile spedirlo con il NuGet della libreria esistente o, se si sta creando un nuovo pacchetto, è possibile usare questo file template. NuSpec come punto di partenza.
È necessario compilare le sezioni relative all'integrazione. La parte più importante è che tutti i file per l'integrazione devono trovarsi in una `xamarin.interactive` directory alla radice del pacchetto. In questo modo è possibile trovare facilmente tutti i file rilevanti per l'integrazione, indipendentemente dal fatto che si utilizzi un pacchetto esistente o ne venga creato uno nuovo.

```xml
<?xml version="1.0"?>
<package xmlns="http://schemas.microsoft.com/packaging/2010/07/nuspec.xsd">
    <metadata>
      <id>$YourNuGetPackage$</id>
      <version>$YourVersion$</version>
      <authors>$YourNameHere$</authors>
      <projectUrl>$YourProjectPage$</projectUrl>
      <description>A short description of your library.</description>
    </metadata>
    <files>
      <file src="Path\To\Your\Integration.dll" target="xamarin.interactive" />
    </files>
</package>
```

Dopo aver creato il file con estensione NuSpec, è possibile comprimere il pacchetto NuGet in modo analogo al seguente:

```csharp
nuget pack MyIntegration.nuspec
```

e quindi pubblicarlo in [NuGet][nugetorg]. Al termine, sarà possibile farvi riferimento da qualsiasi cartella di lavoro e visualizzarla in azione. Nello screenshot seguente è stato creato un pacchetto dell'integrazione di esempio che è stata compilata in questo documento e il pacchetto NuGet è stato installato in una cartella di lavoro:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Cartella di lavoro con integrazione](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Cartella di lavoro con integrazione](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

Si noti che non è possibile `#r` visualizzare alcuna direttiva o qualsiasi elemento per inizializzare l'integrazione: le cartelle di lavoro sono state gestite da tutti gli scenari.

## <a name="next-steps"></a>Fasi successive

Consultare l'altra documentazione per altre informazioni sugli elementi mobili che compongono l'SDK e le integrazioni di [esempio](~/tools/workbooks/samples/index.md) per altre operazioni che è possibile eseguire dall'integrazione, ad esempio per fornire codice JavaScript personalizzato che viene eseguito nel client di cartelle di lavoro.

[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[oxyplot]: http://www.oxyplot.org/
