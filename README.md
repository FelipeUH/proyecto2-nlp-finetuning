# Fine-tuning de Transformers para tareas de NLP

Proyecto 2 del curso **Fundamentos en Procesamiento de Lenguaje Natural**, Universidad de Antioquia (2026-2).

**Estudiante**: Felipe Uribe Holguín

En este proyecto ajusto (*fine-tuning*) modelos Transformer preentrenados de Hugging Face para tres tipos de problemas de NLP en español, usando una GPU T4 gratuita de Google Colab.

| Tipo de problema | Tarea | Dataset | Modelo base | Resultado en test |
|---|---|---|---|---|
| Clasificación de secuencias | Sentimiento (negativo / neutral / positivo) de reseñas de Amazon en español | [`SetFit/amazon_reviews_multi_es`](https://huggingface.co/datasets/SetFit/amazon_reviews_multi_es) | [BETO](https://huggingface.co/dccuchile/bert-base-spanish-wwm-cased) (BERT) | Accuracy **79.0 %** · F1 macro **0.734** |
| Etiquetado de secuencias | Reconocimiento de entidades (PER, ORG, LOC, MISC) en noticias | [`BramVanroy/conll2002`](https://huggingface.co/datasets/BramVanroy/conll2002) (`es`) | BETO (BERT) | F1 por entidad **0.869** |
| Secuencia a secuencia | Traducción inglés → español de literatura clásica | [`Helsinki-NLP/opus_books`](https://huggingface.co/datasets/Helsinki-NLP/opus_books) (`en-es`) | [MarianMT opus-mt-en-es](https://huggingface.co/Helsinki-NLP/opus-mt-en-es) (encoder-decoder) | BLEU **22.67** (sin ajustar: 17.54) |

### Detalle de los mejores modelos

| Tarea | Mejor LR | Batch | Épocas | Datos de entrenamiento | Tiempo (T4) | Resultados en test |
|---|---|---|---|---|---|---|
| Sentimiento | 3e-5 | 32 | 2 | 200 000 reseñas | 35 min | Accuracy 0.790 · F1 macro 0.734 (línea base de clase mayoritaria: 0.40) — negativo 0.845 · neutral 0.481 · positivo 0.876 |
| NER | 8e-5 | 16 | 5 (mejor: época 3) | 8 324 oraciones | 5 min | P 0.859 · R 0.880 · F1 0.869 — PER 0.962 · ORG 0.866 · LOC 0.847 · MISC 0.745 |
| Traducción | 5e-5 | 16 | 2 | 82 431 pares | 21 min | BLEU 17.54 → **22.67** (+5.1) · chrF 44.61 → **47.70** (+3.1) |

## Cómo ejecutarlo

1. Abrir el notebook en Google Colab y seleccionar **Entorno de ejecución → GPU T4**.
2. Ejecutar la sección **0. Configuración**, que monta Google Drive para guardar modelos y resultados.
3. Ejecutar las partes A, B y C. Son independientes y se pueden correr en sesiones distintas. Las métricas se guardan en un archivo `resultados.json`.

Tiempo total aproximado de ejecución: ~1 h 20 min.

## Lo que aprendí

- El *fine-tuning* reutiliza el conocimiento de un modelo preentrenado: solo cambian la cabeza del modelo y la forma de preparar los datos.
- El **learning rate** fue el hiperparámetro que más movió los resultados; el *batch size* casi no influyó. Aun así, las diferencias entre los mejores valores fueron de 0.5–2 puntos: al ejecutar el notebook dos veces, los resultados variaron alrededor de medio punto.
- El *fine-tuning* también sirve para **adaptar** un modelo que ya resuelve la tarea: el traductor ganó 5 puntos de BLEU en el dominio literario y aprendió convenciones como la raya de diálogo del español.
- Cada tarea necesita su propio preprocesamiento y su propia métrica (F1 macro, F1 por entidad con seqeval, BLEU/chrF). Además, hay que elegir con la métrica que importa: en traducción, la pérdida y el BLEU no siempre coincidieron.