<div align="center">

# Landing Page — Advocacia em Cotas Raciais

**Landing Page Jurídica de Alta Performance com SEO Técnico Avançado**

![Badge Next](https://img.shields.io/badge/Next.js-000000?style=for-the-badge&logo=nextdotjs&logoColor=white)
![Badge React](https://img.shields.io/badge/React_19-61DAFB?style=for-the-badge&logo=react&logoColor=black)
![Badge TS](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![Badge Tailwind](https://img.shields.io/badge/TailwindCSS_v4-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white)

**[🔗 Acessar em Produção](`https://cotasraciais.guedesbampi.com.br/`)**
<!-- Substitua o link acima pela URL de produção -->

</div>

---

## 🧩 Sobre o Projeto

Landing page desenvolvida para um escritório de advocacia especializado em **defesa jurídica de cotas raciais**, voltada a candidatos que tiveram o direito indeferido em concursos públicos, vestibulares e processos seletivos.

O foco técnico do projeto foi **SEO estrutural avançado** (dados estruturados, metadados otimizados, conteúdo semântico) combinado a um fluxo de captação de lead duplo: formulário tradicional com envio por e-mail e um fluxo assistido via WhatsApp, ambos validados no client-side antes do envio.

> Layout e conteúdo textual/jurídico fornecidos pelo cliente; arquitetura, implementação, SEO técnico e integrações são de responsabilidade deste desenvolvedor.

---

## 🔍 Destaques Técnicos

### 1. Dados estruturados (Schema.org) para rich snippets no Google

Injeção de JSON-LD tipado como `LegalService`, permitindo que o Google exiba informações do escritório diretamente nos resultados de busca (rich snippets), sem depender apenas do conteúdo textual da página.

```ts
const legalServiceSchema = {
  "@context": "https://schema.org",
  "@type": "LegalService",
  name: "Escritório de Advocacia - Cotas Raciais",
  description:
    "Escritório especializado em defesa de cotas raciais, recursos administrativos e ações judiciais contra indeferimento em bancas de heteroidentificação.",
  url: `https://${domain}`,
  telephone: "+55XXXXXXXXXXX",
  address: {
    "@type": "PostalAddress",
    streetAddress: "•••",
    addressLocality: "Porto Alegre",
    addressRegion: "RS",
    postalCode: "•••",
    addressCountry: "BR",
  },
  areaServed: ["BR"],
  knowsAbout: [
    "Cotas raciais",
    "Banca de heteroidentificação",
    "Recurso administrativo",
    "Ação judicial cotas raciais",
  ],
};

// Injetado no <body> via next/script
<Script
  id="schema-legal-service"
  type="application/ld+json"
  dangerouslySetInnerHTML={{ __html: JSON.stringify(legalServiceSchema) }}
/>
```

### 2. Envio de e-mail via API Route com Nodemailer + SMTP

Rota de API dedicada que processa o formulário de contato e envia e-mail via SMTP, mantendo credenciais fora do código-fonte através de variáveis de ambiente.

```ts
export async function POST(req: Request) {
  try {
    const body = await req.json();
    const { name, email, phone, course } = body;

    const transporter = nodemailer.createTransport({
      host: "smtp.hostinger.com",
      port: 465,
      secure: true,
      auth: {
        user: process.env.EMAIL_USER,
        pass: process.env.EMAIL_PASS,
      },
    });

    const mailOptions = {
      from: `"Land Page Cota Racial" <${process.env.EMAIL_USER}>`,
      to: process.env.EMAIL_USER,
      subject: "Novo contato Land Page Cota Racial",
      text: `Novo contato recebido:
      Nome: ${name}
      Email: ${email}
      Telefone: ${phone}
      Curso: ${course}`,
    };

    await transporter.sendMail(mailOptions);
    return NextResponse.json({ message: "E-mail enviado com sucesso!" }, { status: 200 });
  } catch (error) {
    return NextResponse.json({ message: "Erro ao processar requisição" }, { status: 500 });
  }
}
```

### 3. Fluxo de pré-atendimento via WhatsApp com formulário assistido

Em vez de um botão simples de WhatsApp, o componente coleta nome, tema e descrição do caso antes de redirecionar — a mensagem já chega pronta e contextualizada para o time jurídico, reduzindo o tempo de triagem inicial.

```ts
const createWhatsAppUrl = (e: React.FormEvent<HTMLFormElement>) => {
  e.preventDefault();
  const value = Object.values(values) as string[];
  const existValueEmpty = value.some((v) => v === "" || v == null);

  if (existValueEmpty) {
    return alert("Por favor preencha os campos corretamente para uma melhor avaliação da equipe");
  }

  const message =
    `Olá! Meu nome é ${values.name}. ` +
    `Gostaria de um atendimento sobre o seguinte tema: ${values.course}. ` +
    `Descrição de ajuda: ${values.description} ` +
    `Por favor, aguardo o contato.`;

  const formattedPhone = phoneNumber.replace(/\D/g, "");
  const encodedMessage = encodeURIComponent(message);
  window.open(`https://wa.me/${formattedPhone}?text=${encodedMessage}`, "blank");
};
```

### 4. FAQ dinâmico orientado por conteúdo (accordion controlado por estado)

Componente de FAQ que renderiza as perguntas a partir de uma fonte de conteúdo centralizada (`mocks`), facilitando atualização de conteúdo jurídico sem alterar a lógica do componente.

```tsx
export default function AccordionGroup() {
  const [openIndex, setOpenIndex] = useState<number | null>(null);

  return (
    <div className="w-full flex flex-col gap-7">
      {landContent.section_5.faq.map((item, index) => (
        <div key={index} className="border-[1px] border-gray-300 rounded-md overflow-hidden">
          <button onClick={() => setOpenIndex(openIndex === index ? null : index)}>
            <span>{item.title}</span>
            <span>{openIndex === index ? "−" : "+"}</span>
          </button>
          {openIndex === index && <div>{item.descricao}</div>}
        </div>
      ))}
    </div>
  );
}
```

---

## 📦 Panorama da Arquitetura

- **SEO técnico:** metadados otimizados por página, Schema.org (`LegalService`, `FAQPage`), `lang="pt-BR"`, canonical tags, OpenGraph completo
- **Captação de lead:** dois canais complementares — formulário por e-mail (SMTP) e WhatsApp assistido — ambos com validação client-side
- **Conteúdo centralizado:** textos e FAQ isolados em módulo de `mocks`, permitindo atualização de conteúdo jurídico sem mexer em lógica de componente
- **Segurança:** credenciais de SMTP nunca hardcoded — sempre via variáveis de ambiente

---

## 🛠️ Stack Tecnológica

| Camada | Tecnologias |
| :--- | :--- |
| **Frontend** | Next.js 15 (Turbopack), React 19, TypeScript |
| **Estilo** | Tailwind CSS v4 |
| **E-mail** | Nodemailer (SMTP) |
| **UX/Feedback** | SweetAlert2 |
| **SEO** | Schema.org (JSON-LD), Metadata API do Next.js |
| **Deploy** | Vercel |

---

<div align="center">

### Desenvolvido por Tiago Ramon Becker
[🔗 Acessar Portfólio](https://tiagobecker.vercel.app)

</div>
