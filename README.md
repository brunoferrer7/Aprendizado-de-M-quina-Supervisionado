Estação Metrô

Integrantes:

Walison Araujo Santana — R.A.: 1696860<br>
Cauã Tomas da Paixão — R.A.: 1696689<br>
Bruno Ferrer — R.A.: 1696323<br>

Sobre o projeto<br>

# 🤖 Laboratório de Aprendizado de Máquina Supervisionado

Projeto prático da disciplina **Inteligência Artificial e Machine Learning** (Aula IAML 05 — Prof. Hercules Ramos), que constrói um ciclo completo de Machine Learning supervisionado: dos dados brutos a um modelo servido por API e consumido por uma interface web.

Três problemas de negócio são resolvidos com o **mesmo pipeline genérico**:

| Problema | Tipo | Descrição |
|---|---|---|
| **Churn** | Classificação | Probabilidade de um cliente de telecom cancelar o serviço |
| **Imóveis** | Regressão | Preço estimado de um imóvel |
| **Crédito** | Classificação | Probabilidade de inadimplência em um empréstimo |

---

## 🧠 O que o projeto demonstra

- Pré-processamento sem vazamento de dados (`Pipeline` + `ColumnTransformer`)
- Comparação de algoritmos (Regressão Linear/Logística, Árvore de Decisão, Random Forest, Gradient Boosting) com **validação cruzada de 5 folds**
- Avaliação correta em conjunto de teste nunca visto no treino (acurácia, precisão, recall, F1, ROC AUC, MAE, RMSE, R²)
- Análise de **limiar de decisão** orientada a custo de negócio
- Modelo servido por uma **API Flask** (`/api/prever/<problema>`) e consumido por um **formulário web** dinâmico
- Boas práticas: sementes fixas (reprodutibilidade), versão de biblioteca travada, validação de entrada na API

---

## 📁 Estrutura do repositório

```
laboratorio-ml/
├── config.py              # Define os problemas, features, limites e opções
├── gerar_dados.py         # Gera dados sintéticos (data/*.csv)
├── treinar.py             # Compara algoritmos, avalia e salva os modelos (models/)
├── app.py                 # API Flask + serve a interface web
├── templates/
│   └── index.html         # Formulário web (abas geradas dinamicamente)
├── eda_credito.py         # Análise exploratória do problema de crédito
├── avaliacao_credito.py   # Curva ROC e análise de limiar por custo
├── graficos/              # Gráficos e capturas de tela gerados
├── models/                # Modelos treinados (.joblib) e métricas (.json)
├── data/                  # CSVs gerados (não versionar dados reais)
└── requirements.txt
```

---

## 🚀 Como rodar

### Opção 1 — Local (VS Code / terminal)

```bash
# 1. Clonar e entrar na pasta
git clone <url-do-seu-repo>
cd laboratorio-ml

# 2. Criar e ativar o ambiente virtual
python -m venv .venv
source .venv/bin/activate        # Linux/macOS
.venv\Scripts\Activate.ps1       # Windows PowerShell

# 3. Instalar as dependências
pip install -r requirements.txt

# 4. Gerar os dados sintéticos
python gerar_dados.py

# 5. Treinar e comparar os modelos
python treinar.py

# 6. Subir a API e a interface web
python app.py
```

Acesse **http://localhost:5000** — a interface já sobe com as 3 abas.

> Na primeira execução, `python app.py` sozinho já detecta se os dados/modelos não existem e roda os passos 4 e 5 automaticamente.

### Opção 2 — Google Colab

Use o notebook `laboratorio_ml_colab.ipynb` incluso no repositório: basta abrir no Colab e ir em **Ambiente de execução → Executar tudo**. A interface abre em uma janela embutida no próprio notebook.

---

## 📦 requirements.txt

```
flask>=3.0
pandas>=2.2
scikit-learn==1.9.1
joblib>=1.3
numpy
matplotlib
seaborn
```

> A versão do `scikit-learn` é travada porque um modelo salvo (`.joblib`) só tem compatibilidade garantida com a mesma versão que o treinou.

---

## 🔌 API — principais rotas

| Rota | Método | Descrição |
|---|---|---|
| `/` | GET | Interface web |
| `/api/problemas` | GET | Lista os problemas disponíveis, features e métricas |
| `/api/prever/<nome>` | POST | Recebe um JSON com as features e retorna a previsão |

**Exemplo:**
```bash
curl -X POST http://localhost:5000/api/prever/credito \
  -H "Content-Type: application/json" \
  -d '{"idade": 24, "renda_mensal": 1800, "tempo_emprego_anos": 0.5,
       "score_credito": 420, "dividas_ativas": 5, "possui_imovel": "nao",
       "finalidade": "negocio", "prazo_meses": 60, "valor_emprestimo": 9000}'
```
```json
{"probabilidade": 0.9505855985606549}
```

---

## 📊 Resultados obtidos

**Churn de clientes** — modelo escolhido: Regressão Logística (ROC AUC 0,789 no teste)
**Preço de imóveis** — modelo escolhido: Gradient Boosting (R² 0,942 no teste)
**Inadimplência em crédito** — modelo escolhido: Regressão Logística (ROC AUC 0,762 no teste)

Análise de limiar (crédito): usando os custos de negócio (R$ 8.000 por inadimplente aprovado vs. R$ 1.500 por bom cliente recusado), o limiar ótimo de **0,20** reduz o custo estimado em ~31% frente ao limiar padrão de 0,5.

Detalhes completos, gráficos e a análise exploratória estão em `graficos/` e no relatório do exercício.

---

## ⚠️ Dados sintéticos

Todos os dados (`data/*.csv`) são **sintéticos**, gerados por código com padrões estatísticos realistas (`gerar_dados.py`, com semente fixa). Não há dados pessoais reais — o projeto é seguro para fins didáticos e para publicação pública.

---

## ⚖️ Ética e boas práticas

- Identificadores (`id_cliente`, `id_imovel`, `id_contrato`) são excluídos das features para evitar vazamento de dados.
- Features sensíveis (raça, gênero, religião) e possíveis *proxies* (como CEP) não são usadas nos modelos de crédito.
- Decisões de alto impacto (ex.: negar crédito) devem manter **humano no circuito** — o modelo apoia, não substitui, a decisão humana.

---

## 👨‍🏫 Créditos

Material didático da disciplina **Inteligência Artificial e Machine Learning** — Prof. Hercules Ramos.
Implementação e exercício prático desenvolvidos como atividade de curso.
