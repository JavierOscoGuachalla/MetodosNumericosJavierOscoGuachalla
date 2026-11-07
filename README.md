# Hidroponía eficiente: consumo y reposición de soluciones nutritivas en huertos urbanos

**Materia:** Métodos Numéricos · Desafío individual
**Autor:** [Tu nombre completo]
**Fecha de entrega:** 6 de noviembre de 2026

**Aplicación web:** https://javieroscoguachalla.github.io/MetodosNumericosJavierOscoGuachalla/
**Video explicativo (1 min):** (https://www.youtube.com/watch?v=1oaPLkUaCgo)

---

## 1. Descripción del desafío

Los huertos urbanos hidropónicos cultivan plantas en agua con nutrientes disueltos. A medida que las plantas crecen, absorben esos nutrientes y su concentración en el depósito baja. Si se repone tarde, el cultivo sufre; si se repone temprano o en exceso, se desperdicia fertilizante.

**Comunidad beneficiaria:** personas que mantienen huertos urbanos y pequeños productores, donde el ahorro de recursos importa.

**Pregunta del desafío:** ¿cómo podemos usar ecuaciones diferenciales y métodos numéricos para estimar el consumo de nutrientes en un sistema hidropónico, determinar cuándo reponerlos y reducir el desperdicio de solución nutritiva?

**Meta cuantificable:** calcular el día en que la concentración cae a un valor mínimo y los gramos de fertilizante necesarios para volver a la concentración objetivo.

## 2. Modelo matemático

| Símbolo | Significado | Unidad |
|---|---|---|
| `t` | Tiempo (variable independiente) | días |
| `C(t)` | Concentración de nutrientes (variable dependiente) | mg/L |
| `C₀` | Concentración inicial | mg/L |
| `k` | Tasa de consumo | 1/día |
| `C_mín` | Concentración mínima aceptable | mg/L |
| `C_obj` | Concentración objetivo tras reponer | mg/L |
| `V` | Volumen del depósito | L |

**Ecuación diferencial con condición inicial:**

```
dC/dt = −k·C ,   C(0) = C₀
```

**Supuestos:**
- Las plantas absorben nutrientes a un ritmo proporcional a la concentración disponible.
- Se simula un solo nutriente agregado (concentración total).
- No hay evaporación, reposición ni cambios de volumen durante el intervalo de simulación.
- Los parámetros son **hipotéticos**; la simulación no es una predicción comprobada con datos reales.

**Intervalo de simulación:** `t ∈ [0, T]`, con `T` ajustable (por defecto 20 días).

## 3. Solución analítica

Por separación de variables:

1. `dC / C = −k dt`
2. `∫ dC/C = ∫ −k dt`
3. `ln|C| = −k·t + c₁`
4. `C(t) = A·e^(−kt)`, con `A = e^(c₁)`
5. Con `C(0) = C₀` se obtiene `A = C₀`.

```
C(t) = C₀ · e^(−k t)
```

**Verificación por sustitución:**
- Ecuación: `C′(t) = −k·C₀·e^(−kt) = −k·C(t)` ✔
- Condición inicial: `C(0) = C₀·e⁰ = C₀` ✔

**Resultados derivados:**
- Tiempo de reposición (cuando `C = C_mín`): `t* = ln(C₀ / C_mín) / k`
- Fertilizante necesario: `m (g) = (C_obj − C_actual) · V / 1000`

## 4. Métodos numéricos

Con `f(t, C) = −k·C` y paso `h`, ambos métodos usan los mismos parámetros, condición inicial, intervalo y `h`.

**Heun (Runge-Kutta de orden 2)**

```
k₁ = f(tₙ, Cₙ)
C* = Cₙ + h·k₁
k₂ = f(tₙ₊₁, C*)
Cₙ₊₁ = Cₙ + (h/2)·(k₁ + k₂)
```

**Runge-Kutta clásico de orden 4 (RK4)**

```
k₁ = f(tₙ, Cₙ)
k₂ = f(tₙ + h/2, Cₙ + (h/2)·k₁)
k₃ = f(tₙ + h/2, Cₙ + (h/2)·k₂)
k₄ = f(tₙ + h,   Cₙ + h·k₃)
Cₙ₊₁ = Cₙ + (h/6)·(k₁ + 2k₂ + 2k₃ + k₄)
```

**Errores calculados:**
- Error absoluto: `|C_exacta − C_numérica|`
- Error relativo porcentual: `|C_exacta − C_numérica| / |C_exacta| · 100`
- Error máximo sobre todo el intervalo, con `h` y con `h/2`.

El error global de Heun es de orden `h²` y el de RK4 de orden `h⁴`, por lo que al reducir el paso RK4 mejora mucho más rápido.

## 5. Qué hace la aplicación

- Permite ajustar `C₀`, `k`, `T`, `h`, `C_mín`, `C_obj` y `V`, con unidades visibles.
- Valida las entradas: números mayores que 0, `h ≤ T`, máximo de 5000 pasos, `C_mín < C₀`, `C_obj ≥ C_mín` y estabilidad (`k·h ≤ 2`).
- Ejecuta Heun y RK4 y calcula la solución analítica.
- Muestra una gráfica con las tres curvas y otra con el error absoluto por paso.
- Muestra la tabla por pasos con errores absolutos y relativos.
- Compara dos tamaños de paso (`h` y `h/2`).
- Incluye el procedimiento de los métodos y el primer paso calculado con los datos ingresados.
- Genera una interpretación automática: día de reposición y método con menor error.
- Incluye una calculadora de reposición de fertilizante.
- Se adapta a pantallas pequeñas y al modo claro u oscuro.

## 6. Valores por defecto y ejemplo de resultados

`C₀ = 1500 mg/L`, `k = 0.12 1/día`, `T = 20 días`, `h = 2 días`, `C_mín = 800 mg/L`, `C_obj = 1500 mg/L`, `V = 40 L`.

| Resultado | Valor aproximado |
|---|---|
| Primer paso, Heun (`C(2)`) | 1183.2 mg/L |
| Primer paso, RK4 (`C(2)`) | 1179.95 mg/L |
| Primer paso, exacta (`C(2)`) | 1179.94 mg/L |
| Día de reposición (`C = 800`) | 5.24 días |
| Fertilizante para volver a 1500 mg/L en 40 L | 28 g |

## 7. Cómo ejecutar el proyecto

La aplicación es un único archivo HTML, sin dependencias ni instalación.

**Opción A: en línea.** Abre la URL de GitHub Pages indicada arriba.

**Opción B: en local.**
1. Clona el repositorio: `git clone [URL del repositorio]`
2. Abre `index.html` en cualquier navegador moderno (doble clic).

**Publicar con GitHub Pages:** en el repositorio ve a *Settings → Pages*, elige la rama `main` y la carpeta `/ (root)`, y guarda.

## 8. Estructura del repositorio

```
.
├── index.html   # aplicación completa (HTML, CSS y JavaScript)
└── README.md    # descripción del modelo e instrucciones
```

## 9. Limitaciones

- El consumo proporcional a la concentración es una simplificación; en la práctica depende del tipo de planta, su etapa de crecimiento, la temperatura y la luz.
- No se modelan evaporación, pH, conductividad eléctrica ni la absorción diferenciada de cada nutriente.
- Para uso real habría que estimar `k` con mediciones (por ejemplo, con un medidor de conductividad eléctrica).

## 10. Conclusión

En los ensayos, RK4 produjo un error menor que Heun con el mismo paso, y reducir `h` a la mitad disminuyó el error en ambos métodos. El modelo permite estimar cuándo reponer y cuánto fertilizante añadir, siempre como una simulación con parámetros hipotéticos.
