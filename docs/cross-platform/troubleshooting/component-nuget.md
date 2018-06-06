---
title: Aggiornamento dei riferimenti a componenti di NuGet
description: Questo documento descrive come sostituire i riferimenti a componenti con i pacchetti NuGet a prova di futuro le app, poiché l'archivio di componenti di Xamarin è stata interrotta.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: asb3993
ms.author: amburns
ms.date: 04/18/2018
ms.openlocfilehash: 34f2f5149be63af6abe5aaab197472d067122069
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782034"
---
# <a name="updating-component-references-to-nuget"></a>Aggiornamento dei riferimenti a componenti di NuGet

> [!IMPORTANT]
> L'archivio componenti è stata interrotta a partire da 15 maggio 2018 (la chiusura è stato originariamente [annunciato](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/) nel novembre 2017).
>
> Componenti di Xamarin non sono più supportati in Visual Studio e deve essere sostituiti da pacchetti NuGet. Seguire le istruzioni seguenti per rimuovere manualmente i riferimenti a componenti dai progetti.

Fare riferimento a queste istruzioni per aggiungere pacchetti NuGet on [Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package) oppure [Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Un elenco di Xamarin diffusi [i plug-in e le librerie](https://github.com/xamarin/XamarinComponents/blob/master/README.md) è disponibile per consentire di trovare alternative per i componenti che non sono disponibili come pacchetti NuGet.

## <a name="manually-removing-component-references"></a>Rimozione manuale di riferimenti a componenti

La versione di Visual Studio 15,6 e 7.4: versione di Visual Studio per Mac non supportano più componenti nel progetto. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Se si carica un progetto in Visual Studio, verrà visualizzata la finestra di dialogo seguente, che spiega che è necessario rimuovere tutti i componenti dal progetto manualmente:

![Finestra di dialogo che spiega che un componente è stato trovato nel progetto e deve essere rimosso di avviso](component-nuget-images/component-alert-vs.png)

Per rimuovere un componente dal progetto:

1. Aprire la **csproj** file. A tale scopo, fare clic sul nome del progetto e selezionare **Scarica progetto**. 

2. Fare di nuovo il progetto scaricato e selezionare **modifica csproj {your-progetto-name}**.

3. Trovare i riferimenti nel file `XamarinComponentReference`. Dovrebbe essere simile al seguente:

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

4. Rimuovere i riferimenti a `XamarinComponentReference` e salvare il file. Nell'esempio precedente, è consigliabile rimuovere l'intera `ItemGroup`.

5. Dopo aver salvato il file, fare clic sul nome del progetto e selezionare **Ricarica progetto**.

6. Ripetere i passaggi precedenti per ogni progetto nella soluzione.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Se si carica un progetto in Visual Studio per Mac, verrà visualizzata la finestra di dialogo seguente, che spiega che è necessario rimuovere tutti i componenti dal progetto manualmente:

![Finestra di dialogo che spiega che un componente è stato trovato nel progetto e deve essere rimosso di avviso](component-nuget-images/component-alert.png)

Per rimuovere un componente dal progetto:

1. Aprire il file con estensione csproj. A tale scopo, fare clic sul nome del progetto e selezionare **strumenti > Modifica File**.

2. Trovare i riferimenti nel file `XamarinComponentReference`. Dovrebbe essere simile al seguente:

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

3. Rimuovere i riferimenti a `XamarinComponentReference` e salvare il file. Nell'esempio precedente, è consigliabile rimuovere l'intera `ItemGroup`

4. Ripetere i passaggi precedenti per ogni progetto nella soluzione.

-----

> [!WARNING]
> Le istruzioni seguenti funzionano solo con le versioni precedenti di Visual Studio.
> Il **componenti** nodo non è più disponibile nelle versioni correnti di Visual Studio 2017 o Visual Studio per Mac.

Nelle sezioni seguenti viene descritto come aggiornare le soluzioni esistenti di Xamarin per modificare i riferimenti a componenti ai pacchetti NuGet.

- [Componenti che contengono pacchetti NuGet](#contain)
- [Componenti con le sostituzioni di NuGet](#replace)

La maggior parte dei componenti rientrano in una di queste categorie.
Se si utilizza un componente che sembra non dispone di un pacchetto NuGet equivalente, leggere la [componenti senza un percorso di migrazione NuGet](#require-update) sezione riportata di seguito.

<a name="contain" />

## <a name="components-that-contain-nuget-packages"></a>Componenti che contengono pacchetti NuGet

Molti componenti contengono già i pacchetti NuGet e il percorso di migrazione consiste semplicemente nell'eliminare il riferimento al componente.

È possibile determinare se il componente include già un pacchetto NuGet facendo doppio clic sul componente nella soluzione:

![Nodo componenti espanso](component-nuget-images/solution-sml.png)

Il **pacchetti** scheda elencherà tutti i pacchetti NuGet inclusi nel componente:

![Scheda pacchetti contiene NuGet](component-nuget-images/packages-tab-sml.png)

Si noti che il **assembly** scheda sarà vuota:

![Scheda assembly è vuota](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>Aggiornamento della soluzione

Per aggiornare la soluzione, eliminare il **componente** voce dalla soluzione:

![Eliminare componenti](component-nuget-images/delete-component-sml.png)

Il pacchetto NuGet rimarrà elencato nel **pacchetti** nodo e l'applicazione verrà compilata ed eseguita normalmente. In futuro, verranno eseguiti aggiornamenti a questo pacchetto tramite il **Nuget** funzionalità di aggiornamento:

![Aggiornare il pacchetto NuGet](component-nuget-images/nuget-update-sml.png)


<a name="replace" />

## <a name="components-with-nuget-replacements"></a>Componenti con le sostituzioni di NuGet

Se la pagina di informazioni sul componente **assembly** scheda contiene voci, come illustrato di seguito, è necessario trovare il pacchetto NuGet equivalente manualmente.

![Contiene gli assembly](component-nuget-images/assemblies-tab-sml.png)

Si noti che il **pacchetti** scheda probabilmente saranno vuota:

![](component-nuget-images/packages-tab-empty-sml.png)

_È possibile che contenga le dipendenze di NuGet, ma possono essere ignorati._

Per verificare una sostituzione NuGet pacchetto esiste, eseguire una ricerca in [NuGet.org](https://www.nuget.org/packages), utilizzando il nome del componente, oppure dall'autore.

Ad esempio, è possibile trovare il diffuso **sqlite-net-pcl** pacchetto eseguendo una ricerca per:

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) : il nome del prodotto.
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) : il profilo dell'autore.

### <a name="updating-the-solution"></a>Aggiornamento della soluzione

Dopo aver verificato che il componente è disponibile in NuGet, seguire questi passaggi:

#### <a name="delete-the-component"></a>Eliminare il componente

Fare clic con il pulsante destro sul componente nella soluzione e scegliere **rimuovere**:

![Rimuovere i componenti](component-nuget-images/remove-component-sml.png)

Questa operazione eliminerà il componente e tutti i riferimenti. La compilazione, verrà interrotta finché non si aggiunge il pacchetto NuGet equivalente per sostituirlo.

#### <a name="add-the-nuget-package"></a>Aggiungere il pacchetto NuGet

1. Fare clic su di **pacchetti** nodo e scegliere **Aggiungi pacchetti...** .
2. Ricerca per la sostituzione di NuGet dal nome o dall'autore:

  ![](component-nuget-images/nuget-search-sml.png)

3. Premere **aggiungere pacchetto**.

Il pacchetto NuGet verrà aggiunto al progetto, insieme a eventuali dipendenze.
Questo dovrebbe risolvere la compilazione. Se la compilazione continua a non riuscire, esaminare gli errori per vedere se si sono le differenze tra API tra il componente e il pacchetto NuGet.

<a name="require-update" />

## <a name="components-without-a-nuget-migration-path"></a>Componenti senza un percorso di migrazione di NuGet

Non occorre preoccuparsi se non si trova immediatamente una sostituzione per i componenti utilizzati nell'applicazione. Componenti esistenti continueranno a funzionare in Visual Studio 15,5 e **componenti** nodo verrà visualizzato nella soluzione come di consueto.

Versioni future di Visual Studio, tuttavia, verranno _non_ ripristinare o aggiornare i componenti.
Ciò significa che se si apre la soluzione in un nuovo computer, il componente verrà non scaricato e installato. e l'autore non sarà in grado di fornire gli aggiornamenti. È consigliabile pianificare:

* Estrarre gli assembly del componente e vi fa riferimento direttamente nel progetto.
* Contattare l'autore del componente e chiedere informazioni sui piani di eseguire la migrazione a NuGet.
* Esaminare i pacchetti NuGet alternativi o cercare il codice sorgente, se il componente è open source.

Molti fornitori di componente siano ancora lavorando sulla migrazione a NuGet e altri utenti (inclusi i prodotti disponibili in commercio) potrebbe essere in corso le opzioni di recapito alternativo.


## <a name="related-links"></a>Collegamenti correlati
- [Elenco di diffusi Xamarin Plugins e librerie](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [Installare e utilizzare un pacchetto NuGet (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [Tra cui un pacchetto NuGet (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
