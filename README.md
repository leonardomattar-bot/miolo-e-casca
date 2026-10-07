# Miolo & Casca

Projeto pessoal e local: receitas do maquinadepao.com.br reorganizadas e adaptadas para a
**Mondial NPF-54** (pesos 500, 750 e 1000 g; cores Clara e Média).

## Como abrir

Abra `docs/index.html` no navegador (duplo clique). Funciona offline, sem servidor.
Favoritos, "já fiz", anotações e o rascunho do criador de receitas ficam salvos só nesse navegador.

## Estrutura

| Pasta / arquivo | Conteúdo |
|---|---|
| `etapa1-extracao/` | Etapa 1: texto extraído de cada receita (`receitas.json`), guias de dicas (`dicas.json`), índice (`EXTRACAO.md`) e lista do que foi descartado (`excluidos.json`) |
| `fichas/<coleção>/*.md` | Etapa 3: uma ficha Markdown por receita (761) |
| `docs/` | Etapa 2: landing page (`index.html`), dados (`dados.js`) e fotos padronizadas em 640×480 (`fotos/`). É a pasta publicada pelo GitHub Pages |
| `dados/fotos_web.json` | Origem (página e domínio) de cada foto buscada na web para receitas que não tinham foto no site |
| `dados/fotos_referencia.json` | Receita de origem de cada foto de referência adaptada |
| `dados/` | Dados intermediários e o recorte de energia da tabela TACO usado nos cálculos |
| `scripts/` | Scripts que geram tudo acima |

## Como as receitas foram adaptadas

- **Programa:** cada ciclo do site foi convertido para um dos 19 programas da NPF-54 (ex.: "Amassar / Massa / Pizza" → 10 – Pizza; "Básico / Normal" → 1 – Pão Básico; "Ultra-rápido" → 2 – Pão Rápido, porque a NPF-54 não tem ultra-rápido). As alternativas do original foram mantidas.
- **Peso:** 450/500 g → 500 g; 600/750 g → 750 g; 900 g/1 kg → 1000 g. Quando o original não informa o tamanho, ou informa um tamanho ambíguo, o peso foi escolhido pelo peso estimado do pão (isso fica indicado na ficha). Só os programas que permitem escolher o peso recebem essa configuração, conforme o manual.
- **Cor:** Clara para pães doces (categoria Doce, programas Pão Doce e Panetone, ou açúcares + gorduras ≥ 25% do peso das farinhas); Média para os demais. A cor Escura não é usada.
- **Alertas:** a ficha avisa quando a receita passa dos limites do manual (4 copos de farinha e 2 colheres de chá de fermento biológico seco). As quantidades originais não foram alteradas.
- **Timer:** a ficha avisa para não usar o timer quando a receita leva leite, ovos, queijo ou manteiga (recomendação do manual).

## Fotos

- Fotos do próprio site quando existem.
- Para as demais, `scripts/buscar_fotos_web.py` busca imagens na web (Yandex Imagens) e só aceita uma foto quando o título da página de origem traz as palavras que distinguem o nome da receita, juntas e na mesma frase, e o tipo do produto (pão/bread/brot/pain…, bolo/cake…, geleia/jam…), em qualquer língua. Essas fotos são ilustrativas: aparecem com a marca "web" no card e o link da origem na ficha.
- As demais recebem uma **foto de referência** (`scripts/fotos_referencia.py`): a foto da receita mais parecida (palavras do nome, ingredientes principais, coleção e programa), espelhada e com zoom mais próximo ou mais distante, para não ficar igual à original. Cada foto de origem é usada no máximo 2 vezes. Aparecem com a marca "ref." no card e, na ficha, com o link para a receita de origem. A lista está em `dados/fotos_referencia.json`.

## Como as calorias foram calculadas

- Energia pela **TACO 4ª ed. (NEPA/UNICAMP)** e, na falta, pela **USDA FoodData Central**. Os ingredientes industrializados que não estão em nenhuma das duas usam o rótulo de um produto comum. Sempre que um valor é estimado com base em outro produto, a ficha diz qual.
- Medidas: xícara 240 ml, colher de sopa 15 ml, colher de chá 5 ml, ovo 50 g; densidades por ingrediente em `scripts/ingredientes.py`.
- Quando há alternativas ("manteiga ou margarina"), vale a primeira. Itens "a gosto", sem quantidade, ou usados só para ferver ou fritar não entram na conta, e a ficha lista cada um.
- Peso final: soma dos ingredientes − 11% de evaporação ao assar na máquina, ou − 17,5% ao assar no forno/airfryer. Geleias, pratos, massas fritas ou cozidas usam o peso da mistura, sem perda estimada, e a ficha avisa.

## Refazer tudo

```bash
pip install beautifulsoup4 lxml pillow
python3 scripts/baixar_site.py <cache>     # baixa a lista de publicações e o HTML de cada uma
python3 scripts/extrair.py <cache>
python3 scripts/estruturar.py
python3 scripts/gerar_fichas.py
python3 scripts/baixar_fotos.py <cache>
python3 scripts/buscar_fotos_web.py      # opcional; pode ser interrompido e retomado
python3 scripts/fotos_referencia.py
python3 scripts/gerar_site.py
```

## Publicação no GitHub Pages

Publicado em <https://leonardomattar-bot.github.io/miolo-e-casca/> (repositório público).

- A pasta publicada é `docs/` — o GitHub Pages não aceita outra pasta além de `/` e `/docs`.
- As fichas ficam na **raiz** (`fichas/`) porque o link "Abrir ficha .md" do site usa `../fichas/...`;
  o `index.html` da raiz apenas redireciona para `docs/`.
- A página tem `<meta name="robots" content="noindex, nofollow">`: não é indexada por buscadores,
  então só é encontrada por quem tem o link. Um `robots.txt` em subpasta **não** é respeitado por
  buscadores — o que funciona é o `noindex` na própria página.

Para atualizar:

```bash
cd C:/Users/leonardo/repos/miolo-e-casca
git add -A && git commit -m "atualização" && git push
```

O site se republica em cerca de 1 minuto.
