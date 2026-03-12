# Entornos-7.5

# UML - DIAGRAMAS - Gestión del Sistema "GymMaster"


# Fase 1: Análisis de Requisitos
Diagrama de Casos de uso 

``` mermaid

graph LR
%% Definir Actores
Member((Member))
Administrator((Administrator))


%% Definir límite del Sistema y Acciones
subgraph "Gym Class Reservation"
CU1([Reserve Gym Class])
CU2([Login])
CU3([Wait List])
CU4([Register New Class])
CU5([Cancel Session])


%% Definir relaciones especiales (include y extend)

%% Relación include (obligatoria) entre Reservar Clase e Identificarse como Socio
CU1 -.->|&lt;&lt;include&gt;&gt;| CU2

%% Relación extend (opcional) entre Reservar Clase y Añadirse a la Lista de Espera
CU3 -.->|&lt;&lt;extend&gt;&gt;| CU1

end

%% Definir relaciones Actor/Casos de Uso
Member --- CU1

Administrator --- CU4
Administrator --- CU5

```


# Fase 2: Diseño de la Interacción

Diagrama de Secuencia

``` mermaid

sequenceDiagram
%% NumeradoAutomático para mejor seguimiento
autonumber
%% Definir Actores y Participantes
actor Member 
participant WebInterface
participant ReservationManager
participant Database

%% Definir Flujo
%% Primero, el Socio selecciona "Confirmar Reserva" en la Interfaz Web
Member->>WebInterface: selectConfirmReservation
activate WebInterface
%% Una vez seleccionado, se activa el "Gestor de Reservas"
WebInterface->>ReservationManager: openManager
activate ReservationManager
%% El Gestor de Reservas comprueba la Base de Datos
ReservationManager->>Database: getData()
activate Database
%% La Base de Datos devuelve si la reserva está disponible al Gestor
Database-->>ReservationManager: retrieveData
deactivate Database
%% El Gestor de Reservas trae de vuelta el mensaje a la Interfaz Web
ReservationManager-->>WebInterface: reservationData
deactivate ReservationManager

%% Finalmente, la Web devuelve al Socio la información de disponibilidad para su Reserva
%% Condiciones según el resultado de buscar en la Base de Datos
alt isAvailable
WebInterface -->>Member: reservationAvailable
else isNotAvailable
WebInterface-->>Member: reservationUnavailable
end
deactivate WebInterface

```


Diagrama de Comunicación

``` mermaid

graph LR
Member((:Member))
%% El Socio hace la petición a la Web de Reservas
-- "1: selectConfirmReservation"
--> WebInterface((:WebInterface))
WebInterface
-- "1.1: openReservationManager()"
--> Manager((:ReservationManager))
Manager
-- "2: getData()"
--> Database((:Database))

```


# Fase 3: Lógica del Proceso

Diagrama de Actividades

``` mermaid

stateDiagram-v2
%% Diagrama de Actividades para Validar Reserva
[*] --> ReceiveRequest
%% Recibir petición y revisar datos del Socio
ReceiveRequest --> CheckMemberData 
%% Según si tiene cuota pagada o no decidir siguiente acción del flujo
state check_fee <<choice>>
CheckMemberData --> check_fee
check_fee --> ProcessReservation: [fee paid]
check_fee --> NotifyReservationIneligible: [fee not paid]
%% Si el Socio ha pagado su cuota (reserva elegible), revisar aforo
state check_capacity <<choice>>
ProcessReservation --> check_capacity
check_capacity --> AssignPlace: [capacity not full]
check_capacity --> NotifyNoPlaceAvailable : [full capacity]
%% Si hay aforo, establecer la Reserva asignando y bloqueando plaza
AssignPlace --> EmailConfirm: [place assigned]
%% Finalmente, una vez asignada y reservada la plaza, enviar Email de confirmación
EmailConfirm --> [*]

```


# Fase 4: Ciclo de Vida del Objeto

Diagrama de Estados

``` mermaid

stateDiagram-v2
%% Diagrama de Estados para la Reserva duratne todo su Ciclo de Vida
%% Draft: Está creada o creándose pero todavía no se ha enviado por la aplicación
[*] --> Draft
%%% Issued: Está creada y enviada a través de la aplicación, quedando pendiente 
Draft --> Issued : send()
%% Confirmed: Está confirmada
Issued --> Confirmed : confirm()
%% Cancelled: Está cancelada
Issued --> Cancelled: cancel()
%% Realizada: Fue confirmada y el Socio se presentó a su plaza reservada
Confirmed --> CheckedIn: checkIn()
%% NotShownUp: Fue confirmada pero el Socio no se presentó a su plaza reservada
Confirmed --> NotShownUp: didNotShowUp()
%% Estados Finales
CheckedIn --> [*]
NotShownUp --> [*]
Cancelled --> [*]

```


