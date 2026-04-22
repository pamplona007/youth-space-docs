# Power BI + Python — Aula 7

> Material do professor para ensinar Power BI + Python com demonstrações práticas.

## Preparação da Aula

### Checklist Antes de Começar

- [ ] Python instalado (3.8+) com pandas, matplotlib, seaborn, scikit-learn, wordcloud
- [ ] Power BI Desktop com Python habilitado (verificar configurações)
- [ ] Dataset de exemplo carregado (VendaMais ou criar novo)
- [ ] Arquivos de exercícios prontos

### Verificar Instalação do Python no Power BI

1. Abra o Power BI Desktop
2. Vá em **Arquivo** → **Opções e Configurações** → **Opções**
3. No menu à esquerda, clique em **Python**
4. Verifique se aparece o caminho do Python instalado
5. Se não aparecer, clique em "Detectar" ou configure manualmente o diretório do Python

### Dataset para Demonstrar

Usaremos a base da **VendaMais Distribuidora** já existente em `recursos/dax/vendas-mais/`. Se preferir criar dados sintéticos durante a aula:

```python
import pandas as pd
import numpy as np

np.random.seed(42)
n = 1000

df = pd.DataFrame({
    'id': range(1, n+1),
    'data': pd.date_range('2024-01-01', periods=n, freq='D'),
    'valor': np.random.exponential(200, n),
    'categoria': np.random.choice(['Eletrônicos', 'Roupas', 'Alimentos', 'Decoração'], n),
    'cliente': [f'Cliente_{i%100}' for i in range(n)]
})

df.to_csv('dados_exemplo.csv', index=False)
print("Dataset criado!")
```

---

## Parte 1 — Configurando Python no Power BI (20 min)

### Onde Configurar

1. **Power BI Desktop aberto** → clique em **Arquivo** (canto superior esquerdo)
2. Role para baixo e clique em **Opções e Configurações**
3. Clique em **Opções** (não em "Configurações de Rastreamento")
4. No painel esquerdo, procure a seção **GLOBAL** (a azul escura)
5. Clique em **Python**

### O que você vai ver

Uma tela com:
- "Diretório base do Python padrão" — mostra o caminho do Python
- Caixa de seleção "Detect other Python IDEs" (normalmente marcada)
- Botão "Detect" (caso não tenha encontrado)

### Se não aparecer caminho nenhum:

1. Clique em **Detect**
2. Se ainda não aparecer, marque "Outro" e navegue até:
   - **Windows**: `C:\Users\SeuNome\AppData\Local\Programs\Python\Python311` (ou similar)
   - Ou simplesmente digite `python` no Prompt de Comando para ver o caminho

### Onde verificar depois que está funcionando

No Power BI, vá em **Obter Dados** → procure "Script Python" na lista. Se aparecer, está funcionando.

---

## Parte 2 — Python como Fonte de Dados (30 min)

### Passo a Passo: Importar Dados via Script Python

1. No Power BI Desktop, clique em **Obter Dados** (na aba Home)
2. Na lista de fontes, digite "Python" na busca ou role até encontrar **Script Python**
3. Clique em **Conectar**
4. Na janela que abrir, escreva o script:

```python
import pandas as pd

# Lendo CSV da VendaMais
df = pd.read_csv('c:/caminho/para/vendas.csv')

# Ou criando dados na hora
import numpy as np
np.random.seed(42)

vendas = pd.DataFrame({
    'id': range(1, 1001),
    'data': pd.date_range('2024-01-01', periods=1000, freq='D').strftime('%Y-%m-%d'),
    'valor': np.random.exponential(200, 1000).round(2),
    'produto': np.random.choice(['A', 'B', 'C', 'D', 'E'], 1000),
    'cliente_id': np.random.randint(1, 101, 1000)
})

print(vendas.head())
```

5. Clique em **OK**
6. O Power BI vai executar o script e mostrar uma prévia dos dados
7. Clique em **Transformar Dados** se quiser fazer ajustes no Power Query, ou **Carregar** para incluir direto no modelo

### O que você vê na tela

- Navigator window mostrando o DataFrame como uma tabela
- Preview com as primeiras linhas
- Botões "Load" (Carregar) e "Transform Data" (Transformar Dados)

### Clique em "Transform Data" para abrir Power Query

Isso é útil quando você quer usar Python para limpar os dados e depois fazer transformações adicionais no Power Query.

---

## Parte 3 — Python no Power Query (40 min)

### Quando usar Python no Power Query?

- Limpeza de dados complexos (regex, strings longas)
- Normalização de texto
- Remoção de outliers
- engenharia de features que seria muito complexa em M

### Passo a Passo: Transformar Dados com Python no Power Query

1. **Power BI Desktop** → clique em **Transformar Dados** (Home → Transform Data)
2. No Power Query Editor, você pode usar Python para transformar uma tabela

#### Como adicionar script Python no Power Query:

1. Na faixa de opções superior do Power Query Editor, procure a aba **Transform**
2. No canto direito, você vai ver **Executar Script Python** (ou "Run Python Script" em inglês)
3. Clique nele

#### Interface que vai aparecer:

Uma janela grande de texto onde você escreve o script Python. A tabela do Power Query fica disponível como um DataFrame chamado `dataset`.

### Exemplo 1: Limpeza de Dados

```
# Limpar coluna de texto - remover espaços extras e converter para maiúscula
dataset['cliente'] = dataset['cliente'].str.strip().str.upper()

# Remover outliers (valores acima de 3 desvios padrão)
mean_val = dataset['valor'].mean()
std_val = dataset['valor'].std()
dataset = dataset[(dataset['valor'] < mean_val + 3*std_val) & 
                   (dataset['valor'] > mean_val - 3*std_val)]

# Normalizar valores entre 0 e 1
dataset['valor_normalizado'] = (dataset['valor'] - dataset['valor'].min()) / (dataset['valor'].max() - dataset['valor'].min())

# Converter datas para formato padrão
dataset['data'] = pd.to_datetime(dataset['data'])
```

### Exemplo 2: Engenharia de Features

```
# Criar colunas derivadas
dataset['ano'] = pd.to_datetime(dataset['data']).dt.year
dataset['mes'] = pd.to_datetime(dataset['data']).dt.month
dataset['dia_semana'] = pd.to_datetime(dataset['data']).dt.day_name()

# Criar flag para fins de semana
dataset['fim_de_semana'] = dataset['dia_semana'].isin(['Saturday', 'Sunday'])

# Criar kategoriasegmento baseadas em valor
dataset['segmento'] = pd.cut(dataset['valor'], 
                             bins=[0, 100, 500, 1000, float('inf')],
                             labels=['Econômico', 'Padrão', 'Premium', 'VIP'])

# Preencher valores nulos com mediana
dataset['valor'] = dataset['valor'].fillna(dataset['valor'].median())
```

### Exemplo 3: Operações de Merge (fusão de tabelas)

Se você carregou duas tabelas no Power Query, pode fundi-las com Python:

```
# Supondo que você tenha duas tabelas: 'vendas' e 'clientes'
# No Power Query, selecione a tabela 'vendas' antes de abrir o script Python

vendas = dataset  # Esta é a tabela atualmente selecionada
# Para acessar outra tabela, você precisaria carregá-la no script
# Isso é mais limitado que o Power Query nativo para merges
```

### Limitações do Python no Power Query

- Script Python é executado uma vez e o resultado vira uma etapa estática
- **Não atualiza dinamicamente** como medidas DAX — se os dados de origem mudarem, você precisa reexecutar o script
- Para dados que mudam frequentemente, prefira transformar no Python externo e recarregar
- Pode ser mais lento que transformações nativas do Power Query para operações simples

---

## Parte 4 — Visuais Python (60 min)

### Onde Encontrar Visuais Python no Power BI

1. No painel de Visualizações (lado direito), procure por:
   - Clique na seta para baixo ao lado dos ícones de gráfico
   - Role até encontrar **Python visual** (ícone de "Py")

### Como Criar Seu Primeiro Visual Python

1. Clique no ícone do **Python visual** no painel de Visualizações
2. Um espaço em branco aparece no canvas com mensagem "Enable script visual"
3. Arraste campos para o painel de dados (valores, eixos, legenda)
4. O Power BI vai mostrar o script Python pronto para você editar na parte inferior da tela

### Exemplo 1: Heatmap de Correlação

1. Arraste campos numéricos para "Values" (ex: `valor`, `qtd`, `custo`)
2. No editor de script Python (parte inferior), escreva:

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Power BI passa os dados como DataFrame 'dataset'
# Filtrar apenas vendas concluídas
df = dataset[dataset['status'] == 'Concluída']

# Selecionar colunas numéricas para correlação
colunas_numericas = df[['valor_final', 'qtd', 'custo_total', 'lucro']].copy()

# Calcular matriz de correlação
correlacao = colunas_numericas.corr()

# Criar heatmap
plt.figure(figsize=(10, 8))
sns.heatmap(correlacao, 
            annot=True, 
            fmt='.2f', 
            cmap='RdYlGn',
            center=0,
            square=True,
            linewidths=0.5,
            cbar_kws={'shrink': 0.8})

plt.title('Mapa de Correlação - VendaMais', fontsize=14, fontweight='bold')
plt.tight_layout()

# O Power BI exibe o gráfico automaticamente
plt.show()
```

### Exemplo 2: Gráfico de Dispersão com Regressão

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
            scatter_kws={'alpha': 0.5, 's': 20},
            line_kws={'color': 'red', 'linewidth': 2})

# Adicionar equação da regressão no gráfico
slope, intercept, r_value, p_value, std_err = stats.linregress(df['valor_original'], df['lucro'])
plt.annotate(f'y = {slope:.2f}x + {intercept:.2f}\nR² = {r_value**2:.3f}', 
             xy=(0.05, 0.95), xycoords='axes fraction',
             fontsize=11, verticalalignment='top',
             bbox=dict(boxstyle='round', facecolor='wheat', alpha=0.5))

plt.title('Relação entre Valor Original e Lucro', fontsize=14, fontweight='bold')
plt.xlabel('Valor Original (R$)')
plt.ylabel('Lucro (R$)')
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

### Exemplo 3: Gráfico de Distribuição

```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

df = dataset.copy()

fig, axes = plt.subplots(1, 2, figsize=(14, 5))

# Histograma
axes[0].hist(df['valor_final'], bins=30, color='#3498db', edgecolor='black', alpha=0.7)
axes[0].axvline(df['valor_final'].mean(), color='red', linestyle='--', label=f'Média: R$ {df["valor_final"].mean():.2f}')
axes[0].axvline(df['valor_final'].median(), color='green', linestyle='--', label=f'Mediana: R$ {df["valor_final"].median():.2f}')
axes[0].set_title('Distribuição do Valor Final', fontsize=12, fontweight='bold')
axes[0].set_xlabel('Valor (R$)')
axes[0].set_ylabel('Frequência')
axes[0].legend()

# Boxplot
axes[1].boxplot(df['valor_final'], vert=True, patch_artist=True,
               boxprops=dict(facecolor='#3498db', alpha=0.7))
axes[1].set_title('Boxplot do Valor Final', fontsize=12, fontweight='bold')
axes[1].set_ylabel('Valor (R$)')

plt.tight_layout()
plt.show()
```

---

## Parte 5 — Forecasting e Clustering (90 min)

### Exemplo 4: Forecast com Regressão Linear

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split

df = dataset.copy()

# Converter data para número (dias desde a primeira data)
df['data'] = pd.to_datetime(df['data'])
df['dias'] = (df['data'] - df['data'].min()).dt.days

# Aggregating data by date
vendas_diarias = df.groupby('dias')['valor_final'].sum().reset_index()

X = vendas_diarias[['dias']]
y = vendas_diarias['valor_final']

# Treinar modelo
model = LinearRegression()
model.fit(X, y)

# Previsões para os próximos 30 dias
ultimo_dia = X['dias'].max()
futuro_dias = np.arange(ultimo_dia + 1, ultimo_dia + 31).reshape(-1, 1)
previsoes = model.predict(futuro_dias)

# Plotar histórico + previsão
plt.figure(figsize=(12, 6))
plt.plot(X['dias'], y, 'b-', label='Histórico', linewidth=2)
plt.plot(futuro_dias, previsoes, 'r--', label='Previsão (30 dias)', linewidth=2)
plt.axvline(ultimo_dia, color='gray', linestyle=':', alpha=0.7)
plt.fill_between(futuro_dias.flatten(), previsoes * 0.9, previsoes * 1.1, 
                color='red', alpha=0.1, label='Intervalo de confiança')

plt.title('Forecast de Vendas - Próximos 30 dias', fontsize=14, fontweight='bold')
plt.xlabel('Dias')
plt.ylabel('Valor Total (R$)')
plt.legend()
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()

# Mostrar métricas
from sklearn.metrics import r2_score, mean_absolute_error
y_pred_treino = model.predict(X)
print(f"R² Score: {r2_score(y, y_pred_treino):.3f}")
print(f"MAE: R$ {mean_absolute_error(y, y_pred_treino):,.2f}")
```

### Exemplo 5: Clustering com KMeans

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler

df = dataset.copy()

# Selecionar features para clustering
features = df[['valor_final', 'qtd', 'lucro']].copy()

# Tratar nulos
features = features.fillna(0)

# Normalizar
scaler = StandardScaler()
features_scaled = scaler.fit_transform(features)

# Encontrar número ideal de clusters (Elbow method)
inertias = []
K_range = range(2, 10)
for k in K_range:
    kmeans = KMeans(n_clusters=k, random_state=42, n_init=10)
    kmeans.fit(features_scaled)
    inertias.append(kmeans.inertia_)

plt.figure(figsize=(10, 5))
plt.plot(K_range, inertias, 'bo-', linewidth=2, markersize=8)
plt.xlabel('Número de Clusters (K)')
plt.ylabel('Inércia')
plt.title('Método do Cotovelo para Escolher K')
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()

# Aplicar KMeans com K=4
kmeans = KMeans(n_clusters=4, random_state=42, n_init=10)
df['cluster'] = kmeans.fit_predict(features_scaled)

# Visualizar clusters
plt.figure(figsize=(10, 6))
scatter = plt.scatter(df['valor_final'], df['lucro'], 
                      c=df['cluster'], cmap='viridis', alpha=0.6, s=30)
plt.colorbar(scatter, label='Cluster')
plt.xlabel('Valor Final (R$)')
plt.ylabel('Lucro (R$)')
plt.title('Clientes Segmentados por Cluster', fontsize=14, fontweight='bold')
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()

# Estatísticas por cluster
estatisticas = df.groupby('cluster')[['valor_final', 'lucro', 'qtd']].agg(['mean', 'count'])
print(estatisticas)
```

### Exemplo 6: WordCloud de Descrições

```python
import pandas as pd
import matplotlib.pyplot as plt
from wordcloud import WordCloud
from collections import Counter
import re

df = dataset.copy()

# Supondo que você tenha uma coluna de texto (ex: descrições de produtos)
texto = ' '.join(df['produto'].astype(str).tolist())

# Limpar texto
texto_limpo = re.sub(r'[^\w\s]', '', texto.lower())

# Gerar wordcloud
wordcloud = WordCloud(width=800, height=400, 
                      background_color='white',
                      colormap='viridis',
                      max_words=100,
                      random_state=42).generate(texto_limpo)

plt.figure(figsize=(12, 6))
plt.imshow(wordcloud, interpolation='bilinear')
plt.axis('off')
plt.title('Palavras mais Frequentes nos Produtos', fontsize=14, fontweight='bold')
plt.tight_layout()
plt.show()

# Contagem de palavras
palavras = texto_limpo.split()
contagem = Counter(palavras).most_common(10)
print("Top 10 palavras:", contagem)
```

---

## Parte 6 — Quando Usar Python vs DAX (20 min)

### Comparação Prática

| Situação | DAX | Python |
|---------|-----|--------|
| Medidas agregadas simples | ✅ Rápido | ❌ Overkill |
| Time Intelligence | ✅ Nativo | ⚠️ Possível mas lento |
| Limpeza básica (tipos, nomes) | ✅ Power Query | ⚠️ Desnecessário |
| Limpeza complexa (regex, NLP) | ❌ Impossível | ✅ Ideal |
| Visuais customizados | ❌ Limitado | ✅ Matplotlib, Seaborn |
| Forecasting/ML | ❌ Limitado | ✅ sklearn, statsmodels |
| Clustering | ❌ Impossível | ✅ KMeans, DBSCAN |
| Atualização automática | ✅ Sim | ⚠️ Reexecuta script |
| Performance em grandes volumes | ✅ Otimizado | ⚠️ Pode ser lento |

### Recomendação

- **Use Python no Power BI** quando precisar de:
  - Visualizações statisticamente complexas (heatmap, pairplot)
  - Machine learning (clustering, forecast, classificação)
  - Processamento de texto (NLP, wordcloud, regex)
  - Transformações que seriam muito complexas em M ou DAX

- **Use DAX nativo** quando precisar de:
  - Medidas rápidas que mudam conforme filtros
  - Time Intelligence
  -银Dashboard com alta performance
  - Relatórios que precisam atualizar em tempo real

---

## Exercícios Práticos

### Exercício 1: Heatmap de Correlação

1. Importe o CSV de vendas da VendaMais no Power BI
2. No Power Query, use script Python para:
   - Selecionar apenas vendas "Concluída"
   - Remover outliers (valores acima de 3 desvios padrão)
3. Crie um visual Python com heatmap mostrando correlação entre:
   - `valor_original`, `valor_final`, `custo_total`, `lucro`
4. Formate o heatmap com cores intuitivas (verde=positivo, vermelho=negativo)

**Resultado esperado:** Matriz 4x4 com valores de correlação e cores.

### Exercício 2: Segmentação de Clientes com KMeans

1. Carregue os dados de vendas da VendaMais
2. No Python (via Obter Dados ou Power Query), agregue por cliente:
   - Total de vendas
   - Quantidade de compras
   - Ticket médio
   - Lucro total
3. Aplique KMeans com 4 clusters
4. Visualize os clusters em scatter plot (valor_total vs lucro_total)
5. Interprete cada cluster (ex: "Clientes VIP", "Clientes casuais")

**Resultado esperado:** Scatter plot colorido por cluster + tabela com perfil de cada grupo.

### Exercício 3: Forecast de Vendas

1. Carregue dados de vendas com coluna de data
2. Use Python para agregar vendas por dia
3. Treine modelo de regressão linear
4. Preveja vendas para os próximos 30 dias
5. Plote histórico + previsão com intervalo de confiança

**Resultado esperado:** Gráfico com linha azul (histórico), linha vermelha tracejada (previsão), área rosa (confiança).

### Exercício 4: WordCloud de Produtos

1. Carregue tabela de produtos (ou descrições de produtos)
2. Use Python para gerar wordcloud das categorias/nomes
3. Customize com cores e tamanho máximo de palavras
4. Mostre top 5 palavras mais frequentes

**Resultado esperado:** Nuvem de palavras com tamanho proporcional à frequência.

---

## Gabarito Rápido

### Configuração
- **Arquivo** → **Opções e Configurações** → **Opções** → **Python**

### Script Python como Fonte
- **Obter Dados** → **Script Python** → escrever script → **OK**

### Python no Power Query
- **Transformar Dados** → selecionar tabela → **Transform** → **Executar Script Python**

### Visual Python
- Painel de visualizações → ícone Python (Py) → arrastar campos → escrever script em baixo

### Bibliotecas Suportadas
- pandas, numpy, matplotlib, seaborn, scikit-learn, scipy, statsmodels
- pillow, wordcloud, nltk (se instalado)