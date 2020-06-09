---
title: Collegamento di app Xamarin.iOS
description: Questo documento descrive il linker Xamarin.iOS, che consente di eliminare il codice non utilizzato da un'applicazione Xamarin.iOS per ridurne le dimensioni.
ms.prod: xamarin
ms.assetid: 3A4B2178-F264-0E93-16D1-8C63C940B2F9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/24/2017
ms.openlocfilehash: 263c48c5006ba0060756e1050497c38dfb7c8eae
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567775"
---
# <a name="linking-xamarinios-apps"></a>Collegamento di app Xamarin.iOS

Durante la compilazione dell'applicazione, Visual Studio per Mac o Visual Studio chiama uno strumento denominato **mtouch** che include un linker per il codice gestito. Questo viene usato per rimuovere dalle librerie di classi le funzionalità che non vengono usate dall'applicazione. L'obiettivo consiste nel ridurre le dimensioni dell'applicazione, che viene fornita solo con i bit necessari.

Il linker usa l'analisi statica per determinare i diversi percorsi del codice che l'applicazione tende a seguire. È un comportamento piuttosto oneroso e deve esaminare ogni dettaglio di ogni assembly, per evitare che vengano rimossi elementi individuabili. Il linker non è abilitato per impostazione predefinita nelle compilazioni del simulatore, in modo da accelerare i tempi di compilazione durante il debug. Tuttavia, poiché produce applicazioni di dimensioni minori e può accelerare la compilazione AOT e il caricamento nel dispositivo, tutte le *compilazioni del dispositivo (rilascio)* usano il linker per impostazione predefinita.

Poiché il linker è uno strumento statico, non può contrassegnare per l'inclusione i tipi e i metodi chiamati tramite reflection o di cui viene creata un'istanza in modo dinamico. Sono disponibili diverse opzioni come soluzioni alternative a questa limitazione.

<a name="Linker_Behavior"></a>

## <a name="linker-behavior"></a>Comportamento del linker

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Il processo di collegamento può essere personalizzato tramite l'elenco a discesa Comportamento del linker in **Opzioni progetto**. Per accedere a questo elenco, fare doppio clic sul progetto iOS e passare a **Compilazione iOS > Opzioni linker**, come mostrato di seguito:

[![](linker-images/image1.png "Linker Options")](linker-images/image1.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Il processo di collegamento può essere personalizzato tramite l'elenco a discesa Comportamento del linker in **Proprietà progetto** in Visual Studio.

Eseguire le operazioni seguenti:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul nome del progetto in **Nome progetto** e quindi scegliere **Proprietà**:

    ![](linker-images/linking01w.png "Right-click on the Project Name in the Solution Explorer and select Properties")
2. In **Proprietà progetto**, selezionare **Compilazione iOS**:

    ![](linker-images/linking02w.png "Select IOS Build")
3. Seguire le istruzioni indicate di seguito per modificare le opzioni di collegamento.

-----

Sono disponibili tre opzioni principali, descritte di seguito:

### <a name="dont-link"></a>Non collegare

La disabilitazione del collegamento fa sì che non venga modificato alcun assembly. Per motivi di prestazioni, questa è l'impostazione predefinita quando l'IDE ha come destinazione il simulatore iOS. Per le compilazioni del dispositivo, questa opzione deve essere usata solo come soluzione alternativa ogni volta che il linker contiene un bug che impedisce l'esecuzione dell'applicazione. Se l'applicazione funziona solo con *-nolink*, inviare un [report sui bug](https://github.com/xamarin/xamarin-macios/issues/new).

Questa impostazione corrisponde all'opzione *-nolink* quando si usa lo strumento da riga di comando mtouch.

<a name="Link_SDK_assemblies_only"></a>

### <a name="link-sdk-assemblies-only"></a>Collega solo assembly SDK

In questa modalità il linker lascia inalterati gli assembly e riduce le dimensioni degli assembly SDK, ovvero il codice incluso in Xamarin.iOS, rimuovendo tutto quello che non viene usato dall'applicazione. Questa è l'impostazione predefinita quando l'IDE ha come destinazione dispositivi iOS.

Si tratta dell'opzione più semplice, perché non richiede alcuna modifica nel codice. La differenza con l'opzione di collegamento di tutti gli assembly è che in questa modalità il linker non può eseguire alcune ottimizzazioni e di conseguenza offre un compromesso tra l'attività necessaria per il collegamento di tutti gli assembly e le dimensioni finali dell'applicazione.

Questa impostazione corrisponde all'opzione *-linksdk* quando si usa lo strumento da riga di comando mtouch.

<a name="Link_all_assemblies"></a>

### <a name="link-all-assemblies"></a>Collega tutti gli assembly

Quando si collegano tutti gli assembly, il linker può usare l'intero set delle ottimizzazioni disponibili per rendere l'applicazione quanto più piccola possibile. Questa opzione modifica il codice utente, che può interrompersi ogni volta che vengono usate funzionalità in un modo che l'analisi statica del linker non è in grado di rilevare. In questi casi, ad esempio con servizi Web, reflection o serializzazione, possono essere necessarie alcune modifiche nell'applicazione per collegare tutti gli assembly.

Questa impostazione corrisponde all'opzione *-linkall* quando si usa lo strumento da riga di comando **mtouch**.

<a name="Controlling_the_Linker"></a>

## <a name="controlling-the-linker"></a>Controllo del linker

Quando viene usato, il linker rimuove talvolta codice che può essere stato chiamato in modo dinamico, anche indirettamente. Per gestire questi casi, il linker offre alcune funzionalità e opzioni per assicurare un controllo maggiore sulle sue azioni.

<a name="Preserving_Code"></a>

### <a name="preserving-code"></a>Mantenimento del codice

Quando viene usato, il linker può talvolta rimuovere codice che può essere stato chiamato in modo dinamico usando System.Reflection.MemberInfo.Invoke oppure esportando i metodi in Objective-C tramite l'attributo `[Export]` e quindi richiamando manualmente il selettore.

In questi casi, è possibile indicare al linker di provare a usare intere classi o di mantenere singoli membri applicando l'attributo `[Xamarin.iOS.Foundation.Preserve]` a livello di classe o a livello di membro. Ogni membro che non è collegato in modo statico dall'applicazione è soggetto alla rimozione. Questo attributo viene quindi usato per contrassegnare i membri non referenziati in modo statico, ma che sono comunque necessari per l'applicazione.

Ad esempio, se si creano istanze dei tipi in modo dinamico, potrebbe essere necessario mantenere il costruttore predefinito dei tipi. Se si usa la serializzazione XML, potrebbe essere necessario mantenere le proprietà dei tipi.

È possibile applicare questo attributo in ogni membro di un tipo oppure nel tipo stesso. Se si vuole mantenere l'intero tipo, è possibile usare la sintassi `[Preserve
(AllMembers = true)]` nel tipo.

A volte è necessario mantenere determinati membri, ma solo se è stato mantenuto il tipo che li contiene. In questi casi, usare `[Preserve (Conditional=true)]`

Se non si vuole definire una dipendenze dalle librerie Xamarin, ad esempio quando si compila una libreria di classi portabile (PCL) multipiattaforma, è comunque possibile usare questo attributo.

A questo scopo, è sufficiente dichiarare una classe PreserveAttribute e usarla nel codice, in questo modo:

```csharp
public sealed class PreserveAttribute : System.Attribute {
    public bool AllMembers;
    public bool Conditional;
}
```

Lo spazio dei nomi in cui è definita la classe non è troppo importante, perché il linker cerca questo attributo in base al nome.

 <a name="Skipping_Assemblies"></a>

### <a name="skipping-assemblies"></a>Esclusione di assembly

È possibile specificare gli assembly che devono essere esclusi dal processo del linker, permettendo il normale collegamento di altri assembly. Questo approccio è utile se è impossibile usare `[Preserve]` in alcuni assembly, ad esempio in codice di terze parti, o come soluzione alternativa temporanea per un bug.

Questo comportamento corrisponde all'opzione `--linkskip` quando si usa lo strumento da riga di comando mtouch.

Se quando si usa l'opzione **Collega tutti gli assembly** si vuole indicare al linker di ignorare interi assembly, inserire il codice seguente nelle opzioni **Argomenti aggiuntivi di mtouch** dell'assembly di primo livello:

```csharp
--linkskip=NameOfAssemblyToSkipWithoutFileExtension
```

Se si vuole che il linker ignori più assembly, includere più argomenti `linkskip`:

```csharp
--linkskip=NameOfFirstAssembly --linkskip=NameOfSecondAssembly
```

Non è disponibile alcuna interfaccia utente per usare questa opzione, che invece può essere specificata nella finestra di dialogo Opzioni progetto di Visual Studio per Mac o nel riquadro Proprietà progetto di Visual Studio, all'interno del campo di testo **Argomenti aggiuntivi di mtouch**. Ad esempio, *--linkskip=mscorlib* non collega mscorlib.dll, ma collega altri assembly nella soluzione.

<a name="Disabling_Link_Away"></a>

### <a name="disabling-link-away"></a>Disabilitazione della rimozione del codice

Il linker rimuove il codice che ha una probabilità minima di essere usato nei dispositivi, ad esempio il codice non supportato o non consentito. In rari casi è possibile che un'applicazione o una libreria dipenda da questo codice (funzionante o meno). A partire da Xamarin.iOS 5.0.1, è possibile indicare al linker di ignorare questa ottimizzazione.

Questo comportamento corrisponde all'opzione *-nolinkaway* quando si usa lo strumento da riga di comando mtouch.

Non è disponibile alcuna interfaccia utente per usare questa opzione, che invece può essere specificata nella finestra di dialogo Opzioni progetto di Visual Studio per Mac o nel riquadro Proprietà progetto di Visual Studio, all'interno del campo di testo **Argomenti aggiuntivi di mtouch**. Ad esempio, *--nolinkaway* non rimuove il codice aggiuntivo (circa 100 KB).

### <a name="marking-your-assembly-as-linker-ready"></a>Contrassegnare l'assembly come pronto per il linker

Gli utenti possono scegliere di collegare solo gli assembly SDK, senza eseguire alcun collegamento al codice.  Questo significa anche che qualsiasi libreria di terze parti che non fa parte dell'SDK di base di Xamarin non verrà collegata.

Questo avviene in genere quando non si vuole aggiungere manualmente attributi `[Preserve]` al codice.  L'effetto collaterale di questa scelta è che le librerie di terze parti non vengono collegate, che corrisponde in genere a un'impostazione predefinita corretta, in quanto non è possibile determinare se una libreria di terze parti supporti o meno il linker.

Se nel progetto è presente una libreria o si è uno sviluppatore di librerie riutilizzabili e si vuole che il linker gestisca l'assembly come collegabile, è sufficiente aggiungere l'attributo a livello di assembly, come indicato di seguito [`LinkerSafe`](xref:Foundation.LinkerSafeAttribute) :

```csharp
[assembly:LinkerSafe]
```

La libreria non deve effettivamente fare riferimento alle librerie Xamarin a questo scopo.  Ad esempio, se si compila una libreria di classi portabile che verrà eseguita in altre piattaforme, è comunque possibile usare un attributo `LinkerSafe`.
Il linker Xamarin cerca l'attributo `LinkerSafe` in base al nome e non al tipo effettivo.  Questo significa che è possibile scrivere codice simile al seguente e che questo codice funzionerà:

```csharp
[assembly:LinkerSafe]
// ... assembly attribute should be at top, before source
class LinkerSafeAttribute : System.Attribute {}
```

## <a name="custom-linker-configuration"></a>Configurazione personalizzata del linker

Seguire le [istruzioni per la creazione di un file di configurazione del linker](~/cross-platform/deploy-test/linker.md).

## <a name="related-links"></a>Collegamenti correlati

- [Configurazione personalizzata del linker](~/cross-platform/deploy-test/linker.md)
- [Collegamento in Mac](~/mac/deploy-test/linker.md)
- [Collegamento in Android](~/android/deploy-test/linker.md)
