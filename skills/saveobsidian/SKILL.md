---
name: saveobsidian
description: Comando /saveobsidian — salva a conversa INTEIRA como uma nota de memória no vault do Obsidian do projeto (pedidos, respostas, decisões e pendências) e atualiza as notas que a conversa deixou velhas, sem levar segredo nem dado pessoal. Funciona em qualquer projeto e acha o vault sozinho. Use sempre que o usuário digitar /saveobsidian, "salve no obsidian", "salve a conversa", "guarde este chat no vault", "registre esta conversa", "save to obsidian" ou "save this chat to the vault".
---

# /saveobsidian — salvar a conversa no Obsidian

Deixe a memória deste chat **completa** no vault, para que qualquer sessão futura saiba o que foi
pedido, respondido, feito e decidido, e o que ficou pendente, sem ter lido a conversa.

Escreva no idioma da conversa. As regras do próprio projeto (`CLAUDE.md`, `AGENTS.md`, convenções
do vault) valem mais que os padrões abaixo.

## 1. Achar o vault

Pare no primeiro item que responder:

1. `saveobsidian.config.json` na raiz do projeto (campos abaixo).
2. `obsidian-skill.config.json` na raiz, campo `vault` (é o da skill `atualizar-obsidian`).
3. `CLAUDE.md` ou `AGENTS.md` que diga onde o vault está.
4. Uma pasta do projeto com `.obsidian/` dentro, até 4 níveis de profundidade, ignorando
   `node_modules`, `.git`, `dist`, `build`, `.next`, `vendor` e `target`. Um resultado: é esse.
   Mais de um: pergunte qual.
5. Nenhum: pergunte ao usuário. Ofereça usar um vault que ele já tenha ou criar
   `Obsidian/<nome do projeto>/`. **Sem resposta, não crie nada**: um vault criado no lugar errado
   parece que salvou e ninguém acha depois. Grave a resposta em `saveobsidian.config.json`.

| Campo de `saveobsidian.config.json` | Padrão | Para quê |
| --- | --- | --- |
| `vault` | obrigatório | pasta do vault, relativa à raiz do projeto ou absoluta |
| `pastaSessoes` | `Sessões` | pasta das memórias, relativa ao vault |
| `indice` | procurado no passo 6 | nota que lista as memórias |

**Vault fora da pasta do projeto** costuma ser compartilhado entre projetos: sem `pastaSessoes`
definida, use `Sessões/<nome do projeto>/`, para as conversas de um não se misturarem com as do outro.

## 2. Reunir a conversa inteira

Do primeiro pedido ao último, não só o que mudou desde a última vez.

**Conversa resumida:** se o começo do chat foi compactado, ele não está mais à vista. Recupere-o da
nota que já existe e do histórico, no caminho que o resumo informar ou em
`~/.claude/projects/<pasta do projeto>/<sessão>.jsonl`. O histórico é só fonte: ele traz saída de
ferramenta e pode trazer segredo, então nada vai copiado direto. O que não der para recuperar fica
marcado como "segundo o resumo". Nunca preencha de cabeça.

## 3. Achar ou criar a nota

Siga o padrão de nome que já existe na pasta. Pasta nova: `Memória de chat AAAA-MM-DD.md`.

Se já houver nota com esse nome, **leia antes de escrever**:

- ela registra o mesmo primeiro pedido: é deste chat. Mantenha tudo o que ela já diz, menos o que a
  conversa corrigiu;
- ela registra outra conversa, ou é de outro ano (`periodo` no frontmatter, em nome sem o ano): é de
  outro chat. **Nunca sobrescreva.** Crie `Memória de chat AAAA-MM-DD assunto.md`.

## 4. Escrever a nota

Se o vault já tiver um modelo ou notas de sessão anteriores, copie o formato deles. Senão, use este:

```markdown
---
tipo: sessão
periodo: 2026-11-05
atualizado: 2026-11-05
cobre: [src/api/pedidos.ts]
---

# Memória de chat 05/11/2026 — assunto

## Estado agora

- **Pronto:** o que terminou.
- **Esperando autorização:** o que depende do usuário.
- **Em andamento:** o que ficou no meio.

## Linha do tempo

1. **"O pedido, nas palavras do usuário"**: o que foi respondido ou feito, com os números medidos,
   os identificadores do caso e os arquivos tocados.

## Respostas corrigidas

- Primeiro disse X. Estava errado: é Y, e foi assim que descobrimos.

## Decisões do usuário

- O que ele escolheu, e o motivo se ele disse.

## Pendências

- [ ] Aberta.
- [x] Fechada.
```

- Um item na linha do tempo por pedido do usuário.
- `cobre` lista os arquivos que a conversa mexeu. Sem arquivo, a linha sai. Seção vazia também sai.
- Datas absolutas, nunca "ontem" ou "semana passada".
- Detalhe técnico mora na nota do assunto; a memória aponta com `[[link]]`, e só para nota que existe.

## 5. Atualizar o resto do vault

Salvar a memória e deixar velha a nota do assunto não é salvar a conversa.

- Se o projeto tem uma skill de manutenção do vault (como `atualizar-obsidian`), rode-a.
- Senão, para cada arquivo, regra ou decisão que a conversa mudou, busque no vault pelo caminho, pelo
  nome da função ou pelo termo. Nota que ficou errada: **corrija o trecho**, não acrescente um
  parágrafo contraditório embaixo. Atualize o `atualizado:` só das notas que você conferiu.
- Assunto sem nota: registre como pendência na memória. Não invente estrutura nova no vault.
- Conversa que só leu e não mudou nada: pule este passo.

## 6. Ligar a nota ao índice

Nota sem link de entrada ninguém acha. Use o `indice` configurado ou, na raiz do vault, `Home.md`,
`Índice.md`, `Index.md` ou `README.md`. Acrescente o link na lista de sessões, a mais recente primeiro.
Sem lista, crie uma seção `## Sessões` no fim. Sem nenhum índice, crie `Home.md` com essa seção.

Em vault compartilhado, projetos diferentes geram notas de mesmo nome no mesmo dia, e `[[Memória de chat
2026-11-05]]` fica ambíguo. Ali, use o link com o caminho:
`[[Sessões/<nome do projeto>/Memória de chat 2026-11-05]]`.

## 7. Conferir antes de responder

Releia a nota e confirme que estão lá o primeiro e o último pedido. Depois **rode a busca de texto de
verdade**, com estes dois padrões copiados inteiros, nela e nas outras notas que você editou. Este passo
nunca se pula, nem quando a nota parece limpa: olhar e achar que não tem nada é justamente o que deixa
o segredo passar.

```text
ghp_|gho_|github_pat_|sk-[A-Za-z0-9]|xai-|sbp_|AKIA[0-9A-Z]|eyJ[A-Za-z0-9_-]{10,}\.|BEGIN [A-Z ]*PRIVATE KEY|(password|senha|secret|token|api[_-]?key)\s*[:=]\s*\S
\b\d{3}\.?\d{3}\.?\d{3}-?\d{2}\b|\b\d{2}\.?\d{3}\.?\d{3}/?\d{4}-?\d{2}\b|\b(?:\d[ -]?){13,16}\b|[\w.+-]+@[\w-]+\.[\w.]+
```

O primeiro acha segredo; o segundo acha CPF, CNPJ, cartão e e-mail. Achou: tire e conte ao usuário.
Número que só parece documento pode ficar. Confira também se cada `[[link]]` novo aponta para nota
que existe.

## 8. Responder

O caminho da memória, uma linha por nota criada ou editada, o resultado da busca do passo 7 e o que
ficou de fora de propósito.

## O que NÃO vai para o vault

A conversa é salva como REGISTRO, não como transcrição:

- **segredo**: token, chave, senha, cookie, string de conexão. Nem revogado, nem de teste;
- **dado pessoal de terceiros**: só o que identifica o caso, como primeiro nome e número do pedido ou
  do chamado. Nada de CPF, CNPJ de pessoa física, telefone, endereço, e-mail ou cartão;
- **saída de ferramenta inteira**: só o número que importa, com a data da medição.

Vault dentro de um repositório vai junto no próximo commit, e repositório público expõe a nota.

Se o usuário quiser a transcrição literal, avise que ela já existe no histórico da ferramenta e que
copiá-la para o vault levaria esses dados junto.

## Como gravar sem estragar

- Grave pela ferramenta de edição de arquivos. Em heredoc ou em string de Python e de shell, `\b` e
  `\t` viram caracteres de controle dentro da nota.
- Em script, leia o arquivo antes de abri-lo para escrita: `open(p, "w")` apaga o conteúdo na hora, e
  o `read()` seguinte devolve vazio.
- Não mexa em `.obsidian/`: é a configuração do aplicativo.
