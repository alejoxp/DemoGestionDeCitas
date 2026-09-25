# OdontoCare: definición funcional y división modular

## 1. Propósito

OdontoCare es un sistema de gestión para una clínica odontológica. Debe ayudar al personal a administrar pacientes, citas, atención clínica, tratamientos, precios y pagos desde un panel web adaptable a móvil y escritorio. La interfaz conserva la identidad visual Frutiger Aero de la demo: superficies translúcidas, tonos aqua y cielo, fondos luminosos e iconografía clara.

El sistema organiza y registra información. No sustituye el criterio del odontólogo, no diagnostica y no debe presentar una cita reservada como tratamiento ya realizado ni un pago pendiente como dinero recibido.

## 2. Situación actual de la demo

El archivo `demoOdontologia.html` contiene una demostración funcional de estos flujos:

- Selección entre tres especialistas con arancel de consulta distinto.
- Calendario, selección de horario y reserva de cita.
- Ficha de paciente con edad, documento, teléfono y antecedentes básicos.
- Directorio de pacientes con búsqueda, alta y edición.
- Odontograma FDI seleccionable, notas clínicas, plan de tratamiento y fecha de seguimiento.
- Historial por paciente, especialista, tratamiento y última visita.
- Agenda con estado, reprogramación y enlace de WhatsApp para preparar un recordatorio.
- Catálogo de ocho procedimientos con precios de ejemplo.
- Caja con pagos pendientes, pagados y anulados; resumen e informe mensual CSV.
- Exportación e importación manual de un respaldo JSON.

Los registros de la demo se guardan en `localStorage` bajo la clave `odontocare-demo-v2`. Esto solo persiste en el perfil de navegador actual: no sincroniza equipos, no autentica usuarios y no protege datos médicos. Los nombres, teléfonos, precios y pagos precargados son ficticios.

## 3. Personas usuarias y permisos

| Rol | Responsabilidades | Acceso recomendado |
| --- | --- | --- |
| Administrador de clínica | Configurar clínica, usuarios, especialistas, horarios, tarifas y reportes | Administración completa; acceso clínico solo si también tiene autorización asistencial |
| Recepción | Crear y actualizar datos administrativos, gestionar agenda, confirmar citas y preparar recordatorios | Datos de contacto y agenda; no editar notas clínicas ni antecedentes sensibles salvo necesidad autorizada |
| Odontólogo | Consultar antecedentes, odontograma, registrar atención, diagnóstico profesional, evolución y plan | Expediente clínico de sus pacientes; permisos de atención y firma |
| Caja/finanzas | Registrar cobros, referencias, saldos y conciliación | Datos financieros y mínimos datos identificativos; sin acceso a notas clínicas |

La autorización debe validarse en el servidor en cada operación. Ocultar botones en la interfaz no es control de acceso.

## 4. Módulos funcionales

### 4.1 Acceso y configuración de clínica

- Iniciar y cerrar sesión; recuperar acceso y aplicar MFA a cuentas privilegiadas.
- Mantener usuarios, roles, permisos y estado de la cuenta.
- Configurar nombre legal, identificación fiscal, moneda, zona horaria, horarios, días no laborables y datos de contacto.
- Registrar auditoría de inicios de sesión y cambios relevantes.

### 4.2 Panel de inicio

- Mostrar las próximas citas, pendientes de confirmación, atención del día y cobros pendientes.
- Permitir abrir una cita o una ficha según los permisos de la persona usuaria.
- Mostrar alertas operativas, no diagnósticos automáticos.

### 4.3 Pacientes

- Crear, buscar, consultar y editar fichas; detectar posibles duplicados por documento y ofrecer verificación antes de crear otro registro.
- Guardar nombre, documento, fecha de nacimiento, teléfono, correo, dirección y contacto de emergencia.
- Guardar antecedentes, alergias, condiciones médicas y medicamentos con fecha de actualización y autor.
- Distinguir los datos administrativos del expediente clínico.
- Mantener un identificador interno estable aunque cambie el documento o nombre.

### 4.4 Expediente, odontograma e historial clínico

- Mostrar antecedentes relevantes antes de registrar una atención.
- Representar dentición permanente y temporal con nomenclatura FDI; asociar hallazgos y procedimientos a piezas dentales y superficies cuando aplique.
- Registrar cada consulta como una nota nueva con fecha, profesional, motivo, hallazgos, procedimiento realizado, indicaciones y plan de seguimiento.
- Registrar la próxima visita recomendada y mostrarla en el expediente y la agenda.
- Conservar autoría y versiones: una nota firmada no se sobrescribe silenciosamente; las correcciones se agregan con autor, fecha y motivo.
- Permitir adjuntar documentos o radiografías en almacenamiento privado, con acceso auditado.

### 4.5 Especialistas y disponibilidad

- Administrar profesionales, especialidades, duración de consulta, tarifa base y horarios de atención.
- Permitir excepciones de calendario, vacaciones, bloqueos y cambios de turno.
- Calcular disponibilidad por profesional, duración del procedimiento y estado de la cita.

### 4.6 Agenda y citas

- Crear una cita desde una ficha existente o registrar al paciente durante la reserva.
- Seleccionar profesional, servicio, fecha y horario disponible; mostrar el precio estimado antes de confirmar.
- Permitir ver agenda diaria, semanal y por profesional; buscar y filtrar por paciente, fecha y estado.
- Permitir reprogramar y cancelar con motivo y autor.
- Usar estados explícitos: solicitada, confirmada, atendida, cancelada y no asistió. Registrar historial de transiciones.
- Prevenir solapamientos en el servidor: no puede haber dos citas activas del mismo profesional en el mismo intervalo.
- Preparar recordatorios por canales autorizados. La demo abre un enlace de WhatsApp con texto; no envía mensajes automáticamente.

### 4.7 Catálogo de servicios y precios

- Mantener procedimientos, descripción, duración, precio base, estado activo y vigencia.
- Mantener la tarifa de consulta por profesional o especialidad.
- Calcular el presupuesto como consulta más servicios, cantidades y ajustes autorizados.
- Guardar en cada cita y cobro una copia del nombre y precio aplicados en ese momento. Cambiar una tarifa después no debe reescribir documentos históricos.
- Los precios actuales de la demo están expresados en USD y son solo ilustrativos; deben configurarse con la clínica.

### 4.8 Atención y plan de tratamiento

- Desde una cita, abrir la ficha, revisar antecedentes y registrar la atención.
- Asociar procedimientos realizados con piezas, notas, profesional y fecha.
- Separar claramente tratamiento propuesto, tratamiento aceptado, tratamiento pendiente y procedimiento realizado.
- Dividir planes extensos en procedimientos o sesiones, con prioridad, precio estimado y estado.
- Al marcar una cita como atendida, actualizar la última visita solo con la fecha real de atención.

### 4.9 Caja, pagos y recibos

- Crear cargos vinculados a una cita o plan de tratamiento.
- Registrar abonos, pagos completos, método, importe, moneda, referencia, fecha, usuario y recibo.
- Usar estados como pendiente, pagado, anulado y reembolsado; un pago solo pasa a pagado cuando una persona autorizada lo registra o un proveedor de pagos lo confirma.
- No borrar movimientos contables: correcciones mediante anulación, devolución o ajuste con auditoría.
- Separar comprobante de cita de recibo de pago. Un PNG de reserva no demuestra que el dinero se recibió.
- Conciliar referencias y permitir filtrar por periodo, estado, método y paciente.

### 4.10 Historial, informes y exportaciones

- Consultar por paciente citas, tratamientos realizados, planes, pagos y notas según permisos.
- Calcular informes de citas, asistencia, pacientes nuevos, procedimientos, ingresos cobrados, saldos pendientes y anulaciones por periodo.
- Exportar informes CSV con filtros visibles y registrar quién los generó cuando el sistema sea productivo.
- Exportar respaldos de datos solo a usuarios autorizados; cifrar y proteger los respaldos fuera del navegador.

### 4.11 Contacto y notificaciones

- Administrar dirección, teléfono y canales oficiales de la clínica.
- Preparar confirmaciones y recordatorios de cita con consentimiento y preferencias del paciente.
- Registrar el resultado de cada notificación cuando se conecte un proveedor real.

## 5. Flujos principales

### Registrar y atender una consulta

1. Recepción busca al paciente; si no existe, crea su ficha administrativa.
2. El sistema solicita los datos necesarios y alerta de antecedentes relevantes al profesional autorizado.
3. Recepción elige profesional, servicio, fecha y un horario libre; el sistema muestra el desglose de precios.
4. Se confirma la cita y se genera un cargo pendiente, no un pago ficticio.
5. El odontólogo abre la cita, documenta hallazgos, piezas tratadas, procedimiento realizado y plan.
6. La cita pasa a atendida y se actualiza la fecha real de visita.
7. Caja registra el pago o abono; el sistema emite un recibo separado de la confirmación de cita.

### Reprogramar o cancelar

1. Una persona autorizada abre la cita y elige un nuevo intervalo disponible o cancela con motivo.
2. El servidor valida disponibilidad de nuevo al guardar para evitar carreras entre usuarios.
3. Se registra el cambio en la auditoría y se actualiza el recordatorio.
4. Una cancelación no elimina el cargo ni el pago: aplica la política de la clínica y registra anulación o devolución cuando corresponda.

## 6. Modelo de datos recomendado

Las relaciones se basan en identificadores internos, no en nombres. Todas las entidades deben incluir `clinic_id` si en el futuro hay más de una clínica.

- `User`: identidad, credenciales gestionadas por proveedor seguro, rol y estado.
- `Patient`: datos administrativos, documento normalizado, fecha de nacimiento y contacto de emergencia.
- `PatientMedicalProfile`: alergias, condiciones, medicamentos y fechas/autores de actualización.
- `Practitioner`: usuario profesional, especialidades, tarifas y estado.
- `Service`: procedimiento, duración, precio vigente y estado.
- `Appointment`: paciente, profesional, intervalo de inicio/fin, estado, motivo y desglose de precio aplicado.
- `ClinicalEncounter`: cita atendida, paciente, profesional, fecha y estado de firma.
- `ClinicalNote`: hallazgos, evolución, indicaciones, plan, autor y versión; asociada a una atención.
- `ToothFinding`: atención, identificador FDI, superficie, hallazgo o procedimiento y estado.
- `TreatmentPlan` / `TreatmentPlanItem`: propuesta, procedimientos/sesiones, precio congelado y progreso.
- `Invoice` / `Payment` / `PaymentAllocation`: cargos, pagos y aplicación de abonos; conservar importes y monedas.
- `Attachment`: metadatos y referencia a almacenamiento privado, nunca una URL pública permanente.
- `Notification`: canal, destino enmascarado, fecha, estado y consentimiento aplicable.
- `AuditEvent`: usuario, acción, entidad, fecha, motivo y metadatos mínimos necesarios.

Las citas deben usar intervalos con zona horaria definida por la clínica y persistirse en UTC junto con la zona configurada. Los importes deben persistirse como decimal exacto o unidades menores enteras, no como cálculos de coma flotante sin control.

## 7. División de código propuesta

La demo actual está concentrada en un HTML. Para crecer, conviene separar interfaz, lógica de negocio y persistencia. Una estructura posible, independiente del framework de interfaz, es:

```text
src/
  app/
    routes/
    layout/
    navigation/
  modules/
    dashboard/
    patients/
    clinical-record/
    odontogram/
    appointments/
    practitioners/
    services-pricing/
    treatment-plans/
    billing/
    reports/
    notifications/
    users-access/
    clinic-settings/
  shared/
    components/
    forms/
    validation/
    formatting/
    api-client/
    permissions/
server/
  modules/
    patients/
    clinical-record/
    appointments/
    practitioners/
    catalog/
    treatment-plans/
    billing/
    reports/
    notifications/
    users-access/
    audit/
  database/
    migrations/
    seeds/
```

Cada módulo de negocio debe ser propietario de sus reglas y contratos. Por ejemplo, el módulo de citas comprueba disponibilidad y transiciones de estado; el módulo de caja registra movimientos y no depende de cómo se dibuja una tabla en la pantalla. Los módulos se comunican mediante servicios/API y no leyendo directamente el almacenamiento de otro módulo.

### Límites sugeridos de API

- `GET/POST /patients`, `GET/PATCH /patients/:id`
- `GET/POST /patients/:id/encounters`, `GET /patients/:id/history`
- `GET /availability`, `GET/POST /appointments`, `PATCH /appointments/:id`
- `GET/POST /services`, `GET/POST /practitioners`
- `GET/POST /treatment-plans`
- `GET/POST /invoices`, `GET/POST /payments`, `POST /payments/:id/refunds`
- `GET /reports/summary`, `GET /reports/finance.csv`

La interfaz no decide por sí sola si un usuario puede leer una ficha, cobrar o firmar una nota. El servidor autentica, autoriza, valida y registra la operación.

## 8. Seguridad, privacidad y operación real

Antes de migrar datos clínicos reales, el sistema necesita como mínimo:

- Backend autenticado y base de datos con cifrado en tránsito y en reposo; nunca guardar expedientes reales en `localStorage`.
- Permisos por rol y clínica verificados del lado del servidor, sesión segura, MFA para perfiles privilegiados y cierre de sesiones.
- Auditoría de lectura y modificación de información sensible, con especial cuidado para no copiar datos clínicos completos a logs.
- Validación de entradas, límites de intentos, protección de archivos y almacenamiento privado.
- Copias de seguridad cifradas, pruebas periódicas de restauración, política de retención y procedimiento de incidentes.
- Consentimiento y política de privacidad adecuados a la jurisdicción donde opera la clínica.
- Revisión profesional de odontograma, nomenclatura y campos clínicos antes de su uso asistencial.

## 9. Fases de implementación

1. **Base segura:** definir jurisdicción y requisitos, elegir autenticación/backend/base de datos, modelar permisos, auditoría y migraciones. No importar expedientes reales hasta completar esta fase.
2. **Pacientes y agenda:** CRUD de pacientes, especialistas, disponibilidad, reservas, prevención de solapamientos y estados de cita.
3. **Expediente clínico:** antecedentes, odontograma, atenciones firmadas, historial, planes y seguimiento.
4. **Tarifas y caja:** catálogo con vigencias, presupuestos, cargos, pagos, abonos, anulaciones, devoluciones y recibos.
5. **Informes y comunicación:** informes por periodo, exportaciones autorizadas, recordatorios reales y copias de seguridad probadas.
6. **Puesta en producción:** pruebas de permisos, accesibilidad, móvil, rendimiento, restauración, formación del personal y aceptación clínica.

## 10. Criterios de aceptación

- Un usuario solo puede consultar y modificar información permitida por su rol; las pruebas verifican también llamadas directas a la API.
- Dos usuarios no pueden reservar simultáneamente el mismo intervalo de un profesional.
- Reprogramar conserva el historial del cambio y actualiza la disponibilidad.
- Una cita futura no se presenta como tratamiento realizado ni como última visita.
- El precio histórico no cambia al modificar el catálogo.
- Un recibo pagado solo se emite tras registrar un pago válido; una reserva produce una confirmación distinta.
- La evolución clínica conserva autor, fecha, piezas y versiones; no desaparece al editar datos de contacto.
- Los informes concilian sus totales con los movimientos de caja y explican qué estados incluyen.
- La interfaz funciona en móvil y escritorio, permite teclado/lectores de pantalla y no expone tablas sensibles a usuarios sin permiso.
- Existe una copia de seguridad cifrada cuya restauración fue probada.

## 11. Decisiones pendientes

Antes de construir la versión real, la clínica debe definir país y normativa aplicable, moneda e impuestos, estructura de precios, duración de turnos, política de cancelación y devolución, quién puede firmar notas, retención de expedientes, canales de notificación y proveedores de pago. Los precios que aparecen en la demo no son una lista comercial aprobada.
