🌡️ Índice de Risco de Calor Urbano (Heat Risk Index) — Atenas, Grécia
Projeto desenvolvido durante o curso GIS for Climate Action (Esri). O objetivo é construir um Heat Risk Index (HRI) combinando três variáveis espaciais para identificar os CEPs de Atenas que mais se beneficiariam do plantio de árvores como parte de um plano de adaptação climática ao calor extremo.

Cenário
A cidade de Atenas, Grécia, enfrenta ondas de calor crescentes associadas às mudanças climáticas e ao fenômeno de ilha de calor urbana (UHI). Como analista GIS, fui responsável por derivar três variáveis de entrada a partir de dados de satélite e populacionais, padronizá-las em uma escala comum, combiná-las em um índice composto e visualizar os resultados em um mapa web publicado no ArcGIS Online.

Objetivos

Definir a área de estudo recortando os CEPs da Grande Atenas
Derivar temperatura de superfície terrestre a partir de imagens Landsat
Calcular cobertura arbórea por CEP a partir de dados de uso do solo (ESA WorldCover 2021)
Calcular densidade populacional por CEP
Padronizar as três variáveis em escala 1–5 (min-max)
Combinar as variáveis com expressão Arcade e visualizar o HRI no mapa
Publicar o resultado como web map no ArcGIS Online


Estrutura dos Dados
Área de estudo:

49 CEPs da Grande Atenas (excluindo parques nacionais, ilhas e áreas periféricas)
Fonte: Greece Postcodes3 Boundaries — ArcGIS Living Atlas

Variáveis de entrada:
VariávelFonteMétricaTemperatura de superfícieMultispectral Landsat (Living Atlas)Máxima temperatura média por CEP (°C)Cobertura arbóreaESA WorldCover 2021 (Living Atlas)% de ausência de cobertura vegetal por CEPDensidade populacionalGreece Postcodes3 BoundariesHabitantes / km² por CEP

Metodologia
1. Definição da área de estudo

Adição da camada Greece Postcodes3 Boundaries do Living Atlas
Seleção de 56 linhas na tabela de atributos, com remoção de 7 CEPs (parques, ilhas e áreas periféricas), resultando em 49 CEPs
Criação da camada Athens_Postcodes com Copy Features

2. Variável 1 — Temperatura de superfície terrestre

Adição da camada Multispectral Landsat do Living Atlas
Configuração das propriedades da camada:

Processing Template: Band 10 Surface Temperature in Celsius
Mosaic Operator: Mean
Definition Query: Cloud Cover ≤ 0.05 (exclusão de imagens com mais de 5% de nuvens)


Recorte para a área de estudo com Copy Raster (Processing Extent = Athens_Postcodes) → Avg_Surface_Temps_Athens.tif
Sumarização por CEP com Zonal Statistics As Table (Statistics Type: Maximum) → tabela High_Avg_Surface_Temp_by_Postcodes

3. Variável 2 — Cobertura arbórea

Adição da camada ESA WorldCover 2021 Land Cover do Living Atlas
Reclassificação com Remap raster function: células de cobertura arbórea (valor 10) → 1; demais → 0
Recorte para Atenas com Copy Raster → Tree_Canopy_Athens.tif
Contagem de células por CEP com Zonal Statistics As Table (Statistics Type: Sum) → tabela Count_of_Tree_Cells
Cálculo de campos com Calculate Field:

PCT_Tree_Cover = (!SUM! / !COUNT!) * 100
PCT_Lacking = 100 - !PCT_Tree_Cover!



4. Variável 3 — Densidade populacional

Cálculo direto na tabela Athens_Postcodes com Calculate Field:

Pop_Density = !2024 Total Population! / !Area in Square Kilometers!



5. Combinação e padronização

Transferência dos campos PCT_Lacking e MAX para Athens_Postcodes via Join Field
Padronização das três variáveis em escala 1–5 com Standardize Field (método min-max):

Pop_Density_MIN_MAX
PCT_Lacking_MIN_MAX
HighAvgTemps_MIN_MAX



6. Visualização com expressão Arcade

Simbolização por Unclassed Colors com expressão Arcade somando os três índices:

arcadeSum($feature.Pop_Density_MIN_MAX,
    $feature.PCT_Lacking_MIN_MAX,
    $feature.HighAvgTemps_MIN_MAX)

Esquema de cores: Yellow-Orange-Red (Continuous)
Efeito visual: Layer Blend = Multiply

7. Publicação como Web Map

Remoção de camadas auxiliares antes do compartilhamento
Compartilhamento no ArcGIS Online:

Nome: Athens Heat Risk Index_Camila Mariana
Resumo: A heat risk index for postcodes in Athens, Greece, and the surrounding urban areas
Tags: Athens, Climate change, Heat risk, Adaptation plan
Compartilhamento: Everyone




Resultados
CEPs com HRI mais altoCaracterísticaPiraeus / KeratsiniAlta temperatura + baixa cobertura arbórea + alta densidadeKallithea / Nea SmyrniAlta densidade populacional + temperatura elevadaVyronas / KaisarianiBaixa cobertura arbórea + temperatura elevada
CEPs em vermelho escuro = maior prioridade para plantio de árvores
CEPs em amarelo/laranja claro = menor risco acumulado

<img width="1852" height="768" alt="image" src="https://github.com/user-attachments/assets/655164fa-b9eb-4855-9474-7a1561f2e8b1" />

Conclusão
A análise permitiu identificar que:

As áreas centrais e costeiras de Atenas concentram os maiores valores de HRI, combinando alta temperatura de superfície, baixa cobertura arbórea e alta densidade populacional
O mapa web publicado permite que o Chief Heat Officer e gestores municipais priorizem espacialmente as intervenções de plantio de árvores
O método é replicável para outras cidades com dados equivalentes disponíveis no ArcGIS Living Atlas


🛠️ Ferramentas Utilizadas

ArcGIS Pro 3.6 (geoprocessamento, raster functions, padronização, Arcade)
Spatial Analyst Extension (Zonal Statistics As Table)
ArcGIS Online (publicação de web map)
ArcGIS Living Atlas (Landsat, ESA WorldCover, Greece Postcodes)


Autora
Camila Mariana Neri Rosa – Graduação em Oceanografia (UERJ)
LinkedIn | GitHub
