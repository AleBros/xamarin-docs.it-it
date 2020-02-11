---
title: Introduzione con Xamarin Workbooks SDK
description: Questo documento descrive come iniziare a usare l'SDK Xamarin Workbooks, che può essere usato per sviluppare integrazioni per Xamarin Workbooks.
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: e4a9e9113f83dd89b622de3e1f74f458efd4f07f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018694"
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>Introduzione con Xamarin Workbooks SDK

Questo documento fornisce una guida rapida per iniziare a sviluppare integrazioni per Xamarin Workbooks. Gran parte di questo funziona con la Xamarin Workbooks stabile, ma il **caricamento delle integrazioni tramite i pacchetti NuGet è supportato solo nelle cartelle di lavoro 1,3**, nel canale alfa al momento della stesura del documento.

## <a name="general-overview"></a>Panoramica generale

Xamarin Workbooks integrazioni sono librerie di piccole dimensioni che usano l'SDK [`Xamarin.Workbooks.Integrations` NuGet][nuget] per integrarsi con gli agenti Xamarin Workbooks e Inspector per fornire esperienze migliorate.

Ci sono tre passaggi principali per iniziare a sviluppare un'integrazione, che verranno illustrati qui.

## <a name="creating-the-integration-project"></a>Creazione del progetto di integrazione

Le librerie di integrazione sono più sviluppate come librerie multipiattaforma. Poiché si desidera fornire la migliore integrazione su tutti gli agenti disponibili, oltre e in futuro, è consigliabile scegliere un set di librerie supportato a ampio. È consigliabile usare il modello "libreria portatile" per il supporto più ampio:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![modello di libreria portatile Visual Studio per Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![modello di libreria portabile Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

In Visual Studio è necessario assicurarsi di selezionare le piattaforme di destinazione seguenti per la libreria portatile:

[Piattaforme di libreria portabile![Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

Dopo aver creato il progetto di libreria, aggiungere un riferimento alla libreria NuGet `Xamarin.Workbooks.Integration` tramite Gestione pacchetti NuGet.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Visual Studio per Mac NuGet](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

È possibile eliminare la classe vuota creata automaticamente come parte del progetto, perché non sarà necessaria per questa operazione. Dopo aver eseguito questi passaggi, si è pronti per iniziare a creare l'integrazione.

## <a name="building-an-integration"></a>Creazione di un'integrazione

Verrà compilata una semplice integrazione. Il colore verde è molto apprezzato, quindi verrà aggiunto il colore verde come rappresentazione a ogni oggetto. Prima di tutto, creare una nuova classe denominata `SampleIntegration`e impostarla in modo che implementi l'interfaccia `IAgentIntegration`:

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

È necessario aggiungere una [rappresentazione](~/tools/workbooks/sdk/representations.md) per ogni oggetto che è un colore verde. Questa operazione verrà eseguita usando un provider di rappresentazione. I provider ereditano dalla classe `RepresentationProvider`: per il nostro, è sufficiente eseguire l'override di `ProvideRepresentations`:

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

Viene restituito un `Color`, un tipo di rappresentazione predefinito nell'SDK.
Si noterà che il tipo restituito è un `IEnumerable<object>`&mdash;un provider di rappresentazione può restituire molte rappresentazioni per un oggetto. Tutti i provider di rappresentazione vengono chiamati per ogni oggetto, quindi è importante non creare ipotesi sugli oggetti che vengono passati all'utente.

Il passaggio finale consiste nell'eseguire effettivamente la registrazione del provider con l'agente e indicare alle cartelle di lavoro dove trovare il tipo di integrazione. Per registrare il provider, aggiungere questo codice al metodo `IntegrateWith` nella classe `SampleIntegration` creata in precedenza:

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

L'impostazione del tipo di integrazione viene eseguita tramite un attributo a livello di assembly. È possibile inserirlo in AssemblyInfo.cs o nella stessa classe del tipo di integrazione per praticità:

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

Durante lo sviluppo, può risultare più comodo utilizzare `AddProvider` Overload su `RepresentationManager` che consentono di registrare un callback semplice per fornire rappresentazioni all'interno di una cartella di lavoro, quindi spostare tale codice nell'implementazione del `RepresentationProvider` al termine dell'operazione. Un esempio per il rendering di un `PlotModel` [`OxyPlot`][oxyplot] potrebbe essere simile al seguente:

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> Queste API offrono un modo rapido per iniziare subito a lavorare, ma non è consigliabile distribuire un'intera integrazione solo utilizzandoli&mdash;forniscono un controllo minimo sulla modalità di elaborazione dei tipi da parte del client.

Con la rappresentazione registrata, l'integrazione è pronta per la spedizione.

## <a name="shipping-your-integration"></a>Spedizione dell'integrazione

Per distribuire l'integrazione, è necessario aggiungerla a un pacchetto NuGet.
È possibile spedirlo con il NuGet della libreria esistente o, se si sta creando un nuovo pacchetto, è possibile usare questo file template. NuSpec come punto di partenza.
È necessario compilare le sezioni relative all'integrazione. La parte più importante è che tutti i file per l'integrazione devono trovarsi in una directory `xamarin.interactive` alla radice del pacchetto. In questo modo è possibile trovare facilmente tutti i file rilevanti per l'integrazione, indipendentemente dal fatto che si utilizzi un pacchetto esistente o ne venga creato uno nuovo.

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

[![cartella di lavoro con integrazione](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![cartella di lavoro con integrazione](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

Si noti che non viene visualizzata alcuna direttiva di `#r` o qualsiasi elemento per inizializzare l'integrazione: le cartelle di lavoro si sono occupate di tutto questo per l'utente dietro le quinte.

## <a name="next-steps"></a>Passaggi successivi

Consultare l'altra documentazione per altre informazioni sugli elementi mobili che compongono l'SDK e le [integrazioni di esempio](~/tools/workbooks/samples/index.md) per altre operazioni che è possibile eseguire dall'integrazione, ad esempio per fornire codice JavaScript personalizzato che viene eseguito nel client di cartelle di lavoro.

[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[oxyplot]: http://www.oxyplot.org/
