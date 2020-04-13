---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: Utilizzo delle librerie C/C
description: Visual Studio per Mac può essere usato per creare e integrare il codice C/C, multipiattaforma, in app per dispositivi mobili per Android e iOS, usando Xamarin e C. Questo articolo spiega come configurare ed eseguire il debug di un progetto in C, in un'app Xamarin.
author: mikeparker104
ms.author: miparker
ms.date: 11/07/2019
ms.openlocfilehash: 42a59570d727657b2f3c23bd9d1f37e1205717d0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73842810"
---
# <a name="use-cc-libraries-with-xamarin"></a>Utilizzo delle librerie C/C

## <a name="overview"></a>Panoramica

Xamarin consente agli sviluppatori di creare app per dispositivi mobili native multipiattaforma con Visual Studio. In genere, le associazioni di C , vengono utilizzate per esporre i componenti della piattaforma esistenti agli sviluppatori. Tuttavia, in alcuni casi le app Xamarin devono lavorare con le codebase esistenti. A volte i team semplicemente non hanno il tempo, il budget o le risorse per il porting di una codebase di grandi dimensioni, ben collaudata e altamente ottimizzata in C.

[Per lo sviluppo per dispositivi mobili multipiattaforma,](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development) il codice C/C e il codice Cè possono essere compilati come parte della stessa soluzione, offrendo molti vantaggi, tra cui un'esperienza di debug unificata. Microsoft ha utilizzato in questo modo il c/c e Xamarin per distribuire app come [Hyperlapse Mobile](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw) e [Pix Camera](https://www.microsoft.com/microsoftpix).

Tuttavia, in alcuni casi c'è il desiderio (o il requisito) di mantenere gli strumenti e i processi C/C , esistenti e di mantenere il codice della libreria disaccoppiato dall'applicazione, trattando la libreria come se fosse simile a un componente di terze parti. In queste situazioni, la sfida non è solo l'esposizione dei membri rilevanti a C , ma la gestione della libreria come dipendenza. E, naturalmente, l'automazione di questo processo possibile.  

Questo post descrive un approccio di alto livello per questo scenario ed esamina un semplice esempio.

## <a name="background"></a>Background

Il linguaggio C/Cè è considerato un linguaggio multipiattaforma, ma è necessario prestare molta attenzione per garantire che il codice sorgente sia effettivamente multipiattaforma, utilizzando solo C/C, supportato da tutti i compilatori di destinazione e contenente poca o nessuna piattaforma inclusa in modo condizionale o codice specifico del compilatore.

In definitiva il codice deve compilare ed eseguire correttamente su tutte le piattaforme di destinazione, quindi questo si riduce alla comunanza tra le piattaforme (e compilatori) di destinazione. I problemi possono ancora derivare da piccole differenze tra i compilatori e da test così approfonditi (preferibilmente automatizzati) su ogni piattaforma di destinazione diventa sempre più importante.  

## <a name="high-level-approach"></a>Approccio di alto livello

L'illustrazione seguente rappresenta l'approccio in quattro fasi utilizzato per trasformare il codice sorgente C/Cè in una libreria Xamarin multipiattaforma condivisa tramite NuGet e quindi utilizzata in un'app Xamarin.Forms.

![Approccio di alto livello per l'utilizzo di C/C](images/cpp-steps.jpg)

Le 4 fasi sono:

1. Compilazione del codice sorgente C/Cè in librerie native specifiche della piattaforma.
2. Wrapping delle librerie native con una soluzione di Visual Studio.Wrapping the native libraries with a Visual Studio solution.
3. Imballaggio ed push di un pacchetto NuGet per il wrapper .NET.
4. Utilizzo del pacchetto NuGet da un'app Xamarin.

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>Fase 1: compilazione del codice sorgente C/C

L'obiettivo di questa fase consiste nel creare librerie native che possono essere chiamate dal wrapper di C. Questo può o non può essere rilevante a seconda della situazione. I numerosi strumenti e processi che possono essere portati a sopportare in questo scenario comune esulano dall'ambito di questo articolo. Le considerazioni chiave sono la sincronizzazione della codebase C/C. 

Le librerie nell'operazione di istruzione a override sono state create utilizzando Visual Studio Code con uno script della shell associato. Una versione estesa di questa procedura utente è disponibile nel [repository Mobile CAT GitHub](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin) che illustra questa parte dell'esempio in modo più approfondito. Le librerie native vengono considerate come una dipendenza di terze parti in questo caso, tuttavia questa fase è illustrata per il contesto.

Per semplicità, la procedura dettagliata è destinata solo a un sottoinsieme di architetture. Per iOS, usa l'utilità lipo per creare un singolo file binario grasso dai singoli file binari specifici dell'architettura. Android utilizzerà file binari dinamici con estensione .so e iOS utilizzerà un file binario grasso statico con estensione .a. 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>Fase 2: wrapping delle librerie native con una soluzione di Visual Studio

La fase successiva consiste nell'eseguire il wrapping delle librerie native in modo che siano facilmente utilizzate da .NET. Questa operazione viene eseguita con una soluzione di Visual Studio con quattro progetti. Un progetto condiviso contiene il codice comune. I progetti destinati a ciascuno dei progetti Xamarin.Android, Xamarin.iOS e .NET Standard consentono di fare riferimento alla libreria in modo indipendente dalla piattaforma.

L'involucro utilizza '[l'esca e switch trucco,'](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)descritto da Paul Betts. Questo non è l'unico modo, ma rende facile fare riferimento alla libreria ed evita la necessità di gestire in modo esplicito le implementazioni specifiche della piattaforma all'interno dell'applicazione consumer stessa. Il trucco è essenzialmente garantire che le destinazioni (.NET Standard, Android, iOS) condividono lo stesso spazio dei nomi, nome dell'assembly e struttura della classe. Poiché NuGet preferirà sempre una libreria specifica della piattaforma, la versione di .NET Standard non viene mai utilizzata in fase di esecuzione.

La maggior parte del lavoro in questo passaggio si concentrerà sull'utilizzo di P/Invoke per chiamare i metodi della libreria nativa e gestire i riferimenti agli oggetti sottostanti. L'obiettivo è quello di esporre la funzionalità della libreria al consumer, astraendo qualsiasi complessità. Gli sviluppatori di Xamarin.Forms non hanno bisogno di avere conoscenze di lavoro sul funzionamento interno della libreria non gestita. Si dovrebbe sentire come se si sta utilizzando qualsiasi altra libreria gestita di C .

In definitiva, l'output di questa fase è un set di librerie .NET, una per destinazione, insieme a un documento nuspec che contiene le informazioni necessarie per compilare il pacchetto nel passaggio successivo.

**Fase 3: imballaggio e push di un pacchetto NuGet per il wrapper .NET**

La terza fase consiste nella creazione di un pacchetto NuGet usando gli elementi di compilazione del passaggio precedente. Il risultato di questo passaggio è un pacchetto NuGet che può essere utilizzato da un'app Xamarin.The outcome from this step is a NuGet package that can be consumed from a Xamarin app. La procedura dettagliata usa una directory locale da utilizzare come feed NuGet.The walkthrough uses a local directory to serve as the NuGet feed. Nell'ambiente di produzione, questo passaggio deve pubblicare un pacchetto in un feed NuGet pubblico o privato e deve essere completamente automatizzato.

**Fase 4: Utilizzo del pacchetto NuGet da un'app Xamarin.Forms**

Il passaggio finale consiste nel fare riferimento e usare il pacchetto NuGet da un'app Xamarin.Forms.The final step is to reference and use the NuGet package from a Xamarin.Forms app. Ciò richiede la configurazione del feed NuGet in Visual Studio per l'utilizzo del feed definito nel passaggio precedente.

Dopo aver configurato il feed, è necessario fare riferimento al pacchetto da ogni progetto nell'app Xamarin.Forms multipiattaforma. 'Il trucco esca-e-switch' fornisce interfacce identiche, in modo che la funzionalità della libreria nativa può essere chiamata utilizzando il codice definito in un'unica posizione.

Il repository del codice sorgente contiene un [elenco di ulteriori letture](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up) che include articoli su come configurare un feed NuGet privato in Azure DevOps e su come eseguire il push del pacchetto in tale feed. Pur richiedendo un po' più di tempo di installazione rispetto a una directory locale, questo tipo di feed è migliore in un ambiente di sviluppo in team.

## <a name="walk-through"></a>Procedura dettagliata

I passaggi forniti sono specifici di **Visual Studio per Mac**, ma la struttura funziona anche in Visual Studio **2017.**

### <a name="prerequisites"></a>Prerequisiti

Per seguire, lo sviluppatore avrà bisogno di:

- [Riga di comando NuGet (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

- [*Visual Studio* *per Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> Per distribuire le app su un iPhone è necessario un [**account sviluppatore Apple**](https://developer.apple.com/) attivo.

## <a name="creating-the-native-libraries-stage-1"></a>Creazione delle librerie native (fase 1)

La funzionalità della libreria nativa è basata sull'esempio tratto da Procedura dettagliata: creazione e utilizzo di [una libreria statica (C )](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017).

In questa procedura dettagliata viene ignorata la prima fase, ovvero la compilazione delle librerie native, poiché la libreria viene fornita come dipendenza di terze parti in questo scenario. Le librerie native precompilate sono incluse insieme al [codice di esempio](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin) o possono essere [scaricate](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts) direttamente.

### <a name="working-with-the-native-library"></a>Utilizzo della libreria nativa

L'esempio *MathFuncsLib* originale include `MyMathFuncs` una singola classe chiamata con la definizione seguente:The original MathFuncsLib example includes a single class called with the following definition:

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

Una classe aggiuntiva definisce le funzioni wrapper che consentono a un `MyMathFuncs` consumer .NET di creare, eliminare e interagire con la classe nativa sottostante.

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

Saranno queste funzioni wrapper che vengono utilizzate sul lato [Xamarin.](https://visualstudio.microsoft.com/xamarin/)

## <a name="wrapping-the-native-library-stage-2"></a>Wrapping della libreria nativa (fase 2)

Questa fase richiede le [librerie precompilate](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts) descritte nella [sezione precedente.](#creating-the-native-libraries-stage-1)

### <a name="creating-the-visual-studio-solution"></a>Creazione della soluzione di Visual StudioCreating the Visual Studio solution

1. In **Visual Studio per Mac**fare clic su Nuovo **progetto** (dalla pagina di *benvenuto)* o **su Nuova soluzione** (dal menu *File).*
2. Nella finestra **Nuovo progetto** scegliere **Progetto condiviso** (dall'interno di *Multipiattaforma > Libreria*) e quindi fare clic su **Avanti**.
3. Aggiornare i seguenti campi, quindi fare clic su **Crea**:

    - **Nome progetto:** MathFuncs.Condiviso  
    - **Nome soluzione:** MathFuncs (Informazioni in base al gruppo dei co  
    - **Località:** Utilizzare il percorso di salvataggio predefinito (o scegliere un'alternativa)   
    - **Creare un progetto all'interno della directory della soluzione:Create** a project within the solution directory: Impostare questa opzione su checked
4. Da **Esplora soluzioni**fare doppio clic sul progetto **MathFuncs.Shared** e passare a **Impostazioni principali**.
5. Rimuovere **. Condiviso** dallo **spazio dei nomi predefinito** in modo che sia impostato solo su **MathFuncs** , quindi fare clic su **OK**.
6. Aprire **MyClass.cs** (creato dal modello), quindi rinominare sia la classe che il nome del file **in MyMathFuncsWrapper** e modificare lo spazio dei nomi **in MathFuncs**.
7. **CTRL e CLICCA** sulla soluzione **MathFuncs**, quindi scegliere **Aggiungi nuovo progetto...** dal menu **Aggiungi.**
8. Nella finestra **Nuovo progetto** scegliere Libreria **standard .NET** (dalla *libreria multipiattaforma >*) e quindi fare clic su **Avanti**.
9. Scegliere **.NET Standard 2.0** e quindi fare clic su **Avanti**.
10. Aggiornare i seguenti campi, quindi fare clic su **Crea**:

    - **Nome progetto:** MathFuncs.Standard  
    - **Località:** Utilizzare lo stesso percorso di salvataggio del progetto condiviso   

11. In **Esplora soluzioni**fare doppio clic sul progetto **MathFuncs.Standard.**
12. Passare a **Impostazioni principali**, quindi aggiornare Spazio dei **nomi predefinito** a **MathFuncs**.
13. Passare alle impostazioni **di output** , quindi aggiornare il **nome dell'assembly** in **MathFuncs**.
14. Passare alle impostazioni **del compilatore** , modificare la **configurazione** per **rilasciare**, impostando Le informazioni di **debug** solo **i simboli,** quindi fare clic su **OK**.
15. Elimina **Class1.cs/Getting avviato** dal progetto (se uno di questi è stato incluso come parte del modello).
16. **CTRL e CLICCA** nella cartella **Dipendenze/Riferimenti** del progetto, quindi scegli **Modifica riferimenti**.
17. Selezionare **MathFuncs.Shared** dalla scheda **Progetti,** quindi fare clic su **OK**.
18. Ripetere i passaggi da 7 a 17 (ignorando il passaggio 9) utilizzando le configurazioni seguenti:

    | **NOME DEL PROGETTO**  | **NOME MODELLO**   | **MENU NUOVO PROGETTO**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs.Android | Libreria di classi       | Libreria > Android      |
    | MathFuncs.iOS     | Libreria di binding     | Libreria > iOS          |

19. Da **Esplora soluzioni**fare doppio clic sul progetto **MathFuncs.Android,** quindi passare alle impostazioni **del compilatore.**

20. Con la **configurazione** impostata su **Debug**, modificare **Definisci simboli** per includere **Android;**.

21. Modificare la **configurazione** in **Rilascio**, quindi modificare **Definisci simboli** per includere anche **Android;**.

22. Ripetere i passaggi 19-20 per **MathFuncs.iOS**, modificando **Definisci simboli** per includere **iOS;** anziché **Android;** in entrambi i casi.

23. Compilare la soluzione nella configurazione **di rilascio** (**CONTROL , COMANDO e B**) e verificare che tutti e tre gli assembly di output (Android, iOS, .NET Standard) (nelle rispettive cartelle bin del progetto) condividano lo stesso nome **MathFuncs.dll**.

In questa fase, la soluzione deve avere tre destinazioni, una a testa per Android, iOS e .NET Standard e un progetto condiviso a cui fa riferimento ognuna delle tre destinazioni. Questi devono essere configurati per utilizzare lo stesso spazio dei nomi predefinito e gli stessi assembly di output con lo stesso nome. Ciò è necessario per l'approccio "bait and switch" menzionato in precedenza.

### <a name="adding-the-native-libraries"></a>Aggiunta delle librerie native

Il processo di aggiunta delle librerie native alla soluzione wrapper varia leggermente tra Android e iOS.

#### <a name="native-references-for-mathfuncsandroid"></a>Riferimenti nativi per MathFuncs.Android

1. **CONTROL : CLICK** nel progetto **MathFuncs.Android** , quindi scegliere **Nuova cartella** dal menu **Aggiungi** denominandola **libs**.

2. Per ogni **ABI** (Application Binary Interface), **CTRL e CLICK** nella cartella **libs,** quindi scegliere **Nuova cartella** dal menu **Aggiungi,** denominandola del rispettivo **ABI**. In questo caso:

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > Per una panoramica più dettagliata, vedere l'argomento [Architetture e CPU](https://developer.android.com/ndk/guides/arch) della [guida per gli sviluppatori NDK](https://developer.android.com/ndk/guides/), in particolare la sezione sulla risoluzione del [codice nativo nei pacchetti di app.](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages)

3. Verificare la struttura delle cartelle:  

    ```folders
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. Aggiungere le librerie **.so** corrispondenti a ciascuna delle cartelle **ABI** in base al mapping seguente:

    **arm64-v8a:** libs/Android/arm64

    **armeabi-v7a:** libs/Android/arm  

    **x86:** libs/Android/x86

    **x86_64:** libs/Android/x86_64

    > [!NOTE]
    > Per aggiungere file, **CTRL e CLICCA** nella cartella che rappresenta il rispettivo **ABI**, quindi scegli **Aggiungi file...** dal menu **Aggiungi.** Scegliere la libreria appropriata (dalla directory **PrecompiledLibs),** quindi fare clic su **Apri** e infine fare clic **su OK** per *copiare il file*nella directory .

5. Per ognuno dei file **.so,** **CTRL e CLICK** quindi scegli l'opzione **EmbeddedNativeLibrary** dal menu Operazione di **compilazione.**

Ora la cartella **libs** dovrebbe apparire come segue:

```folders
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

1. **CONTROL : CLICK** nel progetto **MathFuncs.iOS,** quindi scegliere **Aggiungi riferimento nativo** dal menu **Aggiungi.** 
2. Scegliere la libreria **libMathFuncs.a** (da libs/ios nella directory **PrecompiledLibs),** quindi fare clic su **Apri** 
3. **CTRL : CLICCA** sul file **libMathFuncs** (all'interno della cartella **Riferimenti nativi,** quindi scegli l'opzione **Proprietà** dal menu  
4. Configurare le proprietà **di riferimento nativo** in modo che siano selezionate (che mostrano un'icona a tick) nel riquadro Proprietà:Configure the Native Reference properties so they are checked (showing a tick icon) in the **Properties** Pad:

    - Forza carico
    - È il C
    - Collegamento intelligente

    > [!NOTE]
    > L'uso di un tipo di progetto di libreria di associazione insieme a un [riferimento nativo](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references) incorpora la libreria statica e ne consente di essere collegato automaticamente all'app Xamarin.iOS che vi fa riferimento (anche quando è incluso tramite un pacchetto NuGet).

5. Aprire **ApiDefinition.cs**, eliminando il codice commentato basato su modelli (lasciando solo lo `MathFuncs` spazio dei nomi), quindi eseguire lo stesso passaggio per **Structs.cs** 

    > [!NOTE]
    > Un progetto di libreria di associazione richiede questi file (con il *ObjCBindingApiDefinition* e *ObjCBindingCoreSource* azioni di compilazione) per la compilazione. Tuttavia, scriveremo il codice, per chiamare la nostra libreria nativa, all'esterno di questi file in un modo che può essere condiviso tra destinazioni di libreria Android e iOS utilizzando P/Invoke standard.

### <a name="writing-the-managed-library-code"></a>Scrittura del codice della libreria gestita

A questo punto, scrivere il codice di C , per chiamare la libreria nativa. L'obiettivo è quello di nascondere qualsiasi complessità sottostante. Il consumer non deve avere bisogno di alcuna conoscenza di lavoro degli elementi interni della libreria nativa o dei concetti P/Invoke.  

#### <a name="creating-a-safehandle"></a>Creazione di un SafeHandleCreating a SafeHandle

1. **CTRL e CLICCA** sul progetto **MathFuncs.Shared,** quindi scegliere **Aggiungi file...** dal menu **Aggiungi.** 
2. Scegliere **Classe vuota** dalla finestra Nuovo **file,** denominarla **MyMathFuncsSafeHandle** e quindi fare clic su **Nuovo**
3. Implementare la classe **MyMathFuncsSafeHandle:**

    ```csharp
    using System;
    using Microsoft.Win32.SafeHandles;

    namespace MathFuncs
    {
        internal class MyMathFuncsSafeHandle : SafeHandleZeroOrMinusOneIsInvalid
        {
            public MyMathFuncsSafeHandle() : base(true) { }

            public IntPtr Ptr => handle;

            protected override bool ReleaseHandle()
            {
                // TODO: Release the handle here
                return true;
            }
        }
    }
    ```

    > [!NOTE]
    > Un [SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2) è il modo preferito per lavorare con le risorse non gestite nel codice gestito. Questo astrae un sacco di codice boilerplate relativo alla finalizzazione critica e il ciclo di vita degli oggetti. Il proprietario di questo handle può successivamente trattarlo come qualsiasi altra risorsa gestita e non dovrà implementare il [modello Usa e getta](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose)completo. 

#### <a name="creating-the-internal-wrapper-class"></a>Creazione della classe wrapper interna

1. Aprire **MyMathFuncsWrapper.cs**, modificandola in una classe statica interna

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. Nello stesso file, aggiungere la seguente istruzione condizionale alla classe:

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > In questo modo viene impostato il valore costante **DllName** in base al fatto che la libreria venga compilata per **Android** o **iOS**. Questo per affrontare le diverse convenzioni di denominazione utilizzate da ogni rispettiva piattaforma, ma anche il tipo di libreria utilizzata in questo caso. Android utilizza una libreria dinamica e pertanto si aspetta un nome di file che include l'estensione. Per iOS, '*__Internal*' è obbligatorio poiché si usa una libreria statica.

3. Aggiungere un riferimento a **System.Runtime.InteropServices** all'inizio del file **di MyMathFuncsWrapper.cs**

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. Aggiungere i metodi wrapper per gestire la creazione e l'eliminazione della classe **MyMathFuncs:**

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > Stiamo passando la nostra costante **DllName** per il **DllImport** attributo con il **EntryPoint** che indica in modo esplicito il runtime .NET il nome della funzione da chiamare all'interno di tale libreria. Tecnicamente, non è necessario fornire il valore **EntryPoint** se i nomi dei metodi gestiti erano identici a quello non gestito. Se non ne viene fornito uno, il nome del metodo gestito verrà invece utilizzato come **EntryPoint.** Tuttavia, è meglio essere espliciti.  

5. Aggiungere i metodi wrapper per consentire l'utilizzo della classe **MyMathFuncs** utilizzando il codice seguente:

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
    > Stiamo usando tipi semplici per i parametri in questo esempio. Dal momento che il marshalling è una copia bit per bit in questo caso non richiede alcun lavoro aggiuntivo da parte nostra. Si noti inoltre l'utilizzo della classe **MyMathFuncsSafeHandle** anziché dello standard **IntPtr**. Il **IntPtr** viene automaticamente mappato a **SafeHandle** come parte del processo di marshalling.

6. Verificare che la classe **MyMathFuncsWrapper** finita venga visualizzata come segue:

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

#### <a name="completing-the-mymathfuncssafehandle-class"></a>Completamento della classe MyMathFuncsSafeHandle

1. Aprire la classe **MyMathFuncsSafeHandle,** passare al commento **TODO** segnaposto all'interno del metodo **ReleaseHandle:**

    ```csharp
    // TODO: Release the handle here
    ```

1. Sostituire la riga **TODO:**

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(handle);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>Scrittura della classe MyMathFuncs

Una volta completato il wrapper, creare una classe MyMathFuncs che gestirà il riferimento all'oggetto MyMathFuncs non gestito.  

1. **CTRL e CLICCA** sul progetto **MathFuncs.Shared,** quindi scegliere **Aggiungi file...** dal menu **Aggiungi.** 
2. Scegliere **Classe vuota** dalla finestra Nuovo **file,** denominarla **MyMathFuncs** e quindi fare clic su **Nuovo**
3. Aggiungere i membri seguenti alla classe **MyMathFuncs:**

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. Implementare il costruttore per la classe in modo che crei e memorizzi un handle per l'oggetto **nativo MyMathFuncs** quando viene creata un'istanza della classe:

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. Implementare l'interfaccia IDisposable utilizzando il codice seguente:Implement the **IDisposable** interface using the following code:

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

6. Implementare i metodi **MyMathFuncs** utilizzando la classe **MyMathFuncsWrapper** per eseguire il lavoro reale sotto il cofano passando il puntatore archiviato all'oggetto non gestito sottostante. Il codice dovrebbe essere il seguente:

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

#### <a name="creating-the-nuspec"></a>Creazione del nuspec

Per fare in modo che la libreria venga inserita nel pacchetto e distribuita tramite NuGet, la soluzione necessita di un file **nuspec.** In questo modo verranno identificati gli assembly risultanti che verranno inclusi per ogni piattaforma supportata.

1. **CTRL e CLICK** nella soluzione **MathFuncs**, quindi scegliere **Aggiungi cartella della soluzione** dal menu **Aggiungi** denominandola **SolutionItems**.
2. **CTRL e CLICCA** nella cartella **SolutionItems,** quindi scegliere **Nuovo file...** dal menu **Aggiungi.**
3. Scegliere **File XML vuoto** nella finestra Nuovo **file,** denominarlo **MathFuncs.nuspec,** quindi fare clic su **Nuovo**.
4. Aggiornare MathFuncs.nuspec con i metadati di base del pacchetto da visualizzare al consumer **NuGet.Update** **MathFuncs.nuspec** with the basic package metadata to be displayed to the NuGet consumer. Ad esempio:

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
    > Vedere il documento di [riferimento nuspec](https://docs.microsoft.com/nuget/reference/nuspec) per ulteriori dettagli sullo schema utilizzato per questo manifesto.

5. Aggiungere `<files>` un elemento come `<package>` elemento figlio `<metadata>`dell'elemento (appena sotto `<file>` ), identificando ogni file con un elemento separato:

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > Quando un pacchetto viene installato in un progetto e dove sono presenti più assembly specificati con lo stesso nome, NuGet sceglierà in modo efficace l'assembly più specifico per la piattaforma specificata.

6. Aggiungere `<file>` gli elementi per gli assembly **Android:Add** the elements for the Android assemblies:

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. Aggiungere `<file>` gli elementi per gli assembly **iOS:Add** the elements for the iOS assemblies:

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. Aggiungere `<file>` gli elementi per gli assembly **netstandard2.0:**

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. Verificare il manifesto **nuspec:**

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
    > Questo file specifica i percorsi di output dell'assembly da una build di **rilascio,** quindi assicurarsi di compilare la soluzione utilizzando tale configurazione.

A questo punto, la soluzione contiene 3 assembly .NET e un manifesto **nuspec** di supporto.

## <a name="distributing-the-net-wrapper-with-nuget"></a>Distribuzione del wrapper .NET con NuGetDistributing the .NET wrapper with NuGet

Il passaggio successivo consiste nel creare pacchetti e distribuire il pacchetto NuGet in modo che possa essere facilmente utilizzato dall'app e gestito come dipendenza. Il wrapping e l'utilizzo potrebbero essere tutti eseguiti all'interno di un'unica soluzione, ma la distribuzione della libreria tramite NuGet facilita il disaccoppiamento e ci consente di gestire queste codebase in modo indipendente.

### <a name="preparing-a-local-packages-directory"></a>Preparazione di una directory dei pacchetti locali

La forma più semplice di feed NuGet è una directory locale:

1. Nel **Finder,** passare a una comoda directory. Ad esempio, **/Utenti**.
2. Scegliere **Nuova cartella** dal menu **File,** specificando un nome significativo, ad esempio **local-nuget-feed**.

### <a name="creating-the-package"></a>Creazione del pacchetto

1. Impostare la configurazione di **compilazione** su **Release**ed eseguire una compilazione utilizzando **COMANDO .**
2. Apri **Terminale** e cambia directory nella cartella contenente il file **nuspec.**
3. In **Terminale**, eseguire il comando **nuget pack** specificando il file **nuspec,** la **versione** (ad esempio, 1.0.0) e **OutputDirectory** utilizzando la cartella creata nel [passaggio precedente,](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed)ovvero **local-nuget-feed**. Ad esempio:

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **Verificare** che **MathFuncs.1.0.0.nupkg** sia stato creato nella directory **local-nuget-feed.**

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>[FACOLTATIVO] Uso di un feed NuGet privato con DevOps di AzureUsing a private NuGet feed with Azure DevOps

Una tecnica più affidabile è descritta in [Introduzione ai pacchetti NuGet in Azure DevOps](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package), che mostra come creare un feed privato ed eseguire il push del pacchetto (generato nel passaggio precedente) in tale feed.

È ideale avere questo flusso di lavoro completamente automatizzato, ad esempio usando le pipeline di Azure.It is ideal to have this workflow fully automated, for example using [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts). Per altre informazioni, vedere Introduzione alle pipeline di Azure.For more information, see [Get started with Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts).

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>Utilizzo del wrapper .NET da un'app Xamarin.FormsConsuming the .NET wrapper from a Xamarin.Forms app

Per completare la procedura dettagliata, creare un'app **Xamarin.Forms** per usare il pacchetto appena pubblicato nel feed **NuGet** locale.

### <a name="creating-the-xamarinforms-project"></a>Creazione del progetto **Xamarin.Forms**

1. Aprire una nuova istanza di **Visual Studio per Mac.** Questo può essere fatto dal **Terminale**:

    ```bash
    open -n -a "Visual Studio"
    ```

2. In **Visual Studio per Mac**fare clic su Nuovo **progetto** (dalla pagina di *benvenuto)* o **su Nuova soluzione** (dal menu *File).*
3. Nella finestra **Nuovo progetto** scegliere Applicazione **moduli vuoti** (dall'interno di *Multipiattaforma > App*) e quindi fare clic su **Avanti**.
4. Aggiornare i seguenti campi, quindi fare clic su **Avanti**:

    - **Nome app:** MathFuncsApp.
    - **Identificatore organizzazione:** Utilizzare uno spazio dei nomi inverso, ad esempio _com your_org._
    - **Piattaforme di destinazione:** Usa l'impostazione predefinita (destinazioni Android e iOS).
    - **Codice condiviso:** Impostare questa opzione su .NET Standard (una soluzione "Libreria condivisa" è possibile, ma oltre l'ambito di questa procedura dettagliata).

5. Aggiornare i seguenti campi, quindi fare clic su **Crea**:

    - **Nome progetto:** MathFuncsApp.
    - **Nome soluzione:** MathFuncsApp.  
    - **Località:** Utilizzare il percorso di salvataggio predefinito (o selezionare un'alternativa).

6. In **Esplora soluzioni**, **CTRL e CLICK** sulla destinazione (**MathFuncsApp.Android** o **MathFuncs.iOS**) per il test iniziale, quindi scegliere Imposta come progetto di **avvio**.
7. Scegliere il **dispositivo** preferito o **l'emulatore di simulatore**/**Emulator**. 
8. Eseguire la soluzione (**COMANDO e RITORNO**) per verificare che il progetto **Xamarin.Forms** basato su modelli venga compilato ed eseguito per il corretto. 

    > [!NOTE]
    > **iOS** (in particolare il simulatore) tende ad avere il tempo di compilazione/distribuzione più veloce.iOS (specifically the simulator) tends to have the fastest build/deploy time.

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>Aggiunta del feed NuGet locale alla configurazione NuGet

1. In **Visual Studio**scegliere **Preferenze** (dal menu di **Visual Studio).**
2. Scegliere **Origini** da nella sezione **NuGet** , quindi fare clic su **Aggiungi**.
3. Aggiornare i seguenti campi, quindi fare clic su **Aggiungi origine**:

    - **Nome:** Specificare un nome significativo, ad esempio Local-Packages.Provide a meaningful name, for example, Local-Packages.  
    - **Località:** Specificare la cartella **local-nuget-feed** creata nel [passaggio precedente.](#preparing-a-local-packages-directory)

    > [!NOTE]
    > In questo caso non è necessario specificare un **nome utente** e una **password**. 

4. Fare clic su **OK**.

### <a name="referencing-the-package"></a>Riferimento al pacchetto

Ripetere i passaggi seguenti per ogni progetto (**MathFuncsApp**, **MathFuncsApp.Android**e **MathFuncsApp.iOS**).

1. **CTRL e CLICCA** sul progetto, quindi scegli **Aggiungi pacchetti NuGet...** dal menu **Aggiungi.**
2. Cercare **MathFuncs**. 
3. Verificare che la **versione** del pacchetto sia **1.0.0** e che gli altri dettagli vengano visualizzati come previsto, ad esempio **Titolo** e **Descrizione,** ovvero *MathFuncs* e La libreria wrapper di *esempio C.* 
4. Selezionare il pacchetto **MathFuncs** , quindi fare clic su **Aggiungi pacchetto**.

### <a name="using-the-library-functions"></a>Utilizzo delle funzioni di libreria

A questo punto, con un riferimento al pacchetto **MathFuncs** in ognuno dei progetti, le funzioni sono disponibili per il codice c'è.

1. Aprire **MainPage.xaml.cs** all'interno del progetto **Xamarin.Forms**comune **MathFuncsApp** (a cui fa riferimento **MathFuncsApp.Android e** **MathFuncsApp.iOS**).
2. Aggiungere le istruzioni **using** per **System.Diagnostics** e **MathFuncs** all'inizio del file:

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. Dichiarare un'istanza `MyMathFuncs` della classe all'inizio della `MainPage` classe:

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. Eseguire `OnAppearing` l'override dei metodi e `OnDisappearing` dalla `ContentPage` classe base:

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

5. Aggiornare `OnAppearing` il metodo `myMathFuncs` per inizializzare la variabile dichiarata in precedenza:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. Aggiornare `OnDisappearing` il metodo `Dispose` per `myMathFuncs`chiamare il metodo su :

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. Implementare un metodo privato denominato TestMathFuncs come indicato di seguito:Implement a private method called **TestMathFuncs** as follows:

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

8. Infine, `TestMathFuncs` chiamare alla fine `OnAppearing` del metodo:

    ```csharp
    TestMathFuncs();
    ```

9. Eseguire l'app in ogni piattaforma di destinazione e convalidare l'output nel riquadro **di output dell'applicazione** viene visualizzato come segue:Run the app on each target platform and validate the output in the Application Output Pad appears as follows:

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > Se si verifica un '*DLLNotFoundException*' durante il test su Android, o un errore di compilazione su iOS, assicurarsi di verificare che l'architettura della CPU del dispositivo/emulatore/simulatore che si sta utilizzando sia compatibile con il sottoinsieme che abbiamo scelto di supportare. 

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come creare un'app Xamarin.Forms che usa librerie native tramite un wrapper .NET comune distribuito tramite un pacchetto NuGet.This article explained how to create a Xamarin.Forms app that uses native libraries through a common .NET wrapper distributed via a NuGet package. L'esempio fornito in questa procedura dettagliata è intenzionalmente molto semplicistico per illustrare più facilmente l'approccio. Un'applicazione reale dovrà gestire complessità quali la gestione delle eccezioni, i callback, il marshalling di tipi più complessi e il collegamento con altre librerie di dipendenze. Una considerazione fondamentale è il processo mediante il quale l'evoluzione del codice C , viene coordinata e sincronizzata con le applicazioni wrapper e client. Questo processo può variare a seconda che una o entrambe queste preoccupazioni siano di competenza di un singolo team. In entrambi i casi, l'automazione è un vero vantaggio. Di seguito sono riportate alcune risorse che forniscono ulteriori informazioni su alcuni dei concetti chiave insieme ai download pertinenti. 

### <a name="downloads"></a>Download

- [Strumenti nuGet Command Line (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>Esempi

- [Hyperlapse sviluppo mobile multipiattaforma con C](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft Pix (C, C, Xamarin e Xamarin)](https://devblogs.microsoft.com/xamarin/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Porta di esempio Mono San Angeles](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk/)

### <a name="further-reading"></a>Altre informazioni

[Articoli relativi al contenuto di questo post](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up)