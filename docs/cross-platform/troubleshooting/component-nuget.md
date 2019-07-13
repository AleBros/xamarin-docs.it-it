---
title: Aggiornamento dei riferimenti ai componenti per NuGet
description: Questo documento descrive come sostituire i riferimenti a componenti con i pacchetti NuGet a prova di futuro delle App, poiché Store il componente Xamarin è stato interrotto.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: asb3993
ms.author: amburns
ms.date: 04/18/2018
ms.openlocfilehash: fbef8f60a31511be926ee2adf5e239ad8955b208
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864532"
---
# <a name="updating-component-references-to-nuget"></a>Aggiornamento dei riferimenti ai componenti per NuGet

> [!IMPORTANT]
> Store il componente è stato interrotto a partire dal 15 maggio 2018 (questa chiusura è stato originariamente [annunciato](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/) nel novembre 2017).
>
> I componenti di Xamarin non sono più supportati in Visual Studio e devono essere sostituiti da pacchetti NuGet. Seguire le istruzioni seguenti per rimuovere manualmente riferimenti a componenti dai progetti.

Fare riferimento a queste istruzioni per l'aggiunta di pacchetti NuGet nel [Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package) oppure [Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Un elenco di Xamarin più diffusi [plug-in e le librerie](https://github.com/xamarin/XamarinComponents/blob/master/README.md) è disponibile per consentire di trovare alternative di componenti che non sono disponibili come pacchetti NuGet.

## <a name="manually-removing-component-references"></a>Rimozione manuale di riferimenti a componenti

La versione 15.6 di Visual Studio e versione 7.4 di Visual Studio per Mac non supportano più componenti nel progetto. 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Se si carica un progetto in Visual Studio, viene visualizzata la finestra di dialogo seguente, che spiega che è necessario rimuovere tutti i componenti dal progetto manualmente:

![Finestra di dialogo che spiega che un componente è stato trovato nel progetto e deve essere rimosso di avviso](component-nuget-images/component-alert-vs.png)

Per rimuovere un componente dal progetto:

1. Aprire il **file con estensione csproj** file. A tale scopo, fare doppio clic sul nome del progetto e selezionare **Scarica progetto**. 

2. Fare clic nuovamente sul progetto scaricato e selezionare **modifica {your-project-name} csproj**.

3. Trovare tutti i riferimenti nel file per `XamarinComponentReference`. Dovrebbe essere simile all'esempio seguente:

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

4. Rimuovere i riferimenti a `XamarinComponentReference` e salvare il file. Nell'esempio precedente, è possibile rimuovere l'intera `ItemGroup`.

5. Dopo aver salvato il file, fare doppio clic sul nome del progetto e selezionare **Ricarica progetto**.

6. Ripetere i passaggi precedenti per ogni progetto nella soluzione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Se si carica un progetto in Visual Studio per Mac, viene visualizzata la finestra di dialogo seguente, che spiega che è necessario rimuovere tutti i componenti dal progetto manualmente:

![Finestra di dialogo che spiega che un componente è stato trovato nel progetto e deve essere rimosso di avviso](component-nuget-images/component-alert.png)

Per rimuovere un componente dal progetto:

1. Aprire il file con estensione csproj. A tale scopo, fare doppio clic sul nome del progetto e selezionare **strumenti > Modifica File**.

2. Trovare tutti i riferimenti nel file per `XamarinComponentReference`. Dovrebbe essere simile all'esempio seguente:

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

3. Rimuovere i riferimenti a `XamarinComponentReference` e salvare il file. Nell'esempio precedente, è possibile rimuovere l'intera `ItemGroup`

4. Ripetere i passaggi precedenti per ogni progetto nella soluzione.

-----

> [!WARNING]
> Le istruzioni seguenti funzionano solo con le versioni precedenti di Visual Studio.
> Il **componenti** nodo non è più disponibile nelle versioni correnti di Visual Studio 2017 o Visual Studio per Mac.

Le sezioni seguenti illustrano come aggiornare le soluzioni esistenti di Xamarin per modificare i riferimenti ai componenti per i pacchetti NuGet.

- [Componenti che contengono i pacchetti NuGet](#contain)
- [Componenti con le sostituzioni di NuGet](#replace)

La maggior parte dei componenti rientrano in una di queste categorie.
Se si usa un componente che non dispone di un pacchetto NuGet equivalente, vedere la [componenti senza un percorso di migrazione NuGet](#require-update) sezione riportata di seguito.

<a name="contain" />

## <a name="components-that-contain-nuget-packages"></a>Componenti che contengono i pacchetti NuGet

Molti componenti contengono già i pacchetti NuGet e il percorso di migrazione consiste nell'eliminare il riferimento del componente.

È possibile determinare se il componente include già un pacchetto NuGet facendo doppio clic sul componente nella soluzione:

![Nodo componenti espanso](component-nuget-images/solution-sml.png)

Il **pacchetti** scheda elencherà tutti i pacchetti NuGet inclusi nel componente:

![Scheda dei pacchetti contiene NuGet](component-nuget-images/packages-tab-sml.png)

Si noti che il **assembly** scheda sarà vuota:

![Scheda assembly è vuota](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>L'aggiornamento della soluzione

Per aggiornare la soluzione, eliminare il **componente** voce dalla soluzione:

![Elimina componente](component-nuget-images/delete-component-sml.png)

Il pacchetto NuGet rimarrà elencato nel **pacchetti** nodo e l'app verrà compilata ed eseguita come di consueto. In futuro, verranno eseguiti aggiornamenti a questo pacchetto tramite il **Nuget** funzionalità di aggiornamento:

![Aggiornare il pacchetto NuGet](component-nuget-images/nuget-update-sml.png)


<a name="replace" />

## <a name="components-with-nuget-replacements"></a>Componenti con le sostituzioni di NuGet

Se la pagina di informazioni sul componente **assembly** scheda contiene le voci, come illustrato di seguito, sarà necessario trovare manualmente il pacchetto NuGet equivalente.

![Contiene gli assembly](component-nuget-images/assemblies-tab-sml.png)

Si noti che il **pacchetti** scheda probabilmente sarà vuota:

![](component-nuget-images/packages-tab-empty-sml.png)

_È possibile che contenga le dipendenze di NuGet, ma possono essere ignorati._

Per verificare una sostituzione NuGet il pacchetto è presente, cercare [NuGet.org](https://www.nuget.org/packages), usando il nome del componente, oppure in alternativa dall'autore.

Ad esempio, è possibile trovare il famoso **sqlite-net-libreria di classi portabile** pacchetto eseguendo una ricerca per:

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) : il nome del prodotto.
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) : il profilo dell'autore.

### <a name="updating-the-solution"></a>L'aggiornamento della soluzione

Dopo aver verificato che il componente è disponibile in NuGet, seguire questa procedura:

#### <a name="delete-the-component"></a>Eliminare il componente

Fare clic con il pulsante destro sul componente nella soluzione e scegliere **rimuovere**:

![Rimuovi componente](component-nuget-images/remove-component-sml.png)

Questa operazione eliminerà il componente e tutti i riferimenti. Questa operazione interrompe la compilazione, finché non si aggiunge il pacchetto NuGet equivalente per sostituirlo.

#### <a name="add-the-nuget-package"></a>Aggiungere il pacchetto NuGet

1. Fare clic sui **pacchetti** nodo, quindi scegliere **Aggiungi pacchetti...** .
2. Cercare la sostituzione di NuGet dal nome o dall'autore:

    ![](component-nuget-images/nuget-search-sml.png)

3. Premere **Aggiungi pacchetto**.

Verrà aggiunto il pacchetto NuGet al progetto, insieme a eventuali dipendenze.
Questo dovrebbe risolvere la compilazione. Se la compilazione continua ad avere esito negativo, esaminare gli errori per vedere se si sono verificati differenze tra API tra il componente e il pacchetto NuGet.

<a name="require-update" />

## <a name="components-without-a-nuget-migration-path"></a>Componenti senza un percorso di migrazione di NuGet

Non occorre preoccuparsi se non si trova immediatamente una sostituzione per i componenti utilizzati nell'applicazione. I componenti esistenti continueranno a funzionare in Visual Studio 15.5 e il **componenti** nodo verrà visualizzato nella soluzione come di consueto.

Le versioni future di Visual Studio, tuttavia, verranno _non_ ripristinare o aggiornare i componenti.
Ciò significa che se si apre la soluzione in un nuovo computer, il componente verrà non scaricato e installato; e l'autore non sarà in grado di fornire aggiornamenti. È consigliabile pianificare:

* Estrarre gli assembly del componente e vi fanno riferimento direttamente nel progetto.
* Contattare l'autore del componente e Chiedi informazioni sui piani per eseguire la migrazione a NuGet.
* Analizzare i pacchetti NuGet alternativi, o se il componente è open source di ricerca del codice sorgente.

Molti fornitori di componenti siano ancora lavorando su eseguire la migrazione a NuGet e altri utenti (inclusi i prodotti disponibili in commercio) potrebbero essere in corso le opzioni di recapito alternativi.


## <a name="related-links"></a>Collegamenti correlati
- [Elenco dei più diffusi Xamarin Plugins e librerie](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [Installare e usare un pacchetto NuGet (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [Inserimento di un pacchetto NuGet (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
