# Plan de Mejoras — Sistema de Facturación Marvin Tours

---

## 1. Placeholders en los inputs de "Factura para"

### Contexto actual
La sección **"Factura para"** (`div.bill-to`) usa `div[contenteditable="true"]` con texto visible que actúa como label y valor a la vez:

```html
<div contenteditable="true" class="editable">Nombre del cliente</div>
<div contenteditable="true" class="editable">Dirección</div>
<div contenteditable="true" class="editable">Ciudad (Código Postal)</div>
<div contenteditable="true" class="editable">CIF/NIF:</div>
```

Dado que se usan `contenteditable` (no `<input>`), el placeholder debe simularse con CSS y un atributo `data-placeholder`.

### Cambios a realizar

#### HTML — reemplazar los `div` del `bill-to` con atributo `data-placeholder`
```html
<div class="bill-to">
  <h3>Factura para</h3>
  <div contenteditable="true" class="editable placeholder-field" data-placeholder="Nombre del cliente"></div>
  <div contenteditable="true" class="editable placeholder-field" data-placeholder="Dirección"></div>
  <div contenteditable="true" class="editable placeholder-field" data-placeholder="Ciudad (Código Postal)"></div>
  <div contenteditable="true" class="editable placeholder-field" data-placeholder="CIF/NIF:"></div>
</div>
```

#### CSS — simular placeholder con `::before`
```css
.placeholder-field:empty::before {
    content: attr(data-placeholder);
    color: #adb5bd;
    pointer-events: none;
    font-style: italic;
}

.placeholder-field:focus:empty::before {
    color: #ced4da;
}
```

> **Alcance:** Solo afecta a `div.bill-to`. Los campos de `div.bill-from` (Datos de la Empresa) conservan su texto pre-rellenado ya que son datos fijos del emisor.

---

## 2. Mejoras de UX/UI — Responsive y Diseño

### 2.1 Responsive (Mobile-first)

#### Problema actual
- `.billing-section` usa `grid-template-columns: 1fr 1fr` sin breakpoints → columnas se comprimen en móvil.
- `.totals-section` tiene `width: 300px` fijo → desborda en pantallas < 360px.
- El botón de imprimir (`position: fixed`) puede tapar contenido en móvil.
- El encabezado (`header-content`) con `flex` pierde legibilidad en pantallas pequeñas.

#### Breakpoints propuestos

```css
/* Tablet — ≤ 768px */
@media (max-width: 768px) {
    .billing-section {
        grid-template-columns: 1fr;
        gap: 20px;
    }

    .header-content {
        flex-direction: column;
        align-items: flex-start;
    }

    .invoice-title {
        text-align: left;
        width: 100%;
    }

    .totals-section {
        width: 100%;
    }

    .invoice-body {
        padding: 20px;
    }
}

/* Móvil — ≤ 480px */
@media (max-width: 480px) {
    .invoice-header {
        padding: 20px 15px;
    }

    .invoice-title h2 {
        font-size: 26px;
    }

    .services-table th,
    .services-table td {
        padding: 10px 8px;
        font-size: 13px;
    }

    .logo-placeholder {
        width: 140px;
        height: 56px;
    }

    /* Botón de imprimir abajo centrado en móvil */
    #printBtn {
        bottom: 15px;
        top: auto;
        right: 50%;
        transform: translateX(50%);
    }
}
```

---

### 2.2 Mejoras de diseño general

#### Tipografía y jerarquía visual
- Importar **Google Fonts: Inter** (moderna, legible en pantalla y al imprimir).
- Aumentar `line-height` a `1.6` en el body para mejor legibilidad.

#### Colores y tokens CSS
Centralizar colores con variables CSS para facilitar el mantenimiento:

```css
:root {
    --color-primary: #3498db;
    --color-dark: #2c3e50;
    --color-surface: #f8f9fa;
    --color-border: #e9ecef;
    --color-text-muted: #adb5bd;
    --radius-base: 8px;
}
```

#### Tabla de servicios
- Añadir `font-variant-numeric: tabular-nums` en columnas de precios para alineación correcta de decimales.
- Zebra striping suave: `tr:nth-child(even)` con fondo `#fafbfc`.
- Columna "Cantidad" con `width: 80px` fija para evitar que se estire.

#### Sección de totales
- Cambiar `margin-left: auto` + `width: 300px` por `max-width: 320px; width: 100%; margin-left: auto` para que sea fluido.

#### Botón "Añadir servicio"
- Cambiar estilo a línea punteada (`border: 2px dashed var(--color-primary)`) con fondo transparente — patrón UX estándar para "agregar fila".

#### Observaciones
- Aumentar el `padding` interno de `.observations` (actualmente es `2px`, casi nulo).
- Añadir `min-height: 48px` al div editable de observaciones.

#### Accesibilidad básica
- Añadir `role="textbox"` y `aria-label` en los `contenteditable` de "Factura para".
- Asegurar ratio de contraste ≥ 4.5:1 en texto sobre fondo blanco.

#### Feedback de edición
- Añadir `outline` y transición suave al hacer hover sobre campos editables para indicar que son interactivos.

```css
.editable:hover {
    border-color: var(--color-primary);
    background: #fff;
    cursor: text;
}
```

---

## 3. README — Sistema de Facturación Marvin Tours

### Archivo: `README.md`

```markdown
# Marvin Tours — Sistema de Facturación

## ¿Qué es este sistema?

Este es un sistema de facturación web diseñado específicamente para **Marvin Tours**,
una empresa de transporte turístico con sede en Alcalá de Henares, España.

El sistema permite generar, editar e imprimir facturas profesionales directamente
desde el navegador, sin necesidad de instalar software adicional.

## ¿Para qué sirve?

- Emitir facturas a clientes por servicios de transporte turístico.
- Registrar los datos del cliente (razón social, dirección, NIF/CIF).
- Detallar los servicios prestados con precio unitario e importe.
- Calcular subtotales, IVA y total de forma visual.
- Imprimir o exportar la factura como PDF desde el navegador.

## Características principales

- **Edición en línea:** todos los campos son editables directamente en pantalla.
- **Diseño responsivo:** funciona en escritorio, tablet y móvil.
- **Listo para imprimir:** estilos de impresión optimizados para A4.
- **Sin dependencias externas:** HTML, CSS y JavaScript puros.

## Estructura del proyecto

marvin_tours/
├── index.html   # Aplicación principal
├── logo.png     # Logotipo de Marvin Tours
└── README.md    # Este archivo

## Uso

1. Abrir `index.html` en cualquier navegador moderno.
2. Completar los datos del cliente en la sección "Factura para".
3. Añadir los servicios con el botón "+ Añadir servicio".
4. Pulsar "🖨️ Imprimir Factura" para imprimir o guardar como PDF.

## Empresa

**Marvin Tours**
Calle Honduras 19, Bloque 1 piso 3
28806, Alcalá de Henares, España
NIE/NIF/CIF: 05963990K
```

---

## Orden de implementación sugerido

| Prioridad | Tarea | Impacto | Esfuerzo |
|-----------|-------|---------|----------|
| 1 | Placeholders en "Factura para" | Alto | Bajo |
| 2 | Variables CSS + corrección de totales responsivos | Alto | Bajo |
| 3 | Breakpoints responsive (`@media`) | Alto | Medio |
| 4 | Mejoras de tabla (tipografía numérica, zebra) | Medio | Bajo |
| 5 | Feedback hover en campos editables | Medio | Bajo |
| 6 | Tipografía Inter + `line-height` | Medio | Bajo |
| 7 | Crear `README.md` | Bajo | Bajo |
