# Modelo de Optimización de Asignación y Secuenciación de Producción

## 📌 Resumen Ejecutivo
Este proyecto resuelve un problema complejo de asignación de producción y secuenciación de órdenes de trabajo (OT) sobre una red satural de talleres textiles. Mediante **Programación Lineal Entera Mixta (MIP)** implementada en **Python (`PuLP` / Solver `CBC`)**, se optimiza la cobertura de demanda y el valor estratégico de la entrega respetando restricciones estrictas de capacidad física y elegibilidad técnica.

---

## 🎯 1. Diagnóstico Inicial de la Red (Macro-Análisis)
* **Demanda Total:** 787 órdenes/materiales ($3,357,750.60$ minutos requeridos).
* **Oferta Total Instalada:** 211 talleres ($1,948,185.00$ minutos disponibles).
* **Desbalance Estructural:** Ocupación del **172.37%** sobre la capacidad de la red (déficit crítico de capacidad del $41.93\%$).
* **Nivel de Urgencia:** El **96.8%** de las órdenes corresponden a la categoría **Prioridad 3** (Mayor importancia).

---

## 🔬 2. Metodología y Formulación Matemática

El modelo maximiza la función de valor global $Z$:

$$\max Z = \sum_{i} \sum_{j} (\alpha \cdot P_i + \beta \cdot V_j) \cdot X_{ij}$$

### **Parámetros y Pesos Estratégicos:**
* **$\alpha = 0.7$ (Urgencia Comercial):** Prioridad de la orden $P_i \in \{1, 2, 3\}$.
* **$\beta = 0.3$ (Calidad de la Red):** Valor numérico de idoneidad del proveedor $V_j$ (*Confianza = 1.0, Potenciales = 0.8, Oportunidad = 0.5, Acompañamiento = 0.2*).

### **Restricciones del Sistema:**
1. **Unicidad y No-Fraccionamiento:** Cada material se asigna a máximo un taller ($\sum_j X_{ij} \le 1$).
2. **Capacidad Disponible:** La suma de minutos asignados no supera el límite físico del taller ($\sum_i T_i \cdot X_{ij} \le C_j$).
3. **Compatibilidad Técnica:** Matriz de elegibilidad ajustada por línea de producto ($37,344$ variables viables).

---

## 🧠 3. Principales Supuestos Considerados
1. **Relación Orden-Material (1:1):** Cada orden de trabajo contiene un único código de material e información técnica indivisible.
2. **Tratamiento del Historial y Experiencia Previa:** Durante la exploración de datos (EDA) se identificó que el $100\%$ de los materiales de la presente colección son códigos nuevos. Por eficiencia de datos, el término de experiencia por material se evaluó en cero ($E_{ij} = 0$), absorbiendo la preferencia del taller a través de su clasificación comercial ($V_j$).
3. **Ponderación Balanceada:** Se priorizó un peso del 70% a la urgencia de la prenda para responder al *Time to Market* de la colección.

---

## 📊 4. Resultados Clave y Métricas de Desempeño (KPI)

| Métrica / KPI | Valor Alcanzado | Interpretación de Negocio |
| :--- | :---: | :--- |
| **Órdenes Asignadas Exitosamente** | **499 OT (63.41%)** | Se rescataron casi dos tercios de las órdenes totales a pesar del déficit de red. |
| **Horas de Producción Cubiertas** | **23,465.17 hrs** | $1,407,910.31$ minutos de demanda puestos en fabricación. |
| **Atención a Prioridad 3 (Urgentes)** | **490 OT (98.2%)** | Concentración del $98.2\%$ de los cupos en la mercancía más crítica. |
| **Ocupación de Talleres de Confianza**| **96.06%** | Saturation casi total de la red de mayor calidad y menor riesgo. |
| **Uso Efectivo de la Capacidad de Red**| **72.27%** | Optimización del empaquetado de órdenes según límites individuales por taller. |

---

## ⏱️ 5. Secuenciación de Producción (*Time to Market*)
Para definir la cola de fabricación dentro de la planta de cada taller, se aplicó una regla de despacho jerárquica:
1. **Criterio 1:** Orden descendente por **Prioridad Comercial** ($P_3 \to P_2 \to P_1$).
2. **Criterio 2 (Regla SPT - Shortest Processing Time):** Orden ascendente por minutos de proceso dentro del mismo nivel de prioridad.

Esta regla **SPT** acelera el flujo de salida (*Flow Time*), permitiendo disponer de prendas terminadas en el menor tiempo posible.

---

## 🚀 6. Instrucciones de Ejecución
1. Clonar el repositorio.
2. Abrir `prueba_modulo_1_luisa_ochoa.ipynb` en Google Colab o Jupyter Notebook.
3. Asegurarse de tener instaladas las librerías: `pip install pulp pandas numpy matplotlib seaborn`.
4. Ejecutar todas las celdas en secuencia. El solver `CBC` resolverá la instancia en menos de 15 segundos.
