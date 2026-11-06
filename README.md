# Concursos SP - App Automático

App estático que lista concursos públicos em São Paulo com:
- Salário acima de R$ 10.000
- Mais de 10 vagas
- Atuação em SP
- Inscrições abertas / edital publicado / autorizado

## Como colocar no ar (gratuito)

1. Crie um repositório novo no GitHub (pode ser público ou privado).
2. Faça upload de todos os arquivos desta pasta para o repositório.
3. Vá em **Settings → Pages**.
4. Em **Source**, escolha **Deploy from a branch** e selecione a branch `main` (ou `master`) e a pasta `/ (root)`.
5. Salve. O site ficará disponível em `https://SEU-USUARIO.github.io/NOME-DO-REPOSITORIO/` em alguns minutos.

## Atualização automática

O arquivo `.github/workflows/update.yml` faz o site se atualizar sozinho todo dia usando GitHub Actions.

- Ele roda o script Python que tenta buscar dados novos e regenera o `index.html`.
- Depois faz commit e push automaticamente.
- Você pode forçar uma atualização a qualquer momento em **Actions → Atualizar Concursos SP → Run workflow**.

## Observações importantes

- O scraping de sites de concursos é frágil (os sites mudam o HTML com frequência). Se o script parar de achar dados novos, ele usa os dados de fallback que já estão no código.
- Sempre confira o edital oficial antes de se inscrever.
- Para melhorar a busca, edite o arquivo `scripts/update_concursos.py` (função `scrape_pci` ou adicione outras fontes).

## Dependências (já configuradas no workflow)

- requests
- beautifulsoup4
'''