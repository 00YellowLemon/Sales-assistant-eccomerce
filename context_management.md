# Context Management Strategy for Sales Assistant AI

This document outlines the context management and memory strategies for our LangChain-powered sales assistant AI. Effective context engineering ensures the AI can remember user preferences, maintain state across interactions, and access relevant product knowledge without exceeding context limits or losing focus.

Based on the core principles of context engineering, our strategy is organized into four key areas: Write Context, Select Context, Compress Context, and Isolate Context.

## 1. Write Context (Saving Information)

Writing context involves persisting information outside of the immediate prompt so it can be used throughout the session or across multiple sessions. For our sales AI, this means remembering user needs, cart state, and previous interactions.

*   **Conversation Memory (Short-Term):**
    *   **Purpose:** To remember the back-and-forth dialogue within a single shopping session. This allows the AI to understand follow-up questions (e.g., if the user asks "What colors does the iPhone 16 Pro Max come in?" and then asks "Add the black one to my cart," the AI knows which "black one" is being referred to).
    *   **LangChain Implementation:** We will use `ConversationBufferMemory` to maintain the raw chat history for the duration of the current session. The history will be passed into the prompt using a `MessagesPlaceholder`.

*   **Entity & Preference Scratchpads:**
    *   **Purpose:** To explicitly track user preferences discovered during needs assessment (e.g., "user is a photographer," "user prefers large screens," "budget is $1500") and current cart state.
    *   **LangChain Implementation:** We can use `ConversationEntityMemory` or simply extract and maintain a separate dictionary of facts in the application state that gets injected into the prompt via a custom system message variable.

## 2. Select Context (Pulling Relevant Information)

Selecting context means efficiently pulling only the necessary information into the agent's context window. Given our product catalog in Firestore, we cannot load all products at once.

*   **Tool-Based Selection (RAG & Search):**
    *   **Purpose:** To fetch precise product details or recommendations only when needed, avoiding prompt bloat.
    *   **Implementation:** As defined in `tool_strategy.md`, the AI relies heavily on tools like `search_products_by_attributes`, `recommend_products_by_use_case`, and fine-grained tools like `get_product_specs`.
    *   **LangChain Implementation:** We will use LangChain's Tool calling features (e.g. `bind_tools` on Chat Models) so the model itself decides when to query the Firestore database to pull relevant facts into the conversation.

*   **Prompt Injection Selection:**
    *   **Purpose:** To include dynamic, essential context (like the current cart contents or the generated checkout link) directly into the system prompt when relevant.
    *   **Implementation:** Using a `PromptTemplate`, we will inject the output of the `view_cart` tool or the current cart state directly into the system instructions for every turn.

## 3. Compress Context (Managing Token Limits)

As a shopping session progresses (from browsing to comparing to checkout), the context window can become overloaded with old tool calls and chat history, leading to distraction or high costs.

*   **Summarization:**
    *   **Purpose:** To compress older conversation turns into a dense summary while keeping the most recent turns raw for immediate context.
    *   **LangChain Implementation:** If conversation histories grow too long, we will transition from `ConversationBufferMemory` to `ConversationSummaryBufferMemory`. This LangChain memory class uses an LLM to periodically summarize older messages while keeping the recent $N$ messages intact.

*   **Trimming Tool Outputs:**
    *   **Purpose:** Search queries (like `search_products_by_attributes`) might return large results. If the user only asked for top recommendations, we should limit the output size.
    *   **Implementation:** We will design our tools to return compressed, relevant fields instead of full Firestore documents. If the text returned from a tool is still too large, we can use LangChain document transformers to trim or extract only the essential features before passing it back to the agent.

## 4. Isolate Context (Separating Concerns)

Isolating context involves splitting information up to help the agent focus on specific tasks without cross-contamination.

*   **Multi-Agent / Prompt Routing Strategy:**
    *   **Purpose:** Our AI has distinct phases: *Product Discovery/Comparison* and *Guided Checkout*. Mixing the instructions and context for both can confuse the model.
    *   **LangChain Implementation:** We will use LangChain expression language (LCEL) and routing logic (e.g., using a `RunnableBranch` or a separate routing chain) to route the user to different specialized agents:
        1.  **Sales Agent:** Equipped with product search and comparison tools.
        2.  **Checkout Agent:** Equipped with cart management and checkout guidance tools.
    *   By isolating these roles, the Checkout Agent doesn't need the product database context, and the Sales Agent doesn't need complex checkout validation rules, keeping their respective context windows clean and focused.