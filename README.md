# ProgettoDSBD_RapisardaGabriele-SambataroMario

Lo sviluppo dell'applicazione è avvenuto sfruttando le funzionalità fornite dall'ambiente docker al fine di creare un applicativo in grado di gestire diversi microservizi.
In particolare sono stati implementati i seguenti container:
- MySQL(db): questo container mantiene una distribuzione MySql alla versione 8.0.32, per memorizzare i dati in modo persistente.
- Zoo e Kafka: i due container conterranno i rispettivi servizi necessari ad implementare il messaging asincrono tipico della piattaforma Apache Kafka.
- Init-Kafka: container che permette di inizializzare il topic utilizzato per implementare la comunicazione asincrona.
- ETL data pipeline: come da definizione questo microservizio prevede una serie di step di processamento di dati provenienti da una specificata applicazione, nel caso 
in esame si è utilizzata come sorgente il seguente server Prometheus http://15.160.61.227:29090/". Verranno valutate un set di 5 metriche negli intervalli di tempo di 
1h,3h e 12h.
- Data Storage: microservizio che tramite Kafka preleva i dati inviati sullo specifico topic, e li inserisce nelle apposite tabelle del DB “DataStorage” creato tramite 
il container mysql.
- Data Retrieval: microservizio che offre un interfaccia REST API in grado di effettuare QUERY sul DB sopracitato e restituire attraverso delle GET le informazioni 
precedentemente processate dall’ ETL data pipeline e salvate tramite l’apposito microservizio.
- SLA Manager: microservizio che permette di definire uno SLA set composto da 5 metriche(tali metriche vengono ricavate tramite una POST API effettuata sfruttando 
l’enstensione TALEND API TESTER), che restituisce attraverso delle GET il numero di violazioni passate e previste nei prossimi 10m(numero di volte che il parametro 
sotto esame sfora un range di valori inviato al microservizio tramite la sopracitata POST API).


GUIDA all'avvio dell'applicazione:

- Avviare il docker-compose all'interno della cartella progettodsbd
- Una volta verificato che lo stato dei container sia ok verranno eseguiti in automatico i .py dei singoli container
- [nota] ETL data pipeline ha attualmente settati come parametro di start_time[10m,15m,20m] per un test rapido dell'applicativo, se si vuole eseguire per [1h,3h,12h] è 
necessario cambiare questi dati sia dal codice di /etl_data_pipeline/main.py e /sla_manager/main.py
- una volta completata l'elaborazione dell'ETL i dati verranno inviati e salvati nel db attraverso il data_storage
- sarà dunque possibile andare a visualizzare i valori riguandanti metriche e metadati attraverso le API fornite dal data_retrieval
- Per quanto riguarda invece le prediction sarà necessario inviare una POST al seguente indirizzo: " 127.0.0.1:5003/SLA " (la procedura per eseguire questa operazione 
è possibile visualizzarla all'indirizzo " 127.0.0.1:5003")



#SLA GET
- GET -> http://localhost:5003  
- GET -> http://localhost:5003/check_json
- GET -> http://localhost:5003/prediction_json

#SLA POST necessaria per caricare i dati relativi alle prediction sul db
- POST -> http://localhost:5003/SLA

#DATA_RETRIEVAL

- GET -> http://localhost:5002/metrics
- GET -> http://localhost:5002/metadata
- GET -> http://localhost:5002/predict
