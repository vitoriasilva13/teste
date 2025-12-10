# 🐟 Calculadora de Peso de Peixes

Uma aplicação web científica para calcular o peso de peixes a partir do comprimento, utilizando relações alométricas baseadas em regressão linear logarítmica.

## 📋 Descrição

**`index.html`** é uma calculadora multi-espécies que processa arquivos CSV com dados históricos para estimar o peso de peixes usando a **Lei de Potência Alométrica**. O sistema automaticamente calcula os coeficientes alométricos para cada espécie no dataset e oferece uma interface intuitiva para fazer predições.

## 🧮 Fundamento Matemático

### Modelo Alométrico

A relação peso-comprimento em peixes segue uma **lei de potência**:

$$W = a \cdot L^b$$

Onde:
- **W** = Peso (gramas)
- **L** = Comprimento (centímetros)
- **a** = Fator de condição (intercepto)
- **b** = Coeficiente alométrico (expoente, geralmente entre 2,5 e 3,5)

### Linearização Logarítmica

Para calcular os parâmetros **a** e **b**, aplicamos logaritmo natural em ambos os lados:

$$\ln(W) = \ln(a) + b \cdot \ln(L)$$

Isso transforma a relação não-linear em uma **equação linear**:

$$Y = A + b \cdot X$$

Onde:
- $Y = \ln(W)$
- $X = \ln(L)$
- $A = \ln(a)$ (intercepto da reta)
- $b$ = inclinação da reta

### Regressão Linear (Método dos Mínimos Quadrados)

Com os dados transformados, calculamos os parâmetros usando as fórmulas de regressão linear:

**Inclinação (b):**

$$b = \frac{n \sum(XY) - \sum X \sum Y}{n \sum(X^2) - (\sum X)^2}$$

**Intercepto (A):**

$$A = \frac{\sum Y - b \sum X}{n}$$

**Conversão para o parâmetro 'a':**

$$a = e^A$$

Onde:
- $n$ = número de amostras
- $\sum X$ = soma dos logaritmos dos comprimentos
- $\sum Y$ = soma dos logaritmos dos pesos
- $\sum XY$ = soma dos produtos $X \cdot Y$
- $\sum X^2$ = soma dos quadrados de $X$

## 🚀 Como Usar

1. **Abra o arquivo** `index.html` no navegador
2. **Carregue um CSV** com colunas: `species`, `length`, `weight`
   - Exemplo: `fish_data.csv` ou `tucunare.csv`
3. O sistema **calcula automaticamente** os coeficientes para cada espécie
4. **Selecione uma espécie** no menu dropdown
5. **Digite o comprimento** do peixe
6. **Clique em "Calcular Peso"** para obter a estimativa

### Recursos Adicionais

- **Ver tabela dos dados**: Visualize todos os registros carregados do CSV
- **Ver coeficientes**: Consulte os valores de `a` e `b` calculados para cada espécie, com a quantidade de amostras utilizada
- **Proteção contra XSS**: Nomes de espécies são sanitizados para segurança

## 📊 Estrutura dos Dados

### Formato CSV Aceito

```csv
species,length,weight
Anabas testudineus,10.66,3.45
Cichla monoculus,18.5,85.2
```

**Requisitos:**
- Linha de cabeçalho obrigatória
- Valores numéricos positivos
- Separador: vírgula (`,`)

## 💻 Tecnologias

- **HTML5**: Estrutura das páginas
- **CSS3**: Estilos inline e custom properties
- **JavaScript (Vanilla)**: Toda a lógica matemática e manipulação do DOM

**Sem dependências externas** - funciona offline, basta abrir no navegador!

## 🔬 Exemplo de Cálculo

Dado um conjunto de dados de campo:

| Comprimento (cm) | Peso (g) |
|------------------|----------|
| 10               | 15       |
| 20               | 125      |
| 30               | 410      |

**Passo 1**: Transformação logarítmica
- $X_1 = \ln(10) = 2.303$, $Y_1 = \ln(15) = 2.708$
- $X_2 = \ln(20) = 2.996$, $Y_2 = \ln(125) = 4.828$
- $X_3 = \ln(30) = 3.401$, $Y_3 = \ln(410) = 6.016$

**Passo 2**: Cálculo das somas
- $\sum X = 8.700$, $\sum Y = 13.552$
- $\sum XY = 39.464$, $\sum X^2 = 25.386$

**Passo 3**: Aplicação das fórmulas
- $b = \frac{3 \times 39.464 - 8.700 \times 13.552}{3 \times 25.386 - 8.700^2} = 3.02$
- $A = \frac{13.552 - 3.02 \times 8.700}{3} = -4.23$
- $a = e^{-4.23} = 0.0145$

**Modelo resultante**: $W = 0.0145 \cdot L^{3.02}$

**Predição para L = 25 cm**: $W = 0.0145 \times 25^{3.02} = 228$ gramas

## ⚠️ Considerações Importantes

- **Use `Math.log()`**: Logaritmo natural, não `Math.log10()`
- **Validação de dados**: O código ignora valores ≤ 0
- **CSV sem aspas**: O parser não suporta vírgulas dentro de campos
- **Pop-up blockers**: Podem impedir a visualização das tabelas de dados
- **Precisão**: Coeficiente `a` usa 4-5 casas decimais, `b` usa 3 casas

## 📁 Arquivos do Projeto

```
.
├── index.html          # Calculadora multi-espécies (CSV)
├── fish_data.csv       # Dataset com 4000+ registros (múltiplas espécies)
├── tucunare.csv        # Dataset de Cichla (46 registros)
└── README.md           # Esta documentação
```

## 🎯 Casos de Uso

- **Pesquisa ictiológica**: Estimar biomassa em estudos populacionais
- **Pesca sustentável**: Prever peso sem necessidade de balança
- **Educação**: Demonstrar conceitos de regressão e alometria
- **Trabalho de campo**: Calibração rápida com dados locais

## 📖 Referências Científicas

A relação peso-comprimento é amplamente utilizada em biologia pesqueira:

- **b ≈ 3**: crescimento isométrico (proporções constantes)
- **b < 3**: crescimento alométrico negativo (peixe fica mais "fino")
- **b > 3**: crescimento alométrico positivo (peixe fica mais "robusto")

## 🤝 Melhorias Futuras

Possíveis extensões:

- [ ] Cálculo do coeficiente de determinação (R²)
- [ ] Intervalo de confiança para predições
- [ ] Suporte a outras unidades (polegadas, libras)
- [ ] Gráficos de dispersão com a linha de regressão
- [ ] Exportar coeficientes em formatos científicos (JSON, CSV)
- [ ] Análise de resíduos

## 📄 Licença

Código aberto para fins educacionais e científicos.

---

**Desenvolvido com 🐟 para a comunidade científica**
