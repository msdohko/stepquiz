# 🛡️ StepQuiz

Quiz competitivo mobile-first para treinamento de segurança e preparação de auditoria interna, desenvolvido para uma equipe de ~20 pessoas de uma planta industrial (Grupo Volvo).

**🔗 Acesse:** [msdohko.github.io/stepquiz](https://msdohko.github.io/stepquiz/)

---

## 📋 Sobre o projeto

O StepQuiz nasceu de uma necessidade real: preparar uma equipe de fábrica para uma auditoria de segurança, transformando o material de estudo (flashcards físicos) em uma experiência de quiz competitiva, gamificada e acessível de qualquer celular — **sem exigir login ou instalação de app**.

Cada pessoa cria um cadastro simples (usuário, senha, equipe), responde rodadas de 5 perguntas com tempo cronometrado, acumula pontos, e disputa posição num ranking individual e por equipe, visível para todo o time.

## ✨ Funcionalidades

- **Cadastro simples** — nome, sobrenome, usuário (sugerido automaticamente a partir do nome, mas editável), senha e equipe, sem necessidade de e-mail ou conta em qualquer serviço
- **Usuário separado do nome de exibição** — login com um usuário curto, enquanto o ranking mostra o nome completo da pessoa (evita ambiguidade entre colegas com o mesmo primeiro nome)
- **Quiz cronometrado** — rodadas de 5 perguntas de múltipla escolha, com 20s por pergunta (configurável pelo admin)
- **Sistema de pontuação** — 10 pontos por acerto + bônus de 10 pontos por gabaritar a rodada
- **Sistema de corações (estilo Duolingo)** — 3 tentativas representadas por corações; cada coração usado regenera individualmente 8h depois, em fila (o 1º volta em 8h, o 2º em 16h, o 3º em 24h, contados a partir da primeira perda), com contador regressivo ao vivo mostrando quando o próximo coração chega
- **Revisão de erros** — ao final de cada rodada, a pessoa vê exatamente quais perguntas errou e qual era a resposta certa, reforçando o aprendizado (não só a pontuação)
- **Ranking ao vivo** — pódio (🥇🥈🥉) individual e por equipe, com lista completa expansível e tratamento de empates (mesma pontuação = mesma posição)
- **Painel administrativo** — restrito a um usuário admin, permite:
  - Gerenciar o banco de perguntas (adicionar/remover) sem precisar reimplantar o site, com lista recolhível/expansível pra não poluir a tela
  - Ajustar o tempo por pergunta em tempo real
  - Ver um **relatório das perguntas mais erradas pelo time**, com percentual de erro e volume de respostas — ajuda a saber onde reforçar o treinamento antes da auditoria de verdade
  - Remover cadastros e resetar o ranking pra reiniciar testes
- **Senhas protegidas** — senhas nunca são salvas em texto puro; usam hash SHA-256 com salt único por pessoa (contas antigas são migradas automaticamente no próximo login)
- **Identidade visual sob medida** — cores, logo e tipografia adaptadas ao material oficial de segurança da empresa
- **Materiais de onboarding** — cartaz e tutorial em PDF (com capturas de tela reais do app) pra divulgar entre a equipe antes mesmo de abrir o link

## 🛠️ Tecnologias

- **Frontend**: HTML, CSS e JavaScript puro (sem frameworks) — um único arquivo, leve e fácil de hospedar
- **Backend / banco de dados**: [Firebase Firestore](https://firebase.google.com/products/firestore), acessado via API REST (sem SDK), garantindo compatibilidade com ambientes restritivos de CSP
- **Hospedagem**: [GitHub Pages](https://pages.github.com/)
- **Fonte**: [Nunito](https://fonts.google.com/specimen/Nunito) (Google Fonts)

## 🧠 Decisões técnicas

O projeto passou por duas migrações de infraestrutura ao longo do desenvolvimento:

1. **Protótipo inicial** rodando como artifact do Claude, usando armazenamento nativo da plataforma — funcional para testes rápidos, mas com a limitação de exigir que o visitante estivesse autenticado numa conta Claude.
2. **Versão final**, migrada para **Firebase Firestore + GitHub Pages**, eliminando essa dependência: qualquer pessoa com o link acessa e usa o app livremente, sem conta, sem login, de qualquer dispositivo.

Essa decisão priorizou a real usabilidade do público-alvo (colaboradores de chão de fábrica, muitos sem hábito de uso de aplicativos web) acima da conveniência de desenvolvimento.

## 🚀 Como rodar localmente

Por ser um projeto client-side puro, basta:

```bash
git clone https://github.com/msdohko/stepquiz.git
cd stepquiz
# abra o index.html direto no navegador, ou sirva com qualquer servidor estático:
python3 -m http.server 8000
```

Para conectar a uma instância própria do Firebase, crie um projeto no [Firebase Console](https://console.firebase.google.com), ative o Firestore, e substitua as constantes `FIREBASE_PROJECT_ID` e `FIREBASE_API_KEY` no início do `<script>` em `index.html`. As regras de segurança usadas no projeto (acesso público de leitura/escrita, adequado para um app interno de baixo risco) são:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /kv/{document=**} {
      allow read, write: if true;
    }
  }
}
```

Como essas regras são abertas, o app nunca guarda senha em texto puro — cada senha é transformada em hash (SHA-256 + salt único por pessoa) antes de ir para o banco, então mesmo alguém acessando a API diretamente não consegue ler as senhas reais.

## 📱 Design

Mobile-first, pensado para ser usado majoritariamente no celular. Identidade visual construída a partir do material de segurança já usado pela empresa (cartões físicos de treinamento "Kamishibai"), com fundo ilustrado, paleta verde/azul da marca e componentes com forte affordance visual (importante para um público que usa o app rapidamente, entre uma tarefa e outra).

## 🔭 Próximas melhorias

Algumas ideias mapeadas para uma próxima versão:

- **Recuperação de senha** — hoje, se a pessoa esquece a senha, só o admin consegue resolver (removendo e recriando o cadastro, com perda do histórico). Um fluxo de recuperação (por e-mail, ou por pergunta de segurança, dado que o público não usa e-mail corporativo no dia a dia) resolveria isso sem depender do admin.
- **Domínio personalizado** — trocar o link padrão do GitHub Pages por um domínio próprio, mais fácil de divulgar.
- **Avatar com foto real** — hoje os avatares usam iniciais com cor gerada automaticamente; permitir upload de foto de perfil deixaria a experiência mais pessoal.
- **Histórico de erros por pessoa** — o relatório de erros hoje é agregado (o time todo); mostrar pra cada pessoa individualmente quais perguntas ela mais erra ao longo do tempo tornaria o reforço ainda mais direcionado.
- **Versão em inglês** — tanto da interface quanto deste README, pensando em portfólio internacional.
- **Notificações** — avisar quando alguém ultrapassa a posição de outra pessoa no ranking (reforça o engajamento).

## 👤 Autor

Desenvolvido por **Eduardo** — projeto pessoal com aplicação real no ambiente de trabalho, do rascunho em papel ao deploy em produção.
