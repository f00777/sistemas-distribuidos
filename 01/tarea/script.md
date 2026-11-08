# Guion Definitivo de Presentación: Reddit Tech Stack & Distributed Systems Architecture

**Curso:** Sistemas Distribuidos  
**Unidad Académica:** Escuela de Ingeniería Informática y Telecomunicaciones — Universidad Diego Portales (UDP)  
**Integrantes:** Felipe Maturana, Alejandro Gaona  
**Tiempo Total Asignado:** Máximo 15 minutos de presentación + 5 minutos de ronda de preguntas  
**Tiempo Objetivo del Guion:** 13:00 a 13:30 minutos (margen óptimo de seguridad)  
**Distribución de Roles:**
* **Expositor 1 (Felipe Maturana):** Diapositivas 1 a 5 (~6:30 minutos)
* **Expositor 2 (Alejandro Gaona):** Diapositivas 6 a 11 (~6:45 minutos)  
*(Ambos expositores superan ampliamente los 3 minutos mínimos requeridos por persona).*

---

## ESTRUCTURA DEL DOCUMENTO
1. [Sección 1: Guion en Español (Presentación Regular)](#sección-1-guion-en-español)
2. [Sección 2: English Script (Bonus Track +1.0)](#sección-2-english-script)
3. [Sección 3: Estrategia y Preparación para la Ronda de Preguntas (5 Minutos)](#sección-3-estrategia-y-preparación-para-la-ronda-de-preguntas-5-minutos)

---

# SECCIÓN 1: GUION EN ESPAÑOL

---

### Diapositiva 1: Reddit Tech Stack (Portada)
* **Tiempo asignado:** 0:00 - 0:45 (45 segundos)
* **Expositor:** Felipe Maturana
* **Objetivo de Rúbrica:** Formalidad, presentación del equipo y encuadre del caso de estudio.

> *"Muy buenos días profesor y compañeros. Junto a mi compañero Alejandro Gaona, presentaremos hoy nuestro caso de estudio para el curso de Sistemas Distribuidos: la arquitectura y el stack tecnológico del backend de Reddit.*
> 
> *A lo largo de los próximos 13 minutos, analizaremos en profundidad cómo una de las plataformas con mayor volumen de tráfico del mundo evolucionó desde un monolito tradicional hacia una arquitectura distribuida altamente escalable y desacoplada. Expondremos sus componentes de backend, mecanismos de comunicación síncrona y asíncrona, tolerancia a fallos, estrategias de persistencia políglota y un análisis crítico de su disponibilidad histórica con incidentes reales a gran escala."*

---

### Diapositiva 2: What is Reddit? & Motivation of Choice
* **Tiempo asignado:** 0:45 - 2:15 (1:30 minutos)
* **Expositor:** Felipe Maturana
* **Objetivo de Rúbrica:** Cumplir 100% con *Contenido - Indicador 1* (Contextualización, funcionalidades y justificación explícita de la elección del sistema).

> *"Para iniciar el contexto, ¿qué es Reddit? Fundada en 2005, es una plataforma de agregación de noticias sociales, calificación de contenidos y debate comunitario. Hoy en día se ubica constantemente entre los diez sitios web más visitados del planeta. Su estructura se organiza en más de 100.000 comunidades activas llamadas 'subreddits', donde los usuarios publican enlaces, texto, imágenes y video, interactuando mediante comentarios en hilos anidados y un sistema de votación positiva y negativa —upvotes y downvotes—.*
> 
> *Ahora bien, ¿cuál fue nuestra motivación para elegir Reddit como caso de estudio? Esta elección responde a dos justificaciones técnicas indispensables para el ramo:*
> 1. *En primer lugar, a diferencia de redes sociales basadas en lecturas estáticas, Reddit es esencialmente un **motor masivo distribuido de votación y conversación en tiempo real**. El desafío de calcular clasificaciones dinámicas con millones de votos concurrentes y renderizar árboles de comentarios de profundidad arbitraria con baja latencia constituye un problema paradigmático de consistencia y concurrencia distribuida.*
> 2. *En segundo lugar, Reddit representa un caso de estudio real de **evolución arquitectónica**: documenta la compleja transición técnica desde un monolito frágil en Python ('r2') hacia microservicios de alto rendimiento en Go, persistencia políglota y flujos reactivos basados en Change Data Capture (CDC), logrando modernizarse sin interrumpir la operación global."*

---

### Diapositiva 3: Reddit at Scale & Historical Availability
* **Tiempo asignado:** 2:15 - 4:00 (1:45 minutos)
* **Expositor:** Felipe Maturana
* **Objetivo de Rúbrica:** Cumplir con *Contenido - Indicador 3* y *Dominio - Indicador 2* (Métricas de tráfico masivo, disponibilidad histórica, SLAs y análisis de incidentes reales).

> *"Para comprender las exigencias impuestas a este backend, revisemos las cifras que definen su escala:*
> *Reddit atiende diariamente a más de **116 millones de usuarios activos (DAUs)** y **443 millones semanales**, sumando más de **2.200 millones de visitas mensuales**, donde el 78% proviene de clientes móviles y un 56.7% corresponde a navegación anónima sin sesión.*
> *En términos de carga de datos, la plataforma procesa **3.140 millones de comentarios e interacciones anuales**, 1.400 millones de reproducciones de video al mes y más de 70 millones de búsquedas por semana sobre más de 100.000 subreddits.*
> 
> *Frente a semejante volumen, ¿cómo se ha comportado su **disponibilidad histórica**?*
> * *Actualmente, los registros oficiales de **redditstatus.com** reflejan una disponibilidad histórica cercana al **99.9%** (tres nueves) en sus servicios centrales. Sin embargo, alcanzar este nivel requirió superar una larga historia de inestabilidad: en sus primeros años, Reddit era célebre por la recurrente pantalla de error del extraterrestre Snoo con ojos de cruz que decía 'You Broke Reddit', provocada por la saturación de lecturas y bloqueos en su base de datos relacional durante picos virales.*
> * *Incluso en su arquitectura moderna basada en la nube, los fallos a gran escala han dejado valiosas lecciones de sistemas distribuidos. El incidente más relevante fue el **Pi-Day Outage del 14 de marzo de 2023**, una caída total de **314 minutos** (más de 5 horas). El desencadenante fue una actualización del clúster principal de Kubernetes de la versión 1.23 a la 1.24. En esta versión, Kubernetes eliminó definitivamente la etiqueta heredada 'node-role.kubernetes.io/master'. Debido a que los reflectores de rutas BGP de Calico estaban configurados para ejecutarse exclusivamente en nodos con esa etiqueta, el selector no encontró ningún nodo disponible. Como consecuencia, las rutas de red internas cayeron, el servicio CoreDNS del clúster colapsó y ningún pod pudo comunicarse entre sí.*
> * *Otro fallo de gran impacto fue la caída global de **Fastly CDN el 8 de junio de 2021** por aproximadamente 50 minutos, donde un bug latente en los proxies de borde activado por un cambio de configuración provocó errores 503 en el 85% de la red de Fastly, dejando fuera de línea a Reddit junto a Twitch y GitHub. Esto impulsó a Reddit a implementar estrategias de failover multi-CDN.*
> 
> *Estos eventos evidencian que en sistemas masivos, la disponibilidad no depende solo de la base de datos, sino críticamente del plano de control del orquestador y de la resiliencia en la capa perimetral."*

---

### Diapositiva 4: Why a Distributed System?
* **Tiempo asignado:** 4:00 - 5:15 (1:15 minutos)
* **Expositor:** Felipe Maturana
* **Objetivo de Rúbrica:** Fundamentación teórica formal (Tolerancia a fallos, escalabilidad, eficiencia y tipos de transparencia de Tanenbaum).

> *"¿Por qué Reddit requiere obligatoriamente una arquitectura distribuida? Porque debe satisfacer de forma simultánea tres objetivos fundamentales de la disciplina:*
> * * **Tolerancia a fallos:** Garantizar la ausencia de puntos únicos de falla (Single Points of Failure). Si un servidor, una zona de disponibilidad o un contenedor colapsa, el sistema continúa operando mediante replicación de datos activa y auto-recuperación.*
> * * **Escalabilidad:** Permitir el crecimiento horizontal independiente. Ante picos repentinos de tráfico —como sesiones masivas de preguntas 'Ask Me Anything' (AMA) o acontecimientos mundiales de última hora—, los microservicios afectados deben escalar en réplicas sin necesidad de sobredimensionar el resto de la plataforma.*
> * * **Eficiencia:** Minimizar la latencia y maximizar el rendimiento. Esto se logra derivando el 95% de las lecturas a memorias intermedias y desacoplando las escrituras hacia colas asíncronas.*
> 
> *En términos de la teoría de Sistemas Distribuidos, Reddit busca la **transparencia de distribución**: que a los ojos del usuario final o de la aplicación cliente, la plataforma se comporte como si fuera un único sistema centralizado coherente, ocultando la complejidad de miles de servidores interconectados."*

---

### Diapositiva 5: Reddit Core Architecture (Diagrama Macro)
* **Tiempo asignado:** 5:15 - 6:30 (1:15 minutos)
* **Expositor:** Felipe Maturana
* **Objetivo de Rúbrica:** Identificación clara de la arquitectura global y transición entre expositores.

> *"En esta diapositiva podemos observar el diagrama estructural de la arquitectura backend central de Reddit, documentado por ByteByteGo.*
> *Distinguimos claramente las capas del sistema:*
> 1. *En la cúspide, el tráfico entrante de navegadores y aplicaciones móviles atraviesa la red perimetral (**Fastly CDN**) y balanceadores de carga basados en Envoy.*
> 2. *La capa de entrada lógica es un API Gateway federado en **GraphQL**, que centraliza el enrutamiento hacia la lógica de negocio.*
> 3. *La capa de aplicación, dividida entre los modernos **microservicios en Go** y los componentes residuales del monolito original.*
> 4. *La capa de almacenamiento y caché políglota, combinando **PostgreSQL**, **Apache Cassandra**, **Redis** y **Memcached**.*
> 5. *Y la infraestructura base con **AWS EKS** y el bus de mensajería compuesto por **RabbitMQ** y **Apache Kafka con Debezium**.*
> 
> *A continuación, mi compañero Alejandro Gaona explicará cómo cada uno de estos bloques resuelve las propiedades distribuidas y los protocolos de comunicación subyacentes."*

---

### Diapositiva 6: Architecture: Solving the Three Goals
* **Tiempo asignado:** 6:30 - 8:00 (1:30 minutos)
* **Expositor:** Alejandro Gaona
* **Objetivo de Rúbrica:** Cumplir con *Contenido - Indicador 2 y 6* (Funcionalidad de cada componente, solución de las tres metas y transparencias de distribución).

> *"Muchas gracias Felipe. Entrando en el análisis funcional de los componentes y cómo resuelven las tres metas distribuidas:*
> * *El **GraphQL Gateway** implementa la **transparencia de acceso y de ubicación**: ofrece un punto de entrada unificado que oculta al cliente en qué máquinas o lenguajes reside cada servicio específico.*
> * *Los **Microservicios en Go** aportan **escalabilidad modular** y **aislamiento de fallos**: si el servicio de analítica o de recomendación de feeds sufre degradación, la funcionalidad esencial de lectura y escritura de comentarios continúa intacta.*
> * *En la base de datos relacional, **PostgreSQL (ThingDB)** utiliza réplicas de solo lectura bajo **transparencia de replicación**, absorbiendo la alta demanda de lectura sin comprometer el nodo primario de escritura.*
> * ***Memcached y Redis** representan el pilar de eficiencia: operan en conjunto absorbiendo más del **95% de las lecturas** en memoria RAM antes de que toquen el almacenamiento secundario.*
> * ***Kafka y Debezium (CDC)** resuelven la **consistencia eventual** de la plataforma, desacoplando a los servicios productores de los consumidores mediante flujos de eventos continuos.*
> * ***RabbitMQ** gestiona colas de tareas asíncronas para absorber ráfagas masivas de escritura.*
> * ***Apache Cassandra** aporta alta disponibilidad y tolerancia a particiones de red entre múltiples centros de datos para datos de series de tiempo y votos.*
> * *Y finalmente, **AWS con Kubernetes (EKS)** materializa la **transparencia ante fallos**, monitoreando la salud de los pods y reiniciando automáticamente cualquier instancia averiada en milisegundos."*

---

### Diapositiva 7: Languages & API Layer (Comunicación Síncrona)
* **Tiempo asignado:** 8:00 - 9:30 (1:30 minutos)
* **Expositor:** Alejandro Gaona
* **Objetivo de Rúbrica:** Cumplir con el requerimiento de *Comunicación* (patrones síncronos) y *Dominio de Tecnologías* (Python r2 vs. Go microservices, GraphQL Federation DGS).

> *"Profundicemos ahora en la capa de lenguajes y en el modelo de **comunicación síncrona** del sistema:*
> * *En sus inicios (2005), Reddit fue concebido como un monolito en Python conocido como **'r2'**. Si bien Python permitió un rápido desarrollo inicial, su ejecución interpretada y el Global Interpreter Lock (GIL) impusieron un cuello de botella infranqueable para tareas concurrentes intensivas en CPU, tales como la serialización de árboles de comentarios gigantescos.*
> * *Para superar esto, Reddit emprendió la reescritura de sus servicios críticos en **Go (Golang)**. Gracias al modelo de concurrencia basado en **Goroutines** y canales, Go permite gestionar decenas de miles de hilos ligeros por proceso con un uso mínimo de memoria, reduciendo drásticamente la latencia de las APIs.*
> * *Respecto a la **comunicación síncrona cliente-servidor e inter-servicios**, Reddit descartó el modelo REST tradicional e implementó **GraphQL Federation**. Los clientes web y móviles se comunican mediante HTTP/JSON con un Gateway central. Detrás de este gateway, el esquema global de datos se particiona en subgrafos administrados independientemente por cada equipo de ingeniería, denominados **Domain Graph Services (DGS)**.*
> * *Esto otorga una clara **transparencia de ubicación**: el cliente solicita en una sola llamada síncrona datos de un post, el perfil del autor y los comentarios. El gateway descompone la consulta y realiza llamadas internas concurrentes de baja latencia mediante RPC/HTTP a los microservicios de Go pertinentes, integrando la respuesta final sin acoplamiento entre el frontend y la red interna."*

---

### Diapositiva 8: Databases & Caching (Persistencia Políglota y Teorema CAP)
* **Tiempo asignado:** 9:30 - 11:00 (1:30 minutos)
* **Expositor:** Alejandro Gaona
* **Objetivo de Rúbrica:** Demostrar dominio en bases de datos distribuidas, compensaciones del Teorema CAP y estrategias de caching.

> *"En la capa de almacenamiento, Reddit aplica el paradigma de **persistencia políglota**, seleccionando diferentes motores según los compromisos del **Teorema CAP**:*
> * *Para los datos relacionales primarios (cuentas de usuario, posts y subreddits), utilizan **PostgreSQL** bajo un patrón histórico propio llamado **ThingDB**. ThingDB utiliza un esquema de **Entidad-Atributo-Valor (EAV)** con solo dos tablas maestras: 'data' (atributos del elemento o 'thing') y 'links' (relaciones entre ellos). Este diseño permitió una flexibilidad total similar a NoSQL, evitando bloqueos por migraciones de esquema en bases de datos con miles de millones de filas, delegando la lógica de joins a la capa de aplicación.*
> * *Para el conteo de votos y datos de series temporales de alta escritura, utilizan **Apache Cassandra**. Cassandra es una base de datos distribuida multi-centro de datos sin nodo maestro. Frente al teorema CAP, Reddit prioriza la **Disponibilidad y la Tolerancia a Particiones (AP)**. Mediante su modelo de **Consistencia Configurable (Tunable Consistency)**, Reddit utiliza niveles de lectura y escritura como 'LOCAL_QUORUM', aceptando una **consistencia eventual** controlada: un usuario siempre podrá emitir su voto sin rechazo, aun si la replicación inter-datacenter tarda cientos de milisegundos en converger.*
> * *En la capa de caché en memoria, implementan dos motores especializados: **Memcached**, utilizado como almacén clave-valor simple para fragmentos de renderizado HTML y tokens de sesión; y **Redis**, utilizado como almacén de datos estructurados para leaderboards en tiempo real, candados distribuidos (locks) y limitadores de tasa de peticiones (rate limiting)."*

---

### Diapositiva 9: Async Messaging & Infrastructure (Comunicación Asíncrona)
* **Tiempo asignado:** 11:00 - 12:30 (1:30 minutos)
* **Expositor:** Alejandro Gaona
* **Objetivo de Rúbrica:** Cumplir con el requerimiento de *Comunicación Asíncrona*, tolerancia a fallos en orquestación y patrones de migración con cero downtime.

> *"En cuanto a la **comunicación asíncrona y la infraestructura**, Reddit implementa dos patrones de mensajería altamente especializados:*
> 1. *El primer patrón es la **comunicación basada en colas de mensajes con RabbitMQ**, utilizando el protocolo estándar **AMQP**. Se emplea para tareas de ejecución en segundo plano desacopladas ('fire-and-forget'): cuando un usuario vota, se envía un mensaje a la cola y el servidor responde inmediatamente al cliente, permitiendo que grupos de workers actualicen contadores y disparen notificaciones sin bloquear el ciclo de petición HTTP.*
> 2. *El segundo patrón es el **streaming distribuido de eventos con Apache Kafka y Debezium**, aplicando **Change Data Capture (CDC)**. Debezium lee en tiempo real el registro de transacciones WAL (Write-Ahead Log) de PostgreSQL y publica cada inserción o modificación en tópicos de Kafka sobre protocolo binario TCP. Servicios downstream suscritos a Kafka procesan estos eventos de forma reactiva para actualizar los índices de búsqueda en Elasticsearch, activar sistemas de detección de spam y alimentar pipelines de analítica en tiempo real.*
> 
> *A nivel de infraestructura, toda la flota de microservicios se despliega en **AWS con Kubernetes (EKS)** en múltiples zonas de disponibilidad, ofreciendo **transparencia de fallos** mediante autoescalado de pods y controladores de replicación.*
> *Asimismo, para migrar servicios y almacenes de datos sin interrumpir el sitio, Reddit diseñó el patrón **'Sister Datastore'**: el tráfico de escritura se envía en paralelo al almacén antiguo y al nuevo; un validador asíncrono compara las lecturas verificando consistencia al 100% bajo tráfico productivo, y solo tras la validación se realiza la conmutación final sin un solo segundo de inactividad."*

---

### Diapositivas 10 y 11: Bibliography I & II (Cierre y Conclusiones)
* **Tiempo asignado:** 12:30 - 13:15 (45 segundos)
* **Expositor:** Alejandro Gaona
* **Objetivo de Rúbrica:** Referencias verificables, síntesis técnica y apertura a la ronda de preguntas.

> *"Para finalizar, en las diapositivas 10 y 11 listamos las fuentes de información utilizadas en esta investigación, destacando los análisis arquitectónicos de ByteByteGo, métricas de tráfico de Interteam, la monitorización oficial de Reddit Status y los postmortems de ingeniería de r/RedditEng y Fastly.*
> 
> *En síntesis, Reddit demuestra cómo un sistema a escala planetaria logra sobrevivir y escalar combinando microservicios concurrentes, persistencia políglota guiada por el teorema CAP, desacoplamiento asíncrono por eventos y una rigurosa búsqueda de las transparencias de distribución.*
> 
> *Muchas gracias por su atención. Damos paso a la ronda de preguntas."*

---
---

# SECCIÓN 2: ENGLISH SCRIPT

> [!NOTE]
> Presenting the oral defense in English automatically activates the **Bonus Criterion** in the rubric, awarding **+1.0 extra point** to mathematically guarantee a 7.0 grade.

---

### Slide 1: Reddit Tech Stack (Title Slide)
* **Time Target:** 0:00 - 0:45 (45 seconds)
* **Speaker:** Felipe Maturana
* **Rubric Target:** Formality, team presentation, and study framing.

> *"Good morning professor and fellow classmates. Together with my partner Alejandro Gaona, today we present our case study for the Distributed Systems course: the architecture and backend technology stack of Reddit.*
> 
> *Over the next thirteen minutes, we will examine in depth how one of the highest-traffic platforms on the internet evolved from a traditional monolithic web application into a highly resilient, scalable, and decoupled distributed system. We will cover its core components, synchronous and asynchronous communication protocols, fault tolerance, polyglot persistence strategies, and a critical analysis of its historical availability through major real-world incidents."*

---

### Slide 2: What is Reddit? & Motivation of Choice
* **Time Target:** 0:45 - 2:15 (1:30 minutes)
* **Speaker:** Felipe Maturana
* **Rubric Target:** 100% fulfillment of *Content - Indicator 1* (Context, features, and explicit technical justification of choice).

> *"To establish context, what is Reddit? Founded in 2005, Reddit is a social news aggregation, content rating, and discussion platform. Today, it ranks consistently among the top ten most visited websites worldwide. It is structured into more than 100,000 active communities called 'subreddits', where users submit links, text posts, images, and video, engaging in threaded discussions and community curation via upvotes and downvotes.*
> 
> *Now, what was our motivation for choosing Reddit over other distributed architectures? This choice rests upon two core technical pillars:*
> 1. *First, unlike static read-mostly social networks, Reddit is fundamentally a **massive distributed real-time voting and conversation engine**. Managing eventual consistency while aggregating millions of concurrent votes and dynamically rendering nested comment trees of arbitrary depth with millisecond latency is a classic and complex distributed systems problem.*
> 2. *Second, Reddit represents an ideal paradigm of **architectural evolution**: it documents how a rapidly scaling company successfully migrated from a fragile Python monolith ('r2') to high-performance Go microservices, polyglot persistence, and event-driven Change Data Capture (CDC), all while sustaining uninterrupted global traffic."*

---

### Slide 3: Reddit at Scale & Historical Availability
* **Time Target:** 2:15 - 4:00 (1:45 minutes)
* **Speaker:** Felipe Maturana
* **Rubric Target:** *Content - Indicator 3* and *Mastery - Indicator 2* (Traffic metrics, historical uptime, SLAs, and deep analysis of production outages).

> *"To understand the operational load placed on this backend, let us review the platform's scale metrics:*
> *Reddit serves more than **116 million Daily Active Users (DAUs)** and **443 million Weekly Active Users**, recording over **2.2 billion monthly visits**, with 78% originating from mobile devices and 56.7% browsing anonymously.*
> *In terms of write and read throughput, the platform processes **3.14 billion comments and interactions annually**, 1.4 billion monthly native video views, and over 70 million weekly search queries across more than 100,000 subreddits.*
> 
> *Under this volume, how has its **historical availability** held up?*
> * *Today, official logs from **redditstatus.com** indicate a historical availability close to **99.9%** (three nines) across core services. However, achieving this resilience required overcoming years of stability struggles. In its early days, Reddit was infamous for the 'You Broke Reddit' error screen featuring Snoo the alien with crossed-out eyes, caused by relational database lock contention during viral traffic surges.*
> * *Even within its modern cloud infrastructure, major disruptions highlight the inherent challenges of distributed systems. The most critical event was the **Pi-Day Outage on March 14, 2023**, a total sitewide blackout lasting **314 minutes** (over 5 hours). The trigger was a routine Kubernetes upgrade from version 1.23 to 1.24 on their primary 1,000-node cluster. In Kubernetes 1.24, the legacy node label 'node-role.kubernetes.io/master' was permanently deprecated and removed. Because Reddit's Calico BGP route reflectors were configured with a node selector targeting that exact label, the selector matched zero nodes. Consequently, in-cluster BGP routes dropped, CoreDNS collapsed, and pods lost all inter-service communication.*
> * *Another prominent failure was the **Fastly CDN outage on June 8, 2021**, lasting approximately 50 minutes. A dormant edge proxy bug triggered by a valid customer configuration change caused 85% of Fastly's global Points of Presence to return 503 errors, taking Reddit offline alongside Twitch and GitHub. This prompted Reddit to adopt multi-CDN redundancy strategies.*
> 
> *These incidents prove that in planetary-scale systems, availability depends not merely on database capacity, but critically on orchestrator control planes and edge-layer resilience."*

---

### Slide 4: Why a Distributed System?
* **Time Target:** 4:00 - 5:15 (1:15 minutes)
* **Speaker:** Felipe Maturana
* **Rubric Target:** Formal theoretical foundation (Fault tolerance, scalability, efficiency, and Tanenbaum distribution transparencies).

> *"Why must Reddit operate as a distributed system? Because it must simultaneously fulfill three core goals of distributed computing:*
> * * **Fault Tolerance:** Eliminating Single Points of Failure. If a physical node, an availability zone, or an individual microservice crashes, the system continues serving users through active data replication and automated container rescheduling.*
> * * **Scalability:** Enabling elastic horizontal scaling. During severe traffic spikes —such as global sporting events or celebrity 'Ask Me Anything' (AMA) sessions—, targeted microservices can scale horizontally without requiring costly overprovisioning of the entire stack.*
> * * **Efficiency:** Maximizing throughput while maintaining sub-second latencies. This is achieved by shielding databases behind a multi-tier in-memory cache hierarchy absorbing 95% of reads, and offloading heavy writes to asynchronous brokers.*
> 
> *From a theoretical standpoint, Reddit actively pursues **distribution transparency**: ensuring that to the end-user and client applications, the entire distributed cluster appears and behaves as a single, coherent, centralized system."*

---

### Slide 5: Reddit Core Architecture (Macro Diagram)
* **Time Target:** 5:15 - 6:30 (1:15 minutes)
* **Speaker:** Felipe Maturana
* **Rubric Target:** High-level architectural walkthrough and structured speaker handoff.

> *"On this slide, we observe the macro architectural diagram of Reddit's core backend, documented by ByteByteGo.*
> *We can distinguish five functional layers:*
> 1. *At the perimeter, incoming web and mobile traffic enters via the **Fastly CDN** and Envoy-based load balancers.*
> 2. *The logical entry point is a federated **GraphQL API Gateway**, unifying request routing across backend domains.*
> 3. *The compute layer, composed of modern **Go microservices** working in tandem with transitioning legacy monolithic modules.*
> 4. *The polyglot persistence and caching tier, combining **PostgreSQL**, **Apache Cassandra**, **Redis**, and **Memcached**.*
> 5. *And the foundational infrastructure layer, powered by **AWS EKS** and an asynchronous messaging spine featuring **RabbitMQ** and **Apache Kafka with Debezium**.*
> 
> *Now, my partner Alejandro Gaona will explain how these components implement distributed properties and manage communication protocols."*

---

### Slide 6: Architecture: Solving the Three Goals
* **Time Target:** 6:30 - 8:00 (1:30 minutes)
* **Speaker:** Alejandro Gaona
* **Rubric Target:** Component functionality, mapping to distributed goals, and distribution transparencies.

> *"Thank you Felipe. Moving into the functional breakdown of how each component solves our three core distributed goals:*
> * *The **GraphQL Gateway** enforces **access and location transparency**, presenting a unified endpoint that shields clients from knowing which physical servers or backend languages host specific services.*
> * *The **Go Microservices** provide **modular scalability** and **fault isolation**: a sudden load spike or failure in feed generation or analytics never disrupts the core capability of viewing and publishing content.*
> * *In the relational layer, **PostgreSQL (ThingDB)** employs read replicas under **replication transparency**, handling heavy read traffic without starving primary write transactions.*
> * ***Memcached and Redis** serve as the efficiency engine, working in tandem to absorb over **95% of read operations** in RAM before queries ever reach persistent disk storage.*
> * ***Kafka and Debezium (CDC)** establish **eventual consistency**, completely decoupling event producers from asynchronous downstream consumers.*
> * ***RabbitMQ** absorbs write bursts and scheduled jobs through asynchronous queuing.*
> * ***Apache Cassandra** provides masterless, multi-datacenter fault tolerance and partition tolerance for high-volume time-series data.*
> * *And finally, **AWS with Kubernetes (EKS)** delivers **failure transparency**, continuously evaluating pod health checks and rescheduling failed containers in milliseconds."*

---

### Slide 7: Languages & API Layer (Synchronous Communication)
* **Time Target:** 8:00 - 9:30 (1:30 minutes)
* **Speaker:** Alejandro Gaona
* **Rubric Target:** Synchronous communication patterns, RPC/HTTP protocols, and language trade-offs (Python r2 vs. Go microservices, GraphQL Federation DGS).

> *"Let us now analyze the programming language layer and the **synchronous communication** architecture:*
> * *Historically, Reddit began as a Python monolith called **'r2'**. While Python allowed rapid feature velocity, its interpreted nature and the Global Interpreter Lock (GIL) created severe CPU bottlenecks when serializing complex, deeply nested comment trees under global load.*
> * *To resolve this, Reddit rewrote latency-critical execution paths in **Go (Golang)**. Leveraging lightweight **Goroutines** and channels, Go processes tens of thousands of concurrent network connections per instance with minimal memory overhead, cutting API latencies dramatically.*
> * *Regarding **synchronous client-to-server and inter-service communication**, Reddit moved away from REST and adopted **GraphQL Federation**. Web and mobile applications query a single GraphQL gateway over HTTP/JSON. Behind this gateway, the overall schema is split into domain-driven subgraphs called **Domain Graph Services (DGS)**.*
> * *This provides strict **location transparency**: the client requests a post, its author details, and comments in one synchronous call. The gateway decomposes the query, issues concurrent, low-latency RPC/HTTP requests to the corresponding Go microservices, and aggregates the payload back to the client, fully decoupling the client from the underlying microservice topology."*

---

### Slide 8: Databases & Caching (Polyglot Persistence & CAP Theorem)
* **Time Target:** 9:30 - 11:00 (1:30 minutes)
* **Speaker:** Alejandro Gaona
* **Rubric Target:** Polyglot persistence, CAP Theorem trade-offs (AP vs. CP), EAV schemas, and caching tiers.

> *"In the storage tier, Reddit applies **polyglot persistence**, matching data engines to specific trade-offs dictated by the **CAP Theorem**:*
> * *For core relational metadata (user accounts, subreddits, and post entities), they utilize **PostgreSQL** under a proprietary schema design known as **ThingDB**. ThingDB implements an **Entity-Attribute-Value (EAV)** pattern using two primary tables: 'data' for attributes and 'links' for relationships. This provided NoSQL-like schema agility, avoiding costly table locking and schema migrations across billions of rows, while shifting relational join complexity into the application layer.*
> * *For high-throughput vote tracking and time-series history, Reddit utilizes **Apache Cassandra**. Cassandra is a distributed, masterless NoSQL database engineered for cross-datacenter replication. Under the CAP theorem, Reddit prioritizes **Availability and Partition Tolerance (AP)**. Using Cassandra's **Tunable Consistency**, read and write operations typically execute at 'LOCAL_QUORUM', embracing **eventual consistency**: an upvote is registered immediately without failure, even if inter-datacenter synchronization takes hundreds of milliseconds to settle.*
> * *For memory caching, Reddit deploys two specialized engines: **Memcached**, operating as an ultra-fast raw key-value store for rendered HTML fragments and session tokens; and **Redis**, utilized as a structured in-memory data store for live subreddit leaderboards, distributed locks, and rate-limiting counters."*

---

### Slide 9: Async Messaging & Infrastructure (Asynchronous Communication)
* **Time Target:** 11:00 - 12:30 (1:30 minutes)
* **Speaker:** Alejandro Gaona
* **Rubric Target:** Asynchronous communication protocols (AMQP, TCP Kafka), Change Data Capture (CDC), container orchestration, and zero-downtime migration patterns.

> *"Examining **asynchronous communication and infrastructure**, Reddit employs two specialized messaging patterns:*
> 1. *The first is **Message Queuing with RabbitMQ**, utilizing the standard **AMQP protocol**. This handles fire-and-forget background jobs: when a user votes or triggers an alert, the API enqueues a lightweight message and immediately returns HTTP 200 OK. Asynchronous worker pools consume the queue, processing database tallies without blocking user-facing request threads.*
> 2. *The second pattern is **Distributed Event Streaming with Apache Kafka and Debezium**, implementing **Change Data Capture (CDC)**. Debezium continuously monitors PostgreSQL's transaction Write-Ahead Log (WAL) and streams every insert, update, and delete into Kafka topics over its binary TCP protocol. Downstream consumer services read these topics in real time to update Elasticsearch indexes, feed spam detection models, and power analytics pipelines without putting any query load on production databases.*
> 
> *From an infrastructure perspective, microservices run on **AWS Kubernetes (EKS)** across multiple Availability Zones, enforcing **failure transparency** via automated pod replication and self-healing restarts.*
> *Furthermore, to safely migrate databases and microservices without maintenance windows, Reddit engineers designed the **'Sister Datastore'** pattern: writes are duplicated to both legacy and modern datastores; an asynchronous validator checks read parity under live production workloads; and once 100% data consistency is verified, cutover occurs with zero downtime."*

---

### Slides 10 & 11: Bibliography I & II (Conclusion & Q&A)
* **Time Target:** 12:30 - 13:15 (45 seconds)
* **Speaker:** Alejandro Gaona
* **Rubric Target:** Verifiable citations, technical closure, and handover to question panel.

> *"To conclude, slides 10 and 11 detail the official sources cited throughout our research, including ByteByteGo's architecture deep dives, Interteam traffic analyses, Reddit Status telemetry, and engineering postmortems published by r/RedditEng and Fastly.*
> 
> *In summary, Reddit exemplifies how a modern planetary platform sustains extreme scale through concurrent microservices, CAP-aware polyglot persistence, event-driven asynchronous decoupling, and strict adherence to distribution transparencies.*
> 
> *Thank you very much for your time and attention. We are now open to any questions from the professor and our classmates."*

---
---

# SECCIÓN 3: ESTRATEGIA Y PREPARACIÓN PARA LA RONDA DE PREGUNTAS (5 MINUTOS)

Para blindar la nota 7.0 en la dimensión de **Dominio - Indicador 5** (*"Dan respuesta a las preguntas que se le plantean de manera correcta y argumentada"*), a continuación se presentan las respuestas técnicas a las preguntas más probables del docente:

### Pregunta 1: *"¿Por qué son solo dos integrantes si el enunciado solicitaba grupos de 4?"*
* **Respuesta argumentada:**
  > *"Profesor, abordamos el caso en pareja asegurando que la complejidad arquitectónica analizada —que abarca desde la captura de transacciones CDC a bajo nivel en el WAL de PostgreSQL, hasta el análisis de ruteo BGP en Kubernetes y consistencia configurable en Cassandra— posee un nivel de detalle técnico equivalente o superior al de un grupo de cuatro integrantes. Además, ambos expusimos de forma equilibrada por más de seis minutos cada uno, cumpliendo con creces el requerimiento individual de tiempo de la pauta."*

### Pregunta 2: *"¿Cómo se aplica exactamente el Teorema CAP en Reddit?"*
* **Respuesta argumentada:**
  > *"Reddit no es puramente CP ni puramente AP, sino que adopta una persistencia políglota particionada:*
  > * Para la capa relacional en PostgreSQL (cuentas, seguridad, autenticación), priorizan la **Consistencia (CP)** para evitar estados incongruentes de identidad.*
  > * Para la capa de alta concurrencia en Apache Cassandra (conteo de votos y analítica en tiempo real), priorizan la **Disponibilidad y Tolerancia a Particiones (AP)** con consistencia eventual, utilizando quórums locales para que la acción de votar jamás sea rechazada por la red."*

### Pregunta 3: *"¿Qué ocurrió técnicamente en el Pi-Day Outage de 2023 y qué enseña sobre sistemas distribuidos?"*
* **Respuesta argumentada:**
  > *"Durante la actualización de Kubernetes 1.23 a 1.24 en su clúster de mil nodos, se eliminó la etiqueta 'node-role.kubernetes.io/master'. Los reflectores de ruta BGP de Calico dependían de un nodeSelector con esa etiqueta exacta para programarse en el plano de control. Al desaparecer la etiqueta, Calico no pudo agendarse, se rompieron las tablas de ruteo IP entre nodos y CoreDNS quedó aislado, provocando que ningún microservicio pudiera resolver nombres de red internos. Nos enseña que en sistemas distribuidos orquestados, el plano de control y las abstracciones de red perimetral representan dependencias críticas que deben contar con planes de rollback verificados en producción."*

### Pregunta 4: *"¿Cuál es la diferencia entre la comunicación síncrona y asíncrona en Reddit?"*
* **Respuesta argumentada:**
  > *"La comunicación **síncrona** se reserva para el flujo cliente-servidor a través de GraphQL Federation sobre HTTP/JSON, donde el usuario espera una respuesta inmediata de lectura. La comunicación **asíncrona** se divide en dos: colas de tareas con RabbitMQ sobre protocolo AMQP para operaciones de fondo ('fire-and-forget' como votos y notificaciones), y streaming continuo de eventos con Apache Kafka leyendo el WAL de PostgreSQL mediante Debezium (CDC) sobre TCP binario para propagar cambios de estado hacia Elasticsearch y analítica sin sobrecargar la base de datos."*
