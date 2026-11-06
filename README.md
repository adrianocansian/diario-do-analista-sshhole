# SSHole (CVE-2026-123158) | Comandos de diagnóstico e resposta

Material complementar do **Diário do Analista** para a avaliação de instalações OpenSSH em sistemas Linux com musl libc sem PAM.

**[Consultar os comandos, organizados em seis seções](COMANDOS.md)**

## Escopo

Os exemplos incluem identificação de pré-condições, validação de regras `Match`, atualização do Alpine Linux, mitigação, inventário de contêineres e triagem inicial de logs. Eles foram disponibilizados para uso em sistemas próprios ou explicitamente autorizados.

## Segurança operacional

- Leia o comando completo antes de executar. Não execute os seis blocos como um único script.
- Os comandos de atualização e mitigação podem alterar a configuração e interromper sessões SSH. Confirme acesso alternativo e mantenha uma sessão administrativa aberta.
- A saída de uma triagem de logs não substitui investigação de incidente.
- Antes de aplicar qualquer mudança, confira a documentação oficial do sistema, a configuração efetiva do sshd e a existência de backups.

## Fontes primárias

- [OpenSSH portable 10.6p2](https://www.openssh.org/txt/release-10.6p2)
- [Alpine Linux: security advisory](https://alpinelinux.org/posts/2026-11-05-openssh-password-authentication-bypass/)
- [CVE-2026-123158 — NVD](https://nvd.nist.gov/vuln/detail/CVE-2026-123158)

**Aviso:** este material é fornecido para orientação técnica; adapte os procedimentos ao ambiente e à política de mudanças da organização.