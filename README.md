# Step 1: Set up prompt with `aichat`

---
model: ollama:phi4:latest
temperature: 0
top_p: 0
---

You are a precise and intelligent summarizer. Given a single class definition, perform the following tasks:
	1.	Title your output with the fully qualified name (FQN) of the class.
	2.	Summarize the overall purpose and functionality of the class in 1–3 concise sentences.
	3.	List and describe each method in the class, summarizing its functionality in one sentence per method.
	4.	Identify and list referenced classes from the same codebase, based on the import statements. To determine this:
    	•	Use the FQN of the current class (e.g., com.example.MyClass) to infer the project package prefix (e.g., com.example).
    	•	From the import statements, list only those classes whose FQN starts with the same prefix (e.g., com.example.*).
    	•	Ignore imports that do not start with the same prefix (such as standard library or third-party classes).

Use clear and concise technical language appropriate for developers. Be accurate and avoid assumptions.

---

# Step 2: Write function to recursively go through kotlin files

```
function summarize_kt_files
      set dir $argv[1]
      echo > summaries.md
      for file in $dir/**/*.{kt,kts}
          aichat -r %codesummarizer% -f "$file" >> summaries.md
          echo "Processed $file"
      end
  end
```

# Step 3: Prompt Claude

---

<insert>
  summaries.md
</insert>

I have attached a software codebase description file. I want you to analyze the entire document and produce a comprehensive technical document with the following key requirements:
	1.	Document Length & Style
	•	The document should be roughly 20-30 pages in length
	•	Write in a clear, professional, and technically sound style suitable for internal engineering documentation or stakeholder review.
	•   Make sure that sentences are short, to improve readability
	2.	Target Chapters
	•	2. Framework and Technologies
	•	3. System Architecture
	•	Specifically focus on the modularity of the system.
	•	4. Core Functionalities / Modules
	•	Treat drivers (i.e., third-party integrations, external services) as a special subsection or topic.
	•	5. Data Models and Storage
	•	6. Integration Points and APIs
	•	7. Security and Permissions
	3.	Level of Detail
	•	Provide a high-level architectural and functional overview.
	•	Focus on why components exist and what they do, rather than detailing all the code logic or line-by-line descriptions.
	•	Highlight any use of notable design patterns (e.g., dependency injection, repository pattern, factory pattern, etc.) where relevant.
	•	Avoid excessive code-level detail, but include brief mentions or examples if needed to illustrate important points.
	•	When describing classes and methods, summarize their responsibilities, emphasizing interactions and dependencies among components.
	4.	Specific Guidance for Each Chapter
	•	(2) Framework and Technologies
	•	Identify the main programming language(s) and key frameworks/libraries (e.g., Spring Boot, Django, Express.js, React, Angular, etc.).
	•	Mention why these frameworks/libraries might be suitable or how they are used (e.g., web MVC, REST handling, data persistence, etc.).
	•	(3) System Architecture
	•	Describe the overall structure (e.g., layered architecture, microservices, monolith).
	•	Emphasize the modularity: how the system is divided into modules, packages, or components.
	•	If there is any notable architectural pattern (onion/hexagonal architecture, event-driven, domain-driven design), briefly explain how it’s implemented.
	•	Mention how these modules communicate (APIs, direct calls, message queues, etc.).
	•	(4) Core Functionalities / Modules
	•	Outline the main business logic modules (e.g., “User Management,” “Payment Processing,” “Notification Service,” etc.).
	•	Drivers / Third-Party Integrations: If there are specialized connectors or clients that integrate with external systems (e.g., payment gateways, social media APIs, external data sources), treat them as a dedicated subsection.
	•	Briefly describe the purpose of each module and how it fits into the larger system.
	•	(5) Data Models and Storage
	•	Summarize key database entities, tables, or schemas.
	•	Outline relationships (one-to-one, one-to-many, many-to-many) and any notable constraints.
	•	Mention the type of storage (SQL, NoSQL, file storage, etc.).
	•	(6) Integration Points and APIs
	•	Identify any internal/external REST APIs, GraphQL endpoints, gRPC interfaces, or other communication mechanisms.
	•	Note how data flows in and out of the system.
	•	Include a brief explanation of authentication or authorization steps for each integration (if applicable).
	•	(7) Security and Permissions
	•	Summarize how the application handles user authentication (e.g., OAuth2, JWT, session-based).
	•	Describe any role-based access control (RBAC) or permission frameworks used.
	•	Mention any encryption or secure data storage practices (if present).
	5.	Tone & Structure
	•	Present each chapter with a clear heading and subheadings.
	•	Use bullet points, short paragraphs, or brief examples where helpful.
	•	Avoid deep code explanations—stay focused on architecture, design rationale, and system-level behavior.

⸻

Rules:


- Never mention class names, only feature packages and class type (service, controller, driver, dto, orm entity, etc)
- Comprehensive, complete and detailed described functionality from a high level. Inter-class and inter-feature relations
- You have a very detailed and friendly tone, explain why things are created as they are.
- Project is KOTLIN language! 
- Project is a payment orchestration platform 
- Documentation is for the backend service oriented application


Final Request to the LLM
	1.	Read and analyze all the provided code carefully.
	2.	Create a 20-30 page summary document with the exact chapter structure requested in this prompt:
2. Framework and Technologies
3. System Architecture (focus on modularity)
4. Core Functionalities / Modules (including drivers/3rd-party integrations)
5. Data Models and Storage
6. Integration Points and APIs
7. Security and Permissions
	3.	Adhere to the guidelines on level of detail, style, and coverage.
	4.	Keep the focus on architecture and functionality rather than in-depth code listings.
	5.	Ensure clarity and correctness, verifying that the final descriptions accurately represent the codebase’s design.
	
------

