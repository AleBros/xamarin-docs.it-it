---
ms.assetid: EA2D979E-9151-4CE9-9289-13B6A979838B
title: Usare C/C++ librerie con Xamarin
description: Visual Studio per Mac può essere usato per compilare e integrare C/C++ codice multipiattaforma nelle app per dispositivi mobili per Android e iOS, usando Xamarin C#e. Questo articolo illustra come configurare ed eseguire il debug di C++ un progetto in un'app Xamarin.
author: mikeparker104
ms.author: miparker
ms.date: 11/07/2019
ms.openlocfilehash: 42a59570d727657b2f3c23bd9d1f37e1205717d0
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842810"
---
# <a name="use-cc-libraries-with-xamarin"></a>Usare C/C++ librerie con Xamarin

## <a name="overview"></a>Panoramica

Xamarin consente agli sviluppatori di creare app per dispositivi mobili native multipiattaforma con Visual Studio. In genere C# , le associazioni vengono utilizzate per esporre i componenti della piattaforma esistenti agli sviluppatori. In alcuni casi, tuttavia, le app Xamarin devono usare basi di codice esistenti. A volte i team non hanno il tempo, il budget o le risorse per trasferire una codebase di C#grandi dimensioni, ben collaudata e altamente ottimizzata.

[Visual C++ per lo sviluppo di app per dispositivi mobili multipiattaforma](https://docs.microsoft.com/visualstudio/cross-platform/visual-cpp-for-cross-platform-mobile-development) consente C# di compilare il codice C/C++ e come parte della stessa soluzione, offrendo molti vantaggi, tra cui un'esperienza di debug unificata. Microsoft ha usato C/C++ e Xamarin in questo modo per fornire app come la fotocamera con [dispositivi mobili iperscaduti](https://www.microsoft.com/p/hyperlapse-mobile/9wzdncrd1prw) e [pix](https://www.microsoft.com/microsoftpix).

Tuttavia, in alcuni casi è preferibile (o requisito) che i processi C/C++ strumenti e i processi esistenti vengano mantenuti e che il codice di libreria venga separato dall'applicazione, considerando la libreria come se fosse simile a un componente di terze parti. In queste situazioni, la sfida non solo espone i membri rilevanti a C# ma gestisce la libreria come dipendenza. E, naturalmente, automatizzare il maggior parte del processo possibile.  

Questo post illustra un approccio di alto livello per questo scenario ed esamina un semplice esempio.

## <a name="background"></a>Sfondo

C/C++ è considerato un linguaggio multipiattaforma, ma è necessario prestare particolare attenzione per garantire che il codice sorgente sia in realtà multipiattaforma, usando solo C/C++ supportato da tutti i compilatori di destinazione e che contenga una piattaforma o un codice specifico del compilatore senza condizionale.

Infine, il codice deve essere compilato ed eseguito correttamente in tutte le piattaforme di destinazione, quindi questa operazione si riduce alla comunanza tra le piattaforme (e i compilatori) di destinazione. I problemi possono ancora verificarsi da piccole differenze tra i compilatori e, di conseguenza, un test completo (preferibilmente automatizzato) in ogni piattaforma di destinazione diventa sempre più importante.  

## <a name="high-level-approach"></a>Approccio di alto livello

La figura seguente rappresenta l'approccio in quattro fasi usato per trasformare il codiceC++ C/sorgente in una libreria Xamarin multipiattaforma condivisa tramite NuGet e quindi viene utilizzata in un'app Xamarin.Forms.

![Approccio di alto livello per l'utilizzo diC++ C/con Xamarin](images/cpp-steps.jpg)

Le quattro fasi sono:

1. Compilazione del codice C/C++ source nelle librerie native specifiche della piattaforma.
2. Wrapping delle librerie native con una soluzione di Visual Studio.
3. Compressione e push di un pacchetto NuGet per il wrapper .NET.
4. Utilizzo del pacchetto NuGet da un'app Xamarin.

### <a name="stage-1-compiling-the-cc-source-code-into-platform-specific-native-libraries"></a>Fase 1: compilazione del codice sorgente/C++ C nelle librerie native specifiche della piattaforma

L'obiettivo di questa fase è creare librerie native che possono essere chiamate dal C# wrapper. Questo potrebbe non essere pertinente a seconda della situazione. I numerosi strumenti e processi che possono essere portati in questo scenario comune esulano dall'ambito di questo articolo. Le considerazioni principali sono la sincronizzazione diC++ C/codebase con qualsiasi codice wrapper nativo, testing unità sufficiente e automazione della compilazione. 

Le librerie nella procedura dettagliata sono state create usando Visual Studio Code con uno script della shell associato. Una versione estesa di questa procedura dettagliata è reperibile nel repository di [GitHub per dispositivi mobili](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin) che illustra questa parte dell'esempio in modo più approfondito. Le librerie native vengono considerate come dipendenze di terze parti in questo caso, tuttavia questa fase viene illustrata per il contesto.

Per semplicità, la procedura dettagliata è destinata solo a un subset di architetture. Per iOS, usa l'utilità Lipo per creare un singolo file binario Fat dai singoli file binari specifici dell'architettura. Android utilizzerà i binari dinamici con. Pertanto, l'estensione e iOS utilizzeranno un file binario Fat statico con estensione. 

### <a name="stage-2-wrapping-the-native-libraries-with-a-visual-studio-solution"></a>Fase 2: wrapping delle librerie native con una soluzione di Visual Studio

La fase successiva consiste nel eseguire il wrapping delle librerie native in modo che siano facilmente utilizzabili da .NET. Questa operazione viene eseguita con una soluzione di Visual Studio con quattro progetti. Un progetto condiviso contiene il codice comune. I progetti destinati a ogni Xamarin.Android, Xamarin.iOS e .NET Standard consentono di fare riferimento alla libreria in modo indipendente dalla piattaforma.

Il wrapper USA '[The Bait and switch Trick](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)', descritto da Paul Betts. Questo non è l'unico modo, ma semplifica la creazione di un riferimento alla libreria evitando la necessità di gestire in modo esplicito le implementazioni specifiche della piattaforma all'interno dell'applicazione stessa. Il trucco è essenzialmente garantire che le destinazioni (.NET Standard, Android, iOS) condividano lo stesso spazio dei nomi, il nome dell'assembly e la struttura della classe. Poiché NuGet preferisce sempre una libreria specifica della piattaforma, la versione .NET Standard non viene mai usata in fase di esecuzione.

La maggior parte delle operazioni in questo passaggio si focalizzerà sull'uso di P/Invoke per chiamare i metodi della libreria nativa e la gestione dei riferimenti agli oggetti sottostanti. L'obiettivo è esporre la funzionalità della libreria al consumer astraendone qualsiasi complessità. Gli sviluppatori Xamarin.Forms non devono avere conoscenze operative sui lavori interni della libreria non gestita. Dovrebbe sembrare che usi qualsiasi altra libreria gestita C# .

Infine, l'output di questa fase è costituito da un set di librerie .NET, uno per destinazione, insieme a un documento NuSpec che contiene le informazioni necessarie per compilare il pacchetto nel passaggio successivo.

**Fase 3: compressione e push di un pacchetto NuGet per il wrapper .NET**

La terza fase sta creando un pacchetto NuGet usando gli artefatti di compilazione del passaggio precedente. Il risultato di questo passaggio è un pacchetto NuGet che può essere utilizzato da un'app Xamarin. Nella procedura dettagliata viene usata una directory locale che funge da feed NuGet. In produzione, questo passaggio deve pubblicare un pacchetto in un feed NuGet pubblico o privato e deve essere completamente automatizzato.

**Fase 4: utilizzo del pacchetto NuGet da un'app Xamarin.Forms**

Il passaggio finale consiste nel fare riferimento e usare il pacchetto NuGet da un'app Xamarin.Forms. Questa operazione richiede la configurazione del feed NuGet in Visual Studio per usare il feed definito nel passaggio precedente.

Una volta configurato il feed, è necessario fare riferimento al pacchetto da ogni progetto nell'app Xamarin.Forms multipiattaforma. ' The Bait-and-switch Trick ' offre interfacce identiche, quindi la funzionalità della libreria nativa può essere chiamata usando il codice definito in un'unica posizione.

Il repository del codice sorgente contiene un [elenco di ulteriori letture](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up) che include articoli su come configurare un feed NuGet privato in Azure DevOps e su come eseguire il push del pacchetto nel feed. Quando si richiede un tempo di installazione inferiore rispetto a una directory locale, questo tipo di feed è migliore in un ambiente di sviluppo team.

## <a name="walk-through"></a>Procedura dettagliata

I passaggi forniti sono specifici per **Visual Studio per Mac**, ma la struttura funziona anche in **Visual Studio 2017** .

### <a name="prerequisites"></a>Prerequisites

Per seguire la procedura, lo sviluppatore dovrà:

- [Riga di comando di NuGet (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)

- [*Visual Studio* *per Mac*](https://visualstudio.microsoft.com/downloads)

> [!NOTE]
> Per distribuire le app in un iPhone, è necessario un [**account Apple Developer**](https://developer.apple.com/) attivo.

## <a name="creating-the-native-libraries-stage-1"></a>Creazione di librerie native (fase 1)

La funzionalità della libreria nativa è basata sull'esempio di [procedura dettagliata: creazione e utilizzo di una libreriaC++statica ()](https://docs.microsoft.com/cpp/windows/walkthrough-creating-and-using-a-static-library-cpp?view=vs-2017).

Questa procedura dettagliata ignora la prima fase, compilando le librerie native, perché la libreria viene fornita come dipendenza di terze parti in questo scenario. Le librerie native precompilate sono incluse insieme al [codice di esempio](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin) oppure possono essere [scaricate](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts) direttamente.

### <a name="working-with-the-native-library"></a>Uso della libreria nativa

L'esempio *MathFuncsLib* originale include una singola classe denominata `MyMathFuncs` con la definizione seguente:

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

Una classe aggiuntiva definisce funzioni wrapper che consentono a un consumer .NET di creare, eliminare e interagire con la classe nativa `MyMathFuncs` sottostante.

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

Si tratta di funzioni wrapper utilizzate sul lato [Xamarin](https://visualstudio.microsoft.com/xamarin/) .

## <a name="wrapping-the-native-library-stage-2"></a>Wrapping della libreria nativa (fase 2)

Questa fase richiede le [librerie precompilate](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin/Sample/Artefacts) descritte nella [sezione precedente](#creating-the-native-libraries-stage-1).

### <a name="creating-the-visual-studio-solution"></a>Creazione della soluzione Visual Studio

1. In **Visual Studio per Mac**fare clic su **nuovo progetto** (nella *pagina iniziale*) o su **nuova soluzione** (dal menu *file* ).
2. Dalla finestra **nuovo progetto** scegliere **progetto condiviso** (dall'interno di *Libreria > multipiattaforma*) e quindi fare clic su **Avanti**.
3. Aggiornare i campi seguenti, quindi fare clic su **Crea**:

    - **Nome progetto:** MathFuncs. Shared  
    - **Nome soluzione:** MathFuncs  
    - **Percorso:** Usa il percorso di salvataggio predefinito (oppure scegli un'alternativa)   
    - **Creare un progetto all'interno della directory della soluzione:** Imposta questa opzione su Checked
4. In **Esplora soluzioni**fare doppio clic sul progetto **MathFuncs. Shared** e passare a **impostazioni principali**.
5. Rimuovere **. Condiviso** dallo **spazio dei nomi predefinito** in modo che sia impostato solo su **MathFuncs** , quindi fare clic su **OK**.
6. Aprire **MyClass.cs** (creato dal modello), quindi rinominare sia la classe che il nome del file in **MyMathFuncsWrapper** e impostare lo spazio dei nomi su **MathFuncs**.
7. **Controllare e fare clic** sulla soluzione **MathFuncs**, quindi scegliere **Aggiungi nuovo progetto** dal menu **Aggiungi** .
8. Dalla finestra **nuovo progetto** scegliere **libreria .NET standard** (dall'interno di *Libreria > multipiattaforma*) e quindi fare clic su **Avanti**.
9. Scegliere **.NET Standard 2,0** , quindi fare clic su **Avanti**.
10. Aggiornare i campi seguenti, quindi fare clic su **Crea**:

    - **Nome progetto:** MathFuncs. standard  
    - **Percorso:** USA lo stesso percorso di salvataggio del progetto condiviso   

11. In **Esplora soluzioni**fare doppio clic sul progetto **MathFuncs. standard** .
12. Passare a **impostazioni principali**, quindi aggiornare **lo spazio dei nomi predefinito** a **MathFuncs**.
13. Passare alle impostazioni di **output** , quindi aggiornare il **nome dell'assembly** a **MathFuncs**.
14. Passare alle impostazioni del **compilatore** , modificare la **configurazione** in **Release**, impostare le **informazioni di debug sui** **simboli solo** e quindi fare clic su **OK**.
15. Elimina **Class1.cs/getting avviata** dal progetto (se una di queste è stata inclusa come parte del modello).
16. **Controllare e fare clic** sulla cartella **dipendenze/riferimenti** del progetto, quindi scegliere **modifica riferimenti**.
17. Selezionare **MathFuncs. Shared** dalla scheda **progetti** , quindi fare clic su **OK**.
18. Ripetere i passaggi 7-17 (ignorando il passaggio 9) utilizzando le configurazioni seguenti:

    | **NOME PROGETTO**  | **NOME MODELLO**   | **MENU NUOVO PROGETTO**   |
    |-------------------| --------------------| -----------------------|
    | MathFuncs. Android | Libreria di classi       | Libreria di > Android      |
    | MathFuncs. iOS     | Libreria di associazione     | Libreria > iOS          |

19. Da **Esplora soluzioni**fare doppio clic sul progetto **MathFuncs. Android** , quindi passare alle impostazioni del **compilatore** .

20. Con la **configurazione** impostata su **debug**, modificare **definire i simboli** per includere **Android;** .

21. Modificare la **configurazione** in **Release**, quindi modificare **define symbols** per includere anche **Android;** .

22. Ripetere i passaggi 19-20 per **MathFuncs. iOS**, modificando la **definizione dei simboli** per includere **iOS** , anziché **Android;** in entrambi i casi.

23. Compilare la soluzione in configurazione **versione** (**CTRL + comando + B**) e verificare che tutti e tre gli assembly di output (Android, iOS, .NET standard) (nelle rispettive cartelle bin del progetto) condividano lo stesso nome **MathFuncs. dll**.

In questa fase, la soluzione deve avere tre destinazioni, una a parte per Android, iOS e .NET Standard e un progetto condiviso a cui fa riferimento ognuna delle tre destinazioni. Questi devono essere configurati in modo da usare lo stesso spazio dei nomi e assembly di output predefiniti con lo stesso nome. Questa operazione è necessaria per l'approccio "Bait and switch" descritto in precedenza.

### <a name="adding-the-native-libraries"></a>Aggiunta di librerie native

Il processo di aggiunta delle librerie native alla soluzione wrapper varia leggermente tra Android e iOS.

#### <a name="native-references-for-mathfuncsandroid"></a>Riferimenti nativi per MathFuncs. Android

1. **Controllare e fare clic** sul progetto **MathFuncs. Android** , quindi scegliere **nuova cartella** dal menu **Aggiungi** in cui è denominato **libs**.

2. Per ogni interfaccia **Abi** (Application Binary Interface), **controllare e fare clic** sulla cartella **libs** , quindi scegliere **nuova cartella** dal menu **Aggiungi** , denominarla dopo la rispettiva **Abi**. In questo caso:

    - arm64-v8a
    - armeabi-v7a
    - x86
    - x86_64  

    > [!NOTE]
    > Per una panoramica più dettagliata, vedere l'argomento [architetture e CPU](https://developer.android.com/ndk/guides/arch) dalla guida per gli [sviluppatori di NDK](https://developer.android.com/ndk/guides/), in particolare la sezione relativa all'indirizzamento del [codice nativo nei pacchetti dell'applicazione](https://developer.android.com/ndk/guides/abis#native-code-in-app-packages).

3. Verificare la struttura di cartelle:  

    ```folders
    - lib
        - arm64-v8a
        - armeabi-v7a
        - x86
        - x86_64
    ```

4. Aggiungere le librerie **. so** corrispondenti a ogni cartella **Abi** in base al mapping seguente:

    **arm64-V8A:** libs/Android/arm64

    **ARMEABI-v7a:** libs/Android/ARM  

    **x86:** libs/Android/x86

    **x86_64:** libs/Android/x86_64

    > [!NOTE]
    > Per aggiungere file, **controllare e fare clic** sulla cartella che rappresenta il rispettivo **Abi**, quindi scegliere **Aggiungi file** dal menu **Aggiungi** . Scegliere la libreria appropriata (dalla directory **PrecompiledLibs** ), quindi fare clic su **Apri** e quindi su **OK** lasciando l'opzione predefinita per *copiare il file nella directory*.

5. Per ognuno dei file **. so** , **controllare** e scegliere l'opzione **EmbeddedNativeLibrary** dal menu **azione di compilazione** .

A questo punto la cartella **libs** dovrebbe apparire come segue:

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

#### <a name="native-references-for-mathfuncsios"></a>Riferimenti nativi per MathFuncs. iOS

1. **Controllare e fare clic** sul progetto **MathFuncs. iOS** , quindi scegliere **Aggiungi riferimento nativo** dal menu **Aggiungi** . 
2. Scegliere la libreria **libMathFuncs. a** (da libs/iOS sotto la directory **PrecompiledLibs** ), quindi fare clic su **Apri** 
3. **CTRL + clic** sul file **libMathFuncs** (nella cartella **riferimenti nativi** , quindi scegliere l'opzione **Proprietà** dal menu  
4. Configurare le proprietà dei **riferimenti nativi** in modo che siano controllate (mostrando un'icona di segno di spunta) nel riquadro delle **Proprietà** :

    - Forza caricamento
    - ÈC++
    - Collegamento intelligente

    > [!NOTE]
    > L'uso di un tipo di progetto di libreria di associazione insieme a un [riferimento nativo](https://docs.microsoft.com/xamarin/cross-platform/macios/native-references) incorpora la libreria statica e la consente di collegarsi automaticamente all'app Xamarin.iOS che vi fa riferimento (anche quando è inclusa tramite un pacchetto NuGet).

5. Aprire **ApiDefinition.cs**, eliminando il codice commentato basato su modelli (lasciando solo lo spazio dei nomi `MathFuncs`), quindi eseguire lo stesso passaggio per **structs.cs** 

    > [!NOTE]
    > Per la compilazione di un progetto di libreria di associazione è necessario che questi file (con le azioni di compilazione *ObjCBindingApiDefinition* e *ObjCBindingCoreSource* ) siano disponibili. Tuttavia, si scriverà il codice per chiamare la libreria nativa, al di fuori di questi file in modo che possa essere condivisa tra le destinazioni della libreria Android e iOS usando P/Invoke standard.

### <a name="writing-the-managed-library-code"></a>Scrittura del codice di libreria gestita

A questo punto, C# scrivere il codice per chiamare la libreria nativa. L'obiettivo è nascondere qualsiasi complessità sottostante. Il consumer non deve necessariamente avere una conoscenza del funzionamento degli elementi interni della libreria nativa o dei concetti di P/Invoke.  

#### <a name="creating-a-safehandle"></a>Creazione di un SafeHandle

1. **Controllare e fare clic** sul progetto **MathFuncs. Shared** , quindi scegliere **Aggiungi file** dal menu **Aggiungi** . 
2. Scegliere **classe vuota** dalla finestra **nuovo file** , denominarla **MyMathFuncsSafeHandle** e quindi fare clic su **nuovo** .
3. Implementare la classe **MyMathFuncsSafeHandle** :

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
    > Un [SafeHandle](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.safehandle?view=netframework-4.7.2) è il modo migliore per lavorare con le risorse non gestite nel codice gestito. In questo modo si estrae una grande quantità di codice standard correlato alla finalizzazione critica e al ciclo di vita degli oggetti. Il proprietario di questo handle può quindi gestirlo come qualsiasi altra risorsa gestita e non dovrà implementare il [modello Disposable](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose)completo. 

#### <a name="creating-the-internal-wrapper-class"></a>Creazione della classe wrapper interna

1. Aprire **MyMathFuncsWrapper.cs**e modificarlo in una classe statica interna

    ```csharp
    namespace MathFuncs
    {
        internal static class MyMathFuncsWrapper
        {
        }
    }
    ```

2. Nello stesso file aggiungere l'istruzione condizionale seguente alla classe:

    ```csharp
    #if Android
        const string DllName = "libMathFuncs.so";
    #else
        const string DllName = "__Internal";
    #endif
    ```

    > [!NOTE]
    > Viene impostato il valore costante **dllname** a seconda che la libreria venga compilata per **Android** o **iOS**. Questo consente di risolvere le diverse convenzioni di denominazione usate da ogni piattaforma, ma anche il tipo di libreria usato in questo caso. Android usa una libreria dinamica e quindi prevede un nome file, inclusa l'estensione. Per iOS,' *__Internal*' è necessario perché si usa una libreria statica.

3. Aggiungere un riferimento a **System. Runtime. InteropServices** nella parte superiore del file **MyMathFuncsWrapper.cs**

    ```csharp
    using System.Runtime.InteropServices;
    ```

4. Aggiungere i metodi wrapper per gestire la creazione e l'eliminazione della classe **MyMathFuncs** :

    ```csharp
    [DllImport(DllName, EntryPoint = "CreateMyMathFuncsClass")]
    internal static extern MyMathFuncsSafeHandle CreateMyMathFuncs();

    [DllImport(DllName, EntryPoint = "DisposeMyMathFuncsClass")]
    internal static extern void DisposeMyMathFuncs(MyMathFuncsSafeHandle ptr);
    ```

    > [!NOTE]
    > Viene passato il **dllname** costante all'attributo **dllimport** insieme al **EntryPoint** , che indica in modo esplicito al runtime .NET il nome della funzione da chiamare all'interno della libreria. Tecnicamente, non è necessario specificare il valore **EntryPoint** se i nomi dei metodi gestiti erano identici a quelli non gestiti. Se non ne viene specificato uno, verrà usato il nome del metodo gestito come **EntryPoint** . Tuttavia, è preferibile essere esplicito.  

5. Aggiungere i metodi wrapper per consentire l'uso della classe **MyMathFuncs** usando il codice seguente:

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
    > In questo esempio vengono utilizzati tipi semplici per i parametri. Poiché il marshalling è una copia bit per bit, in questo caso non richiede alcun lavoro aggiuntivo da parte dell'it. Si noti anche l'uso della classe **MyMathFuncsSafeHandle** anziché di **IntPtr**standard. Il **IntPtr** viene automaticamente mappato a **SafeHandle** come parte del processo di marshalling.

6. Verificare che la classe **MyMathFuncsWrapper** completata venga visualizzata come indicato di seguito:

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

1. Aprire la classe **MyMathFuncsSafeHandle** , passare al commento **todo** segnaposto all'interno del metodo **ReleaseHandle** :

    ```csharp
    // TODO: Release the handle here
    ```

1. Sostituire la riga **todo** :

    ```csharp
    MyMathFuncsWrapper.DisposeMyMathFuncs(handle);
    ```

#### <a name="writing-the-mymathfuncs-class"></a>Scrittura della classe MyMathFuncs

Ora che il wrapper è stato completato, creare una classe MyMathFuncs che gestirà il riferimento all'oggetto C++ MyMathFuncs non gestito.  

1. **Controllare e fare clic** sul progetto **MathFuncs. Shared** , quindi scegliere **Aggiungi file** dal menu **Aggiungi** . 
2. Scegliere **classe vuota** dalla finestra **nuovo file** , denominarla **MyMathFuncs** e quindi fare clic su **nuovo** .
3. Aggiungere i membri seguenti alla classe **MyMathFuncs** :

    ```csharp
    readonly MyMathFuncsSafeHandle handle;
    ```

4. Implementare il costruttore per la classe in modo da creare e archiviare un handle per l'oggetto **MyMathFuncs** nativo quando viene creata un'istanza della classe:

    ```csharp
    public MyMathFuncs()
    {
        handle = MyMathFuncsWrapper.CreateMyMathFuncs();
    }
    ```

5. Implementare l'interfaccia **IDisposable** usando il codice seguente:

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

6. Implementare i metodi **MyMathFuncs** usando la classe **MyMathFuncsWrapper** per eseguire il lavoro reale sotto la cappa passando il puntatore archiviato all'oggetto non gestito sottostante. Il codice deve essere il seguente:

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

#### <a name="creating-the-nuspec"></a>Creazione di NuSpec

Per fare in modo che la libreria venga impacchettata e distribuita tramite NuGet, la soluzione richiede un file **NuSpec** . In questo modo verrà identificato quale degli assembly risultanti verrà incluso per ogni piattaforma supportata.

1. **Controllare e fare clic** sulla soluzione **MathFuncs**, quindi scegliere **Aggiungi cartella soluzione** dal menu **Aggiungi** per denominarlo **SolutionItems**.
2. **Controllare e fare clic** sulla cartella **SolutionItems** , quindi scegliere **nuovo file** dal menu **Aggiungi** .
3. Scegliere **file XML vuoto** dalla finestra **nuovo file** , denominarlo **MathFuncs. NuSpec** e quindi fare clic su **nuovo**.
4. Aggiornare **MathFuncs. NuSpec** con i metadati del pacchetto di base da visualizzare al consumer **NuGet** . Esempio:

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
    > Per altri dettagli sullo schema usato per questo manifesto, vedere il documento di [riferimento di NuSpec](https://docs.microsoft.com/nuget/reference/nuspec) .

5. Aggiungere un elemento `<files>` come figlio dell'elemento `<package>` (appena sotto `<metadata>`), identificando ogni file con un elemento `<file>` separato:

    ```xml
    <files>

        <!-- Android -->

        <!-- iOS -->

        <!-- netstandard2.0 -->

    </files>
    ```

    > [!NOTE]
    > Quando un pacchetto viene installato in un progetto e dove sono presenti più assembly specificati con lo stesso nome, NuGet sceglie in modo efficace l'assembly più specifico per la piattaforma specificata.

6. Aggiungere gli elementi `<file>` per gli assembly **Android** :

    ```xml
    <file src="MathFuncs.Android/bin/Release/MathFuncs.dll" target="lib/MonoAndroid81/MathFuncs.dll" />
    <file src="MathFuncs.Android/bin/Release/MathFuncs.pdb" target="lib/MonoAndroid81/MathFuncs.pdb" />
    ```

7. Aggiungere gli elementi `<file>` per gli assembly **iOS** :

    ```xml
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.dll" target="lib/Xamarin.iOS10/MathFuncs.dll" />
    <file src="MathFuncs.iOS/bin/Release/MathFuncs.pdb" target="lib/Xamarin.iOS10/MathFuncs.pdb" />
    ```

8. Aggiungere gli elementi `<file>` per gli assembly **netstandard 2.0** :

    ```xml
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.dll" target="lib/netstandard2.0/MathFuncs.dll" />
    <file src="MathFuncs.Standard/bin/Release/netstandard2.0/MathFuncs.pdb" target="lib/netstandard2.0/MathFuncs.pdb" />
    ```

9. Verificare il manifesto **NuSpec** :

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
    > Questo file specifica i percorsi di output dell'assembly da una build di **rilascio** , quindi assicurarsi di compilare la soluzione usando tale configurazione.

A questo punto, la soluzione contiene 3 assembly .NET e un manifesto **NuSpec** di supporto.

## <a name="distributing-the-net-wrapper-with-nuget"></a>Distribuzione del wrapper .NET con NuGet

Il passaggio successivo consiste nel creare il pacchetto e distribuire il pacchetto NuGet in modo che possa essere facilmente utilizzato dall'app e gestito come dipendenza. Il wrapping e l'utilizzo possono essere eseguiti in un'unica soluzione, ma la distribuzione della libreria tramite NuGet facilita la separazione e consente di gestire queste codebase in modo indipendente.

### <a name="preparing-a-local-packages-directory"></a>Preparazione di una directory dei pacchetti locale

La forma più semplice del feed NuGet è una directory locale:

1. In **Finder**passare a una directory comoda. Ad esempio, **/Users**.
2. Scegliere **nuova cartella** dal menu **file** , specificando un nome significativo, ad esempio **local-NuGet-feed**.

### <a name="creating-the-package"></a>Creazione del pacchetto

1. Impostare la **configurazione della build** su **Release**ed eseguire una compilazione usando il **comando + B**.
2. Aprire il **terminale** e spostarsi nella cartella contenente il file **NuSpec** .
3. Nel **terminale**eseguire il comando di **NuGet Pack** specificando il file **NuSpec** , la **versione** (ad esempio 1.0.0) e il **OutputDirectory** usando la cartella creata nel [passaggio precedente](https://docs.microsoft.com/xamarin/cross-platform/cpp/index#creating-a-local-nuget-feed), ovvero **local-NuGet-feed**. Esempio:

    ```bash
    nuget pack MathFuncs.nuspec -Version 1.0.0 -OutputDirectory ~/local-nuget-feed
    ```

4. **Verificare** che **MathFuncs. 1.0.0. nupkg** sia stato creato nella directory **local-NuGet-feed** .

### <a name="optional-using-a-private-nuget-feed-with-azure-devops"></a>OPZIONALE Uso di un feed NuGet privato con Azure DevOps

Una tecnica più affidabile è descritta in [Introduzione ai pacchetti NuGet in Azure DevOps](https://docs.microsoft.com/azure/devops/artifacts/get-started-nuget?view=vsts&tabs=new-nav#publish-a-package), che illustra come creare un feed privato ed effettuare il push del pacchetto (generato nel passaggio precedente) a tale feed.

È preferibile che questo flusso di lavoro sia completamente automatizzato, ad esempio usando [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts). Per ulteriori informazioni, vedere [Introduzione a Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=vsts).

## <a name="consuming-the-net-wrapper-from-a-xamarinforms-app"></a>Utilizzo del wrapper .NET da un'app Xamarin.Forms

Per completare la procedura dettagliata, creare un'app **Xamarin.Forms** per utilizzare il pacchetto appena pubblicato nel feed **NuGet** locale.

### <a name="creating-the-xamarinforms-project"></a>Creazione del progetto **Xamarin.Forms**

1. Aprire una nuova istanza di **Visual Studio per Mac**. Questa operazione può essere eseguita dal **terminale**:

    ```bash
    open -n -a "Visual Studio"
    ```

2. In **Visual Studio per Mac**fare clic su **nuovo progetto** (nella *pagina iniziale*) o su **nuova soluzione** (dal menu *file* ).
3. Dalla finestra **nuovo progetto** scegliere **app moduli vuota** (dall'interno dell' *app multipiattaforma >* ), quindi fare clic su **Avanti**.
4. Aggiornare i campi seguenti, quindi fare clic su **Avanti**:

    - **Nome dell'app:** MathFuncsApp.
    - **Identificatore dell'organizzazione:** Usare uno spazio dei nomi inverso, ad esempio _com. {Your_Org}_ .
    - **Piattaforme di destinazione:** Usare il valore predefinito (destinazioni Android e iOS).
    - **Codice condiviso:** Impostare questa impostazione su .NET Standard (è possibile una soluzione "Shared Library", ma esula dall'ambito di questa procedura dettagliata).

5. Aggiornare i campi seguenti, quindi fare clic su **Crea**:

    - **Nome progetto:** MathFuncsApp.
    - **Nome soluzione:** MathFuncsApp.  
    - **Percorso:** Usare il percorso di salvataggio predefinito oppure scegliere un'alternativa.

6. In **Esplora soluzioni**, **controllare e fare clic** sulla destinazione (**MathFuncsApp. Android** o **MathFuncs. iOS**) per il test iniziale, quindi scegliere **Imposta come progetto di avvio**.
7. Scegliere il **dispositivo** preferito o il **simulatore**/**emulatore**. 
8. Eseguire la soluzione (**comando + return**) per verificare che il progetto **Xamarin.Forms** basato su modelli venga compilato ed eseguito in modo corretto. 

    > [!NOTE]
    > **iOS** (in particolare il simulatore) tende a disporre del tempo di compilazione/distribuzione più rapido.

### <a name="adding-the-local-nuget-feed-to-the-nuget-configuration"></a>Aggiunta del feed NuGet locale alla configurazione NuGet

1. In **Visual Studio**scegliere **Preferenze** dal menu di **Visual Studio** .
2. Scegliere **origini** dalla sezione **NuGet** , quindi fare clic su **Aggiungi**.
3. Aggiornare i campi seguenti, quindi fare clic su **Aggiungi origine**:

    - **Nome:** Fornire un nome significativo, ad esempio, local-Packages.  
    - **Percorso:** Specificare la cartella **local-NuGet-feed** creata nel [passaggio precedente](#preparing-a-local-packages-directory).

    > [!NOTE]
    > In questo caso non è necessario specificare un **nome utente** e una **password**. 

4. Fare clic su **OK**.

### <a name="referencing-the-package"></a>Riferimento al pacchetto

Ripetere i passaggi seguenti per ogni progetto (**MathFuncsApp**, **MathFuncsApp. Android**e **MathFuncsApp. iOS**).

1. **Controllare e fare clic** sul progetto, quindi scegliere **Aggiungi pacchetti NuGet** dal menu **Aggiungi** .
2. Cercare **MathFuncs**. 
3. Verificare che la **versione** del pacchetto sia **1.0.0** e che gli altri dettagli siano visualizzati come previsto, ad esempio il **titolo** e la **Descrizione**, ovvero *MathFuncs* e la *libreria wrapper di C++ esempio*. 
4. Selezionare il pacchetto **MathFuncs** , quindi fare clic su **Aggiungi pacchetto**.

### <a name="using-the-library-functions"></a>Uso delle funzioni della libreria

Ora, con un riferimento al pacchetto **MathFuncs** in ogni progetto, le funzioni sono disponibili per il C# codice.

1. Aprire **MainPage.XAML.cs** dall'interno del progetto **Xamarin.Forms**comune **MathFuncsApp** (a cui fa riferimento sia **MathFuncsApp. Android** che **MathFuncsApp. iOS**).
2. Aggiungere istruzioni **using** per **System. Diagnostics** e **MathFuncs** all'inizio del file:

    ```csharp
    using System.Diagnostics;
    using MathFuncs;
    ```

3. Dichiarare un'istanza della classe `MyMathFuncs` all'inizio della classe `MainPage`:

    ```csharp
    MyMathFuncs myMathFuncs;
    ```

4. Eseguire l'override dei metodi `OnAppearing` e `OnDisappearing` dalla classe di base `ContentPage`:

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

5. Aggiornare il metodo `OnAppearing` per inizializzare la variabile `myMathFuncs` dichiarata in precedenza:

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();
        myMathFuncs = new MyMathFuncs();
    }
    ```

6. Aggiornare il metodo `OnDisappearing` per chiamare il metodo `Dispose` su `myMathFuncs`:

    ```csharp
    protected override void OnDisappearing()
    {
        base.OnAppearing();
        myMathFuncs.Dispose();
    }
    ```

7. Implementare un metodo privato denominato **TestMathFuncs** come segue:

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

8. Infine, chiamare `TestMathFuncs` alla fine del metodo `OnAppearing`:

    ```csharp
    TestMathFuncs();
    ```

9. Eseguire l'app in ogni piattaforma di destinazione e verificare che l'output nel riquadro di **output dell'applicazione** venga visualizzato come segue:

    ```csharp
    1 + 2 = 3
    1 - 2 = -1
    1 * 2 = 2
    1 / 2 = 0.5
    ```

    > [!NOTE]
    > Se si verifica un "*DLLNotFoundException*" durante il test in Android o un errore di compilazione in iOS, assicurarsi di controllare che l'architettura della CPU del dispositivo/emulatore/simulatore usato sia compatibile con il subset che è stato scelto di supportare. 

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare un'app Xamarin.Forms che usa librerie native tramite un wrapper .NET comune distribuito tramite un pacchetto NuGet. L'esempio fornito in questa procedura dettagliata è intenzionalmente molto semplicistico per illustrare più facilmente l'approccio. Un'applicazione reale dovrà gestire complessità quali la gestione delle eccezioni, i callback, il marshalling di tipi più complessi e il collegamento ad altre librerie di dipendenze. Una considerazione fondamentale è il processo mediante il quale l'evoluzione del C++ codice è coordinata e sincronizzata con il wrapper e le applicazioni client. Questo processo può variare a seconda che uno o entrambi i problemi siano responsabili di un singolo team. In entrambi i casi, l'automazione è un vero vantaggio. Di seguito sono riportate alcune risorse che forniscono ulteriori informazioni su alcuni concetti chiave insieme ai relativi download. 

### <a name="downloads"></a>Download

- [Strumenti della riga di comando di NuGet (CLI)](https://docs.microsoft.com/nuget/tools/nuget-exe-cli-reference#macoslinux)
- [Visual Studio](https://visualstudio.microsoft.com/vs)

### <a name="examples"></a>Esempi

- [Sviluppo di app per dispositivi mobili multipiattaforma ipercadenti conC++](https://blogs.msdn.microsoft.com/vcblog/2015/06/26/hyperlapse-cross-platform-mobile-development-with-visual-c-and-xamarin/)
- [Microsoft Pix (C++ e Xamarin)](https://devblogs.microsoft.com/xamarin/microsoft-research-ships-intelligent-apps-with-the-power-of-c-and-ai/)
- [Porta di esempio di mono San Angeles](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk/)

### <a name="further-reading"></a>Ulteriori informazioni

[Articoli relativi al contenuto del post](https://github.com/xamcat/mobcat-samples/tree/master/cpp_with_xamarin#wrapping-up)