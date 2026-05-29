# 🤖 Fluxos Inteligentes com Python + IA

> Automação de leitura e classificação de PDFs operacionais usando Python e Gemini API.

[![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python)](https://python.org)
[![Gemini API](https://img.shields.io/badge/Gemini-API-4285F4?logo=google)](https://ai.google.dev)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebooks-F37626?logo=jupyter)](https://jupyter.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

---

## 💡 Contexto do projeto

Este projeto foi concebido para demonstrar um pipeline de automação de documentos operacionais com IA — originalmente pensado para rodar com a **Claude API (Anthropic)**, que oferece excelente capacidade de leitura e estruturação de documentos.

Por questões de acesso à licença paga, a implementação atual utiliza o **Google Gemini**, que se comportou muito bem na prática e tem a vantagem de oferecer múltiplos modelos gratuitos. O código do notebook 03 já inclui lógica de fallback automático entre eles — se um modelo atingir o limite de quota, o próximo da lista é acionado automaticamente.

Os dados utilizados são **100% fictícios**, criados no notebook 01 para simular documentos reais de uma empresa (Notas Fiscais, Ordens de Serviço, Relatórios e Planilhas). O objetivo é mostrar a lógica, o pipeline e os resultados esperados — prontos para serem aplicados com dados reais.

---

## 📌 O que este projeto faz

```
📄 PDFs brutos
    ↓  Etapa 1 — Cria a estrutura de pastas e gera PDFs fictícios de exemplo
    ↓  Etapa 2 — Extrai texto e tabelas (pdfplumber / PyMuPDF)
    ↓  Etapa 3 — Classifica e estrutura os dados via Gemini API
    ↓  Etapa 4 — Gera KPIs, gráficos e relatório executivo (Excel + PDF)
📊 Relatórios prontos para uso
```

---

## 📂 Estrutura do projeto

```
pdf-classifier-claude/
├── notebooks/
│   ├── 01_pdficticios_coleta_e_organizacao.ipynb
│   ├── 02_extracao_de_texto.ipynb
│   ├── 03_classificacao_gemini.ipynb
│   └── 04_Geração_de_Relatório_Final.ipynb
├── .env.example
├── .gitignore
├── requirements.txt
└── README.md
```

As pastas abaixo são **criadas automaticamente** ao rodar os notebooks:

```
pdfs_entrada/       ← PDFs brutos a processar
pdfs_processados/   ← PDFs já lidos e extraídos
resultados/         ← JSONs gerados pelas etapas 2 e 3
relatorios/         ← Relatórios finais (Excel, PDF)
```

---

## 🚀 Como usar

### 1. Clone o repositório

```bash
git clone https://github.com/seu-usuario/pdf-classifier-gemini.git
cd pdf-classifier-claude
```

### 2. Instale as dependências

```bash
pip install -r requirements.txt
```

### 3. Configure sua API key

O notebook 03 usa o Google Gemini. Configure a chave via **Colab Secrets**:

No menu lateral do Colab → 🔑 **Secrets** → **Add new secret**
- Nome: `GEMINI_API_KEY`
- Valor: sua chave do [Google AI Studio](https://aistudio.google.com/app/apikey) (gratuito)

### 4. Execute os notebooks em ordem

| # | Notebook | O que faz |
|---|----------|-----------|
| 01 | `01_pdficticios_coleta_e_organizacao.ipynb` | Cria as 4 pastas do projeto e gera 8 PDFs fictícios (2 NFs, 3 OSs, 1 Relatório, 1 Planilha) |
| 02 | `02_extracao_de_texto.ipynb` | Extrai texto e tabelas de cada PDF; salva um JSON por documento + consolidado |
| 03 | `03_classificacao_gemini.ipynb` | Envia o texto ao Gemini e retorna JSON estruturado com tipo, entidades, valores, datas e itens críticos |
| 04 | `04_Geração_de_Relatório_Final.ipynb` | Gera KPIs, gráficos, relatório Excel (3 abas) e PDF executivo |

> ⚠️ Cada notebook depende do anterior. Execute sempre na sequência 01 → 02 → 03 → 04.

---

## 🤖 Fallback automático entre modelos Gemini

O notebook 03 percorre uma lista de modelos em ordem de custo/velocidade. Se um modelo estiver indisponível ou com quota esgotada, o próximo é acionado automaticamente — sem interromper o pipeline:

```
gemini-2.0-flash-lite   → mais rápido e gratuito (primeira tentativa)
gemini-2.0-flash        → intermediário
gemini-2.5-flash        → mais capaz
gemini-2.5-pro          → reserva (última opção)
```

---

## 📤 Exemplo de saída — Etapa 3

```json
{
  "tipo_documento": "Nota Fiscal Eletrônica",
  "confianca": "alta",
  "status": "ativo",
  "resumo": "NF-e de venda de equipamentos de TI para Comércio Nordeste SA no valor de R$ 11.643,20.",
  "entidades": {
    "parte_1": "Tech Solutions Ltda",
    "parte_2": "Comércio Nordeste SA",
    "responsavel": null
  },
  "valores": {
    "principal": "R$ 11.643,20",
    "multa": null,
    "outros": null
  },
  "datas": {
    "inicio": null,
    "vencimento": null,
    "assinatura": null
  },
  "indicadores": ["Venda de hardware", "Cliente corporativo"],
  "itens_criticos": []
}
```

---

## 📊 Arquivos gerados ao final do pipeline

| Arquivo | Onde | Descrição |
|---------|------|-----------|
| `catalogo_pdfs.json` | `resultados/` | Lista de PDFs coletados na Etapa 1 |
| `extracao_consolidada.json` | `resultados/` | Textos e tabelas extraídos de todos os PDFs |
| `<nome>_classificado.json` | `resultados/` | Classificação individual de cada documento |
| `classificacao_final.json` | `resultados/` | Consolidado com todos os resultados da Etapa 3 |
| `graficos_etapa4.png` | `resultados/` | Gráficos de distribuição por tipo e confiança |
| `relatorio_final.xlsx` | `resultados/` | Excel com 3 abas: Documentos, Resumo Executivo, Por Tipo |
| `relatorio_executivo.pdf` | `resultados/` | PDF com KPIs, distribuição e itens críticos |

---

## 🧰 Bibliotecas utilizadas

| Biblioteca | Etapa | Função |
|------------|-------|--------|
| `reportlab` | 1 | Geração dos PDFs fictícios de exemplo |
| `pdfplumber` | 2 | Extração de texto e tabelas (principal) |
| `pymupdf` | 2 | Extração de texto e metadados (fallback) |
| `google-generativeai` | 3 | Classificação inteligente via Gemini API |
| `pandas` | 4 | Estruturação e análise dos dados |
| `openpyxl` | 4 | Exportação para Excel |
| `matplotlib` | 4 | Geração dos gráficos |

---

## 📄 Licença

MIT — sinta-se livre para usar, adaptar e contribuir.
