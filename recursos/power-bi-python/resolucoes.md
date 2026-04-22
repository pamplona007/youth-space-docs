# Resoluções — Power BI + Python (Aula 7)

## Como Usar Este Arquivo

Estas resoluções são **passo a passo detalhado** para os exercícios. Cada exercício mostra:
1. **O que você vai ver na tela** (interface)
2. **Exatamente onde clicar**
3. **O que digitar/colar**
4. **O resultado esperado**

Use para corrigir durante a aula ou para estudo individual.

---

## Exercício 1 — Configuração do Python

### Passo a Passo

**Onde você está no Power BI:**

```
Power BI Desktop aberto, tela branca (canvas)
```

**1. Clique no menu Arquivo**

```
Arquivo (canto superior esquerdo, ícone azul) → abre menu dropdown
```

**2. Vá em Opções e Configurações**

```
Arquivo
├── Novo
├── Abrir...
├── Obter Dados
├── Opções e Configurações    ← CLICAR AQUI
│   ├── Configurações de Rastreamento
│   └── Opções                ← CLICAR AQUI
├── Salvar
└── ...
```

**3. No painel esquerdo da janela Opções**

```
Procure a seção GLOBAL (texto azul escuro, canto superior esquerdo)
Role para baixo até encontrar "Python"
Clique em "Python"

┌─────────────────────────────────────────────────────────────┐
│ OPÇÕES E CONFIGURAÇÕES                                      │
│                                                             │
│ GLOBAL                                                      │
│ ├── Geral                                                   │
│ ├── Arquivo atual                                           │
│ ├── carregamento de dados                                   │
│ ├── Consultas de DirectQuery                                │
│ ├── Segurança                                                │
│ ├── Privacidade                                             │
│ ├── Funcionalidades de visualização em prévia               │
│ ├── Atualização automática do conjunto de resultados       │
│ └── Python                                             ← VOCÊ ESTÁ AQUI
└─────────────────────────────────────────────────────────────┘
```

**4. O que você deve ver na tela**

```
┌─────────────────────────────────────────────────────────────┐
│  Python                                                     │
│                                                             │
│  Opções de script Python                                   │
│  ─────────────────────                                     │
│                                                             │
│  Diretório base do Python padrão:                          │
│  [C:\Users\SeuNome\AppData\Local\Programs\Python\Python311]│
│                                                             │
│  [✓] Detect other Python IDEs                               │
│                                                             │
│  [Detectar]                                                 │
│                                                             │
│  Linguagens de script Python instalados:                   │
│  Python 3.11 (64-bit)                                      │
└─────────────────────────────────────────────────────────────┘
```

**Se o caminho aparecer:** ✅ Configurado corretamente!

**Se NÃO aparecer:**
1. Verifique se o Python está instalado: abra Prompt de Comando e digite `python --version`
2. Se instalado, clique em **Detectar**
3. Se ainda não funcionar, Instale Python manualmente e aponte o diretório

---

## Exercício 2 — Importar Dados via Script Python

### Passo a Passo

**1. Vá em Obter Dados**

```
Ribbon (faixa de opções superior) → aba HOME → botão "Obter Dados"
                                                    ↓
                                            Dropdown aparece
                                            ├─ Mais...
                                            ├─ Arquivo
                                            │   ├─ Texto/CSV
                                            │   ├─ Excel
                                            │   └─ ...
                                            ├─ Banco de dados
                                            │   └─ ...
                                            └─ Online Services
                                            └─ ...
                                            └─ Script Python   ← CLICAR
```

**2. Na janela "Script Python"**

```
┌─────────────────────────────────────────────────────────────┐
│  Script Python                                      [X]    │
│                                                             │
│  Cole seu script Python abaixo:                             │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ import pandas as pd                                  │   │
│  │                                                       │   │
│  │ import numpy as np                                   │   │
│  │ np.random.seed(42)                                    │   │
│  │                                                       │   │
│  │ datas = pd.date_range('2024-01-01', periods=2000,     │   │
│  │                        freq='D')                      │   │
│  │ vendas = pd.DataFrame({                               │   │
│  │     'id_venda': range(1, 2001),                       │   │
│  │     'data': datas,                                    │   │
│  │     'id_cliente': np.random.randint(1, 501, 2000),    │   │
│  │     'id_produto': np.random.randint(1, 101, 2000),    │   │
│  │     'id_vendedor': np.random.randint(1, 21, 2000),    │   │
│  │     'qtd': np.random.randint(1, 10, 2000),           │   │
│  │     'preco_unitario': np.random.uniform(10, 500,      │   │
│  │                           2000).round(2),             │   │
│  │     'custo_unitario': np.random.uniform(5, 250,      │   │
│  │                           2000).round(2),             │   │
│  │     'desconto': np.random.uniform(0, 0.2, 2000)       │   │
│  │                .round(2),                            │   │
│  │     'status': np.random.choice(['Concluída',          │   │
│  │                 'Cancelada', 'Devolvida'],            │   │
│  │                 2000, p=[0.85, 0.10, 0.05])          │   │
│  │ })                                                    │   │
│  │                                                       │   │
│  │ vendas['valor_original'] = vendas['qtd'] *            │   │
│  │                             vendas['preco_unitario']  │   │
│  │ vendas['valor_final'] = vendas['valor_original'] *    │   │
│  │                         (1 - vendas['desconto'])      │   │
│  │ vendas['custo_total'] = vendas['qtd'] *               │   │
│  │                          vendas['custo_unitario']     │   │
│  │ vendas['lucro'] = vendas['valor_final'] -             │   │
│  │                   vendas['custo_total']               │   │
│  │                                                       │   │
│  │ print(vendas.head())                                  │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│                                          [OK]  [Cancelar]   │
└─────────────────────────────────────────────────────────────┘
```

**3. Clique OK**

- O Power BI vai executar o script Python (pode levar 10-30 segundos)
- Aparecera uma janela "Navegador" mostrando o DataFrame `vendas`

**4. Na janela Navegador**

```
┌─────────────────────────────────────────────────────────────┐
│  Navegador                                            [X]   │
│                                                             │
│  ┌──────────────────┐  Visualização de dados               │
│  │ ▼ Scripts Python │  ┌─────────────────────────────────┐  │
│  │                  │  │   vendas                       │  │
│  │   ▼ vendas       │  │                                 │  │
│  │       Visualiza- │  │ id_venda  data   id_cliente... │  │
│  │       ção de     │  │ 1        2024-01-01  234        │  │
│  │       dados      │  │ 2        2024-01-02  87         │  │
│  │                  │  │ 3        2024-01-03  152        │  │
│  │       [✓]        │  │ ...                            │  │
│  │                  │  │                                 │  │
│  └──────────────────┘  └─────────────────────────────────┘  │
│                                                             │
│                           [Transformar Dados] [OK]          │
└─────────────────────────────────────────────────────────────┘
```

**5. Clique em OK (ou Transformar Dados se quiser abrir Power Query)**

- Dados serão carregados no modelo
- Painel de campos agora mostra a tabela `vendas` com todas as colunas

---

## Exercício 3 — Python no Power Query

### Passo a Passo

**1. Abra o Power Query**

```
Ribbon → aba HOME → "Transformar Dados" (ícone de tabela com铅笔)
                                                    ↓
                                            Power Query Editor abre
```

**2. No Power Query Editor, selecione a tabela vendas**

```
┌─────────────────────────────────────────────────────────────┐
│ Power Query Editor                                          │
│                                                             │
│  Painel esquerdo (Consultas):                               │
│  ├─ vendas                    ← CLIQUE AQUI                │
│  └─ ...                                                      │
└─────────────────────────────────────────────────────────────┘
```

**3. Vá na aba Transform**

```
Ribbon (faixa de opções superior) →
├── Home
├── Transform       ← CLICAR NESTA ABA
├── Add Column
└── View

Na aba Transform, procure no canto direito:
┌─────────────────────────────────────────────────────────────┐
│ Transform | Add Column | View                               │
│                                                             │
│ No grupo "Run Python script" (canto direito da faixa):     │
│   [Python icon] Executar Script Python      ← CLIQUE AQUI   │
└─────────────────────────────────────────────────────────────┘
```

**4. Janela "Executar Script Python"**

```
┌─────────────────────────────────────────────────────────────┐
│  Executar Script Python                              [X]   │
│                                                             │
│  Insira o script Python. O conjunto de dados atual está    │
│  disponível como variável 'dataset'.                       │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ # Remover vendas canceladas                          │   │
│  │ dataset = dataset[dataset['status'] == 'Concluída']  │   │
│  │                                                       │   │
│  │ # Criar novas colunas                                │   │
│  │ dataset['valor_original'] = dataset['qtd'] *         │   │
│  │                              dataset['preco_unitario']│   │
│  │ dataset['valor_final'] = dataset['valor_original'] * │   │
│  │                        (1 - dataset['desconto'])     │   │
│  │ dataset['custo_total'] = dataset['qtd'] *            │   │
│  │                         dataset['custo_unitario']     │   │
│  │ dataset['lucro'] = dataset['valor_final'] -         │   │
│  │                   dataset['custo_total']              │   │
│  │                                                       │   │
│  │ # Extrair ano e mês da data                          │   │
│  │ dataset['data'] = pd.to_datetime(dataset['data'])     │   │
│  │ dataset['ano'] = dataset['data'].dt.year             │   │
│  │ dataset['mes'] = dataset['data'].dt.month             │   │
│  │                                                       │   │
│  │ # Criar coluna de segmento por valor                  │   │
│  │ dataset['segmento'] = pd.cut(dataset['valor_final'],  │   │
│  │                             bins=[0, 100, 500, 1000,  │   │
│  │                             float('inf')],            │   │
│  │                             labels=['Econômico',       │   │
│  │                             'Padrão', 'Premium', 'VIP'])│   │
│  │                                                       │   │
│  │ # Remover outliers                                    │   │
│  │ mean_val = dataset['valor_final'].mean()             │   │
│  │ std_val = dataset['valor_final'].std()               │   │
│  │ dataset = dataset[                                    │   │
│  │     (dataset['valor_final'] < mean_val + 3*std_val) & │   │
│  │     (dataset['valor_final'] > mean_val - 3*std_val)]  │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│                                          [OK]  [Cancelar]   │
└─────────────────────────────────────────────────────────────┘
```

**5. Clique OK**

- O Power BI executa o script (pode levar alguns segundos)
- Resultado aparece como novas colunas adicionadas à tabela

**6. Verifique o resultado**

```
No Power Query Editor, você verá:
- Colunas originais (id_venda, data, id_cliente, etc.)
- Novas colunas: valor_original, valor_final, custo_total, lucro, ano, mes, segmento

┌─────────────────────────────────────────────────────────────┐
│ Consulta: vendas                        [Preview]            │
│                                                             │
│ id_venda | data | ... | valor_original | valor_final | ... │
│     1   | ...  |    |     1500.00     |   1350.00   | ...  │
│     2   | ...  |    |     2300.00     |   2070.00   | ...  │
└─────────────────────────────────────────────────────────────┘
```

**7. Salvar e fechar**

```
Ribbon → Home → "Fechar e Aplicar" (Close & Apply)
                            ↓
                    Power BI importa os dados
                    e retorna ao canvas
```

---

## Exercício 4 — Heatmap de Correlação

### Passo a Passo

**1. Criar visual Python**

```
No painel de Visualizações (direita):
┌───────────────────────────────────────┐
│ [Bar Chart] [Col Chart] [Line] ...    │
│                                       │
│ Clique na SETA PARA BAIXO (▼) para    │
│ expandir o catálogo de visuais       │
│                                       │
│ Role até encontrar:                   │
│   🐍 Python visual          ← CLIQUE  │
└───────────────────────────────────────┘
```

**2. O que aparece no canvas**

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│     ┌─────────────────────────────────────────────────┐     │
│     │                                                 │     │
│     │   Habilitar script visual                       │     │
│     │                                                 │     │
│     │   Para habilitar um visual de script Python,    │     │
│     │   arraste campos de dados para este visual.     │     │
│     │                                                 │     │
│     │   [+ Adicionar dados]                           │     │
│     │                                                 │     │
│     └─────────────────────────────────────────────────┘     │
│                                                             │
│  Painel de campos (direita):                               │
│  Não há campos especificados                               │
└─────────────────────────────────────────────────────────────┘
```

**3. Arraste campos para o visual**

```
No painel de campos (direita do canvas):
│ vendas
│ ├── id_venda
│ ├── data
│ ├── id_cliente
│ ├── id_produto
│ ├── id_vendedor
│ ├── qtd
│ ├── preco_unitario
│ ├── custo_unitario
│ ├── desconto
│ ├── status
│ ├── valor_original      ← ARRASTE PARA O VISUAL
│ ├── valor_final          ← ARRASTE PARA O VISUAL
│ ├── custo_total          ← ARRASTE PARA O VISUAL
│ ├── lucro                ← ARRASTE PARA O VISUAL
│ ├── ano
│ ├── mes
│ └── segmento

Ao arrastar, solte no painel "Values" do visual:
┌─────────────────────────────────────┐
│ Values                              │
│                                     │
│ valor_original (Σ)                  │
│ valor_final (Σ)                     │
│ custo_total (Σ)                     │
│ lucro (Σ)                           │
└─────────────────────────────────────┘
```

**4. Editor de script Python aparece**

```
Na parte inferior da tela do Power BI:

┌─────────────────────────────────────────────────────────────┐
│ Python visual script editor                                  │
│                                                             │
│  # Correlação entre variáveis numéricas                     │
│  colunas = ['valor_original', 'valor_final', 'custo_total', │
│             'lucro']                                        │
│  df = dataset[colunas].copy()                               │
│                                                             │
│  correlacao = df.corr()                                     │
│                                                             │
│  plt.figure(figsize=(10, 8))                                │
│  sns.heatmap(correlacao, annot=True, fmt='.2f',             │
│              cmap='RdYlGn', center=0, square=True,          │
│              linewidths=0.5, cbar_kws={'shrink': 0.8})       │
│  plt.title('Correlação entre Variáveis de Vendas',         │
│            fontsize=14, fontweight='bold', pad=20)          │
│  plt.tight_layout()                                         │
│  plt.show()                                        [▶ Run]  │
└─────────────────────────────────────────────────────────────┘
```

**5. Cole o script completo e clique em Run**

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Power BI passa os dados selecionados como DataFrame 'dataset'
colunas = ['valor_original', 'valor_final', 'custo_total', 'lucro']
df = dataset[colunas].copy()

# Calcular matriz de correlação
correlacao = df.corr()

# Criar heatmap
plt.figure(figsize=(10, 8))
sns.heatmap(correlacao, 
            annot=True, 
            fmt='.2f', 
            cmap='RdYlGn',
            center=0,
            square=True,
            linewidths=0.5,
            cbar_kws={'shrink': 0.8, 'label': 'Correlação'})

plt.title('Correlação entre Variáveis de Vendas', fontsize=14, fontweight='bold', pad=20)
plt.tight_layout()
plt.show()
```

**6. Resultado esperado**

```
Um heatmap 4x4 aparecera no canvas do Power BI, mostrando:
- valor_original correlacionado com valor_final (~0.99, verde forte)
- valor_original correlacionado com custo_total (~0.98, verde forte)
- lucro correlacionado com valor_original, valor_final (correlações variáveis)
- Cores: verde = correlação positiva forte, vermelho = correlação negativa
```

---

## Exercício 5 — Clustering KMeans

### Passo a Passo

**1. Crie um novo visual Python (clique em outro espaço em branco ou no ícone Python novamente)**

**2. Para clustering, você precisa de dados agregados por cliente. Use Python no Power Query primeiro:**

```
Power Query → selecionar tabela vendas → Transform → Executar Script Python:

# Agregar dados por cliente
cliente_stats = dataset.groupby('id_cliente').agg({
    'valor_final': ['sum', 'mean', 'count'],
    'lucro': 'sum',
    'qtd': 'sum'
}).reset_index()

cliente_stats.columns = ['id_cliente', 'total_vendas', 'ticket_medio', 
                         'num_compras', 'lucro_total', 'qtd_total']

# Isso cria uma nova tabela no Power Query (cliente_stats)
```

**3. No visual Python, arraste:**
- `total_vendas` → Values
- `ticket_medio` → Values
- `lucro_total` → Values

**4. Cole o script:**

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler

# Preparar features
features = dataset[['total_vendas', 'ticket_medio', 'lucro_total']].copy()
features = features.fillna(0)

# Normalizar
scaler = StandardScaler()
features_scaled = scaler.fit_transform(features)

# Clustering com 4 clusters
n_clusters = 4
kmeans = KMeans(n_clusters=n_clusters, random_state=42, n_init=10)
dataset['Cluster'] = kmeans.fit_predict(features_scaled)

# Visualizar
plt.figure(figsize=(12, 6))

# Scatter plot
plt.subplot(1, 2, 1)
colors = ['#FF6B6B', '#4ECDC4', '#45B7D1', '#96CEB4']
for i in range(n_clusters):
    cluster_data = dataset[dataset['Cluster'] == i]
    plt.scatter(cluster_data['total_vendas'], 
               cluster_data['lucro_total'],
               c=colors[i], 
               label=f'Cluster {i}',
               alpha=0.6, s=50)

plt.xlabel('Total de Vendas (R$)')
plt.ylabel('Lucro Total (R$)')
plt.title('Segmentação de Clientes')
plt.legend()
plt.grid(True, alpha=0.3)

# Bar chart de tamanhos dos clusters
plt.subplot(1, 2, 2)
cluster_sizes = dataset['Cluster'].value_counts().sort_index()
bars = plt.bar(range(n_clusters), cluster_sizes.values, color=colors)
plt.xlabel('Cluster')
plt.ylabel('Número de Clientes')
plt.title('Tamanho dos Clusters')
plt.xticks(range(n_clusters))

for bar, size in zip(bars, cluster_sizes.values):
    plt.text(bar.get_x() + bar.get_width()/2, bar.get_height() + 5, 
             str(size), ha='center', va='bottom')

plt.tight_layout()
plt.show()
```

**5. Clique Run — resultado esperado:**

```
Scatter plot colorido com 4 grupos de pontos em cores diferentes
Barras mostrando quantidade de clientes em cada cluster
```

---

## Exercício 6 — Forecasting

### Passo a Passo

**1. Crie novo visual Python**

**2. Arraste `data` para Axis e `valor_final` para Values**

**3. Script:**

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression
from sklearn.metrics import r2_score, mean_absolute_error

# Converter e agregar
dataset['data'] = pd.to_datetime(dataset['data'])
vendas_diarias = dataset.groupby('data')['valor_final'].sum().reset_index()
vendas_diarias = vendas_diarias.sort_values('data')

# Feature de dias
vendas_diarias['dias'] = (vendas_diarias['data'] - vendas_diarias['data'].min()).dt.days

X = vendas_diarias[['dias']]
y = vendas_diarias['valor_final']

# Treinar modelo
model = LinearRegression()
model.fit(X, y)

# Previsão
ultimo_dia = vendas_diarias['dias'].max()
dias_futuros = np.arange(ultimo_dia + 1, ultimo_dia + 31).reshape(-1, 1)
previsoes = model.predict(dias_futuros)

ultima_data = vendas_diarias['data'].max()
datas_futuras = pd.date_range(start=ultima_data + pd.Timedelta(days=1), periods=30)

# Plot
plt.figure(figsize=(14, 7))
plt.plot(vendas_diarias['data'], y, 'b-', linewidth=1.5, label='Histórico', alpha=0.7)
plt.plot(datas_futuras, previsoes, 'r--', linewidth=2, label='Previsão (30 dias)')
plt.fill_between(datas_futuras, previsoes * 0.85, previsoes * 1.15, 
                 color='red', alpha=0.15, label='Intervalo de Confiança')
plt.axvline(ultima_data, color='gray', linestyle=':', alpha=0.7)
plt.xlabel('Data')
plt.ylabel('Vendas Diárias (R$)')
plt.title('Forecast de Vendas — Próximos 30 dias')
plt.legend()
plt.grid(True, alpha=0.3)
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()

# Métricas
r2 = r2_score(y, model.predict(X))
mae = mean_absolute_error(y, model.predict(X))
print(f"R²: {r2:.3f}")
print(f"MAE: R$ {mae:,.0f}")
```

**Resultado esperado:**

```
Gráfico mostrando:
- Linha azul: histórico de vendas
- Linha vermelha tracejada: previsão
- Área rosa: intervalo de confiança
- Linha vertical pontilhada: ponto de separação histórico/previsão
```

---

## Exercício 7 — WordCloud

### Script:

```python
import pandas as pd
import matplotlib.pyplot as plt
from wordcloud import WordCloud
from collections import Counter
import re

texto = ' '.join(dataset['categoria'].astype(str))

texto_limpo = re.sub(r'[^\w\s]', '', texto.lower())

wordcloud = WordCloud(width=1000, height=500, 
                      background_color='white',
                      colormap='viridis',
                      max_words=50,
                      random_state=42).generate(texto_limpo)

plt.figure(figsize=(14, 7))
plt.imshow(wordcloud, interpolation='bilinear')
plt.axis('off')
plt.title('Palavras mais Frequentes nos Produtos')
plt.tight_layout()
plt.show()
```

---

## Resumo: Onde Clicar

|操作 |在哪里 |
|-----|-------|
| Configurar Python | Arquivo → Opções e Configurações → Opções → Python |
| Importar via Python | Obter Dados → Script Python |
| Python no Power Query | Transformar Dados → Transform → Executar Script Python |
| Visual Python | Painel de Visualizações → (expandir) → Python visual |
| Executar script | Botão ▶ Run ao lado do editor Python |