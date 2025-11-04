---
title: Análisis de Flujo de Datos Simulado con Spark y Jekyll
layout: default
---

<div style="text-align: center; margin: 40px 0;">
  <h1 style="font-size: 2.5em; color: #2c3e50; margin-bottom: 10px;">📊 Hecho por: Pablo Roncancio</h1>
  <h2 style="font-size: 1.5em; color: #7f8c8d; font-weight: 300;">En Apache Spark y Jekyll</h2>
  <hr style="width: 60%; margin: 30px auto; border: 2px solid #3498db;">
</div>

---

## 🎯 Objetivo

Aplicar **analítica avanzada** para procesar un flujo de datos simulado en un contexto empresarial usando **Python** y **Spark**, demostrando el poder del procesamiento distribuido y el análisis en tiempo real.

---

## 🛒 Escenario: Tienda Online y Análisis de Clics

<div style="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); padding: 30px; border-radius: 15px; color: white; margin: 30px 0;">

Imaginemos una **tienda online** que desea analizar en tiempo real los clics de los usuarios dentro de su sitio web.

**El objetivo es:**
- 🔍 Detectar patrones de navegación
- 📈 Medir el nivel de interés de los clientes
- 🎯 Optimizar campañas publicitarias
- 💡 Mejorar recomendaciones de productos

</div>

### 📋 Estructura del Dataset

Para simular este escenario, se generó un conjunto de datos con las siguientes columnas:

| Campo | Descripción |
|-------|-------------|
| **Timestamp** | Fecha y hora del clic |
| **User_ID** | Identificador único del usuario |
| **Clicks** | Número de clics en esa franja temporal |

> 📊 **Total de registros:** 1000 clics simulados en distintas ventanas de tiempo

---

## ⚙️ Proceso de Implementación con Spark

### **1️⃣ Configuración del Entorno**

<div style="background-color: #f8f9fa; padding: 20px; border-left: 4px solid #3498db; margin: 20px 0;">

Se instaló **Apache Spark 3.5.0** en Google Colab y se configuró el entorno de ejecución de Java y las variables `SPARK_HOME` y `JAVA_HOME`.

</div>

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("ClickstreamAnalysis").getOrCreate()
print("Versión de Spark:", spark.version)
```

---

### **2️⃣ Carga y Exploración de Datos**

El archivo CSV se cargó en un **DataFrame de Spark** para trabajar con grandes volúmenes de datos de manera eficiente:

```python
df = spark.read.csv("clickstream_data.csv", header=True, inferSchema=True)
df.show(5)
```

<div style="background-color: #e8f5e9; padding: 15px; border-radius: 8px; margin: 20px 0;">
✅ <strong>Ventaja:</strong> Spark permite procesar datasets que no cabrían en memoria usando un solo equipo.
</div>

---

### **3️⃣ Procesamiento del Flujo**

Se simuló un flujo de clics usando **ventanas de 1 minuto**, agrupando los clics por usuario:

```python
from pyspark.sql.functions import window, col, sum

clicks_per_user = df.groupBy("User_ID").agg(sum("Clicks").alias("Total_Clicks"))
clicks_per_user.show()
```

<div style="background: linear-gradient(to right, #ff6b6b, #ee5a6f); padding: 20px; border-radius: 10px; color: white; margin: 20px 0;">
⚡ <strong>Spark Streaming:</strong> El procesamiento en ventanas diferencia al streaming del procesamiento por lotes, permitiendo analizar datos en tiempo real.
</div>

---

### **4️⃣ Visualización de Resultados**

Con los datos procesados, se creó una **gráfica de barras** mostrando los clics por usuario utilizando `matplotlib`:

```python
import matplotlib.pyplot as plt

pdf = clicks_per_user.toPandas()
plt.bar(pdf["User_ID"], pdf["Total_Clicks"])
plt.xlabel("Usuarios")
plt.ylabel("Clics Totales")
plt.title("Clics por Usuario en Ventanas de 1 Minuto")
plt.show()
```

<div style="text-align: center; margin: 30px 0;">
  <img src="/grafico-clics.png" alt="Gráfico de clics" style="max-width: 100%; border-radius: 10px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">
  <p style="color: #7f8c8d; font-style: italic; margin-top: 10px;">Figura 1: Distribución de clics por usuario</p>
</div>

---

## 💡 Interpretación Analítica

<div style="display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin: 30px 0;">

<div style="background-color: #fff3cd; padding: 20px; border-radius: 10px; border-left: 5px solid #ffc107;">
<h3 style="margin-top: 0;">👥 Usuarios Activos</h3>
Algunos usuarios presentan <strong>clics significativamente mayores</strong>, lo que indica:
<ul>
<li>Mayor interacción con productos</li>
<li>Usuarios recurrentes o fieles</li>
<li>Oportunidad para marketing personalizado</li>
</ul>
</div>

<div style="background-color: #d1ecf1; padding: 20px; border-radius: 10px; border-left: 5px solid #17a2b8;">
<h3 style="margin-top: 0;">🎯 Usuarios Pasivos</h3>
Los usuarios con <strong>poca actividad</strong> pueden representar:
<ul>
<li>Visitantes ocasionales</li>
<li>Clientes potenciales</li>
<li>Necesidad de incentivos para conversión</li>
</ul>
</div>

</div>

---

## 🏗️ Arquitectura y Despliegue del Blog

### **Arquitectura del Proyecto**

<div style="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); padding: 30px; border-radius: 15px; color: white; margin: 30px 0;">

```
┌─────────────────────────────────────────┐
│   Google Colab + PySpark                │
│   Procesamiento y Visualización         │
└───────────────┬─────────────────────────┘
                │
                ▼
┌─────────────────────────────────────────┐
│   Jekyll                                 │
│   Generación del Blog Estático          │
└───────────────┬─────────────────────────┘
                │
                ▼
┌─────────────────────────────────────────┐
│   GitHub Pages                           │
│   Alojamiento en la Nube (Gratuito)     │
└─────────────────────────────────────────┘
```

</div>

### **Pasos de Despliegue**

1. **Instalación:** Jekyll localmente o mediante GitHub Pages (`<usuario>.github.io`)
2. **Tema:** Cayman - diseño limpio y adaptativo
3. **Contenido:** Archivo Markdown en `_posts/` o raíz
4. **Assets:** Gráficos `.png` guardados en `/assets/images/`
5. **Publicación:** Automática mediante `git push`

---

## ⚡ Streaming vs Procesamiento por Lotes

<table style="width: 100%; border-collapse: collapse; margin: 30px 0; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
  <thead>
    <tr style="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white;">
      <th style="padding: 15px; text-align: left;">📦 Procesamiento por Lotes</th>
      <th style="padding: 15px; text-align: left;">⚡ Procesamiento en Streaming</th>
    </tr>
  </thead>
  <tbody>
    <tr style="background-color: #f8f9fa;">
      <td style="padding: 15px;">Analiza datos acumulados y almacenados</td>
      <td style="padding: 15px;">Analiza datos en tiempo real mientras llegan</td>
    </tr>
    <tr>
      <td style="padding: 15px;">Mayor latencia</td>
      <td style="padding: 15px;">Baja latencia y decisiones inmediatas</td>
    </tr>
    <tr style="background-color: #f8f9fa;">
      <td style="padding: 15px;">📊 <strong>Ejemplo:</strong> Análisis de ventas diarias</td>
      <td style="padding: 15px;">🔥 <strong>Ejemplo:</strong> Monitoreo de clics por segundo</td>
    </tr>
  </tbody>
</table>

<div style="background-color: #d4edda; padding: 20px; border-radius: 10px; border-left: 5px solid #28a745; margin: 20px 0;">
✨ <strong>Insight 2025:</strong> El streaming permite detectar comportamientos instantáneos, esencial para empresas que buscan reaccionar de forma ágil ante sus clientes.
</div>

---

## 🎓 Cierre y Reflexión

<div style="background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%); padding: 40px; border-radius: 15px; color: white; text-align: center; margin: 40px 0;">

Este ejercicio permitió aplicar **analítica avanzada con Spark**, demostrando cómo el procesamiento en streaming puede aportar **valor inmediato** a un negocio digital.

<p style="margin-top: 20px; font-size: 1.1em;">
El uso de herramientas como <strong>Spark</strong>, <strong>Python</strong> y <strong>Jekyll</strong> facilita la integración de la analítica con la publicación de resultados en la web.
</p>

</div>

---

<div style="text-align: center; color: #7f8c8d; margin: 50px 0 20px 0; padding-top: 30px; border-top: 2px solid #ecf0f1;">
  <p style="font-size: 0.9em;">© 2025 - Blog de <strong>Pablo Roncancio</strong></p>
  <p style="font-size: 0.8em; margin-top: 10px;">Construido con ❤️ usando Spark, Python y Jekyll</p>
</div>
