# Configuração do Squid Proxy para Controle de Acesso

Este documento explica as regras implementadas no Squid Proxy para controle de acesso a sites específicos.

## Regras Implementadas

### 1. Bloqueio de Redes Sociais com Exceção

**Objetivo**: Bloquear Facebook e YouTube para toda a rede, exceto para um IP específico.

```squid
acl sites_bloqueados dstdomain .facebook.com .youtube.com
acl ip_liberado src 192.168.0.100

http_access allow ip_liberado sites_bloqueados
http_access deny sites_bloqueados
```

Explicação:

- dstdomain: Bloqueia por domínio (incluindo subdomínios pelo . antes do nome)

- src: Define o IP com permissão especial

- Ordem é crucial: a exceção deve vir antes da regra geral

### 2. Filtro de Conteúdo com Exceção

**Objetivo**: Bloquear qualquer conteúdo com "terra" na URL, exceto o site terraviva.com.
squid

```squid
acl bloqueio_palavra url_regex -i terra
acl bloqueio_palavra_req req_header Referer -i terra
acl site_permitido dstdomain .terraviva.com

http_access allow site_permitido
http_access deny bloqueio_palavra
http_access deny bloqueio_palavra_req
```

Explicação:

- url_regex: Busca a palavra em qualquer parte da URL

- -i: Case insensitive (não diferencia maiúsculas/minúsculas)

- req_header: Verifica também no cabeçalho Referer

## Fontes Oficiais

- [Documentação Oficial do Squid (ACLs)](http://www.squid-cache.org/Doc/config/acl/)
- [Documentação Oficial do Squid (HTTP Access)](http://www.squid-cache.org/Doc/config/http_access/)
- [Site IFRN squid (URL Regex)](https://mange.ifrn.edu.br/tutorial/squid/listas/)

## Considerações Importantes

- Ordem das regras é fundamental (Squid processa de cima para baixo)

- Reinicie o Squid após alterações: systemctl restart squid
