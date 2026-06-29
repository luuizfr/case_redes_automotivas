# 🚗 Análise Competitiva de Redes Automotivas no Brasil

Este repositório contém a resolução do desafio técnico de Engenharia de Dados e Análise Espacial para o mercado de centros automotivos no Brasil. 

O projeto processa, limpa e analisa ~4.800 pontos de venda de 6 grandes redes (Continental, Michelin, Bosch, Bridgestone, Pirelli e Speedmax), cruzando dados espaciais e demográficos para entregar inteligência competitiva e recomendações acionáveis de expansão.

## 🛠️ Tecnologias e Diferenciais Implementados

Para garantir a máxima precisão e performance, este projeto não utiliza apenas manipulação básica de tabelas. Foram implementados os seguintes **diferenciais técnicos**:

* **Deduplicação Espacial de Alta Performance:** Utilização de `BallTree` (Scikit-Learn) com métrica *Haversine* para encontrar lojas físicas duplicadas entre as redes num raio de 50 metros, substituindo o ineficiente cruzamento por strings.
* **Geoprocessamento Real:** Uso de `geopandas` e da malha oficial de municípios do IBGE (Shapefiles/GeoPackage).
* **Precisão Cartográfica:** Conversão de sistemas de coordenadas (CRS de EPSG:4326 para EPSG:5880 métrico) para calcular de forma exata os buffers geográficos e evitar a "armadilha da terra plana".
* **Ambiente Moderno:** Gestão de dependências e ambiente virtual ultrarrápido construído com `uv`.

---

## 📊 Estrutura do Projeto

O raciocínio analítico foi dividido utilizando o princípio de Separação de Responsabilidades (SoC) em 3 notebooks distintos:

1. **`01_unificacao_e_limpeza.ipynb`**: Data pipeline, padronização de schema e deduplicação espacial.
2. **`02_analise_exploratoria.ipynb`**: Business Intelligence, cálculo de densidade populacional e Índice HHI de concentração de mercado (Rivalidade Local).
3. **`03_analise_espacial.ipynb`**: Geração de heatmaps em HTML e cálculo das **Zonas Brancas** (municípios isolados fora do raio de 30km de concorrência).

---

## 🎯 Recomendação de Negócios: Onde abrir 3 novas lojas?

Baseado na análise combinada de densidade demográfica, índice HHI (baixa concentração) e na identificação de "Zonas Brancas" (nenhuma das 6 grandes redes num raio de 30km), a recomendação para a abertura de 3 novas lojas focadas em "Oceanos Azuis" (alta demanda potencial e zero concorrência homologada) é:

**1. [NOME DA CIDADE 1] - [UF]**
* **Por quê?** Esta é a maior área urbana/demográfica mapeada como "Zona Branca" absoluta. A abertura de uma loja nesta localidade garante o monopólio geográfico de serviços automotivos premium num raio de 30km, capturando toda a frota local sem necessidade de guerra de preços com outras grandes redes.

**2. [NOME DA CIDADE 2] - [UF]**
* **Por quê?** Município de grande extensão isolada. Apesar da frota do interior, o custo de aquisição de clientes (CAC) local seria extremamente baixo devido à ausência de concorrência oficial das marcas listadas. Representa um polo de atração para cidades satélites ainda menores no entorno.

**3. [NOME DA CIDADE 3] - [UF]**
* **Por quê?** Estrategicamente posicionada fora da saturação litorânea/sudeste vista no mapa de calor. Enquanto os grandes centros apresentam um Índice HHI altamente competitivo (saturação), esta praça oferece terreno livre para fidelização inicial da frota regional e estabelecimento rápido de *Brand Awareness* (reconhecimento de marca).

*(Para reproduzir estes dados, siga as instruções de instalação abaixo)*

---

## ⚙️ Como rodar este projeto

1. Clone este repositório:
   ```bash
   git clone [https://github.com/SEU-USUARIO/case_redes_automotivas.git](https://github.com/SEU-USUARIO/case_redes_automotivas.git)
   cd case_redes_automotivas