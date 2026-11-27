# aipdlc-checks

Builder confiável da Loja Demo, um varejo fictício (AI-PDLC, CORE-14). Repositório da Platform.

`.github/workflows/build.yml` é um workflow reutilizável: os repositórios de produção o chamam no push para o
branch principal. Ele constrói a imagem, publica no GHCR e gera a proveniência SLSA assinada pelo GitHub. A
política da empresa (`trustedBuilders`) aceita só artefatos cuja proveniência foi assinada por este arquivo:

```bash
gh attestation verify oci://ghcr.io/loja-demo-aipdlc/loja-checkout-api@sha256:... \
  --owner loja-demo-aipdlc --signer-workflow loja-demo-aipdlc/aipdlc-checks/.github/workflows/build.yml --source-ref refs/heads/main
```

Mudança aqui muda o que a empresa considera build confiável: exige duas aprovações além do autor.

## Identidade de verificação

`.github/workflows/verify.yml` é o **required workflow** que o ruleset da organização exige em todo PR do
perímetro (CORE-02). Ele baixa o motor dos checks pelo SHA fixado em `ENGINE_SHA`, avalia o PR pela API, sem
executar código do PR, e falha quando bloqueia. Um workflow criado num branch de outro repositório não satisfaz a
regra. Trocar a versão do motor é um PR aqui, com duas aprovações além do autor.

Uma review não dispara required workflows: o `aipdlc-review.yml` de cada repositório reexecuta o `aipdlc-verify`.
