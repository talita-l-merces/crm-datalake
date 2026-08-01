# Projeto Data Lake — Hotelaria e Multipropriedade

Estruturação de um data lake para uma empresa fictícia do setor de hotelaria e multipropriedade, com arquitetura medalhão e foco em visão unificada do hóspede.

---

## Problema

Na era dos dados, empresas de todos os segmentos enfrentam a necessidade de estruturação e governança. O segmento da hotelaria não é diferente: com um fluxo grande de dados todos os dias, as empresas do setor são guardiãs de informações delicadas de milhares de pessoas.

Antes de trabalhar os dados em si, é preciso entender a dor de uma empresa que gera essa quantidade de informação. A seguir, temas recorrentes que — sem a devida estrutura de dados — fazem com que uma empresa desse segmento tome decisões no escuro.

**Falta de conhecimento real do consumidor:**

- **Qual a localidade desse consumidor? É estrangeiro? Quais os padrões de localização?**
  Sem resposta, as ações de comunicação e campanhas são feitas de forma genérica.

- **De quais canais o consumidor vem?**
  Quando não se entende qual foi a porta final de venda e que condição fez a compra acontecer, o canal não pode ser trabalhado com consciência.

- **O hóspede está no hotel: o que ele consome, do que ele mais gosta?**
  Sem preferências mapeadas, as ofertas serão sempre genéricas.

- **O que o hóspede achou da experiência?**
  Sempre é possível melhorar, mas sem um norte a correção é um tiro no escuro.

---

## Sandbox

O projeto usa dados sintéticos, gerados para reproduzir os defeitos de qualidade típicos de sistemas hoteleiros — múltiplos formatos de telefone, duplicidade de cadastro, campos obrigatórios vazios, inconsistência de formato de data. Isso permite que a arquitetura seja pública sem exposição de dados pessoais.

---

## Abordagem

O padrão arquitetural adotado é o de um **CDP componível** (*composable CDP*): em vez de contratar uma plataforma fechada, que duplicaria os dados em mais um silo, a visão unificada do hóspede é construída sobre o próprio lake — resolução de identidade na camada silver, segmentos na gold — e ativada nas ferramentas operacionais via **reverse ETL**, que devolve os segmentos calculados para a plataforma que executa a comunicação.

O objetivo prático é a **micro-segmentação**: em vez de poucos blocos demográficos estáveis, muitos segmentos comportamentais pequenos, recalculados a cada ciclo. Isso só é operacionalmente viável quando cálculo e ativação são automáticos.

A base de segmentação é o **RFM** — recência, frequência e valor monetário —, modelo herdado do marketing direto e ancestral de toda segmentação comportamental. No contexto hoteleiro, ele é enriquecido com perfil de estadia, canal de origem, consumo interno e satisfação.

---

## Consumidores

**Primários — grão de hóspede**

- Marketing e comercial: operação de réguas de comunicação e campanhas.

**Secundário — grão de dia × tipo de quarto**

- Diretoria: acompanhamento de ocupação e resultado.

Grãos distintos exigem tabelas distintas, ambas derivadas da mesma camada silver.

---

## Frequência

A frequência é definida pelo consumidor primário. Réguas de comunicação e campanhas operam com gatilhos diários — aniversário, pré-estadia, pós-estadia —, o que estabelece a atualização diária como requisito mínimo. O painel da diretoria tolera frequência menor, mas é atendido pela mesma cadência.

**Frequência: diária.**

---

## Arquitetura

Arquitetura medalhão, com armazenamento em Parquet particionado.

| Camada | Conteúdo |
|---|---|
| **Bronze** | Dado como chegou da origem, sem limpeza. É o ponto de retorno para reprocessamento e a evidência de qualidade na origem. |
| **Silver** | Dado padronizado, deduplicado e tipado, com resolução de identidade entre fontes. Verdades universais sobre o dado, independentes de consumidor. |
| **Gold** | Modelada por caso de uso. Uma tabela por consumidor, no grão que ele consome. |

---

## Estrutura do repositório

```
crm-datalake/
├── data/
│   ├── raw/            # dado bruto — não versionado
│   └── sample/         # fixtures sintéticos para teste
├── docs/
│   ├── adr/            # decisões de arquitetura
│   ├── inventario.md   # inventário de fontes e campos
│   └── perguntas-origem.md
├── output/
│   └── profiles/       # saída do profiler de dados
├── src/                # código
└── notebooks/          # exploração
```
