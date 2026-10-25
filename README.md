# Determinantes del ingreso laboral en México (ENOE 2025-T1)

¿Cuánto influyen la escolaridad, la experiencia, el tipo de empleo o el género en lo que gana un trabajador en México? Este repositorio responde esa pregunta con un **modelo de regresión lineal múltiple** sobre los microdatos de la **Encuesta Nacional de Ocupación y Empleo (ENOE)** del INEGI, primer trimestre de 2025.

El análisis se enfoca en los **trabajadores asalariados** (subordinados que reciben un pago): 81,360 personas en la muestra, que representan a unos 23.5 millones de trabajadores en el país.

## Principales hallazgos

El modelo explica el **27.3% de la variabilidad** del ingreso mensual (R² ajustado = 0.273), y las nueve variables son estadísticamente significativas.

Manteniendo las demás variables constantes:

| Variable                        | Efecto sobre el ingreso mensual                                                                  |
| ------------------------------- | ------------------------------------------------------------------------------------------------ |
| Años de escolaridad             | **+$654** por cada año adicional                                                                 |
| Ser mujer                       | **−$2,000** frente a un hombre con las mismas características                                    |
| Tener prestaciones              | +$1,934                                                                                          |
| Pertenecer a un sindicato       | +$472                                                                                            |
| Tamaño de la empresa            | +$303 por cada categoría de tamaño (≈ $3,000 entre una empresa de 1 persona y una de más de 500) |
| Contrato por escrito            | +$234                                                                                            |
| Horas trabajadas a la semana    | +$78 por hora                                                                                    |
| Experiencia potencial           | +$73 por año                                                                                     |
| Horas de estudio o capacitación | −$27 por hora a la semana                                                                        |

Al comparar los **coeficientes estandarizados**, la escolaridad es por mucho la variable con mayor peso, más del doble que cualquier otra. Le siguen las horas trabajadas, el tamaño de la empresa, la experiencia y las prestaciones. Ser mujer tiene un efecto negativo de magnitud similar a ese segundo grupo: aun con la misma escolaridad, experiencia, horas y tipo de empleo, las mujeres ganan menos.

## Datos

Se usan tres tablas de la ENOE 2025-T1, unidas a nivel persona:

| Tabla    | Contenido                                            | Variables que aportan                                                  |
| -------- | ---------------------------------------------------- | ---------------------------------------------------------------------- |
| **COE1** | Cuestionario de Ocupación y Empleo, preguntas 1 a 5  | Sindicato, contrato, tamaño de empresa, prestaciones, horas trabajadas |
| **COE2** | Cuestionario de Ocupación y Empleo, preguntas 6 a 11 | Horas de estudio o capacitación                                        |
| **SDEM** | Características sociodemográficas                    | Ingreso mensual (`ingocup`), sexo, edad, años de escolaridad           |

Las tablas se unen con la llave `tipo, mes_cal, cd_a, ent, con, v_sel, n_hog, h_mud, n_ren`. La llave de 7 campos que documenta el INEGI tiene duplicados desde que la encuesta combina entrevistas cara a cara y telefónicas.

### Variables del modelo

- **Variable objetivo:** ingreso mensual del trabajo principal (`ingocup`, en pesos).
- **Variables explicativas:**
  - Sindicato, contrato por escrito y prestaciones (binarias).
  - Tamaño de la empresa (ordinal, de 1 a 11).
  - Horas de estudio o capacitación a la semana.
  - Mujer (binaria).
  - Horas trabajadas habitualmente a la semana.
  - Años de escolaridad.
  - Experiencia potencial, calculada con la ecuación de Mincer: `edad − escolaridad − 6`.

### Filtros principales

- Población de 15 a 97 años con ingreso mensual reportado.
- Solo trabajadores subordinados con pago. Las preguntas de sindicato, contrato y prestaciones solo se les hacen a ellos.
- Se eliminan las respuestas "no sabe" o "no especificado" de cada variable.

El notebook explica cada decisión paso a paso.

## Estructura del repositorio

```
├── Tarea5_FernandoHernandez_LuisBeltran.ipynb   # Notebook con todo el análisis
├── Reporte_Tarea5_Regresion_ENOE.pdf            # Reporte con resultados e interpretación
├── Reporte_Tarea5_Regresion_ENOE.docx           # Versión editable del reporte
└── README.md
```

El notebook está organizado en seis partes:

1. Unión de las tablas COE1, COE2 y SDEM.
2. Variable objetivo: ingreso mensual.
3. Variables explicativas y matriz de correlación.
4. Modelo de regresión lineal múltiple.
5. Interpretación del modelo y análisis de residuales.
6. Coeficientes estandarizados.

## Cómo reproducirlo

Los datos **no se incluyen** en el repositorio por su tamaño.

1. Descarga los datos abiertos de la ENOE 2025-T1 en formato CSV desde el [sitio del INEGI](https://www.inegi.org.mx/programas/enoe/15ymas/#datos_abiertos).
2. Descomprime la carpeta `conjunto_de_datos_enoe_2025_1t_csv/` en la raíz del repositorio. Debe contener al menos las carpetas de COE1, COE2 y SDEM:
   ```
   conjunto_de_datos_enoe_2025_1t_csv/
   ├── conjunto_de_datos_coe1_enoe_2025_1t/
   ├── conjunto_de_datos_coe2_enoe_2025_1t/
   └── conjunto_de_datos_sdem_enoe_2025_1t/
   ```
3. Instala las dependencias (Python 3.13):
   ```bash
   pip install pandas numpy matplotlib statsmodels jupyter
   ```
4. Abre el notebook y ejecuta todas las celdas.

## Limitaciones

- El modelo es **descriptivo, no causal**: muestra asociaciones, no efectos causales. Por ejemplo, la brecha por sexo puede reflejar discriminación, pero también diferencias de ocupación que el modelo no mide.
- Los residuales no siguen una distribución normal por la cola larga del ingreso. Modelar el logaritmo del ingreso es el siguiente paso natural.
- La experiencia potencial es una aproximación: no descuenta los años en que la persona no trabajó.
- Los resultados aplican solo a trabajadores asalariados, no a trabajadores por cuenta propia ni a empleadores.

## Autores

**Fernando Hernández** y **Luis Beltrán**

Proyecto desarrollado para la materia _Análisis Estadístico Multivariado_ de la Maestría en Ciencia de Datos del ITESO (2026).
