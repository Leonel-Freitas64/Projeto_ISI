# TP01 — Integração de Sistemas de Informação

**Leonel Diogo Ribeiro Freitas**
**Número de estudante:** 31520  
**Curso:** Licenciatura em Engenharia de Sistemas Informáticos  
**Unidade curricular:** Integração de Sistemas de Informação  
**Ano letivo:** 2026/2027  
**Repositório:** https://github.com/Leonel-Freitas64/Projeto_ISI

## Vídeo de demonstração

**Link do vídeo:** https://youtu.be/YgDDa46XTAk

O vídeo de demonstração encontra-se na ligação acima. O respetivo código QR está incluído na secção 8.1 do relatório PDF.

## Objetivo

Integração de dados de anúncios imobiliários em Portugal com dados estatísticos do INE, através de um processo ETL implementado no KNIME. Os dados tratados são persistidos em MySQL e consultados num dashboard desenvolvido em Node-RED.

## Estrutura e descrição dos ficheiros

```text
tp01-31520/
├── README.md
├── doc/
│   └── 31520_doc.pdf              Relatório final
├── dataintegration/
│   └── KNIME_project/             Workflow KNIME completo, incluindo configurações
│       └── data/                  Ficheiros usados pelos leitores KNIME e exportações
├── data/
│   ├── input/
│   │   ├── portugal_listinigs.csv Fonte de anúncios imobiliários
│   │   └── indica.json           Fonte de indicadores estatísticos
│   └── output/
│       ├── File_0.json           Exemplo de exportação em JSON
│       └── File_0.xml            Exemplo de exportação em XML
└── src/
    └── NODE-RED.json            Fluxo exportado do Node-RED
```

**Nota:** Os ficheiros `data/input` e `data/output` também existem dentro da pasta `dataintegration/KNIME_project/data`, para preservar os caminhos relativos configurados nos nós do KNIME. O workflow conserva alguns dados de execução em cache; essas caches não substituem uma execução completa.

## Ferramentas necessárias

- KNIME Analytics Platform, com os componentes necessários aos nós presentes no workflow.
- MySQL Server acessível localmente ou numa máquina configurada.
- Node.js e Node-RED.
- Nós Node-RED: `node-red-node-mysql` e `@flowfuse/node-red-dashboard` (Dashboard 2.0).
- Ligação à Internet para o ramo que consulta a API REST.

## Como executar

### 1. Preparar o KNIME

1. Abrir o KNIME Analytics Platform.
2. Importar ou abrir o workflow na pasta `dataintegration/KNIME_project/`.
3. Confirmar os caminhos dos nós **CSV Reader** e **JSON Reader**. No projeto entregue, estes usam o diretório de dados do próprio workflow (`knime.workflow.data`), com os nomes `portugal_listinigs.csv` e `indica.json`.
4. Confirmar as restantes configurações de leitura e escrita, incluindo a localização dos ficheiros exportados.
5. Configurar a ligação MySQL e as credenciais nos respetivos nós de base de dados.
6. Executar os nós do workflow pela ordem das dependências ou executar o workflow completo, verificando as saídas e eventuais erros.

### 2. Configurar o MySQL

1. Ter o servidor MySQL em funcionamento.
2. Preparar a base de dados `listings` e as tabelas utilizadas no workflow, incluindo `analise_imobiliaria` e `logs_etl`, de acordo com os tipos de dados produzidos pelo KNIME.
3. Conferir a configuração dos nós **DB Writer** e os modos de criação/inserção antes da execução.
4. Verificar as tabelas após a execução, por exemplo:

```sql
SELECT * FROM listings.analise_imobiliaria LIMIT 10;
SELECT * FROM listings.logs_etl ORDER BY data_execucao DESC LIMIT 10;
```

**Limitação da entrega:** não foi identificado no projeto original um script SQL autónomo de criação da base de dados/tabelas. A estrutura deve ser confirmada no KNIME e/ou na instância MySQL usada no desenvolvimento. Não são fornecidos dumps da base de dados.

### 3. Iniciar o Node-RED

Com Node.js e npm instalados, executar num terminal:

```bash
npm install -g --unsafe-perm node-red
node-red
```

1. Abrir o editor do Node-RED em `http://localhost:1880`.
2. Instalar pelo gestor de paletas os módulos `node-red-node-mysql` e `@flowfuse/node-red-dashboard`.
3. Importar o fluxo existente em `src/NODE-RED.json`.
4. Na configuração da ligação MySQL, confirmar o servidor, a porta (por omissão `3306`), a base de dados `listings`, o utilizador e a palavra-passe.
5. Fazer **Deploy** e executar a consulta que alimenta o dashboard.
6. Abrir o dashboard pelo endereço indicado pelo nó `ui-base`/pela configuração do Dashboard 2.0 e confirmar os gráficos.

O Node-RED consulta a tabela `analise_imobiliaria` depois da sua atualização pelo KNIME. As credenciais privadas devem ser configuradas localmente.
