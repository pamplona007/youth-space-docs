# Power BI + Python — AULA 7
## Explicação Completa para o Professor

---

## O que você vai aprender hoje

Imagine que você tem o Power BI aberto na frente de você. Hoje a gente vai aprender a fazer o Python conversar com o Power BI — e por que isso é revolucionário.

**Ao final desta aula, você vai saber:**
- Configurar Python no Power BI
- Importar dados via script Python
- Limpar dados com Python no Power Query
- Criar visuais que não existem no Power BI nativo (heatmap, wordcloud, etc.)
- Fazer machine learning básico (clustering, forecast) dentro do Power BI

---

## PARTE 1 — Por que Python no Power BI? (10 min)

### O problema que a gente resolve

O Power BI é excelente para:
- Dashboards interativos
- Gráficos padrão (barras, linhas, pizzas)
- Medidas com DAX
- Time intelligence

Mas tem coisas que o Power BI **não faz bem**:
- Visualizações estatísticas (heatmap de correlação, pairplot)
- Machine learning (clustering, forecast, classificação)
- Processamento de texto avançado (NLP, wordcloud)
- Limpeza de dados muito complexa (regex, múltiplos replaces)

É aí que entra o Python.

### A integração Python-Power BI funciona em 3 lugares:

```
LUGAR 1: Obter Dados → Script Python
├── Usa Python para CARREGAR dados
├── Dados sintéticos, APIs, scraped data
└── Executa uma vez na importação

LUGAR 2: Power Query → Transform → Executar Script Python
├── Usa Python para TRANSFORMAR dados
├── Limpeza, engenharia de features
└── Executa uma vez na transformação

LUGAR 3: Visualizações → Python Visual
├── Usa Python para VISUALIZAR dados
├── Gráficos customizados (matplotlib, seaborn)
└── Atualiza quando filtros mudam
```

**Importante entender:** Python no Power BI não é dinâmico como DAX. Quando você usa Python para carregar ou transformar dados, o script executa uma vez e fica "congelado" — não recalcula automaticamente quando você muda um filtro. Os **visuais Python** são diferentes: eles sim atualizam conforme você interage com o relatório.

---

## PARTE 2 — Configurando o Python (5 min)

### Abra o Power BI comigo

1. **Power BI Desktop aberto?** Ótimo.

2. Clique em **Arquivo** (canto superior esquerdo, ícone azul)

3. Quando o menu abrir, procure **Opções e Configurações** — passa o mouse por cima, não clique ainda

4. Vai abrir outro menu. Clique em **Opções**

5. Agora você está numa janela com muitas abas no painel esquerdo. Procura a seção **GLOBAL** (texto azul escuro)

6. Role a lista até encontrar **Python** — clique nele

**O que você vai ver:**

```
┌─────────────────────────────────────────────────────────────┐
│  Python                                                     │
│                                                             │
│  Opções de script Python                                   │
│  ─────────────────────                                     │
│                                                             │
│  Diretório base do Python padrão:                          │
│  [C:\Users\SeuNome\AppData\Local\Programs\Python\Python311] │
│                                                             │
│  Se não aparecer caminho nenhum, clique em DETECTAR         │
│  Se ainda não funcionar, você precisa instalar o Python    │
│                                                             │
│  ✓ Detect other Python IDEs  (recomendo deixar marcado)   │
└─────────────────────────────────────────────────────────────┘
```

**Por que isso existe?** O Power BI precisa saber onde está instalado o Python no seu computador para conseguir executar os scripts. Ele precisa de Python 3.8 ou superior.

**Bibliotecas que vão funcionar:**
- `pandas` — manipulação de dados
- `numpy` — cálculos numéricos
- `matplotlib` — gráficos
- `seaborn` — gráficos estatísticos
- `scikit-learn` — machine learning
- `scipy` — estatísticas
- `wordcloud` — nuvens de palavras

**Como verificar se suas bibliotecas estão instaladas:**

```bash
# Abra o Prompt de Comando (Windows + R, digite "cmd", Enter)
# Digite:

python -c "import pandas; import matplotlib; import seaborn; import sklearn; print('Tudo OK!')"

# Se aparecer "Tudo OK!", suas bibliotecas estão instaladas
# Se aparecer erro, você precisa instalar:
# pip install pandas numpy matplotlib seaborn scikit-learn scipy wordcloud
```

---

## PARTE 3 — Python como Fonte de Dados (15 min)

### O que a gente vai fazer

Ao invés de clicar em "Obter Dados → CSV", a gente vai escrever um script Python que gera ou busca os dados. Isso é útil quando:

1. Você quer criar dados sintéticos (testes, demos)
2. Você quer puxar dados de uma API que o Power BI não suporta nativamente
3. Você quer fazer uma transformação inicial antes de carregar

### Passo a passo no Power BI

**1. Na faixa de opções superior (ribbon), clique em:**

```
Home → Obter Dados → Mais...
         |
         └── aparece uma lista enorme de fontes
         └──Role até encontrar "Script Python" (ou digite "Python" na busca)
```

**2. Clique em "Script Python" → Conectar**

Vai abrir uma janela assim:

```
┌─────────────────────────────────────────────────────────────┐
│  Script Python                                      [X]   │
│                                                             │
│  Cole seu script Python abaixo:                            │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                                                      │   │
│  │                                                      │   │
│  │                                                      │   │
│  │                                                      │   │
│  │                                                      │   │
│  │                                                      │   │
│  │                                                      │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│                                          [OK]  [Cancelar]   │
└─────────────────────────────────────────────────────────────┘
```

**3. Agora a gente vai escribir o script.** Digite (ou cole):

```python
import pandas as pd
import numpy as np

np.random.seed(42)  # Isso garante que todo mundo vai ter os MESMOS dados

# Gerar 2000 vendas fictícias
datas = pd.date_range('2024-01-01', periods=2000, freq='D')

vendas = pd.DataFrame({
    'id_venda': range(1, 2001),
    'data': datas,
    'id_cliente': np.random.randint(1, 501, 2000),
    'id_produto': np.random.randint(1, 101, 2000),
    'id_vendedor': np.random.randint(1, 21, 2000),
    'qtd': np.random.randint(1, 10, 2000),
    'preco_unitario': np.random.uniform(10, 500, 2000).round(2),
    'custo_unitario': np.random.uniform(5, 250, 2000).round(2),
    'desconto': np.random.uniform(0, 0.2, 2000).round(2),
    'status': np.random.choice(['Concluída', 'Cancelada', 'Devolvida'],
                               2000, p=[0.85, 0.10, 0.05])
})

# Calcula colunas derivadas
vendas['valor_original'] = vendas['qtd'] * vendas['preco_unitario']
vendas['valor_final'] = vendas['valor_original'] * (1 - vendas['desconto'])
vendas['custo_total'] = vendas['qtd'] * vendas['custo_unitario']
vendas['lucro'] = vendas['valor_final'] - vendas['custo_total']
```

**4. Clique OK**

O Power BI vai executar esse script (pode demorar 10-30 segundos na primeira vez porque precisa iniciar o Python).

**5. Quando terminar**, vai aparecer o "Navegador" mostrando seu DataFrame:

```
┌─────────────────────────────────────────────────────────────┐
│  Navegador                                            [X]   │
│                                                             │
│  ┌──────────────────┐  ┌─────────────────────────────────┐  │
│  │ ▼ Scripts Python │  │   vendas                        │  │
│  │                  │  │                                 │  │
│  │   ▼ vendas       │  │ id_venda  data   id_cliente...  │  │
│  │       [✓]        │  │ 1        2024-01-01  234        │  │
│  │                  │  │ 2        2024-01-02  87         │  │
│  └──────────────────┘  │ 3        2024-01-03  152        │  │
│                        │ ...                            │  │
│                        └─────────────────────────────────┘  │
│                                                             │
│                           [Transformar Dados] [OK]          │
└─────────────────────────────────────────────────────────────┘
```

**6. Clique OK (ou Transformar Dados se quiser abrir Power Query)**

Pronto! Dados carregados via Python.

**Por que usar isso?**
- Você pode criar dados de teste rapidamente
- Pode puxar de APIs que o Power BI não suporta diretamente
- Pode fazer pré-processamento antes de entrar no Power BI

---

## PARTE 4 — Python no Power Query (20 min)

### Isso é mágica negra

O Power Query já é excelente para limpar dados. Mas tem coisas que são muito mais fáceis em Python. Por exemplo:

- Remover outliers statistically (não apenas "maior que X", mas "mais de 3 desvios padrão da média")
- Normalizar texto com lógica complexa
- Criar segments baseados em regras complicadas
- Fazer encoding de variáveis categóricas

### Passo a passo

**1. Clique em "Transformar Dados"** (Home → Transform Data)

**2. No Power Query Editor, selecione a tabela que você quer transformar**

**3. Na faixa de opções superior, vá na aba "Transform"**

**4. No canto direito, você vai ver um botão que diz "Executar Script Python"** (ou "Run Python Script" se estiver em inglês — é um ícone de uma cobra/py)

**5. Clique nele — vai abrir uma janela enorme pra você digitar o script**

**6. O que você escreve lá dentro?**

```python
# O Power Query passa os dados pra você como "dataset"
# Você pode fazer o que quiser com pandas

# 1. Limpar outliers (valores acima de 3 desvios padrão)
mean_val = dataset['valor_final'].mean()
std_val = dataset['valor_final'].std()
dataset = dataset[(dataset['valor_final'] < mean_val + 3*std_val) &
                   (dataset['valor_final'] > mean_val - 3*std_val)]

# 2. Criar novas colunas
dataset['valor_original'] = dataset['qtd'] * dataset['preco_unitario']
dataset['valor_final'] = dataset['valor_original'] * (1 - dataset['desconto'])
dataset['custo_total'] = dataset['qtd'] * dataset['custo_unitario']
dataset['lucro'] = dataset['valor_final'] - dataset['custo_total']

# 3. Extrair ano e mês
dataset['data'] = pd.to_datetime(dataset['data'])
dataset['ano'] = dataset['data'].dt.year
dataset['mes'] = dataset['data'].dt.month

# 4. Criar segmentos
dataset['segmento'] = pd.cut(dataset['valor_final'],
                            bins=[0, 100, 500, 1000, float('inf')],
                            labels=['Econômico', 'Padrão', 'Premium', 'VIP'])
```

**7. Clique OK**

O Power Query vai executar o script e... olha só! Apareceram novas colunas na sua tabela.

**8. Clique em "Fechar e Aplicar"** (Home → Close & Apply)

**Isso é diferente do script na hora de obter dados:**
- **Script Python em "Obter Dados"** → executa uma vez na importação, vira tabela estática
- **Script Python no Power Query** → executa como uma etapa da transformação, pode ser reprocessado

---

## PARTE 5 — Visuais Python (45 min)

### Isso é o mais legal

O Power BI tem uns visuais chamados "Python visual" que permitem você criar gráficos usando matplotlib/seaborn diretamente no relatório. Isso significa que você pode criar **qualquer gráfico que o Python consegue desenhar**.

### Como criar um visual Python

**1. No painel de Visualizações (direita), você vê vários ícones:**

```
[📊] [📈] [📉] [🥧] [🗺️] ...
```

**2. Clique na **seta para baixo** (▼) pra expandir o catálogo de visuais**

**3. Procure por "Python visual"** (ícone com um "Py" ou uma cobra) — clique nele

**4. Vai aparecer um espaço em branco no canvas com uma mensagem:**

```
"Habilitar script visual — arraste campos de dados para este visual"
```

**5. Agora você arrasta campos para o visual:**
- Arraste campos numéricos para "Values"
- Arraste campos categóricos para "Axis" ou "Legend"

**6. Aparece um editor de script Python na parte inferior da tela:**

```
┌─────────────────────────────────────────────────────────────┐
│ Python visual script editor                                  │
│                                                             │
│  # Digite seu código Python aqui                            │
│                                                             │
│                                                             │
│                                                             │
│                                                    [▶ Run]  │
└─────────────────────────────────────────────────────────────┘
```

### Agora a gente vai criar os gráficos

---

#### GRÁFICO 1: Heatmap de Correlação (15 min)

**Arrastar pro visual:** `valor_original`, `valor_final`, `custo_total`, `lucro` (todos em Values)

**Script:**

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# O Power BI passa os dados como um DataFrame chamado "dataset"
# Selecionar as colunas numéricas
colunas = ['valor_original', 'valor_final', 'custo_total', 'lucro']
df = dataset[colunas].copy()

# Calcular correlação
correlacao = df.corr()

# Criar o heatmap
plt.figure(figsize=(10, 8))
sns.heatmap(correlacao,
            annot=True,  # Mostra os números dentro dos quadrados
            fmt='.2f',   # Formata com 2 casas decimais
            cmap='RdYlGn',  # Paleta de cores (verde=positivo, vermelho=negativo)
            center=0,   # Centro da paleta em zero
            square=True,  # Quadrados perfeitos
            linewidths=0.5,  # Linhas entre casas
            cbar_kws={'shrink': 0.8})  # Tamanho da barra de cor

plt.title('Correlação entre Variáveis de Vendas', fontsize=14, fontweight='bold', pad=20)
plt.tight_layout()
plt.show()
```

**Clique em Run (▶)**

**O que você vai ver:** Um heatmap mostrando como as variáveis se relacionam.

```
         valor_original  valor_final  custo_total    lucro
valor_original     1.00        0.99        0.98       0.85
valor_final        0.99        1.00        0.96       0.87
custo_total        0.98        0.96        1.00       0.62
lucro              0.85        0.87        0.62       1.00
```

**Como interpretar:**
- Valor_original e valor_final têm correlação 0.99 (quase perfeita) — faz sentido, porque valor_final é baseado no original menos desconto
- Custo_total e lucro têm correlação mais baixa (0.62) — porque lucro depende também do desconto

**Por que fazer isso no Python e não no Power BI?**
- Heatmap de correlação não existe nos visuais nativos do Power BI
- Seaborn faz isso em 1 linha de código

---

#### GRÁFICO 2: Scatter Plot com Regressão (10 min)

**Arrastar pro visual:** `valor_original` (Axis ou Values) e `lucro` (Values)

**Script:**

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats

df = dataset.copy()

# Gráfico de dispersão com linha de tendência
plt.figure(figsize=(10, 6))
sns.regplot(data=df,
            x='valor_original',
            y='lucro',
            scatter_kws={'alpha': 0.5, 's': 20},  # Transparencia e tamanho dos pontos
            line_kws={'color': 'red', 'linewidth': 2})  # Cor e grossura da linha

# Adicionar equação da regressão
slope, intercept, r_value, p_value, std_err = stats.linregress(df['valor_original'], df['lucro'])
equation_text = f'y = {slope:.2f}x + {intercept:.2f}\nR² = {r_value**2:.3f}'
plt.annotate(equation_text,
             xy=(0.05, 0.95),
             xycoords='axes fraction',
             fontsize=11,
             va='top',
             bbox=dict(boxstyle='round', facecolor='wheat', alpha=0.5))

plt.title('Relação entre Valor Original e Lucro', fontsize=14, fontweight='bold')
plt.xlabel('Valor Original (R$)')
plt.ylabel('Lucro (R$)')
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

**O que aparece:** Pontos espalhados com uma linha vermelha (regressão linear) e a equação no canto.

**Para que serve isso?**
- Visualizar se há relação linear entre duas variáveis
- O R² diz quanto da variação é explicada pela relação (quanto mais perto de 1, melhor)

---

#### GRÁFICO 3: Distribuição (histograma + boxplot) (10 min)

**Arrastar pro visual:** `valor_final` (Values)

**Script:**

```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

df = dataset.copy()

fig, axes = plt.subplots(1, 2, figsize=(14, 5))

# Histograma com média e mediana
axes[0].hist(df['valor_final'], bins=30, color='#3498db', edgecolor='black', alpha=0.7)
axes[0].axvline(df['valor_final'].mean(), color='red', linestyle='--',
                linewidth=2, label=f'Média: R$ {df["valor_final"].mean():.2f}')
axes[0].axvline(df['valor_final'].median(), color='green', linestyle='--',
                linewidth=2, label=f'Mediana: R$ {df["valor_final"].median():.2f}')
axes[0].set_title('Distribuição do Valor Final', fontsize=12, fontweight='bold')
axes[0].set_xlabel('Valor (R$)')
axes[0].set_ylabel('Frequência')
axes[0].legend()

# Boxplot
axes[1].boxplot(df['valor_final'], vert=True, patch_artist=True,
               boxprops=dict(facecolor='#3498db', alpha=0.7),
               medianprops=dict(color='red', linewidth=2))
axes[1].set_title('Boxplot do Valor Final', fontsize=12, fontweight='bold')
axes[1].set_ylabel('Valor (R$)')
axes[1].set_xticklabels(['Valor Final'])

# Adicionar anotações no boxplot
stats_text = f'Min: R$ {df["valor_final"].min():.2f}\n'
stats_text += f'Q1: R$ {df["valor_final"].quantile(0.25):.2f}\n'
stats_text += f'Mediana: R$ {df["valor_final"].median():.2f}\n'
stats_text += f'Q3: R$ {df["valor_final"].quantile(0.75):.2f}\n'
stats_text += f'Max: R$ {df["valor_final"].max():.2f}'
plt.annotate(stats_text, xy=(1.3, df['valor_final'].median()), fontsize=9,
             bbox=dict(boxstyle='round', facecolor='lightyellow'))

plt.tight_layout()
plt.show()
```

**O que aparece:** Dois gráficos lado a lado — histograma mostrando a distribuição e boxplot mostrando os quartis.

**Para que serve?**
- Entender a forma da distribuição dos seus dados
- Identificar valores atípicos (outliers) — pontos fora das "caixas" do boxplot
- Comparar distribuições diferentes

---

## PARTE 6 — Machine Learning no Power BI (60 min)

### Isso é avançando

Agora a gente vai fazer coisas que o Power BI não faz de jeito nenhum: clustering e forecast.

---

#### CLUSTERING: Segmentar Clientes com KMeans (30 min)

**Primeiro, agregue os dados por cliente no Power Query:**

1. Abra Transformar Dados
2. Selecione a tabela vendas
3. Transform → Executar Script Python:

```python
# Agregar estatísticas por cliente
cliente_stats = dataset.groupby('id_cliente').agg({
    'valor_final': ['sum', 'mean', 'count'],
    'lucro': 'sum',
    'qtd': 'sum'
}).reset_index()

# Flatten column names (tirar a hierarquia)
cliente_stats.columns = ['id_cliente', 'total_vendas', 'ticket_medio',
                         'num_compras', 'lucro_total', 'qtd_total']
```

4. OK → Fechar e Aplicar

Agora você tem uma tabela `cliente_stats` com dados agregados por cliente.

**Criar visual Python:**

1. Clique em Python visual
2. Arraste `total_vendas`, `ticket_medio`, `lucro_total` para Values

**Script:**

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler

# Selecionar features para clustering
features = dataset[['total_vendas', 'ticket_medio', 'lucro_total']].copy()

# Tratar valores nulos (se houver)
features = features.fillna(0)

# Normalizar os dados (MUITO IMPORTANTE para KMeans)
scaler = StandardScaler()
features_scaled = scaler.fit_transform(features)

# Definir número de clusters — vamos usar 4
n_clusters = 4
kmeans = KMeans(n_clusters=n_clusters, random_state=42, n_init=10)
dataset['Cluster'] = kmeans.fit_predict(features_scaled)

# Visualizar
fig, axes = plt.subplots(1, 2, figsize=(14, 6))

# Scatter plot: total_vendas vs lucro_total
colors = ['#FF6B6B', '#4ECDC4', '#45B7D1', '#96CEB4']
for i in range(n_clusters):
    cluster_data = dataset[dataset['Cluster'] == i]
    axes[0].scatter(cluster_data['total_vendas'],
                   cluster_data['lucro_total'],
                   c=colors[i],
                   label=f'Cluster {i}',
                   alpha=0.6, s=50)

axes[0].set_xlabel('Total de Vendas (R$)')
axes[0].set_ylabel('Lucro Total (R$)')
axes[0].set_title('Segmentação de Clientes por Cluster', fontsize=12, fontweight='bold')
axes[0].legend()
axes[0].grid(True, alpha=0.3)

# Bar chart: tamanho de cada cluster
cluster_sizes = dataset['Cluster'].value_counts().sort_index()
bars = axes[1].bar(range(n_clusters), cluster_sizes.values, color=colors)
axes[1].set_xlabel('Cluster')
axes[1].set_ylabel('Número de Clientes')
axes[1].set_title('Tamanho dos Clusters', fontsize=12, fontweight='bold')
axes[1].set_xticks(range(n_clusters))

for bar, size in zip(bars, cluster_sizes.values):
    axes[1].text(bar.get_x() + bar.get_width()/2, bar.get_height() + 5,
                 str(size), ha='center', va='bottom')

plt.tight_layout()
plt.show()

# Mostrar estatísticas dos clusters
print("\n=== Perfil dos Clusters ===")
estatisticas = dataset.groupby('Cluster')[['total_vendas', 'ticket_medio', 'lucro_total']].mean()
print(estatisticas.round(2))
```

**O que aparece:**
- Scatter plot colorido com 4 grupos de clientes
- Barras mostrando quantos clientes em cada cluster
- No console (parte inferior), as estatísticas médias de cada cluster

**Como interpretar os clusters:**
```
Cluster 0 (Vermelho): Clientes com vendas baixas e lucro baixo — "Casuais"
Cluster 1 (Azul): Clientes com vendas altas e lucro alto — "VIPs"
Cluster 2 (Verde): Clientes com muitas compras mas ticket médio baixo — "Frequentes"
Cluster 3 (Roxo): Clientes com ticket médio alto mas poucas compras — "Premium"
```

---

#### FORECAST: Prever vendas futuras (30 min)

**Arrastar pro visual:** `data` (Axis) e `valor_final` (Values)

**Script:**

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression
from sklearn.metrics import r2_score, mean_absolute_error

# Converter data e agregar por dia
dataset['data'] = pd.to_datetime(dataset['data'])
vendas_diarias = dataset.groupby('data')['valor_final'].sum().reset_index()
vendas_diarias = vendas_diarias.sort_values('data')

# Criar feature de dias (número de dias desde a primeira data)
vendas_diarias['dias'] = (vendas_diarias['data'] - vendas_diarias['data'].min()).dt.days

# Preparar X e y
X = vendas_diarias[['dias']]
y = vendas_diarias['valor_final']

# Treinar modelo de regressão linear
model = LinearRegression()
model.fit(X, y)

# Prever próximos 30 dias
ultimo_dia = vendas_diarias['dias'].max()
dias_futuros = np.arange(ultimo_dia + 1, ultimo_dia + 31).reshape(-1, 1)
previsoes = model.predict(dias_futuros)

# Criar datas futuras correspondentes
ultima_data = vendas_diarias['data'].max()
datas_futuras = pd.date_range(start=ultima_data + pd.Timedelta(days=1), periods=30)

# Plotar
plt.figure(figsize=(14, 7))

# Linha do histórico (azul)
plt.plot(vendas_diarias['data'], y, 'b-', linewidth=1.5, label='Histórico', alpha=0.7)

# Linha de previsão (vermelha tracejada)
plt.plot(datas_futuras, previsoes, 'r--', linewidth=2, label='Previsão (30 dias)')

# Intervalo de confiança (simples: ±15%)
plt.fill_between(datas_futuras,
                 previsoes * 0.85,
                 previsoes * 1.15,
                 color='red', alpha=0.15,
                 label='Intervalo de Confiança (85-115%)')

# Linha vertical separando histórico de previsão
plt.axvline(ultima_data, color='gray', linestyle=':', alpha=0.7, linewidth=1.5)

# Métricas do modelo
r2 = r2_score(y, model.predict(X))
mae = mean_absolute_error(y, model.predict(X))
slope = model.coef_[0]
intercept = model.intercept_

# Anotação com métricas
annotation_text = f'Modelo: y = {slope:.2f}x + {intercept:.0f}\nR² = {r2:.3f}\nMAE = R$ {mae:,.0f}'
plt.annotate(annotation_text,
             xy=(0.02, 0.98), xycoords='axes fraction',
             fontsize=10, va='top',
             bbox=dict(boxstyle='round,pad=0.5', facecolor='lightyellow', edgecolor='gray', alpha=0.9))

plt.xlabel('Data', fontsize=11)
plt.ylabel('Vendas Diárias (R$)', fontsize=11)
plt.title('Forecast de Vendas — Próximos 30 dias', fontsize=14, fontweight='bold')
plt.legend(loc='upper left')
plt.grid(True, alpha=0.3)
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

# Mostrar valores previstos
print("\n=== Previsão para os Próximos 30 dias ===")
previsao_df = pd.DataFrame({'Data': datas_futuras, 'Previsao_Vendas': previsoes.round(2)})
print(previsao_df.to_string(index=False))
```

**O que aparece:**
- Linha azul: histórico de vendas
- Linha vermelha tracejada: previsão
- Área rosa: intervalo de confiança
- Anotação com R² e MAE

**Como interpretar as métricas:**
- **R² (0 a 1):** Quanto maior, melhor. Diz quanto das variações nas vendas é explicada pelo tempo. R² = 0.85 significa que 85% da variação é explicada pela passagem do tempo.
- **MAE (R$):** Erro médio absoluto. Se MAE = 500, significa que em média a previsão erra por R$ 500 para mais ou para menos.

**Limitações do forecast linear:**
- Assume que a tendência é linear (linha reta)
- Não captura sazonalidade (picos de fim de ano, por exemplo)
- Para forecasts mais sofisticados, use `statsmodels.tsa` ou bibliotecas especializadas

---

## PARTE 7 — Quando Usar Python vs DAX (15 min)

### A grande pergunta

| Situação | DAX | Python no Power BI |
|----------|-----|---------------------|
| Medida que muda com filtro | ✅ Perfeito | ❌ Não atualiza |
| Time Intelligence | ✅ Perfeito | ⚠️ Possível mas trabalhoso |
| Limpeza básica | ✅ Power Query | ⚠️ Desnecessário |
| Limpeza complexa (regex, NLP) | ❌ Impossível | ✅ Perfeito |
| Visualização avançada | ❌ Limitado | ✅ Qualquer gráfico |
| Clustering/Forecast/ML | ❌ Impossível | ✅ sklearn |
| Performance em milhões de linhas | ✅ Otimizado | ⚠️ Pode ser lento |
| Atualização automática | ✅ Sim | ⚠️ Reexecuta script |

### Regra prática

**Use Python quando:**
1. Você precisa de um gráfico que o Power BI não tem (heatmap, violin plot, etc.)
2. Você precisa de machine learning (clustering, classificação, forecast)
3. Você precisa de processamento de texto avançado

**Use DAX quando:**
1. A medida precisa recalcular conforme o usuário interage com filtros
2. Você está fazendo Time Intelligence
3. Performance é crítica (milhões de linhas)

**Use Power Query (M) quando:**
1. Limpeza simples de dados (renomear, mudar tipo, substituir valores)
2. Você quer que a transformação seja reprocessada automaticamente

### Exemplo prático

**Cenário:** Ricardo quer ver o faturamento por mês.

**Abordagem DAX:**
- Criar medida: `Faturamento = SUM(vendas[valor_final])`
- Colocar num visual de linha com mês no axis
- Quando o usuário filtrar por ano, a medida atualiza automaticamente

**Abordagem Python:**
- Agregar no script: `vendas.groupby(vendas['data'].dt.to_period('M'))['valor_final'].sum()`
- Plotar com `plt.plot()`
- Funciona, mas se o usuário filtrar por ano, o gráfico não atualiza

**Conclusão:** Para medidas que precisam ser dinâmicas, use DAX. Para análises estáticas ou gráficos que não existem no Power BI, use Python.

---

## PARTE 8 — Próximos Passos e Recursos (5 min)

### O que você pode fazer depois da aula

1. **Aprimorar o clustering:**
   - Experimentar diferentes números de clusters (k=3, k=5, etc.)
   - Usar o "Método do Cotovelo" para escolher k ideal
   - Adicionar mais features (idade do cliente, categoria preferida, etc.)

2. **Forecast mais sofisticado:**
   - Usar ARIMA ou Prophet para capturar sazonalidade
   - Adicionar variáveis externas (feriados, promoções)
   - Criar intervalos de confiança mais realistas

3. **WordCloud:**
   - Se você tem descrições de produtos ou comentários de clientes
   - Gerar nuvem de palavras para ver termos mais frequentes

### Recursos para estudar mais

- [Documentação oficial Microsoft - Python no Power BI](https://learn.microsoft.com/pt-br/power-bi/connect-data/desktop-python-scripts)
- [Seaborn Gallery](https://seaborn.pydata.org/examples/) — exemplos de todos os gráficos
- [scikit-learn Documentation](https://scikit-learn.org/stable/) — algoritmos de ML
- [Real Python - Data Visualization](https://realpython.com/python-matplotlib-guide/)

---

## Resumo da Aula

```
┌────────────────────────────────────────────────────────────┐
│                    RESUMO DA AULA                         │
│                                                            │
│  Python no Power BI = superpoderes adicionais              │
│                                                            │
│  3 lugares de uso:                                         │
│  ├── Obter Dados → Script Python                          │
│  │   └── Importar dados gerados ou de APIs                │
│  ├── Power Query → Executar Script Python                 │
│  │   └── Limpeza e engenharia de features                 │
│  └── Visualizações → Python Visual                        │
│      └── Gráficos customizados + ML                       │
│                                                            │
│  Bibliotecas principais:                                   │
│  ├── pandas (dados)                                       │
│  ├── matplotlib/seaborn (visualização)                    │
│  └── sklearn (machine learning)                           │
│                                                            │
│  Quando usar Python vs DAX:                                │
│  ├── DAX → medidas dinâmicas, time intelligence           │
│  └── Python → gráficos avançados, ML, texto              │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

---

## Exercícios para Fazer em Casa

1. **Heatmap de correlação** entre outras variáveis do dataset
2. **KMeans com 5 clusters** — interpretar cada cluster
3. **Forecast de 60 dias** — comparar com o que você conhece do negócio
4. **WordCloud** das categorias de produtos
5. **Comparar gráfico de linha DAX vs Python** — documentar diferenças
