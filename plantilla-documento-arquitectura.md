# Documento de Arquitectura del Sistema de Gestión de Órdenes y Entregas

## 1. Introducción  
Este documento describe la arquitectura inicial del sistema de gestión de órdenes y entregas, incluyendo requisitos funcionales, requisitos de calidad y restricciones que deben ser consideradas en el diseño del software.

**Equipo:** _Los Inhackeables_  
**Integrantes:** 
_Juan David Ascencio - 2359660-3743_ 
_Geraldine Florez - 2269476-3743_
_David Stiven Mujanajinsoy - 2376834-3743_
_Andrés Felipe Salcedo - 2359304-3743_
**Fecha:** _[01/03/2026]_  

---

## 2. Requisitos Funcionales  
Los requisitos funcionales se presentan en forma de **historias de usuario**, especificando los **criterios de aceptación**.

### **Historias de Usuario**

#### **US-01**:
##### Historia: 
_"Como cliente
Quiero consultar el catálogo de productos con precio y disponibilidad
Para decidir qué comprar con información confiable"_

##### Criterios de aceptación:

* Dado que existen productos registrados en el sistema
Cuando el cliente consulta el catálogo
Entonces el sistema debe mostrar nombre, categoría, precio y disponibilidad.

* Dado que un producto tiene stock mayor a 0
Cuando se muestra en el catálogo
Entonces debe aparecer como Disponible.

* Dado que un producto tiene stock igual a 0
Cuando se consulta el catálogo
Entonces debe aparecer como Agotado.

* Dado que el inventario cambia
Cuando el cliente actualiza la vista del catálogo
Entonces la información debe reflejar el nuevo stock.

* Dado que un usuario no autenticado accede al sistema
Cuando consulta el catálogo
Entonces debe poder visualizar los productos sin iniciar sesión.

##### Cumplimiento de INVEST:

Independent: No depende del módulo de pedidos ni entregas.

Negotiable: Se pueden agregar filtros o búsqueda sin afectar su núcleo.

Valuable: Permite al cliente tomar decisiones informadas.

Estimable: Alcance técnico claro (lectura de datos y exposición vía API).

Small: Se limita únicamente a consulta de productos.

Testable: La verificación se puede hacer siguiendo el flujo de cada criterio de aceptación.

---

#### **US-02**:

##### Historia:

*"Como cliente autenticado
Quiero crear un pedido seleccionando productos disponibles
Para comprar y recibir los artículos en mi domicilio"*

##### Criterios de aceptación:

* Dado que el cliente no ha iniciado sesión
  Cuando intenta confirmar un pedido
  Entonces el sistema debe solicitar autenticación.

* Dado que un producto tiene stock suficiente
  Cuando el cliente agrega una cantidad válida al carrito
  Entonces el sistema debe permitir la acción.

* Dado que el cliente intenta agregar una cantidad mayor al stock disponible
  Cuando confirma el pedido
  Entonces el sistema debe rechazar la operación e informar el motivo.

* Dado que el pedido es confirmado correctamente
  Cuando el sistema procesa la compra
  Entonces debe registrar el pedido, cambiar el estado a Aprobado, descontar el stock correspondiente y registrar el pago como Pagado.

##### Cumplimiento de INVEST:

Independent: Puede implementarse sin el módulo de notificaciones.

Negotiable: Reglas de validación o flujo pueden ajustarse.

Valuable: Es la funcionalidad central del negocio.

Estimable: Flujo definido con validaciones claras.

Small: Se enfoca únicamente en creación y confirmación del pedido.

Testable: Estados y validaciones son comprobables.

---

#### **US-03**:

##### Historia:

*"Como cliente
Quiero consultar el estado actualizado de mi pedido
Para saber qué está pasando con mi compra sin llamar"*

##### Criterios de aceptación:

* Dado que el cliente tiene un pedido registrado
  Cuando consulta el detalle del pedido
  Entonces debe visualizar el estado actual (Aprobado, Empacado, En ruta, Entregado, Cancelado).

* Dado que el pedido está en estado En ruta
  Cuando el repartidor actualiza su estado
  Entonces el cliente debe ver el cambio reflejado en máximo 30 segundos.

* Dado que ocurre una novedad en la entrega
  Cuando el repartidor la registra
  Entonces debe mostrarse en el historial del pedido.

* Dado que un usuario intenta consultar un pedido que no le pertenece
  Cuando realiza la solicitud
  Entonces el sistema debe denegar el acceso.

##### Cumplimiento de INVEST:

Independent: No depende del módulo de creación de productos.

Negotiable: Puede ampliarse con notificaciones push.

Valuable: Reduce carga al servicio al cliente.

Estimable: Estados definidos y actualizaciones periódicas claras.

Small: Se limita al seguimiento del pedido.

Testable: Actualización y control de acceso verificables.

---

#### **US-04**:

##### Historia:

*"Como administrador
Quiero gestionar el inventario de productos
Para asegurar que no se vendan artículos sin existencias"*

##### Criterios de aceptación:

* Dado que el administrador está autenticado con rol válido
  Cuando crea o actualiza un producto
  Entonces el sistema debe guardar los cambios correctamente.

* Dado que el administrador intenta asignar un stock negativo
  Cuando guarda el cambio
  Entonces el sistema debe rechazar la operación.

* Dado que un pedido es confirmado
  Cuando el sistema lo procesa
  Entonces el stock debe descontarse automáticamente.

* Dado que llega nueva mercancía
  Cuando el administrador actualiza el inventario
  Entonces el nuevo stock debe reflejarse inmediatamente en el catálogo.

* Dado que se realiza una modificación de inventario
  Cuando el cambio es confirmado
  Entonces debe quedar registrado en el historial del sistema.

##### Cumplimiento de INVEST:

Independent: No depende del módulo de entregas.

Negotiable: Reglas adicionales pueden incorporarse.

Valuable: Evita ventas inválidas y pérdidas.

Estimable: Funciones CRUD claramente definidas.

Small: Se enfoca en inventario exclusivamente.

Testable: Reglas de negocio verificables.

---

#### **US-05**:

##### Historia:

*"Como operador logístico
Quiero asignar pedidos a repartidores
Para garantizar una distribución ordenada y controlada"*

##### Criterios de aceptación:

* Dado que un pedido está en estado Empacado
  Cuando el operador lo asigna a un repartidor
  Entonces el sistema debe registrar la asignación con fecha y hora.

* Dado que un pedido ya está asignado
  Cuando se intenta asignar nuevamente
  Entonces el sistema debe impedir la duplicación.

* Dado que el pedido ha sido asignado
  Cuando el repartidor confirma la salida
  Entonces el estado debe cambiar a En ruta.

* Dado que ocurre una novedad
  Cuando el operador decide reasignar
  Entonces el sistema debe permitir la reasignación y dejar trazabilidad.

##### Cumplimiento de INVEST:

Independent: Puede desarrollarse sin afectar catálogo.

Negotiable: El algoritmo de asignación puede mejorarse.

Valuable: Organiza la operación logística.

Estimable: Flujo acotado y claro.

Small: Se centra solo en asignación.

Testable: Estados y reglas verificables.

---

#### **US-06**:

##### Historia:

*"Como repartidor
Quiero registrar novedades durante la entrega
Para dejar evidencia de situaciones que afecten el pedido"*

##### Criterios de aceptación:

* Dado que el repartidor está asignado a un pedido
  Cuando marca el pedido como Entregado
  Entonces el sistema debe actualizar el estado y registrar fecha y hora.

* Dado que ocurre una situación como Dirección errada o Cliente ausente
  Cuando el repartidor la registra
  Entonces el sistema debe almacenar la novedad con comentario obligatorio.

* Dado que se registra una novedad
  Cuando el cliente consulta su pedido
  Entonces debe ver el cambio reflejado en máximo 30 segundos.

* Dado que un repartidor no asignado intenta modificar el pedido
  Cuando realiza la acción
  Entonces el sistema debe denegar el acceso.

##### Cumplimiento de INVEST:

Independent: No depende del módulo de pagos.

Negotiable: Se pueden añadir nuevos tipos de novedad.

Valuable: Aporta trazabilidad y control operativo.

Estimable: Reglas y actores claramente definidos.

Small: Solo actualiza estados y registra eventos.

Testable: Control de acceso y actualización verificables.

---

>  **Instrucciones:**  
> - Completar al menos **6 historias de usuario**.  
> - Asegurar que cada historia tenga criterios de aceptación claros y verificables.  

---

## 3. Requisitos de Calidad  
Los requisitos de calidad se presentan en forma de **historias de calidad**, siguiendo la estructura de Len Bass.


### **Historias de Calidad**
| **ID**    | **Fuente**         | **Estímulo**         | **Artefactos**         | **Entorno**         | **Respuesta**         | **Medida de Respuesta**         |
| --------- | ------------------ | -------------------- | ---------------------- | ------------------- | --------------------- | ------------------------------- |
| **RQ-01** | Cliente | Consulta el catálogo de productos | API de catálogo / servicio de productos | Condiciones normales (carga promedio) | El sistema debe procesar la solicitud y retornar la lista de productos con su información | El tiempo de respuesta no debe superar **300 ms en el 95% de las solicitudes** |
| **RQ-02** | Cliente autenticado| Confirma un pedido | Servicio de pedidos |Condiciones normales | El sistema debe validar, registrar el pedido, actualizar el inventario y confirmar la transacción| El proceso completo debe ejecutarse en **menos de 500 ms en el 95% de los casos** |
| **RQ-03** | Usuario (cliente, administrador, repartidor) | Usuario (cliente, administrador, repartidor) | Plataforma completa (microservicios + API Gateway) | Operación continua (24/7) | El sistema debe estar disponible para atender solicitudes | Disponibilidad mínima de **99.5% mensual** |
| **RQ-04** | Incremento de usuarios concurrentes| Aumento sostenido de carga | Infraestructura en Kubernetes | Hora pico | El sistema debe escalar automáticamente agregando instancias sin interrumpir el servicio | Escalar hasta **3x la carga normal en menos de 2 minutos** sin degradar el tiempo de respuesta por encima de **500 ms** |
| **RQ-05** | Usuario malicioso o error de autenticación| Múltiples intentos fallidos de inicio de sesión | Servicio de autenticación (OAuth2 / JWT) | Operación normal| El sistema debe bloquear temporalmente el acceso y registrar el evento | Bloqueo tras **5 intentos fallidos consecutivos en menos de 2 minutos** y registro del evento en logs |
| **RQ-06** | Usuario no autorizado | Intenta acceder a recursos de otro usuario | API Gateway / servicios protegidos | Operación normal | El sistema debe rechazar la solicitud y registrar el intento | Respuesta con código **HTTP 403 en menos de 200 ms** y registro en logs de seguridad|
| **RQ-07** | Evento de negocio (pedido confirmado, actualización de estado)| Envío de mensajes a través del sistema de mensajería| Broker de mensajería (Kafka o RabbitMQ) | Alta concurrencia |El sistema debe garantizar que los mensajes se entreguen una sola vez sin pérdida ni duplicación| Tasa de pérdida de mensajes **0%** y duplicación **menor al 0.01%**|
| **RQ-08** | Sistema de gestión de pedidos (evento interno)| Se actualiza el estado de un pedido o se registra una novedad| Servicio de pedidos / servicio de notificaciones / frontend web | Operación normal | El sistema debe propagar el cambio de estado y reflejarlo en la interfaz del cliente | El estado debe visualizarse actualizado en la interfaz en un máximo de **30 segundos en el 100% de los casos** |
| **RQ-09** | Sistema de monitoreo | Ocurre un error o degradación del servicio | Sistema de monitoreo (Prometheus, Grafana, logs centralizados) | Operación normal o alta carga | El sistema debe registrar métricas, generar logs y activar alertas| La alerta debe generarse en **menos de 1 minuto** desde la ocurrencia del error|
| **RQ-10** | Equipo de desarrollo | Se requiere desplegar una nueva versión de un microservicio | Pipeline CI/CD (GitHub Actions + Kubernetes)| Producción |El sistema debe permitir despliegues sin afectar la disponibilidad | Despliegue con **cero downtime** o interrupciones menores a **30 segundos** |


---

## 4. Restricciones del Sistema  
Las restricciones establecen **limitaciones** en la arquitectura del sistema, ya sean tecnológicas, de negocio, regulatorias o de infraestructura.

### **Lista de Restricciones**
| **ID**    | **Restricción**                                                                                                                                                          | **Evidencia (cita del relato)**                                                                                     |
|-----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| **R-01** | El sistema debe implementarse con Spring Boot y arquitectura de microservicios.                                                                                          | Se debe implementar con Spring Boot y arquitectura por microservicios, con un API Gateway (Spring Cloud Gateway o Kong). |
| **R-02** | El sistema debe incluir un API Gateway para controlar acceso, seguridad y enrutamiento.                                                                                  | Se debe implementar con Spring Boot y arquitectura por microservicios, con un API Gateway (Spring Cloud Gateway o Kong). |
| **R-03** | La autenticación debe realizarse con OAuth2 y JWT.                                                                                                                       | Autenticación con OAuth2 y JWT.                                                                                   |
| **R-04** | La mensajería asíncrona debe implementarse con Kafka o RabbitMQ.                                                                                                         | Mensajería asíncrona con Kafka o RabbitMQ.                                                                        |
| **R-05** | El despliegue debe realizarse en Kubernetes con integración y despliegue continuo (CI/CD) utilizando GitHub Actions.                                                     | Despliegue en Kubernetes y CI/CD con GitHub Actions.                                                             |
| **R-06** | El monitoreo debe realizarse con Prometheus y Grafana, y los logs deben ser centralizados con alertas configuradas.                                                      | Monitoreo con Prometheus y Grafana, más logs centralizados y alertas.                                            |
| **R-07** | El sistema debe garantizar que no haya pérdida ni duplicación de mensajes en los flujos de mensajería/eventos.                                                           | En los flujos donde usemos mensajería/eventos, no podemos permitir pérdida de mensajes ni duplicaciones que dañen el negocio. |
| **R-08** | Los tiempos de respuesta de los servicios críticos no deben superar los 300 ms en condiciones de carga normal.                                                           | En horas pico, la atención debe sentirse ágil: tecnología nos puso como referencia que, si un servicio tarda más de 300 ms en responder, debemos revisar cuellos de botella. |
| **R-09** | El sistema debe ser capaz de escalar dinámicamente para manejar incrementos sostenidos de carga sin interrumpir a los usuarios.                                           | Y si la carga sube de forma sostenida, debe ser posible levantar más instancias sin interrumpir a los usuarios.   |
| **R-10** | Los accesos al sistema deben ser controlados por roles y se deben detectar intentos sospechosos de acceso.                                                               | Los accesos deben ser por roles y se debe detectar y reaccionar ante intentos sospechosos (por ejemplo, múltiples intentos fallidos de autenticación o uso de tokens inválidos). |
| **R-11** | El sistema debe registrar métricas, logs y alertas para garantizar trazabilidad y permitir la detección de errores o anomalías.                                           | El sistema debe dejar trazabilidad. Necesitamos métricas, logs y alertas.                                        |


> **Tipos de restricciones:**
> - **Tecnológicas:** Lenguajes, frameworks o herramientas que deben utilizarse.  
> - **De negocio:** Normativas o estándares de la empresa.  
> - **Regulatorias:** Cumplimiento de normativas legales o de seguridad.  
> - **De infraestructura:** Limitaciones en hardware, red o almacenamiento.



