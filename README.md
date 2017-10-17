# Logh-Broker © ® ™ [loh ˈbrəʊkə] whitepaper (туалетная бумага (rus.)) #


## Abstract ##
Лох-брокер © ® ™ - высоэффективный комплекс программного обеспечения, предназначенный для перекладывания из пустого в порожнее.


## Disclaimer ##
Лох-брокер не супер-пупер персистентная очередь, которая может удовлетворить любые извращенные нуждны. Это всего лишь штука, которая перекладывает ваши логи - быстро и надежно.

## Собственно оно... ##
### Основные концепции: ###
 
 * Fast and robust. Быстро и эффективно © ® ™.
 * NFOS (no fucking over subscription). Все по-честному и без сюрпризов (другими словами, плачу - и плачу)
 * Exactly-once. Если смогли, то доставили ровно один раз (вы же понимаете, что настоящий EO в сферическом вакууме работает плохо?)
  
  
### Секция архитектурирования: ###
 
 * Producer -> Broker -> Consumer
 * WTF?!
 * Profit!!!
 
 
### Немного терминологии: 0) ###
 
 * Server/Machine (_сервер, машина_) - машина в стойке, которая работает и ~~майнит бит-коины~~ делает что-нибудь полезное.
 * Log message (_лог, мессага_) - осмысленная единица информации, которую хочется до кого-то доставить.
 * Queue (_очередь_) - туда складываются мессаги.
 * Producer (_продюсер_) - машина, которая производит логи в неимоверных количествах.
 * Consumer (_консумер_) - машина, желающая получить логи и ничего не потерять (но денег там нет).
 * Node (_нода_) - процесс на сервере (брокера), пачка дисков и ресурсы сети и CPU, обеспечивающие некоторые _гарантии_ производительности.
 * Worker (_хуятор_) - машина или процесс, делающий что-то полезное для общего блага.
 * Broker (_брокер_) - программно-аппаратный комплекс, который зачем-то нужен, чтобы доставлять логи от _продюсера_ до _консумера_.
 * Minor failure (_упало_) - техническая проблема, приводящая к временной недоступности узлов системы. Не включает фатальные последствия, типа замена оборудования.
 * Major failure (_пизда_) - техническа проблема, приводящая к эквиваленту полного уничтожения единицы оборудования (сервера).
 * Durable (_надежно_) - значит система не рассыпается, если машинка _упала_.
 * Durable broker (особо надежный брокер) - _брокер_, который не рассыпается, если одной ноде настала _пиздеца_.
 * Exactly-once - эвфемизм, означающий, что мессага будет доставлена до консумера не более одного раза. А мы прикладываем все усилия, чтобы она была доставлена хотя бы один раз.
 * Replication (_репликация_) - механизм замедления работы системы, приводящий к чрезмерному расходу дискового пространства, но дающий некоторые плюшки в случае пизды.
 * Scalable (_скейлится_) - означает, что система может переварить больше _логов_, если добавить в нее больше _машин_.
 * Elastic (_латексный_) - означает, что со временем, после добаления _машин_, система может начать переваривать больше _логов_.
 * Guaranties (_гарантии_) - некие слабо проверяемые в реальности обещания, которые дают друг-другу клиенты и брокер.
 * Catch-up (_догон_) - ситуация, когда продюсер/консумер затупил и вынужден поднимать данные с дисков.
 * Memory (_память_) - дешевая и быстрая (2000р/GB, 10xGB/s) херня на сервере. Дешевая в плане эксплуатации системы, но напихать до бесконечности не получится.
 * Buffered (_буферизированные_) - данные, которые находятся в _памяти_.
 * B/b (_байт, бит_) - ничего такого, просто восьми-битный байт.
 * RPS (_рпс_) - количество единиц информации в секунду.
 * NIC (_сетка_) - 10Gb Ethernet network interface controller за >350$, который может херачить over 1GB/s.
 * Disk (_диск_) - ~10TB диск, который может писать и читать со скоростью >200MB/s.
 * HDD (_шпиндель_) - обычный жесткий диск на блинах.
 * SSD (_ссд_) - дорогой диск малого объема, имеющий свойство вести себя стремительно, но непредсказуемо - как понос.
 * Seek (_сик_) - время достаточное, чтобы попить кофе. Необходимо диску для внезапного обращения в другую область (считаем, что это ~5-8ms read, ~8-12ms write на шпинделях).
 * High-load (_выско-что-там_) - нагрузка на систему, превышающая возможности гейбука в 1000 раз.
 * Source 1) (_соурс_) - идентификатор источника данных (машины), который пришлепывается к каждой мессаге. 
 * Partition (_партиция_) - какой-то устойчивый тег _мессаги_, который позволяет _консумеру_ определенным образом выбрать из всего потока данных какую-то часть. Тоже пришлепывается к мессаге.
 * Ordering 2) (_порядок_) - мифические _гарантии_ упорядочивания _мессаг_.
 * Ack (_акк_) - подтверждение второй стороны о качественно проделанной работе.
 * Async (_асинхронно_) - значит не ждать у моря погоды, а _продуктивно_ заниматься своими делами, пока не придет _акк_ на прошлую операцию. Предполагается, что, хотя бы, _акки_ _упорядочены_.
 * Streaming (_стриминг_) - протокол обмена данными, когда кто-то неудержимо пихает в дырку данные, а другой их неудержимо принимает.
 * Back-pressure (_придерживание_) - это когда во время _стриминга_ уже больше не лезет и принимающая сторона ~~толкает ладонью в грудь~~ посылает специальное сообщение.
 * To be defined: HTTP, API, DC, cross and all that shit.
 
 
 0) Надеюсь, что искушенный читатель из списка терминологии сможет сообразить что к чему._
 1) Пока не очевидно, может ли машина менять _соурс_, и что нам это может дать - кроме геморроя, но (потенциально) упрощения работы продюсера._
 2) Так-как _продюсеры_ с трудом могут что-либо упорядочить между собой, то совершенно непонятно, зачем какой-либо порядок консумерам _логов_. Но если в пределах одного _соурса_ можно будет получить _гарантии_ _порядка_ на халяву, то почему бы и нет.
 
  
 ### Мотивация: ###
 
 * Перекладывание из пустого в порожнее - основа современной технологии, позволяющая передать работу по обработке данных множества (и без того занятых) машин другим машинам.
 * На данный момент, для большого потока данных (Message Size ~10k, RPS * Message size > 10Gb) эта задача  решена крайней хуево.
 * Почему хуево? Я не знаю, но Amazon SQS выдает 300RPS, Redis и Memcached для этого не используют с трудом, RabbitMQ затыкается на нескольких тысячх RPS, а Apache Kafka eventually разваливается.
 * Почему так? Потому что  решают задачу очереди сообщений в общем виде. Типа, у вас может миллион продюсеров или миллион консумеров, или можно слать сообщения по-одному, или у вас может быть большой перекос по ключам партицирования, или просто сделано херово и т.д.
 * А как же быть? Зажать клиентам и железу яйца в тиски!
  
 
 ### Fast and !@#$%^: ###
 
 А что нужно, чтобы... Нужно архитектурировать:
 
 * Быстро слать мессаги? Не надо дожидаться подтверждения. Для этого любой хуятор должен пихать не задумываясь о последствиях, и думать только о себе. А любые _акки_ получать _асинхронно_. _Стримить_, короче говоря.
 * Быстро работать с диском? Не смешивать операции записи и чтения, потому что это _сики_. При современной скорости чтения шпинделя, один жёсткий сик эквивалентен чтению 16MB. *) Не все сики одинаково вредны.
 * Быстро писать на диск? Отстреливать плохие диски. В общем случае - ничего. Быстро гадить на диск можно почти с любым паттерном использования. Главное - сделать предварительный truncate и отключить atime. Вопрос в том, а что потом делать читателям...
 * Надежно писать на диск? Звать fsync, ёба.
 * Надежно писать данные? Реплицировать.
 * Быстро реплицировать? Хранить копии, без всяких этих хафманов. Реплицировать по цепочке.
 * Быстро читать со шпинделя? Читать линейно и что-то одно (и писать по-возможности тоже). 
 * Быстро читать данные? Не забывать, что у нас есть реплики - и балансировать между ними.
 * Загрузить _сеть_? В общем случае, надо _стримить_.
 * Быстро догонять? Скорость записи на ноду _не должна_ превышать (например) 1/4 скорости чтения с этой ноды.
 * Обслужить много консумеров? Нужно много _партиций_.
 * Писать много партиций? Нужно много _нод_.
 * Распределить нагрузку продюсеров? Детерминированно побить нагрузку продюсера на _две_ части и писать на _две_ ноды. __Нельзя__ писать данные продюсера на недетримированное количество нод, это может привести к n^2 деградации консумеров.
 
 
 
