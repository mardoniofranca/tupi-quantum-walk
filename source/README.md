# Caminhada Quântica 1D (Quantum Walk)

Simulação de uma **Caminhada Quântica Discreta Unidimensional** usando Python, NumPy e Matplotlib.

---

##  O que é uma Caminhada Quântica?

Uma caminhada quântica é o análogo quântico da caminhada aleatória clássica. Em vez de o "andarilho" ir para a esquerda ou direita com probabilidade clássica (50/50), ele existe em **superposição** de estados — indo para os dois lados ao mesmo tempo — até ser observado.

O resultado é uma distribuição de probabilidade radicalmente diferente da gaussiana clássica: ela exibe **dois picos simétricos** nas extremidades e interferência destrutiva no centro (formato em "U" ou "W").

---

##  Componentes do Código

### 1. `hadamard()`

```python
def hadamard():
    return (1/np.sqrt(2)) * np.array([[1, 1],
                                      [1, -1]])
```

Retorna a **Porta Hadamard** — a "moeda quântica" do sistema.

| Entrada | Saída (superposição) |
|--------|----------------------|
| `\|0⟩` (spin ↑) | `(|0⟩ + |1⟩) / √2` |
| `\|1⟩` (spin ↓) | `(|0⟩ - |1⟩) / √2` |

Ela coloca o spin em **superposição** antes de cada passo, criando a interferência quântica característica.

---

### 2. `shift_operator(state)`

```python
def shift_operator(state):
    ...
    new_state[x + 1, 0] += state[x, 0]  # |+⟩ → move para a direita
    new_state[x - 1, 1] += state[x, 1]  # |−⟩ → move para a esquerda
```

O **operador de deslocamento condicional**: move o andarilho dependendo do estado da moeda quântica (spin).

- Coluna `0` → spin ↑ → desloca **+1** (direita)
- Coluna `1` → spin ↓ → desloca **−1** (esquerda)

O estado `state` tem shape `(n_positions, 2)`:
- Eixo 0 → posições no espaço
- Eixo 1 → estado interno da moeda `[|+⟩, |−⟩]`

---

### 3. `coin_operator(state, C)`

```python
def coin_operator(state, C):
    return np.einsum('ij,xj->xi', C, state)
```

Aplica a **porta Hadamard a cada posição** do espaço usando `einsum`.

- `'ij,xj->xi'` → para cada posição `x`, multiplica a moeda `C[i,j]` pelo estado interno `state[x,j]`
- Equivale a aplicar `C @ state[x]` para todo `x` simultaneamente

---

### 4. `quantum_walk(n_steps, n_positions)`

```python
state[center] = (1/np.sqrt(2)) * np.array([1, 1j])
```

O coração da simulação. Pontos-chave:

| Detalhe | Explicação |
|--------|-----------|
| **Estado inicial** | Andarilho no centro, moeda em superposição `(|0⟩ + i|1⟩)/√2` |
| **Fase `1j` (imaginária)** | Garante simetria da distribuição final em torno do centro |
| **Loop** | A cada passo: aplica a moeda → aplica o deslocamento |
| **Normalização** | O fator `1/√2` mantém a norma do estado igual a 1 |

A ordem importa: **coin → shift**, repetido `n_steps` vezes.

---

### 5. `probability_distribution(state)`

```python
return np.sum(np.abs(state)**2, axis=1)
```

Calcula a **probabilidade de o andarilho estar em cada posição**:

```
P(x) = |ψ(x, +)|² + |ψ(x, −)|²
```

Soma os módulos ao quadrado de ambos os spins em cada posição — princípio de Born da mecânica quântica.

---

## Loop Principal

```python
for n in range(1, n_steps):
    n_positions = 2 * n + 1
    state = quantum_walk(n, n_positions)
    prob = probability_distribution(state)
    positions = np.arange(n_positions) - n_positions // 2
```

| Variável | Significado |
|---------|-------------|
| `n` | Número de passos da caminhada |
| `n_positions = 2n + 1` | Grade cresce para comportar o alcance máximo `[-n, +n]` |
| `positions` | Recentra o eixo x em zero (posição relativa ao início) |

A cada iteração, o número de passos **e** o tamanho do espaço crescem juntos — evitando que o andarilho "bata nas bordas".

---

##  Resultado Visual

A distribuição evolui de um pico central (n pequeno) para o característico **perfil bimodal** da caminhada quântica:

```
Prob
 |       *               *
 |      ***             ***
 |     *****           *****
 |  **                       **
 |_________________________________  Posição
       -n         0          +n
```

Diferente da caminhada clássica (gaussiana centrada em 0), a caminhada quântica acumula probabilidade **nas extremidades** por interferência construtiva, com supressão no centro por interferência destrutiva.

---

## Dependências

```bash
pip install numpy matplotlib
```

---

##  Como Executar

```bash
python quantum_walk.py
```

---

##  Referências

- Aharonov, Y. et al. (1993). *Quantum random walks*. Physical Review A.
- Kempe, J. (2003). *Quantum random walks: an introductory overview*. Contemporary Physics.
- Nielsen & Chuang — *Quantum Computation and Quantum Information*

---

##  Conceitos-Chave

`Superposição` · `Interferência Quântica` · `Porta Hadamard` · `Espaço de Hilbert` · `Princípio de Born` · `Operador Unitário`
