# CyberDefenders (RedLine Stealer)

Este lab teve foco em investigação básica de malware a partir de um executável suspeito. O objetivo principal não foi engenharia reversa profunda, mas entender o fluxo de análise: como partir de um hash e chegar ao comportamento da ameaça.

Segui a própria ordem das questões do lab, o que ajudou a estruturar o raciocínio e evitar se perder durante a investigação.

---

## Ponto de partida: hash e classificação

A análise começou com o hash no VirusTotal, que funciona como o primeiro checkpoint de qualquer investigação.

Ali já surgiram sinais importantes:
- Classificação como Trojan (Microsoft)
- Nome associado: “wextract”
- Técnica MITRE T1005 (coleta de dados locais)

Mesmo sem análise profunda, isso já indicava um comportamento típico de stealer — foco em coleta de informações da máquina.

Aqui entra um ponto importante:
o objetivo inicial não é confirmar tudo, mas formar uma hipótese.

---

## Comportamento de rede

Depois da triagem inicial, o foco foi entender comunicação externa.

Usando o ANY.RUN, foi possível observar o comportamento dinâmico do malware:
- conexão com IP externo (C2)
- uso de porta específica
- execução em tempo real

Essa etapa é essencial porque sai do “o que dizem sobre o malware” e vai para “o que ele realmente faz”.

Aqui a análise começa a se aproximar de um cenário real de SOC:
processo executa → conecta → possivel exfiltração

---

## Identificação da família

Com o comportamento suspeito confirmado, o próximo passo foi validar a família do malware.

No MalwareBazaar:
- regra YARA: detect_Redline_Stealer

Isso confirmou que o binário pertence à família RedLine Stealer, conhecida por roubo de credenciais, cookies e dados sensíveis.

Aqui acontece a consolidação:
hash + comportamento + YARA → identidade da ameaça

---

## Enriquecimento de inteligência

Com o IP identificado, a análise seguiu para o ThreatFox.

O objetivo aqui não era descobrir algo novo, mas enriquecer o contexto:
- aliases como “RecordStealer”
- múltiplos nomes para a mesma ameaça

Isso mostra um ponto importante na prática:
uma mesma campanha pode aparecer com nomes diferentes dependendo da fonte

Por isso, correlação é mais importante que nome.

---

## Fluxo mental da análise

Mais importante que cada ferramenta foi entender o processo:

hash → classificação → comportamento → familia → inteligencia

Cada etapa responde uma pergunta diferente:
- VirusTotal → o que é?
- ANY.RUN → o que faz?
- MalwareBazaar → a que familia pertence?
- ThreatFox → como é visto no ecossistema?

Esse encadeamento é o que transforma dados soltos em investigação.

---

## Pensamento de Blue Team

Esse tipo de análise reflete exatamente o dia a dia:

um alerta chega (hash, arquivo, IP)  
→ validação inicial  
→ análise de comportamento  
→ identificação da ameaça  
→ enriquecimento  

Não é sobre uma ferramenta, mas sobre conectar fontes.

---

## Conclusão

Esse lab não foi sobre profundidade técnica, mas sobre fluxo e organização de análise.

Mostra como:
- diferentes plataformas se complementam
- dados isolados ganham valor quando correlacionados
- investigação segue um processo, não passos aleatórios

Para quem está começando, esse tipo de exercício é essencial:
ajuda a construir raciocínio antes de entrar em análises mais avançadas