# Pauta de gravação — Curso Clickmax

Site de uma página para acompanhar a produção das aulas do curso da Clickmax.
Lista os 13 módulos e ~120 aulas, e marca em que pé está cada uma:
**a fazer → roteiro pronto → gravada → publicada**.

Serve como pauta de gravação, não como site institucional. Quem abre é a
pessoa que grava as aulas: ela clica no selo de cada aula pra avançar o
status, escreve a nota de roteiro e vê quanto falta.

## O que tem aqui

```
index.html    o site inteiro — HTML, CSS e JS em um só arquivo
README.md     este arquivo
```

Sem build, sem dependências, sem framework. Abrir o `index.html` no
navegador já funciona.

## Como rodar local

```bash
open index.html            # macOS
# ou, se quiser servir por HTTP:
python3 -m http.server 8000
```

## Como o estado é salvo

Tudo (status, notas e aulas adicionadas) vive em `localStorage`, na chave
`pauta-clickmax-v1`, como um único JSON:

```js
{ status: {"m1-0":"gravada"}, notas: {"m1-0":"mostrar tela de workspace"}, extras: {} }
```

Consequências que importam:

- É **por navegador e por dispositivo**. Marcar no notebook não aparece no celular.
- Limpar dados do site apaga a pauta. O botão "Copiar pauta" gera um backup em texto.
- Não tem servidor, então **não serve pra equipe acompanhar junto**. Se precisar
  disso, veja "Próximos passos".

## Deploy

Escolha um. Todos servem arquivo estático, que é o caso aqui.

### GitHub Pages

```bash
git init
git add .
git commit -m "Pauta de gravação do curso Clickmax"
gh repo create pauta-clickmax --private --source=. --push
```

Depois, no repositório: **Settings → Pages → Source: Deploy from a branch →
`main` / `/ (root)`**. O site sai em `https://<usuario>.github.io/pauta-clickmax/`.

Se o repositório for privado, o Pages exige plano pago para manter o site
privado — no plano gratuito o site fica público mesmo com repositório privado.

### Vercel

```bash
npx vercel        # preview
npx vercel --prod # produção
```

Não precisa configurar build: quando perguntar, deixe o comando de build
vazio e o output directory como a raiz.

### Netlify

```bash
npx netlify-cli deploy --dir=. --prod
```

## Próximos passos possíveis

Em ordem de esforço:

1. **Trocar o conteúdo.** A estrutura do curso está no array `CURRICULO`, no
   topo do `<script>`. Cada módulo é `{id, nome, obj, aulas:[...]}`. Mudar o
   `id` de um módulo zera o progresso das aulas dele, porque o status é
   guardado por `id`-índice — renomeie o `nome`, nunca o `id`.
2. **Proteger com senha.** Netlify e Vercel fazem isso na plataforma, sem código.
3. **Pauta compartilhada pela equipe.** Aí precisa de backend. O caminho mais
   curto é Supabase: uma tabela `aulas` com colunas `id`, `status`, `nota`, e
   trocar as funções `carregar()` e `salvar()` — que hoje são as duas únicas
   funções que tocam o armazenamento, de propósito.
4. **Datas de gravação e prazo por módulo**, se a produção passar a ter calendário.

## Decisões de estrutura

Os módulos seguem o menu da própria Clickmax (Marketing, Mensagens,
Produtos & Entregáveis, Contatos & Comercial, Vendas & Recuperação,
Configurações) para que cada módulo seja gravado sem trocar de área da
plataforma no meio.

O módulo 1, "Onboarding Express", é a única trilha linear: cada aula assume o
estado que a anterior deixou na conta. As demais são biblioteca de consulta —
cada aula abre dizendo onde clicar pra chegar na tela.
