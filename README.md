# Entornos-7.5

#UML - DIAGRAMAS

#Diagrama de Casos de uso 

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
