# UNIVERSIDAD

### Consultas sobre una tabla

1. ### Devuelve un listado con el primer apellido, segundo apellido y el nombre de todos los alumnos. El listado deberá estar ordenado alfabéticamente de menor a mayor por el primer apellido, segundo apellido y nombre.

```sql
SELECT apellido1, apellido2, nombre
FROM alumno
ORDER BY apellido1 ASC, apellido2 ASC;

+-----------+-----------+--------+
| apellido1 | apellido2 | nombre |
+-----------+-----------+--------+
| Díaz      | Fernández | Laura  |
| García    | López     | María  |
| Hernández | Pérez     | Ana    |
| Martínez  | Sánchez   | Juan   |
| Rodríguez | Gómez     | Pedro  |
+-----------+-----------+--------+
```

2. #### Averigua el nombre y los dos apellidos de los alumnos que no han dado de alta su número de teléfono en la base de datos.

```sql
SELECT nombre, apellido1, apellido2
FROM alumno
WHERE id NOT IN (SELECT id_alumno
                FROM telefono_alumno);
                
+--------+-----------+-----------+
| nombre | apellido1 | apellido2 |
+--------+-----------+-----------+
| Pedro  | Rodríguez | Gómez     |
| Laura  | Díaz      | Fernández |
+--------+-----------+-----------+
```

3. #### Devuelve el listado de los alumnos que nacieron en 1999.

```sql
SELECT nombre, apellido1, apellido2
FROM alumno
WHERE YEAR(fecha_nacimiento) = 1999;

+--------+-----------+-----------+
| nombre | apellido1 | apellido2 |
+--------+-----------+-----------+
| María  | García    | López     |
| Ana    | Hernández | Pérez     |
| Laura  | Díaz      | Fernández |
+--------+-----------+-----------+
```

4. #### Devuelve el listado de profesores que no han dado de alta su número de teléfono en la base de datos y además su nif termina en K.

```sql
SELECT p.nombre, p.apellido1, p.apellido2
FROM profesor AS p
INNER JOIN nif_profesor AS nf ON nf.id_profesor = p.id
LEFT JOIN telefono_profesor AS tp ON p.id = tp.id_profesor
WHERE tp.id_profesor IS NULL
AND nf.nif LIKE '%K';
```

5. #### Devuelve el listado de las asignaturas que se imparten en el primer cuatrimestre, en el tercer curso del grado que tiene el identificador 7.

```sql
SELECT nombre
FROM asignatura
WHERE id_cuatrimestre = 1 AND id_curso = 3 AND id_grado = 7;
```

### Consultas multitabla (Composición interna)

1. #### Devuelve un listado con los datos de todas las alumnas que se han matriculado alguna vez en el Grado en Ingeniería Informática (Plan 2015).

```sql
SELECT a.nombre, a.apellido1, a.apellido2
FROM alumno as a
INNER JOIN alumno_se_matricula_asignatura AS asma ON asma.id_alumno = a.id
INNER JOIN asignatura AS ag ON asma.id_asignatura = ag.id
INNER JOIN grado AS g On g.id = ag.id_grado
WHERE a.id_sexo = 2 AND g.nombre = 'Ingeniería Informática';

+--------+-----------+-----------+
| nombre | apellido1 | apellido2 |
+--------+-----------+-----------+
| María  | García    | López     |
| Ana    | Hernández | Pérez     |
| Laura  | Díaz      | Fernández |
+--------+-----------+-----------+
```

2. #### Devuelve un listado con todas las asignaturas ofertadas en el Grado en Ingeniería Informática (Plan 2015).

```sql
SELECT ag.nombre
FROM asignatura AS ag
INNER JOIN grado AS g ON g.id = ag.id_grado
WHERE g.nombre = 'Ingeniería Informática';

+--------------+
| nombre       |
+--------------+
| Matemáticas  |
| Programación |
| Historia     |
+--------------+
```

3. #### Devuelve un listado de los profesores junto con el nombre del departamento al que están vinculados. El listado debe devolver cuatro columnas, primer apellido, segundo apellido, nombre y nombre del departamento. El resultado estará ordenado alfabéticamente de menor a mayor por los apellidos y el nombre.

```sql
SELECT p.apellido1, p.apellido2, p.nombre, d.nombre AS nombre_departamento
FROM profesor as p
INNER JOIN departamento AS d ON p.id_departamento = d.id
ORDER BY p.apellido1 ASC, p.apellido2 ASC, p.nombre ASC;

+-----------+-----------+-----------+-----------------------------+
| apellido1 | apellido2 | nombre    | nombre_departamento         |
+-----------+-----------+-----------+-----------------------------+
| Díaz      | Pérez     | Alejandro | Departamento de Informática |
| González  | Fernández | Pablo     | Departamento de Informática |
| López     | Martínez  | Sofía     | Departamento de Física      |
| Martín    | Sanz      | Elena     | Departamento de Física      |
| Ruiz      | Gómez     | Carlos    | Departamento de Matemáticas |
+-----------+-----------+-----------+-----------------------------+
```

4. #### Devuelve un listado con el nombre de las asignaturas, año de inicio y año de fin del curso escolar del alumno con nif 26902806M.

```sql
SELECT ag.nombre, ce.nombre
FROM alumno AS al
INNER JOIN alumno_se_matricula_asignatura AS asma ON asma.id_alumno = al.id
INNER JOIN curso_escolar AS ce ON ce.id = asma.id_curso_escolar
INNER JOIN asignatura As ag ON ag.id = asma.id_asignatura
INNER JOIN nif_alumno As na ON na.id_alumno = al.id
WHERE na.nif = '26902806M';
```

5. #### Devuelve un listado con el nombre de todos los departamentos que tienen profesores que imparten alguna asignatura en el Grado en Ingeniería Informática (Plan 2015).

```sql
SELECT d.nombre
FROM departamento as d
INNER JOIN profesor as p ON d.id = p.id_departamento
INNER JOIN asignatura as ag ON ag.id_profesor = p.id
INNER JOIN grado AS g ON g.id = ag.id_grado
WHERE g.nombre = 'Ingeniería Informática';

+-----------------------------+
| nombre                      |
+-----------------------------+
| Departamento de Matemáticas |
| Departamento de Informática |
+-----------------------------+
```

6. #### Devuelve un listado con todos los alumnos que se han matriculado en alguna asignatura durante el curso escolar 2018/2019.

```sql
SELECT al.nombre, al.apellido1, al.apellido2
FROM alumno AS al
INNER JOIN alumno_se_matricula_asignatura AS asma ON asma.id_alumno = al.id
INNER JOIN curso_escolar AS ce ON ce.id = asma.id_curso_escolar
WHERE YEAR(ce.año_inicio) = 2018 AND YEAR(ce.año_fin) = 2019;

+--------+-----------+-----------+
| nombre | apellido1 | apellido2 |
+--------+-----------+-----------+
| María  | García    | López     |
| Ana    | Hernández | Pérez     |
| Laura  | Díaz      | Fernández |
+--------+-----------+-----------+
```

### Consultas multitabla (Composición externa)

1. #### Devuelve un listado con los nombres de todos los profesores y los departamentos que tienen vinculados. El listado también debe mostrar aquellos profesores que no tienen ningún departamento asociado. El listado debe devolver cuatro columnas, nombre del departamento, primer apellido, segundo apellido y nombre del profesor. El resultado estará ordenado alfabéticamente de menor a mayor por el nombre del departamento, apellidos y el nombre.

```sql
SELECT d.nombre, p.apellido1, p.apellido2, p.nombre
FROM profesor as p
LEFT JOIN departamento as d ON p.id_departamento = d.id
ORDER BY d.nombre ASC, p.apellido1 ASC, p.apellido2 ASC, p.nombre ASC;

+-----------------------------+-----------+-----------+-----------+
| nombre                      | apellido1 | apellido2 | nombre    |
+-----------------------------+-----------+-----------+-----------+
| Departamento de Física      | López     | Martínez  | Sofía     |
| Departamento de Física      | Martín    | Sanz      | Elena     |
| Departamento de Informática | Díaz      | Pérez     | Alejandro |
| Departamento de Informática | González  | Fernández | Pablo     |
| Departamento de Matemáticas | Ruiz      | Gómez     | Carlos    |
+-----------------------------+-----------+-----------+-----------+
```

2. #### Devuelve un listado con los profesores que no están asociados a un departamento.

```sql
SELECT p.nombre, p.apellido1, p.apellido2
FROM profesor AS p
LEFT JOIN departamento AS d ON p.id_departamento = d.id
WHERE d.id IS NULL;
```

3. #### Devuelve un listado con los departamentos que no tienen profesores asociados.

```sql
SELECT d.nombre
FROM departamento AS d
LEFT JOIN profesor as p ON d.id = p.id_departamento
WHERE p.id IS NULL;
```

4. #### Devuelve un listado con los profesores que no imparten ninguna asignatura.

```sql
SELECT p.nombre, p.apellido1, p.apellido2
FROM profesor as p
LEFT JOIN asignatura as ag ON p.id = ag.id_profesor
WHERE ag.id IS NULL;

+-----------+-----------+-----------+
| nombre    | apellido1 | apellido2 |
+-----------+-----------+-----------+
| Alejandro | Díaz      | Pérez     |
+-----------+-----------+-----------+
```

5. #### Devuelve un listado con las asignaturas que no tienen un profesor asignado.

```sql
SELECT ag.nombre
FROM asignatura as ag
LEFT JOIN profesor as p ON ag.id_profesor = p.id
WHERE p.id IS NULL;

+----------+
| nombre   |
+----------+
| Historia |
+----------+
```

6. #### Devuelve un listado con todos los departamentos que tienen alguna asignatura que no se haya impartido en ningún curso escolar. El resultado debe mostrar el nombre del departamento y el nombre de la asignatura que
no se haya impartido nunca.

```sql
SELECT d.nombre AS nombre_departamento, a.nombre AS nombre_asignatura
FROM departamento AS d
LEFT JOIN profesor AS p ON d.id = p.id_departamento
LEFT JOIN asignatura AS a ON p.id = a.id_profesor
LEFT JOIN alumno_se_matricula_asignatura AS asma ON a.id = asma.id_asignatura
LEFT JOIN curso_escolar AS ce ON asma.id_curso_escolar = ce.id
WHERE ce.id IS NULL
AND p.id_departamento IS NOT NULL;

+-----------------------------+-------------------+
| nombre_departamento         | nombre_asignatura |
+-----------------------------+-------------------+
| Departamento de Informática | NULL              |
+-----------------------------+-------------------+
```

### Consultas resumen

1. #### Devuelve el número total de alumnas que hay.

```sql
SELECT COUNT(id) AS total_alumnos
FROM alumno;

+---------------+
| total_alumnos |
+---------------+
|             5 |
+---------------+
```

2. #### Calcula cuántos alumnos nacieron en 1999.

```sql
SELECT COUNT(id) AS total_alumnos_1999
FROM alumno
WHERE YEAR(fecha_nacimiento) = 1999;

+--------------------+
| total_alumnos_1999 |
+--------------------+
|                  3 |
+--------------------+
```

3. #### Calcula cuántos profesores hay en cada departamento. El resultado sólo debe mostrar dos columnas, una con el nombre del departamento y otra con el número de profesores que hay en ese departamento. El resultado sólo debe incluir los departamentos que tienen profesores asociados y deberá estar ordenado de mayor a menor por el número de profesores.

```sql
SELECT d.nombre AS nombre_departamento, COUNT(p.id) AS cantidad_profesores
FROM departamento d
INNER JOIN profesor p ON d.id = p.id_departamento
GROUP BY d.nombre
ORDER BY cantidad_profesores DESC;

+-----------------------------+---------------------+
| nombre_departamento         | cantidad_profesores |
+-----------------------------+---------------------+
| Departamento de Informática |                   2 |
| Departamento de Física      |                   2 |
| Departamento de Matemáticas |                   1 |
+-----------------------------+---------------------+
```

4. #### Devuelve un listado con todos los departamentos y el número de profesores que hay en cada uno de ellos. Tenga en cuenta que pueden existir departamentos que no tienen profesores asociados. Estos departamentos también tienen que aparecer en el listado.

```sql
SELECT d.nombre AS nombre_departamento, COUNT(p.id) AS cantidad_profesores
FROM departamento AS d
LEFT JOIN profesor AS p ON d.id = p.id_departamento
GROUP BY d.nombre;

+-----------------------------+---------------------+
| nombre_departamento         | cantidad_profesores |
+-----------------------------+---------------------+
| Departamento de Física      |                   2 |
| Departamento de Informática |                   2 |
| Departamento de Matemáticas |                   1 |
+-----------------------------+---------------------+
```

5. #### Devuelve un listado con el nombre de todos los grados existentes en la base de datos y el número de asignaturas que tiene cada uno. Tenga en cuenta que pueden existir grados que no tienen asignaturas asociadas. Estos grados también tienen que aparecer en el listado. El resultado deberá estar ordenado de mayor a menor por el número de asignaturas.

```sql
SELECT g.nombre AS nombre_grado, COUNT(ag.id) AS cantidad_asignaturas
FROM grado as g
RIGHT JOIN  asignatura as ag ON ag.id_grado = g.id
GROUP BY g.nombre
ORDER BY cantidad_asignaturas DESC;

+------------------------+----------------------+
| nombre_grado           | cantidad_asignaturas |
+------------------------+----------------------+
| Ingeniería Informática |                    3 |
| Matemáticas            |                    2 |
+------------------------+----------------------+
```

6. #### Devuelve un listado con el nombre de todos los grados existentes en la base de datos y el número de asignaturas que tiene cada uno, de los grados que tengan más de 40 asignaturas asociadas.

```sql
SELECT g.nombre AS nombre_grado, COUNT(ag.id) AS cantidad_asignaturas
FROM grado AS g
RIGHT JOIN asignatura AS ag ON ag.id_grado = g.id
GROUP BY g.nombre
HAVING COUNT(ag.id) > 40;
```

7. #### Devuelve un listado que muestre el nombre de los grados y la suma del número total de créditos que hay para cada tipo de asignatura. El resultado debe tener tres columnas: nombre del grado, tipo de asignatura y la suma
de los créditos de todas las asignaturas que hay de ese tipo. Ordene el resultado de mayor a menor por el número total de créditos.

```sql
SELECT g.nombre as nombre_grado, ta.nombre AS tipo_asignatura, SUM(ag.creditos) AS creditos
FROM grado AS g
INNER JOIN asignatura as ag ON ag.id_grado = g.id
INNER JOIN tipo_asignatura As ta ON ta.id = ag.id_tipo_asignatura
GROUP BY g.nombre, ta.nombre;
+------------------------+-----------------+----------+
| nombre_grado           | tipo_asignatura | creditos |
+------------------------+-----------------+----------+
| Ingeniería Informática | Obligatoria     |       13 |
| Ingeniería Informática | Optativa        |        4 |
| Matemáticas            | Obligatoria     |       11 |
+------------------------+-----------------+----------+
```

8. #### Devuelve un listado que muestre cuántos alumnos se han matriculado de alguna asignatura en cada uno de los cursos escolares. El resultado deberá mostrar dos columnas, una columna con el año de inicio del curso escolar y
otra con el número de alumnos matriculados.

```sql
SELECT ce.año_inicio AS año_inicio_curso_escolar, COUNT(asma.id_alumno)
FROM curso_escolar AS ce
INNER JOIN alumno_se_matricula_asignatura AS asma ON asma.id_curso_escolar = ce.id
GROUP BY ce.id;

+--------------------------+-----------------------+
| año_inicio_curso_escolar | COUNT(asma.id_alumno) |
+--------------------------+-----------------------+
| 2018-09-01               |                     3 |
| 2019-09-01               |                     2 |
+--------------------------+-----------------------+
```

9. #### Devuelve un listado con el número de asignaturas que imparte cada profesor. El listado debe tener en cuenta aquellos profesores que no imparten ninguna asignatura. El resultado mostrará cinco columnas: id, nombre, primer apellido, segundo apellido y número de asignaturas. El resultado estará ordenado de mayor a menor por el número de asignaturas.

```sql
SELECT p.id, p.nombre, p.apellido1, p.apellido2, COUNT(ag.id) AS numero_de_asignaturas
FROM profesor AS p
LEFT JOIN asignatura as ag ON p.id = ag.id_profesor
GROUP BY p.id;

+----+-----------+-----------+-----------+-----------------------+
| id | nombre    | apellido1 | apellido2 | numero_de_asignaturas |
+----+-----------+-----------+-----------+-----------------------+
|  1 | Carlos    | Ruiz      | Gómez     |                     1 |
|  2 | Sofía     | López     | Martínez  |                     1 |
|  3 | Pablo     | González  | Fernández |                     1 |
|  4 | Elena     | Martín    | Sanz      |                     1 |
|  5 | Alejandro | Díaz      | Pérez     |                     0 |
+----+-----------+-----------+-----------+-----------------------+
```

### Subconsultas

1. #### Devuelve todos los datos del alumno más joven.

```sql
SELECT a.id,a.nombre,a.apellido1, a.apellido2, a.id_ciudad, a.fecha_nacimiento, a.id_sexo
FROM alumno AS a
WHERE a.fecha_nacimiento = (
        SELECT 
            MAX(fecha_nacimiento) 
        FROM alumno
);

+----+--------+-----------+-----------+-----------+------------------+---------+
| id | nombre | apellido1 | apellido2 | id_ciudad | fecha_nacimiento | id_sexo |
+----+--------+-----------+-----------+-----------+------------------+---------+
|  2 | Juan   | Martínez  | Sánchez   |         2 | 2000-05-20       |       1 |
+----+--------+-----------+-----------+-----------+------------------+---------+
```

2. #### Devuelve un listado con los profesores que no están asociados a un departamento.

```sql
SELECT nombre, apellido1, apellido2
FROM profesor 
WHERE id_departamento NOT IN (
    SELECT id
    FROM departamento
);
```

3. #### Devuelve un listado con los departamentos que no tienen profesores asociados.

```sql
SELECT nombre AS nombre_departamento
FROM departamento
WHERE id NOT IN(
	SELECT id_departamento
    FROM profesor
);
```

4. #### Devuelve un listado con los profesores que tienen un departamento asociado y que no imparten ninguna asignatura.

```sql
SELECT nombre, apellido1, apellido2
FROM profesor
WHERE id_departamento IS NOT NULL AND
id NOT IN(
	SELECT id_profesor
    FROM asignatura
);
```

5. #### Devuelve un listado con las asignaturas que no tienen un profesor asignado.

```sql
SELECT nombre as nombre_asignatura
FROM asignatura
WHERE id_profesor NOT IN (
    SELECT id
    FROM profesor
);
```

6. #### Devuelve un listado con todos los departamentos que no han impartido asignaturas en ningún curso escolar.

```sql
SELECT nombre AS nombre_departamento
FROM departamento
WHERE id NOT IN (
        SELECT DISTINCT d.id
        FROM departamento AS d
        INNER JOIN profesor AS p ON d.id = p.id_departamento
        INNER JOIN asignatura AS a ON p.id = a.id_profesor
        INNER JOIN alumno_se_matricula_asignatura AS asma ON a.id = asma.id_asignatura
        INNER JOIN curso_escolar AS ce ON asma.id_curso_escolar = ce.id
    );

```

### PROCEDURES

#### 1.

```sql
DELIMITER $$
CREATE PROCEDURE InsertarAlumno(
    IN nombre VARCHAR(25),
    IN apellido1 VARCHAR(50),
    IN apellido2 VARCHAR(50),
    IN id_ciudad INT,
    IN fecha_nacimiento DATE,
    IN id_sexo INT
)
BEGIN
    INSERT INTO alumno (nombre, apellido1, apellido2, id_ciudad, fecha_nacimiento, id_sexo)
    VALUES (nombre, apellido1, apellido2, id_ciudad, fecha_nacimiento, id_sexo);
END $$
DELIMITER ;
```

#### 2.

```sql
DELIMITER $$
CREATE PROCEDURE ObtenerAlumnoPorID(
    IN alumno_id INT
)
BEGIN
    SELECT nombre, apellido1, apellido2, fecha_nacimiento, id_sexo FROM alumno WHERE id = alumno_id;
END $$
DELIMITER ;
```

#### 3.

```sql
DELIMITER $$

CREATE PROCEDURE ActualizarCiudadAlumno(
    IN p_id INT,
    IN p_id_ciudad INT
)
BEGIN
    UPDATE alumno 
    SET id_ciudad = p_id_ciudad
    WHERE id = p_id;
END $$

DELIMITER ;
```

#### 4.

```sql
DELIMITER $$

CREATE PROCEDURE EliminarAlumno(
    IN p_id INT
)
BEGIN
    DELETE FROM alumno WHERE id = p_id;
END $$
DELIMITER ;
```

#### 5.

```sql
DELIMITER  $$

CREATE PROCEDURE CantidadProfesores()

BEGIN 
	SELECT d.nombre AS nombre_departamento, COUNT(p.id) AS cantidad_profesores
    FROM departamento AS d
    LEFT JOIN profesor AS p ON d.id = p.id_departamento
    GROUP BY d.nombre;
END $$
DELIMITER ;
```

#### 6.

```sql
DELIMITER $$

CREATE PROCEDURE InsertarAsignatura(
    IN p_nombre VARCHAR(100),
    IN p_creditos DOUBLE,
    IN p_id_tipo_asignatura INT,
    IN p_id_curso INT,
    IN p_id_cuatrimestre INT,
    IN p_id_profesor INT,
    IN p_id_grado INT
)
BEGIN
    INSERT INTO asignatura (nombre, creditos, id_tipo_asignatura, id_curso, id_cuatrimestre, id_profesor, id_grado) 
    VALUES (p_nombre, p_creditos, p_id_tipo_asignatura, p_id_curso, p_id_cuatrimestre, p_id_profesor, p_id_grado);
END $$
DELIMITER ;
```

#### 7.

```sql
DELIMITER $$

CREATE PROCEDURE CreditosPorAsignatura()
BEGIN
	SELECT g.nombre as nombre_grado, ta.nombre AS tipo_asignatura, SUM(ag.creditos) AS creditos
FROM grado AS g
INNER JOIN asignatura as ag ON ag.id_grado = g.id
INNER JOIN tipo_asignatura As ta ON ta.id = ag.id_tipo_asignatura
GROUP BY g.nombre, ta.nombre;
END $$
DELIMITER ;
	
```

#### 8.

```sql
DELIMITER $$

CREATE PROCEDURE ObtenerPromedioCreditosPorCuatrimestre()
BEGIN
    SELECT c.nombre, AVG(ag.creditos) AS promedio_creditos
    FROM cuatrimestre c
    INNER JOIN asignatura ag ON c.id = ag.id_cuatrimestre
    GROUP BY c.id;
END $$

DELIMITER ;
```

#### 9.

```sql
DELIMITER $$

CREATE PROCEDURE EncontrarAlumnosPorTipoAsignatura(
    IN p_tipo_asignatura VARCHAR(100)
)
BEGIN
    SELECT a.nombre, a.apellido1, a.apellido2
    FROM alumno a
    INNER JOIN alumno_se_matricula_asignatura asma ON a.id = asma.id_alumno
    INNER JOIN asignatura ag ON asma.id_asignatura = ag.id
    INNER JOIN tipo_asignatura ta ON ag.id_tipo_asignatura = ta.id
    WHERE ta.nombre = p_tipo_asignatura;
END $$

DELIMITER ;
```

#### 10.

```sql
DELIMITER $$

CREATE PROCEDURE ObtenerNumAsignaturasPorProfesor()
BEGIN
    SELECT p.nombre, COUNT(a.id) AS num_asignaturas
    FROM profesor p
    LEFT JOIN asignatura a ON p.id = a.id_profesor
    GROUP BY p.id;
END $$

DELIMITER ;
```

### VIEWS

#### 1.

```sql
CREATE VIEW total_alumnos AS
SELECT COUNT(id) AS total_alumnos
FROM alumno;
```

#### 2.

```sql
CREATE VIEW alumnos_1999 AS
SELECT COUNT(id) AS total_alumnos_1999
FROM alumno
WHERE YEAR(fecha_nacimiento) = 1999;
```

#### 3.

```sql
CREATE VIEW profesores_por_departamento AS
SELECT d.nombre AS nombre_departamento, COUNT(p.id) AS cantidad_profesores
FROM departamento d
INNER JOIN profesor p ON d.id = p.id_departamento
GROUP BY d.nombre
ORDER BY cantidad_profesores DESC;
```

#### 4.

```sql
CREATE VIEW alumnos_alfabeticamente AS
SELECT apellido1, apellido2, nombre
FROM alumno
ORDER BY apellido1 ASC, apellido2 ASC;
```

#### 5.

```sql
CREATE VIEW profesor_departamento As
SELECT p.apellido1, p.apellido2, p.nombre, d.nombre AS nombre_departamento
FROM profesor as p
INNER JOIN departamento AS d ON p.id_departamento = d.id
ORDER BY p.apellido1 ASC, p.apellido2 ASC, p.nombre ASC;
```

#### 6.

```sql
CREATE VIEW asignatura_sin_profesor AS
SELECT ag.nombre
FROM asignatura as ag
LEFT JOIN profesor as p ON ag.id_profesor = p.id
WHERE p.id IS NULL;
```

#### 7.

```sql
CREATE VIEW grado_asignaturas AS
SELECT g.nombre AS nombre_grado, COUNT(ag.id) AS cantidad_asignaturas
FROM grado as g
RIGHT JOIN  asignatura as ag ON ag.id_grado = g.id
GROUP BY g.nombre
ORDER BY cantidad_asignaturas DESC;
```

#### 8.

```sql
CREATE VIEW alumnos_por_curso_escolar AS
SELECT ce.año_inicio AS año_inicio_curso_escolar, COUNT(asma.id_alumno)
FROM curso_escolar AS ce
INNER JOIN alumno_se_matricula_asignatura AS asma ON asma.id_curso_escolar = ce.id
GROUP BY ce.id;
```

#### 9.

```sql
CREATE VIEW asignatura_por_profesor AS
SELECT p.id, p.nombre, p.apellido1, p.apellido2, COUNT(ag.id) AS numero_de_asignaturas
FROM profesor AS p
LEFT JOIN asignatura as ag ON p.id = ag.id_profesor
GROUP BY p.id;
```

#### 10.

```sql
CREATE VIEW asignatura_de_AS
SELECT ag.nombre
FROM asignatura AS ag
INNER JOIN grado AS g ON g.id = ag.id_grado
WHERE g.nombre = 'Ingeniería Informática';
```

