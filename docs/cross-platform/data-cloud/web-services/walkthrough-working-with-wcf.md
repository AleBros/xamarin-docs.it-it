---
title: 'Procedura dettagliata: utilizzo di WCF'
description: Questa procedura dettagliata illustra come un'applicazione per dispositivi mobili compilata con Xamarin può utilizzare un servizio web WCF utilizzando la classe BasicHttpBinding.
ms.prod: xamarin
ms.assetid: D0E83342-2E79-4D25-BD57-43718F5628C4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/17/2018
ms.openlocfilehash: 7f6885415e1b5e0c988d13fe331703213b9b8fb7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---working-with-wcf"></a>Procedura dettagliata: utilizzo di WCF

_Questa procedura dettagliata illustra come un'applicazione per dispositivi mobili compilata con Xamarin può utilizzare un servizio web WCF utilizzando la classe BasicHttpBinding._


È un requisito comune per le applicazioni mobili in grado di comunicare con i sistemi back-end. Esistono molti e opzioni per i framework di back-end, tra cui [Windows Communication Foundation](http://msdn.microsoft.com/en-us/library/ms731082.aspx) (WCF). Questa procedura dettagliata verrà fornito un esempio di come un'applicazione per dispositivi mobili con Xamarin può utilizzare un servizio WCF utilizzando la `BasicHttpBinding` classe. La procedura dettagliata include i seguenti argomenti:

1.  **Creare un servizio WCF** -In questa sezione si creerà un servizio WCF di base con due metodi. Il primo metodo richiederà un parametro di stringa, mentre l'altro metodo un oggetto in c#. In questa sezione verrà anche descritto come configurare la workstation dello sviluppatore per consentire l'accesso remoto per il servizio WCF.
1.  **Creare un'applicazione di xamarin** -dopo aver creato il servizio WCF, si creerà una semplice applicazione di xamarin che verrà utilizzato il servizio WCF. In questa sezione illustra le procedure creare una classe proxy del servizio WCF per facilitare la comunicazione con il servizio WCF.
1.  **Creare un'applicazione di xamarin** -la parte finale di questa esercitazione prevede la creazione di una semplice applicazione di xamarin che verrà utilizzato il servizio WCF.

<a name="Requirements" />

## <a name="requirements"></a>Requisiti

Questa procedura dettagliata si presuppone che si abbia familiarità con la creazione e utilizzo dei servizi WCF.

<a name="Creating_a_WCF_Service" />

## <a name="creating-a-wcf-service"></a>Creazione di un servizio WCF

La prima attività davanti a noi consiste nel creare un servizio WCF per comunicare con un applicazioni per dispositivi mobili.

1. Avviare Visual Studio 2017 e creare un nuovo progetto.
1. Nel **nuovo progetto** finestra di dialogo Seleziona il **WCF > libreria di servizi WCF** , modello e il nome della soluzione `HelloWorldService`:

    ![](walkthrough-working-with-wcf-images/new-wcf-service.png "Creare una nuova libreria di servizi WCF")

1. In **Esplora**, aggiungere una nuova classe denominata `HelloWorldData` al progetto:

    ```csharp
        using System.Runtime.Serialization;

        namespace HelloWorldService
        {
            [DataContract]
            public class HelloWorldData
            {
                [DataMember]
                public bool SayHello { get; set; }

                [DataMember]
                public string Name { get; set; }

                public HelloWorldData()
                {
                    Name = "Hello ";
                    SayHello = false;
                }
            }
        }
    ```


1. In **Esplora**, rinominare `IService1.cs` a `IHelloWorldService.cs`e rinominare `Service1.cs` a `HelloWorldService.cs`.
1. In **Esplora**aprire `IHelloWorldService.cs` e sostituire il codice con il codice seguente:

    ```csharp
        using System.ServiceModel;

        namespace HelloWorldService
        {
            [ServiceContract]
            public interface IHelloWorldService
            {
                [OperationContract]
                string SayHelloTo(string name);

                [OperationContract]
                HelloWorldData GetHelloData(HelloWorldData helloWorldData);
            }
        }
    ```
  
    Questo servizio sono disponibili due metodi: uno che accetta una stringa per un parametro e un altro che accetta un oggetto .NET.

1. In **Esplora**aprire `HelloWorldService.cs` e sostituire il codice con il codice seguente:

    ```csharp
        using System;

        namespace HelloWorldService
        {
            public class HelloWorldService : IHelloWorldService
            {
                public HelloWorldData GetHelloData(HelloWorldData helloWorldData)
                {
                    if (helloWorldData == null)
                        throw new ArgumentException("helloWorldData");

                    if (helloWorldData.SayHello)
                        helloWorldData.Name = "Hello World to {helloWorldData.Name}";

                    return helloWorldData;
                }

                public string SayHelloTo(string name)
                {
                    return "Hello World to you, {name}";
                }
            }
        }
    ```

1. In **Esplora soluzioni**, aprire `App.config`, aggiornare il `name` attributo del `<service>` nodo, il `contract` attributo del `<endpoint>` nodo e `baseAddress` attributo del `<add>` nodo:

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            ...
            <services>
              <service name="HelloWorldService.HelloWorldService">
                <endpoint address="" binding="basicHttpBinding" contract="HelloWorldService.IHelloWorldService">
                  <identity>
                    <dns value="localhost" />
                  </identity>
                </endpoint>
                <endpoint address="mex" binding="mexHttpBinding" contract="IMetadataExchange" />
                <host>
                  <baseAddresses>
                    <add baseAddress="http://localhost:8733/Design_Time_Addresses/HelloWorldService/" />
                  </baseAddresses>
                </host>
              </service>
            </services>
            ...
        </configuration>
    ```

1. Compilare ed eseguire il servizio WCF. Il servizio sarà ospitato da client di prova WCF:

    ![](walkthrough-working-with-wcf-images/hosted-wcf-service.png "Servizio WCF in esecuzione nel client di prova")

1. Con il client di prova WCF in esecuzione, avviare un browser e passare all'endpoint del servizio WCF:

    ![](walkthrough-working-with-wcf-images/wcf-service-browser.png "Pagina informazioni browser del servizio WCF")

> [!IMPORTANT]
> Nella sezione seguente è necessaria solo se è necessario accettare le connessioni remote in una workstation Windows 10. La sezione può essere ignorata se si dispone di una piattaforma alternativa in cui distribuire il servizio WCF.

<a name="Allow_Remote_Access_to_IIS_Express" />

## <a name="configuring-remote-access-to-iis-express"></a>Configurazione accesso remoto a IIS Express

Hosting di WCF in locale è adeguato quando le connessioni provengono solo dal computer locale. Dispositivi remoti (ad esempio un dispositivo Android o un iPhone), tuttavia, non avrà accesso a un servizio WCF locale. Pertanto, in questa sezione viene descritto come configurare Windows 10 e IIS Express per accettare le connessioni remote:

1.  **Configurare IIS Express per le connessioni Remote accettare** -questo passaggio prevede la modifica del file di configurazione per IIS Express accettare le connessioni remote su una porta specifica e quindi impostando una regola per IIS Express accettare il traffico in ingresso.
1.  **Aggiungere un'eccezione in Windows Firewall** -è necessario aprire una porta attraverso Windows Firewall che applicazioni remote è possono utilizzare per comunicare con il servizio WCF.

    È necessario conoscere l'indirizzo IP della workstation. Ai fini di questo esempio si presuppone che la nostra workstation è l'indirizzo IP 192.168.1.143.

1. Esaminiamo la configurazione di IIS Express per l'ascolto delle richieste esterne. È possibile farlo modificando il file di configurazione per IIS Express `[solutiondirectory]\.vs\config\applicationhost.config`, come illustrato nella schermata seguente:

    [![](walkthrough-working-with-wcf-images/image05.png "È possibile farlo modificando il file di configurazione per IIS Express in solutiondirectory.vsconfigapplicationhost.config, come illustrato in questa schermata")](walkthrough-working-with-wcf-images/image05.png#lightbox)


    Individuare il `site` elemento con il nome `HelloWorldWcfHost`. Dovrebbe essere simile il frammento XML seguente:

    ```xml
        <site name="HelloWorldWcfHost" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="\\vmware-host\Shared Folders\tom\work\xamarin\code\private-samples\webservices\HelloWorld\HelloWorldWcfHost" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:8733:localhost" />
            </bindings>
        </site>
    ```
 
    È necessario aggiungere un altro `binding` per aprire la porta 8734 al traffico esterno. Aggiungere il seguente codice XML per il `bindings` elemento, sostituendo l'indirizzo IP con il proprio indirizzo IP:

    ```xml
    <binding protocol="http" bindingInformation="*:8734:192.168.1.143" />
    ```
    
    Consente di configurare IIS Express per il traffico HTTP da qualsiasi indirizzo IP remoto sulla porta 8734 l'indirizzo IP esterno del computer. Precedente frammento di codice si presuppone che l'indirizzo IP del computer che esegue IIS Express è 192.168.1.143. Dopo la modifica, il `bindings` elemento dovrebbe essere simile al seguente:

    ```xml
        <site name="HelloWorldWcfHost" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="\\vmware-host\Shared Folders\tom\work\xamarin\code\private-samples\webservices\HelloWorld\HelloWorldWcfHost" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:8733:localhost" />
                <binding protocol="http" bindingInformation="*:8734:192.168.1.143" />
            </bindings>
        </site>
    ```

1. Successivamente, è necessario configurare IIS Express accettare connessioni in ingresso sulla porta 8734. Avvio del backup di un prompt dei comandi amministrativo ed eseguire questo comando:

    `> netsh http add urlacl url=http://192.168.1.143:9608/ user=everyone`

1. Il passaggio finale consiste nel configurare Windows Firewall per consentire il traffico sulla porta 8734 esterno. Da un prompt dei comandi amministrativo, eseguire il comando seguente:

    `> netsh advfirewall firewall add rule name="IISExpressXamarin" dir=in protocol=tcp localport=8734 profile=private remoteip=localsubnet action=allow`

    Questo comando consente il traffico in ingresso sulla porta 8734 da tutti i dispositivi nella stessa subnet come workstation Windows 10.

È stato creato un servizio WCF di base ospitato in IIS Express che accetterà le connessioni in ingresso da altri dispositivi o computer la subnet. È possibile verificare quanto eseguendo l'applicazione e visitare `http://localhost:8733/Design_Time_Addresses/HelloWorldService/` nella workstation e `http://192.168.1.143:8734/Design_Time_Addresses/HelloWorldService/` da un altro computer della subnet.

Per consentire a IIS Express mantenere in esecuzione e serve il servizio, disattivare il **modifica e continuazione** opzione *le proprietà del progetto > Web > debugger*.

## <a name="creating-a-proxy-for-the-web-service"></a>Creazione di un Proxy per il servizio Web

Prima di un'applicazione può utilizzare il servizio, è necessario creare un proxy del servizio web per il servizio WCF. Per ottenere questo risultato, è possibile procedere come segue:

1. Aggiungere una libreria di classi Standard .NET denominato `HelloWorldServiceProxy`ed eliminare tutte le classi nel progetto.
1. Eseguire il `HelloWorldService` progetto.
1. Con il `HelloWorldService` del progetto in esecuzione, aggiungere un nuovo **servizio connesso** al progetto, tramite il **Provider riferimento del servizio Web WCF Microsoft**.
1. Nel **Endpoint del servizio** scheda della finestra il **Configura riferimento a servizio Web WCF** finestra di dialogo, fare clic sul **Discover** pulsante, eliminare `mex` dalla fine del rilevato endpoint nel **URI** elenco a discesa, immettere `HelloWorldServiceProxy` come il **Namespace**, fare clic su di **successivo** pulsante.
1. Nel **opzioni del tipo di dati** scheda della finestra il **Configura riferimento a servizio Web WCF** finestra di dialogo, accettare le impostazioni predefinite, fare clic sul **Avanti** pulsante.
1. Nel **opzioni Client** scheda della finestra il **Configura riferimento a servizio Web WCF** finestra di dialogo, verificare che il **pubblica** casella di controllo è selezionata e fare clic sul **fine**  pulsante.
1. Compilare il `HelloWorldServiceProxy` progetto.

> [!NOTE]
> Un'alternativa alla creazione del proxy utilizzando Provider riferimento del servizio Web di Microsoft WCF in Visual Studio 2017 consiste nell'utilizzare lo strumento ServiceModel Metadata Utility Tool (svcutil.exe). Per ulteriori informazioni, vedere [strumento ServiceModel Metadata Utility Tool (Svcutil.exe)](https://docs.microsoft.com/en-us/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Creating_a_Xamarin_Android_Application" />

## <a name="creating-a-xamarinandroid-application"></a>Creazione di un'applicazione di xamarin

Il proxy del servizio WCF può essere utilizzato da un'applicazione di xamarin, come indicato di seguito:

1. In Visual Studio, aggiungere un nuovo progetto Android vuoto alla soluzione e denominarlo `HelloWorld.Android`.
1. Nel `HelloWorld.Android` del progetto, aggiungere un riferimento al `HelloWorldServiceProxy` progetto e un riferimento di `System.ServiceModel` dello spazio dei nomi.
1. In **Esplora**aprire `Resources/layout/main.axml` e sostituire il codice XML esistente con il codice XML seguente:

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                  android:orientation="vertical"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent">
            <LinearLayout
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="0px"
                    android:layout_weight="1">
                <Button
                        android:id="@+id/sayHelloWorldButton"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:text="@string/say_hello_world" />
                <TextView
                        android:text="Large Text"
                        android:textAppearance="?android:attr/textAppearanceLarge"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:id="@+id/sayHelloWorldTextView" />
            </LinearLayout>
            <LinearLayout
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="0px"
                    android:layout_weight="1">
                <Button
                        android:id="@+id/getHelloWorldDataButton"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:text="@string/get_hello_world_data" />
                <TextView
                        android:text="Large Text"
                        android:textAppearance="?android:attr/textAppearanceLarge"
                        android:layout_width="fill_parent"
                        android:layout_height="wrap_content"
                        android:id="@+id/getHelloWorldDataTextView" />
            </LinearLayout>
        </LinearLayout>
    ```
    
    La schermata seguente mostra l'interfaccia utente nella finestra di progettazione:

    [![](walkthrough-working-with-wcf-images/image09.png "Si tratta di una schermata dell'aspetto questa interfaccia utente nella finestra di progettazione")](walkthrough-working-with-wcf-images/image09.png#lightbox)
    
1. In **Esplora**aprire `Resources/values/Strings.xml` e aggiungere il codice XML seguente:

    ```xml
    <string name="say_hello_world">Say Hello World</string>
    <string name="get_hello_world_data">Get Hello World data</string>
    ```
    
1. In **Esplora**aprire `MainActivity.cs` e sostituire il codice esistente con il codice seguente:

    ```csharp
        [Activity(Label = "HelloWorld.Android", MainLauncher = true)]
        public class MainActivity : Activity
        {
            static readonly EndpointAddress Endpoint = new EndpointAddress("<insert_WCF_service_endpoint_here>");

            HelloWorldServiceClient _client;
            Button _getHelloWorldDataButton;
            TextView _getHelloWorldDataTextView;
            Button _sayHelloWorldButton;
            TextView _sayHelloWorldTextView;
            ...
        }
    ```

    Sostituire `<insert_WCF_service_endpoint_here>` con l'indirizzo dell'endpoint WCF.

1. In `MainActivity.cs`, modificare il `OnCreate` metodo in modo che contenga il codice seguente:

    ```csharp
        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            InitializeHelloWorldServiceClient();

            // This button will invoke the GetHelloWorldData - the method that takes a C# object as a parameter.
            _getHelloWorldDataButton = FindViewById<Button>(Resource.Id.getHelloWorldDataButton);
            _getHelloWorldDataButton.Click += GetHelloWorldDataButtonOnClick;
            _getHelloWorldDataTextView = FindViewById<TextView>(Resource.Id.getHelloWorldDataTextView);

            // This button will invoke SayHelloWorld - this method takes a simple string as a parameter.
            _sayHelloWorldButton = FindViewById<Button>(Resource.Id.sayHelloWorldButton);
            _sayHelloWorldButton.Click += SayHelloWorldButtonOnClick;
            _sayHelloWorldTextView = FindViewById<TextView>(Resource.Id.sayHelloWorldTextView);
        }
    ```
    
    Il codice precedente consente di inizializzare le variabili di istanza per la classe e collega alcuni gestori di eventi.

1. In `MainActivity.cs`, creare un'istanza della classe proxy client tramite l'aggiunta di due metodi seguenti:

    ```csharp
        void InitializeHelloWorldServiceClient()
        {
            BasicHttpBinding binding = CreateBasicHttpBinding();
            _client = new HelloWorldServiceClient(binding, Endpoint);
        }

        static BasicHttpBinding CreateBasicHttpBinding()
        {
            BasicHttpBinding binding = new BasicHttpBinding
            {
                Name = "basicHttpBinding",
                MaxBufferSize = 2147483647,
                MaxReceivedMessageSize = 2147483647
            };

            TimeSpan timeout = new TimeSpan(0, 0, 30);
            binding.SendTimeout = timeout;
            binding.OpenTimeout = timeout;
            binding.ReceiveTimeout = timeout;
            return binding;
        }
    ```
    
    Il codice precedente crea e Inizializza un `HelloWorldServiceClient` oggetto.

1. In `MainActivity.cs`, aggiungere i gestori eventi per i due pulsanti di `Activity`:

    ```csharp
        async void GetHelloWorldDataButtonOnClick(object sender, EventArgs e)
        {
            var data = new HelloWorldData
            {
                Name = "Mr. Chad",
                SayHello = true
            };

            _getHelloWorldDataTextView.Text = "Waiting for WCF...";
            HelloWorldData result;
            try
            {
                result = await _client.GetHelloDataAsync(data);
                _getHelloWorldDataTextView.Text = result.Name;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }

        async void SayHelloWorldButtonOnClick(object sender, EventArgs e)
        {
            _sayHelloWorldTextView.Text = "Waiting for WCF...";
            try
            {
                var result = await _client.SayHelloToAsync("Kilroy");
                _sayHelloWorldTextView.Text = result;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
    ```
  
1. Eseguire l'applicazione, assicurarsi che il servizio WCF è in esecuzione e fare clic su due pulsanti. L'applicazione chiama WCF in modo asincrono, a condizione che il `Endpoint` campo sia impostato correttamente:

    [![](walkthrough-working-with-wcf-images/image08.png "Entro 30 secondi una risposta deve essere ricevuta da ogni metodo WCF e l'applicazione dovrebbe apparire simile a questa schermata")](walkthrough-working-with-wcf-images/image08.png#lightbox)

<a name="Creating_a_Xamarin_iOS_Application" />

## <a name="creating-a-xamarinios-application"></a>Creazione di un'applicazione di xamarin

Il proxy del servizio WCF può essere utilizzato da un'applicazione di xamarin. IOS, come indicato di seguito:

1. In Visual Studio, aggiungere un nuovo iPhone **singola visualizzazione applicazione** progetto alla soluzione e assegnargli il nome `HelloWorld.iOS`.
1. Nel `HelloWorld.iOS` del progetto, aggiungere un riferimento al `HelloWorldServiceProxy` progetto e un riferimento di `System.ServiceModel` dello spazio dei nomi.
1. In **Esplora**, fare doppio clic su `Main.storyboard` per aprire il file nella finestra di progettazione iOS. Aggiungere il seguente `UIButton` e `UITextView` controlli:

    ||nome|Titolo|
    |--- |--- |--- |
    |`UIButton`|`sayHelloWorldButton`|Ad esempio "Hello, World"|
    |`UITextView`|`sayHelloWorldText`||
    |`UIButton`|`getHelloWorldDataButton`|"Hello, World" di ottenere dati|
    |`UITextView`|`getHelloWorldDataText`||

    Dopo aver aggiunto i controlli, l'interfaccia utente dovrebbe essere simile a nella schermata seguente:

    [![](walkthrough-working-with-wcf-images/image12.png "Dopo aver aggiunto i controlli, l'interfaccia utente sarà simile a questa schermata")](walkthrough-working-with-wcf-images/image12.png#lightbox)

1. In **Esplora**aprire `ViewController.cs` e aggiungere il codice seguente:

    ```xml
        public partial class ViewController : UIViewController
        {
            static readonly EndpointAddress Endpoint = new EndpointAddress("<insert_WCF_service_endpoint_here>");
            HelloWorldServiceClient _client;
            ...
        }
    ```
  
    Sostituire `<insert_WCF_service_endpoint_here>` con l'indirizzo dell'endpoint WCF.

1. In `ViewController.cs`, aggiornare il `ViewDidLoad` metodo in modo che è simile al seguente:

    ```csharp
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();
            InitializeHelloWorldServiceClient();

            getHelloWorldDataButton.TouchUpInside += GetHelloWorldDataButton_TouchUpInside;
            sayHelloWorldButton.TouchUpInside += SayHelloWorldButton_TouchUpInside;
        }
    ```
  
1. In `ViewController.cs`, aggiungere il `InitializeHelloWorldServiceClient` e `CreateBasicHttpBinding` metodi:

    ```csharp
        void InitializeHelloWorldServiceClient()
        {
            BasicHttpBinding binding = CreateBasicHttpBinding();
            _client = new HelloWorldServiceClient(binding, Endpoint);
        }

        static BasicHttpBinding CreateBasicHttpBinding()
        {
            BasicHttpBinding binding = new BasicHttpBinding
            {
                Name = "basicHttpBinding",
                MaxBufferSize = 2147483647,
                MaxReceivedMessageSize = 2147483647
            };

            TimeSpan timeout = new TimeSpan(0, 0, 30);
            binding.SendTimeout = timeout;
            binding.OpenTimeout = timeout;
            binding.ReceiveTimeout = timeout;
            return binding;
        }
    ```
  
1. In `ViewController.cs`, aggiungere i gestori eventi per il `TouchUpInside` eventi nelle due `UIButton` istanze:

    ```csharp
        async void GetHelloWorldDataButton_TouchUpInside(object sender, EventArgs e)
        {
            getHelloWorldDataText.Text = "Waiting for WCF...";
            var data = new HelloWorldData
            {
                Name = "Mr. Chad",
                SayHello = true
            };

            HelloWorldData result;
            try
            {
                result = await _client.GetHelloDataAsync(data);
                getHelloWorldDataText.Text = result.Name;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }

        async void SayHelloWorldButton_TouchUpInside(object sender, EventArgs e)
        {
            sayHelloWorldText.Text = "Waiting for WCF...";
            try
            {
                var result = await _client.SayHelloToAsync("Kilroy");
                sayHelloWorldText.Text = result;
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
    ```

1. Eseguire l'applicazione, assicurarsi che il servizio WCF è in esecuzione e fare clic su due pulsanti. L'applicazione chiama WCF in modo asincrono, a condizione che il `Endpoint` campo sia impostato correttamente:

    [![](walkthrough-working-with-wcf-images/image10.png "Entro 30 secondi una risposta deve essere ricevuta da ogni metodo WCF e l'applicazione dovrebbe essere simile a questa schermata")](walkthrough-working-with-wcf-images/image10.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questa esercitazione viene descritto come lavorare con un servizio WCF in un'applicazione per dispositivi mobili con xamarin e xamarin. IOS. Viene illustrato come creare un servizio WCF e viene illustrato come configurare Windows 10 e IIS Express per accettare connessioni dai dispositivi remoti. Quindi illustrato come generare un client del proxy WCF e illustrato come utilizzare il client del proxy in applicazioni sia xamarin e xamarin. IOS.


## <a name="related-links"></a>Collegamenti correlati

- [HelloWorld (esempio)](https://developer.xamarin.com/samples/mobile/WCF-Walkthrough/)
- [Sviluppo di applicazioni orientate ai servizi con WCF](https://docs.microsoft.com/en-us/dotnet/framework/wcf/index)
- [Procedura: creare un Client Windows Communication Foundation](https://docs.microsoft.com/en-us/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [ServiceModel Metadata Utility Tool (svcutil.exe)](https://docs.microsoft.com/en-us/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
