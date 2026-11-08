# Ceiba Pay — Resumen ejecutivo
**Análisis de sentimientos + segmentación de clientes · Presupuesto 2027**
*Adjunto: notebook `ceiba_pay_sentiment_segmentation.ipynb` con el pipeline completo.*

---

## La pregunta
¿Qué segmentos de clientes tiene Ceiba Pay, qué siente cada uno y dónde invertir en 2027 para proteger el margen y crecer?

## Lo esencial en 4 cifras
| NPS general | Margen en riesgo | Concordancia sentimiento–NPS | Segmentos |
|---|---|---|---|
| **−8** | **54 %** del margen viene de un segmento con NPS −40 | **ρ = 0,53** | **5** por comportamiento de uso |

## Qué encontramos
1. **Hay más críticos que fans.** 43 % de detractores frente a 35 % de promotores.
2. **Cada segmento tiene un dolor distinto**, así que una sola acción no sirve para todos.

| Segmento | Clientes | NPS | % margen | Lo que más mencionan |
|---|---|---|---|---|
| Comerciantes QR | 15 % | −40 | 54 % | Comisiones y límites (49 %) |
| Ahorradores | 20 % | +34 | 27 % | Bolsillos de ahorro (40 %); piden nuevos productos (25 %) |
| Jóvenes P2P | 30 % | +16 | 9 % | Transferencias; fallas de la app (28 %) |
| Usuarios de efectivo | 20 % | −36 | 11 % | Dónde retirar (40 %); app difícil (27 %) |
| Dormidos | 15 % | −42 | 0,3 % | Registro y validación (44 %) |

3. **Lo que se valora:** que sea gratis, pagar con QR, la rapidez y los bolsillos de ahorro.
4. **Lo que duele:** el registro, las comisiones, el acceso a efectivo y la estabilidad de la app.

## Recomendaciones 2027
| # | Hallazgo | Segmento | Acción concreta | Indicador |
|---|---|---|---|---|
| 1 | Generan el 54 % del margen con NPS −40; la mitad se queja de comisiones y límites | Comerciantes QR | **Plan comerciante:** comisión escalonada por volumen, límites más altos para negocios verificados y soporte prioritario | NPS del segmento; abandono mensual; margen del segmento |
| 2 | NPS −36; el 40 % no encuentra dónde retirar; 55 años, 39 % en municipios | Usuarios de efectivo | **Retiros con QR sin tarjeta en cadenas con presencia municipal** (D1, Ara, Dollarcity), como hizo Nu con D1 en sept. 2026, + **modo simple** en la app | NPS del segmento; % de quejas por retiro; retiros exitosos / intentos |
| 3 | NPS +34 y 27 % del margen; el 25 % pide CDT, inversión o crédito | Ahorradores | **Bolsillo con rendimiento o CDT digital** | Adopción del producto; saldo promedio; margen del segmento |

**Siguiente prioridad (bajo costo):** simplificar el registro y la validación de identidad para activar a los clientes dormidos.

## Cómo se hizo
- **Calidad de datos:** se corrigieron la escala de los porcentajes, las edades vacías y los valores imposibles. No se eliminó ningún cliente y el archivo original quedó intacto.
- **Sentimiento:** traducción automática ES→EN + TextBlob, validado contra el NPS.
- **Temas:** 13 temas por palabras clave, que cubren el 99,6 % de los comentarios.
- **Segmentación:** K-Means (K = 5) solo con variables de uso, con logaritmo y estandarización.

## Limitaciones
Los datos parecen sintéticos (patrones demasiado limpios). TextBlob no capta el sarcasmo y depende de la traducción. El NPS corresponde a un solo trimestre (jul.–sep. 2026).
