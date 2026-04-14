# Script — Geração de Dados da PlayMax

> Script Python que gera os CSVs fictícios com problemas propositais de qualidade de dados.

## Como usar

1. Abra o terminal na pasta onde quer gerar os dados
2. Copie o código abaixo ou use o arquivo `.py` diretamente
3. Execute: `python script.py`
4. Os CSVs serão gerados na subpasta `generated/`

## Código

```python
import pandas as pd
import random
from datetime import datetime, timedelta
import string

# CONFIG
NUM_USUARIOS = 3000
NUM_STREAMERS = 50
NUM_CATEGORIAS = 20
NUM_TRANSMISSOES = 800
NUM_DOACOES = 15000

# LISTAS BASE
categorias_jogos = [
    "RPG", "FPS", "MOBA", "Battle Royale", "Corrida", "Esporte",
    "Luta", "Aventura", "Estratégia", "Simulação", "Indie",
    "Ação", "Puzzle", "Horror", "Sandbox", "Roguelike",
    "Racing", "Fighting", "Survival", "MMORPG"
]

nomes_streamers = [
    "Gaules", "Alanzoka", "Nobru", "Coringa", "Marta", "Cezar",
    "Rato", "Beleza", "BrTT", "Froskurinn", "Myth", "Shroud",
    "Ninja", "Pokimane", "TimTheTatman", "Sykkuno", "Valkyrae",
    "DrLupo", "Sacriel", "Summit1g", "Lirik", "HasanAbi",
    "xQc", "Adin Ross", "Bugha", " Mong", "Fally", "LlAD", "MOW",
    "Jovirone", "Cebrone", "M.ab", "El gato", "M_loko", "Pz"
]

cidades_brasil = [
    "São Paulo", "Rio de Janeiro", "Belo Horizonte", "Fortaleza",
    "Salvador", "Curitiba", "Recife", "Goiânia", "Manaus", "Porto Alegre"
]

plataformas = ["PC", "PlayStation", "Xbox", "Celular", "Nintendo Switch"]
tipos_doacao = ["Bits", "Pix", "Assinatura"]
status_transmissao = ["Ativa", "Encerrada", "Ao Vivo", "Cancelada"]

# Gerador de texto com problemas
def texto_com_problemas(texto):
    """Adiciona espaços extras, maiúsculas inconsistentes, etc."""
    problemas = [
        lambda s: s,  # normal
        lambda s: s.upper(),  # todo maiúsculo
        lambda s: s.lower(),  # todo minúsculo
        lambda s: f" {s}",  # espaço antes
        lambda s: f"{s} ",  # espaço depois
        lambda s: f"  {s}  ",  # espaços dos dois lados
        lambda s: s.replace(" ", "  "),  # espaço duplo interno
    ]
    return random.choice(problemas)(texto)


def gerar_categoria_com_problema():
    """Gera categoria com possíveis problemas de formatação."""
    cat = random.choice(categorias_jogos)
    if random.random() < 0.3:
        cat = texto_com_problemas(cat)
    if random.random() < 0.1:
        cat += random.choice([" 🎮", " 🎯", " ✨", ""])
    return cat


def gerar_valor_nulo():
    """Retorna valor nulo ou texto representativo."""
    choices = [
        None,
        "NA",
        "-",
        "null",
        "",
        "N/A",
        "#N/A",
        "NULL"
    ]
    return random.choice(choices)


# ========== CATEGORIAS ==========
categorias = []
for i in range(1, NUM_CATEGORIAS + 1):
    categorias.append({
        "id_categoria": i,
        "nome_categoria": gerar_categoria_com_problema()
    })

df_categorias = pd.DataFrame(categorias)

# ========== STREAMERS ==========
streamers = []
usados = set()
for i in range(1, NUM_STREAMERS + 1):
    while True:
        nome = random.choice(nomes_streamers)
        if nome not in usados:
            usados.add(nome)
            break

    streamers.append({
        "id_streamer": i,
        "nome_streamer": texto_com_problemas(nome),
        "cidade": random.choice(cidades_brasil),
        "seguidores": random.randint(1000, 5000000),
        "categoria_principal": random.choice(categorias_jogos),
        "data_inicio": (datetime(2020, 1, 1) + timedelta(days=random.randint(0, 1400))).date()
    })

df_streamers = pd.DataFrame(streamers)

# ========== CALENDÁRIO ==========
start_date = datetime(2024, 1, 1)
end_date = datetime(2025, 12, 31)
total_days = (end_date - start_date).days

dates = []
for i in range(0, total_days + 1):
    d = start_date + timedelta(days=i)
    dates.append({"data": d.date()})

df_calendario = pd.DataFrame(dates)

# ========== TRANSMISSÕES ==========
transmissoes = []
ids_usados = []

for i in range(1, NUM_TRANSMISSOES + 1):
    duracao = random.randint(30, 720)  # minutos
    viewers = random.randint(50, 50000)
    streamer_id = random.randint(1, NUM_STREAMERS)

    # Adicionar duplicatas (5% de chance)
    num_registros = 1
    if random.random() < 0.05:
        num_registros = random.randint(2, 3)

    for r in range(num_registros):
        registro_id = i if r == 0 else i * 100 + r
        ids_usados.append(registro_id)

        transmissoes.append({
            "id_transmissao": registro_id,
            "id_streamer": streamer_id,
            "id_categoria": random.randint(1, NUM_CATEGORIAS),
            "data_inicio": (start_date + timedelta(days=random.randint(0, total_days))).date(),
            "duracao_min": duracao,
            "viewers_pico": viewers,
            "status": random.choice(status_transmissao)
        })

df_transmissoes = pd.DataFrame(transmissoes)

# ========== USUÁRIOS ==========
usuarios = []
for i in range(1, NUM_USUARIOS + 1):
    nome = f"user_{random.randint(1000, 9999)}"
    usuarios.append({
        "id_usuario": i,
        "nome_usuario": texto_com_problemas(nome),
        "cidade": random.choice(cidades_brasil),
        "idade": random.randint(16, 65),
        "data_cadastro": (start_date + timedelta(days=random.randint(0, total_days))).date()
    })

df_usuarios = pd.DataFrame(usuarios)

# ========== DOAÇÕES ==========
doacoes = []
valores_base = [5, 10, 20, 50, 100, 200, 500, 1000]

for i in range(1, NUM_DOACOES + 1):
    # Tipo de doação
    tipo = random.choice(tipos_doacao)

    # Valor com possíveis problemas
    if random.random() < 0.1:
        # Valores inválidos
        valor = random.choice([-50, -10, 0, 9999999, None])
    elif random.random() < 0.15:
        # Valores normais mas com texto
        valor = random.choice(valores_base)
    else:
        valor = random.choice(valores_base)

    # Data com possíveis problemas
    if random.random() < 0.08:
        # Data fora do período
        data_idx = random.randint(-100, total_days + 100)
        data = start_date + timedelta(days=data_idx)
    else:
        data_idx = random.randint(0, total_days)
        data = start_date + timedelta(days=data_idx)

    # Streamer ID - às vezes inválido (órfão)
    if random.random() < 0.05:
        streamer_id = random.randint(NUM_STREAMERS + 1, NUM_STREAMERS + 10)
    else:
        streamer_id = random.randint(1, NUM_STREAMERS)

    # Transmissão ID
    if random.random() < 0.03:
        trans_id = random.randint(NUM_TRANSMISSOES + 100, NUM_TRANSMISSOES + 500)
    else:
        trans_id = random.choice(ids_usados)

    doacoes.append({
        "id_doacao": i,
        "id_transmissao": trans_id,
        "id_usuario": random.randint(1, NUM_USUARIOS),
        "id_streamer": streamer_id,
        "data": data.date(),
        "tipo": tipo,
        "valor": valor,
        "mensagem": texto_com_problemas("Muito bom!") if random.random() < 0.3 else ""
    })

df_doacoes = pd.DataFrame(doacoes)

# Adicionar duplicatas explícitas em doações (3% adicional)
df_duplicatas = df_doacoes.sample(frac=0.03).copy()
df_doacoes = pd.concat([df_doacoes, df_duplicatas], ignore_index=True)

# ========== SALVAR ==========
import os
os.makedirs("generated", exist_ok=True)

df_categorias.to_csv("generated/categorias.csv", index=False)
df_streamers.to_csv("generated/streamers.csv", index=False)
df_calendario.to_csv("generated/calendario.csv", index=False)
df_transmissoes.to_csv("generated/transmissoes.csv", index=False)
df_usuarios.to_csv("generated/usuarios.csv", index=False)
df_doacoes.to_csv("generated/doacoes.csv", index=False)

print("✅ Base de dados gerada com problemas propositais!")
print(f"   - {len(df_transmissoes)} transmissões")
print(f"   - {len(df_doacoes)} doações")
print(f"   - {len(df_streamers)} streamers")
print(f"   - {len(df_categorias)} categorias")
print(f"   - {len(df_usuarios)} usuários")
```

## Saída

Gera os seguintes arquivos na subpasta `generated/`:

- `generated/categorias.csv` — 20 registros (com problemas de formatação)
- `generated/streamers.csv` — 50 registros (com espaços e case inconsistentes)
- `generated/calendario.csv` — de 2024-01-01 até 2025-12-31
- `generated/transmissoes.csv` — 800 registros (com duplicatas)
- `generated/usuarios.csv` — 3.000 registros
- `generated/doacoes.csv` — ~15.500 registros (com duplicatas, valores inválidos, IDs órfãos)

## Requisitos

```bash
pip install pandas
```
