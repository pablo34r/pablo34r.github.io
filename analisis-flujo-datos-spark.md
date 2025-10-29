---
title: "Análisis de Flujo de Datos Simulado con Spark y Jekyll"
layout: default
---

# 💡 Análisis de Flujo de Datos Simulado con Spark y Jekyll

## 🎯 Objetivo
Aplicar analítica avanzada para procesar un flujo de datos simulado en un contexto empresarial usando Python y Apache Spark, demostrando el poder del procesamiento distribuido y el análisis en tiempo real.

---

## 🛍️ Escenario: Tienda Online y Análisis de Clics
Imaginemos una tienda online que desea analizar en tiempo real los clics de los usuarios dentro de su sitio web.  
El objetivo es detectar patrones de navegación, medir el nivel de interés de los clientes y optimizar campañas publicitarias o recomendaciones de productos.

### 📂 Dataset simulado
Se generó un conjunto de datos con las siguientes columnas:

- Timestamp: Fecha y hora del clic.  
- User_ID: Identificador del usuario.  
- Clicks: Número de clics realizados en esa franja temporal.  

El dataset contiene 1000 registros simulados, representando clics en distintas ventanas de tiempo.

---

## ⚙️ Proceso de Implementación con Spark

### 1️⃣ Configuración del Entorno
Se instaló Apache Spark 3.5.0 en Google Colab y se configuró el entorno de ejecución de Java y las variables SPARK_HOME y JAVA_HOME.

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("ClickstreamAnalysis").getOrCreate()
print("Versión de Spark:", spark.version)
2️⃣ Carga y Exploración de Datos
El archivo CSV se cargó en un DataFrame de Spark:

python
Copiar código
df = spark.read.csv("clickstream_data.csv", header=True, inferSchema=True)
df.show(5)
Esto permite trabajar con grandes volúmenes de datos de manera eficiente.

3️⃣ Procesamiento del Flujo
Se simuló un flujo de clics usando ventanas de 1 minuto, agrupando los clics por usuario:

python
Copiar código
from pyspark.sql.functions import sum

clicks_per_user = df.groupBy("User_ID").agg(sum("Clicks").alias("Total_Clicks"))
clicks_per_user.show()
El procesamiento en ventanas es una de las características que diferencia a Spark Streaming del procesamiento por lotes, ya que permite analizar los datos en tiempo real.

4️⃣ Visualización
Con los datos procesados, se creó una gráfica de barras mostrando los clics por usuario utilizando matplotlib:

python
Copiar código
import matplotlib.pyplot as plt

pdf = clicks_per_user.toPandas()
plt.bar(pdf["User_ID"], pdf["Total_Clicks"])
plt.xlabel("Usuarios")
plt.ylabel("Clics Totales")
plt.title("Clics por Usuario en Ventanas de 1 Minuto")
plt.show()

📊 Interpretación Analítica
El análisis muestra que algunos usuarios presentan una cantidad de clics significativamente mayor que otros.
Esto puede indicar:

Mayor nivel de interacción o interés en los productos.

Posibles usuarios recurrentes o fieles.

Oportunidad de aplicar estrategias de marketing personalizado para retenerlos.

Además, los usuarios con poca actividad pueden representar visitantes ocasionales o clientes potenciales que necesitan incentivos adicionales para completar una compra.

🧱 Arquitectura y Despliegue del Blog
🧩 Arquitectura
El proyecto se estructura de la siguiente forma:

Google Colab + PySpark: procesamiento y visualización de los datos.

Jekyll: generación del blog estático.

GitHub Pages: alojamiento gratuito en la nube.

Markdown (.md): formato usado para documentar el proceso y resultados.

🚀 Despliegue del Blog
Se creó el repositorio pablo34r.github.io en GitHub.

Se activó GitHub Pages y se seleccionó el tema Cayman.

Este archivo Markdown se colocó en la raíz del repositorio.

Se guardaron los gráficos generados (.png) en /assets/images/.

El blog se actualiza automáticamente cada vez que se realiza un push al repositorio.

🔄 Diferencias entre Streaming y Procesamiento por Lotes
Procesamiento por Lotes	Procesamiento en Streaming
Analiza datos acumulados y almacenados.	Analiza datos en tiempo real mientras llegan.
Mayor latencia.	Baja latencia y decisiones inmediatas.
Ejemplo: ventas diarias.	Ejemplo: clics por segundo.

El streaming permite detectar comportamientos instantáneos, lo cual es esencial en 2025 para empresas que buscan reaccionar de forma ágil ante sus clientes.

🧠 Cierre y Reflexión
Este ejercicio permitió aplicar analítica avanzada con Spark, demostrando cómo el procesamiento en streaming puede aportar valor inmediato a un negocio digital.
El uso de herramientas como Spark, Python y Jekyll facilita la integración de la analítica con la publicación de resultados en la web.

© 2025 - Blog de Pablo Roncancio
