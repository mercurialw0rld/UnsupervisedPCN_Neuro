# Unsupervised Predictive Coding Network (PCN) for Orientation Tuning & RSA Comparison

Este repositorio contiene la implementación desde cero en PyTorch de una **Red de Codificación Predictiva No Supervisada (Unsupervised PCN)**. El modelo fue entrenado utilizando estímulos visuales continuos (*gratings* con diferentes orientaciones) y validado frente a datos neurofisiológicos reales de la corteza visual primaria de ratones (**Mouse V1**) mediante **Análisis de Similitud Representacional (RSA)**.

El proyecto está inspirado en los principios de plausibilidad biológica del procesamiento jerárquico cortical

---

## 🧠 Arquitectura del Modelo

El modelo implementa una jerarquía generativa *top-down* basada en el algoritmo de minimización de energía por pasos de tiempo correlacionados. A diferencia de los enfoques clásicos supervisados, la red se autoorganiza basándose únicamente en la estadística local del estímulo

* **Capa de Entrada ($x^{(0)}$):** 3072 neuronas (estímulos visuales aplanados de *gratings*).
* **Capa Oculta 1 ($x^{(1)}$):** 256 neuronas.
* **Capa Oculta 2 ($x^{(2)}$):** 64 neuronas.


### Dinámica del Algoritmo:
1. **Fase de Inferencia (Fast Timescale):** Las activaciones neuronales $x^{(l)}$ se actualizan de forma iterativa mediante descenso de gradiente manual para minimizar la energía del error de predicción global ($\mathcal{L} = \frac{1}{2}\sum ||\epsilon^{(l)}||^2$), manteniendo los pesos fijos
2. **Fase de Aprendizaje (Slow Timescale):** Los pesos sinápticos $W^{(l)}$ se modifican mediante reglas locales de plasticidad de tipo Hebbiano (asociación post-sináptica del error). Para garantizar estabilidad en arquitecturas profundas, se utiliza un esquema de $T_{\text{learn}} = B$ pasos de actualización por batch.
---

## 📊 Resultados Destacados

### 1. Dinámica de Convergencia (Energía del Error)
Al calibrar adecuadamente las tasas de la inferencia y el aprendizaje ($\eta_{\text{infer}} = 0.01$, $\eta_{\text{learn}} = 0.0002$), la red logra una convergencia monótona y estable, reduciendo la energía del error global a niveles mínimos, lo que valida la consistencia del paisaje de energía.

Epoch 1 | Inference Energy: 119142.1719 | Learning Energy: 11191.2324
Epoch 10 | Inference Energy: 633.5059 | Learning Energy: 584.7316
Epoch 20 | Inference Energy: 205.2604 | Learning Energy: 194.5243
Epoch 30 | Inference Energy: 91.1984 | Learning Energy: 87.2553
Epoch 40 | Inference Energy: 46.9006 | Learning Energy: 45.1035
Epoch 50 | Inference Energy: 26.3239 | Learning Energy: 25.4060
Epoch 60 | Inference Energy: 15.7567 | Learning Energy: 15.2527
Epoch 70 | Inference Energy: 9.9262 | Learning Energy: 9.6351
Epoch 80 | Inference Energy: 6.5532 | Learning Energy: 6.3787
Epoch 90 | Inference Energy: 4.5296 | Learning Energy: 4.4219
Epoch 100 | Inference Energy: 3.2840 | Learning Energy: 3.2164
Epoch 110 | Inference Energy: 2.5021 | Learning Energy: 2.4587
Epoch 120 | Inference Energy: 2.0009 | Learning Energy: 1.9725

### 2. Representational Dissimilarity Matrices (RDMs)
Se generaron las matrices de disimilitud (1 - correlación de Pearson) para los 181 estímulos continuos de orientación. 
<img width="1489" height="530" alt="image" src="https://github.com/user-attachments/assets/e9a8c551-b8a0-4210-9580-060f783861c7" />



**Observación:** La **Capa 1 y 2 de la PCN** replica de forma espontánea la macroestructura geométrica observada en los datos neurofisiológicos de **Mouse V1**, capturando la topología del espacio visual continuo e incluso manifestando la naturaleza cíclica/circular de las orientaciones en los extremos (0° - 180°).

### 3. Representational Similarity Analysis (RSA)
Se evaluó la consistencia estadística entre los espacios representacionales de la red artificial y la corteza biológica mediante la correlación de Spearman ($r$) sobre el triángulo superior de las RDMs:

--- RSA Analysis (n=181 stimuli) ---
Layer 1 — RSA r = 0.170, p = 2.7739e-105
Layer 2 — RSA r = 0.222, p = 1.4253e-180

El p-valor extremadamente bajo ($p \ll 0.001$) rechaza categóricamente la hipótesis nula, demostrando que la PCN comparte una organización representacional con el cerebro del mamífero significativamente robusta. La magnitud moderada del coeficiente $r$ es consistente con un régimen de aprendizaje puramente no supervisado expuesto a la variabilidad y ruido intrínseco de los datos biológicos.

---

