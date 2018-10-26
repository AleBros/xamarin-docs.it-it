---
title: Introduzione a Xamarin Workbooks SDK
description: Questo documento descrive come iniziare a usare il SDK di cartelle di lavoro Xamarin, che possono essere usati per sviluppare le integrazioni per Xamarin Workbooks.
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 5800e98acbff147735ae4a6125979a4b47be2367
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115367"
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>Introduzione a Xamarin Workbooks SDK

Questo documento fornisce una Guida rapida introduttiva con lo sviluppo di integrazioni per Xamarin Workbooks. Gran parte di questo funzionerà con la versione stabile di Xamarin Workbooks, ma **caricamento integrazioni tramite pacchetti NuGet è supportato solo nella versione 1.3 di cartelle di lavoro**, il canale alfa al momento della scrittura.

## <a name="general-overview"></a>Panoramica generale

Integrazioni Xamarin Workbooks sono librerie di piccole dimensioni che utilizzano le [ `Xamarin.Workbooks.Integrations` NuGet] [ nuget] SDK per integrare il Xamarin Workbooks e Inspector agenti per offrire esperienze ottimizzate.

Sono previsti 3 passaggi principali per iniziare lo sviluppo di un'integrazione, ovvero li descriveremo qui.

## <a name="creating-the-integration-project"></a>Creazione del progetto di integrazione

Librerie di integrazione meglio vengono sviluppate come raccolte multipiattaforma. Poiché si desidera fornire la migliore integrazione su tutti gli agenti disponibili, passati e futuri, è opportuno scegliere un set di librerie supportato su vasta scala. È consigliabile usare il modello "Libreria portabile" per il supporto più ampio:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Libreria portabile modello Visual Studio per Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Libreria portabile modello Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

In Visual Studio, è necessario assicurarsi di che selezionare le seguenti piattaforme di destinazione per la libreria portabile:

[![Libreria portabile piattaforme Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

Dopo aver creato il progetto di libreria, aggiungere un riferimento al nostro `Xamarin.Workbooks.Integration` libreria NuGet tramite Gestione pacchetti NuGet.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![NuGet Visual Studio per Mac](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

È opportuno eliminare la classe vuota che viene creato come parte del progetto, ovvero è non sarà più necessario per questo. Dopo aver eseguito questi passaggi, si è pronti per iniziare a compilare l'integrazione.

## <a name="building-an-integration"></a>Creazione di un'integrazione

Verrà compilata una semplice integrazione. È davvero piace il colore verde, quindi si aggiungerà il colore verde come rappresentazione di ogni oggetto. In primo luogo, creare una nuova classe denominata `SampleIntegration`e impostarlo come implementare nostro [ `IAgentIntegration` ] [ integration-type] interfaccia:

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

Ciò che si desidera effettuare è aggiungere una [rappresentazione](~/tools/workbooks/sdk/representations.md) per ogni oggetto che rappresenta un colore verde. A tale scopo Usa un provider di rappresentazione. I provider di ereditano il [ `RepresentationProvider` ] [ reppr] classe, ovvero per noi, è sufficiente eseguire l'override [ `ProvideRepresentations` ] [ prrep]:

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

Viene restituito un [ `Color` ] [ color], una pre-compilato il tipo di rappresentazione in Windows SDK.
Si noterà che in questo caso il tipo restituito è un `IEnumerable<object>` &mdash;un provider di rappresentazione può restituire molti rappresentazioni per un oggetto. Tutti i provider di rappresentazione vengono chiamati per ogni oggetto, pertanto è importante non fare supposizioni su quali oggetti vengono passati all'utente.

Il passaggio finale consiste per registrare il provider di servizi con l'agente e indicare le cartelle di lavoro dove trovare il tipo di integrazione effettivamente. Per registrare il provider, aggiungere questo codice per il `IntegrateWith` metodo nel `SampleIntegration` classe creata in precedenza:

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

Impostare il tipo di integrazione viene eseguito tramite un attributo a livello di assembly. Nel file AssemblyInfo.cs o nella stessa classe come tipo di integrazione per motivi di praticità, è possibile inserire questo:

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

Durante lo sviluppo, può risultare più comodo usare [ `AddProvider` overload] [ addprovider] su `RepresentationManager` che consentono di registrare un callback semplice per fornire rappresentazioni all'interno di una cartella di lavoro e quindi spostare il codice nel `RepresentationProvider` implementazione quando hai finito. Un esempio per il rendering un' [ `OxyPlot` ] [ oxyplot] `PlotModel` simile alla seguente:

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> Queste API offrono un modo rapido per iniziare subito, ma è consigliabile non shipping un'integrazione intera solo usarle&mdash;forniscono un controllo molto limitato sulla modalità di elaborazione dei tipi dal client.

Con la rappresentazione registrata, l'integrazione è pronto per la spedizione.

## <a name="shipping-your-integration"></a>L'integrazione di spedizione

Per fornire l'integrazione, è necessario aggiungerlo a un pacchetto NuGet.
È possibile fornirlo con NuGet della libreria esistente o se si crea un nuovo pacchetto, è possibile utilizzare questo file con estensione nuspec di modello come punto di partenza.
È necessario compilare le sezioni rilevanti per l'integrazione. La parte più importante è che tutti i file per l'integrazione deve avere un `xamarin.interactive` directory nella radice del pacchetto. Ciò ci consente di trovare facilmente tutti i file rilevanti per l'integrazione, indipendentemente dall'utilizzo di un pacchetto esistente o crearne uno nuovo.

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

Dopo aver creato il file con estensione nuspec, si può pack NuGet come segue:

```csharp
nuget pack MyIntegration.nuspec
```

e quindi pubblicarlo per [NuGet][nugetorg]. Dopo che è presente, sarà possibile farvi riferimento da qualsiasi cartella di lavoro e vederlo in azione. Nello screenshot seguente, è stato inserito l'integrazione di esempio che è stato compilato in questo documento e installato il pacchetto NuGet in una cartella di lavoro:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![Cartella di lavoro con l'integrazione di](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Cartella di lavoro con l'integrazione di](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

Si noti che non viene visualizzato alcun `#r` direttive o qualsiasi elemento per inizializzare l'integrazione, ovvero le cartelle di lavoro è eseguita automaticamente tutto ciò è dietro le quinte.

## <a name="next-steps"></a>Passaggi successivi

Check-out a altra documentazione per ulteriori informazioni su di elementi che compongono il SDK, nonché il [integrazioni di esempio](~/tools/workbooks/samples/index.md) ad altre operazioni è possibile usare l'integrazione, come ad esempio JavaScript personalizzato che viene eseguito in il client di cartelle di lavoro.

[integration-type]: https://developer.xamarin.com/api/type/Xamarin.Interactive.IAgentIntegration/
[repman-api]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[xir]: https://developer.xamarin.com/api/namespace/Xamarin.Interactive.Representations/
[reppr]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[prrep]: https://developer.xamarin.com/api/member/Xamarin.Interactive.Representations.RepresentationProvider.ProvideRepresentations/p/System.Object/
[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[addprovider]: https://developer.xamarin.com/api/member/Xamarin.Interactive.Representations.IRepresentationManager.AddProvider/
[oxyplot]: http://www.oxyplot.org/
