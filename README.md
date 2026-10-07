# Multas

Sistema de análise de multas de trânsito: varre pastas de notificações (PDF),
extrai os dados de cada documento e gera um resumo por placa em Excel.

## Estrutura de pastas

Multas/ 
├── analise_multas.ipynb # Notebook principal 
├── resumo_multas.xlsx # Saída gerada (não versionado) 
├── Placas/ 
    └── / # Uma pasta por veículo (ex.: BSC4C89) 
        └── A_Vencer/ # Notificações com prazo em aberto 
        └── Pagas/ # Multas quitadas 
        └── Vencidas/ # Prazos expirados


## Padrão de nome dos arquivos

notificacao{Tipo}{PLACA}{NUMERO}.pdf


- **Tipo**: `Autuacao` ou `Penalidade`
- **NUMERO**: código do órgão + AIT + código da infração
  (ex.: `000100J0106240827455`)

A placa é lida do nome do arquivo e o status vem da pasta — a estrutura
de diretórios pode variar (maiúsculas/minúsculas, espaços) sem quebrar a análise.

## Fluxo do notebook

| Célula | Responsabilidade |
|--------|------------------|
| 1 | Importações (todas concentradas aqui) |
| 2 | Caminhos e descoberta de arquivos (compatível com PyInstaller) |
| 3 | Leitura dos PDFs — extração por coordenadas + fallback por regex |
| 4 | Criação dos DataFrames (datas, valores numéricos, dias para vencimento) |
| 5 | Resumo por placa (quantidade, valor total, menor prazo, alerta) |
| 6 | Exportação para `resumo_multas.xlsx` |

A extração por coordenadas funciona para PRF e prefeituras, pois o gerador
do PDF é o mesmo. Campos críticos (valor, datas, AIT) têm validação e
fallback, e a placa/status são sempre confirmados pela estrutura de pastas.

## Requisitos

- Python 3.11+
- Dependências:

```bash
pip install pandas pdfplumber openpyxl
Uso
Coloque os PDFs em Placas/<PLACA>/<Status>/
Execute as células do notebook em ordem (1 → 6)
O resultado fica em resumo_multas.xlsx (abas "Detalhado" e "Resumo por Placa")