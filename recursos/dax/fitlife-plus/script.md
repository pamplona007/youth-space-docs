# FitLife+ — Script de Geração de Dados

Execute este script Python para gerar todos os arquivos CSV do dataset FitLife+.

```python
import csv
import random
import os
from datetime import date, timedelta
from collections import defaultdict

random.seed(42)

# ─── Diretório de saída ──────────────────────────────────────────────────────
OUTPUT_DIR = "fitlife-plus/csv"
os.makedirs(OUTPUT_DIR, exist_ok=True)

# ─── Helpers de data ─────────────────────────────────────────────────────────
START = date(2024, 1, 1)
END   = date(2025, 12, 31)
DAYS  = (END - START).days + 1  # 731 dias

def d(offset: int) -> str:
    return (START + timedelta(days=offset)).isoformat()

# ─── Dados de referência ─────────────────────────────────────────────────────
CIDADES = ["Natal", "Recife", "Fortaleza", "Salvador", "João Pessoa"]

UNIDADES = [
    (1, "FitLife+ Norte",       "Natal",          date(2018, 1, 15), 450,  40),
    (2, "FitLife+ Centro",        "Natal",          date(2018, 6,  1), 300,  35),
    (3, "FitLife+ Boa Viagem",    "Recife",         date(2019, 3, 10), 800,  85),
    (4, "FitLife+ Aldeota",      "Fortaleza",       date(2019, 9,  1), 600,  65),
    (5, "FitLife+ Pituba",        "Salvador",        date(2020, 2, 15), 700,  75),
    (6, "FitLife+ Centro Sul",    "Salvador",        date(2021, 7,  1), 500,  55),
    (7, "FitLife+ Manaíra",       "João Pessoa",     date(2020, 5,  1), 550,  60),
    (8, "FitLife+ Cabo Branco",   "João Pessoa",     date(2022, 11, 1), 480,  50),
]

PLANOS = [
    (1, "Básico",        79.90,  "Academia + aulas coletivas básicas",    "Acesso"),
    (2, "Plus",         129.90, "Academia + aulas coletivas + avaliação", "Acesso"),
    (3, "Premium",      189.90, "Tudo incluso + 4 sessões personal/mês",  "Acesso"),
    (4, "VIP",          299.90, "Acesso ilimitado + personal exclusivo",   "Acesso"),
    (5, "Corporativo",  249.90, "Empresa + 10 a 50 acessos + benefícios", "Corporativo"),
]

MODALIDADES = [
    (1, "Musculação", "Treino com pesos e máquinas",               30, 60, "Todos"),
    (2, "Spinning",   "Aula de bike indoor com música",            20, 45, "Intermediário"),
    (3, "Funcional",  "Circuito funcional com peso corporal",       25, 50, "Todos"),
    (4, "Yoga",       "Prática de yoga para flexibilidade",        20, 60, "Iniciante"),
    (5, "CrossFit",   "Treino de alta intensidade funcional",       15, 60, "Avançado"),
    (6, "HIIT",       "Treino intervalado de alta intensidade",     20, 30, "Avançado"),
]

NOMES_M = [
    "Lucas Silva","Gabriel Santos","Matheus Oliveira","Pedro Costa","Rafael Souza",
    "Bruno Lima","Felipe Martins","Thiago Pereira","Daniel Carvalho","Marcos Almeida",
    "Gustavo Ribeiro","Leonardo Ferreira","Henrique Cardoso","Rodrigo Ramos","André Barbosa",
    "Vinícius Rocha","Eduardo Nascimento","Caio Guimarães","Samuel Torres","Renan Araújo",
    "Diego Monteiro","Fábio Mendes","Hugo Clementino","Igor Batista","Jonathan Dias",
]
NOMES_F = [
    "Ana Souza","Beatriz Lima","Camila Costa","Daniela Oliveira","Eduarda Santos",
    "Fernanda Pereira","Gabriela Almeida","Helena Martins","Isabela Ferreira","Juliana Cardoso",
    "Larissa Ribeiro","Mariana Campos","Natália Guimarães","Patrícia Rocha","Renata Araújo",
    "Sara Batista","Tatiana Dias","Vanessa Mendes","Yasmin Clementino","Zoe Barbosa",
]

# ─── Writer auxiliar ─────────────────────────────────────────────────────────
def write_csv(filename, rows):
    path = os.path.join(OUTPUT_DIR, filename)
    with open(path, "w", newline="", encoding="utf-8") as f:
        w = csv.writer(f)
        w.writerows(rows)
    print(f"  ✓ {filename}  ({len(rows)-1} linhas de dados)")

# ─── 1. UNIDADES ────────────────────────────────────────────────────────────
rows = [["id_unidade","nome_unidade","cidade","data_inauguracao","area_m2","equipamentos"]]
for u in UNIDADES:
    rows.append([u[0], u[1], u[2], u[3].isoformat(), u[4], u[5]])
write_csv("unidades.csv", rows)

# ─── 2. PLANOS ──────────────────────────────────────────────────────────────
rows = [["id_plano","nome_plano","valor_mensal","descricao","tipo"]]
for p in PLANOS:
    rows.append([p[0], p[1], p[2], p[3], p[4]])
write_csv("planos.csv", rows)

# ─── 3. MODALIDADES ─────────────────────────────────────────────────────────
rows = [["id_modalidade","nome_modalidade","descricao","capacidade","duracao_min","nivel"]]
for m in MODALIDADES:
    rows.append([m[0], m[1], m[2], m[3], m[4], m[5]])
write_csv("modalidades.csv", rows)

# ─── 4. INSTRUTORES ─────────────────────────────────────────────────────────
ESPECIALIDADES = [m[1] for m in MODALIDADES] + ["Geral"]
AVALIACOES = [3.5, 3.5, 4.0, 4.0, 4.0, 4.5, 4.5, 4.5, 5.0, 5.0]

instrutores = []
for i in range(1, 31):
    id_unidade = UNIDADES[(i - 1) % 8][0]
    especialidade = random.choice(ESPECIALIDADES)
    hire_d = date(2018, 1, 1) + timedelta(days=random.randint(0, 2190))
    salario = round(random.uniform(2500, 6000), 2)
    avaliacao = random.choice(AVALIACOES)
    nome = f"{random.choice(NOMES_M if i % 3 != 2 else NOMES_F)} {random.choice(['da Silva','dos Santos','de Oliveira','Costa','Lima'])}"
    instrutores.append((i, nome, id_unidade, especialidade, hire_d.isoformat(), salario, avaliacao))

rows = [["id_instrutor","nome_instrutor","id_unidade","especialidade","data_contratacao","salario","avaliacao_media"]]
rows += instrutores
write_csv("instrutores.csv", rows)

# ─── 5. MEMBROS ─────────────────────────────────────────────────────────────
membros = []
STATUSES = ["Ativo"] * 2200 + ["Inativo"] * 400 + ["Suspenso"] * 250 + ["Cancelado"] * 150
PLAN_WEIGHTS = [40, 30, 20, 5, 5]

def random_dob() -> str:
    # Membros com 16–72 anos
    days_ago = random.randint(16*365, 72*365)
    return (date.today() - timedelta(days=days_ago)).isoformat()

def random_enrollment(status: str) -> str:
    if status == "Cancelado":
        return date(2018, 1, 1) + timedelta(days=random.randint(0, 2190))
    else:
        return date(2019, 1, 1) + timedelta(days=random.randint(0, DAYS))

for i in range(1, 3001):
    sexo = random.choice(["M", "F", "Outro"])
    nomes = NOMES_M if sexo == "M" else NOMES_F
    sobrenome = random.choice(["da Silva","dos Santos","de Oliveira","Costa","Lima","Pereira","Almeida","Ribeiro"])
    nome = f"{random.choice(nomes)} {sobrenome}"
    dob = random_dob()
    cidade = random.choice(CIDADES)
    id_plano = random.choices(range(1, 6), weights=PLAN_WEIGHTS)[0]
    id_unidade = random.choice([u[0] for u in UNIDADES])
    data_matricula = random_enrollment(STATUSES[i-1])
    status = STATUSES[i-1]
    data_cancelamento = (date(2024, 1, 1) + timedelta(days=random.randint(0, DAYS-1))).isoformat() \
                        if status == "Cancelado" else ""
    membros.append((i, nome, dob, sexo, cidade, id_plano, id_unidade, data_matricula, status, data_cancelamento))

rows = [["id_membro","nome_membro","data_nascimento","sexo","cidade","id_plano","id_unidade_principal","data_matricula","status","data_cancelamento"]]
rows += membros
write_csv("membros.csv", rows)

# ─── 6. CHECKINS ────────────────────────────────────────────────────────────
checkins = []
id_checkin = 1
HOURS_WEIGHTED = [6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22]

for mid, mstatus in enumerate(STATUSES, start=1):
    if mstatus == "Ativo":
        n = random.randint(5, 60)
    elif mstatus == "Inativo":
        n = random.randint(0, 5)
    elif mstatus == "Suspenso":
        n = random.randint(0, 3)
    else:
        n = random.randint(0, 2)

    used_dates = set()
    for _ in range(n):
        offset = random.randint(0, DAYS - 1)
        if mstatus == "Cancelado":
            mat = membros[mid - 1][7]
            cancel = membros[mid - 1][9]
            if cancel and offset > (date.fromisoformat(cancel) - START).days:
                continue
        used_dates.add(offset)

    for offset in used_dates:
        tipo = random.choices(["Livre", "Aula"], weights=[70, 30])[0]
        id_unidade = random.choice([u[0] for u in UNIDADES])
        hora = random.choice(HOURS_WEIGHTED)
        checkins.append((id_checkin, mid, id_unidade, d(offset),
                         f"{hora:02d}:{random.choice([0,15,30,45]):02d}",
                         tipo))
        id_checkin += 1

rows = [["id_checkin","id_membro","id_unidade","data_checkin","horario","tipo"]]
rows += checkins
write_csv("checkins.csv", rows)

# ─── 7. AULAS ────────────────────────────────────────────────────────────────
aulas = []
id_aula = 1

SCHEDULE = {
    "Musculação": [(6,0),(7,0),(8,0),(9,0),(10,0),(11,0),(14,0),(15,0),(16,0),(17,0),(18,0),(19,0),(20,0)],
    "Spinning":   [(6,0),(7,30),(9,0),(10,30),(18,0),(19,0),(20,0)],
    "Funcional":  [(7,0),(8,0),(9,0),(17,0),(18,0),(19,0)],
    "Yoga":       [(6,0),(7,0),(8,0),(10,0),(17,0),(18,0),(20,0)],
    "CrossFit":   [(7,0),(8,0),(9,0),(18,0),(19,0),(20,0)],
    "HIIT":       [(6,30),(7,30),(18,30),(19,30),(20,30)],
}

for unidade in UNIDADES:
    uid = unidade[0]
    for offset in range(DAYS):
        for mod_name, slots in SCHEDULE.items():
            mod_id = next(m[0] for m in MODALIDADES if m[1] == mod_name)
            capacidade = next(m[3] for m in MODALIDADES if m[0] == mod_id)
            for hora, minuto in slots:
                if random.random() < 0.15:
                    continue
                unit_instrutores = [ins[0] for ins in instrutores if ins[2] == uid]
                if not unit_instrutores:
                    continue
                id_instrutor = random.choice(unit_instrutores)
                vagas = capacidade
                if random.random() < 0.4:
                    inscritos = vagas
                elif random.random() < 0.7:
                    inscritos = random.randint(int(vagas * 0.7), vagas - 1)
                else:
                    inscritos = random.randint(0, int(vagas * 0.7))
                compareceu = random.randint(0, inscritos) if random.random() < 0.85 else 0
                aulas.append((id_aula, mod_id, id_instrutor, uid,
                               d(offset), f"{hora:02d}:{minuto:02d}",
                               vagas, inscritos, compareceu))
                id_aula += 1

rows = [["id_aula","id_modalidade","id_instrutor","id_unidade","data_aula","horario","vagas","inscritos","compareceu"]]
rows += aulas
write_csv("aulas.csv", rows)

# ─── 8. FATURAS ─────────────────────────────────────────────────────────────
faturas = []
id_fatura = 1

for mid, mstatus in enumerate(STATUSES, start=1):
    id_plano = membros[mid - 1][5]
    valor = PLANOS[id_plano - 1][2]
    data_mat = date.fromisoformat(membros[mid - 1][7])
    data_cancel = membros[mid - 1][9]
    cancel_date = date.fromisoformat(data_cancel) if data_cancel else None

    current = date(2024, 1, 1)
    while current <= END:
        due_day = date(current.year, current.month, 5)
        if data_mat <= due_day:
            if not (cancel_date and due_day > cancel_date):
                if random.random() < 0.90:
                    days_to_pay = random.randint(0, 15)
                    dp = due_day + timedelta(days=days_to_pay)
                    status = "Pago"
                elif random.random() < 0.7:
                    dp = due_day + timedelta(days=random.randint(16, 60))
                    status = "Pago"
                else:
                    dp = "" if random.random() < 0.5 else (due_day + timedelta(days=random.randint(1,5))).isoformat()
                    status = random.choice(["Pendente", "Atrasado"])
                faturas.append((id_fatura, mid, id_plano, due_day.isoformat(),
                                 dp if dp else "", valor, status))
                id_fatura += 1
        current = date(current.year, current.month + 1, 1) if current.month < 12 else date(current.year + 1, 1, 1)

rows = [["id_fatura","id_membro","id_plano","data_vencimento","data_pagamento","valor","status_pagamento"]]
rows += faturas
write_csv("faturas.csv", rows)

print(f"\n✅ Todos os arquivos gerados em {OUTPUT_DIR}/")
print(f"   {len(checkins)} check-ins | {len(aulas)} aulas | {len(faturas)} faturas")
```

---

## Arquivos Gerados

A execução do script produz os seguintes arquivos CSV em `fitlife-plus/csv/`:

| Arquivo | Linhas | Descrição |
|---------|--------|-----------|
| `unidades.csv` | 8 | Unidades da academia |
| `planos.csv` | 5 | Planos de assinatura |
| `modalidades.csv` | 6 | Tipos de aula |
| `instrutores.csv` | 30 | Instrutores |
| `membros.csv` | 3.000 | Membros |
| `checkins.csv` | ~45.000 | Registros de entrada |
| `aulas.csv` | ~6.500 | Aulas em grupo |
| `faturas.csv` | ~8.000 | Faturas mensais |

---

## Carregando no Power BI

1. Crie um novo arquivo PBIX
2. **Obter Dados → Texto/CSV** e carregue cada arquivo
3. No modelo de dados, crie os seguintes relacionamentos:

```
checkins[id_membro]              → membros[id_membro]
checkins[id_unidade]             → unidades[id_unidade]
aulas[id_modalidade]             → modalidades[id_modalidade]
aulas[id_instrutor]              → instrutores[id_instrutor]
aulas[id_unidade]                → unidades[id_unidade]
faturas[id_membro]               → membros[id_membro]
faturas[id_plano]                → planos[id_plano]
membros[id_plano]                → planos[id_plano]
membros[id_unidade_principal]    → unidades[id_unidade]
instrutores[id_unidade]          → unidades[id_unidade]
```

4. Marque `calendario[data]` como tabela de datas: **Marcar como Tabela de Datas**
5. Oculte as colunas de chave estrangeira nas tabelas de fatos (mantenha visíveis apenas as colunas de medida)
6. Comece os exercícios!

---

## Conexão com os Datasets Anteriores

Este dataset continua a jornada de aprendizagem:

- **VendaMais** ensinou: agregações básicas, inteligência temporal (YTD, MTD, SPLY, YoY), médias móveis
- **PlayMax** ensinou: limpeza de dados, transformações no Power Query, merge de consultas
- **FitLife+** (este dataset) ensina: FILTER + CALCULATE, iteradores, ranking, segmentação, churn, estatísticas

Thiago (gerente de operações) já sabe construir um dashboard de vendas. Agora ele quer responder perguntas como: *"Qual instrutor tem as aulas com melhor avaliação?"*, *"Qual percentual de membros está em risco de cancelar?"*, e *"Qual unidade gera mais receita por m²?"*
