# Opening Range Breakout (ORB) - Implementación de Referencia

Este repositorio contiene una implementación de ejemplo de la estrategia **Opening Range Breakout (ORB)** con filtro de volumen para trading algorítmico de futuros.

## Estrategia: Opening Range Breakout con Filtro de Volumen

### Tesis de la Estrategia

Los primeros 30 minutos del Regular Trading Hours (RTH) establecen el rango de negociación inicial del día. Las rupturas de este rango acompañadas de volumen alto tienden a continuar en la dirección de la ruptura debido al posicionamiento institucional temprano y la participación de algoritmos institucionales.

### Fundamento Estadístico

- **70%** de los días de tendencia rompen el rango de apertura en los primeros 90 minutos
- Las rupturas con volumen **>150%** del promedio tienen **65%** de probabilidad de continuación
- Mayor efectividad en instrumentos con alta liquidez (ES, NQ, YM)

### Parámetros de la Estrategia

```
Duración del rango de apertura: 30 minutos (9:30-10:00 AM ET)
Multiplicador de volumen: 1.5x
Stop loss: 0.5% del precio de entrada
Take profit: Ratio 2:1 (riesgo-beneficio)
Tamaño de posición: 1 microcontrato
Horario de operación: 10:00 AM - 3:30 PM ET
```

### Lógica de Implementación

#### 1. Inicialización (9:30 AM ET)
- Calcular volumen promedio de los últimos 20 días para los primeros 30 minutos
- Inicializar variables del rango de apertura

#### 2. Construcción del Rango (9:30-10:00 AM)
- Registrar el máximo y mínimo del período
- Acumular volumen del período de construcción

#### 3. Detección de Señales (10:00 AM - 3:30 PM)
**Señal de Compra:**
- Precio > máximo del rango de apertura
- Volumen acumulado > 1.5x el promedio histórico
- No hay posición abierta

**Señal de Venta:**
- Precio < mínimo del rango de apertura  
- Volumen acumulado > 1.5x el promedio histórico
- No hay posición abierta

#### 4. Gestión de Posiciones
- **Stop Loss:** Extremo opuesto del rango de apertura
- **Take Profit:** 2x la distancia del stop loss
- **Trailing Stop:** Mover a breakeven al alcanzar 50% del objetivo
- **Cierre forzado:** 3:50 PM ET si la posición sigue abierta

### Mitigación de Riesgos

1. **Stop loss estructural** basado en el rango de apertura
2. **Filtro de volumen** para evitar falsas rupturas en mercados sin convicción
3. **Límite de operaciones:** máximo 2 trades por día
4. **Límite de pérdida diaria:** $50 USD
5. **Exclusión de horarios volátiles:** no operar en los últimos 30 minutos del RTH

### Estructura del Proyecto

```
/
├── src/
│   ├── strategy/
│   │   ├── orb_strategy.py      # Lógica principal de la estrategia
│   │   ├── risk_manager.py      # Gestión de riesgo y posiciones
│   │   └── market_data.py       # Procesamiento de datos de mercado
│   ├── utils/
│   │   ├── time_utils.py        # Utilidades de horarios de mercado
│   │   └── volume_calculator.py # Cálculos de volumen histórico
│   └── backtest/
│       ├── backtester.py        # Motor de backtesting
│       └── performance.py       # Métricas de rendimiento
├── tests/
├── data/                        # Datos históricos de ejemplo
├── config/
│   └── strategy_config.json     # Configuración de parámetros
└── examples/
    └── run_backtest.py          # Ejemplo de ejecución

```

### Instalación y Uso

```bash
# Clonar el repositorio
git clone <repository-url>
cd ORB-Momentum

# Instalar dependencias
pip install -r requirements.txt

# Ejecutar ejemplo de backtesting
python examples/run_backtest.py
```

### Consideraciones de Implementación

Este es un **ejemplo educativo** que demuestra:
- Arquitectura modular para estrategias algorítmicas
- Gestión adecuada del riesgo
- Separación de responsabilidades (datos, estrategia, ejecución)
- Código testeable y mantenible

### Disclaimer

Esta implementación es únicamente para fines educativos y de investigación. No constituye asesoramiento de inversión. El trading de futuros conlleva riesgos significativos y puede resultar en pérdidas substanciales.

### Próximos Pasos

Para adaptar esta estrategia a un entorno de producción, considerar:
- Integración con broker real (Interactive Brokers, TD Ameritrade, etc.)
- Manejo de reconexiones y fallos de red
- Logging y monitoreo de operaciones
- Optimización de parámetros mediante walk-forward analysis
- Implementación de múltiples timeframes y filtros adicionales