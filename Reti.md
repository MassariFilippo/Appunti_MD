# APPUNTI RETI DI TELECOMUNICAZIONE
## Protocolli di Internet e IP

### Architettura di Internet

L'architettura di Internet è organizzata in strati:

1. Applicazioni (e-mail, ftp, telnet, www)
2. TCP/UDP (Strato di Trasporto)
3. IP (Strato di Rete)
4. Collegamento dati (es. Ethernet, X25, Aloha)
5. Fisico

Questa struttura corrisponde al modello OSI semplificato, con IP che opera al livello di rete (strato 3).

### Internet Protocol (IP) - RFC 791

Caratteristiche principali:
- Funziona a commutazione di pacchetto in modalità connectionless
- Gestisce la trasmissione di datagrammi da sorgente a destinazione attraverso reti eterogenee
- Identifica host e router tramite indirizzi IP di 32 bit
- Frammenta e riassembla i datagrammi quando necessario
- Offre un servizio "best effort" senza garanzie di affidabilità o controllo di flusso

### Struttura degli indirizzi IP

- Lunghezza fissa di 32 bit
- Rappresentazione in formato dotted decimal (es. 137.204.212.1)
- Numero massimo teorico di indirizzi: 2^32 = 4.294.967.296
- Assegnati dalla IANA (Internet Assigned Numbers Authority)

### Formato del pacchetto IP

Il pacchetto IP è composto da:

1. Header (intestazione)
2. Payload (dati utente)

### Campi principali dell'header IP

- **Prima riga (identificazione)**
  - **Version (4 bit)**: versione del protocollo IP (attualmente 4)
  - **IHL (Internet Header Length) (4 bit)**: lunghezza dell'intestazione in parole da 32 bit
  - **Type of Service (8 bit)**: indica il tipo di servizio richiesto (continua ad essere un problema non risolto)
  - **Total Length (16 bit)**: lunghezza totale del datagramma in byte (fino a 65,535 byte)

- **Seconda riga (controllo di dimensione e segmentazione del pacchetto)**
  - **Identification (16 bit)**: identificatore univoco del datagramma (dato che un pacchetto potrebbe essere diviso in rete)
  - **Flags (3 bit)**: controllo della frammentazione (spiegati sotto)
  - **Fragment Offset (13 bit)**: posizione del frammento nel datagramma originale (indica la posizione del primo byte rispetto all'inizio)

- **Terza riga (controllo dell'errore)**
  - **Time to Live (TTL) (8 bit)**: numero massimo di hop consentiti (a livello funzionale potrebbe anche non esistere più perché la rete è molto efficiente ed i pacchetti non girano più a caso per essa, oggi vengono usati come strumenti di controllo)
  - **Protocol (8 bit)**: indica il protocollo di livello superiore
  - **Header Checksum (16 bit)**: per il controllo degli errori nell'intestazione

- **Quarta riga**
  - **Source Address (32 bit)**: indirizzo IP sorgente

- **Quinta riga**
  - **Destination Address (32 bit)**: indirizzo IP destinazione

- **Sesta riga opzionale (quasi mai usata)**
  - **Options (variabile)**: opzioni aggiuntive
  - **Padding**: bit di riempimento per allineare l'intestazione a 32 bit

### Frammentazione dei datagrammi

- Necessaria quando il datagramma è troppo grande per essere trasmesso su una rete
- Può essere effettuata da qualsiasi apparato di rete con protocollo IP
- I frammenti vengono riassemblati solo dal terminale ricevente
- Il campo "Fragment Offset" indica la posizione del frammento nel datagramma originale
- I campi "Identification", "Flags" e "Fragment Offset" sono usati per gestire la frammentazione

### Flags per la frammentazione:
- Bit 0: sempre 0
- Bit 1 (DF - Don't Fragment): 
  - 0 = si può frammentare
  - 1 = non si può frammentare (nel caso fosse a 1 e fosse necessario frammentarlo lo distruggo)
- Bit 2 (MF - More Fragments):
  - 0 = ultimo frammento (aiuta a riordimare i paccheti in arrivo, è tecnicamente superfluo ma ci risparmia il calcolo di vedere quanto era lungo)
  - 1 = frammento intermedio

### Calcolo del Fragment Offset:
- Il datagramma è diviso in blocchi di 8 byte (64 bit)
- L'offset è calcolato in unità di 8 byte dall'inizio del datagramma originale (non ho bisogno di mappare tutti i bit ma posso mapparli a blocchi di 8 byte per ridurre a 13 il numero di bit necessari a tenerne traccia, ciò implica che la frammentazione non potrà mai scendere sotto i 64 bit perché non sarei più in grado di ricomporre il pacchetto)

### Time to Live (TTL)

- Imposta un limite al numero di hop che un pacchetto può attraversare
- Valore iniziale tipico: 64 (massimo 255)
- Decrementato di 1 ad ogni hop
- Se raggiunge 0, il pacchetto viene scartato

### Riassemblaggio dei datagrammi

- I frammenti possono arrivare fuori sequenza o con tempi diversi
- Il riassemblaggio avviene solo al terminale di destinazione
- Utilizza i campi Identification, Flags e Fragment Offset per ricostruire correttamente il datagramma originale

## Istradamento IP

### Internet e il Modello di Instradamento IP
- **Instradamento a pacchetto**: Internet utilizza la commutazione a pacchetto per trasmettere dati.
- Esistono più percorsi per raggiungere una destinazione. Il routing (instradamento) avviene pacchetto per pacchetto, e i router decidono quale percorso seguire.

### Componenti della Rete
- **Network IP**: Internet è costituita da tante reti isolate (Network IP). Ogni network IP è un'isola che contiene host (calcolatori terminali). Questi host devono essere necessariamente in grado di comunicare tra di loro; il caso più estremo è un solo host (calcolatore) che parla con se stesso.
- **Router**: Detti anche Gateway, collegano le isole e permettono la comunicazione tra reti diverse. Funzionano fino al livello 3 del modello OSI. In molti casi, il trasferimento di dati compie un percorso che passa per più gateway e probabilmente anche per più network. Due router connessi che a loro volta connettono altrettante network IP possono essere visti,

### Tecnologie di Implementazione
   - **Wi-Fi**: Wireless a breve distanza. (dal punto di vista dell'ip è un tipo di comunicazione 1a1 che passa per il centro stella e viene poi smistata )
   - **ADSL/xDSL**: Connessioni via cavo a media distanza.
   - **Ethernet**: Connessioni cablate locali.
   - **GPRS/EDGE/LTE**: Connessioni radio fornite da operatori pubblici.

### Indirizzo IP
   - L'indirizzo IP, usato per raggiungere una specifica network al dilà dell'plementazione su cui lavora, è composto da due parti:
     - **Net ID**: Identifica la rete (network ip), parte di sinistra dell'indirizzo
     - **Host ID**: Identifica l'host all'interno della rete (singolo calcolatore), parte destra dell'indirizzo.
   - La distinzione tra Net ID e Host ID è determinata dalla **Netmask**.

### Routing e Instradamento
- **Direct Delivery**: Quando l'IP sorgente e destinatario sono sulla stessa rete. Supponendo di essere in collegamento Ethernet in un network IP, inseriremo l'IP in un indirizzo di livello 2 contenente il MAC del calcolatore da raggiungere. Il MAC è fondamentale perché evita che tutti i calcolatori debbano ricevere tutti i pacchetti e verificare se l'IP è il loro, cosa che renderebbe gli host incapaci di fare qualsiasi altra cosa data la mole di lavoro. Questo MAC viene reperito grazie a una tabella contenuta nel calcolatore che mette in relazione IP con MAC. Se non è presente, lo richiede partendo dall'IP che vuole raggiungere grazie al protocollo ARP, che fa una richiesta broadcast. Questo scambio è una consegna diretta.

- **Indirect Delivery**: Se l'IP destinatario è su un'altra rete, il pacchetto viene inviato a un router intermedio. A livello 1, l'IP di destinazione sarà quello del calcolatore obiettivo, ma a livello 2, il MAC sarà quello del gateway della propria rete. Poiché il gateway è un router, non scarterà il pacchetto nonostante l'IP di destinazione non corrisponda al proprio; invece, lo instraderà verso un altro router. Il primo e l'ultimo scambio, tra l'host spedente e il rispettivo router e tra l'host ricevente e il rispettivo router, sono scambi diretti. Tutti gli scambi intermedi sono indiretti e devono essere completati entro il limite di 255 hop imposto dal TTL. La decisione nei passaggi indiretti è simile a quella nei passaggi diretti: il router utilizzerà il protocollo ARP (pacchetti broadcast a livello data link) per ottenere l'indirizzo del gateway successivo, ovvero l'unico altro calcolatore della loro rete, per passargli il pacchetto. In questi casi, è opportuno avere un indirizzo IP /30, in modo tale che, tolti i 2 indirizzi proibiti, ne restino 2 per i 2 gateway della rete. Per determinare se è possibile effettuare una consegna diretta, si utilizza la tabella di instradamento IP. Grazie a essa, è possibile inviare una richiesta ARP, nella speranza di una ARP reply, attraverso l'interfaccia corretta e agli indirizzi che contengono l'IP dell'host, evitando una richiesta ARP broadcast che appesantirebbe eccessivamente l'infrastruttura di rete. In caso di mancanza di reply, dato che l'IP è un protocollo best effort, si scarta il pacchetto e si segnala l'errore.

- **Tabella di Instradamento**: Ogni nodo (host o router) ha una tabella che contiene informazioni sulle destinazioni possibili, netmask, gateway e interfacce. Le righe della tabella rappresentano le singole istanze di instradamento, mentre le colonne contengono le opzioni di instradamento. Tipicamente, la tabella ha cinque colonne principali:
  - **Destination**: Contiene gli indirizzi IP o i gruppi di IP raggiungibili.
  - **Netmask**: Indica la netmask che permette di interpretare correttamente l'IP.
  - **Gateway**: L'IP del gateway successivo a cui fare riferimento.(Se si dovesse essere già un host finale ci possono esere più possibilità come per esempio il proprio ip, la scritta self ecc)
  - **Interface**: L'interfaccia di rete attraverso cui inviare il pacchetto.(Mi dice da dove fare la richiesta Arp qualora fosse necessarai)
  - **Metric**: Rappresenta il costo del percorso, utilizzato per determinare il percorso migliore quando sono disponibili più opzioni.
Questa struttura permette ai nodi di instradare i pacchetti in modo efficiente, scegliendo il percorso ottimale basato sulle informazioni disponibili nella tabella di instradamento.

!CHIEDERE SPEIEGAZIONE DI COME FARE LA ARP UNA VOLTA CHE SI ARRIVA AL GATWAY DELLA NETWORK IP GIUSTA!

- **Utilizzo della Tabella di Instradamento da parte dei Gateway**: I gateway utilizzano la tabella di instradamento per determinare il percorso ottimale per ogni pacchetto. La tabella contiene informazioni su destinazioni, netmask, gateway e interfacce. Quando un pacchetto arriva al gateway, questo esamina l'indirizzo IP di destinazione e lo confronta con le voci nella tabella di instradamento. Il gateway seleziona la voce con la netmask più lunga che corrisponde all'indirizzo di destinazione (Longest Prefix Match) e inoltra il pacchetto attraverso l'interfaccia appropriata. Questo processo evita il sovraccarico della rete con broadcast inutili, poiché il gateway invia il pacchetto solo all'interfaccia specifica che conduce verso la destinazione finale. In questo modo, i pacchetti vengono instradati in modo efficiente e preciso, riducendo il traffico di rete e migliorando le prestazioni complessive. Se non trova una corrispondenza, scarta il pacchetto e comunica un errore. Per trovare la corrispondenza migliore, avendo l'IP di destinazione, il gateway procede con il processo di table lookup durante il quale, partendo dalla netmask con valore più grande e andando a decrescere, prende la netmask e fa l'AND bit a bit con l'IP di destinazione del datagramma e lo confronta con l'IP di destinazione della singola riga. Qualora il risultato fosse uguale, la corrispondenza è trovata (abbiamo trovato il ricevente); altrimenti, continua con le altre righe e tiene il risultato più simile che quindi avvicina di più all'host finale.

- **Longest Prefix Match**: Per selezionare il percorso corretto, il nodo confronta l'indirizzo di destinazione con la netmask più lunga disponibile nella tabella.

### Protocolli ARP e Risoluzione degli Indirizzi
   - **ARP (Address Resolution Protocol)**: Utilizzato per trovare l'indirizzo fisico (MAC) di un host a partire dall'indirizzo IP. Il processo coinvolge:
     1. Un messaggio broadcast ARP request.
     2. La risposta dell'host con il proprio indirizzo MAC.
   - **Cache ARP**: Ogni host mantiene una tabella di cache con le corrispondenze IP-MAC.

### Subnetting
- Esempio di **Netmask**: Una netmask è composta da 32 bit che sono in locale e rimangono affibiati al singolo calcolatore, tutti i calcolatori della stessa network avranno la stessa netmask, con tutti 1 a sinistra e tutti 0 a destra. Il punto in cui avviene il cambio rappresenta la separazione tra Net-ID e Host-ID nell'indirizzo IP. Per la parte di host, indipendentemente dalla dimensione, gli indirizzi con tutti 0 e tutti 1 non vengono assegnati e sono riservati a compiti specifici. Quindi, si perdono 2 indirizzi: quello con tutti 0 identifica la network ID senza la parte di host, mentre con tutti gli 1 è riservato al gateway predefinito.
  - **Net-ID**: 192.168.1.0/24 indica una rete con Net-ID di 24 bit.
  - Può essere suddivisa in sottoreti usando la netmask per identificare la parte Net e Host dell'indirizzo.

### Routing Aggregato
La **semplificazione delle tabelle di routing** avviene aggregando più network in un’unica voce, riducendo la complessità per i router. Questo processo è noto come **supernetting** o **route aggregation**.

### Come si aggregano le reti
1. **Identificazione delle reti contigue**: Per aggregare le reti, è necessario che queste siano contigue, ovvero che gli indirizzi IP siano consecutivi.
2. **Calcolo della supernet**: Si determina una nuova netmask che copra tutte le reti contigue. Ad esempio, se si hanno le reti 192.168.1.0/24 e 192.168.2.0/24, si può aggregarle in una singola rete 192.168.0.0/22.
3. **Aggiornamento delle tabelle di routing**: Le voci delle singole reti vengono sostituite da una voce unica che rappresenta la supernet.

### Vantaggi del Routing Aggregato
- **Riduzione delle voci nelle tabelle di routing**: Aggregando le reti, si diminuisce il numero di voci che i router devono gestire, semplificando il processo di instradamento.
- **Miglioramento delle prestazioni**: Con meno voci da esaminare, i router possono prendere decisioni di instradamento più rapidamente, migliorando le prestazioni complessive della rete.
- **Minore utilizzo di memoria**: Le tabelle di routing più piccole richiedono meno memoria, liberando risorse per altre operazioni.
- **Scalabilità**: La rete diventa più scalabile, poiché l'aggiunta di nuove reti contigue può essere gestita facilmente attraverso l'aggiornamento della supernet esistente.
- **Riduzione del traffico di aggiornamento**: Con meno voci da aggiornare, si riduce il traffico di aggiornamento delle tabelle di routing tra i router, migliorando l'efficienza della rete.

### Uso del Gateway
   - **Gateway**: Il nodo che connette due network IP. È responsabile dell'instradamento dei pacchetti verso la destinazione finale.

### Esempi di Tabelle di Routing
   - La tabella di routing contiene campi come:
     - **Destination**: L'indirizzo IP o la rete da raggiungere.
     - **Netmask**: La maschera di rete che separa Net ID da Host ID.
     - **Gateway**: L'indirizzo IP del router da usare.
     - **Interface**: L'interfaccia di rete attraverso cui inviare il pacchetto.

### Classless vs Classfull IP
- **Indirizzi IP e Netmask**:
  - Gli indirizzi IP pubblici devono essere unici su Internet e identificano sorgente e destinazione nel datagramma IP.
  - La **netmask** è locale a ciascun nodo, utilizzata per determinare la porzione di indirizzo che rappresenta la rete e l'host. Non viene trasportata nel datagramma, ma è parte della tabella di routing del nodo.
  - Inizialmente, la divisione tra Net-ID e Host-ID era assoluta, mentre successivamente si è passati a un approccio più flessibile (Classless).

### Classi di indirizzi IP (Classfull)
- Durante le fasi iniziali di Internet, gli indirizzi IP erano suddivisi in **classi**:
  - **Classe A**: grandi reti (da 0.0.0.0 a 127.255.255.255) ed andavano letti come se avessero un netmask /8.
  - **Classe B**: reti di medie dimensioni (da 128.0.0.0 a 191.255.255.255) ed andavano letti come se avessero un netmask /16.
  - **Classe C**: piccole reti (da 192.0.0.0 a 223.255.255.255) ed andavano letti come se avessero un netmask /24.
  - **Classe D**: indirizzi multicast (da 224.0.0.0 a 239.255.255.255), riservati al multicast.
  - **Classe E**: sperimentale (da 240.0.0.0 a 255.255.255.255), riservati a casi sperimentali.
  - **Indirizzi riservati**: ad esempio, 127.x.x.x per loopback e 255.255.255.255 per broadcast.
- Dunque, la netmask dell'indirizzo era implicita nella classe dell'indirizzo stesso. Tuttavia,
 questo approccio presentava limitazioni significative, come la scarsa flessibilità nella gestione 
 degli indirizzi IP e l'inefficienza nell'allocazione degli indirizzi. Per risolvere questi problemi, 
 è stato introdotto il **Classless Inter-Domain Routing (CIDR)**, che permette una gestione più efficiente 
 e flessibile degli indirizzi IP, eliminando la rigida suddivisione in classi e utilizzando netmask variabili.
- Con CIDR, la netmask è specificata esplicitamente utilizzando la notazione **slash** (ad esempio, /24), 
permettendo una suddivisione più granulare e ottimizzata degli indirizzi IP.
- Inoltre, CIDR facilita l'aggregazione delle rotte (supernetting), riducendo la complessità delle tabelle di 
routing e migliorando l'efficienza della rete.
- Per esempio, un indirizzo IP come 192.168.1.0/24 indica che i primi 24 bit dell'indirizzo sono utilizzati per 
identificare la rete, mentre i restanti 8 bit sono utilizzati per identificare gli host all'interno di quella rete.
- Questa flessibilità consente di adattare meglio la dimensione delle reti alle esigenze specifiche, evitando 
sprechi di indirizzi IP e migliorando la scalabilità della rete.

### Subnetting
- La **subdivisione in sottoreti (subnetting)** consente di frammentare una rete principale in reti più piccole 
(subnet) per assegnarle a diverse sotto-amministrazioni all'interno di un'organizzazione.
  - La **subnet mask** permette di personalizzare l'assegnazione dell'indirizzo IP, suddividendo 
  l'Host-ID in due parti: una parte per la subnet e l'altra per l'host.
  - **Esempio**: L'Università di Bologna utilizza una rete di classe B (137.204.0.0) e divide 
  l'Host-ID per creare 254 sottoreti di classe C, utilizzando la netmask 255.255.255.0.

### CIDR (Classless Inter-Domain Routing)
- Con la diffusione di Internet, la suddivisione rigida in classi si è dimostrata inefficiente, 
portando alla creazione di CIDR (**RFC 1519**).
  - CIDR elimina la logica delle classi nei router e consente la 
  **definizione variabile della dimensione del Net-ID**.
  - Le tabelle di routing includono le netmask per una gestione più flessibile delle reti.
  - **Obiettivi**:
    - Ottimizzazione dello spazio di indirizzi IP.
    - Aggregazione delle informazioni di routing (supernetting).
    - Gestione di reti di classe A e B limitate e della crescita delle tabelle di routing.
- Oggi, la distinzione tra host e net è locale a tal punto che dipende dal punto in cui si guarda, 
punto nel quale è contenuta la netmask di riferimento per quella specifica istanza. Dunque, 
uno stesso indirizzo ha rilevanza diversa in punti diversi della rete.
  - **Esempio**: Un indirizzo IP come 192.168.1.0/24 può essere visto come una singola rete 
  in un contesto, mentre in un altro contesto, con una netmask diversa, può rappresentare una 
  parte di una rete più grande o essere suddiviso in sottoreti più piccole.
  - Questo approccio flessibile permette una gestione più efficiente e scalabile degli indirizzi IP, 
  adattandosi meglio alle esigenze specifiche delle diverse reti.

### Supernetting
- Il **supernetting** consente di unire più reti contigue per ridurre le voci nelle tabelle di routing. Ad esempio:
  - Un'organizzazione ha bisogno di circa 2000 indirizzi IP: invece di una rete di classe B (64k indirizzi), è preferibile combinare 8 reti di classe C (2048 indirizzi).
  - Supernetting consente di rappresentare queste reti come una singola super-rete: es. 194.24.0.0/21, con netmask 255.255.248.0.
- **Operazioni duali**:
  - Subnetting riduce la parte Host-ID per estendere il Net-ID.
  - Supernetting estende l'Host-ID per aggregare reti con Net-ID simili.

### Protocolli di Controllo e ICMP
- **IP** è un protocollo **best effort**, non garantisce la consegna corretta dei datagrammi e si appoggia a protocolli affidabili di livello superiore come **TCP**.
  - Tuttavia, è necessario un protocollo per gestire situazioni anomale e segnalare errori: **ICMP (Internet Control Message Protocol)**.
  - ICMP segnala errori come:
    - **Destination Unreachable (Type 3)**: quando un gateway non può raggiungere la rete o l'host.
    - **Time Exceeded (Type 11)**: quando il Time-to-Live (TTL) di un datagramma si azzera.
    - **Redirect (Type 5)**: un router segnala all'host sorgente una via più ottimale.
  - ICMP non corregge gli errori, ma fornisce informazioni per diagnosticare problemi di rete.

### Comandi utili: PING e TRACEROUTE
- **PING**:
  - Verifica se un host è raggiungibile inviando pacchetti ICMP di tipo "echo" e ricevendo "echo reply".
  - Opzioni: definire il numero di pacchetti, timeout, dimensione pacchetti, ecc.
- **TRACEROUTE**:
  - Mostra il percorso dei pacchetti verso una destinazione, utilizzando pacchetti ICMP con TTL crescente. 
  Mostra il nome DNS e l'indirizzo IP dei nodi intermedi.

### **Gestione della numerazione IP: DHCP**
- **DHCP (Dynamic Host Configuration Protocol)** consente la configurazione dinamica e automatica di un indirizzo IP per un host, assegnando:
  - Indirizzo IP, netmask, gateway predefinito, server DNS, ecc.
- **Processo DHCP**:
  - **DHCPDISCOVER**: l'host cerca un server DHCP inviando un messaggio di richiesta in broadcast.
  - **DHCPOFFER**: i server DHCP rispondono proponendo un indirizzo IP.
  - **DHCPREQUEST**: l'host accetta una delle offerte e richiede l'indirizzo IP.
  - **DHCPACK**: il server DHCP conferma la configurazione con un messaggio di risposta.

### Progettazione di Reti Aziendali

- **Esempio di rete aziendale**: Tre siti aziendali (S1, S2, S3) devono essere interconnessi con una rete a maglia completa. 
Gli indirizzi di classe C assegnati sono basati su 196.200.96.0/24. Con questa rete ho più IP del necessario, 
dato che ho 256 IP, ma con una /25 ne avrei avuti 128 e sarei stato troppo di misura.

- **Soluzione 1**: Non avendo una sola rete fisica, non possiamo avere consegna diretta. 
Dovremmo usare dei gateway con una rete "comune" di collegamento tra questi ultimi. Avendo una /24, 
posso ottenere 4 reti /26, dunque 62 IP per blocco. Questa soluzione provoca un grande spreco nel 
blocco usato per il collegamento (uso 3 numeri per i gateway e i restanti 58 non servono a niente). 
Inoltre, se devo crescere, sarò in difficoltà perché se creo una nuova sede essa necessiterà di un nuovo 
blocco che non ho modo di creare. Se aumento i terminali nelle sedi già esistenti, posso arrivare solo a 62.

- **Soluzione 2**: Potrei valutare netmask a grandezza differenziata. Ricordiamoci che più divido, 
più mi mangio numeri, dato che per ogni divisione il primo e l'ultimo numero sono non utilizzabili. 
Creerò dunque 2 reti /26 (metà degli indirizzi), 3 reti /27 (3/4 dei restanti indirizzi), 
1 rete /28 (1/2 dei restanti indirizzi) e /30 con le quali completerò gli indirizzi. Così facendo, 
le 2 reti /26 saranno usate per 2 sedi, 1 rete /27 per la terza sede e 3 reti /30 per i 3 gateway, 
mantenendo disponibili tutti gli altri indirizzi per il futuro.
Se possibile, un'unica rete per tutti i gateway è preferibile a questa seconda soluzione, perché se 
dobbiamo fisicamente creare l'infrastruttura urbana, è più facile da realizzare. Fossero state 3 isole, forse 
fisicamente sarebbero state più comode 3 reti.

### ICMP (Internet Control Message Protocol)
  - **Struttura pacchetto ICMP**:
    1. **IP Header**: Intestazione del protocollo IP.
    2. **Message Type**: Tipo di messaggio ICMP (8 bit).
    3. **Message Code**: Codice del messaggio ICMP (8 bit).
    4. **Checksum**: Controllo degli errori per l'intestazione ICMP (16 bit).
    5. **Additional Fields**: Campi aggiuntivi specifici per il tipo di messaggio ICMP.
    6. **Data**: Dati del messaggio ICMP.

- **Tipi di messaggi ICMP**:
  - **Destination Unreachable (Type 3)**: Notifica l'inaccessibilità di host o sottorete.
    - **Code 0**: Network Unreachable
    - **Code 1**: Host Unreachable
    - **Code 2**: Protocol Unreachable
    - **Code 3**: Port Unreachable
    - **Code 4**: Fragmentation Needed and Don't Fragment was Set
    - **Code 5**: Source Route Failed
    - **Code 11**: Destination Network Unreachable for Type of Service
  - **Time Exceeded (Type 11)**: Notifica il superamento del TTL o il timeout per il riassemblaggio dei frammenti.
  - **Echo/Echo Reply (Type 8/0)**: Utilizzati per determinare lo stato di raggiungibilità di un host.
  - **Timestamp Request/Reply (Type 13/14)**: Misura il tempo di transito nella rete.

### Comandi di Rete
- **PING**: Verifica la raggiungibilità di un host inviando pacchetti di tipo ICMP Echo Request.
  - Parametri principali: `-n` (numero di pacchetti), `-t` (ping continuo), `-a` (risoluzione DNS), 
  `-l` (dimensione del pacchetto).
- **TRACEROUTE**: Identifica il percorso seguito dai pacchetti verso una destinazione tramite la gestione del TTL.
  - **Come funziona**: Traceroute invia pacchetti con un valore TTL (Time To Live) inizialmente impostato a 1. 
  Ogni router lungo il percorso decrementa il TTL di 1. Quando il TTL raggiunge 0, il router scarta il pacchetto 
  e invia un messaggio ICMP "Time Exceeded" al mittente. Traceroute incrementa quindi il TTL e invia un nuovo pacchetto, 
  ripetendo il processo fino a raggiungere la destinazione finale. Questo permette di identificare ogni hop (router) 
  lungo il percorso.
  - Parametri principali: `-m` (TTL massimo), `-q` (numero di query per hop), `-w` (timeout per risposta).

### Gestione degli Indirizzi IP
- **DHCP (Dynamic Host Configuration Protocol)**: Automatizza l'assegnazione dinamica di IP, netmask, gateway e DNS. Processo chiave:
  - **DHCPDISCOVER** → **DHCPOFFER** → **DHCPREQUEST** → **DHCPACK**.
  - Il processo inizia con un messaggio broadcast di livello 2 (come ARP) chiamato **DHCPDISCOVER**, 
  inviato sulla rete per cercare un server DHCP. I server DHCP rispondono con un messaggio **DHCPOFFER**, 
  proponendo i parametri di configurazione. L'host seleziona una delle offerte e invia un **DHCPREQUEST** 
  al server scelto. Il server risponde con un **DHCPACK**, confermando i parametri di configurazione.

### Filtraggio dei Pacchetti
### Metodologie di Filtraggio dei Datagrammi
- Le metodologie di filtraggio dei datagrammi sono tecniche utilizzate per controllare il traffico di rete 
in base a criteri predefiniti. Questi criteri possono includere indirizzi IP, numeri di porta, protocolli e 
altre informazioni contenute nei pacchetti. In questo contesto, i gateway fungono da punti di controllo per 
il traffico di rete, applicando le regole di filtraggio per garantire che solo il traffico autorizzato possa 
attraversare la rete. Queste metodologie sono fondamentali per implementare firewall e altre soluzioni di sicurezza 
di rete, garantendo che solo il traffico autorizzato possa attraversare i confini della rete.
  - **Packet Filter**: Controlla l'accesso a determinati servizi o indirizzi in base alle regole 
  impostate sugli IP, protocolli o porte. È detto instradamento selettivo e opera a livello IP basando 
  le sue decisioni sulla natura dell'IP stesso come il TTL, i tipi di indirizzi ecc. Il vantaggio è che 
  a livello di gateway basta implementare il filtro a livello software, ma esistono situazioni in cui i 
  filtri a livello IP non sono sufficienti.
  - **Stateful Packet Inspection (SPI)**: Monitora lo stato delle connessioni e adatta dinamicamente le regole 
  di filtraggio. Adattando opportunamente il software, si può andare più a fondo nel pacchetto guardando, per 
  esempio, il tipo di protocollo, attuando così filtri semanticamente più efficaci. Ciò viola il protocollo OSI 
  dato che vado a leggere dati più profondi del livello IP.
  - **Application Layer Gateway (Proxy)**: Monitora le connessioni applicative (ad esempio, FTP, HTTP, SIP), 
  garantendo controllo e sicurezza a livello applicativo. In questo caso, il gateway agisce come un host che 
  scompone il pacchetto fino al livello applicativo e poi lo reinstrada se non è destinato a lui. A differenza 
  dei normali gateway con implementazioni specifiche, un proxy a livello applicativo è più complesso sia a livello 
  hardware che software.

- Le tre differenti versioni vengono adottate a necessità dato che la prima è la più leggera a 
livello computazionale e la più facile da implementare ma quella meno efficace, e viceversa per la terza.

### Firewall
Il firewall o portatagliafuoco ci difende combinando le tecnologie precedentemente descritte:
- **Packet Filter**: Filtra i pacchetti seguendo le politiche stabilite.
  - Filtri: Generalmente configurati staticamente.
  - La maggioranza delle configurazioni non permettono pacchetti per porte "non-standard" 
  (Internet Assigned Numbers Authority – IANA).
- **Stateful Packet Inspection**:
  - Mantiene il contesto dei pacchetti sia nel trasporto che nello strato applicativo.
  - Adatta dinamicamente le specifiche dei filtri.
- **Application Layer Gateway (trasparente o proxy esplicito)**:
  - Monitora le connessioni: Analizza il contenuto dei protocolli applicativi.
  - A scapito della sicurezza di comunicazione end-to-end.
  - Adatta dinamicamente le specifiche dei filtri.
- **Per ogni strato (layer) dello stack possono essere applicate politiche (policies) differenti**.
- Protezione Host: funge da filto software/hardware per accessi indesiderati dall'esterno della rete. 
(sevono architetture di difesa più complesse). Dunque si potrebbero usare configurazioni di packet filter 
e proxy per migliorare la sicurezza della rete. Un'architettura di difesa multilivello può includere:

1. **Packet Filter**: Filtra i pacchetti in base a regole predefinite sugli indirizzi IP, protocolli e porte. 
È il primo livello di difesa e agisce a livello di rete.
2. **Stateful Packet Inspection (SPI)**: Monitora lo stato delle connessioni e adatta dinamicamente le regole 
di filtraggio. Fornisce un controllo più approfondito rispetto ai semplici packet filter.
3. **Application Layer Gateway (Proxy)**: Monitora e controlla le connessioni a livello applicativo, 
garantendo un controllo dettagliato sui protocolli specifici come HTTP, FTP, e SIP. Questo livello può 
bloccare attacchi che sfruttano vulnerabilità a livello applicativo.

Combinando queste tecnologie, si ottiene una protezione più robusta contro una vasta gamma di minacce, 
migliorando la sicurezza complessiva della rete.

### Network Address Translation (NAT)
E' un gateway con funzione di paket filter che si interpone tra 2 networ e può cambiare il contenuto dei pacchetti 
in partivolare gli indirizzi sia ip che di porta.
Viene definito a livello concettuale ma non a livelli fisico/implementativo dato che ci sono molteplici modi di farlo.
- **Funzioni**: Maschera gli indirizzi IP interni, permettendo a reti private di accedere a reti pubbliche. 
- I tipi di NAT principali:
  - **Basic NAT - Conversione di indirizzo**: Converte solo gli indirizzi IP. Si trova tra 2 network e funge da gateway e modifica gli 
  indirizzi sorgente modificando IP e porta per poi reindirizzare al destinatario per poi fare il 
  contrario al ritorno grazie a una tabella delle conversioni. Questo permette di rendere il flusso monodirezionale, 
  ovvero chi è dietro al basic NAT comunica con l'esterno solo se ha richiesto qualcosa da fuori, 
  dunque costruito in questa maniera permette di essere uno strumento di protezione anche se è stato 
  concepito per risparmiare numeri.
  - **Port Address Translation (PAT) (VERIFICARE CHE SIA QUESTO IL NOME)**: Tecnica utilizzata nei router per mappare più indirizzi IP privati su un singolo 
  indirizzo IP pubblico, utilizzando numeri di porta univoci per distinguere le connessioni. Questo processo consente a 
  più dispositivi all'interno di una rete locale (LAN) di condividere un singolo indirizzo IP pubblico per accedere a Internet.
  Quando un dispositivo all'interno della rete locale invia un pacchetto verso Internet, il router sostituisce l'indirizzo 
  IP privato del dispositivo con l'indirizzo IP pubblico del router e assegna un numero di porta univoco. 
  Quando il pacchetto di risposta ritorna, il router utilizza il numero di porta per determinare a quale dispositivo 
  interno inoltrare il pacchetto. PAT è una forma di Network Address Translation (NAT) ed è particolarmente 
  utile per conservare gli indirizzi IP pubblici, che sono una risorsa limitata. Inoltre, offre un livello aggiuntivo 
  di sicurezza, poiché gli indirizzi IP privati non sono visibili dall'esterno della rete locale. Qui effettivamente 
  vado a risparmiare indirizzi IP poiché possiamo internamente vedere più indirizzi come fossero lo stesso, 
  così facendo però riduco i possibili flussi distinguibili a 2^16 dato che vengono distinti dalle porte. 
  (VERIFICARE SE SEMANTICAMENTE CORRETTO)
  - **Full Cone NAT, Restricted Cone NAT, Symmetric NAT**: Definiscono il tipo di traffico permesso.

### Considerazioni sui Firewall
- **Protezione degli host**: Il firewall può essere software (per accessi domestici) o hardware (per reti aziendali).
- **Politiche di sicurezza**:
  - **Default deny**: Blocca tutto eccetto ciò che è esplicitamente permesso.
  - **Default permit**: Permette tutto eccetto ciò che è esplicitamente bloccato.

(MANCANO 24 MIN DELLA LEZIONE DEL 16 OTTOBRE POICHè NON HO TROVATO LE SLIDE)

## Routing

### Funzioni di IP
- **Indirizzamento**: L'IP fornisce un sistema di indirizzamento univoco per identificare dispositivi sulla rete.
- **Frammentazione**: L'IP può dividere i pacchetti di dati in frammenti più piccoli per adattarsi alla dimensione massima del pacchetto supportata dai vari segmenti della rete.
- **Instradamento**:
  - Decidere che percorso un datagramma deve seguire per raggiungere la destinazione.
  - Utilizza le PCI dei datagrammi, in particolare l'indirizzo di destinazione.
  - Determina il comportamento della funzione di commutazione nei nodi.
  - Il problema dell'instradamento è più generale rispetto al protocollo di livello 3.

### Algoritmi e protocolli di instradamento
- **Instradamento**: scelta del percorso.
  - Spesso significa scegliere il prossimo router (next hop).
- **Algoritmo di instradamento**:
  - Obiettivi: semplicità, robustezza, stabilità, efficienza.

### Tabella di instradamento
- I nodi di commutazione utilizzano tabelle predisposte localmente.
- **Algoritmi senza tabella e con tabella**:
  - **Senza tabella**: Flooding, Random, Deflection routing, Source routing.
  - **Con tabella**:
    - **Instradamento fisso e centralizzato**: Utilizzato per sistemi estremamente statici, dove i dati rimangono invariati per un tempo molto più lungo della singola comunicazione. Questo metodo risulta sempre uguale dal punto di vista dell'utente.
    - **Instradamento dinamico a distanza minima**: I percorsi vengono aggiornati periodicamente per adattarsi ai cambiamenti della rete dunque risulta più dinaico del precedente.

### Flooding
- Flooding è una tecnica di instradamento in cui ogni nodo della rete ritrasmette ogni pacchetto ricevuto su tutte le sue porte, eccetto quella da cui il pacchetto è arrivato. Questo metodo garantisce che tutte le possibili strade vengano percorse, assicurando che almeno una copia del pacchetto raggiunga la destinazione.
- Garantisce:
  - **Affidabilità**: Flooding garantisce che il pacchetto raggiunga la destinazione anche se alcuni percorsi sono interrotti.
  - **Percorso più breve**: Il primo pacchetto che raggiunge la destinazione lo fa percorrendo il percorso più breve disponibile.
  - **Semplicità**: L'algoritmo è semplice da implementare poiché non richiede tabelle di instradamento complesse.
- Contro:
  - **Proliferazione dei pacchetti**: La rete può essere sovraccaricata da un numero eccessivo di pacchetti duplicati, causando congestione.
  - **Inefficienza**: L'invio di pacchetti su tutte le porte può risultare inefficiente in termini di utilizzo della larghezza di banda.
- UTilizzato:
  - **Broadcasting**: Flooding è utile per inviare messaggi di broadcast, dove l'obiettivo è raggiungere tutti i nodi della rete.
  - **Scenari di emergenza**: In situazioni in cui è cruciale che il messaggio raggiunga la destinazione, indipendentemente dall'efficienza.

### Soluzioni per il Flooding
- Non ritrasmettere il pacchetto da dove è arrivato.
- Identificazione dei pacchetti (indirizzo sorgente + numero di sequenza).
- Uso di un TTL per evitare pacchetti infiniti.
È importante ricordare che all'aumentare di queste soluzioni il protocollo si complica, andando via via a perdere di utilità a causa del suo complicarsi.

### Instradamento dinamico e statico
- **Statico**: percorsi predefiniti all'inizializzazione.
- **Dinamico**: percorsi aggiornati periodicamente per adattarsi ai cambiamenti.

### Random e Deflection Routing
- **Random Routing**: Il prossimo hop è scelto casualmente. Questo metodo è molto inefficiente e raramente utilizzato.
- **Deflection Routing**: Il pacchetto viene inviato sulla linea con meno pacchetti in attesa. Questo metodo è stato 
studiato per reti a griglia (Manhattan), dove si dimostra essere una buona soluzione.
  - **Problemi**: I pacchetti possono arrivare fuori sequenza o entrare in cicli infiniti.

### Store-and-Forward
Il pacchetto viene verificato, memorizzato e confrontato con la tabella di instradamento. 
Dopo l'elaborazione, si seleziona un'uscita e il pacchetto viene inserito in coda. 
Tutto questo avviene in una coda che gestisce l'elaborazione.

### Shortest Path Routing
L'instradamento a percorso più breve implica l'associazione di una lunghezza a ciascun 
collegamento e la ricerca dei percorsi a costo minimo utilizzando algoritmi come Bellman-Ford e 
Dijkstra. Questo può essere implementato in modo centralizzato o distribuito, sia in maniera sincrona che asincrona. 
Quando i nodi di rete vengono accesi, conoscono solo la configurazione delle loro interfacce, che può essere statica 
o dinamica tramite DHCP. Con queste informazioni, popolano la tabella di instradamento iniziale. Per implementare il 
routing a percorso più breve (shortest path) verso qualsiasi destinazione, devono utilizzare uno o più protocolli di 
routing per scambiarsi informazioni e apprendere la topologia della rete, e uno o più algoritmi per il calcolo dei 
percorsi più brevi basati sulle informazioni ottenute.

### Teoria dei grafi e rappresentazione della rete
- Le reti possono essere rappresentate come grafi orientati o non orientati.
- Il peso degli archi rappresenta il costo del collegamento.

### Routing Distance Vector
Basato sull'algoritmo Bellman-Ford, ogni nodo invia un vettore con le distanze agli altri nodi. 
Questo metodo è piuttosto datato e presenta diversi problemi, ma su piccoli sistemi questi non emergono, 
rendendolo interessante in casi specifici, ovvero con dati statici, privi di variazioni in corso d'opera, 
e in cui possiamo conoscere l'intera struttura, cosa oggi impossibile dato che la rete è dinamica e distribuita. 
In questo metodo, ogni nodo ha una lista con la distanza dagli altri nodi. Inizialmente, questa lista è composta 
solo dalla distanza da se stesso, ovvero 0. Successivamente, i nodi (router) condivideranno con i nodi a cui sono 
direttamente collegati i propri dati detti **Distance vector**, aggiornando così le tabelle dei vicini. Questo processo continua finché non 
si reperiscono informazioni da tutti i nodi, passando per i vicini e ottenendo i percorsi migliori nel sistema.
- **Problemi**:
  - **Cold start e convergenza lenta**: La convergenza si raggiunge dopo un numero di iterazioni pari al numero di nodi. 
  Questo comporta una lenta convergenza su reti grandi, poiché gli aggiornamenti tra nodi non possono essere inviati 
  continuamente, ma devono lasciare che anche i messaggi circolino, ritardando così la convergenza.
  - **Conteggio all'infinito**: In alcuni casi, si potrebbe tentare di convergere all'infinito. 
  Ad esempio, se ci sono tre router in fila e si rompe il collegamento tra due di essi, gli altri due potrebbero 
  scambiarsi aggiornamenti di distanza dal terzo all'infinito. Questo li porta a convincersi reciprocamente che 
  per raggiungere il terzo router debbano usare l'altro a cui sono collegati, finendo per passarsi all'infinito 
  pacchetti e aggiornamenti di distanza, andando a bruciare capacità computazionale. Per ovviare, si sceglie una 
  distanza tra nodi superata la quale si considera la distanza come infinita ed implementare un Triggered Update,
  ovvero un aggiornamento istantaneo del distance vector qualora ci fosse una variazione di distanza.
  - **Bouncing effect**: Situazione in cui un router si "convince" che sia opportuno mandare i propri pacchetti 
  a un altro router che poi glieli rimanderà. Questo è solitamente dovuto a una rottura e, fino a quando non viene 
  inviato un aggiornamento, i router si rimpallano i pacchetti.
- **Soluzioni**
  - **Split Horizon**: evitare di informare un nodo su una destinazione raggiungibile solo tramite esso, 
  inviando distance vector differrnziati in cui ometto tutte le distanze da nodi che vedono il ricevente come gateway.
  - **Triggered Update**: inviare aggiornamenti immediatamente in caso di modifica.
Queste solozioni non sono definitive in quanto avendo una rete con dei cicli vanno a perdere di efficacia.

### Routing Link State
- Ogni nodo costruisce un'immagine della rete tramite informazioni scambiate con i vicini.
- Si utilizza Dijkstra per calcolare i percorsi minimi.

### Router IP
- **Funzioni dei router**: Routing, Forwarding, Switching, Trasmissione.
- **Classificazione dei router**: SOHO, Access, Enterprise, Backbone.

### Tabelle di Routing e Forwarding
- **Routing table**: contiene route prefix, next hop, e metric.
- **Forwarding table**: ottimizzata per l'inoltro rapido dei datagrammi.