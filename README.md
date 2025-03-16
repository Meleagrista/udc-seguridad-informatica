<div align="center">

  <img src="assets/udc-logo.png" alt="Logo de la Universidad de A Coruña" width="400" height="auto" />
  <h1>Legislación y Seguridad Informática</h1>
  <p>Repositorio para centralizar apuntes, materiales, ejercicios y recursos relacionados con la materia.</p>
  
<p>
  <a href="https://github.com/Meleagrista/udc-seguridad-informaticae/graphs/contributors">
    <img src="https://img.shields.io/github/contributors/Meleagrista/udc-seguridad-informatica" alt="Contribuidores"/>
  </a>
  <a href="">
    <img src="https://img.shields.io/github/last-commit/Meleagrista/udc-seguridad-informatica" alt="Última actualización"/>
  </a>
  <a href="https://github.com/Meleagrista/udc-seguridad-informatica/stargazers">
    <img src="https://img.shields.io/github/stars/Meleagrista/udc-seguridad-informatica" alt="Favoritos"/>
  </a>
  <a href="https://github.com/Meleagrista/udc-seguridad-informatica/watchers">
    <img src="https://img.shields.io/github/watchers/Meleagrista/udc-seguridad-informatica" alt="Siguiendo"/>
  </a>
</p>

</div>
<br/>

<a id="top"></a>
<!-- <p align="right"><a href="#top">back to top</a></p> -->


# Sobre _Legislación y Seguridad Informática_
La asignatura **Legislación y Seguridad Informática** es una materia optativa del tercer curso del **Grado en Ingeniería Informática**, impartida en el primer cuatrimestre y con una carga de **6 créditos**. 

> En la asignatura de Legislación y Seguridad Informática se proporciona al alumno unos fundamentos en seguridad de la información, y con ello un valor añadido sobre otros “profesionales” del sector. En todo momento nos centramos en aquellos aspectos de interés para su futuro profesional, intentando llevar los contenidos de la asignatura hacia los temas y entornos de relevancia para el mundo empresarial. Nuestra profesión se centra en “hacer”, no únicamente en “saber hacer”, y a ser posible en “hacerlo lo mejor posible”.

Más información en la **[guía docente oficial](https://guiadocente.udc.es/guia_docent/index.php?centre=614&ensenyament=614G01&assignatura=614G01024&idioma=cast&any_academic=2024_25)**.

<details>
  <summary>Tabla de Contenidos</summary>
  <ol>
    <li>
      <a href="#evaluación-de-la-materia">Evaluación de la materia</a>
      <ul>
        <li><a href="#criterios-de-evaluación-y-calificación">Criterios de evaluación y calificación</a></li>
        <li><a href="#calificaciones-de-las-prácticas-de-laboratorio">Calificaciones de las prácticas de laboratorio</a></li>
      </ul>
    </li>
    <li>
      <a href="#primeros-pasos">Primeros Pasos</a>
      <ul>
        <li><a href="#guía-de-uso">Guía de uso</a></li>
      </ul>
    </li>
    <li>
      <a href="#contribuir-al-proyecto">Contribuir al proyecto</a>
      <ul>
        <li><a href="#cómo-contribuir">¿Cómo contribuir?</a></li>
        <li><a href="#contribuyentes-destacados">Contribuyentes destacados</a></li>
      </ul>
    </li>
    <li><a href="#contactos">Contactos</a></li>
  </ol>
</details>

## Evaluación de la materia
La evaluación de la asignatura se basa en **prácticas de laboratorio** y una **prueba de respuesta múltiple**. A continuación, se detallan los criterios y métodos utilizados para la calificación de los estudiantes.

### Criterios de evaluación y calificación
1. **Prácticas de laboratorio**: Cada alumno, tras considerar que ha superado cada práctica y siempre antes del plazo establecido para cada una, deberá pasar una prueba oral. En ella, el profesor plantea pequeñas pruebas que los alumnos deberán resolver sobre las máquinas virtuales del laboratorio de prácticas, defendiendo sus desarrollos de forma oral. Los plazos límite de defensa de prácticas serán informados durante el curso.

2. **Prueba de respuesta múltiple**: Esta prueba incluye los contenidos y, en general, todo aspecto relacionado con los objetivos de la asignatura.

### Calificaciones de las prácticas de laboratorio
Las siguientes tablas muestran las calificaciones obtenidas en las prácticas de laboratorio durante el curso.  

| Identificador |  Alumnos                         |Descripción | Curso   | Calificación | Comentario |
|-------------- |----------------------------------|------------|-------- |------------- |------------|
| p1		    | Martín do Río & Fernando Álvarez | -          | 2023/24 | 2/4[^1]      | -          |
| p2            | Martín do Río & Fernando Álvarez | -          | 2023/24 | 3/4          | -          |
| p3            | Martín do Río & Fernando Álvarez | -          | 2023/24 | 3/4          | -          |

[^1]: Suspendimos la primera evaluación; esta es la nota máxima que se permite en la recuperación.

# Primeros pasos
Este proyecto consiste en la implementación de mecanismos de seguridad como procesos de identificación y autenticación, control de accesos, control de flujo de información, registros de auditoría y cifrado de información. Estos conocimientos son esenciales para formar profesionales capaces de diseñar y construir sistemas seguros y eficientes.

## Guía de uso
Cada **práctica de laboratorio** tiene requisitos únicos, por lo que cada una de ellas cuenta con diferentes documentos que incluyen los enunciados, nuestras notas y los pasos a seguir.

### Organización de archivos
1. **`enunciado`**: Archivo que describe las actividades a realizar en cada práctica. Su contenido puede variar ligeramente cada año.
2. **`apuntes`**: Documento donde registramos toda la información relevante sobre la práctica, incluyendo conceptos clave y notas que puedan ser útiles durante la defensa.
3. **`defensa`**: Archivo que detalla los pasos necesarios para reproducir la práctica en caso de reiniciar la máquina. También incluye información clave para resolver posibles dudas durante la defensa.


# Contribuir al proyecto
Si tienes una sugerencia para mejorar este proyecto, puedes hacer un **fork** del repositorio y crear un **pull request**, o simplemente abrir un **issue** para discutirlo.  

Todas las contribuciones son **enormemente apreciadas**. ¡Gracias por tu apoyo!  

## Cómo contribuir

1. Realiza un **fork** del proyecto.  
2. Crea una nueva rama para tu mejora.  

```bash
git checkout -b feature/<nueva caracteristica>
```
3. Realiza los cambios y haz un **commit**.  

```bash
git commit -m 'Añadir <nueva característica>'
```
4. Sube los cambios a tu rama.
```bash
git push origin feature/<nueva caracteristica>
```
5. Abre un **pull request** para revisión.  

## Contribuyentes Destacados  

<a href="https://github.com/Meleagrista/udc-seguridad-informatica/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=Meleagrista/udc-seguridad-informatica" />
</a>

</br>

# Contactos
Para cualquier consulta o sugerencia, puedes ponerte en contacto con:  

**Martín do Río Rico** – [mdoriorico@gmail.com](mailto:mdoriorico@gmail.com) 

