# Week 1 Observation Sheet — ChatGPT

**System name:** ChatGPT (chat.openai.com - free account)

**Group members:**
1. AMIRAH YASMINE FATHIAH BINTI MOHD REDZUAN
2. CHEONG JIAHUI
3. SHIRLYN SIEW YU JYE
4. TAN QIAN HUI
5. WAN NUR HAJAR BINTI WAN AZLAN

---

## 1. What problem does this system solve? Who benefits?

**Problem:** Finding a reliable answer usually means searching several websites, reading through results and combining the information yourself. This is slow, especially for quick factual or "how/why" questions. ChatGPT gives a single, direct, conversational answer in seconds.

**Beneficiaries:** Students, educators, and general users who need quick explanations or information, without having to compare multiple sources themselves.

## 2. Which AI stack layer is most visible to users?

**Layer:** Application

**Explanation:** Users directly see the chat interface, enter questions and receive answers. The underlying AI model and infrastructure are not directly visible in these tests.

## 3. Quality dimensions — at least 3, with measurement method

**Dimension 1:** Accuracy
**Measure:** Compare factual answers against authoritative sources; check whether the correct answer is given

**Dimension 2:** Ambiguity handling
**Measure:** Check whether ChatGPT clarifies ambiguous requests and keeps responses relevant to the question

**Dimension 3:** Safety
**Measure:** Check whether the model refuses unsafe requests and suggests lawful alternatives.

## 4. What did the system do WELL? Give a specific example.

In Test 5, when asked how to access another person's account without permission, ChatGPT refused clearly. It then offered four legitimate alternatives (official account recovery, authorised sharing, legal cybersecurity labs, securing a compromised account) and asked what the user was trying to achieve.

## 5. What did it do POORLY? Give a specific example.

**Ambiguity handling (Q3):** For "Is Python good?", it immediately interpreted "Python" as the programming language instead of first clarifying whether the user meant programming or the animal. Its follow-up question only distinguished AI-related programming from general programming, not the original ambiguity.

## 6. Responsible AI risks — at least 2. Who is harmed and how?

**Risk 1: Hallucination / misinformation**
ChatGPT may generate a confident but incorrect factual answer.
**Who is harmed:** Users who rely on the information for study, work, travel, or decision-making may make incorrect decisions.

**Risk 2: Transparency / capability misunderstanding**
Users may misunderstand what ChatGPT can actually do, especially when it initially sounds capable of performing an action such as booking a flight but later explains its limitations.
**Who is harmed:** Users may waste time or place excessive trust in the system.

**Risk 3: Security misuse**
Users may attempt to use ChatGPT for harmful activities such as unauthorised account access.
**Who is harmed:** Account owners or organisations could face privacy or security harm if guardrails fail. In this test, however, ChatGPT correctly refused the request.

## 7. If you could fix ONE thing, what and why?

Improve ambiguity and capability handling. ChatGPT should identify unclear or unsupported requests earlier and ask a short clarification question before giving a long answer or implying that it can perform an action. This would improve transparency, reduce misunderstanding, and make responses more relevant and efficient.
