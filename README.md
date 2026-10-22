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
