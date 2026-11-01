---
title: "Desvendando Tanenbaum: Cap. 2 – Sinais, Meios e Transmissão de Dados"
date: 2026-11-01T13:00:00-03:00
draft: false
series:
  - "Desvendando Tanenbaum"
tags:
  - "redes-de-computadores"
  - "tanenbaum"
  - "camada-fisica"
  - "transmissao-de-dados"
  - "fibra-optica"
  - "modulacao-digital"
  - "multiplexacao"
summary: "Uma exploração didática da camada física, dos limites impostos aos canais de comunicação e das tecnologias que transportam bits por cobre, fibra óptica e ondas eletromagnéticas."
---

> **Aviso e Créditos Acadêmicos:** Este artigo é uma síntese didática e autoral baseada no Capítulo 2 ("A Camada Física") de *Redes de Computadores* (6ª edição), de Andrew S. Tanenbaum, Nick Feamster e David J. Wetherall. O texto organiza e explica os conceitos com palavras próprias, sem substituir a leitura da obra original.

## 1. Bits não atravessam cabos

Quando enviamos uma mensagem, assistimos a um vídeo ou abrimos uma página, costumamos imaginar os dados viajando pela rede como uma sequência de zeros e uns. Essa representação é útil para o software, mas não descreve o que realmente atravessa o meio físico.

Cabos não transportam números abstratos. Uma fibra óptica conduz pulsos de luz. Um par de cobre sofre variações de tensão. Uma antena irradia ondas eletromagnéticas. Em todos esses casos, o receptor precisa observar um fenômeno físico e decidir quais símbolos foram enviados.

Essa é a responsabilidade da camada física: estabelecer como os bits serão representados, transmitidos e recuperados por um meio real. O problema parece simples até lembrarmos que nenhum canal é perfeito. Sinais enfraquecem, sofrem distorção, recebem interferência e disputam espaço com outras transmissões.

A tese deste capítulo nasce dessa limitação: **a velocidade de uma rede não depende apenas do processador ou do protocolo, mas das propriedades físicas do canal e das técnicas usadas para aproveitar sua capacidade**.

## 2. Todo sinal complexo é formado por componentes mais simples

Um pulso quadrado parece uma forma elementar de representar um bit. Na prática, ele resulta da combinação de várias frequências. A análise de Fourier permite decompor uma função periódica em uma soma de senos e cossenos:

$$
g(t) = \frac{1}{2}c + \sum_{n=1}^{\infty} a_n \sin(2\pi nft) + \sum_{n=1}^{\infty} b_n \cos(2\pi nft)
$$

Nessa expressão, $f = 1/T$ é a frequência fundamental do sinal periódico, enquanto $a_n$ e $b_n$ representam as amplitudes de seus harmônicos.

O ponto importante para redes não está em decorar a fórmula. Está em perceber que um canal físico não transmite todas essas frequências da mesma forma. Componentes mais altas podem sofrer maior atenuação. Quando parte dos harmônicos desaparece, o pulso recebido perde suas bordas e se torna mais difícil de distinguir.

```text
Sinal ideal                 Depois do canal

+-----+     +-----+             /\        /\
|     |     |     |            /  \      /  \
+     +-----+     +      ->   /    \____/    \
```

A **largura de banda analógica** descreve a faixa de frequências que o canal consegue transportar sem atenuação excessiva. Ela é medida em hertz. Isso não deve ser confundido com a taxa de dados, medida em bits por segundo. Uma grandeza descreve o espectro disponível; a outra, a quantidade de informação digital transmitida.

> **Exemplo da fonte:** O capítulo analisa a transmissão do caractere ASCII `b`, representado por `01100010`, por uma linha telefônica de largura de banda limitada. Conforme a taxa aumenta, menos harmônicos conseguem atravessar o canal e a forma de onda recebida se torna mais difícil de reconhecer.

(Tanenbaum, Feamster e Wetherall, 6ª ed., Seção 2.4, pp. 69–73)

### Nyquist mostra que até um canal ideal tem limite

Mesmo sem ruído, um canal com largura de banda finita não comporta variações arbitrariamente rápidas. Para um canal passa-baixas de largura de banda $B$ que utiliza $V$ níveis discretos, o limite de Nyquist é:

$$
\text{Taxa máxima} = 2B\log_2 V
$$

Aqui, $B$ é medido em hertz e $V$ indica quantos níveis distintos podem representar símbolos. Ao aumentar $V$, cada símbolo passa a carregar mais bits. O ganho, porém, tem custo: níveis mais próximos são mais difíceis de distinguir quando há ruído.

### Shannon inclui o ruído que existe no mundo real

Canais reais estão sujeitos a ruído. Shannon relacionou a largura de banda $B$ e a razão entre a potência do sinal $S$ e a potência do ruído $N$:

$$
C = B\log_2\left(1 + \frac{S}{N}\right)
$$

A capacidade $C$ é expressa em bits por segundo. A relação sinal-ruído também pode aparecer em decibéis:

$$
\operatorname{SNR}_{dB} = 10\log_{10}\left(\frac{S}{N}\right)
$$

Nyquist e Shannon respondem a perguntas diferentes. Nyquist mostra o efeito da largura de banda e do número de níveis em um canal ideal. Shannon estabelece o teto teórico de um canal ruidoso. Nenhuma das fórmulas entrega automaticamente a velocidade de uma rede real, pois implementações ainda enfrentam codificação, interferência, erros e limitações de hardware.

## 3. Cobre e fibra resolvem o mesmo problema de formas diferentes

Um meio guiado direciona o sinal por um caminho físico. Par trançado, cabo coaxial e fibra óptica pertencem a essa categoria, mas suas propriedades diferem bastante.

### O trançamento do cobre não é decorativo

O par trançado utiliza dois condutores isolados enrolados um ao redor do outro. O receptor observa a diferença de potencial entre eles. Como interferências externas tendem a afetar os dois fios de modo semelhante, parte do ruído comum pode ser cancelada.

```text
Fio A   /\/\/\/\/\/\/\
Fio B   \/\/\/\/\/\/\/
```

Categorias diferentes impõem requisitos distintos de construção, frequência e blindagem. O número estampado no cabo não é uma promessa isolada de velocidade. Distância, conectores, instalação e interferência também influenciam o enlace.

O cabo coaxial organiza os condutores de forma concêntrica: um condutor central, uma camada isolante e uma blindagem externa. Essa geometria oferece maior proteção contra interferência e tornou o coaxial importante em sistemas de televisão a cabo e redes HFC (*Hybrid Fiber-Coaxial*).

### Na fibra, a informação viaja como luz

Uma fibra óptica possui um núcleo cercado por um revestimento de menor índice de refração. Em condições adequadas, a luz permanece confinada no núcleo por reflexão total interna.

```text
Revestimento
================================================
   \      /\      /\      /\      /
----\----/--\----/--\----/--\----/---- Núcleo
     \  /    \  /    \  /    \  /
================================================
Revestimento
```

Na fibra multimodo, diferentes trajetórias podem chegar em momentos ligeiramente diferentes, causando dispersão modal. Na monomodo, o núcleo estreito restringe a propagação e reduz esse efeito, favorecendo distâncias maiores e taxas elevadas.

As janelas ópticas também importam porque a atenuação varia com o comprimento de onda. A escolha da fonte luminosa, do detector e da faixa de operação depende do compromisso entre custo, distância e capacidade.

| Critério | Par trançado | Coaxial | Fibra óptica |
|---|---|---|---|
| Sinal | Elétrico | Elétrico | Óptico |
| Interferência eletromagnética | Suscetível | Menor devido à blindagem | Imune ao acoplamento eletromagnético |
| Instalação | Simples e barata | Mais rígida | Exige maior precisão |
| Alcance típico | Curto | Curto a metropolitano | Longo |
| Capacidade potencial | Moderada | Alta | Muito alta |

A fibra não é automaticamente a melhor escolha em qualquer cenário. Ela oferece grande capacidade e baixa atenuação, mas interfaces ópticas, emendas e manutenção exigem ferramentas e conhecimentos específicos.

(Tanenbaum, Feamster e Wetherall, 6ª ed., Seção 2.1, pp. 57–64)

## 4. O ar é um meio compartilhado e imprevisível

No cabo, o sinal permanece relativamente confinado. No rádio, a energia se espalha pelo ambiente, encontra obstáculos e pode chegar ao receptor por caminhos diferentes.

Frequência e comprimento de onda estão relacionados por:

$$
\lambda f = c
$$

Nessa equação, $\lambda$ é o comprimento de onda, $f$ é a frequência e $c$ é a velocidade de propagação da luz no meio. Frequências diferentes apresentam comportamentos distintos de propagação e exigem antenas, potências e técnicas de modulação adequadas.

Ondas de rádio em faixas mais baixas podem contornar obstáculos ou acompanhar melhor a superfície terrestre. Micro-ondas tendem a exigir linha de visada e permitem feixes mais direcionais. Infravermelho e luz visível oferecem comunicação localizada, mas são bloqueados por obstáculos opacos.

### O mesmo sinal pode chegar várias vezes

Em ambientes urbanos e internos, uma onda reflete em paredes, solo e objetos. O receptor recebe versões atrasadas do mesmo sinal. Dependendo da fase, elas podem se reforçar ou se cancelar. Esse fenômeno é o desvanecimento por múltiplos caminhos, ou *multipath fading*.

```text
Transmissor ------ caminho direto ------> Receptor
     \                                  /
      \---- reflexão em obstáculo ------/
```

A potência também diminui com a distância. No espaço livre, a densidade de potência se espalha por uma área cada vez maior. Isso ajuda a explicar por que alcance, frequência, obstáculos e potência de transmissão precisam ser analisados em conjunto.

### Espalhar o espectro pode aumentar a robustez

No FHSS (*Frequency Hopping Spread Spectrum*), transmissor e receptor alternam frequências segundo uma sequência compartilhada. Uma interferência estreita pode afetar alguns saltos sem impedir toda a comunicação.

No DSSS (*Direct Sequence Spread Spectrum*), cada bit é combinado com uma sequência mais rápida de *chips*, espalhando sua energia por uma banda maior. A técnica permite recuperar o sinal conhecido mesmo na presença de interferência.

A banda ultralarga, ou UWB (*Ultra-Wideband*), emprega pulsos curtos distribuídos por uma faixa extensa. Sua resolução temporal favorece aplicações de localização e medição de distância.

(Tanenbaum, Feamster e Wetherall, 6ª ed., Seções 2.2 e 2.3, pp. 64–69)

## 5. Modulação é a arte de representar bits em sinais

A camada física precisa escolher quais mudanças observáveis transportarão a informação. Pode alterar nível elétrico, amplitude, frequência, fase ou uma combinação dessas propriedades.

### Banda base usa o próprio nível do sinal

Na transmissão em banda base, o sinal ocupa frequências próximas de zero. Códigos de linha determinam como bits se transformam em níveis e transições.

No NRZ (*Non-Return-to-Zero*), níveis diferentes representam valores binários. O método é simples, mas sequências longas sem transições dificultam a recuperação do relógio.

No NRZI (*Non-Return-to-Zero Inverted*), uma transição representa determinado valor. Codificações em blocos, como 4B/5B, substituem grupos de bits por símbolos escolhidos para garantir transições suficientes.

O objetivo não é apenas representar zeros e uns. Um código de linha também precisa equilibrar sincronização, largura de banda, componente contínua e tolerância a erros.

### Banda passante modifica uma portadora

Quando o canal opera em torno de uma faixa de frequência específica, os dados modulam uma portadora. ASK altera a amplitude, FSK altera a frequência e PSK altera a fase.

No QPSK (*Quadrature Phase Shift Keying*), quatro fases representam quatro símbolos, permitindo dois bits por símbolo. A QAM (*Quadrature Amplitude Modulation*) combina amplitude e fase. Uma constelação com mais pontos carrega mais bits por símbolo, mas exige melhor relação sinal-ruído para que o receptor diferencie pontos próximos.

```text
Q
^       *       *
|
|
+-----------------> I
|
|       *       *
```

A taxa de símbolos, medida em baud, indica quantos símbolos são enviados por segundo. Se cada símbolo representa $k$ bits, então:

$$
\text{taxa de bits} = \text{taxa de símbolos} \times k
$$

Aumentar os bits por símbolo melhora a eficiência espectral, mas reduz a margem contra ruído. Modulações modernas adaptam esse compromisso conforme a qualidade do canal.

### Multiplexação faz vários fluxos dividirem o mesmo meio

A multiplexação permite compartilhar infraestrutura:

- FDM separa usuários por faixas de frequência;
- TDM separa usuários por intervalos de tempo;
- WDM separa fluxos ópticos por comprimentos de onda;
- OFDM divide o canal em várias subportadoras ortogonais.

| Técnica | Recurso dividido | Ideia central |
|---|---|---|
| FDM | Frequência | Cada fluxo ocupa uma subfaixa |
| TDM | Tempo | Cada fluxo usa intervalos determinados |
| WDM | Comprimento de onda | Diferentes cores compartilham a fibra |
| OFDM | Subportadoras ortogonais | Vários canais estreitos operam em paralelo |

No CDMA (*Code Division Multiple Access*), vários transmissores podem compartilhar tempo e frequência usando sequências de chips distintas. Se as sequências forem ortogonais, o receptor consegue projetar o sinal combinado sobre o código desejado e recuperar a contribuição de uma estação.

Para sequências $S$ e $T$ de comprimento $m$, a ortogonalidade pode ser expressa como:

$$
S \cdot T = \frac{1}{m}\sum_{i=1}^{m} S_iT_i = 0
$$

O resultado zero indica que uma sequência não contribui para a correlação da outra. Em sistemas reais, sincronização, potência e propagação tornam o problema mais complexo do que o caso matemático ideal.

(Tanenbaum, Feamster e Wetherall, 6ª ed., Seção 2.4.4, pp. 78–83)

## 6. A última milha revela os compromissos da engenharia

As técnicas anteriores aparecem combinadas nas redes que conectam residências, empresas e dispositivos móveis. Cada arquitetura tenta aproveitar uma infraestrutura existente ou contornar uma limitação física.

### A telefonia reaproveitou o cobre

A PSTN (*Public Switched Telephone Network*) foi projetada para voz. O circuito terminal conectava a residência à central por par trançado. Tecnologias xDSL aproveitaram frequências não utilizadas pela telefonia convencional para transportar dados.

No ADSL (*Asymmetric Digital Subscriber Line*), a banda é dividida em vários subcanais. Mais capacidade é destinada ao sentido de recepção porque o uso residencial tradicional costuma exigir mais *download* que *upload*.

Quando a fibra chega à residência, arquiteturas FTTH (*Fiber To The Home*) podem usar redes ópticas passivas. Um divisor óptico compartilha a fibra entre assinantes sem exigir equipamentos eletrônicos ativos em cada ponto da rua.

```text
OLT ---- fibra ---- divisor passivo ---- ONT 1
                           |------------- ONT 2
                           +------------- ONT 3
```

### TV a cabo virou rede de dados

Redes HFC combinam troncos de fibra com cabos coaxiais no acesso final. O DOCSIS define como modems compartilham esse meio. O tráfego de recepção é distribuído pelo segmento; no sentido de envio, o equipamento central precisa coordenar os assinantes para evitar transmissões simultâneas incompatíveis.

A diferença para um par dedicado é importante: vizinhos conectados ao mesmo segmento coaxial compartilham capacidade. O desempenho percebido depende do provisionamento e da atividade conjunta.

### Mobilidade depende de reutilização e transferência

Redes celulares dividem o território em células. Frequências podem ser reutilizadas em áreas suficientemente separadas, aumentando a capacidade do sistema. Quando o usuário se move, a rede transfere a comunicação entre estações-base por um *handover*.

As gerações celulares mudaram voz, dados e núcleo da rede. Sistemas iniciais priorizavam voz; gerações posteriores incorporaram dados por pacotes; 4G adotou um núcleo baseado em IP. O 5G amplia o uso de múltiplas antenas, células menores e virtualização, mas seus resultados dependem das faixas de frequência e da implantação disponível.

Satélites oferecem outra forma de cobertura. GEO mantém posição aparente fixa, mas a grande distância aumenta o atraso. LEO reduz a distância e a latência de propagação, porém exige constelações e transferências frequentes entre satélites.

(Tanenbaum, Feamster e Wetherall, 6ª ed., Seções 2.5 a 2.8, pp. 83–118)

## 7. Glossário técnico da camada física

| # | Termo | Explicação simples | Significado técnico |
|---|---|---|---|
| 1 | Largura de banda | Faixa de frequências disponível | Intervalo de frequências transmitido pelo canal |
| 2 | Atenuação | Enfraquecimento do sinal | Redução de potência durante a propagação |
| 3 | Harmônico | Componente senoidal de um sinal | Frequência múltipla da fundamental |
| 4 | Baud | Símbolos transmitidos por segundo | Taxa de sinalização do canal |
| 5 | QAM | Modulação de amplitude e fase | Esquema que representa símbolos em uma constelação |
| 6 | OFDM | Várias subportadoras em paralelo | Multiplexação por frequências ortogonais |
| 7 | Reflexão total interna | Luz confinada na fibra | Fenômeno que mantém o feixe dentro do núcleo |
| 8 | WDM | Várias cores na mesma fibra | Multiplexação por comprimento de onda |
| 9 | CDMA | Usuários separados por códigos | Acesso múltiplo por sequências de chips |
| 10 | HFC | Fibra combinada com coaxial | Arquitetura híbrida usada por redes de cabo |
| 11 | PON | Fibra compartilhada passivamente | Rede óptica com divisores sem alimentação elétrica |
| 12 | Handover | Transferência entre células | Procedimento de continuidade durante a mobilidade |

## 8. A física entrega bits, não significado

A camada física estabelece o terreno sobre o qual todas as outras camadas trabalham. Fourier ajuda a entender a composição dos sinais. Nyquist relaciona largura de banda, símbolos e taxa em um canal ideal. Shannon mostra como o ruído limita a capacidade de um canal real.

Cobre, fibra e rádio não são apenas meios intercambiáveis. Cada um impõe custos, alcances e formas de interferência. Modulação e multiplexação transformam essas restrições em decisões práticas: quantos bits cabem em cada símbolo, como vários fluxos compartilham o meio e quanto ruído o receptor consegue suportar.

Ainda assim, receber uma sequência de bits não significa receber uma mensagem correta. A camada física não delimita sozinha onde um bloco começa, não garante que todos os bits estejam certos e não impede que um transmissor rápido sobrecarregue o receptor. Esses problemas pertencem à camada de enlace.

> **Nota de Conclusão do Estudo:** A camada física converte informação digital em fenômenos mensuráveis e determina os limites básicos de capacidade, alcance e confiabilidade do canal. Compreender sinais, meios, modulação e multiplexação prepara o estudo da camada de enlace, responsável por organizar o fluxo bruto em quadros e lidar com erros e controle de transmissão.
