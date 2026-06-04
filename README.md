Optimización de Inventario y Prevención de Quiebres de Stock

Este proyecto implementa una solución de analítica avanzada e inteligencia de negocios de extremo a extremo (End-to-End) para el control dinámico de inventarios, automatizando la extracción, transformación y el cálculo estadístico de niveles críticos de reposición.

Arquitectura de la Solución
* **Procesamiento y ETL:** Python (Pandas) dentro de un entorno Jupyter Notebook para la simulación, limpieza y neteo de movimientos logísticos (Entradas/Salidas).
* **Modelado de Datos:** Esquema en Estrella en Power BI unificando tablas de hechos transaccionales (`Ventas` y `Movimientos`) con dimensiones clave (`Productos` y `Calendario`).
* **Lógica de Negocio (DAX Avanzado):** Modelado predictivo y de control basado en el comportamiento de la demanda.

Fórmulas Estadísticas Implementadas (DAX)

 1. Inventario Acumulado Histórico (Stock Actual)
Calcula el neto acumulado de movimientos de inventario en tiempo real para cualquier contexto temporal seleccionado:
```dax
Stock_Actual = 
CALCULATE(
    SUM(Fact_Movimientos_Procesado[NETO_MOVIMIENTOS]),
    FILTER(
        ALL(Dim_Calendario),
        Dim_Calendario[FechaKey] <= MAX(Dim_Calendario[FechaKey])
    )
)

Stock_Seguridad = 
VAR PromedioVentasDiarias = AVERAGE(Fact_Ventas[CANT_VENDIDA])
VAR DesviacionEstandarVentas = STDEV.P(Fact_Ventas[CANT_VENDIDA])
VAR LeadTimeProveedorDias = 7
RETURN
(PromedioVentasDiarias * LeadTimeProveedorDias) + (1.65 * DesviacionEstandarVentas * SQRT(LeadTimeProveedorDias))

Alerta_Stock = 
IF(
    [Stock_Actual] < [Stock_Seguridad], 
    "⚠️ REPOSICIÓN CRÍTICA", 
    "✅ OK"
)

