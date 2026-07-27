# 📊 Análisis de Ventas e Insights de Negocio — Power BI & ETL

## 📄 Descripción del Proyecto
Este proyecto aborda el flujo completo de análisis de datos (*End-to-End*) a partir de un conjunto de datos brutos de ventas en formato CSV. Comprende la exploración inicial, la limpieza y transformación intensiva de los datos (**ETL**) mediante **Power Query**, el desarrollo de un **modelo de datos con métricas en DAX**, y finalmente el diseño de **dashboards interactivos en Power BI** para la toma de decisiones estratégicas.

---

## 🛠️ Flujo de Trabajo y Metodología (Pipeline de Datos)

### 1. Exploración Inicial y Diagnóstico
Se analizó el archivo `.csv` bruto para identificar inconsistencias estructurales, tipos de datos incorrectos, fallas de formato y valores atípicos o nulos.

![Vista previa de datos brutos en CSV](https://github.com/user-attachments/assets/b67eb38f-3444-43bf-b6e0-8e2de2d94ddd)

---

### 2. Limpieza y Transformación (ETL en Power Query)
Para garantizar la integridad y calidad de la información, se ejecutaron los siguientes procesos en **Power Query**:

* **Corrección de Tipos de Datos:**  
  * `Fecha_venta`: Transformado de tipo *Texto* a tipo *Fecha*.
  * `Descuento`: Reasignado a tipo *Número Decimal*.
* **Estandarización y Normalización de Textos:**  
  * Corrección de ortografía, tildes y formato (Mayúscula inicial) en nombres y apellidos de vendedores y campos de texto clave.
  * Unificación del formato y granularidad de las fechas.
* **Ingeniería de Características (*Feature Engineering*):**  
  Creación de columnas calculadas clave para facilitar el análisis temporal y financiero:
  * `total_venta`, `descuento aplicado`, `venta neta`.
  * Atributos temporales: `año`, `mes (número)`, `trimestre`, `mes (nombre)`.

![Paso 1 - Transformaciones en Power Query](https://github.com/user-attachments/assets/5338dc0d-e65a-43d1-880d-b93b80b97d58)
![Paso 2 - Modelo de datos transformado](https://github.com/user-attachments/assets/4f11f0d9-8ab0-4f67-bb42-49d74c8c576e)

---

## 📐 DAX

Se estructuraron medidas calculadas en **DAX** para analizar el rendimiento de ventas y la rentabilidad del negocio de forma dinámica:

### 📌 KPIs Generales y Financieros
```dax
// Venta Total Bruta
Total de Venta (valores) = 
SUM(ventas_technostore_sucio[Total_Venta])

// Venta Neta Total (Considerando los descuentos)
Total de Venta Neta (valores) = 
SUM(ventas_technostore_sucio[Venta Neta])

// Ticket Promedio por Transacción
Ticket Promedio = 
DIVIDE(
    SUM('ventas_technostore_sucio'[Venta Neta]), 
    COUNTROWS('ventas_technostore_sucio')
)

// Cantidad Total de Transacciones
Total de Venta (cantidad) = 
CALCULATE(
    COUNTA('ventas_technostore_sucio'[ID_Venta]),
    ALLSELECTED('ventas_technostore_sucio'[ID_Venta])
)
```

### 📦 Identificación de Productos Top (Ranking)
```dax
// Producto con mayor volumen de ventas (en unidades)
Producto Mas Vendido (cantidad) = 
VAR TablaProductos =
    SUMMARIZE(
        'ventas_technostore_sucio',
        'ventas_technostore_sucio'[Producto],
        "TotalUnidades", SUM('ventas_technostore_sucio'[Cantidad])
    )
VAR TopProducto =
    TOPN(1, TablaProductos, [TotalUnidades], DESC)
RETURN
    MAXX(TopProducto, 'ventas_technostore_sucio'[Producto])

// Producto con mayor volumen de ventas (en ingresos/moneda)
Producto Más Vendido (valores) = 
VAR T =
    ADDCOLUMNS(
        SUMMARIZE(ventas_technostore_sucio, ventas_technostore_sucio[Producto]),
        "VentaTotal", [Venta_Neta]
    )
RETURN
    MAXX(
        TOPN(1, T, [VentaTotal], DESC),
        ventas_technostore_sucio[Producto]
    )
```
   
### 🎯 Comparativa vs. Meta
```dax
// Venta Neta del Año 2024
Venta Neta 2024 = 
CALCULATE(
    [Total de Venta Neta (valores)],
    FILTER(
        ALL( ventas_technostore_sucio[Fecha_Venta] ),
        YEAR( ventas_technostore_sucio[Fecha_Venta] ) = 2024
    )
)

// Porcentaje de Crecimiento con respecto a la Meta
Crecimiento Meta % = 
VAR Porcentaje =
    DIVIDE(
        [Venta Neta 2024] - [Meta al año II],
        [Meta al año II]
    )
RETURN
    FORMAT(Porcentaje, "+0.00%;-0.00%")
```
---

## 📈 Dashboards Interactivos en Power BI

El proyecto cuenta con tableros interactivos diseñados para navegar intuitivamente entre métricas macro y análisis detallados a nivel de vendedor, producto y tiempo.

### Vista 1: Resumen Ejecutivo
![Dashboard Vista 1](https://github.com/user-attachments/assets/ea543a00-854a-4d28-affb-84d31883a948)

### Vista 2: Análisis Vendedores
![Dashboard Vista 2](https://github.com/user-attachments/assets/9102b9b1-0988-4a8c-b33b-f723379bb3e4)

### Vista 3: Análisis Geográficos
![Dashboard Vista 3](https://github.com/user-attachments/assets/f861962e-915c-4290-b2d4-62a0d51aa87d)

### Vista 4: Análisis de Productos y Categorías
![Dashboard Vista 4](https://github.com/user-attachments/assets/ee57d6b3-1d5d-4eb4-9d95-b5da14ac8d18)

### Vista 5:Análisis de Comportamiento de Ventas
![Dashboard Vista 5](https://github.com/user-attachments/assets/d47f2c0d-125c-43e5-b9bb-2c4a01e25c28)

---

## 🧰 Herramientas Utilizadas
* **Power BI Desktop:** Modelado de datos, DAX y diseño de paneles interactivos.
* **Power Query:** Extracción, limpieza y transformación de datos (ETL).
* **CSV / Excel:** Origen de datos brutos.


