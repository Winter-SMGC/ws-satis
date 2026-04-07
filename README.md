# ws-satis

Um registro privado de pacotes Composer baseado em [Satis](https://github.com/composer/satis) para a organização Winter-SMGC. Ele automaticamente constrói e publica pacotes PHP no GitHub Pages sob demanda.

**URL do Registro:** https://winter-smgc.github.io/ws-satis

## Requisitos

- PHP 8.4+
- [Composer](https://getcomposer.org/)

## Uso

O registro já está configurado no `composer.json` do repositório principal da organização:

```json
{
  "repositories": [
    {
      "type": "composer",
      "url": "https://winter-smgc.github.io/ws-satis/packages.json"
    }
  ]
}
```

Ao iniciar um projeto a partir do repositório principal, essa configuração já estará incluída. Para instalar um pacote:

```bash
composer require winter-smgc/<nome-do-pacote>
```

## Adicionando um Novo Pacote

1. Adicione uma nova entrada ao array `repositories` no `satis.json`:

```json
{
  "type": "vcs",
  "url": "https://github.com/Winter-SMGC/<nome-do-repositorio>"
}
```

2. Faça o commit e envie a alteração.
3. Acione uma reconstrução pelo workflow do GitHub Actions (veja [Construindo o Registro](#construindo-o-registro)).

## Construindo o Registro

O registro é construído automaticamente pelo workflow do GitHub Actions definido em `.github/workflows/build.yml`. Ele pode ser acionado de duas formas:

- **Manualmente** — pelo botão *Run workflow* na interface do GitHub Actions.
- **Via API** — envie um evento `repository_dispatch` com o tipo `rebuild`:

```bash
curl -X POST \
  -H "Authorization: Bearer <GITHUB_TOKEN>" \
  -H "Accept: application/vnd.github+json" \
  https://api.github.com/repos/Winter-SMGC/ws-satis/dispatches \
  -d '{"event_type":"rebuild"}'
```

### Etapas da Construção

1. Faz o checkout do repositório.
2. Configura o PHP 8.4 com o Composer.
3. Instala o Satis (`composer/satis:dev-main`).
4. Executa `php satis-tool/bin/satis build satis.json public --no-interaction`.
5. Publica o diretório `public/` gerado no GitHub Pages.

## Construção Local

```bash
# Instalar o Satis
composer create-project --keep-vcs --no-dev composer/satis:dev-main satis-tool \
  --no-interaction \
  --no-progress

# Construir o registro
php satis-tool/bin/satis build satis.json public --no-interaction
```

O repositório gerado estará disponível no diretório `public/`.

## Configuração

Todas as configurações de repositório e arquivo são definidas no `satis.json`:

| Campo | Valor | Descrição |
|-------|-------|-----------|
| `name` | `winter-smgc/registry` | Identificador do registro |
| `homepage` | https://winter-smgc.github.io/ws-satis | URL pública |
| `repositories` | array de objetos `{ type, url }` | Lista de repositórios VCS a indexar |
| `repositories[].type` | `"vcs"` | Tipo de fonte (sistema de controle de versão) |
| `repositories[].url` | URL do GitHub | Endereço do repositório do pacote |
| `require-all` | `true` | Indexa todas as versões de cada pacote listado |
| `archive.directory` | `dist` | Onde os arquivos ZIP são armazenados |
| `archive.format` | `zip` | Formato do arquivo |
| `archive.skip-dev` | `true` | Exclui dependências de desenvolvimento dos arquivos |
| `output-html` | `false` | Desativa a geração de página HTML |

## Pacotes

| Pacote | Fonte |
|--------|-------|
| `winter-smgc/ws-plugin-banner` | https://github.com/Winter-SMGC/ws-plugin-banner |
| `winter-smgc/ws-core-plugin` | https://github.com/Winter-SMGC/ws-core-plugin |

## Licença

Consulte os pacotes individuais para suas respectivas licenças.