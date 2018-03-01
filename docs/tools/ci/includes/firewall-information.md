## <a name="firewall-configuration"></a>Configurazione del firewall

In ordine per i test da inviare a Xamarin Test Cloud, è possibile che il computer di invio di test deve essere in grado di comunicare con i server del Cloud di Test. I firewall devono essere configurati per consentire il traffico di rete da e verso i server si trova in **testcloud.xamarin.com** sulle porte 80 e 443. Questo endpoint è gestito dal DNS e l'indirizzo IP è soggetto a modifiche. 

In alcuni casi, un test (o un dispositivo che esegue il test) deve comunicare con i server web protetti da un firewall. In questo scenario il firewall deve essere configurato per consentire il traffico dagli indirizzi IP seguenti:

* **195.249.159.238**
* **195.249.159.239**
