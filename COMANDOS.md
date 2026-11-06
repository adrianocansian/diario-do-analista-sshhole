# SSHole (CVE-2026-123158) — Comandos operacionais

Comandos extraídos do alerta do Diário do Analista. Execute **somente em sistemas sob sua administração ou com autorização**.

**Atenção:** os comandos das seções 3 e 4 alteram o sistema e podem interromper o acesso SSH. Confirme o acesso por chave e mantenha uma sessão administrativa aberta. Os demais são de diagnóstico/investigação. Não execute todos os blocos como um único script.

## 1. Identificação do host e pré-condições

```sh
# Verifica se a libc é musl (presença do loader musl)
ls /lib/ld-musl-* 2>/dev/null && echo "musl detectada"

# Alpine: qual pacote do servidor SSH está instalado e em que versão
apk info -v 2>/dev/null | grep -E '^openssh-server'

# Configuração efetiva do sshd
sshd -T | grep -Ei '^(usepam|passwordauthentication|permitrootlogin) '

# Contas com campo de senha "*" (alvos potenciais)
awk -F: '$2=="*" {print $1}' /etc/shadow

# Contas com hash $5$ ou $6$ (habilitam a exploração)
awk -F: '$2 ~ /^\$[56]\$/ {print $1}' /etc/shadow
```

## 2. Conferência de regras Match

```sh
# Exemplo: testar as regras aplicáveis ao usuário deploy e a uma origem específica
# Substitua o endereço de documentação pelo IP de origem pertinente ao seu ambiente.
sshd -T -C user=deploy,host=localhost,addr=192.0.2.10 \
  | grep -Ei '^(usepam|passwordauthentication|permitrootlogin|authenticationmethods|kbdinteractiveauthentication) '
```

## 3. Atualização do OpenSSH no Alpine

```sh
apk upgrade -U -a
rc-service sshd restart
```

## 4. Mitigação emergencial (modifica configuração SSH)

```sh
mkdir -p /etc/ssh/sshd_config.d
echo 'PasswordAuthentication no' > /etc/ssh/sshd_config.d/00-no-passwords.conf
sed -i.bak -e '1i PasswordAuthentication no' \
  -e 's/^\(\s*PasswordAuthentication[^y#]*\)yes/\1no/i' \
  /etc/ssh/sshd_config
sshd -t && rc-service sshd restart
sshd -T | grep -i passwordauthentication
```

## 5. Inventário de contêineres com sshd

```sh
# Containers em execução que têm um processo sshd
for c in $(docker ps -q); do
  docker exec "$c" sh -c 'pgrep -x sshd >/dev/null 2>&1' && \
    echo "sshd ativo em: $(docker inspect -f '{{.Name}}' "$c")"
done
```

## 6. Busca inicial de evidências em logs

```sh
for u in $(awk -F: '$2=="*" {print $1}' /etc/shadow); do
  grep -E "sshd.*Accepted password for $u " /var/log/messages* 2>/dev/null
done
```

## Observações

- `192.0.2.10` é endereço de documentação e deve ser substituído.
- Um evento `Accepted password` é um sinal para investigação, não prova isolada da exploração.
- Consulte o artigo e as fontes primárias para condições de aplicabilidade e versões corrigidas.
