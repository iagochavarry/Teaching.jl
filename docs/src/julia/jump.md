# JuMP

JuMP é a biblioteca que usamos para resolver os problemas de otimização que escrevemos com a ajuda de um computador, JuMP não é a única biblioteca que faz isso, e nem Julia é a única linguagem com esse tipo de biblioteca.

* Em Python: [CVXPY](https://www.cvxpy.org/)

Existem inclusve linguagens e softwares criados unica e exclusivamente para isso:

* [GAMS](https://www.gams.com/)
* [AMPL](https://ampl.com/)

A melhor forma de descobrir as ferramentas oferecidas pela biblioteca é pela [documentação](http://www.juliaopt.org/JuMP.jl/stable/)

## Exemplo Básico

### Formulação

Usaremos o exemplo da produção de berços e armários para mostrar as ferramentas básicas do JuMP,
o modelo pode ser formulado da seguinte forma:

```math
\begin{align}
%
\max_{x \geq 0} & \sum 4x_1 + 3x_2\\
%
\mbox{s.a.: } & \nonumber \\
& 2x_1 + x_2 \leq 4 \\
& x_1 + 2x_2 \leq 4 \\
%
\end{align}
```

### Criando e resolvendo o modelo

Para escrever o problema no JuMP deveremos usar as macros (funções que tem @ na frente) para definir variáveis (@variables), restrições (@constraint) e a função objetivo (@objective)

```julia
using JuMP, Clp

modeloProd = Model(with_optimizer(Clp.Optimizer)) # Cria ma variável modeloProd onde podemos escrevr variáveis, restrições, qual solver usar etc.

@variable(modeloProd, x[i = 1:2] >= 0) # Definimos que em modeloProd existe uma variável x com duas entradas maiores que 0

# Definimos que em modeloProd existem restrições (uma associada à armários e outra à berços)
@constraint(modeloProd, armarios, 2*x[1] + x[2] <= 4)
@constraint(modeloProd, bercos, x[1] + 2*x[2] <= 4)

# Definimos que em modeloProd existe uma função objetivo
@objective(modeloProd, Max, 4*x[1] + 3*x[2])

JuMP.optimize!(modeloProd) # Resolve o problema de otimização

JuMP.value.(x) #Valor das variáveis x
JuMP.objective_value(modeloProd) #Valor da função objetivo no ótimo
```

### Valores das variáveis duais

É possível avaliar o valor das variáveis duais associadas a cada restrição usando a função `dual`

```julia
using JuMP, Clp

modeloProd = Model(with_optimizer(Clp.Optimizer))

@variable(modeloProd, x[i = 1:2] >= 0)

@constraint(modeloProd, armarios, 2*x[1] + x[2] <= 4)
@constraint(modeloProd, bercos, x[1] + 2*x[2] <= 4)

@objective(modeloProd, Max, 4*x[1] + 3*x[2])

JuMP.optimize!(modeloProd)

# Valor das variáveis duais associadas a cada restrição
dual(armarios)
dual(bercos)
```

### Como obter as constantes A, b e c de um problema de programação linear