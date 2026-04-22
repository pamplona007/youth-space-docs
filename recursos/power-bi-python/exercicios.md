# Exercícios — Power BI + Python (Aula 7)

## Contexto

Você é-analista de dados na **VendaMais Distribuidora**. O gerente Ricardo quer entender melhor seus dados usando Python no Power BI. Ele ouviu falar que é possível usar bibliotecas como pandas, seaborn e sklearn diretamente no Power BI e quer que você explore isso.

**Dataset:** Use os CSVs da pasta `recursos/dax/vendas-mais/` (clientes, produtos, vendas, vendedores, calendario).

---

## Exercício 1 — Configuração e Primeiro Visual

### Objetivo
Verificar se o Python está configurado corretamente no Power BI.

**Passos no Power BI:**

1. Abra o Power BI Desktop
2. Clique em **Arquivo** (canto superior esquerdo)
3. Clique em **Opções e Configurações**
4. Clique em **Opções**
5. No painel esquerdo, procure **Python** (está em GLOBAL, perto do fundo)
6. Verifique se aparece um caminho em "Diretório base do Python padrão"

**Se o caminho aparecer:** Parabéns, o Python já está configurado! ✅

**Se não aparecer:**
1. Clique em **Detectar**
2. Se ainda não funcionar, instale o Python manualmente e aponte para a pasta

**Entrega:** Screenshot da tela de configuração mostrando o caminho do Python.

---

## Exercício 2 — Importar Dados via Script Python

### Objetivo
Importar os dados de vendas usando um script Python em vez do conector CSV padrão.

**Passos no Power BI:**

1. Clique em **Obter Dados** (na aba Home, faixa de opções superior)
2. Na lista de fontes, digite "Python" na busca ou role até encontrar **Script Python**
3. Clique em **Conectar**

**Script para usar:**

```python
import pandas as pd

# Criar dataset sintético para demonstrar (substitua pelo caminho real do CSV se preferir)
import numpy as np
np.random.seed(42)

# Gerar dados de exemplo
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
    'status': np.random.choice(['Concluída', 'Cancelada', 'Devolvida'], 2000, p=[0.85, 0.10, 0.05])
})

vendas['valor_original'] = vendas['qtd'] * vendas['preco_unitario']
vendas['valor_final'] = vendas['valor_original'] * (1 - vendas['desconto'])
vendas['custo_total'] = vendas['qtd'] * vendas['custo_unitario']
vendas['lucro'] = vendas['valor_final'] - vendas['custo_total']

print(vendas.head())
```

4. Clique em **OK**
5. Na janela "Navegador", você verá o DataFrame `vendas` — clique em **Carregar**

**Entrega:** Screenshot do Power BI mostrando as colunas carregadas no painel de campos.

---

## Exercício 3 — Limpeza de Dados no Power Query com Python

### Objetivo
Usar Python no Power Query para limpar dados e criar novas colunas.

**Passos no Power BI:**

1. Clique em **Transformar Dados** (Home → Transform Data)
2. No Power Query Editor, você verá a tabela `vendas`
3. Na faixa de opções superior, vá em **Transform**
4. No canto direito, clique em **Executar Script Python** (ou "Run Python Script")
5. Na janela que abrir, escreva:

```python
# Remover vendas canceladas
dataset = dataset[dataset['status'] == 'Concluída']

# Criar novas colunas
dataset['valor_original'] = dataset['qtd'] * dataset['preco_unitario']
dataset['valor_final'] = dataset['valor_original'] * (1 - dataset['desconto'])
dataset['custo_total'] = dataset['qtd'] * dataset['custo_unitario']
dataset['lucro'] = dataset['valor_final'] - dataset['custo_total']

# Extrair ano e mês da data
dataset['data'] = pd.to_datetime(dataset['data'])
dataset['ano'] = dataset['data'].dt.year
dataset['mes'] = dataset['data'].dt.month

# Criar coluna de segmento por valor
dataset['segmento'] = pd.cut(dataset['valor_final'], 
                            bins=[0, 100, 500, 1000, float('inf')],
                            labels=['Econômico', 'Padrão', 'Premium', 'VIP'])

# Remover outliers (acima de 3 desvios padrão)
mean_val = dataset['valor_final'].mean()
std_val = dataset['valor_final'].std()
dataset = dataset[(dataset['valor_final'] < mean_val + 3*std_val) & 
                   (dataset['valor_final'] > mean_val - 3*std_val)]
```

6. Clique em **OK**
7. Observe o resultado — as colunas aparecerão no Power Query
8. Clique em **Fechar e Aplicar** (Home → Close & Apply)

**Entrega:** Screenshot mostrando as novas colunas no Power Query Editor.

---

## Exercício 4 — Heatmap de Correlação com Seaborn

### Objetivo
Criar um visual Python mostrando correlação entre variáveis de vendas.

**Passos no Power BI:**

1. Com a tabela de vendas carregada, clique em qualquer espaço vazio do canvas
2. No painel de Visualizações (direita), clique na seta para baixo ao lado dos ícones
3. Procure e selecione **Python visual** (ícone com "Py")
4. Um espaço de visual aparecera no canvas com mensagem "Enable script visual"
5. Arraste os campos numéricos para o painel de dados:
   - Arraste `valor_original` para "Values"
   - Arraste `valor_final` para "Values"
   - Arraste `custo_total` para "Values"
   - Arraste `lucro` para "Values"

**Script Python para colar no editor (parte inferior da tela):**

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Power BI passa os dados selecionados como DataFrame 'dataset'
# Correlação entre variáveis numéricas
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

# Importante: manter apenas plt.show() ou apenas a figura
plt.show()
```

6. Clique no botão **▶** (Play/Executar) ao lado do script
7. O heatmap aparecera no canvas

**Entrega:** Screenshot do visual Python mostrando o heatmap de correlação.

---

## Exercício 5 — Clustering de Clientes com KMeans

### Objetivo
Segmentar clientes usando KMeans e visualizar os clusters.

**Preparação (no Power Query):**

1. Abra o Power Query (Transformar Dados)
2. Selecione a tabela `vendas` ou `clientes`
3. Use Python para agregar dados por cliente:

```python
# Agregar vendas por cliente
df = dataset.merge(clientes, on='id_cliente') if you have a clientes table
# Otherwise, work with vendas directly

# Agregar por cliente
cliente_stats = dataset.groupby('id_cliente').agg({
    'valor_final': ['sum', 'mean', 'count'],
    'lucro': 'sum',
    'qtd': 'sum'
}).reset_index()

# Flatten column names
cliente_stats.columns = ['id_cliente', 'total_vendas', 'ticket_medio', 'num_compras', 'lucro_total', 'qtd_total']
```

**Script Python no Visual:**

1. Crie um novo visual Python no canvas
2. Arraste `total_vendas`, `ticket_medio`, `lucro_total` para Values

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler

# Selecionar features para clustering
features = dataset[['total_vendas', 'ticket_medio', 'lucro_total']].copy()

# Tratar valores nulos
features = features.fillna(0)

# Normalizar os dados
scaler = StandardScaler()
features_scaled = scaler.fit_transform(features)

# Definir número de clusters (usando 4)
n_clusters = 4
kmeans = KMeans(n_clusters=n_clusters, random_state=42, n_init=10)
dataset['Cluster'] = kmeans.fit_predict(features_scaled)

# Visualizar
plt.figure(figsize=(12, 6))

# Scatter plot: total_vendas vs lucro_total
plt.subplot(1, 2, 1)
colors = ['#FF6B6B', '#4ECDC4', '#45B7D1', '#96CEB4']
for i in range(n_clusters):
    cluster_data = dataset[dataset['Cluster'] == i]
    plt.scatter(cluster_data['total_vendas'], 
               cluster_data['lucro_total'],
               c=colors[i], 
               label=f'Cluster {i}',
               alpha=0.6, s=50)

plt.xlabel('Total de Vendas (R$)', fontsize=11)
plt.ylabel('Lucro Total (R$)', fontsize=11)
plt.title('Segmentação de Clientes por Cluster', fontsize=12, fontweight='bold')
plt.legend()
plt.grid(True, alpha=0.3)

# Gráfico de barras com tamanho dos clusters
plt.subplot(1, 2, 2)
cluster_sizes = dataset['Cluster'].value_counts().sort_index()
bars = plt.bar(range(n_clusters), cluster_sizes.values, color=colors)
plt.xlabel('Cluster', fontsize=11)
plt.ylabel('Número de Clientes', fontsize=11)
plt.title('Tamanho dos Clusters', fontsize=12, fontweight='bold')
plt.xticks(range(n_clusters))

for bar, size in zip(bars, cluster_sizes.values):
    plt.text(bar.get_x() + bar.get_width()/2, bar.get_height() + 5, 
             str(size), ha='center', va='bottom', fontsize=10)

plt.tight_layout()
plt.show()

# Mostrar estatísticas dos clusters
print("\n=== Perfil dos Clusters ===")
estatisticas = dataset.groupby('Cluster')[['total_vendas', 'ticket_medio', 'lucro_total']].mean()
print(estatisticas.round(2))
```

**Entrega:** Screenshot do scatter plot colorido mostrando os clusters.

---

## Exercício 6 — Forecasting de Vendas

### Objetivo
Criar um modelo de regressão linear para prever vendas dos próximos 30 dias.

**Script Python no Visual:**

1. Crie um novo visual Python
2. Arraste `data` para Axis e `valor_final` para Values (ou agregue por dia primeiro)

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

# Criar feature de dias
vendas_diarias['dias'] = (vendas_diarias['data'] - vendas_diarias['data'].min()).dt.days

X = vendas_diarias[['dias']]
y = vendas_diarias['valor_final']

# Treinar modelo
model = LinearRegression()
model.fit(X, y)

# Previsões futuras (próximos 30 dias)
ultimo_dia = vendas_diarias['dias'].max()
dias_futuros = np.arange(ultimo_dia + 1, ultimo_dia + 31).reshape(-1, 1)
previsoes = model.predict(dias_futuros)

# Criar datas futuras para o plot
ultima_data = vendas_diarias['data'].max()
datas_futuras = pd.date_range(start=ultima_data + pd.Timedelta(days=1), periods=30)

# Plotar
plt.figure(figsize=(14, 7))

# Linha do histórico
plt.plot(vendas_diarias['data'], y, 'b-', linewidth=1.5, label='Histórico', alpha=0.7)

# Linha de previsão
plt.plot(datas_futuras, previsoes, 'r--', linewidth=2, label='Previsão (30 dias)')

# Intervalo de confiança (simples: ±15%)
plt.fill_between(datas_futuras, 
                 previsoes * 0.85, 
                 previsoes * 1.15, 
                 color='red', alpha=0.15, 
                 label='Intervalo de Confiança (85-115%)')

# Linha vertical marcando início da previsão
plt.axvline(ultima_data, color='gray', linestyle=':', alpha=0.7, linewidth=1.5)
plt.text(ultima_data, plt.ylim()[1]*0.95, ' Início da Previsão', 
         fontsize=9, color='gray', va='top')

# Anotação com métricas do modelo
r2 = r2_score(y, model.predict(X))
mae = mean_absolute_error(y, model.predict(X))
slope = model.coef_[0]
intercept = model.intercept_

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

**Entrega:** Screenshot do gráfico mostrando histórico + previsão com intervalo de confiança.

---

## Exercício 7 — WordCloud de Produtos

### Objetivo
Gerar uma nuvem de palavras das categorias ou nomes de produtos.

**Script Python no Visual:**

1. Crie um novo visual Python
2. Arraste a coluna `produto` ou `categoria` para o visual

```python
import pandas as pd
import matplotlib.pyplot as plt
from wordcloud import WordCloud
from collections import Counter
import re

# Supondo que você tenha uma coluna de texto
texto = ' '.join(dataset['produto'].astype(str) if 'produto' in dataset.columns else dataset['categoria'].astype(str))

# Limpar texto
texto_limpo = re.sub(r'[^\w\s]', '', texto.lower())

# Gerar wordcloud
wordcloud = WordCloud(width=1000, height=500, 
                      background_color='white',
                      colormap='viridis',
                      max_words=50,
                      min_font_size=10,
                      random_state=42,
                      contour_width=2,
                      contour_color='steelblue').generate(texto_limpo)

# Plotar
plt.figure(figsize=(14, 7))
plt.imshow(wordcloud, interpolation='bilinear')
plt.axis('off')
plt.title('Palavras mais Frequentes nos Produtos', fontsize=16, fontweight='bold', pad=20)
plt.tight_layout()
plt.show()

# Top 10 palavras
palavras = texto_limpo.split()
contagem = Counter(palavras).most_common(10)
print("\n=== Top 10 Palavras ===")
for palavra, freq in contagem:
    print(f"{palavra}: {freq}")
```

**Entrega:** Screenshot da wordcloud.

---

## Desafio Extra — Comparação DAX vs Python

### Objetivo
Reproduzir o mesmo visual usando DAX nativo E Python, documentando prós e contras.

**Visual para comparar:** Gráfico de evolução mensal de vendas.

**Versão DAX:**
1. Criar medida: `Vendas do Mês = SUM(vendas[valor_final])`
2. Criar visual de linha com `data` no Axis e `Vendas do Mês` nos Values

**Versão Python:**
```python
import pandas as pd
import matplotlib.pyplot as plt

dataset['data'] = pd.to_datetime(dataset['data'])
dataset['ano_mes'] = dataset['data'].dt.to_period('M')

vendas_mensais = dataset.groupby('ano_mes')['valor_final'].sum().reset_index()
vendas_mensais['ano_mes'] = vendas_mensais['ano_mes'].astype(str)

plt.figure(figsize=(12, 6))
plt.plot(vendas_mensais['ano_mes'], vendas_mensais['valor_final'], 'b-o', linewidth=2, markersize=6)
plt.xlabel('Mês')
plt.ylabel('Vendas (R$)')
plt.title('Evolução Mensal de Vendas')
plt.xticks(rotation=45)
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

**Entrega:** Documentar em uma tabela:

| Aspecto | DAX | Python |
|---------|-----|--------|
| Tempo de desenvolvimento | | |
| Performance (reFRESH) | | |
| Flexibilidade visual | | |
| Manutenção futura | | |
| Reutilização em outros relatórios | | |

---

## Entrega Final

Ao final da aula, você deve ter:

1. ✅ Screenshot da configuração do Python no Power BI
2. ✅ Screenshot dos dados carregados via Script Python
3. ✅ Screenshot do Power Query mostrando transformações Python
4. ✅ Heatmap de correlação (Exercício 4)
5. ✅ Scatter plot com clusters de clientes (Exercício 5)
6. ✅ Gráfico de forecast com previsão (Exercício 6)
7. ✅ WordCloud de produtos (Exercício 7)
8. ✅ Tabela comparativa DAX vs Python (Desafio Extra)