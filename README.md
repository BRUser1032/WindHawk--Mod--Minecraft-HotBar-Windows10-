# Minecraft Hotbar para a barra de tarefas do Windows 10 (Windhawk)

Deixa a barra de tarefas do **Windows 10** com visual de **hotbar do Minecraft**: barra transparente e slots escuros com moldura cinza atrás dos botões dos apps, do botão Iniciar, dos ícones da bandeja e do relógio.

Feito para quem não pode migrar para o Windows 11. O visual parte da versão para Windows 11 do mesmo autor: [WindHawk--Mod--Minecraft-HotBar](https://github.com/BRUser1032/WindHawk--Mod--Minecraft-HotBar).

Configuração apresentada no canal **@BitRizeBR** (YouTube), feita com ajuda do Claude (IA).

> **Experimental.** Isto é um mod compilável (C++) para o Windhawk, ao contrário da versão do Windows 11, que é só um arquivo YAML. Veja a seção [Status dos testes](#status-dos-testes) antes de instalar: nem tudo foi confirmado.

<!-- Adicione aqui um print de cada variante, por exemplo: docs/fullbar.png e docs/splitbar.png -->

---

## Escolha uma variante

| | **fullbar** | **splitbar** |
|---|---|---|
| Botões dos apps | ficam onde o Windows põe (à esquerda, ao lado do Iniciar) | **centralizados** na barra |
| Botão Iniciar | à esquerda | à esquerda, separado dos apps |
| Bandeja e relógio | à direita | à direita |
| Estado dos testes | testado em 1 máquina (prints) | **não testado** |
| Risco | menor | maior: move uma janela interna da barra |
| Arquivo | `minecraft-hotbar-win10-fullbar.wh.cpp` | `minecraft-hotbar-win10-splitbar.wh.cpp` |

**Use só uma variante por vez.** As duas usam o mesmo ponto de hook no Explorer e não devem rodar juntas. Se tiver instalado uma versão antiga deste mod (id `minecraft-hotbar-win10`), desative-a antes.

Na dúvida, comece pela **fullbar**.

---

## Requisitos

| Item | Detalhe |
|---|---|
| Windows | Windows 10 22H2, 64 bits (build 19045.x) |
| Windhawk | [windhawk.net](https://windhawk.net), versão 2.x |
| Tema | modo escuro (as cores dos slots foram pensadas para ele) |
| Barra de tarefas | horizontal, na parte de baixo ou de cima da tela |

Windows 11 **não** é suportado por este repositório.

---

## Início rápido

1. Instale o Windhawk.
2. Baixe o arquivo `.wh.cpp` da variante escolhida. Abra no Bloco de Notas, selecione tudo (`Ctrl+A`) e copie (`Ctrl+C`). Já aconteceu de um caractere extra ser colado no fim do código ao copiar de outro lugar, e isso quebra a compilação.
3. No Windhawk, crie um novo mod, apague o código de exemplo e cole o arquivo inteiro.
4. Compile e ative o mod.
5. Se os slots não aparecerem, reinicie o Explorer (Gerenciador de Tarefas → Windows Explorer → Reiniciar).
6. Ajuste o Windows: **Configurações → Personalização → Barra de tarefas → "Combinar botões da barra de tarefas" → "Sempre, ocultar rótulos"**. Sem isso, cada botão fica largo e o slot não ocupa a largura inteira.

Se der erro ao compilar, copie o texto da aba **Output** e abra um issue.

---

## Configurações do mod

Ficam na aba de configurações do mod no Windhawk. Mudar uma configuração recarrega o mod.

| Configuração | Padrão | O que faz | Variante |
|---|---|---|---|
| Frame around the Start button | ligado | Slot atrás do botão Iniciar | ambas |
| Frames around the notification-area icons | ligado | Slots atrás dos ícones da bandeja (chevron, Wi-Fi, volume etc.) | ambas |
| Frame around the clock/date | ligado | Slot atrás do idioma e do relógio | ambas |
| One single slot for the whole tray | desligado | Um slot grande para a bandeja inteira, em vez de vários | ambas |
| Square app slots (with gaps) | desligado | Slots quadrados com espaço entre eles. Desligado, os slots se encostam como na hotbar real | ambas |
| Center the app buttons | ligado | Centraliza os botões dos apps | splitbar |
| Horizontal offset of the centered buttons (px) | 0 | Positivo move para a direita, negativo para a esquerda | splitbar |

Se a parte da janela auxiliar (Iniciar, bandeja e relógio) der problema, desligue as três primeiras opções. Os slots dos apps continuam funcionando.

---

## Status dos testes

Ambiente testado: Windows 10 22H2 (19045.x), 1280x720, monitor único, tema escuro, **uma máquina**. Build exata: _(preencher)_.

### fullbar

| Item | Status |
|---|---|
| Barra de tarefas transparente | Funciona (confirmado no print) |
| Slots estilo hotbar atrás dos botões dos apps | Funciona (confirmado no print) |
| Slot no botão Iniciar | Funciona (confirmado no print) |
| Slots na bandeja (ícones agrupados) | Funciona (confirmado no print) |
| Slot do idioma e do relógio juntos | Funciona (confirmado no print). O texto fica com pouca folga nas bordas |
| Slot no botão de notificações | Funciona (confirmado no print) |
| Moldura branca no app ativo | Não confirmado nos prints |
| Slot único para a bandeja (opção) | Não testado |
| Slots quadrados com espaço (opção) | Não testado |
| Janela auxiliar some com app em tela cheia | Não testado |
| Desativar o mod devolve a barra ao normal | Não testado |
| Segundo monitor | Não suportado (só a barra principal recebe Iniciar, bandeja e relógio) |
| Barra de tarefas vertical | Não suportado |

### splitbar

| Item | Status |
|---|---|
| Tudo da fullbar | Mesmo código da fullbar, com os mesmos resultados esperados |
| Centralização dos botões dos apps | **Não testado** |
| Posição se mantém ao abrir e fechar apps | **Não testado** |
| Botões voltam para a esquerda ao desativar o mod | **Não testado** |

---

## Como funciona

- **Slots dos apps:** o mod faz hook na função `CTaskBtnGroup::_DrawBar` do `explorer.exe`, que desenha o fundo de cada botão, e desenha o slot no lugar. O ícone continua sendo desenhado pelo Windows por cima.
- **Barra transparente:** usa a API não documentada `SetWindowCompositionAttribute`, a mesma abordagem de ferramentas como o TranslucentTB. O Windows desfaz o efeito em alguns momentos (por exemplo, ao abrir o menu Iniciar), então o mod reaplica a cada 250 ms.
- **Iniciar, bandeja e relógio:** são janelas separadas, que o mod não consegue pintar diretamente. Uma janela auxiliar, transparente ao mouse, fica logo **abaixo** da barra e desenha os slots ali. Como a barra é transparente, o slot aparece por trás do ícone e do texto. Ela se esconde quando um app está em tela cheia.
- **Bandeja agrupada:** os ícones da bandeja no Windows 10 têm cerca de 22 a 25 px de largura e o mod não altera esse espaçamento. Por isso ícones vizinhos dividem um slot até ele ficar com largura parecida com a altura da barra.
- **Cores e espessuras:** foram medidas de um print da hotbar do Windows 11 do autor, e não vêm de texturas do jogo. São aproximações.
- **Slots com `AlphaBlend`:** com a barra transparente, desenhar com GDI puro fazia as cores somarem ao papel de parede em vez de cobri-lo. Desenhar com `AlphaBlend` resolveu.
- **Centralização (splitbar):** o mod mede os botões pela interface de acessibilidade do Windows (MSAA) e move a janela `MSTaskListWClass` para que os botões fiquem no meio. O Windows devolve os botões para a esquerda quando o layout muda, então a posição é reaplicada cerca de 10 vezes por segundo. Um pulo curto ao abrir ou fechar um app é possível.

---

## Limitações e riscos

- **Depende de nomes internos do Windows.** O hook usa um símbolo interno do `explorer.exe`, e o splitbar usa nomes de janelas internas da barra. Uma atualização do Windows pode quebrar. Se o símbolo não for encontrado, o mod não carrega (o log mostra "Failed to hook").
- **Usa uma API não documentada** para a transparência.
- **Testado em poucas máquinas.** Outras builds, resoluções e escalas de tela podem precisar de ajuste.
- **Código novo, em C++, dentro do Explorer.** Um erro pode travar o Explorer. Se isso acontecer, desative o mod e abra um issue com o log.
- **O código foi escrito com apoio de IA e revisado por testes na prática**, não por auditoria formal.

### Conflitos

- **Classic Taskbar Fix** (aubymori): usa o mesmo hook. Não rode junto.
- **Outros mods ou programas que mudam o fundo ou a posição dos botões da barra** (por exemplo TaskbarX ou TranslucentTB): podem brigar com a transparência e, no caso do splitbar, com a centralização. Não foi testado.

---

## Como voltar ao normal

Desative ou remova o mod no Windhawk. O código devolve o fundo normal da barra ao descarregar (e, no splitbar, tenta devolver os botões à posição original). Isso ainda não foi confirmado em teste; se a barra ficar estranha, reinicie o Explorer.

---

## Como reportar um problema

Abra um issue com:

1. Versão e build do Windows (`winver`), resolução e escala de tela.
2. Versão do Windhawk e variante usada (fullbar ou splitbar).
3. Um print da barra, com o problema destacado.
4. O texto da aba **Log** do Windhawk. O mod registra as janelas que encontrou na bandeja (linhas "Tray children seen" e "Tray cells"), o que ajuda a diagnosticar builds diferentes.

---

## Créditos e licença

- **aubymori**: a técnica de hook em `CTaskBtnGroup::_DrawBar` e o layout das estruturas de renderização vêm do mod **Classic Taskbar Fix** ([winclassic.net](https://winclassic.net/thread/1889/classic-taskbar-fix)). Este código é **derivado** desse trabalho; o autor original mantém o crédito pela técnica.
- **WasiXGamer**: tema **Minecraft Hotbar** para o Windows 11, que serviu de referência visual (as cores e espessuras foram medidas de uma configuração baseada nele).
- **m417z**: Windhawk e o guia de estilos do Taskbar Styler.
- **TranslucentTB** e **TaskbarX**: referências de abordagem (transparência e centralização). Nenhum código deles foi usado.
- **BRUser1032 / BitRize**: direção do projeto, testes e ajustes.
- **Claude (Anthropic)**: escrita do código e da documentação.

"Minecraft" é marca da Mojang/Microsoft. Este projeto **não é afiliado** à Mojang, à Microsoft, ao Windhawk nem aos autores citados, e não usa texturas ou arquivos do jogo.

**Licença: pendente de verificação.** Este repositório ainda não declara licença, e a licença do código original do Classic Taskbar Fix não foi verificada. Até lá, trate o uso como "sem licença declarada".
