
[Documentação](../documentacao.md)

# Stash

Repositório do time no stash:

<https://stash.uol.intranet/projects/BIBD>

**Comandos basicos do Git**

```bash
#se der problema de certificado no stash, rodar este comando antes do git clone
git config --global http.sslverify false
git clone <url do projeto do bitbucket.git>

git pull (para atualizar)
<adicione suas modificações>
git status (para verificar as alterações)
git add <nome do arquivo> ou git add . (para adicionar tudo)
git commit -m "mensagem do commit sem acento ou cedilha"
git push origin master


#pra cancelar os git add (antes de dar o commit)
git reset <file>
```

- [Erro git fatal Authentication failed no Stash apos alterar a senha de rede](stash/erro-git-fatal-authentication-failed-no-stash-apos-alterar-a-senha-de-rede.md)
- [Visual Studio Code](stash/visual-studio-code.md)
