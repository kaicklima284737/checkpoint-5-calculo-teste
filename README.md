# Checkpoint 5 — Derivadas, Mínimos Quadrados e Otimização

Projeto aplicado de modelagem matemática para análise do throughput de um serviço de processamento de imagens conforme o número de workers do pool aumenta.

## Arquivos

- `checkpoint.ipynb` — notebook principal, com a análise matemática completa: mínimos quadrados, derivadas, máximos e mínimos, otimização, implementação em Python, gráficos, teste de sensibilidade e interpretação, incluindo explicações detalhadas sobre todo o conteúdo.
- `requirements.txt` — dependências necessárias.
- `README.md` — instruções de execução.

## Modelo

O modelo adotado é um polinômio de grau 2, ajustado por mínimos quadrados a partir dos dados do teste de carga (número de workers x throughput observado, em req/s):

$$
T(w) = a\,w^{2} + b\,w + c
$$

onde:

- **`w`** é o número de workers do pool (unidade: workers);
- **`T(w)`** é o throughput previsto, em requisições por segundo (req/s);
- **`a`, `b`, `c`** são os coeficientes obtidos pelo método dos mínimos quadrados, com `numpy.polyfit()` / `numpy.linalg.lstsq()`.

O ajuste utiliza todos os 12 pontos fornecidos no enunciado (`w` de 2 a 24 workers), em vez de forçar a parábola a passar exatamente por apenas 3 pontos escolhidos, respeitando a variabilidade experimental dos dados de desempenho.

## Derivadas e otimização

A partir da função ajustada, o notebook calcula simbolicamente (com SymPy) a primeira derivada `T'(w)`, resolve `T'(w) = 0` para localizar o ponto crítico, analisa o sinal de `T'(w)` para identificar as regiões de ganho, saturação e degradação de desempenho, e usa a segunda derivada `T''(w)` para confirmar a classificação do ponto crítico como máximo.

$$
T'(w) = 2a\,w + b, \qquad T''(w) = 2a
$$

O ponto crítico é comparado com as extremidades do domínio operacional `2 ≤ w ≤ 24` para determinar o máximo e o mínimo absolutos, e o resultado contínuo é traduzido em uma recomendação inteira implementável (incluindo uma configuração "quase ótima" que atinge pelo menos 99% do throughput máximo previsto, usando menos workers).

## Teste de sensibilidade

O notebook também refaz o ajuste aumentando em 5% apenas a medição de `w=16`, comparando os novos coeficientes, o novo ponto ótimo e a nova recomendação inteira com os resultados originais, para avaliar a estabilidade da decisão técnica.

## Como executar

### 1. Instalar dependências

```bash
pip install -r requirements.txt

ou

py -m pip install -r requirements.txt
```

### 2. Executar o notebook

Abra `checkpoint.ipynb` no Jupyter Notebook, JupyterLab ou VS Code e execute todas as células em ordem (Run All). O notebook é totalmente reprodutível: todos os gráficos e tabelas são gerados pelo próprio código, a partir da base de dados definida no início do notebook.

```bash
OBS:
```
Verifique se o caminho do arquivo está dentro do caminho:
```bash
PS C:\Users\user\Downloads\CP5 Cálculo>
```
Ou onde está localizado o arquivo, antes de executar os comandos no terminal, para evitar erro de não encontrar arquivo.

## Decisões técnicas

O número de workers deve ser inteiro na implantação real, portanto o ótimo contínuo obtido a partir da derivada não é diretamente implementável. A equipe recomenda um novo teste controlado com `w = 15` workers (ótimo inteiro), com `w = 14` como alternativa "quase ótima" caso a prioridade seja economia de recursos, e `w = 16` como referência por ser a configuração de maior throughput já medida experimentalmente.

Recomenda-se validar essa recomendação com um novo teste de carga controlado antes de qualquer mudança definitiva em produção, já que o modelo é uma aproximação por mínimos quadrados construída a partir de um número limitado de medições.

| Nome:| RM:|
| ---:| ---:|
|Kaick Lima Silva       | 574060 |
|Gustavo Basso          | 572623 |
|Guilherme Salles       | 572933 |
|Pedro Feltrin          | 569038 |
|Guilherme Kozikoski    | 571611 |
