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

O check obrigatório `aipdlc` é publicado pelo GitHub App `aipdlc-verify`, cujo webhook chega a um Lambda na AWS
(CORE-02). `.github/workflows/verify-deploy.yml` é o único caminho para trocar o código desse Lambda: ele baixa o
motor do repositório privado `ai-pdlc` pelo `ENGINE_SHA` fixado, constrói a imagem, assina a proveniência e
implanta pelo digest, no environment `verificacao` (CORE-33). Trocar a versão do motor é um PR aqui, com duas
aprovações além do autor.
