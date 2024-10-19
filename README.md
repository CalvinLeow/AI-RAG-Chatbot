# AI-RAG-Chatbot
Chatbot powered by Llama3 and Retrieval-Augmented Generation

## Abstract
The project aims to develop a chatbot tool using Large Language Models (LLM) alongside Retrieval-Augmented Generation (RAG) to enhance the learning experience of Data Structures and Algorithms (DSA). The chatbot utilises the Ollama service and is deployed on Google Colab and is designed to prepare students for online assessments as they prepare to go for their IWSP interviews by engaging in realistic and coherent short conversations. The chatbot also utilises prompt engineering based on the CO-STAR framework, alongside pre-trained models for embedding, a FAISS search index, and a PDF resource on Data Structures and Algorithms to provide relevant answers and guide students in an interactive and adaptive manner. The following report outlines the development and testing of the chatbot, including its methodology, implementation results, and conclusion on its effectiveness.

## Methodology
The chatbot system was developed in the following stages with key components.

### 1. LLM Setup and Configuration
The LLM was set up using Ollama, a lightweight service compatible with Google Colab. Ollama is an open-source project that allows developers to access a library of pre-trained Large Language Models (LLMs). For our project, the team utilised Meta's Llama3 model for its impressive performance and capability to generate human-like responses in text-based applications. Integration with the LangChain framework further enhanced the environment to facilitate better interaction with the LLM and improve conversational coherence.

### 2. Data Source and Retrieval-Augmented Generation (RAG)
Retrieval-Augmented Generation (RAG) is used to integrate retrieved information from external sources with the current LLM component. This preserves the flexibility of the generative AI model while ensuring that the replies from the chatbot are based on legitimate and reliable academic resources. To provide accurate responses and "textbook answers", a PDF cheat sheet on Data Structures and Algorithms is integrated into the chatbot's workflow. The cheat sheet helps provide an accurate and concise glossary, and will be useful when providing definitions and strict explanations when necessary.

Text Extraction: The PDF file is first retrieved using Wget and processed using PyPDF2 to extract and save all the text into a temporary string. PDF files were choses as the external knowledge base due to its simplicity and scalability as it is commonly used in education and research.

Text Chunking: Once the text has been extracted from the PDF, it is split into manageable pieces called chunks to ensure more effective processing when generating embeddings and conducting searches. Chunking is done by splitting the entire text into segments of 500 characters each. This chunk size was selected to strike a balance between granularity and contextual coherence. Smaller chunk sizes may risk important information being cut off, while large chunk sizes are heavier in performance and can be less precise.

Embedding: Once the text has been chunked, each chunk is converted into an embedding using a pre-trained SentenceTransformer model. This process transforms each chunk of text into a vector representation that captures its semantic meaning. This can ensure that similar concepts are clustered together in the vector space to allow semantic search. The all-MiniLM-L6-v2 model was chosen for its stable build and widespread use as well as its efficiency in generating high-quality embeddings.

FAISS Indexing: Once embeddings have been generated, they are stored in a FAISS index (Facebook AI Similarity Search) for fast and scalable similarity search. This index allows the system to quickly find chunks of text that are semantically closest to the user's query.

Retrieve Relevant Information: Finally, when the student asks a question, the chatbot similarly generates an embedding for that query which is then used to search for a match against the existing FAISS index, retrieving the top (k) closest chunks of text based on the L2 distance between the query embedding and the stored embeddings. For this project, the system retrieves only the top 5 most relevant chunks of text (k=5). This number was chosen to strike a good balance between performance and providing enough relevant context.

### 3. Prompt Engineering using CO-STAR Framwork
As part of the actual chatbot function, directives and rules were set up using the CO-STAR framework to instruct the chatbot of its objectives and purpose as well as it maintain conversation structure and coherence based on the project specifications.

The chatbot’s prompts were divided into six key elements, summarised as:

Context: The chatbot helps students with DSA concepts.

Outcome: Each response is brief and ends with a follow-up question.

Scale: Responses are tailored to the knowledge level of the student.

Tone: The chatbot maintains a motivational and supportive tone.

Actor: The student interacts with the chatbot.

Resources: The chatbot leverages the pre-trained Llama3 model and textbook data.

**Some additional rules include:**

Decline to engage in off-topic discussions and redirect the conversation back to DSA.

Answers should be 50 words or less.

Use textbook PDF as RAG only when necessary.

### 4. Chatbot Function and Gradio
The chatbot function itself also integrates several components to further provide meaningful, context-aware conversations. User input/response and chatbot responses are continuously appended to a string to maintain a full conversation history between the student and the chatbot. The most recent 6 lines of dialogue are also continuously selected and stored as context to provide connected and relevant conversations while maintaining performance. The chatbot invokes the LLM using the full prompt, which consists of the:

Base prompt: Structured using the CO-STAR framework to guide the conversation.

Conversation context: Recent history of user and chatbot exchanges.

Relevant text from the PDF: Retrieved through FAISS, using embeddings from the sentence-transformer model.

User input: The current question or response from the student.

Finally, the chatbot is integrated into a Gradio interface for ease of use, allowing students to interact with the chatbot using simple text input/output format. Ensuring the chatbot is intuitive and easily accessible for all users.

## Results
The chatbot was successfully deployed using the methodology described above on Google Colab's Python 3 runtime with the T4-GPU hardware accelerator. The following key results were observed:

### 1. Accurate and Contextually Relevant Responses
Llama3 integrated with FAISS indexing and RAG allows the chatbot to successfully fetch relevant and accurate results from its own model as well as textbook sections based on user queries. The storage of conversation history and context as well as the use of full prompts help ensure an effective conversation flow to fully assist the student in any question they may have.

In the following example interaction, the chatbot is demonstrated to be capable of accessing resources from its own LLM with the added ability to combine RAG information for a more accurate and coherent answer:

Student: "What is the definition of a linked list?"

Chatbot: "According to your textbook, a linked list is "a common data structure made of one or more than one nodes chained together in a single direction." It's a linear data structure where each node contains some useful information and a pointer to the next node.

Now, let's dive deeper! What do you think about the difference between singly linked lists (SLL) and doubly linked lists (DLL)?"

Student: "The singly-linked list holds data and a link to the next component. While in a doubly-linked list, every node includes a link to the previous node."

Chatbot: "Great job summarizing the key differences between singly linked lists (SLL) and doubly linked lists (DLL)! It's clear you've got a good grasp of how these two fundamental data structures work.

Now, let's dive deeper! What do you think is the most significant advantage of using a DLL over an SLL? Is it the ability to traverse the list in both directions, or something else entirely?"

Student: "According to my textbook, give me in exact quotes when do i need to use recursion."

Chatbot: "I'm happy to help you with your question about when to use recursion!

According to your textbook, "Every time you are using a tree or converting something into a tree, consider Recursion." Additionally, it suggests that you should use recursion when:


Divided into a number of subproblems that are smaller instances of the same problem.

Each instance of the subproblem is identical in nature.

The solutions of each subproblem can be combined to solve the problem at hand.

Follow-up question: Can you think of an example where these conditions are met, making recursion a suitable choice for solving the problem?"
### 2. Effective Conversation Flow
The CO-STAR framework led to structured conversations, ensuring each student query was met with a clear answer followed by a relevant probing question. It also helped ensured optimal chatbot behaviour aligned with our objectives. The additional rules successfully prevent the student from asking off-topic questions and gently redirects the conversation back to DSA topics. The continued use of a conversation history and context helps the chatbot better understand the conversation to assist the student effectively.

Example interaction:

Student: "What is an algorithm?"

Chatbot: "An algorithm is a set of instructions or step-by-step procedure used to solve a problem or perform a specific task. In computer science, algorithms are typically used to solve computational problems, such as sorting a list of numbers or searching for a particular item in a database.

Now, let's dive deeper! What type of algorithm are you most interested in learning about: sorting, searching, graph traversal, or something else?"

Student: "Something else"

Chatbot: "Since you're interested in learning about "something else", I'll assume it's not one of the specific algorithms mentioned earlier (sorting, searching, graph traversal).

To clarify, are you looking to explore other data structures or topics related to Data Structures and Algorithms? For instance, have you considered learning about Trees, Heaps, Tries, or perhaps Bit Manipulation techniques?

Please let me know how I can further assist you!"

Student: "How do I play football?"

Chatbot: "I'm happy to help you with your Data Structures and Algorithms questions! However, I noticed that your previous question was not related to DSA. To get started, let's focus on a specific topic.

You didn't ask a question about DSA, but since we're here, would you like to explore a particular concept or algorithm? For example, you could ask about sorting algorithms like Selection Sort, Insertion Sort, or even Merge Sort (which is covered in the textbook)."

### 3. Gradio Interface
The gradio interface performs well and is intuitive and modular. The chatbot is also scalable to be able to utilise React, Streamlit etc.

### 4. Performance
The response time of the initial question averages about 30 seconds. This is because the chatbot has to perform a cold start and requires more time to fully initialise the chatbot system. The first query activates the entire pipeline, including embedding generation, FAISS search, LLM invocation, context management, and token processing. After the intial question, the chatbot is able to output a response within 3 seconds. Memory usage is minimal and stable, at about 3GB and 6GB of RAM and GPU RAM respectively.

## Conclusion
The development of the DSA chatbot using Ollama and Llama3 successfully met the project’s goals of providing an engaging, informative, and contextually relevant learning experience for students. Through the use of prompt engineering (CO-STAR), FAISS indexing, and a reliable DSA resource, the chatbot was able to deliver meaningful conversations that catered to the needs of students at different levels of understanding.

The integration of Gradio made it possible to deploy the system in an accessible and user-friendly format. In future iterations, improvements could focus on expanding the knowledge base, enhancing natural language understanding, and optimising response times for an even smoother user experience.

This project has demonstrated the potential of LLMs in educational contexts, and future work will include performance testing and optimising and scaling the chatbot to handle a broader range of queries and content sources.
