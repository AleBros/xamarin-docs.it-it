---
title: Aggiornamento di riferimenti ai componenti in NuGet
description: Questo documento descrive come sostituire i riferimenti ai componenti con i pacchetti NuGet per la prova futura delle app, dal momento che l'archivio componenti Novell è stato interrotto.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: davidortinau
ms.author: daortin
ms.date: 04/18/2018
ms.openlocfilehash: f81df8ac253e53b16c3ab09bf80d66a7b6324854
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571519"
---
# <a name="updating-component-references-to-nuget"></a>Aggiornamento di riferimenti ai componenti in NuGet

> [!IMPORTANT]
> L'archivio componenti è stato interrotto a partire dal 15 maggio 2018 (questa chiusura è stata originariamente [annunciata](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/) nel 2017 novembre).
>
> I componenti Novell non sono più supportati in Visual Studio e devono essere sostituiti dai pacchetti NuGet. Seguire le istruzioni riportate di seguito per rimuovere manualmente i riferimenti ai componenti dai progetti.

Vedere queste istruzioni per l'aggiunta di pacchetti NuGet in [Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package) o [Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

È disponibile un elenco di [librerie e plug](https://github.com/xamarin/XamarinComponents/blob/master/README.md) -in Novell più diffusi che consentono di trovare alternative ai componenti non disponibili come pacchetti NuGet.

## <a name="manually-removing-component-references"></a>Rimozione manuale di riferimenti ai componenti

La versione 15,6 di Visual Studio e la versione 7,4 di Visual Studio per Mac non supportano più i componenti del progetto. 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Se si carica un progetto in Visual Studio, viene visualizzata la finestra di dialogo seguente, in cui viene spiegato che è necessario rimuovere manualmente i componenti dal progetto:

![Finestra di dialogo di avviso che informa che è stato trovato un componente nel progetto e che deve essere rimosso](component-nuget-images/component-alert-vs.png)

Per rimuovere un componente dal progetto:

1. Aprire il file con estensione **csproj**. A tale scopo, fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Scarica progetto**. 

2. Fare di nuovo clic con il pulsante destro del mouse sul progetto scaricato e scegliere **modifica {nome-progetto}. csproj**.

3. Trovare tutti i riferimenti nel file a `XamarinComponentReference` . Dovrebbe essere simile all'esempio seguente:

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

4. Rimuovere i riferimenti a `XamarinComponentReference` e salvare il file. Nell'esempio precedente, è possibile rimuovere l'intero oggetto `ItemGroup` .

5. Una volta salvato il file, fare clic con il pulsante destro del mouse sul nome del progetto e selezionare **Ricarica progetto**.

6. Ripetere i passaggi precedenti per ogni progetto nella soluzione.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Se si carica un progetto in Visual Studio per Mac, viene visualizzata la finestra di dialogo seguente, in cui viene spiegato che è necessario rimuovere manualmente i componenti dal progetto:

![Finestra di dialogo di avviso che informa che è stato trovato un componente nel progetto e che deve essere rimosso](component-nuget-images/component-alert.png)

Per rimuovere un componente dal progetto:

1. Aprire il file con estensione csproj. A tale scopo, fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **strumenti > modifica file**.

2. Trovare tutti i riferimenti nel file a `XamarinComponentReference` . Dovrebbe essere simile all'esempio seguente:

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

3. Rimuovere i riferimenti a `XamarinComponentReference` e salvare il file. Nell'esempio precedente, è possibile rimuovere l'intera`ItemGroup`

4. Ripetere i passaggi precedenti per ogni progetto nella soluzione.

-----

> [!WARNING]
> Le istruzioni seguenti funzionano solo con le versioni precedenti di Visual Studio.
> Il nodo **componenti** non è più disponibile nelle versioni correnti di Visual Studio 2017 o Visual Studio per Mac.

Le sezioni seguenti illustrano come aggiornare le soluzioni Novell esistenti per modificare i riferimenti ai componenti dei pacchetti NuGet.

- [Componenti che contengono pacchetti NuGet](#contain)
- [Componenti con sostituzioni NuGet](#replace)

La maggior parte dei componenti rientra in una delle categorie precedenti.
Se si usa un componente che non ha un pacchetto NuGet equivalente, leggere i [componenti senza una sezione del percorso di migrazione NuGet](#require-update) riportata di seguito.

<a name="contain"></a>

## <a name="components-that-contain-nuget-packages"></a>Componenti che contengono pacchetti NuGet

Molti componenti contengono già pacchetti NuGet e il percorso di migrazione è semplicemente eliminare il riferimento al componente.

È possibile determinare se il componente include già un pacchetto NuGet facendo doppio clic sul componente nella soluzione:

![Nodo componenti espanso](component-nuget-images/solution-sml.png)

Nella scheda **pacchetti** sono elencati tutti i pacchetti NuGet inclusi nel componente:

![Scheda pacchetti contiene NuGet](component-nuget-images/packages-tab-sml.png)

Si noti che la scheda **assembly** è vuota:

![Scheda assembly vuota](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>Aggiornamento della soluzione

Per aggiornare la soluzione, eliminare la voce del **componente** dalla soluzione:

![Elimina componente](component-nuget-images/delete-component-sml.png)

Il pacchetto NuGet rimarrà elencato nel nodo **pacchetti** e l'app verrà compilata ed eseguita come di consueto. In futuro, gli aggiornamenti a questo pacchetto verranno eseguiti tramite la funzionalità di aggiornamento di **NuGet** :

![Aggiornare il pacchetto NuGet](component-nuget-images/nuget-update-sml.png)

<a name="replace"></a>

## <a name="components-with-nuget-replacements"></a>Componenti con sostituzioni NuGet

Se la scheda **assembly** pagina Informazioni componente contiene voci come illustrato di seguito, sarà necessario trovare il pacchetto NuGet equivalente manualmente.

![Contiene assembly](component-nuget-images/assemblies-tab-sml.png)

Si noti che la scheda **pacchetti** sarà probabilmente vuota:

![](component-nuget-images/packages-tab-empty-sml.png)

_Può contenere dipendenze NuGet, ma è possibile ignorarle._

Per confermare l'esistenza di un pacchetto NuGet sostitutivo, cercare in [NuGet.org](https://www.nuget.org/packages), usando il nome del componente o in alternativa per autore.

Ad esempio, è possibile trovare il popolare pacchetto **sqlite-net-PCL** cercando:

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl): nome del prodotto.
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum): profilo dell'autore.

### <a name="updating-the-solution"></a>Aggiornamento della soluzione

Dopo aver confermato che il componente è disponibile in NuGet, attenersi alla procedura seguente:

#### <a name="delete-the-component"></a>Eliminare il componente

Fare clic con il pulsante destro del mouse sul componente nella soluzione e scegliere **Rimuovi**:

![Rimuovi componente](component-nuget-images/remove-component-sml.png)

Questa operazione eliminerà il componente e tutti i riferimenti. In questo modo si interrompe la compilazione, fino a quando non si aggiunge il pacchetto NuGet equivalente per sostituirlo.

#### <a name="add-the-nuget-package"></a>Aggiungere il pacchetto NuGet

1. Fare clic con il pulsante destro del mouse sul nodo **pacchetti** e scegliere **Aggiungi pacchetti...**.
2. Cercare la sostituzione di NuGet in base al nome o all'autore:

    ![](component-nuget-images/nuget-search-sml.png)

3. Premere **Aggiungi pacchetto**.

Il pacchetto NuGet verrà aggiunto al progetto, insieme a tutte le dipendenze.
Questa operazione dovrebbe correggere la compilazione. Se la compilazione continua a non riuscire, esaminare ogni errore per verificare se sono presenti differenze nell'API tra il componente e il pacchetto NuGet.

<a name="require-update"></a>

## <a name="components-without-a-nuget-migration-path"></a>Componenti senza percorso di migrazione NuGet

Non è importante se non si trova immediatamente una sostituzione per i componenti usati nell'applicazione. I componenti esistenti continueranno a funzionare in Visual Studio 15,5 e il nodo **componenti** verrà visualizzato come di consueto nella soluzione.

Le versioni future di Visual Studio, tuttavia, _non_ consentono di ripristinare o aggiornare i componenti.
Ciò significa che se si apre la soluzione in un nuovo computer, il componente non verrà scaricato e installato; e l'autore non sarà in grado di fornire aggiornamenti. È necessario pianificare:

- Estrarre gli assembly dal componente e farvi riferimento direttamente nel progetto.
- Contattare l'autore del componente e richiedere i piani per la migrazione a NuGet.
- Esaminare i pacchetti NuGet alternativi o cercare il codice sorgente se il componente è open source.

Molti fornitori di componenti continuano a lavorare per eseguire la migrazione a NuGet e altri (inclusi i prodotti commercialmente disponibili) potrebbero esaminare opzioni di recapito alternative.

## <a name="related-links"></a>Collegamenti correlati

- [Elenco dei plug-in e delle librerie Novell più diffusi](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [Installare e usare un pacchetto NuGet (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [Inclusione di un pacchetto NuGet (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
