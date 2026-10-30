# Plataforma de adopción de mascotas

Script SQL para crear y poblar la base de datos `plataforma_adopcion`, seguido de consultas básicas y consultas con agregación.

## 1. Creación de la base de datos, tablas y datos de prueba

```sql
create database plataforma_adopcion;

drop table if exists seguimiento cascade;
drop table if exists adopcion cascade;
drop table if exists solicitud_adopcion cascade;
drop table if exists vacunacion cascade;
drop table if exists mascota cascade;
drop table if exists vacuna cascade;
drop table if exists adoptante cascade;
drop table if exists refugio cascade;

create table refugio (
    id_refugio serial primary key,
    nombre varchar(150) not null,
    direccion varchar(200) not null,
    telefono varchar(20) not null,
    correo varchar(100) not null unique
);

create table adoptante (
    id_adoptante serial primary key,
    nombre varchar(150) not null,
    documento varchar(20) not null unique,
    telefono varchar(20) not null,
    correo varchar(100) not null unique,
    direccion varchar(200)
);

create table vacuna (
    id_vacuna serial primary key,
    nombre varchar(100) not null unique,
    descripcion text
);

create table mascota (
    id_mascota serial primary key,
    nombre varchar(100) not null,
    especie varchar(50) not null,
    raza varchar(50),
    edad int not null check (edad >= 0),
    sexo char(1) not null check (sexo in ('m', 'h')),
    tamano varchar(20) not null check (tamano in ('pequeño', 'mediano', 'grande')),
    estado_salud varchar(100) default 'saludable',
    descripcion text,
    estado varchar(20) not null default 'disponible' check (estado in ('disponible', 'adoptado', 'en_proceso')),
    id_refugio int not null references refugio(id_refugio)
);

create table vacunacion (
    id_vacunacion serial primary key,
    id_mascota int not null references mascota(id_mascota),
    id_vacuna int not null references vacuna(id_vacuna),
    fecha_aplicacion date not null default current_date,
    unique (id_mascota, id_vacuna, fecha_aplicacion)
);

create table solicitud_adopcion (
    id_solicitud serial primary key,
    fecha_solicitud date not null default current_date,
    estado varchar(20) not null default 'pendiente' check (estado in ('pendiente', 'aprobada', 'rechazada')),
    motivo_rechazo varchar(255),
    id_adoptante int not null references adoptante(id_adoptante),
    id_mascota int not null references mascota(id_mascota)
);

create table adopcion (
    id_adopcion serial primary key,
    fecha_adopcion date not null default current_date,
    id_solicitud int not null unique references solicitud_adopcion(id_solicitud)
);

create table seguimiento (
    id_seguimiento serial primary key,
    fecha_visita date not null default current_date,
    observaciones text,
    estado_mascota varchar(100) not null default 'excelente',
    id_adopcion int not null references adopcion(id_adopcion)
);


insert into refugio (nombre, direccion, telefono, correo) values
('los pitbul', 'calle 10 # 15-20, medellin', '3001112233', 'contacto@lospitbul.com'),
('refugio el awo', 'carrera 45 # 50-10, bogota', '3102223344', 'hola@elawo.org'),
('catdog', 'avenida 5 # 12-8, cali', '3203334455', 'info@catdog.com'),
('refugio esperanza', 'calle 80 # 70-15, barranquilla', '3114445566', 'ayuda@esperanza.org'),
('terranova', 'carrera 20 # 10-5, pereira', '3155556677', 'contacto@terranova.com'),
('mi mascota', 'calle 50 # 40-12, bucaramanga', '3186667788', 'adopciones@mimascota.com'),
('el arca de noe', 'avenida 33 # 66-99, medellin', '3007778899', 'contacto@arca.org'),
('pet market', 'calle 100 # 15-45, bogota', '3108889900', 'hola@petmarket.com'),
('veterinaria sevilla', 'carrera 15 # 20-30, cali', '3209990011', 'info@sevilla.com'),
('clinicapet', 'calle 5 # 10-20, santa marta', '3110001122', 'ayuda@clinicapet.org');

insert into adoptante (nombre, documento, telefono, correo, direccion) values
('santiago machado', '1001234567', '3001230001', 'santiago.machado@mail.com', 'calle 12 # 34-56'),
('maria isabel', '1002345678', '3102340002', 'maria.isabel@mail.com', 'carrera 45 # 12-34'),
('miguel salazar', '1003456789', '3203450003', 'miguel.salazar@mail.com', 'avenida 78 # 90-12'),
('maria jose pulgarin', '1004567890', '3114560004', 'mariajose.pulgarin@mail.com', 'calle 56 # 78-90'),
('valentina mesa', '1005678901', '3155670005', 'valentina.mesa@mail.com', 'carrera 23 # 45-67'),
('valentina marentes', '1006789012', '3186780006', 'valentina.marentes@mail.com', 'avenida 12 # 34-56'),
('carlos mosquera', '1007890123', '3007890007', 'carlos.mosquera@mail.com', 'calle 90 # 12-34'),
('juan jose ocampo', '1008901234', '3108900008', 'juanjose.ocampo@mail.com', 'carrera 67 # 89-01'),
('juan esteban tobon', '1009012345', '3209010009', 'juanesteban.tobon@mail.com', 'avenida 34 # 56-78'),
('jose angel sanchez', '1010123456', '3110120010', 'joseangel.sanchez@mail.com', 'calle 78 # 90-12');

insert into vacuna (nombre, descripcion) values
('rabia', 'vacuna contra la rabia'),
('parvovirus', 'vacuna contra el parvovirus canino'),
('moquillo', 'vacuna contra el moquillo canino'),
('triple felina', 'vacuna para gatos (panleucopenia, herpesvirus, calicivirus)'),
('leucemia felina', 'vacuna contra la leucemia felina'),
('bordetella', 'vacuna contra la tos de las perreras'),
('lyme', 'vacuna contra la enfermedad de lyme'),
('leptospirosis', 'vacuna contra la leptospirosis'),
('coronavirus canino', 'vacuna contra el coronavirus canino'),
('pentavalente', 'vacuna combinada de 5 enfermedades');

insert into mascota (nombre, especie, raza, edad, sexo, tamano, estado_salud, descripcion, estado, id_refugio) values
('luna', 'perro', 'labrador', 3, 'h', 'grande', 'saludable', 'muy juguetona', 'adoptado', 1),
('ryan', 'perro', 'criollo', 2, 'm', 'mediano', 'vacunado', 'protector', 'adoptado', 2),
('pelusa', 'gato', 'siames', 1, 'h', 'pequeño', 'saludable', 'tranquila', 'adoptado', 3),
('pocillo', 'perro', 'pitbull', 4, 'm', 'grande', 'saludable', 'requiere paciencia', 'adoptado', 4),
('akira', 'gato', 'criollo', 2, 'm', 'pequeño', 'saludable', 'independiente', 'adoptado', 5),
('zeus', 'perro', 'husky', 5, 'h', 'grande', 'saludable', 'muy activa', 'adoptado', 6),
('manolo', 'gato', 'persa', 3, 'm', 'mediano', 'vacunado', 'dormilon', 'adoptado', 7),
('tobey', 'perro', 'pug', 1, 'm', 'pequeño', 'saludable', 'gracioso', 'adoptado', 8),
('lola', 'gato', 'criollo', 4, 'h', 'pequeño', 'saludable', 'cazadora', 'adoptado', 9),
('zeus_pastor', 'perro', 'pastor aleman', 6, 'm', 'grande', 'saludable', 'guardian', 'adoptado', 10),
('choco', 'gato', 'angora', 1, 'm', 'pequeño', 'saludable', 'jugueton', 'disponible', 1),
('tainy', 'perro', 'dalmata', 2, 'm', 'grande', 'en tratamiento', 'tranquilo', 'en_proceso', 2);

insert into vacunacion (id_mascota, id_vacuna, fecha_aplicacion) values
(1, 1, '2024-03-10'),
(1, 2, '2024-08-15'),
(2, 1, '2025-01-20'),
(3, 4, '2025-04-05'),
(4, 1, '2025-07-11'),
(5, 4, '2025-09-30'),
(6, 1, '2025-11-22'),
(7, 4, '2026-02-14'),
(8, 1, '2026-05-18'),
(9, 4, '2026-08-05');

insert into solicitud_adopcion (fecha_solicitud, estado, motivo_rechazo, id_adoptante, id_mascota) values
('2025-02-10', 'aprobada', null, 1, 1),
('2025-04-18', 'aprobada', null, 2, 2),
('2025-06-25', 'aprobada', null, 3, 3),
('2025-08-30', 'aprobada', null, 4, 4),
('2025-10-15', 'aprobada', null, 5, 5),
('2025-12-05', 'aprobada', null, 6, 6),
('2026-01-20', 'aprobada', null, 7, 7),
('2026-03-11', 'aprobada', null, 8, 8),
('2026-06-08', 'aprobada', null, 9, 9),
('2026-09-02', 'aprobada', null, 10, 10),
('2026-05-14', 'rechazada', 'espacio insuficiente en casa', 1, 11),
('2026-07-22', 'rechazada', 'alergia severa en la familia', 2, 12),
('2026-10-30', 'rechazada', 'falta de tiempo para cuidados', 3, 11);

insert into adopcion (fecha_adopcion, id_solicitud) values
('2025-02-15', 1),
('2025-04-22', 2),
('2025-06-30', 3),
('2025-09-05', 4),
('2025-10-20', 5),
('2025-12-10', 6),
('2026-01-25', 7),
('2026-03-16', 8),
('2026-06-14', 9),
('2026-09-08', 10);

insert into seguimiento (fecha_visita, observaciones, estado_mascota, id_adopcion) values
('2025-05-15', 'perro muy feliz y adaptado', 'excelente', 1),
('2025-07-22', 'todas las vacunas al dia', 'muy bueno', 2),
('2025-09-30', 'el gato subio de peso saludablemente', 'excelente', 3),
('2025-12-05', 'comportamiento excelente con ninos', 'bueno', 4),
('2026-01-20', 'se muestra mas independiente', 'excelente', 5),
('2026-03-10', 'mucha energia, requiere mas paseos', 'bueno', 6),
('2026-04-25', 'pelaje brillante y sano', 'excelente', 7),
('2026-06-16', 'jugueton pero muy educado', 'muy bueno', 8),
('2026-09-14', 'visita reciente al veterinario sin novedades', 'bueno', 9),
('2026-12-08', 'la familia esta encantada con el', 'excelente', 10);
```

## 2. Consultas básicas

### 1. Listar el identificador, nombre, raza, edad y estado de los 3 perros de tamaño grande de mayor edad.

```sql
select id_mascota, nombre, raza, edad, estado
from mascota
where especie = 'perro' and tamano = 'grande'
order by edad desc
limit 3;
```

### 2. Consultar los adoptantes cuyo nombre comienza por “maria”, mostrando su identificador, nombre, teléfono y correo, ordenados alfabéticamente.

```sql
select id_adoptante, nombre, telefono, correo
from adoptante
where nombre like 'maria%'
order by nombre;
```

### 3. Obtener las mascotas con una edad comprendida entre 2 y 4 años, mostrando su nombre, especie y edad, ordenadas por edad y nombre.

```sql
select id_mascota, nombre, especie, edad
from mascota
where edad between 2 and 4
order by edad, nombre;
```

### 4. Listar las adopciones realizadas durante el año 2025, ordenadas cronológicamente por su fecha de adopción.

```sql
select id_adopcion, fecha_adopcion, id_solicitud
from adopcion
where fecha_adopcion between '2025-01-01' and '2025-12-31'
order by fecha_adopcion;
```

### 5. Identificar los refugios ubicados en la ciudad de Medellín, mostrando su nombre, dirección y teléfono.

```sql
select id_refugio, nombre, direccion, telefono
from refugio
where direccion like '%medellin%';
```

## 3. Consultas con agregación

### 1. Calcular, para cada especie, el total de mascotas registradas, la suma de sus edades, la edad promedio, la edad mínima y la edad máxima, ordenando el resultado de mayor a menor cantidad de mascotas.

```sql
select especie,
       count(*) as total_mascotas,
       sum(edad) as suma_edades,
       round(avg(edad), 1) as edad_promedio,
       min(edad) as edad_minima,
       max(edad) as edad_maxima
from mascota
group by especie
order by total_mascotas desc;
```

### 2. Determinar los refugios que tienen más de una mascota registrada, indicando el nombre del refugio y la cantidad de mascotas que alberga.

```sql
select r.nombre as refugio,
       count(m.id_mascota) as total_mascotas
from refugio r
inner join mascota m on m.id_refugio = r.id_refugio
group by r.nombre
having count(m.id_mascota) > 1;
```

### 3. Contar la cantidad de solicitudes de adopción según su estado (pendiente, aprobada o rechazada), ordenando el resultado de mayor a menor cantidad.

```sql
select estado, count(*) as total_solicitudes
from solicitud_adopcion
group by estado
order by total_solicitudes desc;
```

### 4. Obtener, para cada estado de solicitud, la cantidad total de solicitudes junto con la fecha de la primera y de la última solicitud registrada.

```sql
select estado,
       count(*) as total,
       min(fecha_solicitud) as primera_solicitud,
       max(fecha_solicitud) as ultima_solicitud
from solicitud_adopcion
group by estado;
```

### 5. Identificar las vacunas que han sido aplicadas al menos dos veces, mostrando el nombre de la vacuna y el número de aplicaciones, ordenadas de mayor a menor.

```sql
select v.nombre as vacuna,
       count(*) as aplicaciones
from vacunacion vc
inner join vacuna v on v.id_vacuna = vc.id_vacuna
group by v.nombre
having count(*) >= 2
order by aplicaciones desc;
```
## 4 consultas con inner y left join

### 1.Listar todas las mascotas registradas (mostrando su identificador, nombre y especie) junto con el nombre de las vacunas que se les han aplicado y la fecha de aplicación, incluyendo también a aquellas mascotas que aún no tienen ninguna vacuna registrada, ordenadas por el identificador de la mascota

```
select 
	m.id_mascota, 
	m.nombre as mascota, 
	m.especie, v.nombre as vacuna, 
	vc.fecha_aplicacion
from mascota m
left join vacunacion vc on m.id_mascota = vc.id_mascota
left join vacuna v on vc.id_vacuna = v.id_vacuna
order by m.id_mascota;
```

### 2.Generar un reporte analítico de todos los refugios que indique su nombre, la cantidad total de solicitudes de adopción recibidas por sus animales, la edad promedio de las mascotas albergadas (mostrando un 0 si no tienen ninguna registrada) y la fecha de la solicitud de adopción más reciente. El reporte debe incluir absolutamente todos los refugios, incluso si no tienen mascotas o historial de solicitudes.

```
select 
    r.nombre as refugio, 
    count(s.id_solicitud) as total_solicitudes, 
    coalesce(round(avg(m.edad), 1), 0) as edad_promedio, 
    max(s.fecha_solicitud) as ultima_solicitud
from refugio r
left join mascota m on r.id_refugio = m.id_refugio
left join solicitud_adopcion s on m.id_mascota = s.id_mascota
group by r.id_refugio, r.nombre
order by total_solicitudes desc;
```

### 3. Mostrar las cinco solicitudes de adopción más recientes, indicando su fecha, estado, el nombre del adoptante que la realizó y el nombre de la mascota solicitada.

```sql
select
    s.id_solicitud,
    s.fecha_solicitud,
    s.estado,
    a.nombre as adoptante,
    m.nombre as mascota
from solicitud_adopcion s
inner join adoptante a on a.id_adoptante = s.id_adoptante
inner join mascota m on m.id_mascota = s.id_mascota
order by s.fecha_solicitud desc
limit 5;
```

## 5. consultas con conjuntos

### 1.Para un boletín informativo general, necesitamos una lista consolidada de todos los nombres y correos electrónicos registrados en nuestra plataforma. Genera una lista única (sin duplicados) combinando el nombre y correo de los adoptantes con el nombre y correo de los refugios. Renombra las columnas a nombre_contacto y correo_contacto.

```
select nombre as nombre_contacto, correo as correo_contacto
from adoptante
union
select nombre, correo
from refugio
order by nombre_contacto;

```

### 2. Necesitamos identificar qué ciudades o barrios donde viven nuestros adoptantes no cuentan con cobertura de ningún refugio local. Encuentra las direcciones (o zonas) registradas por los adoptantes que no existan en la lista de direcciones de nuestros refugios

```
select direccion
from adoptante
except
select direccion
from refugio;

```

### 3. El refugio está buscando perfiles de mascotas que cumplan simultáneamente con dos condiciones para una campaña de adopción especial: mascotas que sean de tamaño pequeño y que estén saludables.

```
select nombre
from mascota
where tamano = 'pequeño'
intersect
select nombre
from mascota
where estado_salud = 'saludable';

```

## 6. triggers y procedimientos

### 1. Trigger funcional : En la plataforma de adopción, cuando una solicitud se aprueba y se registra formalmente en la tabla adopcion, el estado de la mascota en la tabla mascota debe pasar automáticamente a 'adoptado' y cualquier otra solicitud que estuviera 'pendiente' para ese mismo animal debe cerrarse como 'rechazada'. Implementar un trigger after insert sobre la tabla adopcion automatiza esta regla de negocio directamente en el motor de la base de datos, evitando inconsistencias humanas o que dos personas adopten a la misma mascota por error.

```
create or replace function fn_actualizar_mascota_adoptada()
returns trigger as $$
declare
    v_id_mascota int;
begin
    select id_mascota into v_id_mascota
    from solicitud_adopcion
    where id_solicitud = new.id_solicitud;

    update mascota
    set estado = 'adoptado'
    where id_mascota = v_id_mascota;

    update solicitud_adopcion
    set estado = 'rechazada',
        motivo_rechazo = 'la mascota ya fue adoptada en otro proceso'
    where id_mascota = v_id_mascota
      and id_solicitud <> new.id_solicitud
      and estado = 'pendiente';

    return new;
end;
$$ language plpgsql;

create trigger trg_actualizar_estado_adopcion
after insert on adopcion
for each row
execute function fn_actualizar_mascota_adoptada();

```

### 2. procedimiento almacenado : El proceso de aprobar y concretar una adopción implica validar el estado actual de la solicitud, modificar múltiples tablas (solicitud_adopcion, adopcion y seguimiento) y asegurar que ninguna operación quede a medias. Un procedimiento almacenado permite encapsular toda esta transacción en una sola llamada (call): verifica que la solicitud esté 'pendiente' (lanzando una excepción si no lo está), aprueba la solicitud, crea el registro oficial de adopción y programa automáticamente la primera visita de seguimiento para dentro de 30 días

```
create or replace procedure sp_aprobar_y_registrar_adopcion(
    p_id_solicitud int,
    p_observacion_inicial text
)
language plpgsql
as $$
declare
    v_estado_actual varchar(20);
    v_id_nueva_adopcion int;
begin
    select estado into v_estado_actual
    from solicitud_adopcion
    where id_solicitud = p_id_solicitud;

    if not found then
        raise exception 'la solicitud % no existe', p_id_solicitud;
    end if;

    if v_estado_actual <> 'pendiente' then
        raise exception 'la solicitud % no se puede aprobar porque su estado actual es %', p_id_solicitud, v_estado_actual;
    end if;

    update solicitud_adopcion
    set estado = 'aprobada',
        motivo_rechazo = null
    where id_solicitud = p_id_solicitud;

    insert into adopcion (fecha_adopcion, id_solicitud)
    values (current_date, p_id_solicitud)
    returning id_adopcion into v_id_nueva_adopcion;

    insert into seguimiento (fecha_visita, observaciones, estado_mascota, id_adopcion)
    values (current_date + interval '30 days', p_observacion_inicial, 'excelente', v_id_nueva_adopcion);

    raise notice 'adopcion registrada con exito con id % y seguimiento programado', v_id_nueva_adopcion;
end;
$$;

```

## prueba de ejecucion 

```
insert into solicitud_adopcion (fecha_solicitud, estado, motivo_rechazo, id_adoptante, id_mascota)
values (current_date, 'pendiente', null, 4, 11);

call sp_aprobar_y_registrar_adopcion(14, 'primera visita de control programada al mes de entrega');

select id_mascota, nombre, estado from mascota where id_mascota = 11;
select * from adopcion where id_solicitud = 14;
select * from seguimiento where id_adopcion = (select id_adopcion from adopcion where id_solicitud = 14);


call sp_aprobar_y_registrar_adopcion(16, 'primera visita de control programada al mes de entrega');
select id_mascota, nombre, estado from mascota where id_mascota = 11;
select * from adopcion where id_solicitud = 16;
select * from seguimiento where id_adopcion = (select id_adopcion from adopcion where id_solicitud = 16);

```

