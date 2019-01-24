---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: Usare le librerie C/C++ con Xamarin
description: 'Visual Studio per Mac può essere usato per compilare e integrare codice C/C++ multipiattaforma in App per dispositivi mobili per Android e iOS, usando Xamarin e C#. Questo articolo illustra come configurare ed eseguire il debug di un progetto C++ in un''app Xamarin.'
author: mikeparker104
ms.author: miparker
ms.date: 12/17/20178
---
# <a name="use-cc-libraries-with-xamarin"></a>Usare le librerie C/C++ con Xamarin

## <a name="overview"></a>Panoramica

Xamarin consente agli sviluppatori di creare App per dispositivi mobili native multipiattaforma con Visual Studio. In generale, C# associazioni vengono utilizzate per esporre i componenti esistenti di piattaforma per gli sviluppatori. Tuttavia, esistono volte quando necessità di App Xamarin di lavorare con le basi di codice. In alcuni casi i team semplicemente non hanno il tempo, budget o risorse alla porta a grandi dimensioni, ben collaudato e altamente ottimizzata codebase per C#.

[Visual C++ per lo sviluppo per dispositivi mobili multipiattaforma](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development) Abilita C/C++ e C# codice da compilare come parte della stessa soluzione, che offre numerosi vantaggi tra cui un'esperienza di debug unificata. Microsoft ha usato in questo modo per distribuire le app, ad esempio C/C++ e Xamarin [Hyperlapse Mobile](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw) e [fotocamera Pix](https://www.microsoft.com/microsoftpix).

Tuttavia, in alcuni casi vi è un desiderio (o requisito) mantenere strumenti C/C++ e i processi e per mantenere il codice della libreria di disaccoppiare l'applicazione, considerando la libreria come se fosse analoga a un componente di terze parti esistenti. In queste situazioni, la richiesta di verifica non solo espone i membri rilevanti da C# , ma la libreria come una dipendenza di gestione. E, naturalmente, automatizzare la maggior parte di questo processo possibili.  

In questo post illustra un approccio di alto livello per questo scenario e illustra un esempio semplice.

## <a name="background"></a>Sfondo

C/C++ viene considerato un linguaggio lo sviluppo multipiattaforma, ma è necessario prestare particolare attenzione per assicurarsi che il codice sorgente sia effettivamente lo sviluppo multipiattaforma, tramite C/C++ solo supportati da tutti i compilatori di destinazione e che contiene minime o non piattaforma in modo condizionale incluso o codice specifico del compilatore.

In definitiva il codice deve compilare ed eseguire correttamente anche in tutte le piattaforme di destinazione, che pertanto si tratta per la somiglianza tra le piattaforme (e compilatori) di destinazione. Ancora si verifichino problemi da differenze minime tra i compilatori e quindi completa test (preferibilmente automatizzati) in ogni destinazione piattaforma diventa sempre più importante.  

## <a name="high-level-approach"></a>Approccio generale

Nella figura seguente rappresenta l'approccio di quattro fasi consente di trasformare il codice sorgente C/C++ in una libreria Xamarin multipiattaforma che viene condiviso tramite NuGet e quindi viene utilizzata in un'app xamarin. Forms.
 

![Approccio generale per l'utilizzo di C/C++ con Xamarin](images/cpp-steps.jpg)

Le 4 fasi sono:

1.  La compilazione di codice sorgente C/C++ in librerie native specifiche della piattaforma.
2.  Ritorno a capo le librerie native con una soluzione di Visual Studio.
3.  Creazione di un pacchetto e il push di un pacchetto NuGet per il wrapper .NET.
4.  Utilizza il pacchetto NuGet da un'app Xamarin.

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>Fase 1: La compilazione del codice di origine C/C++ in librerie native specifiche della piattaforma

L'obiettivo di questa fase consiste nel creare librerie native che possono essere chiamate per il C# wrapper. Può supportare o potrebbe non essere pertinente a seconda della situazione. Molti strumenti e processi che possono essere attivati da ricordare in questo scenario comune non rientrano nell'ambito di questo articolo. Le principali considerazioni sono mantenere C/C++ codebase sincronizzato con qualsiasi codice wrapper nativo, unit test, sufficienti e automazione della compilazione. 

Le librerie nella procedura dettagliata sono state create usando Visual Studio Code con uno script shell associato. Una versione estesa di questa procedura dettagliata è reperibile nella [repository GitHub CAT Mobile](https://github.com/xamarin/mobcat/blob/dev/samples/cppwithxamarin/README.md) che illustra questa parte dell'esempio nel modo più approfondito. Le librerie native viene considerate come una dipendenza di terze parti in questo caso tuttavia questa fase è illustrata per il contesto.


Per semplicità, la procedura dettagliata ha come destinazione solo un subset delle architetture. Per iOS, Usa l'utilità lipo per creare una singola file System fat binario dai singoli file binari specifici dell'architettura. Android userà dinamico dei file binari con estensione SO e iOS userà una file System fat statica binaria con estensione a estensione. 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>Fase 2: Ritorno a capo le librerie native con una soluzione di Visual Studio

La fase successiva consiste nell'inserire le librerie native in modo che vengano utilizzati con facilità da .NET. Questa operazione viene eseguita con una soluzione di Visual Studio con quattro progetti. Un progetto condiviso contiene il codice comune. I progetti destinati a ognuno di xamarin. Android, xamarin. IOS e .NET Standard consentono la raccolta a cui fare riferimento in modo indipendente dalla piattaforma.

Degli utilizzi wrapper[lo stratagemma specchietto per le allodole](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/),' descritto da Paul Betts. Ciò non è l'unico modo, ma questo servizio semplifica fare riferimento alla libreria e evita la necessità di gestire in modo esplicito le implementazioni specifiche della piattaforma all'interno dell'applicazione dispendiosa in termini di se stesso. Lo stratagemma consiste essenzialmente nel garantire che le destinazioni (.NET Standard, Android, iOS) condividano lo stesso spazio dei nomi, nome dell'assembly e struttura delle classi. Poiché NuGet preferiranno sempre una libreria specifica della piattaforma, la versione di .NET Standard non è mai usata in fase di esecuzione.

La maggior parte del lavoro in questo passaggio verrà illustrato tramite P/Invoke per chiamare i metodi della libreria nativa e la gestione di riferimenti agli oggetti sottostanti. L'obiettivo consiste nell'esporre la funzionalità della libreria per il consumer durante l'estrazione di qualsiasi complessità. Gli sviluppatori di xamarin. Forms non sono necessario avere familiarità con i meccanismi interni della libreria non gestita. Dovrebbe risultare come qualsiasi altro gestite usano C# library.

In definitiva, l'output di questa fase è un set di librerie .NET, uno per ogni destinazione, insieme a un documento nuspec che contiene le informazioni necessarie per compilare il pacchetto nel passaggio successivo.

**Fase 3: Creazione di un pacchetto e il push di un pacchetto NuGet per il wrapper .NET**

La terza fase consiste nel creare un pacchetto NuGet con gli elementi di compilazione nel passaggio precedente. Il risultato da questo passaggio è un pacchetto NuGet che può essere utilizzato da un'app Xamarin. La procedura dettagliata Usa una directory locale come feed NuGet. Nell'ambiente di produzione, questo passaggio deve pubblicare un pacchetto a un pubblico o privato NuGet feed e devono essere completamente automatizzato.

**Fase 4: Che utilizzano il pacchetto NuGet da un'app xamarin. Forms**

Il passaggio finale consiste per fare riferimento e usare il pacchetto NuGet da un'app xamarin. Forms. Ciò richiede la configurazione di NuGet feed in Visual Studio per usare il feed definito nel passaggio precedente.

Dopo aver configurato il feed, il pacchetto è necessario fare riferimento a ogni progetto nell'app xamarin. Forms multipiattaforma. 'Il trucco bait-and-switch' fornisce interfacce identiche, in modo che la funzionalità della libreria nativa può essere chiamata usando codice definito in un'unica posizione.

Repository del codice sorgente contiene un' [elenco di letture di approfondimento](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up) che include articoli su come configurare un feed su Azure DevOps NuGet privati e su come effettuare il push del pacchetto tale feed. Questa tecnologia richiede un po' più tempo di installazione di una directory locale, questo tipo di feed è preferibile in un ambiente di sviluppo del team.

## <a name="walk-through"></a>Procedura dettagliata

I passaggi descritti sono specifici di **Visual Studio per Mac**, ma il funzionamento della struttura **Visual Studio 2017** anche.

### <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura, lo sviluppatore avrà bisogno:

-   [Riga di comando NuGet (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

-   [*Visual Studio* *per Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> Un oggetto attivo [**Account per sviluppatore Apple**](https://developer.apple.com/) è necessaria per distribuire le App in un iPhone.

## <a name="creating-the-native-libraries-stage-1"></a>Creazione di librerie native (fase 1)

La funzionalità della libreria nativa è basata sull'esempio [procedura dettagliata: Creazione e utilizzo di una libreria statica (C++)](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017).

Questa procedura dettagliata ignora la prima fase, la creazione di librerie native, poiché la libreria viene fornita come una dipendenza di terze parti in questo scenario. Le librerie native precompilate sono incluso con il [esempi di codice](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin) oppure può essere [scaricato](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts) direttamente.

### <a name="working-with-the-native-library"></a>Utilizzo della libreria nativa di

Originale *MathFuncsLib* esempio include una sola classe denominata MyMathFuncs con la definizione seguente: 

```cpp
namespace MathFuncs
{
    class MyMathFuncs
    {
    public:
        double Add(double a, double b);
        double Subtract(double a, double b);
        double Multiply(double a, double b);
        double Divide(double a, double b);
    };
}
```

Una classe aggiuntiva definisce funzioni wrapper che consentono un consumer di .NET creare, eliminare e interagire con la classe MyMathFuncs nativa sottostante.

```cpp
#include "MyMathFuncs.h"
using namespace MathFuncs;

extern "C" {
    MyMathFuncs* CreateMyMathFuncsClass();
    void DisposeMyMathFuncsClass(MyMathFuncs* ptr);
    double MyMathFuncsAdd(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsSubtract(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsMultiply(MyMathFuncs *ptr, double a, double b);
    double MyMathFuncsDivide(MyMathFuncs *ptr, double a, double b);
}
```

Sarà queste funzioni wrapper che vengono usate nel [Xamarin](https://visualstudio.microsoft.com/xamarin/) lato.

## <a name="wrapping-the-native-library-stage-2"></a>Ritorno a capo la libreria nativa (fase 2)

Questa fase richiede la [precompilato librerie](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin/Sample/Artefacts) descritti nel [sezione precedente](https://docs.microsoft.com/xamarin/cross-platform/cpp/index).

### <a name="creating-the-visual-studio-solution"></a>Creazione della soluzione di Visual Studio

1. Nelle **Visual Studio per Mac**, fare clic su **nuovo progetto** (dal *Paginainiziale*) o **nuova soluzione** (dal *File* dal menu).
2. Dal **nuovo progetto** finestra, scegliere **progetto condiviso** (dall'interno *multipiattaforma > libreria*) e quindi fare clic su **Avanti**.
3. Aggiornare i campi seguenti, quindi fare clic su **Create**:

    - **Nome del progetto:** MathFuncs.Shared  
    - **Nome della soluzione:** MathFuncs  
    - **Percorso:** Usare il valore predefinito percorso di salvataggio (o un'alternativa di selezione)   
    - **Creare un progetto nella directory della soluzione:** Impostare questa opzione per selezionata
4. Dal **Esplora soluzioni**, fare doppio clic sulla **MathFuncs.Shared** del progetto e passare a **impostazioni principali**.
5. Rimuovere **. Shared** dal **Default Namespace** in modo da impostarlo su **MathFuncs** solo, quindi fare clic su **OK**.
6. Aprire **MyClass.cs** (creato dal modello), quindi rinominare la classe e nome file in cui **MyMathFuncsWrapper** e modificare lo spazio dei nomi **MathFuncs**.
7. **CTRL + clic** sulla soluzione **MathFuncs**, quindi scegliere **Aggiungi nuovo progetto...**  dal **Add** menu.
8. Dal **nuovo progetto** finestra, scegliere **libreria .NET Standard** (dall'interno *multipiattaforma > libreria*) e quindi fare clic su **Avanti**.
9. Scegli **.NET Standard 2.0** e quindi fare clic su **successivo**.
10. Aggiornare i campi seguenti, quindi fare clic su **Create**:

    - **Nome del progetto:** MathFuncs.Standard  
    - **Percorso:** Usare lo stesso percorso del progetto condiviso di salvataggio   

11. Dal **Esplora soluzioni**, fare doppio clic sulla **MathFuncs.Standard** progetto.
12. Passare a **impostazioni principali**, quindi aggiornare **Namespace predefinito** al **MathFuncs**.
13. Passare al **Output** le impostazioni, quindi aggiornare **il nome dell'Assembly** al **MathFuncs** quindi fare clic su **OK**.
14. Passare al **compilatore** cambiano le impostazioni la **Configuration** a **versione**, l'impostazione **informazioni di Debug** a  **Solo i simboli**.
15. Eliminare **Class1.cs/Getting avviato** dal progetto (se uno di questi è stata incluso come parte del modello).
16. **CTRL + clic** sul progetto **dipendenze/References** cartella, quindi scegliere **Modifica riferimenti**.
17. Selezionare **MathFuncs.Shared** dalle **progetti** tab, quindi fare clic su **OK**.
18. Ripetere i passaggi da 7-17 (ignorando passaggio 9) usando le configurazioni seguenti:

    | **NOME DEL PROGETTO**  | **NOME DEL MODELLO**   | **MENU NUOVO PROGETTO**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs.Android | Libreria di classi       | Android > libreria      |
    | MathFuncs.iOS     | Libreria di binding     | iOS > libreria          |

19. Dal **Esplora soluzioni**, fare doppio clic sul **MathFuncs.Android** del progetto e quindi passare al **compilatore** impostazioni.

20. Con il **Configuration** impostata su **eseguire il Debug**, modificare **Definisci simboli** includere **Android;**.

21. Modifica il **Configuration** al **Release**, quindi modificare **Definisci simboli** per includere anche **Android;**.

22. Ripetere i passaggi da 19-20 per **MathFuncs.iOS**, modifica **Definisci simboli** includere **iOS;** anziché **Android;** in entrambi i casi.

23. Compilare la soluzione **Release** configuration (**controllo + comando + B**) e verificare che tutti gli assembly di output di tre (Android, iOS, .NET Standard) (nelle cartelle bin rispettivo progetto) condividono lo stesso nome **MathFuncs.dll**.

In questa fase, la soluzione deve avere tre destinazioni, uno ciascuno per Android, iOS e .NET Standard e un progetto condiviso a cui viene fatto riferimento da ognuna delle tre destinazioni. Che devono essere configurati per usare gli stessi assembly di output e dello spazio dei nomi predefinito con lo stesso nome. Ciò è necessario per l'approccio di 'specchietto per le allodole' indicato in precedenza.

### <a name="adding-the-native-libraries"></a>Aggiungere le librerie native

Il processo di aggiunta di librerie native per la soluzione wrapper varia leggermente tra Android e iOS.

#### <a name="native-references-for-mathfuncsandroid"></a>Riferimenti nativi per MathFuncs.Android

1. **CTRL + clic** nella **MathFuncs.Android** del progetto, quindi scegliere **nuova cartella** dal **Add** menu denominarlo **libs**.

2. Per ognuno **ABI** (Application Binary Interface), **CTRL + clic** nel **libs** cartella, quindi scegliere ** Nuova cartella** dalle **Add** menu, denominarlo in seguito rispettivi **ABI**. In questo caso:

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > Per una panoramica più dettagliata, vedere la [architetture e le CPU](https://developer.android.com/ndk/guides/arch) argomento dal [Guida per gli sviluppatori NDK](https://developer.android.com/ndk/guides/), in particolare la sezione su come rispondere [codice nativo in pacchetti di applicazioni ](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages).

3. Verificare che la struttura di cartelle:  

    ```
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. Aggiungere corrispondente **SO** librerie a ognuna delle **ABI** cartelle in base al mapping seguente:

    **arm64-v8a:** libs/Android/arm64

    **armeabi-v7a:** libs/Android/arm  

    **x86:** libs/Android/x86

    **x86_64:** libs/Android/x86_64

    > [!NOTE]
    > Per aggiungere file, **CTRL + clic** per la cartella che rappresenta la rispettiva **ABI**, quindi scegliere **Aggiungi file...**  dal **Add** menu. Scegliere la libreria appropriata (dal **PrecompiledLibs** directory) quindi fare clic su **Open** e quindi fare clic su **OK** lasciando l'opzione predefinita *copia di file nella directory*.

5. Per ognuno del **SO** i file, **CTRL + clic** quindi scegliere il **EmbeddedNativeLibrary** opzione il **azione di compilazione** menu.

A questo punto il **libs** cartella apparirà come segue:

```bash
- lib
    - arm64-v8a
        - libMathFuncs.so
    - armeabi-v7a
        - libMathFuncs.so
    - x86 
        - libMathFuncs.so
    - x86_64 
        - libMathFuncs.so
```

#### <a name="native-references-for-mathfuncsios"></a>Riferimenti nativi per MathFuncs.iOS

1. **CTRL + clic** nella **MathFuncs.iOS** del progetto, quindi scegliere **aggiungere riferimento nativo** dal **Aggiungi** menu. 
2. Scegliere il **libMathFuncs.a** libreria (da libs/ios sotto il **PrecompiledLibs** directory) quindi fare clic su **Open** 
3. **CTRL + clic** sul **libMathFuncs** file (all'interno di **riferimenti nativi** cartella, quindi scegliere il **proprietà** dal menu di scelta  
4. Configurare il **riferimento nativo** delle proprietà in modo che queste vengono confrontate (rappresentato da un'icona di segno di graduazione) nel **proprietà** riempimento:
        
    - Caricamento forzato
    - È C++
    - Collegamento intelligente 

    > [!NOTE]
    > Usando un tipo di progetto libreria di binding insieme a un [riferimento nativo](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references) incorpora la libreria statica e consente di essere collegate automaticamente con l'app xamarin. IOS che vi fa riferimento (anche quando viene incluso tramite un pacchetto NuGet). 

5. Aprire **ApiDefinition.cs**, l'eliminazione basata su modelli di codice commentato (lasciando solo il **MathFuncs** dello spazio dei nomi), quindi eseguire la stessa procedura per **Structs.cs** 

    > [!NOTE]
    > Questi file è necessario un progetto di libreria di Binding (con il *ObjCBindingApiDefinition* e *ObjCBindingCoreSource* azioni di compilazione) per compilare. Tuttavia, si scriverà il codice, per chiamare la libreria nativa, di fuori di questi file in modo che possono essere condivisi tra le destinazioni libreria di Android e iOS usando standard di P/Invoke.

### <a name="writing-the-managed-library-code"></a>Scrittura del codice di libreria gestita

A questo punto, scrivere il C# codice per chiamare la libreria nativa. L'obiettivo consiste nel nascondere qualsiasi complessità sottostanti. Il consumer non necessitano di alcuna conoscenza di utilizzo dei componenti interni della libreria nativa o dei concetti di P/Invoke.  

#### <a name="creating-a-safehandle"></a>Creazione di SafeHandle

1. **CTRL + clic** nella **MathFuncs.Shared** del progetto, quindi scegliere **Add File...**  dal **Add** menu. 
2. Scegliere **classe vuota** dalle **nuovo File** finestra, denominarlo **MyMathFuncsSafeHandle** e quindi fare clic su **New**
3. Implementare il **MyMathFuncsSafeHandle** classe:

    ```csharp
    using System;
    using Microsoft.Win32.SafeHandles;

    namespace MathFuncs
    {
        internal class MyMathFuncsSafeHandle : SafeHandleZeroOrMinusOneIsInvalid
        {
            public MyMathFuncsSafeHandle() : base(true) { }

            public IntPtr Ptr => this.handle;

            protected override bool ReleaseHandle()
            {
                // TODO: Release the handle here
                return true;
            }
        }
    }
    ```

    > [!NOTE]
    > Oggetto [SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2) è il modo migliore per lavorare con le risorse non gestite nel codice gestito. Ciò consente di evitare una grande quantità di codice boilerplate correlati al ciclo di vita oggetto e finalizzazione critica. Il proprietario di questo handle può successivamente considerarla come qualsiasi altra risorsa gestita e non sarà necessario implementare la versione completa [modello Disposable](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose). 

#### <a name="creating-the-internal-wrapper-class"></a>Creazione della classe wrapper interna

1. Aprire **MyMathFuncsWrapper.cs**, modificandolo in una classe statica interna

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. Nello stesso file, aggiungere l'istruzione condizionale seguente alla classe:

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > Consente di impostare il **DllName** base indica se la libreria viene compilata per valore costante **Android** o **iOS**. Si tratta per soddisfare le diverse convenzioni di denominazione usate da ogni rispettiva piattaforma, ma anche il tipo di libreria in uso in questo caso. Android Usa una libreria dinamica e pertanto si aspetta che un nome di file, inclusi l'estensione. Per iOS, '*__Internal*' è obbligatorio poiché si sta usando una libreria statica.

3. Aggiungere un riferimento a **InteropServices** nella parte superiore delle **MyMathFuncsWrapper.cs** file

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. Aggiungere i metodi wrapper per gestire la creazione e l'eliminazione delle **MyMathFuncs** classe:

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > Si sta inoltrando la costante **DllName** per il **DllImport** attributo con il **EntryPoint** che indica di in modo esplicito di runtime di .NET il nome della funzione da chiamare all'interno della libreria. Tecnicamente, non è necessario fornire il **EntryPoint** valore se ai nomi di metodo gestito sono stati identici a quello non gestito. Se non ne viene specificato, il nome di metodo gestito potrebbe essere usato come le **EntryPoint** invece. Tuttavia, è preferibile essere espliciti.  

5. Aggiungere i metodi wrapper per consentire di lavorare con i **MyMathFuncs** classe usando il codice seguente:

    ```csharp
    [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
    internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
    internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
    internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

    [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
    internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
    ```

    > [!NOTE]
    > Uso di tipi semplici per i parametri in questo esempio. Poiché il marshalling è una copia bit per bit in questo caso non richiede alcuna attività aggiuntiva da parte nostra. Si noti inoltre l'utilizzo dei **MyMathFuncsSafeHandle** classe invece dello standard **IntPtr**. Il **IntPtr** automaticamente viene eseguito il mapping per il **SafeHandle** come parte del processo di marshalling.

6. Verificare che il termine **MyMathFuncsWrapper** classe viene visualizzata come di seguito:

    ```csharp
    using System.Runtime.InteropServices;

    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
            #if Android
                const string DllName = "libMathFuncs.so";
            #else
                const string DllName = "__Internal";
            #endif

            [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
            internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

            [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
            internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);

            [DllImport(DllName, EntryPoint = "MyMathFuncsAdd")]
            internal static extern double Add(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsSubtract")]
            internal static extern double Subtract(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsMultiply")]
            internal static extern double Multiply(MyMathFuncsSafeHandle ptr, double a, double b);

            [DllImport(DllName, EntryPoint = "MyMathFuncsDivide")]
            internal static extern double Divide(MyMathFuncsSafeHandle ptr, double a, double b);
        }
    }
    ```

#### <a name="completing-the-mymathfuncssafehandle-class"></a>La classe MyMathFuncsSafeHandle completato
1. Aprire il **MyMathFuncsSafeHandle** classe, passare il segnaposto **TODO** commenti all'interno i **ReleaseHandle** (metodo):
    ```csharp
    // TODO: Release the handle here
    ```
2. Sostituire il **TODO** riga:

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(this);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>Scrive la classe MyMathFuncs

Ora che il wrapper è completo, creare una classe MyMathFuncs che gestirà il riferimento all'oggetto MyMathFuncs C++ non gestito.  

1. **CTRL + clic** nella **MathFuncs.Shared** del progetto, quindi scegliere **Add File...**  dal **Add** menu. 
2. Scegliere **classe vuota** dalle **nuovo File** finestra, denominarlo **MyMathFuncs** e quindi fare clic su **New**
3. Aggiungere i membri seguenti per il **MyMathFuncs** classe:

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. Implementare il costruttore della classe in modo che crea e archivia un handle all'oggetto nativo **MyMathFuncs** quando viene creata un'istanza della classe dell'oggetto:

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. Implementare il **IDisposable** interfaccia usando il codice seguente:

    ```csharp
    public class MyMathFuncs : IDisposable
    {
        ...

        protected virtual void Dispose(bool disposing)
        {
            if (handle != null && !handle.IsInvalid)
                handle.Dispose();
        }

        public void Dispose()
        {
            Dispose(true);
            GC.SuppressFinalize(this);
        }

        // ...
    }
    ```

6. Implementare il **MyMathFuncs** metodi usando la **MyMathFuncsWrapper** classe per eseguire il lavoro effettivo dietro le quinte, passando il puntatore è stato archiviato l'oggetto non gestito sottostante. Il codice dovrebbe essere come segue:

    ```csharp
    public double Add(double a, double b)
    {
        return MyMathFuncsWrapper.Add(handle, a, b);
    }

    public double Subtract(double a, double b)
    {
        return MyMathFuncsWrapper.Subtract(handle, a, b);
    }

    public double Multiply(double a, double b)
    {
        return MyMathFuncsWrapper.Multiply(handle, a, b);
    }

    public double Divide(double a, double b)
    {
        return MyMathFuncsWrapper.Divide(handle, a, b);
    }
    ```

#### <a name="creating-the-nuspec"></a>La creazione del nuspec

Per avere la libreria incluso nel pacchetto e distribuiti tramite NuGet, la soluzione richiede un **nuspec** file. Si identificherà cui assembly risultante verrà incluso per ogni piattaforma supportata.

1.  **CTRL + clic** della soluzione **MathFuncs**, quindi scegliere **Aggiungi cartella soluzione** dal **Aggiungi** menu denominarlo  **SolutionItems**.
2.  **CTRL + clic** nel **SolutionItems** cartella, quindi scegliere **nuovo File... **  dalle **Add** menu.
3.  Scegliere **File XML vuoto** dal **nuovo File** finestra, denominarla **MathFuncs.nuspec** e quindi fare clic su **Nuovi**.
4.  Update **MathFuncs.nuspec** con i metadati del pacchetto di base da visualizzare per il **NuGet** consumer. Ad esempio:


    ```xml
    <?xml version="1.0"?>
    <package>
        <metadata>
            <id>MathFuncs</id>
            <version>$version$</version>
            <authors>Microsoft Mobile Customer Advisory Team</authors>
            <description>Sample C++ Wrapper Library</description>
            <requireLicenseAcceptance>false</requireLicenseAcceptance>
            <copyright>Copyright 2018</copyright>
        </metadata>
    </package>
    ```

    > [!NOTE]
    >  Vedere le [di riferimento su nuspec](https://docs.microsoft.com/nuget/reference/nuspec) documento per ulteriori dettagli sullo schema usato per questo manifesto.

5. Aggiungere un `<files>` come figlio dell'elemento di `<package>` elemento (immediatamente sotto `<metadata>`), identificando ogni file con un oggetto separato `<file>` elemento:

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->        

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > Quando un pacchetto viene installato in un progetto e in cui sono presenti più assembly specificati con lo stesso nome, NuGet in modo efficace sceglierà l'assembly che è più specifico per la piattaforma specificata.

6. Aggiungere il `<file>` elementi per il **Android** assembly:

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. Aggiungere il `<file>` elementi per il **iOS** assembly:

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. Aggiungere il `<file>` elementi per il **netstandard2.0** assembly:

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. Verificare i **nuspec** manifesto:

    ```xml
    <?xml version="1.0"?>
    <package>
    <metadata>
        <id>MathFuncs</id>
        <version>$version$</version>
        <authors>Microsoft Mobile Customer Advisory Team</authors>
        <description>Sample C++ Wrapper Library</description>
        <requireLicenseAcceptance>false</requireLicenseAcceptance>
        <copyright>Copyright 2018</copyright>
    </metadata>
    <files>
    
        <!-- Android -->
        <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
        <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
        
        <!-- iOS -->
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
        <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
        
        <!-- netstandard2.0 -->
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
        <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />

    </files>
    </package>
    ```

    > [!NOTE]
    > Questo file specifica i percorsi di output di assembly da un **Release** di compilazione, pertanto assicurarsi di compilare la soluzione usando tale configurazione.

A questo punto, la soluzione contiene 3 assembly .NET e un tipo di supporto **nuspec** manifesto.

## <a name="distributing-the-net-wrapper-with-nuget"></a>Distribuire il wrapper .NET con NuGet

Il passaggio successivo è assemblare e distribuire il pacchetto NuGet, pertanto può essere facilmente utilizzata dall'app e gestita come una dipendenza. Il ritorno a capo e consumo potrebbe essere eseguite tutte in una singola soluzione, ma distribuzione della libreria tramite NuGet facilita la separazione e consente a Microsoft di gestire queste basi di codice in modo indipendente.

### <a name="preparing-a-local-packages-directory"></a>Preparazione di una directory locale dei pacchetti

La forma più semplice dei feed NuGet è una directory locale:

1.  Nelle **Finder**, passare a una directory pratica. Ad esempio, **/Users**.
2.  Scegli **nuova cartella** dal **File** menu, fornire un nome significativo, ad esempio **locale-nuget-feed**.

### <a name="creating-the-package"></a>Creazione del pacchetto

1.  Impostare il **configurazione compilazione** al **Release**ed esegue una compilazione usando **comando + B**.
2.  Aprire **terminale** e passare alla directory alla cartella contenente il **nuspec** file.
3.  Nelle **Terminal**, eseguire il **nuget pack** comando specificando il **nuspec** file, il **versione**  (ad esempio, 1.0.0) e il **OutputDirectory** utilizzo della cartella creata nel [passaggio precedente](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed), vale a dire ** Local-nuget-feed**. Ad esempio:

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **Confermare** che **MathFuncs.1.0.0.nupkg** creato nel **locale-nuget-feed** directory.

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>[FACOLTATIVO] Uso privato del feed NuGet con la metodologia DevOps di Azure

Una tecnica più solida è descritta nella [Introduzione a pacchetti NuGet in Azure DevOps](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package), che mostra come creare un feed privato e il push del pacchetto (generato nel passaggio precedente) per tale feed.

È consigliabile che questo flusso di lavoro completamente automatizzata, ad esempio tramite [pipeline di Azure](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts). Per altre informazioni, vedere [iniziare con le pipeline di Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts).

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>Utilizzo del wrapper .NET da un'app xamarin. Forms
Per completare la procedura dettagliata, creare un **xamarin. Forms** utilizzare semplicemente il pacchetto dell'app pubblicata in locale **NuGet** feed.

### <a name="creating-the-xamarinforms-project"></a>Creazione di **xamarin. Forms** progetto

1. Aprire una nuova istanza della **Visual Studio per Mac**. Questa operazione può essere eseguita dal **Terminal**:

    ```bash
    open -n -a "Visual Studio"
    ```

2. Nelle **Visual Studio per Mac**, fare clic su **nuovo progetto** (dal *Paginainiziale*) o **nuova soluzione** (dal *File* dal menu).
3. Dal **nuovo progetto** finestra, scegliere **App Forms vuota** (dall'interno *multipiattaforma > App*) e quindi fare clic su **Avanti**.
4. Aggiornare i campi seguenti, quindi fare clic su **successivo**:

    - **Nome dell'App:** MathFuncsApp.
    - **Identificatore dell'organizzazione:** Usare uno spazio dei nomi inverso, ad esempio _com. {your_org}_.
    - **Piattaforme di destinazione:** Usare il valore predefinito (destinazioni Android e iOS).
    - **Codice condiviso:** Impostare su .NET Standard (una soluzione "Shared Library" è possibile, ma oltre l'ambito di questa procedura dettagliata).

5. Aggiornare i campi seguenti, quindi fare clic su **Create**:

    - **Nome del progetto:** MathFuncsApp.
    - **Nome della soluzione:** MathFuncsApp.  
    - **Percorso:** Usare il valore predefinito percorso di salvataggio (o scegliere un'alternativa).

6. Nelle **Esplora soluzioni**, **CTRL + clic** nella destinazione (**MathFuncsApp.Android** oppure **MathFuncs.iOS**) per il test iniziale, quindi Scegli **imposta come progetto di avvio**.
7. Scegliere il valore desiderato **periferica** oppure **simulatore**/**emulatore**. 
8. Eseguire la soluzione (**comando + RETURN**) per convalidare che basata su modelli **xamarin. Forms** progetto compilato ed eseguito corretto. 

    > [!NOTE]
    > **iOS** (in particolare il simulatore) tende ad avere il tempo di compilazione/distribuzione più veloce.

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>Aggiunta di NuGet locali feed alla configurazione di NuGet

1. Nelle **Visual Studio**, scegliere **preferenze** (dal **Visual Studio** menu).
2. Scegli **origini** sotto il **NuGet** sezione e quindi fare clic su **Add**.
3. Aggiornare i campi seguenti, quindi fare clic su **Aggiungi origine**:

    - **Nome:** Specificare un nome significativo, ad esempio, i pacchetti locale.  
    - **Percorso:** Specificare il **local-nuget-feed** creato nella cartella le [passaggio precedente](#preparing-a-local-packages-directory).

    > [!NOTE]
    > In questo caso non è necessario specificare una **nomeutente** e **Password**. 

4. Fare clic su **OK**.

### <a name="referencing-the-package"></a>Riferimento al pacchetto

Ripetere i passaggi seguenti per ogni progetto (**MathFuncsApp**, **MathFuncsApp.Android**, e **MathFuncsApp.iOS**).

1. **CTRL + clic** dal progetto, quindi scegliere **Aggiungi pacchetti NuGet...**  dal **Add** menu.
2. Cercare **MathFuncs**. 
3. Verificare i **versione** del pacchetto viene **1.0.0** e altri dettagli vengono visualizzati come previsto, ad esempio il **Title** e **descrizione**, vale a dire , *MathFuncs* e *libreria Wrapper C++ di esempio*. 
4. Selezionare il **MathFuncs** del pacchetto, quindi fare clic su **Add Package**.

### <a name="using-the-library-functions"></a>Usando le funzioni della libreria

A questo punto, con un riferimento per il **MathFuncs** package in ogni progetto, le funzioni sono disponibili per il C# codice.

1.  Aprire **MainPage.xaml.cs** dall'interno di **MathFuncsApp** comuni **xamarin. Forms**progetto (a cui fanno riferimento entrambi **MathFuncsApp.Android** e **MathFuncsApp.iOS**).
2.  Aggiungere **usando** le istruzioni per **System. Diagnostics** e **MathFuncs** nella parte superiore del file:

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. Dichiarare un'istanza del `MyMathFuncs` nella parte superiore della classe di `MainPage` classe:

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. Eseguire l'override di `OnAppearing` e `OnDisappearing` metodi dal `ContentPage` classe di base:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
    }
    ```

5. Aggiorna il `OnAppearing` metodo per inizializzare il `myMathFuncs` variabile dichiarata in precedenza:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. Aggiorna il `OnDisappearing` metodo da chiamare il `Dispose` metodo su `myMathFuncs`:

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. Implementare un metodo privato denominato **TestMathFuncs** come indicato di seguito:

    ```csharp
    private void TestMathFuncs()
    {
        var numberA = 1;
        var numberB = 2;

        // Test Add function
        var addResult = myMathFuncs.Add(numberA, numberB);

        // Test Subtract function
        var subtractResult = myMathFuncs.Subtract(numberA, numberB);

        // Test Multiply function
        var multiplyResult = myMathFuncs.Multiply(numberA, numberB);

        // Test Divide function
        var divideResult = myMathFuncs.Divide(numberA, numberB);

        // Output results
        Debug.WriteLine($"{numberA} + {numberB} = {addResult}");
        Debug.WriteLine($"{numberA} - {numberB} = {subtractResult}");
        Debug.WriteLine($"{numberA} * {numberB} = {multiplyResult}");
        Debug.WriteLine($"{numberA} / {numberB} = {divideResult}");
    }
    ```

8. Infine, chiamare `TestMathFuncs` alla fine del `OnAppearing` metodo:

    ```csharp
    TestMathFuncs();
    ```

9. Eseguire l'app in ciascuna piattaforma di destinazione e convalidare l'output nel **Output applicazione** riquadro viene visualizzato come segue:

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > Se si verifica un '*DLLNotFoundException*' quando il test su Android o un errore di compilazione per iOS, si consiglia di verificare che l'architettura della CPU del dispositivo/emulatore o simulatore si usa sia compatibile con il subset che è stato scelto di supporto tecnico. 

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare un'app xamarin. Forms che usa le librerie native tramite un wrapper .NET comune distribuite tramite il pacchetto NuGet. L'esempio fornito in questa procedura dettagliata è intenzionalmente molto semplice per illustrare più facilmente l'approccio. Un'applicazione reale dovrà occuparsi di complessità, ad esempio la gestione delle eccezioni, i callback, il marshalling dei tipi più complessi e di collegamento ad altre librerie di dipendenza. Un fattore fondamentale è il processo mediante il quale l'evoluzione del codice C++ è coordinato e sincronizzata con le applicazioni client e wrapper. Questo processo può variare a seconda del fatto che uno o entrambi questi problemi sono la responsabilità di un team singolo. In entrambi i casi, l'automazione è dei vantaggi reale. Di seguito sono riportate alcune risorse che fornisce altre informazioni, vedere alcuni dei concetti chiave e i download pertinenti. 

### <a name="downloads"></a>Download

- [Strumenti della riga di comando NuGet (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>Esempi

- [Sviluppo mobile cross-platform di Hyperlapse con C++](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft Pix (C++ e Xamarin)](https://blog.xamarin.com/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Mono San Angeles esempio porta](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)

### <a name="further-reading"></a>Ulteriori informazioni

[Articoli relativi al contenuto di questo post](https://github.com/xamarin/mobcat/tree/master/samples/cpp_with_xamarin#wrapping-up)
