# 🏠 video-imovel — vídeo de imóvel do bruto ao pronto

Plugin do Claude Code, em português. Você entrega o vídeo gravado e, se quiser,
uma frase de briefing. Ele devolve o vídeo editado para **Reels (9:16)**,
**Feed (1:1)** e/ou **YouTube (16:9)**, com:

- **Legendas estilo Reels** palavra a palavra, com a palavra falada em destaque
- **Cards de imóvel**: preço com contador, bairro, quartos, suítes, vagas, m²
- **Diferenciais** com check animado, **localização** com pin e proximidades
- **Selo** ("Pronto para morar", "Aceita financiamento")
- **Cartão do corretor** (logo, nome, CRECI, @) e **botão de WhatsApp** com o número
- Corte automático de silêncios e regravações

Tudo roda no seu computador: a transcrição é local (whisper.cpp), sem chave de
API e sem mandar o vídeo para lugar nenhum.

> **Regra do plugin:** nenhum dado do imóvel é inventado. Preço, metragem,
> quartos etc. só aparecem se estiverem no briefing ou na sua fala.

## Instalar

Dentro do Claude Code:

```
/plugin marketplace add marcusbeda-maker/video-imovel
/plugin install video-imovel@video-imovel
```

Na primeira execução ele instala o que faltar (Node.js, ffmpeg, whisper-cpp,
uv) e baixa o modelo de transcrição multilíngue (~466 MB, uma vez só).

## Configurar sua marca (uma vez)

Crie `%USERPROFILE%\.video-imovel\marca.json` (Windows) ou
`~/.video-imovel/marca.json` (macOS/Linux) e ponha o logo na mesma pasta:

```json
{
  "nome": "Seu Nome",
  "creci": "12345-F",
  "whatsapp": "62999998888",
  "instagram": "@seuusuario",
  "logo": "logo.png",
  "cor": "#1B6E4B",
  "cor2": "#C9A45C",
  "fonte": "'Montserrat', 'Segoe UI', Arial, sans-serif"
}
```

Campo vazio não aparece no vídeo. Sem esse arquivo, o vídeo sai sem cartão do
corretor e sem número no botão de WhatsApp.

## Usar

```
/video-imovel C:\videos\apto.mp4
/video-imovel C:\videos\apto.mp4 reels,feed apartamento 3 quartos no Setor Bueno, R$ 850 mil, CTA WhatsApp
/video-imovel C:\videos\casa.mp4 todos casa alto padrão, 4 suítes, Alphaville
```

Saída em `exports/final/` ao lado do vídeo, mais `edit/decisions.md` (tudo o
que ele decidiu e por quê) e `edit/ficha.md` (cada dado mostrado e de onde veio).

## Testar sem gravar nada

O template traz um gerador de vídeo falso (sala + pessoa + relógio na tela):

```bash
cd template
npm install
npm run teste:video      # cria public/footage.mp4 (9:16, 15 s)
npm run reels            # renderiza o exemplo de imóvel em saida-reels.mp4
npm run feed
npm run youtube
```

## Estrutura

```
video-imovel/
├── .claude-plugin/            # plugin.json + marketplace.json
├── skills/video-imovel/SKILL.md   # o passo a passo que o agente segue
├── agents/
│   ├── footage-scout.md       # lê os quadros e marca rosto/texto numa grade 3x3 (não vê o briefing)
│   └── edit-critic.md         # crítico que tenta reprovar a edição
├── engine/
│   ├── perceive.sh            # amostrador de quadros
│   ├── roughcut.py            # corte de silêncios e regravações
│   ├── local_whisper.py       # whisper.cpp local, em português
│   └── legendas.py            # JSON do whisper → legendas do vídeo
└── template/                  # projeto Remotion
    ├── src/imovel.tsx         # kit imobiliário
    ├── src/Legendas.tsx       # legendas estilo Reels
    ├── src/formatos.ts        # 9:16, 1:1, 16:9 e margens seguras
    ├── src/components/Fundo.tsx   # adapta o vídeo a cada formato
    ├── src/Timeline.exemplo-imovel.tsx
    └── teste/                 # gerador de vídeo de teste
```

## Idioma

Português por padrão. Para vídeo em outro idioma, defina `VIDEO_EDIT_LANG`
(ex.: `en`, `es`) antes de rodar.

## Licenças

- Código: MIT. Adaptação do [video-edit](https://github.com/loveforthegame/video-edit)
  de Abhinav (MIT) — detalhes em `THIRD_PARTY_NOTICES.md`.
- **Remotion** (usado para renderizar) tem licença própria: grátis para pessoa
  física e empresas de até 3 funcionários, inclusive para uso comercial; empresas
  maiores precisam de licença paga. Veja
  [LICENSE do Remotion](https://github.com/remotion-dev/remotion/blob/main/LICENSE.md).
