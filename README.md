# Análise Competitiva de Redes Automotivas no Brasil

Este repositório reúne a resolução do desafio técnico de Engenharia de Dados e Análise Espacial aplicado ao mercado de centros automotivos no Brasil.

O projeto parte de **4.235 pontos de venda brutos** de seis grandes redes (Continental, Michelin, Bosch, Bridgestone, Pirelli e Speedmax), processa e limpa essa base, e chega a **3.578 lojas únicas** depois da deduplicação espacial. Os dados foram cruzados com informações demográficas e a malha municipal do IBGE para gerar inteligência competitiva e recomendações concretas de expansão.

## Tecnologias e diferenciais técnicos

Em vez de se limitar à manipulação básica de tabelas, o projeto incorpora algumas decisões técnicas pensadas para dar mais precisão e robustez à análise:

- **Deduplicação espacial de alta performance**: uso de `BallTree` (Scikit-Learn) com métrica _Haversine_ para identificar lojas físicas duplicadas entre redes diferentes dentro de um raio de 50 metros — uma alternativa muito mais confiável do que o cruzamento por nome/string, que costuma falhar com variações de grafia.
- **Padronização de dados**: normalização dos nomes de cidades (remoção de acentos, caixa alta) para evitar que a mesma cidade apareça fragmentada em mais de uma linha nas análises agregadas.
- **Geoprocessamento real**: `geopandas` combinado com a malha oficial de municípios do IBGE (shapefiles/GeoPackage).
- **Precisão cartográfica**: conversão do sistema de coordenadas de EPSG:4326 para EPSG:5880 (projeção métrica) antes de calcular buffers geográficos, evitando distorções de distância que apareceriam se os cálculos fossem feitos direto em graus.
- **Ambiente moderno**: gestão de dependências com `uv`, mais rápido que as alternativas tradicionais.

## Estrutura do projeto

A análise foi dividida em três notebooks, seguindo o princípio de separação de responsabilidades:

1. **`01_unificacao_e_limpeza.ipynb`** — pipeline de dados, padronização de schema, normalização de cidades e deduplicação espacial.
2. **`02_analise_exploratoria.ipynb`** — inteligência de negócio: densidade populacional e Índice HHI de concentração de mercado (rivalidade local).
3. **`03_analise_espacial.ipynb`** — heatmaps interativos em HTML e cálculo das **zonas brancas** (municípios sem nenhuma loja num raio de 30 km).

## Resultados

### Parte 1 — Qualidade dos dados

**Completude dos campos** (após unificação e filtro geográfico do Brasil):

| Campo     | Preenchimento |
| --------- | ------------- |
| nome      | 100,0%        |
| rede      | 100,0%        |
| cidade    | 100,0%        |
| estado    | 84,89%        |
| lat / lon | 100,0%        |
| telefone  | 70,25%        |
| email     | 28,55%        |
| website   | 34,40%        |

**Impacto da deduplicação espacial** (BallTree, raio de 50 m):

| Métrica                     | Valor        |
| --------------------------- | ------------ |
| Lojas antes da deduplicação | 4.235        |
| Lojas após deduplicação     | 3.578        |
| Duplicatas removidas        | 657 (~15,5%) |

Cerca de 1 em cada 6 registros brutos correspondia ao mesmo ponto físico cadastrado em redes diferentes — uma sobreposição relevante, ainda que bem menor do que se poderia supor antes da limpeza. Nos dados de contato, email e website continuam sendo os campos mais frágeis da base, com menos de 35% de preenchimento, o que limita o alcance de qualquer estratégia de prospecção digital direta.

### Parte 2 — Inteligência de mercado

**Estados mais saturados** (lojas por 100 mil habitantes):

| UF  | Lojas / 100k hab. |
| --- | ----------------- |
| ES  | 3,05              |
| MT  | 2,84              |
| RO  | 2,59              |
| SC  | 2,46              |
| MS  | 2,39              |

**Estados subatendidos** (oportunidade de expansão):

| UF  | Lojas / 100k hab. |
| --- | ----------------- |
| MA  | 0,80              |
| PE  | 0,77              |
| AL  | 0,77              |
| AM  | 0,74              |
| AP  | 0,68              |

**Cidades com maior rivalidade local** (Índice HHI > 2.500, considerando cidades com mais de 10 lojas):

| Cidade                | Lojas | HHI   |
| --------------------- | ----- | ----- |
| Aracaju               | 22    | 6.116 |
| São José do Rio Preto | 18    | 4.136 |
| São Bernardo do Campo | 22    | 3.306 |
| Maringá               | 11    | 3.223 |
| Uberaba               | 11    | 3.223 |

**Demais cidades com HHI elevado** (posições 6 a 10):

| Cidade         | Lojas | HHI   |
| -------------- | ----- | ----- |
| Ribeirão Preto | 16    | 3.203 |
| Porto Velho    | 12    | 3.194 |
| Rio de Janeiro | 62    | 3.070 |
| Santo André    | 15    | 2.978 |
| Gurupi         | 11    | 2.893 |

O Nordeste e o Norte concentram os estados com menor cobertura relativa. Já em termos de concentração de mercado, Aracaju lidera com folga — um HHI de 6.116 é sinal de oligopólio local bem consolidado, com poucas marcas dividindo praticamente toda a demanda da cidade. O mapa interativo de densidade está disponível em `notebooks/heatmap_densidade.html`.

### Parte 3 — Análise espacial

Dos **5.572 municípios brasileiros**, **1.368 (24,5%)** se enquadram como **zonas brancas**: nenhuma loja das seis redes analisadas dentro de um raio de 30 km.

**Top 10 zonas brancas por área isolada:**

| Município                 | UF  | Área (km²) |
| ------------------------- | --- | ---------- |
| Barcelos                  | AM  | 124.143    |
| São Gabriel da Cachoeira  | AM  | 112.488    |
| Oriximiná                 | PA  | 107.788    |
| Tapauá                    | AM  | 86.520     |
| Atalaia do Norte          | AM  | 80.169     |
| Almeirim                  | PA  | 72.963     |
| Jutaí                     | AM  | 71.660     |
| Lábrea                    | AM  | 69.697     |
| Corumbá                   | MS  | 64.502     |
| Santa Isabel do Rio Negro | AM  | 64.084     |

O Amazonas domina isoladamente o ranking de oportunidades espaciais, com 7 dos 10 maiores vácuos geográficos do país concentrados no estado — resultado esperado dada a sua extensão territorial, mas que reforça o tamanho real da lacuna de cobertura na região.

## Recomendação de negócio: onde abrir 3 novas lojas?

Cruzando densidade por UF, concentração de mercado (HHI) e zonas brancas, a recomendação estratégica para a abertura de três novas lojas é:

**1. Barcelos — AM** _(a aposta segura)_

É a maior zona branca mapeada pelo algoritmo espacial (~124 mil km² isolados). Uma loja ali captura praticamente toda a frota local num raio de 30 km, sem precisar competir em preço com nenhuma das seis redes já homologadas na região.

**2. São Gabriel da Cachoeira — AM** _(o oceano azul estadual)_

O Amazonas é um dos estados mais subatendidos do país (0,74 lojas/100k hab.), e São Gabriel da Cachoeira é a segunda maior área isolada do Brasil (~112 mil km²). É o cruzamento mais favorável entre baixa cobertura demográfica e ausência total de concorrência espacial — não só falta loja na cidade, falta loja na região inteira.

**3. Uberaba — MG** _(quebra de oligopólio)_

Com 11 lojas e Índice HHI de 3.223, está entre as cinco cidades mais concentradas do país — o mercado local é dominado por poucas marcas. Mas Uberaba fica no interior de Minas Gerais, longe dos estados já saturados (ES, MT, RO, SC, MS), o que abre espaço para uma marca forte de fora capturar participação de mercado nessa concentração viciada, sem entrar na briga mais madura e desgastante de São Paulo ou Rio de Janeiro.

## Como rodar este projeto

1. Clone este repositório:

   ```bash
   git clone https://github.com/luuizfr/case_redes_automotivas.git
   cd case_redes_automotivas
   ```
