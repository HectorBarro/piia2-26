---
description: La inferencia se hace con la API de Groq; el modelo concreto se lee de GROQ_MODEL y no se fija en el código.
state: Aceptado
---

# ADR — Inferencia con la API de Groq

## Decisión
La clasificación, la extracción de entidades y las preguntas sobre el documento se resuelven llamando a la API de Groq. El nombre del modelo se lee de la variable de entorno `GROQ_MODEL` y la clave de `GROQ_API_KEY`. Este ADR no fija el modelo: el candidato está en `README.md` y se confirma con `specs/evaluacion-metricas.md`.

## Motivación
SDG indicó Groq como servicio de inferencia. No requiere GPU propia, y Groq ofrece modelos multimodales con salida en modo JSON. Sus modelos con visión están en *preview* y pueden retirarse, por eso el modelo se cambia sin tocar el código.

Groq también ofrece salida ajustada a un esquema (`json_schema` en modo estricto), pero no la aplicó en nuestras peticiones con imagen: el 19-11-2026 rechazó con un 400 `json_validate_failed` 11 de 22 extracciones y 2 clasificaciones. Por eso no se confía en Groq para cumplir el esquema: se le pide un objeto JSON (`json_object`) y el esquema se valida en el cliente, que pide al modelo corregir la respuesta si no lo cumple (ver `README.md`, «Cómo funciona el procesamiento»).

## Alternativas consideradas
- Inferencia local con vLLM y un modelo abierto. Aplazada: requiere una GPU que no tenemos. Queda como ampliación en `README.md`.
- Otros proveedores de API. Rechazada: SDG indicó Groq.
- Fijar el nombre del modelo en el código. Rechazada: si Groq retira el modelo habría que cambiar código.

## Referencias
- Visión en Groq (formato de la imagen como *data URL* y límites de tamaño): https://console.groq.com/docs/vision
- Salidas estructuradas, `json_object` frente a `json_schema`: https://console.groq.com/docs/structured-outputs
- Límites de uso por nivel (el diario de tokens cortó la primera prueba): https://console.groq.com/docs/rate-limits
- Códigos de error de la API: https://console.groq.com/docs/errors
- SDK de Python: https://github.com/groq/groq-python
- `docs/Planificación.pdf`
- Resultados de la primera prueba del modelo: `README.md`, sección «Tecnologías», y `output/prueba_modelo_*.csv`.

Cómo se llama a Groq en `src/procesamiento_documental/cliente.py`. Es la única llamada del proyecto. El SDK no reintenta (`max_retries=0`): los reintentos los hace el cliente, para medir bien la latencia y respetar `retry-after`.

```python
import os
import groq

client = groq.Groq(api_key=os.environ["GROQ_API_KEY"], max_retries=0)
response = client.chat.completions.create(
    model=os.environ["GROQ_MODEL"],
    messages=[
        {"role": "system", "content": prompt_con_esqueleto_del_esquema},
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "Extract the entities."},
                {
                    "type": "image_url",
                    "image_url": {"url": "data:image/jpeg;base64,..."},
                },
            ],
        },
    ],
    response_format={"type": "json_object"},  # no "json_schema": ver Motivación
    temperature=0,
)
```

Configuración local, en `.env` (no se versiona):

```
GROQ_API_KEY=gsk_...
GROQ_MODEL=<modelo con visión y modo JSON>
```
