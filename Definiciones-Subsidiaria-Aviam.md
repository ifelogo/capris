# DEFINICIONES DE SUBSIDIARIA: AVIAM
## Proyecto Esfera – Grupo Capris
### Serie Documentos Ágiles v2 – Especificaciones de Integración

---

## Historial de Cambios

| VERSIÓN | RESPONSABLES | NOTAS |
|---------|-------------|-------|
| 1 | Copilot / Equipo Proyecto Esfera | Versión inicial del documento de subsidiaria Aviam |

---

## 1. IDENTIFICACIÓN DE LA SUBSIDIARIA

| Atributo | Valor |
|---------|-------|
| **Nombre completo** | Aviam |
| **Línea de Negocio** | Técnica |
| **País** | Costa Rica |
| **Moneda principal** | CRC (Colón Costarricense) |
| **Código Capris (BDI / aplicaciones corporativas)** | `AVI` |
| **ID interno NetSuite** | *(por confirmar con equipo NetSuite)* |

> **Nota**: El código `AVI` sigue el patrón de 3 caracteres usado por las demás subsidiarias (ej. `LAB` para LabTech, `GUA` para Diagnóstika Guatemala). Confirmar con el área de TI Capris que este código coincide exactamente con el configurado en las aplicaciones corporativas.

---

## 2. CONFIGURACIÓN DE LOOKUPS EN OIC

Al agregar Aviam a la EAI, se deben actualizar los siguientes **Lookups** en Oracle Integration Cloud (OIC). Cada Lookup tiene el sufijo `_MAP` y sigue la estructura `CAPRIS_DOM / NETSUITE_DOM / BUSINESS_DOM`.

### 2.1 Lookup de Subsidiarias (`SUBSIDIARIA_MAP`)

Agregar la siguiente fila:

| CAPRIS_DOM | NETSUITE_DOM | BUSINESS_DOM |
|-----------|-------------|-------------|
| `AVI` | *(ID interno NetSuite de Aviam – por confirmar)* | `Aviam Costa Rica` |

> **Referencia**: Sección 9 del documento corporativo – componente `_SERVICE` de cada integración usa este lookup para traducir el código de subsidiaria Capris al ID interno NetSuite.

### 2.2 Lookup de Monedas (`MONEDA_MAP`)

Confirmar que la moneda CRC ya está mapeada en OIC. Si no existe, agregar:

| CAPRIS_DOM | NETSUITE_DOM | BUSINESS_DOM |
|-----------|-------------|-------------|
| `CRC` | *(ID interno NetSuite para CRC)* | `Colón Costarricense` |

### 2.3 Lookup de Departamentos LEGADMI → NetSuite (`DEPTO_MAP`)

Los departamentos deben alinearse con la estructura contable de Aviam en NetSuite. Completar la siguiente tabla con los departamentos de nómina de Aviam configurados en LEGADMI y sus equivalentes en NetSuite:

| CAPRIS_DOM (código LEGADMI) | NETSUITE_DOM (ID NetSuite) | BUSINESS_DOM (descripción) |
|----------------------------|--------------------------|--------------------------|
| *(código depto 1 LEGADMI)* | *(ID NetSuite)* | *(nombre departamento)* |
| *(código depto 2 LEGADMI)* | *(ID NetSuite)* | *(nombre departamento)* |
| … | … | … |

### 2.4 Lookup de Tipos de Movimientos CXC (`TIPO_MOV_CXC_MAP`)

Mapear los tipos de movimientos de Cuentas por Cobrar de las aplicaciones Capris a los tipos de documentos AR en NetSuite. Completar según la configuración de Aviam:

| CAPRIS_DOM | NETSUITE_DOM | BUSINESS_DOM |
|-----------|-------------|-------------|
| *(tipo mov. Capris)* | *(tipo doc. AR NetSuite)* | *(descripción)* |

---

## 3. INTEGRACIONES DE DATOS MAESTROS

### 3.1 Maestro de Artículos

Aplican todas las reglas corporativas definidas en la Sección 4 del documento principal. No se identifican excepciones subsidiaria-específicas para Aviam.

- **Prefijo en número de artículo**: Los artículos se identifican con el campo `ART_COD_ORG = AVI`.
- **Categorización ABC**: Confirmar con el equipo Aviam si la clasificación ABC coincide con la definición corporativa.

### 3.2 Maestro de Clientes

Aplican todas las reglas corporativas. El campo `CLI_COD_ORG` enviará `AVI` para identificar los clientes de esta subsidiaria.

- **Régimen contribuyente**: Costa Rica utiliza régimen fiscal propio. Confirmar con equipo fiscal Capris los códigos de régimen contribuyente válidos para Aviam.
- **Identificación fiscal**: Formato de cédula jurídica costarricense (ej. `3-101-XXXXXX`).

### 3.3 Maestro de Bodegas

- **Código de bodega**: Sigue el patrón `AVI@<CODIGO_BODEGA>` (ej. `AVI@PRINCIPAL`).
- **Nombre en NetSuite**: Formato `AVIPRIN – AVIAM PRINCIPAL` (o nombre corto acordado).
- Completar el catálogo de bodegas de Aviam:

| Código Capris | Nombre NetSuite | Tipo | Bodega Padre |
|--------------|----------------|------|-------------|
| `PRINCIPAL` | `AVIPRIN – AVIAM PRINCIPAL` | *(tipo)* | *(si aplica)* |
| … | … | … | … |

### 3.4 Maestro de Proyectos / Licitaciones

Aplican reglas corporativas. El campo `LIC_COD_ORG = AVI`.

---

## 4. INTEGRACIONES DE INVENTARIO

Aplican todas las reglas corporativas definidas en la Sección 5 del documento principal.

- **Prefijo en documentos**: Todos los documentos de inventario de Aviam llevan el prefijo `AVI` para garantizar unicidad en NetSuite (ej. `AVIRECEPCION-001`).
- **Bodegas habilitadas**: Ver tabla de bodegas en sección 3.3.

No se identifican excepciones subsidiaria-específicas para Aviam en esta etapa. Confirmar con el equipo funcional si aplican reglas especiales de desalmacenaje o transferencias.

---

## 5. INTEGRACIONES DE COMPRAS

Aplican todas las reglas corporativas definidas en la Sección 6 del documento principal.

- **Prefijo en número de OC**: `AVI<numero>-0` (ej. `AVI1335-0`).
- **Prefijo en facturas de compra**: `AVI<numero>`.

---

## 6. INTEGRACIONES DE VENTAS Y CXC

Aplican todas las reglas corporativas definidas en la Sección 7 del documento principal.

- **Prefijo en documentos de venta**: `AVIFVIM-<numero>` (facturas de venta), `AVI<tipo>-<numero>` para otros documentos.
- **Puntos de facturación (`EMV_PTOFACT`)**: Completar con los códigos de punto de facturación de Aviam:

| Código Punto de Facturación Capris | Descripción |
|-----------------------------------|-------------|
| *(código)* | *(descripción)* |

---

## 7. INTEGRACIONES DE FINANZAS

### 7.1 LEGADMI – Póliza de Nómina

- **Prefijo del archivo CSV**: `AVI`
- **Formato del nombre de archivo**: `AVI<MMYY>_LEGADMI.CSV`
  - Ejemplo: `AVI0425_LEGADMI.CSV` (póliza de nómina de Aviam del mes de Abril de 2025)
- Una vez confirmado, informar al equipo técnico vía canales de soporte para ajustar la integración.

### 7.2 Integración Bancaria BAC

Confirmar si Aviam opera con BAC Costa Rica para los pagos. De ser así, aplican las reglas de la Sección 8 del documento corporativo.

| Atributo | Valor |
|---------|-------|
| Banco | *(por confirmar)* |
| Número de cuenta | *(por confirmar – datos sensibles, no incluir en doc)* |
| Moneda cuenta | CRC / USD |

---

## 8. CONFIGURACIÓN DE NETSUITE

Los siguientes elementos deben ser configurados en NetSuite antes de activar las integraciones de Aviam:

| Ítem | Estado | Responsable |
|-----|--------|-------------|
| Crear subsidiaria Aviam en NetSuite | ⬜ Pendiente | Equipo NetSuite |
| Configurar moneda CRC en subsidiaria | ⬜ Pendiente | Equipo NetSuite |
| Crear plan de cuentas para Aviam | ⬜ Pendiente | Equipo Finanzas Aviam |
| Configurar departamentos en NetSuite | ⬜ Pendiente | Equipo NetSuite |
| Configurar ubicaciones/bodegas en NetSuite | ⬜ Pendiente | Equipo NetSuite |
| Configurar proveedores de tesorería | ⬜ Pendiente | Equipo Finanzas Aviam |
| Obtener ID interno de Aviam en NetSuite | ⬜ Pendiente | Equipo NetSuite |

---

## 9. ACTIVACIÓN EN OIC

Una vez completa la configuración de NetSuite, realizar los siguientes pasos en OIC:

| Ítem | Estado | Responsable |
|-----|--------|-------------|
| Actualizar `SUBSIDIARIA_MAP` con código `AVI` | ⬜ Pendiente | Equipo Técnico EAI |
| Actualizar `MONEDA_MAP` si CRC no existe | ⬜ Pendiente | Equipo Técnico EAI |
| Actualizar `DEPTO_MAP` con departamentos Aviam | ⬜ Pendiente | Equipo Técnico EAI |
| Actualizar `TIPO_MOV_CXC_MAP` con tipos CXC Aviam | ⬜ Pendiente | Equipo Técnico EAI |
| Activar scheduler para todas las integraciones de Aviam | ⬜ Pendiente | Equipo Técnico EAI |
| Ejecutar prueba de integración en Sandbox | ⬜ Pendiente | Equipo QA |
| Validar datos en NetSuite Sandbox | ⬜ Pendiente | Equipo Funcional Aviam |
| Promover a Producción | ⬜ Pendiente | Equipo Técnico EAI |

---

## 10. APROBACIONES

| VERSIÓN | NOMBRE / EMPRESA / CARGO | FIRMA Y FECHA |
|---------|-------------------------|--------------|
| 1 | *(Equipo Funcional Aviam / Capris)* | |
| 1 | *(Gerente de Proyecto Esfera / Capris)* | |
| 1 | *(Líder Técnico / Sol4IT)* | |

---

*Definiciones de Subsidiaria para Grupo Capris – Serie Documentos Ágiles / Arquitectura de Integración*
