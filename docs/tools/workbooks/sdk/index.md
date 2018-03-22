---
title: Introduzione a cartelle di lavoro di Xamarin SDK
ms.topic: article
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 53bc3d1a897eb007cd4c2971b0e3aa3fa01e1238
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2018
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>Introduzione a cartelle di lavoro di Xamarin SDK

Questo documento fornisce una Guida rapida all'introduzione allo sviluppo integrazioni per cartelle di lavoro di Xamarin. La maggior parte di questo funzionerà con le cartelle di lavoro di Xamarin stabile, ma **integrazioni tramite NuGet pacchetti di caricamento è supportato solo in cartelle di lavoro 1.3**, il canale alfa al momento della scrittura.

# <a name="general-overview"></a>Panoramica generale

Integrazioni Xamarin le cartelle di lavoro sono librerie di piccole dimensioni che utilizzano il [ `Xamarin.Workbooks.Integrations` NuGet] [ nuget] SDK per l'integrazione con le cartelle di lavoro di Xamarin e controllo degli agenti per offrire esperienze ottimizzate.

Sono previsti 3 passaggi principali per l'introduzione allo sviluppo di un'integrazione, ovvero viene verrà descritta di seguito.

## <a name="creating-the-integration-project"></a>Creazione del progetto di integrazione

Librerie di integrazione più vengono sviluppate come librerie multipiattaforma. Poiché si desidera fornire l'integrazione migliore in tutti gli agenti disponibili, passati e futuri, è opportuno scegliere un set di librerie ampiamente supportato. È consigliabile utilizzare il modello "Libreria portabile" per il supporto di più ampio:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Libreria portabile modello Visual Studio per Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Libreria portabile modello Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

In Visual Studio, è necessario assicurarsi di selezionare le seguenti piattaforme di destinazione per la libreria portabile:

[![Libreria portabile piattaforme Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

Dopo aver creato il progetto di libreria, aggiungere un riferimento a questo `Xamarin.Workbooks.Integration` libreria NuGet tramite Gestione pacchetti NuGet.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![NuGet di Visual Studio per Mac](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![NuGet di Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

È opportuno eliminare la classe vuota che viene creata come parte del progetto, è non più necessario, per questo oggetto. Dopo aver eseguito questi passaggi, si è pronti per iniziare a creare l'integrazione.

## <a name="building-an-integration"></a>La creazione di un'integrazione

Verrà creata una semplice integrazione. Sono effettivamente molto il colore verde, in modo verrà aggiunto il colore verde come rappresentazione di ogni oggetto. Innanzitutto, creare una nuova classe denominata `SampleIntegration`e implementare il nostro [ `IAgentIntegration` ] [ integration-type] interfaccia:

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

Vogliamo fare è aggiungere una [rappresentazione](~/tools/workbooks/sdk/representations.md) per ogni oggetto che è di colore verde. Verranno eseguite utilizzando un provider di rappresentazione. Provider di ereditano il [ `RepresentationProvider` ] [ reppr] classe, ovvero per interna, è necessario eseguire l'override [ `ProvideRepresentations` ] [ prrep]:

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

Viene restituito un [ `Color` ] [ color], pre-compilato tipo di rappresentazione in Windows SDK.
Si noterà che il tipo restituito è un `IEnumerable<object>` &mdash;un provider di rappresentazione può restituire molti rappresentazioni per un oggetto. Tutti i provider di rappresentazione vengono chiamati per ogni oggetto, pertanto è importante formulare ipotesi in merito a quali oggetti vengono passati all'utente.

Il passaggio finale consiste per registrare il provider con l'agente e indicare le cartelle di lavoro dove trovare il tipo di integrazione effettivamente. Per registrare il provider, aggiungere questo codice per il `IntegrateWith` metodo la `SampleIntegration` classe creata in precedenza:

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

Impostare il tipo di integrazione viene eseguita tramite un attributo a livello di assembly. Nel file AssemblyInfo.cs o nella stessa classe come tipo di integrazione per praticità, è possibile inserire questo:

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

Durante lo sviluppo, può risultare più comodo usare [ `AddProvider` overload] [ addprovider] su `RepresentationManager` che consentono di registrare un callback per fornire rappresentazioni all'interno di una cartella di lavoro semplice e quindi spostare il codice nel `RepresentationProvider` dopo aver terminato di implementazione. Un esempio per il rendering un [ `OxyPlot` ] [ oxyplot] `PlotModel` potrebbe essere simile al seguente:

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> Queste API consentono un modo rapido per ottenere in esecuzione, ma non si consiglia di un intero di integrazione di spedizione solo utilizzando le&mdash;forniscono molto scarso controllo sulla modalità di elaborazione dei tipi dal client.

Con la rappresentazione è registrata, è pronto per fornire l'integrazione.

## <a name="shipping-your-integration"></a>L'integrazione di spedizione

Per fornire l'integrazione, è necessario aggiungerlo a un pacchetto NuGet.
Si spedire con NuGet della libreria esistente o se si crea un nuovo pacchetto, è possibile utilizzare questo file. nuspec del modello come punto di partenza.
È necessario compilare le sezioni pertinenti per l'integrazione. La parte più importante è che tutti i file per l'integrazione devono essere un `xamarin.interactive` directory nella directory principale del pacchetto. Ciò consente di trovare facilmente tutti i file rilevanti per l'integrazione, indipendentemente dall'utilizzo di un pacchetto esistente o crearne uno nuovo.

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

Dopo aver creato il file. nuspec, è possibile comprimere NuGet come illustrato di seguito:

```csharp
nuget pack MyIntegration.nuspec
```

e quindi pubblicarlo su [NuGet][nugetorg]. Una volta è presente, sarà possibile farvi riferimento da qualsiasi cartella di lavoro e visualizzarlo in azione. Nella schermata seguente è stato fornito l'integrazione di esempio che è stato compilato in questo documento e installato il pacchetto NuGet in una cartella di lavoro:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![Cartella di lavoro con l'integrazione](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Cartella di lavoro con l'integrazione](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

Si noti che non viene visualizzato alcun `#r` direttive o qualsiasi elemento per inizializzare l'integrazione, le cartelle di lavoro ha preso in considerazione tutti automaticamente dietro le quinte!

## <a name="next-steps"></a>Passaggi successivi

Consultare la documentazione per ulteriori informazioni sulle parti mobili che costituiscono il SDK, nonché il [esempio integrazioni](~/tools/workbooks/samples/index.md) per operazioni aggiuntive, è possibile eseguire l'integrazione, fornendo JavaScript personalizzato che viene eseguito in il client di cartelle di lavoro.

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
